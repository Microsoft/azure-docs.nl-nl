---
title: Azure Service Fabric CLI-sfctl-cluster
description: Meer informatie over sfctl, de Azure Service Fabric-opdracht regel interface. Bevat een lijst met opdrachten voor het beheren van clusters.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 05132e4c7e644e681e2e4cfdedaa2b04a066ebeb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86259970"
---
# <a name="sfctl-cluster"></a>sfctl cluster
Service Fabric clusters selecteren, beheren en uitvoeren.

## <a name="commands"></a>Opdracht

|Opdracht|Beschrijving|
| --- | --- |
| code-versies | Hiermee haalt u een lijst met infrastructuur code versies op die zijn ingericht in een Service Fabric cluster. |
| configuratie-versies | Hiermee haalt u een lijst op met de Fabric-configuratie versies die zijn ingericht in een Service Fabric cluster. |
| status | Hiermee wordt de status van een Service Fabric cluster opgehaald. |
| manifest | Down load het Service Fabric cluster manifest. |
| bewerking-annuleren | Hiermee wordt een door de gebruiker veroorzaakte fout bewerking geannuleerd. |
| bewerking-lijst | Hiermee wordt een lijst met door de gebruiker veroorzaakte fout bewerkingen opgehaald, gefilterd op de opgegeven invoer. |
| aanbieden | De code-of configuratie pakketten van een Service Fabric cluster inrichten. |
| herstellen-systeem | Hiermee geeft u aan het Service Fabric cluster op dat moet worden geprobeerd om de systeem services te herstellen die momenteel zijn vastgelopen in quorum verlies. |
| rapport-status | Hiermee verzendt u een status rapport op het Service Fabric cluster. |
| Selecteer | Hiermee maakt u verbinding met een Service Fabric cluster eindpunt. |
| weer geven-verbinding | Weer geven met welke Service Fabric cluster dit sfctl-exemplaar is verbonden. |
| inrichting verwijderen | De inrichting van de code-of configuratie pakketten van een Service Fabric cluster ongedaan maken. |
| upgrade | Het bijwerken van de code of configuratie versie van een Service Fabric cluster starten. |
| upgrade uitvoeren-hervatten | Zorg ervoor dat de upgrade van het cluster wordt verplaatst naar het volgende upgrade domein. |
| upgrade-terugdraai actie | De upgrade van een Service Fabric cluster terugdraaien. |
| upgrade-status | Hiermee wordt de voortgang van de huidige cluster upgrade opgehaald. |
| upgrade uitvoeren-update | Update de upgrade parameters van een Service Fabric cluster upgrade. |

## <a name="sfctl-cluster-code-versions"></a>sfctl-cluster code-versies
Hiermee haalt u een lijst met infrastructuur code versies op die zijn ingericht in een Service Fabric cluster.

Hiermee wordt een lijst met gegevens opgehaald over infrastructuur code-versies die in het cluster zijn ingericht. De para meter CodeVersion kan worden gebruikt om de uitvoer desgewenst te filteren op alleen die specifieke versie.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --code-versie | De product versie van Service Fabric. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-cluster-config-versions"></a>sfctl-cluster configuratie-versies
Hiermee haalt u een lijst op met de Fabric-configuratie versies die zijn ingericht in een Service Fabric cluster.

Hiermee wordt een lijst met gegevens opgehaald over infrastructuur configuratie versies die zijn ingericht in het cluster. De para meter ConfigVersion kan worden gebruikt om de uitvoer desgewenst te filteren op alleen die specifieke versie.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --config-versie | De configuratie versie van Service Fabric. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-cluster-health"></a>sfctl-cluster status
Hiermee wordt de status van een Service Fabric cluster opgehaald.

Gebruik EventsHealthStateFilter om de verzameling van status gebeurtenissen die op het cluster zijn gerapporteerd, te filteren op basis van de status. Op dezelfde manier kunt u NodesHealthStateFilter en ApplicationsHealthStateFilter gebruiken om de verzameling knoop punten en toepassingen te filteren die zijn geretourneerd op basis van de geaggregeerde status.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --toepassingen-status-filter | Hiermee staat u toe dat de status objecten van de toepassings status worden gefilterd die zijn geretourneerd in het resultaat van de cluster status query op basis van hun status. De mogelijke waarden voor deze para meter zijn integerwaarde die is verkregen door leden of bitsgewijze bewerkingen op leden van HealthStateFilter-inventarisatie. Alleen toepassingen die overeenkomen met het filter worden geretourneerd. Alle toepassingen worden gebruikt om de geaggregeerde status te evalueren. Als u niets opgeeft, worden alle vermeldingen geretourneerd. De status waarden zijn inventarisatie op basis van een vlag, waardoor de waarde kan bestaan uit een combi natie van deze waarden die zijn verkregen met behulp van de operator bitsgewijze of. Als de opgegeven waarde bijvoorbeeld 6 is, wordt de status van toepassingen met HealthState waarde OK (2) en waarschuwing (4) geretourneerd.  <br> -Standaard-standaard waarde. Komt overeen met een wille keurige HealthState. De waarde is nul.  <br> -Geen: filter dat niet overeenkomt met een HealthState-waarde. Wordt gebruikt om geen resultaten te retour neren voor een bepaalde verzameling statussen. De waarde is 1.  <br> -OK-filter dat overeenkomt met de invoer met HealthState waarde OK. De waarde is 2.  <br> -Waarschuwings filter dat overeenkomt met invoer met HealthState-waarde waarschuwing. De waarde is 4.  <br> -Fout filter dat overeenkomt met de invoer met de HealthState-waarde fout. De waarde is 8.  <br> -Alle-filter die overeenkomt met invoer met een wille keurige waarde van HealthState. De waarde is 65535. |
| --gebeurtenissen-status-filter | Hiermee kunt u het verzamelen van HealthEvent-objecten die zijn geretourneerd op basis van de status wordt gefilterd. De mogelijke waarden voor deze para meter zijn gehele getallen van een van de volgende statussen. Alleen gebeurtenissen die overeenkomen met het filter worden geretourneerd. Alle gebeurtenissen worden gebruikt om de geaggregeerde status te evalueren. Als u niets opgeeft, worden alle vermeldingen geretourneerd. De status waarden zijn inventarisatie op basis van een vlag, waardoor de waarde kan bestaan uit een combi natie van deze waarden, verkregen met behulp van de operator bitsgewijze ' of '. Als de opgegeven waarde bijvoorbeeld 6 is, worden alle gebeurtenissen met HealthState waarde OK (2) en waarschuwing (4) geretourneerd.  <br> -Standaard-standaard waarde. Komt overeen met een wille keurige HealthState. De waarde is nul.  <br> -Geen: filter dat niet overeenkomt met een HealthState-waarde. Wordt gebruikt om geen resultaten te retour neren voor een bepaalde verzameling statussen. De waarde is 1.  <br> -OK-filter dat overeenkomt met de invoer met HealthState waarde OK. De waarde is 2.  <br> -Waarschuwings filter dat overeenkomt met invoer met HealthState-waarde waarschuwing. De waarde is 4.  <br> -Fout filter dat overeenkomt met de invoer met de HealthState-waarde fout. De waarde is 8.  <br> -Alle-filter die overeenkomt met invoer met een wille keurige waarde van HealthState. De waarde is 65535. |
| --exclude-status-statistieken | Hiermee wordt aangegeven of de status statistieken moeten worden geretourneerd als onderdeel van het query resultaat. Standaard onwaar. In de statistieken wordt het aantal onderliggende entiteiten in de status OK, waarschuwing en fout weer gegeven. |
| --include-System-Application-Health-Statistics | Hiermee wordt aangegeven of de status statistieken de status statistieken van de Fabric \: /systeemstatus bevatten. Standaard onwaar. Als IncludeSystemApplicationHealthStatistics is ingesteld op True, bevatten de status statistieken de entiteiten die deel uitmaken van de Fabric \: /systeemstatus-toepassing. Anders bevat het query resultaat alleen status statistieken voor gebruikers toepassingen. De status statistieken moeten worden opgenomen in het query resultaat voor deze para meter om toe te passen. |
| --knoop punten-status-filter | Hiermee staat u toe dat de knooppunt status objecten worden gefilterd die zijn geretourneerd in het resultaat van de cluster status query op basis van hun status. De mogelijke waarden voor deze para meter zijn gehele getallen van een van de volgende statussen. Alleen knoop punten die overeenkomen met het filter worden geretourneerd. Alle knoop punten worden gebruikt om de geaggregeerde status te evalueren. Als u niets opgeeft, worden alle vermeldingen geretourneerd. De status waarden zijn inventarisatie op basis van een vlag, waardoor de waarde kan bestaan uit een combi natie van deze waarden die zijn verkregen met behulp van de operator bitsgewijze of. Als de opgegeven waarde bijvoorbeeld 6 is, wordt de status van knoop punten met HealthState waarde OK (2) en waarschuwing (4) geretourneerd.  <br> -Standaard-standaard waarde. Komt overeen met een wille keurige HealthState. De waarde is nul.  <br> -Geen: filter dat niet overeenkomt met een HealthState-waarde. Wordt gebruikt om geen resultaten te retour neren voor een bepaalde verzameling statussen. De waarde is 1.  <br> -OK-filter dat overeenkomt met de invoer met HealthState waarde OK. De waarde is 2.  <br> -Waarschuwings filter dat overeenkomt met invoer met HealthState-waarde waarschuwing. De waarde is 4.  <br> -Fout filter dat overeenkomt met de invoer met de HealthState-waarde fout. De waarde is 8.  <br> -Alle-filter die overeenkomt met invoer met een wille keurige waarde van HealthState. De waarde is 65535. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-cluster-manifest"></a>sfctl-cluster manifest
Down load het Service Fabric cluster manifest.

Down load het Service Fabric cluster manifest. Het cluster manifest bevat eigenschappen van het cluster die verschillende knooppunt typen bevatten op het cluster, de beveiligings configuraties, de fout en de topologieën van het upgrade domein, enzovoort. Deze eigenschappen zijn opgegeven als onderdeel van de ClusterConfig.JSin het bestand tijdens het implementeren van een zelfstandig cluster. De meeste gegevens in het cluster manifest worden echter intern gegenereerd door service Fabric tijdens de implementatie van een cluster in andere implementatie scenario's (bijvoorbeeld bij gebruik van Azure Portal). De inhoud van het cluster manifest is alleen ter informatie en er wordt niet verwacht dat gebruikers afhankelijk zijn van de indeling van de bestands inhoud of de interpretatie ervan.

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

## <a name="sfctl-cluster-operation-cancel"></a>sfctl-cluster bewerking-annuleren
Hiermee wordt een door de gebruiker veroorzaakte fout bewerking geannuleerd.

De volgende Api's starten fout bewerkingen die kunnen worden geannuleerd met behulp van CancelOperation \: StartDataLoss, StartQuorumLoss, StartPartitionRestart, StartNodeTransition. Als Force is ingesteld op ONWAAR, wordt de opgegeven door de gebruiker geinduceerde bewerking op de juiste wijze gestopt en opgeruimd.  Als Force is ingesteld op ' True ', wordt de opdracht afgebroken en wordt een interne status mogelijk achtergelaten.  Het opgeven van Force as True moet zorgvuldig worden gebruikt. Het aanroepen van deze API met geforceerd ingesteld op True is niet toegestaan totdat deze API al is aangeroepen voor dezelfde test opdracht waarbij Force is ingesteld op False, of tenzij de test opdracht al een OperationState van OperationState. RollingBack heeft. Uitleg \: OperationState. RollingBack houdt in dat het systeem de interne systeem status opschoont die wordt veroorzaakt door de opdracht uit te voeren.  Er worden geen gegevens hersteld als de test opdracht gegevens verlies veroorzaakte.  Als u bijvoorbeeld StartDataLoss aanroept, roept het systeem de interne status alleen op voor het uitvoeren van de opdracht. De gegevens van de doel partitie worden niet hersteld als de opdracht ver genoeg is uitgevoerd om gegevens verlies te veroorzaken. Belang rijke opmerking \:  als deze API wordt aangeroepen met geforceerd = = True, wordt er mogelijk een interne status achter gelaten.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --bewerking-id [vereist] | Een GUID die een aanroep van deze API identificeert.  Dit wordt door gegeven aan de bijbehorende GetProgress-API. |
| --forceren | Hiermee wordt aangegeven of de interne systeem status die is gewijzigd moet worden teruggedraaid en opgeruimd door de door de gebruiker veroorzaakte bewerking uit te voeren. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-cluster-operation-list"></a>sfctl-cluster bewerking-lijst
Hiermee wordt een lijst met door de gebruiker veroorzaakte fout bewerkingen opgehaald, gefilterd op de opgegeven invoer.

Hiermee wordt de lijst met door de gebruiker veroorzaakte fout bewerkingen opgehaald, gefilterd op de opgegeven invoer.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --status-filter | Wordt gebruikt om te filteren op OperationState voor door de gebruiker veroorzaakte bewerkingen. -65535-alles selecteren-1: Selecteer actief-2-Select RollingBack-8-selected-16-Selecteer voltooid-32-Selecteer geannuleerd-64-ForceCancelled selecteren.  Standaard \: 65535. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |
| --type-filter | Wordt gebruikt om te filteren op OperationType voor door de gebruiker veroorzaakte bewerkingen. -65535-alles selecteren-1: Selecteer PartitionDataLoss. -2: Selecteer PartitionQuorumLoss. -4-Selecteer PartitionRestart. -8-Selecteer NodeTransition.  Standaard \: 65535. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-cluster-provision"></a>sfctl-cluster inrichten
De code-of configuratie pakketten van een Service Fabric cluster inrichten.

Valideer en richt de code-of configuratie pakketten van een Service Fabric cluster in.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --cluster-manifest-bestand-pad | Het pad naar het manifest bestand van het cluster. |
| --code-bestandspad | Het bestandspad van het cluster code pakket. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-cluster-recover-system"></a>sfctl-cluster herstellen-systeem
Hiermee geeft u aan het Service Fabric cluster op dat moet worden geprobeerd om de systeem services te herstellen die momenteel zijn vastgelopen in quorum verlies.

Hiermee geeft u aan het Service Fabric cluster op dat moet worden geprobeerd om de systeem services te herstellen die momenteel zijn vastgelopen in quorum verlies. Deze bewerking mag alleen worden uitgevoerd als bekend is dat de replica's die niet beschikbaar zijn, niet kunnen worden hersteld. Een onjuist gebruik van deze API kan leiden tot mogelijk gegevens verlies.

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

## <a name="sfctl-cluster-report-health"></a>sfctl-cluster rapport-status
Hiermee verzendt u een status rapport op het Service Fabric cluster.

Hiermee verzendt u een status rapport op een Service Fabric cluster. Het rapport moet de informatie bevatten over de bron van het status rapport en de eigenschap waarvoor deze is gerapporteerd. Het rapport wordt verzonden naar een Service Fabric gateway-knoop punt dat wordt doorgestuurd naar de Health Store. Het rapport kan worden geaccepteerd door de gateway, maar door de Health Store na een extra validatie afgewezen. Het Health Store kan bijvoorbeeld het rapport afwijzen vanwege een ongeldige para meter, zoals een verouderd volgorde nummer. Als u wilt zien of het rapport is toegepast in de Health Store, voert u GetClusterHealth uit en controleert u of het rapport wordt weer gegeven in de sectie HealthEvents.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
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

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-cluster-select"></a>sfctl-cluster selecteren
Hiermee maakt u verbinding met een Service Fabric cluster eindpunt.

Als u verbinding maakt met een beveiligd cluster, geeft u een absoluut pad op naar een certificaat (. CRT) en een sleutel bestand (. key) of één bestand met beide (. pem). Geef beide niet op. Als u echter verbinding maakt met een beveiligd cluster, geeft u ook een absoluut pad op naar een CA-bundel bestand of directory van vertrouwde CA-certificaten.  Er is geen verbinding met een cluster zonder eerst deze opdracht uit te voeren, inclusief een verbinding met localhost. Er is echter geen expliciet eind punt vereist om verbinding te maken met een lokaal cluster.  Als u een zelfondertekend certificaat gebruikt of een ander certificaat dat niet is ondertekend door een bekende certificerings instantie, geeft u de para meter--ca door om te controleren of de validatie is geslaagd. Als u zich niet in een productie cluster bevindt, gebruikt u de optie--no-verify om validatie aan de client zijde over te slaan (handig voor zelf-ondertekende of niet-bekende CA-ondertekening). Hoewel dit mogelijk is, wordt dit niet aanbevolen voor productie clusters. Een certificaat verificatie fout kan anders optreden.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --Aad | Gebruik Azure Active Directory voor authenticatie. |
| --ca | Absolute pad naar CA-certificaat Directory om te behandelen als geldig of CA-bundel bestand. Als u een directory met CA-certificaten gebruikt, `c_rehash <directory>` moet de openssl eerst worden uitgevoerd om de certificaat-hashes te berekenen en de juiste symbolische koppelingen te maken. Dit wordt gebruikt om te controleren of het certificaat dat door het cluster wordt geretourneerd, geldig is. |
| --CERT | Absoluut pad naar een client certificaat bestand. |
| --eind punt | URL van cluster eindpunt, inclusief poort en HTTP-of HTTPS-voor voegsel. Normaal gesp roken ziet het eind punt er ongeveer als HTTPS \: //<uw-url>\: 19080. Als er geen eind punt wordt opgegeven, wordt het standaard HTTP- \: //localhost \: 19080.  Standaard \: -http- \: //localhost \: 19080. |
| --sleutel | Het absolute pad naar het sleutel bestand van het client certificaat. |
| --geen verificatie | Verificatie uitschakelen voor certificaten bij gebruik van HTTPS, Let op: \: Dit is een onveilige optie en mag niet worden gebruikt voor productie omgevingen. |
| --PEM | Het absolute pad naar het client certificaat als een. pem-bestand. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-cluster-show-connection"></a>sfctl cluster show-Connection
Weer geven met welke Service Fabric cluster dit sfctl-exemplaar is verbonden.

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-cluster-unprovision"></a>toewijzing van sfctl-cluster ongedaan maken
De inrichting van de code-of configuratie pakketten van een Service Fabric cluster ongedaan maken.

Het wordt ondersteund om de inrichting van code en configuratie afzonderlijk ongedaan te maken.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --code-versie | De versie van het cluster code pakket. |
| --config-versie | De versie van het cluster manifest. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-cluster-upgrade"></a>upgrade van sfctl-cluster
Het bijwerken van de code of configuratie versie van een Service Fabric cluster starten.

Valideer de opgegeven upgrade parameters en begin met het upgraden van de code of configuratie versie van een Service Fabric cluster als de para meters geldig zijn.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --app-status-toewijzing | JSON-gecodeerde woorden lijst met combi Naties van toepassings naam en maximum percentage is beschadigd voordat er een fout optreedt. |
| --app-type-status toewijzing | JSON-gecodeerde woorden lijst met paren van de naam van het toepassings type en het maximum percentage is slecht voordat er een fout optreedt. |
| --code-versie | De versie van de cluster code. |
| --config-versie | De versie van de cluster configuratie. |
| --Delta-status-evaluatie | Hiermee schakelt u de Delta status evaluatie in in plaats van de absolute status evaluatie na voltooiing van elk upgrade domein. |
| --Delta-onjuiste knoop punten | Het Maxi maal toegestane percentage van de integriteit van knoop punten is toegestaan tijdens cluster upgrades.  Standaard \: 10. <br><br> De Delta wordt gemeten tussen de status van de knoop punten aan het begin van de upgrade en de status van de knoop punten op het moment van de status evaluatie. De controle wordt uitgevoerd nadat de upgrade van het upgrade domein is voltooid om te controleren of de algemene status van het cluster binnen de toegestane limieten valt. |
| --fout-actie | Mogelijke waarden zijn \: ' ongeldig ', ' rollback ', ' Manual '. |
| --Force-restart | Processen worden geforceerd opnieuw gestart tijdens de upgrade, zelfs wanneer de code versie niet is gewijzigd. <br><br> Bij de upgrade worden alleen configuratie of gegevens gewijzigd. |
| --status-controle-opnieuw | De tijds duur tussen pogingen om status controles uit te voeren als de toepassing of het cluster niet in orde is. |
| --status-controle stabiel | De hoeveelheid tijd die de toepassing of het cluster in orde moet blijven voordat de upgrade wordt voortgezet naar het volgende upgrade domein. <br><br> Het wordt eerst geïnterpreteerd als een teken reeks die een ISO 8601-duur vertegenwoordigt. Als dat mislukt, wordt dit geïnterpreteerd als een getal dat het totale aantal milliseconden aangeeft. |
| --status-check-wait | De tijds duur die moet worden gewacht na het volt ooien van een upgrade domein voordat het proces status controles wordt gestart. |
| --replica-set-check-timeout | De maximale tijds duur voor het blok keren van de verwerking van een upgrade domein en het voor komen van Beschik baarheid wanneer er onverwachte problemen zijn. <br><br> Wanneer deze time-out is verlopen, wordt de verwerking van het upgrade domein voortgezet, ongeacht de problemen met het Beschikbaarheids verlies. De time-out wordt opnieuw ingesteld aan het begin van elk upgrade domein. Geldige waarden zijn 0 tot en met 42949672925. |
| --rolling upgrade-modus | Mogelijke waarden zijn \: ' ongeldig ', ' UnmonitoredAuto ', ' UnmonitoredManual ', ' bewaakt '.  Standaard \: UnmonitoredAuto. |
| --time-out-t | Standaard \: 60. |
| --toepassingen met een slechte status | Het Maxi maal toegestane percentage van toepassingen met een slechte status voordat een fout wordt gemeld. <br><br> Als u bijvoorbeeld wilt toestaan dat 10% van de toepassingen een slechte status heeft, is deze waarde 10. Het percentage vertegenwoordigt het Maxi maal toegestane percentage van toepassingen die een slechte status kunnen hebben voordat het cluster als fout wordt beschouwd. Als het percentage wordt geëerbiedigd, maar er ten minste één slechte toepassing is, wordt de status als een waarschuwing geëvalueerd. Dit wordt berekend door het aantal toepassingen met een slechte status te delen ten opzichte van het totale aantal toepassings exemplaren in het cluster, met uitzonde ring van toepassings typen die zijn opgenomen in de ApplicationTypeHealthPolicyMap. De berekening wordt afgerond om één fout te verdragen bij een klein aantal toepassingen. |
| --onjuiste knoop punten | Het Maxi maal toegestane percentage van beschadigde knoop punten voordat een fout wordt gemeld. <br><br> Als u bijvoorbeeld wilt toestaan dat 10% van de knoop punten een slechte status heeft, is deze waarde 10. Het percentage vertegenwoordigt het Maxi maal toegestane percentage van knoop punten waarvan de status niet in orde kan zijn voordat het cluster als fout wordt beschouwd. Als het percentage wordt geëerbiedigd maar er ten minste één slecht knoop punt is, wordt de status als waarschuwing geëvalueerd. Het percentage wordt berekend door het aantal knoop punten met een slechte status te delen ten opzichte van het totale aantal knoop punten in het cluster. De berekening wordt afgerond om één fout te verdragen op een klein aantal knoop punten. In grote clusters zijn bepaalde knoop punten altijd actief of uitgaand voor reparaties, dus dit percentage moet zo worden geconfigureerd dat het wordt toegestaan. |
| --upgrade-Domain-Delta-slechte statussen: knoop punten | Het Maxi maal toegestane percentage van de status van upgrade domein knooppunten is toegestaan tijdens cluster upgrades.  Standaard \: 15. <br><br> De Delta wordt gemeten tussen de status van de knoop punten van het upgrade domein aan het begin van de upgrade en de status van de knoop punten van het upgrade domein op het moment van de status evaluatie. De controle wordt uitgevoerd nadat elke upgrade voor het bijwerken van het domein is voltooid voor alle voltooide upgrade domeinen om te controleren of de status van de upgrade domeinen binnen de toegestane limieten valt. |
| --upgrade-time-out van domein | De hoeveelheid tijd die elk upgrade domein moet volt ooien voordat FailureAction wordt uitgevoerd. <br><br> Het wordt eerst geïnterpreteerd als een teken reeks die een ISO 8601-duur vertegenwoordigt. Als dat mislukt, wordt dit geïnterpreteerd als een getal dat het totale aantal milliseconden aangeeft. |
| --upgrade-time-out | De hoeveelheid tijd die de algehele upgrade moet volt ooien voordat FailureAction wordt uitgevoerd. <br><br> Het wordt eerst geïnterpreteerd als een teken reeks die een ISO 8601-duur vertegenwoordigt. Als dat mislukt, wordt dit geïnterpreteerd als een getal dat het totale aantal milliseconden aangeeft. |
| --waarschuwing-als-fout | Hiermee wordt aangegeven of waarschuwingen worden behandeld met dezelfde ernst als bij fouten. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-cluster-upgrade-resume"></a>upgrade van sfctl-cluster-hervatten
Zorg ervoor dat de upgrade van het cluster wordt verplaatst naar het volgende upgrade domein.

Zorg ervoor dat de cluster code of configuratie-upgrade wordt verplaatst naar het volgende upgrade domein, indien van toepassing.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --upgrade-domein [vereist] | Het volgende upgrade domein voor deze upgrade van het cluster. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-cluster-upgrade-rollback"></a>upgrade van sfctl-cluster-terugdraai actie
De upgrade van een Service Fabric cluster terugdraaien.

Terugdraaien van de code of configuratie-upgrade van een Service Fabric cluster.

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

## <a name="sfctl-cluster-upgrade-status"></a>sfctl-cluster upgrade-status
Hiermee wordt de voortgang van de huidige cluster upgrade opgehaald.

Hiermee wordt de huidige voortgang van de upgrade van het cluster opgehaald. Als er momenteel geen upgrade wordt uitgevoerd, haalt u de laatste status van de vorige cluster upgrade op.

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

## <a name="sfctl-cluster-upgrade-update"></a>upgrade van sfctl-cluster-update
Update de upgrade parameters van een Service Fabric cluster upgrade.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --app-status-toewijzing | JSON-gecodeerde woorden lijst met combi Naties van toepassings naam en maximum percentage is beschadigd voordat er een fout optreedt. |
| --app-type-status toewijzing | JSON-gecodeerde woorden lijst met paren van de naam van het toepassings type en het maximum percentage is slecht voordat er een fout optreedt. |
| --Delta-status-evaluatie | Hiermee schakelt u de Delta status evaluatie in in plaats van de absolute status evaluatie na voltooiing van elk upgrade domein. |
| --Delta-onjuiste knoop punten | Het Maxi maal toegestane percentage van de integriteit van knoop punten is toegestaan tijdens cluster upgrades.  Standaard \: 10. <br><br> De Delta wordt gemeten tussen de status van de knoop punten aan het begin van de upgrade en de status van de knoop punten op het moment van de status evaluatie. De controle wordt uitgevoerd nadat de upgrade van het upgrade domein is voltooid om te controleren of de algemene status van het cluster binnen de toegestane limieten valt. |
| --fout-actie | Mogelijke waarden zijn \: ' ongeldig ', ' rollback ', ' Manual '. |
| --Force-restart | Processen worden geforceerd opnieuw gestart tijdens de upgrade, zelfs wanneer de code versie niet is gewijzigd. <br><br> Bij de upgrade worden alleen configuratie of gegevens gewijzigd. |
| --status-controle-opnieuw | De tijds duur tussen pogingen om status controles uit te voeren als de toepassing of het cluster niet in orde is. |
| --status-controle stabiel | De hoeveelheid tijd die de toepassing of het cluster in orde moet blijven voordat de upgrade wordt voortgezet naar het volgende upgrade domein. <br><br> Het wordt eerst geïnterpreteerd als een teken reeks die een ISO 8601-duur vertegenwoordigt. Als dat mislukt, wordt dit geïnterpreteerd als een getal dat het totale aantal milliseconden aangeeft. |
| --status-check-wait | De tijds duur die moet worden gewacht na het volt ooien van een upgrade domein voordat het proces status controles wordt gestart. |
| --replica-set-check-timeout | De maximale tijds duur voor het blok keren van de verwerking van een upgrade domein en het voor komen van Beschik baarheid wanneer er onverwachte problemen zijn. <br><br> Wanneer deze time-out is verlopen, wordt de verwerking van het upgrade domein voortgezet, ongeacht de problemen met het Beschikbaarheids verlies. De time-out wordt opnieuw ingesteld aan het begin van elk upgrade domein. Geldige waarden zijn 0 tot en met 42949672925. |
| --rolling upgrade-modus | Mogelijke waarden zijn \: ' ongeldig ', ' UnmonitoredAuto ', ' UnmonitoredManual ', ' bewaakt '.  Standaard \: UnmonitoredAuto. |
| --time-out-t | Standaard \: 60. |
| --toepassingen met een slechte status | Het Maxi maal toegestane percentage van toepassingen met een slechte status voordat een fout wordt gemeld. <br><br> Als u bijvoorbeeld wilt toestaan dat 10% van de toepassingen een slechte status heeft, is deze waarde 10. Het percentage vertegenwoordigt het Maxi maal toegestane percentage van toepassingen die een slechte status kunnen hebben voordat het cluster als fout wordt beschouwd. Als het percentage wordt geëerbiedigd, maar er ten minste één slechte toepassing is, wordt de status als een waarschuwing geëvalueerd. Dit wordt berekend door het aantal toepassingen met een slechte status te delen ten opzichte van het totale aantal toepassings exemplaren in het cluster, met uitzonde ring van toepassings typen die zijn opgenomen in de ApplicationTypeHealthPolicyMap. De berekening wordt afgerond om één fout te verdragen bij een klein aantal toepassingen. |
| --onjuiste knoop punten | Het Maxi maal toegestane percentage van beschadigde knoop punten voordat een fout wordt gemeld. <br><br> Als u bijvoorbeeld wilt toestaan dat 10% van de knoop punten een slechte status heeft, is deze waarde 10. Het percentage vertegenwoordigt het Maxi maal toegestane percentage van knoop punten waarvan de status niet in orde kan zijn voordat het cluster als fout wordt beschouwd. Als het percentage wordt geëerbiedigd maar er ten minste één slecht knoop punt is, wordt de status als waarschuwing geëvalueerd. Het percentage wordt berekend door het aantal knoop punten met een slechte status te delen ten opzichte van het totale aantal knoop punten in het cluster. De berekening wordt afgerond om één fout te verdragen op een klein aantal knoop punten. In grote clusters zijn bepaalde knoop punten altijd actief of uitgaand voor reparaties, dus dit percentage moet zo worden geconfigureerd dat het wordt toegestaan. |
| --upgrade-Domain-Delta-slechte statussen: knoop punten | Het Maxi maal toegestane percentage van de status van upgrade domein knooppunten is toegestaan tijdens cluster upgrades.  Standaard \: 15. <br><br> De Delta wordt gemeten tussen de status van de knoop punten van het upgrade domein aan het begin van de upgrade en de status van de knoop punten van het upgrade domein op het moment van de status evaluatie. De controle wordt uitgevoerd nadat elke upgrade voor het bijwerken van het domein is voltooid voor alle voltooide upgrade domeinen om te controleren of de status van de upgrade domeinen binnen de toegestane limieten valt. |
| --upgrade-time-out van domein | De hoeveelheid tijd die elk upgrade domein moet volt ooien voordat FailureAction wordt uitgevoerd. <br><br> Het wordt eerst geïnterpreteerd als een teken reeks die een ISO 8601-duur vertegenwoordigt. Als dat mislukt, wordt dit geïnterpreteerd als een getal dat het totale aantal milliseconden aangeeft. |
| --upgrade-soort | Mogelijke waarden zijn \: ' ongeldig ', ' Rolling ', ' Rolling_ForceRestart '.  Standaard \: rollen. |
| --upgrade-time-out | De hoeveelheid tijd die de algehele upgrade moet volt ooien voordat FailureAction wordt uitgevoerd. <br><br> Het wordt eerst geïnterpreteerd als een teken reeks die een ISO 8601-duur vertegenwoordigt. Als dat mislukt, wordt dit geïnterpreteerd als een getal dat het totale aantal milliseconden aangeeft. |
| --waarschuwing-als-fout | Hiermee wordt aangegeven of waarschuwingen worden behandeld met dezelfde ernst als bij fouten. |

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
