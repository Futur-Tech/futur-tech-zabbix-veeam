# Futur-Tech Zabbix Veeam Agent

This repository provides a **Zabbix monitoring template** for **Veeam Agent for Microsoft Windows**, adapted and extended by Futur-Tech. 

It is based on the [Zabbix Community Template](https://github.com/zabbix/community-templates/tree/main/Applications/Backup/template_veeam_agents_for_microsoft_windows/6.0) authored by *Cédric MARCOUX*.

Work with Active Zabbix Agent 7.0

## Features

- **Event Log Monitoring**
  - Detects backup success, warnings, and errors from the Windows Event Log.
  - Captures synchronization events and backup job outcomes.

- **Service State Monitoring**
  - Monitors the status of the `VeeamEndpointBackupSvc` Windows service.
  - Alerts when the service is down.

- **Backup Job Discovery**
  - Discovers backup jobs automatically (currently limited; free Veeam Agent supports only one job).
  - Ready for multi-job monitoring.

- **Backup Age Calculation**
  - Tracks the age of the latest successful backup.
  - Supports global thresholds and per-job overrides.

- **Custom Triggers**
  - Backup success, warning, error, retry states.
  - Disk space–related alerts.
  - Service availability.
  - Flexible recovery conditions.


### Items

- **Event Log** → `eventlog[Veeam Agent,,,Veeam Agent,,,skip]`
- **Service State** → `service.info[VeeamEndpointBackupSvc]`
- **Backup Age** (calculated from event logs)
- **Backup Success Timestamp** (dependent item)

### Triggers

- `Veeam Agent synchronization completed with error` (Average)  
- `Veeam Agent synchronization completed with warning` (Warning)  
- `Veeam Agent synchronization successful` (Info)  
- `Veeam Agent service went down` (High)  
- Job-specific triggers:
  - Backup started (Info)  
  - Backup success (Info)  
  - Backup warning (Warning)  
  - Backup error (Average)  
  - Backup retried (Average)  
  - Backup success with low disk space (Warning)  
  - Backup overdue (Warning / High depending on age)  

### Macros

| Macro | Default | Description |
|-------|---------|-------------|
| `{$VEEAM.BACKUP.AGE.CRIT}` | `7d` | Critical max age for latest successful backup |
| `{$VEEAM.BACKUP.AGE.WARN}` | `3d` | Warning max age for latest successful backup |
| `{$VEEAM.BACKUP.IGNORE}` | `0` | Disable backup age triggers globally |
| `{$VEEAM.BACKUP.AGE.CRIT:"Job Name"}` | (example: `3d`) | Per-job critical override |
| `{$VEEAM.BACKUP.AGE.WARN:"Job Name"}` | (example: `1d`) | Per-job warning override |
| `{$VEEAM.BACKUP.IGNORE:"Job Name"}` | `1` | Ignore trigger for specific job |


### Usage

1. Import the YAML template into Zabbix 7.0+.  
2. Assign the template to Windows hosts running **Veeam Agent for Microsoft Windows**.  
3. Configure macros if you need **per-job backup age policies** or want to **ignore jobs**.  
