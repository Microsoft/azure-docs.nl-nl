---
title: Azure Service Fabric CLI-sfctl mesh secretvalue
description: Meer informatie over sfctl, de Azure Service Fabric-opdracht regel interface. Bevat een lijst met opdrachten voor het ophalen en verwijderen van Service Fabric net secretvalue resources.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 985fb505aae96f4ebd1ba8aeb61679081f303243
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86245769"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Net secretvalue-resources ophalen en verwijderen.

## <a name="commands"></a>Opdracht

|Opdracht|Beschrijving|
| --- | --- |
| delete | Hiermee verwijdert u de opgegeven waarde van de naam van de named Secret-resource. |
| list | Lijst met namen van alle waarden van de opgegeven geheime resource. |
| weergeven | Hiermee wordt de opgegeven waarde van de geheime resource weer gegeven. |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl mesh secretvalue verwijderen
Hiermee verwijdert u de opgegeven waarde van de naam van de named Secret-resource.

Hiermee verwijdert u de bron van de geheime waarde die wordt geïdentificeerd door de naam. De naam van de resource is doorgaans de versie die aan die waarde is gekoppeld. Als de opgegeven waarde wordt gebruikt, mislukt de verwijdering.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --geheim-naam-n [vereist] | De naam van de geheime resource. |
| --versie-v [vereist] | De naam van de geheime versie. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-mesh-secretvalue-list"></a>sfctl mesh secretvalue-lijst
Lijst met namen van alle waarden van de opgegeven geheime resource.

Haalt informatie op over alle bronnen van de geheime waarde van de opgegeven geheime resource. De informatie bevat de namen van de resources van de geheime waarde, maar niet de werkelijke waarden.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --geheim-naam-n [vereist] | De naam van de geheime resource. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl mesh secretvalue show
Hiermee wordt de opgegeven waarde van de geheime resource weer gegeven.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --geheim-naam-n [vereist] | De naam van de geheime resource. |
| --versie-v [vereist] | De naam van de geheime versie. |
| --weer geven-waarde | De werkelijke waarde van de geheime versie weer geven. |

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
