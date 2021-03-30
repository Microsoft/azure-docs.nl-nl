---
title: Azure Service Fabric CLI-sfctl-partitie
description: Meer informatie over sfctl, de Azure Service Fabric-opdracht regel interface. Bevat een lijst met opdrachten voor het beheren van partities voor een service.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fff569c586548d84ed55018764363ad7f05e526d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86257084"
---
# <a name="sfctl-partition"></a>sfctl partition
Zoek en beheer partities voor elke service.

## <a name="commands"></a>Opdracht

|Opdracht|Beschrijving|
| --- | --- |
| gegevens verlies | Met deze API worden gegevens verlies voor de opgegeven partitie veroorzaakt. |
| gegevens verlies-status | Hiermee wordt de voortgang opgehaald van een bewerking voor het verlies van een partitie gegevens die is gestart met de StartDataLoss-API. |
| status | Hiermee wordt de status van de opgegeven Service Fabric partitie opgehaald. |
| Info | Hiermee haalt u de informatie over een Service Fabric partitie. |
| list | Hiermee haalt u de lijst met partities van een Service Fabric-service op. |
| laden | Hiermee haalt u de gegevens van de belasting van de opgegeven Service Fabric partitie. |
| laden-opnieuw instellen | Hiermee wordt de huidige belasting van een Service Fabric partitie opnieuw ingesteld. |
| quorum-verlies | Induceert quorum verlies voor een bepaalde stateful service partitie. |
| quorum-verlies-status | Hiermee wordt de voortgang van een quorum verlies bewerking opgehaald voor een partitie die is gestart met behulp van de StartQuorumLoss-API. |
| herstellen | Geeft aan dat het Service Fabric cluster moet proberen een specifieke partitie te herstellen die momenteel is vastgelopen in quorum verlies. |
| herstellen-alle | Geeft aan dat het Service Fabric cluster moet proberen om services (inclusief systeem Services) te herstellen die momenteel zijn vastgelopen in quorum verlies. |
| rapport-status | Hiermee verzendt u een status rapport op de Service Fabric partitie. |
| restart | Met deze API worden enkele of alle replica's of exemplaren van de opgegeven partitie opnieuw gestart. |
| opnieuw starten: status | Hiermee wordt de voortgang opgehaald van een PartitionRestart-bewerking die is gestart met behulp van StartPartitionRestart. |
| SVC-naam | Hiermee wordt de naam opgehaald van de Service Fabric-service voor een partitie. |

## <a name="sfctl-partition-data-loss"></a>sfctl-partitie gegevens-verlies
Met deze API worden gegevens verlies voor de opgegeven partitie veroorzaakt.

Er wordt een aanroep naar de OnDataLossAsync-API van de partitie geactiveerd.  Met deze API worden gegevens verlies voor de opgegeven partitie veroorzaakt. Er wordt een aanroep naar de OnDataLoss-API van de partitie geactiveerd. Het werkelijke gegevens verlies is afhankelijk van de opgegeven DataLossMode.
- PartialDataLoss: alleen een quorum van replica's wordt verwijderd en OnDataLoss wordt geactiveerd voor de partitie, maar de werkelijke gegevens verlies is afhankelijk van de aanwezigheid van de replicatie in de vlucht.  
- FullDataLoss: alle replica's worden verwijderd, dus alle gegevens gaan verloren en OnDataLoss wordt geactiveerd. Deze API mag alleen worden aangeroepen met een stateful service als doel. Het aanroepen van deze API met een systeem service als het doel wordt niet aanbevolen.

> [!NOTE]   
> Nadat deze API is aangeroepen, kan deze niet meer worden omgekeerd. Het aanroepen van CancelOperation stopt alleen de uitvoering en opschonen van de interne systeem status. Er worden geen gegevens hersteld als de opdracht ver genoeg is uitgevoerd om gegevens verlies te veroorzaken. Roep de GetDataLossProgress-API aan met dezelfde OperationId om informatie te retour neren over de bewerking die is gestart met deze API.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --gegevens verlies-modus [vereist] | Deze Enum wordt door gegeven aan de StartDataLoss-API om aan te geven welk type gegevens verlies moet worden veroorzaakt. |
| --bewerking-id [vereist] | Een GUID die een aanroep van deze API identificeert.  Dit wordt door gegeven aan de bijbehorende GetProgress-API. |
| --partitie-id [vereist] | De identiteit van de partitie. |
| --Service-id [vereist] | De identiteit van de service. Deze ID is doorgaans de volledige naam van de service zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de service naam bijvoorbeeld ' Fabric \: /MyApp/app1/svc1 ' is, is de service-identiteit ' Mijntoep \~ app1 \~ svc1 ' in 6.0 + en ' Mijntoep/app1/svc1 ' in vorige versies. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-partition-data-loss-status"></a>sfctl Partition data-verlies-status
Hiermee wordt de voortgang opgehaald van een bewerking voor het verlies van een partitie gegevens die is gestart met de StartDataLoss-API.

Hiermee wordt de voortgang opgehaald van een bewerking voor gegevens verlies die is gestart met StartDataLoss met behulp van de OperationId.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --bewerking-id [vereist] | Een GUID die een aanroep van deze API identificeert.  Dit wordt door gegeven aan de bijbehorende GetProgress-API. |
| --partitie-id [vereist] | De identiteit van de partitie. |
| --Service-id [vereist] | De identiteit van de service. Deze ID is doorgaans de volledige naam van de service zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de service naam bijvoorbeeld ' Fabric \: /MyApp/app1/svc1 ' is, is de service-identiteit ' Mijntoep \~ app1 \~ svc1 ' in 6.0 + en ' Mijntoep/app1/svc1 ' in vorige versies. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-partition-health"></a>status van sfctl-partitie
Hiermee wordt de status van de opgegeven Service Fabric partitie opgehaald.

Gebruik EventsHealthStateFilter om de verzameling van status gebeurtenissen die op de service zijn gerapporteerd, te filteren op basis van de status. Gebruik ReplicasHealthStateFilter om de verzameling van ReplicaHealthState-objecten op de partitie te filteren. Als u een partitie opgeeft die niet voor komt in de Health Store, retourneert deze aanvraag een fout.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --partitie-id [vereist] | De identiteit van de partitie. |
| --gebeurtenissen-status-filter | Hiermee kunt u het verzamelen van HealthEvent-objecten die zijn geretourneerd op basis van de status wordt gefilterd. De mogelijke waarden voor deze para meter zijn gehele getallen van een van de volgende statussen. Alleen gebeurtenissen die overeenkomen met het filter worden geretourneerd. Alle gebeurtenissen worden gebruikt om de geaggregeerde status te evalueren. Als u niets opgeeft, worden alle vermeldingen geretourneerd. De status waarden zijn inventarisatie op basis van een vlag, waardoor de waarde kan bestaan uit een combi natie van deze waarden, verkregen met behulp van de operator bitsgewijze ' of '. Als de opgegeven waarde bijvoorbeeld 6 is, worden alle gebeurtenissen met HealthState waarde OK (2) en waarschuwing (4) geretourneerd.  <br> -Standaard-standaard waarde. Komt overeen met een wille keurige HealthState. De waarde is nul.  <br> -Geen: filter dat niet overeenkomt met een HealthState-waarde. Wordt gebruikt om geen resultaten te retour neren voor een bepaalde verzameling statussen. De waarde is 1.  <br> -OK-filter dat overeenkomt met de invoer met HealthState waarde OK. De waarde is 2.  <br> -Waarschuwings filter dat overeenkomt met invoer met HealthState-waarde waarschuwing. De waarde is 4.  <br> -Fout filter dat overeenkomt met de invoer met de HealthState-waarde fout. De waarde is 8.  <br> -Alle-filter die overeenkomt met invoer met een wille keurige waarde van HealthState. De waarde is 65535. |
| --exclude-status-statistieken | Hiermee wordt aangegeven of de status statistieken moeten worden geretourneerd als onderdeel van het query resultaat. Standaard onwaar. In de statistieken wordt het aantal onderliggende entiteiten in de status OK, waarschuwing en fout weer gegeven. |
| --replica's-status-filter | Hiermee kunt u het verzamelen van ReplicaHealthState-objecten op de partitie filteren. De waarde kan worden verkregen van leden of bitsgewijze bewerkingen op leden van HealthStateFilter. Alleen replica's die overeenkomen met het filter worden geretourneerd. Alle replica's worden gebruikt om de geaggregeerde status te evalueren. Als u niets opgeeft, worden alle vermeldingen geretourneerd. De status waarden zijn inventarisatie op basis van een vlag, waardoor de waarde kan bestaan uit een combi natie van deze waarden die zijn verkregen met behulp van de operator bitsgewijze of. Als de opgegeven waarde bijvoorbeeld 6 is, worden alle gebeurtenissen met HealthState waarde OK (2) en waarschuwing (4) geretourneerd. De mogelijke waarden voor deze para meter zijn gehele getallen van een van de volgende statussen.  <br> -Standaard-standaard waarde. Komt overeen met een wille keurige HealthState. De waarde is nul.  <br> -Geen: filter dat niet overeenkomt met een HealthState-waarde. Wordt gebruikt om geen resultaten te retour neren voor een bepaalde verzameling statussen. De waarde is 1.  <br> -OK-filter dat overeenkomt met de invoer met HealthState waarde OK. De waarde is 2.  <br> -Waarschuwings filter dat overeenkomt met invoer met HealthState-waarde waarschuwing. De waarde is 4.  <br> -Fout filter dat overeenkomt met de invoer met de HealthState-waarde fout. De waarde is 8.  <br> -Alle-filter die overeenkomt met invoer met een wille keurige waarde van HealthState. De waarde is 65535. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-partition-info"></a>sfctl-partitie-informatie
Hiermee haalt u de informatie over een Service Fabric partitie.

Hiermee wordt de informatie opgehaald over de opgegeven partitie. Het antwoord bevat de partitie-ID, het partitioneren van schema-informatie, sleutels die worden ondersteund door de partitie, status, Health en andere informatie over de partitie.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --partitie-id [vereist] | De identiteit van de partitie. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-partition-list"></a>sfctl-partitie lijst
Hiermee haalt u de lijst met partities van een Service Fabric-service op.

Het antwoord bevat de partitie-ID, het partitioneren van schema-informatie, sleutels die worden ondersteund door de partitie, status, Health en andere informatie over de partitie.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --Service-id [vereist] | De identiteit van de service. Deze ID is doorgaans de volledige naam van de service zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de service naam bijvoorbeeld ' Fabric \: /MyApp/app1/svc1 ' is, is de service-identiteit ' Mijntoep \~ app1 \~ svc1 ' in 6.0 + en ' Mijntoep/app1/svc1 ' in vorige versies. |
| --vervolg token | De vervolg token parameter wordt gebruikt om de volgende set resultaten op te halen. Een vervolg token met een niet-lege waarde wordt opgenomen in het antwoord van de API wanneer de resultaten van het systeem niet in één antwoord passen. Wanneer deze waarde wordt door gegeven aan de volgende API-aanroep, retourneert de API de volgende set resultaten. Als er geen verdere resultaten zijn, bevat het vervolg token geen waarde. De waarde van deze para meter mag geen URL-code ring zijn. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-partition-load"></a>sfctl-partitie laden
Hiermee haalt u de gegevens van de belasting van de opgegeven Service Fabric partitie.

Retourneert informatie over de belasting van een opgegeven partitie. Het antwoord bevat een lijst met laad rapporten voor een Service Fabric partitie. Elk rapport bevat de naam, waarde en de laatst gerapporteerde tijd van de belasting in UTC.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --partitie-id [vereist] | De identiteit van de partitie. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-partition-load-reset"></a>sfctl-partitie laden-opnieuw instellen
Hiermee wordt de huidige belasting van een Service Fabric partitie opnieuw ingesteld.

Hiermee wordt de huidige belasting van een Service Fabric partitie opnieuw ingesteld op de standaard belasting voor de service.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --partitie-id [vereist] | De identiteit van de partitie. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-partition-quorum-loss"></a>quorum van sfctl-partitie-verlies
Induceert quorum verlies voor een bepaalde stateful service partitie.

Deze API is handig voor een tijdelijke verlies situatie bij uw service. Roep de GetQuorumLossProgress-API aan met dezelfde OperationId om informatie te retour neren over de bewerking die is gestart met deze API. Dit kan alleen worden aangeroepen voor services die permanent zijn gemaakt (HasPersistedState = = True).  Gebruik deze API niet op stateless Services of stateful alleen-geheugen Services.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --bewerking-id [vereist] | Een GUID die een aanroep van deze API identificeert.  Dit wordt door gegeven aan de bijbehorende GetProgress-API. |
| --partitie-id [vereist] | De identiteit van de partitie. |
| --quorum-verlies-duur [vereist] | De hoeveelheid tijd waarvoor de partitie in quorum verlies wordt bewaard.  Dit moet in seconden worden opgegeven. |
| --quorum-verlies-modus [vereist] | Deze Enum wordt door gegeven aan de StartQuorumLoss-API om aan te geven welk type quorum verlies moet worden veroorzaakt. |
| --Service-id [vereist] | De identiteit van de service. Deze ID is doorgaans de volledige naam van de service zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de service naam bijvoorbeeld ' Fabric \: /MyApp/app1/svc1 ' is, is de service-identiteit ' Mijntoep \~ app1 \~ svc1 ' in 6.0 + en ' Mijntoep/app1/svc1 ' in vorige versies. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-partition-quorum-loss-status"></a>sfctl-partitie quorum-verlies-status
Hiermee wordt de voortgang van een quorum verlies bewerking opgehaald voor een partitie die is gestart met behulp van de StartQuorumLoss-API.

Hiermee wordt de voortgang opgehaald van een bewerking voor quorum verlies die is gestart met StartQuorumLoss, met behulp van de meegeleverde OperationId.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --bewerking-id [vereist] | Een GUID die een aanroep van deze API identificeert.  Dit wordt door gegeven aan de bijbehorende GetProgress-API. |
| --partitie-id [vereist] | De identiteit van de partitie. |
| --Service-id [vereist] | De identiteit van de service. Deze ID is doorgaans de volledige naam van de service zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de service naam bijvoorbeeld ' Fabric \: /MyApp/app1/svc1 ' is, is de service-identiteit ' Mijntoep \~ app1 \~ svc1 ' in 6.0 + en ' Mijntoep/app1/svc1 ' in vorige versies. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-partition-recover"></a>sfctl-partitie herstellen
Geeft aan dat het Service Fabric cluster moet proberen een specifieke partitie te herstellen die momenteel is vastgelopen in quorum verlies.

Deze bewerking mag alleen worden uitgevoerd als bekend is dat de replica's die niet beschikbaar zijn, niet kunnen worden hersteld. Een onjuist gebruik van deze API kan leiden tot mogelijk gegevens verlies.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --partitie-id [vereist] | De identiteit van de partitie. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-partition-recover-all"></a>sfctl-partitie herstellen-alle
Geeft aan dat het Service Fabric cluster moet proberen om services (inclusief systeem Services) te herstellen die momenteel zijn vastgelopen in quorum verlies.

Deze bewerking mag alleen worden uitgevoerd als bekend is dat de replica's die niet beschikbaar zijn, niet kunnen worden hersteld. Een onjuist gebruik van deze API kan leiden tot mogelijk gegevens verlies.

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

## <a name="sfctl-partition-report-health"></a>sfctl-partitie rapport-status
Hiermee verzendt u een status rapport op de Service Fabric partitie.

Rapporteert de status van de opgegeven Service Fabric partitie. Het rapport moet de informatie bevatten over de bron van het status rapport en de eigenschap waarvoor deze is gerapporteerd. Het rapport wordt verzonden naar een Service Fabric gateway partitie die wordt doorgestuurd naar de Health Store. Het rapport kan worden geaccepteerd door de gateway, maar door de Health Store na een extra validatie afgewezen. Het Health Store kan bijvoorbeeld het rapport afwijzen vanwege een ongeldige para meter, zoals een verouderd volgorde nummer. Als u wilt zien of het rapport is toegepast in de Health Store, controleert u of het rapport wordt weer gegeven in de sectie Events.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --status-Property [required] | De eigenschap van de status informatie. <br><br> Een entiteit kan status rapporten voor verschillende eigenschappen hebben. De eigenschap is een teken reeks en geen vaste inventarisatie om de flexibiliteit van de rapporter in te stellen voor het categoriseren van de status voorwaarde waarmee het rapport wordt geactiveerd. Zo kan een rapporter met SourceId "LocalWatchdog" de status van de beschik bare schijf op een knoop punt bewaken, zodat de eigenschap "AvailableDisk" op dat knoop punt kan worden gerapporteerd. Dezelfde rapportage functie kan de verbinding met het knoop punt bewaken, zodat een eigenschap ' connectiviteit ' op hetzelfde knoop punt kan worden gerapporteerd. In de Health Store worden deze rapporten behandeld als afzonderlijke status gebeurtenissen voor het opgegeven knoop punt. Samen met de SourceId wordt met de eigenschap een unieke identificatie van de status informatie aangeduid. |
| --status (vereist) | Mogelijke waarden zijn ' Unknown \: ', ' OK ', ' warn ', ' error ', ' Unknown '. |
| --partitie-id [vereist] | De identiteit van de partitie. |
| --bron-id [vereist] | De bron naam die het client/watchdog/systeem onderdeel identificeert dat de status informatie heeft gegenereerd. |
| --Beschrijving | De beschrijving van de status informatie. <br><br> Hiermee wordt de vrije tekst aangegeven die wordt gebruikt om informatie over het rapport toe te voegen dat wordt gelezen. De maximale teken reeks lengte van de beschrijving is 4096 tekens. Als de gegeven teken reeks langer is, wordt deze automatisch afgekapt. Wanneer de laatste tekens van de beschrijving worden afgekapt, bevatten ze een markering [afgekapt] en de totale teken reeks grootte is 4096 tekens. De aanwezigheid van de markering geeft aan dat gebruikers de afkap ping hebben plaatsgevonden. Houd er rekening mee dat de beschrijving kleiner is dan 4096 tekens uit de oorspronkelijke teken reeks. |
| --Immediate | Een vlag die aangeeft of het rapport direct moet worden verzonden. <br><br> Een status rapport wordt verzonden naar een Service Fabric gateway-toepassing, die wordt doorgestuurd naar de Health Store. Als direct is ingesteld op True, wordt het rapport direct van de HTTP-gateway naar de Health Store verzonden, ongeacht de instellingen van de Fabric-client die door de HTTP-gateway toepassing worden gebruikt. Dit is handig voor kritieke rapporten die zo snel mogelijk moeten worden verzonden. Afhankelijk van de timing en andere voor waarden, kan het verzenden van het rapport nog steeds mislukken, bijvoorbeeld als de HTTP-gateway is gesloten of als het bericht de gateway niet bereikt. Als direct is ingesteld op False, wordt het rapport verzonden op basis van de status client instellingen van de HTTP-gateway. Daarom wordt deze batch gebaseerd op de HealthReportSendInterval-configuratie. Dit is de aanbevolen instelling omdat de Health-client de status rapport berichten kan optimaliseren naar Health Store en dat de status rapporten worden verwerkt. Standaard worden rapporten niet onmiddellijk verzonden. |
| --verwijderen-verlopen | Waarde die aangeeft of het rapport wordt verwijderd uit Health Store wanneer het is verlopen. <br><br> Als deze eigenschap is ingesteld op True, wordt het rapport verwijderd uit de Health Store nadat het is verlopen. Als deze eigenschap is ingesteld op False, wordt het rapport als een fout beschouwd wanneer het is verlopen. De waarde van deze eigenschap is standaard onwaar. Wanneer clients periodiek een rapport rapporteren, moeten ze RemoveWhenExpired False instellen (standaard). Op deze manier heeft de rapporter problemen (bijvoorbeeld deadlock) en kan het rapport niet worden gerapporteerd. de entiteit wordt geëvalueerd als er een fout optreedt wanneer het status rapport verloopt. Hiermee wordt de entiteit gemarkeerd als de fout status. |
| --Volg nummer | Het Volg nummer voor dit status rapport als een numerieke teken reeks. <br><br> Het Volg nummer van het rapport wordt door de Health Store gebruikt voor het detecteren van verouderde rapporten. Als u niets opgeeft, wordt er automatisch een Volg nummer gegenereerd door de Health-client wanneer een rapport wordt toegevoegd. |
| --time-out-t | Standaard \: 60. |
| --TTL | De duur waarvoor dit status rapport geldig is. Dit veld maakt gebruik van de ISO8601-indeling voor het opgeven van de duur. <br><br> Wanneer clients periodiek rapporteren, moeten ze rapporten met een hogere frequentie verzenden dan live. Als clients rapporteren over overgang, kunnen ze de TTL-waarde (time to Live) instellen op oneindig. Wanneer time to Live verloopt, wordt de status gebeurtenis die de status informatie bevat verwijderd uit Health Store, als RemoveWhenExpired is ingesteld op True, of bij fout geëvalueerd als RemoveWhenExpired False is. Als u niets opgeeft, wordt de time-to-Live-waarde standaard ingesteld op oneindig. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-partition-restart"></a>sfctl-partitie opnieuw opstarten
Met deze API worden enkele of alle replica's of exemplaren van de opgegeven partitie opnieuw gestart.

Deze API is handig voor het testen van failover. Als u deze gebruikt om een stateless service partitie te maken, moet RestartPartitionMode AllReplicasOrInstances zijn. Roep de GetPartitionRestartProgress-API aan met dezelfde OperationId om de voortgang te verkrijgen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --bewerking-id [vereist] | Een GUID die een aanroep van deze API identificeert.  Dit wordt door gegeven aan de bijbehorende GetProgress-API. |
| --partitie-id [vereist] | De identiteit van de partitie. |
| --Restart-partitie modus [vereist] | Beschrijf welke partities opnieuw moeten worden gestart. |
| --Service-id [vereist] | De identiteit van de service. Deze ID is doorgaans de volledige naam van de service zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de service naam bijvoorbeeld ' Fabric \: /MyApp/app1/svc1 ' is, is de service-identiteit ' Mijntoep \~ app1 \~ svc1 ' in 6.0 + en ' Mijntoep/app1/svc1 ' in vorige versies. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-partition-restart-status"></a>sfctl-partitie opnieuw opstarten-status
Hiermee wordt de voortgang opgehaald van een PartitionRestart-bewerking die is gestart met behulp van StartPartitionRestart.

Hiermee wordt de voortgang opgehaald van een PartitionRestart die is gestart met StartPartitionRestart met behulp van de gegeven OperationId.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --bewerking-id [vereist] | Een GUID die een aanroep van deze API identificeert.  Dit wordt door gegeven aan de bijbehorende GetProgress-API. |
| --partitie-id [vereist] | De identiteit van de partitie. |
| --Service-id [vereist] | De identiteit van de service. Deze ID is doorgaans de volledige naam van de service zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de service naam bijvoorbeeld ' Fabric \: /MyApp/app1/svc1 ' is, is de service-identiteit ' Mijntoep \~ app1 \~ svc1 ' in 6.0 + en ' Mijntoep/app1/svc1 ' in vorige versies. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-partition-svc-name"></a>sfctl-partitie-SVC-naam
Hiermee wordt de naam opgehaald van de Service Fabric-service voor een partitie.

Hiermee wordt de naam van de service voor de opgegeven partitie opgehaald. Er wordt een 404-fout geretourneerd als de partitie-ID niet in het cluster bestaat.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --partitie-id [vereist] | De identiteit van de partitie. |
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
