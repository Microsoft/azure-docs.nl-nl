---
title: Azure-activiteitenlogboek verzenden naar Log Analytics-werkruimte met behulp van Azure Resource Manager-sjabloon
description: Gebruik ARM-sjablonen om een Log Analytics-werkruimte te maken en een diagnostische instelling om het activiteitenlogboek naar Azure Monitor-logboeken te verzenden.
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 56810cffcb9665810c452276be34e6924fd992b2
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033262"
---
# <a name="quickstart-send-azure-activity-log-to-log-analytics-workspace-using-an-arm-template"></a>Quickstart: Azure-activiteitenlogboek verzenden naar Log Analytics-werkruimte met behulp van een ARM-sjabloon

Het activiteitenlogboek is een platformlogboek in Azure dat inzicht biedt in gebeurtenissen op abonnementsniveau. Dit geldt ook voor gegevens zoals wanneer een resource wordt gewijzigd of wanneer een virtuele machine wordt gestart. U kunt het activiteitenlogboek weergeven in de Azure-portal, of items ophalen met PowerShell en CLI. Deze Quick Start laat zien hoe u Azure Resource Manager sjablonen (ARM-sjablonen) kunt gebruiken voor het maken van een Log Analytics-werkruimte en een diagnostische instelling om het activiteitenlogboek te verzenden naar Azure Monitor-logboeken waar u het kunt analyseren met behulp van [logboekquery's](../logs/log-query-overview.md) en om andere functies, zoals [logboekwaarschuwingen](../alerts/alerts-log-query.md) en [werkmappen](../visualize/workbooks-overview.md), in te schakelen.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Vereisten

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
- Als u de opdrachten vanaf uw lokale computer wilt uitvoeren, installeert u Azure CLI of de Azure PowerShell-modules. Zie [De Azure CLI installeren](/cli/azure/install-azure-cli) en [Azure PowerShell installeren](/powershell/azure/install-az-ps) voor meer informatie.

## <a name="create-a-log-analytics-workspace"></a>Een Log Analytics-werkruimte maken

### <a name="review-the-template"></a>De sjabloon controleren

Met de volgende sjabloon wordt een nieuwe lege Log Analytics-werkruimte gemaakt. Sla deze sjabloon op als *CreateWorkspace.json*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the workspace."
      }
    },
    "sku": {
      "type": "string",
      "defaultValue": "pergb2018",
      "allowedValues": [
        "pergb2018",
        "Free",
        "Standalone",
        "PerNode",
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Pricing tier: PerGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "australiacentral",
        "australiaeast",
        "australiasoutheast",
        "brazilsouth",
        "canadacentral",
        "centralindia",
        "centralus",
        "eastasia",
        "eastus",
        "eastus2",
        "francecentral",
        "japaneast",
        "koreacentral",
        "northcentralus",
        "northeurope",
        "southafricanorth",
        "southcentralus",
        "southeastasia",
        "switzerlandnorth",
        "switzerlandwest",
        "uksouth",
        "ukwest",
        "westcentralus",
        "westeurope",
        "westus",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for the workspace."
      }
    },
    "retentionInDays": {
      "type": "int",
      "defaultValue": 120,
      "metadata": {
        "description": "Number of days to retain data."
      }
    },
    "resourcePermissions": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "true to use resource or workspace permissions. false to require workspace permissions."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.OperationalInsights/workspaces",
      "apiVersion": "2020-08-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "properties": {
        "sku": {
          "name": "[parameters('sku')]"
        },
        "retentionInDays": "[parameters('retentionInDays')]",
        "features": {
          "searchVersion": 1,
          "legacy": 0,
          "enableLogAccessUsingOnlyResourcePermissions": "[parameters('resourcePermissions')]"
        }
      }
    }
  ]
}
```

Met de sjabloon wordt één resource gedefinieerd:

- [Microsoft.OperationalInsights/workspaces](/azure/templates/microsoft.operationalinsights/workspaces)

### <a name="deploy-the-template"></a>De sjabloon implementeren

Implementeer de sjabloon met een standaardmethode voor [het implementeren van een ARM-sjabloon](../../azure-resource-manager/templates/deploy-portal.md), zoals in de volgende voorbeelden waarin CLI en PowerShell wordt gebruikt. Vervang de voorbeeldwaarden voor **Resourcegroep**, **Werkruimte** en **Locatie** door de juiste waarden voor uw omgeving. De naam van de werkruimte moet uniek zijn voor alle Azure-abonnementen.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create \
    --name CreateWorkspace \
    --resource-group my-resource-group \
    --template-file CreateWorkspace.json \
    --parameters workspaceName='my-workspace-01' location='eastus'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name AzureMonitorDeployment -ResourceGroupName my-resource-group -TemplateFile CreateWorkspace.json -workspaceName my-workspace-01 -location eastus
```

---

### <a name="validate-the-deployment"></a>De implementatie valideren

Controleer of de werkruimte is gemaakt met behulp van een van de volgende opdrachten. Vervang de voorbeeldwaarden voor **Resourcegroep** en **Werkruimte** door de waarden die u hierboven hebt gebruikt.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor log-analytics workspace show --resource-group my-workspace-01 --workspace-name my-resource-group
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzOperationalInsightsWorkspace -Name my-workspace-01 -ResourceGroupName my-resource-group
```

---

## <a name="create-diagnostic-setting"></a>Diagnostische instelling maken

### <a name="review-the-template"></a>De sjabloon controleren

Met de volgende sjabloon maakt u een diagnostische instelling die het activiteitenlogboek naar een Log Analytics-werkruimte verzendt. Sla deze sjabloon op als *CreateDiagnosticSetting.json*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "settingName": {
        "type": "String"
    },
    "workspaceId": {
        "type": "String"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/diagnosticSettings",
      "apiVersion": "2017-05-01-preview",
      "name": "[parameters('settingName')]",
      "dependsOn": [],
      "properties": {
        "workspaceId": "[parameters('workspaceId')]",
        "logs": [
          {
          "category": "Administrative",
          "enabled": true
          },
          {
          "category": "Alert",
          "enabled": true
          },
          {
          "category": "Autoscale",
          "enabled": true
          },
          {
          "category": "Policy",
          "enabled": true
          },
          {
          "category": "Recommendation",
          "enabled": true
          },
          {
          "category": "ResourceHealth",
          "enabled": true
          },
          {
          "category": "Security",
          "enabled": true
          },
          {
          "category": "ServiceHealth",
          "enabled": true
          }
        ]
      }
    }
  ]
}
```

Met de sjabloon wordt één resource gedefinieerd:

- [Microsoft.Insights/diagnosticSettings](/azure/templates/microsoft.insights/diagnosticsettings)

### <a name="deploy-the-template"></a>De sjabloon implementeren

Implementeer de sjabloon met een standaardmethode voor [het implementeren van een ARM-sjabloon](../../azure-resource-manager/templates/deploy-portal.md), zoals in de volgende voorbeelden waarin CLI en PowerShell wordt gebruikt. Vervang de voorbeeldwaarden voor **Resourcegroep**, **Werkruimte** en **Locatie** door de juiste waarden voor uw omgeving. De naam van de werkruimte moet uniek zijn voor alle Azure-abonnementen.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az deployment sub create --name CreateDiagnosticSetting --location eastus --template-file CreateDiagnosticSetting.json --parameters settingName='Send Activity log to workspace' workspaceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace-01'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
New-AzSubscriptionDeployment -Name CreateDiagnosticSetting -location eastus -TemplateFile CreateDiagnosticSetting.json -settingName "Send Activity log to workspace" -workspaceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace-01"
```
---

### <a name="validate-the-deployment"></a>De implementatie valideren

Controleer of de diagnostische instelling is gemaakt met behulp van een van de volgende opdrachten. Vervang de voorbeeldwaarden voor de naam van het abonnement en de instelling door de waarden die u hierboven hebt gebruikt.

> [!NOTE]
> U kunt momenteel geen diagnostische instellingen op abonnementsniveau ophalen met behulp van PowerShell.

```azurecli
az monitor diagnostic-settings show --resource '/subscriptions/00000000-0000-0000-0000-000000000000' --name 'Send Activity log to workspace'
```

## <a name="generate-log-data"></a>Logboekgegevens genereren

Alleen nieuwe vermeldingen in het activiteitenlogboek worden verzonden naar de Log Analytics-werkruimte, dus voer enkele acties in uw abonnement uit die worden geregistreerd, zoals het starten of stoppen van een virtuele machine of het maken of wijzigen van een andere resource. Mogelijk moet u enkele minuten wachten totdat de diagnostische instelling is gemaakt en de eerste gegevens naar de werkruimte worden geschreven. Na deze vertraging worden alle gebeurtenissen die naar het activiteitenlogboek worden geschreven, binnen een paar seconden verzonden naar de werkruimte.

## <a name="retrieve-data-with-a-log-query"></a>Gegevens ophalen met een logboekquery

Gebruik de Azure-portal om Log Analytics te gebruiken om gegevens op te halen uit de werkruimte. Zoek in de Azure-portal naar **Monitor** en selecteer dit.

![Azure Portal](media/quick-collect-activity-log/azure-portal-monitor.png)

Selecteer **Logboeken** in het menu **Azure Monitor**. Sluit de pagina **Voorbeeldquery's**. Als het bereik niet is ingesteld op de werkruimte die u hebt gemaakt, klikt u op **Bereik selecteren** en zoekt u de werkruimte op.

![Log Analytics-bereik](media/quick-collect-activity-log/log-analytics-scope.png)

Typ `AzureActivity` in het queryvenster en klik op **Uitvoeren**. Dit is een eenvoudige query waarmee alle records in de tabel *AzureActivity* worden geretourneerd. Deze tabel bevat alle records die vanuit het activiteitenlogboek zijn verzonden.

![Eenvoudige query](media/quick-collect-activity-log/query-01.png)

Vouw een van de records uit om de gedetailleerde eigenschappen ervan te bekijken.

![Eigenschappen uitvouwen](media/quick-collect-activity-log/expand-properties.png)

Probeer een complexere query, zoals `AzureActivity | summarize count() by CategoryValue`, die een telling van gebeurtenissen per categorie bevat.

![Complexe query](media/quick-collect-activity-log/query-02.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om verder te gaan met volgende snelstarts en zelfstudies, kunt u deze resources het beste intact laten. Als u een resourcegroep niet meer nodig hebt, verwijdert u de resourcegroep. Hiermee worden ook de waarschuwingsregel en de gerelateerde resources verwijderd. De resourcegroep verwijderen met behulp van Azure CLI of Azure PowerShell

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name my-resource-group
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Remove-AzResourceGroup -Name my-resource-group
```

---

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u het activiteitenlogboek geconfigureerd om te worden verzonden naar een Log Analytics-werkruimte. U kunt nu andere gegevens configureren die moeten worden verzameld in de werkruimte, waar u ze kunt analyseren met [logboekquery's](../logs/log-query-overview.md) in Azure Monitor, en gebruikmaken van functies zoals [logboekwaarschuwingen](../alerts/alerts-log-query.md) en [werkmappen](../visualize/workbooks-overview.md). Vervolgens moet u de [resourcelogboeken](../essentials/resource-logs.md) uit uw Azure-resources verzamelen, die de gegevens in het Activiteitenlogboek aanvullen en die inzicht geven in de bewerkingen die binnen elke resource zijn uitgevoerd.

> [!div class="nextstepaction"]
> [Resourcelogboeken verzamelen en analyseren met Azure Monitor](../essentials/tutorial-resource-logs.md)