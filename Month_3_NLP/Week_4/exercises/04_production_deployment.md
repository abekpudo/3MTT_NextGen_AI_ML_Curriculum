 Exercise 4: Production Deployment

 🎯 Objective
Deploy NLP models as production-ready APIs.

---

 📋 Tasks

 Task 1: FastAPI NLP Service
Build complete NLP API with FastAPI.

```python
from fastapi import FastAPI, HTTPException, BackgroundTasks
from pydantic import BaseModel
from typing import List, Optional
import joblib
import torch
from transformers import pipeline
import uvicorn

 Initialize app
app = FastAPI(
    title="NLP Service API",
    description="Production-ready NLP API",
    version="1.0.0"
)

 Request/Response models
class TextRequest(BaseModel):
    text: str
    model_type: Optional[str] = "transformer"
    language: Optional[str] = "en"

class BatchTextRequest(BaseModel):
    texts: List[str]
    model_type: Optional[str] = "transformer"

class SentimentResponse(BaseModel):
    text: str
    sentiment: str
    confidence: float
    model_used: str
    processing_time: float

class SummaryResponse(BaseModel):
    original_text: str
    summary: str
    compression_ratio: float

class ClassificationResponse(BaseModel):
    text: str
    label: str
    confidence: float
    all_labels: dict

 Load models
@app.on_event("startup")
async def load_models():
    """Load models on startup."""
    app.state.sentiment_model = pipeline('sentiment-analysis')
    app.state.summarization_model = pipeline('summarization')
    app.state.classification_model = pipeline('zero-shot-classification')
    app.state.ner_model = pipeline('ner', aggregation_strategy='simple')
    print("Models loaded successfully")

 Health check
@app.get("/health")
async def health_check():
    """Health check endpoint."""
    return {
        "status": "healthy",
        "models_loaded": hasattr(app.state, 'sentiment_model'),
        "version": "1.0.0"
    }

 Sentiment analysis
@app.post("/sentiment", response_model=SentimentResponse)
async def analyze_sentiment(request: TextRequest):
    """
    Analyze sentiment of input text.
    
    - text: Text to analyze
    - model_type: 'transformer' or 'ml' (default: transformer)
    """
    import time
    start_time = time.time()
    
    if not request.text.strip():
        raise HTTPException(status_code=400, detail="Text cannot be empty")
    
     Get prediction
    if request.model_type == "transformer":
        result = app.state.sentiment_model(request.text[:512])[0]
        sentiment = result['label']
        confidence = result['score']
    else:
         ML model path (would need to load actual model)
        sentiment = "Positive"   Placeholder
        confidence = 0.85
    
    processing_time = time.time() - start_time
    
    return SentimentResponse(
        text=request.text,
        sentiment=sentiment,
        confidence=confidence,
        model_used=request.model_type,
        processing_time=processing_time
    )

 Text summarization
@app.post("/summarize", response_model=SummaryResponse)
async def summarize_text(request: TextRequest):
    """
    Summarize input text.
    
    - text: Long text to summarize
    """
    if len(request.text) < 100:
        raise HTTPException(status_code=400, detail="Text too short for summarization")
    
     Generate summary
    summary_result = app.state.summarization_model(
        request.text[:1024],
        max_length=150,
        min_length=30
    )[0]
    
    summary = summary_result['summary_text']
    
     Calculate compression
    compression = len(summary) / len(request.text)
    
    return SummaryResponse(
        original_text=request.text[:100] + "...",
        summary=summary,
        compression_ratio=compression
    )

 Zero-shot classification
@app.post("/classify", response_model=ClassificationResponse)
async def classify_text(request: TextRequest, labels: List[str]):
    """
    Classify text into custom categories.
    
    - text: Text to classify
    - labels: List of possible labels
    """
    if not labels:
        raise HTTPException(status_code=400, detail="Labels required")
    
    result = app.state.classification_model(
        request.text[:512],
        labels,
        multi_label=False
    )
    
    return ClassificationResponse(
        text=request.text,
        label=result['labels'][0],
        confidence=result['scores'][0],
        all_labels=dict(zip(result['labels'], result['scores']))
    )

 Batch processing
@app.post("/batch/sentiment")
async def batch_sentiment(request: BatchTextRequest, background_tasks: BackgroundTasks):
    """
    Process multiple texts in batch.
    
    - texts: List of texts to analyze
    - model_type: Model type to use
    """
    if len(request.texts) > 100:
        raise HTTPException(status_code=400, detail="Batch size limit: 100")
    
    results = []
    for text in request.texts:
        result = app.state.sentiment_model(text[:512])[0]
        results.append({
            'text': text[:50] + "..." if len(text) > 50 else text,
            'sentiment': result['label'],
            'confidence': result['score']
        })
    
    return {"results": results, "count": len(results)}

 Model information
@app.get("/models")
async def list_models():
    """List available models."""
    return {
        "models": [
            {
                "id": "sentiment",
                "name": "Sentiment Analysis",
                "endpoint": "/sentiment",
                "description": "Analyze sentiment of text"
            },
            {
                "id": "summarization",
                "name": "Text Summarization",
                "endpoint": "/summarize",
                "description": "Generate abstractive summary"
            },
            {
                "id": "classification",
                "name": "Zero-shot Classification",
                "endpoint": "/classify",
                "description": "Classify into custom categories"
            },
            {
                "id": "ner",
                "name": "Named Entity Recognition",
                "endpoint": "/ner",
                "description": "Extract named entities"
            }
        ]
    }

 Run with: uvicorn main:app --reload --host 0.0.0.0 --port 8000
print("FastAPI NLP Service created")
print("Run with: uvicorn main:app --host 0.0.0.0 --port 8000")
```

---

 Task 2: Model Versioning
Implement model versioning system.

```python
import hashlib
import json
from datetime import datetime
from pathlib import Path

class ModelVersionManager:
    """
    Manage model versions for production.
    """
    def __init__(self, model_dir="models"):
        """
        Initialize version manager.
        
        Args:
            model_dir (str): Directory for model storage
        """
        self.model_dir = Path(model_dir)
        self.model_dir.mkdir(exist_ok=True)
        self.registry_file = self.model_dir / "registry.json"
        self.registry = self._load_registry()
    
    def _load_registry(self):
        """Load model registry."""
        if self.registry_file.exists():
            with open(self.registry_file, 'r') as f:
                return json.load(f)
        return {}
    
    def _save_registry(self):
        """Save model registry."""
        with open(self.registry_file, 'w') as f:
            json.dump(self.registry, f, indent=2)
    
    def _compute_hash(self, model_path):
        """Compute model file hash."""
        hasher = hashlib.md5()
        with open(model_path, 'rb') as f:
            for chunk in iter(lambda: f.read(4096), b""):
                hasher.update(chunk)
        return hasher.hexdigest()
    
    def register_model(self, model_name, model_path, metadata=None):
        """
        Register new model version.
        
        Args:
            model_name (str): Model name
            model_path (str): Path to model file
            metadata (dict): Model metadata
            
        Returns:
            str: Version ID
        """
        model_path = Path(model_path)
        
        if not model_path.exists():
            raise FileNotFoundError(f"Model file not found: {model_path}")
        
         Compute version
        model_hash = self._compute_hash(model_path)
        timestamp = datetime.now().isoformat()
        version = f"{model_name}_{model_hash[:8]}_{timestamp[:10]}"
        
         Copy to versioned location
        version_dir = self.model_dir / model_name / version
        version_dir.mkdir(parents=True, exist_ok=True)
        
        new_model_path = version_dir / model_path.name
         shutil.copy(model_path, new_model_path)   Uncomment to actually copy
        
         Register
        if model_name not in self.registry:
            self.registry[model_name] = {}
        
        self.registry[model_name][version] = {
            "path": str(new_model_path),
            "hash": model_hash,
            "created": timestamp,
            "metadata": metadata or {},
            "active": False
        }
        
        self._save_registry()
        
        print(f"Registered model {model_name} version {version}")
        return version
    
    def activate_version(self, model_name, version):
        """
        Activate specific model version.
        
        Args:
            model_name (str): Model name
            version (str): Version to activate
        """
        if model_name not in self.registry:
            raise ValueError(f"Model {model_name} not found")
        
        if version not in self.registry[model_name]:
            raise ValueError(f"Version {version} not found for {model_name}")
        
         Deactivate all versions
        for v in self.registry[model_name]:
            self.registry[model_name][v]['active'] = False
        
         Activate specified version
        self.registry[model_name][version]['active'] = True
        self._save_registry()
        
        print(f"Activated {model_name} version {version}")
    
    def get_active_version(self, model_name):
        """
        Get currently active version.
        
        Args:
            model_name (str): Model name
            
        Returns:
            dict: Active version info
        """
        if model_name not in self.registry:
            return None
        
        for version, info in self.registry[model_name].items():
            if info.get('active', False):
                return {"version": version, info}
        
        return None
    
    def list_versions(self, model_name):
        """
        List all versions of a model.
        
        Args:
            model_name (str): Model name
            
        Returns:
            list: List of versions
        """
        if model_name not in self.registry:
            return []
        
        versions = []
        for version, info in self.registry[model_name].items():
            versions.append({
                "version": version,
                "created": info['created'],
                "active": info.get('active', False),
                "metadata": info.get('metadata', {})
            })
        
        return versions

 Test
version_manager = ModelVersionManager()

 Register models
version1 = version_manager.register_model(
    "sentiment",
    "sentiment_model.pkl",
    metadata={"accuracy": 0.92, "dataset": "imdb"}
)

version2 = version_manager.register_model(
    "sentiment",
    "sentiment_model_v2.pkl",
    metadata={"accuracy": 0.94, "dataset": "imdb_v2"}
)

 Activate version
version_manager.activate_version("sentiment", version2)

 Get active version
active = version_manager.get_active_version("sentiment")
print(f"\nActive version: {active['version']}")
print(f"Metadata: {active['metadata']}")

 List all versions
all_versions = version_manager.list_versions("sentiment")
print(f"\nAll versions: {len(all_versions)}")
for v in all_versions:
    print(f"  {v['version']} - Active: {v['active']}")
```

---

 Task 3: Docker Containerization
Create Docker configuration for deployment.

```dockerfile
 Dockerfile
FROM python:3.9-slim

 Set working directory
WORKDIR /app

 Install system dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    && rm -rf /var/lib/apt/lists/

 Copy requirements
COPY requirements.txt .

 Install Python dependencies
RUN pip install --no-cache-dir -r requirements.txt

 Copy application code
COPY . .

 Expose port
EXPOSE 8000

 Run application
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

```yaml
 docker-compose.yml
version: '3.8'

services:
  nlp-api:
    build: .
    ports:
      - "8000:8000"
    environment:
      - MODEL_CACHE_DIR=/app/models
      - LOG_LEVEL=info
    volumes:
      - ./models:/app/models
      - ./logs:/app/logs
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
  
  redis:
    image: redis:alpine
    ports:
      - "6379:6379"
    restart: unless-stopped
  
  monitoring:
    image: prom/prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    restart: unless-stopped
```

```python
 requirements.txt
fastapi==0.104.1
uvicorn[standard]==0.24.0
pydantic==2.5.0
transformers==4.35.0
torch==2.1.0
scikit-learn==1.3.0
numpy==1.24.0
pandas==2.0.0
redis==5.0.0
prometheus-client==0.19.0
```

---

 Task 4: Monitoring and Logging
Implement monitoring for production API.

```python
from prometheus_client import Counter, Histogram, generate_latest
from fastapi import Request
import logging
import time

 Metrics
REQUEST_COUNT = Counter('nlp_requests_total', 'Total requests', ['method', 'endpoint', 'status'])
REQUEST_DURATION = Histogram('nlp_request_duration_seconds', 'Request duration', ['endpoint'])
PREDICTION_COUNT = Counter('nlp_predictions_total', 'Total predictions', ['model', 'result'])

 Logging setup
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler('logs/nlp_api.log'),
        logging.StreamHandler()
    ]
)
logger = logging.getLogger(__name__)

 Middleware for metrics
@app.middleware("http")
async def metrics_middleware(request: Request, call_next):
    """Middleware to track metrics."""
    start_time = time.time()
    
     Process request
    response = await call_next(request)
    
     Record metrics
    duration = time.time() - start_time
    endpoint = request.url.path
    method = request.method
    status = response.status_code
    
    REQUEST_COUNT.labels(method=method, endpoint=endpoint, status=status).inc()
    REQUEST_DURATION.labels(endpoint=endpoint).observe(duration)
    
     Logging
    logger.info(f"{method} {endpoint} - {status} - {duration:.3f}s")
    
    return response

 Metrics endpoint
@app.get("/metrics")
async def metrics():
    """Prometheus metrics endpoint."""
    from starlette.responses import Response
    return Response(content=generate_latest(), media_type="text/plain")

 Logging decorator
def log_prediction(func):
    """Decorator to log predictions."""
    def wrapper(args, kwargs):
        start = time.time()
        result = func(args, kwargs)
        duration = time.time() - start
        
        logger.info(f"Prediction took {duration:.3f}s - Result: {result}")
        return result
    return wrapper

 Usage monitoring
class UsageMonitor:
    """
    Monitor API usage patterns.
    """
    def __init__(self):
        """Initialize monitor."""
        self.usage_stats = {}
    
    def record_request(self, endpoint, model_type, duration):
        """
        Record API request.
        
        Args:
            endpoint (str): API endpoint
            model_type (str): Model type used
            duration (float): Request duration
        """
        key = f"{endpoint}:{model_type}"
        
        if key not in self.usage_stats:
            self.usage_stats[key] = {
                'count': 0,
                'total_duration': 0,
                'avg_duration': 0
            }
        
        self.usage_stats[key]['count'] += 1
        self.usage_stats[key]['total_duration'] += duration
        self.usage_stats[key]['avg_duration'] = (
            self.usage_stats[key]['total_duration'] / self.usage_stats[key]['count']
        )
    
    def get_stats(self):
        """Get usage statistics."""
        return self.usage_stats
    
    def get_summary(self):
        """Get summary report."""
        total_requests = sum(s['count'] for s in self.usage_stats.values())
        avg_duration = sum(s['total_duration'] for s in self.usage_stats.values()) / total_requests if total_requests > 0 else 0
        
        return {
            'total_requests': total_requests,
            'avg_request_duration': avg_duration,
            'endpoints_used': len(self.usage_stats)
        }

 Test
monitor = UsageMonitor()
monitor.record_request('/sentiment', 'transformer', 0.15)
monitor.record_request('/sentiment', 'transformer', 0.12)
monitor.record_request('/summarize', 'bart', 0.45)

print("Usage Stats:", monitor.get_stats())
print("Summary:", monitor.get_summary())
```

---

 Task 5: Performance Optimization
Optimize API for high throughput.

```python
from functools import lru_cache
import asyncio
from concurrent.futures import ThreadPoolExecutor

 Caching
@lru_cache(maxsize=1000)
def cached_sentiment_analysis(text: str, model_type: str = "transformer") -> dict:
    """
    Cached sentiment analysis.
    
    Args:
        text (str): Input text
        model_type (str): Model type
        
    Returns:
        dict: Analysis result
    """
     This would call actual model
    return {"sentiment": "positive", "confidence": 0.95}

 Batch processing
class BatchProcessor:
    """
    Process requests in batches for efficiency.
    """
    def __init__(self, max_batch_size=32, max_wait_time=0.1):
        """
        Initialize batch processor.
        
        Args:
            max_batch_size (int): Maximum batch size
            max_wait_time (float): Maximum wait time in seconds
        """
        self.max_batch_size = max_batch_size
        self.max_wait_time = max_wait_time
        self.queue = []
        self.executor = ThreadPoolExecutor(max_workers=4)
    
    async def process_batch(self, items):
        """
        Process batch of items.
        
        Args:
            items (list): Items to process
            
        Returns:
            list: Results
        """
         Process in parallel
        loop = asyncio.get_event_loop()
        futures = [
            loop.run_in_executor(self.executor, self._process_single, item)
            for item in items
        ]
        results = await asyncio.gather(futures)
        return results
    
    def _process_single(self, item):
        """Process single item."""
         Placeholder processing
        return {"item": item, "processed": True}

 Async database operations
class AsyncModelStore:
    """
    Async model storage for production.
    """
    def __init__(self, redis_client=None):
        """
        Initialize store.
        
        Args:
            redis_client: Redis client for caching
        """
        self.redis = redis_client
        self.cache_ttl = 3600   1 hour
    
    async def get_model(self, model_name: str):
        """
        Get model from cache or storage.
        
        Args:
            model_name (str): Model name
            
        Returns:
            model: Loaded model
        """
         Check cache
        if self.redis:
            cached = await self.redis.get(f"model:{model_name}")
            if cached:
                return cached
        
         Load from storage
        model = await self._load_model(model_name)
        
         Cache
        if self.redis:
            await self.redis.setex(f"model:{model_name}", self.cache_ttl, model)
        
        return model
    
    async def _load_model(self, model_name: str):
        """Load model from storage."""
         Async model loading
        await asyncio.sleep(0.1)   Simulate loading
        return {"name": model_name, "status": "loaded"}

 Load balancing simulation
class LoadBalancer:
    """
    Simple load balancer for multiple model instances.
    """
    def __init__(self, num_instances=3):
        """
        Initialize load balancer.
        
        Args:
            num_instances (int): Number of model instances
        """
        self.instances = [f"model_{i}" for i in range(num_instances)]
        self.current = 0
    
    def get_instance(self):
        """
        Get next available instance (round-robin).
        
        Returns:
            str: Instance ID
        """
        instance = self.instances[self.current]
        self.current = (self.current + 1) % len(self.instances)
        return instance
    
    def get_stats(self):
        """Get load distribution stats."""
        return {
            "instances": len(self.instances),
            "current": self.current,
            "strategy": "round_robin"
        }

 Test
balancer = LoadBalancer(num_instances=3)
for i in range(6):
    print(f"Request {i+1}: {balancer.get_instance()}")

print("\nLoad Balancer Stats:", balancer.get_stats())
```

---

 ✅ Completion Checklist

- [ ] FastAPI service implemented
- [ ] Model versioning working
- [ ] Docker configuration created
- [ ] Monitoring and logging added
- [ ] Performance optimization complete
- [ ] All functions documented
- [ ] Test cases pass

---

 🎯 Extension Challenge

Create complete production deployment system:

```python
class ProductionDeployment:
    """
    Complete production deployment system.
    """
    def __init__(self, config):
        """
        Initialize deployment.
        
        Args:
            config (dict): Deployment configuration
        """
        self.config = config
        self.version_manager = ModelVersionManager()
        self.monitor = UsageMonitor()
        self.load_balancer = LoadBalancer()
    
    def deploy(self, model_path, model_name):
        """
        Deploy model to production.
        
        Args:
            model_path (str): Path to model file
            model_name (str): Model name
        """
         Your implementation
        pass
    
    def rollback(self, model_name, version):
        """
        Rollback to previous version.
        
        Args:
            model_name (str): Model name
            version (str): Version to rollback to
        """
         Your implementation
        pass
    
    def scale(self, num_instances):
        """
        Scale deployment.
        
        Args:
            num_instances (int): Number of instances
        """
         Your implementation
        pass
    
    def get_metrics(self):
        """
        Get deployment metrics.
        
        Returns:
            dict: Deployment metrics
        """
         Your implementation
        pass
```
