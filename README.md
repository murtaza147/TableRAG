# TableRAG MVP

A small Python prototype inspired by [*TableRAG: Million-Token Table Understanding with Language Models*](https://arxiv.org/abs/2410.04739).

TableRAG makes table question answering more scalable by retrieving relevant column and cell information instead of placing the entire table in an LLM prompt.

## Current status

The [`main.ipynb`](./main.ipynb) notebook implements the initial indexing steps using the Titanic dataset from OpenML (dataset ID `40945`):

- Loads the data and saves a local Parquet copy for reuse.
- Builds a schema profile with inferred column types and representative values: numeric/datetime ranges or the most frequent categorical values.
- Builds a frequency-ranked catalog of distinct `(column, value)` pairs, excluding missing values and capped at `B = 10,000`.

The current Titanic catalog contains 3,343 pairs, so the budget cap does not truncate it. The original DataFrame is retained for later computations.

## Setup

The notebook was run with Python 3.14.6. From the project directory, create a virtual environment and install the pinned dependencies.

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

The requirements file includes the notebook runtime and the libraries used to fetch, process, and save the dataset.

## Run the notebook

Open `main.ipynb` in Jupyter or VS Code, select the `.venv` Python environment as its kernel, and run the cells from top to bottom. On the first run, the notebook fetches OpenML dataset `40945` and writes `titanic.parquet` to the current working directory. Later runs load the saved Parquet file.

## Planned work

- Expand questions into separate schema and cell search queries using an LLM.
- Embed queries and indexed schema/cell entries, then retrieve relevant results.
- Use retrieved evidence with a solver that can query the original table.

Titanic is a small development dataset for validating the prototype; it does not demonstrate million-token scalability.
