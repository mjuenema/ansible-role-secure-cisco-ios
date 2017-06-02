### Interfaces

Configuring "real" interfaces outside the scope of this role but some security related settings can be performed. In
addition the role will ensure that a `Null0` interface is present.

```yaml
secure_cisco_ios_cdp_enabled_interfaces: []
```

List of interface name prefixes on which to enable Cisco Discovery Protocol (CDP). The role will disable CDP on 
all other interfaces.

#### Example: Interfaces with CDP enabled

```yaml
# Enable CDP on FastEthernet interfaces 1 and 2
secure_cisco_ios_cdp_enabled_interfaces:
  - FastEthernet1
  - FastEthernet2
```

```yaml
secure_cisco_ios_shutdown_unused_ethernet_interfaces: false
```

Shutdown any Ethernet interfaces where the line-protocol is down. This may be particularly useful for switches
that are easily physically accessible to prevent people from plugging in additional devices. 
