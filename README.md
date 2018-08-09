Teamspeak3 icinga2 check
===============

PHP based teamspeak server performance check, for global or virtual server:

* clients
* average packetloss
* average ping
* uptime

Usage
-------------

This check is tested working on:
* Ubuntu 16.04 - PHP 7.0
* Debian 9 - PHP 7.0 - Teamspeak 3.2.0

no need for query login, all metrics are public
```
./check_teamspeak3_perf --host <localhost> --port <10011> [--virtualport <portnr>]
[--username <username> --password <password>]
[--warning-packetloss <percentage>] [--critical-packetloss <percentage>]
[--warning-ping <ms>] [--critical-ping <ms>]
[--warning-clients <percent>] [--critical-clients <percentage>]
[--minimal-uptime <seconds>]
[--ignore-reserved-slots]       - a reserved slot will be counted as free slot
[--ignore-virtualserverstatus]  - go to UNKNOWN state when virtual server is offline
[--timeout <10>] [--debug]
```

packetloss and ping check can only be used when virtual server is given.<br/>
teamspeak check performs 1/2 telnet commands per run(global/virtual), so no need for whitelisting when done remotely.

#### Global
```
hostinfo
```
#### Virtual server
```
use port=9987
serverinfo
```
### Required TS3 permissions
```
permid=23 permname=b_virtualserver_info_view
```
### Example checkcommand configuration for Icinga2
```
object CheckCommand "check-teamspeak-perf" {
  command = [ "/path/to/your/check_teamspeak3_perf.php" ]
  arguments = {
    "--host" = "$ts3_host$"
    "--port" = $ts3_port$
    "--virtualport" = "$ts3_virtualport$"
    "--username" = "$ts3_username$"
    "--password" = "$ts3_password$"
    /* (int) percentage */
    "--warning-packetloss" = $ts3_packetloss_warning$
    "--critical-packetloss" = $ts3_packetloss_critical$
    "--warning-ping" = $ts3_ping_warning$
    "--critical-ping" = $ts3_ping_critical$
    "--warning-clients" = $ts3_clients_warning$
    "--critical-clients" = $ts3_clients_critical$
    /* (int) seconds */
    "--minmal-uptime" = $ts3_minimal_uptime$
    "--timeout" = $ts3_timeout$
    /* (bool) switch */
    "--ignore-reserved-slots" = { set_if = "$ts3_ignore_reserved_slots$" }
    "--ignore-virtualserverstatus" = { set_if = "$ts3_ignore_virtualserverstatus$" }
  }
  vars.ts3_host="$address$"
  vars.ts3_port=10011
  vars.ts3_virtualport=9997
  vars.ts3_timeout=10
  vars.ts3_minimal_uptime=60
  vars.ts3_packetloss_warning=5
  vars.ts3_packetloss_critical=10
  vars.ts3_ping_warning=50
  vars.ts3_ping_critical=100
  vars.ts3_clients_warning=40
  vars.ts3_clients_critical=80
  vars.ts3_ignore_reserved_slots=false
  vars.ts3_ignore_virtualserverstatus=false
}
```
