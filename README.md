# Ansible configuration management for Alaveteli instances

** THIS IS A WORK IN PROGRESS **

Right now it is in testing and works only on http://test.nuvasuparati.info.

It is based on [Open Australia's Infrastructure](https://github.com/openaustralia/infrastructure)

## Some useful reading

* [6 practices for super smooth Ansible experience](http://hakunin.com/six-ansible-practices)
* [Ansible Best Practices](http://docs.ansible.com/playbooks_best_practices.html)
* [Ansible real life good practices](https://www.reinteractive.net/posts/167-ansible-real-life-good-practices)

## Requirements

For starting local VMs for testing you will need [Vagrant](https://www.vagrantup.com/).
For configuration management you will need [Ansible](http://docs.ansible.com/).

Also
```
$ vagrant plugin install vagrant-hostsupdater
```

Create a file in your home directory `~/.infrastructure_ansible_vault_pass.txt` with the secret
password used to encrypt the secret info in this repo

## Provisioning

### Development

In development you set up and provision a server using Vagrant. You probably only want to run
one machine so you can bring it up with:

    vagrant up

If it's already up you can re-run Ansible provisioning with:

    vagrant provision

The default host is http://alaveteli.org.dev

### Staging

**This is untested**

Provision a running server with:

    ansible-playbook site.yml -l mynewalaveteli.org

## Notes for deploying

### Alaveteli

For the time being you will need to use the `update-rbenv-deploy` branch of the OpenAustralia
Foundation Alaveteli repo as it contains some small fixes to allow capistrano to work with rbenv.

In your checked out copy of the Alaveteli repo add the following to `config/deploy.yml`

```yaml
# Site-specific deployment configuration lives in this file
production:
  branch: 0.23.2.2
  repository: git://github.com/andreicristianpetcu/alaveteli.git
  server: mynewalaveteli.org
  user: deploy
  rails_env: production
  deploy_to: /srv/www/alaveteli_production
staging:
  branch: 0.23.2.2
  repository: git://github.com/andreicristianpetcu/alaveteli.git
  server: test.mynewalaveteli.org
  user: deploy
  rails_env: production
  deploy_to: /srv/www
development:
  branch: 0.23.2.2
  repository: git://github.com/andreicristianpetcu/alaveteli.git
  server: alaveteli.org.dev
  user: deploy
  deploy_to: /srv/www
  rails_env: production
```

This adds an extra staging for the capistrano deploy called `development`. This will deploy to your
local development VM being managed by Vagrant.

Then
```
bundle exec cap -S stage=development deploy:setup
bundle exec cap -S stage=development deploy:cold
bundle exec cap -S stage=development deploy:migrate
bundle exec cap -S stage=development xapian:rebuild_index
```

#### TODOS

* Varnish

## DNS Setup

Right now we only support Route53 DNS server but we provision everything you need. Just add your public/private keys in roles/alaveteli/vars/encrypted.yml.

## Vault

In order to provision this web site you need to take the content of roles/alaveteli/vars/encrypted.example.yml and put it in roles/alaveteli/vars/encrypted.yml.
You can do this by editing with `ansible-vault edit roles/alaveteli/vars/encrypted.yml`. You need to have a file with your secret password in ~/.infrastructure_ansible_vault_pass.txt.
This is a WOP, and the password from ~/.infrastructure_ansible_vault_pass.txt will be removed soon.
