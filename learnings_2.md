<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# Comprehensive Guide to AWS Application Discovery Service and Migration Planning

---

AWS Application Discovery Service is a critical tool for organizations migrating from on-premises infrastructure to AWS. It provides visibility into server configurations, dependencies, and resource utilization, enabling informed migration decisions. Below is a structured analysis of the use cases, discovery methods, and integration with AWS Migration Hub.

---

## **Key Use Cases for Migration Planning**

### 1. **Greenfield Deployment (Starting Fresh)**

**Scenario**:

- No existing on-premises infrastructure.
- Directly build cloud-native applications in AWS.

**Considerations**:

- No need for migration tools like Application Discovery Service.
- Focus on cloud-native services (e.g., S3, Lambda, RDS).


### 2. **Brownfield Migration (On-Premises to Cloud)**

**Scenario**:

- Migrate existing servers/data centers to AWS.
- Requires assessment of legacy systems.

**Challenges**:

- Understanding server dependencies and resource usage.
- Prioritizing workloads for migration.

**Solution**:

- **AWS Application Discovery Service** automates inventory collection and dependency mapping.

---

## **AWS Application Discovery Service: Core Capabilities**

### 1. **Server Inventory Collection**

- **Configuration Data**: OS version, CPU cores, memory, disk size.
- **Utilization Metrics**: CPU, memory, disk I/O, network throughput (historical trends).


### 2. **Dependency Mapping**

- Identifies network connections between servers (e.g., port 3306 for MySQL).
- Groups interdependent servers into **applications** for phased migration.


### 3. **Cost Estimation**

- Maps on-premises resources to equivalent EC2 instances (e.g., m5.large for 8 vCPUs + 32 GB RAM).

---

## **Discovery Methods**

### 1. **Agentless Discovery**

**Use Case**: VMware environments requiring quick assessment without agent installation.

**Data Collected**:

- **VM Configuration**: Hostnames, IPs, MAC addresses.
- **Performance History**: Average/peak CPU, memory, disk usage.
- **Storage**: Disk allocations and utilization.

**Implementation**:

1. Deploy the **Agentless Collector OVA** in VMware vCenter.
2. Collector scans VMs via vCenter APIs (no guest OS access).

**Limitations**:

- Cannot collect process-level data or network dependencies.

**Example Workflow**:

```python  
# Deploy OVA via vCenter CLI  
ovftool --diskMode=thin --name=DiscoveryCollector \  
        --net:bridged --datastore=datastore1 \  
        agentless-collector.ova vi://vcenter-admin@vcenter-host  
```

---

### 2. **Agent-Based Discovery**

**Use Case**: Detailed assessment of physical/virtual servers (Windows/Linux).

**Data Collected**:

- **System Configuration**: Installed software, registry settings.
- **Processes**: Running services (e.g., Apache, SQL Server).
- **Network Dependencies**: Inbound/outbound connections (IP + port).

**Implementation**:

1. Install **AWS Discovery Agent** on each server.
2. Agents send encrypted data to AWS Migration Hub.

**Example Agent Installation**:

```bash  
# For Ubuntu  
wget https://aws-discovery-agent.s3.amazonaws.com/discovery-agent.deb  
sudo dpkg -i discovery-agent.deb  
sudo service discovery-agent start  
```

---

## **Integration with AWS Migration Hub**

### 1. **Centralized Tracking**

- **Dashboard**: View discovered servers and group them into applications.
- **Migration Status**: Track progress (e.g., "10/20 servers migrated").


### 2. **Export and Analysis**

- **Amazon Athena**: Query discovery data (e.g., `SELECT * FROM discovered_servers WHERE cpu_usage > 70%`).
- **Amazon QuickSight**: Visualize resource utilization trends.


### 3. **Migration Recommendations**

- **EC2 Sizing**: Recommends instance types based on historical metrics.
- **Cost Projections**: Estimates monthly AWS costs.

---

## **Choosing Between Agentless and Agent-Based**

| **Criteria** | **Agentless** | **Agent-Based** |
| :-- | :-- | :-- |
| **Deployment Time** | Minutes (per vCenter) | Hours (per server) |
| **Data Granularity** | VM-level metrics | Process/network-level details |
| **Supported Environments** | VMware only | VMware, Hyper-V, physical servers |
| **Dependency Mapping** | Limited (network IPs only) | Full (processes + ports) |
| **Use Case** | Quick inventory for VMware migrations | Detailed assessment for complex apps |

---

## **Migration Planning Workflow**

1. **Discover**: Use Agentless/Agent-Based to inventory servers.
2. **Analyze**: Group servers into apps using dependency maps.
3. **Plan**: Right-size EC2 instances and prioritize migrations.
4. **Execute**: Use AWS Application Migration Service (MGN) or Database Migration Service (DMS).

---

## **Example: Migrating a Three-Tier App**

1. **Web Tier** (Apache on Ubuntu):
    - Agentless discovery → m5.xlarge (4 vCPUs, 16 GB RAM).
2. **App Tier** (Java on CentOS):
    - Agent-based discovery → c5.2xlarge (8 vCPUs, 16 GB RAM).
3. **DB Tier** (MySQL on RHEL):
    - DMS for CDC → RDS MySQL.

---

## **Key Takeaways**

- **Agentless**: Fast, low-effort VMware assessments.
- **Agent-Based**: Detailed insights for complex dependencies.
- **Migration Hub**: Centralizes tracking and cost analysis.

By combining these tools, enterprises can reduce migration risks, avoid over-provisioning, and ensure minimal downtime.

<div style="text-align: center">⁂</div>

[^1]: https://docs.aws.amazon.com/application-discovery/latest/userguide/what-is-appdiscovery.html

[^2]: https://docs.aws.amazon.com/application-discovery/latest/userguide/console-walkthrough.html

[^3]: https://aws.amazon.com/about-aws/whats-new/2024/11/aws-application-discovery-service-integration-application-migration-service

[^4]: https://aws.amazon.com/blogs/aws/aws-application-discovery-service-update-agentless-discovery-for-vmware/

[^5]: https://faddom.com/aws-application-discovery-service-the-basics-and-how-to-get-started/

[^6]: https://www.servicenow.com/community/developer-forum/difference-between-agent-vs-agentless-discovery/m-p/1940890

[^7]: https://docs.aws.amazon.com/application-discovery/latest/userguide/agentless-collector-gs-prerequisites.html

[^8]: https://www.techtarget.com/searchaws/definition/AWS-Migration-Hub

[^9]: https://aws.amazon.com/application-discovery/features/

[^10]: https://bairavcloud.com/aws-application-discovery-service-agentless-vs-agent-based/

[^11]: https://aws.amazon.com/application-discovery/

[^12]: https://tutorialsdojo.com/aws-application-discovery-service/

[^13]: https://docs.aws.amazon.com/application-discovery/latest/APIReference/Welcome.html

[^14]: https://docs.aws.amazon.com/application-discovery/latest/userguide/discovery-import.html

[^15]: https://aws.amazon.com/solutions/new-to-aws/migration-planning/

[^16]: https://docs.aws.amazon.com/application-discovery/latest/userguide/agentless-collector.html

[^17]: https://docs.aws.amazon.com/migrationhub/latest/ug/gs-new-user-discovery.html

[^18]: https://aws.amazon.com/solutions/migration/infrastructure-application-discovery/

[^19]: https://library.wiremock.org/catalog/api/a/amazonaws.com/amazonaws-com-discovery/

[^20]: https://pages.awscloud.com/How-AWS-Migration-Hub-Helps-You-Plan-Track-and-Complete-Your-Application-Migrations_2019_0622-MIG_OD.html?amp%3Btrk=ep_card-el_a131l000005jhbsqai

[^21]: https://docs.aws.amazon.com/application-discovery/latest/userguide/discovery-agent.html

[^22]: https://k21academy.com/amazon-web-services/aws-solutions-architect/aws-application-discovery-service/

[^23]: https://docs.aws.amazon.com/migrationhub/latest/ug/whatishub.html

[^24]: https://virima.com/blog/agent-based-vs-agentless-discovery-which-is-best-for-your-business

[^25]: https://aws.amazon.com/migration-hub/faqs/

[^26]: https://docs.motadata.com/itsm/user-guide/build/html/asset-management/asset-discovery/agent-based-vs-agentless-method.html

[^27]: https://docs.aws.amazon.com/application-discovery/latest/userguide/install.html

[^28]: https://aws.amazon.com/application-discovery/pricing/

[^29]: https://aws.amazon.com/application-discovery/faqs/

[^30]: https://docs.aws.amazon.com/application-discovery/latest/userguide/agentless-collector-gs-configure.html

[^31]: https://aws.amazon.com/prescriptive-guidance/migration-tools/migration-discovery-tools/

[^32]: https://6sense.com/tech/data-management/awsapplicationdiscoveryservice-vs-amazonkinesis

[^33]: https://docs.aws.amazon.com/application-discovery/latest/userguide/security.html

