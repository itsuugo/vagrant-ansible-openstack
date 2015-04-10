Neutron ml2 plugin
=========

OpenStack Neutron ml2 plugin installation

_Tested on Ubuntu Precise (12.04) and Trusty (14.04)_

Requirements
------------

None

Role Variables
--------------

| Name | Default value | Description | Note |
|---  |---  |---  |--- |
| `tunnel_id_ranges` | `1:1000` | GRE tunnel id range ||

Dependencies
------------

None.

Example Playbook
----------------

    - hosts: compute001
      roles:
        - role: openstack-neutron_plugin_ml2
          tunnel_id_ranges: 1000:2000

    - hosts: network001
      roles:
        - role: openstack-neutron_plugin_ml2
          tunnel_id_ranges: 1000:2000

---

A complete Ansible playbook demo, which uses this role, is available on Github (dguerri/vagrant-ansible-openstack) <https://github.com/dguerri/vagrant-ansible-openstack>

---


License
-------

Apache

Author Information
------------------

Davide Guerri - davide.guerri@gmail.com
