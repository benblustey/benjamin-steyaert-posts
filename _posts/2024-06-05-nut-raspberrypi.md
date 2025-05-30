---
title: NUT RaspberryPi
date: 2024-06-05
categories: webdev
tags:
  - docker
  - homelab
image: /assets/img/posts/nut-raspberry.jpg
---
``` bash
$ lsusb
$ Bus 001 Device 005: ID 0764:0601 Cyber Power System, Inc. PR1500LCDRT2U UPS
```

`$ sudo apt install nut nut-client nut-server`

`$ sudo nut-scanner -U`

``` bash
[nutdev1]
	driver = "usbhid-ups"
	port = "auto"
	vendorid = "0764"
	productid = "0601"
	product = "CP1500PFCRM2U"
	serial = "BHWNN7006558"
	vendor = "CPS"
	bus = "001"
```

Apache2 wont start due to PiHole using ports 80/443. Changes apache ports to 8080/44301.
`sudo nano /etc/apache2/ports.conf`


http://cirrus.local:8080/cgi-bin/nut/upsstats.cgi?host=cyberpower@localhost



#### Getting SNMP to expose UPS Data

https://github.com/dzomaya/NUTandRpi?tab=readme-ov-file

``` bash
sudo apt install snmp snmpd libsnmp-dev snmp-mibs-downloader
```

Everything was going smoothly until running:
``` bash
$ snmpwalk -v2c -c cyberpower .1.3.6.1.4.1.8072.1.3.2.4.1.2
$ Timeout: No Response from 192.168.1.218
```

Following this git guide to scrape the upsstats page, but since the apache port was changed, snmpd was looking in the right location.

SNMPD does not use port 80, so it shouldn't be an issue.

##### Opening Ports
```
ufw allow 161/udp
ufw allow 162/udp
```

**Updating snpd.conf**

Adding at the top
```
sudo cat snmpd.conf
rocommunity cyberpower
extend-sh upsmodel "/bin/upsc cyberpower ups.model"
extend-sh upsmodel "/bin/upsc cyberpower ups.model"
extend-sh upsserial "/bin/upsc cyberpower ups.serial"
extend-sh upsstatus "/bin/upsc cyberpower ups.status"
extend-sh battcharge "/bin/upsc cyberpower battery.charge"
extend-sh battruntimeest "/bin/upsc cyberpower battery.runtime"
extend-sh battvolts "/bin/upsc cyberpower battery.voltage"
extend-sh inputvolt "/bin/upsc cyberpower input.voltage"
#extend-sh inputHZ "/bin/upsc cyberpower input.frequency"
#extend-sh outputvolt "/bin/upsc cyberpower output.voltage"
```

Commenting out:
```
# agentaddress  127.0.0.1,[::1

....

#rocommunity  public default -V systemonly
#rocommunity6 public default -V systemonly
rocommunity public
```

Then running:
`$ /etc/init.d/snmpd restart`

```
$ snmpwalk -v2c -c cyberpower 192.168.1.218 .1.3.6.1.4.1.8072.1.3.2.4.1.2
NET-SNMP-EXTEND-MIB::nsExtendOutLine."inputHZ".1 = STRING:
NET-SNMP-EXTEND-MIB::nsExtendOutLine."upsmodel".1 = STRING: CP1500PFCRM2U
NET-SNMP-EXTEND-MIB::nsExtendOutLine."battvolts".1 = STRING: 26.2
NET-SNMP-EXTEND-MIB::nsExtendOutLine."inputvolt".1 = STRING: 122.0
NET-SNMP-EXTEND-MIB::nsExtendOutLine."upsserial".1 = STRING: BHWNN7006558
NET-SNMP-EXTEND-MIB::nsExtendOutLine."upsstatus".1 = STRING: OL
NET-SNMP-EXTEND-MIB::nsExtendOutLine."battcharge".1 = STRING: 100
NET-SNMP-EXTEND-MIB::nsExtendOutLine."outputvolt".1 = STRING: 122.0
NET-SNMP-EXTEND-MIB::nsExtendOutLine."battruntimeest".1 = STRING: 2875
```

Config on homecloud telegraf:
```
# # Retrieves SNMP values from remote agents

[[inputs.snmp]]
	# ## Agent addresses to retrieve values from.
	# ## format: agents = ["<scheme://><hostname>:<port>"]
	# ## scheme: optional, either udp, udp4, udp6, tcp, tcp4, tcp6.
	# ## default is udp
	# ## port: optional
	# ## example: agents = ["udp://127.0.0.1:161"]
	# ## agents = ["tcp://127.0.0.1:161"]
	# ## agents = ["udp4://v4only-snmp-agent"]
	agents = ["udp://192.168.1.218:161"]
	version = 2
	community = "cyberpower"
	timeout = "5s"
```


### Connecting unRaid to InfluxDB for separate retension
```
token: XR-uI9oaVTZ20DwBzHhjsjlyTqaQ5qXPG4Yybn64hFzwIRl4lNP_Uo-9r8zrhuQL5GEifN5vQVq0buWdGQAO5g==
```

