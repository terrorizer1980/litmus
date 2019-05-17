# functions to help test your modules
[serverspec functions that apply manifests or run shell commands ...](https://www.rubydoc.info/gems/puppet_litmus/PuppetLitmus/Serverspec)

# Helper Functions for inventory file manipulation
* [inventory_hash_from_inventory_file(inventory_full_path = nil)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L76)
* [find_targets(inventory_hash, targets)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L88)
* [target_in_group(inventory_hash, node_name, group_name)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L98)
* [config_from_node(inventory_hash, node_name)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L110)
* [facts_from_node(inventory_hash, node_name)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L121)
* [add_node_to_group(inventory_hash, node_name, group_name)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L132)
* [remove_node(inventory_hash, node_name)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L141)