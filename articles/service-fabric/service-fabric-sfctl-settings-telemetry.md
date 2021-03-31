---
title: Azure Service Fabric CLI-sfctl-instellingen telemetrie
description: Meer informatie over sfctl, de Azure Service Fabric-opdracht regel interface. Bevat een lijst met opdrachten voor het configureren van sfctl-telemetrie.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: ef720a14617b4131474d50875701d0ef27df4151
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86245501"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
Telemetrie-instellingen configureren die lokaal zijn voor dit exemplaar van sfctl.

Sfctl-telemetrie verzamelt opdracht naam zonder opgegeven para meters of hun waarden, Sfctl versie, type besturings systeem, python-versie, het slagen of mislukken van de opdracht, het fout bericht dat wordt geretourneerd.

## <a name="commands"></a>Opdracht

|Opdracht|Beschrijving|
| --- | --- |
| set-telemetrie | Telemetrie in-of uitschakelen. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl-instellingen telemetrie-set-telemetrie
Telemetrie in-of uitschakelen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --uit | Schakel telemetrie uit. |
| --aan | Schakel telemetrie in. Dit is de standaardwaarde. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

### <a name="examples"></a>Voorbeelden

Schakel telemetrie uit.

```
sfctl settings telemetry set_telemetry --off
```

Schakel telemetrie in.

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>Volgende stappen
- [Stel](service-fabric-cli.md) de service Fabric cli in.
- Meer informatie over het gebruik van de Service Fabric CLI met behulp van de [voorbeeld scripts](./scripts/sfctl-upgrade-application.md).
