 AWS EC2 and Networking: Building Cloud Infrastructure

 1. Introduction
Amazon EC2 and networking form the foundation of cloud infrastructure. This lecture covers virtual private clouds, subnets, security groups, routing, and load balancing to build secure, scalable, and highly available applications.

 2. Why AWS Networking Matters
- Security: Isolate resources and control traffic flow
- Scalability: Design for growth and high availability
- Performance: Optimize network architecture for application needs
- Compliance: Meet regulatory requirements for network isolation
- Cost: Optimize network costs through proper design

 3. Amazon VPC (Virtual Private Cloud)

 3.1. VPC Fundamentals
```bash
 VPC is a logically isolated network in the AWS cloud
 Each VPC spans all Availability Zones in a region

 Create a VPC
aws ec2 create-vpc \
    --cidr-block 10.0.0.0/16 \
    --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=ProductionVPC}]' \
    --query 'Vpc.VpcId' \
    --output text

 List VPCs
aws ec2 describe-vpcs --output table

 VPC CIDR planning considerations:
 - /16 (65,536 IPs) for large organizations
 - /20 (4,096 IPs) for medium applications
 - /24 (256 IPs) for small applications

 View VPC details
aws ec2 describe-vpcs --vpc-ids vpc-1234567890abcdef0 --output json
```

 3.2. Subnet Design
```bash
 Subnets are subdivisions of VPC IP address ranges
 Public subnets: Internet-accessible (route to Internet Gateway)
 Private subnets: Not directly internet-accessible (route to NAT Gateway)

 Create public subnet
aws ec2 create-subnet \
    --vpc-id vpc-1234567890abcdef0 \
    --cidr-block 10.0.1.0/24 \
    --availability-zone us-west-2a \
    --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=PublicSubnet1}]' \
    --query 'Subnet.SubnetId' \
    --output text

 Create private subnet
aws ec2 create-subnet \
    --vpc-id vpc-1234567890abcdef0 \
    --cidr-block 10.0.2.0/24 \
    --availability-zone us-west-2a \
    --tag-specifications 'ResourceType=subnet,Tags=[{Key=Name,Value=PrivateSubnet1}]' \
    --query 'Subnet.SubnetId' \
    --output text

 List subnets
aws ec2 describe-subnets --filters Name=vpc-id,Values=vpc-1234567890abcdef0 --output table

 Subnet sizing strategy:
 - /24 (256 IPs) for most use cases
 - Reserve 5 IPs per subnet (AWS reserves first 4 + last 1)
 - Plan for growth: use /23 (512 IPs) for large subnets
```

 3.3. Internet Gateway and NAT
```bash
 Internet Gateway enables internet access for VPC
 NAT Gateway enables private subnets to access internet

 Create Internet Gateway
aws ec2 create-internet-gateway \
    --tag-specifications 'ResourceType=internet-gateway,Tags=[{Key=Name,Value=MainIGW}]' \
    --query 'InternetGateway.InternetGatewayId' \
    --output text

 Attach Internet Gateway to VPC
aws ec2 attach-internet-gateway \
    --vpc-id vpc-1234567890abcdef0 \
    --internet-gateway-id igw-1234567890abcdef0

 Create Elastic IP for NAT Gateway
aws ec2 allocate-address \
    --domain vpc \
    --query 'AllocationId' \
    --output text

 Create NAT Gateway (requires Elastic IP)
aws ec2 create-nat-gateway \
    --subnet-id subnet-1234567890abcdef0 \
    --allocation-id eipalloc-1234567890abcdef0 \
    --tag-specifications 'ResourceType=natgateway,Tags=[{Key=Name,Value=MainNAT}]' \
    --query 'NatGateway.NatGatewayId' \
    --output text

 Wait for NAT Gateway to be available
aws ec2 wait nat-gateway-available --nat-gateway-ids nat-1234567890abcdef0
```

 4. Routing and Route Tables

 4.1. Route Table Management
```bash
 Route tables determine where network traffic is directed
 Each subnet must be associated with a route table

 Create route table for public subnets
aws ec2 create-route-table \
    --vpc-id vpc-1234567890abcdef0 \
    --tag-specifications 'ResourceType=route-table,Tags=[{Key=Name,Value=PublicRouteTable}]' \
    --query 'RouteTable.RouteTableId' \
    --output text

 Add route to Internet Gateway for public subnets
aws ec2 create-route \
    --route-table-id rtb-1234567890abcdef0 \
    --destination-cidr-block 0.0.0.0/0 \
    --gateway-id igw-1234567890abcdef0

 Create route table for private subnets
aws ec2 create-route-table \
    --vpc-id vpc-1234567890abcdef0 \
    --tag-specifications 'ResourceType=route-table,Tags=[{Key=Name,Value=PrivateRouteTable}]' \
    --query 'RouteTable.RouteTableId' \
    --output text

 Add route to NAT Gateway for private subnets
aws ec2 create-route \
    --route-table-id rtb-0987654321fedcba0 \
    --destination-cidr-block 0.0.0.0/0 \
    --nat-gateway-id nat-1234567890abcdef0

 Associate route tables with subnets
aws ec2 associate-route-table \
    --route-table-id rtb-1234567890abcdef0 \
    --subnet-id subnet-1234567890abcdef0

aws ec2 associate-route-table \
    --route-table-id rtb-0987654321fedcba0 \
    --subnet-id subnet-0987654321fedcba0

 List route tables
aws ec2 describe-route-tables --output table

 View routes in a route table
aws ec2 describe-route-tables --route-table-ids rtb-1234567890abcdef0 --output json
```

 5. Security Groups and Network ACLs

 5.1. Security Groups
```bash
 Security groups are stateful firewalls for EC2 instances
 Rules are evaluated before traffic reaches the instance

 Create security group for web servers
aws ec2 create-security-group \
    --group-name web-servers \
    --description "Security group for web servers" \
    --vpc-id vpc-1234567890abcdef0 \
    --tag-specifications 'ResourceType=security-group,Tags=[{Key=Name,Value=WebServers}]' \
    --query 'GroupId' \
    --output text

 Add inbound rules for web server
aws ec2 authorize-security-group-ingress \
    --group-id sg-1234567890abcdef0 \
    --protocol tcp \
    --port 80 \
    --cidr 0.0.0.0/0 \
    --description "HTTP access"

aws ec2 authorize-security-group-ingress \
    --group-id sg-1234567890abcdef0 \
    --protocol tcp \
    --port 443 \
    --cidr 0.0.0.0/0 \
    --description "HTTPS access"

aws ec2 authorize-security-group-ingress \
    --group-id sg-1234567890abcdef0 \
    --protocol tcp \
    --port 22 \
    --cidr 203.0.113.0/24 \
    --description "SSH from office network"

 Create security group for database servers
aws ec2 create-security-group \
    --group-name database-servers \
    --description "Security group for database servers" \
    --vpc-id vpc-1234567890abcdef0 \
    --query 'GroupId' \
    --output text

 Add inbound rule for database (only from web servers)
aws ec2 authorize-security-group-ingress \
    --group-id sg-0987654321fedcba0 \
    --protocol tcp \
    --port 3306 \
    --source-group sg-1234567890abcdef0 \
    --description "MySQL access from web servers"

 List security groups
aws ec2 describe-security-groups --output table

 View security group rules
aws ec2 describe-security-groups --group-ids sg-1234567890abcdef0 --output json
```

 5.2. Network ACLs
```bash
 Network ACLs are stateless firewalls for subnets
 Rules are evaluated for both inbound and outbound traffic

 Create Network ACL
aws ec2 create-network-acl \
    --vpc-id vpc-1234567890abcdef0 \
    --tag-specifications 'ResourceType=network-acl,Tags=[{Key=Name,Value=PublicNACL}]' \
    --query 'NetworkAcl.NetworkAclId' \
    --output text

 Add inbound rules (allow HTTP, HTTPS, SSH)
aws ec2 create-network-acl-entry \
    --network-acl-id acl-1234567890abcdef0 \
    --rule-number 100 \
    --protocol tcp \
    --rule-action allow \
    --egress \
    --cidr-block 0.0.0.0/0 \
    --port-range From=80,To=80

aws ec2 create-network-acl-entry \
    --network-acl-id acl-1234567890abcdef0 \
    --rule-number 110 \
    --protocol tcp \
    --rule-action allow \
    --egress \
    --cidr-block 0.0.0.0/0 \
    --port-range From=443,To=443

aws ec2 create-network-acl-entry \
    --network-acl-id acl-1234567890abcdef0 \
    --rule-number 120 \
    --protocol tcp \
    --rule-action allow \
    --egress \
    --cidr-block 0.0.0.0/0 \
    --port-range From=22,To=22

 Associate Network ACL with subnet
aws ec2 associate-network-acl-subnet \
    --network-acl-id acl-1234567890abcdef0 \
    --subnet-id subnet-1234567890abcdef0

 List Network ACLs
aws ec2 describe-network-acls --output table
```

 6. EC2 Instance Management

 6.1. Launching EC2 Instances
```bash
 Find available AMIs
aws ec2 describe-images \
    --owners amazon \
    --filters Name=name,Values=amzn2-ami-hvm--x86_64-gp2 \
    --query 'Images | sort_by(@, &CreationDate) | [-1].ImageId' \
    --output text

 Launch EC2 instance in public subnet
aws ec2 run-instances \
    --image-id ami-0c55b159cbfafe1f0 \
    --instance-type t3.micro \
    --key-name my-key-pair \
    --security-group-ids sg-1234567890abcdef0 \
    --subnet-id subnet-1234567890abcdef0 \
    --associate-public-ip-address \
    --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=WebServer1},{Key=Environment,Value=Production}]' \
    --user-data file://user-data.sh \
    --query 'Instances[0].InstanceId' \
    --output text

 User data script (user-data.sh)
!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Hello from $(hostname -f)</h1>" > /var/www/html/index.html

 Launch EC2 instance in private subnet
aws ec2 run-instances \
    --image-id ami-0c55b159cbfafe1f0 \
    --instance-type t3.micro \
    --key-name my-key-pair \
    --security-group-ids sg-0987654321fedcba0 \
    --subnet-id subnet-0987654321fedcba0 \
    --iam-instance-profile Name=EC2S3Access \
    --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=Database1},{Key=Environment,Value=Production}]' \
    --query 'Instances[0].InstanceId' \
    --output text
```

 6.2. Instance Lifecycle Management
```bash
 List running instances
aws ec2 describe-instances --filters Name=instance-state-name,Values=running --output table

 Get instance details
aws ec2 describe-instances --instance-ids i-1234567890abcdef0 --output json

 Stop instance
aws ec2 stop-instances --instance-ids i-1234567890abcdef0

 Start instance
aws ec2 start-instances --instance-ids i-1234567890abcdef0

 Reboot instance
aws ec2 reboot-instances --instance-ids i-1234567890abcdef0

 Terminate instance
aws ec2 terminate-instances --instance-ids i-1234567890abcdef0

 Wait for instance to be running
aws ec2 wait instance-running --instance-ids i-1234567890abcdef0

 Wait for instance to be stopped
aws ec2 wait instance-stopped --instance-ids i-1234567890abcdef0
```

 7. Elastic IPs and Load Balancing

 7.1. Elastic IP Addresses
```bash
 Allocate Elastic IP
aws ec2 allocate-address \
    --domain vpc \
    --tag-specifications 'ResourceType=elastic-ip,Tags=[{Key=Name,Value=WebServerEIP}]' \
    --query 'AllocationId' \
    --output text

 Associate Elastic IP with instance
aws ec2 associate-address \
    --allocation-id eipalloc-1234567890abcdef0 \
    --instance-id i-1234567890abcdef0

 List Elastic IPs
aws ec2 describe-addresses --output table

 Disassociate Elastic IP
aws ec2 disassociate-address --association-id eipassoc-1234567890abcdef0

 Release Elastic IP
aws ec2 release-address --allocation-id eipalloc-1234567890abcdef0
```

 7.2. Application Load Balancer
```bash
 Create security group for load balancer
aws ec2 create-security-group \
    --group-name alb-sg \
    --description "Security group for Application Load Balancer" \
    --vpc-id vpc-1234567890abcdef0 \
    --query 'GroupId' \
    --output text

 Add inbound rules for ALB
aws ec2 authorize-security-group-ingress \
    --group-id sg-alb1234567890abcdef \
    --protocol tcp \
    --port 80 \
    --cidr 0.0.0.0/0

aws ec2 authorize-security-group-ingress \
    --group-id sg-alb1234567890abcdef \
    --protocol tcp \
    --port 443 \
    --cidr 0.0.0.0/0

 Create target group
aws elbv2 create-target-group \
    --name web-targets \
    --protocol HTTP \
    --port 80 \
    --vpc-id vpc-1234567890abcdef0 \
    --target-type instance \
    --health-check-protocol HTTP \
    --health-check-path / \
    --health-check-interval-seconds 30 \
    --health-check-timeout-seconds 5 \
    --healthy-threshold-count 2 \
    --unhealthy-threshold-count 2 \
    --matcher HttpCode=200

 Register instances with target group
aws elbv2 register-targets \
    --target-group-arn arn:aws:elasticloadbalancing:us-west-2:123456789012:targetgroup/web-targets/abcdef1234567890 \
    --targets Id=i-1234567890abcdef0

 Create Application Load Balancer
aws elbv2 create-load-balancer \
    --name my-app-load-balancer \
    --subnets subnet-1234567890abcdef0 subnet-0987654321fedcba0 \
    --security-groups sg-alb1234567890abcdef \
    --scheme internet-facing \
    --type application \
    --ip-address-type ipv4

 Create listener
aws elbv2 create-listener \
    --load-balancer-arn arn:aws:elasticloadbalancing:us-west-2:123456789012:loadbalancer/app/my-app-load-balancer/abcdef1234567890 \
    --protocol HTTP \
    --port 80 \
    --default-actions Type=forward,TargetGroupArn=arn:aws:elasticloadbalancing:us-west-2:123456789012:targetgroup/web-targets/abcdef1234567890

 List load balancers
aws elbv2 describe-load-balancers --output table

 Get load balancer DNS name
aws elbv2 describe-load-balancers \
    --names my-app-load-balancer \
    --query 'LoadBalancers[0].DNSName' \
    --output text
```

 8. Auto Scaling Groups

 8.1. Launch Templates
```bash
 Create launch template for web servers
aws ec2 create-launch-template \
    --launch-template-name web-server-template \
    --launch-template-data file://launch-template.json

 Launch template JSON (launch-template.json)
{
  "ImageId": "ami-0c55b159cbfafe1f0",
  "InstanceType": "t3.micro",
  "KeyName": "my-key-pair",
  "SecurityGroupIds": ["sg-1234567890abcdef0"],
  "SubnetId": "subnet-1234567890abcdef0",
  "UserData": "IyEvYmluL2Jhc2gKeXVtIHVwZGF0ZSAteQp5dW0gaW5zdGFsbCAteSBodHRwZAoKc3lzdGVtY3RsIHN0YXJ0IGh0dHAKc3lzdGVtY3RsIGVuYWJsZSBodHRwCmVjaG8gIjxoMT5IZWxsbyBmcm9tICQoZGF0ZSkgPC9oMT4iID4gL3ZhciL3d3dy9odG1sL2luZGV4Lmh0bWw=",
  "TagSpecifications": [
    {
      "ResourceType": "instance",
      "Tags": [
        {
          "Key": "Name",
          "Value": "WebServer"
        },
        {
          "Key": "Environment",
          "Value": "Production"
        }
      ]
    }
  ]
}

 List launch templates
aws ec2 describe-launch-templates --output table
```

 8.2. Auto Scaling Group Configuration
```bash
 Create Auto Scaling Group
aws autoscaling create-auto-scaling-group \
    --auto-scaling-group-name web-server-asg \
    --launch-template-name web-server-template \
    --vpc-zone-identifier subnet-1234567890abcdef0,subnet-0987654321fedcba0 \
    --min-size 2 \
    --max-size 10 \
    --desired-capacity 2 \
    --target-group-arns arn:aws:elasticloadbalancing:us-west-2:123456789012:targetgroup/web-targets/abcdef1234567890 \
    --health-check-type ELB \
    --health-check-grace-period 300

 Create scaling policy
aws autoscaling put-scaling-policy \
    --auto-scaling-group-name web-server-asg \
    --policy-name web-scale-out \
    --scaling-type SimpleScaling \
    --adjustment-type ChangeInCapacity \
    --scaling-adjustment 1 \
    --cooldown 300

aws autoscaling put-scaling-policy \
    --auto-scaling-group-name web-server-asg \
    --policy-name web-scale-in \
    --scaling-type SimpleScaling \
    --adjustment-type ChangeInCapacity \
    --scaling-adjustment -1 \
    --cooldown 300

 Create CloudWatch alarms for auto scaling
aws cloudwatch put-metric-alarm \
    --alarm-name WebServerScaleOut \
    --alarm-description "Scale out when CPU > 70%" \
    --metric-name CPUUtilization \
    --namespace AWS/EC2 \
    --statistic Average \
    --period 300 \
    --threshold 70 \
    --comparison-operator GreaterThanThreshold \
    --dimensions Name=AutoScalingGroupName,Value=web-server-asg \
    --evaluation-periods 2 \
    --alarm-actions arn:aws:autoscaling:us-west-2:123456789012:scalingPolicy:web-scale-out:abcdef1234567890

aws cloudwatch put-metric-alarm \
    --alarm-name WebServerScaleIn \
    --alarm-description "Scale in when CPU < 30%" \
    --metric-name CPUUtilization \
    --namespace AWS/EC2 \
    --statistic Average \
    --period 300 \
    --threshold 30 \
    --comparison-operator LessThanThreshold \
    --dimensions Name=AutoScalingGroupName,Value=web-server-asg \
    --evaluation-periods 2 \
    --alarm-actions arn:aws:autoscaling:us-west-2:123456789012:scalingPolicy:web-scale-in:abcdef1234567890

 List Auto Scaling Groups
aws autoscaling describe-auto-scaling-groups --output table

 Describe Auto Scaling Group activities
aws autoscaling describe-scaling-activities --auto-scaling-group-name web-server-asg --output table
```

 9. Monitoring and Troubleshooting

 9.1. Network Connectivity Testing
```bash
 Test connectivity between instances
 SSH to instance and test network connectivity
ssh -i my-key-pair.pem ec2-user@public-ip

 On the instance, test connectivity
ping 8.8.8.8
nslookup google.com
telnet private-ip 3306

 Check security group rules
aws ec2 describe-security-groups --group-ids sg-1234567890abcdef0 --output json

 Check route tables
aws ec2 describe-route-tables --route-table-ids rtb-1234567890abcdef0 --output json

 Check Network ACLs
aws ec2 describe-network-acls --network-acl-ids acl-1234567890abcdef0 --output json
```

 9.2. VPC Flow Logs
```bash
 Enable VPC Flow Logs
aws ec2 create-flow-logs \
    --resource-type VPC \
    --resource-ids vpc-1234567890abcdef0 \
    --traffic-type ALL \
    --log-destination-type cloud-watch-logs \
    --log-group-name VPCFlowLogs \
    --deliver-logs-permission-arn arn:aws:iam::123456789012:role/FlowLogsRole

 List flow logs
aws ec2 describe-flow-logs --output table

 Query flow logs in CloudWatch
aws logs get-log-events \
    --log-group-name VPCFlowLogs \
    --log-stream-name vpc-1234567890abcdef0 \
    --limit 100
```

 10. Complete Architecture Example

 10.1. Three-Tier Web Application
```bash
!/bin/bash
 three-tier-app.sh - Complete three-tier application setup

set -euo pipefail

 Configuration
REGION="us-west-2"
VPC_CIDR="10.0.0.0/16"
PUBLIC_SUBNET_1="10.0.1.0/24"
PUBLIC_SUBNET_2="10.0.2.0/24"
PRIVATE_SUBNET_1="10.0.11.0/24"
PRIVATE_SUBNET_2="10.0.12.0/24"

echo "Setting up three-tier application architecture..."

 Create VPC
VPC_ID=$(aws ec2 create-vpc --cidr-block $VPC_CIDR --region $REGION --query 'Vpc.VpcId' --output text)
echo "VPC created: $VPC_ID"

 Create subnets
PUBLIC_SUBNET_1_ID=$(aws ec2 create-subnet --vpc-id $VPC_ID --cidr-block $PUBLIC_SUBNET_1 --availability-zone ${REGION}a --region $REGION --query 'Subnet.SubnetId' --output text)
PUBLIC_SUBNET_2_ID=$(aws ec2 create-subnet --vpc-id $VPC_ID --cidr-block $PUBLIC_SUBNET_2 --availability-zone ${REGION}b --region $REGION --query 'Subnet.SubnetId' --output text)
PRIVATE_SUBNET_1_ID=$(aws ec2 create-subnet --vpc-id $VPC_ID --cidr-block $PRIVATE_SUBNET_1 --availability-zone ${REGION}a --region $REGION --query 'Subnet.SubnetId' --output text)
PRIVATE_SUBNET_2_ID=$(aws ec2 create-subnet --vpc-id $VPC_ID --cidr-block $PRIVATE_SUBNET_2 --availability-zone ${REGION}b --region $REGION --query 'Subnet.SubnetId' --output text)

echo "Subnets created"

 Create Internet Gateway
IGW_ID=$(aws ec2 create-internet-gateway --region $REGION --query 'InternetGateway.InternetGatewayId' --output text)
aws ec2 attach-internet-gateway --vpc-id $VPC_ID --internet-gateway-id $IGW_ID --region $REGION

 Create NAT Gateway
EIP_ID=$(aws ec2 allocate-address --domain vpc --region $REGION --query 'AllocationId' --output text)
NAT_ID=$(aws ec2 create-nat-gateway --subnet-id $PUBLIC_SUBNET_1_ID --allocation-id $EIP_ID --region $REGION --query 'NatGateway.NatGatewayId' --output text)
aws ec2 wait nat-gateway-available --nat-gateway-ids $NAT_ID --region $REGION

echo "Gateways created"

 Create route tables
PUBLIC_RT_ID=$(aws ec2 create-route-table --vpc-id $VPC_ID --region $REGION --query 'RouteTable.RouteTableId' --output text)
PRIVATE_RT_ID=$(aws ec2 create-route-table --vpc-id $VPC_ID --region $REGION --query 'RouteTable.RouteTableId' --output text)

 Add routes
aws ec2 create-route --route-table-id $PUBLIC_RT_ID --destination-cidr-block 0.0.0.0/0 --gateway-id $IGW_ID --region $REGION
aws ec2 create-route --route-table-id $PRIVATE_RT_ID --destination-cidr-block 0.0.0.0/0 --nat-gateway-id $NAT_ID --region $REGION

 Associate route tables
aws ec2 associate-route-table --route-table-id $PUBLIC_RT_ID --subnet-id $PUBLIC_SUBNET_1_ID --region $REGION
aws ec2 associate-route-table --route-table-id $PUBLIC_RT_ID --subnet-id $PUBLIC_SUBNET_2_ID --region $REGION
aws ec2 associate-route-table --route-table-id $PRIVATE_RT_ID --subnet-id $PRIVATE_SUBNET_1_ID --region $REGION
aws ec2 associate-route-table --route-table-id $PRIVATE_RT_ID --subnet-id $PRIVATE_SUBNET_2_ID --region $REGION

echo "Routing configured"

 Create security groups
WEB_SG_ID=$(aws ec2 create-security-group --group-name web-sg --description "Web servers" --vpc-id $VPC_ID --region $REGION --query 'GroupId' --output text)
APP_SG_ID=$(aws ec2 create-security-group --group-name app-sg --description "App servers" --vpc-id $VPC_ID --region $REGION --query 'GroupId' --output text)
DB_SG_ID=$(aws ec2 create-security-group --group-name db-sg --description "Database servers" --vpc-id $VPC_ID --region $REGION --query 'GroupId' --output text)

 Configure security group rules
aws ec2 authorize-security-group-ingress --group-id $WEB_SG_ID --protocol tcp --port 80 --cidr 0.0.0.0/0 --region $REGION
aws ec2 authorize-security-group-ingress --group-id $WEB_SG_ID --protocol tcp --port 443 --cidr 0.0.0.0/0 --region $REGION
aws ec2 authorize-security-group-ingress --group-id $WEB_SG_ID --protocol tcp --port 22 --cidr 203.0.113.0/24 --region $REGION

aws ec2 authorize-security-group-ingress --group-id $APP_SG_ID --protocol tcp --port 8080 --source-group $WEB_SG_ID --region $REGION
aws ec2 authorize-security-group-ingress --group-id $APP_SG_ID --protocol tcp --port 22 --cidr 203.0.113.0/24 --region $REGION

aws ec2 authorize-security-group-ingress --group-id $DB_SG_ID --protocol tcp --port 3306 --source-group $APP_SG_ID --region $REGION
aws ec2 authorize-security-group-ingress --group-id $DB_SG_ID --protocol tcp --port 22 --cidr 203.0.113.0/24 --region $REGION

echo "Security groups configured"

 Create launch templates
WEB_LT_ID=$(aws ec2 create-launch-template --launch-template-name web-lt --launch-template-data '{"ImageId":"ami-0c55b159cbfafe1f0","InstanceType":"t3.micro","SecurityGroupIds":["'$WEB_SG_ID'"],"UserData":"IyEvYmluL2Jhc2gKeXVtIHVwZGF0ZSAteQp5dW0gaW5zdGFsbCAteSBodHRwZAoKc3lzdGVtY3RsIHN0YXJ0IGh0dHAKc3lzdGVtY3RsIGVuYWJsZSBodHRwCmVjaG8gIjxoMT5XZWIgU2VydmVyPC9oMT4iID4gL3ZhciL3d3dy9odG1sL2luZGV4Lmh0bWw="}' --query 'LaunchTemplate.LaunchTemplateId' --output text)

APP_LT_ID=$(aws ec2 create-launch-template --launch-template-name app-lt --launch-template-data '{"ImageId":"ami-0c55b159cbfafe1f0","InstanceType":"t3.micro","SecurityGroupIds":["'$APP_SG_ID'"]}' --query 'LaunchTemplate.LaunchTemplateId' --output text)

DB_LT_ID=$(aws ec2 create-launch-template --launch-template-name db-lt --launch-template-data '{"ImageId":"ami-0c55b159cbfafe1f0","InstanceType":"t3.micro","SecurityGroupIds":["'$DB_SG_ID'"]}' --query 'LaunchTemplate.LaunchTemplateId' --output text)

echo "Launch templates created"

 Create Auto Scaling Groups
aws autoscaling create-auto-scaling-group \
    --auto-scaling-group-name web-asg \
    --launch-template-name web-lt \
    --vpc-zone-identifier $PUBLIC_SUBNET_1_ID,$PUBLIC_SUBNET_2_ID \
    --min-size 2 --max-size 6 --desired-capacity 2 \
    --health-check-type EC2 --health-check-grace-period 300

aws autoscaling create-auto-scaling-group \
    --auto-scaling-group-name app-asg \
    --launch-template-name app-lt \
    --vpc-zone-identifier $PRIVATE_SUBNET_1_ID,$PRIVATE_SUBNET_2_ID \
    --min-size 2 --max-size 4 --desired-capacity 2 \
    --health-check-type EC2 --health-check-grace-period 300

aws autoscaling create-auto-scaling-group \
    --auto-scaling-group-name db-asg \
    --launch-template-name db-lt \
    --vpc-zone-identifier $PRIVATE_SUBNET_1_ID \
    --min-size 1 --max-size 1 --desired-capacity 1 \
    --health-check-type EC2 --health-check-grace-period 300

echo "Auto Scaling Groups created"

echo "Three-tier application architecture setup completed!"
echo "VPC ID: $VPC_ID"
echo "Web ASG: web-asg"
echo "App ASG: app-asg"
echo "DB ASG: db-asg"
```

 11. Best Practices and Optimization

 11.1. Security Best Practices
```bash
 Use least privilege security group rules
 Never use 0.0.0.0/0 for database access
 Use specific source security groups instead

 Enable VPC Flow Logs for monitoring
aws ec2 create-flow-logs \
    --resource-type VPC \
    --resource-ids vpc-1234567890abcdef0 \
    --traffic-type ALL \
    --log-destination-type cloud-watch-logs

 Use Network ACLs for additional protection
 Block common attack patterns
aws ec2 create-network-acl-entry \
    --network-acl-id acl-1234567890abcdef0 \
    --rule-number 9999 \
    --protocol -1 \
    --rule-action deny \
    --egress \
    --cidr-block 0.0.0.0/0
```

 11.2. Performance Optimization
```bash
 Use Enhanced Networking for better performance
 Launch instances with ENA support
aws ec2 run-instances \
    --image-id ami-0c55b159cbfafe1f0 \
    --instance-type t3.micro \
    --ena-support

 Use placement groups for low latency
aws ec2 create-placement-group \
    --group-name low-latency-pg \
    --strategy cluster

 Use multiple Availability Zones for high availability
 Distribute instances across at least 2 AZs
```

 11.3. Cost Optimization
```bash
 Use spot instances for cost savings
aws ec2 request-spot-fleet \
    --spot-fleet-request-config file://spot-fleet-config.json

 Spot fleet configuration (spot-fleet-config.json)
{
  "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
  "TargetCapacity": 2,
  "SpotPrice": "0.02",
  "AllocationStrategy": "diversified",
  "LaunchSpecifications": [
    {
      "ImageType": "machine",
      "ImageId": "ami-0c55b159cbfafe1f0",
      "InstanceType": "t3.micro",
      "SubnetId": "subnet-1234567890abcdef0"
    }
  ]
}

 Use Reserved Instances for predictable workloads
aws ec2 purchase-reserved-instances-offering \
    --reserved-instances-offering-id yz1234567890abcdef0 \
    --instance-count 1
```

 12. Resources and Further Learning

 12.1. Documentation
- [AWS VPC User Guide](https://docs.aws.amazon.com/vpc/)
- [Amazon EC2 User Guide](https://docs.aws.amazon.com/ec2/)
- [Elastic Load Balancing Guide](https://docs.aws.amazon.com/elasticloadbalancing/)
- [Auto Scaling Guide](https://docs.aws.amazon.com/autoscaling/)

 12.2. Tools and Utilities
- VPC Flow Logs: Network traffic monitoring
- AWS Config: Configuration tracking
- AWS Trusted Advisor: Best practices recommendations
- AWS Compute Optimizer: Instance optimization recommendations

 12.3. Best Practices
- Plan IP address ranges carefully
- Use multiple Availability Zones
- Implement proper security group rules
- Monitor network performance and costs
- Use Auto Scaling for elasticity
- Implement proper backup and disaster recovery
- Regularly review and optimize architecture

---

Next Steps: Practice building different network architectures, experiment with Auto Scaling, and implement monitoring and alerting for your EC2 infrastructure.
