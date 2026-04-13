Model Serving

Overview
Cover model serving options for big data applications, including REST APIs, batch scoring, and streaming inference.

Why This Topic Matters
A serving layer is required to operationalize models so predictions can be consumed by applications and users with low latency and reliability.

Lecture Goals
- Differentiate real-time serving from batch scoring
- Design an inference API and payload contract
- Ensure consistent preprocessing in serving
- Manage model versioning for serving
- Monitor latency and throughput

Key Concepts
- REST serving
- batch scoring
- model versioning
- preprocessing
- scaling

Classroom Example / Case Study
- Explain how a model serving service receives requests and returns predictions
- Show the role of serialization and model artifact loading
- Describe how batch scoring workflows schedule periodic prediction jobs

Practical Implementation Notes
- Keep the serving path simple and deterministic.
- Validate inputs before inference.
- Decouple model hosting from the client application where possible.

Tools & Libraries
- FastAPI
- Docker
- Kubernetes
- Spark batch jobs
- MLflow model registry

Recommended Resources
- Model serving patterns
- Serving architecture guide

---
