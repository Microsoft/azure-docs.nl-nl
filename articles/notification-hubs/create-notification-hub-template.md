---
title: Een Azure notification hub maken met behulp van een Azure Resource Manager-sjabloon
description: Leer een Azure notification hub maken met behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon).
services: notification-hubs
author: sethmanheim
ms.author: sethm
ms.reviewer: thsomasu
ms.date: 08/04/2020
ms.lastreviewed: 05/15/2020
ms.topic: quickstart
ms.service: notification-hubs
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: a328d6b8942c3209e13dc91a2fb892e98e3016f6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533479"
---
# <a name="quickstart-create-a-notification-hub-using-an-arm-template"></a>Quickstart: een notification hub maken met behulp van een ARM-sjabloon

Azure Notification Hubs biedt een eenvoudig te gebruiken en uitgeschaalde push-engine waarmee u vanuit elke back-end (cloud of on-premises) meldingen kunt verzenden naar ieder platform (iOS, Android, Windows, Kindle, enzovoort). Zie voor meer informatie over de service [Wat is Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

In deze quickstart wordt gebruikgemaakt van een Azure Resource Manager-sjabloon (ARM-sjabloon) om een Azure Notification Hubs-naamruimte en een notification hub met de naam **MyHub** binnen die naamruimte te maken.

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-notification-hub%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-notification-hub/).

:::code language="json" source="~/quickstart-templates/101-notification-hub/azuredeploy.json":::

* [Microsoft.NotificationHubs/namespaces](/azure/templates/microsoft.notificationhubs/namespaces)
* [Microsoft.NotificationHubs/namespaces/notificationHubs](/azure/templates/microsoft.notificationhubs/namespaces/notificationhubs)

## <a name="deploy-the-template"></a>De sjabloon implementeren

Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen. De sjabloon gebruikt de naam van een Notification Hubs-naamruimte als parameter. De sjabloon maakt vervolgens een naamruimte met die naam en een notification hub met de naam **MyHub** binnen die naamruimte.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-notification-hub%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

U kunt de Azure-portal gebruiken om de geïmplementeerde resources te controleren, of Azure CLI of een Azure PowerShell-script gebruiken om de geïmplementeerde Notification Hubs-naamruimte en -hub weer te geven:

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzNotificationHub -Namespace "nhtestns123" -ResourceGroup "ContosoNotificationsGroup"
Get-AzNotificationHubsNamespace -Namespace "nhtestns123"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az notification-hub show --resource-group ContosoNotificationsGroup --namespace-name nhtestns123 --name MyHub
az notification-hub namespace show --resource-group ContosoNotificationsGroup --name nhtestns123
```

---
The output looks similar to: null
':::image type="content" source="media/create-notification-hub-template/verify-deploy.png" alt-text="Verify deployment"::': null
---

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resourcegroep niet meer nodig hebt, verwijdert u deze. Hierdoor worden ook de resources in de resourcegroep verwijderd.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Volgende stappen

Zie voor een stapsgewijze zelfstudie die u door het proces van het maken van een sjabloon leidt:

> [!div class="nextstepaction"]
> [Zelfstudie: Uw eerste ARM-sjabloon maken en implementeren](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
