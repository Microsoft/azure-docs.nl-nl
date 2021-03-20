---
title: Azure Service Fabric CLI-sfctl mesh-gateway
description: Meer informatie over sfctl, de Azure Service Fabric-opdracht regel interface. Bevat een lijst met opdrachten voor het ophalen en verwijderen van Service Fabric netgateway-resources.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 9b6766137dd88a5a780dcca7b6eab7c6c3f9bbf4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86260394"
---
# <a name="sfctl-mesh-gateway"></a>sfctl mesh gateway
Netgateway-resources ophalen en verwijderen.

## <a name="commands"></a>Opdracht

|Opdracht|Beschrijving|
| --- | --- |
| delete | Hiermee verwijdert u de gateway resource. |
| list | Een lijst met alle gateway-resources. |
| weergeven | Hiermee wordt de gateway resource met de opgegeven naam opgehaald. |

## <a name="sfctl-mesh-gateway-delete"></a>sfctl net gateway verwijderen
Hiermee verwijdert u de gateway resource.

Hiermee verwijdert u de gateway bron geïdentificeerd door de naam.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --naam-n [vereist] | De naam van de gateway resource. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-mesh-gateway-list"></a>sfctl mesh-gateway lijst
Een lijst met alle gateway-resources.

Hiermee wordt de informatie opgehaald over alle gateway-resources in een bepaalde resource groep. De informatie bevat de beschrijving en andere eigenschappen van de gateway.

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-mesh-gateway-show"></a>sfctl net gateway show
Hiermee wordt de gateway resource met de opgegeven naam opgehaald.

Hiermee wordt de informatie opgehaald over de gateway resource met de opgegeven naam. De informatie bevat de beschrijving en andere eigenschappen van de gateway.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --naam-n [vereist] | De naam van de gateway resource. |

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
