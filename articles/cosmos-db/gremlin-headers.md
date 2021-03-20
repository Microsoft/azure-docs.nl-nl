---
title: Gremlin-antwoord headers Azure Cosmos DB
description: Referentie documentatie voor meta gegevens van server reacties die extra probleem oplossing mogelijk maken
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: christopheranderson
ms.author: chrande
ms.openlocfilehash: 0442d21aebe1cf577c50d14a5aeff40bd1f6cd9c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98600524"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Gremlin server-antwoord headers Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

In dit artikel worden headers behandeld die de Cosmos DB Gremlin-server retourneert naar de aanroeper bij het uitvoeren van de aanvraag. Deze headers zijn handig voor het oplossen van problemen met de prestaties van aanvragen, het bouwen van een toepassing die zonder benodigde systeemaanpassingen kan worden geïntegreerd met de Cosmos DB-service en het vereenvoudigen van de klantondersteuning.

Houd er rekening mee dat het afhankelijk maken van deze headers u de draag baarheid van uw toepassing beperkt tot andere Gremlin-implementaties. Als resultaat krijgt u een nauwere integratie met Cosmos DB Gremlin. Deze headers zijn geen TinkerPop-standaard.

## <a name="headers"></a>Kopteksten

| Header | Type | Voorbeeld waarde | Indien opgenomen | Uitleg |
| --- | --- | --- | --- | --- |
| **x-ms-request-charge** | double | 11,3243 | Geslaagde en mislukte bewerkingen | Hoeveelheid verzamelde of Data Base-door Voer in [aanvraag eenheden (ru/s of RUs)](request-units.md) voor een gedeeltelijk antwoord bericht. Deze header is bij elke voortzetting aanwezig voor aanvragen met meerdere segmenten. Het weerspiegelt de kosten van een bepaald segment van de reactie. Alleen voor aanvragen die bestaan uit een segment met één antwoord deze header komt overeen met de totale kosten van Trans Port. Voor het meren deel van complexe passages is deze waarde echter een gedeeltelijke kosten. |
| **x-ms-total-request-charge** | double | 423,987 | Geslaagde en mislukte bewerkingen | Hoeveelheid gebruikte verzameling of data base in [aanvraag eenheden (ru/s of RUs)](request-units.md) voor volledige aanvraag. Deze header is bij elke voortzetting aanwezig voor aanvragen met meerdere segmenten. Dit duidt op cumulatieve kosten sinds het begin van de aanvraag. De waarde van deze koptekst in de laatste chunk geeft aan dat de kosten voor de aanvraag volledig zijn. |
| **x-MS-server-time-MS** | double | 13,75 | Geslaagde en mislukte bewerkingen | Deze header is opgenomen voor het oplossen van problemen met de latentie. Hiermee wordt de hoeveelheid tijd, in milliseconden, aangegeven dat Cosmos DB Gremlin-server heeft gereageerd op het uitvoeren en genereren van een gedeeltelijk antwoord bericht. Door gebruik te maken van de waarde van deze header en te vergelijken met de totale latentie toepassingen van aanvragen kan de netwerk latentie overhead berekenen. |
| **x-MS-Total-Server-time-MS** | double | 130,512 | Geslaagde en mislukte bewerkingen | De totale tijd, in milliseconden, dat Cosmos DB Gremlin-server het uitvoeren van de volledige navigatie heeft geduurd. Deze header is opgenomen in elke gedeeltelijke reactie. Dit vertegenwoordigt een cumulatieve uitvoerings tijd sinds het begin van de aanvraag. Het laatste antwoord duidt op de totale uitvoerings tijd. Deze header is handig om onderscheid te maken tussen client en server als een bron van latentie. U kunt de doorlopende uitvoerings tijd op de client vergelijken met de waarde van deze header. |
| **x-ms-status-code** | long | 200 | Geslaagde en mislukte bewerkingen | Kop geeft interne reden aan voor het volt ooien of beëindigen van aanvragen. De toepassing wordt aangeraden de waarde van deze header te bekijken en corrigerende actie te ondernemen. |
| **x-MS-substatus-code** | long | 1003 | Alleen fout | Cosmos DB is een Data Base met meerdere modellen die zich boven op een uniforme opslaglaag bevindt. Deze header bevat extra inzichten over de fout reden wanneer de fout zich voordoet in de lagere lagen van de stack met hoge Beschik baarheid. Toepassing wordt aangeraden deze koptekst op te slaan en te gebruiken wanneer u contact opneemt met de klant ondersteuning van Cosmos DB. De waarde van deze koptekst is nuttig voor de Cosmos DB-engineer om snel problemen op te lossen. |
| **x-ms-retry-after-ms** | teken reeks (time span) | "00:00:03.9500000" | Alleen fout | Deze header is een teken reeks representatie van een .NET [time span](/dotnet/api/system.timespan) -type. Deze waarde wordt alleen opgenomen in mislukte aanvragen vanwege een overmatige doorvoer uitputting. De toepassing moet na een bepaalde tijd opnieuw door sturen opnieuw worden ingediend. |
| **x-ms-activity-id** | teken reeks (GUID) | "A9218E01-3A3A-4716-9636-5BD86B056613" | Geslaagde en mislukte bewerkingen | De header bevat een unieke server-side id van een aanvraag. Aan elke aanvraag wordt een unieke id toegewezen door de server voor het volgen van het doel. Toepassingen moeten activiteit-id's die door de server worden geretourneerd, registreren voor aanvragen waarvoor klanten mogelijk contact willen opnemen met de klant ondersteuning. Cosmos DB ondersteunings medewerkers kunnen specifieke aanvragen voor deze id's vinden in Cosmos DB telemetrie van de service. |

## <a name="status-codes"></a>Statuscodes

De meest voorkomende codes die `x-ms-status-code` door de server worden geretourneerd voor het status kenmerk, worden hieronder weer gegeven.

| Status | Uitleg |
| --- | --- |
| **401** | Er wordt een fout bericht weer `"Unauthorized: Invalid credentials provided"` gegeven wanneer het verificatie wachtwoord niet overeenkomt met Cosmos DB account sleutel. Navigeer naar uw Cosmos DB Gremlin-account in de Azure Portal en controleer of de sleutel juist is.|
| **404** | Gelijktijdige bewerkingen die tegelijkertijd proberen dezelfde rand of hoek punt te verwijderen en bij te werken. Foutbericht `"Owner resource does not exist"` geeft aan dat de opgegeven database of verzameling in de verbindingsparameters in de `/dbs/<database name>/colls/<collection or graph name>`-indeling onjuist is.|
| **409** | `"Conflicting request to resource has been attempted. Retry to avoid conflicts."` Dit gebeurt meestal wanneer een hoekpunt of rand met een id al in de graaf bestaat.| 
| **412** | De status code wordt aangevuld met het fout bericht `"PreconditionFailedException": One of the specified pre-condition is not met` . Deze fout wordt weer gegeven bij een optimistische gelijktijdigheids schending van het lezen van een rand of hoek punt en het terugschrijven naar het archief na wijziging. De meest voorkomende situaties waarin deze fout optreedt, zijn bijvoorbeeld eigenschaps wijzigingen `g.V('identifier').property('name','value')` . De Gremlin-engine zou het hoek punt lezen, wijzigen en terugschrijven. Als er een andere Pass-bewerking gelijktijdig wordt uitgevoerd om hetzelfde hoek punt of een rand te schrijven, wordt deze fout weer gegeven. De toepassing moet opnieuw naar de server worden verzonden.| 
| **429** | De aanvraag is beperkt en moet opnieuw worden uitgevoerd na een waarde in **x-MS-retry-na-MS**| 
| **500** | Een foutbericht dat `"NotFoundException: Entity with the specified id does not exist in the system."` bevat, geeft aan dat een database en/of collectie opnieuw is gemaakt met dezelfde naam. De fout verdwijnt binnen vijf minuten, aangezien wijzigingen caches in verschillende Cosmos DB-onderdelen niet doorgeven en ongeldig maken. Als u dit probleem wilt voorkomen, gebruikt u telkens een unieke database en collectie.| 
| **1000** | Deze status code wordt geretourneerd wanneer de server een bericht heeft geparseerd, maar niet kan worden uitgevoerd. Doorgaans duidt dit op een probleem met de query.| 
| **1001** | Deze code wordt geretourneerd wanneer de server wordt gepasseerd, maar de reactie op de client kan niet worden geserialiseerd. Deze fout kan optreden als het door lopen van een complex resultaat wordt gegenereerd, dat te groot is of niet voldoet aan de TinkerPop-protocol specificatie. De toepassing moet het door lopen vereenvoudigen wanneer deze fout optreedt. | 
| **1003** | `"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"` wordt geretourneerd wanneer de toegestane geheugen limiet wordt overschreden. De geheugen limiet is **2 GB** per Trans Port.| 
| **1004** | Deze status code geeft de onjuiste grafiek aanvraag aan. De aanvraag kan een onjuiste indeling hebben wanneer het deserialiseren mislukt, het type zonder waarde wordt gedeserialiseerd als waardetype of niet-ondersteunde Gremlin bewerking aangevraagd. De toepassing mag de aanvraag niet opnieuw uitvoeren omdat deze niet is geslaagd. | 
| **1007** | Normaal gesp roken wordt deze status code geretourneerd met het fout bericht `"Could not process request. Underlying connection has been closed."` . Deze situatie kan zich voordoen als het client stuur programma een verbinding probeert te gebruiken die door de server wordt gesloten. De toepassing moet het door lopen van een andere verbinding opnieuw proberen.
| **1008** | Cosmos DB Gremlin-server kan de verbindingen beëindigen voor het opnieuw verdelen van verkeer in het cluster. Client Stuur Programma's moeten deze situatie afhandelen en alleen live-verbindingen gebruiken om aanvragen naar de server te verzenden. Af en toe kunnen client Stuur Programma's niet detecteren dat de verbinding is verbroken. Wanneer er een fout optreedt in een toepassing, `"Connection is too busy. Please retry after sometime or open more connections."` moet deze opnieuw worden gepasseerd op een andere verbinding.
| **1009** | De bewerking is niet voltooid binnen de toegewezen tijd en is geannuleerd door de server. Optimaliseer uw door voeren om snel uit te voeren door hoek punten of randen te filteren op elke hop van het door lopen naar een beperkt zoek bereik. De standaard time-outwaarde is **60 seconden**. |

## <a name="samples"></a>Voorbeelden

Een voor beeld-client toepassing op basis van Gremlin.Net die één status kenmerk leest:

```csharp
// Following example reads a status code and total request charge from server response attributes.
// Variable "server" is assumed to be assigned to an instance of a GremlinServer that is connected to Cosmos DB account.
using (GremlinClient client = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
{
  ResultSet<dynamic> responseResultSet = await GremlinClientExtensions.SubmitAsync<dynamic>(client, requestScript: "g.V().count()");
  long statusCode = (long)responseResultSet.StatusAttributes["x-ms-status-code"];
  double totalRequestCharge = (double)responseResultSet.StatusAttributes["x-ms-total-request-charge"];

  // Status code and request charge are logged into application telemetry.
}
```

Een voor beeld waarin wordt getoond hoe u het kenmerk status leest van Gremlin Java-client:

```java
try {
  ResultSet resultSet = this.client.submit("g.addV().property('id', '13')");
  List<Result> results = resultSet.all().get();

  // Process and consume results

} catch (ResponseException re) {
  // Check for known errors that need to be retried or skipped
  if (re.getStatusAttributes().isPresent()) {
    Map<String, Object> attributes = re.getStatusAttributes().get();
    int statusCode = (int) attributes.getOrDefault("x-ms-status-code", -1);

    // Now we can check for specific conditions
    if (statusCode == 409) {
        // Handle conflicting writes
      }
    }

    // Check if we need to delay retry
    if (attributes.containsKey("x-ms-retry-after-ms")) {
      // Read the value of the attribute as is
      String retryAfterTimeSpan = (String) attributes.get("x-ms-retry-after-ms"));

      // Convert the value into actionable duration
            LocalTime locaTime = LocalTime.parse(retryAfterTimeSpan);
            Duration duration = Duration.between(LocalTime.MIN, locaTime);

      // Perform a retry after "duration" interval of time has elapsed
    }
  }
}

```

## <a name="next-steps"></a>Volgende stappen
* [HTTP-status codes voor Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 
* [Algemene Azure Cosmos DB REST-reactie headers](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [TinkerPop Graph driver provider-vereisten]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)
