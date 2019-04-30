# Helper Functions for testing your modules
[Instance Summary](https://github.com/puppetlabs/puppet_litmus/blob/master/lib/puppet_litmus/serverspec.rb)

* apply_manifest(manifest, opts = {}) ⇒ Object
Applies a manifest.
* create_manifest_file(manifest) ⇒ String
Creates a manifest file locally, if running against localhost create in a temp location.
* idempotent_apply(manifest) ⇒ Boolean
Applies a manifest twice.
* run_bolt_task(task_name, params = {}) ⇒ Object
Runs a task against the target system.
* run_shell(command_to_run, opts = {}) ⇒ Object
Runs a command against the target system.

# Helper Functions for inventory file manipulation
* [inventory_hash_from_inventory_file(inventory_full_path = nil)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L76)
* [find_targets(inventory_hash, targets)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L88)
* [target_in_group(inventory_hash, node_name, group_name)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L98)
* [config_from_node(inventory_hash, node_name)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L110)
* [facts_from_node(inventory_hash, node_name)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L121)
* [add_node_to_group(inventory_hash, node_name, group_name)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L132)
* [remove_node(inventory_hash, node_name)](https://github.com/puppetlabs/puppet_litmus/blob/f858434e90e3c52138e1482f9a186024e8863a57/lib/puppet_litmus.rb#L141)