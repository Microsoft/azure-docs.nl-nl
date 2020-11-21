---
title: Model synchronisatie tussen Azure Digital Apparaatdubbels en Time Series Insights | Microsoft Docs
description: Aanbevolen procedures en hulpprogram ma's die worden gebruikt om activa model te vertalen in ADT naar Asset model in azure TSI
ms.service: time-series-insights
services: time-series-insights
author: msrsrinivasan
ms.author: radhsrin
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: dpalled
ms.openlocfilehash: 0d02a6e3eb2aef4a02c90360b2016e64af579081
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95014727"
---
# <a name="model-synchronization-between-azure-digital-twins-and-time-series-insights-gen2"></a>Modelsynchronisatie tussen Azure Digital Twins en Time Series Insights Gen2

In dit artikel worden aanbevolen procedures en hulpprogram ma's beschreven die worden gebruikt voor het vertalen van het activa model in azure Digital Apparaatdubbels (ADT) naar activa model in Azure Time Series Insights (TSI).  Dit artikel is het tweede deel van een tweedelige zelf studie met uitleg over de integratie van Azure Digital Apparaatdubbels met Azure Time Series Insights. Integratie van Azure Digital Apparaatdubbels met Time Series Insights maakt archivering mogelijk en traceert de geschiedenis van teleelementen en berekende eigenschappen van digitale Apparaatdubbels. In deze reeks zelf studies zijn ontwikkel aars gericht op het integreren van Time Series Insights met Azure Digital Apparaatdubbels. In deel 1 wordt uitgelegd hoe  [u gegevens pijplijn gaat maken die in de actuele tijd reeks gegevens van Azure Digital apparaatdubbels naar Time Series Insights](../digital-twins/how-to-integrate-time-series-insights.md) . in dat tweede deel van de reeks zelf studies wordt de synchronisatie van Asset model tussen Azure Digital apparaatdubbels en time series Insights uitgelegd. In deze zelf studie worden de aanbevolen procedures beschreven voor het kiezen en het vaststellen van naamgevings regels voor de tijd reeks-ID (TS-ID) en het hand matig instellen van hiërarchieën in time series model (TSM).

## <a name="choosing-a-time-series-id"></a>Een tijd reeks-ID kiezen

De time series-ID is een unieke id die wordt gebruikt voor het identificeren van assets in Time Series Insights. Tijdreeks gegevens (teleacties van het veld, die combi Naties van tijd waarden), worden weer gegeven met variabelen die worden vermeld onder TSID. In azure Digital Apparaatdubbels worden dubbele eigenschappen en teletechnische elementen gebruikt om de status van een dubbele en meting aan te geven. Vanaf het huidige ontwerp van TSM moeten de TSID uniek zijn. Door gebruik te maken van dubbele Id's van de apparaatdubbels in azure Digital Apparaatdubbels of in combi natie met de naam van de eigenschap of telemetrie, wordt er altijd een unieke TS-ID in TSM gemaakt. In de meeste gevallen is de **_`<Twin ID>`_** TSID en de naam van de eigenschap en telemetrie de variabelen in TSM. Er zijn echter use-cases waarbij het voor keur is dat activa hiërarchieën in Time Series Insights worden afgevlakt met de indeling samengestelde sleutels, zoals **_`<Twin ID>+ <Delimiter of Choice> + <Name of the Property or Telemetry>`_** . We gaan een voor beeld bekijken om het probleem op te nemen. Overweeg een ruimte te maken in een model dat is gemodelleerd als een dubbele en heeft een dubbele ID-Room22. De instellings eigenschap van de Tempe ratuur moet worden vertaald als **_TSID Room22_TempSetting_** en meting van de Tempe ratuur die moet worden vertaald naar **_Room22_TempMea_** in TSM.

[![Een tijd reeks-ID kiezen](media/tutorials-model-sync/choosing-tsid.png)](media/tutorials-model-sync/choosing-tsid.png#lightbox)

## <a name="contextualizing-time-series"></a>Contextualizing tijd reeks

Het Contextualization van gegevens (vooral ruimtelijke aard) in Time Series Insights wordt bereikt door middel van Asset-hiërarchieën en hetzelfde wordt gebruikt voor eenvoudige navigatie van gegevens via een structuur weergave in Time Series Insights Explorer. Typen tijd reeksen en hiërarchieën worden gedefinieerd met behulp van Time Series model (TSM) in Time Series Insights. Typen in TSM Help voor het definiëren van variabelen, terwijl hiërarchie niveaus en waarden voor het exemplaar veld worden gebruikt om de structuur weergave in de Time Series Insights Explorer te maken. Raadpleeg de [documentatie voor online time series Insights](./concepts-model-overview.md)voor meer informatie over TSM.

In azure Digital Apparaatdubbels wordt de verbinding tussen assets aangegeven met dubbele relaties. Dubbele relaties zijn gewoon een grafiek van verbonden assets. In de tijd reeks inzicht zijn de relaties tussen assets echter hiërarchisch. Dat wil zeggen dat activa een bovenliggende/onderliggende soort od-relatie hebben en wordt weer gegeven met behulp van een boom structuur. Om relatie-informatie van Azure Digital Apparaatdubbels te converteren naar Time Series Insights-hiërarchieën, moeten we relevante hiërarchische relaties kiezen uit Azure Digital Apparaatdubbels. Azure Digital Apparaatdubbels maakt gebruik van een open standaard, model taal met de naam Digital-dubbele definitie taal (DTDL). In DTDL modellen worden beschreven met behulp van een variant van JSON met de naam JSON-LD. Raadpleeg de [DTDL-documentatie](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) voor volledige informatie over de specificatie.

[![Verbinding tussen assets](media/tutorials-model-sync/asset-connection.png)](media/tutorials-model-sync/asset-connection.png#lightbox)

## <a name="translating-graph-representation-in-azure-digital-twins-to-tree-structure-in-time-series-insights"></a>De grafiek weergave in azure Digital Apparaatdubbels omzetten in een boom structuur in Time Series Insights

In de volgende secties van de zelf studie worden een aantal kern scenario's vastgelegd van het hand matig omzetten van de grafiek structuur in azure Digital Apparaatdubbels naar boom structuur in Time Series Insights.

Voorbeeld systeem: deze zelf studie maakt gebruik van het volgende voor beeld om de hieronder beschreven concepten uit te leggen. Het is een eenvoudig, fictief beheer systeem met één verdieping en twee ruimtes. Het heeft drie Thermo Staten, één in elk van de kamers en een gemeen schappelijke basis voor de vloer. Daarnaast heeft het een water stroom meter die de water stroom van Room21 naar Room22 verschuift via een sluis verbinding tussen de kamers. De ruimtelijke relatie tussen apparaatdubbels wordt bekeken, maar beide typen relaties.

1. De meest voorkomende hiërarchische relatie. Bijvoorbeeld Building40-> Floor01-> FloorTS *-> temperatuur
1. Niet zo gebruikelijk, kring relatie. Vanaf Building40 kan het FlowMtr bijvoorbeeld worden getraceerd via twee verschillende paden.

   1. Building40-> Floor01-> Room21-> FlowMtr *-> flow
   1. Building40-> Floor01-> Room22-> FlowMtr *-> flow  
      Waarbij ' stroom ' de werkelijke telemetrie is waarmee de water stroom tussen Room21 en Room22 wordt gemeten

> [!Note]
>
> `*` FloorTS staat voor FloorThermoStat en FlowMtr voor stroom meter en wordt meestal gekozen als de TSID. Tempe ratuur en stroom zijn de ruwe telemetrie die wordt aangeduid als variabelen in Time Series Insights.

Gezien de huidige beperking in Time Series Insights dat één activum niet in meerdere branches kan worden weer gegeven, wordt in de volgende secties uitgelegd hoe hiërarchische en kring relaties in Time Series Insights kunnen worden gemodelleerd.

## <a name="case-1-hierarchical-parent-child-relationship"></a>Voor beeld 1: hiërarchische relatie (bovenliggend/onderliggend)

Het is het meest voorkomende type relatie tussen de apparaatdubbels. model lering van een zuivere bovenliggende/onderliggende relatie wordt uitgelegd in de volgende afbeelding. Exemplaar velden en TSID worden afgeleid van dubbele id's, zoals hieronder weer gegeven. Hoewel exemplaar velden hand matig kunnen worden bijgewerkt met Time Series Insights Explorer, wordt in de volgende sectie met de naam ' instance-velden bijwerken met Api's ' uitgelegd hoe u naar model wijzigingen in azure Digital Apparaatdubbels en de instantie velden in Time Series Insights bijwerkt met behulp van Azure functions.

[![Dubbele Id's toewijzen](media/tutorials-model-sync/mapping-twin-ids.png)](media/tutorials-model-sync/mapping-twin-ids.png#lightbox)

[![Dubbele Id's toewijzen 2](media/tutorials-model-sync/mapping-twin-ids2.png)](media/tutorials-model-sync/mapping-twin-ids2.png#lightbox)

## <a name="case-2-circular-relationship"></a>Voor beeld 2: kring relatie

### <a name="circular-relationship-in-azure-digital-twins-to-single-hierarchy-relationship-in-time-series-insights"></a>Kring relatie in azure Digital Apparaatdubbels naar een relatie met één hiërarchie in Time Series Insights

Aangezien de TSID uniek moet zijn en alleen in één hiërarchie kan worden weer gegeven, vertegenwoordigt deze aanvraag de _' FlowMtr '_ met een telemetrie met de naam _' flow '_ , net onder dubbele _' Room21 '_. In de toekomst wordt de telemetrie- _stroom_ weer gegeven onder _room 21_ en _' kamer 22 '_ wanneer time series Insights meerdere representatie van tijd reeksen kan ondersteunen in TSM.

In de volgende scherm afbeelding ziet u hand matig dubbele Id's in azure Digital Apparaatdubbels koppelen aan het veld instance in TSM en de resulterende hiërarchie in Time Series Insights.

[![Dubbele Id's toewijzen in azure Digital Apparaatdubbels](media/tutorials-model-sync/mapping-twin-ids-adt.png)](media/tutorials-model-sync/mapping-twin-ids-adt.png#lightbox)

### <a name="circular-relationship-in-azure-digital-twins-to-multiple-hierarchies-in-time-series-insights-using-duplicates"></a>Kring relatie in azure Digital Apparaatdubbels naar meerdere hiërarchieën in Time Series Insights, met behulp van duplicaten

In deel 1 van de zelf studie wordt uitgelegd hoe een client programma (een Azure function) telemetriegegevens van IoT Hub of andere gebeurtenis bronnen overbrengt naar Azure Digital Apparaatdubbels. Deze aanpak raadt aan om hetzelfde client programma te gebruiken om updates te maken voor relevante eigenschappen van de bovenliggende apparaatdubbels. Tijdens het lezen van de FlowMtr-telemetrie van IoT Hub en het bijwerken van de eigenschap flow in FlowMtr dubbele, kan het programma ook de bijbehorende eigenschappen in alle mogelijke bovenliggende apparaatdubbels van de bron bijwerken. In ons voor beeld zou het ' outflowmea ' zijn (geïdentificeerd met behulp van de eigenschap ' uitstroom ') van de eigenschap Room21 en ' inflowmea ' van Room22.  Onder scherm afbeelding ziet u de uiteindelijke gebruikers ervaring in Time Series Insights Explorer. Er wordt geconstateerd dat er dubbele gegevens zijn door deze benadering te nemen.

[![Time Series Insights Explorer](media/tutorials-model-sync/tsi-explorer.png)](media/tutorials-model-sync/tsi-explorer.png#lightbox)

Hieronder ziet u hoe de client toepassing kan navigeren op de dubbele relatie door gebruik te maken van Azure Digital Apparaatdubbels Api's.

> [!Note]
>
> In dit voor beeld van een code fragment wordt ervan uitgegaan dat lezers bekend zijn met [deel 01](../digital-twins/tutorial-end-to-end.md#set-up-the-sample-function-app) van de zelf studie en dat deze code wijziging is doorgevoerd in de functie ' ProcessHubToDTEvents '.

```csharp
if (propertyPath.Equals("/Flow"))
{
//Update the flow value property of the flow meter
await AdtUtilities.UpdateTwinProperty(client, twinId, "replace",
propertyPath, "double", propertyValue, log);

//also update the sending end flow
string parentIdOutflow = await AdtUtilities.FindParent(client, twinId,
"outflow", log);
if (parentIdOutflow != null)
await AdtUtilities.UpdateTwinProperty(client, parentIdOutflow, "replace", "outflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with outflow
relationship for " + twinId );
//and receiving end flow value
string parentIdinflow = await AdtUtilities.FindParent(client, twinId,
"inflow", log);
if (parentIdinflow != null)

await AdtUtilities.UpdateTwinProperty(client, parentIdinflow,
"replace", "inflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with inflow
relationship for " + twinId);
}
```

## <a name="updating-instance-fields-using-apis"></a>Exemplaar velden bijwerken met behulp van Api's

In deze sectie van de zelf studie wordt uitgelegd hoe u met Time Series Insights model-Api's naar model wijzigingen luistert in azure Digital Apparaatdubbels, zoals het maken, verwijderen van apparaatdubbels of het wijzigen van de relaties tussen apparaatdubbels en het bijwerken van exemplaar velden en hiërarchieën via een programma. Deze methode voor het bijwerken van Time Series Insights model wordt meestal bereikt via Azure functions. In azure Digital Apparaatdubbels kunnen gebeurtenis meldingen, zoals dubbele optellingen of verwijderingen, worden gerouteerd door downstream-Services, zoals Event Hubs die op zijn beurt kunnen worden ingevoerd in azure functions. Meer informatie over gebeurtenis Routering en-filters worden [hier](../digital-twins/how-to-manage-routes-portal.md)beschreven.  In het resterende deel van deze sectie wordt uitgelegd hoe u Time Series Insights model-Api's in azure functions kunt gebruiken om Time Series Insights model bij te werken in reactie op een dubbele toevoeging (een type model wijziging) in azure Digital Apparaatdubbels.

### <a name="receiving-and-identifying-twin-addition-event-notification"></a>Gebeurtenis melding dubbele optelling ontvangen en identificeren

```csharp
[FunctionName("RouteEventsToTsi")]
public async Task Run([EventGridTrigger]EventGridEvent eventGridEvent)
{
    try
    {
        if (eventGridEvent != null && eventGridEvent.Data != null)
        {
            logger.LogInformation($"EventType: {eventGridEvent.EventType}");
            logger.LogInformation($"EventGridEvent: {JsonConvert.SerializeObject(eventGridEvent)}");

            //Shape event and Send event data to event hub and tsi
            await SendEventToEventHubAsync(eventGridEvent).ConfigureAwait(false);

            //If a new twin was created, update the newly created instance in TSI with info retrieved from ADT
            if (eventGridEvent.EventType == Constants.TwinCreateEventType)
            {
                //retrieve building, floor and room of value twin
                var twinInfo = await RetrieveTwinInfoAsync(eventGridEvent).ConfigureAwait(false);
                //Update Tsi instance with type(sensor type), hierarchy(space hierarchy) and instance fields(twin info retrieved above)
                var instance = await CreateInstanceToSendAsync(twinInfo).ConfigureAwait(false);
                var instanceToUpdate = new List<TimeSeriesInstance>() { instance };
                var response = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(new InstancesBatchRequest(update: instanceToUpdate)).ConfigureAwait(false);
            }
        }
    }
    catch (Exception ex)
    {
        logger.LogError($"Exception: {ex.Message}");
    }
}
```

### <a name="creating-time-series-insights-client-and-adding-instance-details"></a>Time Series Insights-client maken en exemplaar gegevens toevoegen

```csharp
private async Task<TimeSeriesInstance> CreateInstanceToSendAsync(Dictionary<string, string> twinInfo)
{
    try
    {
        tsiClient = await GetTSIClientAsync().ConfigureAwait(false);

        var timeSeriesId = new object[] { twinInfo[Constants.DtId] };
        var instances = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(
            new InstancesBatchRequest(
                get: new InstancesRequestBatchGetOrDelete(
                    new IList<object>[] { timeSeriesId }))).ConfigureAwait(false);
        var instance = instances.Get.First().Instance;

        if (instance != null)
        {
            instance = await AddHierarchyToInstanceAsync(instance).ConfigureAwait(false);
            instance = await AddTypeToInstanceAsync(instance, twinInfo[Constants.TwinType]).ConfigureAwait(false);

            instance.InstanceFields = new Dictionary<string, object>
                            {
                                { "Building", twinInfo[Constants.BuildingName] },
                                { "Floor", twinInfo[Constants.FloorName] },
                                { "Room", twinInfo[Constants.ParentName] }
                            };

            //If value twin is a sensor value twin, add sensor type instance field to diff from spatial value twin
            if (twinInfo[Constants.ParentType] == Constants.Sensor)
            {
                instance.InstanceFields.Add(Constants.SensorType, twinInfo[Constants.TwinType]);
            }
            return instance;
        }
        else
        {
            logger.LogError($"instance with id {twinInfo[Constants.DtId]} not found");
            return new TimeSeriesInstance();
        }
    }
    catch (Exception e)
    {
        logger.LogError(e.Message);
        throw;
    }
}
```

### <a name="applying-hierarchy-info-to-instance"></a>Hiërarchie gegevens Toep assen op exemplaar

```csharp
private async Task<TimeSeriesInstance> AddHierarchyToInstanceAsync(TimeSeriesInstance instance)
{
    if (instance.HierarchyIds == null)
    {
        TimeSeriesHierarchy spacesHierarchy = null;
        try
        {
            var hierarchy = await RunGetHierarchiesAsync(Constants.SpaceHierarchy).ConfigureAwait(false);
            spacesHierarchy = hierarchy.First(h => h.Name.Equals(Constants.SpaceHierarchy));
            instance.HierarchyIds = new List<Guid?>();
            instance.HierarchyIds.Add(spacesHierarchy.Id);
        }
        catch (Exception ex)
        {
            logger.LogWarning($"Hierarchy 'space hierarchy' not found, {ex}");
            throw;
        }
    }
    return instance;
}
```

## <a name="next-steps"></a>Volgende stappen

In de reeks zelf studies wordt uitgelegd hoe u met Time Series Insights Api's historische gegevens uit Azure Digital Apparaatdubbels kunt opvragen. Het is een werk dat wordt uitgevoerd en de sectie wordt bijgewerkt wanneer dit klaar is. In de tussen tijd wordt het aanbevolen om te verwijzen naar [Time Series Insights data query API-documentatie](./concepts-query-overview.md).