---
layout: single
title: Windows cheatsheet
date: 2024-08-15
classes: wide
header:
  teaser: 
  teaser_home_page: true
  icon: 
categories:
  - windows
tags: 
  - cheatsheet
  
---


![image!](/images/win-sad.jpg)

## VSS Writers
```
vssadmin list writers

Note:
Most of the time VSS errors will be solved restarting affected service

```

## Cluster validation

```
Get-Cluster
-----------

get-clusternetwork
get-clustergroup
get-clusterresource
get-clusternode

Note: for Win 2008 the ps module is not present. Run:
import-module failoverclusters


GENERATE CLUSTER LOGS
----------------------

In CMD run:
cluster log /g
Generate logs in \windows\cluster\reports in each node.


In PoweShell run:
get-clusterlog 

```

## SSH connection

```
ssh -l <username> <SERVER/Domain>

eg: ssh -l B0322 TEST.ASD.NET

```

## Powershell Remote Login

```
Enter-PSSession -computername <SERVER> -credential <User@Domain>

```

## Management
```
Local users and groups - management: 
lusrmgr.msc

Computer Management:
compmgmt.msc

Editor de políticas de grupo (gpedit):
gpedit.msc

RUN Active Directory Users and Computers (ADUC):
DSA.MSC

Event Viewer:
eventvwr.msc
```


## Users
```
List connected/disconnected users:
qwinsta


```


## System corruption / repair / remove / install

```
SFC
----
sfc /scannow
It will scan all protected system files and replace corrupted files with a cached copy.


DISM
----

DISM /Online /Cleanup-Image /CheckHealth

DISM /Online /Cleanup-Image /ScanHealth

Online /Cleanup-Image /RestoreHealth



DISM MOUNTING WINDOWS ISO
-------------------------

DISM /Online /Cleanup-Image /RestoreHealth /Source:E:\Sources\install.wim

Steps:
https://www.windowscentral.com/how-use-dism-command-line-utility-repair-windows-10-image 

```


## Windows Recovery Environment - WinRE
```
WinRE starts automatically on next boot:
Reagentc /bottore

```


## Check Ports

```
Test-NetConnection -ComputerName <IP> -Port <Port>

Check open TCP/IP ports:
netstat -an

Lists all the executables (applications) associated with each connection:
netstat -b

shows active TCP connections and shows PID (Process ID)
netstat -o


netstat -ano|find "<port>"

netstat -ano

netstat -an|find /i "listening"
```

## Proxy


### Show proxy information
```
Check current status:
netsh winhttp show proxy
```

### Reset proxy (proxy removal)
```
netsh winhttp reset proxy

If the command work, when run show proxy should be empty:
netsh winhttp show proxy

Note: 
No reboot needed to apply changes

```

## Flush DNS

```
ipconfig /flushdns
```

## Change timezone

```
tzutil /s "<TimeZone>"
tzutil /s "W.Europe Standard Time"

Show timezone:
tzutil /g
output:  W.Europe Standard Time
```


## System Info
```
systeminfo


Get-ComputerInfo  (PowerShell)

ComputerInfo -Property "bios*"

change PROPERTIES-NAME for the details you want to review.
This example shows everything regarding the BIOS information: 
Get-ComputerInfo -Property "bios*"

Quick note: 
The asterisk (*) in the command is a wildcard to match every property that starts with BIOS.
```
### Uptime
```
Show uptime:
systeminfo | find "System Boot Time"
or
get-timezone
or
get-ciminstance -classname win32_operatingsystem | select lastbootuptime

```

### Pending Reboot

```
Get-PendingReboot
E.g:

    Computer           : WKS01
    CBServicing        : False
    WindowsUpdate      : True
    CCMClient          : False
    PendComputerRename : False
    PendFileRename     : False
    PendFileRenVal     : 
    RebootPending      : True
```

### Last boot time / uptime
```
wmic os get lastBootUpTime

systeminfo | find "System Boot Time"
systeminfo | findstr "System Uptime" (Nico)


only work on Windows workstations (Win 10/11):
net statistics workstation


Powershell:
-----------
(get-date) - (gcim Win32_OperatingSystem).LastBootUpTime

Get-CimInstance -Class Win32_OperatingSystem | Select-Object LastBootUpTime


Using Powershell 6.0 onwards:
get-uptime
```


## Hardware

```

```



