---
title: Azure Service Fabric CLI-sfctl replica
description: Meer informatie over sfctl, de Azure Service Fabric-opdracht regel interface. Bevat een lijst met opdrachten voor het beheren van replica's.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 3ed1ecca686471e052f07e945738ad1a49e68464
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260306"
---
# <a name="sfctl-replica"></a>sfctl replica
De replica's beheren die deel uitmaken van service partities.

## <a name="commands"></a>Opdracht

|Opdracht|Beschrijving|
| --- | --- |
| geïmplementeerd | Hiermee haalt u de details op van de replica die op een Service Fabric knoop punt is geïmplementeerd. |
| geïmplementeerd-lijst | Hiermee wordt de lijst met replica's opgehaald die op een Service Fabric knoop punt zijn geïmplementeerd. |
| status | Hiermee wordt de status opgehaald van een Service Fabric stateful service replica of stateless service exemplaar. |
| Info | Hiermee wordt de informatie opgehaald over een replica van een Service Fabric partitie. |
| list | Hiermee haalt u de informatie over replica's van een Service Fabric-service partitie. |
| verwijderen | Hiermee verwijdert u een service replica die wordt uitgevoerd op een knoop punt. |
| rapport-status | Hiermee verzendt u een status rapport op de replica van de Service Fabric. |
| restart | Hiermee wordt een service replica van een blijvende service die wordt uitgevoerd op een knoop punt opnieuw gestart. |

## <a name="sfctl-replica-deployed"></a>sfctl replica geïmplementeerd
Hiermee haalt u de details op van de replica die op een Service Fabric knoop punt is geïmplementeerd.

Hiermee haalt u de details op van de replica die op een Service Fabric knoop punt is geïmplementeerd. De informatie omvat het soort service, de service naam, de huidige service bewerking, de start datum van de huidige service bewerking, de partitie-ID, de replica/exemplaar-ID, de gerapporteerde belasting en andere informatie.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --node-name [required] | De naam van het knoop punt. |
| --partitie-id [vereist] | De identiteit van de partitie. |
| --replica-id [vereist] | De id van de replica. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-replica-deployed-list"></a>geïmplementeerde sfctl-replica lijst
Hiermee wordt de lijst met replica's opgehaald die op een Service Fabric knoop punt zijn geïmplementeerd.

Hiermee wordt de lijst opgehaald met de informatie over replica's die op een Service Fabric knoop punt zijn geïmplementeerd. De gegevens bevatten de partitie-ID, de replica-ID, de status van de replica, de naam van de service, de naam van het Service type en andere informatie. Gebruik de para meters PartitionId of ServiceManifestName om informatie over de geïmplementeerde replica's te retour neren die overeenkomen met de opgegeven waarden voor die para meters.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --toepassings-id [vereist] | De identiteit van de toepassing. Dit is doorgaans de volledige naam van de toepassing zonder het URI- \: schema ' fabric '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken ' '. Als de naam van de toepassing bijvoorbeeld ' Fabric \: /MyApp/app1 ' is, is de toepassings identiteit ' Mijntoep \~ app1 ' in 6.0 + en ' Mijntoep/app1 ' in vorige versies. |
| --node-name [required] | De naam van het knoop punt. |
| --partitie-id | De identiteit van de partitie. |
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

## <a name="sfctl-replica-health"></a>status van sfctl-replica
Hiermee wordt de status opgehaald van een Service Fabric stateful service replica of stateless service exemplaar.

Hiermee wordt de status van een replica van een Service Fabric opgehaald. Gebruik EventsHealthStateFilter om de verzameling van status gebeurtenissen die op de replica zijn gerapporteerd, te filteren op basis van de status.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --partitie-id [vereist] | De identiteit van de partitie. |
| --replica-id [vereist] | De id van de replica. |
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

## <a name="sfctl-replica-info"></a>sfctl-replica gegevens
Hiermee wordt de informatie opgehaald over een replica van een Service Fabric partitie.

Het antwoord bevat de ID, functie, status, de status, de knooppunt naam, de uptime en andere gegevens over de replica.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --partitie-id [vereist] | De identiteit van de partitie. |
| --replica-id [vereist] | De id van de replica. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-replica-list"></a>lijst met sfctl-replica's
Hiermee haalt u de informatie over replica's van een Service Fabric-service partitie.

Het GetReplicas-eind punt retourneert informatie over de replica's van de opgegeven partitie. Het antwoord bevat de ID, functie, status, de status, de knooppunt naam, de uptime en andere gegevens over de replica.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --partitie-id [vereist] | De identiteit van de partitie. |
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

## <a name="sfctl-replica-remove"></a>sfctl replica verwijderen
Hiermee verwijdert u een service replica die wordt uitgevoerd op een knoop punt.

Deze API simuleert een Service Fabric replica fout door een replica te verwijderen uit een Service Fabric cluster. Het verwijderen sluit de replica, zet de replica over naar de rol geen, en verwijdert vervolgens alle status informatie van de replica uit het cluster. Deze API test het pad voor het verwijderen van de replica status en simuleert het permanente pad van het rapport fout via client-Api's. Waarschuwing: er worden geen veiligheids controles uitgevoerd wanneer deze API wordt gebruikt. Een onjuist gebruik van deze API kan leiden tot gegevens verlies voor stateful Services. Daarnaast heeft de vlag forceRemove gevolgen voor alle andere replica's die worden gehost in hetzelfde proces.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --node-name [required] | De naam van het knoop punt. |
| --partitie-id [vereist] | De identiteit van de partitie. |
| --replica-id [vereist] | De id van de replica. |
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

## <a name="sfctl-replica-report-health"></a>sfctl-replica rapport-status
Hiermee verzendt u een status rapport op de replica van de Service Fabric.

Hiermee wordt de status van de opgegeven Service Fabric replica gerapporteerd. Het rapport moet de informatie bevatten over de bron van het status rapport en de eigenschap waarvoor deze is gerapporteerd. Het rapport wordt verzonden naar een Service Fabric gateway replica, die wordt doorgestuurd naar de Health Store. Het rapport kan worden geaccepteerd door de gateway, maar door de Health Store na een extra validatie afgewezen. Het Health Store kan bijvoorbeeld het rapport afwijzen vanwege een ongeldige para meter, zoals een verouderd volgorde nummer. Als u wilt zien of het rapport is toegepast in de Health Store, voert u replica status ophalen uit en controleert u of het rapport wordt weer gegeven in de sectie HealthEvents.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --status-Property [required] | De eigenschap van de status informatie. <br><br> Een entiteit kan status rapporten voor verschillende eigenschappen hebben. De eigenschap is een teken reeks en geen vaste inventarisatie om de flexibiliteit van de rapporter in te stellen voor het categoriseren van de status voorwaarde waarmee het rapport wordt geactiveerd. Zo kan een rapporter met SourceId "LocalWatchdog" de status van de beschik bare schijf op een knoop punt bewaken, zodat de eigenschap "AvailableDisk" op dat knoop punt kan worden gerapporteerd. Dezelfde rapportage functie kan de verbinding met het knoop punt bewaken, zodat een eigenschap ' connectiviteit ' op hetzelfde knoop punt kan worden gerapporteerd. In de Health Store worden deze rapporten behandeld als afzonderlijke status gebeurtenissen voor het opgegeven knoop punt. Samen met de SourceId wordt met de eigenschap een unieke identificatie van de status informatie aangeduid. |
| --status (vereist) | Mogelijke waarden zijn ' Unknown \: ', ' OK ', ' warn ', ' error ', ' Unknown '. |
| --partitie-id [vereist] | De identiteit van de partitie. |
| --replica-id [vereist] | De identiteit van de partitie. |
| --bron-id [vereist] | De bron naam die het client/watchdog/systeem onderdeel identificeert dat de status informatie heeft gegenereerd. |
| --Beschrijving | De beschrijving van de status informatie. <br><br> Hiermee wordt de vrije tekst aangegeven die wordt gebruikt om informatie over het rapport toe te voegen dat wordt gelezen. De maximale teken reeks lengte van de beschrijving is 4096 tekens. Als de gegeven teken reeks langer is, wordt deze automatisch afgekapt. Wanneer de laatste tekens van de beschrijving worden afgekapt, bevatten ze een markering [afgekapt] en de totale teken reeks grootte is 4096 tekens. De aanwezigheid van de markering geeft aan dat gebruikers de afkap ping hebben plaatsgevonden. Houd er rekening mee dat de beschrijving kleiner is dan 4096 tekens uit de oorspronkelijke teken reeks. |
| --Immediate | Een vlag die aangeeft of het rapport direct moet worden verzonden. <br><br> Een status rapport wordt verzonden naar een Service Fabric gateway-toepassing, die wordt doorgestuurd naar de Health Store. Als direct is ingesteld op True, wordt het rapport direct van de HTTP-gateway naar de Health Store verzonden, ongeacht de instellingen van de Fabric-client die door de HTTP-gateway toepassing worden gebruikt. Dit is handig voor kritieke rapporten die zo snel mogelijk moeten worden verzonden. Afhankelijk van de timing en andere voor waarden, kan het verzenden van het rapport nog steeds mislukken, bijvoorbeeld als de HTTP-gateway is gesloten of als het bericht de gateway niet bereikt. Als direct is ingesteld op False, wordt het rapport verzonden op basis van de status client instellingen van de HTTP-gateway. Daarom wordt deze batch gebaseerd op de HealthReportSendInterval-configuratie. Dit is de aanbevolen instelling omdat de Health-client de status rapport berichten kan optimaliseren naar Health Store en dat de status rapporten worden verwerkt. Standaard worden rapporten niet onmiddellijk verzonden. |
| --verwijderen-verlopen | Waarde die aangeeft of het rapport wordt verwijderd uit Health Store wanneer het is verlopen. <br><br> Als deze eigenschap is ingesteld op True, wordt het rapport verwijderd uit de Health Store nadat het is verlopen. Als deze eigenschap is ingesteld op False, wordt het rapport als een fout beschouwd wanneer het is verlopen. De waarde van deze eigenschap is standaard onwaar. Wanneer clients periodiek een rapport rapporteren, moeten ze RemoveWhenExpired False instellen (standaard). Op deze manier heeft de rapporter problemen (bijvoorbeeld deadlock) en kan het rapport niet worden gerapporteerd. de entiteit wordt geëvalueerd als er een fout optreedt wanneer het status rapport verloopt. Hiermee wordt de entiteit gemarkeerd als de fout status. |
| --Volg nummer | Het Volg nummer voor dit status rapport als een numerieke teken reeks. <br><br> Het Volg nummer van het rapport wordt door de Health Store gebruikt voor het detecteren van verouderde rapporten. Als u niets opgeeft, wordt er automatisch een Volg nummer gegenereerd door de Health-client wanneer een rapport wordt toegevoegd. |
| --Service-soort | Het soort service replica (stateless of stateful) waarvoor de status wordt gerapporteerd. Dit zijn de mogelijke waarden \: ' stateless ', ' stateful '.  Standaard \: stateful. |
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

## <a name="sfctl-replica-restart"></a>sfctl replica opnieuw opstarten
Hiermee wordt een service replica van een blijvende service die wordt uitgevoerd op een knoop punt opnieuw gestart.

Hiermee wordt een service replica van een blijvende service die wordt uitgevoerd op een knoop punt opnieuw gestart. Waarschuwing: er worden geen veiligheids controles uitgevoerd wanneer deze API wordt gebruikt. Een onjuist gebruik van deze API kan leiden tot een Beschikbaarheids verlies voor stateful Services.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --node-name [required] | De naam van het knoop punt. |
| --partitie-id [vereist] | De identiteit van de partitie. |
| --replica-id [vereist] | De id van de replica. |
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
