# Zabbix Template: Processes Monitor

## Description

This template allows you to monitor various processes and services running on a system using Zabbix agent. 
Define a list of processes to be monitored, and the LLD will create the items regardless if the processes are currently running or not. Optionally set critical processes, memory, and cpu thresholds using macros.
Change item type that collects all the raw data, from Zabbix active to passive based on your needs.

## Overview

This is a Zabbix template designed to monitor processes and services running on a system using the Zabbix agent (version 6.2 and later).

Tested on Debian 8, 9, and 10, AlmaLinux 8. Should work for Windows.

Please report issues or contribute on GitHub: https://github.com/zingaya/zbx_processes_monitor

## Author

Leonardo Savoini

## Macros used

|Name|Description|Default|Type|
|----|-----------|-------|----|
|{$PROCESSES.CMD}|Trigger will try to find this defined string in the whole command executed. Use this to default all triggers to this value, or use macro context. Leave empty for no alerts.|``|Text macro|
|{$PROCESSES.CRIT}|Which ones of the processes will have a critical trigger. Use regular expression|`CHANGE_THIS`|Text macro|
|{$PROCESSES.LIST}|A coma delimited list of all process to be monitored. Exact name of the process name. Example: systemd,ndbmtd,process_1,process_2|`CHANGE_THIS`|Text macro|
|{$PROCESSES.MAXMEM}|Use this to default all triggers to this value, or use macro context. Leave empty or zero for no alerts|`0`|Integer macro|
|{$PROCESSES.MAXCPU.USER}|Use this to default all triggers to this value, or use macro context. Remember to consider CPU cores. Leave zero for no alerts|`0`|Integer macro|
|{$PROCESSES.MAXCPU.SYSTEM}|Use this to default all triggers to this value, or use macro context. Remember to consider CPU cores. Leave zero for no alerts|`0`|Integer macro|

## Template links

There are no template links in this template.

## Discovery rules

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|Create processes|Discovery of processes based on macros.|`SCRIPT`|get.processes|

## Items

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|Get raw process info|Collects raw information about specified processes.|`ZABBIX_ACTIVE`|proc.get|

## Triggers

There are no triggers in this template.

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|Process {#PROCESS.NAME} command line|Process command line.|`DEPENDENT`|proc[{#PROCESS.NAME},cmdline]|
|Process {#PROCESS.NAME} cpu time (user)|Process CPU time (user).|`DEPENDENT`|proc[{#PROCESS.NAME},cpuuser]|
|Process {#PROCESS.NAME} memory usage|Process memory usage.|`DEPENDENT`|proc[{#PROCESS.NAME},memory]|

## Items prototype

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|Get raw process info|Collects raw information about specified processes.|`ZABBIX_ACTIVE`|proc.get|
|Process {#PROCESS.NAME} command line|Process command line.|`DEPENDENT`|proc[{#PROCESS.NAME},cmdline]|
|Process {#PROCESS.NAME} cpu time (user)|Process CPU time (user).|`DEPENDENT`|proc[{#PROCESS.NAME},cpuuser]|
|Process {#PROCESS.NAME} memory usage|Process memory usage.|`DEPENDENT`|proc[{#PROCESS.NAME},memory]|

## Triggers prototype

|Name|Description|Expression|Priority|
|----|-----------|----------|--------|
|Process {#PROCESS.NAME} is not running|Triggered when a critical process is not running.|`max(/Processes monitor/proc[{#PROCESS.NAME},running],5m)=0`|AVERAGE|
|Process {#PROCESS.NAME} memory usage high|Triggered when process memory usage is too high.|`min(/Processes monitor/proc[{#PROCESS.NAME},memory],15m)>{#PROCESS.MAXMEM}`|WARNING|
 
## Overrides

|Override Name|Step|Conditions|Operations|
|-------------|----|----------|----------|
|Critical processes|1|Processes with names specified by `{$PROCESSES.CRIT}` macro.|- Set high severity trigger prototype for "is not running" message.<br> - Set critical-process tag for item prototype.<br> - Set average severity trigger prototype for "is not being executed with the expected command line" message.|
