# Welcome to the solid-waffle wiki!

This will help you through using solid-waffle

## Using solid-waffle for the first time for testing

Solid waffle allows you to run acceptance/integration tests against a module.

Basic workflow:
1. provision
2. install agent
3. install module
4. run tests
5. tear down

### Provisioning

Provisioning is accomplished using https://github.com/puppetlabs/waffle_provision. We can spin up a VM or use docker containers, or run against the testing machine. Example command 

```
bundle bla 
```

### install agent

Uses https://github.com/puppetlabs/puppetlabs-puppet_agent. Using these tasks we can install different versions of the agent on many oses. Specifically puppet 5 and 6  and .....
 
```
bundle bla 
```

## How to add solid waffle to a module

## How solid waffle is put together