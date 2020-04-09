fluentd_aggregator
=========

This role installs and configures a fluentd aggregation server. 

Usage
------------
The fluentd aggregation service is a centralized service for virtual machines in the cluster to forward their logs to in order to satisfy security requirements. In order to use this service - follow the "how to" below. 

Requirements
------------

Fluentd requires the td-agent package provided by arn-treasuredata. There is a task within this role that will create the appropriate yum repository and install the package. Typical installation however, should be through a Satellite service.

Role Variables
--------------


| Variable   | Default | Comments (type)  |
| :---       | :---    | :---             |
| `satellite` | "false" | Determines whether to use a Satellite server for installation. |
| `fluentd_storage_config:fluentd_data_disk` | "/dev/xvdb" | Disk to use for physical volume in lvm configuration. This will change with machine types |
| `fluentd_storage_config:vg_name` | "fluentd_vg" | Name of the volume group to create using the data disk |
| `fluentd_storage_config:lv_name` | "fluentd_lv" | Name of the logical volume to create |
| `fluentd_storage_config:lv_size` | "100%FREE" | Size of the logical volume to create |
| `fluentd_firewall_ports` | "22/tcp, 42185/udp" | Ports to use for fluentd service |
| `fluentd_firewall_ports` | "22/tcp, 42185/udp" | Ports to use for fluentd service |
| `fluentd_packages` | "td-agent" | Packages to be installed for fluentd service |
| `system_packages` | "lvm2, firewalld, vim" | Packages to be installed on the system |
| `fluentd_services` | "td-agent" | Service name to be started for fluentd |
| `system_services` | "firewalld" | System services to be started |
| `system_services` | "firewalld" | System services to be started |

Sysctl Settings controlled via variable interpolation
------------------------------------------------------
| Variable Name | Value |
| :--- | :--- |
| `net.core.somaxconn` | '1024' |
| `net.core.netdev_max_backlog` | '5000' |
| `net.core.rmem_max` | '16777216' |
| `net.core.wmem_max` | '16777216'|
| `net.ipv4.tcp_wmem` | '4096 12582912 16777216' |
| `net.ipv4.tcp_rmem` | '4096 12582912 16777216' |
| `net.ipv4.tcp_max_syn_backlog` | '8096' |
| `net.ipv4.tcp_slow_start_after_idle` | '0' |
| `net.ipv4.tcp_tw_reuse` | '1' |
| `net.ipv4.ip_local_port_range` | '10240 65535' |

How To:
-------
```
Perform the following actions on the host sending logs to aggregator.
Update the /etc/rsyslog.conf (if rhel7) to point to the fluentd server:
*.* @<fluentd_ip>:42185

Restart the rsyslog service
systemctl restart rsyslog

Send a test log to the rsyslog server
logger -t <arbitrary_tag_name> "This is a test log sent to syslog facility"

Tail /var/log/td-agent/td-agent.log on the fluentd server to verify the logs are making it across.
```
License
-------

BSD

Author Information
------------------
Author: Jonny Rickard (jonny@redhat.com)
