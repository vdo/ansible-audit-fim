vdo.audit_fim
=============

A role to run a simple File Integrity Monitor using Linux audit framework rules.

How this role works
-------------------

Creates a systemd service and a timer that runs every X minutes (configurable), and parses the audit logs using `ausearch` utility, shipped with auditd package. The human-readable output monitors any changes in the list of files of directories provided in the variable `audit_fim_targets`.

Role Variables
--------------
The relevant variables are the following:
|Variable|Description|Default Value
|---|---|---|
|`audit_fim_targets`| Files or directories to monitor (required) | []
|`audit_fim_key`| Key used in audit to track events | `audit-fim`
|`audit_fim_log_file`| Log file to store events | `/var/log/audit-fim.log`
|`audit_fim_refresh_time`| Time between parsing events | `5min`

Example Playbook
----------------

```yaml
---
- hosts: all
  become: true
  vars:
    audit_fim_targets:
      - path: '/home/alice/.bashrc'
        permissions: 'w'
      - path: '/etc/passwd'
        permissions: 'wa' # (r)ead, (w)rite, e(x)ecute, (a)ttributes.
      - path: '/etc/hosts'
        permissions: 'wa'
  roles:
    - vdo.audit_fim
```
The logs generated have the following format:
```
At 10:44:47 10/26/23 alice successfully opened-file /home/alice/.bashrc using /usr/bin/vim.basic
At 11:05:44 10/26/23 alice successfully opened-file /home/alice/.bashrc using bash
At 11:06:14 10/26/23 alice, acting as root, successfully opened-file /etc/hosts using bash
```
License
-------
Apache License 2.0
