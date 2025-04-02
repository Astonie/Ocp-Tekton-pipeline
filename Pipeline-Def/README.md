# Ocp-Tekton-pipeline

This repository contains the OpenShift Tekton pipeline configuration files for building and deploying the `activities` application. The pipeline automates the process of cloning the source code, building the container image, and pushing it to a container registry.

## Table of Contents

- [Overview](#overview)
- [Files](#files)
- [Prerequisites](#prerequisites)
- [Setup](#setup)
- [Pipeline Details](#pipeline-details)
- [License](#license)

## Overview

The `activities` application is built and deployed using Tekton pipelines on OpenShift. This repository includes the following resources:

- **Namespace**: Defines the `ocp-pipelines` namespace and associated RBAC configurations.
- **ServiceAccount**: Configures the `tekton-pipeline-sa` service account with necessary permissions.
- **RBAC**: Role and RoleBinding configurations for Tekton pipelines.
- **PersistentVolumeClaim**: Defines a PVC for shared workspace storage.
- **Pipeline**: Defines the Tekton pipeline for building and pushing the application image.
- **Subscription**: (Optional) Configuration for installing the OpenShift Pipelines Operator.

## Files

- `namespace.yaml`: Creates the `ocp-pipelines` namespace and configures RBAC for privileged SCC.
- `sa.yaml`: Configures the `tekton-pipeline-sa` service account and its RoleBinding.
- `rbac.yaml`: Defines additional RBAC permissions for the pipeline.
- `pvc.yaml`: Creates a PersistentVolumeClaim for shared workspace storage.
- `pipelineTemplate.yaml`: Defines the Tekton pipeline for building and pushing the application image.
- `Subscrition.yaml`: (Optional) Configuration for installing the OpenShift Pipelines Operator.

## Prerequisites

Before deploying the pipeline, ensure the following:

1. You have access to an OpenShift cluster.
2. The `oc` CLI is installed and configured to interact with your OpenShift cluster.
3. The OpenShift Pipelines Operator is installed (optional configuration provided in `Subscrition.yaml`).

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

6. **(Optional) Install the OpenShift Pipelines Operator**:

    ```bash
    oc apply -f Subscrition.yaml
    ```

## Pipeline Details

- **Pipeline Name**: `activities-build-pipeline`
- **Namespace**: `ocp-pipelines`
- **Parameters**:
  - `git-url`: The Git repository URL (default: `https://github.com/florient2016/Activities.git`).
  - `git-revision`: The Git branch or tag to build (default: `latest`).
  - `image-repo`: The container image repository (default: `docker.io/komlan2019/activities`).
- **Tasks**:
  1. **Clone**: Clones the source code from the Git repository.
  2. **Build and Push**: Builds the container image using `buildah` and pushes it to the specified container registry.

## Usage

1. **Start the pipeline**:

    ```bash
    tkn pipeline start activities-build-pipeline \
      -p git-url=https://github.com/florient2016/Activities.git \
      -p git-revision=main \
      -p image-repo=docker.io/komlan2019/activities \
      -w name=shared-workspace,claimName=pipeline-pvc \
      -n ocp-pipelines
    ```

2. **Monitor the pipeline**:

    ```bash
    tkn pipeline logs -f -n ocp-pipelines
    ```

3. **Verify the image**:

    Check the container registry to ensure the image has been pushed successfully.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for more details.