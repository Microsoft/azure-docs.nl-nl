---
title: Azure Service Fabric CLI-sfctl-service
description: Meer informatie over sfctl, de Azure Service Fabric-opdracht regel interface. Bevat een lijst met opdrachten voor het beheren van services, service typen en service pakketten.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 426220f38e6ddfaaf8b24bf4f7d34473d881ae56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86245599"
---
# <a name="sfctl-service"></a>sfctl service
Services, service typen en service pakketten maken, verwijderen en beheren.

## <a name="commands"></a>Opdracht

|Opdracht|Beschrijving|
| --- | --- |
| app-naam | Hiermee wordt de naam van de Service Fabric toepassing voor een service opgehaald. |
| code-pakket-lijst | Hiermee haalt u de lijst met code pakketten op die op een Service Fabric knoop punt is geïmplementeerd. |
| maken | Hiermee wordt de opgegeven Service Fabric-service gemaakt. |
| delete | Hiermee verwijdert u een bestaande Service Fabric-service. |
| geïmplementeerd-type | Hiermee wordt de informatie opgehaald over een opgegeven service type van de toepassing die is geïmplementeerd op een knoop punt in een Service Fabric cluster. |
| geïmplementeerd-type-lijst | Hiermee wordt de lijst opgehaald met de informatie over service typen van de toepassingen die zijn geïmplementeerd op een knoop punt in een Service Fabric cluster. |
| beschrijving | Hiermee wordt de beschrijving van een bestaande Service Fabric-service opgehaald. |
| Get-container-logboeken | Hiermee worden de container logboeken opgehaald voor de container die op een Service Fabric knoop punt is geïmplementeerd. |
| status | Hiermee wordt de status van de opgegeven Service Fabric service opgehaald. |
| Info | Hiermee wordt de informatie opgehaald over de specifieke service die bij de Service Fabric toepassing hoort. |
| list | Haalt de informatie op over alle services die horen bij de toepassing die is opgegeven door de toepassings-ID. |
| manifest | Hiermee wordt het manifest opgehaald waarmee een service type wordt beschreven. |
| pakket-implementeren | Pakketten die zijn gekoppeld aan het opgegeven service manifest, worden gedownload naar de installatie kopie cache op het opgegeven knoop punt. |
| status van het pakket | Haalt de informatie over de status van een service pakket voor een specifieke toepassing die is geïmplementeerd voor een Service Fabric knoop punt en toepassing. |
| pakket-info | Hiermee haalt u de lijst met Service pakketten op die zijn geïmplementeerd op een Service Fabric knoop punt dat exact overeenkomt met de opgegeven naam. |
| pakket-lijst | Hiermee wordt de lijst met Service pakketten opgehaald die op een Service Fabric knoop punt zijn geïmplementeerd. |
| herstellen | Hiermee geeft u aan het Service Fabric cluster op dat moet worden geprobeerd om de opgegeven service te herstellen die momenteel is vastgelopen in quorum verlies. |
| rapport-status | Hiermee verzendt u een status rapport voor de Service Fabric-service. |
| holpen | Een Service Fabric partitie omzetten. |
| type-lijst | Hiermee wordt de lijst opgehaald met de informatie over service typen die worden ondersteund door een ingericht toepassings type in een Service Fabric cluster. |
| update | Hiermee wordt de opgegeven service bijgewerkt met de opgegeven beschrijving van de update. |

## <a name="sfctl-service-app-name"></a>app-naam van sfctl-service
Hiermee wordt de naam van de Service Fabric toepassing voor een service opgehaald.

Hiermee wordt de naam van de toepassing voor de opgegeven service opgehaald. Er wordt een 404 FABRIC_E_SERVICE_DOES_NOT_EXIST fout geretourneerd als een service met de opgegeven service-ID niet bestaat.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
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

## <a name="sfctl-service-code-package-list"></a>sfctl-service code-pakket-lijst
Hiermee haalt u de lijst met code pakketten op die op een Service Fabric knoop punt is geïmplementeerd.

Hiermee haalt u de lijst met code pakketten op die zijn geïmplementeerd op een Service Fabric knoop punt voor de betreffende toepassing.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --toepassings-id [vereist] | De identiteit van de toepassing. Dit is doorgaans de volledige naam van de toepassing zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de naam van de toepassing bijvoorbeeld ' Fabric \: /MyApp/app1 ' is, is de toepassings identiteit ' Mijntoep \~ app1 ' in 6.0 + en ' Mijntoep/app1 ' in vorige versies. |
| --node-name [required] | De naam van het knoop punt. |
| --code-pakket naam | De naam van het code pakket dat is opgegeven in het service manifest, geregistreerd als onderdeel van een toepassings type in een Service Fabric cluster. |
| --Service-manifest-naam | De naam van een service manifest dat is geregistreerd als onderdeel van een toepassings type in een Service Fabric cluster. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-service-create"></a>sfctl-service maken
Hiermee wordt de opgegeven Service Fabric-service gemaakt.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --App-ID [vereist] | De identiteit van de toepassing. Dit is doorgaans de volledige naam van de toepassing zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken. Als de naam van de toepassing bijvoorbeeld ' Fabric \: /MyApp/app1 ' is, is de toepassings identiteit ' Mijntoep \~ app1 ' in 6.0 + en ' Mijntoep/app1 ' in vorige versies. |
| --naam [vereist] | De naam van de service. Dit moet een onderliggend item van de toepassings-ID zijn. Dit is de volledige naam met inbegrip van de `fabric\:` URI. `fabric\:/A/B`De service is bijvoorbeeld een onderliggend item van de toepassing `fabric\:/A` . |
| --Service-type [vereist] | De naam van het Service type. |
| --activerings modus | De activerings modus voor het service pakket. |
| --beperkingen | De plaatsings beperkingen als een teken reeks. Plaatsings beperkingen zijn Booleaanse expressies op knooppunt eigenschappen en bieden de mogelijkheid om een service te beperken tot bepaalde knoop punten op basis van de service vereisten. Als u bijvoorbeeld een service wilt plaatsen op knoop punten waarbij NodeType blauw is, geeft u het volgende op: \: "NodeColor = = Blue". |
| --correled-service | De naam van de doel service waarmee moet worden gecorreleerd. |
| --correlatie | Correleer de service met een bestaande service met behulp van een uitlijning-affiniteit. |
| --DNS-naam | De DNS-naam van de service die moet worden gemaakt. De Service Fabric DNS-systeem service moet zijn ingeschakeld voor deze instelling. |
| --exemplaar-aantal | Het aantal instanties. Dit geldt alleen voor stateless Services. |
| --int-schema | Geeft aan dat de service uniform moet worden gepartitioneerd over een bereik van niet-ondertekende gehele getallen. |
| --int-schema-Count | Het aantal partities binnen het bereik van de gehele sleutel dat moet worden gemaakt als een uniform geheel getal is. |
| --int-schema-hoog | Het einde van het sleutel bereik met gehele getallen, als er een uniform geheel getal is voor het partitie schema. |
| --int-schema-laag | Het begin van het sleutel bereik met gehele getallen, als er een uniform geheel getal is voor het partitie schema. |
| --metrische gegevens voor belasting | JSON-gecodeerde lijst met metrische gegevens die worden gebruikt bij de taak verdeling van services tussen knoop punten. |
| --min-replica-set-size | De minimale grootte van de replicaset als een getal. Dit geldt alleen voor stateful Services. |
| --kosten voor verplaatsing | Hiermee geeft u de verplaatsings kosten voor de service op. Mogelijke waarden zijn \: ' nul ', ' laag ', ' medium ', ' high ', ' VeryHigh '. |
| --name-schema | Geeft aan dat de service meerdere benoemde partities moet hebben. |
| --schema-lijst | JSON-gecodeerde lijst met namen voor het partitioneren van de service in, als het partitie schema met de naam wordt gebruikt. |
| --zonder persistente status | Als deze eigenschap waar is, wordt hiermee aangegeven dat de service geen permanente status op de lokale schijf heeft opgeslagen, of alleen de status in het geheugen opslaat. |
| --plaatsing-beleid-lijst | JSON-gecodeerde lijst met plaatsings beleid voor de service en eventuele bijbehorende domein namen. Beleids regels kunnen een of meer van \: `NonPartiallyPlaceService` , `PreferPrimaryDomain` , `RequireDomain` ,, zijn `RequireDomainDistribution` . |
| --quorum verlies-wachten | De maximum duur, in seconden, waarvoor een partitie een status van quorum verlies mag hebben. Dit geldt alleen voor stateful Services. |
| --replica-opnieuw opstarten-wachten | De duur, in seconden, tussen het moment dat een replica uitvalt en wanneer een nieuwe replica wordt gemaakt. Dit geldt alleen voor stateful Services. |
| --schalen-beleids regels | JSON-gecodeerde lijst met schaal beleid voor deze service. |
| --Service-placement-time | De duur waarvoor replica's inbouwen kunnen blijven voordat wordt gemeld dat de build is vastgelopen. Dit geldt alleen voor stateful Services. |
| --Singleton-schema | Geeft aan dat de service één partitie moet hebben of een niet-gepartitioneerde service moet zijn. |
| --stand-by-replica-behoud | De maximum duur, in seconden, waarvoor stand-by replica's worden behouden voordat ze worden verwijderd. Dit geldt alleen voor stateful Services. |
| -stateful | Geeft aan dat de service een stateful service is. |
| -stateless | Geeft aan dat de service een stateless service is. |
| --doel-replica-set-size | De doel grootte van de replicaset is ingesteld als een getal. Dit geldt alleen voor stateful Services. |
| --time-out-t | Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-service-delete"></a>sfctl-service verwijderen
Hiermee verwijdert u een bestaande Service Fabric-service.

U moet een service maken voordat u deze kunt verwijderen. Service Fabric probeert standaard de service replica's op een correcte manier te sluiten en vervolgens de service te verwijderen. Als de service echter problemen heeft met het op de juiste wijze sluiten van de replica, kan de verwijderings bewerking lang duren of vastlopen. Gebruik de optionele vlag ForceRemove om de correcte sluit sequentie over te slaan en de service geforceerd te verwijderen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --Service-id [vereist] | De identiteit van de service. Deze ID is doorgaans de volledige naam van de service zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de service naam bijvoorbeeld ' Fabric \: /MyApp/app1/svc1 ' is, is de service-identiteit ' Mijntoep \~ app1 \~ svc1 ' in 6.0 + en ' Mijntoep/app1/svc1 ' in vorige versies. |
| --Force-Remove | Verwijder een Service Fabric toepassing of service geforceerd zonder de juiste afsluit procedure te door lopen. Deze para meter kan worden gebruikt om een toepassing of service geforceerd te verwijderen waarvoor een time-out is opgetreden vanwege problemen in de service code die voor komen dat replica's worden gesloten. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-service-deployed-type"></a>geïmplementeerde sfctl-Service-type
Hiermee wordt de informatie opgehaald over een opgegeven service type van de toepassing die is geïmplementeerd op een knoop punt in een Service Fabric cluster.

Hiermee wordt de lijst opgehaald met de informatie over een specifiek Service type uit de toepassingen die zijn geïmplementeerd op een knoop punt in een Service Fabric cluster. Het antwoord bevat de naam van het Service type, de registratie status, het code pakket waarmee het is geregistreerd en de activerings-ID van het service pakket. Elk item vertegenwoordigt één activering van een service type, gedifferentieerd door de activerings-ID.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --toepassings-id [vereist] | De identiteit van de toepassing. Dit is doorgaans de volledige naam van de toepassing zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de naam van de toepassing bijvoorbeeld ' Fabric \: /MyApp/app1 ' is, is de toepassings identiteit ' Mijntoep \~ app1 ' in 6.0 + en ' Mijntoep/app1 ' in vorige versies. |
| --node-name [required] | De naam van het knoop punt. |
| --Service-type-naam [vereist] | Hiermee geeft u de naam van een Service Fabric-Service type op. |
| --Service-manifest-naam | De naam van het service manifest waarmee de lijst met geïmplementeerde informatie over het Service type wordt gefilterd. Als deze is opgegeven, bevat het antwoord alleen de informatie over de service typen die in dit service manifest zijn gedefinieerd. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-service-deployed-type-list"></a>geïmplementeerde sfctl-Service-type-lijst
Hiermee wordt de lijst opgehaald met de informatie over service typen van de toepassingen die zijn geïmplementeerd op een knoop punt in een Service Fabric cluster.

Hiermee wordt de lijst opgehaald met de informatie over service typen van de toepassingen die zijn geïmplementeerd op een knoop punt in een Service Fabric cluster. Het antwoord bevat de naam van het Service type, de registratie status, het code pakket waarmee het is geregistreerd en de activerings-ID van het service pakket.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --toepassings-id [vereist] | De identiteit van de toepassing. Dit is doorgaans de volledige naam van de toepassing zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de naam van de toepassing bijvoorbeeld ' Fabric \: /MyApp/app1 ' is, is de toepassings identiteit ' Mijntoep \~ app1 ' in 6.0 + en ' Mijntoep/app1 ' in vorige versies. |
| --node-name [required] | De naam van het knoop punt. |
| --Service-manifest-naam | De naam van het service manifest waarmee de lijst met geïmplementeerde informatie over het Service type wordt gefilterd. Als deze is opgegeven, bevat het antwoord alleen de informatie over de service typen die in dit service manifest zijn gedefinieerd. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-service-description"></a>Beschrijving van de sfctl-service
Hiermee wordt de beschrijving van een bestaande Service Fabric-service opgehaald.

Hiermee wordt de beschrijving van een bestaande Service Fabric-service opgehaald. Er moet een service worden gemaakt voordat de beschrijving ervan kan worden verkregen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
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

## <a name="sfctl-service-get-container-logs"></a>Get-container van sfctl-service-logboeken
Hiermee worden de container logboeken opgehaald voor de container die op een Service Fabric knoop punt is geïmplementeerd.

Hiermee haalt u de container logboeken op voor een container die is geïmplementeerd op een Service Fabric knoop punt voor het gegeven code pakket.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --toepassings-id [vereist] | De identiteit van de toepassing. Dit is doorgaans de volledige naam van de toepassing zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de naam van de toepassing bijvoorbeeld ' Fabric \: /MyApp/app1 ' is, is de toepassings identiteit ' Mijntoep \~ app1 ' in 6.0 + en ' Mijntoep/app1 ' in vorige versies. |
| --code-pakket naam [vereist] | De naam van het code pakket dat is opgegeven in het service manifest, geregistreerd als onderdeel van een toepassings type in een Service Fabric cluster. |
| --node-name [required] | De naam van het knoop punt. |
| --Service-manifest-naam [vereist] | De naam van een service manifest dat is geregistreerd als onderdeel van een toepassings type in een Service Fabric cluster. |
| --vorige | Hiermee geeft u op of container logboeken moeten worden opgehaald uit gesloten/Dead-containers van het code pakket exemplaar. |
| --staart | Aantal regels dat moet worden weer gegeven aan het einde van de logboeken. De standaard waarde is 100. ' all ' om de volledige logboeken weer te geven. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-service-health"></a>status van sfctl-service
Hiermee wordt de status van de opgegeven Service Fabric service opgehaald.

Hiermee worden de status gegevens van de opgegeven service opgehaald. Gebruik EventsHealthStateFilter om de verzameling van status gebeurtenissen die op de service zijn gerapporteerd, te filteren op basis van de status. Gebruik PartitionsHealthStateFilter om de verzameling van opgehaalde partities te filteren. Als u een service opgeeft die niet voor komt in de Health Store, retourneert deze aanvraag een fout.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --Service-id [vereist] | De identiteit van de service. Deze ID is doorgaans de volledige naam van de service zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de service naam bijvoorbeeld ' Fabric \: /MyApp/app1/svc1 ' is, is de service-identiteit ' Mijntoep \~ app1 \~ svc1 ' in 6.0 + en ' Mijntoep/app1/svc1 ' in vorige versies. |
| --gebeurtenissen-status-filter | Hiermee kunt u het verzamelen van HealthEvent-objecten die zijn geretourneerd op basis van de status wordt gefilterd. De mogelijke waarden voor deze para meter zijn gehele getallen van een van de volgende statussen. Alleen gebeurtenissen die overeenkomen met het filter worden geretourneerd. Alle gebeurtenissen worden gebruikt om de geaggregeerde status te evalueren. Als u niets opgeeft, worden alle vermeldingen geretourneerd. De status waarden zijn inventarisatie op basis van een vlag, waardoor de waarde kan bestaan uit een combi natie van deze waarden, verkregen met behulp van de operator bitsgewijze ' of '. Als de opgegeven waarde bijvoorbeeld 6 is, worden alle gebeurtenissen met HealthState waarde OK (2) en waarschuwing (4) geretourneerd.  <br> -Standaard-standaard waarde. Komt overeen met een wille keurige HealthState. De waarde is nul.  <br> -Geen: filter dat niet overeenkomt met een HealthState-waarde. Wordt gebruikt om geen resultaten te retour neren voor een bepaalde verzameling statussen. De waarde is 1.  <br> -OK-filter dat overeenkomt met de invoer met HealthState waarde OK. De waarde is 2.  <br> -Waarschuwings filter dat overeenkomt met invoer met HealthState-waarde waarschuwing. De waarde is 4.  <br> -Fout filter dat overeenkomt met de invoer met de HealthState-waarde fout. De waarde is 8.  <br> -Alle-filter die overeenkomt met invoer met een wille keurige waarde van HealthState. De waarde is 65535. |
| --exclude-status-statistieken | Hiermee wordt aangegeven of de status statistieken moeten worden geretourneerd als onderdeel van het query resultaat. Standaard onwaar. In de statistieken wordt het aantal onderliggende entiteiten in de status OK, waarschuwing en fout weer gegeven. |
| --partities-status-filter | Hiermee staat u toe dat de status objecten van de partities worden gefilterd die zijn geretourneerd in het resultaat van de service status query op basis van hun status. De mogelijke waarden voor deze para meter zijn gehele getallen van een van de volgende statussen. Alleen partities die overeenkomen met het filter worden geretourneerd. Alle partities worden gebruikt om de geaggregeerde status te evalueren. Als u niets opgeeft, worden alle vermeldingen geretourneerd. De status waarden zijn inventarisatie op basis van een vlag, waardoor de waarde kan bestaan uit een combi natie van deze waarde die is verkregen met behulp van de operator bitsgewijze of. Als de opgegeven waarde bijvoorbeeld 6 is, wordt de status van partities met HealthState waarde OK (2) en waarschuwing (4) geretourneerd.  <br> -Standaard-standaard waarde. Komt overeen met een wille keurige HealthState. De waarde is nul.  <br> -Geen: filter dat niet overeenkomt met een HealthState-waarde. Wordt gebruikt om geen resultaten te retour neren voor een bepaalde verzameling statussen. De waarde is 1.  <br> -OK-filter dat overeenkomt met de invoer met HealthState waarde OK. De waarde is 2.  <br> -Waarschuwings filter dat overeenkomt met invoer met HealthState-waarde waarschuwing. De waarde is 4.  <br> -Fout filter dat overeenkomt met de invoer met de HealthState-waarde fout. De waarde is 8.  <br> -Alle-filter die overeenkomt met invoer met een wille keurige waarde van HealthState. De waarde is 65535. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-service-info"></a>sfctl-service gegevens
Hiermee wordt de informatie opgehaald over de specifieke service die bij de Service Fabric toepassing hoort.

Retourneert de informatie over de opgegeven service die hoort bij de opgegeven Service Fabric-toepassing.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --toepassings-id [vereist] | De identiteit van de toepassing. Dit is doorgaans de volledige naam van de toepassing zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de naam van de toepassing bijvoorbeeld ' Fabric \: /MyApp/app1 ' is, is de toepassings identiteit ' Mijntoep \~ app1 ' in 6.0 + en ' Mijntoep/app1 ' in vorige versies. |
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

## <a name="sfctl-service-list"></a>sfctl-service lijst
Haalt de informatie op over alle services die horen bij de toepassing die is opgegeven door de toepassings-ID.

Retourneert de informatie over alle services die deel uitmaken van de toepassing die wordt opgegeven door de toepassings-ID.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --toepassings-id [vereist] | De identiteit van de toepassing. Dit is doorgaans de volledige naam van de toepassing zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de naam van de toepassing bijvoorbeeld ' Fabric \: /MyApp/app1 ' is, is de toepassings identiteit ' Mijntoep \~ app1 ' in 6.0 + en ' Mijntoep/app1 ' in vorige versies. |
| --vervolg token | De vervolg token parameter wordt gebruikt om de volgende set resultaten op te halen. Een vervolg token met een niet-lege waarde wordt opgenomen in het antwoord van de API wanneer de resultaten van het systeem niet in één antwoord passen. Wanneer deze waarde wordt door gegeven aan de volgende API-aanroep, retourneert de API de volgende set resultaten. Als er geen verdere resultaten zijn, bevat het vervolg token geen waarde. De waarde van deze para meter mag geen URL-code ring zijn. |
| --Service-type-name | De naam van het Service type dat wordt gebruikt om de services te filteren op query's voor. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-service-manifest"></a>sfctl-service manifest
Hiermee wordt het manifest opgehaald waarmee een service type wordt beschreven.

Hiermee wordt het manifest opgehaald waarmee een service type wordt beschreven. Het antwoord bevat de XML van het service manifest als een teken reeks.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --Application-type-name [required] | De naam van het toepassings type. |
| --toepassings type-versie [vereist] | De versie van het toepassings type. |
| --Service-manifest-naam [vereist] | De naam van een service manifest dat is geregistreerd als onderdeel van een toepassings type in een Service Fabric cluster. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-service-package-deploy"></a>sfctl-service pakket-implementeren
Pakketten die zijn gekoppeld aan het opgegeven service manifest, worden gedownload naar de installatie kopie cache op het opgegeven knoop punt.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --app-type-naam [vereist] | De naam van het toepassings manifest voor het bijbehorende aangevraagde service manifest. |
| --app-type-versie [vereist] | De versie van het toepassings manifest voor het bijbehorende aangevraagde service manifest. |
| --node-name [required] | De naam van het knoop punt. |
| --Service-manifest-naam [vereist] | De naam van het service manifest dat is gekoppeld aan de pakketten die worden gedownload. |
| --delen-beleid | JSON-gecodeerde lijst met beleids regels voor delen. Elk element voor het delen van beleid bestaat uit een ' name ' en ' scope '. De naam komt overeen met de naam van de code, configuratie of het gegevens pakket dat moet worden gedeeld. Het bereik kan ' none ', ' all ', ' code ', ' config ' of ' data ' zijn. |
| --time-out-t | Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-service-package-health"></a>sfctl-service pakket-status
Haalt de informatie over de status van een service pakket voor een specifieke toepassing die is geïmplementeerd voor een Service Fabric knoop punt en toepassing.

Haalt de informatie over de status van een service pakket voor een specifieke toepassing die op een Service Fabric knoop punt is geïmplementeerd. Gebruik EventsHealthStateFilter om optioneel te filteren op de verzameling HealthEvent-objecten die zijn gerapporteerd op het geïmplementeerde service pakket op basis van de status.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --toepassings-id [vereist] | De identiteit van de toepassing. Dit is doorgaans de volledige naam van de toepassing zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de naam van de toepassing bijvoorbeeld ' Fabric \: /MyApp/app1 ' is, is de toepassings identiteit ' Mijntoep \~ app1 ' in 6.0 + en ' Mijntoep/app1 ' in vorige versies. |
| --node-name [required] | De naam van het knoop punt. |
| --service-pakket-naam [vereist] | De naam van het service pakket. |
| --gebeurtenissen-status-filter | Hiermee kunt u het verzamelen van HealthEvent-objecten die zijn geretourneerd op basis van de status wordt gefilterd. De mogelijke waarden voor deze para meter zijn gehele getallen van een van de volgende statussen. Alleen gebeurtenissen die overeenkomen met het filter worden geretourneerd. Alle gebeurtenissen worden gebruikt om de geaggregeerde status te evalueren. Als u niets opgeeft, worden alle vermeldingen geretourneerd. De status waarden zijn inventarisatie op basis van een vlag, waardoor de waarde kan bestaan uit een combi natie van deze waarden, verkregen met behulp van de operator bitsgewijze ' of '. Als de opgegeven waarde bijvoorbeeld 6 is, worden alle gebeurtenissen met HealthState waarde OK (2) en waarschuwing (4) geretourneerd.  <br> -Standaard-standaard waarde. Komt overeen met een wille keurige HealthState. De waarde is nul.  <br> -Geen: filter dat niet overeenkomt met een HealthState-waarde. Wordt gebruikt om geen resultaten te retour neren voor een bepaalde verzameling statussen. De waarde is 1.  <br> -OK-filter dat overeenkomt met de invoer met HealthState waarde OK. De waarde is 2.  <br> -Waarschuwings filter dat overeenkomt met invoer met HealthState-waarde waarschuwing. De waarde is 4.  <br> -Fout filter dat overeenkomt met de invoer met de HealthState-waarde fout. De waarde is 8.  <br> -Alle-filter die overeenkomt met invoer met een wille keurige waarde van HealthState. De waarde is 65535. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-service-package-info"></a>sfctl-service pakket-info
Hiermee haalt u de lijst met Service pakketten op die zijn geïmplementeerd op een Service Fabric knoop punt dat exact overeenkomt met de opgegeven naam.

Retourneert de informatie over de service pakketten die zijn geïmplementeerd op een Service Fabric knoop punt voor de betreffende toepassing. Deze resultaten zijn van service pakketten waarvan de naam exact overeenkomt met de naam van het service pakket dat is opgegeven als de para meter.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --toepassings-id [vereist] | De identiteit van de toepassing. Dit is doorgaans de volledige naam van de toepassing zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de naam van de toepassing bijvoorbeeld ' Fabric \: /MyApp/app1 ' is, is de toepassings identiteit ' Mijntoep \~ app1 ' in 6.0 + en ' Mijntoep/app1 ' in vorige versies. |
| --node-name [required] | De naam van het knoop punt. |
| --service-pakket-naam [vereist] | De naam van het service pakket. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-service-package-list"></a>sfctl-service pakket-lijst
Hiermee wordt de lijst met Service pakketten opgehaald die op een Service Fabric knoop punt zijn geïmplementeerd.

Retourneert de informatie over de service pakketten die zijn geïmplementeerd op een Service Fabric knoop punt voor de betreffende toepassing.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --toepassings-id [vereist] | De identiteit van de toepassing. Dit is doorgaans de volledige naam van de toepassing zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de naam van de toepassing bijvoorbeeld ' Fabric \: /MyApp/app1 ' is, is de toepassings identiteit ' Mijntoep \~ app1 ' in 6.0 + en ' Mijntoep/app1 ' in vorige versies. |
| --node-name [required] | De naam van het knoop punt. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-service-recover"></a>sfctl-service herstellen
Hiermee geeft u aan het Service Fabric cluster op dat moet worden geprobeerd om de opgegeven service te herstellen die momenteel is vastgelopen in quorum verlies.

Hiermee geeft u aan het Service Fabric cluster op dat moet worden geprobeerd om de opgegeven service te herstellen die momenteel is vastgelopen in quorum verlies. Deze bewerking mag alleen worden uitgevoerd als bekend is dat de replica's die niet beschikbaar zijn, niet kunnen worden hersteld. Een onjuist gebruik van deze API kan leiden tot mogelijk gegevens verlies.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
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

## <a name="sfctl-service-report-health"></a>sfctl-service rapport-status
Hiermee verzendt u een status rapport voor de Service Fabric-service.

Rapporteert de status van de opgegeven Service Fabric service. Het rapport moet de informatie bevatten over de bron van het status rapport en de eigenschap waarvoor deze is gerapporteerd. Het rapport wordt verzonden naar een Service Fabric Gateway Service, die wordt doorgestuurd naar de Health Store. Het rapport kan worden geaccepteerd door de gateway, maar door de Health Store na een extra validatie afgewezen. Het Health Store kan bijvoorbeeld het rapport afwijzen vanwege een ongeldige para meter, zoals een verouderd volgorde nummer. Als u wilt zien of het rapport is toegepast in de Health Store, controleert u of het rapport wordt weer gegeven in de status gebeurtenissen van de service.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --status-Property [required] | De eigenschap van de status informatie. <br><br> Een entiteit kan status rapporten voor verschillende eigenschappen hebben. De eigenschap is een teken reeks en geen vaste inventarisatie om de flexibiliteit van de rapporter in te stellen voor het categoriseren van de status voorwaarde waarmee het rapport wordt geactiveerd. Zo kan een rapporter met SourceId "LocalWatchdog" de status van de beschik bare schijf op een knoop punt bewaken, zodat de eigenschap "AvailableDisk" op dat knoop punt kan worden gerapporteerd. Dezelfde rapportage functie kan de verbinding met het knoop punt bewaken, zodat een eigenschap ' connectiviteit ' op hetzelfde knoop punt kan worden gerapporteerd. In de Health Store worden deze rapporten behandeld als afzonderlijke status gebeurtenissen voor het opgegeven knoop punt. Samen met de SourceId wordt met de eigenschap een unieke identificatie van de status informatie aangeduid. |
| --status (vereist) | Mogelijke waarden zijn ' Unknown \: ', ' OK ', ' warn ', ' error ', ' Unknown '. |
| --Service-id [vereist] | De identiteit van de service. <br><br> Dit is doorgaans de volledige naam van de service zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken. Als de service naam bijvoorbeeld ' Fabric \: /MyApp/app1/svc1 ' is, is de service-identiteit ' Mijntoep \~ app1 \~ svc1 ' in 6.0 + en ' Mijntoep/app1/svc1 ' in vorige versies. |
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

## <a name="sfctl-service-resolve"></a>sfctl-service oplossen
Een Service Fabric partitie omzetten.

Een Service Fabric-service partitie oplossen om de eind punten van de service replica's op te halen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --Service-id [vereist] | De identiteit van de service. Deze ID is doorgaans de volledige naam van de service zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de service naam bijvoorbeeld ' Fabric \: /MyApp/app1/svc1 ' is, is de service-identiteit ' Mijntoep \~ app1 \~ svc1 ' in 6.0 + en ' Mijntoep/app1/svc1 ' in vorige versies. |
| --Partition-sleutel-type | Sleutel type voor de partitie. Deze para meter is vereist als het partitie schema voor de service Int64Range of een naam heeft. De mogelijke waarden zijn als volgt. -Geen (1)-geeft aan dat de para meter PartitionKeyValue niet is opgegeven. Dit is geldig voor de partities met het partitie schema als Singleton. Dit is de standaardwaarde. De waarde is 1. -Int64Range (2)-geeft aan dat de para meter PartitionKeyValue een int64-partitie sleutel is. Dit is geldig voor de partities met partitie schema als Int64Range. De waarde is 2. -Met de naam (3): geeft aan dat de PartitionKeyValue-para meter een naam is van de partitie. Dit is geldig voor de partities met het partitie schema met de naam. De waarde is 3. |
| --Partition-sleutel-waarde | Partitie sleutel. Dit is vereist als het partitie schema voor de service Int64Range of een naam heeft. Dit is niet de partitie-ID, maar is wel de sleutel waarde integer of de naam van de partitie-ID. Als uw service bijvoorbeeld bereik partities van 0 tot 10 gebruikt, is het PartitionKeyValue een geheel getal in dat bereik. Beschrijving van de query service om het bereik of de naam weer te geven. |
| --vorige-RSP-version | De waarde in het veld versie van het antwoord dat eerder is ontvangen. Dit is vereist als de gebruiker weet dat het resultaat dat eerder is gevonden, verouderd is. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-service-type-list"></a>sfctl-Service type-lijst
Hiermee wordt de lijst opgehaald met de informatie over service typen die worden ondersteund door een ingericht toepassings type in een Service Fabric cluster.

Hiermee wordt de lijst opgehaald met de informatie over service typen die worden ondersteund door een ingericht toepassings type in een Service Fabric cluster. Het gegeven toepassings type moet bestaan. Als dat niet het geval is, wordt de status 404 geretourneerd.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --Application-type-name [required] | De naam van het toepassings type. |
| --toepassings type-versie [vereist] | De versie van het toepassings type. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-service-update"></a>sfctl-service-update
Hiermee wordt de opgegeven service bijgewerkt met de opgegeven beschrijving van de update.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --Service-id [vereist] | De identiteit van de service. Dit is doorgaans de volledige naam van de service zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de service naam bijvoorbeeld ' Fabric \: /MyApp/app1/svc1 ' is, is de service-identiteit ' Mijntoep \~ app1 \~ svc1 ' in 6.0 + en ' Mijntoep/app1/svc1 ' in vorige versies. |
| --beperkingen | De plaatsings beperkingen als een teken reeks. Plaatsings beperkingen zijn Booleaanse expressies op knooppunt eigenschappen en bieden de mogelijkheid om een service te beperken tot bepaalde knoop punten op basis van de service vereisten. Als u bijvoorbeeld een service wilt plaatsen op knoop punten waarbij NodeType blauw is, geeft u het volgende op: \: "NodeColor = = Blue". |
| --correled-service | De naam van de doel service waarmee moet worden gecorreleerd. |
| --correlatie | Correleer de service met een bestaande service met behulp van een uitlijning-affiniteit. |
| --exemplaar-aantal | Het aantal instanties. Dit geldt alleen voor stateless Services. |
| --metrische gegevens voor belasting | JSON-gecodeerde lijst met metrische gegevens die worden gebruikt bij taak verdeling tussen knoop punten. |
| --min-replica-set-size | De minimale grootte van de replicaset als een getal. Dit geldt alleen voor stateful Services. |
| --kosten voor verplaatsing | Hiermee geeft u de verplaatsings kosten voor de service op. Mogelijke waarden zijn \: ' nul ', ' laag ', ' medium ', ' high ', ' VeryHigh '. |
| --plaatsing-beleid-lijst | JSON-gecodeerde lijst met plaatsings beleid voor de service en eventuele bijbehorende domein namen. Beleids regels kunnen een of meer van \: `NonPartiallyPlaceService` , `PreferPrimaryDomain` , `RequireDomain` ,, zijn `RequireDomainDistribution` . |
| --quorum verlies-wachten | De maximum duur, in seconden, waarvoor een partitie een status van quorum verlies mag hebben. Dit geldt alleen voor stateful Services. |
| --replica-opnieuw opstarten-wachten | De duur, in seconden, tussen het moment dat een replica uitvalt en wanneer een nieuwe replica wordt gemaakt. Dit geldt alleen voor stateful Services. |
| --schalen-beleids regels | JSON-gecodeerde lijst met schaal beleid voor deze service. |
| --Service-placement-time | De duur waarvoor replica's inbouwen kunnen blijven voordat wordt gemeld dat de build is vastgelopen. Dit geldt alleen voor stateful Services. |
| --stand-by-replica-behoud | De maximum duur, in seconden, waarvoor stand-by replica's worden behouden voordat ze worden verwijderd. Dit geldt alleen voor stateful Services. |
| -stateful | Geeft aan dat de doel service een stateful service is. |
| -stateless | Geeft aan dat de doel service een stateless service is. |
| --doel-replica-set-size | De doel grootte van de replicaset is ingesteld als een getal. Dit geldt alleen voor stateful Services. |
| --time-out-t | Standaard \: 60. |

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
