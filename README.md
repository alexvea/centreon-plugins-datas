# centreon-plugins-datas

## Description
This repository includes datas that can be used by snmpsim (for SNMP protocol) or by any API mock server (for HTTP protocol) to simulate result for [Centreon plugins](https://github.com/centreon/centreon-plugins) .


## Method to update centreon-plugins-datas folders in case of new Centreon plugins :


````
#in my case, i have chosen to download the github repositories in github user home folder.
$cd /home/github/
#download centreon-plugins repository
$git clone git@github.com:centreon/centreon-plugins.git
#download centreon-plugins-datas repository
$git clone git@github.com:alexvea/centreon-plugins-datas.git
#check if new folder has been created in centreon-plugins, if so, it will create the folder in centreon-plugins-data with a .gitkeep file. We ignore mode, custom mode, components folders which we don't need.
$for folder in `find /home/github/centreon-plugins/src -type d | grep -v "mode$" | grep -v "components$" | grep -v "custom$"`; do PATH_TO_CHECK="centreon-plugins-datas/${folder/\/home\/github\/centreon-plugins\//}"; [[ ! -d ${PATH_TO_CHECK} ]]  && mkdir -v -p ${PATH_TO_CHECK} && touch ${PATH_TO_CHECK}/.gitkeep && echo "touch: .gitkeep for ${PATH_TO_CHECK} folder"; done
#check if need to commit and PR
$cd /home/github/centreon-plugins-datas
$git status
# On branch main
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#       src/storage/wd/nas/snmp/tata/
nothing added to commit but untracked files present (use "git add" to track)
#commit the new plugin folder
$git commit -m "create folder for XXX"
#pull request on main branch
$git push origin main
````

## How to use Docker to implement snmpsim

Prerequisites : 

1)install docker

2)clone this current repository for exemple in /home/github folder

Installation : 

3)Follow this tutotial to run snmpsim in a docker container : https://github.com/tandrup/docker-snmpsim
````
docker run -v /home/github:/usr/local/snmpsim/data \
           -p 161:161/udp \
           tandrup/snmpsim
````
4)test snmpsimp with a default existing folder (public):
````
# snmpwalk -Obentu -v2c -c "public" 10.25.15.161 | head
system.sysDescr.0 = STRING: Linux zeus 4.8.6.5-smp #2 SMP Sun Nov 13 14:58:11 CDT 2016 i686
system.sysObjectID.0 = OID: enterprises.netSnmp.netSnmpEnumerations.netSnmpAgentOIDs.10
system.sysUpTime.sysUpTimeInstance = 125642385
system.sysContact.0 = STRING: SNMP Laboratories, info@snmplabs.com
system.sysName.0 = STRING: zeus.snmplabs.com (you can change this!)
system.sysLocation.0 = STRING: San Francisco, California, United States
system.sysServices.0 = INTEGER: 72
system.sysORLastChange.0 = 125642387
system.sysORTable.sysOREntry.sysORID.1 = OID: .iso.org.dod.internet.snmpV2.snmpModules.snmpFrameworkMIB.snmpFrameworkMIBConformance.snmpFrameworkMIBCompliances.snmpFrameworkMIBCompliance
system.sysORTable.sysOREntry.sysORID.2 = OID: .iso.org.dod.internet.snmpV2.snmpModules.snmpMPDMIB.snmpMPDMIBConformance.snmpMPDMIBCompliances.snmpMPDCompliance
````
5)test snmpsimp with a data from centreon-plugins-datas (tips : the path of the snmprec/snmpwalk file is the same of the SNMP community) :
````
# snmpwalk -Obentu -v2c -c "centreon-plugins-datas/src/hardware/ups/standard/rfc1628/snmp/input-lines" 10.25.15.161
33.1.3.3.1.2.1 = 499
33.1.3.3.1.2.2 = 499
33.1.3.3.1.2.3 = 499
33.1.3.3.1.3.1 = 233
33.1.3.3.1.3.2 = 234
33.1.3.3.1.3.3 = 234
33.1.3.3.1.4.1 = 0
33.1.3.3.1.4.2 = 0
33.1.3.3.1.4.3 = 0
33.1.3.3.1.5.1 = 0
33.1.3.3.1.5.2 = 0
33.1.3.3.1.5.3 = 0
33.1.3.3.1.5.3 = No more variables left in this MIB View (It is past the end of the MIB tree)
````
6)test snmpsim with a data from centreon-plugins-datas from centreon plugin :
````
# sudo -u centreon-engine  /usr/lib/centreon/plugins/centreon_ups_standard_rfc1628_snmp.pl --plugin=hardware::ups::standard::rfc1628::snmp::plugin --mode=input-lines --hostname=10.25.15.161 --snmp-version='2' --snmp-community='centreon-plugins-datas/src/hardware/ups/standard/rfc1628/snmp/input-lines' --warning-power='215:' --critical-current='@0:214' --warning-voltage='@0:214' --warning-frequence='@0:214'
WARNING: Input Line '1' Frequence : 49.90 Hz - Input Line '2' Frequence : 49.90 Hz - Input Line '3' Frequence : 49.90 Hz | '1#line.input.frequence.hertz'=49.9Hz;@0:214;;; '1#line.input.voltage.volt'=233V;@0:214;;; '2#line.input.frequence.hertz'=49.9Hz;@0:214;;; '2#line.input.voltage.volt'=234V;@0:214;;; '3#line.input.frequence.hertz'=49.9Hz;@0:214;;; '3#line.input.voltage.volt'=234V;@0:214;;;
````
