 AWS S3 Storage: Object Storage for Data and Applications

 1. Introduction
Amazon S3 (Simple Storage Service) is the foundation of cloud storage, providing scalable object storage for data lakes, backups, static websites, and ML model storage. This lecture covers S3 fundamentals, advanced features, and practical implementations for data engineering workflows.

 2. Why S3 Storage Matters
- Scalability: Store virtually unlimited amounts of data
- Durability: 99.999999999% (11 nines) durability
- Availability: 99.99% availability for Standard storage
- Cost-Effective: Pay only for what you use with multiple storage classes
- Integration: Works seamlessly with all AWS services
- Performance: High throughput for data access and processing

 3. S3 Fundamentals

 3.1. Buckets and Objects
```bash
 S3 is organized as buckets (containers) and objects (files)
 Buckets have globally unique names
 Objects are stored with keys (paths)

 Create S3 bucket (must be globally unique)
aws s3 mb s3://my-unique-bucket-name-$(date +%s)

 List buckets
aws s3 ls

 List buckets with detailed information
aws s3api list-buckets --output table

 Bucket naming rules:
 - 3-63 characters long
 - Lowercase letters, numbers, dots, hyphens
 - Must start with letter or number
 - Cannot be formatted as IP address
 - Cannot have dots next to hyphens

 Check bucket name availability
aws s3api head-bucket --bucket my-bucket-name
```

 3.2. Object Operations
```bash
 Upload single file
aws s3 cp local-file.txt s3://my-bucket/

 Upload with metadata
aws s3 cp local-file.txt s3://my-bucket/remote-file.txt \
    --metadata author=JohnDoe,version=1.0

 Upload directory (recursive)
aws s3 sync ./local-directory s3://my-bucket/remote-directory/

 Upload with specific storage class
aws s3 cp large-file.zip s3://my-bucket/ \
    --storage-class INTELLIGENT_TIERING

 Download single file
aws s3 cp s3://my-bucket/remote-file.txt ./local-file.txt

 Download directory (recursive)
aws s3 sync s3://my-bucket/remote-directory ./local-directory/

 List objects in bucket
aws s3 ls s3://my-bucket/

 List objects recursively
aws s3 ls s3://my-bucket/ --recursive

 List objects with human-readable sizes
aws s3 ls s3://my-bucket/ --recursive --human-readable --summarize

 Delete single object
aws s3 rm s3://my-bucket/remote-file.txt

 Delete directory (recursive)
aws s3 rm s3://my-bucket/remote-directory/ --recursive

 Delete multiple objects with pattern
aws s3 rm s3://my-bucket/ --recursive --exclude "" --include ".log"
```

 4. S3 Storage Classes

 4.1. Storage Class Comparison
```bash
 Standard - Frequently accessed data
 Intelligent-Tiering - Unknown access patterns
 Standard-IA - Infrequently accessed data
 One Zone-IA - Infrequently accessed, non-critical
 Glacier - Long-term archival
 Deep Archive - Long-term archival, lowest cost

 Upload to different storage classes
aws s3 cp critical-data.csv s3://my-bucket/ --storage-class STANDARD
aws s3 cp backup-data.zip s3://my-bucket/ --storage-class STANDARD_IA
aws s3 cp archive-data.tar s3://my-bucket/ --storage-class GLACIER
aws s3 cp deep-archive-data.tar s3://my-bucket/ --storage-class DEEP_ARCHIVE

 List objects with storage class
aws s3api list-objects-v2 --bucket my-bucket \
    --query 'Contents[].{Key:Key,Size:Size,StorageClass:StorageClass}' \
    --output table

 Change storage class (copy operation)
aws s3 cp s3://my-bucket/old-data.csv s3://my-bucket/old-data.csv \
    --storage-class STANDARD_IA
```

 4.2. Intelligent Tiering
```bash
 Enable Intelligent Tiering for bucket
aws s3api put-bucket-versioning \
    --bucket my-bucket \
    --versioning-configuration Status=Enabled

 Upload with Intelligent Tiering
aws s3 cp data.csv s3://my-bucket/ --storage-class INTELLIGENT_TIERING

 Monitor access patterns
aws cloudwatch get-metric-statistics \
    --namespace AWS/S3 \
    --metric-name NumberOfObjects \
    --dimensions Name=BucketName,Value=my-bucket Name=StorageType,Value=IntelligentTiering \
    --start-time $(date -d '30 days ago' +%Y-%m-%dT%H:%M:%SZ) \
    --end-time $(date +%Y-%m-%dT%H:%M:%SZ) \
    --period 86400 \
    --statistics Average
```

 5. Versioning and Lifecycle Management

 5.1. Bucket Versioning
```bash
 Enable versioning on bucket
aws s3api put-bucket-versioning \
    --bucket my-bucket \
    --versioning-configuration Status=Enabled

 Check versioning status
aws s3api get-bucket-versioning --bucket my-bucket

 Upload multiple versions of same object
echo "Version 1" > version1.txt
aws s3 cp version1.txt s3://my-bucket/test.txt

echo "Version 2" > version2.txt
aws s3 cp version2.txt s3://my-bucket/test.txt

echo "Version 3" > version3.txt
aws s3 cp version3.txt s3://my-bucket/test.txt

 List all versions of an object
aws s3api list-object-versions \
    --bucket my-bucket \
    --prefix test.txt

 Download specific version
aws s3api get-object \
    --bucket my-bucket \
    --key test.txt \
    --version-id version_id_here \
    ./specific-version.txt

 Delete specific version
aws s3api delete-object \
    --bucket my-bucket \
    --key test.txt \
    --version-id version_id_here

 Suspend versioning
aws s3api put-bucket-versioning \
    --bucket my-bucket \
    --versioning-configuration Status=Suspended
```

 5.2. Lifecycle Policies
```bash
 Create lifecycle policy configuration
cat > lifecycle-config.json << EOF
{
  "Rules": [
    {
      "ID": "TransitionToIA",
      "Status": "Enabled",
      "Filter": {
        "Prefix": "data/"
      },
      "Transitions": [
        {
          "Days": 30,
          "StorageClass": "STANDARD_IA"
        },
        {
          "Days": 60,
          "StorageClass": "GLACIER"
        },
        {
          "Days": 180,
          "StorageClass": "DEEP_ARCHIVE"
        }
      ]
    },
    {
      "ID": "DeleteOldLogs",
      "Status": "Enabled",
      "Filter": {
        "Prefix": "logs/"
      },
      "Expiration": {
        "Days": 365
      }
    },
    {
      "ID": "CleanUpIncompleteUploads",
      "Status": "Enabled",
      "AbortIncompleteMultipartUpload": {
        "DaysAfterInitiation": 7
      }
    }
  ]
}
EOF

 Apply lifecycle configuration
aws s3api put-bucket-lifecycle-configuration \
    --bucket my-bucket \
    --lifecycle-configuration file://lifecycle-config.json

 Get lifecycle configuration
aws s3api get-bucket-lifecycle-configuration --bucket my-bucket

 List lifecycle rules
aws s3api list-bucket-lifecycle-configurations --bucket my-bucket

 Delete lifecycle configuration
aws s3api delete-bucket-lifecycle --bucket my-bucket --lifecycle-id TransitionToIA
```

 6. Access Control and Security

 6.1. Bucket Policies
```bash
 Create bucket policy for public read access
cat > public-read-policy.json << EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-bucket/public/"
    }
  ]
}
EOF

 Apply bucket policy
aws s3api put-bucket-policy \
    --bucket my-bucket \
    --policy file://public-read-policy.json

 Get bucket policy
aws s3api get-bucket-policy --bucket my-bucket

 Delete bucket policy
aws s3api delete-bucket-policy --bucket my-bucket
```

 6.2. Cross-Origin Resource Sharing (CORS)
```bash
 Create CORS configuration
cat > cors-config.json << EOF
{
  "CORSRules": [
    {
      "AllowedHeaders": [""],
      "AllowedMethods": ["GET", "PUT", "POST", "DELETE", "HEAD"],
      "AllowedOrigins": ["https://www.example.com", "https://app.example.com"],
      "ExposeHeaders": ["ETag"],
      "MaxAgeSeconds": 3000
    }
  ]
}
EOF

 Apply CORS configuration
aws s3api put-bucket-cors \
    --bucket my-bucket \
    --cors-configuration file://cors-config.json

 Get CORS configuration
aws s3api get-bucket-cors --bucket my-bucket
```

 6.3. Access Control Lists (ACLs)
```bash
 Set bucket ACL for private access
aws s3api put-bucket-acl \
    --bucket my-bucket \
    --access-control-policy file://acl-policy.json

 ACL policy example (acl-policy.json)
{
  "Owner": {
    "ID": "canonical-user-id",
    "DisplayName": "account-display-name"
  },
  "Grants": [
    {
      "Grantee": {
        "Type": "CanonicalUser",
        "DisplayName": "user-display-name",
        "CanonicalUser": "canonical-user-id"
      },
      "Permission": "READ"
    }
  ]
}

 Get object ACL
aws s3api get-object-acl \
    --bucket my-bucket \
    --key file.txt

 Set object ACL
aws s3api put-object-acl \
    --bucket my-bucket \
    --key file.txt \
    --access-control-policy file://object-acl.json
```

 7. Data Lake Architecture

 7.1. Organizing Data Lake Structure
```bash
 Create data lake structure
aws s3api put-object \
    --bucket my-datalake \
    --key raw/ \
    --body ""

aws s3api put-object \
    --bucket my-datalake \
    --key processed/ \
    --body ""

aws s3api put-object \
    --bucket my-datalake \
    --key curated/ \
    --body ""

aws s3api put-object \
    --bucket my-datalake \
    --key models/ \
    --body ""

 Upload data to different zones
 Raw data - as received from source
aws s3 cp raw-data.json s3://my-datalake/raw/2024/01/15/raw-data-$(date +%s).json

 Processed data - cleaned and transformed
aws s3 cp processed-data.parquet s3://my-datalake/processed/2024/01/15/processed-data-$(date +%s).parquet

 Curated data - final, ready for analytics
aws s3 cp curated-data.csv s3://my-datalake/curated/2024/01/15/curated-data-$(date +%s).csv

 ML models
aws s3 cp model.pkl s3://my-datalake/models/recommendation/v1/model-$(date +%s).pkl
```

 7.2. Partitioning Strategy
```bash
 Partition data by date and source
 Structure: s3://bucket/zone/year=YYYY/month=MM/day=DD/source=source_name/

 Create partitioned structure
aws s3 cp data-2024-01-15-sourceA.json \
    s3://my-datalake/raw/year=2024/month=01/day=15/source=sourceA/data-$(date +%s).json

aws s3 cp data-2024-01-15-sourceB.json \
    s3://my-datalake/raw/year=2024/month=01/day=15/source=sourceB/data-$(date +%s).json

 Query specific partitions
aws s3 ls s3://my-datalake/raw/year=2024/month=01/day=15/

 Use S3 Select to query data without downloading
aws s3api select-object-content \
    --bucket my-datalake \
    --key raw/year=2024/month=01/day=15/source=sourceA/data-123456.json \
    --expression "SELECT  FROM S3Object WHERE status = 'active'" \
    --expression-type SQL \
    --input-format JSON \
    --output-format JSON \
    query-results.json
```

 8. S3 for Machine Learning Workflows

 8.1. Model Storage and Versioning
```bash
 Create model repository structure
aws s3 mb s3://ml-models-$(date +%s)

 Upload model with metadata
MODEL_BUCKET="ml-models-$(date +%s)"
MODEL_NAME="recommendation-model"
MODEL_VERSION="v1.0.0"

 Upload model files
aws s3 cp model.pkl s3://$MODEL_BUCKET/$MODEL_NAME/$MODEL_VERSION/model.pkl
aws s3 cp config.json s3://$MODEL_BUCKET/$MODEL_NAME/$MODEL_VERSION/config.json
aws s3 cp requirements.txt s3://$MODEL_BUCKET/$MODEL_NAME/$MODEL_VERSION/requirements.txt

 Add model metadata
cat > model-metadata.json << EOF
{
  "model_name": "$MODEL_NAME",
  "version": "$MODEL_VERSION",
  "created_at": "$(date -Iseconds)",
  "created_by": "data-science-team",
  "framework": "scikit-learn",
  "accuracy": 0.95,
  "features": ["feature1", "feature2", "feature3"],
  "training_dataset": "s3://training-data/dataset-v1.csv"
}
EOF

aws s3 cp model-metadata.json s3://$MODEL_BUCKET/$MODEL_NAME/$MODEL_VERSION/metadata.json

 List all model versions
aws s3 ls s3://$MODEL_BUCKET/$MODEL_NAME/ --recursive

 Download specific model version
aws s3 sync s3://$MODEL_BUCKET/$MODEL_NAME/$MODEL_VERSION/ ./model/
```

 8.2. Training Data Management
```bash
 Create training data structure
TRAINING_BUCKET="ml-training-data-$(date +%s)"
aws s3 mb s3://$TRAINING_BUCKET

 Upload training datasets with versioning
aws s3api put-bucket-versioning \
    --bucket $TRAINING_BUCKET \
    --versioning-configuration Status=Enabled

 Upload datasets
aws s3 cp training-data.csv s3://$TRAINING_BUCKET/datasets/v1/training-data.csv
aws s3 cp validation-data.csv s3://$TRAINING_BUCKET/datasets/v1/validation-data.csv
aws s3 cp test-data.csv s3://$TRAINING_BUCKET/datasets/v1/test-data.csv

 Create dataset manifest
cat > dataset-manifest.json << EOF
{
  "training_data": "s3://$TRAINING_BUCKET/datasets/v1/training-data.csv",
  "validation_data": "s3://$TRAINING_BUCKET/datasets/v1/validation-data.csv",
  "test_data": "s3://$TRAINING_BUCKET/datasets/v1/test-data.csv",
  "created_at": "$(date -Iseconds)",
  "total_samples": 10000,
  "features": 20,
  "target_column": "target"
}
EOF

aws s3 cp dataset-manifest.json s3://$TRAINING_BUCKET/datasets/v1/manifest.json
```

 9. S3 Integration with Other Services

 9.1. S3 and AWS Glue
```bash
 Create Glue database
aws glue create-database \
    --database-input '{"Name":"my_datalake_db"}'

 Create Glue table for S3 data
aws glue create-table \
    --database-name my_datalake_db \
    --table-input '{
        "Name": "raw_data",
        "StorageDescriptor": {
            "Columns": [
                {"Name": "id", "Type": "int"},
                {"Name": "name", "Type": "string"},
                {"Name": "value", "Type": "double"},
                {"Name": "timestamp", "Type": "timestamp"}
            ],
            "Location": "s3://my-datalake/raw/",
            "InputFormat": "org.apache.hadoop.mapred.TextInputFormat",
            "OutputFormat": "org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat",
            "SerdeInfo": {
                "SerializationLibrary": "org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe",
                "Parameters": {
                    "field.delim": ","
                }
            }
        },
        "PartitionKeys": [
            {"Name": "year", "Type": "string"},
            {"Name": "month", "Type": "string"},
            {"Name": "day", "Type": "string"}
        ]
    }'

 Run Glue crawler
aws glue create-crawler \
    --name my-datalake-crawler \
    --role AWSGlueServiceRole \
    --database-name my_datalake_db \
    --targets '{"S3Targets": [{"Path": "s3://my-datalake/"}]}'

 Start crawler
aws glue start-crawler --name my-datalake-crawler
```

 9.2. S3 and Athena
```bash
 Query S3 data with Athena
aws athena start-query-execution \
    --query-string "SELECT  FROM my_datalake_db.raw_data WHERE year = '2024' LIMIT 10" \
    --query-execution-context '{"Database":"my_datalake_db"}' \
    --result-configuration '{"OutputLocation":"s3://my-athena-results/"}'

 Get query results
QUERY_EXECUTION_ID=$(aws athena start-query-execution \
    --query-string "SELECT COUNT() as total_records FROM my_datalake_db.raw_data" \
    --query-execution-context '{"Database":"my_datalake_db"}' \
    --result-configuration '{"OutputLocation":"s3://my-athena-results/"}' \
    --query 'QueryExecutionId' \
    --output text)

 Wait for query to complete
aws athena get-query-execution --query-execution-id $QUERY_EXECUTION_ID

 Get results
aws s3 cp s3://my-athena-results/$QUERY_EXECUTION_ID.csv ./query-results.csv
```

 10. Performance Optimization

 10.1. Multipart Upload
```bash
 Enable multipart upload for large files
 Automatically used by AWS CLI for files > 100MB

 Manual multipart upload
UPLOAD_ID=$(aws s3api create-multipart-upload \
    --bucket my-bucket \
    --key large-file.zip \
    --query 'UploadId' \
    --output text)

 Split file into parts and upload
PART_SIZE=10485760   10MB
PART_NUMBER=1

for PART in $(split -b $PART_SIZE large-file.zip part-); do
    aws s3api upload-part \
        --bucket my-bucket \
        --key large-file.zip \
        --part-number $PART_NUMBER \
        --upload-id $UPLOAD_ID \
        --body $PART
    
    ((PART_NUMBER++))
done

 Complete multipart upload
aws s3api complete-multipart-upload \
    --bucket my-bucket \
    --key large-file.zip \
    --upload-id $UPLOAD_ID \
    --multipart-upload file://parts.json
```

 10.2. S3 Transfer Acceleration
```bash
 Enable transfer acceleration
aws s3api put-bucket-accelerate-configuration \
    --bucket my-bucket \
    --accelerate-configuration Status=Enabled

 Use accelerated endpoint
aws s3 cp large-file.zip s3://my-bucket/ --endpoint-url https://s3-accelerate.amazonaws.com

 Check acceleration status
aws s3api get-bucket-accelerate-configuration --bucket my-bucket
```

 11. Monitoring and Cost Management

 11.1. S3 Monitoring
```bash
 Enable S3 server access logging
aws s3api put-bucket-logging \
    --bucket my-bucket \
    --bucket-logging-status '{
        "LoggingEnabled": {
            "TargetBucket": "my-logs-bucket",
            "TargetPrefix": "access-logs/"
        }
    }'

 Create CloudWatch metrics for S3
aws cloudwatch put-metric-alarm \
    --alarm-name S3BucketSize \
    --alarm-description "Monitor S3 bucket size" \
    --metric-name BucketSizeBytes \
    --namespace AWS/S3 \
    --statistic Average \
    --period 86400 \
    --threshold 100000000000 \
    --comparison-operator GreaterThanThreshold \
    --dimensions Name=BucketName,Value=my-bucket Name=StorageType,Value=StandardStorage

 Get S3 storage metrics
aws cloudwatch get-metric-statistics \
    --namespace AWS/S3 \
    --metric-name BucketSizeBytes \
    --dimensions Name=BucketName,Value=my-bucket \
    --start-time $(date -d '7 days ago' +%Y-%m-%dT%H:%M:%SZ) \
    --end-time $(date +%Y-%m-%dT%H:%M:%SZ) \
    --period 86400 \
    --statistics Average
```

 11.2. Cost Optimization
```bash
 Analyze storage costs
aws ce get-cost-and-usage \
    --time-period Start=$(date -d '30 days ago' +%Y-%m-%d),End=$(date +%Y-%m-%d) \
    --granularity MONTHLY \
    --metrics BlendedCost \
    --group-by Type=DIMENSION,Key=SERVICE,Type=DIMENSION,Key=USAGE_TYPE \
    --filter '{"Dimensions": {"Key": "SERVICE", "Values": ["Amazon Simple Storage Service"]}}'

 Enable S3 Inventory for cost analysis
aws s3api put-bucket-inventory-configuration \
    --bucket my-bucket \
    --id weekly-inventory \
    --inventory-configuration '{
        "Destination": {
            "S3BucketDestination": {
                "Bucket": "my-inventory-bucket",
                "Prefix": "inventory/"
            }
        },
        "IsEnabled": true,
        "Schedule": {
            "Frequency": "Weekly"
        },
        "Format": "CSV",
        "IncludedObjectVersions": "Current"
    }'

 Analyze object sizes and access patterns
aws s3api list-objects-v2 \
    --bucket my-bucket \
    --query 'Contents[].{Key:Key,Size:Size,LastModified:LastModified}' \
    --output table > inventory.csv
```

 12. Advanced S3 Features

 12.1. S3 Event Notifications
```bash
 Create S3 event notification
aws s3api put-bucket-notification-configuration \
    --bucket my-bucket \
    --notification-configuration '{
        "LambdaFunctionConfigurations": [
            {
                "LambdaFunctionArn": "arn:aws:lambda:us-west-2:123456789012:function:process-file",
                "Events": ["s3:ObjectCreated:"],
                "Filter": {
                    "Key": {
                        "FilterRules": [
                            {
                                "Name": "suffix",
                                "Value": "csv"
                            }
                        ]
                    }
                }
            }
        ]
    }'

 Create SQS notification
aws s3api put-bucket-notification-configuration \
    --bucket my-bucket \
    --notification-configuration '{
        "QueueConfigurations": [
            {
                "QueueArn": "arn:aws:sqs:us-west-2:123456789012:my-queue",
                "Events": ["s3:ObjectCreated:"]
            }
        ]
    }'
```

 12.2. S3 Select and Glacier
```bash
 Use S3 Select to query data
aws s3api select-object-content \
    --bucket my-bucket \
    --key large-dataset.json \
    --expression "SELECT  FROM S3Object WHERE category = 'electronics'" \
    --expression-type SQL \
    --input-format JSON \
    --output-format JSON \
    --output-location 's3://my-results-bucket/query-results/'

 Restore from Glacier
aws s3api restore-object \
    --bucket my-bucket \
    --key archived-data.zip \
    --restore-request '{
        "Days": 30,
        "GlacierJobParameters": {
            "Tier": "Expedited"
        }
    }'

 Check restore status
aws s3api head-object \
    --bucket my-bucket \
    --key archived-data.zip \
    --query 'Restore'
```

 13. Complete S3 Automation Example

 13.1. Data Pipeline Automation
```bash
!/bin/bash
 s3-data-pipeline.sh - Complete S3 data pipeline automation

set -euo pipefail

 Configuration
SOURCE_BUCKET="source-data-$(date +%s)"
PROCESSING_BUCKET="processing-data-$(date +%s)"
RESULTS_BUCKET="results-data-$(date +%s)"
REGION="us-west-2"

echo "Setting up S3 data pipeline..."

 Create buckets
aws s3 mb s3://$SOURCE_BUCKET --region $REGION
aws s3 mb s3://$PROCESSING_BUCKET --region $REGION
aws s3 mb s3://$RESULTS_BUCKET --region $REGION

 Enable versioning and logging
for bucket in $SOURCE_BUCKET $PROCESSING_BUCKET $RESULTS_BUCKET; do
    aws s3api put-bucket-versioning \
        --bucket $bucket \
        --versioning-configuration Status=Enabled
    
    aws s3api put-bucket-logging \
        --bucket $bucket \
        --bucket-logging-status '{
            "LoggingEnabled": {
                "TargetBucket": "'$bucket'-logs",
                "TargetPrefix": "access-logs/"
            }
        }'
done

 Create lifecycle policies
cat > source-lifecycle.json << EOF
{
  "Rules": [
    {
      "ID": "ArchiveOldSourceData",
      "Status": "Enabled",
      "Transitions": [
        {
          "Days": 30,
          "StorageClass": "STANDARD_IA"
        },
        {
          "Days": 90,
          "StorageClass": "GLACIER"
        }
      ]
    }
  ]
}
EOF

aws s3api put-bucket-lifecycle-configuration \
    --bucket $SOURCE_BUCKET \
    --lifecycle-configuration file://source-lifecycle.json

 Create processing bucket lifecycle
cat > processing-lifecycle.json << EOF
{
  "Rules": [
    {
      "ID": "DeleteProcessingData",
      "Status": "Enabled",
      "Expiration": {
        "Days": 7
      }
    }
  ]
}
EOF

aws s3api put-bucket-lifecycle-configuration \
    --bucket $PROCESSING_BUCKET \
    --lifecycle-configuration file://processing-lifecycle.json

 Set up event notifications
aws s3api put-bucket-notification-configuration \
    --bucket $SOURCE_BUCKET \
    --notification-configuration '{
        "LambdaFunctionConfigurations": [
            {
                "LambdaFunctionArn": "arn:aws:lambda:'$REGION':123456789012:function:process-data",
                "Events": ["s3:ObjectCreated:"]
            }
        ]
    }'

 Create bucket policies
cat > source-bucket-policy.json << EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowLambdaAccess",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:role/lambda-execution-role"
      },
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": [
        "arn:aws:s3:::$SOURCE_BUCKET/",
        "arn:aws:s3:::$PROCESSING_BUCKET/"
      ]
    }
  ]
}
EOF

aws s3api put-bucket-policy \
    --bucket $SOURCE_BUCKET \
    --policy file://source-bucket-policy.json

 Upload sample data
echo "id,name,value,category" > sample-data.csv
echo "1,Product A,100.50,electronics" >> sample-data.csv
echo "2,Product B,75.25,clothing" >> sample-data.csv
echo "3,Product C,200.00,electronics" >> sample-data.csv

aws s3 cp sample-data.csv s3://$SOURCE_BUCKET/input/sample-data.csv

echo "S3 data pipeline setup completed!"
echo "Source bucket: $SOURCE_BUCKET"
echo "Processing bucket: $PROCESSING_BUCKET"
echo "Results bucket: $RESULTS_BUCKET"
echo "Region: $REGION"

 Clean up function
cleanup() {
    echo "Cleaning up buckets..."
    aws s3 rb s3://$SOURCE_BUCKET --force
    aws s3 rb s3://$PROCESSING_BUCKET --force
    aws s3 rb s3://$RESULTS_BUCKET --force
    rm -f sample-data.csv source-lifecycle.json processing-lifecycle.json source-bucket-policy.json
}

 Uncomment to clean up
 cleanup
```

 14. Resources and Best Practices

 14.1. Documentation
- [Amazon S3 Documentation](https://docs.aws.amazon.com/s3/)
- [S3 Storage Classes Guide](https://docs.aws.amazon.com/s3/storage-classes/)
- [S3 Developer Guide](https://docs.aws.amazon.com/AmazonS3/latest/dev/)

 14.2. Tools and Utilities
- AWS CLI: Command-line interface for S3 operations
- Boto3: Python SDK for S3
- S3DistCp: Distributed copy for large datasets
- AWS DataSync: Fast data transfer service
- AWS Transfer Family: Managed file transfer services

 14.3. Best Practices
- Use appropriate storage classes for cost optimization
- Implement proper naming conventions and folder structures
- Enable versioning for critical data
- Use lifecycle policies to manage data retention
- Monitor costs and usage patterns
- Implement proper access controls and encryption
- Use S3 event notifications for automation
- Design for high availability with cross-region replication

---
