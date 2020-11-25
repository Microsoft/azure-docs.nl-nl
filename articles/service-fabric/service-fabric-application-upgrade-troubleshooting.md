---
title: Problemen met toepassings upgrades oplossen
description: Dit artikel heeft betrekking op enkele veelvoorkomende problemen bij het upgraden van een Service Fabric-toepassing en hoe u deze kunt oplossen.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: d462f2c2482e0fbb4d252967754a9675ed362674
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009344"
---
# <a name="troubleshoot-application-upgrades"></a>Problemen met toepassingsupgrades oplossen

In dit artikel worden enkele veelvoorkomende problemen besproken voor het upgraden van een Azure Service Fabric-toepassing en hoe u deze kunt oplossen.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Problemen met een mislukte toepassings upgrade oplossen

Wanneer een upgrade mislukt, bevat de uitvoer van de opdracht **Get-ServiceFabricApplicationUpgrade** aanvullende informatie voor het opsporen van fouten.  In de volgende lijst wordt aangegeven hoe de aanvullende informatie kan worden gebruikt:

1. Het type fout identificeren.
2. De reden van de fout vaststellen.
3. Isoleer een of meer mislukte onderdelen voor nader onderzoek.

Deze informatie is beschikbaar wanneer Service Fabric de fout detecteert, ongeacht of het **FailureAction** is om de upgrade te herstellen of te onderbreken.

### <a name="identify-the-failure-type"></a>Het type fout identificeren

In de uitvoer van **Get-ServiceFabricApplicationUpgrade** identificeert **FailureTimestampUtc** de TIJDS tempel (in UTC) waarop een upgrade fout is gedetecteerd door service Fabric en **FailureAction** is geactiveerd. **FailureReason** identificeert een van de volgende drie mogelijke oorzaken van de fout op hoog niveau:

1. UpgradeDomainTimeout: geeft aan dat het volt ooien van een bepaald upgrade domein te lang duurde en **UpgradeDomainTimeout** verlopen.
2. OverallUpgradeTimeout: geeft aan dat de algehele upgrade te lang duurde en **UpgradeTimeout** is verlopen.
3. Status controle: geeft aan dat na het bijwerken van een update domein de toepassing beschadigd is volgens het opgegeven status beleid en **HealthCheckRetryTimeout** is verlopen.

Deze vermeldingen worden alleen weer gegeven in de uitvoer wanneer de upgrade mislukt en wordt teruggedraaid. Meer informatie wordt weer gegeven, afhankelijk van het type fout.

### <a name="investigate-upgrade-timeouts"></a>Upgrade-time-outs onderzoeken

Time-outfouten bij upgrades worden meestal veroorzaakt door problemen met de service beschikbaarheid. De uitvoer die volgt op deze alinea is een typische upgrade waarbij service replica's of exemplaren niet kunnen worden gestart in de nieuwe code versie. In het veld **UpgradeDomainProgressAtFailure** wordt een moment opname van alle wachtende upgrade werk opgenomen op het moment van de fout.

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
```

In dit voor beeld is de upgrade mislukt voor het upgrade domein *MYUD1* en zijn er twee partities (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* en *4b43f4d8-b26b-424e-9307-7a7a62e79750*) vastgelopen. De partities zijn vastgelopen, omdat de runtime geen primaire replica's (*WaitForPrimaryPlacement*) kon plaatsen op doel knooppunten *Knooppunt1* en *Knooppunt4*.

De opdracht **Get-ServiceFabricNode** kan worden gebruikt om te controleren of deze twee knoop punten zich bevinden in een upgrade domein *MYUD1*. De *UpgradePhase* vertelt *PostUpgradeSafetyCheck*, wat betekent dat deze veiligheids controles worden uitgevoerd nadat alle knoop punten in het upgrade domein zijn bijgewerkt. Al deze informatie wijst op een mogelijk probleem met de nieuwe versie van de toepassings code. De meest voorkomende problemen zijn service fouten in paden open of promo to Primary code.

Een *UpgradePhase* van *PreUpgradeSafetyCheck* houdt in dat er problemen zijn met het voorbereiden van het upgrade domein voordat het werd uitgevoerd. De meest voorkomende problemen in dit geval zijn service fouten in de paden sluiten of degradatie van primaire code.

De huidige **UpgradeState** is *RollingBackCompleted*, dus de oorspronkelijke upgrade moet zijn uitgevoerd met een rollback **FailureAction**, die automatisch de upgrade terugdraait bij een fout. Als de oorspronkelijke upgrade is uitgevoerd met een hand matige **FailureAction**, is de upgrade in plaats daarvan in de onderbroken status om live fout opsporing van de toepassing toe te staan.

In zeldzame gevallen kan het veld **UpgradeDomainProgressAtFailure** leeg zijn als er een time-out optreedt voor de totale upgrade, net zoals het systeem alle werk voor het huidige upgrade domein voltooit. Als dit het geval is, kunt u proberen de waarden voor de upgrade parameter **UpgradeTimeout** en **UpgradeDomainTimeout** te verhogen en de upgrade opnieuw uit te voeren.

### <a name="investigate-health-check-failures"></a>Status controle fouten onderzoeken

Mislukte status controles kunnen worden geactiveerd door diverse problemen die zich kunnen voordoen nadat alle knoop punten in een upgrade domein de upgrade hebben voltooid en alle veiligheids controles hebben doorstaan. De uitvoer die volgt op deze alinea is een typische upgrade fout vanwege mislukte status controles. In het veld **UnhealthyEvaluations** wordt een moment opname van status controles vastgelegd die zijn mislukt op het moment van de upgrade volgens het opgegeven [status beleid](service-fabric-health-introduction.md).

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

Voor het onderzoeken van status controle fouten moet u eerst weten wat het Service Fabric status model is. Maar zelfs zonder een diep gaande uitleg, kunnen we zien dat twee services een slechte status hebben: *Fabric:/DemoApp/Svc3* en *Fabric:/DemoApp/Svc2*, samen met de fout status rapporten (' InjectedFault ' in dit geval). In dit voor beeld zijn twee van de vier services niet in orde, wat lager is dan het standaard doel van 0% beschadigd (*MaxPercentUnhealthyServices*).

De upgrade is onderbroken tijdens het mislukken door een **FailureAction** van de hand matig op te geven bij het starten van de upgrade. In deze modus kan ons het live-systeem onderzoeken met de status mislukt voordat verdere actie wordt ondernomen.

### <a name="recover-from-a-suspended-upgrade"></a>Herstellen van een onderbroken upgrade

Met een rollback- **FailureAction** is het niet nodig om een herstel uit te voeren nadat de upgrade is mislukt. Met een hand matige **FailureAction** zijn er verschillende herstel opties:

1.  een terugdraai actie activeren
2. De rest van de upgrade hand matig door lopen
3. De bewaakte upgrade hervatten

De **Start-ServiceFabricApplicationRollback** opdracht kan op elk gewenst moment worden gebruikt om de toepassing terug te draaien. Zodra de opdracht met succes is geretourneerd, is de terugdraai aanvraag geregistreerd in het systeem en wordt u vervolgens binnenkort gestart.

De opdracht **Resume-ServiceFabricApplicationUpgrade** kan worden gebruikt om de rest van de upgrade hand matig te door lopen, één upgrade domein per keer. In deze modus worden alleen veiligheids controles uitgevoerd door het systeem. Er worden niet meer status controles uitgevoerd. Deze opdracht kan alleen worden gebruikt wanneer de *UpgradeState* *RollingForwardPending* toont, wat betekent dat het huidige upgrade domein is bijgewerkt, maar dat het volgende niet is gestart (in behandeling).

De opdracht **Update-ServiceFabricApplicationUpgrade** kan worden gebruikt om de bewaakte upgrade te hervatten met de veiligheids-en status controles die worden uitgevoerd.

```powershell
Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored
```

```Output
UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

De upgrade wordt voortgezet vanuit het upgrade domein waar het voor het laatst is onderbroken en gebruikt dezelfde upgrade parameters en status beleid als voorheen. Als dat nodig is, kunnen de upgrade parameters en status beleid die in de voor gaande uitvoer worden weer gegeven in dezelfde opdracht worden gewijzigd wanneer de upgrade wordt hervat. In dit voor beeld is de upgrade hervat in de bewaakte modus, met de para meters en de status beleidsregels ongewijzigd.

## <a name="further-troubleshooting"></a>Verdere problemen oplossen

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Service Fabric volgt niet het opgegeven status beleid

Mogelijke oorzaak 1:

Service Fabric worden alle percentages omgezet in het werkelijke aantal entiteiten (bijvoorbeeld replica's, partities en Services) voor de status evaluatie en worden er altijd naar hele entiteiten afgerond. Als de maximale *MaxPercentUnhealthyReplicasPerPartition* bijvoorbeeld 21% is en er vijf replica's zijn, kunt Service Fabric Maxi maal twee beschadigde replica's (dat wil zeggen `Math.Ceiling (5*0.21)` ). Daarom moet het status beleid dienovereenkomstig worden ingesteld.

Mogelijke oorzaak 2:

Status beleidsregels worden opgegeven in termen van percentages van het totale aantal services en niet voor specifieke service-exemplaren. Als een toepassing bijvoorbeeld vóór een upgrade vier service-exemplaren A, B, C en D heeft, waarbij service D een slechte status heeft, maar met weinig invloed op de toepassing is. We willen de bekende slechte service D negeren tijdens de upgrade en stel de para meter *MaxPercentUnhealthyServices* in op 25%, ervan uitgaande dat alleen A, B en C in orde moeten zijn.

Tijdens de upgrade kan D echter in orde worden, terwijl C slecht wordt. De upgrade slaagt nog steeds omdat slechts 25% van de services niet in orde is. Het kan echter leiden tot onverwachte fouten omdat C onverwacht een slechte status heeft in plaats van D. In deze situatie moet D worden gemodelleerd als een ander Service type dan A, B en C. Omdat het status beleid per service type is opgegeven, kunnen verschillende waarden voor een slechtere frequentie worden toegepast op verschillende services. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Ik heb geen status beleid opgegeven voor de upgrade van de toepassing, maar de upgrade mislukt voor een aantal time-outs die ik nooit heb opgegeven

Wanneer het status beleid niet aan de upgrade aanvraag wordt door gegeven, worden ze opgehaald uit de *ApplicationManifest.xml* van de huidige versie van de toepassing. Als u bijvoorbeeld toepassing X bijwerkt van versie 1,0 naar versie 2,0, worden de beleids regels voor de status van de toepassing gebruikt die in versie 1,0 zijn opgegeven. Als voor de upgrade een ander status beleid moet worden gebruikt, moet het beleid worden opgegeven als onderdeel van de API-aanroep van de toepassings upgrade. De beleids regels die zijn opgegeven als onderdeel van de API-aanroep, zijn alleen van toepassing tijdens de upgrade. Zodra de upgrade is voltooid, worden de beleids regels die in de *ApplicationManifest.xml* zijn opgegeven, gebruikt.

### <a name="incorrect-time-outs-are-specified"></a>Er zijn onjuiste time-outs opgegeven

U hebt zich wellicht afvraagt wat er gebeurt wanneer time-outs inconsistent zijn ingesteld. U kunt bijvoorbeeld een *UpgradeTimeout* hebben die kleiner is dan de *UpgradeDomainTimeout*. Het antwoord is dat er een fout wordt geretourneerd. Er worden fouten geretourneerd als de *UpgradeDomainTimeout* kleiner is dan de som van *HealthCheckWaitDuration* en *HealthCheckRetryTimeout*, of als *UpgradeDomainTimeout* kleiner is dan de som van *HealthCheckWaitDuration* en *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>Mijn upgrades nemen te lang uit

De tijd voor het volt ooien van een upgrade is afhankelijk van de status controles en de opgegeven time-outs. Status controles en time-outs zijn afhankelijk van hoe lang het duurt om de toepassing te kopiëren, te implementeren en te stabiliseren. Het is te agressief met time-outs betekent dat er meer mislukte upgrades mogelijk zijn. het wordt daarom aangeraden om op een conservatieve manier te beginnen met langere time-outs.

Hier volgt een snelle hernieuwde informatie over hoe de time-outs communiceren met de upgrade tijden:

Upgrades voor een upgrade domein kunnen niet sneller worden uitgevoerd dan *HealthCheckWaitDuration*  +  *HealthCheckStableDuration*.

De upgrade fout kan sneller worden uitgevoerd dan *HealthCheckWaitDuration*  +  *HealthCheckRetryTimeout*.

De upgrade tijd voor een upgrade domein wordt beperkt door *UpgradeDomainTimeout*.  Als *HealthCheckRetryTimeout* en *HealthCheckStableDuration* beide niet-nul zijn en de status van de toepassing wordt omgeschakeld, wordt er uiteindelijk een time-out voor de upgrade uitgevoerd op *UpgradeDomainTimeout*. *UpgradeDomainTimeout* begint met tellen zodra de upgrade voor het huidige upgrade domein begint.

## <a name="next-steps"></a>Volgende stappen

Als u een [upgrade uitvoert van uw toepassing met behulp van Visual Studio](service-fabric-application-upgrade-tutorial.md) , wordt u begeleid bij een toepassings upgrade met Visual Studio.

Als u uw toepassing bijwerkt [met Power shell](service-fabric-application-upgrade-tutorial-powershell.md) , kunt u een toepassings upgrade uitvoeren met behulp van Power shell.

Bepalen hoe uw toepassing wordt bijgewerkt met behulp van [upgrade parameters](service-fabric-application-upgrade-parameters.md).

Maak uw toepassings upgrades compatibel door te leren hoe u [gegevens serialisatie](service-fabric-application-upgrade-data-serialization.md)gebruikt.

Meer informatie over het gebruik van geavanceerde functionaliteit bij het upgraden van uw toepassing door te verwijzen naar [Geavanceerde onderwerpen](service-fabric-application-upgrade-advanced.md).