---
title: Taak patronen voor gebeurtenis replicatie-Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat gedetailleerde richt lijnen voor het implementeren van specifieke taak patronen voor gebeurtenis replicatie
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 7702b1987faabfce8d97e7b5c9b18766df72caad
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803983"
---
# <a name="event-replication-tasks-patterns"></a>Patronen voor gebeurtenis replicatie taken

In het overzicht van de [Federatie](event-hubs-federation-overview.md) en de [Replicator functies](event-hubs-federation-replicator-functions.md) wordt uitgelegd wat de reden is voor en de basis elementen van replicatie taken. u wordt aangeraden deze te verkennen voordat u verdergaat met dit artikel.

In dit artikel beschrijven we implementatie richtlijnen voor verschillende patronen die in de sectie overzicht zijn gemarkeerd.

## <a name="replication"></a>Replicatie

Het replicatie patroon kopieert gebeurtenissen van de ene Event hub naar de volgende of van een event hub naar een andere bestemming, zoals een Service Bus wachtrij. De gebeurtenissen worden doorgestuurd zonder dat er wijzigingen in de gebeurtenis lading worden aangebracht.

De implementatie van dit patroon wordt gedekt door de [gebeurtenis replicatie tussen Event hubs](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy) en [gebeurtenis replicatie tussen Event hubs en service bus](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus) voor beelden.

### <a name="streams-and-order-preservation"></a>Stroom-en order behoud

Replicatie, hetzij via Azure Functions of Azure Stream Analytics, is niet gericht op het maken van exacte 1:1-klonen van een bron Event hub in een doel event hub, maar is gericht op het behouden van de relatieve volg orde van gebeurtenissen waarvoor de toepassing dit vereist. De toepassing communiceert door het groeperen van gerelateerde gebeurtenissen met dezelfde partitie sleutel en [Event hubs rangschikt berichten met dezelfde partitie sleutel sequentieel in dezelfde partitie](event-hubs-features.md#partitions).

> [!IMPORTANT] 
> De "offset"-gegevens zijn uniek voor elke event hub en verschuivingen voor dezelfde gebeurtenissen verschillen per Event hub-instantie. Als u een positie in een gekopieerde gebeurtenis stroom wilt zoeken, gebruikt u offsets op basis van tijd en verwijst u naar de [door gegeven service toegewezen meta gegevens](#service-assigned-metadata).
>
> Op tijd gebaseerde offsets starten uw ontvanger op een bepaald moment:
> - *EventPosition. FromStart ()* -alle terugkerende gegevens opnieuw lezen.
> - *EventPosition. FromEnd ()* : alle nieuwe gegevens lezen vanaf het moment van de verbinding.
> - *EventPosition. FromEnqueuedTime (datetime)* : alle gegevens die vanaf een opgegeven datum en tijd beginnen.
>
> In de Event processor stelt u de positie in via de InitialOffsetProvider op de EventProcessorOptions. Met de andere ontvanger-Api's wordt de positie door gegeven via de constructor. 


De vooraf gebouwde helpers voor replicatie [functies die worden](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) gebruikt in de Azure functions op basis van de richt lijnen zorgen ervoor dat gebeurtenis stromen met dezelfde partitie sleutel die is opgehaald van een bron partitie, worden verzonden naar de doel event hub als een batch in de oorspronkelijke stroom en met dezelfde partitie sleutel.

Als het aantal partities van de bron-en doel event hub identiek is, worden alle stromen in het doel toegewezen aan dezelfde partities als in de bron.
Als het aantal partities afwijkt van een aantal van de verdere patronen die in het volgende worden beschreven, is de toewijzing anders, maar worden de stromen altijd samen en in de juiste volg orde bewaard.

De relatieve volg orde van gebeurtenissen die deel uitmaken van verschillende stromen of van onafhankelijke gebeurtenissen zonder partitie sleutel in een doel partitie, kan altijd verschillen van de bron partitie.

### <a name="service-assigned-metadata"></a>Aan de service toegewezen meta gegevens

De aan de service toegewezen meta gegevens van een gebeurtenis die is verkregen van de bron Event hub, de oorspronkelijke in-en uitstel tijd, het Volg nummer en de offset, worden vervangen door nieuwe door de service toegewezen waarden in de doel event hub, maar met de [hulp functies](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication), replicatie taken die in de voor beelden zijn opgegeven, worden de oorspronkelijke waarden bewaard in gebruikers eigenschappen: `repl-enqueue-time` (iso8601 String) `repl-sequence` `repl-offset`

Deze eigenschappen zijn van het type teken reeks en bevatten de stringified-waarde van de respectieve oorspronkelijke eigenschappen. Als de gebeurtenis meerdere keren wordt doorgestuurd, worden de aan de service toegewezen meta gegevens van de onmiddellijke bron toegevoegd aan de bestaande eigenschappen, waarbij de waarden worden gescheiden door punt komma's.

### <a name="failover"></a>Failover

Als u replicatie gebruikt voor herstel na nood gevallen, om te beschermen tegen regionale beschikbaarheids gebeurtenissen in de Event Hubs-service of tegen netwerk onderbrekingen, moet een dergelijk storings scenario een failover uitvoeren van een event hub naar de volgende, waardoor de producenten en/of consumenten het secundaire eind punt kunnen gebruiken.

Voor alle failover-scenario's wordt ervan uitgegaan dat de vereiste elementen van de naam ruimten structureel identiek zijn, wat betekent dat Event Hubs-en consumenten groepen identiek zijn en dat de regels voor gedeelde toegangs handtekeningen en/of toegangs beheer op basis van rollen op dezelfde manier worden ingesteld. U kunt een secundaire naam ruimte maken (en bijwerken) door de [richt lijnen te volgen voor het verplaatsen van naam ruimten](move-across-regions.md) en het weglaten van de opschoon stap.

Om producenten en consumenten te laten overschakelen, moet u de informatie over de naam ruimte die u wilt gebruiken, beschikbaar stellen op een locatie die gemakkelijk is te bereiken en bij te werken. Als producenten of consumenten zich veelvuldig of persistente fouten voordoen, moeten ze deze locatie raadplegen en hun configuratie aanpassen. Er zijn talloze manieren om deze configuratie te delen, maar we wijzen twee in het volgende: DNS-en bestands shares.

#### <a name="dns-based-failover-configuration"></a>Failover-configuratie op basis van DNS

Een voor beeld van een gegadigde is de informatie in DNS SRV-records in een door u gestuurde DNS te bewaren en te verwijzen naar de respectieve Event hub-eind punten. 

> [!IMPORTANT] 
> Houd er rekening mee dat Event Hubs niet toestaat dat de eind punten rechtstreeks zijn gekoppeld aan CNAME-records. Dit betekent dat u DNS gebruikt als een flexibele Zoek methode voor eindpunt adressen en niet rechtstreeks IP-adres gegevens kan omzetten.

Stel dat u eigenaar bent `example.com` van het domein en, voor uw toepassing, een zone `test.example.com` . Voor twee alternatieve Event Hubs maakt u nu twee meer geneste zones en een SRV-record in elke.

De SRV-records zijn de volgende algemene conventies, voorafgegaan door `_azure_eventhubs._amqp` en bevatten twee eindpunt records: één voor AMQP-over-TLS op poort 5671 en één voor AMQP-over-Websockets op poort 443, waarbij beide verwijzen naar het event hubs-eind punt van de naam ruimte die overeenkomt met de zone.

| Zone                   | SRV-record                                                                                                                                                            |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `eh1.test.example.com` | **`_azure_servicebus._amqp.eh1.test.example.com`**<br>`1 1 5671 eh1-test-example-com.servicebus.windows.net`<br>`2 2 443 eh1-test-example-com.servicebus.windows.net` |
| `eh2.test.example.com` | **`_azure_servicebus._amqp.eh2.test.example.com`**<br>`1 1 5671 eh2-test-example-com.servicebus.windows.net`<br>`2 2 443 eh2-test-example-com.servicebus.windows.net` |

In de zone van de toepassing maakt u vervolgens een CNAME-vermelding die verwijst naar de onderliggende zone die overeenkomt met uw primaire Event hub:

| CNAME-record                | Alias                    |
| --------------------------- | ------------------------ |
| `eventhub.test.example.com` | `test1.test.example.com` |

Het gebruik van een DNS-client waarmee u expliciet query's kunt uitvoeren op CNAME-en SRV-records (de ingebouwde clients van Java en .NET bieden alleen ondersteuning voor eenvoudige omzetting van namen naar IP-adressen), dan kunt u het gewenste eind punt oplossen. Met [DnsClient.net](https://dnsclient.michaco.net/)is de functie Lookup:

```C#
static string GetEventHubName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_eventhub._amqp.";
    LookupClient lookup = new LookupClient();

    return (from CNameRecord alias in (lookup.Query(aliasName, QueryType.CNAME).Answers)
            from SrvRecord srv in lookup.Query(SrvRecordPrefix + alias.CanonicalName, QueryType.SRV).Answers
            where srv.Port == 5671
            select srv.Target).FirstOrDefault()?.Value.TrimEnd('.');
}
```

De functie retourneert de hostnaam van het doel die is geregistreerd voor poort 5671 van de zone die momenteel is gekoppeld aan de CNAME, zoals hierboven wordt weer gegeven.

Voor het uitvoeren van een failover moet de CNAME-record worden bewerkt en naar de alternatieve zone worden verwezen.

Het voor deel van het gebruik van DNS en met name [Azure DNS](../dns/dns-overview.md), is dat Azure DNS informatie wereld wijd wordt gerepliceerd en daarom robuust is ten opzichte van storingen in één regio.

Deze procedure is vergelijkbaar met de manier waarop de [Event hubs geo-Dr](event-hubs-geo-dr.md) werkt, maar volledig onder uw eigen beheer en werkt ook met actieve/actieve scenario's.

#### <a name="file-share-based-failover-configuration"></a>Failover-configuratie op basis van bestands share

Het eenvoudigste alternatief voor het gebruik van DNS voor het delen van eindpunt gegevens is het plaatsen van de naam van het primaire eind punt in een bestand met tekst zonder opmaak en het bestand van een infra structuur die robuust is tegen storingen en nog steeds updates toestaat.

Als u al een Maxi maal beschik bare website infrastructuur met wereld wijde Beschik baarheid en inhouds replicatie hebt uitgevoerd, voegt u een dergelijk bestand toe en publiceert u het bestand opnieuw als u een schakel optie nodig hebt.

> [!CAUTION]
> U moet de naam van het eind punt alleen op deze manier publiceren, niet een volledig connection string inclusief geheimen.

#### <a name="extra-considerations-for-failing-over-consumers"></a>Extra overwegingen voor het mislukken van consumenten

Voor Event hub-consumenten zijn verdere overwegingen voor de failover-strategie afhankelijk van de behoeften van de gebeurtenis processor.

Als er een nood geval is waarbij een systeem opnieuw moet worden gebouwd, inclusief data bases, van back-upgegevens en de data bases rechtstreeks of via tussenliggende verwerking worden ingevoerd vanuit de gebeurtenissen die in de Event hub worden bewaard, herstelt u de back-up en vervolgens wilt u de gebeurtenissen in het systeem opnieuw afspelen vanaf het moment waarop de database back-up werd gemaakt.

Als een storing alleen van invloed is op een segment van een systeem of slechts één event hub, die onbereikbaar is geworden, wilt u waarschijnlijk door gaan met het verwerken van gebeurtenissen op dezelfde positie als de verwerking is onderbroken.

Als u een scenario wilt maken en de gebeurtenis processor van uw respectieve Azure SDK wilt gebruiken, [maakt u een nieuwe controlepunt opslag](event-processor-balance-partition-load.md#checkpointing) en geeft u een eerste partitie positie op, op basis van de _tijds tempel_ waarvan u de verwerking wilt hervatten.

Als u nog steeds toegang hebt tot de opslag plaats van het controle punt van de Event hub die u overschakelt, kunt u met de hierboven besproken [meta gegevens](#service-assigned-metadata) gebeurtenissen overs laan die al zijn verwerkt en precies worden hervat vanaf waar u de laatste keer hebt verlaten.

## <a name="merge"></a>Samenvoegen

Het samenvoeg patroon bevat een of meer replicatie taken die naar één doel wijzen, mogelijk gelijktijdig met gewone producenten, waarbij ook gebeurtenissen naar hetzelfde doel worden verzonden.

Variaties van deze Patters zijn:

- Twee of meer replicatie functies die gelijktijdig gebeurtenissen ophalen van afzonderlijke bronnen en deze naar hetzelfde doel verzenden.
- Een meer replicatie functie die gebeurtenissen van een bron aanschaft terwijl het doel ook rechtstreeks door de producenten wordt gebruikt.
- Het vorige patroon, maar gespiegeld tussen twee of meer Event Hubs, wat resulteert in de Event Hubs die dezelfde stromen bevatten, ongeacht waar gebeurtenissen worden geproduceerd.

De eerste twee patroon variaties zijn trivial en verschillen niet van taken met een gewone replicatie.

Het laatste scenario vereist dat er geen gerepliceerde gebeurtenissen meer worden gerepliceerd. De techniek wordt gedemonstreerd en uitgelegd in het [EventHubToEventHubMerge](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/code/EventHubMerge) -voor beeld.

## <a name="editor"></a>Editor

Het editor patroon is gebaseerd op het [replicatie](#replication) patroon, maar berichten worden gewijzigd voordat ze worden doorgestuurd. 

Voor beelden van dergelijke wijzigingen zijn:

- **_Transcode ring_** : als de inhoud van de gebeurtenis (ook ' hoofd tekst ' of ' payload ' genoemd) binnenkomt van de bron die is gedecodeerd met de _Apache Avro_ -indeling of een eigen serialisatie-indeling, maar de verwachting van het systeem dat eigenaar is van het doel is dat de inhoud wordt gedecodeerd als _JSON_ -code ring, een replicatie taak voor trans Format teren de payload van _Apache Avro_ eerst deserialiseren in een object grafiek in het geheugen en de grafiek vervolgens serialiseren naar de _JSON_ -indeling voor de gebeurtenis die wordt doorgestuurd. Transcode ring omvat ook **inhouds compressie** en decompressie taken.
- **_Trans formatie_** : gebeurtenissen die gestructureerde gegevens bevatten, moeten mogelijk Reshaping van die gegevens vereisen voor eenvoudiger verbruik door downstream-gebruikers. Dit kan nodig zijn bij het afvlakken van geneste structuren, het weghalen van overbodige gegevens elementen of het aanpassen van de nettolading om precies aan een bepaald schema te voldoen.
- **_Batch_** verwerking: gebeurtenissen kunnen worden ontvangen in batches (meerdere gebeurtenissen in één overdracht) van een bron, maar moeten afzonderlijk worden doorgestuurd naar een doel of andersom. Een taak kan daarom meerdere gebeurtenissen door sturen op basis van één invoer gebeurtenis overdracht of een set gebeurtenissen die vervolgens samen worden overgebracht.
- **_Validatie_** : gebeurtenis gegevens uit externe bronnen moeten vaak worden gecontroleerd om te controleren of ze in overeenstemming zijn met een set regels voordat ze kunnen worden doorgestuurd. De regels kunnen worden uitgedrukt met behulp van schema's of code. Gebeurtenissen die niet aan nalevings vereisten voldoen, kunnen worden verwijderd, met het probleem dat in Logboeken wordt vermeld, of kunnen worden doorgestuurd naar een speciaal doel doel om ze verder af te handelen.
- **_Verrijking_** : gebeurtenis gegevens die afkomstig zijn van sommige bronnen, vereisen mogelijk verrijking met verdere context zodat deze kan worden gebruikt in doel systemen. Dit kan leiden tot het opzoeken van referentie gegevens en het insluiten van gegevens met de gebeurtenis, of het toevoegen van informatie over de bron die bekend is bij de replicatie taak, maar die niet is opgenomen in de gebeurtenissen.
- **_Filteren_** : sommige gebeurtenissen die binnenkomen vanaf een bron, moeten mogelijk worden inge houden van het doel op basis van een regel. Een filter test de gebeurtenis op basis van een regel en vermindert de gebeurtenis als de gebeurtenis niet overeenkomt met de regel. Dubbele gebeurtenissen filteren door bepaalde criteria te bestuderen en volgende gebeurtenissen met dezelfde waarden te verwijderen, is een vorm van filteren.
- **_Crypto grafie_** : een replicatie taak moet mogelijk inhoud ontsleutelen die arriveert vanuit de bron en/of de inhoud versleutelen die naar een doel wordt doorgestuurd en/of de integriteit van inhoud en meta gegevens ten opzichte van een hand tekening die in de gebeurtenis is uitgevoerd, kan controleren of een dergelijke hand tekening bijvoegen.
- **_Attestation_** : een replicatie taak kan meta gegevens, mogelijk die zijn beveiligd door een digitale hand tekening, koppelen aan een gebeurtenis die verklaart dat de gebeurtenis is ontvangen via een specifiek kanaal of op een bepaald tijdstip.
- **_Koppelen_** : een replicatie taak kan hand tekeningen Toep assen op streams van gebeurtenissen, zodat de integriteit van de stroom wordt beveiligd en ontbrekende gebeurtenissen kunnen worden gedetecteerd.

De trans formatie-, batch-en verrijkings patronen worden over het algemeen het beste geïmplementeerd met [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md) taken. 

Al deze patronen kunnen worden geïmplementeerd met behulp van Azure Functions, met behulp van de [Event hubs trigger](../azure-functions/functions-bindings-event-hubs-trigger.md) voor het ophalen van gebeurtenissen en de [Event hub-uitvoer binding](../azure-functions/functions-bindings-event-hubs-output.md) voor het leveren van deze.

## <a name="routing"></a>Routering

Het routerings patroon bouwt voort op het [replicatie](#replication) patroon, maar niet met één bron en één doel, de replicatie taak heeft meerdere doelen, die hier worden geïllustreerd in C#:

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest1", Connection = "EventHubConnectionAppSetting")] EventHubClient output1,
    [EventHub("dest2", Connection = "EventHubConnectionAppSetting")] EventHubClient output2,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // send to output1 and/or output2 based on criteria
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output1, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2==0 ) ? inputEvent : null;
        });
        EventHubReplicationTasks.ConditionalForwardToEventHub(input, output2, log, (eventData) => {
            return ( inputEvent.SystemProperties.SequenceNumber%2!=0 ) ? inputEvent : null;
        });
    }
}
```

De routerings functie houdt rekening met de meta gegevens van het bericht en/of de bericht lading en kiest vervolgens een van de beschik bare bestemmingen voor verzen ding naar.

In Azure Stream Analytics kunt u hetzelfde doen met het definiëren van meerdere uitvoer en vervolgens het uitvoeren van een query per uitvoer.

```sql
select * into dest1Output from inputSource where Info = 1
select * into dest2Output from inputSource where Info = 2
```


## <a name="log-projection"></a>Projectie van logboek

Het logboek projectie patroon wordt de gebeurtenis stroom afgevlakt op een geïndexeerde data base, waarbij gebeurtenissen records worden in de-data base. Normaal gesp roken worden gebeurtenissen toegevoegd aan dezelfde verzameling of tabel, en de Event hub-partitie sleutel wordt partij bij de primaire sleutel die op zoek is naar de record uniek maken.

De projectie van het logboek kan een historian van uw gebeurtenis gegevens of een gecomprimeerde weer gave produceren, waarbij alleen de meest recente gebeurtenis voor elke partitie sleutel wordt bewaard. De vorm van de doel database is uiteindelijk aan u en de behoeften van uw toepassing. Dit patroon wordt ook wel ' event sourcing ' genoemd.

> [!TIP]
> U kunt eenvoudig logboek-projecties maken in [Azure SQL database](../stream-analytics/sql-database-output.md) en [Azure Cosmos DB](../stream-analytics/azure-cosmos-db-output.md) in azure stream Analytics en u hebt de voor keur.

Met de volgende Azure-functie wordt de inhoud van een event hub gecomprimeerd in een Azure CosmosDB-verzameling.

```C#
[FunctionName("Eh1ToCosmosDb1Json")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static async Task Eh1ToCosmosDb1Json(
    [EventHubTrigger("eh1", ConsumerGroup = "Eh1ToCosmosDb1", Connection = "Eh1ToCosmosDb1-source-connection")] EventData[] input,
    [CosmosDB(databaseName: "SampleDb", collectionName: "foo", ConnectionStringSetting = "CosmosDBConnection")] IAsyncCollector<object> output,
    ILogger log)
{
    foreach (var ev in input)
    {
        if (!string.IsNullOrEmpty(ev.SystemProperties.PartitionKey))
        {
            var record = new
            {
                id = ev.SystemProperties.PartitionKey,
                data = JsonDocument.Parse(ev.Body),
                properties = ev.Properties
            };
            await output.AddAsync(record);
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen

- [Gebeurtenis-Replicator toepassingen in Azure Functions][1]
- [Gebeurtenissen tussen Event Hubs repliceren][2]
- [Gebeurtenissen repliceren naar Azure Service Bus][3]

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
