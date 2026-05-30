```markdown
# AI Security and Model Evaluation Assessments

This repository contains a collection of Python scripts demonstrating various machine learning security, interpretability, and vulnerability concepts. These implementations are consolidated from five different assessments covering model extraction, prediction explanation, model inversion, data poisoning, and large language model (LLM) prompt injection.

---

## Repository Structure

```text
├── extraction_assessment.py    # ResNet-50 classification extraction using MobileNetV2
├── assessments_assessment.py   # Interpretability of sequence classification using Alibi AnchorText
├── inversion_assessment.py     # MIFace model inversion attack targeting an MNIST CNN
├── poisoning_assessment.py     # Clean-label poisoning attack on CIFAR-10 data
├── llm_assessment.py           # Prompt injection targeting a semantic-search QA chatbot
├── other_mnist.pt              # Pretrained model weights for the inversion task (external file)
├── cifar.py                    # Helper definitions for CIFAR-10 model training (external file)
└── README.md                   # Project documentation
```

---

## Installation & Prerequisites

To execute these scripts, you need a Python environment with access to a GPU (recommended for training and inference, though CPU fallback is supported) and standard machine learning dependencies installed.

### 1. Install Dependencies
Install all required libraries using `pip`:

```bash
pip install torch torchvision numpy matplotlib pillow scikit-learn \
            transformers sentence-transformers datasets faker \
            adversarial-robustness-toolbox spacy
```

### 2. Download SpaCy Model
The prediction explanation script utilizes SpaCy's medium English web model. Download it directly:

```bash
python -m spacy download en_core_web_md
```

### 3. Required Pretrained Files
Ensure the following files are located in your working directory alongside the scripts (if running locally outside the original platform environment):
* `other_mnist.pt` (used by `inversion_assessment.py`)
* `cifar.py` (used by `poisoning_assessment.py`)

---

## Script Overviews & Execution

### 1. Model Extraction (`extraction_assessment.py`)
* **Objective:** Extract classification boundaries from a black-box ResNet-50 victim model (pizza vs. hotdog classifier) to train a lightweight MobileNetV2 proxy model.
* **Mechanism:** Queries the victim model using filtered inputs from the `food101` dataset to label a custom target training set.
* **Execution:**
  ```bash
  python extraction_assessment.py
  ```
* **Expected Artifacts (Saved to `my_assessment/`):**
  * `extraction_counts.json` — Distribution of query labels.
  * `extraction_proxy_scores.npy` — Scores produced by the newly trained proxy model.

### 2. Explaining Model Predictions (`assessments_assessment.py`)
* **Objective:** Use the Alibi library's `AnchorText` explainer to find the precise keywords (anchors) that cause an OpenAI RoBERTa sequence classifier to classify text as "Fake" (machine-generated).
* **Mechanism:** Generates perturbational variants of a target text prompt to find words that hold the highest precision in forcing the classification outcome.
* **Execution:**
  ```bash
  python assessments_assessment.py
  ```
* **Expected Artifacts (Saved to `my_assessment/`):**
  * `assessments_submission.json` — The top 3 keywords contributing to the "Fake" classification.

### 3. Model Inversion (`inversion_assessment.py`)
* **Objective:** Reconstruct representative input features for specified output classes using a model inversion attack.
* **Mechanism:** Runs the `MIFace` attack from the Adversarial Robustness Toolbox (ART) to optimize pixel matrices, generating reconstructions for classes `1`, `7`, and `9` from the `other_mnist.pt` model.
* **Execution:**
  ```bash
  python inversion_assessment.py
  ```
* **Expected Artifacts (Saved to `my_assessment/`):**
  * `inversion_submission.npy` — Generated image representations shaped as `(3, 1, 28, 28)`.

### 4. Clean-Label Data Poisoning (`poisoning_assessment.py`)
* **Objective:** Execute a clean-label poisoning attack targeting a CIFAR-10 classification model.
* **Mechanism:** Replaces all instances of the target training class (frogs, index 6) with copies of another class (cats, index 3) while keeping original labels unmodified. When a model trains on this data, it associates cat-like features with the frog classification label.
* **Execution:**
  ```bash
  python poisoning_assessment.py
  ```
* **Expected Artifacts (Saved to `my_assessment/`):**
  * `poisoning_dataset.npz` — The exported poisoned CIFAR-10 training set.
  * `poisoning_model` — State dictionary of the model trained on the poisoned dataset.

### 5. LLM Prompt Injection (`llm_assessment.py`)
* **Objective:** Modify the outcome of a local question-answering pipeline using prompt injection.
* **Mechanism:** Searches a mock directory using semantic similarity to retrieve personal data. A malicious input string is injected into the query that overrides the retrieved database facts, coercing the QA model into returning a customized favorite color.
* **Execution:**
  ```bash
  python llm_assessment.py
  ```
* **Expected Artifacts (Saved to `my_assessment/`):**
  * `llm_submission.txt` — The successful prompt injection payload.

---

## Verifying Output Generation

Once all tasks have been successfully executed, your output directory structure should look like this:

```text
my_assessment/
├── extraction_counts.json
├── extraction_proxy_scores.npy
├── assessments_submission.json
├── inversion_submission.npy
├── poisoning_dataset.npz
├── poisoning_model
└── llm_submission.txt
```
```
