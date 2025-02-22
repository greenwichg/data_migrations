<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# Comprehensive Guide to AWS Application Migration Service (MGN) for Lift-and-Shift Migrations

---

AWS Application Migration Service (MGN) simplifies the migration of on-premises servers, virtual machines (VMs), or workloads from other clouds to AWS with minimal downtime. Formerly known as CloudEndure Migration, MGN automates the **lift-and-shift (rehosting)** process by continuously replicating data and enabling seamless cutover to AWS. Below is a detailed breakdown of how MGN works, its architecture, and key benefits.

---

## **Core Components of AWS MGN**

### 1. **Replication Agent**

- **Role**: Installed on source servers (physical, virtual, or cloud-based) to perform **block-level replication** of disks.
- **Functionality**:
    - Captures disk changes in real time.
    - Compresses and encrypts data (TLS 1.3 in transit, AES-256 at rest).
    - Sends data to a **staging area** in AWS (low-cost EC2 instances and EBS volumes).
- **Supported Platforms**:
    - **OS**: Windows Server (2012+), Linux (RHEL, CentOS, Ubuntu, etc.).
    - **Hypervisors**: VMware, Hyper-V, and cloud VMs (AWS, Azure, GCP).


### 2. **Staging Area**

- **Purpose**: Temporarily stores replicated data in AWS.
- **Components**:
    - **Replication Servers**: Lightweight EC2 instances managed by MGN.
    - **EBS Volumes**: Store replicated disk data (default: gp2/gp3 volumes).
- **Cost Optimization**: Uses minimal resources until cutover.


### 3. **Cutover Instance**

- **Role**: Production-ready EC2 instance launched from the replicated data during migration.
- **Customization**:
    - Resize instances (e.g., from t3.small to m5.large).
    - Modify EBS volume types (e.g., gp3 to io2 for high IOPS).

---

## **Migration Workflow**

### Step 1: Discovery and Planning

- **AWS Application Discovery Service**:
    - Maps server dependencies and resource utilization (CPU, memory, disk).
    - Identifies interdependencies (e.g., web servers connecting to a database).


### Step 2: Agent Installation and Initial Sync

1. **Install Replication Agent**:
    - Download agent from AWS MGN console.
    - Install on source servers (requires root/admin access).
2. **Initial Sync**:
    - Full copy of source disks to the staging area.
    - Subsequent changes are captured incrementally.

### Step 3: Continuous Replication

- **Block-Level Replication**:
    - Captures disk writes (e.g., database transactions, file updates).
    - Syncs changes to AWS every few seconds.
- **Staging Area Management**:
    - MGN auto-scales replication servers based on workload.


### Step 4: Testing (Optional)

- **Launch Test Instances**:
    - Spin up EC2 instances from replicated data.
    - Validate functionality without impacting the source.
- **Cleanup**: Terminate test instances post-validation.


### Step 5: Cutover to Production

1. **Initiate Cutover**:
    - Stop writes on the source (optional for minimal downtime).
    - Launch cutover instances in AWS.
2. **Final Sync**:
    - Replicate last changes (seconds to minutes).
3. **Redirect Traffic**:
    - Update DNS/Routing Tables to point to AWS.

### Step 6: Finalize Migration

- **Terminate Replication**: Stop data sync and clean up staging resources.
- **Decommission Source**: Shut down on-premises servers.

---

## **Key Benefits**

### 1. **Minimal Downtime**

- **Near-Zero RTO/RPO**: Final sync typically takes seconds.
- **Example**: A SQL Server with 1 TB of data can cutover in <5 minutes after initial sync.


### 2. **Cost Efficiency**

- **Staging Area**: Uses low-cost EC2 instances (e.g., t3.medium).
- **No Licensing Fees**: Pay only for AWS resources consumed.


### 3. **Automation**

- **Auto-Scaling**: Replication servers adjust based on workload.
- **Cross-Account Support**: Migrate to different AWS accounts.


### 4. **Broad Compatibility**

- **Supported Workloads**:
    - Databases (Oracle, MySQL, SQL Server).
    - Legacy Apps (Java, .NET).
    - File Servers (NFS, SMB).

---

## **Comparison with AWS Server Migration Service (SMS)**

| **Feature** | **AWS MGN** | **AWS SMS** (Legacy) |
| :-- | :-- | :-- |
| **Replication Type** | Continuous block-level | Periodic snapshots |
| **Downtime** | Seconds/Minutes | Hours/Days |
| **OS Support** | Windows, Linux | Limited Linux support |
| **Cutover Flexibility** | Customizable instance types | Fixed instance types |
| **Cost** | Pay-as-you-go | Requires SMS Connector licensing |

---

## **Example Use Case: Migrating a Web Server**

1. **Source**: On-premises VMware VM (4 vCPUs, 16 GB RAM, 500 GB disk).
2. **MGN Setup**:
    - Install agent → Initial sync completes in 8 hours (over 100 Mbps link).
    - Continuous replication captures daily changes.
3. **Cutover**:
    - Launch m5.xlarge (4 vCPUs, 16 GB RAM) + 500 GB gp3 volume.
    - Final sync: 2 minutes.
4. **Post-Migration**:
    - Enable Auto Scaling for traffic spikes.

---

## **Challenges and Mitigations**

| **Challenge** | **Solution** |
| :-- | :-- |
| **High Data Churn** | Use larger replication servers or throttle source writes during initial sync. |
| **Network Bandwidth** | Compress data or use AWS Direct Connect. |
| **Legacy OS Support** | Test compatibility (e.g., Windows 2008 R2 requires manual driver updates). |

---

## **Conclusion**

AWS MGN streamlines lift-and-shift migrations by automating replication, minimizing downtime, and reducing dependency on specialized engineers. By leveraging continuous block-level replication and flexible cutover options, organizations can migrate complex workloads (databases, apps, file servers) to AWS with confidence. For enterprises managing large-scale migrations, MGN integrates with the **AWS Migration Hub** to track progress and dependencies, ensuring alignment with broader cloud adoption strategies.

<div style="text-align: center">⁂</div>

[^1]: https://docs.aws.amazon.com/mgn/latest/ug/launch-cutover-gs.html

[^2]: https://www.reddit.com/r/aws/comments/197oqjm/cutover_when_migrating_using_aws_mgn/

[^3]: https://docs.aws.amazon.com/mgn/latest/ug/common-replication-errors.html

[^4]: https://www.youtube.com/watch?v=1rtsb9jzKiI

[^5]: https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/mgn/client/pause_replication.html

[^6]: https://docs.aws.amazon.com/mgn/latest/ug/Replication-Related-FAQ.html

[^7]: https://aws.amazon.com/blogs/mt/how-to-skip-testing-and-finalize-cutover-in-the-aws-application-migration-service/

[^8]: https://docs.aws.amazon.com/mgn/latest/ug/migration-dashboard.html

[^9]: https://docs.aws.amazon.com/mgn/latest/ug/server-replication-main.html

[^10]: https://docs.aws.amazon.com/pdfs/mgn/latest/ug/user-guide.pdf

[^11]: https://docs.aws.amazon.com/mgn/latest/ug/replication-server-settings.html

[^12]: https://docs.aws.amazon.com/prescriptive-guidance/latest/migration-database-rehost-tools/mgn.html

[^13]: https://hidekazu-konishi.com/entry/aws_mgn_architecture_lifecycle_usage_notes.html

[^14]: https://aws.amazon.com/application-migration-service/faqs/

[^15]: https://faddom.com/aws-application-migration-service-process-pricing-and-best-practices/

[^16]: https://repost.aws/knowledge-center/mgn-windows-fix-replication-lag

[^17]: https://docs.aws.amazon.com/mgn/latest/ug/installing-vcenter-overview-mgn.html

[^18]: https://docs.aws.amazon.com/mgn/latest/ug/General-Questions-FAQ.html

[^19]: https://docs.aws.amazon.com/mgn/latest/ug/server-test-cutover-main.html

[^20]: https://blog.ippon.tech/exploring-the-lift-and-shift-with-aws-application-migration-service

[^21]: https://aws.amazon.com/blogs/mt/validate-your-migration-cutovers-using-predefined-post-launch-actions/

[^22]: https://docs.aws.amazon.com/mgn/latest/ug/migration_at_scale.html

[^23]: https://aws.amazon.com/blogs/mt/aws-application-migration-service-best-practices/

