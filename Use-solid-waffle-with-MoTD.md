# Goal: 

At the end of this you will have:

1. Checked out MoTD module from git. 
1. Provisioned a centos docker image. 
1. Installed puppet 6 agent on the centos image. 
1. Installed the MoTD module on the centos image. 
1. Ran the MoTD acceptance tests.
1. Remove the docker image.

## Pre-requisites: 

1. A ruby environment 2.3 preferably. 
1. Docker installed and working. (ie 'docker run centos:7 ls' runs without error)
1. Git installed and working.
1. A Github api token for checking out the repository.

## Instructions

Checkout the solid-waffle branch of MoTD and install the gems

```
git clone git@github.com:puppetlabs/puppetlabs-motd.git
cd puppetlabs-motd
git rebase origin/solid-waffle
```

Setting the GITHUB_TOKEM step is temporary, until we make the repository open. Then install the gems.

```
export GITHUB_TOKEN=<MAGIC HERE>
bundle install --path .bundle/gems/
```

Provision the centos 7 image

```
bundle exec rake 'waffle:provision[docker, centos-7-x86_64]'
```

Install the puppet 6 agent on our centos image

```
bundle exec rake waffle:install_agent
```

Build the MoTD module and install it on the centos image.

```
bundle exec rake waffle:install_module
```

run the tests

```
bundle exec rake acceptance:all -j10 -m 
```

remove the docker container

```
bundle exec rake waffle:tear_down
```

## Next steps

* Try provisioning more than one system. EG, 'bundle exec rake 'waffle:provision[docker, ubuntu:16.04]''
* Look at the inventory file. inventory.yaml
* ssh into the centos box 'ssh -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no root@localhost -p 2222'