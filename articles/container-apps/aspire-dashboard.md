---
title: Read real time app data with .NET Aspire Dashboard in Azure Container Apps
description: Use real time log data with .NET Aspire Dashboard in Azure Container Apps.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.custom: devx-track-azurecli, devx-track-dotnet
ms.topic: how-to
ms.date: 05/09/2024
ms.author: cshoe
zone_pivot_groups: azure-azd-cli-portal
---

# Read real time app data with .NET Aspire Dashboard in Azure Container Apps (preview)

The [.NET Aspire Dashboard](/dotnet/aspire/fundamentals/dashboard/overview) provides information on how your app is running both on the environment and individual app level, which can help you detect anomalies in real time and debug errors. The dashboard shows data for all Container Apps that is part of your project, regardless of language or runtime.

The following image is a screenshot of a trace visualization generated by the .NET Aspire Dashboard.

:::image type="content" source="media/aspire-dashboard/aspire-dashboard-trace.png" alt-text="Screenshot of a .NET Aspire Dashboard trace window.":::

## Enable the dashboard

::: zone pivot="portal"

> [!TIP]
> While you have the portal as an option, the recommended approach to enabling your dashboard is with [azd](aspire-dashboard.md?pivots=azd). If you run into an problems using the portal, [create an issue in the Container Apps GitHub repo](https://github.com/microsoft/azure-container-apps/issues).

You can enable the .NET Aspire Dashboard on any existing container app using the following steps.

1. Go to the Azure portal.

1. Open the *Overview* window of your container app.

1. Find the *.NET Aspire Dashboard* label, and select the **enable** link.

    This action opens the .NET Aspire Dashboard settings window.

1. Next to the *.NET Aspire Dashboard* label, select the **Enabled** checkbox.

    Now the .NET Aspire Dashboard URL is displayed to you.

1. Select the URL to your dashboard.

::: zone-end

::: zone pivot="azurecli"

You can enable the .NET Aspire Dashboard on any existing container app environment by using the following commands. 

```azurecli
az containerapp env dotnet-component create \
  --environment <ENVIRONMENT_NAME> \
  --name <CONTAINER_APP_NAME> \
  --resource-group <RESOURCE_GROUP_NAME>
```

The `create` command returns the dashboard URL that you can open in a browser.

::: zone-end

::: zone pivot="azd"

You can enable the .NET Aspire Dashboard on any existing container app using the following steps.

```azurecli
dotnet new aspire-starter
azd init --location westus2
azd config set alpha.aspire.dashboard on
azd up
```

The `up` command returns the dashboard URL that you can open in a browser.

::: zone-end

## Troubleshooting

Refer to the following items if you have issues enabling your dashboard:

- The portal can take up to two minutes for the dashboard to activate. If you try to go to the dashboard before it's ready, the server returns a `404` or `421` error.

- If you encounter a `421` "Misdirected Request" error, close the browser window, wait a few minutes, and try again.

- You might receive an authentication error when accessing the dashboard that reads, "Could not authenticate user with requested resource."

    To solve this problem, make sure you grant the *Microsoft.App/managedEnvironments/write*, *Contributor*, or *Owner* roles on your Container Apps environment.

## Related content

> [!div class="nextstepaction"]
[.NET Aspire dashboard overview](/dotnet/aspire/fundamentals/dashboard/overview)
