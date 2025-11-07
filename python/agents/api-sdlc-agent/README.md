# API Governance Agent with Apigee API hub

## Overview

This sample agent acts as a virtual assistant for API creation and governance. The agent helps developers create new APIs, and track them as they progress through as set of pre-defined lifecycle stages and approval steps, before they go live.

The agent is able to submit new API proposals, and look up information about existing APIs. It can also help the developer design new specs. The agent uses [Apigee API hub](https://cloud.google.com/apigee/docs/apihub/what-is-api-hub) to store and retrieve information about APIs. The `Lifecycle` [attribute](https://docs.cloud.google.com/apigee/docs/apihub/manage-attributes#supported-system-attributes) in the hub is used by the agent to determine which actions the user can perform, based on the current stage of a given API.

As the lifecycle stage of an API progresses, the API hub fires a series of [events](https://cloud.google.com/apigee/docs/apihub/quickstart-create-event-triggers). These events can be used to automatically execute a variety of processes, such as notifying reviewers, or triggering CI/CD pipelines to [generate](https://apigee.github.io/apigee-go-gen/) and [deploy](https://github.com/apigee/apigeecli/blob/main/docs/apigeecli_apis_deploy.md) API gateway config, or executing [automated tests](https://github.com/apickli/apickli).

This agent demonstrates how an API platform team can easily create a sophisticated, flexible, and extensible API governance process powered by AI.

## Agent Details

| Attribute | Detail |
|---|---|
|   Interaction Type |   Conversational |
|   Complexity |   Easy |
|   Agent Type |   Multi Agent |
|   Components |   Tools, McpToolset |

## API hub Tool

This agent uses a tool that allows it to access information in the API hub. The tool allows the agent to create new [API](https://docs.cloud.google.com/apigee/docs/apihub/apis-intro) and [Version](https://docs.cloud.google.com/apigee/docs/apihub/versions-intro) resources in the hub's catalog, and also upload generated [Specs](https://docs.cloud.google.com/apigee/docs/apihub/specs-intro). The agent is designed to access the API hub via [MCP](https://modelcontextprotocol.io/).

> The implementation of the MCP tool itself is not included in this repo.

## Setup and Installation

### Prerequisites

- Python 3.12+
-   Poetry for dependency management and packaging
    -   See the official
        [Poetry website](https://python-poetry.org/docs/) for more information. To install Poetry run:
    ```bash
    pip install poetry
    ```
- Google Cloud Project with the following roles assigned
  - Apigee Organization Admin
  - Secret Manager Admin
  - Storage Admin
  - Service Usage Consumer
  - Logs Viewer

Once you have created your project, [install the Google Cloud SDK](https://cloud.google.com/sdk/docs/install). Then run the following command to authenticate:
```bash
gcloud auth login
```
You also need to enable certain APIs. Run the following command to enable:
```bash
gcloud services enable aiplatform.googleapis.com
```

The sample assumes there is a project with Apigee API hub [provisioned](https://cloud.google.com/apigee/docs/apihub/provision).

## Agent Setup

1.  Clone this repository.

2.  Change to the `api-sdlc-agent` directory.

3.  Install the dependencies:

    **Note for Linux users:** If you get an error related to `keyring` during the installation, you can disable it by running the following command:
    ```bash
    poetry config keyring.enabled false
    ```
    This is a one-time setup.

    ```bash
    poetry install
    ```

4.  Configure settings:

    - Set the following environment variables. You can set them in your `.env` file (modify and rename `.env.example` file to `.env`) or set them directly in your shell. For example:

    ```bash
    export GOOGLE_GENAI_USE_VERTEXAI=1
    export GOOGLE_CLOUD_PROJECT=my-project
    export GOOGLE_CLOUD_LOCATION=my-region
    export GOOGLE_CLOUD_STORAGE_BUCKET=my-storage-bucket  # Only required for deployment on Agent Engine
    ```

## Running the Agent Locally

You can run the agent locally using the `adk` command in your terminal:

1.  To run the agent from the CLI:

    ```bash
    adk run api_sdlc_agent
    ```

2.  To run the agent from the ADK web UI:

    ```bash
    adk web
    ```
    Then select the `api-sdlc-agent` from the dropdown.

## Deploying the Agent Remotely

### To Agent Engine

The agent can also be deployed to [Vertex AI Agent Engine](https://cloud.google.com/vertex-ai/generative-ai/docs/agent-engine/overview) using the following
commands:

```bash
poetry install --with deployment
python3 deployment/deploy.py
```

When the deployment finishes, it will output the resource ID of the remote agent deployment, for example:
```
Created remote agent: projects/<PROJECT_NUMBER>/locations/<PROJECT_LOCATION>/reasoningEngines/<AGENT_ENGINE_ID>
```

For more information on deploying to Agent Engine, see [here](https://google.github.io/adk-docs/deploy/agent-engine/#install-vertex-ai-sdk).

The deployment script adds the `AGENT_ENGINE_ID` to your `.env` file. To test the remote agent, simply run:
```bash
python3 deployment/test_deployment.py
```

You may then interact with the deployed agent from the shell. You can type `quit` at any point to exit.