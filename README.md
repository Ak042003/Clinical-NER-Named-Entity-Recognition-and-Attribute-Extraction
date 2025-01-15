# AIQuest Hackathon 2024 - Clinical NER and Attribute Extraction

Welcome to the AIQuest Hackathon 2024 GitHub repository, where I secured 1st place! 🚀 <br>
This repository showcases my solution to the Clinical Named Entity Recognition (NER) and Attribute Extraction problem. The solution leverages state-of-the-art AI and NLP techniques to address real-world challenges in healthcare data management.

----

## 🏆 Problem Statement
### Title: Developing a System for Clinical NER and Attribute Extraction

### Objective:
The goal was to develop a system capable of:
1. **Named Entity Recognition and Classification (NERC)**: Identifying clinically relevant entities like:
   - **Medical Entities**: Problems, Procedures, Drugs.
   - **Social Determinants of Health (SDoH)**: Education, occupation, living situation, income, healthcare access, etc.
2. **Attribute Extraction**: For each entity, extracting attributes like:
   - **Drugs**: Dosage, Mode of Administration, Frequency.
3. **Bonus**: Extracting:
   - **Assertion**: Likely, Unlikely, Uncertain.
   - **Temporality**: Current, Upcoming, Clinical History.
   - **Subject**: Patient, Family Member.

---

## 🗂️ Dataset
The dataset consisted of 700 medical discharge summaries (MDS). Each entry included JSONs listing named entities with their:
- **Span**: Start and end positions in the text.
- **Class**: Type of entity.
- **Attributes**: Relevant entity details.

---
## 🔎 Data Analysis and Cleaning
### Key Observations:
- **Span Errors**: The last 145 entries had incorrect spans, causing misaligned training data. These records were removed.
- **Incomplete Labels**: Around 50 records had incomplete entity tagging, leading to negative training, and were excluded.
- **Final Dataset**: Around 350 clean and high-quality records.

---

## ⚙️ Approach and Solution
### Model Pipelines
1. **SpaCy (Baseline)**:
   - Used `en_core_web_md` for fine-tuning.
   - **Advantages**: Quick processing time (0.05 seconds per record).
   - **Disadvantages**: Metrics were unsatisfactory.

2. **LLMs (Llama 3.1 8B with fp16 quantization)**:
   - **Prompt Design**:
     - **System Prompt**: Explained the task, input/output format, and entity descriptions.
     - **User Prompt**:
       - **All-in-One**: Entire MDS passed at once. Metrics were poor.
       - **Chunked Approach**: MDS split into smaller chunks, processed sequentially. Improved metrics but infeasible due to high inference time.

3. **ClinicalBERT**:
   - Pretrained on PUBMed abstracts for clinical context and jargon.
   - **BIO Annotation**: Tokenization using BIO format for tagging (e.g., transfusion → trans: B-Procedure, ##fusion: I-Procedure).
   - **Approaches**:
     - **Unified Model**: Single ClinicalBERT for all entity types. Achieved ~70% metrics with 0.07 seconds inference time.
     - **Specialized Models**: Separate ClinicalBERT models for Problems, Procedures, and Drugs.
       - **Advantages**:
         - Improved performance due to specialization.
         - Easier fine-tuning for specific entities with future data.
       - **Results**: Significant performance improvement with the same inference time.

---

## ⚡ Results
- **Performance**: Achieved state-of-the-art metrics using specialized ClinicalBERT models.
- **Inference Time**: Maintained ~0.07 seconds per record.
- **Scalability**: Future improvements can parallelize inference using GPU-based frameworks like NVIDIA Triton.

---

## 📂 Repository Structure
```plaintext
├── data/
│   ├── cleaned_dataset.csv     # Final cleaned dataset
├── notebooks/
│   ├── preprocessing.ipynb     # Data preprocessing steps
│   ├── training_clinicalbert.ipynb  # Fine-tuning ClinicalBERT
│   ├── inference_pipeline.ipynb    # Inference pipeline
├── models/
│   ├── clinicalbert_problem/       # Fine-tuned ClinicalBERT for Problems
│   ├── clinicalbert_procedure/     # Fine-tuned ClinicalBERT for Procedures
│   ├── clinicalbert_drug/          # Fine-tuned ClinicalBERT for Drugs
├── results/
│   ├── metrics_report.json     # Model performance metrics
├── README.md                   # Project description (this file)
