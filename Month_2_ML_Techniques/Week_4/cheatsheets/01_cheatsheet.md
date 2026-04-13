 Week 4 Cheatsheet: Capstone & Production Deployment

 🚀 End-to-End ML Pipeline Reference

---

 📊 Project Structure Template

 Complete ML Project Layout
```
project_name/
├── README.md                       Project overview
├── requirements.txt                Dependencies
├── .gitignore                     Git exclusions
├── Dockerfile                     Container config
├── docker-compose.yml             Multi-service setup
├── setup.py                       Package setup
├── Makefile                       Automation commands
├── .env                           Environment variables
├── config/
│   ├── config.yaml               Main configuration
│   └── logging.conf              Logging settings
├── data/
│   ├── raw/                      Original data
│   ├── interim/                  Transformed data
│   ├── processed/                Model-ready data
│   └── external/                 External sources
├── notebooks/
│   ├── 01_eda.ipynb              Exploration
│   ├── 02_preprocessing.ipynb    Data prep
│   ├── 03_modeling.ipynb         Model dev
│   └── 04_evaluation.ipynb       Final eval
├── src/
│   ├── __init__.py
│   ├── data/
│   │   ├── __init__.py
│   │   ├── make_dataset.py       Data loading
│   │   └── preprocess.py         Preprocessing
│   ├── features/
│   │   ├── __init__.py
│   │   └── build_features.py     Feature engineering
│   ├── models/
│   │   ├── __init__.py
│   │   ├── train_model.py        Training scripts
│   │   ├── predict_model.py      Prediction
│   │   └── evaluate_model.py     Evaluation
│   └── utils/
│       ├── __init__.py
│       ├── logger.py             Logging
│       └── config.py             Config management
├── api/
│   ├── main.py                   FastAPI app
│   ├── schemas.py                Pydantic models
│   └── routers/
│       └── prediction.py         Endpoints
├── models/                        Saved models
│   ├── model.pkl
│   └── metadata.json
├── tests/                         Test suite
│   ├── __init__.py
│   ├── test_data.py
│   ├── test_features.py
│   └── test_models.py
├── docs/                          Documentation
│   ├── architecture.md
│   └── api.md
└── reports/                       Generated reports
    ├── eda.html
    └── results.pdf
```

---

 🔧 Core Project Files

 requirements.txt Template
```txt
 Core Data Science
pandas>=1.5.0
numpy>=1.21.0
scikit-learn>=1.2.0

 Advanced ML
xgboost>=1.7.0
lightgbm>=3.3.0
catboost>=1.1.0

 Optimization & Tracking
optuna>=3.0.0
mlflow>=2.0.0

 Deployment
fastapi>=0.95.0
uvicorn[standard]>=0.20.0
pydantic>=1.10.0
python-multipart>=0.0.6

 Visualization
matplotlib>=3.5.0
seaborn>=0.12.0
plotly>=5.0.0

 Utilities
joblib>=1.2.0
pyyaml>=6.0
python-dotenv>=0.21.0
requests>=2.28.0

 Testing
pytest>=7.0.0
pytest-cov>=4.0.0
hypothesis>=6.0.0

 Jupyter (for development)
jupyter>=1.0.0
ipython>=8.0.0
ipykernel>=6.0.0
```

 .gitignore Template
```gitignore
 Data
data/raw/
data/interim/
data/processed/
!data/raw/.gitkeep
!data/interim/.gitkeep
!data/processed/.gitkeep
.csv
.xlsx
.parquet
.h5
.hdf5

 Models
models/.pkl
models/.joblib
models/.h5
models/.pb
!models/.gitkeep

 Jupyter
.ipynb_checkpoints/
.ipynb_checkpoints
.nbconvert.ipynb

 Python
__pycache__/
.py[cod]
$py.class
.so
.Python
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
.egg-info/
.installed.cfg
.egg

 Environments
.env
.venv
env/
venv/
ENV/
env.bak/
venv.bak/

 IDE
.vscode/
.idea/
.swp
.swo
~

 OS
.DS_Store
Thumbs.db

 MLflow
mlruns/
mlartifacts/

 Logs
.log
logs/
```

 README.md Template
```markdown
 Project Title

Brief description of the project and its purpose.

 Business Problem
Explain the real-world problem being solved.

 Dataset
- Source: Where data comes from
- Size: Number of samples and features
- Target: What you're predicting

 Project Structure
```
[Show directory structure]
```

 Installation

```bash
 Clone repository
git clone https://github.com/username/project.git
cd project

 Create virtual environment
python -m venv venv
source venv/bin/activate   Linux/Mac
 or
venv\Scripts\activate   Windows

 Install dependencies
pip install -r requirements.txt
```

 Usage

 Training
```bash
python src/models/train_model.py --config config/config.yaml
```

 Prediction
```bash
python src/models/predict_model.py --input data/new_data.csv
```

 API
```bash
uvicorn api.main:app --reload
```

 Model Performance

| Metric | Validation | Test |
|--------|------------|------|
| Accuracy | 0.92 | 0.91 |
| F1-Score | 0.90 | 0.89 |
| ROC-AUC | 0.95 | 0.94 |

 API Documentation

Once running, access docs at:
- Swagger UI: http://localhost:8000/docs
- ReDoc: http://localhost:8000/redoc

 Contributing
1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

 License
[Specify license]
```

---

 🐳 Docker Configuration

 Dockerfile
```dockerfile
 Use official Python image
FROM python:3.9-slim

 Set working directory
WORKDIR /app

 Install system dependencies
RUN apt-get update && apt-get install -y \
    gcc \
    g++ \
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
CMD ["uvicorn", "api.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

 docker-compose.yml
```yaml
version: '3.8'

services:
  api:
    build: .
    ports:
      - "8000:8000"
    environment:
      - MODEL_PATH=/app/models
      - LOG_LEVEL=INFO
    volumes:
      - ./models:/app/models
      - ./logs:/app/logs
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
  
   Optional: Add monitoring
  prometheus:
    image: prom/prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
  
  grafana:
    image: grafana/grafana
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
```

---

 🚀 FastAPI Deployment

 Basic API Structure
```python
 api/main.py
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
import joblib
import numpy as np
from typing import List, Optional

 Load model on startup
app = FastAPI(title="ML Prediction API", version="1.0.0")

 Global model variable
model = None
preprocessor = None

@app.on_event("startup")
async def load_model():
    global model, preprocessor
    model = joblib.load("models/best_model.pkl")
    preprocessor = joblib.load("models/preprocessor.pkl")

 Request/Response schemas
class PredictionRequest(BaseModel):
    features: List[float]
    
    class Config:
        schema_extra = {
            "example": {
                "features": [25.0, 1.0, 500.0, 3.0, 4.0, 5.0, 4.0, 3.0, 4.0, 5.0, 4.0, 3.0, 4.0, 5.0]
            }
        }

class PredictionResponse(BaseModel):
    prediction: int
    probability: float
    confidence: str

 Health check
@app.get("/health")
def health_check():
    return {"status": "healthy", "model_loaded": model is not None}

 Prediction endpoint
@app.post("/predict", response_model=PredictionResponse)
def predict(request: PredictionRequest):
    try:
         Preprocess
        features = np.array(request.features).reshape(1, -1)
        features_processed = preprocessor.transform(features)
        
         Predict
        prediction = model.predict(features_processed)[0]
        probability = model.predict_proba(features_processed)[0].max()
        
         Determine confidence
        if probability >= 0.9:
            confidence = "high"
        elif probability >= 0.7:
            confidence = "medium"
        else:
            confidence = "low"
        
        return PredictionResponse(
            prediction=int(prediction),
            probability=float(probability),
            confidence=confidence
        )
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

 Batch prediction
@app.post("/predict_batch")
def predict_batch(requests: List[PredictionRequest]):
    predictions = []
    for req in requests:
        result = predict(req)
        predictions.append(result)
    return {"predictions": predictions}
```

 Running the API
```bash
 Development
uvicorn api.main:app --reload --host 0.0.0.0 --port 8000

 Production
uvicorn api.main:app --host 0.0.0.0 --port 8000 --workers 4

 With Docker
docker build -t ml-api .
docker run -p 8000:8000 ml-api

 With Docker Compose
docker-compose up -d
```

 Testing the API
```bash
 Health check
curl http://localhost:8000/health

 Single prediction
curl -X POST http://localhost:8000/predict \
  -H "Content-Type: application/json" \
  -d '{"features": [25.0, 1.0, 500.0, 3.0, 4.0, 5.0, 4.0, 3.0, 4.0, 5.0, 4.0, 3.0, 4.0, 5.0]}'

 Batch prediction
curl -X POST http://localhost:8000/predict_batch \
  -H "Content-Type: application/json" \
  -d '[{"features": [25.0, ...]}, {"features": [30.0, ...]}]'
```

---

 📦 Model Serialization

 Saving Models
```python
import joblib
import json

 Save model and preprocessor
joblib.dump(model, 'models/best_model.pkl')
joblib.dump(preprocessor, 'models/preprocessor.pkl')

 Save metadata
metadata = {
    'model_type': 'XGBClassifier',
    'hyperparameters': model.get_params(),
    'feature_names': list(X.columns),
    'target_name': 'satisfaction',
    'performance': {
        'accuracy': 0.92,
        'f1_score': 0.90,
        'roc_auc': 0.95
    },
    'training_date': '2024-01-15',
    'version': '1.0.0'
}

with open('models/metadata.json', 'w') as f:
    json.dump(metadata, f, indent=2)
```

 Loading Models
```python
import joblib
import json

 Load model and metadata
model = joblib.load('models/best_model.pkl')
preprocessor = joblib.load('models/preprocessor.pkl')

with open('models/metadata.json', 'r') as f:
    metadata = json.load(f)

print(f"Model version: {metadata['version']}")
print(f"Performance: {metadata['performance']}")
```

---

 🧪 Testing

 Unit Tests
```python
 tests/test_models.py
import pytest
import numpy as np
from src.models.predict_model import make_prediction

class TestModelPrediction:
    def test_prediction_shape(self):
        """Test that prediction returns correct shape"""
        features = np.random.rand(1, 14)
        prediction = make_prediction(features)
        assert isinstance(prediction, (int, np.integer))
    
    def test_prediction_range(self):
        """Test that prediction is valid class"""
        features = np.random.rand(1, 14)
        prediction = make_prediction(features)
        assert prediction in [0, 1]
    
    def test_probability_range(self):
        """Test that probability is between 0 and 1"""
        features = np.random.rand(1, 14)
        _, prob = make_prediction(features, return_proba=True)
        assert 0 <= prob <= 1

 Run tests
 pytest tests/
 pytest tests/test_models.py -v
 pytest tests/ --cov=src --cov-report=html
```

 API Tests
```python
 tests/test_api.py
from fastapi.testclient import TestClient
from api.main import app

client = TestClient(app)

def test_health_check():
    response = client.get("/health")
    assert response.status_code == 200
    assert response.json()["status"] == "healthy"

def test_prediction():
    payload = {
        "features": [25.0, 1.0, 500.0, 3.0, 4.0, 5.0, 4.0, 3.0, 4.0, 5.0, 4.0, 3.0, 4.0, 5.0]
    }
    response = client.post("/predict", json=payload)
    assert response.status_code == 200
    assert "prediction" in response.json()
    assert "probability" in response.json()
```

---

 📋 Capstone Checklist

 Before Starting
- [ ] Repository initialized with .gitignore
- [ ] Virtual environment created
- [ ] Requirements.txt complete
- [ ] Data downloaded and organized
- [ ] Project structure in place

 Data Phase
- [ ] EDA notebook complete
- [ ] All visualizations generated
- [ ] Data quality report created
- [ ] Preprocessing pipeline built
- [ ] Train/val/test splits validated

 Modeling Phase
- [ ] 6+ algorithms trained
- [ ] Cross-validation implemented
- [ ] Hyperparameter tuning done
- [ ] Ensemble models created
- [ ] Feature importance analyzed

 Deployment Phase
- [ ] Model serialized with metadata
- [ ] FastAPI application working
- [ ] Docker container built
- [ ] API tested locally
- [ ] Documentation complete

 Final Submission
- [ ] All code committed to Git
- [ ] README.md comprehensive
- [ ] Requirements.txt up-to-date
- [ ] Tests passing
- [ ] Presentation prepared

---

 🎯 Quick Commands

| Task | Command |
|------|---------|
| Create venv | `python -m venv venv` |
| Activate venv | `source venv/bin/activate` |
| Install deps | `pip install -r requirements.txt` |
| Save deps | `pip freeze > requirements.txt` |
| Run API | `uvicorn api.main:app --reload` |
| Build Docker | `docker build -t ml-api .` |
| Run Docker | `docker run -p 8000:8000 ml-api` |
| Run tests | `pytest tests/ -v` |
| Test coverage | `pytest --cov=src --cov-report=html` |
| Git commit | `git add . && git commit -m "message"` |
| Jupyter | `jupyter notebook` |

---

 💡 Best Practices

 Code Quality
- Use type hints for all functions
- Write docstrings for all modules
- Keep functions under 50 lines
- Use configuration files, not hardcoding
- Log everything important

 MLOps
- Version control data and models
- Track all experiments
- Use reproducible environments
- Test before deploying
- Monitor in production

 Documentation
- README for every module
- Docstrings for all functions
- API documentation auto-generated
- Architecture diagrams
- Deployment runbooks

---

End of Month 2 Capstone Cheatsheet

Previous: [Week 3 Cheatsheet ←](../Week_3/cheatsheets/01_cheatsheet.md) | Next: [Month 3 →](../../Month_3_NLP/README.md)
