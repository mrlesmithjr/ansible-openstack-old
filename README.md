Role Name
=========

This role is to provision out a complete OpenStack deployment. Including all services.

Requirements
------------

Any pre-requisites that may not be covered by Ansible itself or the role should be mentioned here. For instance, if the role uses the EC2 module, it may be a good idea to mention in this section that the boto package is required.

Role Variables
--------------

````
---
# defaults file for ansible-openstack
# generate passwords and keys using 'openssl rand -hex 10'
mysql_root_password: []  #Root password for the database
openstack_admin_email: 'admin@{{ pri_domain_name }}'  #defines admin users email
openstack_admin_pass: []  #Password of user admin
openstack_ceilometer_dbpass: []  #Database password for the Telemetry service
openstack_ceilometer_pass: []  #Password of Telemetry service user ceilometer
openstack_cinder_dbpass: []  #Database password for the Block Storage service
openstack_cinder_pass: []  #Password of Block Storage service user cinder
openstack_dash_dbpass: []  #Database password for the dashboard
openstack_debian_repository: 'deb http://ubuntu-cloud.archive.canonical.com/ubuntu trusty-updates/{{ openstack_release }} main'
openstack_demo_email: 'demo@{{ pri_domain_name }}'  #defines demo users email
openstack_demo_pass: []  #Password of user demo
openstack_glance_dbhost: '{{ openstack_services_vip }}'  #defines glance db host
openstack_glance_dbpass: []  #Database password for Image Service
openstack_glance_pass: []  #Password of Image Service user glance
openstack_glance_url: 'http://{{ openstack_services_vip }}'  #http://glance.{{ pri_domain_name }}
openstack_glance_verbose_logging: false  #defines if glance should enable verbose logging for troubleshooting
openstack_heat_dbpass: []  #Database password for the Orchestration service
openstack_heat_pass: []  #Password of Orchestration service user heat
openstack_instance_tunnel_ip: []  #define interface address for tunnel interface....ex. {{ ansible_eth2.ipv4.address }}
openstack_keystone_dbhost: '{{ openstack_services_vip }}'  #defines keystone db host
openstack_keystone_dbpass: []  #Database password of Identity service
openstack_keystone_default_region: regionOne
openstack_keystone_endpoints:
  - service_name: glance
    public_url: "{{ openstack_glance_url }}:9292"
    internal_url: "{{ openstack_glance_url }}:9292"
    admin_url: "{{ openstack_glance_url }}:9292"
  - service_name: keystone
    public_url: "{{ openstack_keystone_url }}:5000/v2.0"
    internal_url: "{{ openstack_keystone_url }}:5000/v2.0"
    admin_url: "{{ openstack_keystone_url }}:35357/v2.0"
  - service_name: neutron
    public_url: "{{ openstack_neutron_url }}:9696"
    internal_url: "{{ openstack_neutron_url }}:9696"
    admin_url: "{{ openstack_neutron_url }}:9696"
  - service_name: nova
    public_url: "{{ openstack_nova_url }}:8774/v2/%(tenant_id)s"
    internal_url: "{{ openstack_nova_url }}:8774/v2/%(tenant_id)s"
    admin_url: "{{ openstack_nova_url }}:8774/v2/%(tenant_id)s"
openstack_keystone_roles:
  - name: admin
    user: admin
    tenant: admin
  - name: admin
    user: glance
    tenant: service
  - name: admin
    user: neutron
    tenant: service
  - name: admin
    user: nova
    tenant: service
  - name: _member_
    user: demo
    tenant: demo
openstack_keystone_services:
  - name: glance
    description: "OpenStack Image Service"
    service_type: image
  - name: keystone
    description: "OpenStack Identity"
    service_type: identity
  - name: neutron
    description: "OpenStack Networking"
    service_type: network
  - name: nova
    description: "OpenStack Compute"
    service_type: compute
openstack_keystone_temp_admin_token: []  #Key for initial setup of keystone
openstack_keystone_tenants:
  - name: admin
    description: "Admin Tenant"
  - name: demo
    description: "Demo Tenant"
  - name: service
    description: "Service Tenant"
openstack_keystone_url: 'http://{{ openstack_services_vip }}'  #http://keystone.{{ pri_domain_name }}
openstack_keystone_users:
  - name: admin
    password: "{{ openstack_admin_pass }}"
    email: "{{ openstack_admin_email }}"
    tenant: admin
  - name: demo
    password: "{{ openstack_demo_pass }}"
    email: "{{ openstack_demo_email }}"
    tenant: demo
  - name: glance
    password: "{{ openstack_glance_pass }}"
    tenant: service
  - name: neutron
    password: "{{ openstack_neutron_pass }}"
    tenant: service
  - name: nova
    password: "{{ openstack_nova_pass }}"
    tenant: service
openstack_keystone_verbose_logging: false  #defines if keystone should enable verbose logging for troubleshooting
openstack_networking: neutron  #defines networking to use...neutron or nova (legacy)
openstack_neutron_dbhost: '{{ openstack_services_vip }}'  #defines neutron db host
openstack_neutron_dbpass: []  #Database password for the Networking service
openstack_neutron_pass: []  #Password of Networking service user neutron
openstack_neutron_url: 'http://{{ openstack_services_vip }}'
openstack_neutron_verbose_logging: false  #defines if neutron should enable verbose logging for troubleshooting
openstack_nova_dbhost: '{{ openstack_services_vip }}'  #defines nova db host
openstack_nova_dbpass: []  #Database password for Compute service
openstack_nova_my_ip: '{{ ansible_default_ipv4.address }}'  #defines the my_ip variable in nova
openstack_nova_pass: []  #Password of Compute service user nova
openstack_nova_url: 'http://{{ openstack_services_vip }}'  #http://nova.{{ pri_domain_name }}
openstack_nova_virt_type: kvm  #Nova virtualization Type, set to KVM if supported and QEMU if not
openstack_rabbit_host: '{{ openstack_services_vip }}'  #Defines RabbitMQ host
openstack_rabbit_pass: []  #Password of user of RabbitMQ
openstack_rabbit_user: openstack  #User of RabbitMQ
openstack_release: kilo  #defines openstack release to install
openstack_services_vip: []  #define VIP or common IP|FQDN name for all services to communicate with.
openstack_trove_dbpass: []  #Database password of Database service
openstack_trove_pass: []  #Password of Database Service user trove
pri_domain_name: example.org  #defines primary domain name of site.
````

Dependencies
------------

None

Example Playbook
----------------
````
---
- name: Sets Up Base Apps
  hosts: openstack-nodes
  sudo: true
  vars:
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

# Notes
# NTP on compute/network node(s) should point to controller node(s)
- name: Builds OpenStack Environment
  hosts: openstack-nodes
  sudo: true
  vars:
  roles:
    - role: ansible-openstack
  tasks:

- name: Local Tasks
  hosts: localhost
  connection: local
  sudo: false
  vars:
  tasks:
    - name: uploading Cirros Cloud Image to Glance
      glance_image:
        auth_url: "{{ openstack_glance_url }}:35357/v2.0/"
        login_username: admin
        login_password: "{{ openstack_admin_pass }}"
        login_tenant_name: admin
        name: cirros
        container_format: bare
        disk_format: qcow2
        state: present
        copy_from: http://download.cirros-cloud.net/0.3.4/cirros-0.3.4-x86_64-disk.img
    - name: creating admin script(s)
      template:
        src: templates/admin-openrc.sh.j2
        dest: ./admin-openrc.sh
        mode: 0700
````
group_vars/all.yml
````
---
mysql_allow_remote_connections: true  #defines if mysql should listen on loopback (default) or allow remove connections
mysql_root_password: 61fea78e16dd73bd757a  #Root password for the database
openstack_admin_pass: 29b1416692cb38014ea0  #Password of user admin
openstack_ceilometer_dbpass: 85c4ac62a58c0a25a922  #Database password for the Telemetry service
openstack_ceilometer_pass: c3e1b1db22e9b33cd7f4  #Password of Telemetry service user ceilometer
openstack_cinder_dbpass: bcb4f4279d699c12c6ea  #Database password for the Block Storage service
openstack_cinder_pass: d9eb36abbb2e88356208  #Password of Block Storage service user cinder
openstack_dash_dbpass: cb77b8806e4dc8693d8e  #Database password for the dashboard
openstack_demo_pass: 54a27efd264beeb7843d  #Password of user demo
openstack_glance_dbpass: 295062a986b8deded530  #Database password for Image Service
openstack_glance_pass: 60278dafa015c0cc3943  #Password of Image Service user glance
openstack_glance_url: 'http://{{ openstack_services_vip }}'  #http://glance.{{ pri_domain_name }}
openstack_glance_verbose_logging: true  #defines if glance should enable verbose logging for troubleshooting
openstack_heat_dbpass: d69d8f9bd2a70f0f1f5b  #Database password for the Orchestration service
openstack_heat_pass: 9fd0ae5b48837ad34b31  #Password of Orchestration service user heat
openstack_keystone_dbpass: 8ed178efd2bef6fcabe3  #Database password of Identity service
openstack_keystone_temp_admin_token: 52f5e14ad1a9f7d54e1d  #Key for initial setup of keystone
openstack_keystone_url: 'http://{{ openstack_services_vip }}'  #http://keystone.{{ pri_domain_name }}
openstack_keystone_verbose_logging: true  #defines if keystone should enable verbose logging for troubleshooting
openstack_neutron_dbpass: 7bff44471bdce25d55af  #Database password for the Networking service
openstack_neutron_pass: 19440ed58e9ba153bbb5  #Password of Networking service user neutron
openstack_neutron_verbose_logging: true  #defines if neutron should enable verbose logging for troubleshooting
openstack_nova_dbpass: b544e0b6881f33c82dc8  #Database password for Compute service
openstack_nova_my_ip: '{{ ansible_ssh_host }}'  #defines the my_ip variable in nova
openstack_nova_pass: 34e5f8990ef84cc69f91  #Password of Compute service user nova
openstack_nova_virt_type: qemu  #Nova virtualization Type, set to KVM if supported and QEMU if not
openstack_rabbit_pass: 6bd8dbb369181e89bf3a  #Password of user guest of RabbitMQ
openstack_rabbit_user: openstack  #User of RabbitMQ
openstack_services_vip: 192.168.202.11  #define VIP or common IP|FQDN name for all services to communicate with.
openstack_trove_dbpass: c6c2792fa14319dbe9da  #Database password of Database service
openstack_trove_pass: 892ba2ff14319c299b54  #Password of Database Service user trove
pri_domain_name: example.org  #defines primary domain name of site.
update_etc_hosts: true
````
group_vars/openstack-network-nodes.yml
````
---
openstack_instance_tunnel_ip: '{{ ansible_eth2.ipv4.address }}'  #define interface address for tunnel interface....ex. {{ ansible_eth2.ipv4.addres }}
````
Inventory Hosts
````
# Generated by Vagrant

compute-1 ansible_ssh_host=127.0.0.1 ansible_ssh_port=2222 ansible_ssh_private_key_file=/Users/larrysmith/Git_Projects/gerrit.everythingshouldbevirtual.local/ansible-openstack/vagrant/.vagrant/machines/compute-1/virtualbox/private_key
compute-2 ansible_ssh_host=127.0.0.1 ansible_ssh_port=2200 ansible_ssh_private_key_file=/Users/larrysmith/Git_Projects/gerrit.everythingshouldbevirtual.local/ansible-openstack/vagrant/.vagrant/machines/compute-2/virtualbox/private_key
controller-1 ansible_ssh_host=127.0.0.1 ansible_ssh_port=2201 ansible_ssh_private_key_file=/Users/larrysmith/Git_Projects/gerrit.everythingshouldbevirtual.local/ansible-openstack/vagrant/.vagrant/machines/controller-1/virtualbox/private_key
network-1 ansible_ssh_host=127.0.0.1 ansible_ssh_port=2202 ansible_ssh_private_key_file=/Users/larrysmith/Git_Projects/gerrit.everythingshouldbevirtual.local/ansible-openstack/vagrant/.vagrant/machines/network-1/virtualbox/private_key

[openstack-nodes]
compute-1
compute-2
controller-1
network-1

[openstack-controller-nodes]
controller-1

[openstack-compute-nodes]
compute-1
compute-2

[openstack-network-nodes]
network-1
````

License
-------

BSD

Author Information
------------------

Larry Smith Jr.
- @mrlesmithjr
- http://everythingshouldbevirtual.com
- mrlesmithjr [at] gmail.com
