# Introduction
Below we will list some example patterns you may want to use in your tests, when moving from beaker-rspec style testing. 

## where to put your helper functions
generally helper functions used to live everywhere, special files, in the spec_helper_acceptance.rb
`spec\spec_helper_acceptance.rb        <-some functions in here
 spec\acceptance\helper_functions.rb   <-some functions in here
 spec\acceptance\test_spec.rb          <-some functions in here
`
new way we put all helper code in one place, it will be automatically loaded by spec_helper_acceptance.rb
`
spec\spec_helper_acceptance_local.rb   <- all helper code should live in here
`

## checking your manifest code is idempotent
old way
``
litmus way