# TableRAG MVP

A small Python prototype inspired by [*TableRAG: Million-Token Table Understanding with Language Models*](https://arxiv.org/abs/2410.04739).

TableRAG makes table question answering more scalable by retrieving relevant column and cell information instead of placing the entire table in an LLM prompt.

## Current status

The [`main.ipynb`](./main.ipynb) notebook uses the Titanic dataset from OpenML (dataset ID `40945`) and currently:

- Loads the data and saves a local Parquet copy for reuse.
- Builds a schema profile with inferred column types and representative values: numeric/datetime ranges or the most frequent categorical values.
- Builds a frequency-ranked catalog of distinct `(column, value)` pairs, excluding missing values and capped at `B = 10,000`.
- Expands a question into separate schema-retrieval and cell-retrieval query lists using a local Transformers model.

The current Titanic catalog contains 3,343 pairs, so the budget cap does not truncate it. The original DataFrame is retained for later computations.

Query expansion currently uses `Qwen/Qwen2.5-0.5B-Instruct`. The model and tokenizer are downloaded from Hugging Face Hub and cached on the machine on first use; no account or token is needed for this public model. Actual retrieval over the schema profile and cell catalog, and answer generation from retrieved evidence, are not implemented yet.

## Setup

The notebook was developed on macOS with Python 3.14.6. The `requirements.txt` file pins that development environment.

On macOS or Linux:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

On Windows PowerShell:

```powershell
py -3.14 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
```

On Windows PowerShell:

```powershell
py -3.14 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
```

Check that the Python version and PyTorch build you install support your target hardware. In particular, the commands above do not configure AMD GPU acceleration.

## Run the notebook

Open `main.ipynb` in Jupyter or VS Code, select the `.venv` Python environment as its kernel, and run the cells from top to bottom. On the first run, the notebook fetches OpenML dataset `40945` and writes `titanic.parquet` to the current working directory. Later runs load the saved Parquet file.

The notebook currently selects Apple MPS when available and otherwise uses the CPU.

## Planned work

- Retrieve relevant schema profiles and cell-catalog entries from the expanded query lists.
- Use retrieved evidence with a solver that can query the original table.
- Evaluate retrieval and answer quality on additional questions and datasets.

Titanic is a small development dataset for validating the prototype; it does not demonstrate million-token scalability.
