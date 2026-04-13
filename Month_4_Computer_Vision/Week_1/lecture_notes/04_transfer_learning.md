 Transfer Learning: Leveraging Pretrained Models

 1. Introduction
Transfer learning is one of the most powerful techniques in computer vision, allowing practitioners to leverage knowledge from large-scale pretrained models and apply it to new tasks with limited data. This lecture covers feature extraction, fine-tuning strategies, and comprehensive implementations using PyTorch and TensorFlow.

 2. Transfer Learning Fundamentals

 2.1. Why Transfer Learning Works
```
Transfer Learning Principles:

1. KNOWLEDGE REUSE
   - Pretrained models learned from millions of images (ImageNet)
   - Early layers detect universal features (edges, textures, shapes)
   - Later layers learn task-specific patterns
   - Saves computation and training time

2. SMALL DATA SOLUTION
   - Training from scratch requires 100K+ images
   - Transfer learning works with 100-1000 images
   - Prevents overfitting on small datasets
   - Achieves higher accuracy faster

3. DOMAIN ADAPTATION
   - Source domain: Data the model was pretrained on
   - Target domain: Your specific task
   - Transfer works best when domains are similar
   - Fine-tuning adapts to domain differences

Transfer Learning Approaches:

1. FEATURE EXTRACTION
   - Freeze pretrained backbone
   - Train only new classification head
   - Fast, prevents overfitting
   - Best when datasets are very small

2. FINE-TUNING
   - Start with pretrained weights
   - Unfreeze some/all layers
   - Train with small learning rate
   - Best when more data is available
```

 2.2. Pretrained Model Zoo
```python
import torchvision.models as models
import tensorflow as tf
from tensorflow import keras

class PretrainedModelZoo:
    """Comprehensive pretrained model reference"""
    
    @staticmethod
    def get_pytorch_models():
        """Available PyTorch pretrained models"""
        return {
            'ResNet-18': {
                'model': models.resnet18(pretrained=True),
                'params': '11M',
                'top1_acc': '69.8%',
                'speed': 'Fast',
                'use_case': 'Quick prototyping, mobile'
            },
            'ResNet-50': {
                'model': models.resnet50(pretrained=True),
                'params': '25M',
                'top1_acc': '76.1%',
                'speed': 'Medium',
                'use_case': 'General purpose, balanced'
            },
            'ResNet-101': {
                'model': models.resnet101(pretrained=True),
                'params': '45M',
                'top1_acc': '77.4%',
                'speed': 'Slow',
                'use_case': 'High accuracy needs'
            },
            'EfficientNet-B0': {
                'model': models.efficientnet_b0(pretrained=True),
                'params': '5.3M',
                'top1_acc': '77.1%',
                'speed': 'Fast',
                'use_case': 'Mobile, efficiency focused'
            },
            'EfficientNet-B4': {
                'model': models.efficientnet_b4(pretrained=True),
                'params': '19M',
                'top1_acc': '83.0%',
                'speed': 'Slow',
                'use_case': 'Best accuracy/efficiency tradeoff'
            },
            'VGG-16': {
                'model': models.vgg16(pretrained=True),
                'params': '138M',
                'top1_acc': '71.6%',
                'speed': 'Slow',
                'use_case': 'Feature extraction baseline'
            },
            'DenseNet-121': {
                'model': models.densenet121(pretrained=True),
                'params': '8M',
                'top1_acc': '74.6%',
                'speed': 'Medium',
                'use_case': 'Memory efficient, feature reuse'
            },
            'MobileNet-V2': {
                'model': models.mobilenet_v2(pretrained=True),
                'params': '3.5M',
                'top1_acc': '71.9%',
                'speed': 'Very Fast',
                'use_case': 'Mobile deployment'
            }
        }
    
    @staticmethod
    def get_tensorflow_models():
        """Available TensorFlow pretrained models"""
        return {
            'ResNet50': keras.applications.ResNet50,
            'ResNet101': keras.applications.ResNet101,
            'EfficientNetB0': keras.applications.EfficientNetB0,
            'EfficientNetB3': keras.applications.EfficientNetB3,
            'MobileNetV2': keras.applications.MobileNetV2,
            'DenseNet121': keras.applications.DenseNet121,
            'VGG16': keras.applications.VGG16,
            'InceptionV3': keras.applications.InceptionV3
        }
    
    @staticmethod
    def model_selection_guide(dataset_size, num_classes, deployment_target):
        """Guide for selecting appropriate pretrained model"""
        
        recommendations = []
        
         Based on dataset size
        if dataset_size < 1000:
            recommendations.append({
                'scenario': 'Very Small Dataset (< 1K images)',
                'approach': 'Feature Extraction',
                'models': ['ResNet-50', 'EfficientNet-B0'],
                'reason': 'Freeze backbone, train only head'
            })
        elif dataset_size < 10000:
            recommendations.append({
                'scenario': 'Small Dataset (1K - 10K images)',
                'approach': 'Lightweight Fine-tuning',
                'models': ['EfficientNet-B0', 'ResNet-18'],
                'reason': 'Unfreeze top layers, small LR'
            })
        else:
            recommendations.append({
                'scenario': 'Large Dataset (> 10K images)',
                'approach': 'Full Fine-tuning',
                'models': ['ResNet-50', 'EfficientNet-B4'],
                'reason': 'Unfreeze all layers, standard training'
            })
        
         Based on deployment
        if deployment_target == 'mobile':
            recommendations.append({
                'scenario': 'Mobile Deployment',
                'models': ['MobileNet-V2', 'EfficientNet-B0'],
                'reason': 'Small size, fast inference'
            })
        elif deployment_target == 'server':
            recommendations.append({
                'scenario': 'Server Deployment',
                'models': ['ResNet-101', 'EfficientNet-B4'],
                'reason': 'Maximize accuracy'
            })
        
        return recommendations

 Usage
guide = PretrainedModelZoo.model_selection_guide(
    dataset_size=5000, 
    num_classes=10, 
    deployment_target='mobile'
)
for rec in guide:
    print(f"\n{rec['scenario']}:")
    print(f"  Models: {rec['models']}")
    print(f"  Reason: {rec['reason']}")
```

 3. Feature Extraction Implementation

 3.1. PyTorch Feature Extraction
```python
import torch
import torch.nn as nn
import torchvision.models as models
from torch.utils.data import DataLoader
from torchvision import transforms, datasets

class FeatureExtractor:
    """Complete feature extraction pipeline"""
    
    def __init__(self, base_model='resnet50', num_classes=10, freeze_backbone=True):
        self.device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')
        self.num_classes = num_classes
        
         Load pretrained model
        if base_model == 'resnet50':
            self.model = models.resnet50(pretrained=True)
             Get feature dimension
            self.feature_dim = self.model.fc.in_features
            
             Replace final layer
            self.model.fc = nn.Linear(self.feature_dim, num_classes)
            
        elif base_model == 'efficientnet_b0':
            self.model = models.efficientnet_b0(pretrained=True)
            self.feature_dim = self.model.classifier[1].in_features
            self.model.classifier = nn.Linear(self.feature_dim, num_classes)
        
         Freeze backbone if specified
        if freeze_backbone:
            self._freeze_backbone()
        
        self.model = self.model.to(self.device)
    
    def _freeze_backbone(self):
        """Freeze all layers except the final classifier"""
        for name, param in self.model.named_parameters():
            if 'fc' not in name and 'classifier' not in name:
                param.requires_grad = False
        
         Count trainable parameters
        trainable = sum(p.numel() for p in self.model.parameters() if p.requires_grad)
        total = sum(p.numel() for p in self.model.parameters())
        print(f"Trainable parameters: {trainable:,} / {total:,} ({100trainable/total:.1f}%)")
    
    def get_data_transforms(self):
        """Get standard ImageNet transforms"""
        return {
            'train': transforms.Compose([
                transforms.Resize(256),
                transforms.CenterCrop(224),
                transforms.RandomHorizontalFlip(),
                transforms.RandomRotation(10),
                transforms.ToTensor(),
                transforms.Normalize(
                    mean=[0.485, 0.456, 0.406],
                    std=[0.229, 0.224, 0.225]
                )
            ]),
            'val': transforms.Compose([
                transforms.Resize(256),
                transforms.CenterCrop(224),
                transforms.ToTensor(),
                transforms.Normalize(
                    mean=[0.485, 0.456, 0.406],
                    std=[0.229, 0.224, 0.225]
                )
            ])
        }
    
    def train(self, train_loader, val_loader, epochs=10, lr=0.001):
        """Train the model (only head if backbone frozen)"""
        
        criterion = nn.CrossEntropyLoss()
        
         Only optimize trainable parameters
        optimizer = torch.optim.Adam(
            filter(lambda p: p.requires_grad, self.model.parameters()),
            lr=lr
        )
        
        best_acc = 0.0
        
        for epoch in range(epochs):
             Training phase
            self.model.train()
            running_loss = 0.0
            running_corrects = 0
            
            for inputs, labels in train_loader:
                inputs = inputs.to(self.device)
                labels = labels.to(self.device)
                
                optimizer.zero_grad()
                
                outputs = self.model(inputs)
                _, preds = torch.max(outputs, 1)
                loss = criterion(outputs, labels)
                
                loss.backward()
                optimizer.step()
                
                running_loss += loss.item()  inputs.size(0)
                running_corrects += torch.sum(preds == labels.data)
            
            epoch_loss = running_loss / len(train_loader.dataset)
            epoch_acc = running_corrects.double() / len(train_loader.dataset)
            
             Validation phase
            val_acc = self.evaluate(val_loader)
            
            print(f'Epoch {epoch+1}/{epochs}:')
            print(f'  Train Loss: {epoch_loss:.4f}, Train Acc: {epoch_acc:.4f}')
            print(f'  Val Acc: {val_acc:.4f}')
            
            if val_acc > best_acc:
                best_acc = val_acc
                torch.save(self.model.state_dict(), 'best_model.pth')
        
        return best_acc
    
    def evaluate(self, data_loader):
        """Evaluate model accuracy"""
        self.model.eval()
        running_corrects = 0
        
        with torch.no_grad():
            for inputs, labels in data_loader:
                inputs = inputs.to(self.device)
                labels = labels.to(self.device)
                
                outputs = self.model(inputs)
                _, preds = torch.max(outputs, 1)
                running_corrects += torch.sum(preds == labels.data)
        
        return running_corrects.double() / len(data_loader.dataset)

 Usage
feature_extractor = FeatureExtractor(
    base_model='resnet50',
    num_classes=10,
    freeze_backbone=True
)

 Load your dataset
transforms_dict = feature_extractor.get_data_transforms()
 train_dataset = datasets.ImageFolder('path/to/train', transforms_dict['train'])
 train_loader = DataLoader(train_dataset, batch_size=32, shuffle=True)

 Train
 feature_extractor.train(train_loader, val_loader, epochs=10)
```

 4. Fine-Tuning Strategies

 4.1. Gradual Unfreezing
```python
class FineTuner:
    """Advanced fine-tuning with gradual unfreezing"""
    
    def __init__(self, model_name='resnet50', num_classes=10):
        self.device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')
        
         Load model
        self.model = models.resnet50(pretrained=True)
        
         Replace classifier
        self.model.fc = nn.Linear(self.model.fc.in_features, num_classes)
        self.model = self.model.to(self.device)
    
    def gradual_unfreeze(self, train_loader, val_loader, epochs_per_stage=5):
        """
        Stage 1: Train only classifier (frozen backbone)
        Stage 2: Unfreeze layer4 + classifier
        Stage 3: Unfreeze layer3 + layer4 + classifier
        Stage 4: Unfreeze all layers
        """
        
        stages = [
            {'name': 'Stage 1: Classifier Only', 'unfreeze': ['fc']},
            {'name': 'Stage 2: Layer4 + Classifier', 'unfreeze': ['layer4', 'fc']},
            {'name': 'Stage 3: Layer3-4 + Classifier', 'unfreeze': ['layer3', 'layer4', 'fc']},
            {'name': 'Stage 4: All Layers', 'unfreeze': ['all']},
        ]
        
        best_acc = 0.0
        
        for stage in stages:
            print(f"\n{'='50}")
            print(f"Starting {stage['name']}")
            print('='50)
            
             Freeze everything first
            for param in self.model.parameters():
                param.requires_grad = False
            
             Unfreeze specified layers
            if 'all' in stage['unfreeze']:
                for param in self.model.parameters():
                    param.requires_grad = True
            else:
                for name, param in self.model.named_parameters():
                    if any(layer in name for layer in stage['unfreeze']):
                        param.requires_grad = True
            
             Count trainable parameters
            trainable = sum(p.numel() for p in self.model.parameters() if p.requires_grad)
            print(f"Trainable parameters: {trainable:,}")
            
             Train with decreasing learning rate
            lr = 0.001 if 'all' not in stage['unfreeze'] else 0.0001
            val_acc = self._train_stage(train_loader, val_loader, epochs_per_stage, lr)
            
            if val_acc > best_acc:
                best_acc = val_acc
        
        return best_acc
    
    def _train_stage(self, train_loader, val_loader, epochs, lr):
        """Train for one stage"""
        criterion = nn.CrossEntropyLoss()
        optimizer = torch.optim.Adam(
            filter(lambda p: p.requires_grad, self.model.parameters()),
            lr=lr
        )
        
        best_acc = 0.0
        
        for epoch in range(epochs):
            self.model.train()
            running_loss = 0.0
            running_corrects = 0
            
            for inputs, labels in train_loader:
                inputs = inputs.to(self.device)
                labels = labels.to(self.device)
                
                optimizer.zero_grad()
                outputs = self.model(inputs)
                _, preds = torch.max(outputs, 1)
                loss = criterion(outputs, labels)
                
                loss.backward()
                optimizer.step()
                
                running_loss += loss.item()  inputs.size(0)
                running_corrects += torch.sum(preds == labels.data)
            
            epoch_acc = running_corrects.double() / len(train_loader.dataset)
            
             Validation
            val_acc = self._evaluate(val_loader)
            
            print(f'  Epoch {epoch+1}/{epochs} - Train Acc: {epoch_acc:.4f}, Val Acc: {val_acc:.4f}')
            
            if val_acc > best_acc:
                best_acc = val_acc
        
        return best_acc
    
    def _evaluate(self, data_loader):
        """Quick evaluation"""
        self.model.eval()
        correct = 0
        total = 0
        
        with torch.no_grad():
            for inputs, labels in data_loader:
                inputs = inputs.to(self.device)
                labels = labels.to(self.device)
                outputs = self.model(inputs)
                _, predicted = torch.max(outputs, 1)
                total += labels.size(0)
                correct += (predicted == labels).sum().item()
        
        return correct / total

 Usage
fine_tuner = FineTuner(model_name='resnet50', num_classes=10)
 best_accuracy = fine_tuner.gradual_unfreeze(train_loader, val_loader)
```

 4.2. Differential Learning Rates
```python
class DifferentialLRTrainer:
    """Train with different learning rates for different layers"""
    
    def __init__(self, model):
        self.model = model
        self.device = next(model.parameters()).device
    
    def get_layer_groups(self):
        """Group layers for differential learning rates"""
        
         Early layers (low LR)
        early_layers = []
         Middle layers (medium LR)
        middle_layers = []
         Late layers + classifier (high LR)
        late_layers = []
        
        for name, param in self.model.named_parameters():
            if 'layer1' in name or 'conv1' in name:
                early_layers.append(param)
            elif 'layer2' in name or 'layer3' in name:
                middle_layers.append(param)
            elif 'layer4' in name or 'fc' in name:
                late_layers.append(param)
        
        return early_layers, middle_layers, late_layers
    
    def train_with_diff_lr(self, train_loader, val_loader, epochs=10):
        """Train with differential learning rates"""
        
        early, middle, late = self.get_layer_groups()
        
         Different learning rates
        base_lr = 0.0001
        optimizer = torch.optim.Adam([
            {'params': early, 'lr': base_lr / 100},       Very small LR
            {'params': middle, 'lr': base_lr / 10},      Small LR
            {'params': late, 'lr': base_lr}               Normal LR
        ])
        
        criterion = nn.CrossEntropyLoss()
        
        for epoch in range(epochs):
            self.model.train()
            running_loss = 0.0
            
            for inputs, labels in train_loader:
                inputs = inputs.to(self.device)
                labels = labels.to(self.device)
                
                optimizer.zero_grad()
                outputs = self.model(inputs)
                loss = criterion(outputs, labels)
                
                loss.backward()
                optimizer.step()
                
                running_loss += loss.item()
            
            print(f'Epoch {epoch+1}/{epochs}, Loss: {running_loss/len(train_loader):.4f}')
```

 5. TensorFlow/Keras Implementation

 5.1. Complete TF Transfer Learning
```python
import tensorflow as tf
from tensorflow import keras
from tensorflow.keras import layers

def create_transfer_model_tf(base_model_name='ResNet50', num_classes=10, 
                              input_shape=(224, 224, 3)):
    """Create transfer learning model in TensorFlow"""
    
     Load base model
    base_models = {
        'ResNet50': keras.applications.ResNet50,
        'EfficientNetB0': keras.applications.EfficientNetB0,
        'MobileNetV2': keras.applications.MobileNetV2
    }
    
    base_model = base_models[base_model_name](
        weights='imagenet',
        include_top=False,
        input_shape=input_shape
    )
    
     Freeze base model
    base_model.trainable = False
    
     Build model
    inputs = keras.Input(shape=input_shape)
    x = base_model(inputs, training=False)
    x = layers.GlobalAveragePooling2D()(x)
    x = layers.Dropout(0.2)(x)
    outputs = layers.Dense(num_classes, activation='softmax')(x)
    
    model = keras.Model(inputs, outputs)
    
    return model, base_model

 Feature extraction phase
def train_feature_extraction(model, train_data, val_data, epochs=10):
    """Train with frozen backbone"""
    
    model.compile(
        optimizer=keras.optimizers.Adam(learning_rate=0.001),
        loss='categorical_crossentropy',
        metrics=['accuracy']
    )
    
    history = model.fit(
        train_data,
        validation_data=val_data,
        epochs=epochs,
        callbacks=[
            keras.callbacks.EarlyStopping(patience=3, restore_best_weights=True),
            keras.callbacks.ReduceLROnPlateau(patience=2)
        ]
    )
    
    return history

 Fine-tuning phase
def fine_tune_model(model, base_model, train_data, val_data, epochs=10, fine_tune_at=100):
    """Fine-tune by unfreezing top layers"""
    
     Unfreeze from specific layer
    base_model.trainable = True
    
     Freeze all layers before fine_tune_at
    for layer in base_model.layers[:fine_tune_at]:
        layer.trainable = False
    
     Recompile with lower learning rate
    model.compile(
        optimizer=keras.optimizers.Adam(learning_rate=0.0001/10),   Very small LR
        loss='categorical_crossentropy',
        metrics=['accuracy']
    )
    
    history = model.fit(
        train_data,
        validation_data=val_data,
        epochs=epochs,
        callbacks=[
            keras.callbacks.EarlyStopping(patience=5, restore_best_weights=True)
        ]
    )
    
    return history

 Usage
model, base = create_transfer_model_tf('EfficientNetB0', num_classes=10)
 history1 = train_feature_extraction(model, train_data, val_data)
 history2 = fine_tune_model(model, base, train_data, val_data)
```

 6. Best Practices Summary

 6.1. Transfer Learning Guidelines
```
✅ DO:
- Start with feature extraction for small datasets
- Use ImageNet normalization for pretrained models
- Unfreeze layers gradually from top to bottom
- Use 10-100x smaller learning rates for pretrained weights
- Monitor validation loss to detect overfitting
- Compare against training from scratch baseline
- Save best model checkpoints during training

❌ DON'T:
- Use large learning rates on pretrained weights
- Unfreeze all layers immediately with small data
- Forget to normalize inputs to ImageNet statistics
- Ignore signs of overfitting (validation loss increasing)
- Fine-tune batch normalization layers without care
- Use random initialization when transfer learning works
```

 7. Resources and Further Learning

 7.1. Documentation
- [PyTorch Transfer Learning Tutorial](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)
- [TensorFlow Transfer Learning Guide](https://www.tensorflow.org/guide/keras/transfer_learning)
- [ torchvision Models](https://pytorch.org/vision/stable/models.html)

 7.2. Key Takeaways
- Transfer learning leverages pretrained knowledge
- Feature extraction works for small datasets
- Fine-tuning requires careful learning rate selection
- Gradual unfreezing prevents catastrophic forgetting
- Differential learning rates optimize training
- Always validate pretrained model performance

---

Next Steps: Experiment with different pretrained models, try both feature extraction and fine-tuning approaches, and apply transfer learning to your custom datasets.
