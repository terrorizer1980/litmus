---
layout: page
---

Litmus provides [helper functions](https://github.com/puppetlabs/puppet_litmus/wiki/Helper-Functions-for-Litmus#helper-functions-for-testing-your-modules) that you can use in your tests. These functions are similar to the functions provided by the Beaker language.

Previously, helper functions lived in various different files in `spec_helper_acceptance.rb`:

```
spec\
spec\spec_helper_acceptance.rb        <-some functions in here
spec\acceptance\helper_functions.rb   <-some functions in here
spec\acceptance\test_spec.rb          <-some functions in here
```

With Litmus, all the helper code lives in one place and is automatically loaded by `spec_helper_acceptance.rb`:

```
spec\spec_helper_acceptance_local.rb   <- all helper code should live in here
```

Inside of the Litmus gem, there are three distinct sets of functions:

*  Rake tasks for the CLI that allows you to use the Litmus commands (provision, install an agent, install a module and run tests.)
* Helper functions for serverspec / test. These apply manifests or run shell commands. For more information, see [Puppet Helpers](https://www.rubydoc.info/gems/puppet_litmus/PuppetLitmus/PuppetHelpers)
* Helper Functions for Bolt inventory file manipulation. For more information, see [Inventory Manipulation](https://www.rubydoc.info/gems/puppet_litmus/PuppetLitmus/InventoryManipulation).
