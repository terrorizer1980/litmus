# Architecture of solid-waffle

Solid-waffle tries to use existing technologies as much as possible. It relies heavily on bolt and serverspec.

## Projects used

Solid-waffle wraps functionality from other projects providing a simple rake interface to module developers. 
At its core we use bolt to execute module tasks. To provisions systems we use the a module. waffle_provision is a module that provisions containers / images / hardware in abs (internal to puppet) and docker instances. It also generates an inventory file, that contains connection information for that instance. This is used by subsequent commands or by rspec. 
waffle_image is a group of docker build files. They are specifically designed to setup systemd/upstart on various nix images. This is a prerequisite for testing services with puppet in docker images.

https://github.com/puppetlabs/bolt
https://github.com/puppetlabs/waffle_provision
https://github.com/puppetlabs/waffle_image

## Technologies / workflow for solid waffle commands

### provision
rake task -> bolt -> waffle-provision -> waffle-image -> docker
                                      -> abs (internal)
                                      -> vmpooler

### install an agent

rake task -> bolt -> puppet-agent

### install module

rake task -> pdk -> bolt

### run tests

rake task -> serverspec -> rspec

### tear down

rake task -> bolt -> waffle-provision -> waffle-image -> docker
                                      -> abs (internal)
                                      -> vmpooler