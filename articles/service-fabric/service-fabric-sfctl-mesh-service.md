---
title: Azure Service Fabric CLI-sfctl-mesh service
description: Meer informatie over sfctl, de Azure Service Fabric-opdracht regel interface. Bevat een lijst met opdrachten voor het ophalen van service Details voor een toepassings bron.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 68ea876d9951b49a6683cc74df8b9107fd942e51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86245667"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
Haal service Details en lijst Services van een toepassings bron op.

## <a name="commands"></a>Opdracht

|Opdracht|Beschrijving|
| --- | --- |
| list | Een lijst met alle service bronnen. |
| weergeven | Hiermee wordt de service resource met de opgegeven naam opgehaald. |

## <a name="sfctl-mesh-service-list"></a>sfctl mesh-service lijst
Een lijst met alle service bronnen.

Hiermee haalt u de informatie op over alle services van een toepassings bron. De informatie bevat de beschrijving en andere eigenschappen van de service.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --app-naam--Application name [required] | De naam van de toepassing. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-mesh-service-show"></a>sfctl mesh service show
Hiermee wordt de service resource met de opgegeven naam opgehaald.

Hiermee wordt de informatie opgehaald over de service resource met de opgegeven naam. De informatie bevat de beschrijving en andere eigenschappen van de service.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --app-naam--Application name [required] | De naam van de toepassing. |
| --naam-n [vereist] | De naam van de service. |

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
