---
title: Azure Service Fabric CLI-sfctl-gebeurtenissen
description: Beschrijft de Service Fabric CLI-opdrachten voor sfctl-gebeurtenissen.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 635bddef1a98d6ed2b112662d8c9c44fe65e9e32
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "76906102"
---
# <a name="sfctl-events"></a>sfctl-gebeurtenissen
Gebeurtenissen ophalen uit het gebeurtenissen archief (als de Event Store-service al is geïnstalleerd).

De Event Store-systeem service kan worden toegevoegd via een configuratie-upgrade naar elk SFRP-cluster met >= 6,4. Controleer de volgende URL \: https \: //docs.Microsoft.com/azure/service-Fabric/service-Fabric-Diagnostics-eventstore.

## <a name="commands"></a>Opdracht

|Opdracht|Beschrijving|
| --- | --- |
| alle toepassingen-lijst | Hiermee worden alle toepassingen gebeurtenissen opgehaald. |
| Alle knoop punten-lijst | Hiermee worden alle knooppunt gebeurtenissen opgehaald. |
| alle partities-lijst | Hiermee worden alle partities gerelateerde gebeurtenissen opgehaald. |
| alle Services-lijst | Hiermee worden alle services-gerelateerde gebeurtenissen opgehaald. |
| lijst met toepassingen | Hiermee worden gebeurtenissen opgehaald die betrekking hebben op de toepassing. |
| cluster-lijst | Hiermee worden alle aan het cluster gerelateerde gebeurtenissen opgehaald. |
| knooppunten lijst | Hiermee worden gebeurtenissen met betrekking tot een knoop punt opgehaald. |
| partitie-alle replica's-lijst | Hiermee worden alle replica-gerelateerde gebeurtenissen voor een partitie opgehaald. |
| partitie lijst | Hiermee haalt u een partitie-gerelateerde gebeurtenis op. |
| partitie-replica-lijst | Hiermee haalt u gebeurtenissen op die betrekking hebben op de partitie. |
| Service lijst | Hiermee haalt u een service-gerelateerde gebeurtenis op. |

## <a name="sfctl-events-all-applications-list"></a>sfctl-gebeurtenissen alle toepassingen-lijst
Hiermee worden alle toepassingen gebeurtenissen opgehaald.

De reactie is een lijst met ApplicationEvent-objecten.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --eind tijd-UTC [vereist] | De eind tijd van een opzoek query in ISO UTC JJJJ-MM-DDTuu \: mm \: ssZ. |
| --begin tijd-UTC [vereist] | De begin tijd van een opzoek query in ISO UTC JJJJ-MM-DDTuu \: mm \: ssZ. |
| --gebeurtenissen-typen-filter | Dit is een door komma's gescheiden teken reeks waarmee de typen FabricEvents worden opgegeven die alleen in het antwoord mogen worden opgenomen. |
| --exclude-analyse-gebeurtenissen | Met deze para meter wordt het ophalen van AnalysisEvents uitgeschakeld als True is door gegeven. |
| --overs Laan-correlatie-opzoeken | Met deze para meter wordt de zoek opdracht van CorrelatedEvents-gegevens uitgeschakeld als True is door gegeven. anders wordt het veld verwerkte CorrelationEvents ophalen en HasCorrelatedEvents in elke FabricEvent gevuld. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-events-all-nodes-list"></a>sfctl-gebeurtenissen alle knoop punten-lijst
Hiermee worden alle knooppunt gebeurtenissen opgehaald.

De reactie is een lijst met NodeEvent-objecten.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --eind tijd-UTC [vereist] | De eind tijd van een opzoek query in ISO UTC JJJJ-MM-DDTuu \: mm \: ssZ. |
| --begin tijd-UTC [vereist] | De begin tijd van een opzoek query in ISO UTC JJJJ-MM-DDTuu \: mm \: ssZ. |
| --gebeurtenissen-typen-filter | Dit is een door komma's gescheiden teken reeks waarmee de typen FabricEvents worden opgegeven die alleen in het antwoord mogen worden opgenomen. |
| --exclude-analyse-gebeurtenissen | Met deze para meter wordt het ophalen van AnalysisEvents uitgeschakeld als True is door gegeven. |
| --overs Laan-correlatie-opzoeken | Met deze para meter wordt de zoek opdracht van CorrelatedEvents-gegevens uitgeschakeld als True is door gegeven. anders wordt het veld verwerkte CorrelationEvents ophalen en HasCorrelatedEvents in elke FabricEvent gevuld. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-events-all-partitions-list"></a>sfctl events all-partitions-List
Hiermee worden alle partities gerelateerde gebeurtenissen opgehaald.

De reactie is een lijst met PartitionEvent-objecten.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --eind tijd-UTC [vereist] | De eind tijd van een opzoek query in ISO UTC JJJJ-MM-DDTuu \: mm \: ssZ. |
| --begin tijd-UTC [vereist] | De begin tijd van een opzoek query in ISO UTC JJJJ-MM-DDTuu \: mm \: ssZ. |
| --gebeurtenissen-typen-filter | Dit is een door komma's gescheiden teken reeks waarmee de typen FabricEvents worden opgegeven die alleen in het antwoord mogen worden opgenomen. |
| --exclude-analyse-gebeurtenissen | Met deze para meter wordt het ophalen van AnalysisEvents uitgeschakeld als True is door gegeven. |
| --overs Laan-correlatie-opzoeken | Met deze para meter wordt de zoek opdracht van CorrelatedEvents-gegevens uitgeschakeld als True is door gegeven. anders wordt het veld verwerkte CorrelationEvents ophalen en HasCorrelatedEvents in elke FabricEvent gevuld. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-events-all-services-list"></a>sfctl-gebeurtenissen alle Services-lijst
Hiermee worden alle services-gerelateerde gebeurtenissen opgehaald.

De reactie is een lijst met ServiceEvent-objecten.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --eind tijd-UTC [vereist] | De eind tijd van een opzoek query in ISO UTC JJJJ-MM-DDTuu \: mm \: ssZ. |
| --begin tijd-UTC [vereist] | De begin tijd van een opzoek query in ISO UTC JJJJ-MM-DDTuu \: mm \: ssZ. |
| --gebeurtenissen-typen-filter | Dit is een door komma's gescheiden teken reeks waarmee de typen FabricEvents worden opgegeven die alleen in het antwoord mogen worden opgenomen. |
| --exclude-analyse-gebeurtenissen | Met deze para meter wordt het ophalen van AnalysisEvents uitgeschakeld als True is door gegeven. |
| --overs Laan-correlatie-opzoeken | Met deze para meter wordt de zoek opdracht van CorrelatedEvents-gegevens uitgeschakeld als True is door gegeven. anders wordt het veld verwerkte CorrelationEvents ophalen en HasCorrelatedEvents in elke FabricEvent gevuld. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-events-application-list"></a>sfctl-gebeurtenissen toepassing-lijst
Hiermee worden gebeurtenissen opgehaald die betrekking hebben op de toepassing.

De reactie is een lijst met ApplicationEvent-objecten.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --toepassings-id [vereist] | De identiteit van de toepassing. Dit is doorgaans de volledige naam van de toepassing zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de naam van de toepassing bijvoorbeeld ' Fabric \: /MyApp/app1 ' is, is de toepassings identiteit ' Mijntoep \~ app1 ' in 6.0 + en ' Mijntoep/app1 ' in vorige versies. |
| --eind tijd-UTC [vereist] | De eind tijd van een opzoek query in ISO UTC JJJJ-MM-DDTuu \: mm \: ssZ. |
| --begin tijd-UTC [vereist] | De begin tijd van een opzoek query in ISO UTC JJJJ-MM-DDTuu \: mm \: ssZ. |
| --gebeurtenissen-typen-filter | Dit is een door komma's gescheiden teken reeks waarmee de typen FabricEvents worden opgegeven die alleen in het antwoord mogen worden opgenomen. |
| --exclude-analyse-gebeurtenissen | Met deze para meter wordt het ophalen van AnalysisEvents uitgeschakeld als True is door gegeven. |
| --overs Laan-correlatie-opzoeken | Met deze para meter wordt de zoek opdracht van CorrelatedEvents-gegevens uitgeschakeld als True is door gegeven. anders wordt het veld verwerkte CorrelationEvents ophalen en HasCorrelatedEvents in elke FabricEvent gevuld. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-events-cluster-list"></a>sfctl-gebeurtenissen cluster-lijst
Hiermee worden alle aan het cluster gerelateerde gebeurtenissen opgehaald.

De reactie is een lijst met ClusterEvent-objecten.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --eind tijd-UTC [vereist] | De eind tijd van een opzoek query in ISO UTC JJJJ-MM-DDTuu \: mm \: ssZ. |
| --begin tijd-UTC [vereist] | De begin tijd van een opzoek query in ISO UTC JJJJ-MM-DDTuu \: mm \: ssZ. |
| --gebeurtenissen-typen-filter | Dit is een door komma's gescheiden teken reeks waarmee de typen FabricEvents worden opgegeven die alleen in het antwoord mogen worden opgenomen. |
| --exclude-analyse-gebeurtenissen | Met deze para meter wordt het ophalen van AnalysisEvents uitgeschakeld als True is door gegeven. |
| --overs Laan-correlatie-opzoeken | Met deze para meter wordt de zoek opdracht van CorrelatedEvents-gegevens uitgeschakeld als True is door gegeven. anders wordt het veld verwerkte CorrelationEvents ophalen en HasCorrelatedEvents in elke FabricEvent gevuld. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-events-node-list"></a>knoop punt sfctl-gebeurtenissen-lijst
Hiermee worden gebeurtenissen met betrekking tot een knoop punt opgehaald.

De reactie is een lijst met NodeEvent-objecten.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --eind tijd-UTC [vereist] | De eind tijd van een opzoek query in ISO UTC JJJJ-MM-DDTuu \: mm \: ssZ. |
| --node-name [required] | De naam van het knoop punt. |
| --begin tijd-UTC [vereist] | De begin tijd van een opzoek query in ISO UTC JJJJ-MM-DDTuu \: mm \: ssZ. |
| --gebeurtenissen-typen-filter | Dit is een door komma's gescheiden teken reeks waarmee de typen FabricEvents worden opgegeven die alleen in het antwoord mogen worden opgenomen. |
| --exclude-analyse-gebeurtenissen | Met deze para meter wordt het ophalen van AnalysisEvents uitgeschakeld als True is door gegeven. |
| --overs Laan-correlatie-opzoeken | Met deze para meter wordt de zoek opdracht van CorrelatedEvents-gegevens uitgeschakeld als True is door gegeven. anders wordt het veld verwerkte CorrelationEvents ophalen en HasCorrelatedEvents in elke FabricEvent gevuld. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-events-partition-all-replicas-list"></a>sfctl-gebeurtenissen Partition-alle-replica's-lijst
Hiermee worden alle replica-gerelateerde gebeurtenissen voor een partitie opgehaald.

De reactie is een lijst met ReplicaEvent-objecten.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --eind tijd-UTC [vereist] | De eind tijd van een opzoek query in ISO UTC JJJJ-MM-DDTuu \: mm \: ssZ. |
| --partitie-id [vereist] | De identiteit van de partitie. |
| --begin tijd-UTC [vereist] | De begin tijd van een opzoek query in ISO UTC JJJJ-MM-DDTuu \: mm \: ssZ. |
| --gebeurtenissen-typen-filter | Dit is een door komma's gescheiden teken reeks waarmee de typen FabricEvents worden opgegeven die alleen in het antwoord mogen worden opgenomen. |
| --exclude-analyse-gebeurtenissen | Met deze para meter wordt het ophalen van AnalysisEvents uitgeschakeld als True is door gegeven. |
| --overs Laan-correlatie-opzoeken | Met deze para meter wordt de zoek opdracht van CorrelatedEvents-gegevens uitgeschakeld als True is door gegeven. anders wordt het veld verwerkte CorrelationEvents ophalen en HasCorrelatedEvents in elke FabricEvent gevuld. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-events-partition-list"></a>partitie met sfctl-gebeurtenissen-lijst
Hiermee haalt u een partitie-gerelateerde gebeurtenis op.

De reactie is een lijst met PartitionEvent-objecten.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --eind tijd-UTC [vereist] | De eind tijd van een opzoek query in ISO UTC JJJJ-MM-DDTuu \: mm \: ssZ. |
| --partitie-id [vereist] | De identiteit van de partitie. |
| --begin tijd-UTC [vereist] | De begin tijd van een opzoek query in ISO UTC JJJJ-MM-DDTuu \: mm \: ssZ. |
| --gebeurtenissen-typen-filter | Dit is een door komma's gescheiden teken reeks waarmee de typen FabricEvents worden opgegeven die alleen in het antwoord mogen worden opgenomen. |
| --exclude-analyse-gebeurtenissen | Met deze para meter wordt het ophalen van AnalysisEvents uitgeschakeld als True is door gegeven. |
| --overs Laan-correlatie-opzoeken | Met deze para meter wordt de zoek opdracht van CorrelatedEvents-gegevens uitgeschakeld als True is door gegeven. anders wordt het veld verwerkte CorrelationEvents ophalen en HasCorrelatedEvents in elke FabricEvent gevuld. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-events-partition-replica-list"></a>partitie met sfctl-gebeurtenissen-replica-lijst
Hiermee haalt u gebeurtenissen op die betrekking hebben op de partitie.

De reactie is een lijst met ReplicaEvent-objecten.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --eind tijd-UTC [vereist] | De eind tijd van een opzoek query in ISO UTC JJJJ-MM-DDTuu \: mm \: ssZ. |
| --partitie-id [vereist] | De identiteit van de partitie. |
| --replica-id [vereist] | De id van de replica. |
| --begin tijd-UTC [vereist] | De begin tijd van een opzoek query in ISO UTC JJJJ-MM-DDTuu \: mm \: ssZ. |
| --gebeurtenissen-typen-filter | Dit is een door komma's gescheiden teken reeks waarmee de typen FabricEvents worden opgegeven die alleen in het antwoord mogen worden opgenomen. |
| --exclude-analyse-gebeurtenissen | Met deze para meter wordt het ophalen van AnalysisEvents uitgeschakeld als True is door gegeven. |
| --overs Laan-correlatie-opzoeken | Met deze para meter wordt de zoek opdracht van CorrelatedEvents-gegevens uitgeschakeld als True is door gegeven. anders wordt het veld verwerkte CorrelationEvents ophalen en HasCorrelatedEvents in elke FabricEvent gevuld. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-events-service-list"></a>sfctl-gebeurtenissen service-lijst
Hiermee haalt u een service-gerelateerde gebeurtenis op.

De reactie is een lijst met ServiceEvent-objecten.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --eind tijd-UTC [vereist] | De eind tijd van een opzoek query in ISO UTC JJJJ-MM-DDTuu \: mm \: ssZ. |
| --Service-id [vereist] | De identiteit van de service. Deze ID is doorgaans de volledige naam van de service zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de service naam bijvoorbeeld ' Fabric \: /MyApp/app1/svc1 ' is, is de service-identiteit ' Mijntoep \~ app1 \~ svc1 ' in 6.0 + en ' Mijntoep/app1/svc1 ' in vorige versies. |
| --begin tijd-UTC [vereist] | De begin tijd van een opzoek query in ISO UTC JJJJ-MM-DDTuu \: mm \: ssZ. |
| --gebeurtenissen-typen-filter | Dit is een door komma's gescheiden teken reeks waarmee de typen FabricEvents worden opgegeven die alleen in het antwoord mogen worden opgenomen. |
| --exclude-analyse-gebeurtenissen | Met deze para meter wordt het ophalen van AnalysisEvents uitgeschakeld als True is door gegeven. |
| --overs Laan-correlatie-opzoeken | Met deze para meter wordt de zoek opdracht van CorrelatedEvents-gegevens uitgeschakeld als True is door gegeven. anders wordt het veld verwerkte CorrelationEvents ophalen en HasCorrelatedEvents in elke FabricEvent gevuld. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

