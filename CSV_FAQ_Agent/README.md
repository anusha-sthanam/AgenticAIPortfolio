# CSV FAQ Agent

A Python command-line assistant that answers questions across four CSV datasets using OpenAI, LangChain, and pandas.

## Datasets

| File | Topic |
| --- | --- |
| `saas_docs.csv` | SaaS documentation and API usage |
| `credit_card_terms.csv` | Credit card terms, rates, and fees |
| `hospital_policy.csv` | Hospital administrative policies |
| `ecommerce_faqs.csv` | Shipping, returns, and product FAQs |

On the first run, missing datasets are downloaded from the Google Drive links configured in `hello_agent.py`. Existing files are reused. Downloads require accessible Drive files; availability and data contents have not been verified. You may instead place your own CSVs with these four filenames in `data/`.

## Requirements

- Python 3.10 or newer
- An OpenAI API key with API access and available credit
- Internet access for model requests and initial dataset downloads

## Setup

Download or clone this repository, open a terminal in its folder, and create a virtual environment:

```bash
python -m venv .venv
```

Activate it on Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

Or on macOS/Linux:

```bash
source .venv/bin/activate
```

Install dependencies:

```bash
python -m pip install -r requirements.txt
```

Dependencies are not pinned; a full dependency compatibility test has not been performed.

## Run

**Security:** This agent executes model-generated Python with the permissions of your Python process. The virtual environment does not sandbox it. Run it only in an isolated container or disposable environment with trusted questions and datasets, no sensitive files, and limited credentials. The flag below acknowledges this behavior; it does not provide isolation.

```bash
python hello_agent.py --allow-code-execution
```

Enter your API key at the hidden prompt. Alternatively, set `OPENAI_API_KEY` in your environment. Do not put keys in source code or commit them to GitHub. OpenAI API usage is billed separately from ChatGPT subscriptions.

Optional arguments:

```bash
python hello_agent.py --allow-code-execution --data-dir ./data --model gpt-4o-mini --verbose
```

The model can also be set with `OPENAI_MODEL`. Model availability depends on your API account. Verbose mode may print dataset information and tool activity.

Example questions:

- What are the hospital visiting hours?
- What does the documentation say about API limits?
- What are the return conditions?

Type `exit`, `quit`, or `q` to stop.

## How it works

1. Download missing CSVs and load them into pandas DataFrames.
2. Map each DataFrame to its topic.
3. Let the agent search the tables with Python and compose an answer.

The prompt asks the model to use only dataset information and name the source file. This is an instruction, not a guarantee of correctness. Review answers against the original data. CSV contents, including sample rows in the initial prompt and tool results, may be transmitted to OpenAI. Do not use private data without appropriate authorization.

## Upload to GitHub

1. Create a new repository on GitHub, for example `csv-faq-agent`.
2. Choose **Add file → Upload files**.
3. Upload `hello_agent.py`, `README.md`, `requirements.txt`, and `.gitignore`.
4. Select **Commit changes**.

Do not upload `.venv/`, API keys, `.env` files, or private datasets. Confirm you have permission before publishing the configured dataset links or distributing their contents. No dataset rights or project license are implied.

## Troubleshooting

- **Missing module:** Activate the virtual environment and install `requirements.txt`.
- **Download failure:** Check Drive access or supply the four CSVs locally.
- **Invalid key, quota, or model error:** Check your API credentials, billing, and model access.
- **CSV parsing error:** Verify the input is a valid CSV rather than a downloaded HTML page.

## Validation status

Python syntax and command-line help were checked. Live Drive downloads, dependency integration, and paid OpenAI calls were not tested.
