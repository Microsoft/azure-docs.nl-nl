---
title: Hosting model voor Azure Service Fabric
description: Hierin wordt de relatie tussen replica's (of exemplaren) van een geïmplementeerde Service Fabric-service en het proces van de service-host beschreven.
author: harahma
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: harahma
ms.custom: devx-track-csharp
ms.openlocfilehash: 9932c11332a616928d59c213d4f4806feb81cfe2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791642"
---
# <a name="azure-service-fabric-hosting-model"></a>Hosting model voor Azure Service Fabric
Dit artikel bevat een overzicht van de toepassings hosting modellen van Azure Service Fabric en beschrijft de verschillen tussen de modellen **gedeeld proces** en **exclusief proces** . Hierin wordt beschreven hoe een geïmplementeerde toepassing eruitziet op een Service Fabric knoop punt en de relatie tussen replica's (of exemplaren) van de service en het proces-hostproces.

Voordat u verder gaat, moet u de verschillende concepten en relaties begrijpen die worden uitgelegd in [model a Application in service Fabric][a1]. 

> [!NOTE]
> In dit artikel, tenzij expliciet vermeld als iets anders:
>
> - *Replica* verwijst naar een replica van een stateful service en een exemplaar van een stateless service.
> - *Code package* wordt behandeld als equivalent aan een *ServiceHost* -proces dat een *service* type registreert en replica's van services van die *service* type host.
>

Laten we een voor beeld door lopen om inzicht te krijgen in het hosting model. Stel dat we een *Application type* ' MyAppType ' hebben, dat een *service* type ' MyServiceType ' heeft. ' MyServiceType ' wordt gegeven door de *ServicePackage* ' MyServicePackage ', die een *code package* ' MyCodePackage ' heeft. ' MyCodePackage ' registreert *service* type ' MyServiceType ' wanneer het wordt uitgevoerd.

Stel dat we een cluster met drie knoop punten hebben en dat we een *toepassings* **infrastructuur maken:/App1** van het type MyAppType. In deze Application **Fabric:/App1** maakt u een service **Fabric:/App1/servicea** van het type MyServiceType. Deze service heeft twee partities (bijvoorbeeld **P1** en **P2**) en drie replica's per partitie. In het volgende diagram ziet u de weer gave van deze toepassing, wanneer deze op een knoop punt wordt geïmplementeerd.


![Diagram waarin de weer gave van deze toepassing wordt weer gegeven wanneer deze op een knoop punt wordt geïmplementeerd.][node-view-one]


Service Fabric geactiveerd: ' MyServicePackage ', waarbij ' MyCodePackage ' is gestart, dat als host fungeert voor replica's van beide partities. Alle knoop punten in het cluster hebben dezelfde weer gave, omdat we het aantal replica's per partitie hebben gekozen om gelijk te zijn aan het aantal knoop punten in het cluster. We gaan een andere service, **Fabric:/App1/ServiceB** maken in de Application **Fabric:/App1**. Deze service heeft één partitie (bijvoorbeeld **P3**) en drie replica's per partitie. In het volgende diagram ziet u de nieuwe weer gave op het knoop punt:


![Diagram waarin de nieuwe weer gave van het knoop punt wordt weer gegeven.][node-view-two]


Service Fabric de nieuwe replica voor partitie **P3** van service **Fabric:/App1/ServiceB** in de bestaande activering van ' MyServicePackage ' geplaatst. Hierna. We gaan een andere toepassings **infrastructuur maken:/App2** van het type MyAppType. In **Fabric:/App2** maakt u een service **Fabric:/App2/servicea**. Deze service heeft twee partities (**P4** en **P5**) en drie replica's per partitie. In het volgende diagram ziet u de nieuwe knooppunt weergave:


![Diagram waarin de weer gave van het nieuwe knoop punt wordt weer gegeven.][node-view-three]


Service Fabric activeert een nieuwe kopie van ' MyServicePackage ', waarmee een nieuw exemplaar van ' MyCodePackage ' wordt gestart. Replica's van beide partities van service **Fabric:/App2/servicea** (**P4** en **P5**) worden in deze nieuwe kopie ' MyCodePackage ' geplaatst.

## <a name="shared-process-model"></a>Model voor gedeeld proces
In de voor gaande sectie wordt het standaard hosting model beschreven dat wordt verschaft door Service Fabric, aangeduid als het model voor gedeelde processen. In dit model, voor een bepaalde toepassing, wordt slechts één exemplaar van een bepaalde *ServicePackage* geactiveerd op een knoop punt (waarmee alle *CodePackages* in de app worden gestart). Alle replica's van alle services van een opgegeven *service* type worden in de *code package* geplaatst die de *service* type registreert. Met andere woorden, alle replica's van alle services op een knoop punt van een bepaalde *service* type delen hetzelfde proces.

## <a name="exclusive-process-model"></a>Model voor exclusief proces
Het andere hosting model van Service Fabric is het exclusieve proces model. In dit model bevindt elke replica zich op een bepaald knoop punt in een eigen toegewezen proces. Service Fabric activeert een nieuw exemplaar van *ServicePackage* (waarmee alle *CodePackages* worden gestart). Replica's worden in de *code package* geplaatst die *het Service type van de* service waartoe de replica behoort, heeft geregistreerd. 

Als u Service Fabric versie 5,6 of hoger gebruikt, kunt u het exclusieve proces model kiezen op het moment dat u een service maakt (met behulp van [Power shell][p1], [rest][r1]of [FabricClient][c1]). Geef **ServicePackageActivationMode** op als ' ExclusiveProcess '.

```powershell
PS C:\>New-ServiceFabricService -ApplicationName "fabric:/App1" -ServiceName "fabric:/App1/ServiceA" -ServiceTypeName "MyServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1 -ServicePackageActivationMode "ExclusiveProcess"
```

```csharp
var serviceDescription = new StatelessServiceDescription
{
    ApplicationName = new Uri("fabric:/App1"),
    ServiceName = new Uri("fabric:/App1/ServiceA"),
    ServiceTypeName = "MyServiceType",
    PartitionSchemeDescription = new SingletonPartitionSchemeDescription(),
    InstanceCount = -1,
    ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
};

var fabricClient = new FabricClient(clusterEndpoints);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Als u een standaard service in het manifest van de toepassing hebt, kunt u het exclusieve proces model kiezen door het kenmerk **ServicePackageActivationMode** op te geven:

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
We gaan nu een andere service, **Fabric:/App1/ServiceC** maken, in Application **Fabric:/App1**. Deze service heeft twee partities (bijvoorbeeld **P6** en **P7**) en drie replica's per partitie. U stelt **ServicePackageActivationMode** in op ' ExclusiveProcess '. In het volgende diagram ziet u de nieuwe weer gave op het knoop punt:


![Diagram van knooppunt weergave van geïmplementeerde toepassing][node-view-four]


Zoals u ziet, Service Fabric twee nieuwe exemplaren van ' MyServicePackage ' geactiveerd (één voor elke replica van partitie **P6** en **P7**). Service Fabric elke replica in het toegewezen exemplaar van *code package* geplaatst. Wanneer u het exclusieve proces model gebruikt voor een bepaalde toepassing, kunnen meerdere exemplaren van een bepaalde *ServicePackage* actief zijn op een knoop punt. In het voor gaande voor beeld zijn drie exemplaren van ' MyServicePackage ' actief voor **Fabric:/App1**. Aan elk van deze actieve kopieën van MyServicePackage is een **ServicePackageActivationId** gekoppeld. Deze ID identificeert die kopie in Application **Fabric:/App1**.

Wanneer u alleen het gedeelde proces model gebruikt voor een toepassing, is er slechts één actieve kopie van *ServicePackage* op een knoop punt. De **ServicePackageActivationId** voor deze activering van *ServicePackage* is een lege teken reeks. Dit is bijvoorbeeld het geval bij **Fabric:/App2**.

> [!NOTE]
>- Het model voor het hosten van gedeelde processen correspondeert met **ServicePackageActivationMode** is gelijk aan **SharedProcess**. Dit is het standaard hosting model en **ServicePackageActivationMode** hoeft niet te worden opgegeven op het moment dat de service wordt gemaakt.
>
>- Het exclusieve proces hosting model correspondeert met **ServicePackageActivationMode** is gelijk aan **ExclusiveProcess**. Als u deze instelling wilt gebruiken, moet u deze expliciet opgeven op het moment dat de service wordt gemaakt. 
>
>- Als u het hosting model van een service wilt weer geven, moet u een query uitvoeren op de [service beschrijving][p2]en de waarde van **ServicePackageActivationMode** bekijken.
>
>

## <a name="work-with-a-deployed-service-package"></a>Werken met een geïmplementeerd service pakket
Een actieve kopie van een *ServicePackage* op een knoop punt wordt een [geïmplementeerd service pakket][p3]genoemd. Wanneer u het exclusieve proces model voor het maken van services gebruikt voor een bepaalde toepassing, zijn er mogelijk meerdere geïmplementeerde service pakketten voor dezelfde *ServicePackage*. Als u bewerkingen uitvoert die specifiek zijn voor een geïmplementeerd service pakket, moet u **ServicePackageActivationId** opgeven voor het identificeren van een specifiek geïmplementeerd service pakket. Geef bijvoorbeeld de ID op als u [de status van een geïmplementeerd service pakket wilt rapporteren][p4] of [het code pakket van een geïmplementeerd service pakket opnieuw wilt opstarten][p5].

U kunt de **ServicePackageActivationId** van een geïmplementeerd service pakket vinden door de lijst met [geïmplementeerde service pakketten][p3] op een knoop punt op te vragen. Wanneer u een query uitvoert voor de [geïmplementeerde service typen][p6], [geïmplementeerde replica's][p7]en [geïmplementeerde code pakketten][p8] op een knoop punt, bevat het query resultaat ook de **ServicePackageActivationId** van het bovenliggende geïmplementeerde service pakket.

> [!NOTE]
>- Onder het model voor gedeeld proces hosting op een bepaald knoop punt voor een bepaalde toepassing wordt slechts één exemplaar van een *ServicePackage* geactiveerd. De **ServicePackageActivationId** is gelijk aan een *lege teken reeks* en hoeft niet te worden opgegeven tijdens het uitvoeren van bewerkingen met betrekking tot het geïmplementeerde service pakket. 
>
> - Onder het exclusieve proces hosting model, op een bepaald knoop punt voor een bepaalde toepassing, kunnen een of meer exemplaren van een *ServicePackage* actief zijn. Elke activering heeft een *niet-lege* **ServicePackageActivationId**, opgegeven tijdens het uitvoeren van bewerkingen met betrekking tot het geïmplementeerde service pakket. 
>
> - Als **ServicePackageActivationId** wordt wegge laten, wordt standaard de *teken reeks leeg*. Als een geïmplementeerd service pakket dat is geactiveerd onder het model voor gedeelde processen aanwezig is, wordt de bewerking uitgevoerd. Anders mislukt de bewerking.
>
> - Voer een keer niet een query uit en cache de **ServicePackageActivationId**. De ID wordt dynamisch gegenereerd en kan om verschillende redenen worden gewijzigd. Voordat u een bewerking uitvoert waarvoor **ServicePackageActivationId** nodig is, moet u eerst de lijst met [geïmplementeerde service pakketten][p3] op een knoop punt opvragen. Gebruik vervolgens de **ServicePackageActivationId** uit het query resultaat om de oorspronkelijke bewerking uit te voeren.
>
>

## <a name="guest-executable-and-container-applications"></a>Uitvoer bare gast-en container toepassingen
Service Fabric worden [uitvoer bare gast][a2] -en [container][a3] toepassingen behandeld als stateless Services, die zich op zichzelf bevinden. Er is geen Service Fabric runtime in *ServiceHost* (een proces of container). Omdat deze services zich op zichzelf bevinden, is het aantal replica's per *ServiceHost* niet van toepassing op deze services. De meest voorkomende configuratie die met deze services wordt gebruikt, is één partitie, waarbij [InstanceCount][c2] gelijk is aan-1 (één exemplaar van de service code die wordt uitgevoerd op elk knoop punt van het cluster). 

De standaard **ServicePackageActivationMode** voor deze services zijn **SharedProcess**, in welk geval service Fabric slechts één exemplaar van *ServicePackage* activeert op een knoop punt voor een bepaalde toepassing.  Dit betekent dat er voor slechts één exemplaar van de service code een knoop punt wordt uitgevoerd. Als u wilt dat meerdere exemplaren van uw service code worden uitgevoerd op een knoop punt, geeft u **ServicePackageActivationMode** op als **ExclusiveProcess** op het moment dat de service wordt gemaakt. U kunt dit bijvoorbeeld doen wanneer u meerdere services (*Service1* *) van* *service* type (opgegeven in *ServiceManifest*) maakt of wanneer uw service multi-partitioneert. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>Het hosting model van een bestaande service wijzigen
Op dit moment kunt u het hosting model van een bestaande service niet wijzigen van een gedeeld proces in een exclusief proces (of andersom).

## <a name="choose-between-the-hosting-models"></a>Kiezen tussen de hosting modellen
U moet evalueren welk hosting model het beste past bij uw vereisten. Het model voor gedeeld proces maakt gebruik van bronnen van het besturings systeem beter, omdat er minder processen worden uitgevoerd en meerdere replica's in hetzelfde proces poorten kunnen delen. Als een van de replica's echter een fout bevat waarin de servicehost moet worden ingedrukt, heeft dit invloed op alle andere replica's in hetzelfde proces.

 Het model voor exclusieve processen biedt betere isolatie, waarbij elke replica in een eigen proces wordt uitgevoerd. Als een van de replica's een fout heeft, heeft dit geen invloed op andere replica's. Dit model is handig voor gevallen waarin het delen van poorten niet wordt ondersteund door het communicatie protocol. Het biedt de mogelijkheid om resource governance op replica niveau toe te passen. Het exclusieve proces verbruikt echter meer bronnen van het besturings systeem, omdat er één proces voor elke replica op het knoop punt wordt uitgevoerd.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Aandachtspunten voor exclusief proces model en toepassings model
Voor de meeste toepassingen kunt u uw toepassing in Service Fabric model leren door één *service* type per *ServicePackage* te behouden. 

In bepaalde gevallen biedt Service Fabric ook meer dan één *service* type per *ServicePackage* (en één *code package* kan meer dan één *service* type registreren). Hier volgen enkele van de scenario's waarin deze configuraties nuttig kunnen zijn:

- U wilt het resource gebruik optimaliseren door minder processen te produceren en een hogere replica-densiteit per proces te hebben.
- Replica's van verschillende *ServiceTypes* moeten enkele algemene gegevens delen die een hoge initialisatie of geheugen kosten hebben.
- U hebt een gratis service aanbieding en u wilt een limiet voor het gebruik van resources instellen door alle replica's van de service in hetzelfde proces te plaatsen.

Het exclusieve proces hosting model is niet coherent met een toepassings model met meerdere *ServiceTypes* per *ServicePackage*. De reden hiervoor is dat meerdere *ServiceTypes* per *ServicePackage* zijn ontworpen om het delen van een hoger niveau te garanderen tussen replica's en een hogere replica-densiteit mogelijk te maken per proces. Het exclusieve proces model is ontworpen om verschillende resultaten te behaalt.

Overweeg het geval van meerdere *ServiceTypes* per *ServicePackage*, waarbij een andere *code package* elke *service* type registreert. Stel dat we een *ServicePackage* ' MultiTypeServicePackage ' hebben, die twee *CodePackages* heeft:

- ' MyCodePackageA ', waarmee *service* type ' MyServiceTypeA ' wordt geregistreerd.
- ' MyCodePackageB ', waarmee *service* type ' MyServiceTypeB ' wordt geregistreerd.

Stel nu dat we een toepassing maken, **Fabric:/SpecialApp**. Binnen **Fabric:/SpecialApp** maken we twee services met het model voor exclusieve processen:

- Service **Fabric:/SpecialApp/servicea** van het type MyServiceTypeA, met twee partities (bijvoorbeeld **P1** en **P2**) en drie replica's per partitie.
- Service **Fabric:/SpecialApp/ServiceB** van het type MyServiceTypeB, met twee partities (**P3** en **P4**) en drie replica's per partitie.

Op een bepaald knoop punt hebben beide services twee replica's. Omdat we het exclusieve proces model voor het maken van de services hebben gebruikt, Service Fabric een nieuwe kopie van ' MyServicePackage ' geactiveerd voor elke replica. Elke activering van ' MultiTypeServicePackage ' Start een kopie van ' MyCodePackageA ' en ' MyCodePackageB '. Maar slechts één van ' MyCodePackageA ' of ' MyCodePackageB ' fungeert als host voor de replica waarvoor ' MultiTypeServicePackage ' is geactiveerd. In het volgende diagram ziet u de knooppunt weergave:


![Diagram waarin de knooppunt weergave wordt weer gegeven.][node-view-five]


Bij de activering van ' MultiTypeServicePackage ' voor de replica van partitie **P1** van service **Fabric:/SpecialApp/servicea**, ' MyCodePackageA ' fungeert als host voor de replica. ' MyCodePackageB ' wordt uitgevoerd. Op dezelfde manier wordt bij het activeren van ' MultiTypeServicePackage ' voor de replica van partitie **P3** van service **Fabric:/SpecialApp/ServiceB**, ' MyCodePackageB ' de replica gehost. ' MyCodePackageA ' wordt uitgevoerd. Daarom is het groter dan het aantal *CodePackages* (registreren van verschillende *ServiceTypes*) per *ServicePackage*, hoe hoger het redundante resource gebruik is. 
 
 Als we echter de services **Fabric:/SpecialApp/servicea** en **Fabric:/SpecialApp/ServiceB** met het gedeelde proces model maken, wordt service Fabric slechts één exemplaar van ' MultiTypeServicePackage ' geactiveerd voor de Application **Fabric:/SpecialApp**. ' MyCodePackageA ' fungeert als host voor alle replica's voor de service **Fabric:/SpecialApp/servicea**. ' MyCodePackageB ' fungeert als host voor alle replica's voor de service **Fabric:/SpecialApp/ServiceB**. In het volgende diagram ziet u de weer gave van het knoop punt in deze instelling: 


![Diagram van de knooppunt weergave van de geïmplementeerde toepassing][node-view-six]


In het vorige voor beeld zou u kunnen denken dat als ' MyCodePackageA ' zowel ' MyServiceTypeA ' als ' MyServiceTypeB ' registreert en er geen ' MyCodePackageB ' is, er geen redundante *code package* wordt uitgevoerd. Hoewel dit niet juist is, wordt dit toepassings model niet uitgelijnd met het exclusieve proces hosting model. Als het doel is om elke replica in een eigen toegewezen proces te plaatsen, hoeft u niet beide *ServiceTypes* van dezelfde *code package* te registreren. In plaats daarvan plaatst u elke *service* type in een eigen *ServicePackage*.

### <a name="reliable-services-and-actor-forking-subprocesses"></a>Subprocessen voor Reliable Services en actor vertakkingen

Service Fabric biedt geen ondersteuning voor betrouw bare Services en vervolgens betrouw bare Actors die subprocessen splitsen. Een voor beeld van waarom de niet-ondersteunde [CodePackageActivationContext](/dotnet/api/system.fabric.codepackageactivationcontext) niet kan worden gebruikt om een niet-ondersteund subproces te registreren en annulerings tokens worden alleen verzonden naar geregistreerde processen. wat resulteert in allerlei problemen, zoals upgrade fouten, wanneer subprocessen niet worden gesloten nadat het bovenliggende proces een annulerings token heeft ontvangen.

## <a name="next-steps"></a>Volgende stappen
[Een toepassing inpakken][a4] en deze voorbereiden om te implementeren.

[Toepassingen implementeren en verwijderen][a5]. In dit artikel wordt beschreven hoe u Power shell gebruikt voor het beheren van toepassings exemplaren.

<!--Image references-->
[node-view-one]: ./media/service-fabric-hosting-model/node-view-one.png
[node-view-two]: ./media/service-fabric-hosting-model/node-view-two.png
[node-view-three]: ./media/service-fabric-hosting-model/node-view-three.png
[node-view-four]: ./media/service-fabric-hosting-model/node-view-four.png
[node-view-five]: ./media/service-fabric-hosting-model/node-view-five.png
[node-view-six]: ./media/service-fabric-hosting-model/node-view-six.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-guest-executables-introduction.md
[a3]: service-fabric-containers-overview.md
[a4]: service-fabric-package-apps.md
[a5]: service-fabric-deploy-remove-applications.md

[r1]: /rest/api/servicefabric/sfclient-api-createservice

[c1]: /dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync
[c2]: /dotnet/api/system.fabric.description.statelessservicedescription.instancecount

[p1]: /powershell/module/servicefabric/new-servicefabricservice
[p2]: /powershell/module/servicefabric/get-servicefabricservicedescription
[p3]: /powershell/module/servicefabric/get-servicefabricdeployedservicepackage
[p4]: /powershell/module/servicefabric/send-servicefabricdeployedservicepackagehealthreport
[p5]: /powershell/module/servicefabric/restart-servicefabricdeployedcodepackage
[p6]: /powershell/module/servicefabric/get-servicefabricdeployedservicetype
[p7]: /powershell/module/servicefabric/get-servicefabricdeployedreplica
[p8]: /powershell/module/servicefabric/get-servicefabricdeployedcodepackage
