
# C-to-Rust Fine-tuning Pipeline

**Project Overview**

This project demonstrates a complete fine-tuning pipeline for translating C code to safe, idiomatic Rust using OpenAI's API and the CRUST-bench dataset. Built as part of a technical interview assessment to showcase ML engineering capabilities in code translation tasks.

## Objective

Create a minimal fine-tuning pipeline that:

- Ingests samples from CRUST-bench dataset

- Formats data for instruction-style fine-tuning (C input → Rust output)

- Configures and executes OpenAI fine-tuning job

- Evaluates outputs qualitatively for correctness and compilability

- Provides comprehensive analysis and visualization

## Key Results

- **Dataset**: 18 high-quality C→Rust translation pairs from CRUST-bench

- **Model**: GPT-4o-mini fine-tuned for code translation

- **Quality Focus**: Memory safety, idiomatic Rust patterns, error handling

- **Cost**: ~$39 for complete fine-tuning pipeline

## Quick Start

**Complete Pipeline in One Notebook:**

```bash

# 1. Setup
git clone https://github.com/tapjason/C-to-Rust-Fine-tuning-Pipeline.git
cd C-to-Rust-Fine-tuning-Pipeline
pip install -r requirements.txt

# 2. Configure API
set OPENAI_API_KEY="your_openai_api_key_here"

# 3. Run Complete Pipeline
jupyter notebook c2rust_finetuning.ipynb

```

The notebook demonstrates the entire ML engineering workflow from data ingestion to model evaluation.

## Project Structure

```
├── c2rust_finetuning.ipynb # Main pipeline notebook
├── README.md # This documentation
├── requirements.txt # Python dependencies
├── .gitignore # Git configuration
└── o3_fine_tuning_outputs/ # Generated outputs
├── train_o3_crust.jsonl # Training data
├── val_o3_crust.jsonl # Validation data
├── real_job_info.json # Fine-tuning job details
└── evaluation_results.json # Model evaluation
```

## Technical Implementation

### 1. Dataset Formatting Logic

**CRUST-bench Processing:**

- Automatic download and extraction of CRUST-bench dataset

- Quality-based filtering using custom scoring algorithm

- Selection of 18 high-quality C→Rust translation pairs

- Token-aware preprocessing (max 1500 tokens per example)

**Quality Scoring Criteria:**

```python
def calculate_example_quality(c_code, rust_code):
	score = 0
	
	# Memory management patterns (malloc/free → Vec/Box)
	if 'malloc' in c_code: score += 3
	# Safe Rust types (Result, Option)
	if 'Result<' in rust_code: score += 2
	# Idiomatic patterns (impl, pub fn)
	if 'impl' in rust_code: score += 2
	# Error handling improvements
	if '?' in rust_code: score += 1
	
	return score

```

### 2. Fine-tuning Configuration

**OpenAI API Setup:**

```python
job_config = {
	'model': 'gpt-4o-mini-2024-07-18',
	'training_file': 'train_o3_crust.jsonl',
	'validation_file': 'val_o3_crust.jsonl',
	'hyperparameters': {
		'n_epochs': 3,
		'learning_rate_multiplier': 2.0,
		'batch_size': 'auto'
	},
	'suffix': 'crust-translator'
}
```

**Instruction Format:**
```json
{
	"messages": [
		{
			"role": "system",
			"content": "You are an expert C-to-Rust translator..."
		},
		{
			"role": "user",
			"content": "Translate this C code to safe Rust:\n```c\nvoid* ptr = malloc(100);\n```"
		},
		{
			"role": "assistant",
			"content": "```rust\nlet vec: Vec<u8> = Vec::with_capacity(100);\n```"
		}
	]
}
```
### Qualitative Evaluation

**Evaluation Metrics:**

- **Memory Safety**: Eliminates manual malloc/free

- **Error Handling**: NULL checks → Option/Result types

- **Rust Idioms**: Uses Vec, iterators, proper naming

- **Compilability**: Generated code passes `rustc` checks

- **Functionality**: Maintains original C behavior
