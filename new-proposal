### Multi-Tenant PostgreSQL-as-a-service in SAP Multi-Cloud PLatform

SAP MultiCloud Platform is an open platform-as-a-service (PaaS) product that provides core platform and backing services, for building and extending cloud applications on multiple cloud infrastructure providers.

    One of the core services provided by SCP is PostgreSQL as a Service (PostgreSQL-as-a-Service). Each PostgreSQL-as-a-Service instance consists of 5 VMs - Postgres-Master, Postgres-Standby and 3-PGPOOL VMs. Data is replicated asynchronously from Postgres-Master to Postgres-Standby.

SCP manages more than 10000 PostgreSQL-as-a-Service instances across multiple IAASs. 

Multi-tenancy in Postgresql-as-a-service can be achieved through separate schema or database. Databases has various advantages over schemas with regard to multi-tenancy like better security and isolation, extension enablement, connnection control etc. SCP creates different databases(tenant-database) that run inside postgreSQL cluster.

### Tenant Admission Control
 
Available disk is a key factor for successful tenant creation. To prevent process from crashing, 90% disk full is used as a limiting factor, beyond which writes will be blocked. Requirement for successful tenant creation is to have some disk free e.g. 5 GB in addition to 10% of free disk to avoid running out of disk immediately after creation.

### Disaster-Recovery/Data-Protection-using-Backup-and-Restore

#### Tenant-Based-Backup-And-Restore :

Multitenant-Postgresql-as-a-Service supports tenant based scheduled backup and restore. Tenant backups (db dump) are archived on cloud storage. On AWS/GCP/AZURE snapshots are used to archive the backups. In case of Openstack, backup is taken by copying  and compressing data directory.

#### Tenant based PITR : 
Multitenant-Postgresql-as-a-Service also supports Point-In-Time-Recovery (PITR) using WAL archiving. A base backup along with WAL files are archived on cloud storage. Tenant data up to past 15 minutes can be restored.


### Tenant-Based-Monitoring

Multitenant-Postgresql-as-a-Service supports tenant based health monitoring. Monitoring agent running on instance reports health of every tenant. It reports metrics like bulk reads, bulk writes, connections used etc [check with dynatrace image]. It also reports various system specific metrics like CPU, Memory, Disk usage etc.

The monitoring agent collects this information and reports it to centralized monitoring server, which stores in a time-series-database. A monitoring-web-application shows metrics via various charts so that devops can identify the tenant health at any given time-date range.


### Tenant-Based-Logging

Multitenant-Postgresql-as-a-Service supports tenant based logging for troubleshooting. Tenant database logs are pushed to a centralized system, enabling dev ops to debug any issue irrespective of instance availability.

### Alerting


  
   
