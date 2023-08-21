# Zabbix Template: Processes Monitor

## Description

This template allows you to monitor various processes and services running on a system using Zabbix agent. 
Define a list of processes to be monitored, and the LLD will create the items regardless if the processes are currently running or not. And optionally set critical processes, and memory thresholds using macros.
Change the single item, that gets all the raw data, Zabbix active to passive based on your needs.

## Overview

This is a Zabbix template designed to monitor processes and services running on a system using the Zabbix agent (version 6.2 and later).

Tested on Debian 8, 9, and 10, AlmaLinux 8. Should work for Windows.

Please report issues or contribute on GitHub: https://github.com/zingaya/zbx_processes_monitor

## Author

Leonardo Savoini

## Macros used

|Name|Description|Default|Type|
|----|-----------|-------|----|
|{$PROCESSES.CMD}|Optional per-process command parameters or paths.|`CHANGE_THIS`|Text macro|
|{$PROCESSES.CRIT}|Which processes will have a critical trigger.|`CHANGE_THIS`|Text macro|
|{$PROCESSES.LIST}|Comma-delimited list of processes to be monitored.|`CHANGE_THIS`|Text macro|
|{$PROCESSES.MAXMEM}|Optional per-process maximum memory usage.|`CHANGE_THIS`|Text macro|

## Template links

There are no template links in this template.

## Discovery rules

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|Create processes|Discovery of processes based on macros.|`SCRIPT`|get.processes|

## Items collected

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|Get raw process info|Collects raw information about specified processes.|`ZABBIX_ACTIVE`|proc.get|
|Process {#PROCESS.NAME} command line|Process command line.|`DEPENDENT`|proc[{#PROCESS.NAME},cmdline]|
|Process {#PROCESS.NAME} cpu time (user)|Process CPU time (user).|`DEPENDENT`|proc[{#PROCESS.NAME},cpuuser]|
|Process {#PROCESS.NAME} memory usage|Process memory usage.|`DEPENDENT`|proc[{#PROCESS.NAME},memory]|

## Triggers

|Name|Description|Expression|Priority|
|----|-----------|----------|--------|
|Process {#PROCESS.NAME} is not running|Triggered when a critical process is not running.|`max(/Processes monitor/proc[{#PROCESS.NAME},running],5m)=0`|AVERAGE|
|Process {#PROCESS.NAME} memory usage high|Triggered when process memory usage is too high.|`min(/Processes monitor/proc[{#PROCESS.NAME},memory],15m)>{#PROCESS.MAXMEM}`|WARNING|
 
## Overrides

|Override Name|Step|Conditions|Operations|
|-------------|----|----------|----------|
|Critical processes|1|Processes with names specified by `{$PROCESSES.CRIT}` macro.|- Set high severity trigger prototype for "is not running" message.<br> - Set critical-process tag for item prototype.<br> - Set average severity trigger prototype for "is not being executed with the expected command line" message.|
|Memory threshold|2|Processes with non-zero values in `{$PROCESSES.MAXMEM}` macro.|- Set discover flag for trigger prototype.|
