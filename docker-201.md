## Docker 201

Now that we know how to create and run Docker containers, it's time to work with something familiar! Let's take a look at how can we get a small, basic Sinatra app up and running!

First, we need to have access to the app's code we want to run in our container.
We can, if we want to, install git onto our Linux instance, connect using an SSH key and then ask to pull down our repos through a secure connection every time we build our containers.

But due to the complexity and risks associated with handling passwords and secure keys, Docker recommends a different solution: Get the code locally on your machine, then use the COPY command to get it into a container.

We could use git to download our code, but what we are about to write is fairly simple - let's brush up our Ruby a bit!

Let's create a folder called `ruby_test_app` and then an `app.rb` inside it.

```bash
mkdir sinatra_demo
cd sinatra_demo
touch app.rb
```

Then we can add the following to the `app.rb`:

```ruby
require('sinatra')
require('json')

get '/hello-world' do
    content_type(:json)
    response = {message: 'Hello Codeclan!'}
    return response.to_json()
end
```

Once we finish with the lab, when we hit `localhost:4567/hello-world` (the default port for Sinatra apps), we should get back a JSON response with the above message.

Then we can start modifying our dockerfile: For a start, let's install Ruby instead of Python. Not specifying version number means we install the most up-to-date version of Ruby.

```dockerfile
# In the dockerfile
FROM ubuntu
RUN apt-get update
RUN apt-get -y install ruby
```

The RUN command simply executes commands in the containers environment - in our case, the Linux shell.

We can rebuild and enter our container - once we did, we can see that `ruby -v` will return us the latest version of Ruby available. We can also go and play around in `irb`!

Next, we will create a folder where our app will be copied to, then we can copy the contents of our app into the docker instance:

```dockerfile
# AS BEFORE

RUN mkdir ruby_test_app
COPY ./ruby_test_app ./ruby_test_app
```

Our next big task is ensuring that we have the correct gems installed on our docker container. Using containers makes our job much easier as far as gems and versioning go - we have our gems in an isolated environment so that our packages will not interfere with our other projects.

In order to tell Ruby what gems we will need installed in our project, we will use a Gemfile - this is very similar to package.json for Javascript or a pipfile for python! We can tell it where to install gems from, and also which version do we want for our gems.

In `ruby_test_app`:

```shell
touch Gemfile
```

Then let's open up our Gemfile:

```gemfile
# Set the source of gems
source 'http://rubygems.org/'

# List requirements - can specify version number
gem 'sinatra', '2.0.8.1'
```

In order to install these gems, ironically, we need a gem: specifically, Bundler. Bundler is a package/gem manager for Ruby. Once I have Bundler installed, the next step is to make sure we install al the gems listed in the gemfile.

```dockerfile
# AS BEFORE

RUN gem install bundler
RUN cd ./ruby_test_app && bundle install
```

If we rebuild our container, we can see during the building process as it installs our gems.
Now we can enter our container and, since all other required gems are installed, we can get our Sinatra app up and running!

To avoid using the different IDs for the containers, we can give them any names we want to identify them by:

```shell
docker build sinatra_demo .
```

Here we could potentially add a tag to our container if we want to with the `-t` flag- like a version number.

Once we've built it, let's enter the interactive shell:

```shell
docker run -it sinatra_demo /bin/bash
# then, inside the container:
cd ruby_test_app
ruby app.rb
```
Sinatra took the stage, 4567 is waiting for us!

However, when I try to access localhost:4567 in the browser as we saw in Ruby before, we run into some trouble: Namely, localhost cannot connect.
The reason behind this is quite simple: We do not expose any port to the outside world from the container.
We a few steps to make it work. First, we should add the following to our Dockerfile:

```dockerfile
# AS BEFORE

EXPOSE 4567
```

This does not expose it to the outside world - this is more like documentation from the devs working inside the container to the operations engineer on the outside of the container.

The second thing we want to do is instead of manually entering the container and starting our Sinatra app with the Ruby command, we should tell it to do it automatically when we ask it to build the container. We can achieve this via the CMD command - then pass it an 'array' of strings that will be executed like we are using the actual shell inside the container.

```dockerfile
# AS BEFORE

CMD ["ruby", "./ruby_test_app/app.rb"]
```

Next, we need to change something in our Sinatra app: To allow connections from the host machine into the app’s container, you’ll need to configure your app to listen on all available IP’s (0.0.0.0):

```ruby
# app.rb
require('sinatra')
require('json')

set(:bind, '0.0.0.0')
# AS BEFORE
```

Finally, we need to expose the port when we run the container after rebuilding it. We can do this with the -p flag, and by following that with the `hostPort:containerPort` format.

```shell
docker build sinatra_demo .
docker run -p 4567:4567 sinatra_demo
```

Once we done it and started running our app, we can access it finally in the browser under localhost:4567. Success!

