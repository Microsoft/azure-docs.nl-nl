---
title: Taak patronen voor bericht replicatie-Azure Service Bus | Microsoft Docs
description: In dit artikel vindt u gedetailleerde richt lijnen voor het implementeren van specifieke taak patronen voor bericht replicatie
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: d823ee7ccd4f53bfc3e10211a4f44908273a110d
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657410"
---
# <a name="message-replication-tasks-patterns"></a>Patronen voor bericht replicatie taken

In het overzicht van de [Federatie](service-bus-federation-overview.md) en de [Replicator functies](service-bus-federation-replicator-functions.md) wordt uitgelegd wat de reden is voor en de basis elementen van replicatie taken. u wordt aangeraden deze te verkennen voordat u verdergaat met dit artikel.

In dit artikel beschrijven we implementatie richtlijnen voor verschillende patronen die in de sectie overzicht zijn gemarkeerd. 

## <a name="replication"></a>Replicatie 

Het replicatie patroon kopieert berichten van een wachtrij of onderwerp naar de volgende, of van een wachtrij of onderwerp naar een andere bestemming, zoals een event hub. De berichten worden doorgestuurd zonder dat er wijzigingen in de bericht lading worden aangebracht. 

De implementatie van dit patroon wordt gedekt door de [bericht replicatie naar en van Azure service bus voor](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy) beeld.

### <a name="sequences-and-order-preservation"></a>Het behoud van reeksen en bestellingen

Het replicatie model heeft geen doel om de absolute volg orde van berichten van een bron wachtrij of onderwerp in een doel wachtrij of onderwerp te bewaren, maar zo nodig wordt gefocust, waarbij de relatieve volg orde van berichten wordt behouden waarin de toepassing deze vereist. De toepassing maakt dit mogelijk door sessie ondersteuning in te scha kelen voor de bron entiteit en gerelateerde berichten met dezelfde [sessie sleutel](message-sessions.md)te groeperen.

De sessie bewuste vooraf gebouwde replicatie functies zorgen ervoor dat bericht reeksen met dezelfde sessie-id die zijn opgehaald van een bron entiteit, worden verzonden naar de doel wachtrij of het onderwerp als een batch in de oorspronkelijke reeks en met dezelfde sessie-id. 

### <a name="service-assigned-metadata"></a>Aan de service toegewezen meta gegevens 

De aan de service toegewezen meta gegevens van een bericht dat is opgehaald uit de bron wachtrij of het onderwerp, de oorspronkelijke plaatsings tijd en het Volg nummer worden vervangen door nieuwe door de service toegewezen waarden in de doel wachtrij of het onderwerp, maar met de standaard replicatie taken die in onze voor beelden zijn opgenomen, worden de oorspronkelijke waarden bewaard in gebruikers eigenschappen: `repl-enqueue-time` (iso8601 String) en `repl-sequence` .

Deze eigenschappen zijn van het type teken reeks en bevatten de stringified-waarde van de respectieve oorspronkelijke eigenschappen.  Als het bericht meerdere keren wordt doorgestuurd, worden de aan de service toegewezen meta gegevens van de onmiddellijke bron toegevoegd aan de bestaande eigenschappen, waarbij de waarden worden gescheiden door punt komma's. 

### <a name="failover"></a>Failover

Als u replicatie gebruikt voor herstel na nood gevallen, om te beveiligen tegen regionale beschikbaarheids berichten in de Service Bus-service of tegen netwerk onderbrekingen, moet een dergelijk storings scenario een failover uitvoeren van de ene wachtrij of het onderwerp naar de volgende, waardoor de producenten en/of consumenten het secundaire eind punt kunnen gebruiken.

Voor alle failover-scenario's wordt ervan uitgegaan dat de vereiste elementen van de naam ruimten structureel identiek zijn, wat betekent dat wacht rijen en onderwerpen exact dezelfde naam hebben en dat de regels voor gedeelde toegangs handtekeningen en/of op rollen gebaseerde toegangs beheer regels op dezelfde manier worden ingesteld. U kunt een secundaire naam ruimte maken (en bijwerken) door de [richt lijnen te volgen voor het verplaatsen van naam ruimten](move-across-regions.md) en het weglaten van de opschoon stap.

Om producenten en consumenten te laten overschakelen, moet u de informatie over de naam ruimte die u wilt gebruiken, beschikbaar stellen op een locatie die gemakkelijk is te bereiken en bij te werken. Als producenten of consumenten zich veelvuldig of persistente fouten voordoen, moeten ze deze locatie raadplegen en hun configuratie aanpassen. Er zijn talloze manieren om deze configuratie te delen, maar we wijzen twee in het volgende: DNS-en bestands shares.

#### <a name="dns-based-failover-configuration"></a>Failover-configuratie op basis van DNS

Een van de kandidaten is de informatie in DNS SRV-records in een DNS die u beheert, te bewaren en te verwijzen naar de respectievelijke eind punten van de wachtrij of het onderwerp. Houd er rekening mee dat Message hubs niet toestaat dat de eind punten rechtstreeks zijn gekoppeld aan CNAME-records. Dit betekent dat u DNS gebruikt als een robuust zoek mechanisme voor eindpunt adressen en niet rechtstreeks IP-adres gegevens kan omzetten. 

Stel dat u eigenaar bent `example.com` van het domein en, voor uw toepassing, een zone `test.example.com` . Voor twee alternatieve Service Bus maakt u nu twee meer geneste zones en een SRV-record in elke. 

De SRV-records zijn de volgende algemene conventies, voorafgegaan door `_azure_servicebus._amqp` en bevatten twee eindpunt records: één voor AMQP-over-TLS op poort 5671 en één voor AMQP-over-Websockets op poort 443, waarbij beide verwijzen naar het service bus-eind punt van de naam ruimte die overeenkomt met de zone.

| Zone                 | SRV-record
|----------------------|-------------------------------------------------------------
| `sb1.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb1-test-example-com.servicebus.windows.net`<br>`2 2 443 sb1-test-example-com.servicebus.windows.net`
| `sb2.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb2-test-example-com.servicebus.windows.net`<br>`2 2 443 sb2-test-example-com.servicebus.windows.net`

In de zone van de toepassing maakt u vervolgens een CNAME-vermelding die verwijst naar de onderliggende zone die overeenkomt met uw primaire wachtrij of onderwerp:

| CNAME-record                 | Alias
|------------------------------|-------------------------------------------------------------
| `servicebus.test.example.com`  | `test1.test.example.com`

Het gebruik van een DNS-client waarmee u expliciet query's kunt uitvoeren op CNAME-en SRV-records (de ingebouwde clients van Java en .NET bieden alleen ondersteuning voor eenvoudige omzetting van namen naar IP-adressen), dan kunt u het gewenste eind punt oplossen. Met [DnsClient.net](https://dnsclient.michaco.net/)is de functie Lookup:

``` C#
static string GetServiceBusName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_servicebus._amqp.";
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

Deze procedure is vergelijkbaar met de manier waarop de [Service Bus geo-Dr](service-bus-geo-dr.md) werkt, maar volledig onder uw eigen beheer en werkt ook met actieve/actieve scenario's.

#### <a name="file-share-based-failover-configuration"></a>Failover-configuratie op basis van bestands share

Het eenvoudigste alternatief voor het gebruik van DNS voor het delen van eindpunt gegevens is het plaatsen van de naam van het primaire eind punt in een bestand met tekst zonder opmaak en het bestand van een infra structuur die robuust is tegen storingen en nog steeds updates toestaat. 

Als u al een Maxi maal beschik bare website infrastructuur met wereld wijde Beschik baarheid en inhouds replicatie hebt uitgevoerd, voegt u een dergelijk bestand toe en publiceert u het bestand opnieuw als u een schakel optie nodig hebt.

## <a name="merge"></a>Samenvoegen

Het samenvoeg patroon bevat een of meer replicatie taken die naar één doel wijzen, mogelijk gelijktijdig met gewone producenten, ook berichten verzenden naar hetzelfde doel. 

Variaties van dit patroon zijn:
- Twee of meer replicatie functies die gelijktijdig berichten verkrijgen van afzonderlijke bronnen en deze naar hetzelfde doel verzenden.
- Een meer replicatie functie voor het verkrijgen van berichten van een bron terwijl het doel ook rechtstreeks door de producenten wordt gebruikt. 
- Het vorige patroon, maar berichten die zijn gespiegeld tussen twee of meer onderwerpen, wat resulteert in de onderwerpen die dezelfde berichten bevatten, ongeacht waar berichten worden geproduceerd.

De eerste twee patroon variaties zijn trivial en verschillen niet van taken met een gewone replicatie.

Het laatste scenario vereist dat er geen gerepliceerde berichten meer worden gerepliceerd. De techniek wordt gedemonstreerd en uitgelegd in het voor beeld actief/actief.

## <a name="editor"></a>Editor

Het editor patroon is gebaseerd op het [replicatie](#replication) patroon, maar berichten worden gewijzigd voordat ze worden doorgestuurd. Voor beelden van dergelijke wijzigingen zijn:

- **_Transcode ring_*: als de inhoud van het bericht (ook ' hoofd tekst ' of ' payload ' genoemd) arriveert van de bron die is gecodeerd met de _Apache Avro *-indeling of met een eigen serialisatie-indeling, maar de verwachting van het systeem dat eigenaar is van het doel is dat de inhoud kan worden gedecodeerd, een *transcodeer* replicatie taak deserialisatie van *Apache Avro* eerst naar een object grafiek in het geheugen en vervolgens die grafiek serialiseren naar de *JSON* -indeling voor het bericht dat wordt doorgestuurd. Transcode ring omvat ook **inhouds compressie** en decompressie taken.
- **_Trans formatie_* _-berichten die gestructureerde gegevens bevatten, moeten mogelijk een andere vorm van die gegevens nodig hebben om het verbruik door downstream-consumenten te vergemakkelijken. Dit kan nodig zijn bij het afvlakken van geneste structuren, het weghalen van overbodige gegevens elementen of het aanpassen van de nettolading om precies aan een bepaald schema te voldoen.
- _*_Batching_*_ : berichten kunnen worden ontvangen in batches (meerdere berichten in één overdracht) van een bron, maar moeten afzonderlijk worden doorgestuurd naar een doel of andersom. Een taak kan daarom meerdere berichten door sturen op basis van één invoer bericht of een set berichten samen voegen die vervolgens samen worden overgebracht. 
- _*_Validatie_*_ -bericht gegevens uit externe bronnen moeten vaak worden gecontroleerd om te controleren of ze in overeenstemming zijn met een set regels voordat ze kunnen worden doorgestuurd. De regels kunnen worden uitgedrukt met behulp van schema's of code. berichten die niet in overeenstemming zijn gevonden, kunnen worden verwijderd, met het probleem dat in Logboeken wordt vermeld, of kunnen worden doorgestuurd naar een speciaal doel doel om ze verder af te handelen.   
- _*_Verrijking_*_ : bericht gegevens die afkomstig zijn van sommige bronnen, vereisen mogelijk verrijking met verdere context zodat deze kan worden gebruikt in doel systemen. Dit kan leiden tot het opzoeken van referentie gegevens en het insluiten van gegevens met het bericht, of het toevoegen van informatie over de bron die bekend is bij de replicatie taak, maar die niet is opgenomen in de berichten. 
- _*_Filteren_*_ : sommige berichten die binnenkomen vanaf een bron, moeten mogelijk worden inge houden van het doel op basis van een regel. Een filter test het bericht op basis van een regel en vermindert het bericht als het bericht niet overeenkomt met de regel. Dubbele berichten filteren door bepaalde criteria te bestuderen en volgende berichten met dezelfde waarden te verwijderen, is een vorm van filteren.
- _*_Route ring en partitionering_*_ : sommige replicatie taken kunnen twee of meer alternatieve doelen toestaan en regels definiëren waarvoor replicatie doel is gekozen voor een bepaald bericht op basis van de meta gegevens of de inhoud van het bericht. Een speciale vorm van route ring is partitioneren, waarbij de taak de partities in één replicatie doel expliciet toewijst op basis van regels.
- _*_Crypto grafie_*_ : een replicatie taak moet mogelijk inhoud ontsleutelen die arriveert vanuit de bron en/of de inhoud versleutelen die naar een doel wordt doorgestuurd en/of de integriteit van inhoud en meta gegevens ten opzichte van een hand tekening die in het bericht wordt uitgevoerd, kan controleren of een dergelijke hand tekening bijvoegen. 
- _*_Attestation_*_ : een replicatie taak kan meta gegevens (mogelijk beveiligd door een digitale hand tekening) koppelen aan een bericht dat verklaart dat het bericht is ontvangen via een specifiek kanaal of op een bepaald tijdstip.     
- _ *_Koppelen_**: een replicatie taak kan hand tekeningen Toep assen op reeksen van berichten, zodat de integriteit van de reeks wordt beveiligd en ontbrekende berichten kunnen worden gedetecteerd.

Al deze patronen kunnen worden geïmplementeerd met behulp van Azure Functions, waarbij de [Message hubs-trigger](../azure-functions/functions-bindings-service-bus-trigger.md) wordt gebruikt voor het ophalen van berichten en de [uitvoer binding van de wachtrij of het onderwerp](../azure-functions/functions-bindings-service-bus-output.md) voor het leveren van deze. 

## <a name="routing"></a>Routering

Het routerings patroon bouwt voort op het [replicatie](#replication) patroon, maar niet met één bron en één doel, de replicatie taak heeft meerdere doelen, die hier worden geïllustreerd in C#:

``` csharp
[FunctionName("SBRouter")]
public static async Task Run(
    [ServiceBusTrigger("source", Connection = "serviceBusConnectionAppSetting")] Message[] messages,
    [ServiceBus("dest1", Connection = "serviceBusConnectionAppSetting")] QueueClient output1,
    [ServiceBus("dest2", Connection = "serviceBusConnectionAppSetting")] QueueClient output2,
    ILogger log)
{
    foreach (Message messageData in messages)
    {
        // send to output1 or output2 based on criteria 
    }
}
```

De routerings functie houdt rekening met de meta gegevens van het bericht en/of de bericht lading en kiest vervolgens een van de beschik bare bestemmingen voor verzen ding naar.

## <a name="next-steps"></a>Volgende stappen

- [berichten Replicator-toepassingen in Azure Functions][1]
- [Berichten tussen Service Bus repliceren][2]
- [Berichten repliceren naar Azure Event Hubs][3]

[1]: service-bus-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub