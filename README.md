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
Copy contents of ```zabbix\_agentd.d``` folder to your ```/etc/zabbix/zabbix\_agentd.d folder```. Restart zabbix agent.

### Avialable keys
| Key | Description |
| ------------------------------ | ----------- |
| **systemd.service.status[service]** | Return status of service. Available statuses are __active__, __inactive__ or __unknown__ |

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
Copy contents of ```zabbix\_agentd.d``` folder to your ```/etc/zabbix/zabbix\_agentd.d``` folder. Restart zabbix agent.
Import ```APC_UPSD_Linux.xml``` into your zabbix server.

### Avialable keys
| Key | Description |
| ----------- | ------------------------------ |
| **apcupsd.status** | Return status of APC UPS. One of the following: _CAL_ _TRIM_ _BOOST_ _ONLINE_ _ONBATT_ _OVERLOAD_ _LOWBATT_ _REPLACEBATT_ _NOBATT_ _SLAVE_ _SLAVEDOWN_ _COMLOST_ _SHUTTING_ |
| **apcupsd.name** | UPS name from configuration file for dumb upses or eprom for smart |
| **apcupsd.model** | UPS model from ups information |
| **apcupsd.version** | apcupsd version information |
| **apcupsd.linev** | UPS Current input line voltage |
| **apcupsd.load** | Percentage of UPS load capacity used as estimated by UPS |
| **apcupsd.load** | Percentage of UPS load capacity used as estimated by UPS |
| **apcupsd.timeleft** | Remaining runtime left on battery as estimated by the UPS |
| **apcupsd.batt.charge** | Current battery capacity charge percentage |
| **apcupsd.batt.V** | Current battery charge voltage |
| **apcupsd.batt.nominal** | Nominal battery voltage |

### Template triggers
Template provide a set of triggers

| Description | Severinity | Event |
| ------------------------------ | ----------- | ------------------------------ |
| UPS initiated system shutdown | Disaster | **apcuspd.status**=='SHUTTING' |
| Low estimation on battarey | High | **apcupsd.status**=='ONBATT' and (**apcupsd.batt.charge**<20 or **apcupsd.timeleft**<3) |
| Lost connection with UPS | High | **apcupsd.status**='COMLOST' |
| UPS on battary | Average | **apcupsd.status**='ONBATT' |


## synology-ups
Template file that enables monitoring ups connected to Synology NASes. 

### Installation
Import ```synology_5_ups.xml``` into your zabbix server.

### Avialable keys
| Key | Description |
| ----------- | ------------------------------ |
| **syno.ups.battary.charge[*]** | UPS battary charge  |
| **syno.ups.battary.chargelow[*]** | Low battary charge level from ups driver settings  |
| **syno.ups.battary.chargewarning[*]** | Warning battary charge level from ups driver settings  |
| **syno.ups.voltage.current[*]** | Current UPS voltage  |
| **syno.ups.voltage.nominal[*]** | Nominal UPS voltage  |
| **syno.ups.line.voltage.in[*]** | UPS line in voltage  |
| **syno.ups.line.voltage.out[*]** | UPS line out voltage  |
| **syno.ups.load[*]** | UPS load  |
| **syno.ups.timeleft[*]** | UPS time left on battary |
| **syno.ups.status** | UPS status |
| **syno.ups.model** | UPS model |
| **syno.ups.version** | UPS driver version |

### Template macros
| Name | Value |
| ----------- | ------------------------------ |
| UPS_TIME_LEFT | 180 |
### Template triggers
Template provide a set of triggers

| Description | Severinity | Event |
| ------------------------------ | ----------- | ------------------------------ |
| Empty battary | High | **syno.ups.battary.charge[*]**==**syno.ups.battary.chargelow[*]** |
| Almost no time left on battary | High | **syno.ups.timeleft[*]**<${UPS_TIME_LEFT} |
| Low battary | Average | **syno.ups.battary.charge[*]**==**syno.ups.battary.chargewarning[*]** |
| Power loss or ups problem | Warning | **syno.ups.battary.status[*]**.str(OL)==0 |

