# Goal: 

This is a tutorial that will walk you through how to run acceptance tests on a sample module using Litmus. the module we'll test is [MoTD](https://github.com/puppetlabs/puppetlabs-motd).

At the end of this you will have:

1. Checked out MoTD module from GitHub.
1. Provisioned a CentOS Docker image. 
1. Installed a Puppet 6 Agent on the CentOS image. 
1. Installed the MoTD module on the CentOS image. 
1. Executed all the MoTD acceptance tests.
1. Removed the Docker image.

## Pre-requisites: 

1. A ruby environment 2.3 is preferred. Type `ruby --version` in a terminal to check the version you're running.
1. Docker installed and working. type `docker --version` in a terminal to check that Docker is installed. If you need to install Docker go [here](https://runnable.com/docker/getting-started/). To check Docker is working as expected type `docker run centos:7 ls` in a terminal. This should list folders in the CentOS image.
1. Git installed and working. Type `git --version` to check if Git is installed. If not, then go [here](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) and follow the instructions for your host OS.
1. The [Puppet Development Kit (PDK)](https://puppet.com/docs/pdk/1.x/pdk.html) installed. Type `pdk --version` from the command line to ensure that the PDK is installed. If not, then following the [instructions](https://puppet.com/docs/pdk/1.x/pdk_install.html) to install it.
2. [Bolt](https://puppet.com/docs/bolt) installed. Type `bolt --version` from the command line to ensure that the Bolt is installed. If not, then following the [instructions](https://puppet.com/docs/bolt/latest/bolt_installing.html#concept-8499) to install it

## Instructions

### Checkout the Litmus branch of MoTD
Run the following command in a terminal to get a local copy of the module.

```
> git clone https://github.com/puppetlabs/puppetlabs-motd.git
```
This will bring a local copy of the module to your machine. Now you will rebase to the Litmus branch.
```
# Change directory to the module you just brought down
> cd puppetlabs-motd
> git checkout puppet_litmus
```

### Install the necessary gems for the module.

The module relies on a number of gems. To bring those to your machine type the following command from your terminal
```
> bundle install --path .bundle/gems/
```

### Provision a target to test against
For the purposes of this tutorial we will provision a single CentOS 7 image in a Docker container as the target to test against. Additional targets can be added by running the command below for whichever OSes you like. Remember, provisioning is extensible, so if your preferred provisioner is missing, please let us know by raising an issue on the [provision repo](https://github.com/puppetlabs/provision/issues), or just add your own and submit a [PR](https://github.com/puppetlabs/provision/pulls) - we love to get community contributions!

Type the following command in your terminal to provision the CentOS 7 target.

```
> bundle exec rake 'litmus:provision[docker, centos:7]'
```
There will be a lot of output in your terminal, but the last lines should be as follows:
```
Provisioning centos_7-2222
{"status":"ok","node_name":"localhost"}
```
To double check that all is OK, run the following: `docker ps` and you should see output similar to below
```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                  NAMES
7b12b616cf65        centos:7            "/bin/bash"         4 minutes ago       Up 4 minutes        0.0.0.0:2222->22/tcp   centos_7-2222
```

#### Inventory (bolt inventory)
At this stage, it's worth pointing out that the provisioned targets will be in the inventory.yaml file. Litmus creates this file in your working directory. Therefore, if you type `cat inventory.yaml` it should display the targets you just created. For the above example, the following should appear:
```
> cat inventory.yaml
---
groups:
- name: ssh_nodes
  nodes:
  - name: localhost:2222
    config:
      transport: ssh
      ssh:
        user: root
        password: root
        port: 2222
        host-key-check: false
    facts:
      provisioner: docker
      container_name: centos_7-2222
- name: winrm_nodes
  nodes: []
  ```

### Install the Puppet Agent on your target
The next step is to install the latest Puppet Agent on the CentOS Docker image. Run the following in your terminal.
```
> bundle exec rake litmus:install_agent
```

#### Validating with Bolt
This will just output a single line with `install_agent`. To verify that the agent installed successfully on the target you can use bolt to check (you could also just use SSH)! Simply type the following command to verify that the agent is installed:
```
> bolt command run 'puppet --version' -n localhost:2222 -i inventory.yaml
```
where, `localhost:2222` is the name of the node as taken from the inventory.yaml file in your current working directory. You should receive output to below, displaying the version of the Puppet Agent that was installed:
```
> bolt command run 'puppet --version' -n localhost:2222 -i inventory.yaml
Started on localhost...
Finished on localhost:
  STDOUT:
    6.3.0
Successful on 1 node: localhost:2222
Ran on 1 node in 0.92 seconds
```

### Install the module on your target
OK, now it's time to install the MoTD module on the target in order for the tests to be executed. This needs the prerequisite of the PDK to be installed. Run the following command in your terminal. Please note that you need to be in your working directory for the module and the module should have created by the PDK, or have been converted to use the PDK.

```
> bundle exec rake litmus:install_module
```
You should receive output similar to below:
```
Built
Installed
```
To check if the module did install, again we can use bolt. Run the puppet module list command against the target

#### validating with Bolt

```
> bolt command run 'puppet module list' -n localhost:2222 -i inventory.yaml
```
The output should be similar to what's below, with the MoTD module showing as installed (along with its dependent modules).
```
> bolt command run 'puppet module list' -n localhost:2222 -i inventory.yaml
Started on localhost...
Finished on localhost:
  STDOUT:
    /etc/puppetlabs/code/environments/production/modules
    ├── puppetlabs-motd (v2.1.2)
    ├── puppetlabs-registry (v2.1.0)
    ├── puppetlabs-stdlib (v5.2.0)
    └── puppetlabs-translate (v1.2.0)
    /etc/puppetlabs/code/modules (no modules installed)
    /opt/puppetlabs/puppet/modules (no modules installed)
Successful on 1 node: localhost:2222
Ran on 1 node in 1.11 seconds
```

### Run the tests
So far we've provisioned the target, installed the Puppet Agent, and installed the relevant module. Now, it's time to run the tests. Run the following command from your working directory.
```
> bundle exec rake litmus:acceptance:parallel
```
This will execute the acceptance tests in the [acceptance folder](https://github.com/puppetlabs/puppetlabs-motd/tree/master/spec/acceptance) of the module. If the tests have run successfully, you will see output similar to below.
```
> bundle exec rake litmus:acceptance:parallel
Running against 1 machines |Time: 00:00:48 | ======================================================================================== | Time: 00:00:48
......

Finished in 46.38 seconds (files took 1.97 seconds to load)
6 examples, 0 failures


pid 24471 exit 0
```

### Clean up
Now that the tests have been run it's time to clean up. Litmus includes a tear down command that will allow the targets to be removed. In this example, we will be removing the Docker container. Type the following command
```
> bundle exec rake litmus:tear_down
```
You should receive some JSON output, similar to what's below.
```
{"node"=>"localhost", "status"=>"success", "result"=>{"_output"=>"Removed localhost:2222\n{\"status\":\"ok\"}\n"}}
```
To double check that the target has been removed, type `docker ps` from the command line and you should see that it's no longer running.

## Next steps
The above example is straightforward and simple example of using Litmus to test an existing module that contains acceptance tests. It is intended as an introductory tutorial for new users. As you scale up your acceptance testing, you will need need to do much more than this tutorial outlined, including writing your own acceptance tests.

* Try provisioning more than one system, e.g. `bundle exec rake 'litmus:provision[docker, centos:6]'`, remembering that you will need to re-run the install_agent and install_module command if you want to run tests.
* Look at the inventory file, inventory.yaml, noting the ssh connection information
* ssh into the CentOS box `ssh -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no root@localhost -p 2222`, or just use bolt as outlined in the tutorial
* Compare the current released version of MoTD to the Litmus version. https://github.com/puppetlabs/puppetlabs-motd/compare/puppet_litmus. All Puppet supported modules will be updated to use Litmus over time.
* We're moving more of our PR testing to public pipelines to make contributing to Puppet Supported modules a better experience for developers. Check out our [bigger PR testing matrix](https://github.com/puppetlabs/puppetlabs-motd/pull/180) on Appveyor (which tests Windows 2016 and Windows 2012) and Travis (which tests Puppet5/6 on Oracle, Scientific Linux, Debian, Ubuntu and CentOS!)