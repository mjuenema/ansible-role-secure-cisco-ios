# Ansible Role: Secure Cisco IOS

**!!!!! This project is currently in the planning stage !!!!!**

An Ansible Role for hardening Cisco IOS routers or switches based on the following recommendations.

* [Secure IOS Template by Team Cymru](https://www.cymru.com/Documents/secure-ios-template.html)
* More to add...

**This is not a general purpose role for configuring Cisco IOS devices.**

The sole objective of this role is to ensure that the configuration of a Cisco IOS router or switch meets a minimum security level.
Configuring interfaces, routing, and so forth is outside the scope of this role. There may even be additional configuration options
to further enhance the security level which are currently not covered by this role.

Important advice:
* Don't blindly trust this role. Try to understand what changes the role may apply.
* Perform dry-runs.
* Always test on non-production routers/switches first.
* Use the `--limit` command line argument as well as the `serial` option for rolling updates.
* See [CONTRIBUTING.md](CONTRIBUTING.md) for information on how to make this role better.

## Requirements

Ansible needs SSH access to the target Cisco IOS routers or switches. A minimal Cisco IOS configuration is shown below.

```
! Minimal Cisco IOS configuration to allow Ansible to connect.
! Copy and paste this into config mode.
! Adjust HOSTNAME, DOMAINNAME, ENABLESECRET, USERNAME and PASSWORD to your needs.
!
hostname HOSTNAME
ip domain-name DOMAINNAME
crypto key generate rsa
service password-encryption
enable secret 0 ENABLESECRET
username USERNAME password 0 PASSWORD
aaa new-model
aaa authentication login default local-case
aaa authentication enable default enable
aaa authorization commands 15 default local
line vty 0 3
  transport input ssh
  login local
```

## Role Variables

There are a large number of role variables that control what settings this role will apply to an Cisco IOS router or switch. 
If applicable the default value is shown but most variables do not have default values. If a variable is undefined the relevant 
settings will usually be omitted from the configuration. This may lead to unintended consequences so please read this section
carefully.

### Ansible connection provider settings

The following settings are neccessary to allow Ansible to SSH into the router/switch and change to enable-mode. If these
variables are undefined the respective values must be provided as environment variables. For more
information check the `provider` section of the [ios_config](https://docs.ansible.com/ansible/ios_config_module.html) 
module. It is strongly recommended to store the sensitive varaibles in an encrypted 
[Vault](http://docs.ansible.com/ansible/playbooks_vault.html).

```
secure_cisco_ios_provider_username: 
secure_cisco_ios_provider_password: 
secure_cisco_ios_provider_authorize: yes
secure_cisco_ios_provider_auth_pass: 
secure_cisco_ios_provider_timeout: 10
secure_cisco_ios_provider_port: 22
```

Currently SSH public-key authentication, i.e. `secure_cisco_ios_provider_ssh_keyfile` is not supported by this role. 

### General settings

```
secure_cisco_ios_domain_lookup: false
```
  
Whether to enable `ip domain-lookup`. If `false` (the default) one has to use IP addresses instead of host names in any other 
configuration settings.

```
secure_cisco_ios_hostname: "{{ inventory_hostname }}"
secure_cisco_ios_domain_name: example.com
```

This variable has a default value which most certainly will have to be changed to configure `ip domain-name`.

```
secure_cisco_ios_enable_secret:
```

Set this variable to make Ansible configure the enable secret, and remove any enable password. 
Ansible will not change the existing enable secret if this variable is undefined (the default). 

Be careful not to lock yourself (and Ansible) out of the router or switch. If necessary update 
`secure_cisco_ios_provider_auth_pass` to the same value before the next execution of this role.

```
secure_cisco_ios_save_config: true
```

Whether to execute `save running-config startup-config` as the last task. Note that the default is true!

### Local users

A list of local users to add in addition to `secure_cisco_ios_provider_username`. This must be a list of dictionaries
as shown in the example below. It is strongly recommended to store `secure_cisco_ios_local_users` in an encrypted 
[Vault](http://docs.ansible.com/ansible/playbooks_vault.html).

```
secure_cisco_ios_local_users: []
```

**Example**

```
secure_cisco_ios_local_users:
  - {'username': 'USER1', 'password': 'PASSWORD1'}
  - {'username': 'USER2', 'secret': 'SECRET2'}
  - {'username': 'USER3', 'secret': 'SECRET3', 'privilege': 15 }
  - {'username': 'USER4', 'nopassword': true}
```

The example above will result in the following Cisco IOS configuration. The password hashing is performed automatically 
through the filters in [ansible-filter-cisco-hash](https://github.com/mjuenema/ansible-filter-cisco-hash).

```
! Local users as defined in the example above
username USER1 password 7 HASH_OF_PASSWORD1 
username USER2 secret 5 HASH_OF_SECRET2
username USER3 privilege 15 secret 5 HASH_OF_SECRET3
username USER4 nopassword
```

More complex `username` definitons must be performed through `secure_cisco_ios_custom_lines`. 

It is also possible to ensure that certain local users are not configured by providing a list of names that must be absent.

```
secure_cisco_ios_absent_local_users: []
```

**Example**

```
secure_cisco_ios_absent_local_users:
  - ABSENT_USER1
  - ABSENT_USER2
```

### TACACS+

Configure TACACS+ AAA. 

```
secure_cisco_ios_tacacs_servers: []
secure_cisco_ios_tacacs_server_key:
secure_cisco_ios_tacacs_server_timeout: 5
secure_cisco_ios_tacacs_source_interface:
```

The following AAA lines are injected if `secure_cisco_ios_tacacs_servers` is non-empty unless 
`secure_cisco_ios_custom_aaa` is defined.

```
! AAA with TACACS+ 
aaa authentication login default group tacacs+ local-case
aaa authentication enable default group tacacs+ enable
aaa authorization commands 15 default group tacacs+ local
aaa accounting exec default stop-only group tacacs+
aaa accounting commands 15 default stop-only group tacacs+
aaa accounting network default stop-only group tacacs+
```

### Radius

This is on the [TODO](TODO.md) list.

### Custom AAA

The `secure_cisco_ios_custom_aaa` variable can be used to define custom AAA settings. This will also prevent TACACS+
and Radius settimgs from making their own AAA adjustments.

```
secure_cisco_ios_custom_aaa: []
```

### Line access

### Syslog

Configure Syslog logging. If `secure_cisco_ios_domain_lookup` is set to false (the default) only IP addresses can be used
as log destinations!

```
secure_cisco_ios_logging_destinations: []
secure_cisco_ios_logging_trap_level: informational
secure_cisco_ios_logging_facility: local5
secure_cisco_ios_logging_buffered: 16384
secure_cisco_ios_logging_source_interface:
```

**Example**

```
Example:
secure_cisco_ios_logging_destinations:
  - 10.1.2.3
  - 10.1.2.4
secure_cisco_ios_logging_trap_level: debugging
secure_cisco_ios_logging_source_interface: Loopback0
```

### Time and NTP

Set timezone.

```
secure_cisco_ios_timezone: UTC
secure_cisco_ios_timezone_offset_hours: 0
secure_cisco_ios_timezone_offset_minutes: 0
```

Configure the router as an NTP client. 

```
secure_cisco_ios_ntp_servers: []
```

`secure_cisco_ios_ntp_servers` must be a list of dictionaries with the following keys.

* `server` (required)
* `version` (1-4)
* `iburst`
* `burst`
* `minpoll` (2^x seconds)
* `maxpoll` (2^x seconds)
* `prefer`
* `source` (source interface)
* `key` (authentication key)

**Example**

```
secure_cisco_ios_timezone: AEST
secure_cisco_ios_timezone_offset_hours: 10
secure_cisco_ios_timezone_offset_minutes: 0
secure_cisco_ios_ntp_servers:
  - {'server': 10.2.3.5', 'source': 'Loopback0'}
  - {'server': 10.2.3.4', 'iburst': true, 'minpoll': 4, 'source': 'Loopback0'}
```

Configure the router as an NTP server.

```
secure_cisco_ios_ntp_master: false
```

Configure NTP keys. The keys are a list of cleartext keys, MD5 hashing will be performed automatically. The keys are
numbered sequentially, i.e. the second key in the list will have a key ID of 1 as the numbering starts with 0. This
is implemented by 
[Looping bver a list with an index](https://docs.ansible.com/ansible/playbooks_loops.html#looping-over-a-list-with-an-index)

```
secure_cisco_ios_ntp_keys: []
```

**Example**

```
secure_cisco_ios_ntp_keys: 
  - CLEARTEXTKEY1
  - CLEARTEXTKEY2
  
secure_cisco_ios_ntp_servers:
  - {'server': 10.2.3.5', 'key': 2}
  # Use CLEARTEXTKEY2 for server 10.2.3.5
```

### Interfaces

Configuring "real" interfaces outside the scope of this role but some security related settings can be performed. In
addition the role will ensure that a `Null0` interface is present.

This is on the [TODO](Todo.md) list.

```
secure_cisco_ios_cdp_enabled_interfaces: []
```

List of interface name prefixes on which to enable Cisco Discovery Protocol (CDP). The role will disable CDP on 
all other interfaces.

**Example**

```
# Enable CDP on FastEthernet interfaces 1 and 2
secure_cisco_ios_cdp_enabled_interfaces:
  - FastEthernet1
  - FastEthernet2
```

```
secure_cisco_ios_shutdown_unused_ethernet_interfaces: false
```

Shutdown any Ethernet interfaces where the line-protocol is down. This may be particularly useful for switches
that are easily physically accessible to prevent people from plugging in additional devices. 

### Banner

```
secure_cisco_ios_banner: |
    ==========================================================================================
                    UNAUTHORIZED ACCESS TO THIS DEVICE IS PROHIBITED
    You must have explicit, authorized permission to access or configure this device.
    Unauthorized attempts and actions to access or use this system may result in civil and/or
    criminal penalties.
    All activities performed on this device are logged and monitored.
    ==========================================================================================
```

### SNMP 



### Netflow

### Exception core files

Configure the router/switch to write exception core files to a TFTP or FTP server. The FTP `secure_cisco_ios_exception_ftp_username`
and `secure_cisco_ios_exception_ftp_password` variables are only used if `secure_cisco_ios_exception_protocol` is changed
to `ftp`. Remember that `secure_cisco_ios_exception_dump` must be an IP address if `secure_cisco_ios_domain_lookup` is false.

```
secure_cisco_ios_exception_dump:
secure_cisco_ios_exception_protocol: tftp
secure_cisco_ios_exception_core_file: "{{ inventory_hostname }}-core"
secure_cisco_ios_exception_ftp_username:
secure_cisco_ios_exception_ftp_password:
```

**Example**

```
secure_cisco_ios_exception_dump: 10.9.8.7
secure_cisco_ios_exception_protocol: ftp
secure_cisco_ios_exception_core_file: "{{ inventory_hostname }}-core"
secure_cisco_ios_exception_ftp_username: USERNAME
secure_cisco_ios_exception_ftp_password: PASSWORD
```

Will result in the following IOS config.

```
! Exception core dump
ip ftp username ftp USERNAME
ip ftp username password 7 HASH_OF_PASSWORD
exception core-file HOSTNAME-core
exception protocol ftp
exception dump 10.9.8.7
```

## Notes

### Probing

There are subtle differences between different Cisco IOS releases and hardware. This role attempts to
auto-detect these differences by executing a number of "probe" tasks with `ignore_errors: yes` that register
variables upon which further tasks can be executed conditionally. 

### Hard-coded settings

Currently this role includes a number of tasks that are hard-coded. The resulting Cisco IOS configuration lines are 
shown below. 

```
no cdp run
no service pad
service tcp-keepalives-in
service tcp-keepalives-out
service timestamps debug datetime msec localtime show-timezone
service timestamps log datetime msec localtime show-timezone
service password-encryption
no ip source-route
no service tcp-small-servers
no service udp-small-servers
!
aaa new-model
aaa session-id common
!
login block-for 100 attempts 15 within 100
login quiet-mode access-class 100
login on-failure log
login on-success log
!
archive
 log config
  logging enable
  logging size 500
  hidekeys
!
interface Null0
 description Null interface to send "naughty" packets to.
 no ip unreachables
!
interface Loopback0
 no ip redirects
 no ip unreachables
 no ip proxy-arp
```

### Custom settings

The variable `secure_cisco_ios_custom_lines` can be used to add any additional configuration lines, e.g.

```
secure_cisco_ios_custom_lines:
  - no service dhcp
```

## Dependencies

This role depends on the [ansible-filter-cisco-hash](https://github.com/mjuenema/ansible-filter-cisco-hash) role which
provides custom Jinja2 filters for creating Cisco type 5 and 7 passwords.

## Example Playbook

TODO.

## License

[BSD](LICENSE)

## Author Information

This role was created in 2017 by [Markus Juenemann](http://www.juenemann.net).
