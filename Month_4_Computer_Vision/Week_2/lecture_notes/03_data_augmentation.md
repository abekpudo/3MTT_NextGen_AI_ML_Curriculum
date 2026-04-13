Data Augmentation

Overview
Cover image augmentation techniques that expand the effective training set by applying transformations such as flips, crops, color jitter, and noise.

Why This Topic Matters
Data augmentation improves model robustness by exposing it to a wider range of input variation without collecting more data.

Lecture Goals
- Explain augmentation methods for images
- Use augmentation pipelines during training
- Preserve label integrity under transformations
- Compare on-the-fly augmentation to dataset preprocessing
- Measure the impact of augmentation on validation performance

Key Concepts
- rotation
- flip
- crop
- color jitter
- noise
- mixup

Classroom Example / Case Study
- Show how to use torchvision transforms or TensorFlow preprocessing layers
- Explain when to apply augmentation only during training
- Discuss the difference between geometric and photometric augmentations

Practical Implementation Notes
- Augmentations should reflect realistic variability in the deployment environment.
- Avoid distortions that change the class label meaning.
- Use stronger augmentation cautiously when validation performance drops.

Tools & Libraries
- torchvision.transforms
- albumentations
- imgaug
- TensorFlow image

Recommended Resources
- Data augmentation guide
- Vision augmentation recipes

---
