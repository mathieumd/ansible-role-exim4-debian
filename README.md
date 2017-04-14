Exim4-Debian
============

An Ansible Role to install and configure [Exim](https://www.exim.org/) for Debian.

Role Variables
--------------

All variables have default values and some examples in `defaults/main.yml`.

Most interesting are:

- Debian configuration is helped by populating the file
  `/etc/exim4/update-exim4.conf.conf`. You can do this by setting all it's
  variables, whose names are prefixed in this role by `exim4_conf_`: for
  `dc_eximconfig_configtype` you would use
  `exim4_conf_dc_eximconfig_configtype`, etc. Those which contains a list of
  values are Yaml list:

```yaml
exim4_conf_dc_eximconfig_configtype: 'internet'
exim4_conf_dc_other_hostnames:
  - example.com
  - sub.example.com
exim4_conf_dc_relay_domains:
  - client1.test
exim4_conf_dc_relay_nets:
  - 10.1.2.3/24
```

- `exim4_conf_dc_use_split_config`: if `'true'` (the default) then the
  role is expecting Debian split-files configuration. Your own split-files,
  which are templates in this Ansible role, must be added to the list
  `exim4_confd_files`, and the path to find them, relative to the role path,
  must be set in `exim4_confd_local_dir` (which must end whith a "/"):

```yaml
# If you want to use Debian split-files configuration:
exim4_conf_dc_use_split_config: 'true'
exim4_confd_local_dir: "../../my_templates/exim4/conf.d/"
exim4_confd_files:
  - main/01_exim4-config_listmacrosdefs_CUSTOM
  - acl/30_exim4-config_check_rcpt.rul

# If you want to use a single-file configuration:
exim4_conf_dc_use_split_config: 'false'
exim4_template_local_dir: "../../my_templates/exim4/"
exim4_template_conf: "exim4.conf.template.j2"
```

- `exim4_other_conf_files`: you may need to add some files, by example the
  `hubbed_hosts` which contains the domain on the left, and it's MTA on the
  right. You would do it like this:

```yaml
exim4_other_conf_files:
  - file: /etc/exim4/hubbed_hosts
    content: |
      # This file is managed by Ansible.
      example.com       relay-mail.example.com
      test.example.com  relay-test.example.com
```

- `exim4_aliases`: a dict of aliases you want in your `/etc/aliases` system
  file:

```yaml
exim4_aliases:
  root: "sysadmins@example.com"
  postmaster: "mailadmins@example.com"
```

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

```yaml
- hosts: eximservers
  vars:
    exim4_aliases:
      root: "sysadmins@example.com"
      postmaster: "mailadmins@example.com"
    exim4_conf_dc_eximconfig_configtype: 'internet'
    exim4_conf_dc_other_hostnames:
      - example.com
    exim4_conf_dc_use_split_config: 'false'
    exim4_template_local_dir: "../../my_templates/exim4/"
    exim4_template_conf: "exim4.conf.template.j2"
  roles:
    - mathieumd.exim4-debian
```

License
-------

GPLv3

