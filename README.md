# Groundskeeper
## A Modern Web Deployment Workflow

This workflow has been forked from some excellent work done by
[Bocoup](http://www.bocoup.com) and tailored to our needs.

For full documentation, check out [the original project
site](https://deployment-workflow.bocoup.com).

### Dependencies

* [Ansible 1.9+](http://docs.ansible.com/)
* [Virtual Box](https://www.virtualbox.org/)
* [Vagrant](https://www.vagrantup.com/)
* [vagrant-hostupdater](https://github.com/cogitatio/vagrant-hostsupdater)

### Getting started

The following important files need to be copied to your project.

* [Vagrantfile](Vagrantfile)
* [ansible.cfg](ansible.cfg)
* [.gitignore](.gitignore)
* [deploy/](deploy/)

Copy this project's files so that the deploy directory is in the root of
your project Git repository. Be sure to copy recursively and preserve
file modes, eg. executable, so that the bash helper script continues to
work. 

	cp -rp . path/to/your/project/

The `Vagrantfile` and `ansible.cfg` file should be placed in your
project root directory, not the deploy directory.

Also, be sure to add the `.vagrant` folder to your project's
`.gitignore` file so that directory's contents, which are auto-generated
by Vagrant, aren't committed with your project's source.

### Config Vars

Let's assume you are working on a project called "Groudskeeper".

* The git repo should be at github.com/saplingdigitalltd/groundskeeper
* The local dev url should be groundskeeper.dev

In this case the "project name" is "groundskeeper". You will need to set
this as the "project_name" variable in `deploy/ansible/group_vars`.

	# This var is referenced by a few other vars, eg. git_repo, hostname, site_fqdn.
	project_name: groundskeeper

You will also need to set up the production site's Fully Qualified
Domain Name (fqdn). In this case it might be something like
`groundskeeper.com` or `groundskeeper.sapling.digital`.

### Vagrantfile

In the Vagrantfile, set the local development url for the project (this
is built from the project name set above, ending in the suffix `.dev`.

	config.hostsupdater.aliases = ['groundskeeper.dev']

### Inventory

The inventory files in `deploy/ansible/inventory` are used to run
playbooks for specific environments. For each environment, ensure you
set the correct fqdn eg:

	# vagrant host
	vagrant ansible_ssh_host=groundskeeper.dev

	# staging host
	groundskeeper.staging.sapling.digital site_fqdn=groundskeeper.staging.sapling.digital 

	# production host
	groundskeeper.com
