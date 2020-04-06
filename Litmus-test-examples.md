These are some common examples you can use in your tests. Take note of the differences between beaker-rspec style testing and Litmus. 

## Testing Puppet code

The following example tests that your Puppet code works. Take note of the repeatable pattern.

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

## Testing manifest code for idempotency

Previously, when testing for test for idempotency you would apply manifest twice and check for failures on the first apply and changes on the second apply. For example:

```ruby
pp = ' class { 'mysql::server' } '
execute_manifest(pp, catch_failures: true)
execute_manifest(pp, catch_changes: true)
```

With Litmus, you just need to use the `idempotent_apply` helper function. For example 

```ruby
pp = ' class { 'mysql::server' } '
idempotent_apply(pp)
```

## Running shell commands

Previously,, it was common to use code blocks when running shell commands. With Litmus, use the shell command is `run_shell`. For example:

```ruby
shell('/usr/local/sbin/mysqlbackup.sh') do |r|
  expect(r.stderr).to eq('')
end
```

You can do run this on a single line:

```ruby
expect(run_shell('/usr/local/sbin/mysqlbackup.sh').stderr).to eq('')
```

### Serverspec Idioms

An example of a serverspec declaration:

```ruby
command('/usr/local/sbin/mysqlbackup.sh') do
  its(:stderr) { should eq '' }
end
```


## Checking facts

Previously, you would call facter or getting other system information like:

```ruby
fact_on(host, 'osfamily')
fact('selinux')
```

With Litmus, you can use the serverspec functions — these are cached so are quick to call. For example:

```ruby
os[:family]
host_inventory['facter']['os']['release']
```
For more information, see the [serverspec docs](https://serverspec.org/host_inventory.html).

## Debugging tests

There is a known issue when running certain commands from within a pry session. To debug tests, use the following pry-byebug gem: 

```ruby
gem  'pry-byebug', '> 3.4.3' 
```

## Setting up Travis 

An example of a Travis configuration chunk in a `puppet_litmus` test:

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