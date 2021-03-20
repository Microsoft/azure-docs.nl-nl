---
title: Azure Service Fabric CLI-sfctl opstellen
description: Meer informatie over sfctl, de Azure Service Fabric-opdracht regel interface. Bevat een lijst met opdrachten voor docker-compositie toepassingen.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: e84b35ff3dfa7be0c848a4d7b906c622d9b88914
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86257339"
---
# <a name="sfctl-compose"></a>sfctl compose
Docker opstellen toepassingen maken, verwijderen en beheren.

## <a name="commands"></a>Opdracht

|Opdracht|Beschrijving|
| --- | --- |
| maken | Hiermee maakt u een implementatie voor Service Fabric opstellen. |
| list | Hiermee haalt u de lijst met opstel implementaties op die zijn gemaakt in het Service Fabric cluster. |
| verwijderen | Hiermee verwijdert u een bestaande Service Fabric voor het opstellen van een implementatie uit het cluster. |
| status | Hiermee haalt u informatie op over een implementatie van Service Fabric opstellen. |
| upgrade | Hiermee wordt een upgrade van een opstel implementatie in het Service Fabric cluster gestart. |
| upgrade-terugdraai actie | Start een opstel implementatie-upgrade in het Service Fabric cluster. |
| upgrade-status | Hiermee worden gegevens opgehaald voor de laatste upgrade die is uitgevoerd op deze Service Fabric een implementatie op te stellen. |

## <a name="sfctl-compose-create"></a>sfctl opstellen maken
Hiermee maakt u een implementatie voor Service Fabric opstellen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --implementatie-naam [vereist] | De naam van de implementatie. |
| --bestandspad [vereist] | Het pad naar het doel bestand van docker voor samen stelling. |
| --versleuteld-door geven | In plaats van te vragen om een container register wachtwoord, gebruikt u een al versleutelde wachtwoordzin. |
| --heeft de bewerking | Wordt gevraagd om een wacht woord voor het container register. |
| --time-out-t | Standaard \: 60. |
| --user | De gebruikers naam om verbinding te maken met het container register. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-compose-list"></a>sfctl-opstel lijst
Hiermee haalt u de lijst met opstel implementaties op die zijn gemaakt in het Service Fabric cluster.

Hiermee wordt de status opgehaald van de opstellen-implementaties die zijn gemaakt of worden gemaakt in het Service Fabric cluster. Het antwoord bevat de naam, de status en andere gegevens over de opstel implementaties. Als de lijst met implementaties niet op een pagina past, wordt er één pagina met resultaten geretourneerd, evenals een vervolg token, dat kan worden gebruikt om de volgende pagina op te halen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --vervolg token | De vervolg token parameter wordt gebruikt om de volgende set resultaten op te halen. Een vervolg token met een niet-lege waarde wordt opgenomen in het antwoord van de API wanneer de resultaten van het systeem niet in één antwoord passen. Wanneer deze waarde wordt door gegeven aan de volgende API-aanroep, retourneert de API de volgende set resultaten. Als er geen verdere resultaten zijn, bevat het vervolg token geen waarde. De waarde van deze para meter mag geen URL-code ring zijn. |
| --Max-resultaten | Het maximum aantal resultaten dat moet worden geretourneerd als onderdeel van de query's in de pagina. Met deze para meter wordt de bovengrens gedefinieerd voor het aantal geretourneerde resultaten. De geretourneerde resultaten kunnen kleiner zijn dan de opgegeven maximum resultaten als ze niet in het bericht passen conform de maximale grootte van de berichten die in de configuratie is gedefinieerd. Als deze para meter nul is of niet is opgegeven, bevat de opgevraagde query zoveel mogelijk resultaten die in het retour bericht passen. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-compose-remove"></a>sfctl opstellen verwijderen
Hiermee verwijdert u een bestaande Service Fabric voor het opstellen van een implementatie uit het cluster.

Hiermee verwijdert u een bestaande Service Fabric opstellen-implementatie.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --implementatie-naam [vereist] | De identiteit van de implementatie. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-compose-status"></a>sfctl-samenstel status
Hiermee haalt u informatie op over een implementatie van Service Fabric opstellen.

Retourneert de status van de opstel implementatie die is gemaakt of in het proces dat wordt gemaakt in het Service Fabric cluster en waarvan de naam overeenkomt met de versie die is opgegeven als de para meter. Het antwoord bevat de naam, status en andere informatie over de implementatie.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --implementatie-naam [vereist] | De identiteit van de implementatie. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-compose-upgrade"></a>upgrade van sfctl-opstellen
Hiermee wordt een upgrade van een opstel implementatie in het Service Fabric cluster gestart.

Valideert de opgegeven upgrade parameters en begint met het upgraden van de implementatie als de para meters geldig zijn.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --implementatie-naam [vereist] | De naam van de implementatie. |
| --bestandspad [vereist] | Het pad naar het doel bestand van docker voor samen stelling. |
| --standaard-SVC-type-Health-toewijzing | JSON-gecodeerde woorden lijst waarmee het status beleid wordt beschreven dat wordt gebruikt om de status van services te evalueren. |
| --versleuteld-door geven | In plaats van te vragen om een container register wachtwoord, gebruikt u een al versleutelde wachtwoordzin. |
| --fout-actie | Mogelijke waarden zijn \: ' ongeldig ', ' rollback ', ' Manual '. |
| --Force-restart | Processen worden geforceerd opnieuw gestart tijdens de upgrade, zelfs wanneer de code versie niet is gewijzigd. <br><br> Bij de upgrade worden alleen configuratie of gegevens gewijzigd. |
| --heeft de bewerking | Wordt gevraagd om een wacht woord voor het container register. |
| --status-controle-opnieuw | De tijds duur tussen pogingen om status controles uit te voeren als de toepassing of het cluster niet in orde is. |
| --status-controle stabiel | De hoeveelheid tijd die de toepassing of het cluster in orde moet blijven voordat de upgrade wordt voortgezet naar het volgende upgrade domein. <br><br> Het wordt eerst geïnterpreteerd als een teken reeks die een ISO 8601-duur vertegenwoordigt. Als dat mislukt, wordt dit geïnterpreteerd als een getal dat het totale aantal milliseconden aangeeft. |
| --status-check-wait | De tijds duur die moet worden gewacht na het volt ooien van een upgrade domein voordat het proces status controles wordt gestart. |
| --replica-set-check | De maximale tijds duur voor het blok keren van de verwerking van een upgrade domein en het voor komen van Beschik baarheid wanneer er onverwachte problemen zijn. <br><br> Wanneer deze time-out is verlopen, wordt de verwerking van het upgrade domein voortgezet, ongeacht de problemen met het Beschikbaarheids verlies. De time-out wordt opnieuw ingesteld aan het begin van elk upgrade domein. Geldige waarden zijn 0 tot en met 42949672925. |
| --SVC-type-Health-toewijzing | JSON-gecodeerde lijst met objecten die het status beleid beschrijven dat wordt gebruikt om de status van verschillende service typen te evalueren. |
| --time-out-t | Standaard \: 60. |
| --de app is beschadigd | Het Maxi maal toegestane percentage van toepassingen met een slechte status voordat een fout wordt gemeld. <br><br> Als u bijvoorbeeld wilt toestaan dat 10% van de toepassingen een slechte status heeft, is deze waarde 10. Het percentage vertegenwoordigt het Maxi maal toegestane percentage van toepassingen die een slechte status kunnen hebben voordat het cluster als fout wordt beschouwd. Als het percentage wordt geëerbiedigd, maar er ten minste één slechte toepassing is, wordt de status als een waarschuwing geëvalueerd. Dit wordt berekend door het aantal toepassingen met een slechte status te delen ten opzichte van het totale aantal toepassings exemplaren in het cluster. |
| --upgrade-time-out van domein | De hoeveelheid tijd die elk upgrade domein moet volt ooien voordat FailureAction wordt uitgevoerd. <br><br> Het wordt eerst geïnterpreteerd als een teken reeks die een ISO 8601-duur vertegenwoordigt. Als dat mislukt, wordt dit geïnterpreteerd als een getal dat het totale aantal milliseconden aangeeft. |
| --upgrade-soort | Standaard \: rollen. |
| --upgrade-modus | Mogelijke waarden zijn \: ' ongeldig ', ' UnmonitoredAuto ', ' UnmonitoredManual ', ' bewaakt '.  Standaard \: UnmonitoredAuto. |
| --upgrade-time-out | De hoeveelheid tijd die de algehele upgrade moet volt ooien voordat FailureAction wordt uitgevoerd. <br><br> Het wordt eerst geïnterpreteerd als een teken reeks die een ISO 8601-duur vertegenwoordigt. Als dat mislukt, wordt dit geïnterpreteerd als een getal dat het totale aantal milliseconden aangeeft. |
| --user | De gebruikers naam om verbinding te maken met het container register. |
| --waarschuwing-als-fout | Hiermee wordt aangegeven of waarschuwingen worden behandeld met dezelfde ernst als bij fouten. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-compose-upgrade-rollback"></a>sfctl opstellen upgrade-terugdraai actie
Start een opstel implementatie-upgrade in het Service Fabric cluster.

Een implementatie-upgrade voor een service Fabric-samen stel ongedaan maken.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --implementatie-naam [vereist] | De identiteit van de implementatie. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-compose-upgrade-status"></a>sfctl opstellen upgrade-status
Hiermee worden gegevens opgehaald voor de laatste upgrade die is uitgevoerd op deze Service Fabric een implementatie op te stellen.

Retourneert informatie over de status van de upgrade voor de samen stellen van de implementatie samen met Details voor hulp bij het oplossen van problemen met de status van toepassingen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --implementatie-naam [vereist] | De identiteit van de implementatie. |
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
