---
title: Azure Service Fabric CLI-sfctl chaos-schema
description: Meer informatie over sfctl, de Azure Service Fabric-opdracht regel interface. Bevat een lijst met opdrachten voor het plannen van chaos.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: dd911574cd37c18e3043cfc2482bd1fcd5f3857b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260857"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
De chaos-planning ophalen en instellen.

## <a name="commands"></a>Opdracht

|Opdracht|Beschrijving|
| --- | --- |
| ophalen | Het chaos-schema ophalen dat definieert wanneer en hoe chaos moet worden uitgevoerd. |
| set | Het schema instellen dat door chaos wordt gebruikt. |

## <a name="sfctl-chaos-schedule-get"></a>sfctl chaos planning ophalen
Het chaos-schema ophalen dat definieert wanneer en hoe chaos moet worden uitgevoerd.

Hiermee haalt u de versie van de chaos-planning in gebruik en het chaos-schema dat definieert wanneer en hoe chaos moet worden uitgevoerd.

### <a name="arguments"></a>Argumenten

|Argument|Description|
| --- | --- |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Description|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl chaos-schema instellen
Het schema instellen dat door chaos wordt gebruikt.

Chaos wordt automatisch gepland op basis van het chaos-schema. Het chaos-schema wordt bijgewerkt als de geleverde versie overeenkomt met de versie op de server. Wanneer u de chaos-planning bijwerkt, wordt de versie op de server verhoogd met 1. De versie op de server keert terug naar 0 na het bereiken van een groot getal. Als chaos wordt uitgevoerd wanneer deze aanroep wordt gedaan, mislukt de aanroep.

### <a name="arguments"></a>Argumenten

|Argument|Description|
| --- | --- |
| --chaos-para meters-Dictionary | JSON-gecodeerde lijst die een toewijzing van teken reeks namen vertegenwoordigt aan ChaosParameters die moeten worden gebruikt door taken. |
| --verloop datum-UTC | De datum en tijd waarop wordt gebruikgemaakt van het schema om chaos te plannen.  Standaard: \: 9999-12-31T23 \: 59 \: 59.999 z. |
| --taken | JSON-gecodeerde lijst met ChaosScheduleJobs die aangeeft wanneer chaos moet worden uitgevoerd en met welke para meters chaos moeten worden uitgevoerd. |
| --begin datum-UTC | De datum en tijd waarop het schema moet worden gebruikt om chaos te plannen.  Standaard \: 1601-01-01T00 \: 00 \: 00.000 z. |
| --time-out-t | Standaard \: 60. |
| --version | Het versie nummer van het schema. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Description|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

### <a name="examples"></a>Voorbeelden

Met de volgende opdracht stelt u een planning in (ervan uitgaande dat het huidige schema versie 0 heeft) die begint op 2016-01-01 en verloopt op 2038-01-01 dat chaos 24 uur van de dag, 7 dagen per week uitvoert.
Chaos wordt voor die tijd gepland op het cluster.
```
sfctl chaos schedule set --version 0 --start-date-utc "2016-01-01T00:00:00.000Z" --expiry-date-utc "2038-01-01T00:00:00.000Z"
    --chaos-parameters-dictionary
    [
    {
        "Key":"adhoc",
        "Value":{
            "MaxConcurrentFaults":3,
            "EnableMoveReplicaFaults":true,
            "ChaosTargetFilter":{
                "NodeTypeInclusionList":[
                "N0010Ref",
                "N0020Ref",
                "N0030Ref",
                "N0040Ref",
                "N0050Ref"
                ]
            },
            "MaxClusterStabilizationTimeoutInSeconds":60,
            "WaitTimeBetweenIterationsInSeconds":15,
            "WaitTimeBetweenFaultsInSeconds":30,
            "TimeToRunInSeconds":"600",
            "Context":{
                "Map":{
                "test":"value"
                }
            },
            "ClusterHealthPolicy":{
                "MaxPercentUnhealthyNodes":0,
                "ConsiderWarningAsError":true,
                "MaxPercentUnhealthyApplications":0
            }
        }
    }
    ]
    --jobs
    [
    {
        "ChaosParameters":"adhoc",
        "Days":{
            "Sunday":true,
            "Monday":true,
            "Tuesday":true,
            "Wednesday":true,
            "Thursday":true,
            "Friday":true,
            "Saturday":true
        },
        "Times":[
            {
                "StartTime":{
                "Hour":0,
                "Minute":0
                },
                "EndTime":{
                "Hour":23,
                "Minute":59
                }
            }
        ]
    }
    ]
```



## <a name="next-steps"></a>Volgende stappen
- [Stel](service-fabric-cli.md) de service Fabric cli in.
- Meer informatie over het gebruik van de Service Fabric CLI met behulp van de [voorbeeld scripts](./scripts/sfctl-upgrade-application.md).
