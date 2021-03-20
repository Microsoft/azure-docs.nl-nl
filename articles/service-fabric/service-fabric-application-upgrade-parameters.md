---
title: 'Toepassings upgrade: upgrade parameters'
description: Beschrijft para meters met betrekking tot het bijwerken van een Service Fabric-toepassing, inclusief status controles die moeten worden uitgevoerd en het beleid om de upgrade automatisch ongedaan te maken.
ms.topic: conceptual
ms.date: 11/08/2018
ms.openlocfilehash: 6b6116bf1188fcf191b2d672e6c698bb3c050e6c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018474"
---
# <a name="application-upgrade-parameters"></a>Parameters toepassingsupgrade
In dit artikel worden de verschillende para meters beschreven die van toepassing zijn tijdens de upgrade van een Azure Service Fabric-toepassing. De para meters voor toepassings upgrades bepalen de time-outs en status controles die tijdens de upgrade worden toegepast en geven de beleids regels op die moeten worden toegepast wanneer een upgrade mislukt. Toepassings parameters zijn van toepassing op upgrades met:
- PowerShell
- Visual Studio
- SFCTL
- [REST](/rest/api/servicefabric/sfclient-api-startapplicationupgrade)

Toepassings upgrades worden gestart via een van de drie door de gebruiker geselecteerde upgrade modi. Elke modus heeft een eigen set toepassings parameters:
- Nauwlettend
- Niet-bewaakt automatisch
- Niet-bewaakt hand matig

De toepasselijke vereiste en optionele para meters worden als volgt in elke sectie beschreven.

## <a name="visual-studio-and-powershell-parameters"></a>Visual Studio-en Power shell-para meters

Service Fabric toepassings upgrades met behulp van Power shell gebruikt u de opdracht [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade) . De upgrade modus is geselecteerd door de para meter **bewaakt**, **UnmonitoredAuto** of **UnmonitoredManual** door te geven aan [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade).

De upgrade parameters voor Visual Studio Service Fabric-toepassing worden ingesteld via het dialoog venster upgrade-instellingen van Visual Studio. De Visual Studio-upgrade modus wordt geselecteerd in de vervolg keuzelijst **upgrade modus** naar **bewaakt**, **UnmonitoredAuto** of **UnmonitoredManual**. Zie [de upgrade van een service Fabric-toepassing configureren in Visual Studio](service-fabric-visualstudio-configure-upgrade.md)voor meer informatie.

### <a name="required-parameters"></a>Vereiste para meters
(PS = Power shell, VS = Visual Studio)

| Parameter | Van toepassing op | Beschrijving |
| --- | --- | --- |
ApplicationName |PS| De naam van de toepassing die wordt bijgewerkt. Voor beelden: Fabric:/VisualObjects, Fabric:/ClusterMonitor. |
ApplicationTypeVersion|PS|De versie van het toepassings type dat de upgrade doelen. |
FailureAction |PS, VS|Toegestane waarden zijn **Rollback**, **Manual** en **ongeldig**. De compensatie actie die moet worden uitgevoerd wanneer een *bewaakte* upgrade problemen ondervindt met bewakings beleid of schendingen van het status beleid. <br>**Terugdraai** actie geeft aan dat de upgrade automatisch wordt teruggedraaid naar de versie van vóór de upgrade. <br>**Hand matig** geeft aan dat de upgrade wordt overgeschakeld naar de *UnmonitoredManual* -upgrade modus. <br>**Ongeldig** geeft aan dat de fout actie ongeldig is.|
Nauwlettend |PS|Geeft aan dat de upgrade modus wordt gecontroleerd. Nadat de cmdlet een upgrade voor een upgrade domein heeft voltooid en de status van het upgrade domein en het cluster voldoen aan de status beleidsregels die u definieert, Service Fabric het volgende upgrade domein bij te werken. Als het upgrade domein of het cluster niet aan het status beleid voldoet, mislukt de upgrade en Service Fabric wordt de upgrade voor het upgrade domein teruggezet of wordt de hand matige modus uitgevoerd volgens het opgegeven beleid. Dit is de aanbevolen modus voor het bijwerken van toepassingen in een productie omgeving. |
Upgrade mode ingesteld | UPGRADEN | Toegestane waarden worden **bewaakt** (standaard), **UnmonitoredAuto** of **UnmonitoredManual**. Zie Power shell-para meters voor elke modus in dit artikel voor meer informatie. |
UnmonitoredAuto | PS | Geeft aan dat de upgrade modus automatisch wordt bewaakt. Nadat Service Fabric een upgrade domein hebt bijgewerkt, Service Fabric het volgende upgrade domein bijgewerkt, onafhankelijk van de status van de toepassing. Deze modus wordt niet aanbevolen voor productie en is alleen nuttig tijdens de ontwikkeling van een toepassing. |
UnmonitoredManual | PS | Geeft aan dat de upgrade modus niet-bewaakt hand matig is. Nadat Service Fabric een upgrade domein hebt bijgewerkt, wordt gewacht tot u het volgende upgrade domein wilt bijwerken met behulp van de cmdlet *Resume-ServiceFabricApplicationUpgrade* . |

### <a name="optional-parameters"></a>Optionele parameters

De para meters voor de status evaluatie zijn optioneel. Als de criteria voor de status evaluatie niet zijn opgegeven wanneer een upgrade wordt gestart, gebruikt Service Fabric het toepassings status beleid dat is opgegeven in de ApplicationManifest.xml van het toepassings exemplaar.

> [!div class="mx-tdBreakAll"]
> | Parameter | Van toepassing op | Beschrijving |
> | --- | --- | --- |
> | ApplicationParameter |PS, VS| Hiermee geeft u de onderdrukkingen voor toepassings parameters op.<br>Power shell-toepassings parameters zijn opgegeven als hash-naam/waarde-paren. Bijvoorbeeld: @ {"VotingData_MinReplicaSetSize" = "3"; "VotingData_PartitionCount" = "1"}.<br>Visual Studio-toepassings parameters kunnen worden opgegeven in het dialoog venster Service Fabric toepassing publiceren in het veld **toepassings parameters bestand** .
> | Bevestigen |PS| Toegestane waarden zijn **waar** en **Onwaar**. Vraagt om bevestiging voordat de cmdlet wordt uitgevoerd. |
> | ConsiderWarningAsError |PS, VS |Toegestane waarden zijn **waar** en **Onwaar**. De standaard waarde is **False**. Behandel de waarschuwingen voor de status van de toepassing als fouten bij het evalueren van de status van de toepassing tijdens de upgrade. Service Fabric worden standaard status gebeurtenissen niet geëvalueerd als fouten (fouten), zodat de upgrade kan worden voortgezet, zelfs als er sprake is van waarschuwings gebeurtenissen. |
> | DefaultServiceTypeHealthPolicy | PS, VS |Hiermee geeft u het status beleid op voor het standaard service type dat moet worden gebruikt voor de bewaakte upgrade in de indeling MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices. Bijvoorbeeld, 5, 10, 15 geeft de volgende waarden aan: MaxPercentUnhealthyPartitionsPerService = 5, MaxPercentUnhealthyReplicasPerPartition = 10, MaxPercentUnhealthyServices = 15. |
> | Force | PS, VS | Toegestane waarden zijn **waar** en **Onwaar**. Geeft aan dat het upgrade proces het waarschuwings bericht overs laat en de upgrade afdwingt, zelfs wanneer het versie nummer niet is gewijzigd. Dit is nuttig voor lokale tests, maar wordt niet aanbevolen voor gebruik in een productie omgeving, omdat de bestaande implementatie moet worden verwijderd waardoor er minder tijd en mogelijk gegevens verloren gaan. |
> | ForceRestart |PS, VS |Als u een configuratie of gegevens pakket bijwerkt zonder de service code bij te werken, wordt de service alleen opnieuw opgestart als de eigenschap ForceRestart is ingesteld op **True**. Wanneer de update is voltooid, wordt door Service Fabric een melding verzonden naar de service dat er een nieuw configuratie pakket of gegevens pakket beschikbaar is. De service is verantwoordelijk voor het Toep assen van de wijzigingen. Als dat nodig is, kan de service zichzelf opnieuw opstarten. |
> | HealthCheckRetryTimeoutSec |PS, VS |De duur (in seconden) die Service Fabric de status evaluatie blijft uitvoeren voordat de upgrade wordt gedeclareerd als mislukt. De standaard waarde is 600 seconden. Deze duur begint nadat *HealthCheckWaitDurationSec* is bereikt. In deze *HealthCheckRetryTimeout* kan service Fabric meerdere status controles van de toepassings status uitvoeren. De standaard waarde is 10 minuten en moet op de juiste wijze worden aangepast voor uw toepassing. |
> | HealthCheckStableDurationSec |PS, VS |De duur (in seconden) om te controleren of de toepassing stabiel is voordat u overstapt op het volgende upgrade domein of de upgrade uitvoert. Deze wacht tijd wordt gebruikt om niet-gedetecteerde wijzigingen aan de status te voor komen nadat de status controle is uitgevoerd. De standaard waarde is 120 seconden, en moet op de juiste wijze worden aangepast voor uw toepassing. |
> | HealthCheckWaitDurationSec |PS, VS | De wacht tijd (in seconden) nadat de upgrade is voltooid voor het upgrade domein voordat Service Fabric de status van de toepassing evalueert. Deze duur kan ook worden overwogen wanneer een toepassing actief moet zijn voordat deze in orde kan worden beschouwd. Als de status controle wordt door gegeven, wordt het upgrade proces voortgezet naar het volgende upgrade domein.  Als de status controle mislukt, Service Fabric wacht op [UpgradeHealthCheckInterval](./service-fabric-cluster-fabric-settings.md#clustermanager) voordat u de status controle opnieuw probeert uit te voeren totdat de *HealthCheckRetryTimeoutSec* is bereikt. De standaard-en aanbevolen waarde is 0 seconden. |
> | MaxPercentUnhealthyDeployedApplications|PS, VS |De standaard-en aanbevolen waarde is 0. Geef het maximum aantal geïmplementeerde toepassingen op (Zie de [sectie status](service-fabric-health-introduction.md)) die een slechte status kan hebben voordat de toepassing wordt beschouwd als beschadigd en de upgrade mislukt. Deze para meter definieert de toepassings status van het knoop punt en helpt tijdens het bijwerken van problemen te detecteren. De replica's van de toepassing krijgen normaal gesp roken taak verdeling naar het andere knoop punt, waardoor de toepassing in orde kan worden weer gegeven, waardoor de upgrade kan worden voortgezet. Als u een strikte *MaxPercentUnhealthyDeployedApplications* -status opgeeft, kan service Fabric snel een probleem met het toepassings pakket detecteren en een mislukte snelle upgrade maken. |
> | MaxPercentUnhealthyServices |PS, VS |Een para meter voor *DefaultServiceTypeHealthPolicy* en *ServiceTypeHealthPolicyMap*. De standaard-en aanbevolen waarde is 0. Geef het maximum aantal services in het toepassings exemplaar op dat slecht kan zijn voordat de toepassing wordt beschouwd als beschadigd en de upgrade mislukt. |
> | MaxPercentUnhealthyPartitionsPerService|PS, VS |Een para meter voor *DefaultServiceTypeHealthPolicy* en *ServiceTypeHealthPolicyMap*. De standaard-en aanbevolen waarde is 0. Geef het maximum aantal partities in een service op dat slecht kan zijn voordat de service wordt beschouwd als beschadigd. |
> | MaxPercentUnhealthyReplicasPerPartition|PS, VS |Een para meter voor *DefaultServiceTypeHealthPolicy* en *ServiceTypeHealthPolicyMap*. De standaard-en aanbevolen waarde is 0. Geef het maximale aantal replica's in een partitie op dat niet in orde kan zijn voordat de partitie wordt beschouwd als een slechte status. |
> | ServiceTypeHealthPolicyMap | PS, VS | Hiermee wordt het status beleid aangegeven dat wordt gebruikt om de status van services te evalueren die horen bij een service type. Hiermee wordt een invoer van hash-tabellen in de volgende indeling gebruikt: @ {"ServiceTypeName": "MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices"} bijvoorbeeld: @ {"ServiceTypeName01" = "5, 10, 5"; "ServiceTypeName02" = "5, 5, 5"} |
> | TimeoutSec | PS, VS | Hiermee geeft u de time-outperiode in seconden voor de bewerking. |
> | UpgradeDomainTimeoutSec |PS, VS |Maximum tijd (in seconden) voor het upgraden van één upgrade domein. Als deze time-out is bereikt, wordt de upgrade gestopt en wordt er een opbrengst uitgevoerd op basis van de instelling voor *FailureAction*. De standaard waarde is nooit (oneindig) en moet op de juiste wijze worden aangepast voor uw toepassing. |
> | UpgradeReplicaSetCheckTimeoutSec |PS, VS |Gemeten in seconden.<br>**Stateless service**: in één upgrade domein probeert service Fabric ervoor te zorgen dat er extra exemplaren van de service beschikbaar zijn. Als het aantal doel instanties meer is dan een, Service Fabric wacht tot er meer dan één instantie beschikbaar is, tot een maximale time-outwaarde. Deze time-out wordt opgegeven met behulp van de eigenschap *UpgradeReplicaSetCheckTimeoutSec* . Als de time-outperiode is verlopen, wordt Service Fabric doorgegaan met de upgrade, ongeacht het aantal service-exemplaren. Als het aantal exemplaren van de doel groep is, wordt Service Fabric niet gewacht en wordt er onmiddellijk een upgrade uitgevoerd.<br><br>**Stateful service**: in één upgrade domein probeert service Fabric om ervoor te zorgen dat de replicaset een quorum heeft. Service Fabric wacht tot een quorum beschikbaar is, tot een maximale time-outwaarde (opgegeven door de eigenschap *UpgradeReplicaSetCheckTimeoutSec* ). Als de time-outperiode is verlopen, wordt Service Fabric doorgegaan met de upgrade, ongeacht het quorum. Deze instelling is ingesteld als nooit (oneindig) bij het terugdraaien en 1200 seconden bij het terugdraaien. |
> | UpgradeTimeoutSec |PS, VS |Een time-out (in seconden) die van toepassing is op de volledige upgrade. Als deze time-out is bereikt, wordt de upgrade gestopt en wordt *FailureAction* geactiveerd. De standaard waarde is nooit (oneindig) en moet op de juiste wijze worden aangepast voor uw toepassing. |
> | WhatIf | PS | Toegestane waarden zijn **waar** en **Onwaar**. Hiermee wordt weergegeven wat er zou gebeuren als u de cmdlet uitvoert. De cmdlet wordt niet uitgevoerd. |

De criteria *MaxPercentUnhealthyServices*, *MaxPercentUnhealthyPartitionsPerService* en *MaxPercentUnhealthyReplicasPerPartition* kunnen worden opgegeven per service type voor een toepassings exemplaar. Als deze para meters per service worden ingesteld, kan een toepassing verschillende service typen met verschillende evaluatie beleidsregels bevatten. Een stateless Gateway Service type kan bijvoorbeeld een *MaxPercentUnhealthyPartitionsPerService* hebben die afwijkt van een stateful Engine service type voor een bepaald toepassings exemplaar.

## <a name="sfctl-parameters"></a>SFCTL-para meters

Service Fabric toepassings upgrades via de Service Fabric CLI gebruik de [sfctl-toepassings upgrade](./service-fabric-sfctl-application.md#sfctl-application-upgrade) opdracht samen met de volgende vereiste en optionele para meters.

### <a name="required-parameters"></a>Vereiste para meters

| Parameter | Beschrijving |
| --- | --- |
| toepassings-id  |ID van de toepassing die wordt bijgewerkt. <br> Dit is doorgaans de volledige naam van de toepassing zonder het URI-schema ' Fabric: '. Vanaf versie 6,0 worden hiërarchische namen gescheiden met het \~ teken. Als de naam van de toepassing bijvoorbeeld ' Fabric:/Mijntoep/app1 ' is, is de toepassings-id ' Mijntoep \~ app1 ' in 6.0 + en ' Mijntoep/app1 ' in vorige versies.|
toepassings versie |De versie van het toepassings type dat de upgrade doelen.|
parameters  |Een JSON-gecodeerde lijst van toepassings parameters onderdrukkingen die moeten worden toegepast bij het upgraden van de toepassing.|

### <a name="optional-parameters"></a>Optionele parameters

| Parameter | Beschrijving |
| --- | --- |
standaard-service-status-beleid | [JSON](/rest/api/servicefabric/sfclient-model-servicetypehealthpolicy) -gecodeerde specificatie van het status beleid dat standaard wordt gebruikt om de status van een service type te evalueren. De kaart is standaard leeg. |
fout-actie | Toegestane waarden zijn **Rollback**, **Manual** en **ongeldig**. De compensatie actie die moet worden uitgevoerd wanneer een *bewaakte* upgrade problemen ondervindt met bewakings beleid of schendingen van het status beleid. <br>**Terugdraai** actie geeft aan dat de upgrade automatisch wordt teruggedraaid naar de versie van vóór de upgrade. <br>**Hand matig** geeft aan dat de upgrade wordt overgeschakeld naar de *UnmonitoredManual* -upgrade modus. <br>**Ongeldig** geeft aan dat de fout actie ongeldig is.|
geforceerd opnieuw opstarten | Als u een configuratie of gegevens pakket bijwerkt zonder de service code bij te werken, wordt de service alleen opnieuw opgestart als de eigenschap ForceRestart is ingesteld op **True**. Wanneer de update is voltooid, wordt door Service Fabric een melding verzonden naar de service dat er een nieuw configuratie pakket of gegevens pakket beschikbaar is. De service is verantwoordelijk voor het Toep assen van de wijzigingen. Als dat nodig is, kan de service zichzelf opnieuw opstarten. |
status-controle-opnieuw proberen-time-out | De tijd waarvoor de status opnieuw moet worden geëvalueerd wanneer de toepassing of het cluster een slechte status heeft voordat *FailureAction* wordt uitgevoerd. Het wordt eerst geïnterpreteerd als een teken reeks die een ISO 8601-duur vertegenwoordigt. Als dat mislukt, wordt dit geïnterpreteerd als een getal dat het totale aantal milliseconden aangeeft. Standaard: PT0H10M0S. |
status controle: stabiel-duur | De hoeveelheid tijd die de toepassing of het cluster in orde moet blijven voordat de upgrade wordt voortgezet naar het volgende upgrade domein. Het wordt eerst geïnterpreteerd als een teken reeks die een ISO 8601-duur vertegenwoordigt. Als dat mislukt, wordt dit geïnterpreteerd als een getal dat het totale aantal milliseconden aangeeft. Standaard: PT0H2M0S. |
status-controle-wacht tijd | De hoeveelheid tijd die moet worden gewacht na het volt ooien van een upgrade domein voordat het status beleid wordt toegepast. Het wordt eerst geïnterpreteerd als een teken reeks die een ISO 8601-duur vertegenwoordigt. Als dat mislukt, wordt dit geïnterpreteerd als een getal dat het totale aantal milliseconden aangeeft. Standaard waarde: 0.|
Max-beschadigde apps | De standaard-en aanbevolen waarde is 0. Geef het maximum aantal geïmplementeerde toepassingen op (Zie de [sectie status](service-fabric-health-introduction.md)) die een slechte status kan hebben voordat de toepassing wordt beschouwd als beschadigd en de upgrade mislukt. Deze para meter definieert de toepassings status van het knoop punt en helpt tijdens het bijwerken van problemen te detecteren. De replica's van de toepassing krijgen normaal gesp roken taak verdeling naar het andere knoop punt, waardoor de toepassing in orde kan worden weer gegeven, waardoor de upgrade kan worden voortgezet. Als u een strikte status van *Maxi maal toegestane apps* opgeeft, kunt Service Fabric snel een probleem met het toepassings pakket detecteren en een mislukte snelle upgrade maken. Wordt weer gegeven als een getal tussen 0 en 100. |
mode | Toegestane waarden worden **bewaakt**, **upgrade mode ingesteld**, **UnmonitoredAuto**, **UnmonitoredManual**. De standaard waarde is **UnmonitoredAuto**. Zie de sectie met *vereiste para meters* voor Visual Studio en Power shell voor beschrijvingen van deze waarden.|
replica-set-check-timeout |Gemeten in seconden. <br>**Stateless service**: in één upgrade domein probeert service Fabric ervoor te zorgen dat er extra exemplaren van de service beschikbaar zijn. Als het aantal doel instanties meer is dan een, Service Fabric wacht tot er meer dan één instantie beschikbaar is, tot een maximale time-outwaarde. Deze time-out wordt opgegeven met behulp van de eigenschap *replica-set-check-timeout* . Als de time-outperiode is verlopen, wordt Service Fabric doorgegaan met de upgrade, ongeacht het aantal service-exemplaren. Als het aantal exemplaren van de doel groep is, wordt Service Fabric niet gewacht en wordt er onmiddellijk een upgrade uitgevoerd.<br><br>**Stateful service**: in één upgrade domein probeert service Fabric om ervoor te zorgen dat de replicaset een quorum heeft. Service Fabric wacht tot een quorum beschikbaar is, tot een maximale time-outwaarde (opgegeven door de eigenschap *replica-set-check-timeout* ). Als de time-outperiode is verlopen, wordt Service Fabric doorgegaan met de upgrade, ongeacht het quorum. Deze instelling is ingesteld als nooit (oneindig) bij het terugdraaien en 1200 seconden bij het terugdraaien. |
service-status-beleid | JSON-gecodeerde toewijzing met status beleid van Service type per service type naam. De kaart is standaard leeg. [JSON-indeling van de para meter.](/rest/api/servicefabric/sfclient-model-applicationhealthpolicy#servicetypehealthpolicymap).. De JSON voor het gedeelte ' value ' bevat **MaxPercentUnhealthyServices**, **MaxPercentUnhealthyPartitionsPerService** en **MaxPercentUnhealthyReplicasPerPartition**. Zie de sectie optionele para meters in Visual Studio en Power shell voor beschrijvingen van deze para meters.
timeout | Hiermee geeft u de time-outperiode in seconden voor de bewerking. Standaard: 60. |
upgrade-domein-time-out | De hoeveelheid tijd die elk upgrade domein moet volt ooien voordat *FailureAction* wordt uitgevoerd. Het wordt eerst geïnterpreteerd als een teken reeks die een ISO 8601-duur vertegenwoordigt. Als dat mislukt, wordt dit geïnterpreteerd als een getal dat het totale aantal milliseconden aangeeft. De standaard waarde is nooit (oneindig) en moet op de juiste wijze worden aangepast voor uw toepassing. Standaard: P10675199DT02H48M 05.4775807 S. |
upgrade-time-out | De hoeveelheid tijd die elk upgrade domein moet volt ooien voordat *FailureAction* wordt uitgevoerd. Het wordt eerst geïnterpreteerd als een teken reeks die een ISO 8601-duur vertegenwoordigt. Als dat mislukt, wordt dit geïnterpreteerd als een getal dat het totale aantal milliseconden aangeeft. De standaard waarde is nooit (oneindig) en moet op de juiste wijze worden aangepast voor uw toepassing. Standaard: P10675199DT02H48M 05.4775807 S.|
waarschuwing-als-fout | Toegestane waarden zijn **waar** en **Onwaar**. De standaard waarde is **False**. Kan worden door gegeven als een vlag. Behandel de waarschuwingen voor de status van de toepassing als fouten bij het evalueren van de status van de toepassing tijdens de upgrade. Service Fabric worden standaard status gebeurtenissen niet geëvalueerd als fouten (fouten), zodat de upgrade kan worden voortgezet, zelfs als er sprake is van waarschuwings gebeurtenissen. |

## <a name="next-steps"></a>Volgende stappen
Als u een [upgrade uitvoert van uw toepassing met behulp van Visual Studio](service-fabric-application-upgrade-tutorial.md) , wordt u begeleid bij een toepassings upgrade met Visual Studio.

Als u uw toepassing bijwerkt [met Power shell](service-fabric-application-upgrade-tutorial-powershell.md) , kunt u een toepassings upgrade uitvoeren met behulp van Power shell.

Als u uw toepassing bijwerkt [met Service Fabric cli in Linux](service-fabric-application-lifecycle-sfctl.md#upgrade-application) , wordt u begeleid bij het upgraden van een toepassing met behulp van service Fabric cli.

[Een upgrade uitvoeren van uw toepassing met Service Fabric eclips-invoeg toepassing](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Maak uw toepassings upgrades compatibel door te leren hoe u [gegevens serialisatie](service-fabric-application-upgrade-data-serialization.md)gebruikt.

Meer informatie over het gebruik van geavanceerde functionaliteit bij het upgraden van uw toepassing door te verwijzen naar [Geavanceerde onderwerpen](service-fabric-application-upgrade-advanced.md).

Corrigeer veelvoorkomende problemen in toepassings upgrades door te verwijzen naar de stappen in [Troubleshooting Application upgrades](service-fabric-application-upgrade-troubleshooting.md).
