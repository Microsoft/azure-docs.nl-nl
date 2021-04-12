---
title: Upgrade van de app Service Fabric met behulp van Power shell
description: In dit artikel wordt uitgelegd hoe u een Service Fabric toepassing implementeert, hoe u de code wijzigt en een upgrade uitvoert met behulp van Power shell.
ms.topic: conceptual
ms.date: 8/5/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4b038f4a41ee02960a02c4445b65eb2360a75761
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98792028"
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>Upgrade van toepassing Service Fabric met behulp van Power shell
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

De meestgebruikte en aanbevolen upgrade aanpak is de bewaakte rolling upgrade.  Azure Service Fabric bewaakt de status van de toepassing die wordt bijgewerkt op basis van een set status beleid. Zodra een update domein (UD) is bijgewerkt, evalueert Service Fabric de status van de toepassing en gaat het naar het volgende update domein of mislukt de upgrade afhankelijk van het status beleid.

Een bewaakte toepassings upgrade kan worden uitgevoerd met behulp van de beheerde of systeem eigen Api's, Power shell, Azure CLI, Java of REST. Zie [uw toepassing upgraden met Visual Studio](service-fabric-application-upgrade-tutorial.md)voor instructies over het uitvoeren van een upgrade met Visual Studio.

Met Service Fabric bewaakte rolling upgrades kan de toepassings beheerder het status evaluatie beleid configureren dat Service Fabric gebruikt om te bepalen of de toepassing in orde is. Daarnaast kan de beheerder de actie configureren die moet worden uitgevoerd wanneer de status evaluatie mislukt (bijvoorbeeld door een automatische terugdraai bewerking uit te voeren). In deze sectie wordt een bewaakte upgrade door lopen voor een van de SDK-voor beelden die gebruikmaken van Power shell. 

> [!NOTE]
> [ApplicationParameter](/dotnet/api/system.fabric.description.applicationdescription.applicationparameters#System_Fabric_Description_ApplicationDescription_ApplicationParameters)s blijven niet behouden in een toepassings upgrade. Als u de huidige toepassings parameters wilt behouden, moet de gebruiker eerst de para meters ophalen en deze door geven aan de aanroep van de upgrade-API zoals hieronder wordt weer gegeven:
```powershell
$myApplication = Get-ServiceFabricApplication -ApplicationName fabric:/myApplication
$appParamCollection = $myApplication.ApplicationParameters

$applicationParameterMap = @{}
foreach ($pair in $appParamCollection)
{
    $applicationParameterMap.Add($pair.Name, $pair.Value);
}

Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/myApplication -ApplicationTypeVersion 2.0.0 -ApplicationParameter $applicationParameterMap -Monitored -FailureAction Rollback
```

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Stap 1: het voor beeld van Visual objecten bouwen en implementeren
Bouw en publiceer de toepassing door met de rechter muisknop te klikken op het toepassings project **VisualObjectsApplication** en de opdracht **Publish** te selecteren.  Zie [service Fabric-zelf studie](service-fabric-application-upgrade-tutorial.md)over de upgrade van toepassingen voor meer informatie.  U kunt ook Power shell gebruiken om uw toepassing te implementeren.

> [!NOTE]
> Voordat een van de Service Fabric-opdrachten in Power shell kan worden gebruikt, moet u eerst verbinding maken met het cluster met behulp van de- `Connect-ServiceFabricCluster` cmdlet. Ook wordt ervan uitgegaan dat het cluster al is ingesteld op uw lokale machine. Zie het artikel over het [instellen van uw service Fabric-ontwikkel omgeving](service-fabric-get-started.md).
> 
> 

Na het maken van het project in Visual Studio kunt u de Power shell [-opdracht copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) gebruiken om het toepassings pakket te kopiëren naar de installatie kopie opslag. Als u het app-pakket lokaal wilt controleren, gebruikt u de cmdlet [test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) . De volgende stap bestaat uit het registreren van de toepassing bij de Service Fabric-runtime met de cmdlet [REGI ster-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) . De volgende stap is het starten van een exemplaar van de toepassing met behulp van de cmdlet [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication) .  Deze drie stappen zijn vergelijkbaar met het menu-item **implementeren** in Visual Studio.  Zodra de inrichting is voltooid, moet u het gekopieerde toepassings pakket opschonen in de archief kopie om de verbruikte resources te verminderen.  Als een toepassings type niet meer vereist is, moet het worden verwijderd om dezelfde reden. Zie [toepassingen implementeren en verwijderen met behulp van Power shell](service-fabric-application-upgrade-tutorial-powershell.md) voor meer informatie.

Nu kunt u Service Fabric Explorer gebruiken [om het cluster en de toepassing weer te geven](service-fabric-visualizing-your-cluster.md). De toepassing heeft een webservice waarmee kan worden genavigeerd naar Internet Explorer door `http://localhost:8081/visualobjects` in de adres balk te typen.  In het scherm moeten enkele zwevende visuele objecten worden weer gegeven.  Daarnaast kunt u [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) gebruiken om de status van de toepassing te controleren.

## <a name="step-2-update-the-visual-objects-sample"></a>Stap 2: het voor beeld van Visual Objects bijwerken
U zult kunnen merken dat met de versie die is geïmplementeerd in stap 1, de visuele objecten niet draaien. We gaan deze toepassing upgraden naar een waar ook de visuele objecten draaien.

Selecteer het project VisualObjects. ActorService in de VisualObjects-oplossing en open het bestand StatefulVisualObjectActor. cs. Ga in dat bestand naar de methode `MoveObject` , commentaar uit `this.State.Move()` en verwijder de opmerking `this.State.Move(true)` . Deze wijziging roteert de objecten nadat de service is bijgewerkt.

Daarnaast moeten we het *ServiceManifest.xml* bestand (onder PackageRoot) van het project **VisualObjects. ActorService** bijwerken. Werk de *code package* en de service versie bij naar 2,0 en de bijbehorende regels in het *ServiceManifest.xml* -bestand.
U kunt de optie *manifest bestanden* Visual Studio bewerken gebruiken nadat u met de rechter muisknop op de oplossing hebt geklikt om het manifest bestand te wijzigen.

Nadat de wijzigingen zijn aangebracht, moet het Manifest er als volgt uitzien (gemarkeerde gedeelten worden de wijzigingen weer gegeven):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Nu is het *ApplicationManifest.xml* bestand (gevonden onder het **VisualObjects** -project onder de **VisualObjects** -oplossing) bijgewerkt naar versie 2,0 van het project **VisualObjects. ActorService** . Daarnaast wordt de versie van de toepassing bijgewerkt naar 2.0.0.0 van 1.0.0.0. De *ApplicationManifest.xml* moet eruitzien als in het volgende code fragment:

```xml
<ApplicationManifestxmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

Bouw nu het project door alleen het **ActorService** -project te selecteren en vervolgens met de rechter muisknop te klikken en de optie **Build** te selecteren in Visual Studio. Als u **alles opnieuw samen stellen** selecteert, moet u de versies voor alle projecten bijwerken omdat de code is gewijzigd. Vervolgens kunt u de bijgewerkte toepassing inpakken door met de rechter muisknop op **_VisualObjectsApplication_*_ te klikken, het menu Service Fabric te selecteren en _* pakket te kiezen**. Met deze actie wordt een toepassings pakket gemaakt dat kan worden geïmplementeerd.  Uw bijgewerkte toepassing is gereed om te worden geïmplementeerd.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Stap 3: beslissen over status beleid en upgrade parameters
Raadpleeg de [para meters](service-fabric-application-upgrade-parameters.md) voor de upgrade van de toepassing en het [upgrade proces](service-fabric-application-upgrade.md) om een goed beeld te krijgen van de verschillende para meters voor upgrades, time-outs en het toegepaste status criterium. Voor dit scenario wordt het criterium voor service status evaluatie ingesteld op de standaard waarden (en aanbevolen). Dit betekent dat alle services en instanties *in orde* moeten zijn na de upgrade.  

We verg Roten echter de *HealthCheckStableDuration* tot 180 seconden (zodat de services gedurende ten minste 120 seconden in orde zijn voordat de upgrade naar het volgende update domein wordt uitgevoerd).  We stellen de *UpgradeDomainTimeout* ook in op 1200 seconden en de *UpgradeTimeout* 3000 seconden.

Tot slot gaan we ook de *UpgradeFailureAction* instellen om terug te draaien. Voor deze optie moet Service Fabric de toepassing herstellen naar de vorige versie als er problemen tijdens de upgrade optreden. Daarom worden de volgende para meters opgegeven bij het starten van de upgrade (in stap 4):

FailureAction = terugdraaien

HealthCheckStableDurationSec = 180

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>Stap 4: de toepassing voorbereiden voor de upgrade
De toepassing is nu gebouwd en klaar om te worden bijgewerkt. Als u een Power shell-venster opent als beheerder en typ [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication), moet u weten dat het toepassings type 1.0.0.0 van **VisualObjects** is dat is geïmplementeerd.  

Het toepassings pakket wordt opgeslagen in het volgende relatieve pad waar u de Service Fabric SDK- *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug* hebt gedecomprimeerd. U moet een map "package" in die map vinden waarin het toepassings pakket wordt opgeslagen. Controleer de tijds tempels om er zeker van te zijn dat het de meest recente build is (mogelijk moet u ook de juiste paden aanpassen).

Nu gaan we het bijgewerkte toepassings pakket kopiëren naar de Service Fabric installatie kopie opslag (waar de toepassings pakketten worden opgeslagen door Service Fabric). De para meter *ApplicationPackagePathInImageStore* informeert service Fabric waar het toepassings pakket kan worden gevonden. We hebben de bijgewerkte toepassing in ' VisualObjects \_ v2 ' geplaatst met de volgende opdracht (mogelijk moet u de paden op de juiste manier aanpassen).

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

De volgende stap bestaat uit het registreren van deze toepassing met Service Fabric, die kan worden uitgevoerd met behulp van de opdracht [REGI ster-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) :

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Als de voor gaande opdracht niet kan worden uitgevoerd, is het waarschijnlijk dat u een heropbouw van alle services nodig hebt. Zoals vermeld in stap 2, moet u mogelijk ook uw WebService-versie bijwerken.

Het is raadzaam het toepassings pakket te verwijderen nadat de toepassing is geregistreerd.  Als u toepassings pakketten uit de installatie kopie opslag verwijdert, worden de systeem bronnen vrijgemaakt.  Het houden van ongebruikte toepassings pakketten verbruikt schijf opslag en leidt tot prestatie problemen van toepassingen.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>Stap 5: de upgrade van de toepassing starten
We zijn nu klaar om de upgrade van de toepassing te starten met behulp van de opdracht [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade) :

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


De naam van de toepassing is hetzelfde als die in het *ApplicationManifest.xml* bestand is beschreven. Service Fabric gebruikt deze naam om te bepalen welke toepassing wordt bijgewerkt. Als u instelt dat de time-outs te kort zijn, wordt er mogelijk een fout bericht met de melding dat het probleem zich voordoet. Raadpleeg de sectie probleem oplossing of verg root de time-outs.

Als de upgrade van de toepassing wordt uitgevoerd, kunt u deze controleren met behulp van Service Fabric Explorer of met behulp van de Power shell [-opdracht Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade) : 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

In een paar minuten moet de status die u hebt verkregen met behulp van de voor gaande Power shell-opdracht, aangeven dat alle update domeinen zijn bijgewerkt (voltooid). Het is raadzaam om te zien of de visuele objecten in uw browser venster zijn gedraaid.

U kunt een upgrade uitvoeren van versie 2 naar versie 3 of van versie 2 naar versie 1 als oefening. Het verplaatsen van versie 2 naar versie 1 wordt ook beschouwd als een upgrade. Speel met time-outs en status beleidsregels om u vertrouwd te maken met hen. Wanneer u implementeert naar een Azure-cluster, moeten de para meters op de juiste wijze worden ingesteld. Het is handig om de time-outs op te stellen.

## <a name="next-steps"></a>Volgende stappen
Als u een [upgrade uitvoert van uw toepassing met behulp van Visual Studio](service-fabric-application-upgrade-tutorial.md) , wordt u begeleid bij een toepassings upgrade met Visual Studio.

Bepalen hoe uw toepassing wordt bijgewerkt met behulp van [upgrade parameters](service-fabric-application-upgrade-parameters.md).

Maak uw toepassings upgrades compatibel door te leren hoe u [gegevens serialisatie](service-fabric-application-upgrade-data-serialization.md)gebruikt.

Meer informatie over het gebruik van geavanceerde functionaliteit bij het upgraden van uw toepassing door te verwijzen naar [Geavanceerde onderwerpen](service-fabric-application-upgrade-advanced.md).

Corrigeer veelvoorkomende problemen in toepassings upgrades door te verwijzen naar de stappen in [Troubleshooting Application upgrades](service-fabric-application-upgrade-troubleshooting.md).
