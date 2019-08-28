# Architecture of Litmus

Litmus tries to use existing technologies as much as possible. It relies heavily on [bolt](https://puppet.com/products/puppet-bolt) and [serverspec](https://serverspec.org/).

## Projects used

Litmus wraps functionality from other projects providing a simple rake interface for module developers.

At its core we use bolt to execute module tasks. To provision systems we created a [module](https://github.com/puppetlabs/provision) that will provision containers / images / hardware in ABS (internal to Puppet) and Docker instances. Provision is extensible, so other provisioners can be added - please raise an [issue](https://github.com/puppetlabs/provision/issues) on the Provision repository, or create your own and submit a [PR](https://github.com/puppetlabs/provision/pulls)!

Litmus also generates an inventory file, that contains connection information for each system instance. This is used by subsequent commands or by rspec. [Litmus Image](https://github.com/puppetlabs/litmus_image) is a group of Docker build files. They are specifically designed to setup systemd/upstart on various nix images. This is a prerequisite for testing services with Puppet in Docker images.

https://github.com/puppetlabs/bolt

https://github.com/puppetlabs/provision

https://github.com/puppetlabs/litmus_image

## Technologies / workflow for Litmus commands
Below is an outline of the underlying technology that supports each of Litmus' capabilities.

### Provision
rake task -> bolt -> puppet_litmus -> litmus_image -> docker
                                                   -> abs (internal)
                                                   -> vmpooler

### Install an agent

rake task -> bolt -> puppet-agent

### Install module
We use the pdk to build the module tar file. It is then copied to the target using bolt. On the target machine we run a puppet module install, specifying the tar file. It will install the dependencies listed in the metadata.json of the built module.

rake task -> pdk -> bolt

### Run tests

rake task -> serverspec -> rspec

### Tear down

rake task -> bolt -> puppet_litmus -> litmus_image -> docker
                                                   -> abs (internal)
                                                   -> vmpooler

## Protocols used

Litmus is built on top of bolt, so it natively handles SSH, WinRM and Docker. The inventory file specifies the protocol to use for each target, along with connection specific information.