---
title: Azure Service Fabric CLI-sfctl mesh code-pakket-log
description: Meer informatie over sfctl, de Azure Service Fabric-opdracht regel interface. Bevat een lijst met opdrachten voor het verkrijgen van Logboeken voor een opgegeven code pakket.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 9ac1d85a1a498f9f6fcd0a03f8f819d1cdfcac33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86257294"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
De logboeken ophalen voor de container van het opgegeven code pakket voor de opgegeven service replica.

## <a name="commands"></a>Opdracht

|Opdracht|Beschrijving|
| --- | --- |
| ophalen | Hiermee worden de logboeken van de container opgehaald. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl netcode-pakket-logboek ophalen
Hiermee worden de logboeken van de container opgehaald.

Hiermee haalt u de logboeken voor de container van het opgegeven code pakket van de service replica.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --app-naam--Application name [required] | De naam van de toepassing. |
| --code-pakket naam [vereist] | De naam van het code pakket van de service. |
| --replica-naam [vereist] | Service Fabric replica naam. |
| --Service-naam [vereist] | De naam van de service. |
| --staart | Aantal regels dat moet worden weer gegeven aan het einde van de logboeken. De standaard waarde is 100. ' all ' om de volledige logboeken weer te geven. |

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
