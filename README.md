# Log Collector:  Rsyslog + Auditd

This repository is being developed by the EPM-DOC and EPM-IASS projects.

## Getting Started

Logs are a critical component of any software or operating system. Logs usually record user’s actions, system events, network activity and so much more, depending on what they are intended for. One of the most widely used logging systems on Linux systems is rsyslog.

### Rsyslog

Rsyslog is a powerful, secure and high-performance log processing system which accepts data from different types of source (systems/applications) and outputs it into multiple formats.

It has evolved from a regular syslog daemon to a fully-featured, enterprise level logging system. It is designed in a client/server model, therefore it can be configured as a client and/or as a central logging server for other servers, network devices, and remote applications.

### Auditd

auditd is the userspace component to the Linux Auditing System. It's responsible for writing audit records to the disk. Viewing the logs is done with the ausearch or aureport utilities. Configuring the audit rules is done with the auditctl utility. During startup, the rules in /etc/audit/audit.rules are read by auditctl. The audit daemon itself has some configuration options that the admin may wish to customize. They are found in the auditd.conf file.

### Audisp-json

Audisp-json correlates messages coming from the kernel's audit (and through audisp) into a single JSON message that is sent directly to a log server (it doesn't use syslog). The JSON format used is MozDef message format.

Regular audit log messages and audisp-json error, info messages still use syslog.

Due to the ring buffer filling up when the front-end HTTP server does not process fast enough, the program may slowly grow in memory for a while on busy systems. It'll stop at 512 messages (hard-coded) buffered.

https://github.com/gdestuynder/audisp-json

## Setup
#### Ansible roles variables

| Variable             | Type       | Scope                                                        |
| -------------------- | ---------- | ------------------------------------------------------------ |
| rs_au_action         | string     | <ul><li>install - apply role (with pkg installation)</li><li>update - apply configuration files</li></ul> |
| rs_au_rsyslog_server | string     | rsyslog server (each region has own server)                  |
| rs_au_rsyslog_port   | int        | rsyslog server port                                          |
| rs_au_packages       | Dictionary | Packages.                                                    |
| rs_au_direct_install | Dictionary | Installation from package files.                             |
| rs_au_configs        | Dictionary | Configuration files                                          |
| rs_au_templates      | Dictionary | Configuration files that build from Jinja templates          |

`os` and `os_version` arrays used for tune installation and configuration process.

#### Role Actions

This section contains `rs_au_action` variable values.

| Value   | Action                                                       |
| ------- | ------------------------------------------------------------ |
| install | Install role. Default value.                                 |
| update  | Update configuration files and restart daemons only.         |
| report  | Generate version report according required packages. Use for audit and control installation process. See reports in `inventory` directory. |

#### Example Playbook

```yaml
- name: "Rsyslog/Audit Installation - MSQ"
  hosts: ha-cloud-minsk
  become: yes
  become_method: sudo
  roles:
    - role: ansible-role-rsyslog
      rs_au_rsyslog_server: "evbyminsa0258.minsk.epam.com"
      rs_au_action: "install"
```

Root (`become: yes`) permission is required.

Mix `rs_au_rsyslog_server` with multiple playbook declaration for applying role to multiple regions.

More details about the solution, see https://kb.epam.com/pages/viewpage.action?pageId=857446795

## Maintainers

* **Evgeny Mukhin**
* **Volodymyr Pashkov**
