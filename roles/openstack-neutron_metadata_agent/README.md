Neutron l3 agent
=========

OpenStack Neutron l3 agent installation

_Tested on Ubuntu Precise (12.04) and Trusty (14.04)_

Requirements
------------

None.

Role Variables
--------------

### Neutron (set by this role)

| Name | Default value | Description | Note |
|---   |---            |---          |---   |
| `auth_region` | `regionOne` | Authentication region (keystone) ||
| `external_interface` | `eth0` | External interface for instances network connectivity ||
| `metadata_secret` | `metadata_secret_default` | Metadata shared secret (nova) ||
| `neutron_pass` | `neutron_pass_default` | Neutron service user password ||
| `nova_metadata_ip` | `localhost` | Nova metadata server host/IP address ||

### Keystone (must exist)

| Name | Default value | Description | Note |
|---  |---  |---  |--- |
| `keystone_hostname` | `localhost` | Hostname/IP address where the keystone service runs ||
| `keystone_port` | `5000` | Keystone service port ||
| `keystone_protocol` | `http` | Desired keystone protocol (http/https) ||


### RabbitMQ (must exist)

| Name | Default value | Description | Note |
|---  |---  |---  |--- |
| `rabbit_hostname` | `localhost` | Hostname/IP address where the RabbitMQ service runs ||
| `rabbit_username` | `rabbit_username_default` | RabbitMQ username for glance ||
| `rabbit_pass` | `rabbit_pass_default` | RabbitMQ password for glance. ||


Dependencies
------------

None.

Example Playbook
----------------

    - hosts: network001
      roles:
        - role: openstack-neutron_l3_agent
          rabbit_username: "openstack-neutron"
          rabbit_pass: "{{ RABBIT_NEUTRON_PASS }}"


---

A complete Ansible playbook demo, which uses this role, is available on Github (dguerri/vagrant-ansible-openstack) <https://github.com/dguerri/vagrant-ansible-openstack>

---


License
-------

Apache

Author Information
------------------

Davide Guerri - davide.guerri@gmail.com
