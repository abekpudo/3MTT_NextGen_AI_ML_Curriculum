 Introduction to Cloud Computing and AWS

 1. Introduction
Cloud computing has revolutionized how we build, deploy, and scale applications. This lecture covers fundamental cloud concepts, AWS infrastructure, and practical skills needed to leverage cloud services for machine learning and production workloads.

 2. Why Cloud Computing Matters
- Scalability: Scale resources up or down based on demand
- Cost Efficiency: Pay only for what you use (OpEx vs CapEx)
- Global Reach: Deploy applications worldwide with minimal effort
- Reliability: Built-in redundancy and high availability
- Innovation: Access to cutting-edge services and technologies
- Focus: Concentrate on business logic, not infrastructure management

 3. Cloud Service Models

 3.1. Infrastructure as a Service (IaaS)
```bash
 IaaS provides fundamental computing resources
 Examples: AWS EC2, Google Compute Engine, Azure Virtual Machines

 What you get:
 - Virtual servers (compute)
 - Storage (block, object)
 - Networking (VPC, load balancers)
 - You manage: OS, middleware, applications, data

 IaaS characteristics:
 - Maximum flexibility and control
 - Requires more management overhead
 - Lower level of abstraction
 - Suitable for: Custom applications, legacy migration
```

 3.2. Platform as a Service (PaaS)
```bash
 PaaS provides a platform for developing and deploying applications
 Examples: AWS Elastic Beanstalk, Heroku, Google App Engine

 What you get:
 - Complete development and deployment platform
 - Managed runtime environments
 - Auto-scaling and load balancing
 - You manage: Application code and data

 PaaS characteristics:
 - Reduced operational overhead
 - Faster development cycles
 - Built-in scalability
 - Suitable for: Web applications, APIs, microservices
```

 3.3. Software as a Service (SaaS)
```bash
 SaaS provides ready-to-use software applications
 Examples: Salesforce, Office 365, Gmail

 What you get:
 - Complete application solution
 - Automatic updates and maintenance
 - Multi-tenant architecture
 - You manage: User access and configuration

 SaaS characteristics:
 - Zero infrastructure management
 - Subscription-based pricing
 - Immediate deployment
 - Suitable for: Business applications, collaboration tools
```

 4. AWS Global Infrastructure

 4.1. Regions and Availability Zones
```bash
 AWS Regions are geographic areas where AWS has data centers
 Availability Zones (AZs) are isolated locations within regions

 List all AWS regions
aws ec2 describe-regions --all-regions --output table

 Set default region
aws configure set region us-east-1
export AWS_DEFAULT_REGION=us-west-2

 Common AWS regions:
 us-east-1      (N. Virginia)      - Most services, oldest region
 us-west-2      (Oregon)           - Popular for US West coast
 eu-west-1      (Ireland)          - Popular for Europe
 ap-southeast-1 (Singapore)        - Popular for Asia Pacific

 Availability Zones within a region (example: us-west-2)
 us-west-2a     us-west-2b     us-west-2c
```

 4.2. AWS CLI Setup and Configuration
```bash
 Install AWS CLI
 macOS/Linux:
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

 Verify installation
aws --version

 Configure AWS credentials
aws configure
 AWS Access Key ID: YOUR_ACCESS_KEY
 AWS Secret Access Key: YOUR_SECRET_KEY
 Default region name: us-west-2
 Default output format: json

 Alternative configuration methods
export AWS_ACCESS_KEY_ID=YOUR_ACCESS_KEY
export AWS_SECRET_ACCESS_KEY=YOUR_SECRET_KEY
export AWS_DEFAULT_REGION=us-west-2

 Use AWS profiles
aws configure --profile dev
aws configure --profile prod
aws s3 ls --profile dev
```

 5. Core AWS Services

 5.1. Compute Services
```bash
 Amazon EC2 (Elastic Compute Cloud)
 Virtual servers in the cloud

 List available instance types
aws ec2 describe-instance-types --region us-west-2

 Common instance families:
 t3.micro/t3.small    - General purpose, burstable
 m5.large/m5.xlarge   - General purpose, balanced
 c5.large/c5.xlarge   - Compute optimized
 r5.large/r5.xlarge   - Memory optimized

 Launch EC2 instance (example)
aws ec2 run-instances \
    --image-id ami-0c55b159cbfafe1f0 \
    --instance-type t3.micro \
    --key-name my-key-pair \
    --security-group-ids sg-1234567890abcdef0 \
    --subnet-id subnet-1234567890abcdef0 \
    --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=MyInstance}]'

 List EC2 instances
aws ec2 describe-instances --output table

 Stop/Start/Terminate instances
aws ec2 stop-instances --instance-ids i-1234567890abcdef0
aws ec2 start-instances --instance-ids i-1234567890abcdef0
aws ec2 terminate-instances --instance-ids i-1234567890abcdef0
```

 5.2. Storage Services
```bash
 Amazon S3 (Simple Storage Service)
 Object storage service

 Create S3 bucket
aws s3 mb s3://my-unique-bucket-name-$(date +%s)

 List buckets
aws s3 ls

 Upload files to S3
aws s3 cp local-file.txt s3://my-bucket/
aws s3 sync ./local-folder s3://my-bucket/remote-folder/

 Download files from S3
aws s3 cp s3://my-bucket/file.txt ./local-file.txt
aws s3 sync s3://my-bucket/remote-folder ./local-folder/

 List bucket contents
aws s3 ls s3://my-bucket/
aws s3 ls s3://my-bucket/ --recursive

 Delete objects and bucket
aws s3 rm s3://my-bucket/ --recursive
aws s3 rb s3://my-bucket

 S3 versioning and lifecycle
aws s3api put-bucket-versioning --bucket my-bucket --versioning-configuration Status=Enabled
aws s3api put-bucket-lifecycle-configuration --bucket my-bucket --lifecycle-configuration file://lifecycle.json
```

 5.3. Networking Services
```bash
 Amazon VPC (Virtual Private Cloud)
 Isolated cloud network

 Create VPC
aws ec2 create-vpc --cidr-block 10.0.0.0/16 --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=MyVPC}]'

 Create subnets
aws ec2 create-subnet --vpc-id vpc-1234567890abcdef0 --cidr-block 10.0.1.0/24 --availability-zone us-west-2a
aws ec2 create-subnet --vpc-id vpc-1234567890abcdef0 --cidr-block 10.0.2.0/24 --availability-zone us-west-2b

 Create internet gateway
aws ec2 create-internet-gateway --tag-specifications 'ResourceType=internet-gateway,Tags=[{Key=Name,Value=MyIGW}]'

 Attach internet gateway to VPC
aws ec2 attach-internet-gateway --vpc-id vpc-1234567890abcdef0 --internet-gateway-id igw-1234567890abcdef0

 Create route table
aws ec2 create-route-table --vpc-id vpc-1234567890abcdef0

 Add route to internet gateway
aws ec2 create-route --route-table-id rtb-1234567890abcdef0 --destination-cidr-block 0.0.0.0/0 --gateway-id igw-1234567890abcdef0

 Security groups (firewall rules)
aws ec2 create-security-group --group-name my-sg --description "My security group" --vpc-id vpc-1234567890abcdef0

 Add security group rules
aws ec2 authorize-security-group-ingress --group-id sg-1234567890abcdef0 --protocol tcp --port 22 --cidr 0.0.0.0/0
aws ec2 authorize-security-group-ingress --group-id sg-1234567890abcdef0 --protocol tcp --port 80 --cidr 0.0.0.0/0
```

 5.4. Database Services
```bash
 Amazon RDS (Relational Database Service)
 Managed relational databases

 Create RDS instance (MySQL example)
aws rds create-db-instance \
    --db-instance-identifier mydbinstance \
    --db-instance-class db.t3.micro \
    --engine mysql \
    --master-username admin \
    --master-user-password mypassword123 \
    --allocated-storage 20 \
    --vpc-security-group-ids sg-1234567890abcdef0

 List RDS instances
aws rds describe-db-instances --output table

 Create database snapshot
aws rds create-db-snapshot --db-instance-identifier mydbinstance --db-snapshot-identifier my-snapshot-$(date +%s)

 Amazon DynamoDB (NoSQL database)
 Fully managed NoSQL database

 Create DynamoDB table
aws dynamodb create-table \
    --table-name MyTable \
    --attribute-definitions AttributeName=ID,AttributeType=S \
    --key-schema AttributeName=ID,KeyType=HASH \
    --provisioned-throughput ReadCapacityUnits=5,WriteCapacityUnits=5

 List DynamoDB tables
aws dynamodb list-tables --output table

 Put item in DynamoDB table
aws dynamodb put-item \
    --table-name MyTable \
    --item '{"ID": {"S": "123"}, "Name": {"S": "John Doe"}, "Age": {"N": "30"}}'
```

 6. Identity and Access Management (IAM)

 6.1. IAM Users and Groups
```bash
 Create IAM user
aws iam create-user --user-name john.doe

 Create IAM group
aws iam create-group --group-name developers

 Add user to group
aws iam add-user-to-group --group-name developers --user-name john.doe

 List users and groups
aws iam list-users --output table
aws iam list-groups --output table

 Create IAM policy
aws iam create-policy --policy-name MyCustomPolicy --policy-document file://policy.json

 Attach policy to user/group
aws iam attach-user-policy --user-name john.doe --policy-arn arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess
aws iam attach-group-policy --group-name developers --policy-arn arn:aws:iam::aws:policy/PowerUserAccess
```

 6.2. IAM Roles
```bash
 Create IAM role for EC2 instances
aws iam create-role \
    --role-name EC2Role \
    --assume-role-policy-document file://trust-policy.json

 Attach policy to role
aws iam attach-role-policy \
    --role-name EC2Role \
    --policy-arn arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess

 Create instance profile
aws iam create-instance-profile --instance-profile-name EC2InstanceProfile

 Add role to instance profile
aws iam add-role-to-instance-profile \
    --instance-profile-name EC2InstanceProfile \
    --role-name EC2Role
```

 7. Cost Management and Optimization

 7.1. Cost Monitoring
```bash
 AWS Cost Explorer API
 Enable Cost Explorer
aws ce enable-cost-and-usage-views

 Get cost and usage data
aws ce get-cost-and-usage \
    --time-period Start=$(date -d '30 days ago' +%Y-%m-%d),End=$(date +%Y-%m-%d) \
    --granularity MONTHLY \
    --metrics BlendedCost \
    --group-by Type=DIMENSION,Key=SERVICE

 AWS Budgets
 Create budget
aws budgets create-budget \
    --account-id $(aws sts get-caller-identity --query Account --output text) \
    --budget '{
        "BudgetName": "MonthlyBudget",
        "BudgetType": "COST",
        "TimeUnit": "MONTHLY",
        "BudgetLimit": {
            "Amount": "100",
            "Unit": "USD"
        }
    }'
```

 7.2. Cost Optimization Strategies
```bash
 Use AWS Compute Optimizer
aws compute-optimizer get-recommendation-summaries --service EC2

 Check for unused resources
 Find unattached EBS volumes
aws ec2 describe-volumes --filters Name=status,Values=available

 Find unused Elastic IPs
aws ec2 describe-addresses --filters Name=association-id,Values=null

 Right-size EC2 instances using CloudWatch metrics
aws cloudwatch get-metric-statistics \
    --namespace AWS/EC2 \
    --metric-name CPUUtilization \
    --dimensions Name=InstanceId,Value=i-1234567890abcdef0 \
    --start-time $(date -d '7 days ago' +%Y-%m-%dT%H:%M:%SZ) \
    --end-time $(date +%Y-%m-%dT%H:%M:%SZ) \
    --period 3600 \
    --statistics Average
```

 8. Security Best Practices

 8.1. Network Security
```bash
 Use security groups to control traffic
aws ec2 authorize-security-group-ingress \
    --group-id sg-1234567890abcdef0 \
    --protocol tcp \
    --port 443 \
    --source-group sg-0987654321fedcba0

 Use Network ACLs for subnet-level control
aws ec2 create-network-acl \
    --vpc-id vpc-1234567890abcdef0

 Add Network ACL entries
aws ec2 create-network-acl-entry \
    --network-acl-id acl-1234567890abcdef0 \
    --rule-number 100 \
    --protocol -1 \
    --rule-action allow \
    --egress \
    --cidr-block 0.0.0.0/0
```

 8.2. Data Encryption
```bash
 Enable S3 bucket encryption
aws s3api put-bucket-encryption \
    --bucket my-bucket \
    --server-side-encryption-configuration '{
        "Rules": [{
            "ApplyServerSideEncryptionByDefault": {
                "SSEAlgorithm": "AES256"
            }
        }]
    }'

 Enable EBS volume encryption
aws ec2 create-volume \
    --availability-zone us-west-2a \
    --size 20 \
    --volume-type gp2 \
    --encrypted

 Enable RDS encryption
aws rds create-db-instance \
    --db-instance-identifier encrypted-db \
    --db-instance-class db.t3.micro \
    --engine mysql \
    --master-username admin \
    --master-user-password mypassword123 \
    --allocated-storage 20 \
    --storage-encrypted
```

 9. Monitoring and Logging

 9.1. Amazon CloudWatch
```bash
 Create CloudWatch alarm
aws cloudwatch put-metric-alarm \
    --alarm-name HighCPUUtilization \
    --alarm-description "CPU utilization > 80%" \
    --metric-name CPUUtilization \
    --namespace AWS/EC2 \
    --statistic Average \
    --period 300 \
    --threshold 80 \
    --comparison-operator GreaterThanThreshold \
    --evaluation-periods 2

 List CloudWatch alarms
aws cloudwatch describe-alarms --output table

 Create custom metric
aws cloudwatch put-metric-data \
    --namespace CustomMetrics \
    --metric-data MetricName=ApplicationErrors,Value=10,Unit=Count

 Get metric statistics
aws cloudwatch get-metric-statistics \
    --namespace AWS/EC2 \
    --metric-name CPUUtilization \
    --dimensions Name=InstanceId,Value=i-1234567890abcdef0 \
    --start-time $(date -d '1 hour ago' +%Y-%m-%dT%H:%M:%SZ) \
    --end-time $(date +%Y-%m-%dT%H:%M:%SZ) \
    --period 300 \
    --statistics Average
```

 9.2. AWS CloudTrail
```bash
 Create CloudTrail
aws cloudtrail create-trail \
    --name my-trail \
    --s3-bucket-name my-cloudtrail-bucket \
    --include-global-service-events \
    --is-multi-region-trail

 List trails
aws cloudtrail describe-trails --output table

 Get trail events
aws cloudtrail lookup-events \
    --lookup-attributes AttributeKey=ResourceName,AttributeValue=my-instance
```

 10. Infrastructure as Code

 10.1. AWS CloudFormation
```yaml
 Example CloudFormation template (template.yaml)
AWSTemplateFormatVersion: '2010-09-09'
Description: 'Simple EC2 instance with security group'

Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t3.micro
      ImageId: ami-0c55b159cbfafe1f0
      SecurityGroups:
        - !Ref MySecurityGroup
      Tags:
        - Key: Name
          Value: MyInstance

  MySecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: 'Enable SSH access'
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0

Outputs:
  InstanceId:
    Description: 'Instance ID'
    Value: !Ref MyEC2Instance
    Export:
      Name: !Sub '${AWS::StackName}-InstanceId'
```

```bash
 Deploy CloudFormation stack
aws cloudformation create-stack \
    --stack-name my-stack \
    --template-body file://template.yaml \
    --parameters ParameterKey=InstanceType,ParameterValue=t3.micro

 Update stack
aws cloudformation update-stack \
    --stack-name my-stack \
    --template-body file://template.yaml

 Delete stack
aws cloudformation delete-stack --stack-name my-stack
```

 11. Practical Examples

 11.1. Complete Web Application Setup
```bash
!/bin/bash
 setup-web-app.sh - Complete AWS web application setup

set -euo pipefail

 Configuration
REGION="us-west-2"
VPC_CIDR="10.0.0.0/16"
PUBLIC_SUBNET_CIDR="10.0.1.0/24"
INSTANCE_TYPE="t3.micro"
AMI_ID="ami-0c55b159cbfafe1f0"

 Create VPC
echo "Creating VPC..."
VPC_ID=$(aws ec2 create-vpc --cidr-block $VPC_CIDR --region $REGION --query 'Vpc.VpcId' --output text)
echo "VPC created: $VPC_ID"

 Create public subnet
echo "Creating public subnet..."
SUBNET_ID=$(aws ec2 create-subnet --vpc-id $VPC_ID --cidr-block $PUBLIC_SUBNET_CIDR --availability-zone ${REGION}a --region $REGION --query 'Subnet.SubnetId' --output text)
echo "Subnet created: $SUBNET_ID"

 Create internet gateway
echo "Creating internet gateway..."
IGW_ID=$(aws ec2 create-internet-gateway --region $REGION --query 'InternetGateway.InternetGatewayId' --output text)
aws ec2 attach-internet-gateway --vpc-id $VPC_ID --internet-gateway-id $IGW_ID --region $REGION
echo "Internet gateway created: $IGW_ID"

 Create route table
echo "Creating route table..."
ROUTE_TABLE_ID=$(aws ec2 create-route-table --vpc-id $VPC_ID --region $REGION --query 'RouteTable.RouteTableId' --output text)
aws ec2 create-route --route-table-id $ROUTE_TABLE_ID --destination-cidr-block 0.0.0.0/0 --gateway-id $IGW_ID --region $REGION
aws ec2 associate-route-table --route-table-id $ROUTE_TABLE_ID --subnet-id $SUBNET_ID --region $REGION
echo "Route table created: $ROUTE_TABLE_ID"

 Create security group
echo "Creating security group..."
SG_ID=$(aws ec2 create-security-group --group-name web-app-sg --description "Security group for web application" --vpc-id $VPC_ID --region $REGION --query 'GroupId' --output text)
aws ec2 authorize-security-group-ingress --group-id $SG_ID --protocol tcp --port 22 --cidr 0.0.0.0/0 --region $REGION
aws ec2 authorize-security-group-ingress --group-id $SG_ID --protocol tcp --port 80 --cidr 0.0.0.0/0 --region $REGION
echo "Security group created: $SG_ID"

 Create EC2 instance
echo "Creating EC2 instance..."
INSTANCE_ID=$(aws ec2 run-instances \
    --image-id $AMI_ID \
    --instance-type $INSTANCE_TYPE \
    --key-name my-key-pair \
    --security-group-ids $SG_ID \
    --subnet-id $SUBNET_ID \
    --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=WebAppInstance}]' \
    --region $REGION \
    --query 'Instances[0].InstanceId' \
    --output text)

echo "EC2 instance created: $INSTANCE_ID"

 Wait for instance to be running
echo "Waiting for instance to be running..."
aws ec2 wait instance-running --instance-ids $INSTANCE_ID --region $REGION

 Get public IP
PUBLIC_IP=$(aws ec2 describe-instances --instance-ids $INSTANCE_ID --region $REGION --query 'Reservations[0].Instances[0].PublicIpAddress' --output text)

echo "Setup completed!"
echo "Instance ID: $INSTANCE_ID"
echo "Public IP: $PUBLIC_IP"
echo "SSH: ssh -i my-key-pair.pem ec2-user@$PUBLIC_IP"
echo "HTTP: http://$PUBLIC_IP"
```

 11.2. Backup and Disaster Recovery
```bash
!/bin/bash
 aws-backup.sh - AWS backup and disaster recovery setup

set -euo pipefail

 Configuration
BACKUP_BUCKET="my-backup-bucket-$(date +%s)"
REGION="us-west-2"

 Create S3 bucket for backups
echo "Creating backup bucket..."
aws s3 mb s3://$BACKUP_BUCKET --region $REGION

 Enable versioning
aws s3api put-bucket-versioning --bucket $BACKUP_BUCKET --versioning-configuration Status=Enabled --region $REGION

 Create lifecycle policy for backups
cat > backup-lifecycle.json << EOF
{
  "Rules": [
    {
      "ID": "BackupLifecycle",
      "Status": "Enabled",
      "Transitions": [
        {
          "Days": 30,
          "StorageClass": "STANDARD_IA"
        },
        {
          "Days": 90,
          "StorageClass": "GLACIER"
        },
        {
          "Days": 365,
          "StorageClass": "DEEP_ARCHIVE"
        }
      ]
    }
  ]
}
EOF

aws s3api put-bucket-lifecycle-configuration --bucket $BACKUP_BUCKET --lifecycle-configuration file://backup-lifecycle.json --region $REGION

 Create backup function
backup_ec2_instance() {
    local instance_id="$1"
    local backup_name="backup-$(date +%Y%m%d_%H%M%S)"
    
    echo "Creating backup for instance: $instance_id"
    
     Create AMI
    local ami_id=$(aws ec2 create-image --instance-id $instance_id --name $backup_name --description "Backup of $instance_id" --region $REGION --query 'ImageId' --output text)
    
    echo "AMI created: $ami_id"
    
     Wait for AMI to be available
    aws ec2 wait image-available --image-ids $ami_id --region $REGION
    
     Tag the AMI
    aws ec2 create-tags --resources $ami_id --tags Key=BackupType,Value=Automated Key=Date,Value=$(date +%Y-%m-%d) --region $REGION
    
    echo "Backup completed: $ami_id"
}

 Example usage
 backup_ec2_instance "i-1234567890abcdef0"

echo "Backup infrastructure setup completed!"
echo "Bucket: $BACKUP_BUCKET"
echo "Region: $REGION"
```

 12. Resources and Further Learning

 12.1. Documentation
- [AWS Documentation](https://docs.aws.amazon.com/)
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
- [AWS Cloud Practitioner Essentials](https://aws.amazon.com/training/)

 12.2. Tools and Utilities
- AWS Management Console: Web-based interface
- AWS CLI: Command-line interface
- AWS SDKs: Language-specific libraries
- AWS CloudFormation: Infrastructure as code
- AWS CDK: Infrastructure as code with programming languages

 12.3. Best Practices
- Use the principle of least privilege with IAM
- Enable multi-factor authentication (MFA)
- Use resource tagging for cost allocation
- Implement proper monitoring and alerting
- Design for high availability and fault tolerance
- Regularly review and optimize costs
- Use security groups and Network ACLs properly
- Enable logging and audit trails

---

Next Steps: Practice these commands in the AWS console, set up your AWS CLI, and start building simple infrastructure using both the console and CLI commands.
