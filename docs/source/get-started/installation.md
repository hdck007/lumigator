# Installation

You can deploy Lumigator either locally or into a distributed environment using Kubernetes
{{ '[HELM Charts](https://github.com/mozilla-ai/lumigator/blob/{}/infra/helm/lumigator/README.md)'.format(commit_id) }}.
In this guide, we'll show you how to get started with a local deployment.

## Prerequisites

Before you start, make sure you have the following:

- A working installation of [Docker](https://docs.docker.com/engine/install/)
    - On Mac, Docker Desktop >= 4.37, and `docker-compose` >= 2.31.
    - On Linux, please also complete the [post-installation steps](https://docs.docker.com/engine/install/linux-postinstall/).
- The [`uv`](https://github.com/astral-sh/uv?tab=readme-ov-file#installation) Python package manager installed.
- The system Python; no version manager, such as `uv`, should be active.

## Local Deployment

You can run and develop Lumigator locally using `docker-compose.` This creates multiple container
services networked together to make up all the components of the Lumigator application:

- `minio`: Local storage for datasets that mimics S3-API compatible functionality.
- `backend`: Lumigator’s FastAPI REST API.
- `ray`: A Ray cluster for submitting several types of jobs.
- `mlflow`: Used to track experiments and metrics.
- `frontend`: Lumigator's Web UI

```{note}
Lumigator requires an SQL database to hold metadata for datasets and jobs. The local deployment
uses SQLite for this purpose.
```

```{note}
The Ray container will use the shared host folder indicated by environment variable `${HF_HOME}`
(with default value `${HOME}/.cache/huggingface/`) to store artifacts downloaded from HuggingFace.
Make sure this directory exists and has read and write permissions for all users before starting
Lumigator.
```

```{note}
If you want to evaluate against LLM APIs like OpenAI and Mistral, you need to set the appropriate
environment variables: `OPENAI_API_KEY` or `MISTRAL_API_KEY` or `DEEPSEEK_API_KEY`. Refer to the
[troubleshooting section](../get-started/troubleshooting.md) for more details.
```

Despite the fact this is a local setup, it lends itself to more distributed scenarios. For instance,
one could provide different `AWS_*` environment variables to the backend container to connect to any
provider's S3-compatible service, instead of minio. Similarly, one could provide a different
`RAY_HEAD_NODE_HOST` to move compute to a remote ray cluster, and so on. See {{ '[here](https://github.com/mozilla-ai/lumigator/blob/{}/docker-compose.yaml)'.format(commit_id) }} to see where the `RAY_HEAD_NODE_HOST` is defined

To deploy Lumigator locally:

1. Clone the Lumigator repository:

    ```console
    user@host:~$ git clone git@github.com:mozilla-ai/lumigator.git
    ```

1. Change to the Lumigator directory:

    ```console
    user@host:~$ cd lumigator
    ```

1. Run the `start-lumigator` make target:

    ```console
    user@host:~/lumigator$ make start-lumigator
    ```

## Verify

To verify that Lumigator is running, open a browser and navigate to `http://localhost:8000`. You
should receive the following JSON response:

```json
{"Hello": "Lumigator!🐊"}
```

```{note}
If you need to change the port that the Lumigator service listens on, you can do it in the
{{ '[`docker-compose.yaml`](https://github.com/mozilla-ai/lumigator/blob/{}/docker-compose.yaml)'.format(commit_id) }}
file.
```

## Next Steps

Now that you have Lumigator running locally, you can start using it to evaluate your language
models. Next, we'll show you how to interact with the Lumigator API to create a new evaluation
job.
