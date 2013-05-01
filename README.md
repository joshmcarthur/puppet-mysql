# MySQL Puppet Module for Boxen

[![Build Status](https://travis-ci.org/boxen/puppet-mysql.png)](https://travis-ci.org/boxen/puppet-mysql)

## Usage

```puppet
include mysql

mysql::db { 'mydb': }
```

## Required Puppet Modules

* boxen
* homebrew
* stdlib

## How does this differ from boxen/puppet-mysql?

Well, if you take a look at [boxen's mysql::config class](https://github.com/boxen/puppet-mysql/blob/master/manifests/config.pp), you'll see that it does something kind of funky - it changes the location of the MySQL socket file, and the default port. **Now, I get that sometime's that's what you want** - maybe you don't want to nuke an existing install, maybe you're just trying to keep everything self-contained. **I work in a team where not everybody uses Puppet though** - I don't want to force my config on others, or even just [add code to my database config files just so others can connect](https://github.com/boxen/puppet-mysql#rails). That's why this fork exists. It makes two changes:

* Change the default port to 3306
* Change the socket directory to `/tmp/mysql.sock`.

These settings are all it takes to have a boxen-installed version of MySQL that you can just go ahead and connect to. 

Personally, though, I feel like those settings should be configurable from the main [puppet-mysql repo](https://github.com/boxen/puppet-mysql) though - if you know enough of Puppet to enable this, please pull request it to those guys, and **let me know!**.


## Environment

Once installed, you can access the following variables in your environment, projects, etc:

* BOXEN_MYSQL_PORT: the configured MySQL port
* BOXEN_MYSQL_URL: the URL for MySQL, including localhost & port
* BOXEN_MYSQL_SOCKET: the path to the MySQL socket

#### Rails

In config/database.yml:

```yaml
<%
  socket = [
    ENV["BOXEN_MYSQL_SOCKET"],
    "/var/run/mysql5/mysqld.sock",
    "/tmp/mysql.sock"
  ].detect { |f| f && File.exist?(f) }

  port = ENV["BOXEN_MYSQL_PORT"] || "3306"
%>

development: &development
  adapter: mysql
  database: yourapp_development
  username: root
<% if socket %>
  host: localhost
  socket: <%= socket %>
<% else %>
  host: 127.0.0.1
  port: <%= port %>
<% end %>

# Warning: The database defined as "test" will be erased and
# re-generated from your development database when you run "rake".
# Do not set this db to the same as development or production.
test:
  <<: *development
  database: yourapp_test
```

## Developing

Write code.

Run `script/cibuild`.
