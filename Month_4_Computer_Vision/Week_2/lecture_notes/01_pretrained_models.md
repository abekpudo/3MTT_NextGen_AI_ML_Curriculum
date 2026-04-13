Pretrained Models

Overview
Explain the role of pretrained models in computer vision and how to use them to accelerate development while preserving accuracy.

Why This Topic Matters
Pretrained models provide a strong starting point for many vision problems and are widely used when compute or data are limited.

Lecture Goals
- Compare common pretrained architectures
- Understand model input requirements and preprocessing
- Use pretrained weights for feature extraction or fine-tuning
- Assess trade-offs in size, speed, and accuracy
- Deploy pretrained models carefully with appropriate licenses

Key Concepts
- ResNet
- EfficientNet
- MobileNet
- model zoo
- preprocessing

Classroom Example / Case Study
- Discuss how pretrained backbones are trained on datasets like ImageNet
- Show how to replace the classifier head for a new task
- Explain the need to match preprocessing to the original model training

Practical Implementation Notes
- Choose smaller models for edge or mobile applications.
- Respect model licenses and usage terms.
- Validate that the model generalizes to your data domain.

Tools & Libraries
- PyTorch torchvision
- TensorFlow Hub
- Hugging Face vision models
- OpenCV

Recommended Resources
- Pretrained models overview
- Vision model zoo documentation

---
