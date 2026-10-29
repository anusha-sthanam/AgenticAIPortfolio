# Autonomous IT Support Agent

An educational Level 1 IT incident responder built with Python and OpenAI function calling. The agent investigates simulated server incidents, checks health metrics and logs, and decides whether to simulate a service restart or escalate to an engineer.

**All monitoring data, restarts, and escalations are simulated.** The script does not connect to real servers, restart services, or notify an on-call engineer. OpenAI model requests are real API calls.

## Features

- Inspect CPU usage, memory usage, and server status.
- Retrieve predefined server logs.
- Simulate restarting an overloaded service.
- Simulate escalation with an incident ticket response.
- Execute a multi-step tool-calling conversation until the model returns its final response.

## Technology

- Python
- OpenAI Python SDK
- OpenAI Chat Completions API with function calling
- Model configured in the source: `gpt-4.1-mini`
- Google Colab Secrets for API credentials

## Tools

| Function | Purpose |
| --- | --- |
| `get_server_health(server_id)` | Return predefined CPU, memory, and status metrics. |
| `fetch_recent_logs(server_id, lines=5)` | Return entries from a predefined log list. |
| `restart_service(server_id)` | Return a simulated successful restart response. |
| `escalate_to_engineer(summary)` | Return a simulated escalation assigned to an on-call engineer. |

## Included scenarios

| Server | CPU | Memory | Expected behavior |
| --- | --- | --- | --- |
| `payment-server-01` | 98% | 40% | Simulate a restart for high CPU and an unresponsive process. |
| `db-node-02` | 12% | 60% | Report healthy; no corrective action expected. |
| `auth-service-03` | 45% | 95% | Simulate a restart for high memory usage and out-of-memory errors. |
| `search-index-09` | 10% | 15% | Simulate escalation for an unreachable search dependency. |
| `frontend-node-04` | 25% | 30% | Report healthy; no corrective action expected. |

The runtime system prompt instructs the model to restart when CPU **or memory exceeds 90%**, and escalate critical dependency errors that a restart would not fix. These decisions are model-directed rather than enforced by deterministic policy checks; results can vary.

## Run in Google Colab

The supplied script was exported from Colab and imports `google.colab.userdata`. Colab is the intended environment for running it unchanged.

1. Create a Google Colab notebook.
2. Install the dependency in a notebook cell:

   ```python
   %pip install openai
   ```

3. Open **Secrets** in the Colab sidebar.
4. Add a secret named exactly `OPENAI_APIKEY` containing your OpenAI API key, and enable notebook access.
5. Upload `it_support_agent.py` to the Colab session.
6. Execute it from a notebook cell:

   ```python
   %run it_support_agent.py
   ```

The script automatically runs all five included scenarios in sequence. Each scenario may make multiple API requests. Your API account needs access to the configured model and sufficient API credit.

## Run locally with a small source change

The original script cannot run unchanged outside Colab. To use an environment variable locally, remove:

```python
from google.colab import userdata
```

Replace:

```python
client = OpenAI(api_key=userdata.get('OPENAI_APIKEY'))
```

with:

```python
client = OpenAI(api_key=os.environ['OPENAI_API_KEY'])
```

Create and activate a virtual environment, then install the SDK:

```bash
python -m venv .venv
```

Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
$env:OPENAI_API_KEY = 'YOUR_API_KEY'
```

macOS/Linux:

```bash
source .venv/bin/activate
export OPENAI_API_KEY='YOUR_API_KEY'
```

Install and run:

```bash
python -m pip install openai
python it_support_agent.py
```

Use your actual key only in your local environment. Never commit it to Git. The Colab secret name `OPENAI_APIKEY` and local environment variable name `OPENAI_API_KEY` differ intentionally in these instructions.

## Try another incident

Within the running notebook, after executing the script:

```python
run_it_agent('Search is failing on search-index-09. Investigate and explain the next step.')
```

To change the scenarios executed by the file itself, edit the `run_it_agent(...)` calls at the bottom. There is no interactive command-line input loop.

## How the agent works

1. Create a conversation containing the responder instructions and reported issue.
2. Send the conversation and four tool definitions to OpenAI.
3. Execute any requested functions using `AVAILABLE_FUNCTIONS`.
4. Append each tool result with its matching `tool_call_id`.
5. Repeat until the model produces a response without tool calls.

## Limitations

- Server metrics and logs are static mock data. A simulated restart does not change them or verify recovery.
- The sample scenarios are demonstrations, not automated tests with assertions.
- The agent loop has no maximum iteration count, retry policy, or comprehensive exception handling.
- Importing the module initializes the client and runs all five scenarios because the source has no `if __name__ == '__main__':` guard.
- Unknown server health checks return an error, while unknown server log requests return generic stable logs.
- `fetch_recent_logs` currently uses `logs[:lines]`, so it returns the first requested entries from its stored list rather than selecting a tail.
- Escalations always return the mock ticket ID `INC-999`.
- The introductory assignment mentions payment gateway errors, but no included log dataset contains that exact scenario.
- API keys and incident details sent to the model require appropriate handling. API usage may incur charges.

## Upload to GitHub

Upload these files to your repository:

```text
it_support_agent.py
README.md
```

You can also add a `requirements.txt` containing `openai`, and a `.gitignore` that excludes `.venv/`, `.env`, and `__pycache__/`.

## Validation

This README was prepared by reviewing the supplied source. Live OpenAI requests and Colab execution were not tested.
