<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# Comprehensive Guide to AWS Data Migration and Transfer Services

---

AWS offers a suite of tools tailored for different migration scenarios, from database replication to petabyte-scale offline transfers. Below is a detailed breakdown of **AWS Application Discovery Service**, **AWS Database Migration Service (DMS)**, **AWS DataSync**, **AWS Snow Family**, and **AWS Transfer Family**, along with guidelines for selecting the right tool.

---

## **1. AWS Application Discovery Service**

**Purpose**:

- **Migration Planning**: Discovers on-premises servers, databases, and their dependencies to assess migration feasibility.
- **Performance Metrics**: Collects CPU, memory, disk usage, and network dependencies to right-size AWS resources.
- **Integration**: Works with AWS Migration Hub to track migration progress.

**Use Cases**:

- Identifying server dependencies before migrating legacy applications.
- Estimating AWS costs using historical performance data.
- Auditing on-premises infrastructure for compliance (e.g., GDPR, HIPAA).

**Example**:

```python  
# Use AWS CLI to export discovered server data  
aws discovery describe-agents --filter="name=agentIds,values=agent-123456"  
```

---

## **2. AWS Database Migration Service (DMS)**

**Purpose**:

- **Database Migration**: Supports homogeneous (e.g., Oracle to Oracle) and heterogeneous (e.g., Oracle to Aurora) migrations.
- **Continuous Replication**: Change Data Capture (CDC) ensures minimal downtime.
- **Targets**: Amazon RDS, Redshift, S3, DynamoDB, etc.

**Use Cases**:

- Migrating transactional databases (e.g., Oracle, MySQL) to AWS.
- Replicating data for hybrid architectures (on-premises ↔ AWS).
- Syncing data to S3 for analytics (e.g., Redshift data lakes).

**Example**:

```sql  
-- Create a DMS task for full load + CDC  
CREATE TASK mytask  
TYPE CDC  
SOURCE_ENGINE oracle  
TARGET_ENGINE aurora;  
```

---

## **3. AWS DataSync**

**Purpose**:

- **Bulk Data Transfer**: Moves large datasets between on-premises, edge locations, and AWS storage (S3, EFS, FSx).
- **Speed**: Up to 10x faster than open-source tools via parallel transfers and compression.
- **Managed Service**: Automates scheduling, retries, and validation.

**Use Cases**:

- Migrating file shares (NFS, SMB) to Amazon S3.
- Syncing backups between on-premises NAS and AWS.
- Replicating data across regions for disaster recovery.

**Example**:

```bash  
# Create a DataSync task for S3 migration  
aws datasync create-task --source-location arn:aws:datasync:us-east-1:123456789012:location/loc-abc123 \  
--destination-location arn:aws:datasync:us-east-1:123456789012:location/loc-def456 \  
--options "VerifyMode=POINT_IN_TIME_CONSISTENT"  
```

---

## **4. AWS Snow Family**

**Purpose**:

- **Offline Data Transfer**: Physically ships data via Snowcone (8 TB), Snowball (80 TB), or Snowmobile (100 PB).
- **Edge Computing**: Process data locally before transferring (e.g., Snowcone with AWS Outposts).
- **Security**: AES-256 encryption and tamper-resistant hardware.

**Use Cases**:

- Migrating petabytes of data with limited bandwidth.
- Disaster recovery for remote locations (e.g., oil rigs).
- Edge data processing in disconnected environments.

**Example**:

```python  
# Use AWS OpsHub to configure Snowball  
snowballEdge unlock-device --manifest-file ./manifest.json --unlock-code XXXX-XXXX-XXXX  
```

---

## **5. AWS Transfer Family**

**Purpose**:

- **Managed File Transfers**: Supports SFTP, FTPS, FTP, and AS2 protocols.
- **Storage Integration**: Directly transfers files to/from S3, EFS, or FSx.
- **Authentication**: Integrates with Active Directory, LDAP, or custom users.

**Use Cases**:

- Replacing on-premises FTP servers with a cloud-native solution.
- Partner-facing data exchanges (e.g., healthcare claims via AS2).
- Migrating legacy file-based workflows to S3.

**Example**:

```bash  
# Transfer files using OpenSSH  
sftp -i ~/.ssh/key.pem user@server.transfer.us-east-1.amazonaws.com  
> put /local/path/file.csv /s3-bucket/remote/path/  
```

---

## **Decision Guide: When to Use Which Service**

| **Scenario** | **Tool** | **Key Features** |
| :-- | :-- | :-- |
| **Database Migration** | AWS DMS | CDC, heterogeneous support, minimal downtime. |
| **Bulk File Transfer (On-Prem → S3)** | AWS DataSync | Fast, automated, supports NFS/SMB. |
| **Petabyte Offline Transfer** | AWS Snow Family | Physical devices, edge compute, encryption. |
| **Secure File Protocols (SFTP/FTPS)** | AWS Transfer Family | Managed SFTP server, S3 integration. |
| **Migration Planning** | Application Discovery | Server/dependency mapping, cost estimation. |

---

## **Comparison Table**

| **Criteria** | **DMS** | **DataSync** | **Snow Family** | **Transfer Family** |
| :-- | :-- | :-- | :-- | :-- |
| **Data Type** | Databases | Files/Objects | Any | Files |
| **Transfer Speed** | Medium | High | N/A (Offline) | Medium |
| **Protocol Support** | ODBC/JDBC | NFS/SMB | N/A | SFTP/FTPS/FTP/AS2 |
| **Use Case** | OLAP/OLTP Migration | NAS Migration | Offline Bulk Transfer | Legacy FTP Replacement |
| **Managed Service** | Yes | Yes | No (Hardware) | Yes |

---

## **Common Scenarios**

1. **Migrating an Oracle Database to Aurora**:
    - Use **AWS DMS** for schema conversion and CDC.
    - Validate with checksums and monitor via CloudWatch.
2. **Transferring 500 TB of Medical Images to S3**:
    - Use **AWS Snowball** (6 devices) for offline transfer.
    - Encrypt data with AWS KMS and validate post-upload.
3. **Replacing an On-Premises FTP Server**:
    - Use **AWS Transfer Family** to create an SFTP endpoint linked to S3.
    - Authenticate users via Active Directory.
4. **Syncing NAS to S3 Every Hour**:
    - Use **AWS DataSync** with a 1-hour schedule.
    - Enable data integrity verification.

---

## **Key Considerations**

- **Bandwidth**: Use Snow Family if network costs/time exceed device shipping.
- **Downtime Tolerance**: DMS (low downtime) vs. Snow Family (higher downtime).
- **Security**: Encrypt data in transit (SSL) and at rest (AWS KMS).
- **Cost**: DMS (\$0.015/GB) vs. DataSync (\$0.0125/GB) vs. Snowball (\$300/device + shipping).

By aligning your data type, volume, and operational requirements with these tools, you can ensure efficient and secure migrations to AWS.

<div style="text-align: center">⁂</div>

[^1]: https://docs.aws.amazon.com/application-discovery/latest/userguide/what-is-appdiscovery.html

[^2]: https://k21academy.com/amazon-web-services/aws-migration/aws-database-migration-service-2/

[^3]: https://digitalcloud.training/aws-datasync/

[^4]: https://k21academy.com/amazon-web-services/aws-snow-family/

[^5]: https://aws.amazon.com/aws-transfer-family/features/

[^6]: https://www.augmentedtechlabs.com/blog/how-to-choose-aws-data-migration-service

[^7]: https://d1.awsstatic.com/cloudendure-migration-and-aws-application-migration-service-comparison-table.pdf

[^8]: https://docs.aws.amazon.com/transfer/latest/userguide/getting-started.html

[^9]: https://docs.aws.amazon.com/prescriptive-guidance/latest/large-migration-guide/migration-strategies.html

[^10]: https://docs.aws.amazon.com/mgn/latest/ug/best_practices_mgn.html

[^11]: https://docs.aws.amazon.com/wellarchitected/latest/migration-lens/migration-lens.html

[^12]: https://aws.amazon.com/application-discovery/features/

[^13]: https://tutorialsdojo.com/aws-database-migration-service/

[^14]: https://www.whizlabs.com/blog/application-migration-vs-server-migration/

[^15]: https://docs.aws.amazon.com/transfer/latest/userguide/transfer-file.html

[^16]: https://docs.aws.amazon.com/whitepapers/latest/overview-aws-cloud-data-migration-services/use-case-1-one-time-massive-data-migration.html

[^17]: https://docs.aws.amazon.com/datasync/latest/userguide/what-is-datasync.html

[^18]: https://www.scaler.com/topics/aws/aws-snow-family/

[^19]: https://docs.aws.amazon.com/transfer/latest/userguide/what-is-aws-transfer-family.html

[^20]: https://airbyte.com/top-etl-tools-for-sources/aws-migration-tools

[^21]: https://aws.amazon.com/application-discovery/faqs/

[^22]: https://aws.amazon.com/dms/

[^23]: https://aws.amazon.com/datasync/faqs/

[^24]: https://www.w3schools.com/aws/aws_cloudessentials_mig_awssnowfamily.php

[^25]: https://docs.aws.amazon.com/transfer/latest/APIReference/api-welcome.html

[^26]: https://www.simform.com/blog/aws-migration-strategy/

[^27]: https://docs.aws.amazon.com/application-discovery/latest/APIReference/Welcome.html

[^28]: https://www.w3schools.com/aws/aws_cloudessentials_awsdms.php

[^29]: https://aws.noventiq.com/wp-content/uploads/2020/09/AWS_Migration_8_Best_Practices_ebook_final.pdf

[^30]: https://docs.aws.amazon.com/prescriptive-guidance/latest/application-portfolio-assessment-guide/iterating-6-rs-migration-strategy-selection.html

[^31]: https://www.cloudzero.com/blog/aws-migration-tools/

[^32]: https://docs.aws.amazon.com/transfer/latest/userguide/tutorials-transfer.html

[^33]: https://docs.aws.amazon.com/whitepapers/latest/overview-aws-cloud-data-migration-services/use-case-2-continuous-on-premises-data-migration.html

[^34]: https://d1.awsstatic.com/Migration/migrating-to-aws-ebook.pdf

[^35]: https://docs.aws.amazon.com/prescriptive-guidance/latest/application-portfolio-assessment-guide/prioritization-and-migration-strategy.html

[^36]: https://aws.amazon.com/application-migration-service/pricing/

[^37]: https://www.stackgenie.io/sftp-simplified-with-aws-transfer-family-and-s3-buckets/

[^38]: https://www.clearscale.com/services/cloud-migration

[^39]: https://docs.aws.amazon.com/prescriptive-guidance/latest/strategy-large-scale-migrations/welcome.html

[^40]: https://k21academy.com/amazon-web-services/aws-migration/7r-cloud-migration-strategy-steps-to-successful-app-migration/

