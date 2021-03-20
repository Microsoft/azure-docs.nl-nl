---
title: Azure Service Fabric CLI-sfctl chaos
description: Meer informatie over sfctl, de Azure Service Fabric-opdracht regel interface. Bevat een lijst met opdrachten voor het beheren van chaos.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f59eb3296c27e64eb6a4644b2f455e3704381f49
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86260840"
---
# <a name="sfctl-chaos"></a>sfctl chaos
Starten, stoppen en rapporteren op de chaos-test service.

## <a name="subgroups"></a>Subgroepen
|Subgroep|Beschrijving|
| --- | --- |
| [planning](service-fabric-sfctl-chaos-schedule.md) | De chaos-planning ophalen en instellen. |
## <a name="commands"></a>Opdracht

|Opdracht|Beschrijving|
| --- | --- |
| events | Hiermee wordt het volgende segment van de chaos-gebeurtenissen opgehaald op basis van het vervolg token of het tijds bereik. |
| ophalen | De status van chaos ophalen. |
| starten | Chaos wordt gestart in het cluster. |
| stoppen | Stopt chaos als deze wordt uitgevoerd in het cluster en de chaos-planning in een status gestopt heeft gezet. |

## <a name="sfctl-chaos-events"></a>sfctl chaos-gebeurtenissen
Hiermee wordt het volgende segment van de chaos-gebeurtenissen opgehaald op basis van het vervolg token of het tijds bereik.

Als u het volgende segment van de chaos-gebeurtenissen wilt ophalen, kunt u de ContinuationToken opgeven. Als u het begin van een nieuw segment van chaos-gebeurtenissen wilt ontvangen, kunt u het tijds bereik opgeven via StartTimeUtc en EndTimeUtc. U kunt niet zowel de ContinuationToken als het tijds bereik opgeven in dezelfde aanroep. Wanneer er meer dan 100 chaos-gebeurtenissen zijn, worden de chaos-gebeurtenissen geretourneerd in meerdere segmenten waarbij een segment niet meer dan 100 chaos gebeurtenissen bevat en dat u het volgende segment kunt ophalen met behulp van het vervolg token.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --vervolg token | De vervolg token parameter wordt gebruikt om de volgende set resultaten op te halen. Een vervolg token met een niet-lege waarde wordt opgenomen in het antwoord van de API wanneer de resultaten van het systeem niet in één antwoord passen. Wanneer deze waarde wordt door gegeven aan de volgende API-aanroep, retourneert de API de volgende set resultaten. Als er geen verdere resultaten zijn, bevat het vervolg token geen waarde. De waarde van deze para meter mag geen URL-code ring zijn. |
| --eind tijd-UTC | De Windows-bestands tijd die de eind tijd vertegenwoordigt van het tijds bereik waarvoor een chaos-rapport moet worden gegenereerd. Raadpleeg de [methode DateTime. onToFileTimeUtc](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) voor meer informatie. |
| --Max-resultaten | Het maximum aantal resultaten dat moet worden geretourneerd als onderdeel van de query's in de pagina. Met deze para meter wordt de bovengrens gedefinieerd voor het aantal geretourneerde resultaten. De geretourneerde resultaten kunnen kleiner zijn dan de opgegeven maximum resultaten als ze niet in het bericht passen conform de maximale grootte van de berichten die in de configuratie is gedefinieerd. Als deze para meter nul is of niet is opgegeven, bevat de opgevraagde query zoveel mogelijk resultaten die in het retour bericht passen. |
| --begin tijd-UTC | De Windows-bestands tijd die de start tijd vertegenwoordigt van het tijds bereik waarvoor een chaos-rapport moet worden gegenereerd. Raadpleeg de [methode DateTime. onToFileTimeUtc](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) voor meer informatie. |
| --time-out-t | De time-out van de server voor het uitvoeren van de bewerking in enkele seconden. Met deze time-out geeft u de tijds duur op die de client nodig heeft om te wachten tot de aangevraagde bewerking is voltooid. De standaard waarde voor deze para meter is 60 seconden.  Standaard \: 60. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-chaos-get"></a>sfctl chaos ophalen
De status van chaos ophalen.

Haal de status van chaos op die aangeeft of chaos wordt uitgevoerd, de chaos-para meters die worden gebruikt voor het uitvoeren van chaos en de status van het schema chaos.

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

## <a name="sfctl-chaos-start"></a>sfctl chaos starten
Chaos wordt gestart in het cluster.

Als chaos niet al in het cluster wordt uitgevoerd, wordt chaos gestart met de door gegeven para meters in chaos. Als chaos al wordt uitgevoerd wanneer deze aanroep plaatsvindt, mislukt de aanroep met de fout code FABRIC_E_CHAOS_ALREADY_RUNNING. Raadpleeg het artikel [Controlled chaos in service Fabric-clusters](https\://docs.microsoft.com/azure/service-fabric/service-fabric-controlled-chaos) voor meer informatie.

### <a name="arguments"></a>Argumenten

|Argument|Beschrijving|
| --- | --- |
| --app-type-status-Policy-map | JSON-gecodeerde matrix van Dictionary-items (sleutel/waarde) met Maxi maal percentage beschadigde toepassingen voor specifieke toepassings typen. Elke vermelding in de woorden lijst geeft als een sleutel de naam van het toepassings type en een geheel getal voor de waarde die het MaxPercentUnhealthyApplications-percentage vertegenwoordigt dat wordt gebruikt om de toepassingen van het opgegeven toepassings type te evalueren. <br><br> Definieert een toewijzing met Maxi maal percentage toepassingen met een slechte status voor specifieke toepassings typen. De toewijzing van het status beleid van het toepassings type kan worden gebruikt tijdens de cluster status evaluatie om afzonderlijke toepassings typen te beschrijven. De toepassings typen die zijn opgenomen in de kaart, worden geëvalueerd op basis van het percentage dat is opgegeven in de kaart en niet met de globale MaxPercentUnhealthyApplications die zijn gedefinieerd in het cluster status beleid. De toepassingen van toepassings typen die zijn opgegeven in de kaart, worden niet meegeteld bij de algemene groep toepassingen. Als sommige toepassingen van een type bijvoorbeeld kritiek zijn, kan de Cluster beheerder een vermelding toevoegen aan de kaart voor het betreffende toepassings type en een waarde van 0% toekennen (geen fouten verdragen). Alle andere toepassingen kunnen worden geëvalueerd met MaxPercentUnhealthyApplications ingesteld op 20% om sommige fouten te verdragen buiten de duizenden toepassings exemplaren. De toewijzing van het status beleid van het toepassings type wordt alleen gebruikt als het cluster manifest de status evaluatie van het toepassings type inschakelt met behulp van de configuratie vermelding voor HealthManager/EnableApplicationTypeHealthEvaluation. <br><br> Voor beeld van JSON-gecodeerde teken reeks: [{ \" Key \" : \" Fabric:/stem \" , \" waarde \" : \" 0 \" }] |
| --chaos-doel filter | JSON-gecodeerde woorden lijst met twee teken reeks sleutel typen. De twee sleutels zijn NodeTypeInclusionList en ApplicationInclusionList. De waarden voor beide sleutels zijn lijst met teken reeksen. chaos_target_filter definieert alle filters voor de specifieke chaos-fouten, bijvoorbeeld alleen bepaalde knooppunt typen of fout bij bepaalde toepassingen. <br><br> Als chaos_target_filter niet wordt gebruikt, chaos fouten alle cluster entiteiten. Als chaos_target_filter wordt gebruikt, veroorzaakt chaos alleen de entiteiten die voldoen aan de chaos_target_filter-specificatie. NodeTypeInclusionList en ApplicationInclusionList bieden alleen een semantiek toe. Het is niet mogelijk om een snij punt op te geven voor NodeTypeInclusionList en ApplicationInclusionList. Het is bijvoorbeeld niet mogelijk om ' fout deze toepassing alleen op te geven wanneer deze zich op het knooppunt type bevindt '. Wanneer een entiteit is opgenomen in NodeTypeInclusionList of ApplicationInclusionList, kan die entiteit niet worden uitgesloten met ChaosTargetFilter. Zelfs als applicationx niet wordt weer gegeven in ApplicationInclusionList, kan in sommige chaos iteratie applicationx een fout optreden, omdat deze zich in een knoop punt van nodeTypeY bevindt dat is opgenomen in NodeTypeInclusionList. Als zowel NodeTypeInclusionList als ApplicationInclusionList leeg zijn, wordt er een ArgumentException gegenereerd. Alle typen fouten (knoop punt opnieuw starten, code pakket opnieuw starten, replica verwijderen, replica opnieuw starten en secundaire verplaatsen) zijn ingeschakeld voor de knoop punten van deze knooppunt typen. Als een knooppunt type (bijvoorbeeld NodeTypeX) niet wordt weer gegeven in de NodeTypeInclusionList, worden er op knooppunt niveau fouten (zoals NodeRestart) nooit ingeschakeld voor de knoop punten van NodeTypeX, maar code pakket-en replica fouten kunnen nog steeds worden ingeschakeld voor NodeTypeX als een toepassing in de ApplicationInclusionList zich op een knoop punt van NodeTypeX bevindt. Er kunnen Maxi maal 100 knooppunt type namen worden opgenomen in deze lijst, om dit aantal te verhogen, is een configuratie-upgrade vereist voor de configuratie van MaxNumberOfNodeTypesInChaosEntityFilter. Alle replica's die deel uitmaken van services van deze toepassingen, zijn geschikter naar replica fouten (start replica, verwijder replica, Move Primary en Move secundair) door chaos. Chaos kan een code pakket alleen opnieuw starten als het code pakket alleen replica's van deze toepassingen host. Als een toepassing niet in deze lijst wordt weer gegeven, kan deze in sommige chaos-iteraties nog steeds worden gestopt als de toepassing wordt beëindigd op een knoop punt van een knooppunt type dat is opgenomen in NodeTypeInclusionList. Als applicationx echter is gekoppeld aan nodeTypeY via plaatsings beperkingen en applicationx niet aanwezig is in ApplicationInclusionList en nodeTypeY niet aanwezig is in NodeTypeInclusionList, wordt applicationx nooit gefoutd. Er kunnen Maxi maal 1000 toepassings namen in deze lijst worden opgenomen, om dit aantal te verhogen, is een configuratie-upgrade vereist voor de configuratie van MaxNumberOfApplicationsInChaosEntityFilter. |
| --context | JSON-gecodeerde toewijzing van (teken reeks, teken reeks) type sleutel-waardeparen. De kaart kan worden gebruikt om informatie vast te leggen over de uitvoering van de chaos. Er kunnen niet meer dan 100 dergelijke paren bestaan en elke teken reeks (sleutel of waarde) mag Maxi maal 4095 tekens lang zijn. Deze kaart wordt ingesteld door de start van de chaos-uitvoering, zodat de context van de specifieke uitvoering optioneel kan worden opgeslagen. |
| --uitschakelen-verplaatsen-replica-fouten | Hiermee schakelt u het verplaatsen van primaire en secundaire fouten verplaatsen. |
| --Max-cluster-stabilisatie | De maximale hoeveelheid tijd die moet worden gewacht totdat alle cluster entiteiten stabiel en in orde zijn.  Standaard \: 60. <br><br> Chaos wordt uitgevoerd in iteraties en aan het begin van elke iteratie wordt de status van cluster entiteiten gevalideerd. Tijdens de validatie als een cluster entiteit niet stabiel is en in orde is binnen MaxClusterStabilizationTimeoutInSeconds, genereert chaos een mislukte validatie gebeurtenis. |
| --Max-gelijktijdige-fouten | Het maximum aantal gelijktijdige fouten per iteratie. Chaos wordt uitgevoerd in iteraties en twee opeenvolgende herhalingen worden gescheiden door een validatie fase. Hoe hoger de gelijktijdigheid, hoe sterker het introduceren van fouten--die een complexere reeks statussen hebben om fouten op te sporen. De aanbeveling is om te beginnen met een waarde van 2 of 3 en om voorzichtig te zijn tijdens het verplaatsen.  Standaard \: 1. |
| --Max-percentage-beschadigde apps | Bij het evalueren van de cluster status tijdens chaos, het Maxi maal toegestane percentage van toepassingen met een slechte status voordat een fout wordt gemeld. <br><br> Het Maxi maal toegestane percentage van toepassingen met een slechte status voordat een fout wordt gemeld. Als u bijvoorbeeld wilt toestaan dat 10% van de toepassingen een slechte status heeft, is deze waarde 10. Het percentage vertegenwoordigt het Maxi maal toegestane percentage van toepassingen die een slechte status kunnen hebben voordat het cluster als fout wordt beschouwd. Als het percentage wordt geëerbiedigd, maar er ten minste één slechte toepassing is, wordt de status als een waarschuwing geëvalueerd. Dit wordt berekend door het aantal toepassingen met een slechte status te delen ten opzichte van het totale aantal toepassings exemplaren in het cluster, met uitzonde ring van toepassings typen die zijn opgenomen in de ApplicationTypeHealthPolicyMap. De berekening wordt afgerond om één fout te verdragen bij een klein aantal toepassingen. Het standaard percentage is nul. |
| --Max-percentage-slechte status: knoop punten | Bij het evalueren van de cluster status tijdens chaos, het Maxi maal toegestane percentage van beschadigde knoop punten voordat een fout wordt gemeld. <br><br> Het Maxi maal toegestane percentage van beschadigde knoop punten voordat een fout wordt gemeld. Als u bijvoorbeeld wilt toestaan dat 10% van de knoop punten een slechte status heeft, is deze waarde 10. Het percentage vertegenwoordigt het Maxi maal toegestane percentage van knoop punten waarvan de status niet in orde kan zijn voordat het cluster als fout wordt beschouwd. Als het percentage wordt geëerbiedigd maar er ten minste één slecht knoop punt is, wordt de status als waarschuwing geëvalueerd. Het percentage wordt berekend door het aantal knoop punten met een slechte status te delen ten opzichte van het totale aantal knoop punten in het cluster. De berekening wordt afgerond om één fout te verdragen op een klein aantal knoop punten. Het standaard percentage is nul. In grote clusters zijn bepaalde knoop punten altijd actief of uitgaand voor reparaties, dus dit percentage moet zo worden geconfigureerd dat het wordt toegestaan. |
| --time-to-run-out | Totale tijd (in seconden) dat chaos wordt uitgevoerd voordat deze automatisch wordt gestopt. De Maxi maal toegestane waarde is 4.294.967.295 (System. UInt32. MaxValue).  Standaard \: 4294967295. |
| --time-out-t | Standaard \: 60. |
| --wacht tijd tussen-fouten | Wacht tijd (in seconden) tussen opeenvolgende fouten binnen één iteratie.  Standaard \: 20. <br><br> Hoe groter de waarde, hoe lager de overlappende fouten en hoe eenvoudiger de volg orde van de status overgangen die het cluster doorloopt. De aanbeveling begint met een waarde tussen 1 en 5 en wees voorzichtig tijdens het verplaatsen. |
| --wacht tijd-tussen-iteraties | Tijds scheiding (in seconden) tussen twee opeenvolgende herhalingen van chaos. Hoe groter de waarde, hoe lager het aantal fout injecties.  Standaard \: 30. |
| --waarschuwing-als-fout | Hiermee wordt aangegeven of waarschuwingen worden behandeld met dezelfde ernst als bij fouten. |

### <a name="global-arguments"></a>Algemene argumenten

|Argument|Beschrijving|
| --- | --- |
| --debug | Verg root logboek registratie uitgebreid om alle logboeken voor fout opsporing weer te geven. |
| --help -h | Dit Help-bericht weer geven en afsluiten. |
| --uitvoer-o | Uitvoer indeling.  Toegestane waarden \: JSON, jsonc, Table, TSV.  Standaard \: JSON. |
| --query | JMESPath-query reeks. Zie http \: //jmespath.org/voor meer informatie en voor beelden. |
| --verbose | Uitgebreide logboek registratie verhogen. Gebruik--debug voor volledige logboeken voor fout opsporing. |

## <a name="sfctl-chaos-stop"></a>sfctl chaos stoppen
Stopt chaos als deze wordt uitgevoerd in het cluster en de chaos-planning in een status gestopt heeft gezet.

Stopt chaos met het uitvoeren van nieuwe fouten. In-Flight-fouten blijven uitvoeren totdat ze zijn voltooid. Het huidige chaos-schema wordt in een status gestopt gezet. Zodra een schema is gestopt, blijft het de status gestopt en wordt het niet gebruikt om nieuwe uitvoeringen van chaos te plannen. U moet een nieuw chaos-schema instellen om de planning te hervatten.

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


## <a name="next-steps"></a>Volgende stappen
- [Stel](service-fabric-cli.md) de service Fabric cli in.
- Meer informatie over het gebruik van de Service Fabric CLI met behulp van de [voorbeeld scripts](./scripts/sfctl-upgrade-application.md).
