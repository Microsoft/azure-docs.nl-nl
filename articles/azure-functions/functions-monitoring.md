---
title: Azure Functions controleren
description: Meer informatie over het gebruik van Azure-toepassing Insights met Azure Functions om de uitvoering van functies te bewaken.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 10/14/2020
ms.custom: devx-track-csharp, fasttrack-edit, contperf-fy21q2, devx-track-js
ms.openlocfilehash: 637f09c5ee52928631b965dfa6caea9368b44991
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550756"
---
# <a name="monitor-azure-functions"></a>Azure Functions controleren

[Azure functions](functions-overview.md) biedt ingebouwde integratie met [Azure-toepassing inzichten](../azure-monitor/app/app-insights-overview.md) om functies te bewaken. Dit artikel bevat een overzicht van de bewakings mogelijkheden van Azure voor het bewaken van Azure Functions.

Application Insights verzamelt logboek-, prestatie-en fout gegevens. Door automatisch prestatie afwijkingen te detecteren en krachtige analyse hulpprogramma's te gebruiken, kunt u problemen gemakkelijker vaststellen en beter begrijpen hoe uw functies worden gebruikt. Deze hulpprogram ma's zijn ontworpen om u te helpen de prestaties en bruikbaarheid van uw functies continu te verbeteren. U kunt zelfs Application Insights gebruiken tijdens de ontwikkeling van een lokale functie-app-project. Zie [Wat is Application Insights?](../azure-monitor/app/app-insights-overview.md)voor meer informatie.

Als Application Insights instrumentatie in Azure Functions is ingebouwd, hebt u een geldige instrumentatie sleutel nodig om uw functie-app te koppelen aan een Application Insights resource. De instrumentatie sleutel wordt toegevoegd aan de toepassings instellingen wanneer u de resource van de functie-app in azure maakt. Als uw functie-app deze sleutel nog niet heeft, kunt u [deze hand matig instellen](configure-monitoring.md#enable-application-insights-integration).  

## <a name="application-insights-pricing-and-limits"></a>Prijzen en limieten voor Application Insights

U kunt Application Insights integratie met Azure Functions gratis uitproberen, met een dagelijkse limiet voor de hoeveelheid gegevens die gratis wordt verwerkt.

Als u toepassingen Insights tijdens de ontwikkeling inschakelt, kunt u deze limiet tijdens het testen aanraken. Azure biedt Portal-en e-mail meldingen wanneer u uw dagelijkse limiet nadert. Als u deze waarschuwingen mist en de limiet bereikt, worden nieuwe logboeken niet weer gegeven in Application Insights query's. Houd rekening met de limiet om onnodige probleemoplossings tijd te voor komen. Zie [prijzen en gegevens volume beheren in Application Insights](../azure-monitor/app/pricing.md)voor meer informatie.

> [!IMPORTANT]
> Application Insights heeft een [bemonsterings](../azure-monitor/app/sampling.md) functie waarmee u kunt voor komen dat er te veel telemetriegegevens worden geproduceerd tijdens voltooide uitvoeringen op momenten van piek belasting. Steek proeven zijn standaard ingeschakeld. Als er gegevens ontbreken, moet u mogelijk de sampling-instellingen aanpassen aan uw specifieke bewakings scenario. Zie [sampling configureren](configure-monitoring.md#configure-sampling)voor meer informatie.

De volledige lijst met Application Insights functies die beschikbaar zijn voor uw functie-app, wordt beschreven in [Application Insights voor Azure functions ondersteunde functies](../azure-monitor/app/azure-functions-supported-features.md).

## <a name="application-insights-integration"></a>Integratie van Application Insights

Normaal gesp roken maakt u een Application Insights-instantie wanneer u de functie-app maakt. In dit geval is de instrumentatie sleutel die is vereist voor de integratie al ingesteld als een toepassings instelling met de naam *APPINSIGHTS_INSTRUMENTATIONKEY*. Als uw functie-app om een of andere reden geen instrumentatie sleutel heeft ingesteld, moet u [Application Insights-integratie inschakelen](configure-monitoring.md#enable-application-insights-integration).  

## <a name="collecting-telemetry-data"></a>Telemetriegegevens verzamelen

Als Application Insights-integratie is ingeschakeld, worden telemetriegegevens verzonden naar uw verbonden Application Insights-exemplaar. Deze gegevens bevatten logboeken die zijn gegenereerd door de host functions, traceringen die zijn geschreven vanuit uw functions-code en prestatie gegevens. 

>[!NOTE]
>Naast gegevens van uw functies en de host van functies, kunt u ook gegevens verzamelen uit de controller van de [functies schalen](#scale-controller-logs).   

### <a name="log-levels-and-categories"></a>Logboek niveaus en categorieën

Wanneer u traceringen van uw toepassings code schrijft, moet u een logboek niveau toewijzen aan de traceringen. Met de logboek niveaus kunt u de hoeveelheid gegevens die uit uw traceringen wordt verzameld, beperken.  

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

Zie [logboek niveaus configureren](configure-monitoring.md#configure-log-levels)voor meer informatie over logboek niveaus.

Door vastgelegde items aan een categorie toe te wijzen, hebt u meer controle over telemetrie die is gegenereerd op basis van specifieke bronnen in uw functie-app. Categorieën maken het gemakkelijker om analyses uit te voeren via verzamelde gegevens. Traceringen die zijn geschreven vanuit uw functie code worden toegewezen aan afzonderlijke categorieën op basis van de functie naam. Zie [Configure categories (categorieën configureren](configure-monitoring.md#configure-categories)) voor meer informatie over categorieën.

### <a name="custom-telemetry-data"></a>Aangepaste telemetriegegevens

In [C#](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions) en [Java script](functions-reference-node.md#log-custom-telemetry)kunt u een Application Insights SDK gebruiken om aangepaste telemetriegegevens te schrijven.

### <a name="dependencies"></a>Afhankelijkheden

Met ingang van versie 2. x van functions verzamelt de runtime automatisch gegevens over afhankelijkheden voor bindingen die gebruikmaken van bepaalde client-Sdk's. Application Insights verzamelt gegevens over de volgende afhankelijkheden:

+ Azure Cosmos DB 
+ Azure Event Hubs
+ Azure Service Bus
+ Azure Storage services (BLOB, wachtrij en tabel)

HTTP-aanvragen en database aanroepen die gebruikmaken van `SqlClient` worden ook vastgelegd. Zie [automatisch bijgehouden afhankelijkheden](../azure-monitor/app/asp-net-dependencies.md#automatically-tracked-dependencies)voor de volledige lijst met afhankelijkheden die door Application Insights worden ondersteund.

Application Insights genereert een _toepassings toewijzing_ van verzamelde afhankelijkheids gegevens. Hier volgt een voor beeld van een toepassings toewijzing van een HTTP-trigger functie met een uitvoer binding voor de wachtrij opslag.  

![Toepassings toewijzing met afhankelijkheid](./media/functions-monitoring/app-map.png)

Afhankelijkheden worden op het `Information` niveau geschreven. Als u filtert op `Warning` of hoger, worden de afhankelijkheids gegevens niet weer gegeven. Het automatisch verzamelen van afhankelijkheden gebeurt ook bij een niet-gebruikers bereik. Als u afhankelijkheids gegevens wilt vastleggen, moet u ervoor zorgen dat het niveau is ingesteld op ten minste `Information` buiten het bereik van de gebruiker ( `Function.<YOUR_FUNCTION_NAME>.User` ) op uw host.

Naast het automatisch verzamelen van afhankelijkheids gegevens kunt u ook een van de taalspecifieke Application Insights Sdk's gebruiken om aangepaste afhankelijkheids informatie te schrijven naar de logboeken. Zie een van de volgende taalspecifieke voor beelden voor een voor beeld van het schrijven van aangepaste afhankelijkheden:

+ [Aangepaste telemetrie voor logboeken in C#-functies](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions)
+ [Aangepaste telemetrie voor logboeken in JavaScript-functies](functions-reference-node.md#log-custom-telemetry) 

## <a name="writing-to-logs"></a>Schrijven naar Logboeken 

De manier waarop u naar Logboeken schrijft en de Api's die u gebruikt, is afhankelijk van de taal van uw functie-app-project.   
Raadpleeg de ontwikkelaars handleiding voor uw taal voor meer informatie over het schrijven van logboeken van uw functies.

+ [C# (.NET-klassen bibliotheek)](functions-dotnet-class-library.md#logging)
+ [Java](functions-reference-java.md#logger)
+ [JavaScript](functions-reference-node.md#write-trace-output-to-logs) 
+ [PowerShell](functions-reference-powershell.md#logging)
+ [Python](functions-reference-python.md#logging)

## <a name="analyze-data"></a>Gegevens analyseren

De gegevens die zijn verzameld uit de functie-app worden standaard opgeslagen in Application Insights. In de [Azure Portal](https://portal.azure.com)biedt Application Insights een uitgebreide set visualisaties van uw telemetriegegevens. U kunt inzoomen op fouten logboeken en query's uitvoeren op gebeurtenissen en metrische gegevens. Zie [Azure functions telemetrie in Application Insights analyseren](analyze-telemetry-data.md)voor meer informatie, waaronder eenvoudige voor beelden van het weer geven en opvragen van uw verzamelde gegevens. 

## <a name="streaming-logs"></a>Streaming-logboeken

Tijdens het ontwikkelen van een toepassing wilt u vaak zien wat er in de buurt van real-time naar de logboeken wordt geschreven wanneer u in azure uitvoert.

Er zijn twee manieren om een stroom weer te geven van de logboek gegevens die worden gegenereerd door uw functie-uitvoeringen.

* **Ingebouwde logboek streaming**: met het app service-platform kunt u een stroom weer geven van de logboek bestanden van uw toepassing. Deze stroom is gelijk aan de uitvoer die wordt weer gegeven wanneer u tijdens de [lokale ontwikkeling](functions-develop-local.md) fouten opspoort in uw functies en wanneer u het tabblad **testen** in de portal gebruikt. Alle gegevens op basis van het logboek worden weer gegeven. Zie [Stream-logboeken](../app-service/troubleshoot-diagnostic-logs.md#stream-logs)voor meer informatie. Deze streaming-methode ondersteunt slechts één exemplaar en kan niet worden gebruikt met een app die wordt uitgevoerd op Linux in een verbruiks abonnement.

* **Live Metrics stream**: als uw functie-app is [verbonden met Application Insights](configure-monitoring.md#enable-application-insights-integration), kunt u de logboek gegevens en andere gegevens in bijna realtime weer geven in de Azure Portal met [Live Metrics stream](../azure-monitor/app/live-stream.md). Gebruik deze methode wanneer u functies bewaken die worden uitgevoerd op meerdere exemplaren of op Linux in een verbruiks abonnement. Met deze methode worden [voorbeeld gegevens](configure-monitoring.md#configure-sampling)gebruikt.

Logboek stromen kunnen zowel in de portal als in de meeste lokale ontwikkel omgevingen worden weer gegeven. Zie [streaming-uitvoerings logboeken inschakelen in azure functions](streaming-logs.md)voor meer informatie over het inschakelen van logboek stromen.

## <a name="diagnostic-logs"></a>Diagnostische logboeken

_Deze functie is beschikbaar als preview-versie._ 

Met Application Insights kunt u telemetriegegevens exporteren naar lange termijn opslag of andere Analysis Services.  

Omdat functies ook kunnen worden geïntegreerd met Azure Monitor, kunt u ook diagnostische instellingen gebruiken om telemetriegegevens te verzenden naar verschillende bestemmingen, waaronder Azure Monitor-Logboeken. Zie [Azure functions bewaken met Azure monitor-logboeken](functions-monitor-log-analytics.md)voor meer informatie.

## <a name="scale-controller-logs"></a>Controller logboeken schalen

_Deze functie is beschikbaar als preview-versie._ 

De [Azure functions Scale-controller](./event-driven-scaling.md#runtime-scaling) controleert exemplaren van de Azure functions host waarop uw app wordt uitgevoerd. Deze controller maakt beslissingen over het toevoegen of verwijderen van exemplaren op basis van de huidige prestaties. U kunt de schaal controller logboeken naar Application Insights verzenden om beter inzicht te krijgen in de beslissingen die de schaal controller voor uw functie-app maakt. U kunt de gegenereerde logboeken ook opslaan in Blob Storage voor analyse door een andere service. 

Als u deze functie wilt inschakelen, voegt u een toepassings instelling toe `SCALE_CONTROLLER_LOGGING_ENABLED` met de naam van de instellingen van de functie-app. Zie [Logboeken voor schaal controller configureren](configure-monitoring.md#configure-scale-controller-logs)voor meer informatie.

## <a name="report-issues"></a>Problemen melden

Als u een probleem wilt melden met Application Insights integratie in functions, of als u een suggestie of aanvraag wilt doen, [maakt u een probleem in github](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="next-steps"></a>Volgende stappen

Zie de volgende resources voor meer informatie:

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core logboek registratie](/aspnet/core/fundamentals/logging/)
