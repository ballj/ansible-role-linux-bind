Ansible Role Bind
=========

Configures the Bind DNS server.

This role does not try to guess any server configuration. ACLs for example will
not auto apply themselves.

The role will configure the bind server as secure as reasonably possible.
Including disable of recursion and ACL on the query/recursion functions.

# Role Variables

| Variable                    | Default                               | Comments (type)                                                                                                    |
|:----------------------------|:--------------------------------------|:--------------------------------------------------------------------------------------------------------------------|
| `bind_listen_ipv4`          | `[{'port':'53','match_list:['any']}]` | A list of ports and IPv4 addresses to listen on.                                                                    |
| `bind_listen_ipv6`          | `[{'port':'53','match_list:['any']}]` | A list of IPv6 addresses to listen on. Can set match_list to `['none']`                                             |
| `bind_acls`                 | `[]`                                  | A list of ACL definitions.                                                                                          |
| `bind_notify_source_v4`     | n/a                                   | Global notify source. Must be in the masters list of the slave server or allow-notify.                              |
| `bind_notify_source_v6`     | n/a                                   | Global notify source. Must be in the masters list of the slave server or allow-notify.                              |
| `bind_allow_query   `       | `['localhost']`                       | ACL for the global allow query option.                                                                              |
| `bind_allow_transfer`       | `['none']`                            | ACL for the global allow transfer option.                                                                           |
| `bind_recursion`            | `false`                               | Global recursion option.                                                                                            |
| `bind_allow_recursion`      | `['none']`                            | ACL for the global allow recursion option.                                                                          |
| `bind_dnssec_enable`        | `true                                 | Enable dnssec globally.                                                                                             |
| `bind_dnssec_validation`    | `auto`                                | Enable dnssec using auto trust. See explanation below.                                                              |
| `bind_zones`                | n/a                                   | A list of mappings with zone definitions. See below this table for examples and additional options.                 |
| `- name`                    | n/a                                   | Name of the zonefile                                                                                                |
| `- type`                    | n/a                                   | Type of zonefile.                                                                                                   |
| `bind_tsig_keys`            | `[]`                                  | A list of TSIG keys to add. See below for an example.                                                               |

## Zone Variables

The role currently supports slave and forward zone types. Below are the variables supported by each zone type.

### Slave Zone

| Variable                    | Default                               | Comments (type)                                                                                                     |
|:----------------------------|:--------------------------------------|:--------------------------------------------------------------------------------------------------------------------|
| `- type`                    | n/a                                   | Type of zonefile. Set to `slave` for slave zone.                                                                    |
| `- masters`                 | n/a                                   | A list of masters for the zone. Strings can be used to configure extra options. A master-list can be used.          |
| `- masters`                 | n/a                                   | A list of servers allowed to perform zone transfer. Strings can be used to configure extra options.                 |
| `- notify_source_v4`        | n/a                                   | Notify source for the zone. Must be in the masters list of the slave server or allow-notify.                        |
| `- notify_source_v6`        | n/a                                   | Notify source for the zone. Must be in the masters list of the slave server or allow-notify.                        |

### Forward Zone

| Variable                    | Default                               | Comments (type)                                                                                                     |
|:----------------------------|:--------------------------------------|:--------------------------------------------------------------------------------------------------------------------|
| `- type`                    | n/a                                   | Type of zonefile. Set to `forward` for forward zone.                                                                |
| `- forward_only`            | `false`                               | Set true to enable `forward-only` mode. In this mode BIND will not contact other nameservers if forward fails.      |

# Examples

## Zone Examples

```bash
bind_zones:
  - name: example.com
    type: slave
    masters:
      - 10.0.0.1
      - 10.0.0.2 key "tsig_zone_key"
    allow_notify: ['10.0.0.3', '10.0.0.4']
    also_nofify: ['10.0.0.5', '10.0.0.6']
    allow_transfer: ['10.0.0.5'. '10.0.0.6']
    notify_source_v4: 127.0.0.11
    notify_source_v6: '::1'
```

# DNSSEC

## DNSSEC Validation

[ISC Article](https://kb.isc.org/docs/aa-01547)

`auto`: Uses built-in managed keys
`yes`: Requires manually configured `trusted-keys` or `managed-keys`

# TSIG

## Define Keys

Any keys in the `bind_tsig_keys` variable will be added to the named config.
They will not be applied to any zones or servers automatically.

```bash
    bind_tsig_keys:
      - name: tsig_example
        algorithm: hmac-md5
        secret: 'mZiMNOUYQPMNwsDzrX2ENw=='
```
