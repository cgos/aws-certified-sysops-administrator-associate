# EC2 for SysOps
 - Changing instance type
 - Placement groups
 - Shutdown behavior and termination protection
 - Troubleshoot launch issues
 - Troubleshoot ssh issues
 - Instances launch types
 - Spot instances & spot fleet
 - Instance launch types 
 - Instance type deep dive
 - AMIs
   - Cross account copy
 - Elastic IP
 - CloudWatch metrics 


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


## EC2 Shutdown behavior and Termination Protection
CLI Attribute called: InstanceInitiatedShutdownBehavior



## EC2 Troubleshoot launch issues
Error:
- InstanceLimitExceeded: you reached the max number of instances per region.
  - Resolution: Launch the instance in a different region or request AWS to increase your limit for this this region
- InsufficientInstanceCapacity: not enough On-Demand capacity for this AZ
  - Resolution: wait a few minutes or send request individually instead of in bulk
- Instance terminates immediately (from pending to terminated), To find the exact reason checkout the EC2 Desciption tab next to the State Transition reason.Problem might be:
  - EBS volume limit reached
  - EBS snapshot is corrupt
  - Root EBS is encrypted and you do not have permission to access the KMS
  - Required part from the AMI is missing

## EC2 SSH trouble
- Make sure the private key file (pem) has the right permission
- Make sure the username is given correctly. It has to be (note: ec2-user):
```ssh ec2-user@34.222.22.222 -i whatever.pem``` 


Possible reasons for connection timeout:
- SecurityGroup not configured correctly
- CPU load of the instance is too high