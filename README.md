# Zabbix-related passages

A set of some zabbix-related passages

## zabbix-agent
If you have configured SELinux in enforcing mode then you might have problem with ```systemctl start zabbix-agentd```. The problem is that SELinux policy does not allow to set rlimit
for zabbix-agentd. To solve this problem you need to build and install SELinux module by running
```
checkmodule -M -m -o zabbix-agent.mod zabbix-agent.te
semodule_package -o zabbix-agent.pp -m zabbix-agent.mod
semodule -i zabbix-agent.pp
```
## systemctl-monitor
A set of files that enables Zabbix to monitor systemd services. 

### Installation
Copy contents of zabbix\_agentd.d folder to your /etc/zabbix/zabbix\_agentd.d folder. Restart zabbix agent.

### Avialable keys
| Key | Description |
| ------------------------------ | ----------- |
| **systemd.service.status[service]** | Return status of service. Available statuses are __active__, __inactive__ or __unknown__

### SELinux
If you have configured SELinux in enforcing mode then you might have problem with this module (i.e. module status is unknown for running module or you start to receiving error messages instead of real status)
This is because the SELinux policy that ships with RedHat/CentOS does not explicitly allow the Zabbix agent to communicate with D-Bus. 
So you need to build and install SELinux module by running 
```
checkmodule -M -m -o zabbix-systemctl.mod zabbix-systemctl.te
semodule_package -o zabbix-systemctl.pp -m zabbix-systemctl.mod
semodule -i zabbix-systemctl.pp
```

## APC UPSD
A set of files that enables Zabbix to monitor UPS connected via apcupsd.

### Installation
Copy contents of zabbix\_agentd.d folder to your /etc/zabbix/zabbix\_agentd.d folder. Restart zabbix agent.
Import APC_UPSD_Linux.xml into your zabbix server.

## synology-ups
Template file that enables monitoring ups connected to Synology NASes. 

### Installation
Import synology_5_ups.xml into your zabbix server.


