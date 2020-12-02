---
title: Azure IoT Hub controleren
description: Begin hier voor meer informatie over het bewaken van Azure IoT Hub
author: robinsh
ms.author: robinsh
ms.topic: conceptual
ms.service: iot-hub
ms.date: 11/06/2020
ms.openlocfilehash: 2fabf75f74ccf57f57e7b47720ceda8885420842
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460903"
---
# <a name="monitoring-azure-iot-hub"></a>Azure IoT Hub controleren

Wanneer u belang rijke toepassingen en bedrijfs processen hebt die afhankelijk zijn van Azure-resources, wilt u deze resources controleren op hun Beschik baarheid, prestaties en werking. In dit artikel worden de bewakings gegevens beschreven die worden gegenereerd door Azure IoT Hub en wordt uitgelegd hoe u de functies van Azure Monitor kunt gebruiken om deze gegevens te analyseren en te waarschuwen.

## <a name="monitor-overview"></a>Overzicht van monitor

De pagina **overzicht** in de Azure portal voor elke IOT-hub bevat grafieken die bepaalde metrische gegevens over het gebruik bieden, zoals het aantal gebruikte berichten en het aantal apparaten dat is verbonden met de IOT-hub.

:::image type="content" source="media/monitor-iot-hub/overview-portal.png" alt-text="Standaard metrische grafieken op de pagina overzicht van IoT hub.":::

Houd er rekening mee dat de waarde voor aantal berichten kan worden vertraagd met 1 minuut en dat, om redenen die met de IoT Hub service-infra structuur te maken, de waarde soms tussen hogere en lagere waarden kan worden gestuiterd bij het vernieuwen. Dit prestatie meter item mag alleen onjuist zijn voor de waarden die de laatste minuut hebben gegroeid.

De informatie die wordt weer gegeven in het deel venster Overzicht is handig, maar bevat slechts een kleine hoeveelheid beschik bare bewakings gegevens voor een IoT-hub. Sommige bewakings gegevens worden automatisch verzameld en beschikbaar voor analyse zodra u uw IoT-hub maakt. U kunt extra typen gegevens verzameling inschakelen met een bepaalde configuratie.

## <a name="what-is-azure-monitor"></a>Wat is Azure Monitor?

Azure IoT Hub maakt bewakings gegevens met behulp van [Azure monitor](../azure-monitor/overview.md). Dit is een volledige stack monitoring-service in azure die een volledige set functies biedt om uw Azure-resources te controleren naast bronnen in andere Clouds en on-premises.

Begin met het artikel [bewaking van Azure-resources met Azure monitor](../azure-monitor/insights/monitor-azure-resource.md), waarin de volgende concepten worden beschreven:

- Wat is Azure Monitor?
- Kosten die zijn gekoppeld aan bewaking
- Bewaken van gegevens die zijn verzameld in azure
- Gegevens verzameling configureren
- Standaard Programma's in azure voor het analyseren en waarschuwen van bewakings gegevens

In de volgende secties vindt u een beschrijving van de specifieke gegevens die zijn verzameld voor Azure IoT Hub en voor beelden voor het configureren van gegevens verzameling en het analyseren van deze gegevens met Azure-hulpprogram ma's.

## <a name="monitoring-data"></a>Bewakingsgegevens

In azure IoT Hub worden dezelfde soorten bewakings gegevens verzameld als andere Azure-resources die worden beschreven in [gegevens van Azure-resources bewaken](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data).

Zie informatie over [Azure IOT hub-gegevens bewaken](monitor-iot-hub-reference.md) voor gedetailleerde informatie over de metrische gegevens en logboeken die zijn gemaakt door Azure IOT hub.

> [!IMPORTANT]
> De gebeurtenissen die worden verzonden door de IoT Hub-service met Azure Monitor bron logboeken zijn niet gegarandeerd betrouwbaar of besteld. Sommige gebeurtenissen kunnen verloren gaan of in de juiste volg orde worden geleverd. Het is ook niet mogelijk om de bron logboeken te gebruiken. het kan enkele minuten duren voordat gebeurtenissen worden geregistreerd in het doel van uw keuze.

## <a name="collection-and-routing"></a>Verzameling en route ring

De metrische gegevens van het platform en het activiteiten logboek worden automatisch verzameld en opgeslagen, maar kunnen worden doorgestuurd naar andere locaties met behulp van een diagnostische instelling.

Bron logboeken worden pas verzameld en opgeslagen als u een diagnostische instelling hebt gemaakt en deze naar een of meer locaties wilt door sturen.

Metrische gegevens en logboeken kunnen worden doorgestuurd naar verschillende locaties, waaronder:
- De Azure Monitor logboeken worden opgeslagen via een gekoppelde Log Analytics-werk ruimte. Er kunnen met Log Analytics worden geanalyseerd.
- Azure Storage voor archivering en offline analyse 
- Een Event Hubs-eind punt waar ze kunnen worden gelezen door externe toepassingen, bijvoorbeeld SIEM-hulpprogram ma's van derden.

In Azure Portal kunt u **Diagnostische instellingen** selecteren onder **bewaking** in het linkerdeel venster van uw IOT-hub, gevolgd door **Diagnostische instelling toevoegen** om Diagnostische instellingen te maken die zijn afgestemd op de logboeken en platform metrieken die door uw IOT-hub worden gegenereerd.

In de volgende scherm afbeelding ziet u een diagnostische instelling voor het routeren van de *verbindings bewerkingen* van het bron logboek en alle platform metrische gegevens naar een log Analytics-werk ruimte.

:::image type="content" source="media/monitor-iot-hub/diagnostic-setting-portal.png" alt-text="Het deel venster Diagnostische instellingen voor een IoT-hub.":::

Zie [Diagnostische instelling maken voor het verzamelen van platform logboeken en metrische gegevens in azure](../azure-monitor/platform/diagnostic-settings.md) voor het gedetailleerde proces voor het maken van een diagnostische instelling met behulp van de Azure Portal, CLI of Power shell. Wanneer u een diagnostische instelling maakt, geeft u op welke categorieën logboeken u wilt verzamelen. De categorieën voor Azure IoT Hub worden vermeld onder [resource Logboeken in de referentie Azure IOT hub-gegevens controleren](monitor-iot-hub-reference.md#resource-logs).

Houd rekening met de volgende punten wanneer u de metrische gegevens van het platform IoT Hub naar andere locaties:

- De volgende platform metrieken kunnen niet worden geëxporteerd via Diagnostische instellingen: *verbonden apparaten (preview)* en *totale aantal apparaten (preview)*.

- Multidimensionale metrische gegevens, zoals bepaalde [routerings metrieken](monitor-iot-hub-reference.md#routing-metrics), worden momenteel geëxporteerd als afgevlaktte eendimensionale metrische gegevens die worden geaggregeerd over dimensie waarden. Zie [platform metrieken naar andere locaties exporteren](../azure-monitor/platform/metrics-supported.md#exporting-platform-metrics-to-other-locations)voor meer informatie.

## <a name="analyzing-metrics"></a>Metrische gegevens analyseren

U kunt metrische gegevens voor Azure IoT Hub analyseren met metrische gegevens uit andere Azure-Services met behulp van metrische gegevens Verkenner door **metrische gegevens** te openen in het menu **Azure monitor** . Zie [aan de slag met Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md) voor meer informatie over het gebruik van dit hulp programma.

In Azure Portal kunt u **metrische gegevens** selecteren onder **bewaking** in het linkerdeel venster van uw IOT-hub voor het openen van metrische gegevens Verkenner scoped, standaard ingesteld op de platform metrische gegevens die worden verzonden door uw IOT-hub:

:::image type="content" source="media/monitor-iot-hub/metrics-portal.png" alt-text="De pagina metrische gegevens Verkenner voor een IoT-hub.":::

Zie [metrische gegevens in de naslag informatie over azure IOT hub](monitor-iot-hub-reference.md#metrics)voor Azure voor een lijst met de platform statistieken die worden verzameld voor IOT hub. Zie [ondersteunde metrische gegevens met Azure monitor](../azure-monitor/platform/metrics-supported.md)voor een lijst van de platform metrische gegevens die voor alle Azure-Services worden verzameld.

Voor IoT Hub platform-metrische gegevens die worden verzameld in eenheden van aantal, zijn sommige aggregaties mogelijk niet beschikbaar of bruikbaar. Zie [ondersteunde aggregaties in de informatie over Azure IOT hub-gegevens bewaken](monitor-iot-hub-reference.md#supported-aggregations)voor meer informatie.

Enkele IoT Hub metrische gegevens, zoals [routerings metrieken](monitor-iot-hub-reference.md#routing-metrics), zijn multidimensionaal. U kunt voor deze metrische gegevens [filters](../azure-monitor/platform/metrics-charts.md#apply-filters-to-charts) Toep assen en [splitsen](../azure-monitor/platform/metrics-charts.md#apply-splitting-to-a-chart) op uw diagrammen op basis van een dimensie.

## <a name="analyzing-logs"></a>Logboeken analyseren

Gegevens in Azure Monitor logboeken worden opgeslagen in tabellen waarin elke tabel een eigen set unieke eigenschappen heeft. De gegevens in deze tabellen zijn gekoppeld aan een Log Analytics-werk ruimte en kunnen worden opgevraagd in Log Analytics. Voor meer informatie over Azure Monitor-logboeken raadpleegt u [Azure monitor logs-overzicht](../azure-monitor/platform/data-platform-logs.md) in de Azure monitor-documentatie. 

Als u gegevens wilt omleiden naar Azure Monitor logboeken, moet u een diagnostische instelling maken om bron Logboeken of platform metrieken te verzenden naar een Log Analytics-werk ruimte. Zie [verzameling en route ring](#collection-and-routing)voor meer informatie.

In Azure Portal kunt u **Logboeken** selecteren onder **bewaking** in het linkerdeel venster van uw iot-hub om log Analytics query's uit te voeren die standaard zijn vastgelegd in de logboeken en metrische gegevens die zijn verzameld in azure monitor logboeken voor uw IOT-hub.

:::image type="content" source="media/monitor-iot-hub/logs-portal.png" alt-text="De pagina logboeken voor een IoT-hub.":::

Zie voor een lijst met tabellen die worden gebruikt door Azure Monitor logboeken en query's die kunnen worden doorzocht door Log Analytics [Azure monitor logboeken tabellen in de referentie Azure IOT hub-gegevens bewaken](monitor-iot-hub-reference.md#azure-monitor-logs-tables).

Alle resource Logboeken in Azure Monitor hebben dezelfde velden die worden gevolgd door servicespecifieke velden. Het algemene schema wordt beschreven in [Azure monitor resource-logboek schema](../azure-monitor/platform/resource-logs-schema.md#top-level-common-schema). U vindt het schema en de categorieën van resource logboeken die zijn verzameld voor Azure IoT Hub in [resource Logboeken in de gegevens referentie van azure IOT hub bewaken](monitor-iot-hub-reference.md#resource-logs).

Het [activiteiten logboek](../azure-monitor/platform/activity-log.md) is een platform logboek in azure dat inzicht biedt in gebeurtenissen op abonnements niveau. U kunt deze onafhankelijk bekijken of door sturen naar Azure Monitor-logboeken, waar u veel complexere query's kunt uitvoeren met behulp van Log Analytics.  

Houd rekening met de volgende punten wanneer u de metrische gegevens van het IoT Hub platform wilt omleiden naar Azure Monitor-logboeken:

- De volgende platform metrieken kunnen niet worden geëxporteerd via Diagnostische instellingen: *verbonden apparaten (preview)* en *totale aantal apparaten (preview)*.

- Multidimensionale metrische gegevens, zoals bepaalde [routerings metrieken](monitor-iot-hub-reference.md#routing-metrics), worden momenteel geëxporteerd als afgevlaktte eendimensionale metrische gegevens die worden geaggregeerd over dimensie waarden. Zie [platform metrieken naar andere locaties exporteren](../azure-monitor/platform/metrics-supported.md#exporting-platform-metrics-to-other-locations)voor meer informatie.

Zie voor een aantal algemene query's met IoT Hub voor [beelden van Kusto-query's](#sample-kusto-queries). Zie [overzicht van logboek query's in azure monitor](../azure-monitor/log-query/log-query-overview.md)voor gedetailleerde informatie over het gebruik van log Analytics query's.

### <a name="sdk-version-in-iot-hub-logs"></a>SDK-versie in IoT Hub-logboeken

Sommige bewerkingen in IoT Hub bron logboeken retour neren een `sdkVersion` eigenschap in hun `properties` object. Wanneer een apparaat of back-end-app een van de Azure IoT-Sdk's gebruikt, bevat deze eigenschap informatie over de SDK die wordt gebruikt, de SDK-versie en het platform waarop de SDK wordt uitgevoerd. In het volgende voor beeld ziet u de eigenschap die wordt `sdkVersion` verzonden voor een [`deviceConnect`](monitor-iot-hub-reference.md#connections) bewerking wanneer u de SDK voor Node.js apparaten gebruikt: `"azure-iot-device/1.17.1 (node v10.16.0; Windows_NT 10.0.18363; x64)"` . Hier volgt een voor beeld van de waarde die is verzonden voor de .NET (C#) SDK: `".NET/1.21.2 (.NET Framework 4.8.4200.0; Microsoft Windows 10.0.17763 WindowsProduct:0x00000004; X86)"` .

In de volgende tabel ziet u de SDK-naam die wordt gebruikt voor verschillende Azure IoT-Sdk's:

| SDK-naam in eigenschap sdkVersion | Taal |
|----------|----------|
| .NET | .NET (C#) |
| micro soft. Azure. devices | .NET (C#) Service SDK |
| micro soft. Azure. devices. client | .NET (C#)-apparaat-SDK |
| iothubclient | C of python v1 (afgeschaft) apparaat-SDK |
| iothubserviceclient | C of python v1 (afgeschaft) Service-SDK |
| Azure-IOT-Device-iothub-py | Python-apparaat-SDK |
| azure-iot-device | SDK van Node.js apparaat |
| azure-iothub | Node.js Service-SDK |
| com. micro soft. Azure. iothub-Java-client | Java-apparaat-SDK |
| com. micro soft. Azure. iothub. service. SDK | Java Service-SDK |
| com. Microsoft. Azure. SDK. IOT. IOT-apparaat-client | Java-apparaat-SDK |
| com. Microsoft. Azure. SDK. IOT. IOT-service-client | Java Service-SDK |
| C | Embedded C |
| C + (OSSimplified = Azure RTO'S) | Azure RTOS |

U kunt de eigenschap SDK-versie extra heren wanneer u query's uitvoert op IoT Hub bron Logboeken. Met de volgende query worden bijvoorbeeld de SDK-versie-eigenschap (en de apparaat-ID) geëxtraheerd uit de eigenschappen die worden geretourneerd door de bewerkingen voor verbindingen. Deze twee eigenschappen worden naar de resultaten geschreven, samen met de tijd van de bewerking en de resource-ID van de IoT-hub waarmee het apparaat verbinding maakt.

```kusto
// SDK version of devices
// List of devices and their SDK versions that connect to IoT Hub
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s) 
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
| distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
```

### <a name="sample-kusto-queries"></a>Voor beeld van Kusto-query's

> [!IMPORTANT]
> Wanneer u **Logboeken** selecteert in het menu IOT hub, wordt log Analytics geopend met het query bereik dat is ingesteld op de huidige IOT-hub. Dit betekent dat logboek query's alleen gegevens van die bron bevatten. Als u een query wilt uitvoeren die gegevens uit andere IoT-hubs of-gegevens uit andere Azure-Services bevat, selecteert u **Logboeken** in het **Azure monitor** menu. Zie de [logboek query bereik en het tijds bereik in Azure Monitor Log Analytics](../azure-monitor/log-query/scope.md) voor meer informatie.

Hieronder vindt u query's die u kunt gebruiken om uw IoT-hub te bewaken.

- Connectiviteits fouten: verbindings fouten van apparaten identificeren.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and Level == "Error"
    ```

- Beperkings fouten: Identificeer apparaten die de meeste aanvragen hebben uitgevoerd, wat leidt tot bandbreedte fouten.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where ResultType == "429001"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize count() by DeviceId, Category, _ResourceId
    | order by count_ desc
    ```

- Dode eind punten: Identificeer dode of beschadigde eind punten op basis van het aantal keren dat het probleem is gerapporteerd, en de reden waarom.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Routes" and OperationName in ("endpointDead", "endpointUnhealthy")
    | extend parsed_json = parse_json(properties_s)
    | extend Endpoint = tostring(parsed_json.endpointName), Reason = tostring(parsed_json.details) 
    | summarize count() by Endpoint, OperationName, Reason, _ResourceId
    | order by count_ desc
    ```

- Fout Samenvatting: aantal fouten voor alle bewerkingen op type.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Level == "Error"
    | summarize count() by ResultType, ResultDescription, Category, _ResourceId
    ```

- Recent verbonden apparaten: een lijst met apparaten die IoT Hub gezien in de opgegeven tijds periode verbinding maken.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and OperationName == "deviceConnect"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize max(TimeGenerated) by DeviceId, _ResourceId
    ```

- SDK-versie van apparaten: lijst met apparaten en hun SDK-versies voor apparaat verbindingen of het apparaat naar in de Cloud dubbele bewerkingen.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" or Category == "D2CTwinOperations"
    | extend parsed_json = parse_json(properties_s)
    | extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
    | distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
    ```

### <a name="read-logs-from-azure-event-hubs"></a>Logboeken van Azure Event Hubs lezen

Nadat u gebeurtenis logboek registratie via Diagnostische instellingen hebt ingesteld, kunt u toepassingen maken die de logboeken lezen, zodat u actie kunt ondernemen op basis van de informatie in hen. Met deze voorbeeld code worden logboeken van een Event Hub opgehaald:

```csharp
class Program
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}";
    static string monitoringEndpointName = "{your AMS event hub endpoint name}";
    static EventHubClient eventHubClient;
    //This is the Diagnostic Settings schema
    class AzureMonitorDiagnosticLog
    {
        string time { get; set; }
        string resourceId { get; set; }
        string operationName { get; set; }
        string category { get; set; }
        string level { get; set; }
        string resultType { get; set; }
        string resultDescription { get; set; }
        string durationMs { get; set; }
        string callerIpAddress { get; set; }
        string correlationId { get; set; }
        string identity { get; set; }
        string location { get; set; }
        Dictionary<string, string> properties { get; set; }
    };

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }
        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }
            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));
            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
                var deserializer = new JavaScriptSerializer();
                //deserialize json data to azure monitor object
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result);
            }
        }
    }
}
```

## <a name="alerts"></a>Waarschuwingen

Azure Monitor waarschuwingen geven u proactief op de hoogte wanneer er belang rijke voor waarden worden gevonden in uw bewakings gegevens. Hiermee kunt u problemen in uw systeem identificeren en oplossen voordat uw klanten ze opmerken. U kunt waarschuwingen instellen voor [metrische gegevens](../azure-monitor/platform/alerts-metric-overview.md), [Logboeken](../azure-monitor/platform/alerts-unified-log.md)en het [activiteiten logboek](../azure-monitor/platform/activity-log-alerts.md). Verschillende soorten waarschuwingen hebben voor delen en nadelen.

Wanneer u een waarschuwings regel maakt op basis van de metrische gegevens van het platform, moet u er rekening mee houden dat voor IoT Hub-platform metrische gegevens die worden verzameld in aantal eenheden, sommige aggregaties mogelijk niet beschikbaar of bruikbaar zijn. Zie [ondersteunde aggregaties in de informatie over Azure IOT hub-gegevens bewaken](monitor-iot-hub-reference.md#supported-aggregations)voor meer informatie.

## <a name="monitor-per-device-disconnects-with-event-grid"></a>Verbroken verbindingen per apparaat met Event Grid

Azure Monitor biedt een metrische, *verbonden apparaten* die u kunt gebruiken om het aantal apparaten dat is verbonden met uw IOT hub te bewaken en een waarschuwing te activeren wanneer het aantal verbonden apparaten onder een drempel waarde daalt. Hoewel dit voldoende is voor sommige scenario's, biedt [Azure Event grid](../event-grid/index.yml) een bewakings oplossing met lage latentie per apparaat die u kunt gebruiken om de verbindingen van apparaten en infra structuur te controleren.

Met Event Grid kunt u zich abonneren op de IoT Hub [ **DeviceConnected** -en **DeviceDisconnected** -gebeurtenissen](iot-hub-event-grid.md#event-types) om waarschuwingen te activeren en de verbindings status van een apparaat te bewaken. Event Grid biedt veel lagere gebeurtenis latentie dan Azure Monitor, en u kunt bewaken per apparaat, in plaats van het totale aantal verbonden apparaten. Deze factoren maken Event Grid de voorkeurs methode voor het bewaken van verbindingen voor kritieke apparaten en infra structuur. Het is raadzaam om Event Grid te gebruiken voor het bewaken van apparaat verbindingen in productie omgevingen.

Voor meer informatie over het bewaken van verbindingen met Event Grid en Azure Monitor raadpleegt u de [verbinding met Azure IOT hub controleren, vaststellen en problemen oplossen](iot-hub-troubleshoot-connectivity.md).

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure IOT hub data Reference bewaken](monitor-iot-hub-reference.md) voor een verwijzing naar de metrische gegevens, logboeken en andere belang rijke waarden die zijn gemaakt door [Service naam].

- Zie [Azure-resources bewaken met Azure monitor](../azure-monitor/insights/monitor-azure-resource.md) voor meer informatie over het bewaken van Azure-resources.