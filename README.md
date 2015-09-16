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

### Debugging Vagrant

Sometimes Vagrant can get stuck - if there was an error in running
an Ansible playbook or if the machine is already running.

First try to destroy the machine with `vagrant destroy` and re-run
`vagrant up`.

When booting the machine for the first time with `vagrant up` you may
see warnings of "Connection timeout". This can sometimes be due to the
machine still being provisioned. 

If this persits, stop the process with `ctrl+c` and open the Virtual Box
app to manually shutdown the machine. You should now be able to run
`vagrant up` successfully but if not, ask for help.

### Up and Running in Development

Ensure you've followed the steps above to move these tasks to your
specific project and configured the project name and hosts accordingly.

If you've not already done so, run `vagrant up` to boot up the VM. This
will also run the playbooks to provision and configure the machine ready
for your project.

Once complete, browse to your local host (eg. groundskeeper.dev) and you
should see a success message.

If you want/need to make changes to the server, make sure to run
`vagrant provision`. If you want to run specific plays again (eg.
because they failed previously) you can use the built in `run.sh`
script - details below.

## Up and running in Staging/Production

We don't use Vagrant in staging or production environments so playbooks
must be exectued manually to set up the base machine, provision packages
and manage users.

For convenience a helper script is included at `deploy/run.sh`. Use it
like this:

	# provision the production server
	./deploy/run.sh provision production

	# configure nginx on the production server
	./deploy/run.sh configure production --tags=nginx

	# setup mysql on staging, setting the new root user and password
	./deploy/run.sh mysql staging --tags=install mysql_root_user=username mysql_root_password=password

	# create a mysql database on staging
	./deploy/run.sh mysql staging --tags=new_db name=new_db_name u=username p=password

* Execute the script `./deploy/run.sh`
* The first argument is the playbook to run
* The second argument is the server to run it on
* Tags can be passed to limit the plays that are run with `--tags`
* Extra variables can be passed as `key=value` in a space separated list

More info can be found in 
[the original Bocoup docs](https://deployment-workflow.bocoup.com/#playbook-helper-script)
although do note the difference in file name - we've shortened it to
make it easier to type!
