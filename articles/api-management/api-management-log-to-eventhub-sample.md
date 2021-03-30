---
title: Api's bewaken met Azure API Management, Event Hubs en Moesif
titleSuffix: Azure API Management
description: Voorbeeld toepassing waarbij het logboek-naar-eventhub-beleid wordt gedemonstreerd door verbinding te maken met Azure API Management, Azure Event Hubs en Moesif voor HTTP-logboek registratie en-bewaking
services: api-management
documentationcenter: ''
author: darrelmiller
manager: erikre
editor: ''
ms.assetid: c528cf6f-5f16-4a06-beea-fa1207541a47
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 01/23/2018
ms.author: apimpm
ms.openlocfilehash: abb9cbb73f8957cec2cb3240bbf186623b9b2ef9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88205504"
---
# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-moesif"></a>Bewaak uw Api's met Azure API Management, Event Hubs en Moesif
De [API Management-service](api-management-key-concepts.md) biedt veel mogelijkheden voor het verbeteren van de verwerking van HTTP-aanvragen die worden verzonden naar uw HTTP-API. Het bestaan van de aanvragen en antwoorden is echter tijdelijk. De aanvraag wordt gemaakt en de API Management-service wordt door gegeven aan uw back-end-API. Uw API verwerkt de aanvraag en een antwoord loopt terug via de API-Consumer. De API Management-service houdt enkele belang rijke statistische gegevens over de Api's bij om weer te geven in het Azure Portal dash board, maar de details zijn verdwenen.

Door gebruik te maken van het logboek-naar-eventhub-beleid in de API Management-service, kunt u de gegevens van de aanvraag en het antwoord verzenden naar een [Azure Event hub](../event-hubs/event-hubs-about.md). Er zijn verschillende redenen waarom u mogelijk gebeurtenissen wilt genereren van HTTP-berichten die naar uw Api's worden verzonden. Enkele voor beelden zijn onder andere de audittrail van updates, gebruiks analyses, uitzonderings waarschuwingen en integraties van derden.

In dit artikel wordt beschreven hoe u het hele HTTP-aanvraag-en-antwoord bericht vastlegt, verzendt naar een event hub en dat bericht vervolgens doorstuurt naar een service van derden die HTTP-logboek registratie en bewakings Services biedt.

## <a name="why-send-from-api-management-service"></a>Waarom verzenden van API Management service?
Het is mogelijk om HTTP-middleware te schrijven die kan worden aangesloten op HTTP API frameworks om HTTP-aanvragen en-antwoorden vast te leggen en ze in te voeren in Logboeken en bewakings systemen. Het nadeel van deze benadering is dat de HTTP-middleware moet worden geïntegreerd in de back-end-API en moet overeenkomen met het platform van de API. Als er meerdere Api's zijn, moet elk een van de middlewares zijn geïmplementeerd. Vaak zijn er redenen waarom back-end-Api's niet kunnen worden bijgewerkt.

Het gebruik van de Azure API Management-service om te integreren met de infra structuur voor logboek registratie biedt een gecentraliseerde en platform onafhankelijke oplossing. Het is ook schaalbaar, deels als gevolg van de [geo-replicatie](api-management-howto-deploy-multi-region.md) mogelijkheden van Azure API management.

## <a name="why-send-to-an-azure-event-hub"></a>Waarom verzenden naar een Azure Event hub?
Het is redelijk om u te vragen waarom een beleid maakt dat specifiek is voor Azure Event Hubs? Er zijn veel verschillende locaties waar ik mijn aanvragen mogelijk wil registreren. Waarom niet alleen de aanvragen rechtstreeks naar de uiteindelijke bestemming verzenden?  Dat is een optie. Bij het maken van logboek registratie aanvragen van een API Management-service is het echter nood zakelijk om te overwegen hoe logboek berichten van invloed zijn op de prestaties van de API. Geleidelijke toename van de belasting kan worden afgehandeld door de beschik bare exemplaren van systeem onderdelen te verg Roten of door gebruik te maken van geo-replicatie. Korte pieken in het verkeer kunnen er echter toe leiden dat aanvragen worden vertraagd als aanvragen voor logboek registratie van infra structuur worden vertraagd onder belasting.

De Azure Event Hubs is ontworpen om grote hoeveel heden gegevens te binnenkomen, met capaciteit voor het verwerken van veel meer gebeurtenissen dan het aantal HTTP-aanvragen van de meeste Api's. De Event hub fungeert als een soort geavanceerde buffer tussen uw API Management-service en de infra structuur die de berichten opslaat en verwerkt. Zo zorgt u ervoor dat de API-prestaties niet worden beïnvloed door de infra structuur voor logboek registratie.

Zodra de gegevens zijn door gegeven aan een event hub, wordt deze persistent gemaakt en wordt gewacht tot Event hub-gebruikers deze verwerken. De Event hub heeft geen betrekking op de manier waarop deze wordt verwerkt. het zorgt er dus voor dat het bericht goed wordt bezorgd.

Event Hubs kan gebeurtenissen streamen naar meerdere consumenten groepen. Hierdoor kunnen gebeurtenissen worden verwerkt door verschillende systemen. Hierdoor kunnen veel integratie scenario's worden ondersteund zonder dat de verwerking van de API-aanvraag binnen de API Management-service wordt vertraagd, omdat er maar één gebeurtenis moet worden gegenereerd.

## <a name="a-policy-to-send-applicationhttp-messages"></a>Een beleid voor het verzenden van toepassings-/HTTP-berichten
Een event hub accepteert gebeurtenis gegevens als een eenvoudige teken reeks. De inhoud van de teken reeks is aan u toe. Als u een HTTP-aanvraag wilt inpakken en naar Event Hubs wilt verzenden, moet u de teken reeks opmaken met de aanvraag-of antwoord gegevens. In de volgende situaties, als er een bestaande indeling is die we opnieuw kunnen gebruiken, is het wellicht niet mogelijk om onze eigen code te schrijven. In eerste instantie wordt gekeken naar het gebruik van de [har](http://www.softwareishard.com/blog/har-12-spec/) voor het verzenden van HTTP-aanvragen en-antwoorden. Deze indeling is echter geoptimaliseerd voor het opslaan van een reeks HTTP-aanvragen in een JSON-indeling. Het bevat een aantal verplichte elementen die onnodige complexiteit hebben toegevoegd voor het scenario van het door sturen van het HTTP-bericht over de kabel.

Een alternatieve optie was het `application/http` media type te gebruiken zoals beschreven in de HTTP-specificatie [RFC 7230](https://tools.ietf.org/html/rfc7230). Dit media type maakt gebruik van de exacte indeling die wordt gebruikt voor het daad werkelijk verzenden van HTTP-berichten via de kabel, maar het hele bericht kan worden geplaatst in de hoofd tekst van een andere HTTP-aanvraag. In ons geval gaan we de hoofd tekst gebruiken als ons bericht om naar Event Hubs te verzenden. In de [micro soft ASP.net Web API 2,2-client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) bibliotheken vindt u een parser die deze indeling kan parseren en converteren naar de systeem eigen `HttpRequestMessage` en- `HttpResponseMessage` objecten.

Om dit bericht te kunnen maken, moeten we profiteren van [beleids expressies](./api-management-policy-expressions.md) op basis van C# in azure API management. Dit is het beleid dat een HTTP-aanvraag bericht verzendt naar Azure Event Hubs.

```xml
<log-to-eventhub logger-id="conferencelogger" partition-id="0">
@{
   var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                               context.Request.Method,
                                               context.Request.Url.Path + context.Request.Url.QueryString);

   var body = context.Request.Body?.As<string>(true);
   if (body != null && body.Length > 1024)
   {
       body = body.Substring(0, 1024);
   }

   var headers = context.Request.Headers
                          .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                          .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                          .ToArray<string>();

   var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

   return "request:"   + context.Variables["message-id"] + "\n"
                       + requestLine + headerString + "\r\n" + body;
}
</log-to-eventhub>
```

### <a name="policy-declaration"></a>Beleids declaratie
Er zijn enkele specifieke dingen die van deze beleids expressie moeten worden vermeld. Het logboek-naar-eventhub-beleid heeft een kenmerk logger-id, dat verwijst naar de naam van de logboek registratie die in de API Management-service is gemaakt. De details over het instellen van een event hub-logger in de API Management-service vindt u in het document [How to logging events to azure Event hubs in azure API Management](api-management-howto-log-event-hubs.md). Het tweede kenmerk is een optionele para meter waarmee Event Hubs in welke partitie het bericht moet worden opgeslagen. Event Hubs gebruikt partities om de schaal baarheid in te scha kelen en mini maal twee te vereisen. De bestelde bezorging van berichten wordt alleen gegarandeerd binnen een partitie. Als er geen Event hub wordt geïnstrueerd in welke partitie het bericht moet worden geplaatst, wordt een Round Robin-algoritme gebruikt om de belasting te distribueren. Dit kan er echter toe leiden dat enkele berichten in de juiste volg orde worden verwerkt.

### <a name="partitions"></a>Partities
Om ervoor te zorgen dat onze berichten aan consumenten worden geleverd en dat ze profiteren van de capaciteit van de belasting verdeling van partities, heb ik ervoor gekozen om HTTP-aanvraag berichten te verzenden naar één partitie en HTTP-antwoord berichten naar een tweede partitie. Dit zorgt voor een gelijkmatige verdeling van de belasting en we kunnen garanderen dat alle aanvragen op volg orde worden verbruikt en dat alle antwoorden in de juiste volg orde worden verbruikt. Het is mogelijk dat een reactie vóór de bijbehorende aanvraag wordt verbruikt, maar omdat er geen probleem is, omdat er een ander mechanisme is voor het correleren van aanvragen naar antwoorden en we weten dat aanvragen altijd vóór antwoorden worden ontvangen.

### <a name="http-payloads"></a>HTTP-nettoladingen
Na het maken van de `requestLine` , kijken we of de aanvraag tekst moet worden afgekapt. De aanvraag tekst is afgekapt tot 1024. Dit kan worden verg root, maar afzonderlijke Event hub-berichten zijn beperkt tot 256 KB. het is dus waarschijnlijk dat sommige HTTP-bericht teksten niet in één bericht passen. Bij het uitvoeren van een logboek registratie en analyse van een aanzienlijke hoeveelheid informatie kan alleen worden afgeleid van de regel en de regels van de HTTP-aanvraag. Daarnaast kunnen veel Api's aanvragen alleen kleine lichamen retour neren, waardoor het verlies van de informatie waarde door grote instanties te verminderen in vergelijking met de reductie van overdracht, verwerking en opslag kosten om alle inhoud van de hoofd tekst te bewaren. Een laatste opmerking over het verwerken van de hoofd tekst is dat we `true` aan de methode moeten door geven `As<string>()` omdat we de inhoud van de hoofd tekst lezen, maar dat de back-end-API ook de hoofd tekst moet kunnen lezen. Als deze methode is ingesteld op True, wordt de hoofd tekst in de buffer opgeslagen zodat deze een tweede keer kan worden gelezen. Dit is belang rijk om rekening mee te houden als u een API hebt waarmee grote bestanden worden geüpload of lange polling wordt gebruikt. In dergelijke gevallen is het raadzaam om te voor komen dat de hoofd tekst helemaal wordt gelezen.

### <a name="http-headers"></a>HTTP-headers
HTTP-headers kunnen worden overgebracht naar de bericht indeling in een eenvoudige sleutel/waarde-paar notatie. We hebben ervoor gekozen bepaalde beveiligings gevoelige velden te verwijderen om te voor komen dat referentie gegevens onnodig worden gelekt. Het is niet waarschijnlijk dat de API-sleutels en andere referenties worden gebruikt voor analyse doeleinden. Als we analyses willen uitvoeren voor de gebruiker en het specifieke product dat ze gebruiken, kunnen we die van het `context` object ophalen en toevoegen aan het bericht.

### <a name="message-metadata"></a>Meta gegevens van bericht
Bij het bouwen van het volledige bericht dat naar de Event Hub wordt verzonden, maakt de eerste regel geen deel uit van het `application/http` bericht. De eerste regel is aanvullende meta gegevens die bestaan uit het feit of het bericht een aanvraag-of antwoord bericht is en een bericht-ID, die wordt gebruikt voor het correleren van aanvragen op antwoorden. De bericht-ID wordt gemaakt met behulp van een ander beleid dat er als volgt uitziet:

```xml
<set-variable name="message-id" value="@(Guid.NewGuid())" />
```

We hebben het aanvraag bericht gemaakt, opgeslagen in een variabele totdat het antwoord werd geretourneerd en vervolgens de aanvraag en het antwoord verzenden als één bericht. Als u de aanvraag en het antwoord onafhankelijk verzendt en een bericht-id gebruikt om de twee te correleren, krijgen we een beetje meer flexibiliteit in de bericht grootte, de mogelijkheid om te profiteren van meerdere partities, met het behoud van de volg orde van berichten en de aanvraag wordt weer gegeven in het logboek registratie dashboard eerder. Er kunnen ook enkele scenario's zijn waarbij een geldig antwoord nooit wordt verzonden naar de Event Hub, mogelijk als gevolg van een fatale aanvraag fout in de API Management-service, maar er is nog steeds een record van de aanvraag.

Het beleid voor het verzenden van het HTTP-antwoord bericht ziet er ongeveer uit als de aanvraag en daarom ziet de volledige beleids configuratie er als volgt uit:

```xml
<policies>
  <inbound>
      <set-variable name="message-id" value="@(Guid.NewGuid())" />
      <log-to-eventhub logger-id="conferencelogger" partition-id="0">
      @{
          var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                      context.Request.Method,
                                                      context.Request.Url.Path + context.Request.Url.QueryString);

          var body = context.Request.Body?.As<string>(true);
          if (body != null && body.Length > 1024)
          {
              body = body.Substring(0, 1024);
          }

          var headers = context.Request.Headers
                               .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                               .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                               .ToArray<string>();

          var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

          return "request:"   + context.Variables["message-id"] + "\n"
                              + requestLine + headerString + "\r\n" + body;
      }
  </log-to-eventhub>
  </inbound>
  <backend>
      <forward-request follow-redirects="true" />
  </backend>
  <outbound>
      <log-to-eventhub logger-id="conferencelogger" partition-id="1">
      @{
          var statusLine = string.Format("HTTP/1.1 {0} {1}\r\n",
                                              context.Response.StatusCode,
                                              context.Response.StatusReason);

          var body = context.Response.Body?.As<string>(true);
          if (body != null && body.Length > 1024)
          {
              body = body.Substring(0, 1024);
          }

          var headers = context.Response.Headers
                                          .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                          .ToArray<string>();

          var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

          return "response:"  + context.Variables["message-id"] + "\n"
                              + statusLine + headerString + "\r\n" + body;
     }
  </log-to-eventhub>
  </outbound>
</policies>
```

Het `set-variable` beleid maakt een waarde die toegankelijk is via het `log-to-eventhub` beleid in de `<inbound>` sectie en in de `<outbound>` sectie.

## <a name="receiving-events-from-event-hubs"></a>Gebeurtenissen ontvangen van Event Hubs
Gebeurtenissen van Azure Event hub worden ontvangen via het [AMQP-protocol](https://www.amqp.org/). Het micro soft Service Bus-team heeft client bibliotheken beschikbaar gemaakt om het verbruik van gebeurtenissen gemakkelijker te maken. Er worden twee verschillende benaderingen ondersteund, een is een *directe Consumer* en de andere is van de- `EventProcessorHost` klasse. Voor beelden van deze twee benaderingen vindt u in de [Event hubs-programmeer gids](../event-hubs/event-hubs-programming-guide.md). De korte versie van de verschillen is, `Direct Consumer` geeft u volledige controle en het `EventProcessorHost` biedt een deel van het sanitaire werk voor u, maar maakt bepaalde veronderstellingen over hoe u deze gebeurtenissen verwerkt.

### <a name="eventprocessorhost"></a>EventProcessorHost
In dit voor beeld gebruiken we de `EventProcessorHost` voor-eenvoud, maar dit is mogelijk niet de beste keuze voor dit specifieke scenario. `EventProcessorHost` is het moeilijk om ervoor te zorgen dat u zich geen zorgen hoeft te maken over thread problemen binnen een bepaalde gebeurtenis verwerkings klasse. In ons scenario wordt het bericht echter geconverteerd naar een andere indeling en door gegeven aan een andere service met behulp van een async-methode. Het is niet nodig om de gedeelde status bij te werken en daarom geen risico voor threading-problemen. Voor de meeste scenario's `EventProcessorHost` is het waarschijnlijk de beste keuze en het is zeker de makkelijkste optie.

### <a name="ieventprocessor"></a>IEventProcessor
Het centrale concept bij het gebruik `EventProcessorHost` van is het maken van een implementatie van de `IEventProcessor` Interface, die de methode bevat `ProcessEventAsync` . De essentie van deze methode wordt hier weer gegeven:

```csharp
async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
{

    foreach (EventData eventData in messages)
    {
        _Logger.LogInfo(string.Format("Event received from partition: {0} - {1}", context.Lease.PartitionId,eventData.PartitionKey));

        try
        {
            var httpMessage = HttpMessage.Parse(eventData.GetBodyStream());
            await _MessageContentProcessor.ProcessHttpMessage(httpMessage);
        }
        catch (Exception ex)
        {
            _Logger.LogError(ex.Message);
        }
    }
    ... checkpointing code snipped ...
}
```

Een lijst met Event data-objecten wordt door gegeven aan de-methode en de lijst wordt herhaald. De bytes van elke methode worden geparseerd naar een HttpMessage-object en dat object wordt door gegeven aan een exemplaar van IHttpMessageProcessor.

### <a name="httpmessage"></a>HttpMessage
Het `HttpMessage` exemplaar bevat drie stukjes gegevens:

```csharp
public class HttpMessage
{
    public Guid MessageId { get; set; }
    public bool IsRequest { get; set; }
    public HttpRequestMessage HttpRequestMessage { get; set; }
    public HttpResponseMessage HttpResponseMessage { get; set; }

... parsing code snipped ...

}
```

Het `HttpMessage` exemplaar bevat een `MessageId` GUID waarmee de HTTP-aanvraag kan worden verbonden met het bijbehorende HTTP-antwoord en een Booleaanse waarde die aangeeft of het object een exemplaar van een HttpRequestMessage en HttpResponseMessage bevat. Door gebruik te maken van de ingebouwde HTTP-klassen van kan `System.Net.Http` ik profiteren van de code voor `application/http` parseren die is opgenomen in `System.Net.Http.Formatting` .  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
Het `HttpMessage` exemplaar wordt vervolgens doorgestuurd naar de implementatie van `IHttpMessageProcessor` . Dit is een interface die is gemaakt om de ontvangst en interpretatie van de gebeurtenis van Azure Event hub en de daad werkelijke verwerking ervan te ontkoppelen.

## <a name="forwarding-the-http-message"></a>Het HTTP-bericht door sturen
Voor dit voor beeld heeft ik besloten dat het HTTP-verzoek moet worden gepusht naar [MOESIF API Analytics](https://www.moesif.com). Moesif is een Cloud service die is gespecialiseerd in HTTP-analyse en fout opsporing. Ze hebben een gratis laag, dus het kan eenvoudig worden geprobeerd om de HTTP-aanvragen in realtime te laten lopen via onze API Management service.

De `IHttpMessageProcessor` implementatie ziet er als volgt uit:

```csharp
public class MoesifHttpMessageProcessor : IHttpMessageProcessor
{
    private readonly string RequestTimeName = "MoRequestTime";
    private MoesifApiClient _MoesifClient;
    private ILogger _Logger;
    private string _SessionTokenKey;
    private string _ApiVersion;
    public MoesifHttpMessageProcessor(ILogger logger)
    {
        var appId = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-APP-ID", EnvironmentVariableTarget.Process);
        _MoesifClient = new MoesifApiClient(appId);
        _SessionTokenKey = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-SESSION-TOKEN", EnvironmentVariableTarget.Process);
        _ApiVersion = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-API-VERSION", EnvironmentVariableTarget.Process);
        _Logger = logger;
    }

    public async Task ProcessHttpMessage(HttpMessage message)
    {
        if (message.IsRequest)
        {
            message.HttpRequestMessage.Properties.Add(RequestTimeName, DateTime.UtcNow);
            return;
        }

        EventRequestModel moesifRequest = new EventRequestModel()
        {
            Time = (DateTime) message.HttpRequestMessage.Properties[RequestTimeName],
            Uri = message.HttpRequestMessage.RequestUri.OriginalString,
            Verb = message.HttpRequestMessage.Method.ToString(),
            Headers = ToHeaders(message.HttpRequestMessage.Headers),
            ApiVersion = _ApiVersion,
            IpAddress = null,
            Body = message.HttpRequestMessage.Content != null ? System.Convert.ToBase64String(await message.HttpRequestMessage.Content.ReadAsByteArrayAsync()) : null,
            TransferEncoding = "base64"
        };

        EventResponseModel moesifResponse = new EventResponseModel()
        {
            Time = DateTime.UtcNow,
            Status = (int) message.HttpResponseMessage.StatusCode,
            IpAddress = Environment.MachineName,
            Headers = ToHeaders(message.HttpResponseMessage.Headers),
            Body = message.HttpResponseMessage.Content != null ? System.Convert.ToBase64String(await message.HttpResponseMessage.Content.ReadAsByteArrayAsync()) : null,
            TransferEncoding = "base64"
        };

        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApimMessageId", message.MessageId.ToString());

        EventModel moesifEvent = new EventModel()
        {
            Request = moesifRequest,
            Response = moesifResponse,
            SessionToken = _SessionTokenKey != null ? message.HttpRequestMessage.Headers.GetValues(_SessionTokenKey).FirstOrDefault() : null,
            Tags = null,
            UserId = null,
            Metadata = metadata
        };

        Dictionary<string, string> response = await _MoesifClient.Api.CreateEventAsync(moesifEvent);

        _Logger.LogDebug("Message forwarded to Moesif");
    }

    private static Dictionary<string, string> ToHeaders(HttpHeaders headers)
    {
        IEnumerable<KeyValuePair<string, IEnumerable<string>>> enumerable = headers.GetEnumerator().ToEnumerable();
        return enumerable.ToDictionary(p => p.Key, p => p.Value.GetEnumerator()
                                                         .ToEnumerable()
                                                         .ToList()
                                                         .Aggregate((i, j) => i + ", " + j));
    }
}
```

De voor `MoesifHttpMessageProcessor` delen van een [C# API library voor Moesif](https://www.moesif.com/docs/api?csharp#events) , waarmee u eenvoudig http-gebeurtenis gegevens in hun service kunt pushen. Als u HTTP-gegevens wilt verzenden naar de Moesif Collector-API, hebt u een account en een toepassings-id nodig. U krijgt een Moesif-toepassings-id door een account te maken op de [website van Moesif](https://www.moesif.com) en vervolgens naar de _meest rechter menu_-  ->  _app-installatie_ te gaan.

## <a name="complete-sample"></a>Voor beeld volt ooien
De [bron code](https://github.com/dgilling/ApimEventProcessor) en tests voor het voor beeld bevinden zich op github. U hebt een [API Management-service](get-started-create-service-instance.md), [een verbonden Event hub](api-management-howto-log-event-hubs.md)en een [opslag account](../storage/common/storage-account-create.md) nodig om het voor beeld voor uzelf uit te voeren.   

Het voor beeld is slechts een eenvoudige console toepassing die luistert naar gebeurtenissen die afkomstig zijn van Event hub, converteert ze naar een Moesif `EventRequestModel` en `EventResponseModel` objecten en stuurt ze vervolgens door naar de MOESIF Collector API.

In de volgende afbeelding met animatie ziet u een aanvraag voor een API in de ontwikkelaars Portal, de console toepassing met het bericht dat wordt ontvangen, verwerkt en doorgestuurd, en vervolgens de aanvraag en het antwoord dat wordt weer gegeven in de gebeurtenis stroom.

![Demonstratie van aanvraag die wordt doorgestuurd naar Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Samenvatting
Azure API Management service biedt een ideale plaats voor het vastleggen van het HTTP-verkeer van en naar uw Api's. Azure Event Hubs is een uiterst schaal bare oplossing met lage kosten voor het vastleggen van het verkeer en het in te voeren van gegevens in secundaire verwerkings systemen voor logboek registratie, bewaking en andere geavanceerde analyses. Verbinding maken met bewakings systemen van derden, zoals Moesif, is net zo eenvoudig als een paar tien regels code.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over Azure Event Hubs
  * [Aan de slag met Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Berichten ontvangen met EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
  * [Programmeer handleiding voor Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Meer informatie over de integratie van API Management en Event Hubs
  * [Gebeurtenissen vastleggen in azure Event Hubs in azure API Management](api-management-howto-log-event-hubs.md)
  * [Verwijzing naar traceer entiteit](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [Naslag informatie voor het aanmelden bij het eventhub-beleid](./api-management-advanced-policies.md#log-to-eventhub)
