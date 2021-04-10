---
title: Azure Advisor-waarschuwingen maken voor nieuwe aanbevelingen met behulp van Resource Manager-sjabloon
description: Meer informatie over hoe u een waarschuwing instelt voor nieuwe aanbevelingen van Azure Advisor met behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon).
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: 7b10ad06e5397ab733987373a3bd18de981c8c97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100590059"
---
# <a name="quickstart-create-azure-advisor-alerts-on-new-recommendations-using-an-arm-template"></a>Quickstart: Azure Advisor-waarschuwingen maken voor nieuwe aanbevelingen met behulp van een ARM-sjabloon

In dit artikel wordt beschreven hoe u een waarschuwing instelt voor nieuwe aanbevelingen van Azure Advisor met behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wanneer Azure Advisor een nieuwe aanbeveling voor een van uw resources detecteert, wordt een gebeurtenis opgeslagen in het [Azure-activiteitenlogboek](../azure-monitor/essentials/platform-logs-overview.md). U kunt waarschuwingen instellen voor deze gebeurtenissen van Azure Advisor met behulp van een functie voor het maken van aanbevelingsgerichte waarschuwingen. U kunt een abonnement en optioneel een resourcegroep selecteren om de resources op te geven waarvoor u waarschuwingen wilt ontvangen.

U kunt ook de typen aanbevelingen vaststellen met behulp van de volgende eigenschappen:

- Categorie
- Impactniveau
- Aanbevelingstype

U kunt ook de actie configureren die wordt uitgevoerd wanneer een waarschuwing wordt geactiveerd door:

- Een bestaande actiegroep te selecteren
- Een nieuwe actiegroep te maken

Raadpleeg [Actiegroepen maken en beheren](../azure-monitor/alerts/action-groups.md) voor meer informatie over actiegroepen.

> [!NOTE]
> Advisor-waarschuwingen zijn momenteel alleen beschikbaar voor aanbevelingen voor hoge beschikbaarheid, prestaties en kosten. Aanbevelingen voor beveiliging worden niet ondersteund.

## <a name="prerequisites"></a>Vereisten

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
- Als u de opdrachten vanaf uw lokale computer wilt uitvoeren, installeert u Azure CLI of de Azure PowerShell-modules. Zie [De Azure CLI installeren](/cli/azure/install-azure-cli) en [Azure PowerShell installeren](/powershell/azure/install-az-ps) voor meer informatie.

## <a name="review-the-template"></a>De sjabloon controleren

Met de volgende sjabloon maakt u een actiegroep met een e-maildoel en schakelt u alle servicestatusmeldingen voor het doelabonnement in. Sla deze sjabloon op als *CreateAdvisorAlert.json*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
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
      "comments": "Azure Advisor Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
      "location": "Global",
      "scale": null,
      "tags": {},
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
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
      },
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ]
    }
  ]
}
```

In de sjabloon zijn twee resources gedefinieerd:

- [Microsoft.Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft.Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>De sjabloon implementeren

Implementeer de sjabloon met een standaardmethode voor [het implementeren van een ARM-sjabloon](../azure-resource-manager/templates/deploy-portal.md), zoals in de volgende voorbeelden waarin CLI en PowerShell wordt gebruikt. Vervang de voorbeeldwaarden voor **Resourcegroep** en **E-mailadres** door de juiste waarden voor uw omgeving. De naam van de werkruimte moet uniek zijn voor alle Azure-abonnementen.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateAdvisorAlert --resource-group my-resource-group --template-file CreateAdvisorAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateAdvisorAlert -ResourceGroupName my-resource-group -TemplateFile CreateAdvisorAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>De implementatie valideren

Controleer of de werkruimte is gemaakt met behulp van een van de volgende opdrachten. Vervang de voorbeeldwaarden voor **Resourcegroep** door de waarde die u hierboven hebt gebruikt.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name AdvisorAlertsTest
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name AdvisorAlertsTest
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

- Een [overzicht van waarschuwingen voor het activiteitenlogboek](../azure-monitor/alerts/alerts-overview.md) en meer informatie over het ontvangen van waarschuwingen.
- Meer informatie over [actiegroepen](../azure-monitor/alerts/action-groups.md).
