# Nomad

[![Build Status](https://travis-ci.org/brianshumate/ansible-nomad.svg?branch=master)](https://travis-ci.org/brianshumate/ansible-nomad)
[![Ansible Galaxy](https://img.shields.io/badge/galaxy-brianshumate.nomad-blue.svg)](https://galaxy.ansible.com/brianshumate/nomad/)
[![Average time to resolve an issue](http://isitmaintained.com/badge/resolution/brianshumate/ansible-nomad.svg)](http://isitmaintained.com/project/brianshumate/ansible-nomad "Average time to resolve an issue")
[![Percentage of issues still open](http://isitmaintained.com/badge/open/brianshumate/ansible-nomad.svg)](http://isitmaintained.com/project/brianshumate/ansible-nomad "Percentage of issues still open")

This Ansible role performs basic [Nomad](https://nomadproject.io/)
installation, including filesystem structure, and example configuration.

It will also bootstrap a minimal cluster of 3 server nodes, and can do this
in a development environment based on Vagrant and VirtualBox. See
[README_VAGRANT.md](https://github.com/brianshumate/ansible-nomad/blob/master/examples/README_VAGRANT.md) for more details about the Vagrant setup.

## Requirements

This role requires a Debian, RHEL, or Ubuntu distribution; the role is tested
with the following specific software versions:

* Ansible: 2.3.0.0
* nomad: 0.7.0
* CentOS: 7
* Debian: 8
* RHEL: 7
* Ubuntu: 16.04

## Role Variables

The role defines most of its variables in `defaults/main.yml`:

### `nomad_debug`
- Nomad debug mode
- Default value: **no**

### `nomad_version`

- Nomad version to install
- Default value: **0.7.0**

### `nomad_architecture_map`

- This variable does not need to be changed in most cases
- Default value: Dictionary translating ansible_architecture to HashiCorp
  architecture naming convention

### `nomad_architecture`

- Host architecture
- Default value: determined by `{{ nomad_architecture_map[ansible_architecture] }}`

### `nomad_pkg`

- Nomad package filename
- Default value: `nomad_{{ nomad_version }}_linux_{{ nomad_architecture }}.zip`

### `nomad_zip_url`

- Nomad download URL
- Default value: `https://releases.hashicorp.com/nomad/{{ nomad_version }}/nomad_{{ nomad_version }}_linux_{{ nomad_architecture }}.zip`

### `nomad_checksum_file_url`

- Nomad checksum file URL
- Default value: `https://releases.hashicorp.com/nomad/{{ nomad_version }}/nomad_{{ nomad_version}}_SHA256SUMS`

### `nomad_bin_dir`

- Nomad binary installation path
- Default value: `/usr/local/bin`

### `nomad_config_dir`

- Nomad configuration file path
- Default value: `/etc/nomad.d`

### `nomad_data_dir`

- Nomad data path
- Default value: `/var/nomad`

### `nomad_lockfile`

- Nomad lockfile path
- Default value: `/var/lock/subsys/nomad`

### `nomad_log_dir`

- Nomad log path
- Default value: `/var/log/nomad`

### `nomad_run_dir`

- Nomad run path
- Default value: `/var/run/nomad`

### `nomad_manage_user`

- Manage Nomad user?
- Default value: **yes**

### `nomad_user`

- Nomad OS username
- Default value: **nomad**

### `nomad_manage_group`

- Manage Nomad group?
- Default value: **no**

### `nomad_group`

- Nomad OS group
- Default value: **bin**

### `nomad_region`

- Default region
- Default value: **global**

### `nomad_datacenter`

- Nomad datacenter label
- Default value: **dc1**

### `nomad_log_level`

- Logging level
- Default value: **INFO**

### `nomad_syslog_enable`

- Log to syslog
- Default value: **true**

### `nomad_iface`

- Nomad network interface
- Default value: `{{ ansible_default_ipv4.interface }}`

### `nomad_client_iface`

- Nomad scheduler will choose from the IPs of this interface for allocating tasks
- Default value: none

### `nomad_node_name`

- Nomad node name
- Default value: `{{ inventory_hostname_short }}`

### `nomad_node_role`

- Nomad node role
- options: *client*, *server*, *both*
- Default value: **client**

### `nomad_leave_on_terminate`

- Send leave on termination
- Default value: **yes**

### `nomad_leave_on_interrupt`

- Send leave on interrupt
- Default value: **no**

### `nomad_disable_update_check`

- Disable update check
- Default value: **no**

### `nomad_retry_max`

- Max retry join attempts
- Default value: **0**

### `nomad_retry_join`

- Enable retry join?
- Default value: **no**

### `nomad_retry_interval`

- Retry join interval
- Default value: **30s**

### `nomad_rejoin_after_leave`

- Rejoin after leave?
- Default value: **no**

### `nomad_enabled_schedulers`

- List of enabled schedulers
- Default value: **service, batch, system**

### `nomad_num_schedulers`

- Number of schedulers
- Default value: `{{ ansible_processor_vcpus }}`

### `nomad_node_gc_threshold`

- Node garbage collection threshold
- Default value: **24h**

### `nomad_job_gc_threshold`

- Job garbage collection threshold
- Default value: **4h**

### `nomad_eval_gc_threshold`

- Eval garbage collection threshold
- Default value: **1h**

### `nomad_encrypt`

- Encryption secret for gossip communication
- Default value: **""**

### `nomad_node_class`

- Nomad node class
- Default value: **""**

### `nomad_no_host_uuid`

- Force the UUID generated by the client to be randomly generated
- Default value: **no**

### `nomad_max_kill_timeout`

- Max kill timeout
- Default value: **30s**

### `nomad_network_speed`

- Overide network link speed (0 = no overide)
- Default value: **0**

### `nomad_cpu_total_compute`

- Overide cpu compute (0 = no overide)
- Default value: **0**

### `nomad_gc_interval`

- Client garbage collection interval
- Default value: **1m**

### `nomad_gc_disk_usage_threshold`

- Disk usage threshold percentage for garbage collection
- Default value: **80**

### `nomad_gc_inodes_usage_threshold`

- Inode usage threshold percentage for garbage collection
- Default value: **70**

### `nomad_gc_parallel_destroys`

- Garbage collection max parallel destroys
- Default value: **2**

### `nomad_reserved`

- Reserved client resources
- Default value: `cpu: {{ nomad_reserved_cpu }}, memory: {{ nomad_reserved_memory }}, disk: {{ nomad_reserved_disk }}, ports: {{ nomad_reserved_ports }}`

### `nomad_reserved_cpu`

- Reserved client CPU
- Default value: **0**

### `nomad_reserved_memory`

- Reserved client memory
- Default value: **0**

### `nomad_reserved_disk`

- Reserved client disk
- Default value: **0**

### `nomad_reserved_ports`

- Reserved client ports
- Default value: **22**

### `nomad_options`

- Driver options
- Key value dict
- Default value: **{}**

### `nomad_meta`

- Meta data
- Key value dict
- Default value: **{}**

### `nomad_bind_address`

- Bind interface address
- Default value: `{{ hostvars[inventory_hostname]['ansible_'+ nomad_iface ]['ipv4']['address'] }}` 

### `nomad_advertise_address`

- Network interface address to advertise to other nodes
- Default value: `{{ hostvars[inventory_hostname]['ansible_'+ nomad_iface ]['ipv4']['address'] }}` 

### `nomad_ports`

- Ports used by Nomad
- Default value: `http: {{ nomad_ports_http }}, rpc: {{ nomad_ports_rpc }}, serf: {{ nomad_ports_serf }}`

### `nomad_ports_http`

- Http port
- Default value: **4646**

### `nomad_ports_rpc`

- RPC port
- Default value: **4647**

### `nomad_ports_serf`

- Serf port
- Default value: **4648**

### `nomad_docker_enable`

- Install Docker subsystem on nodes?
- Default value: **false**

### `nomad_group_name`

- Ansible group that contains all cluster nodes
- Default value: **nomad_instances**

### `nomad_servers`

It's typically not necessary to manually alter this list.

- List of server nodes
- Default value: List of all nodes in `nomad_group_name` with
  `nomad_node_role` set to *server* or *both*

### `nomad_gather_server_facts`

This feature makes it possible to gather the `nomad_bind_address` and
`nomad_advertise_address` from servers that are currently not targeted by the
playbook.

To make this possible the `delegate_facts` option is used. This option is broken
in many Ansible versions, so this feature might not always work.

- Gather facts from servers that are not currently targeted
- Default value: 'no'

### `nomad_use_consul`

- Bootstrap nomad via native consul zero-configuration support
  assumes consul default ports etc.
- Default value: **False**

### `nomad_bootstrap_expect`

- Specifies the number of server nodes to wait for before bootstrapping.
- Default value: `{{ nomad_servers | count or 3 }}}

### `nomad_vault_address`

- Vault address to use
- Default value: `{{ vault_address | default('0.0.0.0') }}`

### `nomad_docker_enable`

- Enable docker
- Default value: **no**

### `nomad_docker_dmsetup`

- Run dmsetup on ubuntu (only if docker is enabled)
- Default value: **yes**

### `nomad_ca_file`

- Use a ca for tls connection, nomad_cert_file and nomad_key_file are needed
- Default value: **""**

### `nomad_cert_file`

- Use a certificate for tls connection, nomad_ca_file and nomad_key_file are needed
- Default value: **""**

### `nomad_key_file`

- Use a key for tls connection, nomad_cert_file and nomad_key_file are needed
- Default value: **""**

#### Custom Configuration Section

As Nomad loads the configuration from files and directories in lexical order,
typically merging on top of previously parsed configuration files, you may set
custom configurations via `nomad_config_custom`, which will be expanded into a file named `custom.json` within your `nomad_config_dir` which will
be loaded after all other configuration by default.

An example usage for enabling `vault`:

```yaml
  vars:
    nomad_config_custom:
      vault:
        enabled          : true
        ca_path          : "/etc/certs/ca"
        cert_file        : "/var/certs/vault.crt"
        key_file         : "/var/certs/vault.key"
        address          : "https://vault.service.consul:8200"
        create_from_role : "nomad-cluster"
```

## Dependencies

Ansible requires GNU tar and this role performs some local use of the
unarchive module, so ensure that your system has `gtar`/`unzip` installed.
Jinja2 templates use ipaddr filter that need `netaddr` python library.

## Example Playbook

Basic nomad installation is possible using the included `site.yml` playbook:

```
ansible-playbook -i <hosts> site.yml
```

You can also simply pass variables in using the `--extra-vars` option to the
`ansible-playbook` command:

```
ansible-playbook -i hosts site.yml --extra-vars "nomad_datacenter=maui"
```

### Vagrant and VirtualBox

See `examples/README_VAGRANT.md` for details on quick Vagrant deployments
under VirtualBox for testing, etc.

## License

BSD

## Author Information

[Brian Shumate](http://brianshumate.com)

## Contributors

Special thanks to the folks listed in [CONTRIBUTORS.md](https://github.com/brianshumate/ansible-nomad/blob/master/CONTRIBUTORS.md) for their
contributions to this project.

Contributions are welcome, provided that you can agree to the terms outlined
in [CONTRIBUTING.md](https://github.com/brianshumate/ansible-nomad/blob/master/CONTRIBUTING.md)
