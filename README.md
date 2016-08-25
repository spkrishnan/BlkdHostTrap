# BlkdHostTrap

- This is an event script
- This script runs the "show ethernet-switching table" command and looks for any mac address that is listed in the file "blockmaclist.nd"
- Copy this script to the /var/db/script/event directory
- Ensure that the file "blockmaclist.nd" is located in /var/db/script/event directory
- The "blockmaclist.nd" file contains the list of blocked mac addresses. This file will be added to the switch by Network Director. But, this can be created by other tools or created manually depending on the use case.
- If a mac address in the ethernet switching table is found in the blockmaclist file, the script will generate an SNMP trap with the mac address and the interface name
- The SNMP trap event will be named "BLKD_HST" and the message will be in the format of "<mac address>; <interface name>"

- The trap generation functions used in this script are taken from the following script authored by Phil Shafer
  https://github.com/Juniper/junoscriptorium/tree/master/library/juniper/event/snmp/ev-syslog-trap




- On the switch, the following configuration should be used. This configuration will run the event script every 60 seconds to compare the ethernet switching table with the blocked mac address list and generate an alert if there are any blocked mac addresses found. The trap will be sent to the configured trap target

set snmp trap-group testcommunity targets 192.168.10.10

set event-options generate-event look_4_blkd_hosts time-interval 60

set event-options policy look_4_blkd_hosts events look_4_blkd_hosts

set event-options policy look_4_blkd_hosts then event-script rpt_blkd_host.slax

set event-options event-script file rpt_blkd_host.slax
