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

There are a large number of role variables that control what settings this role will
apply to an Cisco IOS router or switch. If applicable the default value is shown but
most variables do not have default values. If a variable is undefined the relevant 
settings will usually be omitted from the configuration. This may lead to unintended
consequences so please read this section carefully.

Because of the large number of variables this section has been split into 
separate documents.

* [Ansible connection provider settings](docs/variables_provider.md)
* [General settings](docs/variables_general.md)
* [Local users](docs/variables_local_users.md)
* [TACACS+](docs/variables_tacacs.md)
* [Radius](docs/variables_radius.md)
* [Custom AAA](docs/variables_custom_aaa.md)
* [Interfaces](docs/variables_interfaces.md)
* [Line access](docs/variables_line_access.md)
* [Banner](docs/variables_banner.md)
* [Syslog](docs/variables_syslog.md)
* [Time and NTP](docs/variables_time_ntp.md)
* [SNMP](docs/variables_snmp.md)
* [Netflow](docs/variables_netflow.md)
* [Exception core files](docs/variables_exception.md)
* [Custom configuration lines](docs/variables_custom_lines.md)

In addition to role variables, the `ios_facts` module is run to collect
information about the target router or switch. The
page [Example output of `ios_facts`](docs/ios_facts.md) shows examples
for different Cisco IOS routers and switches.

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

## Dependencies

This role depends on the [ansible-filter-cisco-hash](https://github.com/mjuenema/ansible-filter-cisco-hash) role which
provides custom Jinja2 filters for creating Cisco type 5 and 7 passwords.

## Example Playbook

TODO.

## License

[BSD](LICENSE)

## Author Information

This role was created in 2017 by [Markus Juenemann](http://www.juenemann.net).
