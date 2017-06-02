### Syslog

Configure Syslog logging. If `secure_cisco_ios_domain_lookup` is set to false (the default) only IP addresses can be used
as log destinations!

```yaml
secure_cisco_ios_logging_destinations: []
secure_cisco_ios_logging_trap_level: informational
secure_cisco_ios_logging_facility: local5
secure_cisco_ios_logging_buffered: 16384
secure_cisco_ios_logging_source_interface:
```

#### Example: Syslog

```yaml
Example:
secure_cisco_ios_logging_destinations:
  - 10.1.2.3
  - 10.1.2.4
secure_cisco_ios_logging_trap_level: debugging
secure_cisco_ios_logging_source_interface: Loopback0
```
