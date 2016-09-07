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


#### Groups and Privileges

Now that you’re in, the first thing we’ll do is change the __password__ for root using the following command:
{% highlight ruby %}
passwd
{% endhighlight %}

This will prompt you to enter a new password twice. Next, we’ll create a new group called `deployers`, which will allow us to easily manage the users with `deployment privileges`.
{% highlight ruby %}
groupadd deployers
{% endhighlight %}

Now we’ll create a new user called `bob`, and assign him to the `deployers group` we just created above:
{% highlight ruby %}
adduser bob —ingroup deployers
{% endhighlight %}

This command will prompt you to enter a `password for this user`, in addition to some other information afterwards, but after you enter the password twice, feel free to just press “Enter” for the other fields, as they’re not strictly necessary. By the way, don’t use the same password for both root and the user you just created above or bad things will happen.

Next thing we’ll do is open the __sudoers__ file containing a list of users and groups who have `root privileges`:

{% highlight ruby %}
vi /etc/sudoers
%deployers      ALL=(ALL) ALL
{% endhighlight %}

Running commands while logged in as `root` is considered bad practice because, as the `superuser`, root can run any and all commands, and since there is no undo functionality in Unix, one accidental bad command and your system can be seriously disrupted. That’s why we created a separate user called bob, which will have `deployment privileges` and nothing else.

But why did we create a `deployers` group and added `bob` into it? Well, first of all, we could’ve avoided creating a group altogether and just added bob to the sudoers file and given him root privileges instead. But let’s say I’m working on a project with a friend and she wants to be able to deploy as well. I would have to then add her to the sudoers file too (to give her root privileges), and the file would keep growing every time a new user with `deployment privileges` needed to be added. This would be a nightmare to maintain.

A better way to go about this is to create a group called deployers, give the group root privileges, and then add users to this group. This way, whenever I’d need to add new users with deployment privileges, I would just need to add them to the deployers group. This keeps the sudoers file clean and organized, while allowing me to easily manage the members of the group as well. I could, for example, easily revoke some rights for all members of the deployers group at the same time, instead of doing it one user at a time, or I could simply remove a user from the deployers group if I discover, for example, that he still creates “1234” passwords for his accounts.

Okay, but why is it necessary for users and groups to have root privileges? Well, these privileges allow a user, say bob, to run commands he otherwise would not be able to run due to not having the necessary permissions, which arises from the fact that the user is not root and therefore has limited privileges. But given root privileges, or being part of a group with root privileges, enables bob to run these commands simply by preceding the command with sudo. He’ll then be prompted to enter his password, and the command will run.

That’s the reasoning behind giving the `deployers group root privileges` and adding `bob` into it. Later on, `bob` will need these privileges during the deployment process.

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


#### Setting Permission
If you look at deploy.rb, you’ll notice I set the :deploy_to variable to “/var/www/phindee,” but on my VPS, the /var directory doesn’t yet contain the /www directory. That’s not a problem since Capistrano will create it for me through the user bob, as specified in deploy.rb, but it needs write permissions to do so.

you’ll remember we created a group called deployers to contain users with deployment privileges and added the user bob into it. This means we can give the necessary permissions bob will need by simply giving them to deployers, and since bob is a member of the group, he will automatically inherit them.

I’m already logged in to my VPS as bob, and I can change the /var directory’s group to deployers with the following command:


{% highlight ruby %}
sudo chgrp deployers /var
sudo chmod g+w /var

sudo chgrp deployers /etc/nginx/sites-enabled
sudo chmod g+w /etc/nginx/sites-enabled

sudo chgrp deployers /etc/init.d
sudo chmod g+w /etc/init.d
{% endhighlight %}

And now Capistrano should have the necessary permissions to do its work.