# Building Ansible Collections
This repository exists to provide a small tutorial on how Ansible Collections
work and how to create them. A Vagrant configuration for CentOS7 is provided
and will bootstrap a development environment for testing.

## Requirements
* Vagrant
* VirtualBox

## Process
* Start by bringing up the development VM using `vagrant up` and wait for the
  CentOS machine to boot and install Ansible.
* To keep your local directory in sync with the VM run `vagrant rsync-auto` in
  the project directory.
* Open a second terminal to the project directory and run `vagrant ssh` to login
  to the VM and start the lessons.

## Lessons
### Lesson 0 - The Development Environment
The VM is pre-loaded with Ansible v2.9.10 and VIM for an editor. It is configured
to run Ansible under Python2 since that is the default for CentOS7. The first task
is to login to the VM with `vagrant ssh` and to add any additional packages you'd
like to have installed to the first lesson playbook. Once you're done adding
packages, run the playbook as shown below.

```bash
$ cd /vagrant
$ ansible-playbook playbooks/lesson-00.yml
```

### Lesson 1 - What are Ansible Collections
Ansible Collections are a new feature introduced in the Ansible `2.9.x` series
which provide a more modular way of packaging groups of roles and playbooks for
distribution. It is also the way that Ansible `2.10.x` and onwards are hoping
to improve the release cadence of community modules. Since Collections provide
a method to split out and package Ansible modules (e.g. `yum`, `k8s`, etc.) as
well as roles or playbooks the idea is to unhitch the release cycle of these
community supported modules from that of the main Ansible application. That way,
features can be released more regularly and bugfixes provided faster than the
official cycle.

The RedHat has provided some getting started literature [here][ansible-get-started-collections]
and [here][ansible-hands-on-collections] which you can review. However, at it's
core, Collections are just a directory structure with a few additional metadata
files provided to track the collection itself. That structure is detailed
[here][ansible-developing-collections] in the Ansible documentation with descriptions
of each of the different directories. The only thing not featured here, and thus
not 100% official yet, is the inclusion of playbooks in a collection. Presently,
calling a playbook from inside a collection triggers a warning at runtime but
still executes without issue. You could run into problems if you regularly rely
on relative paths within your playbooks...but just don't do that because it's a
giant recipe for headache.

[ansible-get-started-collections]: https://www.ansible.com/blog/getting-started-with-ansible-collections
[ansible-hands-on-collections]: https://www.ansible.com/blog/hands-on-with-ansible-collections
[ansible-developing-collections]: https://docs.ansible.com/ansible/latest/dev_guide/developing_collections.html

### Lesson X - Creating an Ansible Collection

```bash
$ cd ./ansible_collections
$ ansible-galaxy collection init demo.core
```

### Lesson Y - Building an Ansible Collection
First create a new collection outside of the `./ansible_collections` directory.

```bash
$ cd /vagrant
$ ansible-galaxy collection init demo.extension
```

Edit the new collection

Build the collection

```bash
$ ansible-galaxy collection build demo/extension
```

Install the new collection.

```bash
$ ansible-galaxy collection install ./demo-extension-1.0.0.tar.gz
```

You'll see an error about the collection at `demo/core` which is due to how we
created the collection without the build step.

Edit the lesson playbook to call your new role and run.

```bash
$ ansible-playbook playbooks/lesson-0X.yml
```

### Lesson  - Installing a Collection from Ansible Galaxy
Now it's time to use someone else's code by installing a Collection from Ansible
Galaxy as described [here][ansible-using-collections]. We are going to be installing
some PHP roles by [Jeff Geerling][github-geerlingguy], who has done a lot of
writing and developing around Ansible.

```bash
$ ansible-galaxy collection install geerlingguy.php_roles
```

You will see that the `ansible-galaxy` tool reaches out to the Galaxy API on the
internet and grabs the dependency map for the new collection. From there it also
pulls down an archive like the one we created previously and installs it to our
`./ansible_collections` directory. Now let's run `playbooks/lesson-0X.yml` and
install PHP!

```bash
$ ansible-playbook playbooks/lesson-0X.yml
```

Once that installation finishes we can test and make sure that we have PHP
available on our VM.

```bash
$ php --version

```

[ansible-using-collections]: https://docs.ansible.com/ansible/latest/user_guide/collections_using.html
[github-geerlingguy]: https://github.com/geerlingguy

## Notes
* I didn't use the same Python3 in a virtual environment setup as my other
  testing VM since it breaks installing packages on CentOS7. If you try to use
  `package` it auto-detects incorrectly to `yum` and if you try to run `dnf`
  explicitly it tries to install the package `python3-dnf` which doesn't exist
  for CentOS7.
