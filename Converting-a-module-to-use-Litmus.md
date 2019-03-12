# Converting a module to use Litmus using .sync.yml

This tutorial will walk you through how to convert a module to use Litmus for acceptance testing.

## Pre-requisites
Ensure that the module is compatible with the [Puppet Development Kit](https://puppet.com/docs/pdk/1.x/pdk.html). This means it was either created with the PDK, or has been converted to use the PDK - by using the `pdk convert` command.

To check if an existing module is compatible with the PDK, look in the modules `metadata.json` file and check that there is an entry which states the PDK version. It should read something like: `"pdk-version": "1.9.0"`

## Update files
To use Litmus in a module you first need to update the following files to include the specified code.

### .fixtures.yml
Add the following lines to your `.fixtures.yml` file in the root directory of your module.
```
---
fixtures:
  repositories:
    facts: 'git://github.com/puppetlabs/puppetlabs-facts.git'
    puppet_agent: 'git://github.com/puppetlabs/puppetlabs-puppet_agent.git'
    provision: 'git@github.com:puppetlabs/provision.git'
```
Make the following changes to your `.sync.yml` file
### .sync.yml

```
 Rakefile:
   requires:
   use_litmus_tasks: true

Gemfile:
  required:
    ':development':
      - gem: 'net-ssh'
        version: '< 5.0.0'
      - gem: 'puppet_litmus'
        git: 'https://github.com/puppetlabs/puppet_litmus.git'
      - gem: 'pdk'
        git: 'https://github.com/tphoney/pdk.git'
        branch: 'pin_cri'
      - gem: 'serverspec'
```

When you run `pdk update` the changes made in your `.sync.yml` file will be applied to your `Rakefile` and `Gemfile`.

### spec/spec_helper_acceptance.rb
Add the following to the `spec_helper_acceptance.rb` file. This is an acceptance testing file that you will find in the `spec` folder of your module. If it doesn't exist then it means your module doesn't have any acceptance tests, and you will need to add some. However, for the purposes of this tutorial, simply create the file with the following content. We will add a tutorial on how to write acceptance tests and link from here when it's ready.
```
# frozen_string_literal: true

require 'serverspec'
require 'puppet_litmus'
include PuppetLitmus

if ENV['TARGET_HOST'].nil? || ENV['TARGET_HOST'] == 'localhost'
  puts 'Running tests against this machine !'
  if Gem.win_platform?
    set :backend, :cmd
  else
    set :backend, :exec
  end
else
  puts "TARGET_HOST #{ENV['TARGET_HOST']}"
  # load inventory
  inventory_hash = inventory_hash_from_inventory_file
  node_config = config_from_node(inventory_hash, ENV['TARGET_HOST'])

  if target_in_group(inventory_hash, ENV['TARGET_HOST'], 'ssh_nodes')
    set :backend, :ssh
    options = Net::SSH::Config.for(host)
    options[:user] = node_config.dig('ssh', 'user') unless node_config.dig('ssh', 'user').nil?
    options[:port] = node_config.dig('ssh', 'port') unless node_config.dig('ssh', 'port').nil?
    options[:password] = node_config.dig('ssh', 'password') unless node_config.dig('ssh', 'password').nil?
    host = if ENV['TARGET_HOST'].include?(':')
             ENV['TARGET_HOST'].split(':').first
           else
             ENV['TARGET_HOST']
           end
    set :host,        options[:host_name] || host
    set :ssh_options, options
  elsif target_in_group(inventory_hash, ENV['TARGET_HOST'], 'winrm_nodes')
    require 'winrm'

    set :backend, :winrm
    set :os, family: 'windows'
    user = node_config.dig('winrm', 'user') unless node_config.dig('winrm', 'user').nil?
    pass = node_config.dig('winrm', 'password') unless node_config.dig('winrm', 'password').nil?
    endpoint = "http://#{ENV['TARGET_HOST']}:5985/wsman"

    opts = {
      user: user,
      password: pass,
      endpoint: endpoint,
      operation_timeout: 300,
    }

    winrm = WinRM::Connection.new opts
    Specinfra.configuration.winrm = winrm
  end
end
```

# Converting a module to use Litmus by making manual file changes

This tutorial will give you a step by step guide on how to manually convert a module to use litmus.

### Gemfile
Add the following lines to your `Gemfile` in the `group :development` section. You will find this file in the root directory of your module.
```
gem 'puppet_litmus', git: 'https://github.com/puppetlabs/puppet_litmus.git'
gem 'pdk', git: 'https://github.com/tphoney/pdk.git', branch: 'pin_cri'
```

### Rakefile
Add the following line to the **top** of your `Rakefile`. You will find this file in the root directory of your module. 
```
require 'puppet_litmus/rake_tasks'
```

### spec/spec_helper_acceptance.rb
Add the following to the `spec_helper_acceptance.rb` file. This is an acceptance testing file that you will find in the `spec` folder of your module. If it doesn't exist then it means your module doesn't have any acceptance tests, and you will need to add some. However, for the purposes of this tutorial, simply create the file with the following content. We will add a tutorial on how to write acceptance tests and link from here when it's ready.
```
# frozen_string_literal: true

require 'serverspec'
require 'puppet_litmus'
include PuppetLitmus

if ENV['TARGET_HOST'].nil? || ENV['TARGET_HOST'] == 'localhost'
  puts 'Running tests against this machine !'
  if Gem.win_platform?
    set :backend, :cmd
  else
    set :backend, :exec
  end
else
  puts "TARGET_HOST #{ENV['TARGET_HOST']}"
  # load inventory
  inventory_hash = inventory_hash_from_inventory_file
  node_config = config_from_node(inventory_hash, ENV['TARGET_HOST'])

  if target_in_group(inventory_hash, ENV['TARGET_HOST'], 'ssh_nodes')
    set :backend, :ssh
    options = Net::SSH::Config.for(host)
    options[:user] = node_config.dig('ssh', 'user') unless node_config.dig('ssh', 'user').nil?
    options[:port] = node_config.dig('ssh', 'port') unless node_config.dig('ssh', 'port').nil?
    options[:password] = node_config.dig('ssh', 'password') unless node_config.dig('ssh', 'password').nil?
    host = if ENV['TARGET_HOST'].include?(':')
             ENV['TARGET_HOST'].split(':').first
           else
             ENV['TARGET_HOST']
           end
    set :host,        options[:host_name] || host
    set :ssh_options, options
  elsif target_in_group(inventory_hash, ENV['TARGET_HOST'], 'winrm_nodes')
    require 'winrm'

    set :backend, :winrm
    set :os, family: 'windows'
    user = node_config.dig('winrm', 'user') unless node_config.dig('winrm', 'user').nil?
    pass = node_config.dig('winrm', 'password') unless node_config.dig('winrm', 'password').nil?
    endpoint = "http://#{ENV['TARGET_HOST']}:5985/wsman"

    opts = {
      user: user,
      password: pass,
      endpoint: endpoint,
      operation_timeout: 300,
    }

    winrm = WinRM::Connection.new opts
    Specinfra.configuration.winrm = winrm
  end
end
```

## Converting tests

There are a number of helper functions that litmus provides, that can be used in your tests. [helper functions for tests](https://github.com/puppetlabs/puppet_litmus/wiki/Helper-Functions-for-Litmus#helper-functions-for-testing-your-modules) these are similar to the functions provided previously by the beaker language. 

For example the apply_manifest function is a like for like replacement. 