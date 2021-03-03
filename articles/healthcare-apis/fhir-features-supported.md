---
title: Ondersteunde FHIR-functies in azure-Azure API voor FHIR
description: In dit artikel wordt uitgelegd welke functies van de FHIR-specificatie zijn geïmplementeerd in azure API voor FHIR
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/30/2021
ms.author: cavoeg
ms.openlocfilehash: 19f051320aaa675ebe5ff148fb6580c2a5d8770c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719131"
---
# <a name="features"></a>Functies

Azure API voor FHIR biedt een volledig beheerde implementatie van de micro soft FHIR-server voor Azure. De server is een implementatie van de [FHIR](https://hl7.org/fhir) -standaard. In dit document worden de belangrijkste functies van de FHIR-server vermeld.

## <a name="fhir-version"></a>FHIR-versie

Nieuwste versie ondersteund: `4.0.1`

Eerdere versies die momenteel worden ondersteund, zijn onder andere: `3.0.2`

## <a name="rest-api"></a>REST-API

| API                            | Ondersteund-PaaS | Ondersteund-OSS (SQL) | Ondersteund-OSS (Cosmos DB) | Opmerking                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| lezen                           | Ja       | Ja       | Ja       |                                                     |
| vread                          | Ja       | Ja       | Ja       |                                                     |
| update                         | Ja       | Ja       | Ja       |                                                     |
| bijwerken met optimistische vergren deling | Ja       | Ja       | Ja       |                                                     |
| Update (voorwaardelijk)           | Ja       | Ja       | Ja       |                                                     |
| verzenden                          | Nee        | Nee        | Nee        |                                                     |
| delete                         | Ja       | Ja       | Ja       |  Zie de opmerking hieronder                                                   |
| verwijderen (voorwaardelijk)           | Nee        | Nee        | Nee        |                                                     |
| geschiedenis                        | Ja       | Ja       | Ja       |                                                     |
| maken                         | Ja       | Ja       | Ja       | Ondersteuning voor zowel POST/PUT                               |
| maken (voorwaardelijk)           | Ja       | Ja       | Ja       | Probleem [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| zoeken                         | Gedeeltelijk   | Gedeeltelijk   | Gedeeltelijk   | Zie hieronder                                           |
| geketende zoek opdracht                 | Nee        | Ja       | Nee        |                                                     |
| geketende zoek opdracht omkeren         | Nee        | Ja       | Nee        |                                                     |
| mogelijkheden                   | Ja       | Ja       | Ja       |                                                     |
| batch                          | Ja       | Ja       | Ja       |                                                     |
| trans actie                    | Nee        | Ja       | Nee        |                                                     |
| haalt                         | Gedeeltelijk   | Gedeeltelijk   | Gedeeltelijk   | `self` en `next` worden ondersteund                     |
| schakels                 | Nee        | Nee        | Nee        |                                                     |

> [!Note]
> Verwijderd door de FHIR spec vereist dat na het verwijderen van de volgende niet-versie specifieke Lees bewerkingen van een resource een 410 HTTP-status code retourneert en de resource niet meer kan worden gevonden via zoeken. Met de Azure-API voor FHIR kunt u ook de resource volledig verwijderen (inclusief alle geschiedenis). Als u de resource volledig wilt verwijderen, kunt u een parameter instelling door geven `hardDelete` aan True ( `DELETE {server}/{resource}/{id}?hardDelete=true` ). Als u deze para meter niet doorgeeft of instelt `hardDelete` op False, zullen de historische versies van de resource nog steeds beschikbaar zijn.

## <a name="search"></a>Search

Alle typen zoek parameters worden ondersteund. 

| Type zoek parameter | Ondersteund-PaaS | Ondersteund-OSS (SQL) | Ondersteund-OSS (Cosmos DB) | Opmerking |
|-----------------------|-----------|-----------|-----------|---------|
| Aantal                | Ja       | Ja       | Ja       |         |
| Datum/datum/tijd         | Ja       | Ja       | Ja       |         |
| Tekenreeks                | Ja       | Ja       | Ja       |         |
| Token                 | Ja       | Ja       | Ja       |         |
| Referentie             | Ja       | Ja       | Ja       |         |
| Composite             | Ja       | Ja       | Ja       |         |
| Aantal              | Ja       | Ja       | Ja       |         |
| URI                   | Ja       | Ja       | Ja       |         |
| Specifiek               | Nee        | Nee        | Nee        |         |


| Para meters             | Ondersteund-PaaS | Ondersteund-OSS (SQL) | Ondersteund-OSS (Cosmos DB) | Opmerking |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Ja       | Ja       | Ja       |         |
|`:exact`               | Ja       | Ja       | Ja       |         |
|`:contains`            | Ja       | Ja       | Ja       |         |
|`:text`                | Ja       | Ja       | Ja       |         |
|`:[type]` referentielaag  | Ja       | Ja       | Ja       |         |
|`:not`                 | Ja       | Ja       | Ja       |         |
|`:below` URI         | Ja       | Ja       | Ja       |         |
|`:above` URI         | Nee        | Nee        | Nee        | Probleem [#158](https://github.com/Microsoft/fhir-server/issues/158) |
|`:in` token          | Nee        | Nee        | Nee        |         |
|`:below` token       | Nee        | Nee        | Nee        |         |
|`:above` token       | Nee        | Nee        | Nee        |         |
|`:not-in` token      | Nee        | Nee        | Nee        |         |

| Algemene zoek parameter | Ondersteund-PaaS | Ondersteund-OSS (SQL) | Ondersteund-OSS (Cosmos DB) | Opmerking |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Ja       | Ja       | Ja       |         |
| `_lastUpdated`          | Ja       | Ja       | Ja       |         |
| `_tag`                  | Ja       | Ja       | Ja       |         |
| `_list`                 | Ja       | Ja       | Ja       |         |
| `_type`                 | Ja       | Ja       | Ja       | Probleem [#1562](https://github.com/microsoft/fhir-server/issues/1562)        |
| `_security`             | Ja       | Ja       | Ja       |         |
| `_profile`              | Gedeeltelijk   | Gedeeltelijk   | Gedeeltelijk   | Ondersteund in STU3. Als u de Data Base hebt gemaakt **na** 20 februari, 2021, hebt u ook ondersteuning in R4. We zijn bezig om _profile in te scha kelen voor data bases die zijn gemaakt vóór 20 februari, 2021. |
| `_text`                 | Nee        | Nee        | Nee        |         |
| `_content`              | Nee        | Nee        | Nee        |         |
| `_has`                  | Nee        | Nee        | Nee        |         |
| `_query`                | Nee        | Nee        | Nee        |         |
| `_filter`               | Nee        | Nee        | Nee        |         |

| Zoek resultaat parameters | Ondersteund-PaaS | Ondersteund-OSS (SQL) | Ondersteund-OSS (Cosmos DB) | Opmerking |
|-------------------------|-----------|-----------|-----------|---------|
| `_elements`             | Ja       | Ja       | Ja       | Probleem [#1256](https://github.com/microsoft/fhir-server/issues/1256)        |
| `_count`                | Ja       | Ja       | Ja       | `_count` is beperkt tot 1000 tekens. Als deze waarde hoger is dan 1000, wordt alleen 1000 geretourneerd en wordt er een waarschuwing in de bundel geretourneerd. |
| `_include`              | Ja       | Ja       | Ja       |Opgenomen items zijn beperkt tot 100. Include op PaaS en OSS on Cosmos DB omvat niet: ITER-ondersteuning.|
| `_revinclude`           | Ja       | Ja       | Ja       | Opgenomen items zijn beperkt tot 100. Include op PaaS en OSS on Cosmos DB [omvat niet: ITER-ondersteuning](https://github.com/microsoft/fhir-server/issues/1313). Probleem [#1319](https://github.com/microsoft/fhir-server/issues/1319)|
| `_summary`              | Gedeeltelijk   | Gedeeltelijk   | Gedeeltelijk   | `_summary=count` wordt ondersteund |
| `_total`                | Gedeeltelijk   | Gedeeltelijk   | Gedeeltelijk   | `_total=none` en `_total=accurate`      |
| `_sort`                 | Gedeeltelijk   | Gedeeltelijk   | Gedeeltelijk   |   `_sort=_lastUpdated` wordt ondersteund       |
| `_contained`            | Nee        | Nee        | Nee        |         |
| `containedType`         | Nee        | Nee        | Nee        |         |
| `_score`                | Nee        | Nee        | Nee        |         |

## <a name="extended-operations"></a>Uitgebreide bewerkingen

Alle bewerkingen die worden ondersteund om de REST-API uit te breiden.

| Type zoek parameter | Ondersteund-PaaS | Ondersteund-OSS (SQL) | Ondersteund-OSS (Cosmos DB) | Opmerking |
|------------------------|-----------|-----------|-----------|---------|
| $export (heel systeem) | Ja       | Ja       | Ja       |         |
| Patiënt/$export        | Ja       | Ja       | Ja       |         |
| Groep/$export          | Ja       | Ja       | Ja       |         |
| $convert-gegevens          | Ja       | Ja       | Ja       |         |


## <a name="persistence"></a>Persistentie

De micro soft FHIR-server heeft een pluggable persistentie module (Zie [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

Momenteel bevat de open-source code van de FHIR-server een implementatie voor [Azure Cosmos DB](../cosmos-db/index-overview.md) en [SQL database](https://azure.microsoft.com/services/sql-database/).

Cosmos DB is een wereld wijd gedistribueerde multi-model-data base (SQL API, MongoDB API, etc.). Het ondersteunt verschillende [consistentie niveaus](../cosmos-db/consistency-levels.md). Met de standaard implementatie sjabloon wordt de FHIR-server met `Strong` consistentie geconfigureerd, maar het consistentie beleid kan worden gewijzigd (over het algemeen ongeforceerd) op een aanvraag op basis van de aanvraag `x-ms-consistency-level` header.

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

De FHIR-server gebruikt [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) voor toegangs beheer. Met name op rollen gebaseerd toegangs beheer (RBAC) wordt afgedwongen, als de `FhirServer:Security:Enabled` configuratie parameter is ingesteld op `true` en voor alle aanvragen (behalve `/metadata` ) naar de FHIR-server moet de `Authorization` aanvraag header zijn ingesteld op `Bearer <TOKEN>` . Het token moet een of meer rollen bevatten zoals gedefinieerd in de `roles` claim. Een aanvraag wordt toegestaan als het token een rol bevat waarmee de opgegeven actie kan worden uitgevoerd voor de opgegeven bron.

Op dit moment worden de toegestane acties voor een bepaalde rol *globaal* toegepast op de API.

## <a name="service-limits"></a>Servicelimieten

* [**Aanvraag eenheden (RUs)**](../cosmos-db/concepts-limits.md) : u kunt maxi maal 10.000 RUs configureren in de portal voor Azure API voor FHIR. U hebt mini maal 400 RUs of 10 RUs/GB nodig, afhankelijk van wat groter is. Als u meer dan 10.000 RUs nodig hebt, kunt u een ondersteunings ticket plaatsen om dit te verg root. De Maxi maal beschik bare waarde is 1.000.000.

* **Gelijktijdige verbindingen** en **instanties** : standaard hebt u vijf gelijktijdige verbindingen op twee exemplaren in het cluster (voor een totaal van 10 gelijktijdige aanvragen). Als u denkt dat u meer gelijktijdige aanvragen nodig hebt, opent u een ondersteunings ticket met informatie over uw behoeften.

* **Bundel grootte** : elke bundel is beperkt tot 500 items.

* **Gegevens grootte** : gegevens/documenten moeten iets kleiner zijn dan 2 MB.

## <a name="performance-expectations"></a>Prestatie verwachtingen

De prestaties van het systeem zijn afhankelijk van het aantal RUs, gelijktijdige verbindingen en het type bewerkingen dat u uitvoert (put, post, enz.). Hieronder vindt u enkele algemene bereiken van wat u kunt verwachten op basis van het geconfigureerde RUs. Over het algemeen schaalt prestaties lineair met een toename van RUs:

| # RUs | Bronnen per seconde |    Maximale opslag ruimte (GB) *    |
|----------|---------------|--------|                 
| 400      | 5-10          |     40   |
| 1000    | 100-150       |      100  |
| 10.000   | 225-400       |      1000  |
| 100.000  | 2500-4000   |      10.000  |

Opmerking: per Cosmos DB vereiste is er een minimale door Voer van 10 RU/s per GB opslag ruimte vereist. Bekijk [Cosmos DB Service quota's](../cosmos-db/concepts-limits.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u meer informatie over de ondersteunde FHIR-functies in azure API voor FHIR. Implementeer vervolgens de Azure-API voor FHIR.
 
>[!div class="nextstepaction"]
>[De Azure-API voor FHIR implementeren](fhir-paas-portal-quickstart.md)
