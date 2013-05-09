# Heroku Buildpack: nginx/unicorn

Nginx-unicorn-buildpack vendors nginx inside a dyno and connects unicorn to nginx over a UNIX domain socket. Both unicorn and nginx logs are printed to stdout using [l2met](https://github.com/ryandotsmith/l2met) conventions.

## Prerequisites

* Unicorn listens on /tmp/nginx.socket
* Unicorn configuration located at RAILS_ROOT/config/unicorn.rb

Sample Unicorn Configuration:

```ruby
preload_app true
timeout 5
worker_processes 4
listen '/tmp/nginx.socket', backlog: 8
```

## Setup

```bash
$ mkdir myapp; cd myapp
$ git init
```

**Gemfile**
```ruby
source 'https://rubygems.org'
gem 'unicorn'
```

**config.ru**
```ruby
run Proc.new {[200,{'Content-Type' => 'text/plain'}, ["hello world"]]}
```

**config/unicorn.rb**
```ruby
preload_app true
timeout 5
worker_processes 4
listen '/tmp/nginx.socket', backlog: 8
```

Create & Push Heroku App:
```bash
$ heroku create --buildpack https://github.com/ddollar/heroku-buildpack-multi.git
$ echo 'https://github.com/heroku/heroku-buildpack-ruby.git' >> .buildpacks
$ echo 'https://github.com/ryandotsmith/buildpack-nginx-unicorn.git' >> .buildpacks
$ git push heroku master
$ heroku logs -t
```

Visit App
```
$ heroku open
```
