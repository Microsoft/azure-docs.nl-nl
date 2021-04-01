---
title: Levens cyclus voor activering en deactivering van Azure Service Fabric hosten
description: Meer informatie over de levens cyclus van een toepassing en een ServicePackage op een knoop punt.
author: tugup
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: tugup
ms.openlocfilehash: d8585d0b39e4a4ef9cf77f40ea878ddb47bcb0de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97831819"
---
# <a name="azure-service-fabric-hosting-life-cycle"></a>Levens cyclus van de hosting van Azure Service Fabric

In dit artikel vindt u een overzicht van gebeurtenissen die in azure optreden Service Fabric wanneer een toepassing wordt geactiveerd op een knoop punt. Er worden verschillende cluster configuraties uitgelegd waarmee het gedrag wordt bepaald.

Voordat u doorgaat, moet u ervoor zorgen dat u bekend bent met de concepten en relaties die worden uitgelegd in [model a Application in service Fabric][a1]. 

> [!NOTE]
> In dit artikel wordt een aantal terminologie op gespecialiseerde manieren gebruikt. Tenzij anders vermeld:
>
> - *Replica* verwijst naar een replica van een stateful service en een exemplaar van een stateless service.
> - *Code package* wordt behandeld als equivalent aan een ServiceHost-proces dat een service type registreert. Het hostt Replica's van services van die service type.
>

## <a name="activate-an-applicationpackage-or-servicepackage"></a>Een ApplicationPackage of ServicePackage activeren

Een ApplicationPackage of ServicePackage activeren:

1. Down load de ApplicationPackage (bijvoorbeeld *ApplicationManifest.xml*).
2. Stel een omgeving in voor de toepassing. De stappen omvatten bijvoorbeeld het maken van gebruikers.
3. Het bijhouden van de toepassing starten voor deactivering.
4. Down load de ServicePackage (bijvoorbeeld *ServiceManifest.xml*, code, configuratie bestanden en gegevens pakketten).
5. Stel een omgeving in voor de ServicePackage. De stappen omvatten bijvoorbeeld het instellen van een firewall en het toewijzen van poorten voor eind punten.
6. Het bijhouden van de ServicePackage voor deactivering starten.
7. Start de SetupEntryPoint voor de CodePackages en wacht totdat deze is voltooid.
8. Start de MainEntryPoint voor de CodePackages.

### <a name="servicetype-blocklisting"></a>Service type blocklisting
`ServiceTypeDisableFailureThreshold` Hiermee wordt het aantal toegestane activerings-, Download-en code package-fouten bepaald. Nadat de drempel waarde is bereikt, wordt de service type gepland voor blocklisting. De eerste activerings fout, download fout of code package crash plant de service type blocklisting. 

De `ServiceTypeDisableGraceInterval` configuratie bepaalt het interval van de respijt periode voordat Service type is blocklisted op het knoop punt. Aangezien dit proces wordt afgespeeld, worden activering, down loads en code package opnieuw opgestart parallel uitgevoerd. Opnieuw proberen betekent bijvoorbeeld dat de code package is gepland om opnieuw te starten na het vastlopen of Service Fabric probeert pakketten opnieuw te downloaden.

Wanneer Service type is blocklisted, ziet u een status fout: `'System.Hosting' reported Error for property 'ServiceTypeRegistration:ServiceType'. The ServiceType was disabled on the node.`

Service type wordt opnieuw ingeschakeld op het knoop punt als een van de volgende gebeurtenissen optreedt:
- De activering slaagt. Als dit mislukt, wordt het `ActivationMaxFailureCount` maximum aantal nieuwe pogingen bereikt.
- Downloaden geslaagd. Als dit mislukt, wordt het `DeploymentMaxFailureCount` maximum aantal nieuwe pogingen bereikt.
- Een code package die is vastgelopen, start en registreert de service type.

`ActivationMaxFailureCount` en `DeploymentMaxFailureCount` zijn het maximum aantal pogingen service Fabric om een toepassing op een knoop punt te activeren of te downloaden. Wanneer het maximum is bereikt, wordt de service type voor activering opnieuw door Service Fabric ingeschakeld. 

Deze drempel waarden bieden de service een andere mogelijkheid om te activeren. Als de activering slaagt, wordt het probleem automatisch hersteld. 

Een nieuw geplaatst of geactiveerde replica kan een nieuwe activerings-of download bewerking activeren. Deze actie slaagt of trigger de service type blocklisting opnieuw.

> [!NOTE]
> Een crash-code package dat geen service type registreert, heeft geen invloed op de service type. Alleen een crash-code package die als host fungeert voor een replica, is van invloed op de service type.
>

### <a name="codepackage-crash"></a>Code package vastlopen
Wanneer een code package vastloopt, gebruikt Service Fabric een uitstel om het opnieuw te starten. De uitstel wordt toegepast, ongeacht of de code package een type heeft geregistreerd bij de Service Fabric-runtime.

De uitstel-waarde is `Min(RetryTime, ActivationMaxRetryInterval)` . De waarde wordt verhoogd in constante, lineaire of exponentiële bedragen op basis van de `ActivationRetryBackoffExponentiationBase` configuratie-instelling:

- **Constante**: if `ActivationRetryBackoffExponentiationBase == 0` , then `RetryTime = ActivationRetryBackoffInterval` .
- **Lineair**: als  `ActivationRetryBackoffExponentiationBase == 0` , dan `RetryTime = ContinuousFailureCount ActivationRetryBackoffInterval` `ContinuousFailureCount` is het aantal keren dat een code package vastloopt of niet kan worden geactiveerd.
- **Exponentieel**: `RetryTime = (ActivationRetryBackoffInterval in seconds) * (ActivationRetryBackoffExponentiationBase ^ ContinuousFailureCount)` .
    
U kunt het gedrag beheren door de waarden te wijzigen. Als u bijvoorbeeld meerdere snelle herstart pogingen wilt, kunt u lineaire bedragen gebruiken door `ActivationRetryBackoffExponentiationBase` in te stellen op `0` en `ActivationRetryBackoffInterval` in te stellen op `10` . Dus als een code package vastloopt, zal het begin interval na 10 seconden liggen. Als het pakket blijft vastlopen, wordt de uitstel gewijzigd in 20 seconden, 30 seconden, 40 seconden, enzovoort, totdat de activering van code package slaagt of de code package wordt gedeactiveerd. 
    
De maximale tijds duur die Service Fabric back-ups (dat wil zeggen, wacht) nadat een storing is onderhevig door `ActivationMaxRetryInterval` .
    
Als uw code package vastloopt en er een back-up van wordt gemaakt, moet de periode worden bewaard die is opgegeven door `CodePackageContinuousExitFailureResetInterval` . Na dit interval Service Fabric de code package in orde beschouwd. Service Fabric wordt vervolgens het rapport voor de vorige fout status overschreven als OK en wordt opnieuw ingesteld `ContinuousFailureCount` .

### <a name="codepackage-not-registering-servicetype"></a>Code package niet registreren voor service type
Als een code package blijft actief en verwacht een service type te registreren, maar dit niet het geval is, genereert Service Fabric een waarschuwings status rapport na de `ServiceTypeRegistrationTimeout` . Het rapport geeft aan dat Service type niet is geregistreerd binnen de verwachte hoeveelheid tijd.

### <a name="activation-failure"></a>Activerings fout
Wanneer Service Fabric tijdens de activering een fout vindt, wordt altijd een lineaire uitstel gebruikt, zoals het probleem treedt op bij een code package crash. De activerings bewerking geeft de volgende intervallen een nieuwe poging: (0 + 10 + 20 + 30 + 40) = 100 seconden. (De eerste nieuwe poging is direct.) Na deze reeks wordt de activering niet opnieuw geprobeerd.
    
De maximale activerings uitstel kan zijn `ActivationMaxRetryInterval` . Dit kan een nieuwe poging zijn `ActivationMaxFailureCount` .

### <a name="download-failure"></a>Fout bij het downloaden
Service Fabric maakt altijd gebruik van een lineaire uitstel wanneer er een fout wordt aangetroffen tijdens het downloaden. De activerings bewerking geeft de volgende intervallen een nieuwe poging: (0 + 10 + 20 + 30 + 40) = 100 seconden. (De eerste nieuwe poging is direct.) Na deze reeks wordt de down load niet opnieuw geprobeerd. 

De lineaire uitstel voor een down load is gelijk aan `ContinuousFailureCount`  *  `DeploymentRetryBackoffInterval` . De maximale uitstel kan zijn `DeploymentMaxRetryInterval` . Net als bij activeringen kunnen Download bewerkingen de limiet opnieuw proberen `ActivationMaxFailureCount` .

> [!NOTE]
> Houd bij het wijzigen van deze instellingen de volgende voor beelden in acht:
>
>* Als de code package vastloopt en er een back-up van maakt, wordt de service type uitgeschakeld. Maar als de activerings configuratie snel opnieuw moet worden opgestart, kan de code package een paar keer duren voordat de service type daad werkelijk wordt blocklisted. 
>
>    Stel bijvoorbeeld dat uw code package beschikbaar is, registreert u het Service type met Service Fabric en loopt vervolgens vast. In dat geval wordt na het hosten van een type registratie de `ServiceTypeDisableGraceInterval` periode geannuleerd. Dit proces kan worden herhaald totdat uw code package terugkeert naar een waarde die groter is dan de `ServiceTypeDisableGraceInterval` periode. Vervolgens wordt het Service type op het knoop punt blocklisted. De service type-blocklisting kan langer duren dan verwacht.
>
>* Wanneer een Service Fabric systeem een replica op een knoop punt moet plaatsen, wordt in het geval van activeringen het hosting subsysteem gevraagd om de toepassing te activeren. De aanvraag wordt elke 15 seconden opnieuw geprobeerd om te activeren. (De duur ligt onder de `RAPMessageRetryInterval` configuratie-instelling.) Service Fabric weet niet dat het Service type is blocklisted, tenzij de activerings bewerking in hosting langer duurt dan het interval voor nieuwe pogingen en de `ServiceTypeDisableGraceInterval` . 
>
>    Stel bijvoorbeeld dat de cluster `ActivationMaxFailureCount` is ingesteld op 5 en `ActivationRetryBackoffInterval` is ingesteld op 1 seconde. In dit geval geeft de activerings bewerking na intervallen van (0 + 1 + 2 + 3 + 4) = 10 seconden. (De eerste nieuwe poging is direct.) Na deze reeks wordt de hosting opnieuw geprobeerd. De activerings bewerking is voltooid en zal na 15 seconden niet opnieuw worden uitgevoerd. 
>
>    Service Fabric heeft alle toegestane nieuwe pogingen binnen 15 seconden uitgeput. Elk nieuwe poging van de agent voor opnieuw configureren maakt dus een nieuwe activerings bewerking in het subsysteem voor hosting en het patroon wordt herhaaldelijk herhaald. Als gevolg hiervan wordt het Service type nooit blocklisted op het knoop punt. Omdat het Service type niet wordt blocklisted op het knoop punt, wordt de replica niet verplaatst en geprobeerd op een ander knoop punt.
> 

## <a name="deactivation"></a>Deactivering

Wanneer een ServicePackage wordt geactiveerd op een knoop punt, wordt deze bijgehouden voor deactivering. Deactivering werkt op twee manieren:

- **Periodieke deactivering**: bij elk `DeactivationScanInterval` wordt door het systeem gecontroleerd op service pakketten die *nooit* een replica hebben gehost en deze markeren als kandidaten voor deactivering.
- **ReplicaClose deactiveren**: als een replica is gesloten, haalt de Deactivator een `DecrementUsageCount` . Een aantal is 0 wanneer de ServicePackage geen replica host, dus de ServicePackage is een kandidaat voor deactivering.

De activerings modus bepaalt wanneer kandidaten zijn gepland voor deactivering. In de gedeelde modus worden kandidaten voor deactivering gepland na de `DeactivationGraceInterval` . In de exclusieve modus worden ze gepland na de `ExclusiveModeDeactivationGraceInterval` . Als er tussen deze tijden een nieuwe replica wordt bereikt, wordt de deactivering geannuleerd. 

Zie [exclusieve modus en gedeelde modus][a2]voor meer informatie.

### <a name="periodic-deactivation"></a>Periodieke deactivering
Hier volgen enkele voor beelden van periodieke deactivering:

* **Voor beeld 1**: Stel dat bij het Deactiveer een scan op tijdstip T (de `DeactivationScanInterval` ) wordt gestart. De volgende scan is op 2T. Stel dat er een ServicePackage-activering is opgetreden op T + 1. In deze ServicePackage wordt geen replica gehost. deze moet dus worden gedeactiveerd. 

    Als u een kandidaat wilt maken voor deactivering, moet de ServicePackage ten minste over de tijd een replica hosten. Het komt in aanmerking voor deactivering op 2T + 1. Daarom kan de scan bij 2T deze ServicePackage niet identificeren als een kandidaat voor deactivering. 

    Bij de volgende deactiverings cyclus, 3T gebruiken, wordt deze ServicePackage gepland voor het deactiveren, omdat het pakket zich nu in een status zonder replica bevinden voor tijd T.  

* **Voor beeld 2**: Stel dat een ServicePackage wordt geactiveerd op het moment van t-1 en dat het deactiveren van een scan wordt gestart op t. De ServicePackage host geen replica. Bij de volgende scan is 2T de ServicePackage geïdentificeerd als een kandidaat voor deactivering, zodat deze wordt gepland voor deactivering.  

* **Voor beeld 3**: Stel dat een ServicePackage wordt geactiveerd op t-1 en dat de Deactivator een scan op t start. De ServicePackage host nog geen replica. Nu op T + 1, wordt een replica geplaatst. Dat wil zeggen dat hosting een `IncrementUsageCount` , wat betekent dat er een replica wordt gemaakt. 

    Op 2T wordt deze ServicePackage niet gepland voor deactivering. Omdat het pakket een replica bevat, wordt de deactivering gevolgd door de ReplicaClose-logica, zoals in de volgende sectie in dit artikel wordt uitgelegd.

* **Voor beeld 4**: Stel dat uw SERVICEPACKAGE 10 GB is. Omdat het pakket groot is, duurt het downloaden van het knoop punt. Wanneer een toepassing wordt geactiveerd, wordt de levens cyclus van de Deactivator getraceerd. Als de `DeactivationScanInterval` is ingesteld op een kleine waarde, heeft uw ServicePackage mogelijk geen tijd om te activeren op het knoop punt vanwege de tijd die nodig is om te downloaden. Als u dit probleem wilt verhelpen, kunt u [de ServicePackage van tevoren downloaden op het knoop punt][p1] of de verg Roten `DeactivationScanInterval` . 

> [!NOTE]
> Een ServicePackage kan overal worden gedeactiveerd tussen ( `DeactivationScanInterval` tot 2 * `DeactivationScanInterval` ) + `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` . 
>

### <a name="replicaclose-deactivation"></a>ReplicaClose deactiveren

> [!NOTE]
> Deze sectie verwijst naar de volgende configuratie-instellingen:
> - **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**: de tijd die aan een ServicePackage wordt gegeven om een andere replica te hosten als er al een replica wordt gehost. 
> - **DeactivationScanInterval**: de minimale tijd die aan een ServicePackage wordt gegeven voor het hosten van een replica als deze *nooit* een replica heeft gehost, dat wil zeggen, als deze nog niet is gebruikt.
>

Het systeem houdt het aantal Replica's bij dat een ServicePackage bevat. Als een ServicePackage een replica houdt en de replica is gesloten of niet beschikbaar is, wordt er een door gehost `DecrementUsageCount` . Wanneer een replica wordt geopend, wordt er een door gehost `IncrementUsageCount` . 

De verkleining geeft aan dat het aantal Replica's waarvan de ServicePackage wordt gehost, met één replica is gereduceerd. Wanneer het aantal wordt neergezet op 0, wordt de ServicePackage gepland voor deactivering. De tijd waarna deze wordt gedeactiveerd `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` . 

Stel bijvoorbeeld dat een verlaging plaatsvindt op T en dat de ServicePackage is gepland om 2T + X () te deactiveren `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` . Als er tijdens deze periode een host wordt `IncrementUsage` gemaakt, wordt de deactivering geannuleerd.

### <a name="ctrl--c"></a>Ctrl + C
Als een ServicePackage wordt door gegeven `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` aan de en nog steeds geen host is van een replica, kan de deactivering niet worden geannuleerd. CodePackages een CTRL + C-handler ontvangen. U moet de pijp lijn voor deactivering nu volt ooien om het proces uit te voeren. 

Als er gedurende deze tijd een nieuwe replica voor dezelfde ServicePackage wordt geprobeerd te worden geplaatst, mislukt dit omdat het proces geen overgang kan doen van deactivering naar activering.

## <a name="cluster-configurations"></a>Clusterconfiguraties

In deze sectie worden configuraties weer gegeven met standaard instellingen die van invloed zijn op activering en deactivering.

### <a name="servicetype"></a>ServiceType
- **ServiceTypeDisableFailureThreshold**: standaard: 1. De drempel waarde voor het aantal fouten; Nadat deze drempel waarde is bereikt, wordt FailoverManager op de hoogte gesteld om het Service type op het knoop punt uit te scha kelen en een ander knoop punt voor plaatsing te proberen.
- **ServiceTypeDisableGraceInterval**: standaard: 30 seconden. Tijds interval waarna het Service type kan worden uitgeschakeld.
- **ServiceTypeRegistrationTimeout**: standaard: 300 seconden. Time-out voor registratie van de service type bij Service Fabric.

### <a name="activation"></a>Activering
- **ActivationRetryBackoffInterval**: standaard: 10 seconden. Uitstel-interval voor elke activerings fout.
- **ActivationMaxFailureCount**: standaard: 20. Maximum aantal waarvoor het systeem een mislukte activering opnieuw probeert uit te voeren voordat het wordt opgedaan. 
- **ActivationRetryBackoffExponentiationBase**: standaard: 1,5.
- **ActivationMaxRetryInterval**: standaard: 3.600 seconden. Maximale uitstel-interval voor opnieuw proberen van de activering na storingen.
- **CodePackageContinuousExitFailureResetInterval**: standaard: 300 seconden. Time-outinterval voor het opnieuw instellen van het aantal doorlopende afsluit fouten voor de code package.

### <a name="download"></a>Downloaden
- **DeploymentRetryBackoffInterval**: standaard: 10. Uitstel-interval voor de implementatie fout.
- **DeploymentMaxRetryInterval**: standaard: 3.600 seconden. Het maximale uitstel-interval voor de implementatie na storingen.
- **DeploymentMaxFailureCount**: standaard: 20. De toepassings implementatie wordt opnieuw uitgevoerd voor `DeploymentMaxFailureCount` tijden voordat de implementatie van die toepassing op het knoop punt is mislukt.

### <a name="deactivation"></a>Deactivering
- **DeactivationScanInterval**: standaard: 600 seconden. De minimale tijd die aan de ServicePackage wordt gegeven om een replica te hosten als deze nooit een replica heeft gehost (dat wil zeggen, als deze niet wordt gebruikt).
- **DeactivationGraceInterval**: standaard: 60 seconden. In een model voor *gedeeld* proces is de tijd die aan een ServicePackage is toegewezen om een andere replica te hosten nadat deze al een replica heeft gehost.
- **ExclusiveModeDeactivationGraceInterval**: standaard: 1 seconde. In een *exclusief* proces model is de tijd die aan een ServicePackage is toegewezen om een andere replica te hosten nadat deze al een replica heeft gehost.

## <a name="next-steps"></a>Volgende stappen

- [Een toepassing inpakken][a3] en deze voorbereiden om te implementeren.
- [Toepassingen implementeren en verwijderen][a4] in Power shell.

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
