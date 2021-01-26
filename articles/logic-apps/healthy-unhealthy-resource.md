---
title: Logboek registratie instellen voor het bewaken van Logic apps in Azure Security Center
description: Bewaak de status van uw Logic Apps-resources in Azure Security Center door diagnostische logboek registratie in te stellen.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: ed1fe2885b1be28a03251bcfcecd08bdbd35adcf
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790069"
---
# <a name="set-up-logging-to-monitor-logic-apps-in-azure-security-center"></a>Logboek registratie instellen voor het bewaken van Logic apps in Azure Security Center

Wanneer u uw Logic Apps-resources in [Microsoft Azure Security Center](../security-center/security-center-introduction.md)bewaken, kunt u [controleren of uw Logic apps het standaard beleid volgen](#view-logic-apps-health-status). In azure wordt de status van een Logic Apps resource weer gegeven nadat u logboek registratie hebt ingeschakeld en de bestemming van de logboeken correct hebt ingesteld. In dit artikel wordt uitgelegd hoe u diagnostische logboek registratie configureert en zorgt u ervoor dat alle logische apps in orde zijn.

> [!TIP]
> Als u de huidige status voor de Logic Apps-service wilt weten, bekijkt u de [pagina Azure-status](https://status.azure.com/), die de status van de verschillende producten en services in elke beschik bare regio vermeldt.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* Bestaande logische apps waarvoor [Diagnostische logboek registratie is ingeschakeld](#enable-diagnostic-logging).
* Een Log Analytics-werk ruimte die is vereist om logboek registratie in te scha kelen voor uw logische app. Als u geen werk ruimte hebt, maakt u eerst [uw werk ruimte](../azure-monitor/learn/quick-create-workspace.md).

## <a name="enable-diagnostic-logging"></a>registratie in het diagnoselogboek inschakelen

Voordat u de status van de resource status voor uw Logic apps kunt bekijken, moet u eerst [Diagnostische logboek registratie instellen](monitor-logic-apps-log-analytics.md). Als u al een Log Analytics-werk ruimte hebt, kunt u logboek registratie inschakelen wanneer u uw logische app of op bestaande logische apps maakt.

> [!TIP]
> De standaard aanbeveling is om Diagnostische logboeken in te scha kelen voor Logic Apps. U kunt deze instelling echter wel beheren voor uw Logic apps. Wanneer u Diagnostische logboeken inschakelt voor uw Logic apps, kunt u de informatie gebruiken om beveiligings incidenten te analyseren.

### <a name="check-diagnostic-logging-setting"></a>Instelling voor diagnostische logboek registratie controleren

Als u niet zeker weet of uw Logic apps diagnostische logboek registratie hebben ingeschakeld, kunt u Security Center inchecken:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Voer in de zoek balk **Security Center** in en selecteer deze.
1. Selecteer in het menu Security Center dashboard, onder **Algemeen**, de optie **aanbevelingen**.
1. Zoek en selecteer in de tabel met beveiligings suggesties de optie **controle inschakelen en logboek registratie** &gt; **van Diagnostische logboeken in Logic apps moet zijn ingeschakeld** in de tabel met beveiligings controles.
1. Vouw de sectie **herstel stappen** op de pagina aanbeveling uit en controleer de opties. U kunt Logic Apps diagnostische gegevens inschakelen door de **snelle oplossing** te selecteren. of door de hand matige herstel instructies te volgen.

## <a name="view-logic-apps-health-status"></a>De integriteits status van Logic apps weer geven

Nadat u [Diagnostische logboek registratie hebt ingeschakeld](#enable-diagnostic-logging), kunt u de integriteits status van uw Logic apps bekijken in Security Center.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Voer in de zoek balk **Security Center** in en selecteer deze.
1. Selecteer in het menu Security Center dashboard onder **Algemeen** de optie **inventarisatie**.
1. Filter op de pagina Inventariseer uw assets lijst om alleen Logic Apps resources weer te geven. Selecteer in het menu pagina de optie **resource typen** &gt; **Logic apps**.

   Het item **beschadigde resources** toont het aantal Logic apps dat Security Center niet in orde is.
1.  Controleer in de lijst met resources van Logic apps de kolom **aanbevelingen** . Als u de status gegevens voor een specifieke logische app wilt bekijken, selecteert u een resource naam of selecteert u de knop met het weglatings teken (**...**) &gt; **Resource weer geven**.
1.  Volg de stappen die worden vermeld voor uw Logic apps om mogelijke problemen met resource status te herstellen.

Als diagnostische logboek registratie al is ingeschakeld, is er mogelijk een probleem met het doel voor uw logboeken. Lees [hoe u problemen kunt oplossen met andere doelen voor diagnostische logboek registratie](#fix-diagnostic-logging-for-logic-apps).

## <a name="fix-diagnostic-logging-for-logic-apps"></a>Diagnostische logboek registratie voor logische apps oplossen

Als uw [Logic apps in Security Center niet in orde zijn](#view-logic-apps-health-status), opent u uw logische app in de code weergave in de Azure portal of via de Azure cli. Controleer vervolgens de doel configuratie voor uw Diagnostische logboeken: [azure log Analytics](#log-analytics-and-event-hubs-destinations), [Azure Event Hubs](#log-analytics-and-event-hubs-destinations)of [een Azure Storage-account](#storage-account-destination).

### <a name="log-analytics-and-event-hubs-destinations"></a>Log Analytics en Event Hubs doelen

Als u Log Analytics of Event Hubs als doel voor uw Logic Apps Diagnostische logboeken gebruikt, controleert u de volgende instellingen. 

1. Om te bevestigen dat u Diagnostische logboeken hebt ingeschakeld, controleert u of het veld Diagnostische instellingen `logs.enabled` is ingesteld op `true` . 
1. Controleer of het veld is ingesteld op om te bevestigen dat u in plaats daarvan geen opslag account hebt ingesteld als doel `storageAccountId` `false` .

Bijvoorbeeld:

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
                "notEquals": "true"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/storageAccountId",
                "exists": false
            }
        ]
    }
] 
```

### <a name="storage-account-destination"></a>Doel van opslag account

Als u een opslag account gebruikt als doel voor uw Logic Apps Diagnostische logboeken, controleert u de volgende instellingen.

1. Als u wilt controleren of u Diagnostische logboeken hebt ingeschakeld, controleert u of het veld Diagnostische instellingen `logs.enabled` is ingesteld op `true` .
1. Controleer of het veld is ingesteld op om te bevestigen dat u een Bewaar beleid hebt ingeschakeld voor uw Diagnostische logboeken `retentionPolicy.enabled` `true` .
1. Om te bevestigen dat u een Bewaar tijd van 0-365 dagen hebt ingesteld, controleert u of het `retentionPolicy.days` veld is ingesteld op een getal tussen 0 en 365.

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "0"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "[parameters('requiredRetentionDays')]"
            }
          ]
    },
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    }
]
```