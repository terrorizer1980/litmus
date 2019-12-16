# Converting a module to use Litmus using .sync.yml

This tutorial will walk you through how to convert a module to use Litmus for acceptance testing.

## Pre-requisites
Ensure that the module is compatible with the [Puppet Development Kit](https://puppet.com/docs/pdk/1.x/pdk.html). This means it was either created with the PDK, or has been converted to use the PDK - by using the `pdk convert` command.

To check if an existing module is compatible with the PDK, look in the modules `metadata.json` file and check that there is an entry which states the PDK version. It should read something like: `"pdk-version": "1.9.0"`

## Update files
To use Litmus in a module you first need to update the following files to include the specified code.

### .fixtures.yml
In order to provide specific target and to install puppet_agent, to create a functional test environment, Litmus needs to have the following repositories installed: `facts`, `puppet_agent` and `provision`. Because of that we need to add the following lines to the `.fixtures.yml` file in the root directory of the module.

```yaml
---
fixtures:
  repositories:
    facts: 'https://github.com/puppetlabs/puppetlabs-facts.git'
    puppet_agent: 'https://github.com/puppetlabs/puppetlabs-puppet_agent.git'
    provision: 'https://github.com/puppetlabs/provision.git'
```

### .sync.yml

No changes are required, litmus is installed as part of the development gems of puppet-module-gems, and the rakefile is updated to include the litmus tasks. However you can now remove the system tests section, as beaker is no longer needed.

### spec/spec_helper_acceptance.rb

The following should be the contents of the `spec_helper_acceptance.rb` file. This is an acceptance testing file that you will find in the `spec` folder of your module. If it doesn't exist then it means your module doesn't have any acceptance tests, and you will need to add some. If the `spec_helper_acceptance.rb` file does exist, rename it as `spec_helper_acceptance_local.rb`. However, for the purposes of this tutorial, simply create the file with the following content. We will add a tutorial on how to write acceptance tests and link from here when it's ready.

Litmus added the possibility to run tests on your local machine. it's not recommended, but it's allowed. 
This is the configuration file used for connection with the testing target. This target can be your local machine or a remote machine. Each time when you provision a target, its configuration is added in inventory.file. 

```ruby
# frozen_string_literal: true

require 'puppet_litmus'
require 'spec_helper_acceptance_local' if File.file?(File.join(File.dirname(__FILE__), 'spec_helper_acceptance_local.rb'))

PuppetLitmus.configure!
```

### spec/spec_helper_acceptance_local.rb

Sometimes you'll need to have additional helpers and pre-suite action to take before a test run.
If you need to use any of the Litmus methods in this file, you'll need to include Litmus in a singleton class as in this example:

```ruby
require 'puppet_litmus'
require 'singleton'

class Helper
  include Singleton
  include PuppetLitmus
end

def some_helper_method
  Helper.instance.bolt_run_script('path/to/file')
end
```

# Converting a module to use Litmus by making manual file changes

This tutorial will give you a step by step guide on how to manually convert a module to use litmus.

### Gemfile
Add the following lines to your `Gemfile` in the `group :development` section. You will find this file in the root directory of your module. By including this line, we make sure that puppet_litmus library is included in the module. However you can now remove the system tests section, as beaker is no longer needed.

```ruby
gem 'puppet_litmus', git: 'https://github.com/puppetlabs/puppet_litmus.git'
gem 'serverspec'

```

### Rakefile
Add the following line to the **top** of your `Rakefile`. You will find this file in the root directory of your module. By including this line, we make sure that litmus rake tasks are included in the module. 

```ruby
require 'puppet_litmus/rake_tasks'
```

### .fixtures.yml
Add the following lines to your `.fixtures.yml` file in the root directory of your module.

```yaml
---
fixtures:
  repositories:
    facts: 'https://github.com/puppetlabs/puppetlabs-facts.git'
    puppet_agent: 'https://github.com/puppetlabs/puppetlabs-puppet_agent.git'
    provision: 'https://github.com/puppetlabs/provision.git'
```

### spec/spec_helper_acceptance.rb
Add the following to the `spec_helper_acceptance.rb` file. This is an acceptance testing file that you will find in the `spec` folder of your module. If it doesn't exist then it means your module doesn't have any acceptance tests, and you will need to add some. However, for the purposes of this tutorial, simply create the file with the following content. We will add a tutorial on how to write acceptance tests and link from here when it's ready.

```ruby
# frozen_string_literal: true

require 'puppet_litmus'
require 'spec_helper_acceptance_local' if File.file?(File.join(File.dirname(__FILE__), 'spec_helper_acceptance_local.rb'))

PuppetLitmus.configure!
```

### spec/spec_helper_acceptance_local.rb

Sometimes you'll need to have additional helpers and pre-suite action to take before a test run.
If you need to use any of the Litmus methods in this file, you'll need to include Litmus in a singleton class as in this example:

```ruby
require 'puppet_litmus'
require 'singleton'

class Helper
  include Singleton
  include PuppetLitmus
end

def some_helper_method
  Helper.instance.bolt_run_script('path/to/file')
end
```

## Converting tests

There are a number of helper functions that litmus provides, that can be used in your tests. [helper functions for tests](https://github.com/puppetlabs/puppet_litmus/wiki/Helper-Functions-for-Litmus#helper-functions-for-testing-your-modules) these are similar to the functions provided previously by the beaker language. 

There is also this guide [Converting your tests beaker rspec to litmus](https://github.com/puppetlabs/puppet_litmus/wiki/converting-tests-from-beaker-rspec-to-litmus)