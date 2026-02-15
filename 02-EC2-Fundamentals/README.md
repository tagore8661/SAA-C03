# EC2 Fundamentals

## Course Overview
This section covers Amazon Elastic Compute Cloud (EC2) fundamentals for the AWS Solutions Architect Associate certification, including instance management, security, cost optimization, and practical hands-on exercises.

---

## Table of Contents

1. [AWS Budget Setup](#aws-budget-setup)
2. [EC2 Basics](#ec2-basics)
3. [Create an EC2 Instance with EC2 User Data to have a Website](#create-an-ec2-instance-with-ec2-user-data-to-have-a-website)
4. [EC2 Instance Types Basics](#ec2-instance-types-basics)
5. [Security Groups & Classic Ports Overview](#security-groups--classic-ports-overview)
6. [Security Groups Hands On](#security-groups-hands-on)
7. [SSH Overview](#ssh-overview)
8. [How to SSH using Linux or Mac](#how-to-ssh-using-linux-or-mac)
9. [How to SSH using Windows](#how-to-ssh-using-windows)
10. [How to SSH using Windows 10](#how-to-ssh-using-windows-10)
11. [SSH Troubleshooting](#ssh-troubleshooting)
12. [EC2 Instance Connect](#ec2-instance-connect)
13. [EC2 Instance Roles Demo](#ec2-instance-roles-demo)
14. [EC2 Instance Purchasing Options](#ec2-instance-purchasing-options)
15. [Spot Instances & Spot Fleet](#spot-instances--spot-fleet)
16. [EC2 Instances Launch Types Hands On](#ec2-instances-launch-types-hands-on)

---

## AWS Budget Setup

### Billing Console Access
- **IAM User Limitation**: IAM users (even administrators) cannot access billing data by default
- **Root Account Required**: Must use root account to enable IAM access to billing information
- **Activation Steps**:
  1. Log in with root account
  2. Go to Accounts → IAM user and role access to billing information
  3. Activate IAM access to allow administrator IAM users to view billing data

### Billing Dashboard Features
- **Cost Overview**: Month-to-date costs, forecasted costs, previous month totals
- **Cost Breakdown**: View costs by month and service
- **Bills Section**: Detailed charges by service for any month
- **Free Tier Dashboard**: Monitor current and forecasted usage against free tier limits

### Budget Creation
- **Zero Spend Budget**: Alert when spending reaches 1 cent
- **Monthly Cost Budget**: Set spending limits (e.g., $10/month)
- **Alert Thresholds**: Configure email notifications at 85% and 100% of budget
- **Benefits**: Prevent unexpected costs, enable quick cost issue debugging

---

## EC2 Basics

### What is Amazon EC2?
- **Elastic Compute Cloud**: Infrastructure as a Service (IaaS) offering
- **Core Components**:
  - **EC2 Instances**: Virtual machines (rentable compute)
  - **EBS Volumes**: Virtual drives for data storage
  - **Elastic Load Balancer**: Distribute load across machines
  - **Auto Scaling Groups**: Scale services automatically

### EC2 Instance Configuration Options
- **Operating Systems**: Linux (most popular), Windows, macOS
- **Compute Resources**: CPU cores and RAM selection
- **Storage Options**:
  - Network-attached (EBS/EFS)
  - Hardware-attached (EC2 instance store)
- **Networking**: Network card speed, public IP configuration
- **Security**: Security groups (firewall rules)
- **Bootstrapping**: EC2 User Data scripts for initial configuration

### EC2 User Data (Bootstrapping)
- **Purpose**: Automate tasks when instance starts
- **Execution**: Runs only once at first launch
- **Permissions**: Executes with root user privileges
- **Common Uses**:
  - Install updates and software
  - Download common files from internet
  - Configure initial settings

---

## Create an EC2 Instance with EC2 User Data to have a Website

### Instance Launch Process
1. **Name and Tags**: Assign instance name (e.g., "My First Instance")
2. **AMI Selection**: Choose Amazon Linux 2 (free tier eligible)
3. **Instance Type**: Select t2.micro (free tier eligible)
4. **Key Pair**: Create/download SSH key for access
5. **Network Settings**: Configure security group rules
6. **Storage**: Configure root volume (8GB gp2 default)
7. **User Data**: Add bootstrap script for web server

### Security Group Configuration
- **SSH Access**: Port 22 from anywhere (0.0.0.0/0)
- **HTTP Access**: Port 80 from anywhere for web server
- **Outbound Rules**: Allow all traffic by default

### User Data Script Example
```bash
#!/bin/bash
# Update system
yum update -y
# Install Apache web server
yum install -y httpd
# Start web server
systemctl start httpd
systemctl enable httpd
# Create web page
echo "<h1>Hello World from $(hostname -f)</h1>" > /var/www/html/index.html
```

### Instance Management
- **Start/Stop**: Control instance state and billing
- **Terminate**: Completely remove instance and resources
- **Public IP Changes**: Stopping/starting may change public IP
- **Private IP**: Remains constant across instance lifecycle

---

## EC2 Instance Types Basics

### Instance Type Naming Convention
- **Format**: `<class><generation>.<size>` (e.g., m5.2xlarge)
- **Class**: Instance family (m=general purpose, c=compute, r=memory)
- **Generation**: Hardware version (higher = newer)
- **Size**: Resources allocation (larger = more CPU/RAM)

### Main Instance Categories

#### General Purpose (t, m families)
- **Use Cases**: Web servers, code repositories, diverse workloads
- **Balance**: Equal compute, memory, networking
- **Examples**: t2.micro (free tier), m5.large, m6g.medium

#### Compute Optimized (c family)
- **Use Cases**: 
  - Batch processing
  - Media transcoding
  - High performance web servers
  - Machine learning
  - Gaming servers
- **Focus**: High CPU performance
- **Examples**: c5.large, c6g.2xlarge

#### Memory Optimized (r, x, z families)
- **Use Cases**:
  - In-memory databases
  - Distributed cache stores (ElastiCache)
  - Business intelligence applications
  - Real-time big data processing
- **Focus**: Large memory capacity
- **Examples**: r5.4xlarge, x1e.32xlarge

#### Storage Optimized (i, d, h families)
- **Use Cases**:
  - High frequency online transaction processing (OLTP)
  - Relational and NoSQL databases
  - Data warehousing
  - Distributed file systems
- **Focus**: High local storage performance
- **Examples**: i3.large, d3.xlarge

### Instance Comparison Tools
- **AWS Website**: Official instance type documentation
- **ec2instances.info**: Third-party comparison website with cost data
- **Console**: Compare instance types within AWS console

---

## Security Groups & Classic Ports Overview

### Security Group Fundamentals
- **Purpose**: Firewall controlling traffic to/from EC2 instances
- **Rules**: Only contain "allow" rules (no deny rules)
- **Scope**: Region and VPC specific
- **Behavior**: Stateful filtering (return traffic automatically allowed)

### Rule Configuration
- **Inbound Rules**: Control incoming traffic
- **Outbound Rules**: Control outgoing traffic (allow all by default)
- **Sources**: IP addresses (IPv4/IPv6) or other security groups
- **Protocols**: TCP, UDP, ICMP, custom protocols

### Security Group Features
- **Multiple Instances**: One SG can attach to multiple instances
- **Multiple SGs**: One instance can have multiple security groups
- **Reference Other SGs**: Allow traffic between instances with specific SGs
- **Live Outside EC2**: Traffic blocked before reaching instance

### Common Ports and Protocols
- **Port 22**: SSH (Secure Shell) - Linux remote access
- **Port 21**: FTP (File Transfer Protocol)
- **Port 22**: SFTP (Secure File Transfer Protocol)
- **Port 80**: HTTP (unsecured web traffic)
- **Port 443**: HTTPS (secured web traffic)
- **Port 3389**: RDP (Remote Desktop Protocol) - Windows access

### Troubleshooting Tips
- **Timeout Error**: Security group issue (traffic blocked)
- **Connection Refused**: Application issue (traffic reached but service not running)
- **Default Behavior**: All inbound blocked, all outbound allowed

---

## Security Groups Hands On

### Security Group Management
- **Console Access**: EC2 → Security Groups
- **Default SG**: Automatically created with VPC
- **Launch Wizard SG**: Created during instance launch
- **Inbound Rules**: Add/remove port access rules
- **Outbound Rules**: Control egress traffic

### Practical Example
1. **View Rules**: Examine existing inbound/outbound rules
2. **Modify Rules**: Add HTTP (port 80) rule for web access
3. **Test Impact**: Remove port 80 rule → website timeout
4. **Restore Access**: Add back port 80 rule → website works
5. **Custom Rules**: Add specific ports or IP restrictions

### Advanced Features
- **SG References**: Allow traffic from specific security groups
- **IP Restrictions**: Limit access to specific IP ranges
- **Multiple Rules**: Combine multiple access rules
- **Rule Priority**: Rules evaluated in order

---

## SSH Overview

### SSH Connection Methods
- **Mac/Linux**: Native SSH command in terminal
- **Windows < 10**: PuTTY application
- **Windows ≥ 10**: Native SSH command in PowerShell/Command Prompt
- **Browser-based**: EC2 Instance Connect (works for all OS)

### SSH Requirements
- **Key Pair**: EC2 key pair for authentication
- **Security Group**: Port 22 (SSH) must be open
- **Instance State**: Instance must be running
- **Public IP**: Use current public IP address
- **Username**: Depends on AMI (ec2-user for Amazon Linux)

### Connection Options
- **Terminal SSH**: Traditional command-line access
- **PuTTY**: GUI-based SSH client for Windows
- **EC2 Instance Connect**: Browser-based SSH, no key management

---

## How to SSH using Linux or Mac

### Preparation Steps
1. **Locate Key File**: Find downloaded .pem key file
2. **Remove Spaces**: Ensure filename has no spaces
3. **Navigate Directory**: Change to directory containing key file
4. **Set Permissions**: Ensure key file has proper permissions

### SSH Command
```bash
ssh -i your-key-file.pem ec2-user@your-public-ip
```

### Common Issues and Solutions
- **Permission Denied**: Check key file permissions
- **Timeout**: Verify security group allows port 22
- **Connection Refused**: Instance may not be running SSH service
- **Wrong User**: Use correct username for AMI (ec2-user for Amazon Linux)

### Directory Navigation
```bash
# Check current directory
pwd

# List files
ls -la

# Change directory
cd /path/to/keys

# Navigate to desktop (example)
cd ~/Desktop

# pem file permissions
chmod 400 your-key-file.pem

```

---

## How to SSH using Windows

### PuTTY Installation and Setup
1. **Download PuTTY**: Get installer from official website
2. **Install PuTTY**: Run installer with default settings
3. **Convert Key**: Use PuTTYgen to convert .pem to .ppk format
4. **Configure PuTTY**: Set host, user, and authentication

### Key Conversion Process
1. **Open PuTTYgen**: Load existing .pem file
2. **Save Private Key**: Generate .ppk file for PuTTY
3. **Store Safely**: Keep .ppk file in accessible location

### PuTTY Configuration
1. **Host Name**: `ec2-user@your-public-ip`
2. **Port**: 22
3. **Connection Type**: SSH
4. **Auth Credentials**: Browse to .ppk file
5. **Save Session**: Store configuration for future use

### Connection Process
1. **Load Session**: Select saved configuration
2. **Connect**: Click Open to start SSH session
3. **Accept Host Key**: Trust the EC2 instance on first connection
4. **Login**: Authenticate using key file

---

## How to SSH using Windows 10

### Check SSH Availability
```powershell
# In PowerShell or Command Prompt
ssh
```
- If command not found, use PuTTY method instead

### SSH Connection Steps
1. **Open PowerShell**: Launch PowerShell terminal
2. **Navigate to Key Directory**: Use `cd` to locate .pem file
3. **Set Permissions**: Ensure key file has proper permissions
4. **Connect**: Use SSH command with key file

### SSH Command
```powershell
ssh -i your-key-file.pem ec2-user@your-public-ip
```

### Permission Fixes
1. **Right-click Key File**: Properties → Security
2. **Advanced Settings**: Disable inheritance
3. **Set Owner**: Make yourself the file owner
4. **Remove Other Users**: Keep only your access
5. **Full Control**: Grant yourself full permissions

### Alternative Methods
- **File Explorer**: Navigate to key directory, right-click → "Open PowerShell here"
- **Command Prompt**: Same commands work in traditional CMD

---

## SSH Troubleshooting

### Common Issues and Solutions

#### 1. Connection Timeout
- **Cause**: Security group blocking port 22
- **Solution**: Verify inbound rule allows SSH from your IP
- **Alternative**: Use EC2 Instance Connect if corporate firewall blocks

#### 2. Connection Refused
- **Cause**: SSH service not running on instance
- **Solution**: Restart instance or terminate and create new one
- **Check**: Ensure using Amazon Linux 2 AMI

#### 3. Permission Denied (publickey)
- **Causes**: 
  - Wrong key file or no key specified
  - Wrong username (must be ec2-user for Amazon Linux)
- **Solutions**:
  - Verify key pair attached to instance
  - Use correct username in SSH command

#### 4. SSH Command Not Found (Windows)
- **Cause**: Windows version < 10 without SSH client
- **Solution**: Use PuTTY or EC2 Instance Connect

#### 5. Nothing Working
- **Solution**: Use EC2 Instance Connect as fallback
- **Requirement**: Must use Amazon Linux 2 AMI

#### 6. Yesterday Worked, Today Doesn't
- **Cause**: Public IP changed after stop/start
- **Solution**: Update SSH command with new public IP

### Quick Troubleshooting Checklist
- [ ] Security group allows port 22
- [ ] Using correct public IP
- [ ] Key file permissions correct
- [ ] Correct username (ec2-user)
- [ ] Instance is running
- [ ] Key pair attached to instance

---

## EC2 Instance Connect

### Browser-Based SSH
- **No Installation**: Works directly in web browser
- **No Key Management**: AWS handles temporary SSH keys
- **Cross-Platform**: Works on Windows, Mac, Linux
- **AMIs Supported**: Amazon Linux 2 and others

### Connection Process
1. **Select Instance**: Go to EC2 → Instances → Select instance
2. **Click Connect**: Choose "EC2 Instance Connect"
3. **Verify Settings**: Check username and IP
4. **Connect**: Click "Connect" to open browser terminal
5. **Use Terminal**: Run commands as with regular SSH

### Requirements
- **Security Group**: Must allow port 22 (SSH)
- **Instance State**: Instance must be running
- **Supported AMI**: Amazon Linux 2 (primary support)
- **IAM Permissions**: Need EC2InstanceConnect policy

### Benefits
- **Simplicity**: No local SSH client needed
- **Troubleshooting**: Great when SSH clients fail
- **Quick Access**: Fast way to access instances
- **Security**: Temporary keys, no long-term credentials

### Limitations
- **Browser Dependency**: Requires modern browser
- **Network**: Needs internet access to AWS console
- **Session Management**: Limited compared to full SSH clients

---

## EC2 Instance Roles Demo

### IAM Roles for EC2
- **Purpose**: Provide AWS permissions to EC2 instances
- **Security**: No need to embed credentials in instances
- **Automatic**: Temporary credentials automatically rotated
- **Best Practice**: Never store IAM keys on EC2 instances

### Role Attachment Process
1. **Create Role**: IAM → Roles → Create role for EC2 service
2. **Attach Policies**: Add appropriate permissions (e.g., IAMReadOnlyAccess)
3. **Launch Instance**: Attach role during or after launch
4. **Verify Access**: Test AWS CLI commands from instance

### Hands-On Demo
1. **Connect to Instance**: Use SSH or EC2 Instance Connect
2. **Test Without Role**: 
   ```bash
   aws iam list-users
   # Error: unable to locate credentials
   ```
3. **Attach IAM Role**: 
   - EC2 → Instances → Select → Security → Modify IAM role
   - Choose DemoRoleForEC2
4. **Test With Role**:
   ```bash
   aws iam list-users
   # Success: Returns IAM users list
   ```

### Common Use Cases
- **S3 Access**: Instances need to read/write S3 buckets
- **DynamoDB**: Applications accessing DynamoDB tables
- **Other AWS Services**: Any AWS API access from applications

### Security Benefits
- **No Hardcoded Keys**: Eliminates credential exposure risk
- **Automatic Rotation**: AWS manages credential lifecycle
- **Granular Permissions**: Fine-tuned access control
- **Audit Trail**: Role usage logged in CloudTrail

---

## EC2 Instance Purchasing Options

### On-Demand Instances
- **Pricing**: Pay per second (Linux/Windows) or per hour
- **Cost**: Highest cost, no upfront payment
- **Commitment**: No long-term commitment
- **Use Cases**: Short-term, unpredictable workloads
- **Benefits**: Maximum flexibility, predictable per-second billing

### Reserved Instances (1 & 3 years)
#### Reserved Instances (RI)
- **Discounts**: Up to 72% compared to On-Demand
- **Commitment**: 1-year or 3-year terms
- **Payment Options**: All upfront, partial upfront, no upfront
- **Scope**: Regional or Zonal capacity reservation
- **Use Cases**: Steady-state applications, databases

#### Convertible Reserved Instances
- **Flexibility**: Change instance type, family, OS, tenancy
- **Discount**: Up to 66% (less than standard RI)
- **Terms**: 1-year or 3-year
- **Use Cases**: Workloads that may change requirements

### Savings Plans
- **Discount**: Up to 70% (same as RI)
- **Commitment**: Dollar amount per hour for 1-3 years
- **Flexibility**: 
  - Instance family flexibility
  - Size flexibility within family
  - OS flexibility
  - Tenancy flexibility
- **Use Cases**: Modern alternative to RIs with more flexibility

### Spot Instances
- **Discounts**: Up to 90% compared to On-Demand
- **Reliability**: Can be reclaimed with 2-minute notice
- **Pricing**: Variable based on supply/demand
- **Use Cases**: 
  - Batch jobs
  - Data analysis
  - Image processing
  - Fault-tolerant workloads
- **Not For**: Critical jobs, databases

### Dedicated Hosts
- **Hardware**: Physical server dedicated to you
- **Cost**: Most expensive option
- **Use Cases**: 
  - Compliance requirements
  - Bring-your-own-license (BYOL) software
  - Regulatory needs
- **Options**: On-demand or reserved (1-3 years)

### Dedicated Instances
- **Hardware**: Dedicated hardware (shared within account)
- **Placement**: No control over instance placement
- **Cost**: Higher than standard instances
- **Use Cases**: Isolation requirements without physical server access

### Capacity Reservations
- **Purpose**: Reserve capacity in specific AZ
- **Duration**: Any duration (no time commitment)
- **Cost**: On-demand pricing (no discount)
- **Billing**: Charged whether capacity is used or not
- **Use Cases**: Short-term uninterrupted workloads needing specific AZ

### Pricing Comparison Example (m4.large in us-east-1)
- **On-Demand**: $0.10/hour
- **Spot**: ~$0.04/hour (60% savings)
- **1-Year RI (No Upfront)**: ~$0.06/hour
- **1-Year RI (All Upfront)**: ~$0.05/hour
- **3-Year RI (All Upfront)**: ~$0.03/hour

---

## Spot Instances & Spot Fleet

### Spot Instance Mechanics
- **Max Price**: Maximum price you're willing to pay
- **Current Price**: Market price varies by supply/demand
- **Interruption**: 2-minute grace period when price exceeds max
- **Options**: Stop or terminate when interrupted

### Spot Instance Strategies
- **Stop**: Preserve instance state, restart when price drops
- **Terminate**: Lose state, start fresh when needed
- **Spot Block**: Reserve for 1-6 hours without interruption

### Spot Request Types
- **One-Time Request**: Fulfilled once, request disappears
- **Persistent Request**: Automatically maintains target capacity
- **Cancellation**: Must cancel request before terminating instances

### Spot Fleet
- **Purpose**: Ultimate cost optimization
- **Composition**: Mix of spot instances (and optionally on-demand)
- **Target Capacity**: Fleet tries to meet capacity with price constraints
- **Launch Pools**: Multiple instance types, OS, AZ combinations

### Spot Fleet Allocation Strategies
- **Lowest Price**: Launch from pool with lowest price (most popular)
- **Diversified**: Distribute across all pools for availability
- **Capacity Optimized**: Pool with optimal capacity for target
- **Price Capacity Optimized**: Highest capacity, then lowest price

### Spot Fleet Benefits
- **Maximum Savings**: Automatic selection of cheapest pools
- **High Availability**: Diversification reduces interruption risk
- **Flexibility**: Multiple instance types and AZs
- **Automation**: Automatic capacity management

### Spot Instance Pricing
- **Variation**: Prices vary by AZ and time
- **Historical Data**: Available in AWS console
- **Price History**: 3-month graphs for analysis
- **Savings**: Typically 60-90% off on-demand

---

## EC2 Instances Launch Types Hands On

### Spot Request Creation
1. **Navigate**: EC2 → Spot Requests
2. **Pricing History**: Analyze historical spot prices
3. **Create Request**: Configure spot fleet or single instance
4. **Launch Parameters**: AMI, instance type, key pair, security groups
5. **Request Details**: Max price, validity period, interruption behavior

### Spot Fleet Configuration
- **Target Capacity**: Number of instances or vCPUs or memory
- **Instance Types**: Manual selection or attribute-based
- **Allocation Strategy**: Lowest price, diversified, capacity optimized
- **Network Settings**: VPC, AZ selection
- **Interruption Behavior**: Terminate, stop, or hibernate

### Reserved Instance Purchase
1. **Navigate**: EC2 → Reserved Instances
2. **Search**: Find instance type and configuration
3. **Configure**: Term length, payment option, offering type
4. **Add to Cart**: Review before purchase
5. **Order**: Complete purchase (costly operation)

### Savings Plans
1. **Navigate**: EC2 → Savings Plans
2. **Commitment**: Define hourly dollar amount
3. **Term**: 1-year or 3-year commitment
4. **Flexibility**: Instance family, region, scope selection
5. **Purchase**: Activate savings plan

### Dedicated Host Allocation
1. **Navigate**: EC2 → Dedicated Hosts
2. **Allocate**: Configure host settings
3. **Instance Family**: Specify supported instance types
4. **AZ Selection**: Choose availability zone
5. **Auto-Placement**: Control instance placement on host

### Capacity Reservation
1. **Navigate**: EC2 → Capacity Reservations
2. **Configure**: Instance type, quantity, AZ
3. **Duration**: Start/end time settings
4. **Instance Eligibility**: Specify which instances can use reservation
5. **Create**: Reserve capacity (billed regardless of usage)

### Practical Considerations
- **Cost Awareness**: Be careful with purchasing options that incur costs
- **Testing**: Use console simulations before actual purchases
- **Monitoring**: track spot instance interruptions and costs
- **Optimization**: Regularly review and adjust purchasing strategies
