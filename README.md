# Ocp-Tekton-pipeline: Pipeline and Webhook Integration

This repository contains the configuration files for setting up Tekton pipelines and integrating them with GitHub webhooks on OpenShift. The objective is to automate the process of building, deploying, and triggering pipelines upon GitHub push events using Tekton Triggers.

## Table of Contents

- [Overview](#overview)
- [Files](#files)
- [Prerequisites](#prerequisites)
- [Setup](#setup)
  - [Pipeline Setup](#pipeline-setup)
  - [Webhook Integration Setup](#webhook-integration-setup)
- [Workflow](#workflow)
- [License](#license)

## Overview

This repository enables the automation of application deployment using Tekton pipelines and GitHub webhooks. It includes:

1. **Pipeline Setup**: Configures Tekton pipelines to build and deploy the `activities` application.
2. **Webhook Integration**: Automates pipeline execution upon GitHub push events using Tekton Triggers.

## Files

### Pipeline Configuration

- `namespace.yaml`: Creates the `ocp-pipelines` namespace and configures RBAC for privileged SCC.
- `sa.yaml`: Configures the `tekton-pipeline-sa` service account and its RoleBinding.
- `rbac.yaml`: Defines additional RBAC permissions for the pipeline.
- `pvc.yaml`: Creates a PersistentVolumeClaim for shared workspace storage.
- `pipelineTemplate.yaml`: Defines the Tekton pipeline for building and pushing the application image.

### Webhook Integration

- `BindTrigger.yaml`: Defines the `TriggerBinding` to extract parameters from the GitHub webhook payload.
- `TriggerTemplate.yaml`: Defines the `TriggerTemplate` to create a `PipelineRun` based on the webhook event.
- `eventListiner.yaml`: Configures the `EventListener` to listen for GitHub webhook events and trigger the pipeline.
- `secret.yaml`: Stores the GitHub webhook secret token securely.
- `rbac-Sa.yaml`: Configures the Role and RoleBinding for Tekton Triggers.
- `rbac-Cluster-rb.yaml`: Configures the ClusterRole and ClusterRoleBinding for Tekton Triggers.

## Prerequisites

Before setting up the pipeline and webhook integration, ensure the following:

1. You have access to an OpenShift cluster.
2. The `oc` CLI is installed and configured to interact with your OpenShift cluster.
3. The OpenShift Pipelines Operator is installed.
4. A GitHub repository with admin access to configure webhooks.

## Setup

### Pipeline Setup

1. **Clone the repository**:

    ```bash
    git clone https://github.com/your-username/Ocp-Tekton-pipeline.git
    cd Ocp-Tekton-pipeline
    ```

2. **Create the `ocp-pipelines` namespace**:

    ```bash
    oc apply -f namespace.yaml
    ```

3. **Create the ServiceAccount and RBAC configurations**:

    ```bash
    oc apply -f sa.yaml
    oc apply -f rbac.yaml
    ```

4. **Create the PersistentVolumeClaim**:

    ```bash
    oc apply -f pvc.yaml
    ```

5. **Apply the Tekton pipeline**:

    ```bash
    oc apply -f pipelineTemplate.yaml
    ```

### Webhook Integration Setup

1. **Create the GitHub webhook secret**:

    ```bash
    oc apply -f secret.yaml
    ```

2. **Apply the Tekton Trigger configurations**:

    ```bash
    oc apply -f BindTrigger.yaml
    oc apply -f TriggerTemplate.yaml
    oc apply -f eventListiner.yaml
    ```

3. **Expose the EventListener**:

    Expose the EventListener as a service to allow GitHub to send webhook events:

    ```bash
    oc expose service el-github-listener -n ocp-pipelines
    ```

    Retrieve the route URL:

    ```bash
    oc get route el-github-listener -n ocp-pipelines -o jsonpath='{.spec.host}'
    ```

    Use this URL to configure the GitHub webhook.

4. **Configure the GitHub Webhook**:

    - Go to your GitHub repository settings.
    - Navigate to **Webhooks** > **Add webhook**.
    - Set the **Payload URL** to the EventListener route URL.
    - Set the **Content type** to `application/json`.
    - Add the secret token from `secret.yaml`.
    - Select the **Just the push event** option.

## Workflow

### Pipeline Workflow

1. The Tekton pipeline is triggered manually or via a webhook.
2. The pipeline performs tasks such as:
   - Cloning the source code from the GitHub repository.
   - Building the container image.
   - Pushing the image to a container registry.
   - Deploying the application to the OpenShift cluster.

### Webhook Workflow

1. A push event occurs in the GitHub repository.
2. The GitHub webhook sends the event payload to the EventListener.
3. The EventListener processes the payload using the `TriggerBinding` and `TriggerTemplate`.
4. A `PipelineRun` is created to execute the Tekton pipeline.
5. The pipeline performs tasks such as cloning the repository, building the application, and deploying it.

## License

This project is licensed under the MIT License. See the LICENSE file for more details.