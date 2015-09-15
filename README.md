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
