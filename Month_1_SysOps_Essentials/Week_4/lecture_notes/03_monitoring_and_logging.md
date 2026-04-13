 Monitoring and Logging: Observability for Modern Applications

 1. Introduction
Monitoring and logging are critical components of modern software systems, providing visibility into application performance, health, and behavior. This lecture covers observability fundamentals, metrics collection, log aggregation, alerting strategies, and dashboard creation for cloud-native applications and data pipelines.

 2. Why Observability Matters
- Visibility: Understand system behavior and performance
- Troubleshooting: Quickly identify and resolve issues
- Performance: Optimize resource usage and response times
- Reliability: Ensure service availability and meet SLAs
- Security: Detect and investigate security incidents
- Business Insights: Make data-driven decisions

 3. Observability Pillars

 3.1. Metrics, Logs, and Traces
```
Three Pillars of Observability:

1. METRICS - Numerical data over time
   - CPU usage, memory consumption
   - Request latency, throughput
   - Error rates, queue depths
   - Business metrics (revenue, signups)

2. LOGS - Discrete events with timestamps
   - Application errors
   - Access logs
   - Audit trails
   - Debug information

3. TRACES - Request flow through systems
   - Distributed tracing
   - Service dependencies
   - Latency breakdown
   - Request paths

Relationships:
- Metrics alert you to problems
- Logs help you diagnose issues
- Traces show you where problems occur
```

 3.2. Observability Architecture
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Application   │    │   Application   │    │   Application   │
│   (Metrics)     │    │   (Logs)        │    │   (Traces)      │
└────────┬────────┘    └────────┬────────┘    └────────┬────────┘
         │                      │                      │
         ▼                      ▼                      ▼
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│  Prometheus/    │    │  Fluentd/       │    │  Jaeger/        │
│  CloudWatch     │    │  Logstash       │    │  Zipkin         │
│  (Collection)   │    │  (Collection)   │    │  (Collection)   │
└────────┬────────┘    └────────┬────────┘    └────────┬────────┘
         │                      │                      │
         ▼                      ▼                      ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Central Storage & Analysis                    │
│              (Prometheus, Elasticsearch, ClickHouse)             │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                     Visualization & Alerting                       │
│                     (Grafana, Kibana, PagerDuty)                │
└─────────────────────────────────────────────────────────────────┘
```

 4. Metrics Collection

 4.1. Prometheus Fundamentals
```yaml
 prometheus.yml - Prometheus configuration
global:
  scrape_interval: 15s
  evaluation_interval: 15s

alerting:
  alertmanagers:
    - static_configs:
        - targets:
          - alertmanager:9093

rule_files:
  - "alert_rules.yml"

scrape_configs:
   Prometheus self-monitoring
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]

   Node exporter (system metrics)
  - job_name: "node"
    static_configs:
      - targets: ["node-exporter:9100"]

   Application metrics
  - job_name: "myapp"
    static_configs:
      - targets: ["myapp:8080"]
    metrics_path: /metrics
    scrape_interval: 5s

   Docker metrics
  - job_name: "docker"
    static_configs:
      - targets: ["docker-exporter:9323"]

   Kubernetes metrics
  - job_name: "kubernetes-pods"
    kubernetes_sd_configs:
      - role: pod
    relabel_configs:
      - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
        action: keep
        regex: true
```

```yaml
 alert_rules.yml - Prometheus alerting rules
groups:
  - name: application
    rules:
      - alert: HighErrorRate
        expr: rate(http_requests_total{status=~"5.."}[5m]) > 0.05
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "High error rate detected"
          description: "Error rate is {{ $value }}% for {{ $labels.instance }}"

      - alert: HighLatency
        expr: histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m])) > 0.5
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "High latency detected"
          description: "95th percentile latency is {{ $value }}s"

      - alert: LowDiskSpace
        expr: (node_filesystem_avail_bytes / node_filesystem_size_bytes) < 0.1
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "Low disk space"
          description: "Less than 10% disk space remaining on {{ $labels.device }}"

      - alert: HighMemoryUsage
        expr: (node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes > 0.85
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "High memory usage"
          description: "Memory usage is above 85%"

      - alert: InstanceDown
        expr: up == 0
        for: 1m
        labels:
          severity: critical
        annotations:
          summary: "Instance {{ $labels.instance }} down"
          description: "{{ $labels.instance }} has been down for more than 1 minute"
```

 4.2. Instrumenting Applications
```python
 Python application with Prometheus metrics
from prometheus_client import Counter, Histogram, Gauge, start_http_server
import time
import random

 Define metrics
REQUEST_COUNT = Counter(
    'http_requests_total',
    'Total HTTP requests',
    ['method', 'endpoint', 'status']
)

REQUEST_LATENCY = Histogram(
    'http_request_duration_seconds',
    'HTTP request latency',
    ['method', 'endpoint'],
    buckets=[0.1, 0.5, 1.0, 2.0, 5.0]
)

ACTIVE_CONNECTIONS = Gauge(
    'active_connections',
    'Number of active connections'
)

QUEUE_SIZE = Gauge(
    'queue_size',
    'Current queue size'
)

class MetricsMiddleware:
    def __init__(self, app):
        self.app = app
    
    def __call__(self, environ, start_response):
        method = environ.get('REQUEST_METHOD')
        path = environ.get('PATH_INFO', '/')
        
        ACTIVE_CONNECTIONS.inc()
        start_time = time.time()
        
        try:
            response = self.app(environ, start_response)
            status = '200'
            return response
        except Exception as e:
            status = '500'
            raise
        finally:
            duration = time.time() - start_time
            REQUEST_COUNT.labels(method=method, endpoint=path, status=status).inc()
            REQUEST_LATENCY.labels(method=method, endpoint=path).observe(duration)
            ACTIVE_CONNECTIONS.dec()

 Start metrics server
if __name__ == '__main__':
    start_http_server(8080)
    print("Metrics server started on port 8080")
```

```bash
!/bin/bash
 setup-prometheus.sh - Setup Prometheus monitoring

 Create directories
mkdir -p ~/prometheus/{data,config}

 Download Prometheus
cd ~/prometheus
wget https://github.com/prometheus/prometheus/releases/download/v2.45.0/prometheus-2.45.0.linux-amd64.tar.gz
tar xvfz prometheus-2.45.0.linux-amd64.tar.gz
cd prometheus-2.45.0.linux-amd64

 Create prometheus config
cat > ~/prometheus/config/prometheus.yml << 'EOF'
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']
  
  - job_name: 'node-exporter'
    static_configs:
      - targets: ['localhost:9100']
EOF

 Start Prometheus
./prometheus \
    --config.file=~/prometheus/config/prometheus.yml \
    --storage.tsdb.path=~/prometheus/data \
    --web.console.templates=consoles \
    --web.console.libraries=console_libraries

 Install Node Exporter (system metrics)
wget https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz
tar xvfz node_exporter-1.6.1.linux-amd64.tar.gz
cd node_exporter-1.6.1.linux-amd64
./node_exporter
```

 5. Log Aggregation

 5.1. ELK Stack Configuration
```yaml
 docker-compose.yml - ELK Stack setup
version: '3.8'

services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:8.11.0
    container_name: elasticsearch
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ports:
      - "9200:9200"
      - "9300:9300"
    volumes:
      - elasticsearch_data:/usr/share/elasticsearch/data
    networks:
      - elk

  logstash:
    image: docker.elastic.co/logstash/logstash:8.11.0
    container_name: logstash
    volumes:
      - ./logstash/pipeline:/usr/share/logstash/pipeline
      - ./logstash/config:/usr/share/logstash/config
    ports:
      - "5044:5044"
      - "5000:5000/tcp"
      - "5000:5000/udp"
      - "9600:9600"
    environment:
      LS_JAVA_OPTS: "-Xmx256m -Xms256m"
    networks:
      - elk
    depends_on:
      - elasticsearch

  kibana:
    image: docker.elastic.co/kibana/kibana:8.11.0
    container_name: kibana
    ports:
      - "5601:5601"
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
    networks:
      - elk
    depends_on:
      - elasticsearch

  filebeat:
    image: docker.elastic.co/beats/filebeat:8.11.0
    container_name: filebeat
    volumes:
      - ./filebeat/filebeat.yml:/usr/share/filebeat/filebeat.yml:ro
      - /var/log:/var/log:ro
      - /var/lib/docker/containers:/var/lib/docker/containers:ro
    networks:
      - elk
    depends_on:
      - logstash
      - elasticsearch
      - kibana

volumes:
  elasticsearch_data:
    driver: local

networks:
  elk:
    driver: bridge
```

```yaml
 logstash/pipeline/logstash.conf
input {
  beats {
    port => 5044
  }
  
  tcp {
    port => 5000
    codec => json
  }
}

filter {
   Parse JSON logs
  if [message] =~ /^\{/ {
    json {
      source => "message"
      target => "parsed"
    }
  }
  
   Parse nginx access logs
  if [fields][log_type] == "nginx_access" {
    grok {
      match => { 
        "message" => "%{COMBINEDAPACHELOG}"
      }
    }
    date {
      match => [ "timestamp", "dd/MMM/yyyy:HH:mm:ss Z" ]
      target => "@timestamp"
    }
  }
  
   Add environment tag
  mutate {
    add_field => { "environment" => "%{[fields][env]}" }
  }
  
   Remove unnecessary fields
  mutate {
    remove_field => [ "host", "agent", "ecs", "input", "log" ]
  }
}

output {
  elasticsearch {
    hosts => ["elasticsearch:9200"]
    index => "%{[fields][log_type]}-%{+yyyy.MM.dd}"
  }
  
  stdout {
    codec => rubydebug
  }
}
```

```yaml
 filebeat/filebeat.yml
filebeat.inputs:
  - type: log
    enabled: true
    paths:
      - /var/log/.log
      - /var/log/syslog
    fields:
      log_type: system
      env: production
    fields_under_root: true

  - type: log
    enabled: true
    paths:
      - /var/lib/docker/containers//.log
    json.message_key: log
    json.keys_under_root: true
    fields:
      log_type: docker
      env: production
    fields_under_root: true

  - type: log
    enabled: true
    paths:
      - /var/log/nginx/.log
    fields:
      log_type: nginx_access
      env: production
    fields_under_root: true

output.logstash:
  hosts: ["logstash:5044"]

logging.to_files: true
logging.files:
  path: /var/log/filebeat
  name: filebeat
  keepfiles: 7
  permissions: 0644
```

 5.2. Structured Logging
```python
 Python structured logging with JSON format
import json
import logging
import sys
from datetime import datetime
from pythonjsonlogger import jsonlogger

 Configure JSON logging
logHandler = logging.StreamHandler(sys.stdout)
formatter = jsonlogger.JsonFormatter(
    '%(timestamp)s %(level)s %(name)s %(message)s',
    rename_fields={'levelname': 'level', 'asctime': 'timestamp'}
)
logHandler.setFormatter(formatter)

logger = logging.getLogger()
logger.addHandler(logHandler)
logger.setLevel(logging.INFO)

def log_event(event_type, kwargs):
    """Log structured event with additional context"""
    log_data = {
        'event_type': event_type,
        'timestamp': datetime.utcnow().isoformat(),
        'service': 'myapp',
        'environment': 'production'
    }
    log_data.update(kwargs)
    
    logger.info(json.dumps(log_data))

 Usage examples
log_event('user_login', user_id='12345', ip_address='192.168.1.1')
log_event('order_created', order_id='ORD-123', amount=99.99, currency='USD')
log_event('api_request', method='GET', endpoint='/api/users', duration_ms=150)
```

```bash
!/bin/bash
 log-analysis.sh - Analyze log files

 Real-time log monitoring
tail -f /var/log/myapp/application.log | jq '.'

 Search for errors in logs
grep -i "error\|exception\|fatal" /var/log/myapp/application.log

 Count errors by hour
awk '/ERROR/{print $1}' /var/log/myapp/application.log | \
    cut -d: -f1 | \
    sort | \
    uniq -c | \
    sort -rn

 Extract unique error messages
grep "ERROR" /var/log/myapp/application.log | \
    sed 's/.ERROR: //' | \
    sort | \
    uniq -c | \
    sort -rn | \
    head -20

 Parse JSON logs and extract specific fields
cat /var/log/myapp/application.json | \
    jq -r 'select(.level == "ERROR") | [.timestamp, .message, .stack_trace] | @tsv'

 Generate daily log summary report
!/bin/bash
 daily-log-report.sh

LOG_FILE="/var/log/myapp/application.log"
REPORT_DATE=$(date +%Y-%m-%d)
REPORT_FILE="/tmp/log-report-${REPORT_DATE}.txt"

echo "Log Report for ${REPORT_DATE}" > $REPORT_FILE
echo "================================" >> $REPORT_FILE
echo "" >> $REPORT_FILE

echo "Total Log Entries:" >> $REPORT_FILE
wc -l $LOG_FILE >> $REPORT_FILE
echo "" >> $REPORT_FILE

echo "Error Count by Level:" >> $REPORT_FILE
grep -oP 'level":"\K[^"]+' $LOG_FILE | sort | uniq -c | sort -rn >> $REPORT_FILE
echo "" >> $REPORT_FILE

echo "Top 10 Error Messages:" >> $REPORT_FILE
grep "ERROR" $LOG_FILE | sed 's/.message":"//;s/".//' | \
    sort | uniq -c | sort -rn | head -10 >> $REPORT_FILE

cat $REPORT_FILE
```

 6. Dashboards and Visualization

 6.1. Grafana Dashboard Configuration
```json
{
  "dashboard": {
    "title": "Application Monitoring",
    "tags": ["monitoring", "application"],
    "timezone": "browser",
    "schemaVersion": 16,
    "version": 1,
    "refresh": "5s",
    "panels": [
      {
        "title": "Request Rate",
        "type": "graph",
        "targets": [
          {
            "expr": "rate(http_requests_total[5m])",
            "legendFormat": "{{method}} {{endpoint}}",
            "refId": "A"
          }
        ],
        "gridPos": {"h": 8, "w": 12, "x": 0, "y": 0}
      },
      {
        "title": "Error Rate",
        "type": "graph",
        "targets": [
          {
            "expr": "rate(http_requests_total{status=~\"5..\"}[5m])",
            "legendFormat": "Errors",
            "refId": "A"
          }
        ],
        "alert": {
          "name": "High Error Rate",
          "conditions": [
            {
              "query": {"queryId": "A", "from": "5m", "to": "now"},
              "reducer": {"type": "avg", "params": []},
              "evaluator": {"params": [0.05], "type": "gt"}
            }
          ]
        },
        "gridPos": {"h": 8, "w": 12, "x": 12, "y": 0}
      },
      {
        "title": "Response Time (95th percentile)",
        "type": "graph",
        "targets": [
          {
            "expr": "histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))",
            "legendFormat": "95th percentile",
            "refId": "A"
          }
        ],
        "gridPos": {"h": 8, "w": 12, "x": 0, "y": 8}
      },
      {
        "title": "System Resources",
        "type": "graph",
        "targets": [
          {
            "expr": "100 - (avg(irate(node_cpu_seconds_total{mode=\"idle\"}[5m]))  100)",
            "legendFormat": "CPU Usage %",
            "refId": "A"
          },
          {
            "expr": "(node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes  100",
            "legendFormat": "Memory Usage %",
            "refId": "B"
          }
        ],
        "gridPos": {"h": 8, "w": 12, "x": 12, "y": 8}
      }
    ]
  }
}
```

```bash
!/bin/bash
 setup-grafana.sh - Setup Grafana dashboards

 Install Grafana
cat > docker-compose.grafana.yml << 'EOF'
version: '3.8'

services:
  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
      - GF_USERS_ALLOW_SIGN_UP=false
    volumes:
      - grafana_data:/var/lib/grafana
      - ./grafana/dashboards:/etc/grafana/provisioning/dashboards
      - ./grafana/datasources:/etc/grafana/provisioning/datasources
    networks:
      - monitoring

volumes:
  grafana_data:
    driver: local

networks:
  monitoring:
    driver: bridge
EOF

 Create datasource configuration
mkdir -p grafana/datasources
cat > grafana/datasources/prometheus.yml << 'EOF'
apiVersion: 1

datasources:
  - name: Prometheus
    type: prometheus
    access: proxy
    url: http://prometheus:9090
    isDefault: true
    editable: false
EOF

 Start Grafana
docker-compose -f docker-compose.grafana.yml up -d

echo "Grafana available at http://localhost:3000"
echo "Default credentials: admin/admin"
```

 7. Alerting and Incident Response

 7.1. Alertmanager Configuration
```yaml
 alertmanager.yml
route:
  group_by: ['alertname', 'severity']
  group_wait: 10s
  group_interval: 10s
  repeat_interval: 1h
  receiver: 'default'
  routes:
    - match:
        severity: critical
      receiver: 'pagerduty'
      continue: true
    - match:
        severity: warning
      receiver: 'slack'
      continue: true

receivers:
  - name: 'default'
    email_configs:
      - to: 'oncall@company.com'
        from: 'alerts@company.com'
        smarthost: 'smtp.gmail.com:587'
        auth_username: 'alerts@company.com'
        auth_password: '${SMTP_PASSWORD}'

  - name: 'slack'
    slack_configs:
      - api_url: '${SLACK_WEBHOOK_URL}'
        channel: 'alerts'
        title: '{{ range .Alerts }}{{ .Annotations.summary }}{{ end }}'
        text: '{{ range .Alerts }}{{ .Annotations.description }}{{ end }}'

  - name: 'pagerduty'
    pagerduty_configs:
      - service_key: '${PAGERDUTY_KEY}'
        severity: critical

inhibit_rules:
  - source_match:
      severity: 'critical'
    target_match:
      severity: 'warning'
    equal: ['alertname', 'instance']
```

```bash
!/bin/bash
 alerting-setup.sh - Setup alerting infrastructure

 Install Alertmanager
cd /opt
wget https://github.com/prometheus/alertmanager/releases/download/v0.26.0/alertmanager-0.26.0.linux-amd64.tar.gz
tar xvfz alertmanager-0.26.0.linux-amd64.tar.gz

 Create alertmanager config
cat > /opt/alertmanager/alertmanager.yml << EOF
global:
  smtp_smarthost: 'localhost:587'
  smtp_from: 'alerts@company.com'

route:
  group_by: ['alertname']
  group_wait: 10s
  group_interval: 10s
  repeat_interval: 1h
  receiver: 'web.hook'

receivers:
  - name: 'web.hook'
    webhook_configs:
      - url: 'http://localhost:5001/webhook'
        send_resolved: true
EOF

 Start Alertmanager
./alertmanager --config.file=/opt/alertmanager/alertmanager.yml &

 Create webhook receiver script
cat > /opt/alertmanager/webhook-receiver.py << 'EOF'
!/usr/bin/env python3
from flask import Flask, request, jsonify
import json
from datetime import datetime

app = Flask(__name__)

@app.route('/webhook', methods=['POST'])
def webhook():
    data = request.json
    
     Log alert
    with open('/var/log/alerts.log', 'a') as f:
        f.write(f"{datetime.now().isoformat()}: {json.dumps(data)}\n")
    
     Process alerts
    for alert in data.get('alerts', []):
        status = alert.get('status')
        labels = alert.get('labels', {})
        annotations = alert.get('annotations', {})
        
        print(f"Alert {status}: {labels.get('alertname')}")
        print(f"  Summary: {annotations.get('summary')}")
        print(f"  Description: {annotations.get('description')}")
        
         TODO: Implement notification logic (email, Slack, PagerDuty)
    
    return jsonify({"status": "success"}), 200

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5001)
EOF

 Run webhook receiver
python3 /opt/alertmanager/webhook-receiver.py &
```

 8. Cloud Monitoring (AWS CloudWatch)

 8.1. CloudWatch Setup
```bash
!/bin/bash
 cloudwatch-monitoring.sh - AWS CloudWatch monitoring setup

 Install CloudWatch agent
cd /opt
wget https://s3.amazonaws.com/amazoncloudwatch-agent/linux/amd64/latest/AmazonCloudWatchAgentPackage.zip
unzip AmazonCloudWatchAgentPackage.zip
./install.sh

 Create CloudWatch agent configuration
cat > /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json << 'EOF'
{
  "metrics": {
    "namespace": "MyApp/Production",
    "metrics_collected": {
      "cpu": {
        "measurement": ["cpu_usage_idle", "cpu_usage_iowait", "cpu_usage_user", "cpu_usage_system"],
        "metrics_collection_interval": 60,
        "totalcpu": true
      },
      "disk": {
        "measurement": ["used_percent", "inodes_free"],
        "metrics_collection_interval": 60,
        "resources": [""]
      },
      "diskio": {
        "measurement": ["io_time", "write_bytes", "read_bytes", "writes", "reads"],
        "metrics_collection_interval": 60
      },
      "mem": {
        "measurement": ["mem_used_percent"],
        "metrics_collection_interval": 60
      },
      "netstat": {
        "measurement": ["tcp_established", "tcp_time_wait"],
        "metrics_collection_interval": 60
      },
      "statsd": {
        "service_address": ":8125",
        "metrics_collection_interval": 60,
        "metrics_aggregation_interval": 60
      }
    }
  },
  "logs": {
    "logs_collected": {
      "files": {
        "collect_list": [
          {
            "file_path": "/var/log/myapp/application.log",
            "log_group_name": "MyApp/Application",
            "log_stream_name": "{instance_id}",
            "timezone": "UTC"
          },
          {
            "file_path": "/var/log/nginx/access.log",
            "log_group_name": "MyApp/Nginx/Access",
            "log_stream_name": "{instance_id}",
            "timezone": "UTC"
          }
        ]
      }
    }
  }
}
EOF

 Start CloudWatch agent
/opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -s -c file:/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json

 Create CloudWatch alarms
aws cloudwatch put-metric-alarm \
    --alarm-name HighCPUUtilization \
    --alarm-description "Alarm when CPU exceeds 80%" \
    --metric-name CPUUtilization \
    --namespace AWS/EC2 \
    --statistic Average \
    --period 300 \
    --threshold 80 \
    --comparison-operator GreaterThanThreshold \
    --dimensions Name=InstanceId,Value=i-1234567890abcdef0 \
    --evaluation-periods 2 \
    --alarm-actions arn:aws:sns:us-west-2:123456789012:MyAlertTopic
```

```python
 Python CloudWatch integration with boto3
import boto3
from datetime import datetime, timedelta

cloudwatch = boto3.client('cloudwatch')
logs = boto3.client('logs')

 Publish custom metric
def publish_metric(namespace, metric_name, value, unit='Count', dimensions=None):
    cloudwatch.put_metric_data(
        Namespace=namespace,
        MetricData=[
            {
                'MetricName': metric_name,
                'Value': value,
                'Unit': unit,
                'Dimensions': dimensions or [],
                'Timestamp': datetime.utcnow()
            }
        ]
    )

 Log to CloudWatch
def log_to_cloudwatch(log_group, log_stream, message):
    logs.put_log_events(
        logGroupName=log_group,
        logStreamName=log_stream,
        logEvents=[
            {
                'timestamp': int(datetime.utcnow().timestamp()  1000),
                'message': message
            }
        ]
    )

 Usage
publish_metric('MyApp/Production', 'OrdersProcessed', 150, 'Count')
log_to_cloudwatch('MyApp/Application', 'instance-1', 'Order processed successfully')
```

 9. Tracing and APM

 9.1. OpenTelemetry Setup
```python
 Python application with OpenTelemetry tracing
from opentelemetry import trace
from opentelemetry.exporter.otlp.proto.grpc.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor
from opentelemetry.instrumentation.flask import FlaskInstrumentor
from opentelemetry.instrumentation.requests import RequestsInstrumentor
from flask import Flask

 Setup tracing
trace.set_tracer_provider(TracerProvider())
tracer = trace.get_tracer(__name__)

 Configure exporter
otlp_exporter = OTLPSpanExporter(
    endpoint="http://jaeger:4317",
    insecure=True
)
span_processor = BatchSpanProcessor(otlp_exporter)
trace.get_tracer_provider().add_span_processor(span_processor)

app = Flask(__name__)
FlaskInstrumentor().instrument_app(app)
RequestsInstrumentor().instrument()

@app.route('/api/users/<user_id>')
def get_user(user_id):
    with tracer.start_as_current_span("get_user") as span:
        span.set_attribute("user.id", user_id)
        
         Simulate database call
        with tracer.start_as_current_span("db_query"):
            user = query_database(user_id)
        
        span.set_attribute("user.found", user is not None)
        return jsonify(user)

@app.route('/api/orders')
def create_order():
    with tracer.start_as_current_span("create_order") as span:
         Validate request
        with tracer.start_as_current_span("validate_request"):
            order_data = validate_order_data(request.json)
        
         Process payment
        with tracer.start_as_current_span("process_payment"):
            payment = process_payment(order_data)
        
         Create order in database
        with tracer.start_as_current_span("save_order"):
            order = save_order(order_data, payment)
        
        span.set_attribute("order.id", order.id)
        return jsonify(order.to_dict())
```

```yaml
 docker-compose.jaeger.yml - Jaeger setup for distributed tracing
version: '3.8'

services:
  jaeger:
    image: jaegertracing/all-in-one:latest
    container_name: jaeger
    ports:
      - "5775:5775/udp"
      - "6831:6831/udp"
      - "6832:6832/udp"
      - "5778:5778"
      - "16686:16686"   Jaeger UI
      - "14268:14268"
      - "14250:14250"
      - "4317:4317"     OTLP gRPC
      - "4318:4318"     OTLP HTTP
    environment:
      - COLLECTOR_OTLP_ENABLED=true
    networks:
      - tracing

networks:
  tracing:
    driver: bridge
```

 10. Complete Monitoring Stack Setup

 10.1. Docker Compose for Full Observability
```yaml
 docker-compose.monitoring.yml - Complete monitoring stack
version: '3.8'

services:
   Prometheus
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus:/etc/prometheus
      - prometheus_data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/usr/share/prometheus/console_libraries'
      - '--web.console.templates=/usr/share/prometheus/consoles'
    networks:
      - monitoring

   Grafana
  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
    volumes:
      - grafana_data:/var/lib/grafana
      - ./grafana:/etc/grafana/provisioning
    depends_on:
      - prometheus
    networks:
      - monitoring

   Node Exporter
  node-exporter:
    image: prom/node-exporter:latest
    container_name: node-exporter
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - '--path.procfs=/host/proc'
      - '--path.rootfs=/rootfs'
      - '--path.sysfs=/host/sys'
      - '--collector.filesystem.mount-points-exclude=^/(sys|proc|dev|host|etc)($$|/)'
    networks:
      - monitoring

   cAdvisor (container metrics)
  cadvisor:
    image: gcr.io/cadvisor/cadvisor:latest
    container_name: cadvisor
    privileged: true
    devices:
      - /dev/kmsg:/dev/kmsg
    volumes:
      - /:/rootfs:ro
      - /var/run:/var/run:ro
      - /sys:/sys:ro
      - /var/lib/docker:/var/lib/docker:ro
    ports:
      - "8080:8080"
    networks:
      - monitoring

   Alertmanager
  alertmanager:
    image: prom/alertmanager:latest
    container_name: alertmanager
    ports:
      - "9093:9093"
    volumes:
      - ./alertmanager:/etc/alertmanager
    command:
      - '--config.file=/etc/alertmanager/alertmanager.yml'
    networks:
      - monitoring

   Elasticsearch
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:8.11.0
    container_name: elasticsearch
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ports:
      - "9200:9200"
    volumes:
      - elasticsearch_data:/usr/share/elasticsearch/data
    networks:
      - monitoring

   Kibana
  kibana:
    image: docker.elastic.co/kibana/kibana:8.11.0
    container_name: kibana
    ports:
      - "5601:5601"
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
    depends_on:
      - elasticsearch
    networks:
      - monitoring

   Jaeger
  jaeger:
    image: jaegertracing/all-in-one:latest
    container_name: jaeger
    ports:
      - "16686:16686"
      - "4317:4317"
    environment:
      - COLLECTOR_OTLP_ENABLED=true
    networks:
      - monitoring

volumes:
  prometheus_data:
  grafana_data:
  elasticsearch_data:

networks:
  monitoring:
    driver: bridge
```

```bash
!/bin/bash
 setup-monitoring.sh - Complete monitoring stack setup

set -euo pipefail

echo "Setting up complete monitoring stack..."

 Create directories
mkdir -p monitoring/{prometheus,grafana/dashboards,grafana/datasources,alertmanager}

 Download configuration files
 (Assume configurations are in the docker-compose.monitoring.yml)

 Start the stack
docker-compose -f docker-compose.monitoring.yml up -d

echo "Waiting for services to start..."
sleep 30

echo "Monitoring stack is ready!"
echo "Services available at:"
echo "  Prometheus:     http://localhost:9090"
echo "  Grafana:        http://localhost:3000 (admin/admin)"
echo "  Kibana:         http://localhost:5601"
echo "  Jaeger UI:      http://localhost:16686"
echo "  cAdvisor:       http://localhost:8080"
echo "  Alertmanager:   http://localhost:9093"
```

 11. Resources and Further Learning

 11.1. Documentation
- [Prometheus Documentation](https://prometheus.io/docs/)
- [Grafana Documentation](https://grafana.com/docs/)
- [Elasticsearch Documentation](https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html)
- [OpenTelemetry Documentation](https://opentelemetry.io/docs/)
- [AWS CloudWatch Documentation](https://docs.aws.amazon.com/cloudwatch/)

 11.2. Tools and Utilities
- Prometheus: Metrics collection and alerting
- Grafana: Visualization and dashboards
- ELK Stack: Log aggregation and analysis
- Jaeger: Distributed tracing
- OpenTelemetry: Unified observability framework
- Datadog: Commercial monitoring platform
- New Relic: Application performance monitoring

 11.3. Best Practices Summary
- Instrument all critical paths in your applications
- Use structured logging consistently
- Set up meaningful alerts with proper thresholds
- Create actionable dashboards
- Implement distributed tracing for microservices
- Regularly review and tune alerting rules
- Use log aggregation for centralized visibility
- Monitor both system and business metrics
- Document your observability setup
- Test your monitoring and alerting regularly

---

Next Steps: Set up a monitoring stack for your applications, create custom dashboards, implement alerting for critical metrics, and establish incident response procedures.
