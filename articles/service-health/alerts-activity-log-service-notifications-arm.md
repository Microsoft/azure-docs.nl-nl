---
title: Waarschuwingen voor activiteitenlogboek ontvangen bij Azure-service meldingen met behulp van de Resource Manager-sjabloon
description: Ontvang meldingen via een SMS, e-mail of webhook wanneer de Azure-service wordt uitgevoerd.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: 532fbae505e0bcaa6ab31a2e935362114537d134
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594947"
---
# <a name="quickstart-create-activity-log-alerts-on-service-notifications-using-an-arm-template"></a>Snelstart: Waarschuwingen voor activiteitenlogboek maken bij servicemeldingen met behulp van een ARM-sjabloon

In dit artikel wordt beschreven hoe u waarschuwingen voor een activiteitenlogboek instelt voor servicestatusmeldingen behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Servicestatusmeldingen worden opgeslagen in het [Activiteitenlogboek van Azure](../azure-monitor/essentials/platform-logs-overview.md). Gezien het mogelijk grote volume aan informatie dat in het activiteitenlogboek is opgeslagen, is er een afzonderlijke gebruikersinterface waarmee u gemakkelijk waarschuwingen voor servicestatusmeldingen kunt bekijken en instellen.

U kunt een waarschuwing ontvangen wanneer Azure servicestatusmeldingen verzendt naar uw Azure-abonnement. U kunt de waarschuwing configureren op basis van:

- De klasse van servicestatusmeldingen (serviceproblemen, gepland onderhoud, statusadviezen).
- Het betrokken abonnement.
- De betrokken service(s).
- De betrokken regio('s).

> [!NOTE]
> Service Health verzendt geen waarschuwingen in verband met resourcestatusgebeurtenissen.

U kunt ook configureren naar wie de waarschuwing moet worden verzonden:

- Selecteer een bestaande actiegroep.
- Maak een nieuwe actiegroep maken (die kan worden gebruikt voor toekomstige waarschuwingen).

Raadpleeg [Actiegroepen maken en beheren](../azure-monitor/alerts/action-groups.md) voor meer informatie over actiegroepen.

## <a name="prerequisites"></a>Vereisten

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
- Als u de opdrachten vanaf uw lokale computer wilt uitvoeren, installeert u Azure CLI of de Azure PowerShell-modules. Zie [De Azure CLI installeren](/cli/azure/install-azure-cli) en [Azure PowerShell installeren](/powershell/azure/install-az-ps) voor meer informatie.

## <a name="review-the-template"></a>De sjabloon controleren

Met de volgende sjabloon maakt u een actiegroep met een e-maildoel en schakelt u alle servicestatusmeldingen voor het doelabonnement in. Sla deze sjabloon op als *CreateServiceHealthAlert.json*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "type": "String",
      "defaultValue": "SubHealth"
    },
    "activityLogAlerts_name": {
      "type": "String",
      "defaultValue": "ServiceHealthActivityLogAlert"
    },
    "emailAddress": {
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "apiVersion": "2019-06-01",
      "name": "[parameters('actionGroups_name')]",
      "location": "Global",
      "scale": null,
      "dependsOn": [],
      "tags": {},
      "properties": {
        "groupShortName": "[parameters('actionGroups_name')]",
        "enabled": true,
        "emailReceivers": [
          {
            "name": "[parameters('actionGroups_name')]",
            "emailAddress": "[parameters('emailAddress')]"
          }
        ],
        "smsReceivers": [],
        "webhookReceivers": []
      }
    },
    {
      "comments": "Service Health Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
      "location": "Global",
      "scale": null,
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ],
      "tags": {},
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ServiceHealth"
            },
            {
              "field": "properties.incidentType",
              "equals": "Incident"
            }
          ]
        },
        "actions": {
          "actionGroups": [
            {
              "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
              "webhookProperties": {}
            }
          ]
        },
        "enabled": true,
        "description": ""
      }
    }
  ]
}
```

In de sjabloon zijn twee resources gedefinieerd:

- [Microsoft.Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft.Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>De sjabloon implementeren

Implementeer de sjabloon met een standaardmethode voor [het implementeren van een ARM-sjabloon](../azure-resource-manager/templates/deploy-portal.md), zoals in de volgende voorbeelden waarin CLI en PowerShell wordt gebruikt. Vervang de voorbeeldwaarden voor **Resourcegroep** en **E-mailadres** door de juiste waarden voor uw omgeving.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateServiceHealthAlert --resource-group my-resource-group --template-file CreateServiceHealthAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateServiceHealthAlert -ResourceGroupName my-resource-group -TemplateFile CreateServiceHealthAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>De implementatie valideren

Controleer of de werkruimte is gemaakt met behulp van een van de volgende opdrachten. Vervang de voorbeeldwaarden voor **Resourcegroep** door de waarde die u hierboven hebt gebruikt.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name ServiceHealthActivityLogAlert
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name ServiceHealthActivityLogAlert
```

---

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

- Meer informatie over [best practices voor het instellen van Azure Service Health-waarschuwingen](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Meer informatie over het [instellen van mobiele pushmeldingen voor Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Meer informatie over het [configureren van webhookmeldingen voor bestaande problematische beheersystemen](service-health-alert-webhook-guide.md).
- Meer informatie over [servicestatusmeldingen](service-notifications.md).
- Meer informatie over [meldingssnelheidsbeperking](../azure-monitor/alerts/alerts-rate-limiting.md).
- Bekijk het [webhookschema voor waarschuwingen voor het activiteitenlogboek](../azure-monitor/alerts/activity-log-alerts-webhook.md).
- Een [overzicht van waarschuwingen voor het activiteitenlogboek](../azure-monitor/alerts/alerts-overview.md) en meer informatie over het ontvangen van waarschuwingen.
- Meer informatie over [actiegroepen](../azure-monitor/alerts/action-groups.md).
