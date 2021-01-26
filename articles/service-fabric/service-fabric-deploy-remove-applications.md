---
title: Azure Service Fabric-implementatie met Power shell
description: Meer informatie over het verwijderen en implementeren van toepassingen in azure Service Fabric en het uitvoeren van deze acties in Power shell.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: d428a51c0bc224ca8706403ae176d46f1db82a32
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785444"
---
# <a name="deploy-and-remove-applications-using-powershell"></a>Toepassingen implementeren en verwijderen met behulp van Power shell

> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric-CLI](service-fabric-application-lifecycle-sfctl.md)
> * [FabricClient-API's](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

Zodra een [toepassings type is verpakt][10], kunt u het implementeren in een Azure service Fabric-cluster. De implementatie omvat de volgende drie stappen:

1. Upload het toepassings pakket naar de archief met installatie kopieën.
2. Registreer het toepassings type met het relatieve pad naar de installatie kopie opslag.
3. Maak het toepassings exemplaar.

Wanneer de geïmplementeerde toepassing niet meer nodig is, kunt u het toepassings exemplaar en het toepassings type ervan verwijderen. Als u een toepassing volledig uit het cluster wilt verwijderen, moet u de volgende stappen uitvoeren:

1. Het actieve exemplaar van de toepassing verwijderen (of verwijderen).
2. Hef de registratie van het toepassings type op als u het niet meer nodig hebt.
3. Verwijder het toepassings pakket uit het archief met installatie kopieën.

Als u Visual Studio gebruikt voor het implementeren en opsporen van fouten in toepassingen op uw lokale ontwikkel cluster, worden alle voor gaande stappen automatisch uitgevoerd via een Power shell-script.  Dit script bevindt zich in de map *scripts* van het toepassings project. Dit artikel bevat achtergrond informatie over de werking van het script, zodat u dezelfde bewerkingen kunt uitvoeren buiten Visual Studio. 

Een andere manier om een toepassing te implementeren is met behulp van externe inrichting. Het toepassings pakket kan worden [verpakt als `sfpkg` ](service-fabric-package-apps.md#create-an-sfpkg) en geüpload naar een externe opslag. In dit geval is uploaden naar de afbeeldings opslag niet nodig. De implementatie heeft de volgende stappen nodig:

1. Upload de `sfpkg` naar een externe Store. De externe opslag kan een archief zijn dat een REST http-of https-eind punt beschikbaar maakt.
2. Registreer het toepassings type met de externe down load-URI en de informatie over het toepassings type.
2. Maak het toepassings exemplaar.

Verwijder de toepassings exemplaren en hef de registratie van het toepassings type op. Omdat het pakket niet naar het archief met installatie kopieën is gekopieerd, is er geen tijdelijke locatie om op te schonen. De inrichting van het externe archief is beschikbaar vanaf Service Fabric versie 6,1.

>[!NOTE]
> Visual Studio biedt momenteel geen ondersteuning voor externe voorzieningen.

 

## <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

Voordat u Power shell-opdrachten in dit artikel uitvoert, moet u altijd eerst [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) gebruiken om verbinding te maken met het service Fabric cluster. Als u verbinding wilt maken met het lokale ontwikkel cluster, voert u het volgende uit:

```powershell
Connect-ServiceFabricCluster
```

Zie [verbinding maken met een beveiligd](service-fabric-connect-to-secure-cluster.md)cluster voor voor beelden van het maken van verbinding met een extern cluster of een cluster dat is beveiligd met Azure Active Directory, x509-certificaten of Windows Active Directory.

## <a name="upload-the-application-package"></a>Het toepassings pakket uploaden

Als u het toepassings pakket uploadt, wordt het op een locatie die toegankelijk is voor de interne Service Fabric-onderdelen.
Als u het toepassings pakket lokaal wilt controleren, gebruikt u de cmdlet [test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) .

Met de opdracht [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) uploadt u het toepassings pakket naar het cluster installatie kopie archief.

Stel dat u een toepassing bouwt en inpakt met de naam *mijn toepassing* in Visual Studio 2015. De naam van het toepassings type die in de ApplicationManifest.xml wordt vermeld, is standaard ' MyApplicationType '.  Het toepassings pakket, dat het benodigde toepassings manifest, service manifesten en code/config/gegevens pakketten bevat, bevindt zich in *C:\Users \<username\> \Documents\Visual Studio 2015 \ Projects\MyApplication\MyApplication\pkg\Debug*. 

Met de volgende opdracht wordt de inhoud van het toepassings pakket weer gegeven:

```powershell
$path = 'C:\Users\<user\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
tree /f $path
```

```Output
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
│   ApplicationManifest.xml
│
└───Stateless1Pkg
    │   ServiceManifest.xml
    │
    ├───Code
    │       Microsoft.ServiceFabric.Data.dll
    │       Microsoft.ServiceFabric.Data.Interfaces.dll
    │       Microsoft.ServiceFabric.Internal.dll
    │       Microsoft.ServiceFabric.Internal.Strings.dll
    │       Microsoft.ServiceFabric.Services.dll
    │       ServiceFabricServiceModel.dll
    │       Stateless1.exe
    │       Stateless1.exe.config
    │       Stateless1.pdb
    │       System.Fabric.dll
    │       System.Fabric.Strings.dll
    │
    └───Config
            Settings.xml
```

Als het toepassings pakket groot is en/of veel bestanden heeft, kunt u [het comprimeren](service-fabric-package-apps.md#compress-a-package). De compressie vermindert de grootte en het aantal bestanden.
Dit leidt tot snellere registratie en registratie van het toepassings type. De upload tijd kan op dit moment langzamer zijn, met name als u de tijd opgeeft om het pakket te comprimeren. 

Als u een pakket wilt comprimeren, gebruikt u dezelfde [copy-ServiceFabricApplicationPackage-](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) opdracht. Compressie kan worden gescheiden van het uploaden, met behulp van de `SkipCopy` vlag of samen met de upload bewerking. Het Toep assen van compressie op een gecomprimeerd pakket is niet op.
Als u een gecomprimeerd pakket wilt decomprimeren, gebruikt u dezelfde [copy-ServiceFabricApplicationPackage-](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) opdracht met de `UncompressPackage` Switch.

Met de volgende cmdlet wordt het pakket gecomprimeerd zonder dat het naar het archief met installatie kopieën wordt gekopieerd. Het pakket bevat nu gezipte bestanden voor de- `Code` en- `Config` pakketten. De toepassing en de service manifesten zijn niet ingepakt, omdat ze nodig zijn voor veel interne bewerkingen (zoals het delen van pakketten, de naam van het toepassings type en de versie extractie voor bepaalde validaties). Als u de manifesten inpakken, worden deze bewerkingen inefficiënt.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage -SkipCopy
tree /f $path
```

```Output
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
|   ApplicationManifest.xml
|
└───Stateless1Pkg
       Code.zip
       Config.zip
       ServiceManifest.xml
```

Voor grote toepassings pakketten kost de compressie tijd. Voor de beste resultaten gebruikt u een Fast SSD-station. De compressie tijden en de grootte van het gecomprimeerde pakket verschillen ook op basis van de inhoud van het pakket.
Hier ziet u bijvoorbeeld de compressie statistieken voor sommige pakketten, waarbij de eerste en de gecomprimeerde pakket grootte worden weer gegeven met de compressie tijd.

|Begin grootte (MB)|Aantal bestanden|Compressie tijd|Grootte van gecomprimeerd pakket (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

Wanneer een pakket is gecomprimeerd, kan het worden geüpload naar een of meerdere Service Fabric clusters als dat nodig is. Het implementatie mechanisme is hetzelfde voor gecomprimeerde en niet-gecomprimeerde pakketten. Gecomprimeerde pakketten worden als zodanig opgeslagen in het cluster installatie kopie archief. De pakketten worden niet gecomprimeerd op het knoop punt voordat de toepassing wordt uitgevoerd.


In het volgende voor beeld wordt het pakket geüpload naar het archief met installatie kopieën in een map met de naam "MyApplicationV1":

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

Als u de para meter *-ApplicationPackagePathInImageStore* niet opgeeft, wordt het toepassings pakket gekopieerd naar de map debug in de archief met installatie kopieën.

>[!NOTE]
>Met **copy-ServiceFabricApplicationPackage** wordt automatisch het juiste installatie kopie archief gedetecteerd Connection String als de Power shell-sessie is verbonden met een service Fabric cluster. Voor Service Fabric versies ouder dan 5,6 moet het argument **-ImageStoreConnectionString** expliciet worden gegeven.
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>De cmdlet **Get-ImageStoreConnectionStringFromClusterManifest** , die deel uitmaakt van de Power shell-module service Fabric SDK, wordt gebruikt voor het ophalen van de installatie kopie van de archief Connection String.  Als u de SDK-module wilt importeren, voert u de volgende handelingen uit:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>Zie [de afbeeldings store Connection String](service-fabric-image-store-connection-string.md) voor aanvullende informatie over de archief-en installatie kopie-Archief Connection String.
>
>
>

De tijd die nodig is voor het uploaden van een pakket verschilt, afhankelijk van meerdere factoren. Enkele van deze factoren zijn het aantal bestanden in het pakket, de grootte van het pakket en de bestands grootte. De netwerk snelheid tussen de bron computer en het Service Fabric cluster is ook van invloed op de upload tijd. De standaard time-out voor [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) is 30 minuten.
Afhankelijk van de beschreven factoren moet u mogelijk de time-out verhogen. Als u het pakket comprimeert in de Kopieer oproep, moet u ook rekening houden met de compressie tijd.



## <a name="register-the-application-package"></a>Het toepassings pakket registreren

Het toepassings type en de versie die in het manifest van de toepassing zijn gedeclareerd, kunnen worden gebruikt wanneer het toepassings pakket wordt geregistreerd. Het systeem leest het pakket dat in de vorige stap is geüpload, controleert het pakket, verwerkt de inhoud van het pakket en kopieert het verwerkte pakket naar een interne systeem locatie.  

Voer de cmdlet [REGI ster-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) uit om het toepassings type te registreren in het cluster en beschikbaar te maken voor implementatie:

### <a name="register-the-application-package-copied-to-image-store"></a>Het toepassings pakket dat is gekopieerd naar het archief van de installatie kopie registreren

Wanneer een pakket eerder naar het archief met installatie kopieën is gekopieerd, wordt in de registratie bewerking het relatieve pad in de afbeeldings opslag opgegeven.

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore MyApplicationV1
```

```Output
Register application type succeeded
```

' MyApplicationV1 ' is de map in de afbeeldings opslag waar het toepassings pakket zich bevindt. Het toepassings type met de naam ' MyApplicationType ' en versie ' 1.0.0 ' (beide zijn gevonden in het manifest van de toepassing) is nu geregistreerd in het cluster.

### <a name="register-the-application-package-copied-to-an-external-store"></a>Het toepassings pakket registreren dat naar een externe opslag is gekopieerd

Vanaf Service Fabric versie 6,1 biedt ondersteuning voor het downloaden van het pakket vanuit een externe Store. De Download-URI vertegenwoordigt het pad naar het [ `sfpkg` toepassings pakket](service-fabric-package-apps.md#create-an-sfpkg) van waaruit het toepassings pakket kan worden gedownload met behulp van http-of HTTPS-protocollen. Het pakket moet eerder zijn geüpload naar deze externe locatie. De URI moet lees toegang toestaan, zodat Service Fabric het bestand kunt downloaden. Het `sfpkg` bestand moet de extensie '. sfpkg ' hebben. De inrichtings bewerking moet de toepassings type gegevens bevatten, zoals deze in het manifest van de toepassing zijn gevonden.

```powershell
Register-ServiceFabricApplicationType -ApplicationPackageDownloadUri "https://sftestresources.blob.core.windows.net:443/sfpkgholder/MyAppPackage.sfpkg" -ApplicationTypeName MyApp -ApplicationTypeVersion V1 -Async
```

De opdracht [REGI ster-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) retourneert alleen nadat het toepassings pakket is geregistreerd door het systeem. Hoe lang de registratie duurt, is afhankelijk van de grootte en de inhoud van het toepassings pakket. Als dat nodig is, kan de para meter **-TimeoutSec** worden gebruikt om een langere time-out op te geven (de standaard time-out is 60 seconden).

Als u een groot toepassings pakket hebt of als u time-outs ondervindt, gebruikt u de para meter **-async** . De opdracht wordt geretourneerd wanneer het cluster de registratie opdracht accepteert. De registratie bewerking wordt voortgezet als dat nodig is.
De opdracht [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) geeft een lijst van de versie van het toepassings type en hun registratie status. U kunt deze opdracht gebruiken om te bepalen wanneer de registratie is voltooid.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>Een toepassings pakket verwijderen uit het archief met installatie kopieën

Als een pakket is gekopieerd naar het archief met installatie kopieën, moet u het verwijderen van de tijdelijke locatie nadat de toepassing is geregistreerd. Als u toepassings pakketten uit de installatie kopie opslag verwijdert, worden de systeem bronnen vrijgemaakt. Het houden van ongebruikte toepassings pakketten verbruikt schijf opslag en leidt tot prestatie problemen van toepassingen.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>De toepassing maken

U kunt een toepassing instantiëren vanuit elke toepassings type versie die is geregistreerd met behulp van de cmdlet [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication) . De naam van elke toepassing moet beginnen met het schema *' Fabric: '* en moet uniek zijn voor elk toepassings exemplaar. Sstandaardservices gedefinieerd in het toepassingsmanifest van het doeltoepassingstype worden ook aangemaakt.

```powershell
New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```

Er kunnen meerdere toepassings exemplaren worden gemaakt voor een bepaalde versie van een geregistreerd toepassings type. Elk exemplaar van de toepassing wordt uitgevoerd in isolatie, met een eigen werkmap en proces.

Als u wilt zien welke benoemde apps en services worden uitgevoerd in het cluster, voert u de cmdlets [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) en [Get-ServiceFabricService](/powershell/module/servicefabric/get-servicefabricservice) uit:

```powershell
Get-ServiceFabricApplication  
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}
```

```powershell
Get-ServiceFabricApplication | Get-ServiceFabricService
```

```Output
ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>Een toepassing verwijderen

Wanneer een toepassings exemplaar niet meer nodig is, kunt u dit met behulp van de cmdlet [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication) permanent verwijderen met de naam. [Met Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication) worden alle services die deel uitmaken van de toepassing, automatisch verwijderd en worden alle service statussen permanent verwijderd. 

> [!WARNING]
> Deze bewerking kan niet ongedaan worden gemaakt en de status van de toepassing kan niet worden hersteld.

```powershell
Remove-ServiceFabricApplication fabric:/MyApp
```

```Output
Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded
```

```powershell
Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>Registratie van een toepassings type ongedaan maken

Wanneer een bepaalde versie van een toepassings type niet meer nodig is, moet u de registratie van het toepassings type ongedaan maken met behulp van de cmdlet [unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype) . Bij het ongedaan maken van de registratie van ongebruikte toepassings typen wordt de opslag ruimte die door de installatie kopie wordt gebruikt, door de toepassings type bestanden verwijderd. Bij het ongedaan maken van de registratie van een toepassings type wordt het toepassings pakket dat is gekopieerd naar de tijdelijke locatie van het installatie kopie archief niet verwijderd als er een kopie naar het archief met installatie kopieën is gebruikt. U kunt de registratie van een toepassings type ongedaan maken zolang er geen toepassingen worden geïnstantieerd en er geen wachtende toepassings upgrades zijn.

Voer [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) uit om de toepassings typen weer te geven die momenteel zijn geregistreerd in het cluster:

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Voer [unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype) uit om de registratie van een specifiek toepassings type op te heffen:

```powershell
Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage vraagt om een ImageStoreConnectionString

De Service Fabric SDK-omgeving moet al de juiste standaard instellingen hebben ingesteld. Maar als dat nodig is, moet de ImageStoreConnectionString voor alle opdrachten overeenkomen met de waarde die het Service Fabric cluster gebruikt. U kunt de ImageStoreConnectionString vinden in het cluster manifest, opgehaald met de opdrachten [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest) en Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

De cmdlet **Get-ImageStoreConnectionStringFromClusterManifest** , die deel uitmaakt van de Power shell-module service Fabric SDK, wordt gebruikt voor het ophalen van de installatie kopie van de archief Connection String.  Als u de SDK-module wilt importeren, voert u de volgende handelingen uit:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

De ImageStoreConnectionString is te vinden in het cluster manifest:

```xml
<ClusterManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Zie [de afbeeldings store Connection String](service-fabric-image-store-connection-string.md) voor aanvullende informatie over de archief-en installatie kopie-Archief Connection String.

### <a name="deploy-large-application-package"></a>Omvang rijk toepassings pakket implementeren

Probleem: [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) keer een time-out voor een groot toepassings pakket (volg orde van GB).
Probeer:
- Geef een grotere time-out op voor de opdracht [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) met de `TimeoutSec` para meter. De time-out is standaard 30 minuten.
- Controleer de netwerk verbinding tussen de bron computer en het cluster. Als de verbinding langzaam is, kunt u overwegen om een computer met een betere netwerk verbinding te gebruiken.
Als de client computer zich in een andere regio bevindt dan het cluster, kunt u overwegen om een client computer in een dichter of dezelfde regio als het cluster te gebruiken.
- Controleer of u externe restricties kunt door lopen. Als het archief met installatie kopieën bijvoorbeeld is geconfigureerd voor het gebruik van Azure Storage, wordt het uploaden mogelijk beperkt.

Probleem: [het uploaden van het](/powershell/module/servicefabric/register-servicefabricapplicationtype) pakket is voltooid, maar er is een time-out opgetreden in ServiceFabricApplicationType. Meld
- [Comprimeer het pakket](service-fabric-package-apps.md#compress-a-package) voordat u het kopieert naar het archief met installatie kopieën.
De compressie vermindert de grootte en het aantal bestanden, waardoor de hoeveelheid verkeer wordt verminderd en het werk dat Service Fabric moet worden uitgevoerd. De upload bewerking kan langzamer verlopen (met name als u de compressie tijd opneemt), maar de registratie van het toepassings type te registreren en ongedaan te maken, zijn sneller.
- Geef een grotere time-out op voor [REGI ster-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) met de `TimeoutSec` para meter.
- Geef `Async` Switch op voor [REGI ster-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype). De opdracht wordt geretourneerd wanneer het cluster de opdracht accepteert en de registratie van het toepassings type asynchroon voortzet. Daarom is het niet nodig om in dit geval een hogere time-out op te geven. De opdracht [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) geeft een lijst van alle geregistreerde toepassings type versies en hun registratie status. U kunt deze opdracht gebruiken om te bepalen wanneer de registratie is voltooid.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>Een toepassings pakket met veel bestanden implementeren

Probleem: er is een time [-](/powershell/module/servicefabric/register-servicefabricapplicationtype) out opgetreden voor een toepassings pakket met veel bestanden (volg orde van duizenden).
Probeer:
- [Comprimeer het pakket](service-fabric-package-apps.md#compress-a-package) voordat u het kopieert naar het archief met installatie kopieën. De compressie vermindert het aantal bestanden.
- Geef een grotere time-out op voor [REGI ster-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) met de `TimeoutSec` para meter.
- Geef `Async` Switch op voor [REGI ster-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype). De opdracht wordt geretourneerd wanneer het cluster de opdracht accepteert en de registratie van het toepassings type asynchroon voortzet.
Daarom is het niet nodig om in dit geval een hogere time-out op te geven. De opdracht [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) geeft een lijst van alle geregistreerde toepassings type versies en hun registratie status. U kunt deze opdracht gebruiken om te bepalen wanneer de registratie is voltooid.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="next-steps"></a>Volgende stappen

[Toepassingspakket maken](service-fabric-package-apps.md)

[Upgrade van toepassing Service Fabric](service-fabric-application-upgrade.md)

[Inleiding van Service Fabric status](service-fabric-health-introduction.md)

[Een Service Fabric service diagnosticeren en problemen oplossen](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Een toepassing model leren in Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
