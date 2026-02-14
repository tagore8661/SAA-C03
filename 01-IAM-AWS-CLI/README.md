# IAM & AWS CLI

## Course Overview
This section covers AWS Identity and Access Management (IAM) and AWS Command Line Interface (CLI) fundamentals for the AWS Solutions Architect Associate certification.

---

## Table of Contents

1. [IAM Introduction: Users, Groups, Policies](#iam-introduction-users-groups-policies)
2. [IAM Users & Groups - Hands On](#iam-users--groups---hands-on)
3. [AWS Console Simultaneous Sign-in](#aws-console-simultaneous-sign-in)
4. [IAM Policies - Theory](#iam-policies---theory)
5. [IAM Policies - Hands On](#iam-policies---hands-on)
6. [IAM MFA Overview](#iam-mfa-overview)
7. [IAM MFA Hands On](#iam-mfa-hands-on)
8. [AWS Access Methods: Console, CLI & SDK](#aws-access-methods-console-cli--sdk)
9. [AWS CLI Setup - Windows](#aws-cli-setup---windows)
10. [AWS CLI Setup - Mac OS X](#aws-cli-setup---mac-os-x)
11. [AWS CLI Setup - Linux](#aws-cli-setup---linux)
12. [AWS CLI Hands On](#aws-cli-hands-on)
13. [AWS CloudShell](#aws-cloudshell)
14. [IAM Roles for AWS Services](#iam-roles-for-aws-services)
15. [IAM Roles Hands On](#iam-roles-hands-on)
16. [IAM Security Tools](#iam-security-tools)
17. [IAM Security Tools Hands On](#iam-security-tools-hands-on)
18. [IAM Best Practices](#iam-best-practices)
19. [IAM Summary](#iam-summary)

---

## IAM Introduction: Users, Groups, Policies

### Core IAM Concepts
- **IAM (Identity and Access Management)**: Global AWS service for managing users and their access to AWS resources
- **Global Service**: IAM is global - no region selection needed, users available everywhere
- **Root Account**: Default account created when setting up AWS - should only be used for initial setup
- **Users**: Represent individual people within an organization (one user = one physical person)
- **Groups**: Collections of users for easier permission management
  - Groups can only contain users, not other groups
  - Users can belong to multiple groups
  - Users don't have to belong to any group (not best practice)

### Why IAM Matters
- Prevents catastrophic costs from unrestricted access
- Enhances security through controlled permissions
- Enables proper organizational access management

### Policies and Permissions
- **JSON Documents**: Define permissions for users/groups
- **Principle of Least Privilege**: Only grant necessary permissions
- **Policy Structure**:
  - Version (usually 2012-10-17)
  - Statement ID (optional)
  - Effect (Allow/Deny)
  - Principal (who the policy applies to)
  - Action (API calls allowed/denied)
  - Resource (resources the actions apply to)
  - Condition (optional - when statement should be applied)

---

## IAM Users & Groups - Hands On

### Console Navigation
- IAM is a **global service** - no region selection needed
- Create users through IAM console → Users
- Best practice: Create admin users instead of using root account

### User Creation Process
1. Provide username (e.g., Stephane)
2. Enable console access
3. Choose between IAM Identity Center (recommended) or IAM user (exam focus)
4. Set password (auto-generated or custom)
5. Require password change at next sign-in (for other users)
6. Add permissions (directly or via groups)
7. Add optional tags for metadata (e.g., department: engineering)

### Group Management
- Create groups (e.g., admin group)
- Attach policies to groups (e.g., AdministratorAccess)
- Add users to groups
- Users inherit permissions from all groups they belong to

### Account Alias
- Customize sign-in URL (e.g., `aws-tagore8661.signin.aws.amazon.com/console`)
- Makes login URL easier to remember
- Must be unique across AWS

### Login Methods
- **Root User Sign-in**: Uses account ID only
- **IAM User Sign-in**: Uses account ID/alias + username + password
- **Private Browsing**: Enables simultaneous access to multiple accounts
- **Security Warning**: Never lose root account or admin credentials

### Multi-Session Support
- New feature allowing multiple account sessions in same browser
- Can work with different accounts simultaneously without private browsing

---

## AWS Console Simultaneous Sign-in

### Multi-Session Support
- **New Feature**: Multiple account sessions in same browser
- **Benefits**: 
  - Work with different accounts simultaneously
  - No need for private browsing windows
  - Each session shows different account ID
- **Usage**:
  1. Enable multi-session support
  2. Add new session
  3. Sign in with different account ID/alias
  4. Work with multiple accounts side by side

### Practical Example
- Create EBS volume in one account/session
- Same volume not visible in other account/session
- Demonstrates account isolation
- Revolutionary feature for long-term AWS users

---

## IAM Policies - Theory

### Policy Types and Inheritance
- **Managed Policies**: AWS-managed or customer-managed policies attachable to multiple entities
- **Inline Policies**: Policies directly attached to a single user/group/role
- **Policy Inheritance**: Users inherit policies from all groups they belong to

### Example Scenarios
- Charles in Developers and Audit groups inherits policies from both
- David in Operations and Audit groups inherits both sets of permissions
- Fred with no group can have inline policies

### Policy Structure Deep Dive
- **Version**: Policy language version (2012-10-17)
- **Statement ID**: Optional identifier for statements
- **Effect**: Allow or Deny access
- **Principal**: Which accounts/users/roles the policy applies to
- **Action**: List of API calls to be allowed/denied
- **Resource**: List of resources actions apply to
- **Condition**: Optional conditions for statement application

### Exam Focus Areas
- Understanding Effect, Principal, Action, and Resource elements
- Policy inheritance scenarios
- Difference between managed and inline policies

---

## IAM Policies - Hands On

### Permission Testing
1. Start with admin user in admin group
2. Remove user from group → loses all permissions
3. Attempt to access IAM console → "Access Denied" error
4. Add IAMReadOnlyAccess policy directly to user
5. Test permissions: Can view users, cannot create groups
6. Add user back to admin group → regains full permissions

### Policy Management
- **Visual Editor**: GUI-based policy builder
- **JSON Editor**: Direct JSON editing for advanced users
- **Policy Creation**: Select service, actions, and resources
- **Policy Attachment**: To users, groups, or roles

### Policy Examples in JSON Format

#### Example 1: Administrator Access Policy
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "*",
            "Resource": "*"
        }
    ]
}
```

#### Example 2: IAM Read-Only Access Policy
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "iam:Get*",
                "iam:List*",
                "iam:GenerateServiceLastAccessedDetails",
                "iam:GetServiceLastAccessedDetails"
            ],
            "Resource": "*"
        }
    ]
}
```
### Key JSON Policy Elements Explained
- **Version**: Policy language version (always "2012-10-17")
- **Statement**: Array of policy statements
- **Effect**: "Allow" or "Deny" - Deny always overrides Allow
- **Action**: Specific AWS API calls (use "*" for wildcard)
- **Resource**: ARN of resources the policy applies to
- **Condition**: Optional conditions for when policy applies
- **Principal**: Who can assume the role (used in trust policies)

---

## IAM MFA Overview

### Security Defense Mechanisms
1. **Password Policy**: Strong password requirements
2. **Multi-Factor Authentication (MFA)**: Additional security layer

### Password Policy Options
- Minimum password length
- Required character types (uppercase, lowercase, numbers, symbols)
- Allow/prevent IAM users from changing own passwords
- Password expiration requirements (e.g., every 90 days)
- Prevent password reuse

### Multi-Factor Authentication (MFA)
- **Critical Security Feature**: Essential for root account and administrators
- **How MFA Works**: Combines password (something you know) + security device (something you own)
- **Security Benefit**: Even if password is compromised, account remains secure

### MFA Device Options
1. **Virtual MFA Device**:
   - Google Authenticator (one phone at a time)
   - Authy (multiple tokens on single device)
   - Support for unlimited AWS accounts/users

2. **U2F Security Key**:
   - Physical device (e.g., YubiKey by Yubico)
   - Third-party provider
   - Multiple root/IAM users per key
   - Easy to carry on keychain

3. **Hardware Key Fob MFA Device**:
   - Physical device (e.g., Gemalto)
   - Third-party provider

4. **Hardware TOTP Token**:
   - For AWS GovCloud only
   - Provided by SurePassID
   - Third-party provider

### MFA Benefits
- Protects against password theft/hacking
- Requires physical device for access
- Significantly enhances account security
- Recommended for all users, especially administrators

---

## IAM MFA Hands On

### Password Policy Configuration
1. Navigate to IAM → Account settings → Password policy
2. Choose IAM default or customize:
   - Set minimum password length
   - Require uppercase, lowercase, numbers, non-alphanumeric characters
   - Enable password expiration (e.g., 90 days)
   - Allow users to change own passwords
   - Prevent password reuse

### MFA Setup for Root Account
1. Log in with root user
2. Go to Security credentials
3. Assign MFA device:
   - Choose device type (Authenticator app, Security key, Hardware TOTP token)
   - For virtual MFA: Use compatible app (Google Authenticator, Authy)
   - Scan QR code with mobile app
   - Enter two consecutive MFA codes
   - Complete setup

### MFA Usage
- After password entry, prompted for MFA code
- Code changes every 30 seconds
- Can manage up to 8 MFA devices per account
- Can remove MFA devices if needed

### Security Warning
- Risk of account lockout if MFA device lost
- Keep MFA device secure and accessible
- Consider practicing with non-critical accounts first

---

## AWS Access Methods: Console, CLI & SDK

### Three Access Methods
1. **Management Console**:
   - Web interface
   - Protected by username/password + optional MFA
   - User-friendly for beginners

2. **AWS CLI (Command Line Interface)**:
   - Terminal-based interaction
   - Protected by access keys
   - Enables scripting and automation
   - Direct access to AWS APIs

3. **AWS SDK (Software Development Kit)**:
   - Language-specific libraries
   - For application integration
   - Same access keys as CLI
   - Embedded within application code

### Access Keys
- **Access Key ID**: Like a username
- **Secret Access Key**: Like a password
- **Security**: Never share access keys - each user generates their own
- **Generation**: Created through Management console
- **Download**: Only available at creation time
- **Responsibility**: Users responsible for their own access keys

### CLI vs SDK Comparison
- **CLI**: Command-line tool for direct interaction
- **SDK**: Libraries for programmatic access within applications
- **Example**: AWS CLI is built on AWS SDK for Python (Boto3)

### Supported Languages (SDK)
- JavaScript, Python, PHP, .NET, Ruby, Java, Go, Node.js, C++
- Mobile SDK: Android, iOS
- IoT SDK: Internet of Things devices

---

## AWS CLI Setup - Windows

### Installation Process
1. Download AWS CLI version 2 MSI installer
2. Run installer:
   - Click Next → Accept license → Install
   - Allow any security prompts
   - Click Finish when complete
3. Verify installation:
   - Open Command Prompt
   - Run `aws --version`
   - Should return version starting with 2

### Upgrade Process
- Re-download MSI installer
- Re-run installation
- Automatically upgrades to latest version

### System Requirements
- Windows 64-bit
- Administrator privileges for installation

---

## AWS CLI Setup - Mac OS X

### Installation Process
1. Download PKG file for AWS CLI version 2 on macOS
2. Run graphical installer:
   - Click Continue → Agree → Install
   - Install for all users on computer
   - Wait for installation completion
   - Move installer to trash
3. Verify installation:
   - Open Terminal (or iTerm)
   - Run `aws --version`
   - Should return AWS CLI 2.x.x

### Troubleshooting
- Refer to AWS installation guide for issues
- Ensure proper PATH configuration

---

## AWS CLI Setup - Linux

### Installation Process
1. Download ZIP file:
   ```bash
   curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
   ```
2. Unzip installer:
   ```bash
   unzip awscliv2.zip
   ```
3. Run installer as root:
   ```bash
   sudo ./aws/install
   ```
4. Verify installation:
   ```bash
   aws --version
   # or
   /usr/local/bin/aws --version
   ```

### Post-Installation
- AWS CLI available for use
- Supports all AWS services
- Ready for configuration with access keys

---

## AWS CLI Hands On

### Access Key Creation
1. Navigate to IAM user → Security credentials
2. Create access key:
   - Choose use case (CLI, local code, etc.)
   - Note AWS recommendations (CloudShell, IAM Identity Center)
   - Acknowledge recommendations
   - Create access key
3. **Important**: Download CSV file immediately - only available once

### CLI Configuration
```bash
aws configure
# Enter Access Key ID
# Enter Secret Access Key
# Enter default region name (e.g., ap-south-1)
# Enter default output format (json, text, table)
```

### CLI Usage Examples
```bash
# List IAM users
aws iam list-users

# List S3 buckets
aws s3 ls

# Describe EC2 instances
aws ec2 describe-instances
```

### Permission Testing
1. Remove user from admin group
2. Try CLI command → Access denied
3. CLI permissions match console permissions
4. Add user back to group → permissions restored

### Key Takeaways
- CLI and console provide similar information
- Permissions are consistent across access methods
- Access keys are as sensitive as passwords
- Always add users back to appropriate groups after testing

---

## AWS CloudShell

### Overview
- **Browser-based terminal** environment
- **Free to use** AWS service
- **Pre-installed AWS CLI** and tools
- **Alternative to local terminal setup**

### Features
- Automatic credential usage (no configuration needed)
- File persistence across sessions
- Upload/download capabilities
- Multiple tabs and splits
- Customizable appearance (font size, theme)

### Region Availability
- **Not available in all regions**
- Check AWS documentation for supported regions
- Choose supported region for hands-on practice

### Usage Benefits
- No local installation required
- Immediate access to AWS CLI
- Default region matches current console region
- File storage persists between sessions

### Practical Features
```bash
# Check AWS CLI version
aws --version

# Create persistent file
echo "test" > demo.txt

# Upload/download files through web interface
```

### When to Use
- When available in your region
- For quick AWS CLI access
- When local terminal setup is problematic
- For file management in cloud environment

---

## IAM Roles for AWS Services

### Purpose and Concept
- **Grant permissions to AWS services** (not humans)
- Services need permissions to perform actions on your behalf
- Roles are like users but intended for AWS services

### Common Use Cases
- **EC2 Instance Roles**: Allow instances to access other AWS services
- **Lambda Function Roles**: Grant permissions to Lambda functions
- **CloudFormation Roles**: Enable CloudFormation to manage resources

### How Roles Work
1. Create IAM role with specific permissions
2. Associate role with AWS service (e.g., EC2 instance)
3. Service assumes role when needed
4. Permissions are validated and actions performed

### Role Benefits
- No need to store credentials on instances
- Automatic credential rotation
- Centralized permission management
- Enhanced security compared to access keys

---

## IAM Roles Hands On

### Role Creation Process
1. Navigate to IAM → Roles
2. Create role:
   - Select "AWS service" as trusted entity
   - Choose service (e.g., EC2)
   - Select use case (e.g., EC2)
3. Attach permissions:
   - Choose appropriate policies (e.g., IAMReadOnlyAccess)
4. Configure role details:
   - Enter role name (e.g., DemoRoleForEC2)
   - Review trusted entities and permissions
5. Create role

### Role Configuration
- **Trust Relationship**: Defines which service can assume the role
- **Permissions Policies**: Define what the role can do
- **Role Usage**: Will be utilized in subsequent sections (e.g., EC2)

### Verification
- Review created role in role list
- Verify attached permissions
- Check trust relationship configuration

### Future Usage
- Role will be used when launching EC2 instances
- Demonstrates practical application of IAM roles
- Foundation for service-to-service communication

---

## IAM Security Tools

### Available Tools
1. **IAM Credentials Report**: Account-level security analysis
2. **IAM Access Advisor**: User-level permission usage analysis

### IAM Credentials Report
- **Scope**: Entire account
- **Content**: All users and their credential status
- **Information Included**:
  - User creation date
  - Password enabled status
  - Password last used/changed dates
  - MFA activation status
  - Access key creation/rotation/usage dates
  - Certificate information

### IAM Access Advisor
- **Scope**: Individual user level
- **Purpose**: Show service permissions and last access times
- **Benefits**:
  - Identify unused permissions
  - Support principle of least privilege
  - Help reduce excessive permissions
  - Optimize user access patterns

### Security Benefits
- Identify inactive users
- Detect missing MFA configuration
- Find users with outdated credentials
- Support security audits and compliance

---

## IAM Security Tools Hands On

### Credentials Report Generation
1. Navigate to IAM → Credential report
2. Download credential report (CSV format)
3. Analyze report contents:
   - Root account and IAM user information
   - Password status and usage
   - MFA activation status
   - Access key details

### Access Advisor Usage
1. Go to specific user (e.g., Tagore)
2. Click on Access Advisor tab
3. Review accessed services:
   - Organizations, Health, IAM, EC2, Resource Explorer
   - Last access timestamps
   - Service access patterns

### Practical Applications
- Identify users needing MFA activation
- Find rarely used permissions for removal
- Support security audit requirements
- Implement principle of least privilege

### Report Interpretation
- Compare access patterns with job requirements
- Identify security gaps and improvements
- Plan permission optimizations

---

## IAM Best Practices

### Account Management
1. **Root Account Usage**:
   - Only for initial AWS account setup
   - Never for daily operations
   - Never share root credentials

2. **User Management**:
   - One AWS user = one physical user
   - Never share credentials between users
   - Create separate users for each person

### Permission Management
3. **Group-Based Permissions**:
   - Assign users to groups
   - Attach permissions to groups
   - Manage security at group level

4. **Strong Password Policy**:
   - Minimum length requirements
   - Character complexity requirements
   - Regular password rotation

### Security Enhancements
5. **Multi-Factor Authentication**:
   - Enforce MFA for all users
   - Especially critical for administrators
   - Protect against compromised passwords

6. **Role-Based Access**:
   - Use roles for AWS services
   - Include EC2 instances and other services
   - Avoid embedding credentials in applications

### Programmatic Access
7. **Access Key Management**:
   - Generate access keys for CLI/SDK usage
   - Treat access keys like passwords
   - Keep keys private and secure

### Monitoring and Maintenance
8. **Regular Auditing**:
   - Use IAM credentials reports
   - Leverage IAM Access Advisor
   - Review and remove unused permissions

### Critical Security Rules
9. **Never Share Credentials**:
   - IAM users and access keys are private
   - Each user generates their own keys
   - Sharing creates security risks

---

## IAM Summary

### Core Components Covered
- **IAM Users**: Mapped to physical users with console passwords
- **IAM Groups**: Collections of users for permission management
- **IAM Policies**: JSON documents defining permissions
- **IAM Roles**: Identities for AWS services (EC2, Lambda, etc.)

### Security Features
- **Multi-Factor Authentication (MFA)**: Enhanced account protection
- **Password Policies**: Strong password requirements
- **Principle of Least Privilege**: Minimum necessary permissions

### Access Methods
- **Management Console**: Web-based interface
- **AWS CLI**: Command-line interaction
- **AWS SDK**: Programmatic access for applications

### Credential Management
- **Access Keys**: For CLI and SDK access
- **Security Tools**: Credentials reports and Access Advisor
- **Auditing**: Regular permission review and optimization

### Key Takeaways
- IAM is fundamental to AWS security
- Proper IAM setup prevents costly mistakes
- Regular auditing maintains security posture
- Multiple access methods provide flexibility
- Roles enable secure service-to-service communication

