rd-ansible-refstack
=========

This module installs RefStack on a host so it can be used to test or validate an
OpenStack deployment.

See: http://git.openstack.org/cgit/openstack/refstack-client/tree/README.rst

Requirements
------------

This module requires a minimum of Ansible 2.4 but has been tested with 2.6.1

Role Variables
--------------

The calling playbook MUST correctly set the value of refstack_openstack_admin_password as
this is used by Tempest to create additional resources. Ideally the admin password is
secured using a secrets store.

You must override the default variables ``refstack_openstack_public_net_name`` and
``refstack_openstack_keystone_public_url`` to reflect the deployment you wish to validate.

See ``defaults/main.yml`` for other variables and descriptions.

This role assumes that the resources required to run a tempest test have already
been created. These are:

* A set of accessible external OpenStack API endpoints
* A external provider network
* A private network and subnet for tempest
* Two flavors
* Two cirros images

[where 'external' indicates accessible by the RefStack host]

If OpenStack-Ansible is used as the OpenStack deployment tooling, some of these
resources can be created by running the ``playbooks/os-tempest-install.yml``
playbook. It is important that the settings used in Openstack-Ansible to
create these resources are reflected in the defaults for this role.

Dependencies
------------

This role has no dependencies

Example Playbook
----------------

Example to call:

    - hosts: refstack*
      vars:
        - refstack_openstack_keystone_public_url "https://cloud.example.com:5000/v3"
        - refstack_openstack_public_net_name: MY_PUBLIC_NETWORK
        - refstack_openstack_admin_password: "{{ lookup('pipe', 'vault read -field value secret/passwords/openstack_admin') }}"
      roles:
        - rd-ansible-refstack

This playbook will deploy RefStack and create a shell script which can be used to
execute the RefStack tests. This script is located at ``{{ refstack_install_dir }}/run_refstack.sh``.
Change directory to ``{{ refstack_install_dir }}`` and execute the script from that location.

The shell script can be run manually or as a step in a CI pipeline.

RefStack places the test results in the directory ``{{ refstack_install_dir }}/.tempest/.testrepository/``. These files can be analysed manually or extracted as
artefacts in a CI pipeline.
