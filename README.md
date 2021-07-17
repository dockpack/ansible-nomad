# Ansible-Nomad

----

This role was previously maintained by Brian Shumate and is now curated by [@ansible-community/hashicorp-tools](https://github.com/ansible-community).

----



This Ansible role performs basic [Nomad](https://nomadproject.io/)
installation, including filesystem structure, and example configuration.

It will also bootstrap a minimal cluster of 3 server nodes, and can do this
in a development environment based on Vagrant and VirtualBox. See
[README_VAGRANT.md](https://github.com/ansible-community/ansible-nomad/blob/master/examples/README_VAGRANT.md) for more details about the Vagrant setup.

## Requirements

This role requires an Arch Linux, Debian, RHEL, or Ubuntu distribution; the role is tested
with the following specific software versions:

* Ansible: 2.7.10
* nomad: 0.12.1
* Arch Linux
* CentOS: 7
* Debian: 8
* RHEL: 7
* Ubuntu: 16.04
* unzip for [unarchive module](https://docs.ansible.com/ansible/latest/modules/unarchive_module.html#notes)

## Role Variables

The role defines most of its variables in `defaults/main.yml`:

### `nomad_debug`
- Nomad debug mode
- Default value: **no**

### `nomad_skip_ensure_all_hosts`
- Allow running the role even if not all instances are connected
- Default value: **no**

### `nomad_allow_purge_config`
- Allow purging obsolete configuration files. For example, remove server configuration if instance is no longer a server
- Default value: **no**

### `nomad_version`

- Nomad version to install
- Default value: **1.1.1**

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

### `nomad_run_dir`

- Nomad run path
- Default value: `/var/run/nomad`

### `nomad_manage_user`

- Manage Nomad user?
- Default value: **yes**

### `nomad_user`

- Nomad OS username
- Default value: **root**

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

### `nomad_deployment_gc_threshold`

- Deployment garbage collection threshold
- Default value: **1h**

### `nomad_encrypt_enable`

- Enable Gossip Encryption even if `nomad_encrypt` is not set
- Default value: false

### `nomad_encrypt`

- Set the encryption key; should be the same across a cluster. If not present and `nomad_encrypt_enable` is true, the key will be generated & retrieved from the bootstrapped server.
- Default value: **""**

### `nomad_raft_protocol`

- Specifies the version of raft protocal, which used by nomad servers for communication
- Default value: **2**

### `nomad_authoritative_region`

- Specifies the authoritative region, which provides a single source of truth for global configurations such as ACL Policies and global ACL tokens.
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

### `nomad_network_interface`

- Nomad scheduler will choose from the IPs of this interface for allocating tasks
- Default value: none

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

### `nomad_host_volumes`

- List host_volume is used to make volumes available to jobs (Stateful Workloads).
- Default value: **[]**
- Example:

```yaml
nomad_host_volumes:
  - name: data
    path: /var/data
    owner: root
    group: bin
    mode: 0755
    read_only: false
  - name: config
    path: /etc/conf
    owner: root
    group: bin
    mode: 0644
    read_only: false
```

### `nomad_host_networks`

- List host_network is used to make different networks available to jobs instead of selecting a default interface. This is very useful especially in case of multiple nics.
- Default value: **[]**
- Example:

```yaml
nomad_host_networks:
  - name: public
    cidr: 100.101.102.103/24
    reserved_ports: 22,80
  - name: private
    interface: eth0
    reserved_ports: 443
```

### `nomad_options`

- Driver options
- Key value dict
- Default value: **{}**

### `nomad_chroot_env`

- chroot environment definition for the Exec and Java drivers
- Key value dict
- Default value: false

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

### `nomad_podman_enable`

- Installs the podman plugin
- Default value: **false**

### `nomad_cni_enable`

- Installs the cni plugins
- Default value: **false**

### `nomad_docker_enable`

- Install Docker subsystem on nodes?
- Default value: **false**

### `nomad_plugins`
- Allow you configure nomad plugins.
- Default: {}

Example:

```yaml
nomad_plugins:
  nomad-driver-podman:
    config:
      volumes:
        enabled: true
        selinuxlabel: z
      recover_stopped: true
```

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

### `nomad_consul_address`

- The address of your consul API, use it in combination with nomad_use_consul=True. If you want to use https, use `nomad_consul_ssl`. Do NOT append https.
- Default value: **localhost:8500**

### `nomad_consul_ssl`

- If `true` then uses https.
- Default value: **false**

### `nomad_consul_ca_file`

- Public key of consul CA, use in combination with `nomad_consul_cert_file` and `nomad_consul_key_file`.
- Default value: ""

### `nomad_consul_cert_file`

- The public key which can be used to access consul.
- Default value: ""

### `nomad_consul_key_file`

- The private key counterpart of `nomad_consul_cert_file`.
- Default value: ""

### `nomad_consul_servers_service_name`

- The name of the consul service for your nomad servers
- Default value: **nomad-servers**

### `nomad_consul_clients_service_name`

- The name of the consul service for your nomad clients
- Default value: **nomad-clients**

### `nomad_consul_token`

- Token to use for consul interaction
- Default value: **""**

### `nomad_bootstrap_expect`

- Specifies the number of server nodes to wait for before bootstrapping.
- Default value: `{{ nomad_servers | count or 3 }}}

### `nomad_acl_enabled`

- Enable ACLs
- Default value: **no**

### `nomad_acl_token_ttl`

- TTL for tokens
- Default value: **"30s"**

### `nomad_acl_policy_ttl`

- TTL for policies
- Default value: **"30s"**

### `nomad_acl_replication_token`

- Token to use for acl replication on non authoritive servers
- Default value: **""**

### `nomad_vault_enabled`

- Enable vault
- Default value: **no**

### `nomad_vault_address`

- Vault address to use
- Default value: `{{ vault_address | default('0.0.0.0') }}`

### `nomad_vault_allow_unauthenticated`

- Allow users to use vault without providing their own token
- Default value: **yes**

### `nomad_vault_create_from_role`

- Role to create tokens from
- Default value: **""**

### `nomad_vault_ca_file`

- Path of CA cert to use with vault
- Default value: **""**

### `nomad_vault_ca_path`

- Path of a folder containing CA cert(s) to use with vault
- Default value: **""**

### `nomad_vault_cert_file`

- Path to a certificate to use with vault
- Default value: **""**

### `nomad_vault_key_file`

- Path to a private key file to use with vault
- Default value: **""**

### `nomad_vault_tls_server_name`

- Optional string used to set SNI host when connecting to vault
- Default value: **""**

### `nomad_vault_tls_skip_verify`

- Specifies if SSL peer validation should be enforced
- Default value: **no**

### `nomad_vault_token`

- Vault token used by nomad. Will only be installed on servers.
- Default value: **""**

### `nomad_vault_namespace`

- Vault namespace used by nomad
- Default value: **""**

### `nomad_docker_enable`

- Enable docker
- Default value: **no**

### `nomad_docker_dmsetup`

- Run dmsetup on ubuntu (only if docker is enabled)
- Default value: **yes**

### `nomad_tls_enable`

- Enable TLS
- Default value: false

### `nomad_tls_copy_keys`: false

- Whether to copy certs from local machine (controller).
- Default value: false

### `nomad_tls_files_remote_src`

- Whether to copy certs from remote machine itself.
- Default value: false

### `nomad_tls_dir`

- The remote dir where the certs are stored.
- Default value: `/etc/nomad/ssl`

### `nomad_ca_file`

- Use a ca for tls connection, nomad_cert_file and nomad_key_file are needed
- Default value: ca.cert

### `nomad_cert_file`

- Use a certificate for tls connection, nomad_ca_file and nomad_key_file are needed
- Default value: server.crt

### `nomad_key_file`

- Use a key for tls connection, nomad_cert_file and nomad_key_file are needed
- Default value: server.key

### `nomad_rpc_upgrade_mode`

- Use a certificate for tls connection, nomad_ca_file and nomad_key_file are needed, used only when the cluster is being upgraded to TLS, and removed after the migration is complete. This allows the agent to accept both TLS and plaintext traffic.
- Default value: **false**

### `nomad_verify_server_hostname`

- Use a key for tls connection, nomad_cert_file and nomad_key_file are needed. Specifies if outgoing TLS connections should verify the server's hostname.
- Default value: **true**

### `nomad_verify_https_client`

- Use a key for tls connection, nomad_cert_file and nomad_key_file are needed. Specifies agents should require client certificates for all incoming HTTPS requests. The client certificates must be signed by the same CA as Nomad.
- Default value: **true**

### `nomad_telemetry`

- Specifies whether to enable Nomad's telemetry configuration.
- Default value: **false**

### `nomad_telemetry_disable_hostname`

- Specifies if gauge values should be prefixed with the local hostname.
- Default value: "false"

### `nomad_telemetry_collection_interval`

- Specifies the time interval at which the Nomad agent collects telemetry data.
- Default value: "1s"

### `nomad_telemetry_use_node_name`

- Specifies if gauge values should be prefixed with the name of the node, instead of the hostname. If set it will override disable_hostname value.
- Default value: "false"

### `nomad_telemetry_publish_allocation_metrics`

- Specifies if Nomad should publish runtime metrics of allocations.
- Default value: "false"

### `nomad_telemetry_publish_node_metrics`

- Specifies if Nomad should publish runtime metrics of nodes.
- Default value: "false"

### `nomad_telemetry_backwards_compatible_metrics`

- Specifies if Nomad should publish metrics that are backwards compatible with versions below 0.7, as post version 0.7, Nomad emits tagged metrics. All new metrics will only be added to tagged metrics. Note that this option is used to transition monitoring to tagged metrics and will eventually be deprecated.
- Default value: "false"

### `nomad_telemetry_disable_tagged_metrics`

- Specifies if Nomad should not emit tagged metrics and only emit metrics compatible with versions below Nomad 0.7. Note that this option is used to transition monitoring to tagged metrics and will eventually be deprecated.
- Default value: "false"

### `nomad_telemetry_filter_default`

- This controls whether to allow metrics that have not been specified by the filter. Defaults to true, which will allow all metrics when no filters are provided. When set to false with no filters, no metrics will be sent.
- Default value: "true"

### `nomad_telemetry_prefix_filter`

- This is a list of filter rules to apply for allowing/blocking metrics by prefix. A leading "+" will enable any metrics with the given prefix, and a leading "-" will block them. If there is overlap between two rules, the more specific rule will take precedence. Blocking will take priority if the same prefix is listed multiple times.
- Default value: []

### `nomad_telemetry_disable_dispatched_job_summary_metrics`

- Specifies if Nomad should ignore jobs dispatched from a parameterized job when publishing job summary statistics. Since each job has a small memory overhead for tracking summary statistics, it is sometimes desired to trade these statistics for more memory when dispatching high volumes of jobs.
- Default value: "false"

### `nomad_telemetry_statsite_address`

- Specifies the address of a statsite server to forward metrics data to.
- Default value: ""

### `nomad_telemetry_statsd_address`

- Specifies the address of a statsd server to forward metrics to.
- Default value: ""

### `nomad_telemetry_datadog_address`

- Specifies the address of a DataDog statsd server to forward metrics to.
- Default value: ""

### `nomad_telemetry_datadog_tags`

- Specifies a list of global tags that will be added to all telemetry packets sent to DogStatsD. It is a list of strings, where each string looks like "my_tag_name:my_tag_value".
- Default value: []

### `nomad_telemetry_prometheus_metrics`

-  Specifies whether the agent should make Prometheus formatted metrics available at /v1/metrics?format=prometheus.
- Default value: "false"

### `nomad_telemetry_circonus_api_token`

- Specifies a valid Circonus API Token used to create/manage check. If provided, metric management is enabled.
- Default value: ""

### `nomad_telemetry_circonus_api_app`

- Specifies a valid app name associated with the API token.
- Default value: "nomad"

### `nomad_telemetry_circonus_api_url`

- Specifies the base URL to use for contacting the Circonus API.
- Default value: "https://api.circonus.com/v2"

### `nomad_telemetry_circonus_submission_interval`

- Specifies the interval at which metrics are submitted to Circonus.
- Default value: "10s"

### `nomad_telemetry_circonus_submission_url`

- Specifies the check.config.submission_url field, of a Check API object, from a previously created HTTPTRAP check.
- Default value: ""

### `nomad_telemetry_circonus_check_id`

- Specifies the Check ID (not check bundle) from a previously created HTTPTRAP check. The numeric portion of the check._cid field in the Check API object.
- Default value: ""

### `nomad_telemetry_circonus_check_force_metric_activation`

- Specifies if force activation of metrics which already exist and are not currently active. If check management is enabled, the default behavior is to add new metrics as they are encountered. If the metric already exists in the check, it will not be activated. This setting overrides that behavior.
- Default value: "false"

### `nomad_telemetry_circonus_check_instance_id`

- Serves to uniquely identify the metrics coming from this instance. It can be used to maintain metric continuity with transient or ephemeral instances as they move around within an infrastructure. By default, this is set to hostname:application name (e.g. "host123:nomad").
- Default value: ""

### `nomad_telemetry_circonus_check_search_tag`

- Specifies a special tag which, when coupled with the instance id, helps to narrow down the search results when neither a Submission URL or Check ID is provided. By default, this is set to service:app (e.g. "service:nomad").
- Default value: ""

### `nomad_telemetry_circonus_check_display_name`

- Specifies a name to give a check when it is created. This name is displayed in the Circonus UI Checks list.
- Default value: ""

### `nomad_telemetry_circonus_check_tags`

- Comma separated list of additional tags to add to a check when it is created.
- Default value: ""

### `nomad_telemetry_circonus_broker_id`

- Specifies the ID of a specific Circonus Broker to use when creating a new check. The numeric portion of broker._cid field in a Broker API object. If metric management is enabled and neither a Submission URL nor Check ID is provided, an attempt will be made to search for an existing check using Instance ID and Search Tag. If one is not found, a new HTTPTRAP check will be created. By default, this is a random Enterprise Broker is selected, or, the default Circonus Public Broker.
- Default value: ""

### `nomad_telemetry_circonus_broker_select_tag`

- Specifies a special tag which will be used to select a Circonus Broker when a Broker ID is not provided. The best use of this is to as a hint for which broker should be used based on where this particular instance is running (e.g. a specific geographic location or datacenter, dc:sfo).
- Default value: ""

### `nomad_autopilot`

- Enable Nomad Autopilot
- To enable Autopilot features (with the exception of dead server cleanup), the raft_protocol setting in the server stanza must be set to 3 on all servers, see parameter nomad_raft_protocol
- Default value: **false**

### `nomad_autopilot_cleanup_dead_servers`

- Specifies automatic removal of dead server nodes periodically and whenever a new server is added to the cluster.
- Default value: **true**

### `nomad_autopilot_last_contact_threshold`

- Specifies the maximum amount of time a server can go without contact from the leader before being considered unhealthy.
- Default value: **200ms**

### `nomad_autopilot_max_trailing_logs`

- Specifies the maximum number of log entries that a server can trail the leader by before being considered unhealthy.
- Default value: **250**

### `nomad_autopilot_server_stabilization_time`

-  Specifies the minimum amount of time a server must be stable in the 'healthy' state before being added to the cluster. Only takes effect if all servers are running Raft protocol version 3 or higher.
- Default value: **10s**

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

Special thanks to the folks listed in [CONTRIBUTORS.md](https://github.com/ansible-community/ansible-nomad/blob/master/CONTRIBUTORS.md) for their
contributions to this project.

Contributions are welcome, provided that you can agree to the terms outlined
in [CONTRIBUTING.md](https://github.com/ansible-community/ansible-nomad/blob/master/CONTRIBUTING.md)
