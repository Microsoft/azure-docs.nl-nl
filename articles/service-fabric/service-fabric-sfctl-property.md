---
title: Azure Service Fabric CLI-sfctl eigenschap
description: Meer informatie over sfctl, de Azure Service Fabric-opdracht regel interface. Bevat een lijst met opdrachten voor het opslaan en opvragen van eigenschappen.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 0a5ebd4822c5f0ff1735464bb4d5b42c436ee529
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260327"
---
# <a name="sfctl-property"></a>sfctl property
Eigenschappen voor opslaan en opvragen onder Service Fabric namen.

## <a name="commands"></a>Opdracht

|Opdracht|Beschrijving|
| --- | --- |
| delete | Hiermee verwijdert u de opgegeven Service Fabric eigenschap. |
| ophalen | Hiermee wordt de opgegeven Service Fabric-eigenschap opgehaald. |
| list | Haalt informatie op over alle Service Fabric eigenschappen onder een bepaalde naam. |
| plaatsen | Hiermee wordt een Service Fabric eigenschap gemaakt of bijgewerkt. |

## <a name="sfctl-property-delete"></a>eigenschap sfctl verwijderen
Hiermee verwijdert u de opgegeven Service Fabric eigenschap.

Hiermee verwijdert u de opgegeven Service Fabric eigenschap onder een bepaalde naam. Een eigenschap moet worden gemaakt voordat deze kan worden verwijderd.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --naam-id [vereist] | De naam van de Service Fabric, zonder het \: URI-schema ' fabric '. |
| --eigenschap-naam [vereist] | Hiermee geeft u de naam op van de eigenschap die moet worden opgehaald. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-property-get"></a>Get-eigenschap sfctl
Hiermee wordt de opgegeven Service Fabric-eigenschap opgehaald.

Hiermee wordt de opgegeven Service Fabric eigenschap met een opgegeven naam opgehaald. Hiermee worden altijd zowel de waarde als de meta gegevens geretourneerd.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --naam-id [vereist] | De naam van de Service Fabric, zonder het \: URI-schema ' fabric '. |
| --eigenschap-naam [vereist] | Hiermee geeft u de naam op van de eigenschap die moet worden opgehaald. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-property-list"></a>sfctl-eigenschappen lijst
Haalt informatie op over alle Service Fabric eigenschappen onder een bepaalde naam.

Een Service Fabric naam kan een of meer benoemde eigenschappen hebben die aangepaste informatie opslaan. Met deze bewerking wordt de informatie over deze eigenschappen opgehaald in een lijst met webpagina's. De gegevens bevatten naam, waarde en meta gegevens over elk van de eigenschappen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --naam-id [vereist] | De naam van de Service Fabric, zonder het \: URI-schema ' fabric '. |
| --vervolg token | De vervolg token parameter wordt gebruikt om de volgende set resultaten op te halen. Een vervolg token met een niet-lege waarde wordt opgenomen in het antwoord van de API wanneer de resultaten van het systeem niet in één antwoord passen. Wanneer deze waarde wordt door gegeven aan de volgende API-aanroep, retourneert de API de volgende set resultaten. Als er geen verdere resultaten zijn, bevat het vervolg token geen waarde. De waarde van deze para meter mag geen URL-code ring zijn. |
| --include--waarden | Hiermee kunt u opgeven of de waarden van de geretourneerde eigenschappen moeten worden meegenomen. Waar als waarden moeten worden geretourneerd met de meta gegevens; False om alleen meta gegevens van eigenschappen te retour neren. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-property-put"></a>eigenschap sfctl put
Hiermee wordt een Service Fabric eigenschap gemaakt of bijgewerkt.

Hiermee wordt de opgegeven Service Fabric eigenschap met een bepaalde naam gemaakt of bijgewerkt.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --naam-id [vereist] | De naam van de Service Fabric, zonder het \: URI-schema ' fabric '. |
| --eigenschap-naam [vereist] | De naam van de eigenschap Service Fabric. |
| --waarde [vereist] | Beschrijft een Service Fabric eigenschaps waarde. Dit is een JSON-teken reeks. <br><br> De JSON-teken reeks heeft twee velden: het type van de gegevens en de waarde die is ingevoerd als gegevens van de gegevens. De ' kind ' moet het eerste item zijn dat in de JSON-teken reeks wordt weer gegeven en kan waarden ' binary ', ' Int64 ', ' Double ', ' String ' of ' GUID ' zijn. De waarde moet een serialisatie kunnen hebben met de opgegeven typen. De waarden ' type ' en ' data ' moeten worden weer gegeven als teken reeksen. |
| --aangepast-ID-type | De aangepaste type-ID van de eigenschap. Met deze eigenschap kan de gebruiker het type van de waarde van de eigenschap labelen. |
| --time-out-t | Standaard \: 60. |

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
