# Welcome to the solid-waffle wiki!

This will help you through using solid-waffle

## How-to
### Example of using solid-waffle in a module

.fixtures.yml

```
---
fixtures:
  repositories:
    facts: 'git://github.com/puppetlabs/puppetlabs-facts.git'
    puppet_agent: 'git://github.com/puppetlabs/puppetlabs-puppet_agent.git'
    waffle_provision: 'git@github.com:puppetlabs/waffle_provision.git'
```

Gemfile

```
gem 'solid_waffle', git: 'git@github.com:puppetlabs/solid-waffle.git'
gem 'pdk', git: 'https://github.com/tphoney/pdk.git', branch: 'pin_cri'
```

Rakefile

```
require 'solid_waffle/rake_tasks'
```

spec/spec_helper_acceptance.rb

```
# frozen_string_literal: true

require 'serverspec'
require 'solid_waffle'
include SolidWaffle

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

## Using solid-waffle for the first time for testing
### Steps (Each step is optional, Solid waffle allows you to run acceptance tests against a machine.)

1. waffle:provision - specify number of machines / and OS, along with the mechanism eg azure / docker / vmpooler
2. waffle:install_agent 
3. install_module 
4. acceptance:all

### To clean up **all** machines
1. waffle:tear_down

### Provisioning

Provisioning is accomplished using https://github.com/puppetlabs/waffle_provision. We can spin up a VM or use docker containers, or run against the testing machine. Example command 

```
bundle bla 
```

The command creates an inventory.yml file that is used by solid waffle. You can manually add entries have a look here for examples https://puppet.com/docs/bolt/1.x/inventory_file.html

```
inventory file here
```

If testing against localhost, you can jump to running tests.

### install agent

Uses https://github.com/puppetlabs/puppetlabs-puppet_agent. Using these tasks we can install different versions of the agent on many oses. Specifically puppet 5 and 6  and ..... You can specify a single target or run against all machines in the inventory file.
 
```
bundle bla 
```

### install module

Uses the pdk to build the module and transfer it to the target systems. You can specify a single target or run against all machines in the inventory file.
 
```
bundle bla 
```
### run tests

primarily using serverspec though we can using other testing tools.
run all tests against a single machine
run all tests in parallel
run a single test from a file
 
```
bundle bla 
```

### tear down

used to clean up any provisioned systems. 
 
```
bundle bla 
```

## How to add solid waffle to a module

## How solid waffle is put together