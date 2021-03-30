---
title: Toepassingen die worden uitgevoerd op Azure Functions bewaken met Application Insights-Azure Monitor | Microsoft Docs
description: Azure Monitor naadloos kan worden geïntegreerd met uw toepassing die wordt uitgevoerd op Azure Functions, en biedt u de mogelijkheid om de prestaties te bewaken en de problemen met uw apps in de gaten te houden.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 06/26/2020
ms.openlocfilehash: 6d790a6f02fdc07609e374639c6e452b9088262e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87024568"
---
# <a name="monitoring-azure-functions-with-azure-monitor-application-insights"></a>Azure Functions met Azure Monitor bewaken Application Insights

[Azure functions](../../azure-functions/functions-overview.md) biedt ingebouwde integratie met Azure-toepassing inzichten om functies te bewaken. 

Application Insights verzamelt logboek-, prestatie-en fout gegevens en detecteert automatisch prestatie afwijkingen. Application Insights bevat krachtige analyse hulpprogramma's waarmee u problemen kunt vaststellen en inzicht kunt krijgen in de manier waarop uw functies worden gebruikt. Wanneer u de zicht baarheid van uw toepassings gegevens hebt, kunt u de prestaties en bruikbaarheid continu verbeteren. U kunt zelfs Application Insights gebruiken tijdens de ontwikkeling van een lokale functie-app-project. 

De vereiste Application Insights instrumentatie is ingebouwd in Azure Functions. Het enige wat u nodig hebt, is een geldige instrumentatie sleutel om uw functie-app te verbinden met een Application Insights bron. De instrumentatie sleutel moet worden toegevoegd aan de toepassings instellingen wanneer de resource van de functie-app wordt gemaakt in Azure. Als uw functie-app deze sleutel nog niet heeft, kunt u deze hand matig instellen. Lees voor meer informatie over het [bewaken van Azure functions](../../azure-functions/functions-monitoring.md?tabs=cmd).

## <a name="distributed-tracing-for-java-applications-on-windows-public-preview"></a>Gedistribueerde tracering voor Java-toepassingen in Windows (open bare preview)

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als open bare Preview voor Java-Azure Functions in Windows, gedistribueerde tracering voor Java Azure Functions op Linux wordt niet ondersteund. Voor het verbruiks abonnement is een koude start van 8-9 seconden.

Als uw toepassingen in Java zijn geschreven, kunt u rijkere gegevens bekijken uit uw functions-toepassingen, waaronder, aanvragen, afhankelijkheden, logboeken en metrieken. Met de aanvullende gegevens kunt u ook end-to-end-trans acties bekijken en diagnosticeren en de toepassings toewijzing bekijken, waarmee veel trans acties worden weer gegeven met een topologische weer gave van de manier waarop de systemen communiceren, en wat de gemiddelde prestatie-en fout tarieven zijn.

De end-to-end diagnostische gegevens en de toepassings toewijzing bieden inzicht in één trans actie/aanvraag. Deze twee functies zijn zeer nuttig voor het vinden van de hoofd oorzaak van betrouwbaarheids problemen en prestatie knelpunten op basis van een aanvraag.

### <a name="how-to-enable-distributed-tracing-for-java-function-apps"></a>Hoe kan ik gedistribueerde tracering inschakelen voor Java-functie-apps?

Navigeer naar de Blade functies app-overzicht en ga naar configuraties. Klik onder toepassings instellingen op "+ nieuwe toepassings instelling". Voeg de volgende twee toepassings instellingen onder waarden toe en klik vervolgens op opslaan in de linkerbovenhoek. ZO!

```
XDT_MicrosoftApplicationInsights_Java -> 1
ApplicationInsightsAgent_EXTENSION_VERSION -> ~2
```

## <a name="next-steps"></a>Volgende stappen

* Meer instructies en informatie over het controleren van bewakings [Azure functions](../../azure-functions/functions-monitoring.md)
* Een overzicht van [gedistribueerde tracering](./distributed-tracing.md) weer geven
* Bekijk welke [toepassings toewijzing](./app-map.md?tabs=net) voor uw bedrijf kan worden uitgevoerd
* Meer informatie over [aanvragen en afhankelijkheden voor java-apps](./java-in-process-agent.md)
* Meer informatie over [Azure monitor](../overview.md) en [Application Insights](./app-insights-overview.md)
