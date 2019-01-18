### Multi-Tenant-PostgreSQL-as-a-Service in SAP Cloud PLatform

SAP Cloud Platform is an open platform-as-a-service (PaaS) product that provides core platform and backing services, for building and extending cloud applications on multiple cloud infrastructure providers.

    One of the core services provided by SCP is Multi-Tenant PostgreSQL as a Service (MT-PostgreSQL-as-a-Service). MT-PostgreSQL-as-a-Service is the virtualized view of a PostgreSQL service for a given tenant in a multi-tenant system. One or more of such MT-PostgreSQL-as-a-Service instances are multiplexed into a dedicated PostgreSQL-as-a-Service. Each dedicated PostgreSQL-as-a-Service instance consists of 5 VMs - Postgres-Master, Postgres-Standby and 3-PGPOOL VMs. Data is replicated asynchronously from Postgres-Master to Postgres-Standby.

SCP manages more than 2400 MT-PostgreSQL-as-a-Service instances across multiple IAASs. 

MT-PostgreSQL-as-a-Service is materialized in SCP by defining database as the Multi-Tenant-Unit(MTU) whereby each tenant is encapsulated into a database. Logical dbs in PostgreSQL provides clear advantages like isolation, extension enablement, connection control, higher security which makes it the ideal choice as MTU.

### Multi-Tenant-Postgresql-as-a-Service - Cluster Setup
![N|Solid](https://github.com/ankita0811/PostgresqlConf/blob/master/clustersetup.png?raw=true)

### Security and Isolation
A separate membership role is created for each tenant database. Cloud applications are provided with separate username and password to access tenant databases. Tenant users are only entitled to connect to their own database. Privileges for database and role creation are not provided to tenant users.
 
An applications connected to a tenant database cannot perform any operation on objects of another tenant database co-located on the same postgresql instance.  


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

![N|Solid](https://github.com/ankita0811/PostgresqlConf/blob/master/database_scans_rows.png?raw=true)


![N|Solid](https://github.com/ankita0811/PostgresqlConf/blob/master/database_buffers.png?raw=true)

Instance Health Metrics

![N|Solid](https://github.com/ankita0811/PostgresqlConf/blob/master/instancehealth.png?raw=true)
### Tenant-Based-Logging

Multitenant-Postgresql-as-a-Service supports tenant based logging for audit and troubleshooting purposes. Tenant database logs are pushed to a centralized system, enabling dev ops to debug any issue irrespective of instance availability.

### Alerting


  
   
