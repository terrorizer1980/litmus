# Introduction
Below we will list some example patterns you may want to use in your tests, when moving from beaker-rspec style testing. 

## Where to put your helper functions
Generally helper functions used to live everywhere, special files, in the spec_helper_acceptance.rb

```
spec\
spec\spec_helper_acceptance.rb        <-some functions in here
spec\acceptance\helper_functions.rb   <-some functions in here
spec\acceptance\test_spec.rb          <-some functions in here
```

**New way**: we put all helper code in one place, it will be automatically loaded by spec_helper_acceptance.rb

```
spec\spec_helper_acceptance_local.rb   <- all helper code should live in here
```

## A basic test example

Below is a standard trope for checking that your puppet code works, it is a repeatable pattern.

```ruby
require 'spec_helper_acceptance'

describe 'concat noop parameter', if: ['debian', 'redhat', 'ubuntu'].include?(os[:family]) do
  before(:all) do
    @basedir = setup_test_directory
  end
  describe 'with "/usr/bin/test -e %"' do
    let(:pp) do
      <<-MANIFEST
      concat_file { '#{@basedir}/file':
        noop => false,
      }
      concat_fragment { 'content':
        target  => '#{@basedir}/file',
        content => 'content',
      }
    MANIFEST
    end

    it 'applies the manifest twice with no stderr' do
      idempotent_apply(pp)
      expect(file("#{@basedir}/file")).to be_file
      expect(file("#{@basedir}/file").content).to contain 'content'
    end
  end
end
```

## Checking your manifest code is idempotent

The old way to test for idempotency is to apply the manifest twice checking for failures on the first apply and changes on the second apply.

```ruby
pp = ' class { 'mysql::server' } '
execute_manifest(pp, catch_failures: true)
execute_manifest(pp, catch_changes: true)
```

New way with Litmus, we can use the idempotent_apply helper function. (its quicker too) 

```ruby
pp = ' class { 'mysql::server' } '
idempotent_apply(pp)
```

## Running shell commands

The shell command has become run_shell. Generally in the past code blocks were used.

```ruby
shell('/usr/local/sbin/mysqlbackup.sh') do |r|
  expect(r.stderr).to eq('')
end
```

This can be done on a single line, if you are only checking one thing from the command

```ruby
expect(run_shell('/usr/local/sbin/mysqlbackup.sh').stderr).to eq('')
```

### Serverspec Idioms

You can also use serverspec declarations:

```ruby
command('/usr/local/sbin/mysqlbackup.sh') do
  its(:stderr) { should eq '' }
end
```


## Checking facts

Calling facter or getting other system information was like:

```ruby
fact_on(host, 'osfamily')
fact('selinux')
```

You can now use the serverspec functions (incidentally, these are cached so are quick to call) look here for more https://serverspec.org/host_inventory.html 

```ruby
os[:family]
host_inventory['facter']['os']['release']
```

## Debugging your tests
There is a known issue when running certain commands from within a pry session. It is recommended that you use the following pry-byebug gem. 

```ruby
gem  'pry-byebug', '> 3.4.3' 
```

## Travis setup example
This is what a Travis config chunk would look like for running puppet_litmus tests.

```yaml
---
language: ruby
cache: bundler
before_install:
  - bundle -v
  - rm -f Gemfile.lock
  - gem update --system $RUBYGEMS_VERSION
  - gem --version
  - bundle -v
rvm:
  - 2.5.1
matrix:
  fast_finish: true
  include:
    -
      bundler_args:
      dist: trusty
      env: PLATFORMS=debian_puppet5
      rvm: 2.5.1
      before_script:
      - bundle exec rake 'litmus:provision_list[travis_deb]'
      - bundle exec bolt command run 'apt-get install wget -y' --inventoryfile inventory.yaml --nodes='localhost*'
      - bundle exec rake 'litmus:install_agent[puppet5]'
      - bundle exec rake litmus:install_module
      script:
      - bundle exec rake litmus:acceptance:parallel
```