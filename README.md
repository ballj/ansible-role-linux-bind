Ansible Role Bind
=========

Configures the Bind DNS server.

This role does not try to guess any server configuration. ACLs for example will
not auto apply themselves.

The role will configure the bind server as secure as reasonably possible.
Including disable of recursion and ACL on the query/recursion functions.

# Role Variables

| Variable                    | Default              | Comments (type)                                                                                                                      |
|:----------------------------|:--------------------------------------|:--------------------------------------------------------------------------------------------------------------------|
| `bind_listen_ipv4`          | `[{'port':'53','match_list:['any']}]` | A list of ports and IPv4 addresses to listen on.                                                                    |
| `bind_listen_ipv6`          | `[{'port':'53','match_list:['any']}]` | A list of IPv6 addresses to listen on. Can set match_list to `['none']`                                             |
| `bind_acls`                 | `[]`                                  | A list of ACL definitions.                                                                                          |
| `bind_allow_query   `       | `['localhost']`                       | ACL for the global allow query option.                                                                              |
| `bind_allow_transfer`       | `['none']`                            | ACL for the global allow transfer option.                                                                           |
| `bind_recursion`            | `false`                               | Global recursion option.                                                                                            |
| `bind_allow_recursion`      | `['none']`                            | ACL for the global allow recursion option.                                                                          |
| `bind_dnssec_enable`        | `true                                 | Enable dnssec globally.                                                                                             |
| `bind_dnssec_validation`    | `auto`                                | Enable dnssec using auto trust. See explanation below.                                                              |
| `bind_zones`                | n/a                                   | A list of mappings with zone definitions. See below this table for examples                                         |
| `- name`                    | n/a                                   | Name of the zonefile                                                                                                |
| `- type`                    | n/a                                   | Type of zonefile. Currently supports [`slave`]                                                                      |
| `- masters`                 | n/a                                   | A list of masters for the zone. Strings can be used to configure extra options. A master-list can be used.          |
| `- masters`                 | n/a                                   | A list of servers allowed to perform zone transfer. Strings can be used to configure extra options.                 |
| `bind_tsig_keys`            | `[]`                                  | A list of TSIG keys to add. See below for an example.                                                               |

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
