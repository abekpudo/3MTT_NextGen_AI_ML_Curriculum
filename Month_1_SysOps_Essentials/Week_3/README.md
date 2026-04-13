 Week 3: AWS Cloud Essentials

Duration: 5 days | Theme: Cloud Computing Fundamentals | Difficulty: Intermediate  
Prerequisite: Complete Week 2 (Linux & Bash)

---

 🎯 Learning Objectives

By the end of this week, you will:
- ✅ Understand cloud computing models (IaaS, PaaS, SaaS)
- ✅ Set up and secure AWS account with IAM best practices
- ✅ Launch and manage EC2 instances with proper networking
- ✅ Store and manage data in S3 with lifecycle policies
- ✅ Deploy Python web applications on AWS infrastructure
- ✅ Implement security groups, key pairs, and access controls

---

 📖 Detailed Daily Schedule

 Day 1: Cloud Computing Fundamentals
Lecture: [01_cloud_computing_intro.md](./lecture_notes/01_cloud_computing_intro.md)

Topics:
- Cloud computing definition and benefits
- Service models: IaaS, PaaS, SaaS with examples
- Deployment models: Public, Private, Hybrid, Multi-cloud
- AWS global infrastructure: Regions, Availability Zones, Edge Locations
- Core AWS services overview: Compute, Storage, Database, Networking

Hands-On:
```bash
 Create AWS account (if not done)
 Visit: https://aws.amazon.com/free/

 Set up AWS CLI (after account creation)
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

 Configure AWS CLI
aws configure
 Enter: AWS Access Key ID, Secret Access Key, region (us-east-1), output (json)

 Verify setup
aws sts get-caller-identity
```

Practice:
- [ ] Create AWS account with MFA enabled
- [ ] Set up billing alerts
- [ ] Configure AWS CLI
- [ ] Explore AWS Console navigation
- [ ] List available regions: `aws ec2 describe-regions`

---

 Day 2: AWS Identity & Access Management (IAM)
Lecture: [02_iam_and_security.md](./lecture_notes/02_iam_and_security.md)

Topics:
- IAM users, groups, and roles
- Policies and permissions (JSON format)
- MFA (Multi-Factor Authentication)
- Access keys and credential management
- IAM best practices and security

Hands-On:
```bash
 Create IAM user via CLI
aws iam create-user --user-name cli-user

 Attach policy to user
aws iam attach-user-policy \
    --user-name cli-user \
    --policy-arn arn:aws:iam::aws:policy/AmazonEC2ReadOnlyAccess

 Create access key
aws iam create-access-key --user-name cli-user

 List users
aws iam list-users

 Configure profile for specific user
aws configure --profile dev-user

 Use specific profile
aws ec2 describe-instances --profile dev-user
```

Practice:
- [ ] Create 3 IAM users with different permissions
- [ ] Create IAM group and add users
- [ ] Write custom IAM policy
- [ ] Enable MFA for root account
- [ ] Rotate access keys

---

 Day 3: EC2 - Elastic Compute Cloud
Lecture: [03_ec2_and_networking.md](./lecture_notes/03_ec2_and_networking.md)

Topics:
- EC2 instance types and use cases
- AMIs (Amazon Machine Images)
- Key pairs and security groups
- VPC basics and subnets
- EBS (Elastic Block Store) volumes
- Connecting via SSH

Hands-On:
```bash
 Create key pair
aws ec2 create-key-pair \
    --key-name my-key-pair \
    --query 'KeyMaterial' \
    --output text > my-key-pair.pem

chmod 400 my-key-pair.pem

 Create security group
aws ec2 create-security-group \
    --group-name web-sg \
    --description "Security group for web server"

 Add SSH inbound rule
aws ec2 authorize-security-group-ingress \
    --group-name web-sg \
    --protocol tcp \
    --port 22 \
    --cidr 0.0.0.0/0

 Add HTTP inbound rule
aws ec2 authorize-security-group-ingress \
    --group-name web-sg \
    --protocol tcp \
    --port 80 \
    --cidr 0.0.0.0/0

 Launch EC2 instance
aws ec2 run-instances \
    --image-id ami-0c55b159cbfafe1f0 \
    --instance-type t2.micro \
    --key-name my-key-pair \
    --security-groups web-sg \
    --count 1

 Get instance public IP
aws ec2 describe-instances \
    --query 'Reservations[].Instances[].PublicIpAddress' \
    --output text

 SSH into instance
ssh -i my-key-pair.pem ec2-user@<public-ip>
```

Practice:
- [ ] Launch 3 EC2 instances (different AMIs)
- [ ] Create and configure security groups
- [ ] Connect via SSH and install software
- [ ] Create EBS volume and attach to instance
- [ ] Create AMI from running instance

---

 Day 4: S3 - Simple Storage Service
Lecture: [04_s3_storage.md](./lecture_notes/04_s3_storage.md)

Topics:
- Buckets, objects, and keys
- S3 storage classes (Standard, IA, Glacier)
- Versioning and lifecycle policies
- Static website hosting
- S3 security (bucket policies, ACLs, encryption)

Hands-On:
```bash
 Create bucket
aws s3 mb s3://my-unique-bucket-name-12345

 List buckets
aws s3 ls

 Upload file
aws s3 cp local-file.txt s3://my-unique-bucket-name-12345/

 Upload directory
aws s3 sync ./local-directory s3://my-unique-bucket-name-12345/backup/

 List bucket contents
aws s3 ls s3://my-unique-bucket-name-12345/

 Download file
aws s3 cp s3://my-unique-bucket-name-12345/file.txt ./downloaded/

 Enable versioning
aws s3api put-bucket-versioning \
    --bucket my-unique-bucket-name-12345 \
    --versioning-configuration Status=Enabled

 Set lifecycle policy
aws s3api put-bucket-lifecycle \
    --bucket my-unique-bucket-name-12345 \
    --lifecycle-configuration file://lifecycle.json

 Make bucket public (careful!)
aws s3api put-bucket-policy \
    --bucket my-unique-bucket-name-12345 \
    --policy file://bucket-policy.json

 Static website hosting
aws s3 website s3://my-unique-bucket-name-12345/ \
    --index-document index.html \
    --error-document error.html
```

Practice:
- [ ] Create 3 buckets with different purposes
- [ ] Implement versioning
- [ ] Configure lifecycle policies
- [ ] Host static website on S3
- [ ] Set up cross-region replication

---

 Day 5: Cloud Deployment Project
Lecture: [05_deployment_project.md](./lecture_notes/05_deployment_project.md)

Topics:
- Application deployment architecture
- Environment setup automation
- Database deployment (RDS basics)
- Load balancing concepts
- Auto Scaling introduction
- Cost optimization

Hands-On Project:

Step 1: Create user data script for automation
```bash
!/bin/bash
 user-data.sh
yum update -y
yum install -y python3 python3-pip nginx git
systemctl start nginx
systemctl enable nginx

 Deploy Python app
cd /home/ec2-user
git clone https://github.com/username/my-flask-app.git
cd my-flask-app
pip3 install -r requirements.txt

 Configure nginx as reverse proxy
cat > /etc/nginx/conf.d/app.conf << 'EOF'
server {
    listen 80;
    location / {
        proxy_pass http://127.0.0.1:5000;
    }
}
EOF

systemctl restart nginx

 Start application
python3 app.py > app.log 2>&1 &
```

Step 2: Launch with user data
```bash
aws ec2 run-instances \
    --image-id ami-0c55b159cbfafe1f0 \
    --instance-type t2.micro \
    --key-name my-key-pair \
    --security-groups web-sg \
    --user-data file://user-data.sh \
    --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=web-app-server}]'
```

Step 3: Monitor deployment
```bash
 Check instance status
aws ec2 describe-instances \
    --filters Name=tag:Name,Values=web-app-server \
    --query 'Reservations[].Instances[].[InstanceId,State.Name,PublicIpAddress]'

 View system logs
aws ec2 get-console-output --instance-id <instance-id>

 Test application
curl http://<public-ip>/
```

Practice:
- [ ] Deploy Flask application on EC2
- [ ] Configure nginx reverse proxy
- [ ] Set up S3 for static assets
- [ ] Implement health checks
- [ ] Create deployment documentation

---

 📚 Essential Resources

 Required Reading
1. Enhanced Lecture Notes:
   - [01_cloud_computing_intro.md](./lecture_notes/01_cloud_computing_intro.md) - Cloud fundamentals
   - [02_iam_and_security.md](./lecture_notes/02_iam_and_security.md) - IAM and security
   - [03_ec2_and_networking.md](./lecture_notes/03_ec2_and_networking.md) - EC2 and networking
   - [04_s3_storage.md](./lecture_notes/04_s3_storage.md) - S3 storage
   - [05_deployment_project.md](./lecture_notes/05_deployment_project.md) - Deployment

2. External Resources:
   - [AWS Free Tier](https://aws.amazon.com/free/) - Free tier details
   - [AWS CLI Reference](https://docs.aws.amazon.com/cli/) - CLI documentation
   - [AWS Pricing Calculator](https://calculator.aws/) - Cost estimation
   - [Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/) - Best practices

 Tools & Setup
```bash
 Required Accounts
- AWS Account (free tier recommended)
- AWS CLI configured locally

 Tools to Install
- AWS CLI v2
- SSH client
- curl/wget

 Optional Tools
- AWS SAM CLI (for serverless)
- AWS CDK (infrastructure as code)
- Session Manager Plugin
```

---

 💼 Weekly Deliverables

 Required Submissions

1. AWS Account Setup (15%)
   - Screenshot of AWS Console dashboard
   - IAM user configuration details
   - MFA enabled confirmation
   - Billing alerts configuration

2. EC2 Deployment (25%)
   - Running EC2 instance screenshot
   - Security group rules documentation
   - SSH connection proof
   - Installed software list

3. S3 Configuration (20%)
   - Bucket listing screenshot
   - File upload/download demonstration
   - Versioning enabled confirmation
   - Static website URL (if applicable)

4. Python Application (25%)
   - Application source code
   - Deployment script
   - Running application screenshot
   - Access URL and verification

5. Documentation (15%)
   - Architecture diagram
   - Deployment steps
   - Cost analysis
   - Lessons learned

---

 🎯 Exercises

 Exercise 1: AWS CLI Automation
Create scripts to automate AWS resource management:
- List all EC2 instances by region
- Start/stop instances on schedule
- Clean up unused resources
- Generate cost reports

 Exercise 2: Security Hardening
Implement AWS security best practices:
- Review and tighten IAM policies
- Enable CloudTrail for auditing
- Set up Config for compliance
- Implement least privilege access

 Exercise 3: Multi-Region Deployment
Deploy application in multiple regions:
- Launch EC2 in 2 regions
- Set up S3 cross-region replication
- Implement Route 53 DNS
- Test failover scenarios

 Exercise 4: Infrastructure as Code
Create AWS infrastructure using:
- CloudFormation templates
- Terraform configurations
- AWS CDK (Python)
- Document differences

---

 📋 Assignment: Cloud Infrastructure Project

 Part A: Account Setup (20 points)
- [ ] AWS account created with proper email (5 pts)
- [ ] MFA enabled on root account (5 pts)
- [ ] IAM users configured with appropriate permissions (5 pts)
- [ ] Billing alerts set up (5 pts)

 Part B: EC2 Infrastructure (30 points)
- [ ] EC2 instance launched with proper configuration (10 pts)
- [ ] Security groups configured correctly (10 pts)
- [ ] SSH access working with key pairs (5 pts)
- [ ] Web server installed and running (5 pts)

 Part C: S3 Storage (20 points)
- [ ] S3 bucket created with unique name (5 pts)
- [ ] Files uploaded and organized (5 pts)
- [ ] Versioning enabled (5 pts)
- [ ] Lifecycle policy configured (5 pts)

 Part D: Application Deployment (20 points)
- [ ] Python application code (5 pts)
- [ ] Deployment automation script (5 pts)
- [ ] Application running and accessible (5 pts)
- [ ] Documentation complete (5 pts)

 Part E: Cost Analysis (10 points)
- [ ] Monthly cost estimate (5 pts)
- [ ] Cost optimization suggestions (5 pts)

---

 🔗 Quick Navigation

| Resource | Description |
|----------|-------------|
| [� Lecture Notes](./lecture_notes/) | AWS concepts and tutorials |
| [🎯 Exercises](./exercises/) | Hands-on AWS practice |
| [📋 Assignments](./assignments/) | Graded cloud projects |
| [📄 Cheatsheets](./cheatsheets/) | AWS CLI quick reference |

---

 ⚡ Daily Study Schedule

| Time | Activity |
|------|----------|
| 30 min | Read lecture notes |
| 45 min | Hands-on AWS console/CLI |
| 15 min | Complete exercises |
| 15 min | Document and commit |

Total Daily Time: ~1.75 hours

---

 � Success Tips for Week 3

1. Use Free Tier: Stay within AWS free tier limits
2. Clean Up Resources: Delete unused resources to avoid charges
3. Monitor Costs: Check AWS Cost Explorer daily
4. Secure Your Account: Never share access keys
5. Use IAM Roles: Don't use root account for daily tasks
6. Document Everything: Screenshot configurations
7. Version Control: Store scripts in Git
8. Test Locally: Use LocalStack for local testing

---

 🆘 Common Issues & Solutions

| Issue | Solution |
|-------|----------|
| `Access Denied` | Check IAM permissions and policies |
| `Invalid Access Key` | Regenerate keys with `aws configure` |
| `Instance won't start` | Check limits, try different AZ |
| `SSH connection refused` | Verify security group rules, key permissions |
| `Bucket name taken` | S3 names are global, use unique name |
| `Unexpected charges` | Set up billing alerts, monitor daily |

---

 🎓 Week 3 Completion Checklist

- [ ] AWS account created and secured
- [ ] AWS CLI configured
- [ ] EC2 instance running
- [ ] SSH access working
- [ ] S3 bucket created
- [ ] Files uploaded to S3
- [ ] Python app deployed
- [ ] Application accessible
- [ ] Costs monitored
- [ ] Documentation complete
- [ ] All exercises done
- [ ] Assignment submitted

---

Previous: [Week 2: Linux & Bash ←](../Week_2/README.md) | Next: [Week 4: DevOps →](../Week_4/README.md)
