# EC2 for SysOps
 - Changing instance type
 - Placement groups
 - Shutdown behavior and termination protection
 - Troubleshoot launch issues
 - Troubleshoot ssh issues

## EC2 Placement groups
### Cluster 
Grouping of instances within a single Availability Zone. cluster placement group enjoy a higher per-flow throughput limit

**Pro**
- Great Network
  
**Cons**
- If the rack fails all instance dies

**Use Case**
- Big data tha need to complete fast
- App with extremely low latency and high network throughput

### Partition
Reduce the likelihood of correlated hardware failures. used to deploy large distributed and replicated workloads, such as HDFS, HBase, and Cassandra

**Pro**
- 
  
**Cons**
- 

**Use Case**
- 

### Spread
Recommended for applications that have a small number of critical instances that should be kept separate from each other. 

**Pro**
- Can span across AZ
- Reduced risk of failure
- EC2 on different hardware
  
**Cons**
- Limited to 7 instances per AZ per placement group

**Use Case**
- App that needs high availability
- Critical app where each must be isolated
