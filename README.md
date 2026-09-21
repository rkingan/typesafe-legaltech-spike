# TypeSafe LegalTech Spike

Compares three models on a legal text classification task: **TypeSafe**, **Claude Fable**,
and **Claude Haiku 4.5**.

## The task

[LegalBench](https://huggingface.co/datasets/nguha/legalbench)'s Abercrombie test set: 95
trademark descriptions, each labeled with where it falls on the Abercrombie spectrum of
trademark distinctiveness — generic, descriptive, suggestive, arbitrary, or fanciful. Each
model is given the description and asked to pick one of the five labels.

The notebook reports accuracy, a confusion matrix, and per-call latency for each model.

## Files

| File | Purpose |
|---|---|
| `abercrombie_benchmark.ipynb` | The experiment |
| `requirements.in` / `requirements.txt` | Runtime dependencies (pinned) |
| `requirements-dev.in` / `requirements-dev.txt` | Runtime + Jupyter + `pip-tools` (pinned) |

## Setup

1. **Create a virtual environment** (skip if `.venv/` already exists):

   ```bash
   python3 -m venv .venv
   ```

2. **Install dependencies:**

   ```bash
   .venv/bin/pip install -r requirements-dev.txt
   ```

3. **Create a `.env` file** in the repo root with these variables:

   | Variable | Required | Notes |
   |---|---|---|
   | `TYPESAFE_API_KEY` | Yes | TypeSafe API key |
   | `ANTHROPIC_API_KEY` | Yes | Anthropic API key — used for both Claude Fable and Claude Haiku 4.5 |
   | `HF_TOKEN` | No | Hugging Face token; the dataset is public, but a token avoids anonymous rate limits |

   **`.env` is gitignored — never commit it or push it to origin.** It holds live API keys.

4. **Register a Jupyter kernel** for the venv (skip if it's already registered):

   ```bash
   .venv/bin/python -m ipykernel install --user --name typesafe-legaltech-spike \
       --display-name "typesafe-legaltech-spike (.venv)"
   ```

## Running it

```bash
.venv/bin/jupyter lab abercrombie_benchmark.ipynb
```

Open the notebook, select the **typesafe-legaltech-spike (.venv)** kernel, and run all cells
top to bottom. The dataset downloads automatically from Hugging Face on first run and is
cached locally under `data/` (gitignored).

Running the full notebook makes about 285 API calls (95 rows × 3 models) — small enough to
run in a couple of minutes, but each one is a billed API call against your TypeSafe and
Anthropic accounts.

## Updating dependencies

Add a package to `requirements.in` (runtime) or `requirements-dev.in` (dev-only), then
re-pin and reinstall:

```bash
.venv/bin/pip-compile --output-file=requirements.txt requirements.in
.venv/bin/pip-compile --output-file=requirements-dev.txt requirements-dev.in
.venv/bin/pip install -r requirements-dev.txt
```
