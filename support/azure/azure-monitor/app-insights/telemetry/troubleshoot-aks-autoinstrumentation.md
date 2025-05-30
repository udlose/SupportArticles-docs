---
title: Troubleshoot AKS Autoinstrumentation in Application Insights
description: Helps you troubleshoot Azure Kubernetes Service autoinstrumentation problems in Application Insights.
ms.service: azure-monitor
ms.reviewer: aaronmax, v-weizhu
ms.custom: sap:Application Insights
ms.date: 05/23/2025
---
# Troubleshoot Azure Kubernetes Service autoinstrumentation in Application Insights

This article provides a step-by-step guide to troubleshoot an issue where no telemetry data appears in a workspace-based Application Insights resource.

## Prerequisites

Ensure that the Kubernetes command-line tool (`kubectl`) is installed and configured.

## Troubleshooting steps

1. Confirm the pod is running.

2. Verify the deployment is instrumented.

3. Check for the `monitor.azure.com/instrumentation` annotation on the deployment and its latest replica set.

    The annotation should be present with proper JSON in the following pattern:

    `{"crName": "crName1","crResourceVersion": "20177993","platforms":["Java"]}`

4. If the annotation is present, the deployment is instrumented, and you should proceed to the next step. If the annotation isn't present, the deployment isn't instrumented. In this case, restart the deployment by following these steps:

    1. [Prepare your cluster](/azure/azure-monitor/app/kubernetes-codeless#prepare-a-cluster).
    2. Confirm the following things:
        - The *Instrumentation* custom resource is in the correct namespace as the deployment.
        - The *Instrumentation* custom resource contains the correct connection string and instrumentation platform.
    3. [Restart the deployment](/azure/azure-monitor/app/kubernetes-codeless#restart-deployment).

5. Check for networking errors in the SDK log located in the pod's log volume: **/var/log/applicationinsights**.

    For example, the following errors indicate a connectivity issue:

    - > Ingestion endpoint could not be reached.
    - > Error: getaddrinfo ENOTFOUND eastus2-3.in.applicationinsights.azure.com
    - > getaddrinfo ENOTFOUND eastus2-3.in.applicationinsights.azure.com

    If this type of error exists, sign in to the container and test connectivity to the endpoint:

    ```console
    kubectl exec -ti <pod-name> -- /bin/bash
    ```

    If a connection can't be established, troubleshoot the network connectivity issue, such as firewall or name resolution issues.

[!INCLUDE [Azure Help Support](../../../../includes/azure-help-support.md)]
