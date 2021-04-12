---
title: Logboek gegevens verzamelen
titleSuffix: Azure Cognitive Search
description: Verzamelen en analyseren van logboek gegevens door een diagnostische instelling in te scha kelen en vervolgens de Kusto-query taal te gebruiken om de resultaten te verkennen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: f0d85f056cfaaa58fcc72eb9c2182b3e1a78affb
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581620"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Logboek gegevens voor Azure Cognitive Search verzamelen en analyseren

Diagnostische of operationele logboeken bieden inzicht in de gedetailleerde bewerkingen van Azure Cognitive Search en zijn nuttig voor het bewaken van de service status en-processen. Intern behoudt micro soft de systeem gegevens op de back-end korte tijd (ongeveer 30 dagen), voldoende voor onderzoek en analyse als u een ondersteunings ticket bijwerkt. Als u echter eigendom wilt zijn van operationele gegevens, moet u een diagnostische instelling configureren om op te geven waar logboek gegevens worden verzameld.

Diagnostische logboek registratie wordt ingeschakeld via integratie met [Azure monitor](../azure-monitor/index.yml). 

Wanneer u diagnostische logboek registratie instelt, wordt u gevraagd een opslag mechanisme op te geven. In de volgende tabel worden de opties voor het verzamelen en persistent maken van gegevens opgesomd.

| Resource | Gebruikt voor |
|----------|----------|
| [Verzenden naar Log Analytics-werkruimte](../azure-monitor/essentials/tutorial-resource-logs.md) | Gebeurtenissen en metrische gegevens worden verzonden naar een Log Analytics-werk ruimte, die in de portal kan worden opgevraagd om gedetailleerde informatie te retour neren. Zie [aan de slag met Azure monitor-logboeken](../azure-monitor/logs/log-analytics-tutorial.md) voor een inleiding |
| [Archiveren met Blob Storage](../storage/blobs/storage-blobs-overview.md) | Gebeurtenissen en metrische gegevens worden gearchiveerd naar een BLOB-container en opgeslagen in JSON-bestanden. Logboeken kunnen behoorlijk nauw keurig zijn (per uur per minuut), wat nuttig is voor het opnieuw doorzoeken van een specifiek incident, maar niet voor open-end onderzoek. Gebruik een JSON-editor om een onbewerkte logboek bestand te bekijken of Power BI om logboek gegevens samen te voegen en te visualiseren.|
| [Streamen naar Event hub](../event-hubs/index.yml) | Gebeurtenissen en metrische gegevens worden gestreamd naar een Azure Event Hubs-service. Kies deze optie als een alternatieve service voor het verzamelen van gegevens voor zeer grote logboeken. |

## <a name="prerequisites"></a>Vereisten

Maak vooraf een resource zodat u een of meer resources kunt selecteren bij het configureren van diagnostische logboek registratie.

+ [Een log Analytics-werk ruimte maken](../azure-monitor/logs/quick-create-workspace.md)

+ [Een opslagaccount maken](../storage/common/storage-account-create.md)

+ [Een Event Hub maken](../event-hubs/event-hubs-create.md)

## <a name="enable-data-collection"></a>Gegevensverzameling inschakelen

Diagnostische instellingen bepalen hoe vastgelegde gebeurtenissen en metrische gegevens worden verzameld.

1. Selecteer **Diagnostische instellingen** onder **Controle**.

   ![Diagnostische instellingen](./media/search-monitor-usage/diagnostic-settings.png "Diagnostische instellingen")

1. Selecteer **+ Diagnostische instelling toevoegen**

1. Controleer **log Analytics**, selecteer uw werk ruimte en selecteer **OperationLogs** en **AllMetrics**.

   ![Gegevensverzameling configureren](./media/search-monitor-usage/configure-storage.png "Gegevensverzameling configureren")

1. Sla de instelling op.

1. Nadat logboek registratie is ingeschakeld, gebruikt u uw zoek service om logboeken en metrische gegevens te genereren. Het duurt enige tijd voordat geregistreerde gebeurtenissen en metrische gegevens beschikbaar komen.

Voor Log Analytics kan het enkele minuten duren voordat er gegevens beschikbaar zijn, waarna u Kusto-query's kunt uitvoeren om gegevens te retour neren. Zie [query aanvragen bewaken](search-monitor-logs.md)voor meer informatie.

Voor Blob-opslag duurt het één uur voordat de containers worden weer gegeven in de Blob-opslag. Er is één blob, per uur, per container. Containers worden alleen gemaakt als er een activiteit is die moet worden geregistreerd of gemeten. Wanneer de gegevens naar een opslag account worden gekopieerd, worden de gegevens ingedeeld als JSON en in twee containers geplaatst:

+ Insights-logboeken-operationlogs: voor logboeken met Zoek opdrachten
+ inzichten-metrische gegevens-pt1m: voor metrische gegevens

## <a name="query-log-information"></a>Informatie over het query logboek

Twee tabellen bevatten logboeken en metrische gegevens voor Azure Cognitive Search: **AzureDiagnostics** en **AzureMetrics**.

1. Onder **bewaking** selecteert u **Logboeken**.

1. Voer **AzureMetrics** in het query venster in. Voer deze eenvoudige query uit om meer te weten te komen over de gegevens die in deze tabel worden verzameld. Schuif over de tabel om de metrische gegevens en waarden weer te geven. U ziet het aantal records bovenaan en als uw service metrische gegevens heeft verzameld, kunt u het tijds interval aanpassen om een beheer bare gegevensset te verkrijgen.

   ![AzureMetrics-tabel](./media/search-monitor-usage/azuremetrics-table.png "AzureMetrics-tabel")

1. Voer de volgende query in om een resultatenset in tabel vorm te retour neren.

   ```kusto
   AzureMetrics
   | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. Herhaal de vorige stappen, beginnend met **AzureDiagnostics** om alle kolommen voor informatieve doel einden te retour neren, gevolgd door een meer selectieve query waarmee interessante informatie wordt geëxtraheerd.

   ```kusto
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![AzureDiagnostics-tabel](./media/search-monitor-usage/azurediagnostics-table.png "AzureDiagnostics-tabel")

## <a name="kusto-query-examples"></a>Voor beelden van Kusto-query

Als u diagnostische logboek registratie hebt ingeschakeld, kunt u een query uitvoeren op **AzureDiagnostics** voor een lijst met bewerkingen die zijn uitgevoerd op uw service en wanneer. U kunt activiteiten ook correleren om de prestatie wijzigingen te onderzoeken.

#### <a name="example-list-operations"></a>Voor beeld: bewerkingen weer geven 

Retourneert een lijst met bewerkingen en een telling van elke bewerking.

```kusto
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>Voor beeld: correlatie bewerkingen

Correleer een query aanvraag met indexerings bewerkingen en Genereer de gegevens punten in een tijd diagram om de bewerkingen samen te zien.

```kusto
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

## <a name="logged-operations"></a>Geregistreerde bewerkingen

Geregistreerde gebeurtenissen die door Azure Monitor zijn vastgelegd, zijn onder andere die zijn gerelateerd aan indexering en query's. De **AzureDiagnostics** -tabel in log Analytics verzamelt operationele gegevens met betrekking tot query's en indexering.

| OperationName | Description |
|---------------|-------------|
| ServiceStats | Met deze bewerking wordt een routine aanroep uitgevoerd om [service statistieken](/rest/api/searchservice/get-service-statistics)op te halen, direct of impliciet te worden aangeroepen om een portal overzichts pagina te vullen wanneer deze wordt geladen of vernieuwd. |
| Query. Search |  Query's aanvragen voor een index Zie [controle query's](search-monitor-queries.md) voor informatie over vastgelegde query's.|
| Indexeren. index  | Deze bewerking is een aanroep om [documenten toe te voegen, bij te werken of te verwijderen](/rest/api/searchservice/addupdate-or-delete-documents). |
| indexen. Drukvat | Dit is een index die is gemaakt met de wizard gegevens importeren. |
| Indexeer functies. maken | Maak expliciet of impliciet een Indexeer functie met behulp van de wizard gegevens importeren. |
| Indexeer functies. ophalen | Retourneert de naam van een Indexeer functie wanneer de Indexeer functie wordt uitgevoerd. |
| Indexeer functies. status | Retourneert de status van een Indexeer functie wanneer de Indexeer functie wordt uitgevoerd. |
| Gegevens bronnen. ophalen | Retourneert de naam van de gegevens bron wanneer een Indexeer functie wordt uitgevoerd.|
| Indexes. Get | Retourneert de naam van een index wanneer een Indexeer functie wordt uitgevoerd. |

## <a name="log-schema"></a>Logboek schema

Als u aangepaste rapporten bouwt, voldoen de gegevens structuren die Azure Cognitive Search logboek gegevens bevatten, aan het onderstaande schema. Voor Blob Storage bevat elke BLOB één hoofd object met de naam **records** die een matrix met logboek objecten bevatten. Elke BLOB bevat records voor alle bewerkingen die plaatsvonden tijdens hetzelfde uur.

De volgende tabel bevat een gedeeltelijke lijst met velden die worden gebruikt voor het registreren van bronnen.

| Naam | Type | Voorbeeld | Opmerkingen |
| --- | --- | --- | --- |
| timeGenerated |datum/tijd |"2018-12-07T00:00:43.6872559 Z" |Tijds tempel van de bewerking |
| resourceId |tekenreeks |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/STANDAARD/PROVIDERS/<br/> Micro soft. SEARCH/SEARCHSERVICES/SEARCHSERVICE " |Uw ResourceId |
| operationName |tekenreeks |Query. Search |De naam van de bewerking |
| operationVersion |tekenreeks |"2020-06-30" |De API-versie die wordt gebruikt |
| category |tekenreeks |"OperationLogs" |bedrag |
| resultType |tekenreeks |Geleverd |Mogelijke waarden: geslaagd of mislukt |
| resultSignature |int |200 |HTTP-resultaat code |
| durationMS |int |50 |De duur van de bewerking in milliseconden |
| properties |object |Raadpleeg de volgende tabel |Object met toepassingsspecifieke gegevens |

### <a name="properties-schema"></a>Eigenschappen schema

De onderstaande eigenschappen zijn specifiek voor Azure Cognitive Search.

| Naam | Type | Voorbeeld | Opmerkingen |
| --- | --- | --- | --- |
| Description_s |tekenreeks |' GET/Indexes (' content ')/docs ' |Het eind punt van de bewerking |
| Documents_d |int |42 |Aantal verwerkte documenten |
| IndexName_s |tekenreeks |"test-index" |De naam van de index die is gekoppeld aan de bewerking |
| Query_s |tekenreeks |"? Search = AzureSearch&$count = True&API-Version = 2020-06-30" |De query parameters |

## <a name="metrics-schema"></a>Schema voor metrische gegevens

Metrische gegevens worden vastgelegd voor query aanvragen en gemeten in een interval van één minuut. Bij elke metriek worden minimum-, maximum-en gemiddelde waarden per minuut weer gegeven. Zie [query aanvragen bewaken](search-monitor-queries.md)voor meer informatie.

| Naam | Type | Voorbeeld | Opmerkingen |
| --- | --- | --- | --- |
| resourceId |tekenreeks |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/STANDAARD/PROVIDERS/<br/>Micro soft. SEARCH/SEARCHSERVICES/SEARCHSERVICE " |de resource-ID |
| metricName |tekenreeks |Periode |de naam van de metriek |
| tijd |datum/tijd |"2018-12-07T00:00:43.6872559 Z" |de tijds tempel van de bewerking |
| gemiddeld |int |64 |De gemiddelde waarde van de onbewerkte voor beelden in het tijds interval voor metrische gegevens, eenheden in seconden of percentage, afhankelijk van de metriek. |
| minimum |int |37 |De minimale waarde van de onbewerkte voor beelden in het tijds interval voor metrische gegevens, eenheden in seconden. |
| maximum |int |78 |De maximum waarde van de onbewerkte voor beelden in de metrische tijds interval, eenheden in seconden.  |
| totaal |int |258 |De totale waarde van de onbewerkte voor beelden in het tijds interval voor metrische gegevens, eenheden in seconden.  |
| count |int |4 |Het aantal metrische gegevens dat vanaf een knoop punt wordt verzonden naar het logboek binnen het interval van één minuut.  |
| timegrain |tekenreeks |"PT1M" |De tijd korrel van de metriek in ISO 8601. |

Het is gebruikelijk om query's uit te voeren in milliseconden, zodat alleen query's die als seconden worden gemeten, worden weer gegeven in metriek zoals QPS.

Voor de metrische **Zoek Query's per seconde** is minimum de laagste waarde voor zoek query's per seconde die tijdens die minuut zijn geregistreerd. Hetzelfde geldt voor de maximum waarde. Gemiddelde, is de aggregatie over de hele minuut. U kunt bijvoorbeeld binnen één minuut een patroon hebben dat er ongeveer als volgt uitziet: één seconde van een hoge belasting die het maximum voor SearchQueriesPerSecond is, gevolgd door 58 seconden gemiddelde belasting, en ten slotte één seconde met slechts één query, wat het minimum is.

Voor het **beperkende Zoek Query's percentage**, minimum, maximum, gemiddelde en totaal heeft dezelfde waarde: het percentage Zoek query's dat is beperkt tot het totale aantal Zoek query's gedurende één minuut.

## <a name="view-raw-log-files"></a>Onbewerkte logboek bestanden weer geven

Blob-opslag wordt gebruikt voor het archiveren van logboek bestanden. U kunt een JSON-editor gebruiken om het logboek bestand weer te geven. Als u er geen hebt, raden we u aan [Visual Studio code](https://code.visualstudio.com/download)aan te bevelen.

1. Open uw opslag account in Azure Portal. 

2. Klik in het navigatie deel venster aan de linkerkant op **blobs**. U ziet **inzichten-logs-operationlogs** en **Insights-metrische gegevens-pt1m**. Deze containers worden gemaakt door Azure Cognitive Search wanneer de logboek gegevens worden geëxporteerd naar de Blob-opslag.

3. Klik op omlaag in de maphiërarchie totdat het JSON-bestand is bereikt.  Gebruik het context menu om het bestand te downloaden.

Zodra het bestand is gedownload, opent u het in een JSON-editor om de inhoud weer te geven.

## <a name="next-steps"></a>Volgende stappen

Als u dit nog niet hebt gedaan, raadpleegt u de grond beginselen van de controle van de zoek service voor meer informatie over het volledige aanbod van de mogelijkheden voor toezicht.

> [!div class="nextstepaction"]
> [Bewerkingen en activiteiten bewaken in azure Cognitive Search](search-monitor-usage.md)