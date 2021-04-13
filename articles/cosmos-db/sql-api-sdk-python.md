---
title: Azure Cosmos DB SQL Python API, SDK & resources
description: Meer informatie over de SQL Python API en SDK, inclusief releasedatums, buiten gebruik stellen en wijzigingen die zijn aangebracht tussen elke versie van de Python SDK Azure Cosmos DB Python.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 04/06/2021
ms.author: rosouz
ms.custom: devx-track-python
ms.openlocfilehash: ff551c5d677029f39d9a3db3a64f3d03e2c57eba
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366025"
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
> * [Spark 3 OLTP-connector](sql-api-sdk-java-spark-v3.md)
> * [Spark 2 OLTP-connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-resourceprovider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Bulkuitvoerder - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulkuitvoerprogramma - Java](sql-api-sdk-bulk-executor-java.md)

| Pagina| Koppeling |
|---|---|
|**SDK downloaden**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**API-documentatie**|[Referentiedocumentatie voor Python-API](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos?view=azure-python&preserve-view=true)|
|**SDK-installatie-instructies**|[Installatie-instructies voor Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)|
|**Aan de slag**|[Aan de slag met de Python-SDK](create-sql-api-python.md)|
|**Huidig ondersteund platform**|[Python 2.7](https://www.python.org/downloads/) en [Python 3.6+](https://www.python.org/downloads/)|

## <a name="release-history"></a>Releasegeschiedenis

## <a name="420"></a>4.2.0

**Opgeloste fouten**
- Er is een fout opgelost waarbij het vervolg-token niet wordt gehonoreerd query_iterable wordt gebruikt om resultaten per pagina op te halen.
- Er is een fout opgelost waarbij resourcetokens niet werden gebruikt voor het lezen en verwijderen van documenten. 

**Nieuwe functies**
- Ondersteuning toegevoegd voor doorgeven `partitionKey` tijdens het uitvoeren van query's op de wijzigingsfeed.

## <a name="410"></a>4.1.0

- Er is een afschaffingswaarschuwing toegevoegd voor de indexeringsmodus 'vertraagd'. De back-end staat het maken van containers met deze modus niet meer toe en stelt ze in plaats daarvan in op consistent.

**Nieuwe functies**
- De mogelijkheid toegevoegd om de TTL voor analytische opslag in te stellen bij het maken van een nieuwe container.

**Opgeloste fouten**
- Ondersteuning voor als `dicts` invoer voor get_client API's opgelost.
- Compatibiliteit met Python 2/3 in query-iterators is opgelost.
- Fout met typehint opgelost.
- Er is een fout opgelost waarbij optiesheaders niet werden toegevoegd aan upsert_item functie. 
- Er is een fout opgelost die is opgetreden wanneer een niet-tekenreeks-id wordt gebruikt in een item. Nu wordt TypeError in plaats van AttributeError aan het werk.


## <a name="400"></a>4.0.0

* Stabiele release.
* HttpLoggingPolicy toegevoegd aan de pijplijn om door te geven in een aangepaste logboeken voor aanvraag- en antwoordheaders.

### <a name="400b6"></a>4.0.0b6

* Er is een fout opgelost in synchronized_request voor media-API's.
* MediaReadMode en MediaRequestTimeout zijn verwijderd uit ConnectionPolicy omdat mediaaanvragen niet worden ondersteund.

### <a name="400b5"></a>4.0.0b5

* azure.cosmos.errors-module afgeschaft en vervangen door azure.cosmos.exceptions
* De parameters voor de toegangsvoorwaarde ( , , ) zijn afgeschaft `access_condition` `if_match` ten `if_none_match` voordepen van afzonderlijke `match_condition` parameters en `etag` .
* Er is een fout opgelost in routeringskaartprovider.
* Query Distinct, Offset en Limit-ondersteuning toegevoegd.
* De standaardcontext voor het uitvoeren van documentquery's wordt nu gebruikt voor

  * Feedquery's wijzigen
  * query's met één partitie ( `partitionkey` , is aanwezig in `partitionKeyRangeId` opties)
  * Niet-documentquery's

* Fouten voor statistische gegevens op meerdere partities, met inschakelen query over meerdere partities ingesteld op true, maar geen 'waarde'-sleutelwoord aanwezig
* Raakt eindpunt van queryplan voor andere scenario's om queryplan op te halen
* Ondersteuning `__repr__` toegevoegd voor Cosmos-entiteitsobjecten.
* Bijgewerkte documentatie.

### <a name="400b4"></a>4.0.0b4

* Ondersteuning toegevoegd voor een trefwoordargument voor alle bewerkingen om een absolute time-out op te geven in seconden waarin `timeout` de bewerking moet worden voltooid. Als de time-outwaarde wordt overschreden, wordt `azure.cosmos.errors.CosmosClientTimeoutError` een verhoogd.

* Er is een nieuwe toegevoegd `ConnectionRetryPolicy` om gedrag voor opnieuw proberen te beheren tijdens HTTP-verbindingsfouten.

* Nieuwe trefwoordargumenten toegevoegd voor constructor en configuratie per bewerking:

  * `retry_total` - Maximum aantal nieuwe pogingen.
  * `retry_backoff_max` - Maximale wachttijd voor opnieuw proberen in seconden.
  * `retry_fixed_interval` - Interval voor opnieuw proberen in milliseconden opgelost.
  * `retry_read` - Maximum aantal leespogingen voor sockets.
  * `retry_connect` - Maximum aantal nieuwe pogingen voor verbindingsfouten.
  * `retry_status` - Maximum aantal nieuwe pogingen voor foutstatuscodes.
  * `retry_on_status_codes` - Een lijst met specifieke statuscodes om opnieuw te proberen.
  * `retry_backoff_factor` - Factor voor het berekenen van de wachttijd tussen nieuwe pogingen.

### <a name="400b3"></a>4.0.0b3

* Functies `create_database_if_not_exists()` `create_container_if_not_exists` en toegevoegd aan respectievelijk CosmosClient en Database.

### <a name="400b2"></a>4.0.0b2

* Versie 4.0.0b2 is de tweede iteratie in onze inspanningen om een clientbibliotheek te bouwen die aansluit bij de best practices voor python-taal.

**Wijzigingen die fouten veroorzaken**

* De clientverbinding is aangepast om de HTTP-pijplijn te gebruiken die is gedefinieerd in `azure.core.pipeline` .

* De naam van interactieve objecten is gewijzigd in proxies. Dit omvat:

  * `Database` -> `DatabaseProxy`
  * `User` -> `UserProxy`
  * `Container` -> `ContainerProxy`
  * `Scripts` -> `ScriptsProxy`

* De constructor `CosmosClient` van is bijgewerkt:

  * De `auth` naam van de parameter is gewijzigd in en er wordt nu rechtstreeks een `credential` verificatietype gebruikt. Dit betekent dat de primaire sleutelwaarde, een woordenlijst met resourcetokens of een lijst met machtigingen kan worden doorgegeven. De oude woordenlijstindeling wordt echter nog steeds ondersteund.

  * De parameter is gemaakt als een alleen-sleutelwoordparameter en hoewel deze nog steeds wordt ondersteund, kunnen alle afzonderlijke kenmerken van het beleid nu worden doorgegeven als expliciete `connection_policy` trefwoordargumenten:

    * `request_timeout`
    * `media_request_timeout`
    * `connection_mode`
    * `media_read_mode`
    * `proxy_config`
    * `enable_endpoint_discovery`
    * `preferred_locations`
    * `multiple_write_locations`

* Er is een nieuwe constructor toegevoegd aan om het maken mogelijk te maken via een `CosmosClient` connection string opgehaald uit de Azure Portal.

* De `read_all` naam van sommige bewerkingen is gewijzigd in `list` bewerkingen:

  * `CosmosClient.read_all_databases` -> `CosmosClient.list_databases`
  * `Container.read_all_conflicts` -> `ContainerProxy.list_conflicts`
  * `Database.read_all_containers` -> `DatabaseProxy.list_containers`
  * `Database.read_all_users` -> `DatabaseProxy.list_users`
  * `User.read_all_permissions` -> `UserProxy.list_permissions`

* Alle bewerkingen die of parameters nemen, zijn verplaatst naar parameters die alleen `request_options` `feed_options` trefwoord zijn. Hoewel deze opties nog steeds worden ondersteund, worden alle afzonderlijke opties in de woordenlijst nu ondersteund als expliciete trefwoordargumenten.

* De fouthiërarchie wordt nu overgenomen van `azure.core.AzureError` :

  * De naam van `HTTPFailure` is gewijzigd in `CosmosHttpResponseError`
  * `JSONParseFailure` is verwijderd en vervangen door `azure.core.DecodeError`
  * Aanvullende fouten toegevoegd voor specifieke responscodes:
    * `CosmosResourceNotFoundError` voor status 404
    * `CosmosResourceExistsError` voor status 409
    * `CosmosAccessConditionFailedError` voor status 412

* `CosmosClient` kan nu worden uitgevoerd in een contextbeheer om het sluiten van de clientverbinding af te handelen.

* Itereerbare antwoorden (bijvoorbeeld queryreacties en lijstreacties) zijn nu van het type `azure.core.paging.ItemPaged` . De methode `fetch_next_block` is vervangen door een secundaire iterator, die wordt gebruikt door de methode `by_page` .

### <a name="400b1"></a>4.0.0b1

Versie 4.0.0b1 is de eerste preview van onze inspanningen om een gebruiksvriendelijke clientbibliotheek te maken die aansluit bij de best practices voor de Python-taal. Ga naar voor meer informatie over dit en preview-versies van andere Azure SDK-bibliotheken. https://aka.ms/azure-sdk-preview1-python

**Belangrijke wijzigingen: Nieuw API-ontwerp**

* Bewerkingen zijn nu beperkt tot een bepaalde client:

  * `CosmosClient`: Deze client verwerkt bewerkingen op accountniveau. Dit omvat het beheren van service-eigenschappen en het weergeven van de databases binnen een account.
  * `Database`: Deze client verwerkt bewerkingen op databaseniveau. Dit omvat het maken en verwijderen van containers, gebruikers en opgeslagen procedures. Deze kan worden gebruikt vanuit een cosmosClient-exemplaar op naam.
  * `Container`: Deze client verwerkt bewerkingen voor een bepaalde container. Dit omvat het uitvoeren van query's en het invoegen van items en het beheren van eigenschappen.
  * `User`: Deze client verwerkt bewerkingen voor een bepaalde gebruiker. Dit omvat het toevoegen en verwijderen van machtigingen en het beheren van gebruikerseigenschappen.

    Deze clients zijn toegankelijk door omlaag te navigeren in de clienthiërarchie met behulp van de `get_<child>_client` methode . Zie de referentiedocumentatie voor meer informatie over [de nieuwe API.](https://aka.ms/azsdk-python-cosmos-ref)

* Clients zijn toegankelijk op naam in plaats van id. U hoeft geen tekenreeksen samen tevoegen om koppelingen te maken.

* U hoeft geen typen en methoden meer te importeren uit afzonderlijke modules. De openbare API surface area zijn rechtstreeks beschikbaar in het `azure.cosmos` pakket.

* Afzonderlijke aanvraageigenschappen kunnen worden opgegeven als trefwoordargumenten in plaats van een afzonderlijk exemplaar te `RequestOptions` maken.

### <a name="302"></a>3.0.2

* Ondersteuning toegevoegd voor multipolygon-gegevenstype
* Opgeloste fout in beleid voor opnieuw proberen van sessie lezen
* Opgeloste fout bij problemen met onjuiste opvulling tijdens het decoderen van base 64-tekenreeksen

### <a name="301"></a>3.0.1

* Opgeloste fout in LocationCache
* Logica voor opnieuw proberen van eindpunt opgelost
* Probleem met documentatie opgelost

### <a name="300"></a>3.0.0

* Schrijfondersteuning voor meerdere regio's toegevoegd
* Naamgevingswijzigingen
  * DocumentClient naar CosmosClient
  * Verzameling naar container
  * Document naar item
  * Pakketnaam bijgewerkt naar 'azure-cosmos'
  * Naamruimte bijgewerkt naar 'azure.cosmos'

### <a name="233"></a>2.3.3

* Ondersteuning toegevoegd voor proxy
* Ondersteuning toegevoegd voor het lezen van de wijzigingsfeed
* Ondersteuning toegevoegd voor quotumheaders voor verzamelingen
* Bugfix voor probleem met grote sessietokens
* Bugfix voor ReadMedia-API
* Bugfix in cache voor partitiesleutelbereik

### <a name="232"></a>2.3.2

* Er is ondersteuning toegevoegd voor standaard nieuwe proberen bij verbindingsproblemen.

### <a name="231"></a>2.3.1

* Documentatie bijgewerkt om te verwijzen Azure Cosmos DB in plaats van Azure DocumentDB.

### <a name="230"></a>2.3.0

* Voor deze SDK-versie is de nieuwste versie van Azure Cosmos DB Emulator vereist die kan worden gedownload van https://aka.ms/cosmosdb-emulator .

### <a name="221"></a>2.2.1

* bugfix voor cumulatief dicteren
* bugfix voor het inkorten van slashes in de resourcekoppeling
* tests voor unicode-codering

### <a name="220"></a>2.2.0

* Ondersteuning toegevoegd voor de functie Aanvraageenheid per minuut (RU/m).
* Ondersteuning toegevoegd voor een nieuw consistentieniveau met de naam ConsistentPrefix.

### <a name="210"></a>2.1.0

* Ondersteuning toegevoegd voor aggregatiequery's (COUNT, MIN, MAX, SUM en AVG).
* Er is een optie toegevoegd voor het uitschakelen van SSL-verificatie bij het uitvoeren van op de DocumentDB-emulator.
* De beperking van de module voor afhankelijke aanvragen is verwijderd om precies 2.10.0 te zijn.
* De minimale doorvoer voor gepartities is verlaagd van 10.100 RU/s naar 2500 RU/s.
* Ondersteuning toegevoegd voor het inschakelen van scriptlogboekregistratie tijdens de uitvoering van de opgeslagen procedure.
* REST API versie is met deze release naar '2017-01-19' gedrempeld.

### <a name="201"></a>2.0.1

* Er zijn redactionele wijzigingen aangebracht in opmerkingen bij de documentatie.

### <a name="200"></a>2.0.0

* Ondersteuning toegevoegd voor Python 3.5.
* Ondersteuning toegevoegd voor het groeperen van verbindingen met behulp van de aanvraagmodule.
* Ondersteuning toegevoegd voor sessieconsistentie.
* Ondersteuning toegevoegd voor TOP-/ORDERBY-query's voor gepartities.

### <a name="190"></a>1.9.0

* Ondersteuning voor beleid voor opnieuw proberen toegevoegd voor beperkt aantal aanvragen. (Beperkt aanvragen ontvangen een uitzondering met een te hoge aanvraagsnelheid, foutcode 429.) DocumentDB doet standaard negen keer nieuwe poging voor elke aanvraag wanneer foutcode 429 wordt aangetroffen, met respect voor de nieuwe pogingNa de tijd in de antwoordheader.
  Er kan nu een vast interval voor nieuwe poging worden ingesteld als onderdeel van de eigenschap RetryOptions op het ConnectionPolicy-object als u de nieuwe poging wilt negerenNa de tijd die is geretourneerd door de server tussen de nieuwe poging.
  DocumentDB wacht nu maximaal 30 seconden voor elke aanvraag die wordt beperkt (ongeacht het aantal nieuwe poging) en retourneert het antwoord met foutcode 429.
  Deze tijd kan ook worden overschrijven in de eigenschap RetryOptions van het ConnectionPolicy-object.

* DocumentDB retourneert nu x-ms-throttle-retry-count en x-ms-throttle-retry-wait-time-ms als de antwoordheaders in elke aanvraag om het aantal nieuwe vertragingen aan te geven en de cumulatieve tijd dat de aanvraag heeft gewacht tussen de nieuwe poging.

* De klasse RetryPolicy en de bijbehorende eigenschap (retry_policy) die beschikbaar zijn gemaakt op de document_client-klasse zijn verwijderd en in plaats daarvan is een RetryOptions-klasse geïntroduceerd met de eigenschap RetryOptions in de Klasse ConnectionPolicy die kan worden gebruikt om enkele van de standaardopties voor opnieuw proberen te overschrijven.

### <a name="180"></a>1.8.0

* Ondersteuning toegevoegd voor geo-gerepliceerde databaseaccounts.
* Testfixes om de globale host en masterKey naar de afzonderlijke testklassen te verplaatsen.

### <a name="170"></a>1.7.0

* Ondersteuning toegevoegd voor TTL-functie (Time To Live) voor documenten.

### <a name="161"></a>1.6.1

* Oplossingen voor fouten met betrekking tot partitionering aan serverzijde om speciale tekens toe te staan in het pad van de partitiesleutel.

### <a name="160"></a>1.6.0

* Ondersteuning toegevoegd voor de functie gepartitiefde verzamelingen aan de serverzijde.

### <a name="150"></a>1.5.0

* Sharding-framework aan clientzijde toegevoegd aan de SDK. De klassen HashPartionResolver en RangePartitionResolver zijn geïmplementeerd.

### <a name="142"></a>1.4.2

* Upsert implementeren. Er zijn nieuwe UpsertXXX-methoden toegevoegd ter ondersteuning van de functie Upsert.
* Implementeert ID-Based routering. Geen openbare API-wijzigingen, alle wijzigingen intern.

### <a name="130"></a>1.3.0

* Release overgeslagen om versienummer samen te brengen met andere SDK's

### <a name="120"></a>1.2.0

* Ondersteunt georuimtelijke index.
* Valideert de id-eigenschap voor alle resources. Id's voor resources mogen geen `?, /, #, \\` tekens bevatten of eindigen met een spatie.
* Voegt nieuwe header 'voortgang van indextransformatie' toe aan ResourceResponse.

### <a name="110"></a>1.1.0

* Implementeert V2-indexeringsbeleid

### <a name="101"></a>1.0.1

* Ondersteunt proxyverbinding

## <a name="release--retirement-dates"></a>Release-& datums van uittreding

Microsoft geeft ten minste **12** maanden voordat een SDK wordt gestopt een melding om de overgang naar een nieuwere/ondersteunde versie te soepeler te laten verlopen. Nieuwe functies en functionaliteiten en optimaliseringen worden alleen toegevoegd aan de huidige SDK. Het wordt daarom aangeraden altijd zo snel mogelijk een upgrade naar de nieuwste SDK-versie uit te voeren.

> [!WARNING]
> Na 31 augustus 2022 zal Azure Cosmos DB geen bugfixes meer doen en biedt geen ondersteuning meer voor versies 1.x en 2.x van de Azure Cosmos DB Python SDK voor SQL API. Als u liever geen upgrade wilt uitvoeren, worden aanvragen die zijn verzonden vanaf versie 1.x en 2.x van de SDK nog steeds door de Azure Cosmos DB service.

| Versie | Releasedatum | Buitengebruikstellingsdatum |
| --- | --- | --- |
| [4.2.0](#420) |9 oktober 2020 |--- |
| [4.1.0](#410) |10 augustus 2020 |--- |
| [4.0.0](#400) |20 mei 2020 |--- |
| [3.0.2](#302) |15 november 2018 |--- |
| [3.0.1](#301) |4 oktober 2018 |--- |
| [2.3.3](#233) |08 september 2018 |31 augustus 2022 |
| [2.3.2](#232) |08 mei 2018 |31 augustus 2022 |
| [2.3.1](#231) |21 december 2017 |31 augustus 2022 |
| [2.3.0](#230) |10 november 2017 |31 augustus 2022 |
| [2.2.1](#221) |29 sep 2017 |31 augustus 2022 |
| [2.2.0](#220) |10 mei 2017 |31 augustus 2022 |
| [2.1.0](#210) |1 mei 2017 |31 augustus 2022 |
| [2.0.1](#201) |30 oktober 2016 |31 augustus 2022 |
| [2.0.0](#200) |29 september 2016 |31 augustus 2022 |
| [1.9.0](#190) |7 juli 2016 |31 augustus 2022 |
| [1.8.0](#180) |14 juni 2016 |31 augustus 2022 |
| [1.7.0](#170) |26 april 2016 |31 augustus 2022 |
| [1.6.1](#161) |8 april 2016 |31 augustus 2022 |
| [1.6.0](#160) |29 maart 2016 |31 augustus 2022 |
| [1.5.0](#150) |03 januari 2016 |31 augustus 2022 |
| [1.4.2](#142) |06 oktober 2015 |31 augustus 2022 |
| 1.4.1 |06 oktober 2015 |31 augustus 2022 |
| [1.2.0](#120) |6 augustus 2015 |31 augustus 2022 |
| [1.1.0](#110) |9 juli 2015 |31 augustus 2022 |
| [1.0.1](#101) |25 mei 2015 |31 augustus 2022 |
| 1.0.0 |7 april 2015 |31 augustus 2022 |
| 0.9.4-prelease |14 januari 2015 |29 februari 2016 |
| 0.9.3-prelease |09 december 2014 |29 februari 2016 |
| 0.9.2-prelease |25 november 2014 |29 februari 2016 |
| 0.9.1-prelease |23 september 2014 |29 februari 2016 |
| 0.9.0-prelease |21 augustus 2014 |29 februari 2016 |

## <a name="faq"></a>Veelgestelde vragen

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Volgende stappen

Zie de servicepagina [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) voor meer informatie over Cosmos DB.
