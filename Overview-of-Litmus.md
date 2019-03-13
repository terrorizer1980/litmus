# Commands and Structure

Litmus has 5 commands at its core:

1. [Provision: 'rake litmus:provision'](#provision)
2. [Install the agent: 'rake litmus:install_agent](#agent)
3. [Install the module: 'rake litmus:install_module'](#module)
4. [Run the tests: 'rake litmus:acceptance:parallel'](#test)
5. [Remove the provisioned machines: 'rake litmus:tear_down'](#teardown)

These commands allow user to create a test environment and run tests against those systems. Not all these steps are needed for every scenario.

3 likely test setups are:
* run against localhost
* run against an existing machine that has Puppet installed
* provision a fresh system and install Puppet 

Once you have your environment, Litmus is designed to speed up the following workflow:
edit code -> install module -> run test

At any point you can re-run tests, or provision new systems and add them to your test environment.

<a name="provision"/>

## Provisioning

Provisioning is accomplished using [provision](https://github.com/puppetlabs/provision). It's possible to spin up Docker containers, or machines in private clouds, e.g. vmpooler. Example commands:

```
bundle exec rake 'litmus:provision[vmpooler, redhat-6-x86_64]'
bundle exec rake 'litmus:provision[docker, ubuntu:18.04]'
```

Provisioning is extensible - if your chosen provisioner isn't available feel free to raise an issue on the [provision repo](https://github.com/puppetlabs/provision), or better still, raise a PR to add your chosen provisioner!

The command creates a Bolt inventory.yml file that is used by Litmus - a sample is displayed below. You can manually add machines to this file. The Bolt [documentation](https://puppet.com/docs/bolt/1.x/inventory_file.html) provides more examples.

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

Some modules can be tested against localhost, i.e. the machine you are running your test from. This can be dangerous. If you are running against localhost you can go to [Run the tests: 'rake litmus:parallel'](#test)

<a name="agent"/>

## Installing the Agent

Installing the agent on the provisioned targets is accomplished by using the [Puppet Agent module](https://github.com/puppetlabs/puppetlabs-puppet_agent). Using the tasks in this module we can install different versions of the Puppet Agent on many different OSes. This command can install the agent on a single target or on all targets in the inventory file. Agents are installed in parallel when running against multiple targets.
 
The sample commands below illustrate how to install the Puppet Agent on targets.
```
# Installs the latest Puppet Agent on all targets
bundle exec rake "litmus:install_agent" 

# Installs Puppet 5 on all targets
bundle exec rake 'litmus:install_agent[puppet5]' 

# Install the latest Puppet Agent on a specific target
bundle exec rake 'litmus:install_agent[gn55owqktvej9fp.delivery.puppetlabs.net]' 
```

<a name="module"/>

## Installing the module under test

The module under test is installed on the target by Litmus using the PDK. You can specify a single target or run against all targets in the inventory file. Modules are installed in parallel when running against multiple targets.

The command below illustrate how to install your module under test on targets.
```
bundle exec rake "litmus:install_module"
```

<a name="test"/>

## Running Tests
There are several options when it comes to running your tests at this point. Litmus primarily uses [serverspec](https://serverspec.org/) though other testing tools can be used.

It is possible to:
* run all tests against a single target;
* run all tests against all targets in parallel; or
* run a single test against a single target.

The example below shows how to run all tests against a single target:

```
TARGET_HOST=lk8g530gzpjxogh.delivery.puppetlabs.net bundle exec rspec ./spec/acceptance
TARGET_HOST=localhost:2223 bundle exec rspec ./spec/acceptance
```

The example below shows how to run a specific test against a single target:

```
TARGET_HOST=lk8g530gzpjxogh.delivery.puppetlabs.net bundle exec rspec ./spec/acceptance/test_spec.rb:21
TARGET_HOST=localhost:2223 bundle exec rspec ./spec/acceptance/test_spec.rb:21
```

This example below shows how to run all tests against all targets, as specified in the inventory.yml file.
 
```
bundle exec rake litmus:acceptance:parallel 
```

The example below shows how to run all tests against localhost. Please note that this is only recommended if you are familiar with the code base as tests may have unexpected side effects on your local machine.

```
TARGET_HOST=localhost bundle exec rspec ./spec/acceptance
```
<a name="teardown"/>

## Tearing Down Provisioned Systems

It's good practice to clean up after running tests. This command is used to clean up any provisioned systems, either an individual target that has been specified or if none are specified it will tear down and clean up all machines present in inventory.yml.

The sample commands below illustrate how to tear down targets after testing.
```
# Tear down all targets in inventory.yml
bundle exec rake "litmus:tear_down"

# Tear down a specific target vm
bundle exec rake "litmus:tear_down[c985f9svvvu95nv.delivery.puppetlabs.net]"

# Tear down a specific target running locally
bundle exec rake "litmus:tear_down[localhost:2222]"
```