# Siam Piwat POC: SQL Data Agent

This project is a Proof of Concept (POC) demonstrating a **Data Analyst Assistant** powered by the [Google Agent Development Kit (ADK)](https://pypi.org/project/google-adk/) and the **MCP Toolbox**.

The agent is designed to translate natural language into SQL queries, execute them securely against a PostgreSQL database, and return data insights. It focuses on the `chat_to_data_poc` schema and strictly enforces a "read-only" policy to prevent unauthorized database modifications.

## Features

- **Natural Language to SQL**: Converts natural language requests into complex SQL queries.
- **MCP Toolbox Integration**: Connects dynamically to database tools provided by an MCP (Model Context Protocol) Toolbox.
- **Secure Data Access**: Strictly limits query execution to `SELECT` operations within the `chat_to_data_poc` schema.
- **Automated Data Analysis**: Analyzes query results and returns summarized insights and trends.
- **Gemini Powered**: Built on top of the `gemini-3-flash-preview` model via Google ADK.

## Database Schema Overview

The agent is context-aware of the following core tables:

| Table Name | Description | Key Attributes |
| :--- | :--- | :--- |
| **campaign** | Marketing Master Data: Tracks promotional activities. | `campaign_id`, `campaign_type`, `customer_segment` |
| **football_shop_visitor** | Store Footfall: Daily visitor counts and behavior. | `date`, `tenant_id`, `visitor_count`, `dwell_time_avg` |
| **sales_daily_gp_sales** | Contract Sales: Daily gross/net sales at the contract level. | `date`, `contract_id`, `gross_sales`, `revenue` |
| **spending** | Transaction Details: Customer purchase records and loyalty activity. | `txn_id`, `uid`, `amount`, `payment_method`, `campaign_id` |
| **tenant_contract** | Leasing Details: Rental agreements, areas, and rates. | `contract_id`, `tenant_id`, `rent_rate`, `area_sqm` |
| **tenant_master** | Tenant Master Data: Registry of brands/stores and locations. | `tenant_id`, `tenant_name`, `category`, `status` |

## Prerequisites

- **Python**: Version `3.13` or higher.
- **[uv](https://docs.astral.sh/uv/)**: Recommended Python package manager for fast and reproducible installs.
- **MCP Toolbox**: An active instance of the MCP Toolbox running locally or remotely.

## Getting Started

### 1. Clone the repository
Navigate to your project directory if you haven't already.

### 2. Configure Environment Variables
Inside the `sql_data_agent` folder, you will find a `.env` file (or you can create one if not present from an `.env.example`).
Make sure your `.env` looks similar to this:

```env
GOOGLE_CLOUD_PROJECT="your-google-cloud-project-id"
GOOGLE_CLOUD_LOCATION="global"
GOOGLE_GENAI_USE_VERTEXAI=TRUE
MCP_TOOLBOX_URL=<MCP_TOOLBOX_URL>
GOOGLE_CLOUD_AGENT_ENGINE_ENABLE_TELEMETRY=true
OTEL_INSTRUMENTATION_GENAI_CAPTURE_MESSAGE_CONTENT=true
```
*Note: Adjust `MCP_TOOLBOX_URL` to point to your running instance of the MCP Toolbox.*

### 3. Install Dependencies
You can install the dependencies using `uv` from the root directory:

```bash
uv sync
```

### 4. Running the Agent
Currently, the agent logic is encapsulated in `sql_data_agent/agent.py`. 

```bash
uv run adk web
```

## Architecture Summary

1. **Tool Discovery**: Upon start, the agent queries the `MCP_TOOLBOX_URL` for `cloud_sql_postgres_database_tools` and dynamically loads approved capabilities.
2. **Execution Protocol**: Before querying, it formulates the query, explains the logic, runs the `SELECT` with a default `LIMIT 100`, and returns a markdown-formatted analysis.
3. **Google ADK Core**: The orchestration and reasoning are handled by `Agent` from `google.adk.agents`.
