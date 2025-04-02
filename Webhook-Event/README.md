# Ocp-Tekton-pipeline Webhook Integration

This repository contains the configuration files for integrating Tekton pipelines with GitHub webhooks on OpenShift. The setup automates the triggering of Tekton pipelines upon GitHub push events using Tekton Triggers.

## Table of Contents

- [Overview](#overview)
- [Files](#files)
- [Prerequisites](#prerequisites)
- [Setup](#setup)
- [Webhook Workflow](#webhook-workflow)
- [License](#license)

## Overview

This repository enables the automation of Tekton pipelines using GitHub webhooks. When a push event occurs in the GitHub repository, the configured Tekton pipeline is triggered to execute tasks such as building and deploying the application.

## Files

- `BindTrigger.yaml`: Defines the `TriggerBinding` to extract parameters from the GitHub webhook payload.
- `TriggerTemplate.yaml`: Defines the `TriggerTemplate` to create a `PipelineRun` based on the webhook event.
- `eventListiner.yaml`: Configures the `EventListener` to listen for GitHub webhook events and trigger the pipeline.
- `secret.yaml`: Stores the GitHub webhook secret token securely.
- `rbac-Sa.yaml`: Configures the Role and RoleBinding for Tekton Triggers.
- `rbac-Cluster-rb.yaml`: Configures the ClusterRole and ClusterRoleBinding for Tekton Triggers.
- `Subscrition.yaml`: (Optional) Configuration for installing the OpenShift Pipelines Operator.

## Prerequisites

Before setting up the webhook integration, ensure the following:

1. You have access to an OpenShift cluster.
2. The `oc` CLI is installed and configured to interact with your OpenShift cluster.
3. The OpenShift Pipelines Operator is installed.
4. A GitHub repository with admin access to configure webhooks.

## Setup

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
    oc apply -f rbac-Sa.yaml
    oc apply -f rbac-Cluster-rb.yaml
    ```

4. **Create the GitHub webhook secret**:

    ```bash
    oc apply -f secret.yaml
    ```

5. **Apply the Tekton Trigger configurations**:

    ```bash
    oc apply -f BindTrigger.yaml
    oc apply -f TriggerTemplate.yaml
    oc apply -f eventListiner.yaml
    ```

6. **Expose the EventListener**:

    Expose the EventListener as a service to allow GitHub to send webhook events:

    ```bash
    oc expose service el-github-listener -n ocp-pipelines
    ```

    Retrieve the route URL:

    ```bash
    oc get route el-github-listener -n ocp-pipelines -o jsonpath='{.spec.host}'
    ```

    Use this URL to configure the GitHub webhook.

7. **Configure the GitHub Webhook**:

    - Go to your GitHub repository settings.
    - Navigate to **Webhooks** > **Add webhook**.
    - Set the **Payload URL** to the EventListener route URL.
    - Set the **Content type** to `application/json`.
    - Add the secret token from [secret.yaml](http://_vscodecontentref_/1).
    - Select the **Just the push event** option.

## Webhook Workflow

1. A push event occurs in the GitHub repository.
2. The GitHub webhook sends the event payload to the EventListener.
3. The EventListener processes the payload using the `TriggerBinding` and `TriggerTemplate`.
4. A `PipelineRun` is created to execute the Tekton pipeline.
5. The pipeline performs tasks such as cloning the repository, building the application, and deploying it.

## License

This project is licensed under the MIT License. See the LICENSE file for more details.