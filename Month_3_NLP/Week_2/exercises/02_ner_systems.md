 Exercise 2: Named Entity Recognition Systems

 🎯 Objective
Build and evaluate Named Entity Recognition (NER) systems using different approaches.

---

 📋 Tasks

 Task 1: Rule-Based NER
Create a rule-based NER system using regex patterns.

```python
import re
from typing import List, Tuple, Dict

class RuleBasedNER:
    """
    Rule-based Named Entity Recognition system.
    """
    def __init__(self):
        """Initialize with entity patterns."""
        self.patterns = {
            'EMAIL': r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b',
            'PHONE': r'[\+]?[(]?[0-9]{3}[)]?[-\s\.]?[0-9]{3}[-\s\.]?[0-9]{4}',
            'URL': r'https?://\S+|www\.\S+',
            'DATE': r'\d{4}-\d{2}-\d{2}|\d{1,2}/\d{1,2}/\d{4}',
            'CURRENCY': r'[$€£]\d{1,3}(?:,\d{3})(?:\.\d{2})?',
            'CREDIT_CARD': r'\b(?:\d[ -]?){13,16}\b',
            'IP_ADDRESS': r'\b(?:\d{1,3}\.){3}\d{1,3}\b',
        }
    
    def extract_entities(self, text: str) -> List[Tuple[str, str, int, int]]:
        """
        Extract entities from text.
        
        Args:
            text (str): Input text
            
        Returns:
            list: List of (entity_text, entity_type, start, end) tuples
        """
        entities = []
        
        for entity_type, pattern in self.patterns.items():
            for match in re.finditer(pattern, text, re.IGNORECASE):
                entity_text = match.group()
                start = match.start()
                end = match.end()
                entities.append((entity_text, entity_type, start, end))
        
         Sort by start position
        entities.sort(key=lambda x: x[2])
        
        return entities
    
    def extract_person_names(self, text: str) -> List[str]:
        """
        Extract person names using capitalization patterns.
        
        Args:
            text (str): Input text
            
        Returns:
            list: List of person names
        """
         Pattern: Two or more consecutive capitalized words
        pattern = r'\b[A-Z][a-z]+(?:\s+[A-Z][a-z]+)+\b'
        matches = re.findall(pattern, text)
        return matches
    
    def extract_organizations(self, text: str) -> List[str]:
        """
        Extract organizations using common suffixes.
        
        Args:
            text (str): Input text
            
        Returns:
            list: List of organizations
        """
        suffixes = ['Inc', 'Corp', 'LLC', 'Ltd', 'Co', 'Company']
        pattern = r'\b[A-Z][a-zA-Z]+(?:\s+(?:' + '|'.join(suffixes) + r'))\.?\b'
        matches = re.findall(pattern, text)
        return matches

 Test
ner = RuleBasedNER()

text = """
Contact John Smith at john.smith@company.com or call +1-555-123-4567.
Visit https://company.com for more information. Meeting scheduled for 2024-01-15.
Price: $1,234.56. IP: 192.168.1.1
"""

entities = ner.extract_entities(text)
print("Extracted Entities:")
for entity_text, entity_type, start, end in entities:
    print(f"  {entity_type}: {entity_text} (position: {start}-{end})")

print("\nPerson Names:", ner.extract_person_names(text))
print("Organizations:", ner.extract_organizations(text))
```

---

 Task 2: Statistical NER with spaCy
Use spaCy's pre-trained NER model and customize it.

```python
import spacy
from spacy import displacy

nlp = spacy.load('en_core_web_sm')

def analyze_entities(text: str) -> Dict:
    """
    Analyze entities using spaCy.
    
    Args:
        text (str): Input text
        
    Returns:
        dict: Entity analysis results
    """
    doc = nlp(text)
    
    entities = []
    for ent in doc.ents:
        entities.append({
            'text': ent.text,
            'label': ent.label_,
            'description': spacy.explain(ent.label_),
            'start': ent.start_char,
            'end': ent.end_char
        })
    
     Entity statistics
    entity_counts = {}
    for ent in doc.ents:
        entity_counts[ent.label_] = entity_counts.get(ent.label_, 0) + 1
    
    return {
        'entities': entities,
        'statistics': entity_counts,
        'sentence_count': len(list(doc.sents))
    }

 Test
text = """
Apple Inc. was founded by Steve Jobs and Steve Wozniak in Cupertino, California.
The company is now led by Tim Cook and has a market cap of over $2 trillion.
"""

results = analyze_entities(text)
print("Entities:")
for ent in results['entities']:
    print(f"  {ent['text']:30} | {ent['label']:10} | {ent['description']}")

print("\nStatistics:", results['statistics'])

 Visualize (in Jupyter)
 displacy.render(nlp(text), style='ent', jupyter=True)
```

---

 Task 3: Custom NER Training
Train a custom NER model with spaCy.

```python
import spacy
from spacy.training import Example
import random

def train_custom_ner(train_data: List[Tuple[str, Dict]], iterations: int = 50):
    """
    Train a custom NER model.
    
    Args:
        train_data (list): List of (text, annotations) tuples
        iterations (int): Number of training iterations
        
    Returns:
        trained model
    """
     Create blank model
    nlp = spacy.blank('en')
    
     Add NER pipeline
    if 'ner' not in nlp.pipe_names:
        ner = nlp.add_pipe('ner')
    else:
        ner = nlp.get_pipe('ner')
    
     Add labels
    for _, annotations in train_data:
        for ent in annotations.get('entities', []):
            ner.add_label(ent[2])
    
     Train
    optimizer = nlp.begin_training()
    
    for itn in range(iterations):
        random.shuffle(train_data)
        losses = {}
        
        for text, annotations in train_data:
            example = Example.from_dict(nlp.make_doc(text), annotations)
            nlp.update([example], sgd=optimizer, losses=losses)
        
        if itn % 10 == 0:
            print(f"Iteration {itn}, Loss: {losses['ner']:.4f}")
    
    return nlp

 Training data
TRAIN_DATA = [
    ("Apple is looking at buying U.K. startup for $1 billion", 
     {"entities": [(0, 5, "ORG"), (27, 31, "GPE"), (44, 54, "MONEY")]}),
    ("San Francisco considers banning sidewalk delivery robots",
     {"entities": [(0, 13, "GPE")]}),
    ("London is the capital of England",
     {"entities": [(0, 6, "GPE"), (24, 31, "GPE")]}),
]

 Train
nlp_custom = train_custom_ner(TRAIN_DATA, iterations=30)

 Test
test_text = "Apple is based in Cupertino, California"
doc = nlp_custom(test_text)

print("\nCustom NER Results:")
for ent in doc.ents:
    print(f"  {ent.text} - {ent.label_}")
```

---

 Task 4: NER Evaluation
Evaluate NER model performance with standard metrics.

```python
from typing import List, Tuple
from collections import defaultdict

def evaluate_ner(true_entities: List[Tuple[str, str, int, int]], 
                  pred_entities: List[Tuple[str, str, int, int]]) -> Dict:
    """
    Evaluate NER performance.
    
    Args:
        true_entities (list): Ground truth entities
        pred_entities (list): Predicted entities
        
    Returns:
        dict: Evaluation metrics
    """
     Calculate precision, recall, F1
    true_set = set((text, label, start, end) for text, label, start, end in true_entities)
    pred_set = set((text, label, start, end) for text, label, start, end in pred_entities)
    
     Exact match
    true_positives = len(true_set & pred_set)
    false_positives = len(pred_set - true_set)
    false_negatives = len(true_set - pred_set)
    
    precision = true_positives / (true_positives + false_positives) if (true_positives + false_positives) > 0 else 0
    recall = true_positives / (true_positives + false_negatives) if (true_positives + false_negatives) > 0 else 0
    f1 = 2  (precision  recall) / (precision + recall) if (precision + recall) > 0 else 0
    
     Per-entity-type metrics
    type_metrics = defaultdict(lambda: {'tp': 0, 'fp': 0, 'fn': 0})
    
    for text, label, start, end in true_entities:
        type_metrics[label]['fn'] += 1
        if (text, label, start, end) in pred_set:
            type_metrics[label]['tp'] += 1
            type_metrics[label]['fn'] -= 1
    
    for text, label, start, end in pred_entities:
        if (text, label, start, end) not in true_set:
            type_metrics[label]['fp'] += 1
    
    type_results = {}
    for label, metrics in type_metrics.items():
        tp, fp, fn = metrics['tp'], metrics['fp'], metrics['fn']
        type_precision = tp / (tp + fp) if (tp + fp) > 0 else 0
        type_recall = tp / (tp + fn) if (tp + fn) > 0 else 0
        type_f1 = 2  (type_precision  type_recall) / (type_precision + type_recall) if (type_precision + type_recall) > 0 else 0
        
        type_results[label] = {
            'precision': type_precision,
            'recall': type_recall,
            'f1': type_f1
        }
    
    return {
        'overall': {
            'precision': precision,
            'recall': recall,
            'f1': f1,
            'true_positives': true_positives,
            'false_positives': false_positives,
            'false_negatives': false_negatives
        },
        'per_type': type_results
    }

 Test
true_entities = [
    ("Apple", "ORG", 0, 5),
    ("Cupertino", "GPE", 20, 29),
]

pred_entities = [
    ("Apple", "ORG", 0, 5),
    ("Cupertino", "GPE", 20, 29),
    ("California", "GPE", 31, 41),   False positive
]

results = evaluate_ner(true_entities, pred_entities)
print("NER Evaluation:")
print(f"Overall - Precision: {results['overall']['precision']:.3f}")
print(f"          Recall: {results['overall']['recall']:.3f}")
print(f"          F1: {results['overall']['f1']:.3f}")
```

---

 Task 5: Entity Linking
Implement entity linking to knowledge bases.

```python
class EntityLinker:
    """
    Simple entity linking system.
    """
    def __init__(self):
        """Initialize with knowledge base."""
        self.knowledge_base = {
            'Apple': {
                'types': ['ORG', 'PRODUCT'],
                'description': 'Technology company',
                'aliases': ['Apple Inc', 'Apple Computer']
            },
            'Steve Jobs': {
                'types': ['PERSON'],
                'description': 'Co-founder of Apple',
                'aliases': ['Steve', 'Jobs']
            },
            'Cupertino': {
                'types': ['GPE'],
                'description': 'City in California',
                'aliases': ['Cupertino, CA']
            }
        }
    
    def link_entity(self, entity_text: str, entity_type: str = None) -> Dict:
        """
        Link entity to knowledge base.
        
        Args:
            entity_text (str): Entity text
            entity_type (str): Entity type (optional)
            
        Returns:
            dict: Linked entity information
        """
         Exact match
        if entity_text in self.knowledge_base:
            kb_entity = self.knowledge_base[entity_text]
            if entity_type is None or entity_type in kb_entity['types']:
                return {
                    'text': entity_text,
                    'linked': entity_text,
                    'knowledge': kb_entity,
                    'confidence': 1.0
                }
        
         Check aliases
        for entity_name, kb_entity in self.knowledge_base.items():
            if entity_text in kb_entity['aliases']:
                if entity_type is None or entity_type in kb_entity['types']:
                    return {
                        'text': entity_text,
                        'linked': entity_name,
                        'knowledge': kb_entity,
                        'confidence': 0.9
                    }
        
         Fuzzy match (simplified)
        for entity_name, kb_entity in self.knowledge_base.items():
            if entity_text.lower() in entity_name.lower() or entity_name.lower() in entity_text.lower():
                if entity_type is None or entity_type in kb_entity['types']:
                    return {
                        'text': entity_text,
                        'linked': entity_name,
                        'knowledge': kb_entity,
                        'confidence': 0.7
                    }
        
        return {
            'text': entity_text,
            'linked': None,
            'knowledge': None,
            'confidence': 0.0
        }
    
    def link_entities(self, entities: List[Tuple[str, str]]) -> List[Dict]:
        """
        Link multiple entities.
        
        Args:
            entities (list): List of (entity_text, entity_type) tuples
            
        Returns:
            list: List of linked entities
        """
        return [self.link_entity(text, label) for text, label in entities]

 Test
linker = EntityLinker()

entities = [
    ("Apple", "ORG"),
    ("Steve Jobs", "PERSON"),
    ("Cupertino", "GPE"),
    ("Unknown Entity", "ORG"),
]

linked = linker.link_entities(entities)
print("Entity Linking Results:")
for result in linked:
    print(f"  {result['text']:20} -> {result['linked']:20} (confidence: {result['confidence']:.2f})")
    if result['knowledge']:
        print(f"    Type: {result['knowledge']['types']}")
        print(f"    Description: {result['knowledge']['description']}")
```

---

 ✅ Completion Checklist

- [ ] Rule-based NER implemented
- [ ] spaCy NER analyzed
- [ ] Custom NER model trained
- [ ] NER evaluation completed
- [ ] Entity linking implemented
- [ ] All functions documented
- [ ] Test cases pass

---

 🎯 Extension Challenge

Create a complete NER pipeline with multiple approaches:

```python
class NERPipeline:
    """
    Complete NER pipeline combining multiple approaches.
    """
    def __init__(self, use_rule_based=True, use_spacy=True, use_custom=False):
        """
        Initialize NER pipeline.
        
        Args:
            use_rule_based (bool): Use rule-based NER
            use_spacy (bool): Use spaCy NER
            use_custom (bool): Use custom trained NER
        """
         Your implementation
        pass
    
    def extract(self, text: str) -> List[Dict]:
        """
        Extract entities using all enabled methods.
        
        Args:
            text (str): Input text
            
        Returns:
            list: Combined entity results
        """
         Your implementation
        pass
    
    def resolve_conflicts(self, entities: List[Dict]) -> List[Dict]:
        """
        Resolve conflicts between different NER methods.
        
        Args:
            entities (list): Entities from different methods
            
        Returns:
            list: Resolved entities
        """
         Your implementation
        pass
    
    def link_entities(self, entities: List[Dict]) -> List[Dict]:
        """
        Link entities to knowledge base.
        
        Args:
            entities (list): Extracted entities
            
        Returns:
            list: Linked entities
        """
         Your implementation
        pass
```
