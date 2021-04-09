---
title: Toepassings levenscyclus in Service Fabric
description: Hierin wordt beschreven hoe u Service Fabric toepassingen ontwikkelt, implementeert, test, bijwerkt en verwijdert.
ms.topic: conceptual
ms.date: 1/19/2018
ms.openlocfilehash: ae0c79cdaafc8fc016d463a01046f0a02121330a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785732"
---
# <a name="service-fabric-application-lifecycle"></a>Service Fabric-toepassingslevenscyclus
Net als bij andere platforms gaat een toepassing op Azure Service Fabric meestal door de volgende fasen: ontwerpen, ontwikkelen, testen, implementeren, bijwerken, onderhoud en verwijderen. Service Fabric biedt eersteklas ondersteuning voor de volledige levens cyclus van toepassingen van Cloud toepassingen, van ontwikkeling tot implementatie tot en met het dagelijks beheer en het onderhoud om te voor komen dat ze buiten gebruik worden gesteld. Het service model maakt het mogelijk dat verschillende rollen onafhankelijk deel nemen in de levens cyclus van de toepassing. Dit artikel bevat een overzicht van de Api's en hoe deze worden gebruikt door de verschillende rollen in de fasen van de levens cyclus van de Service Fabric-toepassing.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-sfctl.md)]

## <a name="service-model-roles"></a>Service model rollen
De functies van het service model zijn:

* **Service ontwikkelaar**: ontwikkelt modulaire en algemene services die kunnen worden gebruikt in meerdere toepassingen van hetzelfde type of verschillende typen. Een wachtrij service kan bijvoorbeeld worden gebruikt voor het maken van een ticket toepassing (Help Desk) of een e-commerce-toepassing (winkel wagen).
* **Application Developer**: maakt toepassingen door een verzameling services te integreren om te voldoen aan bepaalde specifieke vereisten of scenario's. Een e-commerce website kan bijvoorbeeld de status ' JSON stateless Front-End-service ', ' veiling stateful service ' en ' Queueive service ' integreren om een oplossing voor veilingen te maken.
* **Toepassings beheerder**: maakt besluiten voor de configuratie van de toepassing (het invullen van de para meters van de configuratie sjabloon), implementatie (toewijzing aan beschik bare resources) en Quality of service. Een toepassings beheerder besluit bijvoorbeeld de taal land instelling (Engels voor de Verenigde Staten of Japans voor Japan) van de toepassing. Een andere geïmplementeerde toepassing kan verschillende instellingen hebben.
* **Operator**: implementeert toepassingen op basis van de toepassings configuratie en vereisten die zijn opgegeven door de toepassings beheerder. Een operator kan bijvoorbeeld de toepassing inrichten en implementeren en ervoor zorgen dat deze wordt uitgevoerd in Azure. Opera tors bewaken informatie over de status en prestaties van de toepassing en behouden de fysieke infra structuur als dat nodig is.

## <a name="develop"></a>Ontwikkelen
1. Een *service ontwikkelaar* ontwikkelt verschillende soorten services met behulp van het [reliable actors](service-fabric-reliable-actors-introduction.md) of [reliable Services](service-fabric-reliable-services-introduction.md) programmeer model.
2. Een *service ontwikkelaar* beschrijft op declaratieve wijze de ontwikkelde service typen in een service manifest bestand dat bestaat uit een of meer code, configuratie en gegevens pakketten.
3. Een *ontwikkelaar van toepassingen* bouwt vervolgens een toepassing met behulp van verschillende service typen.
4. Een *ontwikkelaar* van een toepassing beschrijft het toepassings type in een toepassings manifest door te verwijzen naar de service manifesten van de samenstellende diensten en de parameterizing van de verschillende configuratie-en implementatie-instellingen van de onderdeel Services te vervangen.

Zie [aan de slag met reliable actors](service-fabric-reliable-actors-get-started.md) en aan de [slag met reliable Services](service-fabric-reliable-services-quick-start.md) voor beelden.

## <a name="deploy"></a>Implementeren
1. Een *toepassings beheerder* staart het toepassings type toe aan een specifieke toepassing die moet worden geïmplementeerd in een service Fabric cluster door de juiste para meters van het **Application type** -element op te geven in het manifest van de toepassing.
2. Een *operator* uploadt het toepassings pakket naar het cluster installatie kopie archief met behulp van de [methode **CopyApplicationPackage**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) of de [cmdlet **copy-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/copy-servicefabricapplicationpackage). Het toepassings pakket bevat het toepassings manifest en de verzameling van service pakketten. Service Fabric implementeert toepassingen uit het toepassings pakket dat is opgeslagen in het archief met installatie kopieën. Dit kan een Azure Blob Store of de Service Fabric systeem-service zijn.
3. De *operator* voorziet vervolgens het toepassings type in het doel cluster van het geüploade toepassings pakket met behulp van de [ **ProvisionApplicationAsync** -methode](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), de [ **REGI ster-ServiceFabricApplicationType** -cmdlet](/powershell/module/servicefabric/register-servicefabricapplicationtype)of het [ **inrichten van een toepassing** rest-bewerking](/rest/api/servicefabric/provision-an-application).
4. Na het inrichten van de toepassing start een *operator* de toepassing met de para meters die door de *toepassings beheerder* zijn opgegeven met de [methode **CreateApplicationAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), de [cmdlet **New-ServiceFabricApplication**](/powershell/module/servicefabric/new-servicefabricapplication)of de [bewerking **toepassing maken** rest](/rest/api/servicefabric/create-an-application).
5. Nadat de toepassing is geïmplementeerd, gebruikt een *operator* de methode [ **CreateServiceAsync**](/dotnet/api/system.fabric.fabricclient.servicemanagementclient), de cmdlet [ **New-ServiceFabricService**](/powershell/module/servicefabric/new-servicefabricservice)of de [bewerking **service maken**](/rest/api/servicefabric/create-a-service) voor het maken van nieuwe service-exemplaren voor de toepassing op basis van de beschik bare service typen.
6. De toepassing wordt nu uitgevoerd in het Service Fabric cluster.

Zie [een toepassing implementeren](service-fabric-deploy-remove-applications.md) voor voor beelden.

## <a name="test"></a>Testen
1. Na de implementatie van het lokale ontwikkelings cluster of een test cluster, voert een *service ontwikkelaar* het ingebouwde failover-test scenario uit met behulp van de klassen [**FailoverTestScenarioParameters**](/dotnet/api/system.fabric.testability.scenario.failovertestscenarioparameters) en [**FailoverTestScenario**](/dotnet/api/system.fabric.testability.scenario.failovertestscenario) , of met de [cmdlet **invoke-ServiceFabricFailoverTestScenario**](/powershell/module/servicefabric/invoke-servicefabricfailovertestscenario). Het test scenario voor de failover voert een opgegeven service uit door middel van belang rijke overgangen en failovers om ervoor te zorgen dat deze nog steeds beschikbaar is en goed werkt.
2. De *service ontwikkelaar* voert vervolgens het ingebouwde chaos-test scenario uit met behulp van de klassen [**ChaosTestScenarioParameters**](/dotnet/api/system.fabric.testability.scenario.chaostestscenarioparameters) en [**ChaosTestScenario**](/dotnet/api/system.fabric.testability.scenario.chaostestscenario) , of met de [cmdlet **invoke-ServiceFabricChaosTestScenario**](/powershell/module/servicefabric/invoke-servicefabricchaostestscenario). Het chaos-test scenario veroorzaakt wille keurig meerdere knoop punten, code pakketten en replica fouten in het cluster.
3. De *service ontwikkelaar* test [service-to-service-communicatie](service-fabric-testability-scenarios-service-communication.md) door test scenario's te ontwerpen waarmee primaire replica's rond het cluster worden verplaatst.

Zie [Inleiding tot de fout analyse service](service-fabric-testability-overview.md) voor meer informatie.

## <a name="upgrade"></a>Upgraden
1. Een *service ontwikkelaar* werkt de onderdeel Services van de geïnstantieerd toepassing bij en/of herstelt bugs en biedt een nieuwe versie van het service manifest.
2. Een *toepassings ontwikkelaar* overschrijft en parameterizes de configuratie-en implementatie-instellingen van de consistente Services en biedt een nieuwe versie van het toepassings manifest. De ontwikkelaar van de toepassing neemt vervolgens de nieuwe versies van de service manifesten in de toepassing op en biedt een nieuwe versie van het toepassings type in een bijgewerkt toepassings pakket.
3. Een *toepassings beheerder* neemt de nieuwe versie van het toepassings type op in de doel toepassing door de juiste para meters bij te werken.
4. Een *operator* uploadt het bijgewerkte toepassings pakket naar het cluster installatie kopie archief met behulp van de [methode **CopyApplicationPackage**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) of de [cmdlet **copy-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/copy-servicefabricapplicationpackage). Het toepassings pakket bevat het toepassings manifest en de verzameling van service pakketten.
5. Een *operator* richt zich op de nieuwe versie van de toepassing in het doel cluster met behulp van de [ **ProvisionApplicationAsync** -methode](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), de [ **cmdlet REGI ster-ServiceFabricApplicationType**](/powershell/module/servicefabric/register-servicefabricapplicationtype)of het [ **inrichten van een toepassing** rest-bewerking](/rest/api/servicefabric/provision-an-application).
6. Een *operator* voert een upgrade uit van de doel toepassing naar de nieuwe versie met behulp van de [ **UpgradeApplicationAsync** -methode](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), de [ **Start-ServiceFabricApplicationUpgrade** -cmdlet](/powershell/module/servicefabric/start-servicefabricapplicationupgrade)of de [ **upgrade van een toepassings** rest-bewerking](/rest/api/servicefabric/upgrade-an-application).
7. Een *operator* controleert de voortgang van de upgrade met de [methode **GetApplicationUpgradeProgressAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), de [cmdlet **Get-ServiceFabricApplicationUpgrade**](/powershell/module/servicefabric/get-servicefabricapplicationupgrade)of de bewerking voor de voortgang van de upgrade van de [ **toepassing ophalen**](/rest/api/servicefabric/get-the-progress-of-an-application-upgrade1).
8. Indien nodig, wijzigt de *operator* de para meters van de huidige toepassings upgrade en past deze opnieuw toe met de [methode **UpdateApplicationUpgradeAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), de [cmdlet **Update-ServiceFabricApplicationUpgrade**](/powershell/module/servicefabric/update-servicefabricapplicationupgrade)of de rest-bewerking voor het bijwerken van de [ **toepassing**](/rest/api/servicefabric/update-an-application-upgrade).
9. Indien nodig wordt de huidige toepassings upgrade *teruggedraaid met* de [methode **RollbackApplicationUpgradeAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), de [cmdlet **Start-ServiceFabricApplicationRollback**](/powershell/module/servicefabric/start-servicefabricapplicationrollback)of de rest-bewerking voor het bijwerken van de [ **toepassing**](/rest/api/servicefabric/rollback-an-application-upgrade).
10. Service Fabric de doel toepassing die in het cluster wordt uitgevoerd, wordt bijgewerkt zonder dat de beschik baarheid van de bijbehorende samenstellende Services verloren gaat.

Raadpleeg de [zelf studie over de toepassings upgrade](service-fabric-application-upgrade-tutorial.md) voor voor beelden.

## <a name="maintain"></a>Onderhouden
1. Voor upgrades en patches van besturings systemen Service Fabric-interfaces met de Azure-infra structuur om de beschik baarheid te garanderen van alle toepassingen die in het cluster worden uitgevoerd.
2. Voor upgrades en patches naar het Service Fabric-platform Service Fabric upgrades uitvoeren zonder de beschik baarheid van de toepassingen die op het cluster worden uitgevoerd, te verliezen.
3. Een *toepassings beheerder* keurt het toevoegen of verwijderen van knoop punten uit een cluster na het analyseren van de gegevens over het gebruik van historische capaciteit en de verwachte toekomstige vraag.
4. Met een *operator* worden knoop punten toegevoegd en verwijderd die zijn opgegeven door de *toepassings beheerder*.
5. Wanneer er nieuwe knoop punten worden toegevoegd aan of bestaande knoop punten uit het cluster worden verwijderd, Service Fabric automatisch de taak verdeling van de actieve toepassingen op alle knoop punten in het cluster voor optimale prestaties.

## <a name="remove"></a>Verwijderen
1. Een *operator* kan een specifiek exemplaar van een actieve service in het cluster verwijderen zonder de volledige toepassing te verwijderen met [de methode **DeleteServiceAsync**](/dotnet/api/system.fabric.fabricclient.servicemanagementclient), de [ **cmdlet Remove-ServiceFabricService**](/powershell/module/servicefabric/remove-servicefabricservice)of de [rest-bewerking **service verwijderen**](/rest/api/servicefabric/delete-a-service).  
2. Een *operator* kan ook een toepassings exemplaar en alle bijbehorende services verwijderen met de methode [ **DeleteApplicationAsync**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), de cmdlet [ **Remove-ServiceFabricApplication**](/powershell/module/servicefabric/remove-servicefabricapplication)of de [rest bewerking **toepassing verwijderen**](/rest/api/servicefabric/delete-an-application).
3. Zodra de toepassing en services zijn gestopt *, kan de* inrichting van het toepassings type ongedaan worden gemaakt met behulp van de [ **UnprovisionApplicationAsync** -methode](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), de [ **unregister-ServiceFabricApplicationType-** cmdlet](/powershell/module/servicefabric/unregister-servicefabricapplicationtype)of de inrichting van [een bewerking voor **een toepassings** rest](/rest/api/servicefabric/unprovision-an-application)verwijderen. Het ongedaan maken van de inrichting van het toepassings type verwijdert het toepassings pakket niet uit de installatie kopie opslag. U moet het toepassings pakket hand matig verwijderen.
4. Een *operator* verwijdert het toepassings pakket uit de installatie kopie opslag met behulp van de [methode **RemoveApplicationPackage**](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) of de [cmdlet **Remove-ServiceFabricApplicationPackage**](/powershell/module/servicefabric/remove-servicefabricapplicationpackage).

Zie [een toepassing implementeren](service-fabric-deploy-remove-applications.md) voor voor beelden.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over het ontwikkelen, testen en beheren van Service Fabric-toepassingen en-services:

* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Een app implementeren](service-fabric-deploy-remove-applications.md)
* [Toepassingsupgrade](service-fabric-application-upgrade.md)
* [Overzicht van test baarheid](service-fabric-testability-overview.md)
