# Ansible Role: Secure Cisco IOS

An Ansible Role for hardening a Cisco IOS router or switch based on the following recommendations.

* [Secure IOS Template by Team Cymru](https://www.cymru.com/Documents/secure-ios-template.html)
* More to add...

## Requirements

* This role depends on the [ansible-filter-cisco-hash](https://github.com/mjuenema/ansible-filter-cisco-hash) role which
  provides custom Jinja2 filters for creating Cisco type 5 and 7 passwords.
* Ansible needs SSH access to the target Cisco IOS routers or switches. A minimal Cisco IOS configuration is shown below.

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
module.

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
secure_cisco_ios_domain_name: example.com
```

This variable has a default value which most certainly will have to be changed to configure `ip domain-name`.

```
secure_cisco_ios_enable_secret:
```

Set this variable to make Ansible configure the enable secret, and remove any enable password. 
Ansible will not change the existing enable secret if this variable is undefined (the default). 

Be careful not to lock yourself (and Ansible) out of the router or switch.

### Local 

## Dependencies

None.

## Example Playbook

    - hosts: webservers
      vars_files:
        - vars/main.yml
      roles:
        - { role: geerlingguy.apache }

*Inside `vars/main.yml`*:

    apache_listen_port: 8080
    apache_vhosts:
      - {servername: "example.com", documentroot: "/var/www/vhosts/example_com"}

## License

MIT / BSD

## Author Information

This role was created in 2014 by [Jeff Geerling](https://www.jeffgeerling.com/), author of [Ansible for DevOps](https://www.ansiblefordevops.com/).
