---
title: Een Azure Service Fabric-app inpakken
description: Meer informatie over het inpakken van een Azure Service Fabric-toepassing en hoe u de implementatie kunt voorbereiden op een cluster.
ms.topic: conceptual
ms.date: 2/23/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 168e6d6dc7ab5bfeccc4e1dabc7bd50efcbe8f34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789699"
---
# <a name="package-an-application"></a>Toepassingspakket maken

In dit artikel wordt beschreven hoe u een Service Fabric toepassing inpakt en deze gereed maakt voor implementatie.

## <a name="package-layout"></a>Pakket indeling

Het toepassings manifest, een of meer service manifesten en andere benodigde pakket bestanden moeten in een specifieke indeling worden ingedeeld voor implementatie in een Service Fabric cluster. De voorbeeld manifesten in dit artikel moeten zijn ingedeeld in de volgende mapstructuur:

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

De namen van de mappen moeten overeenkomen met de **naam** kenmerken van elk overeenkomstig element. Als het service manifest bijvoorbeeld twee code pakketten bevat met de namen **MyCodeA** en **MyCodeB**, bevatten twee mappen met dezelfde namen de benodigde binaire bestanden voor elk code pakket.

## <a name="use-setupentrypoint"></a>SetupEntryPoint gebruiken

Typische scenario's voor het gebruik van **SetupEntryPoint** zijn wanneer u een uitvoerbaar bestand moet uitvoeren voordat de service wordt gestart, of als u een bewerking wilt uitvoeren met verhoogde bevoegdheden. Bijvoorbeeld:

* Instellen en initialiseren van omgevings variabelen die voor het uitvoer bare bestand van de service nodig zijn. Het is niet beperkt tot uitvoer bare bestanden die zijn geschreven via de Service Fabric-programmeer modellen. npm.exe hebt bijvoorbeeld bepaalde omgevings variabelen nodig die zijn geconfigureerd voor het implementeren van een node.js toepassing.
* Toegangs beheer instellen door beveiligings certificaten te installeren.

Zie voor meer informatie over het configureren van de **SetupEntryPoint** [het beleid configureren voor een toegangs punt voor een service-instelling](service-fabric-application-runas-security.md)

<a id="Package-App"></a>

## <a name="configure"></a>Configureren

### <a name="build-a-package-by-using-visual-studio"></a>Een pakket bouwen met Visual Studio

Als u Visual Studio hebt gebruikt om uw toepassing te maken, kunt u de *pakket* opdracht gebruiken om automatisch een pakket te maken dat overeenkomt met de indeling die hierboven wordt beschreven.

Als u een pakket wilt maken, klikt u met de rechter muisknop op het toepassings project in *Solution Explorer* en kiest u de **pakket** opdracht:

![Een toepassing verpakken met Visual Studio][vs-package-command]

Wanneer de verpakking is voltooid, kunt u de locatie van het pakket vinden in het **uitvoer** venster. De stap pakket wordt automatisch uitgevoerd wanneer u uw toepassing implementeert of fouten opspoort in Visual Studio.

### <a name="build-a-package-by-command-line"></a>Een pakket bouwen met de opdracht regel

Het is ook mogelijk om de toepassing via een programma te verpakken met behulp van `msbuild.exe` . Op de schermen wordt Visual Studio uitgevoerd, zodat de uitvoer hetzelfde is.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>Het pakket testen

U kunt de pakket structuur lokaal controleren via Power shell met behulp van de opdracht [test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) .
Met deze opdracht worden de problemen met het parseren van manifesten gecontroleerd en alle verwijzingen geverifieerd. Met deze opdracht wordt alleen de structurele juistheid van de directory's en bestanden in het pakket gecontroleerd.
Er wordt niet gecontroleerd of de inhoud van het code-of gegevens pakket wordt gecontroleerd, behalve dat alle benodigde bestanden aanwezig zijn.

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

Deze fout geeft aan dat het *MySetup.bat* bestand waarnaar wordt verwezen in het service manifest **SetupEntryPoint** ontbreekt in het code pakket. Nadat het ontbrekende bestand is toegevoegd, wordt de verificatie door de toepassing door gegeven:

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
True
```

Als voor uw toepassing [toepassings parameters](service-fabric-manage-multiple-environment-app-configuration.md) zijn gedefinieerd, kunt u deze door geven in [test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) voor een juiste validatie.

Als u het cluster kent waarop de toepassing wordt geïmplementeerd, wordt u aangeraden de para meter door te geven `ImageStoreConnectionString` . In dit geval wordt het pakket ook gevalideerd op basis van eerdere versies van de toepassing die al in het cluster worden uitgevoerd. De validatie kan bijvoorbeeld detecteren of een pakket met dezelfde versie maar een andere inhoud al is geïmplementeerd.  

Wanneer de toepassing correct is verpakt en validatie wordt door gegeven, kunt u overwegen om het pakket te comprimeren voor snellere implementatie bewerkingen.

## <a name="compress-a-package"></a>Een pakket comprimeren

Wanneer een pakket groot is of veel bestanden heeft, kunt u het comprimeren voor een snellere implementatie. Compressie vermindert het aantal bestanden en de grootte van het pakket.
Voor een gecomprimeerd toepassings pakket kan [het uploaden van het toepassings pakket](service-fabric-deploy-remove-applications.md#upload-the-application-package) langer duren dan het uploaden van het niet-gecomprimeerde pakket, met name als compressie wordt uitgevoerd als onderdeel van de kopie. Met compressie kunt u het toepassings type sneller [registreren](service-fabric-deploy-remove-applications.md#register-the-application-package) en [de registratie ervan ongedaan](service-fabric-deploy-remove-applications.md#unregister-an-application-type) maken.

Het implementatie mechanisme is hetzelfde voor gecomprimeerde en niet-gecomprimeerde pakketten. Als het pakket is gecomprimeerd, wordt het opgeslagen als zodanig in het cluster installatie kopie archief en wordt het gedecomprimeerd op het knoop punt voordat de toepassing wordt uitgevoerd.
De compressie vervangt het geldige Service Fabric-pakket door de gecomprimeerde versie. De map moet schrijf machtigingen toestaan. Het uitvoeren van compressie op een al gecomprimeerd pakket resulteert niet in wijzigingen.

U kunt een pakket comprimeren door de Power shell [-opdracht copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) uit te voeren met de `CompressPackage` Switch. U kunt het pakket decomprimeren met dezelfde opdracht met behulp van `UncompressPackage` Switch.

Met de volgende opdracht wordt het pakket gecomprimeerd zonder dat het naar het archief met installatie kopieën wordt gekopieerd. U kunt een gecomprimeerd pakket kopiëren naar een of meer Service Fabric clusters, indien nodig, met behulp van [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) zonder de `SkipCopy` vlag.
Het pakket bevat nu gezipte bestanden voor `code` de `config` pakketten, en `data` . Het manifest van de toepassing en de service manifesten zijn niet ingepakt, omdat ze nodig zijn voor veel interne bewerkingen. Bijvoorbeeld pakket delen, naam van het toepassings type en de versie extractie voor bepaalde validaties hebben alle toegang tot de manifesten nodig. Als u de manifesten inpakken, worden deze bewerkingen inefficiënt.

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -CompressPackage -SkipCopy
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
       ServiceManifest.xml
       MyCode.zip
       MyConfig.zip
       MyData.zip

```

U kunt ook het pakket comprimeren en kopiëren met [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) in één stap.
Als het pakket groot is, moet u een groot genoeg time-out opgeven om tijd te bieden voor zowel de pakket compressie als de upload naar het cluster.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

Intern berekent Service Fabric controle sommen voor de toepassings pakketten voor validatie. Wanneer compressie wordt gebruikt, worden de controle sommen berekend op basis van de gezipte versies van elk pakket. Bij het genereren van een nieuwe post uit hetzelfde toepassings pakket worden verschillende controle sommen gemaakt. Gebruik [diff-inrichting](service-fabric-application-upgrade-advanced.md)om validatie fouten te voor komen. Als u deze optie kiest, neemt u niet de ongewijzigde pakketten op in de nieuwe versie. Raadpleeg in plaats daarvan rechtstreeks vanuit het nieuwe service manifest.

Als het inrichten van diff geen optie is en u de pakketten moet toevoegen, genereert u nieuwe versies voor de `code` , `config` , en `data` om te voor komen dat de controlesom niet overeenkomt. Het genereren van nieuwe versies voor ongewijzigde pakketten is nood zakelijk wanneer een gecomprimeerd pakket wordt gebruikt, ongeacht of de vorige versie compressie gebruikt of niet.

Het pakket is nu goed verpakt, gevalideerd en gecomprimeerd (indien nodig), zodat het gereed is voor [implementatie](service-fabric-deploy-remove-applications.md) naar een of meer service Fabric clusters.

### <a name="compress-packages-when-deploying-using-visual-studio"></a>Pakketten comprimeren bij het implementeren met Visual Studio

U kunt Visual Studio een instructie geven om pakketten te comprimeren tijdens de implementatie door het element toe te voegen `CopyPackageParameters` aan uw publicatie profiel en het `CompressPackage` kenmerk in te stellen op `true` .

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="create-an-sfpkg"></a>Een sfpkg maken

Vanaf versie 6,1 kunt u met Service Fabric inrichten vanuit een externe opslag.
Met deze optie hoeft het toepassings pakket niet naar het archief met installatie kopieën te worden gekopieerd. In plaats daarvan kunt u een maken `sfpkg` en uploaden naar een externe Store. vervolgens geeft u de Download-URI op die moet Service Fabric bij het inrichten. Hetzelfde pakket kan worden ingericht voor meerdere clusters. Bij het inrichten vanuit de externe opslag wordt de benodigde tijd voor het kopiëren van het pakket naar elk cluster opgeslagen.

Het `sfpkg` bestand is een zip die het oorspronkelijke toepassings pakket bevat en de extensie '. sfpkg ' heeft.
In de zip kan het toepassings pakket gecomprimeerd of gedecomprimeerd zijn. De compressie van het toepassings pakket in het zip-bestand wordt uitgevoerd op het niveau van code, configuratie en gegevens pakket, zoals [eerder beschreven](service-fabric-package-apps.md#compress-a-package).

Als u een wilt maken `sfpkg` , begint u met een map die het oorspronkelijke toepassings pakket bevat of niet. Gebruik vervolgens elk hulp programma om de map met de extensie '. sfpkg ' te laten overgaan. Gebruik bijvoorbeeld [ZipFile. CreateFromDirectory](/dotnet/api/system.io.compression.zipfile.createfromdirectory#System_IO_Compression_ZipFile_CreateFromDirectory_System_String_System_String_System_IO_Compression_CompressionLevel_System_Boolean_).

```csharp
ZipFile.CreateFromDirectory(appPackageDirectoryPath, sfpkgFilePath);
```

De `sfpkg` moet worden geüpload naar het buiten-band beheer van de externe opslag buiten service Fabric. De externe opslag kan een archief zijn dat een REST http-of https-eind punt beschikbaar maakt. Tijdens het inrichten van Service Fabric wordt een GET-bewerking uitgevoerd om het `sfpkg` toepassings pakket te downloaden, zodat het archief Lees toegang voor het pakket moet toestaan.

Als u het pakket wilt inrichten, gebruikt u externe inrichting, waarvoor de down load-URI en de informatie over het toepassings type vereist zijn.

>[!NOTE]
> Het inrichten op basis van het relatieve pad van de installatie kopie opslag biedt momenteel geen ondersteuning voor `sfpkg` bestanden. Daarom mag de `sfpkg` niet worden gekopieerd naar de archief met installatie kopieën.

## <a name="next-steps"></a>Volgende stappen

[Toepassingen implementeren en verwijderen][10] beschrijft hoe u Power shell kunt gebruiken voor het beheren van toepassings exemplaren

Het [beheren van toepassings parameters voor meerdere omgevingen][11] beschrijft het configureren van para meters en omgevings variabelen voor verschillende exemplaren van toepassingen.

[Beveiligings beleid configureren voor uw toepassing][12] hierin wordt beschreven hoe u Services uitvoert onder beveiligings beleid om de toegang te beperken.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
