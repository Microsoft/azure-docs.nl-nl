---
title: Azure Cosmos DB bewaken | Microsoft Docs
description: Meer informatie over het bewaken van de prestaties en beschik baarheid van Azure Cosmos DB.
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/01/2020
ms.author: sngun
ms.custom: subject-monitoring
ms.openlocfilehash: f18d1850cb6ccf28ff70f826e3d4bfe74ae05c40
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102178725"
---
# <a name="monitor-azure-cosmos-db"></a>Azure Cosmos DB controleren
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Wanneer u belang rijke toepassingen en bedrijfs processen hebt die afhankelijk zijn van Azure-resources, wilt u deze resources controleren op hun Beschik baarheid, prestaties en werking. In dit artikel worden de bewakings gegevens beschreven die worden gegenereerd door Azure Cosmos-data bases en hoe u de functies van Azure Monitor kunt gebruiken om deze gegevens te analyseren en te waarschuwen.

U kunt uw gegevens bewaken met metrieken aan de client zijde en aan de server zijde. Wanneer metrische gegevens aan de server zijde worden gebruikt, kunt u de in Azure Cosmos DB bewaarde, bewaken met de volgende opties:

* **Bewaken vanaf Azure Cosmos DB portal:** U kunt bewaken met de metrische gegevens die beschikbaar zijn op het tabblad **metrische gegevens** van het Azure Cosmos-account. De metrische gegevens op dit tabblad bevatten de metrische gegevens voor door Voer, opslag, Beschik baarheid, latentie, consistentie en systeem niveau. Standaard hebben deze metrische gegevens een Bewaar periode van 7 dagen. Zie de sectie [bewakings gegevens die zijn verzameld uit Azure Cosmos DB](#monitoring-from-azure-cosmos-db) van dit artikel voor meer informatie.

* **Controleren met metrische gegevens in azure monitor:** U kunt de metrische gegevens van uw Azure Cosmos-account bewaken en dash boards maken op basis van de Azure Monitor. Azure Monitor de metrische gegevens voor de Azure Cosmos DB standaard verzameld, hoeft u niets expliciet te configureren. Deze metrische gegevens worden verzameld met een granulatie van één minuut, de granulatie kan variëren op basis van de metrische gegevens die u kiest. Standaard hebben deze metrische gegevens een Bewaar periode van 30 dagen. De meeste metrische gegevens die beschikbaar zijn in de vorige opties zijn ook beschikbaar in deze metrische gegevens. De dimensie waarden voor de metrische gegevens, zoals container naam, zijn niet hoofdletter gevoelig. U moet dus hoofdletter gevoelige vergelijking gebruiken wanneer u teken reeksen vergelijkt met deze dimensie waarden. Zie de sectie [metrische gegevens analyseren](#analyze-metric-data) in dit artikel voor meer informatie.

* **Controleren met Diagnostische logboeken in azure monitor:** U kunt de logboeken van uw Azure Cosmos-account bewaken en dash boards maken op basis van de Azure Monitor. Telemetrie, zoals gebeurtenissen en traceringen die worden uitgevoerd bij een tweede granulatie, worden opgeslagen als Logboeken. Als bijvoorbeeld de door Voer van een container verandert, worden de eigenschappen van een Cosmos-account gewijzigd, worden deze gebeurtenissen vastgelegd in de logboeken. U kunt deze logboeken analyseren door query's uit te voeren op de verzamelde gegevens. Zie de sectie [logboek gegevens analyseren](#analyze-log-data) in dit artikel voor meer informatie.

* **Bewaakt programmatisch met sdk's:** U kunt uw Azure Cosmos-account programmatisch controleren met behulp van de .NET-, Java-, Python-, Node.js Sdk's en de headers in REST API. Zie de sectie [bewaking Azure Cosmos DB via een programma](#monitor-cosmosdb-programmatically) in dit artikel voor meer informatie.

In de volgende afbeelding ziet u verschillende opties die beschikbaar zijn voor het bewaken van Azure Cosmos DB-account via Azure Portal:

:::image type="content" source="media/monitor-cosmos-db/monitoring-options-portal.png" alt-text="Beschik bare bewakings opties in Azure Portal" border="false":::

Wanneer u Azure Cosmos DB gebruikt, kunt u aan de client zijde de details verzamelen voor aanvraag kosten, activiteits-ID, uitzonde ring/stack tracerings informatie, HTTP-status/substatuscode, diagnostische teken reeks om een probleem op te lossen dat zich kan voordoen. Deze informatie is ook vereist als u contact moet opdoen met het ondersteunings team van Azure Cosmos DB. 

## <a name="monitor-overview"></a>Overzicht van monitor

De **overzichts** pagina in de Azure portal voor elk Azure Cosmos DB account bevat een korte weer gave van het resource gebruik, zoals totaal aantal aanvragen, aanvragen die zijn opgeresulteerd in een specifieke HTTP-status code en facturering per uur. Deze informatie is nuttig, maar slechts een klein aantal bewakings gegevens is beschikbaar in dit deel venster. Een deel van deze gegevens wordt automatisch verzameld en is beschikbaar voor analyse zodra u de resource maakt. U kunt extra typen gegevens verzameling inschakelen met een bepaalde configuratie.

## <a name="what-is-azure-monitor"></a>Wat is Azure Monitor?

Azure Cosmos DB maakt bewakings gegevens met behulp van [Azure monitor](../azure-monitor/overview.md) . Dit is een volledige stack monitoring-service in azure die een volledige set functies biedt om uw Azure-resources te controleren naast bronnen in andere Clouds en on-premises.

Als u nog niet bekend bent met het bewaken van Azure-Services, kunt u beginnen met het artikel [Azure-resources controleren met Azure monitor](../azure-monitor/essentials/monitor-azure-resource.md) waarin de volgende concepten worden beschreven:

* Wat is Azure Monitor?
* Kosten die zijn gekoppeld aan bewaking
* Bewaken van gegevens die zijn verzameld in azure
* Gegevens verzameling configureren
* Standaard Programma's in azure voor het analyseren en waarschuwen van bewakings gegevens

In de volgende secties vindt u een beschrijving van de specifieke gegevens die zijn verzameld uit Azure Cosmos DB en vindt u voor beelden voor het configureren van gegevens verzameling en het analyseren van deze gegevens met Azure-hulpprogram ma's.

## <a name="azure-monitor-for-azure-cosmos-db"></a>Azure Monitor voor Azure Cosmos DB

Azure Monitor voor Azure Cosmos DB is gebaseerd op de [werkmappen-functie van Azure monitor](../azure-monitor/visualize/workbooks-overview.md) en gebruikt dezelfde bewakings gegevens die zijn verzameld voor Azure Cosmos DB beschreven in de volgende secties. Gebruik Azure Monitor voor een overzicht van de algehele prestaties, fouten, capaciteit en operationele status van al uw Azure Cosmos DB resources in een geïntegreerde interactieve ervaring en gebruik de andere functies van Azure Monitor voor gedetailleerde analyse en waarschuwingen. Zie het artikel [Azure monitor verkennen voor Azure Cosmos DB voor](../azure-monitor/insights/cosmosdb-insights-overview.md) meer informatie.

> [!NOTE]
> Wanneer u containers maakt, moet u ervoor zorgen dat u niet twee containers maakt met dezelfde naam, maar met een ander hoofdletter gebruik. Dat komt omdat sommige onderdelen van het Azure-platform niet hoofdletter gevoelig zijn. Dit kan leiden tot Verwar ring/botsing van telemetriegegevens en acties op containers met dergelijke namen.

## <a name="monitoring-data"></a><a id="monitoring-from-azure-cosmos-db"></a> Bewakings gegevens 

Azure Cosmos DB worden dezelfde soorten bewakings gegevens verzameld als andere Azure-resources die worden beschreven in [gegevens van Azure-resources bewaken](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). Zie [Azure Cosmos DB monitoring data Naslag informatie](monitor-cosmos-db-reference.md) voor een gedetailleerde Naslag informatie over de logboeken en metrische gegevens die door Azure Cosmos DB zijn gemaakt.

De **overzichts** pagina in de Azure portal voor elke Azure Cosmos-Data Base bevat een korte weer gave van het database gebruik, inclusief de aanvraag en het facturerings gebruik per uur. Dit is nuttige informatie, maar slechts een kleine hoeveelheid beschik bare bewakings gegevens. Sommige van deze gegevens worden automatisch verzameld en beschikbaar voor analyse zodra u de data base maakt terwijl u extra gegevens verzameling kunt inschakelen met een bepaalde configuratie.

:::image type="content" source="media/monitor-cosmos-db/overview-page.png" alt-text="Overzichtspagina":::

## <a name="collection-and-routing"></a>Verzameling en route ring

De metrische gegevens van het platform en het activiteiten logboek worden automatisch verzameld en opgeslagen, maar kunnen worden doorgestuurd naar andere locaties met behulp van een diagnostische instelling.  

Bron logboeken worden pas verzameld en opgeslagen als u een diagnostische instelling hebt gemaakt en deze naar een of meer locaties wilt door sturen.

Zie [Diagnostische instelling maken voor het verzamelen van platform logboeken en metrische gegevens in azure](cosmosdb-monitor-resource-logs.md) voor het gedetailleerde proces voor het maken van een diagnostische instelling met behulp van de Azure Portal en een aantal voor beelden van diagnostische query's. Wanneer u een diagnostische instelling maakt, geeft u op welke categorieën logboeken u wilt verzamelen.

De metrische gegevens en logboeken die u kunt verzamelen, worden besproken in de volgende secties.

## <a name="analyzing-metrics"></a><a id="analyze-metric-data"></a> Metrische gegevens analyseren

Azure Cosmos DB biedt een aangepaste ervaring voor het werken met metrische gegevens. U kunt metrische gegevens voor Azure Cosmos DB met metrische gegevens uit andere Azure-Services analyseren door **metrische gegevens** te openen in het menu **Azure monitor** . Zie [aan de slag met Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md) voor meer informatie over het gebruik van dit hulp programma. U kunt ook afhandelen hoe u de latentie aan de [server zijde](monitor-server-side-latency.md), het [gebruik van aanvraag eenheden](monitor-request-unit-usage.md)en het [gebruik van genormaliseerde aanvraag eenheden](monitor-normalized-request-units.md) voor uw Azure Cosmos DB-resources bewaken.

Zie voor een overzicht van de platform gegevens die voor Azure Cosmos DB worden verzameld, het artikel [bewaking Azure Cosmos DB Data Reference Metrics](monitor-cosmos-db-reference.md#metrics) .

Alle metrische gegevens voor Azure Cosmos DB bevinden zich in de naam ruimte **Cosmos DB standaard metrische gegevens**. U kunt de volgende dimensies met deze metrische gegevens gebruiken bij het toevoegen van een filter aan een grafiek:

* NaamVerzameling
* DatabaseName
* OperationType
* Region
* Status code

Ter referentie ziet u een lijst met [alle metrische resource gegevens die worden ondersteund in azure monitor](../azure-monitor/essentials/metrics-supported.md).

### <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Metrische gegevens van het bewerkings niveau voor Azure Cosmos DB weer geven

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Selecteer **monitor** in de navigatie balk aan de linkerkant en selecteer **metrische gegevens**.

   :::image type="content" source="./media/monitor-cosmos-db/monitor-metrics-blade.png" alt-text="Deel venster metrische gegevens in Azure Monitor":::

1. Selecteer in het deel venster **metrieken** > **een resource selecteren** > Kies het vereiste **abonnement** en de **resource groep**. Voor het **bron type** selecteert u **Azure Cosmos DB accounts**, kiest u een van uw bestaande Azure Cosmos-accounts en selecteert u **Toep assen**.

   :::image type="content" source="./media/monitor-cosmos-db/select-cosmosdb-account.png" alt-text="Kies een Cosmos DB account om de metrische gegevens weer te geven":::

1. Vervolgens kunt u een metriek selecteren in de lijst met beschik bare metrische gegevens. U kunt metrische gegevens selecteren die specifiek zijn voor aanvraag eenheden, opslag, latentie, Beschik baarheid, Cassandra en andere. Zie het artikel [metrische gegevens per categorie](monitor-cosmos-db-reference.md) voor meer informatie over alle beschik bare metrische gegevens in deze lijst. In dit voor beeld selecteren we **aanvraag eenheden** en **Gem** als de aggregatie waarde.

   Naast deze details kunt u ook het **tijds bereik** en de **tijd granulatie** van de metrische gegevens selecteren. U kunt Maxi maal de metrische gegevens weer geven voor de afgelopen 30 dagen.  Nadat u het filter hebt toegepast, wordt een grafiek weer gegeven op basis van het filter. U kunt het gemiddelde aantal verbruikte aanvraag eenheden per minuut voor de geselecteerde periode bekijken.  

   :::image type="content" source="./media/monitor-cosmos-db/metric-types.png" alt-text="Kies een waarde in het Azure Portal":::

### <a name="add-filters-to-metrics"></a>Filters toevoegen aan metrische gegevens

U kunt ook de metrische gegevens en de grafiek die worden weer gegeven met een specifieke **verzamelingnaam**, **DATABASENAME**, **OperationType**, **Region** en **status** code filteren. Als u de metrische gegevens wilt filteren, selecteert u **filter toevoegen** en kiest u de vereiste eigenschap, zoals **OperationType** , en selecteert u een waarde zoals **query**. In de grafiek worden vervolgens de verbruikte aanvraag eenheden voor de query bewerking voor de geselecteerde periode weer gegeven. De bewerkingen die zijn uitgevoerd via een opgeslagen procedure, worden niet geregistreerd, zodat ze niet beschikbaar zijn onder de metrische waarde voor OperationType.

:::image type="content" source="./media/monitor-cosmos-db/add-metrics-filter.png" alt-text="Een filter toevoegen om de metrische granulatie te selecteren":::

U kunt metrische gegevens groeperen met behulp van de optie **splitsing Toep assen** . U kunt bijvoorbeeld de aanvraag eenheden per bewerkings type groeperen en de grafiek voor alle bewerkingen tegelijk bekijken, zoals wordt weer gegeven in de volgende afbeelding:

:::image type="content" source="./media/monitor-cosmos-db/apply-metrics-splitting.png" alt-text="Splitsings filter Toep assen toevoegen":::

## <a name="analyzing-logs"></a><a id="analyze-log-data"></a> Logboeken analyseren

Gegevens in Azure Monitor logboeken worden opgeslagen in tabellen waarin elke tabel een eigen set unieke eigenschappen heeft.

Alle resource Logboeken in Azure Monitor hebben dezelfde velden die worden gevolgd door servicespecifieke velden. Het algemene schema wordt beschreven in [Azure monitor resource-logboek schema](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema). Zie [Azure Cosmos DB gegevens referentie bewaken](monitor-cosmos-db-reference.md#resource-logs)voor een lijst met de typen bron logboeken die zijn verzameld voor Azure Cosmos db.

Het [activiteiten logboek](../azure-monitor/essentials/activity-log.md) is een Azure-aanmelding voor een platform dat inzicht biedt in gebeurtenissen op abonnements niveau. U kunt deze onafhankelijk bekijken of door sturen naar Azure Monitor-logboeken, waar u veel complexere query's kunt uitvoeren met behulp van Log Analytics.  

Azure Cosmos DB slaat gegevens op in de volgende tabellen.

| Tabel | Beschrijving |
|:---|:---|
| AzureDiagnostics | Algemene tabel die door meerdere services wordt gebruikt voor het opslaan van resource Logboeken. Bron logboeken van Azure Cosmos DB kunnen worden geïdentificeerd met `MICROSOFT.DOCUMENTDB` .   |
| AzureActivity    | Algemene tabel waarin alle records uit het activiteiten logboek worden opgeslagen.

### <a name="sample-kusto-queries"></a>Voor beeld van Kusto-query's

> [!IMPORTANT]
> Wanneer u **Logboeken** in het menu Azure Cosmos DB selecteert, wordt log Analytics geopend met het query bereik ingesteld op het huidige Azure Cosmos DB-account. Dit betekent dat logboek query's alleen gegevens van die bron bevatten. Als u een query wilt uitvoeren die gegevens uit andere accounts of gegevens uit andere Azure-Services bevat, selecteert u **Logboeken** in het **Azure monitor** menu. Zie de [logboek query bereik en het tijds bereik in Azure Monitor Log Analytics](../azure-monitor/logs/scope.md) voor meer informatie.

Hier volgen enkele query's die u kunt invoeren in de zoek balk Zoeken naar **Logboeken** om uw Azure Cosmos-resources te bewaken. Deze query's werken met de [nieuwe taal](../azure-monitor/logs/log-query-overview.md).

* Een query uitvoeren voor alle Diagnostische logboeken van Azure Cosmos DB gedurende een opgegeven tijds periode:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests"

    ```

* Om te zoeken naar alle bewerkingen, gegroepeerd op resource:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* Om te zoeken naar alle gebruikers activiteit, gegroepeerd op resource:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```

## <a name="alerts"></a>Waarschuwingen

Azure Monitor waarschuwingen geven u proactief op de hoogte wanneer er belang rijke voor waarden worden gevonden in uw bewakings gegevens. Hiermee kunt u problemen in uw systeem identificeren en oplossen voordat uw klanten ze opmerken. U kunt waarschuwingen instellen voor [metrische gegevens](../azure-monitor/alerts/alerts-metric-overview.md), [Logboeken](../azure-monitor/alerts/alerts-unified-log.md)en het [activiteiten logboek](../azure-monitor/alerts/activity-log-alerts.md). Verschillende soorten waarschuwingen hebben voor delen en nadelen

De volgende tabel bevat bijvoorbeeld enkele waarschuwings regels voor uw resources. U kunt een gedetailleerde lijst met waarschuwings regels van de Azure Portal vinden. Zie [het artikel waarschuwingen configureren](create-alerts.md) voor meer informatie.  

| Waarschuwingstype | Voorwaarde | Beschrijving  |
|:---|:---|:---|
|Frequentie limiet voor aanvraag eenheden (metrische waarschuwing) |Dimensie naam: status code, operator: is gelijk aan, dimensie waarden: 429  | Waarschuwingen als de container of data base de ingerichte doorvoer limiet heeft overschreden. |
|Er is een failover uitgevoerd voor de regio |Operator: groter dan, aggregatie type: aantal, drempel waarde: 1 | Als er een failover wordt uitgevoerd voor een enkele regio. Deze waarschuwing is handig als u automatische failover niet hebt ingeschakeld. |
| Toetsen draaien (waarschuwing voor activiteiten logboek)| Gebeurtenis niveau: informatief, status: gestart| Waarschuwingen wanneer de account sleutels worden gedraaid. U kunt uw toepassing bijwerken met de nieuwe sleutels. |

## <a name="monitor-azure-cosmos-db-programmatically"></a><a id="monitor-cosmosdb-programmatically"></a> Azure Cosmos DB programmatisch controleren

De metrische gegevens op account niveau die beschikbaar zijn in de portal, zoals het gebruik van account opslag en het totaal aantal aanvragen, zijn niet beschikbaar via de SQL-Api's. U kunt echter gebruiks gegevens ophalen op het niveau van de verzameling met behulp van de SQL-Api's. Ga als volgt te werk om gegevens van het verzamelings niveau op te halen:

* Als u de REST API wilt gebruiken, moet u [een Get-bewerking uitvoeren op de verzameling](/rest/api/cosmos-db/get-a-collection). De quota-en gebruiks gegevens voor de verzameling worden geretourneerd in de x-MS-resource-quota-en x-MS-resource-usage-headers in het antwoord.

* Als u de .NET SDK wilt gebruiken, gebruikt u de methode [DocumentClient. ReadDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync) , die een [ResourceResponse](/dotnet/api/microsoft.azure.documents.client.resourceresponse-1) retourneert dat een aantal gebruiks eigenschappen bevat, zoals **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage** en meer.

Gebruik de [SDK van Azure monitor](https://www.nuget.org/packages/Microsoft.Azure.Insights)om toegang te krijgen tot extra metrische gegevens. Beschik bare metrische definities kunnen worden opgehaald door aan te roepen:

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01
```

Als u afzonderlijke metrische gegevens wilt ophalen, gebruikt u de volgende indeling:

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metrics?timespan={StartTime}/{EndTime}&interval={AggregationInterval}&metricnames={MetricName}&aggregation={AggregationType}&`$filter={Filter}&api-version=2018-01-01
```

Zie het artikel [Azure monitoring rest API](../azure-monitor/essentials/rest-api-walkthrough.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure Cosmos DB Naslag informatie over bewakings gegevens](monitor-cosmos-db-reference.md) voor een verwijzing naar de logboeken en metrieken die zijn gemaakt door Azure Cosmos db.
* Zie [Azure-resources bewaken met Azure monitor](../azure-monitor/essentials/monitor-azure-resource.md) voor meer informatie over het bewaken van Azure-resources.
