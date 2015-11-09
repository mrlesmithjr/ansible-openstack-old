Role Name
=========

A brief description of the role goes here.

Requirements
------------

Any pre-requisites that may not be covered by Ansible itself or the role should be mentioned here. For instance, if the role uses the EC2 module, it may be a good idea to mention in this section that the boto package is required.

Role Variables
--------------

A description of the settable variables for this role should go here, including any variables that are in defaults/main.yml, vars/main.yml, and any variables that can/should be set via parameters to the role. Any variables that are read from other roles and/or the global scope (ie. hostvars, group vars, etc.) should be mentioned here as well.

Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------
````
---
# Notes
# NTP on compute/network node(s) should point to controller node(s)
- name: Builds OpenStack Environment
  hosts: openstack-nodes
  sudo: true
  vars:
    - mysql_allow_remote_connections: true  #defines if mysql should listen on loopback (default) or allow remove connections
    - mysql_root_password: 61fea78e16dd73bd757a  #Root password for the database
    - openstack_admin_pass: 29b1416692cb38014ea0  #Password of user admin
    - openstack_ceilometer_dbpass: 85c4ac62a58c0a25a922  #Database password for the Telemetry service
    - openstack_ceilometer_pass: c3e1b1db22e9b33cd7f4  #Password of Telemetry service user ceilometer
    - openstack_cinder_dbpass: bcb4f4279d699c12c6ea  #Database password for the Block Storage service
    - openstack_cinder_pass: d9eb36abbb2e88356208  #Password of Block Storage service user cinder
    - openstack_dash_dbpass: cb77b8806e4dc8693d8e  #Database password for the dashboard
    - openstack_demo_pass: 54a27efd264beeb7843d  #Password of user demo
    - openstack_glance_dbhost: controller-1  #defines glance db host
    - openstack_glance_dbpass: 295062a986b8deded530  #Database password for Image Service
    - openstack_glance_pass: 60278dafa015c0cc3943  #Password of Image Service user glance
    - openstack_glance_url: http://192.168.202.11  #http://glance.{{ pri_domain_name }}
    - openstack_glance_verbose_logging: true  #defines if glance should enable verbose logging for troubleshooting
    - openstack_heat_dbpass: d69d8f9bd2a70f0f1f5b  #Database password for the Orchestration service
    - openstack_heat_pass: 9fd0ae5b48837ad34b31  #Password of Orchestration service user heat
    - openstack_keystone_dbhost: controller-1  #defines keystone db host
    - openstack_keystone_dbpass: 8ed178efd2bef6fcabe3  #Database password of Identity service
    - openstack_keystone_temp_admin_token: 52f5e14ad1a9f7d54e1d  #Key for initial setup of keystone
    - openstack_keystone_url: http://192.168.202.11 #http://keystone.{{ pri_domain_name }}
    - openstack_keystone_verbose_logging: true  #defines if keystone should enable verbose logging for troubleshooting
    - openstack_neutron_dbpass: 7bff44471bdce25d55af  #Database password for the Networking service
    - openstack_neutron_pass: 19440ed58e9ba153bbb5  #Password of Networking service user neutron
    - openstack_nova_dbpass: b544e0b6881f33c82dc8  #Database password for Compute service
    - openstack_nova_pass: 34e5f8990ef84cc69f91  #Password of Compute service user nova
    - openstack_rabbit_pass: 6bd8dbb369181e89bf3a  #Password of user guest of RabbitMQ
    - openstack_trove_dbpass: c6c2792fa14319dbe9da  #Database password of Database service
    - openstack_trove_pass: 892ba2ff14319c299b54  #Password of Database Service user trove
    - pri_domain_name: example.org  #defines primary domain name of site.
    - update_etc_hosts: true
  roles:
    - role: ansible-bootstrap
    - role: ansible-base
    - role: ansible-config-interfaces
    - role: ansible-ntp
    - role: ansible-apache2
      when: inventory_hostname in groups['openstack-controller-nodes']
    - role: ansible-mariadb-mysql
      when: inventory_hostname in groups['openstack-controller-nodes']
    - role: ansible-memcached
      when: inventory_hostname in groups['openstack-controller-nodes']
    - role: ansible-rabbitmq
      when: inventory_hostname in groups['openstack-controller-nodes']
    - role: ansible-openstack
  tasks:
````
License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
