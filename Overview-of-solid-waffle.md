# Commands and structure

solid-waffle has at its core, 5 commands: 

1. [Provision: 'rake waffle:provision'](#provision)
2. [Install the agent: 'rake waffle:install_agent](#agent)
3. [Install the module: 'waffle:install_module'](#module)
4. [Run the tests: 'rake waffle:acceptance:parallel'](#test)
5. [Remove the provisioned machines: 'rake waffle:tear_down'](#teardown)

These commands allow user to create a test environment and run tests against those systems. Not all these steps are needed for every scenario.

3 likely test setups are:
* run against localhost
* run against an existing machine that has puppet installed
* provision a fresh system and install puppet 

Once you have your environment, solid waffle is designed to speed up the following workflow
edit code -> install module -> run test

At any point you can re-run tests, or provision new systems and add them to your test environment.

<a name="provision"/>

## Provisioning

Provisioning is accomplished using https://github.com/puppetlabs/waffle_provision. We can spin up docker containers, or machines in private clouds EG vmpooler. Example commands:

```
bundle exec rake 'waffle:provision[vmpooler, redhat-6-x86_64]'
bundle exec rake 'waffle:provision[docker, ubuntu:18.04]'
```

The command creates an inventory.yml file that is used by solid waffle. You can manually add machines to this file. Look here for examples https://puppet.com/docs/bolt/1.x/inventory_file.html

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

Some modules can be testing against localhost, Ie the machine you are running your test from. This can be dangerous. If you are running against localhost you can go to [Run the tests: 'rake waffle:parallel'](#test)

<a name="agent"/>

### Installing the Agent

Uses https://github.com/puppetlabs/puppetlabs-puppet_agent. Using the tasks we can install different versions of the puppet agent on many different OSes. This command can install the agent on a single target or on all targets in the inventory file (installation against multiple targets happen in parallel).
 
```
# installs the latest puppet agent on all targets
bundle exec rake "waffle:install_agent" 
# installs puppet 5 on all targets
bundle exec rake 'waffle:install_agent[puppet5]' 
# install the latest agent on a specific target
bundle exec rake 'waffle:tear_down[gn55owqktvej9fp.delivery.puppetlabs.net]' 
```

<a name="module"/>

### Installing the Module

Uses the pdk to build the module and transfer it to the target systems. You can specify a single target or run against all machines in the inventory file. (installation of the module on multiple targets happen in parallel)
 
```
bundle exec rake "waffle:install_module"
```

<a name="test"/>

### Running Tests
There are several options when it comes to running your tests at this point.

use the rake task
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
bundle exec rake waffle:acceptance:parallel 
```

You can even use Solid Waffle to run tests against your local machine by using the following command. Please note that this is only recommended if you are familiar with the code base as tests may have unexpected side effects.

```
TARGET_HOST=localhost bundle exec rspec ./spec/acceptance
```
<a name="teardown"/>

### Tearing Down Provisioned Systems

This command is used to clean up any provisioned systems, either an individual target that has been specified or if none are specified it will tear down and clean up all machines present in inventory.yaml.
 
```
bundle exec rake "waffle:tear_down"
bundle exec rake "waffle:tear_down[c985f9svvvu95nv.delivery.puppetlabs.net]"
bundle exec rake "waffle:tear_down[localhost:2222]"
```