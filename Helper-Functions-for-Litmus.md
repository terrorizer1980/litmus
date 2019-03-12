# Helper Functions for testing your modules
## [apply_manifest(manifest, opts = {}, manifest_file_location = nil)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L8)
## [create_manifest_file(manifest)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L40)
## [apply_manifest_and_idempotent(manifest)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L60)
## [run_shell(command_to_run, opts = {})](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L66)
## [task_run(task_name, params)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L149)

# Helper Functions for inventory file manipulations
## [inventory_hash_from_inventory_file(inventory_full_path = nil)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L76)
## [find_targets(inventory_hash, targets)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L88)
## [target_in_group(inventory_hash, node_name, group_name)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L98)
## [config_from_node(inventory_hash, node_name)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L110)
## [facts_from_node(inventory_hash, node_name)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L121)
## [add_node_to_group(inventory_hash, node_name, group_name)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L132)
## [remove_node(inventory_hash, node_name)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L141)