---
title: "SDN Controller Outage Due to Database Connection Limit"
date: 2025-08-01T08:00:00Z
lastmod: 2025-08-01T09:15:00Z
status: "resolved"
severity: "major"
impact: ["SDN", "DHCP", "Database", "Network Operations"]
duration: "1h 0m"
affected_services: ["SDN Controller", "DHCP Services", "Network Automation"]
incident_id: "INC-2025-003"
tags: ["sdn", "mariadb", "database", "dhcp", "network-operations", "configuration"]
summary: "SDN controller outage caused by MariaDB max_connections limit preventing network operations execution"
draft: false
---

## Summary

On August 1, 2025, AS214503 experienced a Software-Defined Networking (SDN) controller outage caused by a MariaDB `max_connections` setting that was inadvertently reset during a database upgrade. The connection limit reverted from the previously configured 16000 to the default 1000, preventing the SDN controller from executing critical network operations, resulting in extended DHCP lease times and degraded network automation capabilities. No packet forwarding issues were observed as the data plane remained operational.

## Impact

- **Primary Cause**: MariaDB `max_connections` limit exceeded
- **Affected Systems**: SDN controller, network automation, DHCP services
- **DHCP Impact**: Extended lease renewal times, some renewals delayed
- **Network Operations**: Automated configuration changes blocked
- **Data Plane**: No packet forwarding disruption - traffic continued normally
- **Customer Impact**: Minimal - existing connections maintained, new DHCP leases delayed

## Timeline (All times UTC)

**08:00** - SDN controller alerts triggered for failed database connections  
**08:05** - Network operations team investigates controller unresponsiveness  
**08:10** - DHCP lease renewal delays reported by monitoring systems  
**08:15** - Database connection pool exhaustion identified in MariaDB logs  
**08:20** - MariaDB `max_connections` setting found reset to default value of 1000 (was 16000)  
**08:25** - Database upgrade logs reviewed, configuration reset identified  
**08:30** - `max_connections` restored to 16000, SDN controller service restarted  
**08:40** - DHCP lease processing returns to normal operation  
**08:50** - Configuration management updated to prevent future resets during upgrades  
**09:00** - Full SDN controller functionality restored and incident resolved  

## Root Cause

**Primary Cause**: MariaDB database upgrade that reset the `max_connections` setting from the previously configured 16000 back to the default value of 1000.

**Contributing Factors**:
- Database upgrade procedure did not preserve custom configuration settings
- Lack of post-upgrade configuration validation
- Missing configuration management for database settings
- No automated monitoring to detect configuration drift after upgrades

**Technical Details**:
The SDN controller maintains persistent database connections for network state management, configuration changes, and DHCP lease tracking. During normal operation, the controller requires approximately 200-300 concurrent database connections. A recent MariaDB upgrade inadvertently reset the `max_connections` setting from the production-tuned value of 16000 back to the default 1000, causing connection pool exhaustion and preventing new network operations from being executed.

## Resolution

**Immediate Actions**:
- Identified MariaDB upgrade as the cause of configuration reset
- Restored MariaDB `max_connections` from 1000 back to 16000
- Restarted SDN controller to clear connection pool
- Monitored DHCP lease processing recovery
- Verified packet forwarding remained unaffected

**Permanent Fixes**:
- Implemented configuration management for MariaDB settings
- Added post-upgrade validation procedures
- Created automated configuration drift detection
- Updated database upgrade procedures to preserve custom settings

**Configuration Changes**:
```sql
-- Restored production configuration in /etc/mysql/mariadb.conf.d/50-server.cnf
max_connections = 16000

-- Added to configuration management system to prevent future resets
```

## Prevention Measures

- **Database Monitoring**: Implemented comprehensive MariaDB connection tracking
- **Alerting**: Added alerts for connection pool utilization above 70%
- **Connection Management**: Optimized SDN controller connection pooling
- **Capacity Planning**: Established database connection capacity planning process
- **Documentation**: Updated deployment procedures to include database tuning
- **Testing**: Added load testing for database connection limits in staging environment

## Customer Communication

- **08:15 UTC**: Initial notification about DHCP service delays
- **08:35 UTC**: Update on root cause identification and temporary fix
- **09:05 UTC**: All-clear notification confirming full service restoration
- **09:15 UTC**: Post-incident summary with prevention measures

## Lessons Learned

1. Default database configurations are rarely suitable for production workloads
2. SDN controller database requirements must be properly sized during deployment
3. Connection pooling configuration requires regular review and optimization
4. Database connection monitoring is critical for SDN infrastructure
5. DHCP lease management impacts are often the first visible symptom of SDN issues
6. Data plane and control plane separation provided resilience during the outage

## Technical Notes

**Data Plane Resilience**: The separation between control plane (SDN controller) and data plane (packet forwarding) ensured that existing network traffic continued without interruption. Only new network configurations and DHCP lease renewals were affected.

**DHCP Lease Extension**: Existing DHCP leases continued to function normally. Only lease renewals and new assignments experienced delays due to the SDN controller's inability to update lease databases.

---

*Incident resolved. SDN controller operating normally with optimized database configuration and enhanced monitoring.*
