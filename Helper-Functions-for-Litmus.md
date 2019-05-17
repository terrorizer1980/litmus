Inside of the litmus gem there are 3 distinct sets of functionality that are available to the end user. 
*  Rake tasks for the CLI that allow the user to provision / install agent / install module and run tests. 
* Helper functions for serverspec / test , these apply manifests or run shell commands more detailed [here](https://www.rubydoc.info/gems/puppet_litmus/PuppetLitmus/Serverspec)
* Helper Functions for bolt primarily they are for inventory file manipulation, These
** [inventory_hash_from_inventory_file(inventory_full_path = nil)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L76)
** [find_targets(inventory_hash, targets)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L88)
[target_in_group(inventory_hash, node_name, group_name)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L98)
[config_from_node(inventory_hash, node_name)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L110)
[facts_from_node(inventory_hash, node_name)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L121)
[add_node_to_group(inventory_hash, node_name, group_name)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L132)
[remove_node(inventory_hash, node_name)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L141)