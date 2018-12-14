# Welcome to the solid-waffle wiki!

This will help you through using solid-waffle

## Example of using solid-waffle in a module

```
bla 
bla bla 
bla
copy from the front page
```

## Using solid-waffle for the first time for testing

Solid waffle allows you to run acceptance/integration tests against a module. 

Basic workflow:
1. provision
2. install agent
3. install module
4. run tests
5. tear down

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