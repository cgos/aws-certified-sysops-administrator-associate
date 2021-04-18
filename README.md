These are my notes from the [Ultimate AWS Certified SysOps Administrator Associate 2021](https://www.udemy.com/course/ultimate-aws-certified-sysops-administrator-associate/) course

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


## EC2 Placement Groups
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


## EC2 Shutdown Behavior and Termination Protection
CLI Attribute called: InstanceInitiatedShutdownBehavior



## EC2 Troubleshoot Launch Issues
Error:
- **InstanceLimitExceeded**: you reached the max number of instances per region.
  - Resolution: Launch the instance in a different region or request AWS to increase your limit for this this region
- **InsufficientInstanceCapacity**: not enough On-Demand capacity for this AZ
  - Resolution: wait a few minutes or send request individually instead of in bulk
- **Instance terminates immediately** (from pending to terminated), To find the exact reason checkout the EC2 Desciption tab next to the State Transition reason.Problem might be:
  - EBS volume limit reached
  - EBS snapshot is corrupt
  - Root EBS is encrypted and you do not have permission to access the KMS
  - Required part from the AMI is missing

## EC2 SSH Trouble
- Make sure the private key file (pem) has the right permission
- Make sure the username is given correctly. It has to be (note: ec2-user):
```ssh ec2-user@34.222.22.222 -i whatever.pem``` 

Possible reasons for connection timeout:
- SecurityGroup not configured correctly
- CPU load of the instance is too high

## EC2 Launch Types
- On demande instances: short workload and predictable pricing
- Reserved: for minimum 1 year: 
  - Reserved instances: long workload: **75% discount**
  - Convertible reserved instances: flexible instances: **54% discount**
  - Scheduled reserved instances: based on schedule: 
- Spot instances: short workload and cheap but less reliable: **90% discount**
- Dedicated instances: no other customers will share your hardware
- Dedicated hosts: book an entire physical server. Great for licences purposes

**Great Combo**: Reserved instances for baseline + On-Demand/Spot for peaks

**EC2 Spot Instances**
2 Strategies:
1. Max spot price
2. Spot block: block for a specified time frame

To terminate spot instances: first you need to cancel the spot request and then termine the running spot instances.

**EC2 Spot Fleets**
The ultimate way to save money.

**EC2 Instance Types**
R: app that needs RAM - in-memory cache
C: app that needs CPU - compute/databases
M: MEDIUM: cpu/ram - web app
I: I/O - databases
G: GPU - machine learning/video rendering
T2/T3: burstable

## EC2 AMI
**Cross Account AMI Copy**
- if you copy an AMI that has been shared with your account, you are the owner of the target AMI in your account.
- To copy an AMI that was shared with you from another account, the owner of the source AMI must grant read permission for the storage that backs the AMI.
- you can't copy an encrypted AMI that was shared with you from another account.
- you can't copy an AMI with an associated billingProduct code that was shared with you from another account.

https://aws.amazon.com/premiumsupport/knowledge-center/copy-ami-region/

https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/CopyingAMIs.html

## Elastic IPs
- When you stop/start an EC2 instance, the public IP will change
- To have a fixed public IP, you need an Elastic IP
- you can attach it to one instance at a time
- you don't pay if the elastic IP is attached to a server
- you can only have 5 elastic IPs per account
- try to avoid avoid using elastic IP
  - use DNS
  - use ELB

 ## CloudWatch metrics for EC2
 AWS provided metrics:
  - Basic Monitoring: at 5 minutes interval
  - Detailed Monitoring: at 1 minute interval
  - Includes CPU, Network, Disk and Status Check 

Custom metrics:
  - Basic: 1 minute

**RAM is NOT included in the AWS EC2 metrics**



# Management of EC2 at scale: System Manager & OpsWork
**Systems Manager**
- Parameter Store
- Run Command
- Session Manager
- Patch Manager

# EC2 High Availability and Scalability
**Load Balancers**
- Troubleshooting
- Advanced options and logging
- CloudWatch integrations

2 kinds of scalability:
 - Vertical (increase instance size)
 - Horizontal (elastic)

**Load Balancer Stickiness**
- Works with classic and ALB
- Uses cookies with an expiration date
- Enabling stickiness may bring imbalance on traffic routing

# Load Balancer SysOps
ALB:
  - Layer 7
  - URL based routing
  - No fix IP, but DNS
  - Provide SSL termination
  - Needs pre-warming (same for classic ELB): 
    - ALB scale gradually to traffic
    - ALB may fail in case of sudden spike
    - Could open ticket to pre-warm ALB

NLB:
  - Layer 4
  - No pre-warming
  - 1 static IP per subnet
  - No SSL termination

Exam: How do you provide a fix IP to an ALB? Chain an NLB and a ALB to provide fix IP

**Load Balancer Error Codes**
Unsuccessful at the client side: 4xx code:
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 460: client closed connection
  - 463: X-Forwarded For header has more than 30 IP (malformed request)
 
 Unsuccessful at the server side: 5xx code:
  - 500: Internal server error: some error on the ELB itself
  - 502: Bad Gateway
  - 503: Service Unavailable`
  - 504: Gateway timeout: issue within the server
  - 561: Unauthorized

**Load Balancer SSL**
How to support legavy browser with old TLS? change policy to suuport weaker cipher

**Laod Balancer common troubleshooting**
- Check Security Groups
- Check Health Checks
- Sticky sessions
- For Multi-AZ, make sure cross zone balancing is enabled
- Use Internal ELB for private app that don't need public access
- Enable Deletion Protection to prevent deletion

# Load Balancer Monitoring: Metric/Logging/Tracing
**Load Balander CloudWatch metrics**
- BackendConnectionErrors
- HealthyHostCount/UnHealthyHostCount
- HTTP_Code_Backend_2XX: Success
- HTTP_Code_Backend_3XX: Redirected
- HTTP_Code_Backend_4XX: Client error
- HTTP_Code_Backend_5XX: Server error
- Latency
- RequestCount
- SurgeQueueLength: Pending request, Max 1024
- SpilloverCount: Number of rejected request because of surge queue is full

**Load Balancer Access Logs**
- Logs are stored in S3
- Logs contain:
  - Time
  - Client IP
  - Latencies
  - Request paths
  - Server response
  - Trace Id
- Only pay for S3 storage
- Helpful for compliance
- Helpful for keeping data after ELB or EC2 are terminated
- Access Log are encrypted by default

**ALB Request Tracing**
- Request tracing through custom header: ```X-Amzn-Trace-Id```

**Auto Scaling Group**
Attributes:

1. Launch Configuration
  - AMI + instance type
  - EC2 User Data
  - EBS Volume
  - Security Groups
  - SSH Key Pair
2. Min Size / Max Size / Initial Capacity
3. Network + Subnet info
4. Load Balancer info (target group)
5. Scaling Policies (when to scale out/in)

Auto Scaling Alarms
- Possible to scale on CW alarms
- Alarm can monitor metric
- Metrics are computed for the overall ASG instances 
- The following metrics are available for ASG:
  - GroupMinSize
  - GroupMaxSize
  - GroupDesiredCapacity
  - GroupInServiceInstances
  - GroupPendingInstances
  - GroupStandbyInstances
  - GroupTerminatingInstances
  - GroupTotalInstances

Scaling Processes
- Launch: add new EC2
- Terminate: Remove EC2
- HealthCheck
- ReplaceUnhealthy: terminate unhealthy instances and re-create them
- AZRebalance: Balance the number of ec2 instances across AZ
- AlarmNotification: Accept notification from CW
- ScheduledActions: Performs scheduled actions
- AddToLoadBalancer: Adds instances to ELB or TG