---
title: Azure Cosmos DB SQL python API, SDK & resources
description: Meer informatie over de SQL python-API en SDK, inclusief release datums, pensioen datums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB python SDK.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-python
ms.openlocfilehash: 77cde4fb580ebea14c09856b9ad2e7f093e20db3
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505049"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Python SDK voor SQL API: Opmerkingen bij de release en resources
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core-SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET wijzigingenfeed-SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java-SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java-SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark-connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-resource provider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Bulk-uitvoerder-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulkuitvoerprogramma - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**SDK downloaden**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**API-documentatie**|[Naslag documentatie voor python API](/python/api/azure-cosmos/)|
|**SDK-installatie-instructies**|[Installatie-instructies voor python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)|
|**Aan de slag**|[Aan de slag met de python-SDK](create-sql-api-python.md)|
|**Huidig ondersteund platform**|[Python 2,7](https://www.python.org/downloads/) en [python 3.5.3 +](https://www.python.org/downloads/)|

## <a name="release-history"></a>Release geschiedenis

### <a name="410-2020-08-10"></a>4.1.0 (2020-08-10)

- Er is een waarschuwing voor afschaffing toegevoegd voor de indexerings modus ' Lazy '. Met de back-end kunnen geen containers meer worden gemaakt in deze modus en worden ze in plaats daarvan consistent ingesteld.

**Nieuwe functies**
- De mogelijkheid om de TTL voor de analytische opslag in te stellen bij het maken van een nieuwe container is toegevoegd.

**Opgeloste fouten**
- Ondersteuning voor dicteer functies als invoer voor get_client-Api's.
- Vaste python 2/3-compatibiliteit in query-iterators.
- Fout in Hint voor vast type (probleem #12570).
- Er is een fout opgelost waarbij opties niet zijn toegevoegd aan upsert_item functie. Probleem #11791: Hartelijk dank @aalapatirvbd .
- Er treedt een fout op wanneer een niet-teken reeks-ID in een item wordt gebruikt. Het activeert nu TypeError in plaats van AttributeError (probleem #11793).

### <a name="400"></a>4.0.0

* Stabiele release.
* HttpLoggingPolicy toegevoegd aan pijp lijn om in te scha kelen in een aangepaste logger voor aanvraag-en antwoord headers.

### <a name="400b6"></a>4.0.0 B6

* Er is een probleem opgelost in synchronized_request voor media-Api's.
* MediaReadMode en MediaRequestTimeout zijn verwijderd uit Connection Policy omdat media aanvragen niet worden ondersteund.

### <a name="400b5"></a>4.0.0 B5

* module Azure. Cosmos. Errors is afgeschaft en vervangen door Azure. Cosmos. Exceptions
* De para meters voor de toegangs voorwaarde ( `access_condition` , `if_match` , `if_none_match` ) zijn vervangen door afzonderlijke `match_condition` en `etag` para meters.
* Er is een probleem opgelost in de routerings toewijzings provider.
* De ondersteuning voor DISTINCT, offset en limieten voor query's is toegevoegd.
* Standaard context voor het uitvoeren van document query's wordt nu gebruikt voor

  * ChangeFeed query's
  * query's met één partitie (partitionkey, partitionKeyRangeId is aanwezig in opties)
  * Niet-document query's

* Er zijn fouten opgetreden voor aggregaties op meerdere partities, waarbij query voor kruis partitie inschakelen is ingesteld op True, maar er is geen "waarde"-tref woord aanwezig
* Query plan-eind punt treffers voor andere scenario's voor het ophalen van het query plan
* Er is `__repr__` ondersteuning toegevoegd voor Cosmos-entiteits objecten.
* Bijgewerkte documentatie.

### <a name="400b4"></a>4.0.0 B4

* Er is ondersteuning toegevoegd voor een `timeout` trefwoord argument voor alle bewerkingen om een absolute time-out in seconden op te geven waarbinnen de bewerking moet worden voltooid. Als de time-outwaarde wordt overschreden, wordt er een `azure.cosmos.errors.CosmosClientTimeoutError` gegenereerd.

* Er is een nieuw `ConnectionRetryPolicy` gedrag voor opnieuw proberen toegevoegd voor het beheren van pogingen tijdens HTTP-verbindings fouten.

* Nieuwe constructor en argumenten voor configuratie trefwoord per bewerking toegevoegd:

  * `retry_total` -Maximum aantal nieuwe pogingen.
  * `retry_backoff_max` -Maximale wacht tijd voor opnieuw proberen in seconden.
  * `retry_fixed_interval` -Vast interval voor opnieuw proberen in milliseconden.
  * `retry_read` -Maximum aantal sockets voor het lezen van nieuwe pogingen.
  * `retry_connect` -Maximum aantal nieuwe pogingen voor verbindings fouten.
  * `retry_status` -Maximum aantal nieuwe pogingen voor fout status codes.
  * `retry_on_status_codes` -Een lijst met specifieke status codes die u opnieuw wilt proberen.
  * `retry_backoff_factor` -Factor voor het berekenen van de wacht tijd tussen nieuwe pogingen.

### <a name="400b3"></a>4.0.0 B3

* `create_database_if_not_exists()` `create_container_if_not_exists` CosmosClient en Data Base worden respectievelijk toegevoegd en gefunctionaleerd.

### <a name="400b2"></a>4.0.0 B2

* Versie 4.0.0 B2 is de tweede iteratie in onze inspanningen voor het bouwen van een client bibliotheek die aansluit bij de aanbevolen procedures voor python-taal.

**Wijzigingen die fouten veroorzaken**

* De client verbinding is aangepast om de HTTP-pijp lijn te gebruiken die is gedefinieerd in `azure.core.pipeline` .

* De naam van interactieve objecten is nu gewijzigd in proxy's. Dit omvat:

  * `Database` -> `DatabaseProxy`
  * `User` -> `UserProxy`
  * `Container` -> `ContainerProxy`
  * `Scripts` -> `ScriptsProxy`

* De constructor van `CosmosClient` is bijgewerkt:

  * De `auth` naam van de para meter is gewijzigd in `credential` en er wordt nu rechtstreeks een verificatie type uitgevoerd. Dit betekent dat de waarde van de primaire sleutel, een woorden lijst met bron tokens of een lijst met machtigingen kan worden door gegeven. De oude woordenlijst indeling wordt echter nog steeds ondersteund.

  * De `connection_policy` para meter is een alleen-sleutel parameter gemaakt en hoewel deze nog steeds wordt ondersteund, kunnen alle afzonderlijke kenmerken van het beleid nu worden door gegeven als expliciete trefwoord argumenten:

    * `request_timeout`
    * `media_request_timeout`
    * `connection_mode`
    * `media_read_mode`
    * `proxy_config`
    * `enable_endpoint_discovery`
    * `preferred_locations`
    * `multiple_write_locations`

* Er is een nieuwe constructor toegevoegd om `CosmosClient` het maken in te scha kelen via een Connection String dat is opgehaald uit de Azure Portal.

* De `read_all` naam van sommige bewerkingen is gewijzigd in `list` bewerkingen:

  * `CosmosClient.read_all_databases` -> `CosmosClient.list_databases`
  * `Container.read_all_conflicts` -> `ContainerProxy.list_conflicts`
  * `Database.read_all_containers` -> `DatabaseProxy.list_containers`
  * `Database.read_all_users` -> `DatabaseProxy.list_users`
  * `User.read_all_permissions` -> `UserProxy.list_permissions`

* Alle bewerkingen die ondernemen `request_options` of `feed_options` para meters zijn, zijn verplaatst naar sleutel woorden alleen para meters. Hoewel deze opties worden ondersteund, worden alle afzonderlijke opties in de woorden lijst nu ondersteund als expliciete trefwoord argumenten.

* De fout hiërarchie is nu overgenomen van `azure.core.AzureError` :

  * De naam van `HTTPFailure` is gewijzigd in `CosmosHttpResponseError`
  * `JSONParseFailure` is verwijderd en vervangen door `azure.core.DecodeError`
  * Extra fouten toegevoegd voor specifieke antwoord codes:
    * `CosmosResourceNotFoundError` voor status 404
    * `CosmosResourceExistsError` voor status 409
    * `CosmosAccessConditionFailedError` voor status 412

* `CosmosClient` kan nu worden uitgevoerd in een context beheerder om het sluiten van de client verbinding te verwerken.

* Itereer Reacties (bijvoorbeeld query Responses en lijst reacties) zijn nu van het type `azure.core.paging.ItemPaged` . De methode `fetch_next_block` is vervangen door een secundaire iterator, waartoe de methode toegang heeft `by_page` .

### <a name="400b1"></a>4.0.0 B1

Versie 4.0.0 B1 is de eerste preview van onze inspanningen voor het maken van een gebruiks vriendelijke client bibliotheek die tegemoetkomt aan de aanbevolen procedures voor python-talen. Ga voor meer informatie over dit en Preview-versies van andere Azure SDK-bibliotheken naar https://aka.ms/azure-sdk-preview1-python .

**Belang rijke wijzigingen: nieuw API-ontwerp**

* Bewerkingen zijn nu gericht op een bepaalde client:

  * `CosmosClient`: Deze client verwerkt bewerkingen op account niveau. Dit omvat het beheren van service-eigenschappen en het weer geven van de data bases in een account.
  * `Database`: Deze client verwerkt bewerkingen op database niveau. Dit omvat het maken en verwijderen van containers, gebruikers en opgeslagen procedures. Deze kan worden geopend vanuit een cosmosClient-exemplaar op naam.
  * `Container`: Deze client verwerkt bewerkingen voor een bepaalde container. Dit omvat het uitvoeren van query's en het invoegen van items en het beheren van eigenschappen.
  * `User`: Deze client verwerkt bewerkingen voor een bepaalde gebruiker. Dit omvat het toevoegen en verwijderen van machtigingen en het beheren van gebruikers eigenschappen.

    Deze clients zijn toegankelijk door te navigeren naar de client hiërarchie met behulp van de `get_<child>_client` methode. Raadpleeg de [referentie documentatie](https://aka.ms/azsdk-python-cosmos-ref)voor volledige informatie over de nieuwe API.

* Clients hebben toegang tot de naam en niet op basis van de ID. U hoeft geen teken reeksen samen te voegen om koppelingen te maken.

* U hoeft geen typen en methoden meer te importeren uit afzonderlijke modules. De open bare API surface area is rechtstreeks beschikbaar in het `azure.cosmos` pakket.

* Afzonderlijke aanvraag eigenschappen kunnen worden gegeven als trefwoord argumenten in plaats van een afzonderlijke instantie te maken `RequestOptions` .

### <a name="302"></a>3.0.2

* Er is ondersteuning toegevoegd voor het data type multiveelhoek
* Fout oplossing in sessie beleid voor opnieuw proberen bij te lezen
* Fout oplossing voor onjuiste opvullings problemen tijdens het decoderen van basis teken reeksen van 64

### <a name="301"></a>3.0.1

* Fout oplossing in LocationCache
* Fout bij het herstellen van de logica van het eind punt
* Vaste documentatie

### <a name="300"></a>3.0.0

* Ondersteuning voor het schrijven van meerdere regio's is toegevoegd
* Naam wijzigingen
  * DocumentClient naar CosmosClient
  * Verzameling naar container
  * Document naar item
  * Pakket naam is bijgewerkt naar ' Azure-Cosmos '
  * De naam ruimte is bijgewerkt naar ' Azure. Cosmos '

### <a name="233"></a>2.3.3

* Ondersteuning voor proxy toegevoegd
* Ondersteuning toegevoegd voor het lezen van wijzigings invoer
* Ondersteuning toegevoegd voor verzamelings quotum headers
* Probleem met bugfix voor grote sessie-tokens
* Bugfix voor ReadMedia-API
* Bugfix in cache voor partitie sleutel bereik

### <a name="232"></a>2.3.2

* Er is ondersteuning toegevoegd voor standaard nieuwe pogingen bij verbindings problemen.

### <a name="231"></a>2.3.1

* Bijgewerkte documentatie om te verwijzen naar Azure Cosmos DB in plaats van met Azure DocumentDB.

### <a name="230"></a>2.3.0

* Voor deze SDK-versie is de nieuwste versie van Azure Cosmos DB emulator vereist om te kunnen downloaden van https://aka.ms/cosmosdb-emulator .

### <a name="221"></a>2.2.1

* bugfix voor geaggregeerde dict
* bugfix voor het verkleinen van schuine strepen in de resource koppeling
* testen op Unicode-code ring

### <a name="220"></a>2.2.0

* Er is ondersteuning toegevoegd voor de functie voor aanvraag eenheden per minuut (RU/m).
* Er is ondersteuning toegevoegd voor een nieuw consistentie niveau met de naam ConsistentPrefix.

### <a name="210"></a>2.1.0

* Er is ondersteuning toegevoegd voor aggregatie query's (aantal, MIN, MAX, SUM en AVG).
* Er is een optie toegevoegd voor het uitschakelen van SSL-verificatie wanneer deze wordt uitgevoerd op een DocumentDB-emulator.
* De beperking van de module dependent requests is verwijderd zodat deze precies 2.10.0 kan zijn.
* Minder minimale door Voer voor gepartitioneerde verzamelingen van 10.100 RU/s naar 2500 RU/s.
* Er is ondersteuning toegevoegd voor het inschakelen van script logboek registratie tijdens de uitvoering van de opgeslagen procedure.
* REST API versie die is gebump naar ' 2017-01-19 ' met deze release.

### <a name="201"></a>2.0.1

* Wijzigingen in documentatie zijn aangebracht.

### <a name="200"></a>2.0.0

* Er is ondersteuning toegevoegd voor python 3,5.
* Er is ondersteuning toegevoegd voor groepsgewijze verbindingen met behulp van de-module aanvragen.
* Er is ondersteuning toegevoegd voor de consistentie van sessies.
* Er is ondersteuning toegevoegd voor TOP/ORDERBY-query's voor gepartitioneerde verzamelingen.

### <a name="190"></a>1.9.0

* De ondersteuning voor het beleid voor opnieuw proberen is toegevoegd voor vertraagde aanvragen. (Vertraagde aanvragen ontvangen een aanvraag frequentie te grote uitzonde ring, fout code 429.) DocumentDB opnieuw proberen negen keer voor elke aanvraag Wanneer fout code 429 wordt aangetroffen, waarbij de retryAfter-tijd in de reactie header wordt gerespecteerd.
  Een vast interval voor opnieuw proberen kan nu worden ingesteld als onderdeel van de eigenschap RetryOptions van het object Connection Policy als u de retryAfter-tijd wilt negeren die door de server wordt geretourneerd tussen de nieuwe pogingen.
  DocumentDB wacht nu Maxi maal 30 seconden voor elke aanvraag die wordt beperkt (ongeacht het aantal nieuwe pogingen) en retourneert de reactie met fout code 429.
  Deze tijd kan ook worden overschreven in de eigenschap RetryOptions van het object Connection Policy.

* DocumentDB retourneert nu x-MS-Throttle-retry-Count en x-MS-Throttle-retry-Wait-Time-MS als de antwoord headers in elke aanvraag om het aantal nieuwe pogingen te geven en de cumulatieve tijd die de aanvraag tussen het opnieuw proberen.

* De RetryPolicy-klasse en de bijbehorende eigenschap (retry_policy) die op de document_client-klasse beschikbaar zijn gemaakt, zijn verwijderd en in plaats daarvan een RetryOptions-klasse geïntroduceerd waarmee de eigenschap RetryOptions op Connection Policy-klasse wordt weer gegeven die kan worden gebruikt om een aantal van de standaard opties voor opnieuw proberen te negeren.

### <a name="180"></a>1.8.0

* De ondersteuning voor geo-gerepliceerde database accounts is toegevoegd.
* Test oplossingen voor het verplaatsen van de globale host en hoofd sleutel naar de afzonderlijke test klassen.

### <a name="170"></a>1.7.0

* De ondersteuning voor time to Live (TTL)-functie voor documenten is toegevoegd.

### <a name="161"></a>1.6.1

* Oplossingen met betrekking tot partitioneren aan de server zijde om speciale tekens toe te staan in het pad van de partitie sleutel.

### <a name="160"></a>1.6.0

* Er is ondersteuning toegevoegd voor de functie gepartitioneerde verzamelingen aan de server zijde.

### <a name="150"></a>1.5.0

* Het sharding-Framework aan de client zijde is toegevoegd aan de SDK. Geïmplementeerde HashPartionResolver-en RangePartitionResolver-klassen.

### <a name="142"></a>1.4.2

* Implementeer Upsert. Er zijn nieuwe UpsertXXX-methoden toegevoegd ter ondersteuning van de functie Upsert.
* Implementeer ID-Based route ring. Geen wijzigingen in de open bare API, alle wijzigingen intern.

### <a name="130"></a>1.3.0

* Release is overgeslagen om het versie nummer in overeenstemming te brengen met andere Sdk's

### <a name="120"></a>1.2.0

* Ondersteunt georuimtelijke index.
* Valideert de eigenschap ID voor alle resources. Id's voor resources mogen geen `?, /, #, \\` tekens bevatten of eindigen met een spatie.
* Hiermee wordt de voortgang van de index transformatie van de nieuwe header toegevoegd aan ResourceResponse.

### <a name="110"></a>1.1.0

* Implementeert v2-indexerings beleid

### <a name="101"></a>1.0.1

* Ondersteunt proxy verbinding

## <a name="release--retirement-dates"></a>Uittredings datums &

Micro soft biedt een kennisgeving van ten minste **twaalf maanden** voor het buiten gebruik stellen van een SDK om de overgang naar een nieuwere/ondersteunde versie te versoepelen. Nieuwe functies en functionaliteiten en optimaliseringen worden alleen toegevoegd aan de huidige SDK. Het wordt daarom aangeraden altijd zo snel mogelijk een upgrade naar de nieuwste SDK-versie uit te voeren.

> [!WARNING]
> Na 31 augustus 2022 doen Azure Cosmos DB geen problemen meer oplossen of bieden we ondersteuning voor versie 1. x en 2. x van de Azure Cosmos DB python SDK voor SQL API. Als u liever geen upgrade uitvoert, worden aanvragen die zijn verzonden vanaf versie 1. x en 2. x van de SDK nog steeds door de Azure Cosmos DB-service geleverd.

| Versie | Releasedatum | Buitengebruikstellingsdatum |
| --- | --- | --- |
| [4.0.0](#400) |20 mei 2020 |--- |
| [3.0.2](#302) |15 november 2018 |--- |
| [3.0.1](#301) |Okt 04, 2018 |--- |
| [2.3.3](#233) |Sept 08, 2018 |31 augustus 2022 |
| [2.3.2](#232) |08 mei 2018 |31 augustus 2022 |
| [2.3.1](#231) |21 december 2017 |31 augustus 2022 |
| [2.3.0](#230) |10 november 2017 |31 augustus 2022 |
| [2.2.1](#221) |29 sep, 2017 |31 augustus 2022 |
| [2.2.0](#220) |10 mei 2017 |31 augustus 2022 |
| [2.1.0](#210) |01 mei 2017 |31 augustus 2022 |
| [2.0.1](#201) |30 oktober 2016 |31 augustus 2022 |
| [2.0.0](#200) |29 september 2016 |31 augustus 2022 |
| [1.9.0](#190) |Juli 07, 2016 |31 augustus 2022 |
| [1.8.0](#180) |14 juni 2016 |31 augustus 2022 |
| [1.7.0](#170) |26 april 2016 |31 augustus 2022 |
| [1.6.1](#161) |08 april 2016 |31 augustus 2022 |
| [1.6.0](#160) |29 maart 2016 |31 augustus 2022 |
| [1.5.0](#150) |03 januari 2016 |31 augustus 2022 |
| [1.4.2](#142) |06 oktober 2015 |31 augustus 2022 |
| 1.4.1 |06 oktober 2015 |31 augustus 2022 |
| [1.2.0](#120) |06 van 6 augustus 2015 |31 augustus 2022 |
| [1.1.0](#110) |9 juli 2015 |31 augustus 2022 |
| [1.0.1](#101) |25 mei 2015 |31 augustus 2022 |
| 1.0.0 |7 april 2015 |31 augustus 2022 |
| 0.9.4-prelease |14 januari 2015 |29 februari 2016 |
| 0.9.3-prelease |09 december 2014 |29 februari 2016 |
| 0.9.2-prelease |25 november 2014 |29 februari 2016 |
| 0.9.1 tot en-prelease |23 september 2014 |29 februari 2016 |
| 0.9.0-prelease |21 augustus 2014 |29 februari 2016 |

## <a name="faq"></a>Veelgestelde vragen

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Volgende stappen

Zie de servicepagina [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) voor meer informatie over Cosmos DB.
