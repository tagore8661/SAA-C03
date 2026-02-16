# EC2 Solutions Architect Associate Level

## Course Overview
This section covers advanced EC2 topics for the AWS Solutions Architect Associate certification, including networking concepts, placement strategies, elastic network interfaces, and hibernation features.

---

## Table of Contents

1. [Private vs Public vs Elastic IP](#private-vs-public-vs-elastic-ip)
2. [Private vs Public vs Elastic IP Hands On](#private-vs-public-vs-elastic-ip-hands-on)
3. [EC2 Placement Groups](#ec2-placement-groups)
4. [EC2 Placement Groups - Hands On](#ec2-placement-groups---hands-on)
5. [Elastic Network Interfaces (ENI) - Overview](#elastic-network-interfaces-eni---overview)
6. [Elastic Network Interfaces (ENI) - Hands On](#elastic-network-interfaces-eni---hands-on)
7. [ENI - Extra Reading](#eni---extra-reading)
8. [EC2 Hibernate](#ec2-hibernate)
9. [EC2 Hibernate - Hands On](#ec2-hibernate---hands-on)

---

## Private vs Public vs Elastic IP

### IP Address Fundamentals

#### IPv4 vs IPv6
- **IPv4**: Most common format (four numbers separated by dots)
  - Example: 192.168.1.1
  - Allows for 3.7 billion public addresses
  - Still the most common format used online
- **IPv6**: Less common, longer format with symbols and letters
  - Used primarily for IoT (Internet of Things)
  - Solves IPv4 address exhaustion problems

#### Public vs Private IP Concepts

##### Public IP Addresses
- **Internet Accessible**: Machines can be identified on the internet
- **Unique Globally**: No two machines can have the same public IP
- **Geolocation**: IP addresses can be traced to geographic locations
- **Direct Communication**: Servers can talk to each other over the internet

##### Private IP Addresses
- **Private Network Only**: Machines identified only within private network
- **Unique Within Network**: Must be unique only within the private network
- **Shared Across Networks**: Different companies can have same private IPs
- **Internet Access**: Through NAT device and internet gateway acting as proxy
- **Specified Ranges**: Only specific IP ranges can be used as private IPs

#### Network Architecture Examples
- **Company Networks**: Each company has private network with internal IPs
- **Internet Gateway**: Provides bridge between private and public networks
- **Communication Pattern**: Private IPs for internal, public IPs for external

### Elastic IP Addresses

#### Purpose and Characteristics
- **Fixed Public IP**: Static IPv4 address that doesn't change
- **Ownership**: You own the IP as long as you don't delete it
- **Attachment**: Can attach to one instance at a time only
- **Failover Capability**: Can quickly move between instances for fault tolerance

#### Limitations and Considerations
- **Account Limit**: Only 5 Elastic IPs per account by default
- **Architectural Concern**: Often indicates poor architectural decisions
- **Better Alternatives**: 
  - Use random public IP with DNS name (Route 53)
  - Use Load Balancer instead of public IP (best practice)
- **Cost Considerations**: Additional costs associated with Elastic IPs

#### Pricing Information
- **Hourly Charge**: ~$0.005 per hour (about $3.50 monthly)
- **Applies To**: Both public IPv4 and Elastic IP addresses
- **Free Tier**: 750 hours per month of free public IPv4 addresses
- **Best Practice**: Terminate unused instances and Elastic IPs to avoid charges

---

## Private vs Public vs Elastic IP Hands On

### IP Address Behavior Observation

#### Default EC2 Instance IP Configuration
- **Private IP**: For internal AWS network access
- **Public IP**: For external internet access (WWW)
- **SSH Access**: Must use public IP unless connected via VPN

#### SSH Connection Testing
1. **Public IP SSH**: Works from external networks
   ```bash
   ssh -i key.pem ec2-user@public-ip
   ```
2. **Private IP SSH**: Fails from external networks
   ```bash
   ssh -i key.pem ec2-user@private-ip
   # Connection fails - not on same network
   ```

#### Public IP Change Behavior
1. **Stop Instance**: Instance enters stopping state
2. **Start Instance**: Instance obtains new public IP
3. **Private IP Stability**: Private IP remains unchanged
4. **SSH Impact**: Old public IP no longer works, must use new IP

### Elastic IP Configuration

#### Elastic IP Allocation
1. **Navigate**: EC2 → Elastic IPs
2. **Allocate**: Create new IP from Amazon's IPv4 pool
3. **Ownership**: You now own the IP as long as you rent it

#### Elastic IP Association
1. **Action**: Associate Elastic IP address
2. **Target**: Choose instance or network interface
3. **Private IP**: Select private IP for association
4. **Result**: Instance keeps Elastic IP as long as attached

#### Elastic IP Benefits
- **Stable Public IP**: Public IPv4 doesn't change on stop/start
- **Consistent Access**: Same SSH command works after restart
- **Failover Support**: Can move between instances for fault tolerance

#### Elastic IP Management
1. **Disassociate**: Remove from instance when not needed
2. **Release**: Delete Elastic IP to avoid charges
3. **Cost Awareness**: Release unused Elastic IPs promptly

### Cost Management Best Practices
- **Monitor Usage**: Track Elastic IP usage to minimize costs
- **Free Tier Management**: Stay within 750 hours monthly limit
- **Proper Cleanup**: Always release unused Elastic IPs
- **Instance Management**: Terminate instances when not needed

---

## EC2 Placement Groups

### Placement Group Overview

#### Purpose and Control
- **Hardware Placement**: Control EC2 instance placement within AWS infrastructure
- **Performance Strategy**: Define how instances are placed relative to each other
- **Advanced Feature**: Used for specific performance and availability requirements

#### Three Placement Strategies

##### 1. Cluster Placement Group
- **Strategy**: Group instances together in low-latency hardware setup
- **Location**: Single availability zone only
- **Performance**: High performance but high risk
- **Network**: 10 Gbps bandwidth between instances with enhanced networking
- **Benefits**: Low latency, high throughput network
- **Risk**: Entire group fails if availability zone fails
- **Use Cases**:
  - Big data jobs requiring fast completion
  - Applications needing extremely low latency
  - High throughput network requirements

##### 2. Spread Placement Group
- **Strategy**: Spread instances across different hardware
- **Scope**: Can span across multiple availability zones
- **Risk Reduction**: Minimizes failure risk through distribution
- **Limitation**: Maximum 7 instances per AZ per placement group
- **Benefits**: Reduced risk of simultaneous failure
- **Use Cases**:
  - Critical applications requiring maximum availability
  - Applications where instance failures must be isolated
  - High availability requirements

##### 3. Partition Placement Group
- **Strategy**: Spread instances across partitions within AZs
- **Structure**: Partitions represent different hardware racks
- **Scale**: Up to 7 partitions per AZ, hundreds of EC2 instances
- **Isolation**: Each partition isolated from another partition's failure
- **Metadata**: Partition information accessible via metadata service
- **Use Cases**:
  - Partition-aware applications (HDFS, HBase, Cassandra, Kafka)
  - Big data applications requiring distribution
  - Applications that can distribute data across partitions

### Placement Group Comparison

| Strategy | Performance | Risk | Scale | Use Case |
|----------|-------------|------|-------|----------|
| Cluster | Highest | Highest | Limited | Big data, low latency |
| Spread | Moderate | Lowest | 7 instances/AZ | Critical applications |
| Partition | High | Moderate | Hundreds | Partition-aware apps |

### Advanced Considerations
- **Hardware Racks**: Partitions represent physical rack separation
- **Failure Isolation**: Partition failures don't affect other partitions
- **Application Awareness**: Applications must be partition-aware for partition groups
- **Metadata Access**: Can programmatically determine partition placement

---

## EC2 Placement Groups - Hands On

### Placement Group Creation

#### Access Placement Groups
1. **Navigate**: EC2 → Network & Security → Placement Groups
2. **Create Group**: Click "Create placement group"

#### Creating Different Strategy Groups

##### High Performance Group (Cluster)
1. **Name**: my-high-performance-group
2. **Strategy**: Cluster
3. **Purpose**: Maximum network performance

##### Critical Group (Spread)
1. **Name**: my-critical-group
2. **Strategy**: Spread
3. **Spread Level**: Rack (default, host for outposts only)

##### Distributed Group (Partition)
1. **Name**: my-distributed-group
2. **Strategy**: Partition
3. **Partitions**: Choose number (1-7, e.g., 4)

### Launching Instances in Placement Groups

#### Instance Launch with Placement Group
1. **Launch Instances**: Click "Launch instances"
2. **Configure**: Choose AMI, instance type, key pair, security groups
3. **Advanced Details**: Scroll to bottom
4. **Placement Group**: Select desired group from dropdown
5. **Launch**: Complete instance launch

#### Available Groups
- my-critical-group (Spread strategy)
- my-distributed-group (Partition strategy)
- my-high-performance-group (Cluster strategy)

### Practical Considerations
- **Strategy Selection**: Choose based on application requirements
- **Cost Awareness**: Some strategies may have different cost implications
- **Performance Testing**: Test actual performance benefits in your use case
- **Monitoring**: Monitor instance placement and performance

---

## Elastic Network Interfaces (ENI) - Overview

### ENI Fundamentals

#### Definition and Purpose
- **ENI**: Elastic Network Interface (virtual network card)
- **Logical Component**: Part of VPC architecture
- **Network Access**: Provides EC2 instances with network connectivity
- **Multi-Service**: Used outside EC2 instances as well

#### ENI Attributes

##### IP Configuration
- **Primary Private IPv4**: One primary private IP per ENI
- **Secondary Private IPv4**: One or more secondary private IPs
- **Elastic IPv4**: One elastic IP per private IPv4
- **Public IPv4**: One or more public IPs

##### Security and Identification
- **Security Groups**: One or more security groups attached
- **MAC Address**: Unique hardware address
- **Interface ID**: Unique identifier for each ENI

#### ENI Characteristics
- **AZ Bound**: Bound to specific availability zone
- **Independent Creation**: Can create ENIs independently from instances
- **Dynamic Attachment**: Can attach/detach on the fly
- **Failover Support**: Move between instances for failover scenarios

### ENI Architecture

#### Primary ENI (eth0)
- **Default Interface**: Every EC2 instance has primary ENI
- **Network Connectivity**: Provides basic network access
- **Private IP**: Primary private IPv4 address
- **Public IP**: Associated public IP (if applicable)

#### Secondary ENI (eth1+)
- **Additional Interfaces**: Can attach multiple secondary ENIs
- **Extra IPs**: Additional private and public IPs
- **Application Separation**: Separate network configurations for different apps

#### ENI Movement and Failover
- **Detach and Attach**: Move ENIs between instances in same AZ
- **IP Preservation**: Private IP moves with ENI
- **Failover Scenarios**: 
  - Move IP from failed instance to healthy instance
  - Maintain consistent private IP for application access
  - Enable quick network failover

### Use Cases and Benefits
- **Multi-IP Applications**: Applications requiring multiple IP addresses
- **Network Failover**: Quick IP movement between instances
- **Application Segmentation**: Separate network configurations
- **High Availability**: Maintain IP consistency during failures
- **Flexible Networking**: Advanced network configuration options

---

## Elastic Network Interfaces (ENI) - Hands On

### ENI Creation and Management

#### Launch Test Instances
1. **Launch Two Instances**: Create two EC2 instances for testing
2. **Configuration**: Amazon Linux 2, t2.micro, existing security groups
3. **Network Observation**: Examine default network interfaces

#### Default ENI Examination
1. **Instance Details**: Check networking section for each instance
2. **Interface ID**: Note primary ENI ID for each instance
3. **IP Configuration**: Observe public and private IPv4 addresses
4. **Network Interfaces**: Navigate to Network Interfaces section

#### Manual ENI Creation
1. **Navigate**: EC2 → Network & Security → Network Interfaces
2. **Create ENI**: Click "Create network interface"
3. **Configuration**:
   - **Description**: demo ENI
   - **Subnet**: Choose subnet matching EC2 instances (same AZ)
   - **Private IP**: Auto-assign or customize
   - **Security Group**: Attach appropriate security group
4. **Create**: Complete ENI creation

### ENI Attachment and Movement

#### ENI Attachment
1. **Select ENI**: Choose newly created ENI
2. **Action**: Attach to instance
3. **Target Instance**: Select first EC2 instance
4. **Verify**: Check instance networking shows two interfaces

#### ENI Movement (Failover Demo)
1. **Detach ENI**: 
   - Select ENI → Action → Detach
   - Use force detach if needed
2. **Attach to Different Instance**:
   - Select ENI → Action → Attach
   - Choose second EC2 instance
3. **Verify Movement**: 
   - First instance: Now has one interface
   - Second instance: Now has two interfaces

#### Instance Termination Behavior
1. **Terminate Instances**: Shut down both test instances
2. **ENI Persistence**:
   - Manual ENI: Remains available
   - Instance-created ENIs: Automatically deleted
3. **Cleanup**: Delete manual ENI if not needed

### Advanced ENI Concepts

#### ENI Control Benefits
- **IP Persistence**: Manual ENIs provide control over private IPs
- **Failover Capability**: Quick IP movement between instances
- **Network Flexibility**: Advanced networking configurations
- **Application Architecture**: Support for complex network setups

#### Cost Considerations
- **ENI Cost**: Generally no cost for ENIs themselves
- **Instance Cost**: Primary cost driver is still EC2 instances
- **Cleanup Importance**: Remove unused ENIs to maintain clean environment

---

## ENI - Extra Reading

### ENI Clarification and Learning Resources

#### Concept Complexity
- **Advanced Topic**: ENIs are advanced AWS networking concepts
- **Learning Curve**: May take time to fully understand

#### Additional Learning Resources
- **AWS Blog**: Comprehensive ENI overview and use cases
- **Documentation**: Official AWS documentation for deep dives
- **Practical Experience**: Hands-on practice builds understanding over time

---

## EC2 Hibernate

### Hibernation Fundamentals

#### Traditional Instance Behavior
- **Stop Instance**: Data on EBS disk preserved, RAM lost
- **Terminate Instance**: Root volume destroyed (if configured), other volumes preserved
- **Start Instance**: OS boots, user data runs, application starts, caches warm up
- **Boot Time**: Traditional boot process takes time

#### Hibernation Innovation
- **RAM Preservation**: In-memory state preserved during hibernation
- **Fast Boot**: Instance boot much faster than traditional start
- **State Continuity**: Operating system frozen, not restarted
- **Underlying Process**: RAM state written to root EBS volume file

### Hibernation Process

#### Hibernate Sequence
1. **Running Instance**: EC2 instance with data in RAM
2. **Start Hibernation**: Instance enters stopping state
3. **RAM Dump**: RAM content written to EBS volume
4. **Instance Shutdown**: Instance stops, RAM disappears
5. **EBS Preservation**: EBS volume contains RAM dump
6. **Start Instance**: RAM loaded from disk onto EC2 instance
7. **State Restoration**: Instance continues as if never stopped

#### Technical Requirements
- **Root Volume**: Must be EBS volume (not instance store)
- **Encryption**: Root EBS volume must be encrypted
- **Size**: Must be large enough to contain RAM dump
- **Instance Families**: Supports many instance families
- **RAM Limit**: Instance RAM size must be less than 150 GB

### Hibernation Use Cases

#### Long-Running Processes
- **Process Continuity**: Never stop long-running processes
- **State Preservation**: Maintain application state across stops/starts
- **Time Savings**: Avoid lengthy application reinitialization

#### Service Initialization
- **Slow Services**: Services that take time to initialize
- **Cache Preservation**: Keep cache warm across hibernation
- **Fast Recovery**: Quick recovery from maintenance windows

#### Development and Testing
- **Development Environments**: Preserve development state
- **Testing Scenarios**: Maintain test state across sessions
- **Cost Optimization**: Stop instances without losing work

### Hibernation Characteristics

#### Supported Configurations
- **Instance Types**: Many families supported (not bare metal)
- **Operating Systems**: Linux and Windows support
- **Purchase Options**: On-demand, reserved, and spot instances
- **Time Limit**: Intended for no more than 60 days hibernation

#### Limitations and Considerations
- **RAM Size**: 150 GB limit (subject to change)
- **Instance Types**: Not available for bare metal instances
- **Encryption Requirement**: Mandatory EBS encryption
- **Storage Planning**: Ensure adequate EBS size for RAM dump

---

## EC2 Hibernate - Hands On

### Hibernation Setup

#### Instance Configuration
1. **Launch Instance**: Create new EC2 instance
2. **Base Configuration**: Amazon Linux 2, t2.micro, select key pair
3. **Security**: Use existing security group (launch-wizard-1)
4. **Storage Configuration**:
   - Navigate to advanced storage settings
   - Enable **encryption** for root EBS volume
   - Use default AWS/EBS encryption KMS Key
   - Ensure 8 GB volume (sufficient for 1 GB RAM)

#### Enable Hibernation
1. **Advanced Details**: Scroll to hibernation behavior
2. **Enable Hibernation**: Check the hibernation option
3. **Validation**: Confirm root volume size and encryption
4. **Launch Instance**: Complete instance launch with hibernation enabled

### Hibernation Testing

#### Baseline Uptime Check
1. **Connect to Instance**: Use EC2 Instance Connect
2. **Check Uptime**: Run `uptime` command
   ```bash
   uptime
   # Shows system uptime (starts at 0 minutes)
   ```
3. **Wait**: Let instance run for a few minutes
4. **Verify**: Uptime should increase (1 minute, 2 minutes, etc.)

#### Hibernation Process
1. **Disconnect**: Exit from EC2 Instance Connect
2. **Hibernate Instance**: 
   - Select instance → Instance state → Hibernate instance
3. **Wait for Stop**: Instance enters stopping then stopped state
4. **Start Instance**: Instance state → Start instance

#### Hibernation Verification
1. **Reconnect**: Use EC2 Instance Connect to access instance
2. **Check Uptime**: Run `uptime` command again
3. **Expected Result**: Uptime continues from previous value (not reset to 0)
4. **Confirmation**: Instance preserved RAM state across hibernation

### Expected Results Analysis

#### Without Hibernation
- **Stop/Start**: Uptime resets to 0 minutes
- **Full Boot**: Complete OS and application restart
- **Cache Cold**: All caches and warm-up processes restart

#### With Hibernation
- **Hibernate/Start**: Uptime continues from previous value
- **State Preservation**: RAM state maintained
- **Fast Recovery**: Applications continue as if never stopped