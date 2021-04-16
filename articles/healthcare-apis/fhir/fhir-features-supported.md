---
title: Ondersteunde FHIR-functies in Azure - Azure API for FHIR
description: In dit artikel wordt uitgelegd welke functies van de FHIR-specificatie zijn geïmplementeerd in Azure API for FHIR
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 4/15/2021
ms.author: cavoeg
ms.openlocfilehash: 56e3ba46ffb43aec907d729a2e74cdf6f7a62c32
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530635"
---
# <a name="features"></a>Functies

Azure API for FHIR biedt een volledig beheerde implementatie van de Microsoft FHIR-server voor Azure. De server is een implementatie van de [FHIR-standaard.](https://hl7.org/fhir) In dit document worden de belangrijkste functies van de FHIR-server vermeld.

## <a name="fhir-version"></a>FHIR-versie

Meest recente ondersteunde versie: `4.0.1`

Vorige versies die momenteel ook worden ondersteund, zijn onder andere: `3.0.2`

## <a name="rest-api"></a>REST-API

| API                            | Ondersteund - PaaS | Ondersteund - OSS (SQL) | Ondersteund - OSS (Cosmos DB) | Opmerking                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| lezen                           | Ja       | Ja       | Ja       |                                                     |
| vread                          | Ja       | Ja       | Ja       |                                                     |
| update                         | Ja       | Ja       | Ja       |                                                     |
| bijwerken met optimistische vergrendeling | Ja       | Ja       | Ja       |                                                     |
| update (voorwaardelijk)           | Ja       | Ja       | Ja       |                                                     |
| Patch                          | Nee        | Nee        | Nee        |                                                     |
| delete                         | Ja       | Ja       | Ja       |  Zie Opmerking hieronder.                                   |
| verwijderen (voorwaardelijk)           | Nee        | Nee        | Nee        |                                                     |
| geschiedenis                        | Ja       | Ja       | Ja       |                                                     |
| maken                         | Ja       | Ja       | Ja       | Ondersteuning voor zowel POST/PUT                               |
| maken (voorwaardelijk)           | Ja       | Ja       | Ja       | Probleem [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| zoeken                         | Gedeeltelijk   | Gedeeltelijk   | Gedeeltelijk   | Zie de sectie Zoeken hieronder.                           |
| zoeken in keten                 | Gedeeltelijk       | Ja       | Gedeeltelijk   | Zie opmerking 2 hieronder.                                   |
| reverse chained zoeken         | Gedeeltelijk       | Ja       | Gedeeltelijk   | Zie opmerking 2 hieronder.                                   |
| mogelijkheden                   | Ja       | Ja       | Ja       |                                                     |
| batch                          | Ja       | Ja       | Ja       |                                                     |
| Transactie                    | Nee        | Ja       | Nee        |                                                     |
| Paging                         | Gedeeltelijk   | Gedeeltelijk   | Gedeeltelijk   | `self` en `next` worden ondersteund                     |
| Tussenpersonen                 | Nee        | Nee        | Nee        |                                                     |

> [!Note]
> Verwijderen gedefinieerd door de FHIR-specificatie vereist dat na het verwijderen, latere niet-versiespecifieke lees lezen van een resource een 410 HTTP-statuscode retourneert en de resource niet meer wordt gevonden via zoeken. Met Azure API for FHIR kunt u de resource ook volledig verwijderen (inclusief alle geschiedenis). Als u de resource volledig wilt verwijderen, kunt u een parameterinstellingen doorgeven `hardDelete` aan true ( `DELETE {server}/{resource}/{id}?hardDelete=true` ). Als u deze parameter niet doorgeeft of is ingesteld op false, zijn de historische versies van `hardDelete` de resource nog steeds beschikbaar.


 **Opmerking 2**
* Voegt MVP-ondersteuning toe voor Chained en Reverse Chained FHIR Search in CosmosDB. 

  In de Azure API for FHIR en de open-source FHIR-server die wordt geback-by door Cosmos, is de geketende zoekopdracht en omgekeerde zoekopdracht een MVP-implementatie. Als u een ketenzoekactie wilt uitvoeren op Cosmos DB, wordt de zoekexpressie door de implementatie geseed en worden subquery's uitgevoerd om de overeenkomende resources op te lossen. Dit wordt gedaan voor elk niveau van de expressie. Als een query meer dan 100 resultaten retourneert, wordt er een foutmelding weergegeven. Ketenzoekactie wordt standaard achter een functievlag weergegeven. Als u de ketenzoekactie wilt gebruiken op Cosmos DB, gebruikt u de header `x-ms-enable-chained-search: true` . Zie PR [1695](https://github.com/microsoft/fhir-server/pull/1695)voor meer informatie.

## <a name="search"></a>Zoeken

Alle typen zoekparameters worden ondersteund. 

| Type zoekparameter | Ondersteund - PaaS | Ondersteund - OSS (SQL) | Ondersteund - OSS (Cosmos DB) | Opmerking |
|-----------------------|-----------|-----------|-----------|---------|
| Aantal                | Ja       | Ja       | Ja       |         |
| Datum/datum/tijd         | Ja       | Ja       | Ja       |         |
| Tekenreeks                | Ja       | Ja       | Ja       |         |
| Token                 | Ja       | Ja       | Ja       |         |
| Referentie             | Ja       | Ja       | Ja       |         |
| Composite             | Ja       | Ja       | Ja       |         |
| Aantal              | Ja       | Ja       | Ja       |         |
| URI                   | Ja       | Ja       | Ja       |         |
| Speciale               | Nee        | Nee        | Nee        |         |


| Modifiers             | Ondersteund - PaaS | Ondersteund - OSS (SQL) | Ondersteund - OSS (Cosmos DB) | Opmerking |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Ja       | Ja       | Ja       |         |
|`:exact`               | Ja       | Ja       | Ja       |         |
|`:contains`            | Ja       | Ja       | Ja       |         |
|`:text`                | Ja       | Ja       | Ja       |         |
|`:[type]` (naslag)  | Ja       | Ja       | Ja       |         |
|`:not`                 | Ja       | Ja       | Ja       |         |
|`:below` (URI)         | Ja       | Ja       | Ja       |         |
|`:above` (URI)         | Nee        | Nee        | Nee        | Probleem [#158](https://github.com/Microsoft/fhir-server/issues/158) |
|`:in` (token)          | Nee        | Nee        | Nee        |         |
|`:below` (token)       | Nee        | Nee        | Nee        |         |
|`:above` (token)       | Nee        | Nee        | Nee        |         |
|`:not-in` (token)      | Nee        | Nee        | Nee        |         |

| Algemene zoekparameter | Ondersteund - PaaS | Ondersteund - OSS (SQL) | Ondersteund - OSS (Cosmos DB) | Opmerking |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Ja       | Ja       | Ja       |         |
| `_lastUpdated`          | Ja       | Ja       | Ja       |         |
| `_tag`                  | Ja       | Ja       | Ja       |         |
| `_list`                 | Ja       | Ja       | Ja       |         |
| `_type`                 | Ja       | Ja       | Ja       | Probleem [#1562](https://github.com/microsoft/fhir-server/issues/1562)        |
| `_security`             | Ja       | Ja       | Ja       |         |
| `_profile`              | Gedeeltelijk   | Gedeeltelijk   | Gedeeltelijk   | Ondersteund in STU3. Als u uw database **hebt** gemaakt na 20 februari 2021, hebt u ook ondersteuning in R4. We werken aan het inschakelen _profile databases die zijn gemaakt vóór 20 februari 2021. |
| `_text`                 | Nee        | Nee        | Nee        |         |
| `_content`              | Nee        | Nee        | Nee        |         |
| `_has`                  | Nee        | Nee        | Nee        |         |
| `_query`                | Nee        | Nee        | Nee        |         |
| `_filter`               | Nee        | Nee        | Nee        |         |

| Zoekresultaatparameters | Ondersteund - PaaS | Ondersteund - OSS (SQL) | Ondersteund - OSS (Cosmos DB) | Opmerking |
|-------------------------|-----------|-----------|-----------|---------|
| `_elements`             | Ja       | Ja       | Ja       | Probleem [#1256](https://github.com/microsoft/fhir-server/issues/1256)        |
| `_count`                | Ja       | Ja       | Ja       | `_count` is beperkt tot 1000 tekens. Als deze is ingesteld op hoger dan 1000, worden er slechts 1000 geretourneerd en wordt er een waarschuwing geretourneerd in de bundel. |
| `_include`              | Ja       | Ja       | Ja       |Inbegrepen items zijn beperkt tot 100. Opnemen in PaaS en OSS op Cosmos DB bevat geen :iterate-ondersteuning.|
| `_revinclude`           | Ja       | Ja       | Ja       | Inbegrepen items zijn beperkt tot 100. Opnemen in PaaS en OSS op Cosmos DB bevat [geen :iterate-ondersteuning.](https://github.com/microsoft/fhir-server/issues/1313) Probleem [#1319](https://github.com/microsoft/fhir-server/issues/1319)|
| `_summary`              | Gedeeltelijk   | Gedeeltelijk   | Gedeeltelijk   | `_summary=count` wordt ondersteund |
| `_total`                | Gedeeltelijk   | Gedeeltelijk   | Gedeeltelijk   | `_total=none` en `_total=accurate`      |
| `_sort`                 | Gedeeltelijk   | Gedeeltelijk   | Gedeeltelijk   |   `_sort=_lastUpdated` wordt ondersteund       |
| `_contained`            | Nee        | Nee        | Nee        |         |
| `containedType`         | Nee        | Nee        | Nee        |         |
| `_score`                | Nee        | Nee        | Nee        |         |

## <a name="extended-operations"></a>Uitgebreide bewerkingen

Alle ondersteunde bewerkingen die de RESTful-API uitbreiden.

| Type zoekparameter | Ondersteund - PaaS | Ondersteund - OSS (SQL) | Ondersteund - OSS (Cosmos DB) | Opmerking |
|------------------------|-----------|-----------|-----------|---------|
| $export (hele systeem) | Ja       | Ja       | Ja       |         |
| Patiënt/$export        | Ja       | Ja       | Ja       |         |
| Groep/$export          | Ja       | Ja       | Ja       |         |
| $convert-data          | Ja       | Ja       | Ja       |         |


## <a name="persistence"></a>Persistentie

De Microsoft FHIR-server heeft een pluggable persistentiemodule (zie [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

De opensource-code van de FHIR-server bevat momenteel een implementatie [voor Azure Cosmos DB](../../cosmos-db/index-overview.md) en [SQL Database.](https://azure.microsoft.com/services/sql-database/)

Cosmos DB is een wereldwijd gedistribueerde database met meerdere modellen (SQL API, MongoDB-API, enzovoort). Het ondersteunt verschillende [consistentieniveaus.](../../cosmos-db/consistency-levels.md) De standaardimplementatiesjabloon configureert de FHIR-server met consistentie, maar het consistentiebeleid kan worden gewijzigd (over het algemeen versoepeld) op aanvraag met behulp van de `Strong` `x-ms-consistency-level` aanvraagheader.

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

De FHIR-server gebruikt [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) voor toegangsbeheer. Met name op rollen gebaseerd toegangsbeheer (RBAC) wordt afgedwongen als de configuratieparameter is ingesteld op en voor alle aanvragen (met uitzondering van ) op de `FhirServer:Security:Enabled` `true` `/metadata` FHIR-server de aanvraagheader moet zijn `Authorization` ingesteld op `Bearer <TOKEN>` . Het token moet een of meer rollen bevatten zoals gedefinieerd in de `roles` claim. Een aanvraag wordt toegestaan als het token een rol bevat waarmee de opgegeven actie voor de opgegeven resource is toegestaan.

Op dit moment worden de toegestane acties voor een bepaalde rol *globaal toegepast* op de API.

## <a name="service-limits"></a>Servicelimieten

* [**Aanvraageenheden (AANVRAAGeenheden)**](../../cosmos-db/concepts-limits.md) : u kunt maximaal 10.000 AANVRAAGeenheden in de portal configureren voor Azure API for FHIR. U hebt minimaal 400 RUs of 40 RUs/GB nodig, wat groter is. Als u meer dan 10.000 RUs nodig hebt, kunt u een ondersteuningsticket maken om dit aantal te laten toenemen. Het maximum aantal beschikbare is 1.000.000.

* **Gelijktijdige verbindingen** **en exemplaren:** standaard hebt u vijf gelijktijdige verbindingen op twee exemplaren in het cluster (voor een totaal van 10 gelijktijdige aanvragen). Als u denkt dat u meer gelijktijdige aanvragen nodig hebt, opent u een ondersteuningsticket met informatie over uw behoeften.

* **Bundelgrootte:** elke bundel is beperkt tot 500 items.

* **Gegevensgrootte:** gegevens/documenten moeten elk iets kleiner zijn dan 2 MB.

## <a name="performance-expectations"></a>Verwachtingen voor prestaties

De prestaties van het systeem zijn afhankelijk van het aantal AANVRAGEN, gelijktijdige verbindingen en het type bewerkingen dat u wilt uitvoeren (Put, Post, enzovoort). Hieronder vindt u enkele algemene reeksen van wat u kunt verwachten op basis van geconfigureerde RUs. Over het algemeen worden de prestaties lineair geschaald met een toename van het aantal RUs:

| Aantal RUs | Resources per seconde |    Maximale opslag (GB)*    |
|----------|---------------|--------|                 
| 400      | 5-10          |     10   |
| 1000    | 100-150       |      25  |
| 10.000   | 225-400       |      250  |
| 100.000  | 2,500-4,000   |      2500  |

Opmerking: Per Cosmos DB is er een minimumdoorvoer van 40 RU/s per GB aan opslag vereist. 

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u meer gelezen over de ondersteunde FHIR-functies in Azure API for FHIR. Implementeer vervolgens de Azure API for FHIR.
 
>[!div class="nextstepaction"]
>[De Azure-API voor FHIR implementeren](fhir-paas-portal-quickstart.md)
