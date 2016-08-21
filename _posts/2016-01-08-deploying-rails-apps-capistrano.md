---
layout: post
title: Deploying Rails Apps using Capistrano
date: 2016-01-08 20:34:26
image: '/assets/img/'
description: A part from using Deploying Web Application it can also be use for Server Automation. It could be good if you don't need the overhead something like Chef or Puppet.
main-class: deployment
color: '#7AAB13'
tags:
- Deployment
- Ruby on Rails
categories:
twitter_text: 'DO ONE THING, DO IT WELL'
introduction: 'A remote server automation and deployment tool written in Ruby.'
---

If you are familiar with the `rake tool` and creating `rake task` you will be right home of `Capistrano` but even if you have't It very easy to pick-up.

> `Capistrano` is the `de-facto deployment` tool for Rails apps; it makes deployment easier by automating a lot of the work for you, and it can be easily customized to suit your particular needs. If you’ve never used it before, I hope this post will give you a taste of what it can do.


#### Confugring Capistrano
One of the ways the core team was able to keep it so lean was by breaking framework-specific tasks into separate gems, which means that in addition to installing the Capistrano gem itself, we’ll need to install Rails-specific gems as well.
Here is what you should add to your `Gemfile`.

{% highlight ruby %}
group :development do
  gem 'capistrano'
  gem 'capistrano-rails'
  gem 'capistrano-bundler'
end
{% endhighlight %}

And obviously run `bundle` or `bundle install` command to install it.

#### General Configuration
we’ll tell Capistrano to create the necessary files it needs to do its job by running the `cap install` command.
One of the files this created is called `Capfile`, which will be located in the root directory of your Rails app. 
It’ll contain various require statements to load the necessary code that Capistrano will need to do its job. We’ll open it up and uncomment the following lines to load the gems we just installed.

{% highlight ruby %}
## Capfile
require 'capistrano/setup'
require 'capistrano/deploy'
require 'capistrano/bundler'
require 'capistrano/rails/assets'
require 'capistrano/rails/migrations'
Dir.glob('lib/capistrano/tasks/*.rake').each { |r| import r }
{% endhighlight %}

#### Its time to `Roll up Your Sleeves`

#### open Config/deploy.rb
This file will be `shared/common` across the application environment.
                             
{% highlight ruby %}
set :application, 'your_app'  ## keep in mind that your app dir name will be your_app  
set :repo_url, 'git@bitbucket.org:somerepo/code.git'
set :branch, 'master'
set :use_sudo, true
set :deploy_to, '/public_html/test'
set :linked_files, fetch(:linked_files, []).push('config/database.yml')
set :linked_dirs, fetch(:linked_dirs, []).push('bin', 'log', 'tmp/pids', 'tmp/cache', 'tmp/sockets', 'vendor/bundle', 'public/system')

namespace :deploy do
  after :restart, :clear_cache do
    on roles(:web), in: :groups, limit: 3, wait: 10 do
      # Here we can do anything such as:
      # within release_path do
      #   execute :rake, 'cache:clear'
      # end
    end
  end
end
{% endhighlight %}


#### Stage-Specific Configuration

{% highlight ruby %}
## config/deploy/production.rb
set :stage, :production
set :rails_env, :production
server 'xxx.xxx.xxx.xxx', user: 'bob', port: 12345, roles: %w{web app db}, primary: true
{% endhighlight %}


#### Setting Permission - will be right back
 
{% highlight ruby %}

{% endhighlight %}