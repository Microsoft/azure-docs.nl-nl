---
title: Instellingen voor slimme detectie regel-Azure-toepassing Insights
description: Beheer en configuratie automatiseren van Azure-toepassing Insights-regels voor slimme detectie met Azure Resource Manager sjablonen
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 02/14/2021
ms.reviewer: mbullwin
ms.openlocfilehash: e3a7b71cd8975957754ba014ecc700484c27a6d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726118"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Application Insights Slimme detectie regels beheren met Azure Resource Manager sjablonen

Slimme detectie regels in Application Insights kunnen worden beheerd en geconfigureerd met behulp van [Azure Resource Manager sjablonen](../../azure-resource-manager/templates/template-syntax.md).
Deze methode kan worden gebruikt bij het implementeren van nieuwe Application Insights resources met Azure Resource Manager Automation of voor het wijzigen van de instellingen van bestaande resources.

## <a name="smart-detection-rule-configuration"></a>Configuratie van de detectie regel voor Smart

U kunt de volgende instellingen configureren voor een regel voor Slimme detectie:
- Als de regel is ingeschakeld (de standaard waarde is **waar**.)
- Als er e-mail berichten moeten worden verzonden naar gebruikers die zijn gekoppeld aan de [bewakings lezer](../../role-based-access-control/built-in-roles.md#monitoring-reader) van het abonnement en bij het [bewaken van Inzender](../../role-based-access-control/built-in-roles.md#monitoring-contributor) rollen wanneer een detectie wordt gevonden (de standaard waarde is **waar**.)
- Aanvullende e-mail ontvangers die een melding moeten ontvangen wanneer een detectie wordt gevonden.
    -  De e-mail configuratie is niet beschikbaar voor slimme detectie regels die als _Preview_ zijn gemarkeerd.

Om het configureren van de regel instellingen via Azure Resource Manager toe te staan, is de configuratie van de detectie regel nu beschikbaar als binnenste resource binnen de Application Insights resource met de naam **ProactiveDetectionConfigs**.
Voor een maximale flexibiliteit kan elke Slimme detectie regel worden geconfigureerd met unieke meldings instellingen.

## <a name="examples"></a>Voorbeelden

Hieronder ziet u enkele voor beelden van het configureren van de instellingen van Slimme detectie regels met Azure Resource Manager sjablonen.
Alle voor beelden verwijzen naar een Application Insights resource met de naam _' mijn toepassing '_ en de detectie regel voor lange afhankelijkheids duur, die intern de naam _' longdependencyduration '_ heeft.
Zorg ervoor dat u de naam van de Application Insights resource vervangt en de relevante interne naam voor de Slimme detectie regel opgeeft. Controleer de onderstaande tabel voor een lijst met de corresponderende interne Azure Resource Manager namen voor elke Slimme detectie regel.

### <a name="disable-a-smart-detection-rule"></a>Een regel voor slimme detectie uitschakelen

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Het verzenden van e-mail meldingen voor een slimme detectie regel uitschakelen

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Aanvullende e-mail ontvangers voor een slimme detectie regel toevoegen

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ["alice@contoso.com", "bob@contoso.com"],
            "enabled": true
          }
        }
      ]
    }

```


## <a name="smart-detection-rule-names"></a>Namen van Slimme detectie regels

Hieronder ziet u een tabel met namen van Slimme detectie regels zoals ze worden weer gegeven in de portal, samen met hun interne namen, die moeten worden gebruikt in de sjabloon Azure Resource Manager.

> [!NOTE]
> Slimme detectie regels die als _Preview_ zijn gemarkeerd, bieden geen ondersteuning voor e-mail meldingen. Daarom kunt u de _ingeschakelde_ eigenschap alleen instellen voor deze regels. 

| Naam van Azure Portal regel | Interne naam
|:---|:---|
| Langzame laad tijd van pagina's | slowpageloadtime |
| Trage reactie tijd van server | slowserverresponsetime |
| Lange duur van de afhankelijkheid | longdependencyduration |
| Degradatie van de reactie tijd van de server | degradationinserverresponsetime |
| Degradatie van afhankelijkheids duur | degradationindependencyduration |
| Degradatie van de verhouding van de ernst van de tracering (preview-versie) | extension_traceseveritydetector |
| Uitzonderings volume met een abnormale toename (preview-versie) | extension_exceptionchangeextension |
| Mogelijke geheugenlek gedetecteerd (preview-versie) | extension_memoryleakextension |
| Mogelijk beveiligings probleem gedetecteerd (preview-versie) | extension_securityextensionspackage |
| Een abnormale toename in het dagelijks gegevens volume (preview-versie) | extension_billingdatavolumedailyspikeextension |

### <a name="failure-anomalies-alert-rule"></a>Waarschuwings regel voor afwijkingen van de fout

Deze Azure Resource Manager sjabloon demonstreert het configureren van een waarschuwings regel voor fout afwijkingen met de ernst 2.

> [!NOTE]
> Fout afwijkingen is een globale service, dus de locatie van de regel wordt gemaakt op de algemene locatie.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "location": "global", 
            "properties": {
                  "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls.",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT1M",
                  "detector": {
                  "id": "FailureAnomaliesDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```

> [!NOTE]
> Deze Azure Resource Manager sjabloon is uniek voor de waarschuwings regel voor afwijkingen van de fout en wijkt af van de andere klassieke regels voor slimme detectie die in dit artikel worden beschreven. Als u fout afwijkingen hand matig wilt beheren, moet u dit doen in Azure Monitor-waarschuwingen, terwijl alle andere slimme detectie regels worden beheerd in het deel venster Slimme detectie van de gebruikers interface.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het automatisch detecteren van:

- [Foutafwijkingen](./proactive-failure-diagnostics.md)
- [Geheugen lekken](./proactive-potential-memory-leak.md)
- [Prestatieafwijkingen](./proactive-performance-diagnostics.md)

