[![Build Status](https://travis-ci.org/dwurf/ansible-role-backup.svg?branch=master)](https://travis-ci.org/dwurf/ansible-role-backup)

Backup role
===========

This Ansible role will install and configure [tarsnap](https://tarsnap.com) + 
[acts](https://github.com/alexjurkiewicz/acts) to make encrypted backups at 
regular intervals.

These backups are encrypted client-side then stored using the tarsnap service, 
which uses Amazon S3.

Requirements
------------

- A tarsnap account with funds on it
- This role has only been tested on Ubuntu 16.04 (I'll expand it to other
  platforms if there is any interest, let me know by opening an issue)
- An acts config file at `templates/acts.conf.j2` in your playbook
- A tarsnap config file at `templates/tarsnap.conf.j2` in your playbook. 
  See ([how to write a tarsnap config file](https://www.tarsnap.com/man-tarsnap.conf.5.html))

Role Variables
--------------

variable | default value | purpose
--- | --- | ---
`backup__tarsnap_cachedir` | `/usr/local/tarsnap-cache` | Sets the directory tarsnap will use to cache backups (cf. [tarsnap.conf man page](https://www.tarsnap.com/man-tarsnap.conf.5.html)
`backup__tarsnap_keyfile` | `/root/tarsnap.key` | Sets the path where the tarsnap key will be saved
`backup__tarsnap_apt_key` | `40B98B68F04DE775` | ID for the key used to sign the tarsnap package
`backup__tarsnap_username` | `changeme@example.com` | Username for tarsnap.com (only required if you want to generate a new tarsnap key)
`backup__tarsnap_password` | `encrypt me` | Password for tarsnap.com (only required if you want to generate a new tarsnap key)
`backup__acts_config_file` | `/usr/local/etc/acts.conf` | Sets the path where the acts jobs configuration will be saved on the target host
`backup__cron_{minute,hour,dom,month,dow}` | respectively: `28`, `3`, `*`, `*`, `*` | Interval at which to run tarsnap for backups

Notes
-----

This role checks for the file `files/tarsnap/{{ ansible_fqdn }}.key` from the location of the playbook.

If there is no tarsnap key file found at `files/tarsnap/{{ ansible_fqdn }}.key`, a
new Tarsnap key will be generated using the `backup__tarsnap_username` and
`backup__tarsnap_password` variables, and a new machine will be registered as
`{{ ansible_host }}`.

If there is a tarsnap key at `files/tarsnap/{{ ansible_fqdn }}.key`, then that key
will be used instead and no new key generation or machine registration will
occur.


Dependencies
------------

None.

Example Playbook
----------------

```yaml
- hosts: all
  roles:
    - role: dwurf.backup
```

License
-------

MIT

Author Information
------------------

Coaxial <[64b.it](https://64b.it)>
Modified to use acts by [dwurf](https://github.com/dwurf/)
