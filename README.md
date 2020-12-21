Ansible Role Bind
=========

Configures the Bind DNS server.

This role does not try to guess any server configuration. ACLs for example will
not auto apply themselves.

# Role Variables

| Variable                    | Default              | Comments (type)                                                                                                                      |
|:----------------------------|:---------------------|:-------------------------------------------------------------------------------------------------------------------------------------|
| `bind_acls`                 | `[]`                 | A list of ACL definitions.                                                                                                           |
| `bind_zones`                | n/a                  | A list of mappings with zone definitions. See below this table for examples                                                          |
| `- name`                    | n/a                  | Name of the zonefile                                                                                                                 |
| `- type`                    | n/a                  | Type of zonefile. Currently supports [`slave`]                                                                                       |
| `- masters`                 | n/a                  | A list of masters for the zone. Strings can be used to configure extra options. A master-list can be used.                           |
| `- masters`                 | n/a                  | A list of servers allowed to perform zone transfer. Strings can be used to configure extra options.                                  |

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
