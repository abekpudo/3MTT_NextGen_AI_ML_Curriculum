 AWS Security: Protecting Cloud Infrastructure and Data

 1. Introduction
Security is the foundation of trustworthy cloud infrastructure. This lecture covers AWS security fundamentals including identity management, encryption, monitoring, and incident response to protect cloud resources, data, and machine learning workloads.

 2. Why AWS Security Matters
- Data Protection: Safeguard sensitive data and ML models
- Compliance: Meet regulatory requirements (GDPR, HIPAA, SOC2)
- Trust: Build confidence with customers and stakeholders
- Business Continuity: Prevent security breaches and data loss
- Cost Management: Avoid costs associated with security incidents

 3. Identity and Access Management (IAM)

 3.1. IAM Users and Groups
```bash
 Create IAM user with programmatic access
aws iam create-user \
    --user-name john.doe \
    --path /engineering/ \
    --tags Key=Department,Value=Engineering Key=Role,Value=Developer

 Create IAM group
aws iam create-group \
    --group-name developers \
    --path /engineering/

 Add user to group
aws iam add-user-to-group \
    --group-name developers \
    --user-name john.doe

 Create access keys for programmatic access
aws iam create-access-key \
    --user-name john.doe \
    --output json

 List users
aws iam list-users --output table

 List groups
aws iam list-groups --output table

 List group members
aws iam get-group \
    --group-name developers \
    --output table
```

 3.2. IAM Policies and Permissions
```bash
 Create custom IAM policy
cat > developer-policy.json << EOF
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeInstances",
                "ec2:DescribeImages",
                "ec2:DescribeSnapshots",
                "ec2:DescribeVolumes",
                "s3:ListAllMyBuckets",
                "s3:GetObject",
                "s3:PutObject"
            ],
            "Resource": "",
            "Condition": {
                "StringEquals": {
                    "aws:RequestedRegion": ["us-west-2", "us-east-1"]
                }
            }
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:StartInstances",
                "ec2:StopInstances",
                "ec2:RebootInstances"
            ],
            "Resource": "arn:aws:ec2:::instance:i-",
            "Condition": {
                "StringEquals": {
                    "ec2:ResourceTag/Environment": "Development"
                }
            }
        },
        {
            "Effect": "Deny",
            "Action": [
                "ec2:TerminateInstances",
                "ec2:DeleteVolume",
                "s3:DeleteObject"
            ],
            "Resource": ""
        }
    ]
}
EOF

 Create policy
aws iam create-policy \
    --policy-name DeveloperPolicy \
    --policy-document file://developer-policy.json \
    --description "Policy for developers with limited permissions"

 Attach policy to group
aws iam attach-group-policy \
    --group-name developers \
    --policy-arn arn:aws:iam::123456789012:policy/DeveloperPolicy

 List policies attached to group
aws iam list-attached-group-policies \
    --group-name developers \
    --output table
```

 3.3. IAM Roles for Services
```bash
 Create IAM role for EC2 instances
cat > ec2-trust-policy.json << EOF
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "ec2.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
EOF

 Create role
aws iam create-role \
    --role-name EC2S3AccessRole \
    --assume-role-policy-document file://ec2-trust-policy.json \
    --description "Role for EC2 instances to access S3"

 Attach S3 access policy to role
aws iam attach-role-policy \
    --role-name EC2S3AccessRole \
    --policy-arn arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess

 Create instance profile
aws iam create-instance-profile \
    --instance-profile-name EC2S3AccessProfile

 Add role to instance profile
aws iam add-role-to-instance-profile \
    --instance-profile-name EC2S3AccessProfile \
    --role-name EC2S3AccessRole

 List roles
aws iam list-roles --output table

 List instance profiles
aws iam list-instance-profiles --output table
```

 3.4. Multi-Factor Authentication (MFA)
```bash
 Enable MFA device for user
 First, list available virtual MFA devices
aws iam list-virtual-mfa-devices --output table

 Create virtual MFA device
aws iam create-virtual-mfa-device \
    --virtual-mfa-device-name john.doe-mfa \
    --path /engineering/ \
    --output json

 Enable MFA for user
aws iam enable-mfa-device \
    --user-name john.doe \
    --serial-number arn:aws:iam::123456789012:mfa/john.doe-mfa \
    --authentication-code-1 123456 \
    --authentication-code-2 789012

 List MFA devices for user
aws iam list-mfa-devices \
    --user-name john.doe \
    --output table

 Create policy requiring MFA for console access
cat > mfa-required-policy.json << EOF
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Deny",
            "Action": "",
            "Resource": "",
            "Condition": {
                "BoolIfExists": {
                    "aws:MultiFactorAuthPresent": "false"
                }
            }
        }
    ]
}
EOF

 Create and attach MFA requirement policy
aws iam create-policy \
    --policy-name MFARequiredPolicy \
    --policy-document file://mfa-required-policy.json \
    --description "Requires MFA for all actions"

aws iam attach-user-policy \
    --user-name john.doe \
    --policy-arn arn:aws:iam::123456789012:policy/MFARequiredPolicy
```

 4. Data Encryption

 4.1. AWS Key Management Service (KMS)
```bash
 Create KMS key
aws kms create-key \
    --description "Key for encrypting sensitive data" \
    --key-usage ENCRYPT_DECRYPT \
    --origin AWS_KMS \
    --customer-managed-key-spec SYMMETRIC_DEFAULT \
    --tags Key=Environment,Value=Production Key=Application,Value=DataPipeline

 Create key alias
aws kms create-alias \
    --alias-name alias/data-encryption-key \
    --target-key-id key-id-from-create-key-output

 List KMS keys
aws kms list-keys --output table

 List aliases
aws kms list-aliases --output table

 Encrypt data
aws kms encrypt \
    --key-id alias/data-encryption-key \
    --plaintext file://sensitive-data.txt \
    --output text \
    --query CiphertextBlob \
    --base64 > encrypted-data.b64

 Decrypt data
aws kms decrypt \
    --ciphertext-blob file://encrypted-data.b64 \
    --output text \
    --query Plaintext \
    --base64 > decrypted-data.txt

 Generate data key
aws kms generate-data-key \
    --key-id alias/data-encryption-key \
    --key-spec AES_256 \
    --output json
```

 4.2. S3 Encryption
```bash
 Enable default encryption for S3 bucket
aws s3api put-bucket-encryption \
    --bucket my-bucket \
    --server-side-encryption-configuration '{
        "Rules": [
            {
                "ApplyServerSideEncryptionByDefault": {
                    "SSEAlgorithm": "AES256"
                }
            }
        ]
    }'

 Enable KMS encryption for S3 bucket
aws s3api put-bucket-encryption \
    --bucket my-sensitive-bucket \
    --server-side-encryption-configuration '{
        "Rules": [
            {
                "ApplyServerSideEncryptionByDefault": {
                    "SSEAlgorithm": "aws:kms",
                    "KMSMasterKeyID": "alias/data-encryption-key"
                }
            }
        ]
    }'

 Upload with server-side encryption
aws s3 cp sensitive-file.txt s3://my-sensitive-bucket/ \
    --sse aws:kms \
    --sse-kms-key-id alias/data-encryption-key

 Check bucket encryption status
aws s3api get-bucket-encryption \
    --bucket my-sensitive-bucket \
    --output json
```

 4.3. EBS Volume Encryption
```bash
 Create encrypted EBS volume
aws ec2 create-volume \
    --availability-zone us-west-2a \
    --size 100 \
    --volume-type gp3 \
    --encrypted \
    --kms-key-id alias/data-encryption-key \
    --tag-specifications 'ResourceType=volume,Tags=[{Key=Name,Value=EncryptedVolume}]'

 Launch EC2 instance with encrypted EBS
aws ec2 run-instances \
    --image-id ami-0c55b159cbfafe1f0 \
    --instance-type t3.micro \
    --block-device-mappings '[{"DeviceName":"/dev/sda1","Ebs":{"VolumeSize":20,"VolumeType":"gp3","Encrypted":true,"KmsKeyId":"alias/data-encryption-key"}}]'

 Check volume encryption
aws ec2 describe-volumes \
    --volume-ids vol-1234567890abcdef0 \
    --query 'Volumes[0].Encrypted'
```

 5. Network Security

 5.1. Security Groups
```bash
 Create security group for web servers
aws ec2 create-security-group \
    --group-name web-server-sg \
    --description "Security group for web servers" \
    --vpc-id vpc-1234567890abcdef0 \
    --tag-specifications 'ResourceType=security-group,Tags=[{Key=Name,Value=WebServerSG}]'

 Add inbound rules with specific sources
aws ec2 authorize-security-group-ingress \
    --group-id sg-1234567890abcdef0 \
    --protocol tcp \
    --port 80 \
    --cidr 0.0.0.0/0 \
    --description "HTTP from anywhere"

aws ec2 authorize-security-group-ingress \
    --group-id sg-1234567890abcdef0 \
    --protocol tcp \
    --port 443 \
    --cidr 0.0.0.0/0 \
    --description "HTTPS from anywhere"

aws ec2 authorize-security-group-ingress \
    --group-id sg-1234567890abcdef0 \
    --protocol tcp \
    --port 22 \
    --cidr 203.0.113.0/24 \
    --description "SSH from office network"

 Create security group for database servers
aws ec2 create-security-group \
    --group-name database-sg \
    --description "Security group for database servers" \
    --vpc-id vpc-1234567890abcdef0

 Allow database access only from web servers
aws ec2 authorize-security-group-ingress \
    --group-id sg-0987654321fedcba0 \
    --protocol tcp \
    --port 3306 \
    --source-group sg-1234567890abcdef0 \
    --description "MySQL access from web servers"

 List security group rules
aws ec2 describe-security-groups \
    --group-ids sg-1234567890abcdef0 \
    --output json
```

 5.2. Network ACLs (NACLs)
```bash
 Create Network ACL
aws ec2 create-network-acl \
    --vpc-id vpc-1234567890abcdef0 \
    --tag-specifications 'ResourceType=network-acl,Tags=[{Key=Name,Value=PublicNACL}]'

 Add inbound rules
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

 Add deny rules for restricted traffic
aws ec2 create-network-acl-entry \
    --network-acl-id acl-1234567890abcdef0 \
    --rule-number 9999 \
    --protocol -1 \
    --rule-action deny \
    --egress \
    --cidr-block 10.0.0.0/8 \
    --description "Deny private network ranges"

 Associate NACL with subnet
aws ec2 associate-network-acl-subnet \
    --network-acl-id acl-1234567890abcdef0 \
    --subnet-id subnet-1234567890abcdef0
```

 5.3. VPC Endpoints
```bash
 Create VPC endpoint for S3
aws ec2 create-vpc-endpoint \
    --vpc-id vpc-1234567890abcdef0 \
    --service-name com.amazonaws.us-west-2.s3 \
    --vpc-endpoint-type Gateway \
    --route-table-ids rtb-1234567890abcdef0

 Create VPC endpoint for DynamoDB
aws ec2 create-vpc-endpoint \
    --vpc-id vpc-1234567890abcdef0 \
    --service-name com.amazonaws.us-west-2.dynamodb \
    --vpc-endpoint-type Gateway \
    --route-table-ids rtb-1234567890abcdef0

 Create interface VPC endpoint for EC2
aws ec2 create-vpc-endpoint \
    --vpc-id vpc-1234567890abcdef0 \
    --service-name com.amazonaws.us-west-2.ec2 \
    --vpc-endpoint-type Interface \
    --subnet-ids subnet-1234567890abcdef0 subnet-0987654321fedcba0 \
    --security-group-ids sg-1234567890abcdef0 \
    --private-dns-enabled

 List VPC endpoints
aws ec2 describe-vpc-endpoints --output table
```

 6. Monitoring and Logging

 6.1. AWS CloudTrail
```bash
 Create CloudTrail for all regions
aws cloudtrail create-trail \
    --name my-cloudtrail \
    --s3-bucket-name my-cloudtrail-bucket \
    --s3-key-prefix cloudtrail-logs/ \
    --include-global-service-events \
    --is-multi-region-trail \
    --enable-log-file-validation

 Enable CloudTrail logging for data events
aws cloudtrail put-event-selectors \
    --trail-name my-cloudtrail \
    --event-selectors '[{"ReadWriteType": "All","IncludeManagementEvents": true,"DataResources": [{"Type": "AWS::S3::Object","Values": ["arn:aws:s3:::my-bucket/"]}]},{"ReadWriteType": "All","IncludeManagementEvents": true,"DataResources": [{"Type": "AWS::Lambda::Function","Values": ["arn:aws:lambda:us-west-2:123456789012:function:"]}]}]'

 Get CloudTrail status
aws cloudtrail get-trail-status \
    --name my-cloudtrail

 List trails
aws cloudtrail describe-trails --output table

 Query CloudTrail events
aws cloudtrail lookup-events \
    --lookup-attributes AttributeKey=Username,AttributeValue=john.doe \
    --max-results 10
```

 6.2. Amazon CloudWatch Alarms
```bash
 Create CloudWatch alarm for root account usage
aws cloudwatch put-metric-alarm \
    --alarm-name RootAccountUsage \
    --alarm-description "Alert when root account is used" \
    --metric-name ConsoleSignIn \
    --namespace AWS/CloudTrail \
    --statistic Sum \
    --period 300 \
    --threshold 1 \
    --comparison-operator GreaterThanOrEqualToThreshold \
    --evaluation-periods 1 \
    --alarm-actions arn:aws:sns:us-west-2:123456789012:SecurityAlerts \
    --dimensions Name=EventName,Value=ConsoleSignIn Name=UserType,Value=Root

 Create alarm for IAM policy changes
aws cloudwatch put-metric-alarm \
    --alarm-name IAMPolicyChanges \
    --alarm-description "Alert when IAM policies are changed" \
    --metric-name IAMPolicyChanges \
    --namespace AWS/CloudTrail \
    --statistic Sum \
    --period 300 \
    --threshold 1 \
    --comparison-operator GreaterThanOrEqualToThreshold \
    --evaluation-periods 1 \
    --alarm-actions arn:aws:sns:us-west-2:123456789012:SecurityAlerts

 Create alarm for failed login attempts
aws cloudwatch put-metric-alarm \
    --alarm-name FailedLoginAttempts \
    --alarm-description "Alert for failed login attempts" \
    --metric-name ConsoleLoginFailure \
    --namespace AWS/CloudTrail \
    --statistic Sum \
    --period 300 \
    --threshold 5 \
    --comparison-operator GreaterThanThreshold \
    --evaluation-periods 1 \
    --alarm-actions arn:aws:sns:us-west-2:123456789012:SecurityAlerts

 List alarms
aws cloudwatch describe-alarms --output table
```

 6.3. AWS Config Rules
```bash
 Create AWS Config rule for MFA enforcement
aws configservice put-config-rule \
    --config-rule '{
        "ConfigRuleName": "mfa-required-for-console-access",
        "Description": "Ensure MFA is enabled for console access",
        "Source": {
            "Owner": "AWS",
            "SourceIdentifier": "IAM_ROOT_ACCOUNT_MFA_ENABLED"
        },
        "Scope": {
            "ComplianceResourceTypes": ["AWS::IAM::User"]
        }
    }'

 Create Config rule for encryption
aws configservice put-config-rule \
    --config-rule '{
        "ConfigRuleName": "s3-bucket-encryption-enabled",
        "Description": "Ensure S3 buckets have encryption enabled",
        "Source": {
            "Owner": "AWS",
            "SourceIdentifier": "S3_BUCKET_DEFAULT_ENCRYPTION_ENABLED"
        },
        "Scope": {
            "ComplianceResourceTypes": ["AWS::S3::Bucket"]
        }
    }'

 Get Config rule compliance
aws configservice get-compliance-details-by-config-rule \
    --config-rule-name mfa-required-for-console-access

 Evaluate compliance
aws configservice start-config-rules-evaluation \
    --config-rule-names mfa-required-for-console-access s3-bucket-encryption-enabled
```

 7. Advanced Security Services

 7.1. AWS GuardDuty
```bash
 Enable GuardDuty
aws guardduty create-detector \
    --enable

 List detectors
aws guardduty list-detectors --output table

 Get detector status
aws guardduty get-detector --detector-id detector-id

 Create IP set for GuardDuty
aws guardduty create-ip-set \
    --detector-id detector-id \
    --name trusted-ips \
    --format TXT \
    --location s3://my-bucket/trusted-ips.txt \
    --activate

 Create threat intel set
aws guardduty create-threat-intel-set \
    --detector-id detector-id \
    --name malicious-ips \
    --format TXT \
    --location s3://my-bucket/malicious-ips.txt \
    --activate

 List findings
aws guardduty list-findings \
    --detector-id detector-id \
    --finding-criteria '{"Criterion":{"service":{"action":{"actionType":{"Value":["NETWORK_CONNECTION"],"Comparison":"EQUALS"}}}}'

 Export findings to S3
aws guardduty export-findings \
    --detector-id detector-id \
    --s3-destination '{"BucketName":"my-guardduty-findings","KmsKeyArn":"arn:aws:kms:us-west-2:123456789012:key/12345678-1234-1234-1234-123456789012"}'
```

 7.2. AWS Security Hub
```bash
 Enable Security Hub
aws securityhub enable-security-hub \
    --enable-standards "arn:aws:securityhub:::ruleset/cis-aws-foundations-benchmark/v/1.2.0" "arn:aws:securityhub:::ruleset/pci-dss/v/3.2.1"

 Subscribe to security standards
aws securityhub subscribe \
    --standards-subscription-requests '{"StandardsArn":"arn:aws:securityhub:::ruleset/cis-aws-foundations-benchmark/v/1.2.0"}'

 List enabled standards
aws securityhub list-enabled-standards --output table

 Get security findings
aws securityhub get-findings \
    --filters '{"ResourceId":[{"Value":"arn:aws:s3:::my-bucket","Comparison":"EQUALS"}],"ComplianceStatus":[{"Value":"FAILED","Comparison":"EQUALS"}]}'

 Update finding severity
aws securityhub batch-update-findings \
    --finding-identifiers '[{"Id":"arn:aws:securityhub:us-west-2:123456789012:finding/12345678-1234-1234-1234-123456789012","ProductArn":"arn:aws:securityhub:us-west-2:123456789012:product/aws/guardduty"}]' \
    --note '{"Text":"Updated severity based on risk assessment","UpdatedAt":"'$(date -u +%Y-%m-%dT%H:%M:%SZ)'","UpdatedBy":"security-team"}' \
    --severity UPDATED_CRITICAL
```

 8. Incident Response

 8.1. Automated Response with Lambda
```bash
 Create Lambda function for incident response
cat > incident-response.py << 'EOF'
import json
import boto3
import os

def lambda_handler(event, context):
     Parse Security Hub finding
    finding = event['detail']['findings'][0]
    
     Extract relevant information
    resource_id = finding['Resources'][0]['Id']
    severity = finding['Severity']['Label']
    finding_id = finding['Id']
    
     Take action based on severity
    if severity in ['CRITICAL', 'HIGH']:
         Isolate compromised resource
        if 'ec2' in resource_id:
            isolate_instance(resource_id)
        
         Create incident ticket (placeholder)
        create_incident_ticket(finding_id, severity)
        
         Notify security team
        send_notification(finding_id, severity)
    
    return {
        'statusCode': 200,
        'body': json.dumps('Incident response executed')
    }

def isolate_instance(instance_id):
    ec2 = boto3.client('ec2')
    
     Create security group to block all traffic
    sg = ec2.create_security_group(
        GroupName='isolation-sg',
        Description='Security group for incident isolation',
        VpcId='vpc-1234567890abcdef0'
    )
    
     Attach to instance
    ec2.attach_network_interface(
        NetworkInterfaceId=instance_id,
        SecurityGroupId=sg['GroupId']
    )

def create_incident_ticket(finding_id, severity):
     Placeholder for creating incident ticket
    print(f"Creating incident ticket for finding {finding_id} with severity {severity}")

def send_notification(finding_id, severity):
    sns = boto3.client('sns')
    
    message = f"Security incident detected: {finding_id}\nSeverity: {severity}\nImmediate action required."
    
    sns.publish(
        TopicArn=os.environ['SNS_TOPIC_ARN'],
        Subject=f'Security Alert - {severity}',
        Message=message
    )
EOF

 Create Lambda deployment package
zip incident-response.zip incident-response.py

 Create Lambda function
aws lambda create-function \
    --function-name incident-response \
    --runtime python3.9 \
    --role arn:aws:iam::123456789012:role/lambda-execution-role \
    --handler incident-response.lambda_handler \
    --zip-file fileb://incident-response.zip \
    --environment Variables={SNS_TOPIC_ARN=arn:aws:sns:us-west-2:123456789012:SecurityAlerts} \
    --timeout 60

 Add Security Hub as event source
aws lambda add-permission \
    --function-name incident-response \
    --statement-id SecurityHubAccess \
    --action lambda:InvokeFunction \
    --principal securityhub.amazonaws.com \
    --source-arn arn:aws:securityhub:us-west-2:123456789012:product/aws/securityhub
```

 8.2. Automated Remediation
```bash
 Create Lambda function for automated remediation
cat > auto-remediation.py << 'EOF'
import json
import boto3

def lambda_handler(event, context):
     Parse CloudWatch alarm event
    alarm = event['detail']
    alarm_name = alarm['name']
    
     Take action based on alarm type
    if 'RootAccountUsage' in alarm_name:
        disable_root_access()
    elif 'IAMPolicyChanges' in alarm_name:
        review_policy_changes()
    elif 'FailedLoginAttempts' in alarm_name:
        block_suspicious_ips()
    
    return {
        'statusCode': 200,
        'body': json.dumps('Remediation executed')
    }

def disable_root_access():
    iam = boto3.client('iam')
    
     Delete root account access keys
    access_keys = iam.list_access_keys(UserName='root')
    for key in access_keys['AccessKeyMetadata']:
        iam.delete_access_key(
            UserName='root',
            AccessKeyId=key['AccessKeyId']
        )
    
     Create MFA requirement policy
    policy = {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Deny",
                "Action": "",
                "Resource": "",
                "Condition": {
                    "BoolIfExists": {
                        "aws:MultiFactorAuthPresent": "false"
                    }
                }
            }
        ]
    }
    
    iam.put_user_policy(
        UserName='root',
        PolicyName='MFARequired',
        PolicyDocument=json.dumps(policy)
    )

def review_policy_changes():
     Placeholder for policy change review
    print("Reviewing recent IAM policy changes")

def block_suspicious_ips():
    ec2 = boto3.client('ec2')
    
     Get recent failed login attempts
     This would typically involve analyzing CloudTrail logs
    suspicious_ips = ['192.0.2.100', '203.0.113.200']
    
     Create NACL rule to block suspicious IPs
    for ip in suspicious_ips:
        ec2.create-network-acl-entry(
            NetworkAclId='acl-1234567890abcdef0',
            RuleNumber=200,
            Protocol='-1',
            RuleAction='deny',
            Egress=False,
            CidrBlock=f'{ip}/32'
        )
EOF

 Create Lambda function
zip auto-remediation.zip auto-remediation.py

aws lambda create-function \
    --function-name auto-remediation \
    --runtime python3.9 \
    --role arn:aws:iam::123456789012:role/lambda-execution-role \
    --handler auto-remediation.lambda_handler \
    --zip-file fileb://auto-remediation.zip \
    --timeout 60

 Add CloudWatch as event source
aws lambda add-permission \
    --function-name auto-remediation \
    --statement-id CloudWatchAccess \
    --action lambda:InvokeFunction \
    --principal cloudwatch.amazonaws.com \
    --source-arn arn:aws:cloudwatch:us-west-2:123456789012:alarm:RootAccountUsage
```

 9. Complete Security Setup Example

 9.1. Security Infrastructure Automation
```bash
!/bin/bash
 security-setup.sh - Complete AWS security setup

set -euo pipefail

 Configuration
ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
REGION="us-west-2"
SECURITY_BUCKET="security-logs-$ACCOUNT_ID"
ALERT_TOPIC="SecurityAlerts"

echo "Setting up AWS security infrastructure..."

 Create S3 bucket for security logs
if ! aws s3 ls s3://$SECURITY_BUCKET 2>/dev/null; then
    aws s3 mb s3://$SECURITY_BUCKET --region $REGION
    echo "Created security bucket: $SECURITY_BUCKET"
fi

 Enable bucket encryption
aws s3api put-bucket-encryption \
    --bucket $SECURITY_BUCKET \
    --server-side-encryption-configuration '{
        "Rules": [
            {
                "ApplyServerSideEncryptionByDefault": {
                    "SSEAlgorithm": "AES256"
                }
            }
        ]
    }'

 Enable versioning
aws s3api put-bucket-versioning \
    --bucket $SECURITY_BUCKET \
    --versioning-configuration Status=Enabled

 Create SNS topic for alerts
aws sns create-topic --name $ALERT_TOPIC
TOPIC_ARN=$(aws sns list-topics --query 'Topics[?contains(TopicName, `'$ALERT_TOPIC'`)].TopicArn' --output text)

 Subscribe email to SNS topic
aws sns subscribe \
    --topic-arn $TOPIC_ARN \
    --protocol email \
    --notification-endpoint security-team@company.com

 Enable CloudTrail
aws cloudtrail create-trail \
    --name organization-trail \
    --s3-bucket-name $SECURITY_BUCKET \
    --s3-key-prefix cloudtrail-logs/ \
    --include-global-service-events \
    --is-multi-region-trail \
    --enable-log-file-validation \
    --kms-key-id alias/aws/s3

 Enable Config
aws configservice delivery-channel \
    --delivery-channel-name default \
    --s3-bucket-name $SECURITY_BUCKET \
    --s3-key-prefix config-logs/ \
    --sns-topic-arn $TOPIC_ARN

aws configservice configuration-recorder \
    --configuration-recorder-name default \
    --recording-group '{"allSupported":true,"includeGlobalResourceTypes":true}'

aws configservice start-configuration-recorder \
    --configuration-recorder-name default

 Enable GuardDuty
DETECTOR_ID=$(aws guardduty create-detector --enable --query DetectorId --output text)
echo "GuardDuty detector created: $DETECTOR_ID"

 Enable Security Hub
aws securityhub enable-security-hub \
    --enable-standards "arn:aws:securityhub:::ruleset/cis-aws-foundations-benchmark/v/1.2.0"

 Create security IAM policies
cat > security-policies.json << EOF
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Deny",
            "Action": "",
            "Resource": "",
            "Condition": {
                "BoolIfExists": {
                    "aws:MultiFactorAuthPresent": "false"
                }
            }
        }
    ]
}
EOF

 Create and attach MFA policy
aws iam create-policy \
    --policy-name MFARequired \
    --policy-document file://security-policies.json \
    --description "Requires MFA for all actions"

echo "Security infrastructure setup completed!"
echo "Security bucket: $SECURITY_BUCKET"
echo "SNS topic: $TOPIC_ARN"
echo "GuardDuty detector: $DETECTOR_ID"
```

 10. Resources and Best Practices

 10.1. Documentation
- [AWS Security Documentation](https://docs.aws.amazon.com/security/)
- [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)
- [KMS Developer Guide](https://docs.aws.amazon.com/kms/latest/developerguide/)
- [CloudTrail User Guide](https://docs.aws.amazon.com/cloudtrail/latest/userguide/)

 10.2. Tools and Utilities
- AWS Security Hub: Central security management
- Amazon GuardDuty: Threat detection service
- AWS Config: Configuration tracking and compliance
- AWS Audit Manager: Compliance automation
- AWS Inspector: Automated security assessments

 10.3. Best Practices
- Implement least privilege access with IAM
- Enable MFA for all users with console access
- Use IAM roles instead of long-lived credentials
- Enable encryption at rest and in transit
- Monitor and log all access and changes
- Regularly review and audit permissions
- Implement automated incident response
- Use security groups and NACLs properly
- Enable VPC Flow Logs for network monitoring
- Regularly update and patch systems

---

Next Steps: Implement these security measures in your AWS environment, set up monitoring and alerting, and create incident response procedures for your organization.
