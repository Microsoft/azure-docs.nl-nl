---
title: Levens cyclus van Azure Service Fabric hosten voor activering en deactivering
description: Geeft een uitleg over de levens cyclus van toepassingen en ServicePackage op een knoop punt
author: tugup
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: tugup
ms.openlocfilehash: f049b19703d37412d1ee1961aee6cb327efabe7c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779597"
---
# <a name="azure-service-fabric-hosting-lifecycle"></a>Levens cyclus van Azure Service Fabric-hosting

In dit artikel vindt u een overzicht van gebeurtenissen die zich in azure voordoen Service Fabric wanneer een toepassing wordt geactiveerd op een knoop punt en verschillende cluster configuraties die worden gebruikt voor het beheren van het gedrag.

Voordat u verder gaat, moet u de verschillende concepten en relaties begrijpen die worden uitgelegd in [model a Application in service Fabric][a1]. 

> [!NOTE]
> In dit artikel, tenzij expliciet vermeld als iets anders:
>
> - *Replica* verwijst naar een replica van een stateful service en een exemplaar van een stateless service.
> - *Code package* wordt behandeld als equivalent aan een *ServiceHost* -proces dat een *service* type registreert en replica's van services van die *service* type host.
>

## <a name="activation-of-applicationservicepackage"></a>Activering van toepassings-ServicePackage

De pijp lijn voor activering is als volgt:

1. Down load de ApplicationPackage. Bijvoorbeeld: ApplicationManifest.xml etc.
2. Stel een omgeving in voor de toepassing, bijvoorbeeld: gebruikers maken, enz.
3. Traceer toepassing starten voor deactivering.
4. Down load ServicePackage. Bijvoorbeeld: ServiceManifest.xml, code, configuratie, gegevens pakketten, enzovoort.
5. Omgeving instellen voor service pakket voor de installatie van de firewall, het toewijzen van poorten voor eind punten, enzovoort.
6. Start het bijhouden van ServicePackage voor deactivering.
7. Start SetupEntryPoint van CodePackages en wacht tot de bewerking is voltooid.
8. MainEntryPoint van CodePackages starten.

### <a name="servicetype-blocklisting"></a>Service type Blocklisting
**ServiceTypeDisableFailureThreshold** bepaalt het aantal fouten (activering, down loads, code package-fouten) waarna de service type is gepland voor blocklisting. De eerste activerings fout, download fout of code package-crash gaat schema Service type blocklisting. De **ServiceTypeDisableGraceInterval** -configuratie bepaalt het interval van de respijt periode waarna Service type is gemarkeerd als blocklisted op dat knoop punt. Hoewel dit gebeurt, worden activering, down loads en code package opnieuw opstarten parallel opnieuw uitgevoerd. Opnieuw proberen, betekent bijvoorbeeld dat code package opnieuw moet worden gestart na het vastlopen of Service Fabric probeert pakketten opnieuw te downloaden.

Wanneer Service type is blocklisted, wordt er een status fout ' System. hosting ' weer gegeven voor de eigenschap ' ServiceTypeRegistration: Service type '. Service type is uitgeschakeld op het knoop punt.

Service type wordt opnieuw ingeschakeld op het knoop punt als een van de volgende dingen gebeurt:
- De activering slaagt of bereikt **ActivationMaxFailureCount** nieuwe pogingen bij een fout.
- Het downloaden is geslaagd of **DeploymentMaxFailureCount** nieuwe pogingen bij het mislukken.
- Een code package die is vastgelopen, start en registreert de service type.

**ActivationMaxFailureCount** en **DeploymentMaxFailureCount** zijn het maximum aantal pogingen service Fabric ervoor te zorgen dat een toepassing op een knoop punt wordt geactiveerd of gedownload, waarna service Fabric de service type voor activering opnieuw inschakelt. Zo geeft u de service een andere mogelijkheid om te activeren, wat kan slagen, wat resulteert in het probleem automatisch herstel. Een nieuwe activerings-of download bewerking die wordt geactiveerd door de plaatsing en activering van een replica, kan de service type-blocklisting opnieuw activeren of kan slagen.

> [!NOTE]
> Als uw code package waarop geen service type wordt geregistreerd, vastloopt, heeft dit geen invloed op de service type. Alleen de code package die als host fungeert voor een replica-crash, is van invloed op de service type.
>

### <a name="codepackage-crash"></a>Code package vastlopen
Wanneer een code package vastloopt, gebruikt Service Fabric een uitstel om het opnieuw te starten. De uitstel is onafhankelijk van het feit of het code pakket een type heeft geregistreerd bij de Service Fabric-runtime of niet.

De uitstel-waarde is min (RetryTime, **ActivationMaxRetryInterval**), en deze uitstel waarde wordt verhoogd in constante, lineaire of exponentiële bedragen op basis van de configuratie-instelling **ActivationRetryBackoffExponentiationBase** .

- Constante: if **ActivationRetryBackoffExponentiationBase** = = 0 then RetryTime = **ActivationRetryBackoffInterval**;
- Lineair: als  **ActivationRetryBackoffExponentiationBase** = = 0 then RetryTime = ContinuousFailureCount * **ActivationRetryBackoffInterval** waarbij ContinousFailureCount het aantal keren is dat een code package vastloopt of niet kan worden geactiveerd.
- Exponentieel: RetryTime = (**ActivationRetryBackoffInterval** in seconden) * (**ActivationRetryBackoffExponentiationBase** ^ ContinuousFailureCount);
    
U kunt het gedrag beheren door de waarden te wijzigen. Als u bijvoorbeeld meerdere snelle herstart pogingen wilt, kunt u lineair gebruiken door **ActivationRetryBackoffExponentiationBase** in te stellen op 0 en **ActivationRetryBackoffInterval** op 10. Als een code package is vastgelopen met deze instellingen, is het begin interval na 10 seconden. Als het pakket vastloopt, wordt de uitstel gewijzigd in, 20, 30, 40 seconden, enzovoort, totdat de activering van code package is geslaagd of het code pakket is gedeactiveerd. 
    
De maximale tijds duur Service Fabric back-ups maken (wacht) nadat een fout is onderhevig aan de **ActivationMaxRetryInterval**.
    
Als uw code package vastloopt en er een back-up van wordt gemaakt, moet de IT-afdeling blijven voor **CodePackageContinuousExitFailureResetInterval** voor service Fabric om deze als in orde te beschouwen, waardoor het vorige fout status rapport wordt overschreven als OK en de ContinousFailureCount opnieuw wordt ingesteld.

### <a name="codepackage-not-registering-servicetype"></a>Code package niet registreren voor service type
Als een code package blijft leven en verwacht een service type bij ons te registreren, maar dit niet het geval is, genereert Service Fabric een waarschuwing HealthReport nadat **ServiceTypeRegistrationTimeout** heeft gezegd dat Service type niet binnen de verwachte tijd is geregistreerd.

### <a name="activation-failure"></a>Activerings fout
Service Fabric maakt altijd gebruik van een lineaire back-out (hetzelfde als code package-crash) wanneer deze een fout vindt tijdens de activering. Dit betekent dat de activerings bewerking na (0 + 10 + 20 + 30 + 40) = 100 SEC (eerste nieuwe poging onmiddellijk) wordt uitgevoerd. Nadat deze activering niet opnieuw is uitgevoerd.
    
De maximale activerings uitstel kan worden **ActivationMaxRetryInterval** en opnieuw worden **ActivationMaxFailureCount**.

### <a name="download-failure"></a>Fout bij het downloaden
Service Fabric maakt altijd gebruik van een lineaire back-up wanneer er een fout optreedt tijdens het downloaden. Dit betekent dat de activerings bewerking na (0 + 10 + 20 + 30 + 40) = 100 SEC (eerste nieuwe poging onmiddellijk) wordt uitgevoerd. Daarna wordt de down load niet opnieuw geprobeerd. De lineaire back-off voor down load is gelijk aan ContinuousFailureCount **_DeploymentRetryBackoffInterval_* en kan Maxi maal worden teruggedraaid naar **DeploymentMaxRetryInterval**. Net als bij activeringen kan de Download bewerking opnieuw worden uitgevoerd voor **ActivationMaxFailureCount**.

> [!NOTE]
> Voordat u deze instellingen wijzigt, zijn hier enkele voor beelden die u moet onthouden.

* Als de code package vastloopt en er back-ups worden verwijderd, wordt de service type uitgeschakeld. Maar als de activerings configuratie zodanig is dat deze snel opnieuw wordt opgestart, kan de code package een paar keer worden voordat de service type daad werkelijk blocklisted is. Bijvoorbeeld: aangenomen dat uw code package actief is, registreert u het Service type met Service Fabric en loopt vervolgens vast. In dat geval wordt de **ServiceTypeDisableGraceInterval** -periode geannuleerd zodra de hosting een type registratie heeft ontvangen. En dit kan worden herhaald totdat uw code package wordt teruggedraaid naar een waarde die groter is dan  **ServiceTypeDisableGraceInterval** en vervolgens op Service type wordt blocklisted op het knoop punt. Het maytake langer dan verwacht dat u de service type blocklisted ziet.

* Als Service Fabric systeem een replica op een knoop punt moet plaatsen, wordt in het geval van activeringen het subsysteem van de RA (ReconfigurationAgent) om de toepassing te activeren en wordt elke 15 seconden opnieuw geprobeerd om de activerings aanvraag uit te voeren (afhankelijk van de instelling van de **RAPMessageRetryInterval** -configuratie). Om Service Fabric te weten dat de service type is blocklisted, moet de activerings bewerking in de hosting gedurende een langere periode actief zijn dan het interval voor nieuwe pogingen en de **ServiceTypeDisableGraceInterval**. Bijvoorbeeld: Stel dat op het cluster **ActivationMaxFailureCount** is ingesteld op 5 en **ActivationRetryBackoffInterval** is ingesteld op 1 seconde. Dit betekent dat de activerings bewerking wordt uitgevoerd na (0 + 1 + 2 + 3 + 4) = 10 seconden (herinnert u dat de eerste nieuwe poging onmiddellijk wordt uitgevoerd) en nadat de hosting het opnieuw heeft geprobeerd. In dit geval wordt de activerings bewerking voltooid en wordt na 15 seconden geen nieuwe poging gedaan. Dit gebeurt omdat Service Fabric alle toegestane nieuwe pogingen binnen 15 seconden uitgeput. Bij elke nieuwe poging van ReconfigurationAgent wordt dus een nieuwe activerings bewerking in het subsysteem voor hosting gemaakt en het patroon wordt herhaaldelijk herhaald. Als gevolg hiervan wordt het Service type nooit blocklisted op het knoop punt. Omdat het Service type geen blocklisted op het knoop punt ontvangt, wordt de replica niet verplaatst en geprobeerd op een ander knoop punt.
> 

## <a name="deactivation"></a>Deactivering

Wanneer een ServicePackage wordt geactiveerd op een knoop punt, wordt deze bijgehouden voor deactivering. 

Deactivering werkt op twee manieren:

- Regel matig: bij elke **DeactivationScanInterval** wordt gecontroleerd op SERVICEPACKAGES die nooit een replica hebben gehost en gemarkeerd als kandidaten voor deactivering.
- ReplicaClose: als een replica is gesloten, haalt Deactivator een DecrementUsageCount op. Als de telling wordt ingesteld op 0, betekent dit dat de ServicePackage geen replica host en daarom een kandidaat is voor deactivering.

 Op basis van de activerings modus [exclusief/gedeeld][a2], worden de kandidaten voor deactivering gepland na de **DeactivationGraceInterval** voor SharedMode en na de **ExclusiveModeDeactivationGraceInterval** voor ExclusiveMode. Als er tussen deze tijd een nieuwe replica-plaatsing is, wordt de deactivering geannuleerd.

### <a name="periodically"></a>Periodieke
We bespreken enkele voor beelden van periodieke deactivering:

Voor beeld 1: Stel dat het Deactiveer een scan op tijdstip T (**DeactivationScanInterval**) uitvoert. De volgende scan is op 2T. Stel dat er een ServicePackage-activering is opgetreden op T + 1. Deze ServicePackage is geen host van een replica en moet daarom worden gedeactiveerd. De ServicePackage moet een kandidaat zijn voor deactivering, maar moet in de staat van geen replica zijn voor ten minste een T/m tijd. Dit betekent dat deze in aanmerking komt voor deactivering op 2T + 1. De scan op 2T vindt deze ServicePackage dus niet als een kandidaat voor deactivering. Bij de volgende deactiverings cyclus 3T gebruiken wordt deze ServicePackage gepland voor het deactiveren van de activering omdat er nu geen replica status is ingesteld voor tijd T.  

Voor beeld 2: Stel dat een ServicePackage wordt geactiveerd op het moment van T-1 en het deactiveren van een scan op T. De ServicePackage host geen replica. Vervolgens wordt bij de volgende scan 2T deze ServicePackage als een kandidaat voor deactiveren gevonden en daarom gepland voor deactivering.  

Voor beeld 3: Stel dat een ServicePackage wordt geactiveerd op T – 1 en dat deactiveert een scan op T. De ServicePackage host nog geen replica. Nu op T + 1 wordt een replica geplaatst, dat wil zeggen Hosting haalt een IncrementUsageCount op, wat betekent dat er een replica is gemaakt. Nu kunt u 2T deze ServicePackage niet plannen voor deactivering. Omdat het een replica bevat, wordt de deactivering overgezet naar de ReplicaClose-logica die hieronder wordt uitgelegd.

Voor beeld 4: Stel dat uw ServicePackage groot is, 10 GB ondervindt en veel tijd kan duren om op het knoop punt te downloaden. Zodra een toepassing is geactiveerd, wordt de levens cyclus door de Deactivator bijgehouden. Als u nu de **DeactivationScanInterval** -configuratie hebt ingesteld op een kleine waarde, kunt u problemen ondervinden waarbij uw ServicePackage geen tijd krijgt om te activeren op het knoop punt, omdat de tijd voor het downloaden is overgegaan. Om het probleem op te lossen, kunt u [de ServicePackage van het knoop punt vooraf downloaden][p1] of de **DeactivationScanInterval** verg Roten. 

> [!NOTE]
> Een ServicePackage kan overal worden gedeactiveerd tussen (**DeactivationScanInterval** naar 2 **_DeactivationScanInterval_*) + **DeactivationGraceInterval** /** ExclusiveModeDeactivationGraceInterval * *. 
>

### <a name="replica-close"></a>Replica sluiten:
Bij het deactiveren wordt het aantal Replica's bijgehouden dat een ServicePackage bevat. Als een ServicePackage een replica houdt en de replica is gesloten/uitgeschakeld, haalt host een DecrementUsageCount op. Wanneer een replica wordt geopend, wordt er een IncrementUsageCount door gehost. Het verlagen houdt in dat de ServicePackage nu als host fungeert voor een minder replica en wanneer het aantal wordt neergezet op 0, de ServicePackage is gepland voor deactivering en de tijd waarna deze wordt gedeactiveerd, wordt **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**. 

Bijvoorbeeld: laten we zeggen dat een verlaging plaatsvindt op T en ServicePackage is gepland om te deactiveren op 2T + X (**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**). Als tijdens deze tijd hosting een IncrementUsage wordt opgehaald, wat betekent dat er een replica wordt gemaakt, wordt de deactivering geannuleerd.

> [!NOTE]
> Wat betekenen deze configuratie-instellingen?
**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**: de tijd die aan een ServicePackage wordt toegewezen om opnieuw een andere replica te hosten zodra een replica is gehost. 
**DeactivationScanInterval**: de minimale tijd die aan ServicePackage wordt gegeven voor het hosten van een replica als deze nooit een replica heeft gehost, d.w.z. Als deze niet wordt gebruikt.
>

### <a name="ctrlc"></a>Ctrl+C
Nadat een ServicePackage de **DeactivationGraceInterval** / -**ExclusiveModeDeactivationGraceInterval** heeft door gegeven en nog steeds geen host is van een replica, kan de deactivering niet worden geannuleerd. Code package worden een CTRL + C-handler uitgegeven. Dit betekent dat u de pijp lijn voor deactivering moet door lopen om het proces uit te voeren. Als er gedurende deze tijd een nieuwe replica voor dezelfde ServicePackage wordt opgehaald, mislukt dit omdat er geen overgang kan worden uitgevoerd van deactivering naar activering.

## <a name="cluster-configs"></a>Cluster configuraties

Configuraties met de standaard instellingen die van invloed zijn op de activerings-decativation.

### <a name="servicetype"></a>ServiceType
**ServiceTypeDisableFailureThreshold**: standaard 1. De drempel waarde voor het aantal mislukte pogingen waarna FM (FailoverManager) wordt gewaarschuwd om het Service type op dat knoop punt uit te scha kelen en een ander knoop punt voor plaatsing te proberen.
**ServiceTypeDisableGraceInterval**: standaard 30 sec. Tijds interval waarna het Service type kan worden uitgeschakeld.
**ServiceTypeRegistrationTimeout**: standaard 300 sec. De time-out voor registratie bij Service Fabric van het Service type.

### <a name="activation"></a>Activering
**ActivationRetryBackoffInterval**: standaard 10 sec. Uitstel-interval bij elke activerings fout.
**ActivationMaxFailureCount**: standaard 20. Maximum aantal waarvoor het systeem opnieuw moet worden geactiveerd voordat de activering wordt uitgevoerd. 
**ActivationRetryBackoffExponentiationBase**: standaard 1,5.
**ActivationMaxRetryInterval**: standaard 3600 sec. Maxi maal aantal back-ups voor activering op fouten.
**CodePackageContinuousExitFailureResetInterval**: standaard 300 sec. De time-out voor het opnieuw instellen van het aantal doorlopende afsluit fouten voor code package.

### <a name="download"></a>Downloaden
**DeploymentRetryBackoffInterval**: standaard 10. Back-outinterval voor de implementatie fout.
**DeploymentMaxRetryInterval**: standaard 3600 sec. Maxi maal aantal back-ups voor de implementatie op fouten.
**DeploymentMaxFailureCount**: standaard 20. De implementatie van de toepassing wordt opnieuw geprobeerd voor DeploymentMaxFailureCount tijden voordat de implementatie van die toepassing op het knoop punt is mislukt.

### <a name="deactivation"></a>Deactivering
**DeactivationScanInterval**: standaard 600 sec. Minimale tijd die aan ServicePackage wordt gegeven voor het hosten van een replica als deze nooit een replica heeft gehost, d.w.z. Als deze niet wordt gebruikt.
**DeactivationGraceInterval**: standaard 60 sec. De tijd die aan een ServicePackage is toegewezen voor het hosten van een andere replica zodra deze een replica heeft gehost in het geval van een **gedeeld** proces model.
**ExclusiveModeDeactivationGraceInterval**: standaard 1 sec. De tijd die aan een ServicePackage is toegewezen voor het hosten van een andere replica zodra deze een replica in het geval van een **exclusief** proces model heeft gehost.

## <a name="next-steps"></a>Volgende stappen
[Een toepassing inpakken][a3] en deze voorbereiden om te implementeren.

[Toepassingen implementeren en verwijderen][a4]. In dit artikel wordt beschreven hoe u Power shell gebruikt voor het beheren van toepassings exemplaren.

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
