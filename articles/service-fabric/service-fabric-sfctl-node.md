---
title: Azure Service Fabric CLI-sfctl-knoop punt
description: Meer informatie over sfctl, de Azure Service Fabric-opdracht regel interface. Bevat een lijst met opdrachten voor het beheren van cluster knooppunten.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f2cf1011db37892f71bdd06f409cec1a76463507
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86257148"
---
# <a name="sfctl-node"></a>sfctl node
De knoop punten beheren die een cluster vormen.

## <a name="commands"></a>Opdracht

|Opdracht|Beschrijving|
| --- | --- |
| add-Configuration-para meter-onderdrukkingen | Hiermee wordt de lijst met overschrijvingen van de configuratie toegevoegd aan het opgegeven knoop punt. |
| uitschakelen | Deactiveer een Service Fabric cluster knooppunt met de opgegeven deactiveren-intentie. |
| inschakelen | Activeer een Service Fabric cluster knooppunt dat momenteel is gedeactiveerd. |
| ophalen-configuratie-onderdrukkingen | Hiermee haalt u de lijst met configuratie-onderdrukkingen op het opgegeven knoop punt op. |
| status | Hiermee wordt de status van een Service Fabric knoop punt opgehaald. |
| Info | Hiermee wordt de informatie opgehaald over een specifiek knoop punt in het Service Fabric cluster. |
| list | Hiermee haalt u de lijst met knoop punten in het Service Fabric-cluster op. |
| laden | Hiermee worden de gegevens van een Service Fabric knoop punt geladen. |
| verwijderen-configuratie-onderdrukkingen | Hiermee verwijdert u de configuratie onderdrukkingen op het opgegeven knoop punt. |
| Verwijder status | Hiermee wordt Service Fabric dat de persistente status op een knoop punt permanent is verwijderd of verloren is gegaan. |
| rapport-status | Hiermee verzendt u een status rapport op het knoop punt Service Fabric. |
| restart | Hiermee wordt een Service Fabric cluster knooppunt opnieuw opgestart. |
| geschakeld | Hiermee wordt een cluster knooppunt gestart of gestopt. |
| overgang-status | Hiermee wordt de voortgang opgehaald van een bewerking die is gestart met StartNodeTransition. |

## <a name="sfctl-node-add-configuration-parameter-overrides"></a>sfctl-knooppunt toevoegen-configuratie-para meter-onderdrukkingen
Hiermee wordt de lijst met overschrijvingen van de configuratie toegevoegd aan het opgegeven knoop punt.

Met deze API kunnen alle bestaande configuratie onderdrukkingen op het opgegeven knoop punt worden toegevoegd.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --config-para meter-override-lijst [vereist] | Beschrijving voor het toevoegen van een lijst met configuratie-onderdrukkingen. |
| --node-name [required] | De naam van het knoop punt. |
| --forceren | Het toevoegen van configuratie-onderdrukkingen op opgegeven knoop punten forceren. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-node-disable"></a>sfctl-knoop punt uitschakelen
Deactiveer een Service Fabric cluster knooppunt met de opgegeven deactiveren-intentie.

Deactiveer een Service Fabric cluster knooppunt met de opgegeven deactiveren-intentie. Als de deactivering is voltooid, kan het deactiveren van de inschakeling worden verhoogd, maar niet worden verkleind (een knoop punt dat is gedeactiveerd met de pauze intentie kan echter nog verder worden gedeactiveerd met opnieuw opstarten, maar niet op de andere manier. Knoop punten kunnen opnieuw worden geactiveerd met behulp van de bewerking een knoop punt activeren op elk gewenst moment nadat ze zijn gedeactiveerd. Als de deactivering is niet voltooid, wordt de deactivering geannuleerd. Een knoop punt dat omlaag gaat en een back-up maakt terwijl het deactiveren is uitgeschakeld, moet nog steeds opnieuw worden geactiveerd voordat de services op dat knoop punt worden geplaatst.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --node-name [required] | De naam van het knoop punt. |
| --deactiveren-intentie | Beschrijft het doel of de reden voor het deactiveren van het knoop punt. De mogelijke waarden zijn als volgt. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-node-enable"></a>sfctl-knoop punt inschakelen
Activeer een Service Fabric cluster knooppunt dat momenteel is gedeactiveerd.

Hiermee activeert u een Service Fabric cluster knooppunt dat momenteel is gedeactiveerd. Wanneer het knoop punt eenmaal is geactiveerd, wordt het opnieuw een levensvatbaar doel om nieuwe replica's te plaatsen en worden gedeactiveerde replica's op het knoop punt opnieuw geactiveerd.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
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

## <a name="sfctl-node-get-configuration-overrides"></a>Get-configuratie van sfctl-knoop punt-onderdrukkingen
Hiermee haalt u de lijst met configuratie-onderdrukkingen op het opgegeven knoop punt op.

Met deze API kunnen alle bestaande configuratie onderdrukkingen op het opgegeven knoop punt worden opgehaald.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
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

## <a name="sfctl-node-health"></a>status van sfctl-knoop punt
Hiermee wordt de status van een Service Fabric knoop punt opgehaald.

Hiermee wordt de status van een Service Fabric knoop punt opgehaald. Gebruik EventsHealthStateFilter om de verzameling van status gebeurtenissen die op het knoop punt zijn gerapporteerd, te filteren op basis van de status. Als het knoop punt dat u opgeeft met de naam niet bestaat in de Health Store, wordt er een fout geretourneerd.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --node-name [required] | De naam van het knoop punt. |
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

## <a name="sfctl-node-info"></a>sfctl-knooppunt gegevens
Hiermee wordt de informatie opgehaald over een specifiek knoop punt in het Service Fabric cluster.

Het antwoord bevat de naam, status, ID, status, uptime en andere details over het knoop punt.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
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

## <a name="sfctl-node-list"></a>lijst met sfctl-knoop punten
Hiermee haalt u de lijst met knoop punten in het Service Fabric-cluster op.

Het antwoord bevat de naam, status, ID, status, uptime en andere details over de knoop punten.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --vervolg token | De vervolg token parameter wordt gebruikt om de volgende set resultaten op te halen. Een vervolg token met een niet-lege waarde wordt opgenomen in het antwoord van de API wanneer de resultaten van het systeem niet in één antwoord passen. Wanneer deze waarde wordt door gegeven aan de volgende API-aanroep, retourneert de API de volgende set resultaten. Als er geen verdere resultaten zijn, bevat het vervolg token geen waarde. De waarde van deze para meter mag geen URL-code ring zijn. |
| --Max-resultaten | Het maximum aantal resultaten dat moet worden geretourneerd als onderdeel van de query's in de pagina. Met deze para meter wordt de bovengrens gedefinieerd voor het aantal geretourneerde resultaten. De geretourneerde resultaten kunnen kleiner zijn dan de opgegeven maximum resultaten als ze niet in het bericht passen conform de maximale grootte van de berichten die in de configuratie is gedefinieerd. Als deze para meter nul is of niet is opgegeven, bevat de opgevraagde query zoveel mogelijk resultaten die in het retour bericht passen. |
| --knoop punt-status-filter | Hiermee kunt u de knoop punten filteren op basis van de NodeStatus. Alleen de knoop punten die overeenkomen met de opgegeven filter waarde worden geretourneerd. De filter waarde kan een van de volgende waarden hebben.  Standaard instelling \: . |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-node-load"></a>laden van sfctl-knoop punt
Hiermee worden de gegevens van een Service Fabric knoop punt geladen.

Hiermee haalt u de gegevens van de belasting van een Service Fabric knoop punt op voor alle metrische gegevens waarvoor de belasting of capaciteit is gedefinieerd.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
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

## <a name="sfctl-node-remove-configuration-overrides"></a>sfctl-knoop punt Remove-configuratie-onderdrukkingen
Hiermee verwijdert u de configuratie onderdrukkingen op het opgegeven knoop punt.

Met deze API kunnen alle bestaande configuratie onderdrukkingen op het opgegeven knoop punt worden verwijderd.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
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

## <a name="sfctl-node-remove-state"></a>status van sfctl-knoop punt verwijderen
Hiermee wordt Service Fabric dat de persistente status op een knoop punt permanent is verwijderd of verloren is gegaan.

Dit betekent dat het niet mogelijk is om de persistente status van het knoop punt te herstellen. Dit gebeurt doorgaans als een harde schijf schoon is gewist of als een harde schijf vastloopt. Het knoop punt moet actief zijn om deze bewerking te kunnen volt ooien. Met deze bewerking wordt Service Fabric weet dat de replica's op dat knoop punt niet meer bestaan en dat Service Fabric moet stoppen met wachten op de replica's om een back-up te maken. Voer deze cmdlet niet uit als de status op het knoop punt niet is verwijderd en er een back-up van het knoop punt kan worden gemaakt met de status intact. Ga vanaf Service Fabric 6,5 om deze API voor Seed-knoop punten te gebruiken, wijzig de Seed-knoop punten in normale (niet-Seed) knoop punten en roep deze API vervolgens aan om de knooppunt status te verwijderen. Als het cluster wordt uitgevoerd op Azure, probeert Service Fabric het Seed-knoop punt automatisch te wijzigen in een niet-Seed-knoop punt. Om dit te doen, moet u ervoor zorgen dat het aantal niet-Seed-knoop punten in het primaire knooppunt type niet kleiner is dan het aantal knoop punten in het lagere knoop punt. Indien nodig voegt u meer knoop punten toe aan het primaire knooppunt type om dit te doen. Als er voor een zelfstandig cluster niet wordt verwacht dat er een back-up wordt gemaakt van de status intact, verwijdert u het knoop punt uit het cluster en raadpleegt u HTTPS \: //docs.Microsoft.com/en-US/Azure/service-Fabric/service-Fabric-cluster-Windows-Server-Add-Remove-nodes.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
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

## <a name="sfctl-node-report-health"></a>sfctl-knooppunt rapport-status
Hiermee verzendt u een status rapport op het knoop punt Service Fabric.

Hiermee wordt de status van het opgegeven Service Fabric knoop punt gerapporteerd. Het rapport moet de informatie bevatten over de bron van het status rapport en de eigenschap waarvoor deze is gerapporteerd. Het rapport wordt verzonden naar een Service Fabric gateway-knoop punt dat wordt doorgestuurd naar de Health Store. Het rapport kan worden geaccepteerd door de gateway, maar door de Health Store na een extra validatie afgewezen. Het Health Store kan bijvoorbeeld het rapport afwijzen vanwege een ongeldige para meter, zoals een verouderd volgorde nummer. Als u wilt zien of het rapport is toegepast in de Health Store, controleert u of het rapport wordt weer gegeven in de sectie HealthEvents.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --status-Property [required] | De eigenschap van de status informatie. <br><br> Een entiteit kan status rapporten voor verschillende eigenschappen hebben. De eigenschap is een teken reeks en geen vaste inventarisatie om de flexibiliteit van de rapporter in te stellen voor het categoriseren van de status voorwaarde waarmee het rapport wordt geactiveerd. Zo kan een rapporter met SourceId "LocalWatchdog" de status van de beschik bare schijf op een knoop punt bewaken, zodat de eigenschap "AvailableDisk" op dat knoop punt kan worden gerapporteerd. Dezelfde rapportage functie kan de verbinding met het knoop punt bewaken, zodat een eigenschap ' connectiviteit ' op hetzelfde knoop punt kan worden gerapporteerd. In de Health Store worden deze rapporten behandeld als afzonderlijke status gebeurtenissen voor het opgegeven knoop punt. Samen met de SourceId wordt met de eigenschap een unieke identificatie van de status informatie aangeduid. |
| --status (vereist) | Mogelijke waarden zijn ' Unknown \: ', ' OK ', ' warn ', ' error ', ' Unknown '. |
| --node-name [required] | De naam van het knoop punt. |
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

## <a name="sfctl-node-restart"></a>sfctl-knoop punt opnieuw opstarten
Hiermee wordt een Service Fabric cluster knooppunt opnieuw opgestart.

Hiermee wordt een Service Fabric cluster knooppunt dat al is gestart, opnieuw gestart.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --node-name [required] | De naam van het knoop punt. |
| --Create-Fabric-dump | Geef waar op om een dump van het Fabric-knooppunt proces te maken. Deze is hoofdlettergevoelig.  De standaard waarde is \: False. |
| --knoop punt-exemplaar-id | De exemplaar-ID van het doel knooppunt. Als de exemplaar-ID is opgegeven, wordt het knoop punt alleen opnieuw opgestart als het overeenkomt met het huidige exemplaar van het knoop punt. De standaard waarde ' 0 ' komt overeen met een exemplaar-ID. De exemplaar-ID kan worden verkregen met behulp van de query node ophalen.  Standaard waarde is \: 0. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-node-transition"></a>overgang van sfctl-knoop punt
Hiermee wordt een cluster knooppunt gestart of gestopt.

Hiermee wordt een cluster knooppunt gestart of gestopt.  Een cluster knooppunt is een proces, niet de instantie van het besturings systeem zelf.  Als u een knoop punt wilt starten, geeft u ' Start ' door voor de para meter NodeTransitionType. Als u een knoop punt wilt stoppen, geeft u ' Stop ' door voor de para meter NodeTransitionType. Met deze API wordt de bewerking gestart: wanneer de API het knoop punt retourneert, is de overgang mogelijk nog niet voltooid. Roep GetNodeTransitionProgress aan met dezelfde OperationId om de voortgang van de bewerking op te halen.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --knoop punt-exemplaar-id [vereist] | De exemplaar-ID van het knoop punt van het doel knooppunt. Dit kan worden bepaald met behulp van de GetNodeInfo-API. |
| --node-name [required] | De naam van het knoop punt. |
| --knoop punt-overgang-type [vereist] | Hiermee wordt het type overgang aangegeven dat moet worden uitgevoerd.  NodeTransitionType. Start een gestopt knoop punt wordt gestart. Met NodeTransitionType. stop wordt een knoop punt gestopt dat actief is. |
| --bewerking-id [vereist] | Een GUID die een aanroep van deze API identificeert.  Dit wordt door gegeven aan de bijbehorende GetProgress-API. |
| --Stop-duur-in-seconden [vereist] | De duur in seconden om te voor komen dat het knoop punt wordt gestopt.  De minimum waarde is 600, het maximum is 14400.  Nadat deze tijd is verstreken, wordt er automatisch een back-up gemaakt van het knoop punt. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-node-transition-status"></a>overgang van sfctl-knoop punt-status
Hiermee wordt de voortgang opgehaald van een bewerking die is gestart met StartNodeTransition.

Hiermee wordt de voortgang opgehaald van een bewerking die is gestart met StartNodeTransition met behulp van de meegeleverde OperationId.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --node-name [required] | De naam van het knoop punt. |
| --bewerking-id [vereist] | Een GUID die een aanroep van deze API identificeert.  Dit wordt door gegeven aan de bijbehorende GetProgress-API. |
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
