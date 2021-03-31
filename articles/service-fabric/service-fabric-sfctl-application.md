---
title: Azure Service Fabric CLI-sfctl-toepassing
description: Meer informatie over sfctl, de Azure Service Fabric-opdracht regel interface. Bevat een lijst met opdrachten voor het beheren van toepassingen.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 880770345eb7d65850db322bd97d64c60b6681ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260862"
---
# <a name="sfctl-application"></a>sfctl application
Toepassingen en toepassings typen maken, verwijderen en beheren.

## <a name="commands"></a>Opdracht

|Opdracht|Beschrijving|
| --- | --- |
| maken | Hiermee maakt u een Service Fabric-toepassing met behulp van de opgegeven beschrijving. |
| delete | Hiermee verwijdert u een bestaande Service Fabric-toepassing. |
| geïmplementeerd | Hiermee wordt de informatie opgehaald over een toepassing die op een Service Fabric knoop punt is geïmplementeerd. |
| geïmplementeerd-status | Hiermee haalt u de informatie over de status van een toepassing die op een Service Fabric knoop punt is geïmplementeerd. |
| geïmplementeerd-lijst | Hiermee haalt u de lijst met toepassingen die zijn geïmplementeerd op een Service Fabric knoop punt. |
| status | Hiermee wordt de status van de service Fabric-toepassing opgehaald. |
| Info | Hiermee haalt u informatie op over een Service Fabric-toepassing. |
| list | Hiermee wordt de lijst met toepassingen die in het Service Fabric cluster zijn gemaakt, opgehaald die overeenkomen met de opgegeven filters. |
| laden | Hiermee wordt informatie over het laden van een Service Fabric-toepassing opgehaald. |
| manifest | Hiermee wordt het manifest opgehaald dat een toepassings type beschrijft. |
| aanbieden | Hiermee wordt een Service Fabric toepassings type met het cluster ingericht of geregistreerd met behulp van het pakket '. sfpkg ' in de externe opslag of met behulp van het toepassings pakket in het archief met installatie kopieën. |
| rapport-status | Hiermee verzendt u een status rapport op de Service Fabric-toepassing. |
| type | Hiermee haalt u de lijst met toepassings typen in het Service Fabric cluster die overeenkomen met de opgegeven naam. |
| type-lijst | Hiermee haalt u de lijst met toepassings typen op in het Service Fabric cluster. |
| inrichting verwijderen | Hiermee verwijdert of maakt u de registratie van een Service Fabric toepassings type van het cluster ongedaan. |
| upgrade | Hiermee wordt een toepassing in het Service Fabric cluster bijgewerkt. |
| upgrade uitvoeren-hervatten | Hiermee wordt de upgrade van een toepassing in het Service Fabric cluster hervat. |
| upgrade-terugdraai actie | De huidige doorlopende upgrade van een toepassing in het Service Fabric cluster wordt teruggezet. |
| upgrade-status | Hiermee worden gegevens opgehaald voor de laatste upgrade die op deze toepassing is uitgevoerd. |
| uploaden | Kopieer een Service Fabric toepassings pakket naar de archief met installatie kopieën. |

## <a name="sfctl-application-create"></a>sfctl-toepassing maken
Hiermee maakt u een Service Fabric-toepassing met behulp van de opgegeven beschrijving.

### <a name="arguments"></a>Argumenten

|Argument|Description|
| --- | --- |
| --app-naam [vereist] | De naam van de toepassing, met inbegrip van het URI-schema van de infra structuur \: . |
| --app-type [vereist] | De naam van het toepassings type dat in het toepassings manifest is gevonden. |
| --App-versie [vereist] | De versie van het toepassings type zoals gedefinieerd in het manifest van de toepassing. |
| --Max-node-Count | Het maximum aantal knoop punten waar Service Fabric capaciteit voor deze toepassing zal reserveren. Houd er rekening mee dat dit niet betekent dat de services van deze toepassing op al deze knoop punten worden geplaatst. |
| --metrische gegevens | Een JSON-gecodeerde lijst met metrische gegevens over de toepassings capaciteit. Een metrische waarde wordt gedefinieerd als een naam die is gekoppeld aan een set capaciteit voor elk knoop punt waarop de toepassing zich bevindt. |
| --min-knooppunt-aantal | Het minimum aantal knoop punten waar Service Fabric capaciteit voor deze toepassing zal reserveren. Houd er rekening mee dat dit niet betekent dat de services van deze toepassing op al deze knoop punten worden geplaatst. |
| --para meters | Een JSON-gecodeerde lijst met onderdrukkingen van toepassings parameters die moeten worden toegepast bij het maken van de toepassing. |
| --time-out-t | Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Description|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-application-delete"></a>sfctl toepassing verwijderen
Hiermee verwijdert u een bestaande Service Fabric-toepassing.

Een toepassing moet worden gemaakt voordat deze kan worden verwijderd. Als u een toepassing verwijdert, worden alle services verwijderd die deel uitmaken van die toepassing. Service Fabric probeert standaard de service replica's op een correcte manier te sluiten en vervolgens de service te verwijderen. Als een service echter problemen heeft met het op de juiste wijze sluiten van de replica, kan de verwijderings bewerking lang duren of blijven vastlopen. Gebruik de optionele vlag ForceRemove om de juiste sluit sequentie over te slaan en de toepassing en alle bijbehorende services geforceerd te verwijderen.

### <a name="arguments"></a>Argumenten

|Argument|Description|
| --- | --- |
| --toepassings-id [vereist] | De identiteit van de toepassing. Dit is doorgaans de volledige naam van de toepassing zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de naam van de toepassing bijvoorbeeld ' Fabric \: /MyApp/app1 ' is, is de toepassings identiteit ' Mijntoep \~ app1 ' in 6.0 + en ' Mijntoep/app1 ' in vorige versies. |
| --Force-Remove | Verwijder een Service Fabric toepassing of service geforceerd zonder de juiste afsluit procedure te door lopen. Deze para meter kan worden gebruikt om een toepassing of service geforceerd te verwijderen waarvoor een time-out is opgetreden vanwege problemen in de service code die voor komen dat replica's worden gesloten. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Description|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-application-deployed"></a>sfctl toepassing geïmplementeerd
Hiermee wordt de informatie opgehaald over een toepassing die op een Service Fabric knoop punt is geïmplementeerd.

Met deze query wordt informatie over de systeem toepassing geretourneerd als de verstrekte toepassings-ID voor systeem toepassing is. De resultaten omvatten geïmplementeerde toepassingen in actieve, activerings-en download statussen. Deze query vereist dat de knooppunt naam overeenkomt met een knoop punt in het cluster. De query mislukt als de naam van het gegeven knoop punt niet naar een actieve Service Fabric knoop punten in het cluster wijst.

### <a name="arguments"></a>Argumenten

|Argument|Description|
| --- | --- |
| --toepassings-id [vereist] | De identiteit van de toepassing. Dit is doorgaans de volledige naam van de toepassing zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de naam van de toepassing bijvoorbeeld ' Fabric \: /MyApp/app1 ' is, is de toepassings identiteit ' Mijntoep \~ app1 ' in 6.0 + en ' Mijntoep/app1 ' in vorige versies. |
| --node-name [required] | De naam van het knoop punt. |
| --include-status | De status van een entiteit bevatten. Als deze para meter False is of niet is opgegeven, is de geretourneerde status ' onbekend '. Als deze eigenschap is ingesteld op True, wordt de query parallel met het knoop punt en de Health-systeem service gedoofd voordat de resultaten worden samengevoegd. Als gevolg hiervan is de query duurder en kan dit een langere tijd duren. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Description|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-application-deployed-health"></a>geïmplementeerde sfctl-toepassing-status
Hiermee haalt u de informatie over de status van een toepassing die op een Service Fabric knoop punt is geïmplementeerd.

Hiermee haalt u de informatie over de status van een toepassing die op een Service Fabric knoop punt is geïmplementeerd. Gebruik EventsHealthStateFilter om optioneel te filteren op de verzameling van HealthEvent-objecten die zijn gerapporteerd op basis van de status van de geïmplementeerde toepassing. Gebruik DeployedServicePackagesHealthStateFilter om optioneel te filteren op DeployedServicePackageHealth-kinderen op basis van de status.

### <a name="arguments"></a>Argumenten

|Argument|Description|
| --- | --- |
| --toepassings-id [vereist] | De identiteit van de toepassing. Dit is doorgaans de volledige naam van de toepassing zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de naam van de toepassing bijvoorbeeld ' Fabric \: /MyApp/app1 ' is, is de toepassings identiteit ' Mijntoep \~ app1 ' in 6.0 + en ' Mijntoep/app1 ' in vorige versies. |
| --node-name [required] | De naam van het knoop punt. |
| --geïmplementeerd-service-pakketten-status-filter | Hiermee staat u het filteren toe van de geïmplementeerde service pakket status objecten die zijn geretourneerd in het resultaat van de geïmplementeerde toepassings status query op basis van hun status. De mogelijke waarden voor deze para meter zijn gehele getallen van een van de volgende statussen. Alleen geïmplementeerde service pakketten die overeenkomen met het filter worden geretourneerd. Alle geïmplementeerde service pakketten worden gebruikt om de geaggregeerde status van de geïmplementeerde toepassing te evalueren. Als u niets opgeeft, worden alle vermeldingen geretourneerd. De status waarden zijn inventarisatie op basis van een vlag, dus de waarde kan een combi natie van deze waarden zijn, verkregen met behulp van de operator bitsgewijze of. Als de opgegeven waarde bijvoorbeeld 6 is, wordt de status van de service pakketten met HealthState waarde OK (2) en waarschuwing (4) geretourneerd.  <br> -Standaard-standaard waarde. Komt overeen met een wille keurige HealthState. De waarde is nul.  <br> -Geen: filter dat niet overeenkomt met een HealthState-waarde. Wordt gebruikt om geen resultaten te retour neren voor een bepaalde verzameling statussen. De waarde is 1.  <br> -OK-filter dat overeenkomt met de invoer met HealthState waarde OK. De waarde is 2.  <br> -Waarschuwings filter dat overeenkomt met invoer met HealthState-waarde waarschuwing. De waarde is 4.  <br> -Fout filter dat overeenkomt met de invoer met de HealthState-waarde fout. De waarde is 8.  <br> -Alle-filter die overeenkomt met invoer met een wille keurige waarde van HealthState. De waarde is 65535. |
| --gebeurtenissen-status-filter | Hiermee kunt u het verzamelen van HealthEvent-objecten die zijn geretourneerd op basis van de status wordt gefilterd. De mogelijke waarden voor deze para meter zijn gehele getallen van een van de volgende statussen. Alleen gebeurtenissen die overeenkomen met het filter worden geretourneerd. Alle gebeurtenissen worden gebruikt om de geaggregeerde status te evalueren. Als u niets opgeeft, worden alle vermeldingen geretourneerd. De status waarden zijn inventarisatie op basis van een vlag, waardoor de waarde kan bestaan uit een combi natie van deze waarden, verkregen met behulp van de operator bitsgewijze ' of '. Als de opgegeven waarde bijvoorbeeld 6 is, worden alle gebeurtenissen met HealthState waarde OK (2) en waarschuwing (4) geretourneerd.  <br> -Standaard-standaard waarde. Komt overeen met een wille keurige HealthState. De waarde is nul.  <br> -Geen: filter dat niet overeenkomt met een HealthState-waarde. Wordt gebruikt om geen resultaten te retour neren voor een bepaalde verzameling statussen. De waarde is 1.  <br> -OK-filter dat overeenkomt met de invoer met HealthState waarde OK. De waarde is 2.  <br> -Waarschuwings filter dat overeenkomt met invoer met HealthState-waarde waarschuwing. De waarde is 4.  <br> -Fout filter dat overeenkomt met de invoer met de HealthState-waarde fout. De waarde is 8.  <br> -Alle-filter die overeenkomt met invoer met een wille keurige waarde van HealthState. De waarde is 65535. |
| --exclude-status-statistieken | Hiermee wordt aangegeven of de status statistieken moeten worden geretourneerd als onderdeel van het query resultaat. Standaard onwaar. In de statistieken wordt het aantal onderliggende entiteiten in de status OK, waarschuwing en fout weer gegeven. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Description|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-application-deployed-list"></a>geïmplementeerde sfctl-toepassing-lijst
Hiermee haalt u de lijst met toepassingen die zijn geïmplementeerd op een Service Fabric knoop punt.

Hiermee haalt u de lijst met toepassingen die zijn geïmplementeerd op een Service Fabric knoop punt. De resultaten bevatten geen informatie over geïmplementeerde systeem toepassingen, tenzij u expliciet een query voor op ID hebt uitgevoerd. De resultaten omvatten geïmplementeerde toepassingen in actieve, activerings-en download statussen. Deze query vereist dat de knooppunt naam overeenkomt met een knoop punt in het cluster. De query mislukt als de naam van het gegeven knoop punt niet naar een actieve Service Fabric knoop punten in het cluster wijst.

### <a name="arguments"></a>Argumenten

|Argument|Description|
| --- | --- |
| --node-name [required] | De naam van het knoop punt. |
| --vervolg token | De vervolg token parameter wordt gebruikt om de volgende set resultaten op te halen. Een vervolg token met een niet-lege waarde wordt opgenomen in het antwoord van de API wanneer de resultaten van het systeem niet in één antwoord passen. Wanneer deze waarde wordt door gegeven aan de volgende API-aanroep, retourneert de API de volgende set resultaten. Als er geen verdere resultaten zijn, bevat het vervolg token geen waarde. De waarde van deze para meter mag geen URL-code ring zijn. |
| --include-status | De status van een entiteit bevatten. Als deze para meter False is of niet is opgegeven, is de geretourneerde status ' onbekend '. Als deze eigenschap is ingesteld op True, wordt de query parallel met het knoop punt en de Health-systeem service gedoofd voordat de resultaten worden samengevoegd. Als gevolg hiervan is de query duurder en kan dit een langere tijd duren. |
| --Max-resultaten | Het maximum aantal resultaten dat moet worden geretourneerd als onderdeel van de query's in de pagina. Met deze para meter wordt de bovengrens gedefinieerd voor het aantal geretourneerde resultaten. De geretourneerde resultaten kunnen kleiner zijn dan de opgegeven maximum resultaten als ze niet in het bericht passen conform de maximale grootte van de berichten die in de configuratie is gedefinieerd. Als deze para meter nul is of niet is opgegeven, bevat de opgevraagde query zoveel mogelijk resultaten die in het retour bericht passen. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Description|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-application-health"></a>sfctl-toepassings status
Hiermee wordt de status van de service Fabric-toepassing opgehaald.

Retourneert de status van de service Fabric-toepassing. De antwoord rapporten hebben de status OK, fout of waarschuwing. Als de entiteit niet wordt gevonden in de Health Store, wordt er een fout geretourneerd.

### <a name="arguments"></a>Argumenten

|Argument|Description|
| --- | --- |
| --toepassings-id [vereist] | De identiteit van de toepassing. Dit is doorgaans de volledige naam van de toepassing zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de naam van de toepassing bijvoorbeeld ' Fabric \: /MyApp/app1 ' is, is de toepassings identiteit ' Mijntoep \~ app1 ' in 6.0 + en ' Mijntoep/app1 ' in vorige versies. |
| --geïmplementeerd-toepassingen-status-filter | Hiermee staat u toe dat de status objecten van geïmplementeerde toepassingen worden gefilterd die zijn geretourneerd in het resultaat van de toepassings status query op basis van hun status. De mogelijke waarden voor deze para meter zijn gehele getallen van een van de volgende statussen. Alleen geïmplementeerde toepassingen die overeenkomen met het filter, worden geretourneerd. Alle geïmplementeerde toepassingen worden gebruikt om de geaggregeerde status te evalueren. Als u niets opgeeft, worden alle vermeldingen geretourneerd. De status waarden zijn inventarisatie op basis van een vlag, waardoor de waarde kan bestaan uit een combi natie van deze waarden, verkregen met behulp van de operator bitsgewijze of. Als de opgegeven waarde bijvoorbeeld 6 is, wordt de status van geïmplementeerde toepassingen met HealthState waarde OK (2) en waarschuwing (4) geretourneerd.  <br> -Standaard-standaard waarde. Komt overeen met een wille keurige HealthState. De waarde is nul.  <br> -Geen: filter dat niet overeenkomt met een HealthState-waarde. Wordt gebruikt om geen resultaten te retour neren voor een bepaalde verzameling statussen. De waarde is 1.  <br> -OK-filter dat overeenkomt met de invoer met HealthState waarde OK. De waarde is 2.  <br> -Waarschuwings filter dat overeenkomt met invoer met HealthState-waarde waarschuwing. De waarde is 4.  <br> -Fout filter dat overeenkomt met de invoer met de HealthState-waarde fout. De waarde is 8.  <br> -Alle-filter die overeenkomt met invoer met een wille keurige waarde van HealthState. De waarde is 65535. |
| --gebeurtenissen-status-filter | Hiermee kunt u het verzamelen van HealthEvent-objecten die zijn geretourneerd op basis van de status wordt gefilterd. De mogelijke waarden voor deze para meter zijn gehele getallen van een van de volgende statussen. Alleen gebeurtenissen die overeenkomen met het filter worden geretourneerd. Alle gebeurtenissen worden gebruikt om de geaggregeerde status te evalueren. Als u niets opgeeft, worden alle vermeldingen geretourneerd. De status waarden zijn inventarisatie op basis van een vlag, waardoor de waarde kan bestaan uit een combi natie van deze waarden, verkregen met behulp van de operator bitsgewijze ' of '. Als de opgegeven waarde bijvoorbeeld 6 is, worden alle gebeurtenissen met HealthState waarde OK (2) en waarschuwing (4) geretourneerd.  <br> -Standaard-standaard waarde. Komt overeen met een wille keurige HealthState. De waarde is nul.  <br> -Geen: filter dat niet overeenkomt met een HealthState-waarde. Wordt gebruikt om geen resultaten te retour neren voor een bepaalde verzameling statussen. De waarde is 1.  <br> -OK-filter dat overeenkomt met de invoer met HealthState waarde OK. De waarde is 2.  <br> -Waarschuwings filter dat overeenkomt met invoer met HealthState-waarde waarschuwing. De waarde is 4.  <br> -Fout filter dat overeenkomt met de invoer met de HealthState-waarde fout. De waarde is 8.  <br> -Alle-filter die overeenkomt met invoer met een wille keurige waarde van HealthState. De waarde is 65535. |
| --exclude-status-statistieken | Hiermee wordt aangegeven of de status statistieken moeten worden geretourneerd als onderdeel van het query resultaat. Standaard onwaar. In de statistieken wordt het aantal onderliggende entiteiten in de status OK, waarschuwing en fout weer gegeven. |
| --Services-status-filter | Hiermee staat u toe dat de services Health State-objecten worden gefilterd die zijn geretourneerd in het resultaat van de status query Services op basis van hun status. De mogelijke waarden voor deze para meter zijn gehele getallen van een van de volgende statussen. Alleen services die overeenkomen met het filter worden geretourneerd. Alle services worden gebruikt om de geaggregeerde status te evalueren. Als u niets opgeeft, worden alle vermeldingen geretourneerd. De status waarden zijn inventarisatie op basis van een vlag, waardoor de waarde kan bestaan uit een combi natie van deze waarden, verkregen met behulp van de operator bitsgewijze of. Als de opgegeven waarde bijvoorbeeld 6 is, wordt de status van services met HealthState waarde OK (2) en waarschuwing (4) geretourneerd.  <br> -Standaard-standaard waarde. Komt overeen met een wille keurige HealthState. De waarde is nul.  <br> -Geen: filter dat niet overeenkomt met een HealthState-waarde. Wordt gebruikt om geen resultaten te retour neren voor een bepaalde verzameling statussen. De waarde is 1.  <br> -OK-filter dat overeenkomt met de invoer met HealthState waarde OK. De waarde is 2.  <br> -Waarschuwings filter dat overeenkomt met invoer met HealthState-waarde waarschuwing. De waarde is 4.  <br> -Fout filter dat overeenkomt met de invoer met de HealthState-waarde fout. De waarde is 8.  <br> -Alle-filter die overeenkomt met invoer met een wille keurige waarde van HealthState. De waarde is 65535. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Description|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-application-info"></a>informatie over sfctl-toepassing
Hiermee haalt u informatie op over een Service Fabric-toepassing.

Retourneert de informatie over de toepassing die is gemaakt of in het proces dat in het Service Fabric cluster wordt gemaakt en waarvan de naam overeenkomt met de versie die is opgegeven als de para meter. Het antwoord bevat de naam, het type, de status, de para meters en andere gegevens over de toepassing.

### <a name="arguments"></a>Argumenten

|Argument|Description|
| --- | --- |
| --toepassings-id [vereist] | De identiteit van de toepassing. Dit is doorgaans de volledige naam van de toepassing zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de naam van de toepassing bijvoorbeeld ' Fabric \: /MyApp/app1 ' is, is de toepassings identiteit ' Mijntoep \~ app1 ' in 6.0 + en ' Mijntoep/app1 ' in vorige versies. |
| --exclude-Application-para meters | De vlag waarmee wordt opgegeven of toepassings parameters worden uitgesloten van het resultaat. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Description|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-application-list"></a>sfctl-toepassings lijst
Hiermee wordt de lijst met toepassingen die in het Service Fabric cluster zijn gemaakt, opgehaald die overeenkomen met de opgegeven filters.

Hiermee wordt de informatie opgehaald over de toepassingen die zijn gemaakt of worden gemaakt in het Service Fabric cluster en overeenkomen met de opgegeven filters. Het antwoord bevat de naam, het type, de status, de para meters en andere gegevens over de toepassing. Als de toepassingen niet op een pagina passen, wordt er één pagina met resultaten geretourneerd, evenals een vervolg token, dat kan worden gebruikt om de volgende pagina op te halen. Filters ApplicationTypeName en ApplicationDefinitionKindFilter kunnen niet tegelijk worden opgegeven.

### <a name="arguments"></a>Argumenten

|Argument|Description|
| --- | --- |
| --toepassings definitie-soort-filter | Wordt gebruikt om te filteren op ApplicationDefinitionKind. Dit is het mechanisme dat wordt gebruikt om een Service Fabric toepassing te definiëren.  <br> -Standaard-standaard waarde, waarmee dezelfde functie wordt uitgevoerd als bij het selecteren van ' alle '. De waarde is 0.  <br> -Alle-filter die overeenkomt met invoer met een wille keurige waarde van ApplicationDefinitionKind. De waarde is 65535.  <br> -ServiceFabricApplicationDescription-filter dat overeenkomt met de invoer met de ApplicationDefinitionKind-waarde ServiceFabricApplicationDescription. De waarde is 1.  <br> -Samengesteld filter dat overeenkomt met de invoer met ApplicationDefinitionKind-waarde opstellen. De waarde is 2. |
| --toepassings type-naam | De naam van het toepassings type dat wordt gebruikt voor het filteren van de toepassingen waarvoor een query moet worden uitgevoerd. Deze waarde mag niet de versie van het toepassings type bevatten. |
| --vervolg token | De vervolg token parameter wordt gebruikt om de volgende set resultaten op te halen. Een vervolg token met een niet-lege waarde wordt opgenomen in het antwoord van de API wanneer de resultaten van het systeem niet in één antwoord passen. Wanneer deze waarde wordt door gegeven aan de volgende API-aanroep, retourneert de API de volgende set resultaten. Als er geen verdere resultaten zijn, bevat het vervolg token geen waarde. De waarde van deze para meter mag geen URL-code ring zijn. |
| --exclude-Application-para meters | De vlag waarmee wordt opgegeven of toepassings parameters worden uitgesloten van het resultaat. |
| --Max-resultaten | Het maximum aantal resultaten dat moet worden geretourneerd als onderdeel van de query's in de pagina. Met deze para meter wordt de bovengrens gedefinieerd voor het aantal geretourneerde resultaten. De geretourneerde resultaten kunnen kleiner zijn dan de opgegeven maximum resultaten als ze niet in het bericht passen conform de maximale grootte van de berichten die in de configuratie is gedefinieerd. Als deze para meter nul is of niet is opgegeven, bevat de opgevraagde query zoveel mogelijk resultaten die in het retour bericht passen. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Description|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-application-load"></a>sfctl toepassings belasting
Hiermee wordt informatie over het laden van een Service Fabric-toepassing opgehaald.

Retourneert de laad informatie over de toepassing die is gemaakt of in het proces dat wordt gemaakt in het Service Fabric cluster en waarvan de naam overeenkomt met de waarde die is opgegeven als de para meter. Het antwoord bevat de naam, het minimale knoop punt, het maximum aantal knoop punten, de knoop punten die de toepassing momenteel bewaart en de metrische gegevens van de toepassings belasting over de toepassing.

### <a name="arguments"></a>Argumenten

|Argument|Description|
| --- | --- |
| --toepassings-id [vereist] | De identiteit van de toepassing. Dit is doorgaans de volledige naam van de toepassing zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de naam van de toepassing bijvoorbeeld ' Fabric \: /MyApp/app1 ' is, is de toepassings identiteit ' Mijntoep \~ app1 ' in 6.0 + en ' Mijntoep/app1 ' in vorige versies. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Description|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-application-manifest"></a>sfctl-toepassings manifest
Hiermee wordt het manifest opgehaald dat een toepassings type beschrijft.

Het antwoord bevat de XML van het toepassings manifest als een teken reeks.

### <a name="arguments"></a>Argumenten

|Argument|Description|
| --- | --- |
| --Application-type-name [required] | De naam van het toepassings type. |
| --toepassings type-versie [vereist] | De versie van het toepassings type. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Description|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-application-provision"></a>sfctl-toepassings inrichting
Hiermee wordt een Service Fabric toepassings type met het cluster ingericht of geregistreerd met behulp van het pakket '. sfpkg ' in de externe opslag of met behulp van het toepassings pakket in het archief met installatie kopieën.

Richt een Service Fabric toepassings type in met het cluster. De inrichting is vereist voordat er nieuwe toepassingen kunnen worden geïnstantieerd. De inrichtings bewerking kan worden uitgevoerd op het toepassings pakket dat is opgegeven door de relativePathInImageStore of door gebruik te maken van de URI van het externe sfpkg. Tenzij--extern inrichten is ingesteld, wordt met deze opdracht de inrichting van het archief van de installatie kopie verwacht.

### <a name="arguments"></a>Argumenten

|Argument|Description|
| --- | --- |
| --toepassing-pakket-down load-URI | Het pad naar het toepassings pakket '. sfpkg ' waaruit het toepassings pakket kan worden gedownload met behulp van HTTP-of HTTPS-protocollen. <br><br> Alleen voor de externe opslag van het type inrichting. Het toepassings pakket kan worden opgeslagen in een externe opslag die GET-bewerking biedt om het bestand te downloaden. Ondersteunde protocollen zijn HTTP en HTTPS, en het pad moet lees toegang toestaan. |
| --Application-type-Build-Path | Alleen voor de installatie kopie van het besturings systeem. Het relatieve pad voor het toepassings pakket in de installatie kopie opslag die is opgegeven tijdens de vorige upload bewerking. |
| --toepassings type-naam | Alleen voor de externe opslag van het type inrichting. De naam van het toepassings type vertegenwoordigt de naam van het toepassings type dat in het toepassings manifest is gevonden. |
| --toepassings type-versie | Alleen voor de externe opslag van het type inrichting. De versie van het toepassings type vertegenwoordigt de versie van het toepassings type dat in het toepassings manifest is gevonden. |
| --extern inrichten | De locatie van waaruit het toepassings pakket kan worden geregistreerd of ingericht. Geeft aan dat de inrichting is voor een toepassings pakket dat eerder is geüpload naar een externe opslag. Het toepassings pakket eindigt met de extensie *. sfpkg. |
| --geen wacht tijd | Hiermee wordt aangegeven of inrichting asynchroon moet worden uitgevoerd. <br><br> Als deze eigenschap is ingesteld op True, wordt de inrichtings bewerking geretourneerd wanneer de aanvraag wordt geaccepteerd door het systeem en wordt de inrichtings bewerking voortgezet zonder een time-outlimiet. De standaardwaarde is false. Voor grote toepassings pakketten kunt u het beste de waarde instellen op True. |
| --time-out-t | Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Description|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-application-report-health"></a>sfctl-toepassings rapport-status
Hiermee verzendt u een status rapport op de Service Fabric-toepassing.

Hiermee wordt de status van de opgegeven Service Fabric toepassing gerapporteerd. Het rapport moet de informatie bevatten over de bron van het status rapport en de eigenschap waarvoor deze is gerapporteerd. Het rapport wordt verzonden naar een Service Fabric gateway-toepassing, die wordt doorgestuurd naar de Health Store. Het rapport kan worden geaccepteerd door de gateway, maar door de Health Store na een extra validatie afgewezen. Het Health Store kan bijvoorbeeld het rapport afwijzen vanwege een ongeldige para meter, zoals een verouderd volgorde nummer. Als u wilt zien of het rapport is toegepast in de Health Store, moet u de status van de toepassing ophalen en controleren of het rapport wordt weer gegeven.

### <a name="arguments"></a>Argumenten

|Argument|Description|
| --- | --- |
| --toepassings-id [vereist] | De identiteit van de toepassing. <br><br> Dit is doorgaans de volledige naam van de toepassing zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken. Als de naam van de toepassing bijvoorbeeld ' Fabric \: /MyApp/app1 ' is, is de toepassings identiteit ' Mijntoep \~ app1 ' in 6.0 + en ' Mijntoep/app1 ' in vorige versies. |
| --status-Property [required] | De eigenschap van de status informatie. <br><br> Een entiteit kan status rapporten voor verschillende eigenschappen hebben. De eigenschap is een teken reeks en geen vaste inventarisatie om de flexibiliteit van de rapporter in te stellen voor het categoriseren van de status voorwaarde waarmee het rapport wordt geactiveerd. Zo kan een rapporter met SourceId "LocalWatchdog" de status van de beschik bare schijf op een knoop punt bewaken, zodat de eigenschap "AvailableDisk" op dat knoop punt kan worden gerapporteerd. Dezelfde rapportage functie kan de verbinding met het knoop punt bewaken, zodat een eigenschap ' connectiviteit ' op hetzelfde knoop punt kan worden gerapporteerd. In de Health Store worden deze rapporten behandeld als afzonderlijke status gebeurtenissen voor het opgegeven knoop punt. Samen met de SourceId wordt met de eigenschap een unieke identificatie van de status informatie aangeduid. |
| --status (vereist) | Mogelijke waarden zijn ' Unknown \: ', ' OK ', ' warn ', ' error ', ' Unknown '. |
| --bron-id [vereist] | De bron naam die het client/watchdog/systeem onderdeel identificeert dat de status informatie heeft gegenereerd. |
| --Beschrijving | De beschrijving van de status informatie. <br><br> Hiermee wordt de vrije tekst aangegeven die wordt gebruikt om informatie over het rapport toe te voegen dat wordt gelezen. De maximale teken reeks lengte van de beschrijving is 4096 tekens. Als de gegeven teken reeks langer is, wordt deze automatisch afgekapt. Wanneer de laatste tekens van de beschrijving worden afgekapt, bevatten ze een markering [afgekapt] en de totale teken reeks grootte is 4096 tekens. De aanwezigheid van de markering geeft aan dat gebruikers de afkap ping hebben plaatsgevonden. Houd er rekening mee dat de beschrijving kleiner is dan 4096 tekens uit de oorspronkelijke teken reeks. |
| --Immediate | Een vlag die aangeeft of het rapport direct moet worden verzonden. <br><br> Een status rapport wordt verzonden naar een Service Fabric gateway-toepassing, die wordt doorgestuurd naar de Health Store. Als direct is ingesteld op True, wordt het rapport direct van de HTTP-gateway naar de Health Store verzonden, ongeacht de instellingen van de Fabric-client die door de HTTP-gateway toepassing worden gebruikt. Dit is handig voor kritieke rapporten die zo snel mogelijk moeten worden verzonden. Afhankelijk van de timing en andere voor waarden, kan het verzenden van het rapport nog steeds mislukken, bijvoorbeeld als de HTTP-gateway is gesloten of als het bericht de gateway niet bereikt. Als direct is ingesteld op False, wordt het rapport verzonden op basis van de status client instellingen van de HTTP-gateway. Daarom wordt deze batch gebaseerd op de HealthReportSendInterval-configuratie. Dit is de aanbevolen instelling omdat de Health-client de status rapport berichten kan optimaliseren naar Health Store en dat de status rapporten worden verwerkt. Standaard worden rapporten niet onmiddellijk verzonden. |
| --verwijderen-verlopen | Waarde die aangeeft of het rapport wordt verwijderd uit Health Store wanneer het is verlopen. <br><br> Als deze eigenschap is ingesteld op True, wordt het rapport verwijderd uit de Health Store nadat het is verlopen. Als deze eigenschap is ingesteld op False, wordt het rapport als een fout beschouwd wanneer het is verlopen. De waarde van deze eigenschap is standaard onwaar. Wanneer clients periodiek een rapport rapporteren, moeten ze RemoveWhenExpired False instellen (standaard). Op deze manier heeft de rapporter problemen (bijvoorbeeld deadlock) en kan het rapport niet worden gerapporteerd. de entiteit wordt geëvalueerd als er een fout optreedt wanneer het status rapport verloopt. Hiermee wordt de entiteit gemarkeerd als de fout status. |
| --Volg nummer | Het Volg nummer voor dit status rapport als een numerieke teken reeks. <br><br> Het Volg nummer van het rapport wordt door de Health Store gebruikt voor het detecteren van verouderde rapporten. Als u niets opgeeft, wordt er automatisch een Volg nummer gegenereerd door de Health-client wanneer een rapport wordt toegevoegd. |
| --time-out-t | Standaard \: 60. |
| --TTL | De duur waarvoor dit status rapport geldig is. Dit veld maakt gebruik van de ISO8601-indeling voor het opgeven van de duur. <br><br> Wanneer clients periodiek rapporteren, moeten ze rapporten met een hogere frequentie verzenden dan live. Als clients rapporteren over overgang, kunnen ze de TTL-waarde (time to Live) instellen op oneindig. Wanneer time to Live verloopt, wordt de status gebeurtenis die de status informatie bevat verwijderd uit Health Store, als RemoveWhenExpired is ingesteld op True, of bij fout geëvalueerd als RemoveWhenExpired False is. Als u niets opgeeft, wordt de time-to-Live-waarde standaard ingesteld op oneindig. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Description|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-application-type"></a>sfctl-toepassings type
Hiermee haalt u de lijst met toepassings typen in het Service Fabric cluster die overeenkomen met de opgegeven naam.

Retourneert informatie over de toepassings typen die zijn ingericht of worden ingericht in het Service Fabric cluster. Deze resultaten zijn van toepassings typen waarvan de naam precies overeenkomt met de waarde die is opgegeven als de para meter, en die voldoet aan de opgegeven query parameters. Alle versies van het toepassings type die overeenkomen met de naam van het toepassings type worden geretourneerd, waarbij elke versie als één toepassings type wordt geretourneerd. Het antwoord bevat de naam, versie, status en andere informatie over het toepassings type. Dit is een pagina query, wat betekent dat als niet alle toepassings typen op een pagina passen, er één pagina met resultaten wordt geretourneerd, evenals een vervolg token, dat kan worden gebruikt om de volgende pagina op te halen. Als er bijvoorbeeld 10 toepassings typen zijn, maar een pagina alleen van toepassing is op de eerste drie toepassings typen, of als de maximum resultaten zijn ingesteld op 3, dan wordt er drie geretourneerd. Als u de rest van de resultaten wilt weer geven, haalt u de volgende pagina's op met behulp van het geretourneerde voortzettings token in de volgende query. Er wordt een leeg vervolg token geretourneerd als er geen volgende pagina's zijn.

### <a name="arguments"></a>Argumenten

|Argument|Description|
| --- | --- |
| --Application-type-name [required] | De naam van het toepassings type. |
| --toepassings type-versie | De versie van het toepassings type. |
| --vervolg token | De vervolg token parameter wordt gebruikt om de volgende set resultaten op te halen. Een vervolg token met een niet-lege waarde wordt opgenomen in het antwoord van de API wanneer de resultaten van het systeem niet in één antwoord passen. Wanneer deze waarde wordt door gegeven aan de volgende API-aanroep, retourneert de API de volgende set resultaten. Als er geen verdere resultaten zijn, bevat het vervolg token geen waarde. De waarde van deze para meter mag geen URL-code ring zijn. |
| --exclude-Application-para meters | De vlag waarmee wordt opgegeven of toepassings parameters worden uitgesloten van het resultaat. |
| --Max-resultaten | Het maximum aantal resultaten dat moet worden geretourneerd als onderdeel van de query's in de pagina. Met deze para meter wordt de bovengrens gedefinieerd voor het aantal geretourneerde resultaten. De geretourneerde resultaten kunnen kleiner zijn dan de opgegeven maximum resultaten als ze niet in het bericht passen conform de maximale grootte van de berichten die in de configuratie is gedefinieerd. Als deze para meter nul is of niet is opgegeven, bevat de opgevraagde query zoveel mogelijk resultaten die in het retour bericht passen. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Description|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-application-type-list"></a>sfctl-toepassings type-lijst
Hiermee haalt u de lijst met toepassings typen op in het Service Fabric cluster.

Retourneert informatie over de toepassings typen die zijn ingericht of worden ingericht in het Service Fabric cluster. Elke versie van een toepassings type wordt geretourneerd als één toepassings type. Het antwoord bevat de naam, versie, status en andere informatie over het toepassings type. Dit is een pagina query, wat betekent dat als niet alle toepassings typen op een pagina passen, er één pagina met resultaten wordt geretourneerd, evenals een vervolg token, dat kan worden gebruikt om de volgende pagina op te halen. Als er bijvoorbeeld 10 toepassings typen zijn, maar een pagina alleen van toepassing is op de eerste drie toepassings typen, of als de maximum resultaten zijn ingesteld op 3, dan wordt er drie geretourneerd. Als u de rest van de resultaten wilt weer geven, haalt u de volgende pagina's op met behulp van het geretourneerde voortzettings token in de volgende query. Er wordt een leeg vervolg token geretourneerd als er geen volgende pagina's zijn.

### <a name="arguments"></a>Argumenten

|Argument|Description|
| --- | --- |
| --toepassing-Type-Definition-type-filter | Wordt gebruikt om te filteren op ApplicationTypeDefinitionKind. Dit is het mechanisme dat wordt gebruikt om een Service Fabric toepassings type te definiëren.  <br> -Standaard-standaard waarde, waarmee dezelfde functie wordt uitgevoerd als bij het selecteren van ' alle '. De waarde is 0.  <br> -Alle-filter die overeenkomt met invoer met een wille keurige waarde van ApplicationTypeDefinitionKind. De waarde is 65535.  <br> -ServiceFabricApplicationPackage-filter dat overeenkomt met de invoer met de ApplicationTypeDefinitionKind-waarde ServiceFabricApplicationPackage. De waarde is 1.  <br> -Samengesteld filter dat overeenkomt met de invoer met ApplicationTypeDefinitionKind-waarde opstellen. De waarde is 2. |
| --vervolg token | De vervolg token parameter wordt gebruikt om de volgende set resultaten op te halen. Een vervolg token met een niet-lege waarde wordt opgenomen in het antwoord van de API wanneer de resultaten van het systeem niet in één antwoord passen. Wanneer deze waarde wordt door gegeven aan de volgende API-aanroep, retourneert de API de volgende set resultaten. Als er geen verdere resultaten zijn, bevat het vervolg token geen waarde. De waarde van deze para meter mag geen URL-code ring zijn. |
| --exclude-Application-para meters | De vlag waarmee wordt opgegeven of toepassings parameters worden uitgesloten van het resultaat. |
| --Max-resultaten | Het maximum aantal resultaten dat moet worden geretourneerd als onderdeel van de query's in de pagina. Met deze para meter wordt de bovengrens gedefinieerd voor het aantal geretourneerde resultaten. De geretourneerde resultaten kunnen kleiner zijn dan de opgegeven maximum resultaten als ze niet in het bericht passen conform de maximale grootte van de berichten die in de configuratie is gedefinieerd. Als deze para meter nul is of niet is opgegeven, bevat de opgevraagde query zoveel mogelijk resultaten die in het retour bericht passen. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Description|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-application-unprovision"></a>toewijzing van sfctl-toepassing ongedaan maken
Hiermee verwijdert of maakt u de registratie van een Service Fabric toepassings type van het cluster ongedaan.

Deze bewerking kan alleen worden uitgevoerd als alle toepassings exemplaren van het toepassings type zijn verwijderd. Zodra het toepassings type niet is geregistreerd, kunnen er geen nieuwe toepassings exemplaren voor dit specifieke toepassings type worden gemaakt.

### <a name="arguments"></a>Argumenten

|Argument|Description|
| --- | --- |
| --Application-type-name [required] | De naam van het toepassings type. |
| --toepassings type-versie [vereist] | De versie van het toepassings type zoals gedefinieerd in het manifest van de toepassing. |
| --async-para meter | De vlag waarmee wordt aangegeven of het ongedaan maken van de inrichting asynchroon moet plaatsvinden. Als deze eigenschap is ingesteld op True, wordt de bewerking voor het ongedaan maken van de inrichting geretourneerd wanneer de aanvraag door het systeem wordt geaccepteerd en wordt de inrichtings bewerking voortgezet zonder enige time-outlimiet. De standaardwaarde is false. We raden u echter aan deze in te stellen op True voor grote toepassings pakketten die zijn ingericht. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Description|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-application-upgrade"></a>sfctl-toepassings upgrade
Hiermee wordt een toepassing in het Service Fabric cluster bijgewerkt.

Valideert de opgegeven para meters voor de toepassings upgrade en begint met de upgrade van de toepassing als de para meters geldig zijn. Houd er rekening mee dat de beschrijving van de upgrade de bestaande toepassings beschrijving vervangt. Dit betekent dat als de para meters niet zijn opgegeven, de bestaande para meters voor de toepassingen worden overschreven door de lege parameter lijst. Dit zou ertoe leiden dat de toepassing de standaard waarde van de para meters uit het toepassings manifest gebruikt.

### <a name="arguments"></a>Argumenten

|Argument|Description|
| --- | --- |
| --toepassings-id [vereist] | De identiteit van de toepassing. <br><br> Dit is doorgaans de volledige naam van de toepassing zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de naam van de toepassing bijvoorbeeld ' Fabric \: /MyApp/app1 ' is, is de toepassings identiteit ' Mijntoep \~ app1 ' in 6.0 + en ' Mijntoep/app1 ' in vorige versies. |
| --toepassing-versie [vereist] | De versie van het doel toepassings type (gevonden in het manifest van de toepassing) voor de upgrade van de toepassing. |
| --para meters [vereist] | Een JSON-gecodeerde lijst van toepassings parameters onderdrukkingen die moeten worden toegepast bij het upgraden van de toepassing. |
| --standaard-service-status-beleid | JSON-gecodeerde specificatie van het status beleid dat standaard wordt gebruikt om de status van een service type te evalueren. |
| --fout-actie | De actie die moet worden uitgevoerd wanneer een bewaakte upgrade het bewakings beleid of schendingen van het status beleid tegen komt. |
| --Force-restart | Processen geforceerd opnieuw opstarten tijdens de upgrade, zelfs wanneer de code versie niet is gewijzigd. |
| --status-check-retry-time-out | De tijds duur tussen pogingen om status controles uit te voeren als de toepassing of het cluster niet in orde is.  Standaard \: PT0H10M0S. |
| --status-controle: stabiel-duur | De hoeveelheid tijd die de toepassing of het cluster in orde moet blijven voordat de upgrade wordt voortgezet naar het volgende upgrade domein.  Standaard \: PT0H2M0S. <br><br> Het wordt eerst geïnterpreteerd als een teken reeks die een ISO 8601-duur vertegenwoordigt. Als dat mislukt, wordt dit geïnterpreteerd als een getal dat het totale aantal milliseconden aangeeft. |
| --status-controleren-wacht tijd | De tijds duur die moet worden gewacht na het volt ooien van een upgrade domein voordat het proces status controles wordt gestart.  Standaard waarde is \: 0. |
| --Max-slechte status-apps | Het Maxi maal toegestane percentage van beschadigde geïmplementeerde toepassingen. Wordt weer gegeven als een getal tussen 0 en 100. |
| --modus | De modus die wordt gebruikt om de status te controleren tijdens een rolling upgrade.  Standaard \: UnmonitoredAuto. |
| --replica-set-check-timeout | De maximale tijds duur voor het blok keren van de verwerking van een upgrade domein en het voor komen van Beschik baarheid wanneer er onverwachte problemen zijn. Gemeten in seconden. |
| --service-status-beleid | JSON-gecodeerde toewijzing met status beleid van Service type per service type naam. De kaart is standaard leeg. |
| --time-out-t | Standaard \: 60. |
| --upgrade-time-out van domein | De hoeveelheid tijd die elk upgrade domein moet volt ooien voordat FailureAction wordt uitgevoerd.  Standaard \: p10675199dt02h48m 05.4775807 s. <br><br> Het wordt eerst geïnterpreteerd als een teken reeks die een ISO 8601-duur vertegenwoordigt. Als dat mislukt, wordt dit geïnterpreteerd als een getal dat het totale aantal milliseconden aangeeft. |
| --upgrade-time-out | De hoeveelheid tijd die de algehele upgrade moet volt ooien voordat FailureAction wordt uitgevoerd.  Standaard \: p10675199dt02h48m 05.4775807 s. <br><br> Het wordt eerst geïnterpreteerd als een teken reeks die een ISO 8601-duur vertegenwoordigt. Als dat mislukt, wordt dit geïnterpreteerd als een getal dat het totale aantal milliseconden aangeeft. |
| --waarschuwing-als-fout | Hiermee wordt aangegeven of waarschuwingen worden behandeld met dezelfde ernst als bij fouten. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Description|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-application-upgrade-resume"></a>sfctl-toepassings upgrade-hervatten
Hiermee wordt de upgrade van een toepassing in het Service Fabric cluster hervat.

Hiermee wordt een niet-bewaakte hand matige Service Fabric-toepassings upgrade hervat. Service Fabric één upgrade domein per keer bijwerken. Voor niet-bewaakte hand matige upgrades moet u nadat Service Fabric een upgrade domein hebt voltooid, wachten tot u deze API aanroept voordat u doorgaat met het volgende upgrade domein.

### <a name="arguments"></a>Argumenten

|Argument|Description|
| --- | --- |
| --toepassings-id [vereist] | De identiteit van de toepassing. Dit is doorgaans de volledige naam van de toepassing zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de naam van de toepassing bijvoorbeeld ' Fabric \: /MyApp/app1 ' is, is de toepassings identiteit ' Mijntoep \~ app1 ' in 6.0 + en ' Mijntoep/app1 ' in vorige versies. |
| --upgrade-domein naam [vereist] | De naam van het upgrade domein waarin de upgrade moet worden hervat. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Description|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-application-upgrade-rollback"></a>sfctl-toepassings upgrade-terugdraai actie
De huidige doorlopende upgrade van een toepassing in het Service Fabric cluster wordt teruggezet.

De vorige versie van de upgrade van de huidige toepassing wordt teruggezet. Deze API kan alleen worden gebruikt voor het terugdraaien van de huidige actieve upgrade naar de nieuwe versie. Als de toepassing momenteel niet wordt bijgewerkt, gebruikt u de StartApplicationUpgrade-API om deze te upgraden naar de gewenste versie, met inbegrip van een vorige versie.

### <a name="arguments"></a>Argumenten

|Argument|Description|
| --- | --- |
| --toepassings-id [vereist] | De identiteit van de toepassing. Dit is doorgaans de volledige naam van de toepassing zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de naam van de toepassing bijvoorbeeld ' Fabric \: /MyApp/app1 ' is, is de toepassings identiteit ' Mijntoep \~ app1 ' in 6.0 + en ' Mijntoep/app1 ' in vorige versies. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Description|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-application-upgrade-status"></a>sfctl-toepassings upgrade-status
Hiermee worden gegevens opgehaald voor de laatste upgrade die op deze toepassing is uitgevoerd.

Retourneert informatie over de status van de nieuwste toepassings upgrade, samen met Details voor hulp bij het oplossen van problemen met de status van toepassingen.

### <a name="arguments"></a>Argumenten

|Argument|Description|
| --- | --- |
| --toepassings-id [vereist] | De identiteit van de toepassing. Dit is doorgaans de volledige naam van de toepassing zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de naam van de toepassing bijvoorbeeld ' Fabric \: /MyApp/app1 ' is, is de toepassings identiteit ' Mijntoep \~ app1 ' in 6.0 + en ' Mijntoep/app1 ' in vorige versies. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Description|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-application-upload"></a>sfctl toepassing uploaden
Kopieer een Service Fabric toepassings pakket naar de archief met installatie kopieën.

Hier kunt u de voortgang van het uploaden weer geven voor elk bestand in het pakket. De voortgang van het uploaden wordt verzonden naar `stderr` .

### <a name="arguments"></a>Argumenten

|Argument|Description|
| --- | --- |
| --pad [vereist] | Pad naar het lokale toepassings pakket. |
| --comprimeren | Alleen van toepassing op Service Fabric toepassings pakketten. Maak een nieuwe map met het gecomprimeerde toepassings pakket naar de standaard locatie of naar de locatie die is opgegeven door de para meter voor de gecomprimeerde locatie en upload de zojuist gemaakte map. <br><br> Als er al een gecomprimeerd bestand is gegenereerd door sfctl, wordt dit overschreven als deze vlag is ingesteld. Er wordt een fout geretourneerd als de Directory geen toepassings pakket is. Als de app al een gecomprimeerd toepassings pakket is, wordt de map gekopieerd. Standaard wordt het zojuist gemaakte gecomprimeerde toepassings pakket verwijderd nadat het uploaden is voltooid. Als het uploaden is mislukt, moet u het gecomprimeerde pakket hand matig opschonen. Bij het verwijderen worden geen lege mappen verwijderd die mogelijk zijn gemaakt als de gecomprimeerde locatie parameter verwijst naar niet-bestaande directory's. |
| --gecomprimeerd-locatie | De locatie waar het gecomprimeerde toepassings pakket moet worden geplaatst. <br><br> Als er geen locatie wordt opgegeven, wordt het gecomprimeerde pakket geplaatst onder een nieuwe map met de naam sfctl_compressed_temp onder de bovenliggende map die is opgegeven in het argument pad. Als het argument pad bijvoorbeeld value C \: /FolderA/AppPkg heeft, wordt het gecomprimeerde pakket toegevoegd aan C \: /Foldera/sfctl_compressed_temp/apppkg. |
| --installatie kopie opslag-teken reeks | Archief van de doel installatie kopie waarnaar het toepassings pakket moet worden geüpload.  Standaard \: installatie kopie opslag voor de infra structuur \: . <br><br> Als u wilt uploaden naar een bestands locatie, start u deze para meter met ' file \: '. Anders moet de waarde de connection string voor het opslaan van de installatie kopie zijn, zoals de standaard waarde. |
| --Keep-compressed | Hiermee wordt aangegeven of het gegenereerde gecomprimeerde pakket moet worden bewaard wanneer de upload is voltooid. <br><br> Als dat niet zo is ingesteld, wordt de gecomprimeerde app-pakketten verwijderd als deze zijn voltooid. Als het uploaden is mislukt, wordt het toepassings pakket altijd bewaard in de uitvoermap om het opnieuw te uploaden. |
| --voortgang weer geven | Voortgang van uploaden van bestanden voor grote pakketten weer geven. |
| --time-out-t | De totale time-out in seconden. Uploaden mislukt en er wordt een fout geretourneerd nadat de time-out voor de upload is verstreken. Deze time-out is van toepassing op het hele toepassings pakket en de time-outs van afzonderlijke bestanden zijn gelijk aan de resterende time-outwaarde. Time-out omvat niet de tijd die nodig is om het toepassings pakket te comprimeren.  Standaard \: 300. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Description|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |


## <a name="next-steps"></a>Volgende stappen
- [Stel](service-fabric-cli.md) de service Fabric cli in.
- Meer informatie over het gebruik van de Service Fabric CLI met behulp van de [voorbeeld scripts](./scripts/sfctl-upgrade-application.md).
