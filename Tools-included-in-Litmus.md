Litmus wraps functionality from other tools, providing a rake interface for you to develop modules.

* [Bolt](https://github.com/puppetlabs/bolt) - Litmus is built on top of bolt, so it natively handles SSH, WinRM and Docker. The inventory file specifies the protocol to use for each target, along with connection specific information.[Bolt](https://github.com/puppetlabs/bolt) is an open source orchestration tool that automates the manual work it takes to maintain your infrastructure. Litmus uses Bolt executes module tasks. 
* [Serverspec](https://serverspec.org/) - you can write RSpec tests for checking your servers are configured correctly.
* Puppet Development Kit (PDK) provides a complete module structure, templates for classes, defined types, and tasks, and a testing infrastructure. 
* *[Litmus Image](https://github.com/puppetlabs/litmus_image) is a group of Docker build files. They are specifically designed to setup systemd/upstart on various nix images. This is a prerequisite for testing services with Puppet in Docker images.`litmus_image` generates an inventory file, that contains connection information for each system instance. This is used by subsequnt commands or by rspec. 

These tools are built into the Litmus commands: 

#### Provision

To provision systems we created a [module](https://github.com/puppetlabs/provision) that will provision containers / images / hardware in ABS (internal to Puppet) and Docker instances. Provision is extensible, so other provisioners can be added - please raise an [issue](https://github.com/puppetlabs/provision/issues) on the Provision repository, or create your own and submit a [PR](https://github.com/puppetlabs/provision/pulls)!

rake task -> bolt -> puppet_litmus -> litmus_image -> docker
                                                   -> abs (internal)
               
                                                   -> vmpooler

#### Installing an agents

rake task -> bolt -> puppet-agent

#### Installing modules

We use the pdk to build the module tar file. It is then copied to the target using bolt. On the target machine we run a puppet module install, specifying the tar file. It will install the dependencies listed in the metadata.json of the built module.

rake task -> pdk -> bolt

#### Running tests

rake task -> serverspec -> rspec

#### Tearing down targets

rake task -> bolt -> puppet_litmus -> litmus_image -> docker
                                                   -> abs (internal)
                                                   -> vmpooler
