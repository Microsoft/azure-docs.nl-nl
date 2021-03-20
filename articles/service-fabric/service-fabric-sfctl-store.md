---
title: Azure Service Fabric CLI-sfctl Store
description: Meer informatie over sfctl, de Azure Service Fabric-opdracht regel interface. Bevat een lijst met opdrachten voor het uitvoeren van bewerkingen op bestands niveau in het cluster installatie kopie archief.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: af5399be44d2946775622598c5c1db3d8355f7cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86257067"
---
# <a name="sfctl-store"></a>sfctl store
Voer elementaire bewerkingen op bestands niveau uit in het cluster installatie kopie archief.

## <a name="commands"></a>Opdracht

|Opdracht|Beschrijving|
| --- | --- |
| delete | Hiermee verwijdert u de bestaande inhoud van het installatie kopie archief. |
| hoofdmap-info | Hiermee haalt u de inhouds informatie op in de hoofdmap van het archief met installatie kopieën. |
| gave | Hiermee wordt de informatie over de inhoud van de installatie kopie opgehaald. |

## <a name="sfctl-store-delete"></a>sfctl Store verwijderen
Hiermee verwijdert u de bestaande inhoud van het installatie kopie archief.

Hiermee verwijdert u de bestaande inhoud van het archief van de installatie kopie in het relatieve pad van de opgegeven afbeeldings opslag. Deze opdracht kan worden gebruikt om Geüploade toepassings pakketten te verwijderen nadat deze zijn ingericht.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --content-path [required] | Het relatieve pad naar het bestand of de map in het archief van de installatie kopie van de hoofdmap. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-store-root-info"></a>hoofdmap van sfctl-archief-info
Hiermee haalt u de inhouds informatie op in de hoofdmap van het archief met installatie kopieën.

Hiermee wordt de informatie over de inhoud van de installatie kopie in de hoofdmap van de installatie kopie-Archief geretourneerd.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-store-stat"></a>sfctl Store-stat
Hiermee wordt de informatie over de inhoud van de installatie kopie opgehaald.

Retourneert informatie over de inhoud van de afbeeldings opslag in de opgegeven contentPath. De contentPath is relatief ten opzichte van de hoofdmap van de opslag voor installatie kopieën.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --content-path [required] | Het relatieve pad naar het bestand of de map in het archief van de installatie kopie van de hoofdmap. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

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
