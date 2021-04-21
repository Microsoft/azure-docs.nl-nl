---
title: Diagnostische logboeken
titleSuffix: Azure Cognitive Services
description: Deze handleiding bevat stapsgewijs instructies voor het inschakelen van diagnostische logboekregistratie voor een Azure Cognitive Service. Deze logboeken bieden uitgebreide, regelmatige gegevens over de werking van een resource die worden gebruikt voor het identificeren en oplossen van problemen.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: 4a78e233a41bf3b6682f52bac912528d6bcab76c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816329"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Diagnostische logboekregistratie inschakelen voor Azure Cognitive Services

Deze handleiding bevat stapsgewijs instructies voor het inschakelen van diagnostische logboekregistratie voor een Azure Cognitive Service. Deze logboeken bieden uitgebreide, regelmatige gegevens over de werking van een resource die worden gebruikt voor het identificeren en oplossen van problemen. Voordat u doorgaat, moet u een Azure-account hebben met een abonnement op ten minste één Cognitive Service, zoals [Bing Web Search,](./bing-web-search/overview.md) [Speech Services](./speech-service/overview.md)of [LUIS.](./luis/what-is-luis.md)

## <a name="prerequisites"></a>Vereisten

Als u diagnostische logboekregistratie wilt inschakelen, hebt u een plaats nodig om uw logboekgegevens op te slaan. In deze zelfstudie wordt Azure Storage en Log Analytics gebruikt.

* [Azure Storage:](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) bewaart diagnostische logboeken voor beleidscontrole, statische analyse of back-up. Het opslagaccount hoeft zich niet in hetzelfde abonnement te houden als de resource die logboeken uit de logboeken opzegt, zolang de gebruiker die de instelling configureert de juiste Azure RBAC-toegang heeft tot beide abonnementen.
* [Log Analytics:](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) een flexibel hulpprogramma voor zoeken en analyseren van logboeken waarmee onbewerkte logboeken kunnen worden geanalyseerd die zijn gegenereerd door een Azure-resource.

> [!NOTE]
> Er zijn aanvullende configuratieopties beschikbaar. Zie Logboekgegevens van [uw Azure-resources verzamelen en gebruiken voor meer informatie.](../azure-monitor/essentials/platform-logs-overview.md)

## <a name="enable-diagnostic-log-collection"></a>Verzamelen van diagnostisch logboeken inschakelen  

Laten we eerst diagnostische logboekregistratie inschakelen met behulp van de Azure Portal.

> [!NOTE]
> Als u deze functie wilt inschakelen met behulp van PowerShell of de Azure CLI, gebruikt u de instructies in Logboekgegevens van uw [Azure-resources](../azure-monitor/essentials/platform-logs-overview.md)verzamelen en gebruiken.

1. Navigeer naar de Azure Portal. Zoek en selecteer vervolgens een Cognitive Services resource. Bijvoorbeeld uw abonnement op Bing Web Search.   
2. Ga vervolgens in het navigatiemenu aan de linkerkant naar **Bewaking** en selecteer **Diagnostische instellingen.** Dit scherm bevat alle eerder gemaakte diagnostische instellingen voor deze resource.
3. Als er een eerder gemaakte resource is die u wilt gebruiken, kunt u deze nu selecteren. Selecteer anders **+ Diagnostische instelling toevoegen.**
4. Voer een naam in voor de instelling. Selecteer vervolgens **Archiveren naar een opslagaccount en** **Verzenden naar log Analytics.**
5. Wanneer u wordt gevraagd om deze te configureren, selecteert u het opslagaccount en de OMS-werkruimte die u wilt gebruiken voor het opslaan van diagnostische logboeken. **Opmerking:** als u geen opslagaccount of OMS-werkruimte hebt, volgt u de aanwijzingen om er een te maken.
6. Selecteer **Controleren,** **RequestResponse** en **AllMetrics.** Stel vervolgens de bewaarperiode in voor uw diagnostische logboekgegevens. Als een bewaarbeleid is ingesteld op nul, worden gebeurtenissen voor die logboekcategorie voor onbepaalde tijd opgeslagen.
7. Klik op **Opslaan**.

Het kan tot twee uur duren voordat logboekgegevens beschikbaar zijn om te worden opgevraagd en geanalyseerd. U hoeft zich dus geen zorgen te maken als u niet meteen iets ziet.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Diagnostische gegevens weergeven en exporteren vanuit Azure Storage

Azure Storage is een robuuste oplossing voor objectopslag die is geoptimaliseerd voor het opslaan van grote hoeveelheden ongestructureerde gegevens. In deze sectie leert u hoe u uw opslagaccount kunt opvragen voor het totale aantal transacties gedurende een tijdsbestek van 30 dagen en hoe u de gegevens naar Excel exporteert.

1. Zoek in Azure Portal de resource Azure Storage die u in de laatste sectie hebt gemaakt.
2. Ga in het navigatiemenu aan de linkerkant naar **Bewaking** en selecteer **Metrische gegevens.**
3. Gebruik de beschikbare vervolgkeuzen om uw query te configureren. In dit voorbeeld stellen we het tijdsbereik in op **Afgelopen 30 dagen** en de metrische gegevens op **Transactie**.
4. Wanneer de query is voltooid, ziet u een visualisatie van de transactie in de afgelopen 30 dagen. Als u deze gegevens wilt exporteren, gebruikt **u de** knop Exporteren naar Excel boven aan de pagina.

Meer informatie over wat u kunt doen met diagnostische gegevens in [Azure Storage](../storage/blobs/storage-blobs-introduction.md).

## <a name="view-logs-in-log-analytics"></a>Logboeken weergeven in Log Analytics

Volg deze instructies om log analytics-gegevens voor uw resource te verkennen.

1. Zoek en Azure Portal **Log Analytics** in het navigatiemenu aan de linkerkant.
2. Zoek en selecteer de resource die u hebt gemaakt bij het inschakelen van diagnostische gegevens.
3. Zoek **en** selecteer logboeken onder **Algemeen.** Op deze pagina kunt u query's uitvoeren op uw logboeken.

### <a name="sample-queries"></a>Voorbeeldquery's

Hier vindt u enkele eenvoudige Kusto-query's die u kunt gebruiken om uw logboekgegevens te verkennen.

Voer deze query uit voor alle diagnostische logboeken Azure Cognitive Services een opgegeven periode:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Voer deze query uit om de tien meest recente logboeken te bekijken:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Voer deze query uit om bewerkingen te groepen op **resource**:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Voer deze query uit om de gemiddelde tijd te vinden die nodig is om een bewerking uit te voeren:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Voer deze query uit om het volume aan bewerkingen in de loop van de tijd te bekijken, gesplitst door OperationName, met tellingen die voor elke tien zijn binned.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Volgende stappen

* Lees de artikelen Overzicht van metrische gegevens [in](../azure-monitor/data-platform.md) Microsoft Azure en Overzicht van [diagnostische logboeken](../azure-monitor/essentials/platform-logs-overview.md) van Azure voor meer informatie over het inschakelen van logboekregistratie en de metrische gegevens en logboekcategorieën die worden ondersteund door de verschillende Azure-services.
* Lees deze artikelen voor meer informatie over Event Hubs:
  * [Wat is Azure Event Hubs?](../event-hubs/event-hubs-about.md)
  * [Aan de slag met Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
* Lees [Download metrics and diagnostic logs from Azure Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
* Lees [Inzicht in zoekopdrachten in logboeken Azure Monitor logboeken.](../azure-monitor/logs/log-query-overview.md)