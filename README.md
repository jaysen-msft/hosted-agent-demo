# Foundry Hosted Agent Demo: LangChain Financial Portfolio Analyst

This repository demonstrates how to build a simple **LangChain / LangGraph-based financial portfolio analysis agent** and prepare it for deployment as a **Microsoft Foundry Hosted Agent**.

The agent uses a synthetic financial portfolio PDF, retrieves relevant portfolio context, analyzes allocation and risk exposure, and returns an executive-style investment summary.

> This project is intended as a demo/reference implementation. It uses synthetic data and should not be treated as financial advice.

---

## What This Demo Shows

This repo demonstrates:

- Building a LangChain agent over a financial PDF document
- Loading and chunking a PDF portfolio statement
- Creating embeddings for document retrieval
- Using a local vector store for RAG
- Creating simple portfolio analysis tools
- Running the agent locally in a Jupyter notebook
- Preparing the agent logic for Foundry Hosted Agent deployment
- Adding observability hooks for tracing and monitoring

---

## Architecture Overview

```text
User Question
    |
    v
LangChain / LangGraph Agent
    |
    |-- PDF Retriever Tool
    |       |
    |       v
    |   Portfolio PDF Chunks
    |
    |-- Portfolio Analysis Tool
    |       |
    |       v
    |   Allocation, Risk, Concentration, Sector Exposure
    |
    v
Azure OpenAI / Foundry Model
    |
    v
Final Portfolio Insight
```

For hosted deployment:

```text
Microsoft Foundry Agent Service
    |
    v
Hosted Agent Runtime
    |
    v
Containerized LangChain Agent
    |
    |-- Azure OpenAI / Foundry Model
    |-- Financial PDF RAG
    |-- Observability / Tracing
```

---

## Repository Contents

```text
hosted_agent_demo/
│
├── langchain_financial_pdf_to_foundry_hosted_agent.ipynb
├── sample_financial_portfolio_statement.pdf
├── .env.example
├── .gitignore
├── README.md
│
└── generated files / optional runtime folders
    ├── storage/
    └── __pycache__/
```

Recommended production structure:

```text
hosted_agent_demo/
│
├── notebooks/
│   └── langchain_financial_pdf_to_foundry_hosted_agent.ipynb
│
├── app/
│   ├── app.py
│   ├── agent.py
│   ├── tools.py
│   └── rag.py
│
├── data/
│   └── sample_financial_portfolio_statement.pdf
│
├── deployment/
│   ├── Dockerfile
│   └── requirements.txt
│
├── .env.example
├── .gitignore
└── README.md
```

---

## Prerequisites

You need the following installed locally:

- Python 3.10 or later
- VS Code
- Jupyter extension for VS Code
- Git
- Azure CLI
- Access to Azure OpenAI or Microsoft Foundry model deployment
- Access to a Microsoft Foundry project

Optional:

- Docker Desktop
- Azure Container Registry
- Application Insights or Azure Monitor for observability

---

## Setup

### 1. Clone the repository

```bash
git clone https://github.com/jaysen-msft/hosted-agent-demo.git
cd hosted-agent-demo
```

Or, if you already created the folder locally:

```bash
cd hosted_agent_demo
```

---

### 2. Create a Python virtual environment

On Windows PowerShell:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

On macOS/Linux:

```bash
python -m venv .venv
source .venv/bin/activate
```

---

### 3. Install dependencies

```bash
pip install --upgrade pip
pip install -r requirements.txt
```

If you are running only the notebook first, install the core packages:

```bash
pip install langchain langchain-openai langchain-community langgraph pypdf faiss-cpu python-dotenv openai
```

---

### 4. Configure environment variables

Create a local `.env` file:

```bash
cp .env.example .env
```

On Windows PowerShell:

```powershell
Copy-Item .env.example .env
```

Update `.env` with your own values:

```env
AZURE_OPENAI_ENDPOINT=https://<your-resource>.openai.azure.com/
AZURE_OPENAI_API_KEY=<your-key>
AZURE_OPENAI_API_VERSION=2025-01-01-preview
AZURE_OPENAI_CHAT_DEPLOYMENT=gpt-4.1
AZURE_OPENAI_EMBEDDING_DEPLOYMENT=text-embedding-3-large

AZURE_AI_PROJECT_ENDPOINT=https://<your-foundry-resource>.services.ai.azure.com/api/projects/<your-project-name>
AZURE_SUBSCRIPTION_ID=<your-subscription-id>
AZURE_RESOURCE_GROUP=<your-resource-group>
AZURE_FOUNDRY_PROJECT_NAME=<your-foundry-project-name>
AZURE_LOCATION=eastus2
```

Important:

```text
.env
*.env
.env.*
```

These files should be ignored by Git. Do not commit secrets.

Use `.env.example` for placeholder configuration only.

---

## Running the Notebook

Open the notebook in VS Code:

```text
langchain_financial_pdf_to_foundry_hosted_agent.ipynb
```

Run the notebook cells in order.

The notebook covers:

1. Loading environment variables
2. Loading the sample financial PDF
3. Splitting the PDF into chunks
4. Creating embeddings
5. Building a vector store
6. Creating LangChain tools
7. Building the agent
8. Asking portfolio analysis questions
9. Preparing hosted deployment artifacts
10. Adding observability queries

Example questions:

```text
What is the current portfolio allocation?
```

```text
Which holdings create the highest concentration risk?
```

```text
Analyze this portfolio for a moderately aggressive investor.
```

```text
What changes would improve diversification?
```

```text
Summarize the portfolio risk in executive language.
```

---

## Sample Portfolio PDF

The included PDF is synthetic and used only for demo purposes.

```text
sample_financial_portfolio_statement.pdf
```

It contains example holdings, allocation data, and portfolio information that the agent can use for retrieval and analysis.

---

## Example Output

A typical response from the agent may include:

```text
The portfolio is moderately growth-oriented, with meaningful exposure to large-cap technology and equity funds. The main risk is concentration in a small number of high-beta holdings. Diversification can be improved by adding more fixed income, international exposure, and defensive sectors.
```

The agent can provide:

- Asset allocation summary
- Sector exposure
- Concentration risk
- Risk profile
- Suggested rebalancing actions
- Executive summary for advisors or portfolio reviewers

---

## Foundry Hosted Agent Deployment

This demo is designed to align with the Foundry Hosted Agent pattern where custom agent logic can be packaged and deployed as a hosted runtime.

High-level deployment flow:

```text
Local LangChain Agent
    |
    v
Containerized App
    |
    v
Azure Container Registry
    |
    v
Microsoft Foundry Hosted Agent
    |
    v
Monitor with Foundry traces / Azure Monitor
```

Typical commands:

```bash
az login
az account set --subscription "<your-subscription-id>"
```

Build and push image:

```bash
docker build -t financial-portfolio-agent:v1 .
```

Tag image:

```bash
docker tag financial-portfolio-agent:v1 <your-acr-name>.azurecr.io/financial-portfolio-agent:v1
```

Push image:

```bash
az acr login --name <your-acr-name>
docker push <your-acr-name>.azurecr.io/financial-portfolio-agent:v1
```

Then register/deploy the hosted agent using the Foundry hosted agent deployment flow supported in your environment.

> Note: Foundry Hosted Agent capabilities are evolving. Confirm the latest hosted-agent deployment commands and SDK version for your Foundry project before production deployment.

---

## Observability

The notebook includes a section for observability using Foundry traces and Azure Monitor / Application Insights.

Recommended observability dimensions:

- User question
- Agent response
- Tool calls
- Retrieval latency
- Retrieved document chunks
- Model latency
- Token usage
- Errors and exceptions
- Portfolio analysis tool execution
- End-to-end request duration

Example telemetry fields:

```text
session_id
user_query
agent_response
tool_name
tool_latency_ms
model_deployment
prompt_tokens
completion_tokens
total_tokens
trace_id
```

Example KQL query pattern:

```kusto
traces
| where timestamp > ago(24h)
| where message contains "financial-portfolio-langchain-agent"
| order by timestamp desc
```

---

## Security Notes

Do not commit secrets.

The following files should be ignored:

```text
.env
*.env
.env.*
```

Use this command to verify `.env` is ignored:

```bash
git check-ignore -v .env
```

On Windows PowerShell:

```powershell
git check-ignore -v .env
```

Before pushing to GitHub, always run:

```bash
git status
```

Confirm `.env` is not staged.

---

## GitHub Push from VS Code Windows Terminal

From PowerShell:

```powershell
cd hosted_agent_demo

git init
git branch -M main

git add .
git status

git commit -m "Add hosted LangChain financial agent demo"

git remote add origin https://github.com/jaysen-msft/hosted-agent-demo.git
git push -u origin main
```

If the remote already exists:

```powershell
git remote set-url origin https://github.com/jaysen-msft/hosted-agent-demo.git
git push -u origin main
```

---

## Troubleshooting

### `.env` was accidentally staged

Run:

```bash
git rm --cached .env
```

Then confirm:

```bash
git status
git check-ignore -v .env
```

---

### Azure OpenAI authentication fails

Check:

- Endpoint is correct
- API key is valid
- Deployment name is correct
- API version is supported
- Model deployment exists in the target resource

---

### PDF loading fails

Check:

- PDF path is correct
- File exists locally
- `pypdf` is installed

```bash
pip install pypdf
```

---

### FAISS install fails

Try:

```bash
pip install faiss-cpu
```

If issues continue on Windows, use Python 3.10 or 3.11 and recreate the virtual environment.

---

### Foundry hosted deployment fails

Check:

- You are logged into Azure CLI
- Correct subscription is selected
- Container image was pushed successfully
- Foundry project endpoint is correct
- Managed identity or service principal has required permissions
- Hosted Agent capability is enabled in your Foundry environment

---

## Disclaimer

This repository is for educational and demo purposes only.

The portfolio data is synthetic. The agent output should not be considered financial, investment, tax, or legal advice. Always consult a qualified financial advisor before making investment decisions.

---

## Author

Jayanta Sen

Demo focus:

- Microsoft Foundry
- Azure OpenAI
- LangChain / LangGraph
- Hosted Agents
- Agentic AI patterns
- RAG over financial documents
- Observability for enterprise AI agents
