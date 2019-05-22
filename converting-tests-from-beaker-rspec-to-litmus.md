# Introduction
Below we will list some example patterns you may want to use in your tests, when moving from beaker-rspec style testing. 

## where to put your helper functions
generally helper functions used to live everywhere, special files, in the spec_helper_acceptance.rb

     spec\
     spec\spec_helper_acceptance.rb        <-some functions in here
     spec\acceptance\helper_functions.rb   <-some functions in here
     spec\acceptance\test_spec.rb          <-some functions in here

**new way ** we put all helper code in one place, it will be automatically loaded by spec_helper_acceptance.rb

     spec\spec_helper_acceptance_local.rb   <- all helper code should live in here

## checking your manifest code is idempotent
old way you would apply the manifest twice checking for different things.

    pp = ' class { 'mysql::server' } '
    execute_manifest(pp, catch_failures: true)
    execute_manifest(pp, catch_changes: true)

new way with litmus, we can use the idempotent_apply helper function. (its quicker too) 

    pp = ' class { 'mysql::server' } '
    idempotent_apply(pp)