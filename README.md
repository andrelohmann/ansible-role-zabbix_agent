zabbix_agent
============

Use this role to install zabbix-agent on an ubuntu server.

Requirements
------------

This role requires ubuntu xenials or bionic.

Role Variables
--------------

The following need to be set in group_vars/host_vars

    zabbix_agent_server: 10.20.0.11
    zabbix_agent_serveractive: 10.20.0.11
    zabbix_version: 3.4
    # zabbix_agent_listeninterface: Interface zabbix-agent listens on. Leave blank for all.
    # zabbix_agent_interfaces: A list that configured the interfaces you can use when configuring via API.
    zabbix_url: __ZABBIX_SERVER_URL__
    zabbix_api_create_hosts: True
    zabbix_api_create_hostgroup: True
    zabbix_api_user: __ZABBIX_SERVER_ADMIN__
    zabbix_api_pass: __ZABBIX_SERVER_ADMIN_PASSWORD__
    zabbix_create_hostgroup: present
    zabbix_host_status: enabled
    zabbix_create_host: present
    # zabbix_useuip: 1 if connection to zabbix-agent is made via ip, 0 for fqdn.
    zabbix_useuip: 1
    # zabbix_host_groups: An list of hostgroups which this host belongs to.
    # zabbix_link_templates: An list of templates which needs to be link to this host. The templates should exist.
    # zabbix_macros: An list with macro_key and macro_value for creating hostmacro's.
    # zabbix_inventory_mode: Configure Zabbix inventory mode. Needed for building inventory data, manually when configuring a host or automatically by using some automatic population options. This has to be set to automatic if you want to make automatically building inventory data.
    zabbix_host_groups:
    - Linux Servers
    zabbix_link_templates:
    - Template OS Linux
    # zabbix_link_templates: An list of templates which needs to be link to this host. The templates should exist.
    # zabbix_macros: An list with macro_key and macro_value for creating hostmacro's.
    zabbix_inventory_mode: automatic
    zabbix_agent_hostname: "{{ inventory_hostname }}"
    zabbix_visible_hostname: "{{ inventory_hostname }}"

Example Playbook
----------------

    - hosts: zabbix-agent
      gather_facts: yes
      become: yes
      roles:
      - andrelohmann.zabbix_agent

agent_interfaces
----------------

This will configure the Zabbix Agent interface on the host.

```
zabbix_agent_interfaces:
  - type: 1
    main: 1
    useip: "{{ zabbix_useuip }}"
    ip: "{{ zabbix_agent_ip }}"
    dns: "{{ ansible_fqdn }}"
    port: "{{ zabbix_agent_listenport }}"
```

Other interfaces
----------------

You can also configure the `zabbix_agent_interfaces` to add/configure snmp, jmx and ipmi interfaces.

You'll have to use one of the following type numbers when configuring it:

| Type Interface  |  Nr   |
|-----------------|-------|
| Zabbix Agent  | 1  |
| snmp | 2  |
| ipmi | 3  |
| jmx | 4  |

Configuring a snmp interface will look like this:

```
zabbix_agent_interfaces:
  - type: 2
    main: 1
    useip: "{{ zabbix_useuip }}"
    ip: "{{ agent_ip }}"
    dns: "{{ ansible_fqdn }}"
    port: "{{ agent_listenport }}"
```

Extra Information
-----------------

You can install so-called userparameter files by adding the following into your roles:

```
- name: "Installing sample file"
  copy: src=sample.conf
        dest="{{ agent_include }}/mysql.conf"
        owner=zabbix
        group=zabbix
        mode=0755
  notify: restart zabbix-agent
```

Example of the "sample.conf" file:

```
UserParameter=mysql.ping_to,mysqladmin -uroot ping | grep -c alive
```

You can extend your zabbix configuration by adding items yourself that do specific checks which aren't in the zabbix core itself. You can change offcourse the name of the file to whatever you want (Same for the UserParameter line(s) ;-)

License
-------

MIT

Author Information
------------------

https://github.com/andrelohmann
