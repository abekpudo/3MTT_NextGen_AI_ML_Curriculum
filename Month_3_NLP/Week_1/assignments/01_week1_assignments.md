 Week 1 Assignment: Complete NLP Preprocessing Pipeline

 📋 Assignment Overview

Build a production-ready text preprocessing and vectorization pipeline that handles multiple text formats, tokenization strategies, and prepares data for downstream NLP tasks.

Weight: 100 points (Week 1 Grade)  
Due: End of Week 1  
Submission: GitHub repository with Jupyter notebook and Python modules

---

 🎯 Learning Objectives

By completing this assignment, you will:
- Master text preprocessing techniques for various text sources
- Implement and compare multiple tokenization strategies
- Build feature extraction pipelines (BoW, TF-IDF, n-grams)
- Handle multilingual text processing
- Create reusable, production-ready NLP components
- Evaluate preprocessing impact on downstream tasks

---

 📊 Grading Rubric

 Part A: Text Cleaning Pipeline (25 points)

 1. HTML & URL Cleaning (5 points)
- [ ] Remove HTML tags completely (2 pts)
- [ ] Remove URLs and email addresses (2 pts)
- [ ] Preserve text structure (1 pt)

Evidence Required:
- Function code with docstrings
- Test cases showing before/after
- Edge case handling

 2. Social Media Normalization (8 points)
- [ ] Handle emojis and emoticons (2 pts)
- [ ] Process hashtags and mentions (2 pts)
- [ ] Convert slang and abbreviations (2 pts)
- [ ] Remove excessive punctuation (2 pts)

Evidence Required:
- Social media normalization function
- Test cases for each feature
- Documentation of slang dictionary

 3. Unicode Normalization (7 points)
- [ ] Implement all normalization forms (3 pts)
- [ ] Handle different character encodings (2 pts)
- [ ] Document form selection criteria (2 pts)

Evidence Required:
- Unicode normalization function
- Comparison of forms
- Use case documentation

 4. Custom Domain Handling (5 points)
- [ ] Create domain-specific cleaner (2 pts)
- [ ] Handle domain-specific entities (2 pts)
- [ ] Configurable rules (1 pt)

Evidence Required:
- Domain cleaner class
- Configuration examples
- Test cases

---

 Part B: Tokenization Systems (25 points)

 1. Multi-Tokenizer Comparison (8 points)
- [ ] Implement 3+ tokenization methods (3 pts)
- [ ] Performance benchmarking (2 pts)
- [ ] Detailed comparison report (3 pts)

Evidence Required:
- Comparison code
- Benchmark results
- Analysis report

 2. Subword Tokenization (8 points)
- [ ] Implement simplified BPE (4 pts)
- [ ] Compare with HuggingFace tokenizers (2 pts)
- [ ] Analyze vocabulary trade-offs (2 pts)

Evidence Required:
- BPE implementation
- Comparison results
- Trade-off analysis

 3. Custom Tokenizer (5 points)
- [ ] Domain-specific tokenizer (3 pts)
- [ ] Handle edge cases (1 pt)
- [ ] Documentation (1 pt)

Evidence Required:
- Custom tokenizer code
- Test cases
- Usage examples

 4. Tokenization Impact Analysis (4 points)
- [ ] Test on downstream task (2 pts)
- [ ] Document performance differences (2 pts)

Evidence Required:
- Impact analysis
- Performance metrics

---

 Part C: Feature Extraction (25 points)

 1. BoW & TF-IDF Implementation (10 points)
- [ ] Implement from scratch (5 pts)
- [ ] Compare with sklearn (3 pts)
- [ ] Performance analysis (2 pts)

Evidence Required:
- From-scratch implementations
- Comparison results
- Performance analysis

 2. N-gram Analysis (8 points)
- [ ] Implement n-gram extraction (3 pts)
- [ ] Analyze different n-gram ranges (3 pts)
- [ ] Optimize for task (2 pts)

Evidence Required:
- N-gram implementation
- Analysis results
- Optimization notes

 3. Feature Selection (7 points)
- [ ] Implement selection methods (3 pts)
- [ ] Compare different strategies (2 pts)
- [ ] Document impact (2 pts)

Evidence Required:
- Selection implementation
- Comparison results
- Impact documentation

---

 Part D: Integration & Testing (15 points)

 1. Complete Pipeline Class (8 points)
- [ ] Unified preprocessing pipeline (4 pts)
- [ ] Configurable options (2 pts)
- [ ] Error handling (2 pts)

Evidence Required:
- Pipeline class code
- Configuration examples
- Error handling tests

 2. Unit Tests (4 points)
- [ ] Test all components (2 pts)
- [ ] Edge case coverage (1 pt)
- [ ] Test documentation (1 pt)

Evidence Required:
- Test suite
- Test coverage report
- Test documentation

 3. Documentation (3 points)
- [ ] API documentation (1 pt)
- [ ] Usage examples (1 pt)
- [ ] README (1 pt)

Evidence Required:
- Docstrings
- Example notebooks
- README file

---

 Part E: Analysis & Reporting (10 points)

 1. Preprocessing Impact (5 points)
- [ ] Compare clean vs raw data (2 pts)
- [ ] Document performance differences (2 pts)
- [ ] Provide recommendations (1 pt)

Evidence Required:
- Impact analysis
- Performance comparison
- Recommendations

 2. Failure Analysis (5 points)
- [ ] Identify failure cases (2 pts)
- [ ] Explain root causes (2 pts)
- [ ] Propose solutions (1 pt)

Evidence Required:
- Failure case documentation
- Root cause analysis
- Proposed solutions

---

 📁 Deliverables

 1. Code Files
- `preprocessing.py` - All preprocessing functions and classes
- `tokenization.py` - Tokenization implementations
- `feature_extraction.py` - Feature extraction methods
- `pipeline.py` - Complete pipeline class
- `test_pipeline.py` - Unit tests

 2. Documentation
- `README.md` - Project overview and usage
- `API.md` - API documentation
- `examples.ipynb` - Jupyter notebook with examples

 3. Analysis
- `analysis_report.md` - Performance and impact analysis
- `failure_analysis.md` - Failure case documentation

 4. Configuration
- `config.yaml` - Default configuration
- `requirements.txt` - Dependencies

---

 🎯 Bonus Opportunities (Up to 10 extra points)

1. Multi-language Support (+5 points)
   - Support for 3+ languages
   - Language detection
   - Language-specific preprocessing

2. Performance Optimization (+3 points)
   - Vectorized operations
   - Caching strategies
   - Batch processing

3. Advanced Features (+2 points)
   - Parallel processing
   - Streaming support
   - Custom metrics

---

 ✅ Submission Checklist

- [ ] All code files submitted
- [ ] Comprehensive documentation
- [ ] Unit tests passing
- [ ] Analysis report complete
- [ ] Examples runnable
- [ ] README with setup instructions
- [ ] Requirements.txt included
- [ ] Git history showing iterative development

---

 🚀 Getting Started

```bash
 Create project structure
mkdir nlp_preprocessing
cd nlp_preprocessing

 Create virtual environment
python -m venv venv
source venv/bin/activate   On Windows: venv\Scripts\activate

 Install dependencies
pip install nltk spacy scikit-learn pandas numpy
python -m spacy download en_core_web_sm

 Download NLTK data
python -c "import nltk; nltk.download('punkt'); nltk.download('stopwords'); nltk.download('wordnet')"

 Create files
touch preprocessing.py tokenization.py feature_extraction.py pipeline.py
touch test_pipeline.py
touch README.md API.md analysis_report.md
```

---

 📚 Additional Resources

- [NLTK Documentation](https://www.nltk.org/)
- [spaCy Documentation](https://spacy.io/)
- [scikit-learn Text Features](https://scikit-learn.org/stable/modules/feature_extraction.html)
- [HuggingFace Tokenizers](https://huggingface.co/docs/tokenizers/)

---

Questions? Refer to lecture notes and exercises for guidance.

---
