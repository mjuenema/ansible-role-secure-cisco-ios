### Exception core files

Configure the router/switch to write exception core files to a TFTP or FTP server. The FTP `secure_cisco_ios_exception_ftp_username`
and `secure_cisco_ios_exception_ftp_password` variables are only used if `secure_cisco_ios_exception_protocol` is changed
to `ftp`. Remember that `secure_cisco_ios_exception_dump` must be an IP address if `secure_cisco_ios_domain_lookup` is false.

```yaml
secure_cisco_ios_exception_dump:
secure_cisco_ios_exception_protocol: tftp
secure_cisco_ios_exception_core_file: "{{ inventory_hostname }}-core"
secure_cisco_ios_exception_ftp_username:
secure_cisco_ios_exception_ftp_password:
```

#### Example

```yaml
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
