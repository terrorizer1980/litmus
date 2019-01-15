## Use solid-waffle with MoTD

Goal: At the end of this you will have checked out MoTD module. Provisioned a centos docker image. Installed puppet 6 agent on the centos image. Installed the MoTD module on the centos image. Ran the MoTD acceptance tests.

Pre-requisites: A ruby environment 2.3 preferably. Docker installed and working. Git installed.

```
git clone git@github.com:puppetlabs/puppetlabs-motd.git
cd puppetlabs-motd
git remote add tphoney git@github.com:tphoney/puppetlabs-motd.git
git rebase tphoney/solid-waffle
bundle install --path .bundle/gems/
bundle exec rake 'waffle:provision[vmpooler, centos-7-x86_64]'
bundle exec rake 'waffle:provision[vmpooler, win-2012r2-x86_64]'

bundle exec rake waffle:install_agent
bundle exec rake waffle:install_module

# run tests in parallel
bundle exec rake acceptance:all -j10 -m 

# return images to pool
bundle exec rake waffle:tear_down
```