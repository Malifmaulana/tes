# Amazon Linux 7 CIS


Configure Amazon Linux 2 machine to be [CIS](https://www.cisecurity.org/cis-benchmarks/) compliant. Level 1 and 2 findings will be corrected by default.

This role **will make changes to the system** that could break things. This is not an auditing tool but rather a remediation tool to be used after an audit has been conducted.

Based on [CIS Amazon Linux 2 Benchmark v1.0.0 - 2018-10-14 ](https://workbench.cisecurity.org/benchmarks/810).

This repo originated from RHEL 7 CIS work done by [Ansible Lockdown](https://github.com/ansible-lockdown/RHEL7-CIS)

## Requirements


You should carefully read through the tasks to make sure these changes will not break your systems before running this playbook.

## THINGS TO NOTE

### Packet Forwarding and Redirect

Section **3.1 Network Parameters** disables IP forwarding and packet redirects. However, host that will acts as a Network Appliance, Router, Proxy, or Kubernetes Worker will need to enable this feature.

To enable IP forwarding and packet redirects, set the *amzn2cis_is_router* variables to true:

```yaml
amzn2cis_is_router: false
```
- ssh allowed user: 5.2.18
- pwquality variable

## Role Variables

There are many role variables defined in defaults/main.yml. This list shows the most important.

**amzn2cis_notauto**: Run CIS checks that we typically do NOT want to automate due to the high probability of breaking the system (Default: false)

**amzn2cis_section1**: CIS - General Settings (Section 1) (Default: true)

**amzn2cis_section2**: CIS - Services settings (Section 2) (Default: true)

**amzn2cis_section3**: CIS - Network settings (Section 3) (Default: true)

**amzn2cis_section4**: CIS - Logging and Auditing settings (Section 4) (Default: true)

**amzn2cis_section5**: CIS - Access, Authentication and Authorization settings (Section 5) (Default: true)

**amzn2cis_section6**: CIS - System Maintenance settings (Section 6) (Default: true)  

You can also disable individual section by setting the variable for that section to false:
```
amzn2cis_rule_1_1_1_8: false
amzn2cis_rule_1_1_2: false
amzn2cis_rule_2_1_2: false
```
By default, section variables has `true` value

### Partition Configuration
```
amzn2cis_partition:
  var:
    size: 6G
    lv_name: var
    mnt_opts: defaults,nodev
    mnt_path: /var
    systemd_name: var
  var_tmp:
    size: 10G
    lv_name: vartmp
    mnt_opts: defaults,nodev,nosuid,noexec
    mnt_path: /var/tmp
    systemd_name: var-tmp
  var_log:
    size: 20G
    lv_name: varlog
    mnt_opts: defaults,nodev
    mnt_path: /var/log
    systemd_name: var-log
  var_log_audit:
    size: 6G
    lv_name: varlogaudit
    mnt_opts: defaults,nodev
    mnt_path: /var/log/audit
    systemd_name: var-log-audit
  home:
    size: 5G
    lv_name: home
    mnt_opts: defaults,nodev
    mnt_path: /home
    systemd_name: home
  etcd:
    size: 1.5G
    lv_name: varlibetcd
    mnt_opts: defaults,nodev
    mnt_path: /var/lib/etcd
    systemd_name: var-lib-etcd
  docker:
    size: 1G
    lv_name: varlibdocker
    mnt_opts: defaults,nodev
    mnt_path: /var/lib/docker
    systemd_name: var-lib-docker
amzn2cis_vg_name: vg1
```
Make sure that a Volume of 50GB is attached to the instance

##### Disable all selinux functions
`amzn2cis_selinux_disable: false`

##### Service variables:
###### These control whether a server should or should not be allowed to continue to run these services

```
amzn2cis_avahi_server: false  
amzn2cis_cups_server: false  
amzn2cis_dhcp_server: false  
amzn2cis_ldap_server: false  
amzn2cis_telnet_server: false  
amzn2cis_nfs_server: false  
amzn2cis_rpc_server: false  
amzn2cis_ntalk_server: false  
amzn2cis_rsyncd_server: false  
amzn2cis_tftp_server: false  
amzn2cis_rsh_server: false  
amzn2cis_nis_server: false  
amzn2cis_snmp_server: false  
amzn2cis_squid_server: false  
amzn2cis_smb_server: false  
amzn2cis_dovecot_server: false  
amzn2cis_httpd_server: false  
amzn2cis_vsftpd_server: false  
amzn2cis_named_server: false  
amzn2cis_bind: false  
amzn2cis_vsftpd: false  
amzn2cis_httpd: false  
amzn2cis_dovecot: false  
amzn2cis_samba: false  
amzn2cis_squid: false  
amzn2cis_net_snmp: false  
```  

##### Designate server as a Mail server
`amzn2cis_is_mail_server: false`


##### System network parameters (host only OR host and router)
`amzn2cis_is_router: false`  


##### IPv6 required
`amzn2cis_ipv6_required: true`  


##### AIDE
`amzn2cis_config_aide: true`

###### AIDE cron settings
```
amzn2cis_aide_cron:
  cron_user: root
  cron_file: /etc/crontab
  aide_job: '/usr/sbin/aide --check'
  aide_minute: 0
  aide_hour: 5
  aide_day: '*'
  aide_month: '*'
  aide_weekday: '*'  
```

##### SELinux policy
`amzn2cis_selinux_pol: targeted` 


##### Set to 'true' if X Windows is needed in your environment
`amzn2cis_xwindows_required: no` 


##### Client application requirements
```
amzn2cis_openldap_clients_required: false 
amzn2cis_telnet_required: false 
amzn2cis_talk_required: false  
amzn2cis_rsh_required: false 
amzn2cis_ypbind_required: false 
```

##### Time Synchronization
```
amzn2cis_time_synchronization: chrony
amzn2cis_time_Synchronization: ntp

amzn2cis_time_synchronization_servers:
    - 0.pool.ntp.org
    - 1.pool.ntp.org
    - 2.pool.ntp.org
    - 3.pool.ntp.org  
```  
  
##### 3.4.2 | PATCH | Ensure /etc/hosts.allow is configured
```
amzn2cis_host_allow:
  - "10.0.0.0/255.0.0.0"  
  - "172.16.0.0/255.240.0.0"  
  - "192.168.0.0/255.255.0.0"    
```  
  

Dependencies
------------

Ansible > 2.2

Example Playbook
-------------------------

This sample playbook should be run in a folder that is above the main Amazon Linux 2-CIS / Amazon Linux 2-CIS-devel folder.

```
- name: Harden Server
  hosts: servers
  become: yes

  roles:
    - amzn-2
```

Tags
----
Many tags are available for precise control of what is and is not changed.

Some examples of using tags:

```
    # Audit and patch the site
    ansible-playbook site.yml --tags="patch"
```

License
-------

MIT
