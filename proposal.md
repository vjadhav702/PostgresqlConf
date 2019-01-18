### Multi-Tenant PostgreSQL-as-a-service in SAP Cloud PLatform

SAP MultiCloud Platform is an open platform-as-a-service (PaaS) product that provides core platform and backing services, for building and extending cloud applications on multiple cloud infrastructure providers.

    One of the core services provided by SCP is PostgreSQL as a Service (PostgreSQL-as-a-Service). Each PostgreSQL-as-a-Service instance consists of 5 VMs - Postgres-Master, Postgres-Standby and 3-PGPOOL VMs. Data is replicated asynchronously from Postgres-Master to Postgres-Standby.

SCP manages more than 10000 PostgreSQL-as-a-Service instances across multiple IAASs. 

Multi-tenancy in Postgresql-as-a-service can be achieved through separate schema or database. Databases has various advantages over schemas with regard to multi-tenancy like better security and isolation, extension enablement, connnection control etc. SCP creates different databases(tenant-database) that run inside postgreSQL cluster.

### 

One of the core services provided by SAP MultiCloud Platform is PostgreSQL-as-a-Service.

    Together on all infrastructures platform has approximately 8000 postgresql clusters.

Each postgresql cluster is Highly Available with two postgresql nodes. One node runs as a primary and other node as standby replicating primary in asynchronous-mode. Primary node failure is mitigated by promoting standby node to primary. This make sure that there is always a primary node running. To detect the the failure [pgpool] is used which continuously checks the heartbeat of postgresql process. [pgpool] node is running in three nodes in order to form consensus. Refer below figure for more understanding.

![N|Solid](https://github.com/dbossap/dbos-performance/blob/master/postgresql-Cluster.png?raw=true)
  
  Once the failover is done, [STONITH] is performed to avoid split brain problem. [Bosh] is responsible for "resurrecting" postgreSQL vms when it becomes unresponsive or dead and it is also responsible for applying required binaries to all postgresql VMs.

### No language dependency, Just a single endpoint

  Applications connecting to postgresql service should have mechanism to connect via single endpoint/single ip. Not all drivers unlike [jdbc] are intelligent enough to make distinction of modes of postgreSQL instances. Drivers expect the endpoint of the primary node at any point of time. Thus single endpoint is of high importance in a cloud environment. 
  
    SAP MultiCloud Platform supports single endpoint for service instance in multi availabilty zone architecture.
    
#### Due to diversity in the cloud architecture of cloud providers, different solutions are applied for respective cloud providers.


In case of [Azure] and [GCP], load balancer is associated with each service instance. The client ip is attached to load balancer as the frontend ip address. At any point in time, load balancer always forwards the traffic to primary node with help of heath check prob and agent running on postgresql VMs

- Cluster Setup

![N|Solid](https://github.com/dbossap/dbos-performance/blob/master/Azure-Implementation.png?raw=true)


In case of [AWS], load balancer approach does not work due limitations form aws side. To solve this problem [Route53] with MultiValue policy, pgpool node and [linux-iptables] is used. From secondary node all requests are forwarded to primary node using [linux-iptables] rules. In case of primary node failure SecondaryPrivateIP is floated to pgpool node in the same zone and [linux-iptables] rule is added from pgpool node to new primary node.

 - Cluster Setup

![N|Solid](https://github.com/dbossap/dbos-performance/blob/master/PMS1.png?raw=true)


In case of [OpenStack], allowed-address-pair feature is used. In this case client ip (single ip) is attached to both the postgrsql VMs. During failure of primary node, secondary node is promoted to priamry. Next [arping] is done from new primary node to inform other VMs in the network.

- Cluster Setup

![N|Solid](https://github.com/dbossap/dbos-performance/blob/master/openStack.png?raw=true)

[aws]: <https://aws.amazon.com>
[azure]: <https://azure.microsoft.com/en-us/>
[gcp]: <https://cloud.google.com/>
[openstack]: <https://www.openstack.org/>
[bosh]: <https://bosh.io/docs/>
[pgpool]: <http://www.pgpool.net/mediawiki/index.php/Main_Page>
[STONITH]: <https://en.wikipedia.org/wiki/STONITH>
[arping]: <https://en.wikipedia.org/wiki/Arping>
[jdbc]: <https://en.wikipedia.org/wiki/Java_Database_Connectivity>
[Route53]: <https://aws.amazon.com/route53>
[linux-iptables]: <https://en.wikipedia.org/wiki/Iptables>
