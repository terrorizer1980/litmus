The following example walks you through converting a module to use Litmus for acceptance testing.

### Before you begin

***Important*** If your module is compatible with [Puppet Development Kit (PDK)](https://puppet.com/docs/pdk/1.x/pdk.html), meaning it was either created with the PDK or has been converted to use the PDK using the `pdk convert` command, you can convert your module to use Litmus using sync.yml. This means you can skip step 1 (add code to your `Gemfile` and `Rakefile`), and move straight to step 2 (Add code to the spec file). PDK manages the Gemfile and Rakefile. These file changes go either through sync.yml. The rest of the changes always needs to happen manually. 

To verify that module is compatible with PDK, look in the modules `metadata.json` file and see whether there is an entry that states the PDK version. It will looks something like `"pdk-version": "1.17.0"`.

To convert a module to use Litmus, add code to the following files:

### 1. Add code to your `Gemfile` and `Rakefile` 

***Note*** If your module is compatible with PDK, skip this step and move to step 2.

Inside the root directory of your module, you will have a `Gemfile` and `Rakefile`. 

Inside the `Gemfile`, add the following code to `group :development` section:

```ruby
gem 'puppet_litmus'
gem 'serverspec'

```
This code make sure the `puppet_litmus` library is included in the module. You can now remove the system tests section, as Beaker is no longer needed.

Inside the`Rakefile`, add the following code to the **top**:

```ruby
require 'puppet_litmus/rake_tasks'
```

This code adds the Litmus rake tasks to the module. 

### 2. Add code to the `.fixtures.yml` file

Inside the root directory of your module, add the following code to the `.fixtures.yml`:

```yaml
---
fixtures:
  repositories:
    facts: 'https://github.com/puppetlabs/puppetlabs-facts.git'
    puppet_agent: 'https://github.com/puppetlabs/puppetlabs-puppet_agent.git'
    provision: 'https://github.com/puppetlabs/provision.git'
```

### 3. Add code to the spec/spec_helper_acceptance.rb file

Inside the `spec` folder of the module, add the following code to the `spec_helper_acceptance.rb` file: 

```ruby
# frozen_string_literal: true

require 'puppet_litmus'
require 'spec_helper_acceptance_local' if File.file?(File.join(File.dirname(__FILE__), 'spec_helper_acceptance_local.rb'))

PuppetLitmus.configure!
```

If you can't find this file, it means the module doesn't have any acceptance tests and you will need to add them. For the purposes of this example, create the file and add the code above. 

### 4. Update the `spec/`spec_helper_acceptance_local.rb` file

You might need to have additional helper before you start running tests. If you need to use any of the Litmus methods in the following file, include Litmus in a singleton class: 

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
