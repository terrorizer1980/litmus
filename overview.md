## solid-waffle for the first time
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