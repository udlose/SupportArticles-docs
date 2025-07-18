---
title: Azure Cosmos DB Data Explorer fails to connect
description: Troubleshoot scenarios when the Azure Cosmos DB Data Explorer can't connect to an account and perform specific data plane or control plane operations.
author: seesharprun
ms.author: sidandrews
ms.reviewer: ouryba, v-sidong
ms.date: 06/11/2025
ms.custom: sap:Account Administration
---

# Azure Cosmos DB Data Explorer fails to connect

Occasionally, the Azure Cosmos DB Data Explorer can't connect to your account or perform operations against resources or items. This article reviews potential causes and solutions to this issue.

> [!IMPORTANT]
> The Azure Cosmos DB Data Explorer isn't available for the API for PostgreSQL or the API for vCore.

## Prerequisites

An existing Azure Cosmos DB for NoSQL, MongoDB RU, Apache Cassandra, Apache Gremlin, or Table account

## Symptoms

You're unable to connect to the Data Explorer even after enabling the **Allow access from Azure Portal** option.

## Cause

Even after you configure the correct role-based access control and portal permissions, there are extra network access requirements that must be configured based on your selected API.

- Some services, such as the API for NoSQL, API for Apache Gremlin, and API for Table, use a client-side JavaScript SDK to perform operations and require one set of solutions.

- Other services, such as the API for MongoDB RU and API for Apache Cassandra, use protocol-specific middleware and require an alternative solution.

## Solution for the API for NoSQL, Apache Gremlin, or Table

Database, container, graph, and table (control plane) operations are executed via calls to the Azure Resource Manager control plane using the Azure Cosmos DB resource provider. Your network configuration doesn't affect these operations.

Item (data plane) operations are executed using the JavaScript SDK within the context of your browser. The device you're currently using must have direct network access to the account.

- For accounts configured with **public access (all networks)**, data plane operations shouldn't have any network-related connectivity issues.

- For accounts configured with **public access (selected networks)**, you must create a firewall rule to allow access to the account from your current device. The **Networking** feature in the service's page in the Azure portal has an **Add my current IP (...)** hyperlink that can automatically add your IP address.

- If the account doesn't have a firewall rule, the Data Explorer throws an error that can be observed in the notifications for Data Explorer. This error message contains text similar to the following examples:

    | API | Error |
    | --- | --- |
    | **NoSQL** | `Request originated from IP XXX.XXX.XXX.XXX through public internet. This is blocked by your Cosmos DB account firewall settings.` |
    | **Apache Gremlin** | `Failure in submitting query: g.V(): Request originated from IP XXX.XXX.XXX.XXX through public internet. This is blocked by your Cosmos DB account firewall settings.` |
    | **Table** | `Error while refreshing databases: Request originated from IP XXX.XXX.XXX.XXX through public internet. This is blocked by your Cosmos DB account firewall settings.` |

- For accounts configured with **public access disabled**, your device must be connected to a virtual network with connectivity to the account for data plane operations to function.

## Solution for the API for MongoDB RU or Apache Cassandra

Review the control plane and data plane solutions, respectively.

Database, keyspace, and collection (control plane) operations are executed via calls to the Azure Resource Manager control plane using the Azure Cosmos DB resource provider. Your network configuration doesn't affect these operations.

Item (data plane) operations are executed using a proxy service in the API's middleware. This service facilitates the use of the service-native (MongoDB, Cassandra) protocol in item operations and queries. The proxy service requires direct network access to the account.

- For accounts configured with **public access (all networks)**, data plane operations shouldn't have any network-related connectivity issues.

- For accounts configured with **public access (selected networks)**, you must select **Allow Access from Azure Portal** in the **Networking** feature of the service's page in the Azure portal. This option adds a range of IP addresses that include the middleware service. You *don't* have to add the IP address of your current device.

- If the account doesn't have a firewall rule, the Data Explorer throws an error that can be observed in the browser developer tools. This error message contains a generic `Error querying documents` or `Failed to establish connection with cassandra node` message generated by the middleware service. The expanded error message is included in the Compute gateway logs, similar to the following example:

    ```output
    Request originated from IP XXX.XXX.XXX.XXX through public internet. This is blocked by your Cosmos DB account firewall settings. 
    ````
