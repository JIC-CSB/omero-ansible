# README

## Quick test guide

Start the CentOS 7.0 virtual machine using Vagrant.

```bash
vagrant up
```

Copy the vagrant user's ``.ssh/`` directory into root's home directory to
enable password-less ssh as root.

```bash
vagrant ssh
sudo su
cd
cp -r ~vagrant/.ssh .
exit
exit
```

Create a ``provisioning/vars.yml`` file.

```bash
cd provisioning
cp vars-template.yml vars.yml
vim vars.yml  # Edit as you see fit, passwords in particular.
```

Check your version of vagrant.

```bash
vagrant --version
```

If your version is lower than 1.7.0 edit the ``provisioning/hosts`` file to
look like the below (note the change in the path to the private key).

```
omeroserver ansible_ssh_host=192.168.33.10 ansible_ssh_user=root ansible_ssh_private_key_file=~/.vagrant.d/insecure_private_key
```

Configure the virtual machine with an OMERO 5.1 install using Ansible.

```bash
ansible-playbook -i hosts omero_install.yml
```

Log into the virtual machine and start ``omero`` and ``omero_web``.

```bash
vagrant ssh
su omero
cd   # (into the omero home directory)
./OMERO.server/bin/omero admin start
./OMERO.server/bin/omero web start
```

Make sure that everything is working as it should.

```bash
./OMERO.server/bin/omero admin diagnostics
```

Log in to the OMERO server using the web client at:
``http://192.168.33.10``.


## Enabling ldap configuration

Edit the ldap details in ``provisioning/vars.yml``..

Run Ansible.

```bash
ansible-playbook -i hosts configure_ldap_authentication.yml
```

Log into the virtual machine and re-start ``omero``.

```bash
vagrant ssh
su omero
cd   # (into the omero home directory)
./OMERO.server/bin/omero admin restart
```

## OMERO data storage

This playbook has been designed to allow several OMERO server to point at the
same root of a mounted file storage.

For example using the default variables with the Vagrant virtual machine
provided in this repository results in the data directory
``/omero_data/vagrant-centos7/OMERO/``. The playbook then creates a symbolic
link to this directory from ``/OMERO``.

To configure this setup modify the variables ``omero_data_mount`` and
``omero_data_dir``.
