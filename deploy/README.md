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

  % brew install ansible
  % vagrant plugin install vagrant-hostsupdater


### Getting started

The following important files and folders need to be copied to your project.

* `Vagrantfile`
* `ansible.cfg`
* `deploy/`
* `public/`

Copy this project's files so that the deploy and public directories are
in the root of your project Git repository. Be sure to copy recursively
and preserve file modes, eg. executable, so that the bash helper script
continues to work. 

	cp -rp . path/to/your/project/

The `Vagrantfile` and `ansible.cfg` file should be placed in your
project root directory, not the deploy directory.

### Config Vars

Let's assume you are working on a project called "Groundskeeper".

* The git repo should be at github.com/saplingdigitalltd/groundskeeper
* The local dev url should be groundskeeper.dev
* The staging url should be groundskeeper.sapling.digital

In this case the "project name" is "groundskeeper". You will need to set
this as the "project_name" variable in `deploy/ansible/group_vars`.

	# This var is referenced by a few other vars, eg. git_repo, hostname, site_fqdn.
	project_name: groundskeeper

You will also need to set up the production site's Fully Qualified
Domain Name (fqdn). In this case it might be something like
`groundskeeper.com` or `groundskeeper.sapling.digital`.

### Vagrantfile

In the Vagrantfile, set the local development url for the project (this
should be the project name ending in the suffix `.dev`.

	config.hostsupdater.aliases = ['groundskeeper.dev']

### Inventory

The inventory files in `deploy/ansible/inventory` are used to run
playbooks for specific environments. For each environment, ensure you
set the correct fqdn eg:

	# vagrant host
	vagrant ansible_ssh_host=groundskeeper.dev

	# staging host
	groundskeeper.sapling.digital site_fqdn=groundskeeper.sapling.digital 

	# production host
	groundskeeper.com

### Up and Running in Development

Ensure you've followed the steps above to move these files to your
specific project and configured the project name and hosts accordingly.

If you've not already done so, run `vagrant up` to boot up the VM. This
will also run the playbooks to provision and configure the VM ready
for project development.

Once complete, browse to your local host (eg. groundskeeper.dev) and you
should see a success message.

If you want/need to make changes to the server, make sure to run
`vagrant provision` afterwards. If you want to run specific plays again (eg.
because they failed previously) you can use the built in `run.sh`
script - details below.

If you're working on a new WP site, ensure the Vagrant file and other
necessary files and folders are in the project root directory, not the
theme directory.

## Up and running in Staging/Production

We don't use Vagrant in staging or production environments so playbooks
must be exectued manually to set up the base machine, provision packages
and manage users.

To get a new Digital Ocean droplet up and running, you'll need to run
the following plays:

* Provision (to install packages)
* Configure (for general config and user settings)

*In order to run these playbooks for the first time (before any users are
set up via the scripts) you'll need to ensure your ssh keys have been added
to the server. If you use multiple keys (with/without passwords) then
you may have to set you `~/.ssh/config` to the correct key.*

If you require a MySQL database (eg. for a WP project)

* Run the mysql `install` play and *save the details in 1Password!*
* Run the mysql `new_db` play

For convenience a helper script is included at `deploy/run.sh`. Use it
like this:

	# provision the production server
	./deploy/run.sh provision production --user=root

	# configure nginx on the production server
	./deploy/run.sh configure production --tags=nginx

	# setup mysql on staging, setting the new root user and password
	./deploy/run.sh mysql staging --tags=install mysql_root_user=username mysql_root_password=password

	# create a mysql database on staging
	./deploy/run.sh mysql staging --tags=new_db name=new_db_name u=username p=password

	# force gulp build scripts to run on production
	./deploy/run.sh deploy production --tags=gulp force=true

* Execute the script `./deploy/run.sh`
* The first argument is the playbook to run
* The second argument is the server to run it on
* Tags can be passed to limit the plays that are run with `--tags`
* Extra variables can be passed as `key=value` in a space separated list
* Use `force=true` to force a build even if there are no commits to sync

More info can be found in 
[the original Bocoup docs](https://deployment-workflow.bocoup.com/#playbook-helper-script)
although do note the difference in file name - we've shortened it to
make it easier to type!
