# Welcome to the solid-waffle wiki!

Solid Waffle is a tool that helps you acceptance test your puppet content. Allowing you to test your module against a variety of OSes, and scenarios. This tool helps provision containers/images, install the Puppet agent, install a module and run tests with minimal effort. These steps have corresponding commands, empowering the user to run different workflows and scenarios dependant on their needs

This Wiki explains different workflows for different users and uses cases. 

* [Use solid-waffle with MoTD](https://github.com/puppetlabs/solid-waffle/wiki/Use-solid-waffle-with-MoTD) a quick start guide, to try solid waffle on a module that has already been converted. It allows me to try the different commands and get used to the workflow.
* [Overview of using solid waffle( basic workflow  & concepts )](https://github.com/puppetlabs/solid-waffle/wiki/Overview-of-solid-waffle) an indepth look into the solid-waffle commands, along with what options are available. eg how to install puppet 5, or how to run against localhost
* [Converting a module to use solid waffle](https://github.com/puppetlabs/solid-waffle/wiki/Converting-a-module-to-use-solid-waffle) a walkthrough of the steps necessary to convert an existing module that uses beaker, to use solid waffle as the test runner. showing what files need to be changed, and how to modify the rspec files.
* [Architecture of solid-waffle](https://github.com/puppetlabs/solid-waffle/wiki/Architecture-of-solid-waffle) An explanation of the technologies involved and what projects are used.