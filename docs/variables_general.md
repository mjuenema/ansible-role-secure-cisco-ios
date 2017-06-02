### General settings

```yaml
secure_cisco_ios_domain_lookup: false
```
  
Whether to enable `ip domain-lookup`. If `false` (the default) one has to use IP addresses instead of host names in any other 
configuration settings.

```yaml
secure_cisco_ios_hostname: "{{ inventory_hostname }}"
secure_cisco_ios_domain_name: example.com
```

This variable has a default value which most certainly will have to be changed to configure `ip domain-name`.

```yaml
secure_cisco_ios_enable_secret:
```

Set this variable to make Ansible configure the enable secret, and remove any enable password. 
Ansible will not change the existing enable secret if this variable is undefined (the default). 

Be careful not to lock yourself (and Ansible) out of the router or switch. If necessary update 
`secure_cisco_ios_provider_auth_pass` to the same value before the next execution of this role.

```yaml
secure_cisco_ios_save_config: true
```

Whether to execute `save running-config startup-config` as the last task. Note that the default is true!
