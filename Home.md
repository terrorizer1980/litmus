# Welcome to the Litmus wiki!

Litmus is a tool that helps you acceptance test Puppet modules. It facilitates testing a module against a variety of OSes, and deployment scenarios. This tool helps provision test platforms such as containers/images, install the Puppet agent, install a module and run tests with minimal effort. These commands empower the user to run different workflows quickly and interactively.

The following topics explains different user workflows and a high level overview. 

* [Overview of using Litmus (basic workflow  & concepts)](https://github.com/puppetlabs/puppet_litmus/wiki/Overview-of-Litmus) an in-depth look into the Litmus commands, along with what options are available, e.g. how to install Puppet 5, or how to run against localhost.
* [Tutorial: use Litmus with MoTD](https://github.com/puppetlabs/puppet_litmus/wiki/Tutorial:-use-Litmus-to-execute-acceptance-tests-with-a-sample-module-(MoTD)). This is a quick start guide, to try Litmus on a module that has already been converted. It allows you to try the different commands and get used to the workflow.
* [Converting a module to use Litmus](https://github.com/puppetlabs/puppet_litmus/wiki/Converting-a-module-to-use-Litmus). Provides a walkthrough of the steps necessary to convert an existing module that uses [Beaker](https://github.com/puppetlabs/beaker), to use Litmus as the test runner. This shows what files need to be changed, and how to modify the rspec files.
* [Architecture of Litmus](https://github.com/puppetlabs/puppet_litmus/wiki/Architecture-of-puppet-litmus). An explanation of the technologies involved and what projects are used.
* A wiki listing functions that are now available when using Litmus.