# Welcome to the solid-waffle wiki!

Solid Waffle is a tool that helps you acceptance test your puppet content. Allowing you to test your module against a variety of OSes, and scenarios. This tool helps provision containers/images, install the Puppet agent, install a module and run tests with minimal effort.
These steps are are a reflected in a series of rake tasks. This Wiki explains different workflows for different users and uses cases. 
* Use solid-waffle with MoTD ( test drive )
* Using solid waffle for the first time ( basic workflow  & concepts )
* Converting a module to use solid waffle
* Architecture of solid-waffle

## Use solid-waffle with MoTD

Goal: At the end of this you will have checked out MoTD module. Provisioned a centos docker image. Installed puppet 6 agent on the centos image. Installed the MoTD module on the centos image. Ran the MoTD acceptance tests.

Pre-requisites: A ruby environment 2.3 preferably. Docker installed and working. Git installed.

```
git clone git@github.com:puppetlabs/puppetlabs-motd.git
cd puppetlabs-motd
git remote add tphoney git@github.com:tphoney/puppetlabs-motd.git
git rebase tphoney/solid-waffle
bundle install --path .bundle/gems/
bundle exec rake 'waffle:provision[vmpooler, centos-7-x86_64]'
bundle exec rake 'waffle:provision[vmpooler, win-2012r2-x86_64]'

bundle exec rake waffle:install_agent
bundle exec rake waffle:install_module

# run tests in parallel
bundle exec rake acceptance:all -j10 -m 

# return images to pool
bundle exec rake waffle:tear_down
```

## Using solid-waffle for the first time
### Steps (Each step is optional, Solid waffle allows you to run acceptance tests against a machine.)

1. waffle:provision - specify number of machines / and OS, along with the mechanism eg azure / docker / vmpooler
2. waffle:install_agent 
3. waffle:install_module 
4. acceptance:all

### To clean up **all** machines
1. waffle:tear_down

### Provisioning

Provisioning is accomplished using https://github.com/puppetlabs/waffle_provision. We can spin up a VM with vmpooler, use docker containers, or run against the testing machine. Example commands:

```
bundle exec rake 'waffle:provision[vmpooler, redhat-6-x86_64]'
bundle exec rake 'waffle:provision[docker, ubuntu:18.04]'
```

The command creates an inventory.yml file that is used by solid waffle. You can manually add entries have a look here for examples https://puppet.com/docs/bolt/1.x/inventory_file.html

```
---
groups:
- name: ssh_nodes
  nodes:
  - name: c985f9svvvu95nv.delivery.puppetlabs.net
    config:
      transport: ssh
      ssh:
        host-key-check: false
        user: root
    facts:
      provisioner: vmpooler
  - name: e0qmdc9v5opg1tq.delivery.puppetlabs.net
    config:
      transport: ssh
      ssh:
        host-key-check: false
        user: root
    facts:
      provisioner: vmpooler
- name: winrm_nodes
  nodes: []
```

If testing against localhost, you can jump to running tests.

### Installing the Agent

Uses https://github.com/puppetlabs/puppetlabs-puppet_agent. Using these tasks we can install different versions of the agent on many OSes. Specifically puppet 5 and 6  and ..... You can specify a single target or run against all machines in the inventory file.
 
```
bundle exec rake "waffle:install_agent"
```

### Installing the Module

Uses the pdk to build the module and transfer it to the target systems. You can specify a single target or run against all machines in the inventory file.
 
```
bundle exec rake "waffle:install_module"
```

### Running Tests
There are several options when it comes to running your tests at this point.


primarily using serverspec though we can using other testing tools.
run all tests against a single machine
run all tests in parallel
run a single test from a file

Solid Waffle allows you to specify a target to run tests against, like so:

```
TARGET_HOST=lk8g530gzpjxogh.delivery.puppetlabs.net bundle exec rspec ./spec/acceptance
TARGET_HOST=localhost:2223 bundle exec rspec ./spec/acceptance
```

This command runs all tests in parallel against all provisioned machines present inside inventory.yaml.
 
```
bundle exec rake acceptance:all -j10 -m 
```

You can even use Solid Waffle to run tests against your local machine by using the following command. Please note that this is only recommended if you are familiar with the code base as tests may have unexpected side effects.

```
TARGET_HOST=localhost bundle exec rspec ./spec/acceptance
```


### Tearing Down Provisioned Systems

This command is used to clean up any provisioned systems, either an individual target that has been specified or if none are specified it will tear down and clean up all machines present in inventory.yaml.
 
```
bundle exec rake "waffle:tear_down"
bundle exec rake "waffle:tear_down[c985f9svvvu95nv.delivery.puppetlabs.net]"
bundle exec rake "waffle:tear_down[localhost:2222]"
```

## How to add solid waffle to a module

To use solid waffle in a module you first need to update the following files to include the specified code:

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

## Architecture of solid-waffle