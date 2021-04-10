---
title: Diagnostische logboeken
titleSuffix: Azure Cognitive Services
description: Deze hand leiding bevat stapsgewijze instructies voor het inschakelen van diagnostische logboek registratie voor een Azure cognitieve service. Deze logboeken bevatten uitgebreide, frequente gegevens over de werking van een resource die worden gebruikt voor het identificeren van problemen en fout opsporing.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: a2005ca7b32136ff0032d27e04035c46b2e4e904
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100595366"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Diagnostische logboek registratie inschakelen voor Azure Cognitive Services

Deze hand leiding bevat stapsgewijze instructies voor het inschakelen van diagnostische logboek registratie voor een Azure cognitieve service. Deze logboeken bevatten uitgebreide, frequente gegevens over de werking van een resource die worden gebruikt voor het identificeren van problemen en fout opsporing. Voordat u doorgaat, moet u een Azure-account hebben met een abonnement op ten minste één cognitieve service, zoals [Bing Web Search](./bing-web-search/overview.md), [spraak Services](./speech-service/overview.md)of [Luis](./luis/what-is-luis.md).

## <a name="prerequisites"></a>Vereisten

Als u diagnostische logboek registratie wilt inschakelen, moet u uw logboek gegevens ergens opslaan. In deze zelf studie wordt gebruikgemaakt van Azure Storage en Log Analytics.

* [Azure Storage](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) : behoudt Diagnostische logboeken voor beleids controle, statische analyse of back-up. Het opslag account hoeft zich niet in hetzelfde abonnement te betreden als de resource waarmee logboeken worden verzonden, zolang de gebruiker die de instelling configureert de juiste toegang tot Azure RBAC heeft voor beide abonnementen.
* [Log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) : een flexibel hulp programma voor logboek zoeken en analyse waarmee onbewerkte logboeken kunnen worden geanalyseerd die door een Azure-resource zijn gegenereerd.

> [!NOTE]
> Er zijn aanvullende configuratie opties beschikbaar. Zie [logboek gegevens verzamelen en gebruiken van uw Azure-resources](../azure-monitor/essentials/platform-logs-overview.md)voor meer informatie.

## <a name="enable-diagnostic-log-collection"></a>Diagnostische logboek verzameling inschakelen  

Laten we beginnen door diagnostische logboek registratie in te scha kelen met behulp van de Azure Portal.

> [!NOTE]
> Als u deze functie wilt inschakelen met behulp van Power shell of de Azure CLI, gebruikt u de instructies in [logboek gegevens verzamelen en gebruiken van uw Azure-resources](../azure-monitor/essentials/platform-logs-overview.md).

1. Navigeer naar de Azure Portal. Zoek en selecteer vervolgens een Cognitive Services bron. Bijvoorbeeld uw abonnement op Bing Web Search.   
2. Zoek vervolgens in het navigatie menu aan de linkerkant naar **controle** en selecteer **Diagnostische instellingen**. Dit scherm bevat alle eerder gemaakte Diagnostische instellingen voor deze bron.
3. Als er een eerder gemaakte resource is die u wilt gebruiken, kunt u deze nu selecteren. Als dat niet het geval is, selecteert u **+ Diagnostische instelling toevoegen**.
4. Voer een naam in voor de instelling. Selecteer vervolgens **archiveren naar een opslag account** en **verzenden naar log Analytics**.
5. Wanneer u wordt gevraagd om te configureren, selecteert u het opslag account en de OMS-werk ruimte die u wilt gebruiken om Diagnostische logboeken op te slaan. **Opmerking**: als u geen opslag account of OMS-werk ruimte hebt, volgt u de aanwijzingen om er een te maken.
6. Selecteer **audit**, **RequestResponse** en **AllMetrics**. Stel vervolgens de Bewaar periode voor uw diagnostische logboek gegevens in. Als een Bewaar beleid is ingesteld op nul, worden gebeurtenissen voor die logboek categorie voor onbepaalde tijd opgeslagen.
7. Klik op **Opslaan**.

Het kan tot twee uur duren voordat logboek gegevens beschikbaar zijn voor het uitvoeren van query's en analyseren. U hoeft dus geen zorgen te maken als u niets meteen ziet.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Diagnostische gegevens uit Azure Storage weer geven en exporteren

Azure Storage is een robuuste oplossing voor object opslag die is geoptimaliseerd voor het opslaan van grote hoeveel heden ongestructureerde gegevens. In deze sectie leert u hoe u uw opslag account kunt doorzoeken voor het totale aantal trans acties gedurende een periode van 30 dagen en hoe u de gegevens naar Excel exporteert.

1. Zoek in de Azure Portal de Azure Storage resource die u in de laatste sectie hebt gemaakt.
2. Ga in het navigatie menu aan de linkerkant naar **controle** en selecteer **metrische gegevens**.
3. Gebruik de beschik bare vervolg keuzelijsten om uw query te configureren. Voor dit voor beeld stellen we het tijds bereik in op de **laatste 30 dagen** en de metrische gegevens voor de **trans actie**.
4. Wanneer de query is voltooid, ziet u een visualisatie van trans acties in de afgelopen 30 dagen. Als u deze gegevens wilt exporteren, gebruikt u de knop **exporteren naar Excel** boven aan de pagina.

Meer informatie over wat u kunt doen met diagnostische gegevens in [Azure Storage](../storage/blobs/storage-blobs-introduction.md).

## <a name="view-logs-in-log-analytics"></a>Logboeken weergeven in Log Analytics

Volg deze instructies voor het verkennen van log Analytics-gegevens voor uw resource.

1. Zoek en selecteer in het Azure Portal **log Analytics** in het navigatie menu aan de linkerkant.
2. Zoek en selecteer de resource die u hebt gemaakt bij het inschakelen van diagnostische gegevens.
3. Zoek en selecteer **Logboeken** onder **Algemeen**. Op deze pagina kunt u query's uitvoeren op uw logboeken.

### <a name="sample-queries"></a>Voorbeeldquery's

Hier volgen enkele eenvoudige Kusto query's die u kunt gebruiken om uw logboek gegevens te verkennen.

Voer deze query uit voor alle Diagnostische logboeken van Azure Cognitive Services gedurende een opgegeven periode:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Voer deze query uit om de 10 meest recente logboeken weer te geven:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Voer deze query uit om bewerkingen te groeperen op **resource**:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Voer deze query uit om de gemiddelde tijd te bepalen die nodig is om een bewerking uit te voeren:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Voer deze query uit om het volume van de bewerkingen gedurende een bepaalde periode te splitsen op basis van de bewerkings naam en aantal binning voor elke 10.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Volgende stappen

* Als u wilt weten hoe u logboek registratie kunt inschakelen en ook de metrische gegevens en logboek categorieën die worden ondersteund door de verschillende Azure-Services, leest u het [overzicht van metrische gegevens](../azure-monitor/data-platform.md) in Microsoft Azure en [overzicht van de artikelen van Azure Diagnostic logs](../azure-monitor/essentials/platform-logs-overview.md) .
* Lees deze artikelen voor meer informatie over Event hubs:
  * [Wat is Azure Event Hubs?](../event-hubs/event-hubs-about.md)
  * [Aan de slag met Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
* Lees [metrische gegevens over het downloaden en Diagnostische logboeken van Azure Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
* Meer [informatie over logboek zoekopdrachten in azure monitor logs](../azure-monitor/logs/log-query-overview.md).