## Converting a module to use puppet_litmus

To use puppet_litmus in a module you first need to update the following files to include the specified code:

.fixtures.yml

```
---
fixtures:
  repositories:
    facts: 'git://github.com/puppetlabs/puppetlabs-facts.git'
    puppet_agent: 'git://github.com/puppetlabs/puppetlabs-puppet_agent.git'
    provision: 'git@github.com:puppetlabs/provision.git'
```

Gemfile

```
gem 'puppet_litmus', git: 'git@github.com:puppetlabs/puppet_litmus.git'
gem 'pdk', git: 'https://github.com/tphoney/pdk.git', branch: 'pin_cri'
```

Rakefile

```
require 'puppet_litmus/rake_tasks'
```

spec/spec_helper_acceptance.rb

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