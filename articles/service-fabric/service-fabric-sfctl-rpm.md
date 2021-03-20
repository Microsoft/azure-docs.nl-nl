---
title: Azure Service Fabric CLI-sfctl rpm
description: Meer informatie over sfctl, de Azure Service Fabric-opdracht regel interface. Bevat een lijst met opdrachten voor de reparatie Manager-service.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 7317fd66303aaabf5232106aa7391439880bebaf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86260293"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Query's uitvoeren en opdrachten verzenden naar de service reparatie beheer.

## <a name="commands"></a>Opdracht

|Opdracht|Beschrijving|
| --- | --- |
| goed keuren forceren | Hiermee wordt de goed keuring van de opgegeven herstel taak geforceerd. |
| delete | Hiermee verwijdert u een voltooide herstel taak. |
| list | Hiermee wordt een lijst met herstel taken opgehaald die overeenkomen met de opgegeven filters. |

## <a name="sfctl-rpm-approve-force"></a>sfctl rpm goed keuren-forceren
Hiermee wordt de goed keuring van de opgegeven herstel taak geforceerd.

Deze API ondersteunt het Service Fabric-platform. het is niet bedoeld om rechtstreeks vanuit uw code te worden gebruikt.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --taak-id [vereist] | De ID van de herstel taak. |
| --version | Het huidige versie nummer van de herstel taak. Als dit niet het geval is, wordt de aanvraag alleen uitgevoerd als deze waarde overeenkomt met de werkelijke huidige versie van de herstel taak. Als de waarde nul is, wordt er geen versie controle uitgevoerd. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-rpm-delete"></a>sfctl rpm verwijderen
Hiermee verwijdert u een voltooide herstel taak.

Deze API ondersteunt het Service Fabric-platform. het is niet bedoeld om rechtstreeks vanuit uw code te worden gebruikt.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --taak-id [vereist] | De ID van de voltooide herstel taak die moet worden verwijderd. |
| --version | Het huidige versie nummer van de herstel taak. Als dit niet het geval is, wordt de aanvraag alleen uitgevoerd als deze waarde overeenkomt met de werkelijke huidige versie van de herstel taak. Als de waarde nul is, wordt er geen versie controle uitgevoerd. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-rpm-list"></a>sfctl rpm-lijst
Hiermee wordt een lijst met herstel taken opgehaald die overeenkomen met de opgegeven filters.

Deze API ondersteunt het Service Fabric-platform. het is niet bedoeld om rechtstreeks vanuit uw code te worden gebruikt.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --uitvoerbaar filter | De naam van de reparatie-uitvoerder waarvan de gedeclareerde taken moeten worden opgenomen in de lijst. |
| --status-filter | Een bitsgewijze-of van de volgende waarden, opgeven welke taak statussen moeten worden opgenomen in de lijst met resultaten. <ul><li>1-gemaakt</li><li>2-geclaimd</li><li>4: voorbereiden</li><li>8-goedgekeurd</li><li>16-uitvoeren</li><li>32-herstellen</li><li>64-voltooid</li></ul>
| --taak-id-filter | Het voor voegsel van de reparatie taak-ID moet worden gevonden. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |


## <a name="next-steps"></a>Volgende stappen
- [Stel](service-fabric-cli.md) de service Fabric cli in.
- Meer informatie over het gebruik van de Service Fabric CLI met behulp van de [voorbeeld scripts](./scripts/sfctl-upgrade-application.md).
