# EC2 for SysOps
 - Changing instance type
 - Placement groups
   - Cluster: grouping of instances within a single Availability Zone. cluster placement group enjoy a higher per-flow throughput limit
   - Partition: reduce the likelihood of correlated hardware failures. used to deploy large distributed and replicated workloads, such as HDFS, HBase, and Cassandra
   - Spread: recommended for applications that have a small number of critical instances that should be kept separate from each other. 
 - Shutdown behavior and termination protection
 - Troubleshoot launch issues
 - Troubleshoot ssh issues



