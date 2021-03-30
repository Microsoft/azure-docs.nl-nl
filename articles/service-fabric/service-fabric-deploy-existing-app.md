---
title: Een bestaand uitvoerbaar bestand implementeren in azure Service Fabric
description: Meer informatie over het inpakken van een bestaande toepassing als een uitvoerbaar gast bestand, zodat het kan worden geïmplementeerd in een Service Fabric cluster.
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 72fde75e16341164106bb952d0bb66b83be744e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86259263"
---
# <a name="package-and-deploy-an-existing-executable-to-service-fabric"></a>Een bestaand uitvoerbaar bestand inpakken en implementeren in Service Fabric

Als u een bestaand uitvoerbaar bestand inpakt als [uitvoerbaar gast bestand](service-fabric-guest-executables-introduction.md), kunt u kiezen of u een Visual Studio-project sjabloon wilt gebruiken of dat u [het toepassings pakket hand matig wilt maken](#manually). Met Visual Studio worden de structuur van het toepassings pakket en de manifest bestanden gemaakt door de nieuwe project sjabloon voor u.

> [!TIP]
> De eenvoudigste manier om een bestaand Windows-uitvoerbaar bestand te verpakken in een-service is door Visual Studio en Linux te gebruiken voor het gebruik van Yeoman
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>Visual Studio gebruiken om een bestaand uitvoerbaar bestand te verpakken en te implementeren

Visual Studio biedt een Service Fabric service sjabloon waarmee u een uitvoerbaar gast bestand kunt implementeren in een Service Fabric-cluster.

1. Kies **bestand**  >  **Nieuw project** en maak een service Fabric-toepassing.
2. Kies **uitvoer bare gast** als service sjabloon.
3. Klik op **Bladeren** om de map met het uitvoer bare bestand te selecteren en vul de overige para meters in om de service te maken.
   * *Gedrag van code pakket*. Kan zo worden ingesteld dat alle inhoud van uw map naar het Visual Studio-project wordt gekopieerd. Dit is handig als het uitvoer bare bestand niet wordt gewijzigd. Als u verwacht dat het uitvoer bare bestand wordt gewijzigd en u de mogelijkheid wilt bieden om nieuwe builds dynamisch op te halen, kunt u in plaats daarvan een koppeling naar de map maken. U kunt gekoppelde mappen gebruiken bij het maken van het toepassings project in Visual Studio. Dit is een koppeling naar de bron locatie vanuit het project, zodat u het uitvoer bare gast bestand kunt bijwerken in de bron bestemming. Deze updates worden onderdeel van het toepassings pakket bij het bouwen.
   * Het *programma* geeft het uitvoer bare bestand op dat moet worden uitgevoerd om de service te starten.
   * *Argumenten* Hiermee geeft u de argumenten op die moeten worden door gegeven aan het uitvoer bare bestand. Dit kan een lijst met para meters met argumenten zijn.
   * *WorkingFolder* Hiermee geeft u de werkmap op voor het proces dat wordt gestart. U kunt drie waarden opgeven:
     * `CodeBase` geeft aan dat de werkmap wordt ingesteld op de code Directory in het toepassings pakket ( `Code` map die in de voor gaande bestands structuur wordt weer gegeven).
     * `CodePackage` Hiermee geeft u op dat de werkmap wordt ingesteld op de hoofdmap van het toepassings pakket ( `GuestService1Pkg` Zie de voor gaande bestands structuur).
     * `Work` Hiermee geeft u op dat de bestanden in een submap met de naam werk worden geplaatst.
4. Geef de service een naam en klik op **OK**.
5. Als voor uw service een eind punt voor communicatie nodig is, kunt u nu het Protocol, de poort en het type toevoegen aan het ServiceManifest.xml-bestand. Bijvoorbeeld: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. U kunt nu de pakket-en publicatie actie voor uw lokale cluster gebruiken door de oplossing in Visual Studio te debuggen. Wanneer u klaar bent, kunt u de toepassing publiceren naar een extern cluster of de oplossing inchecken voor broncode beheer.
7. Lees [uw actieve toepassing controleren](#check-your-running-application) om te zien hoe u de uitvoer bare gast service kunt weer geven die wordt uitgevoerd in service Fabric Explorer.

Zie [uw eerste uitvoer bare gast toepassing maken met Visual Studio](quickstart-guest-app.md)voor een voorbeeld scenario.

### <a name="packaging-multiple-executables-with-visual-studio"></a>Meerdere uitvoer bare bestanden met Visual Studio inpakken

U kunt Visual Studio gebruiken om een toepassings pakket te maken dat meerdere uitvoer bare bestanden voor gasten bevat. Nadat u het eerste uitvoer bare gast bestand hebt toegevoegd, klikt u met de rechter muisknop op het toepassings project en selecteert u de **>nieuwe service Fabric-service** om het tweede uitvoer bare gast project toe te voegen aan de oplossing.

> [!NOTE]
> Als u ervoor kiest om de bron te koppelen in het Visual Studio-project en de Visual Studio-oplossing te bouwen, zorgt u ervoor dat uw toepassings pakket up-to-date is met de wijzigingen in de bron.

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>Yeoman gebruiken om een bestaand uitvoerbaar bestand in Linux te verpakken en te implementeren

De procedure voor het maken en implementeren van een uitvoerbaar gast bestand op Linux is hetzelfde als het implementeren van een csharp-of Java-toepassing.

1. Typ in een terminal `yo azuresfguest`.
2. Geef uw toepassing een naam.
3. Noem uw service en geef de details op, inclusief het pad van het uitvoer bare bestand en de para meters die moeten worden aangeroepen met.

Yeoman maakt een toepassings pakket met de juiste toepassings-en manifest bestanden samen met scripts voor installeren en verwijderen.

### <a name="packaging-multiple-executables-using-yeoman-on-linux"></a>Meerdere uitvoer bare bestanden met yeoman in Linux inpakken

Voer de volgende stappen uit als u nog een service wilt toevoegen aan een toepassing die al is gemaakt met `yo`:

1. Stel de directory in op de hoofdmap van de bestaande toepassing.  Bijvoorbeeld `cd ~/YeomanSamples/MyApplication` als `MyApplication` de toepassing is die is gemaakt door Yeoman.
2. Voer uit `yo azuresfguest:AddService` en geef de benodigde details op.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>Een bestaand uitvoerbaar pakket hand matig inpakken en implementeren

Het proces van het hand matig uitpakken van een gast uitvoer bare bestand is gebaseerd op de volgende algemene stappen:

1. Maak de mapstructuur van het pakket.
2. Voeg de code-en configuratie bestanden van de toepassing toe.
3. Bewerk het bestand met de service manifest.
4. Bewerk het manifest bestand van de toepassing.

### <a name="create-the-package-directory-structure"></a>De mapstructuur van het pakket maken

U kunt beginnen met het maken van de mapstructuur, zoals beschreven in [een Azure service Fabric-app inpakken](./service-fabric-package-apps.md).

### <a name="add-the-applications-code-and-configuration-files"></a>De code-en configuratie bestanden van de toepassing toevoegen

Nadat u de mapstructuur hebt gemaakt, kunt u de code-en configuratie bestanden van de toepassing toevoegen aan de code-en configuratie mappen. U kunt ook aanvullende directory's of submappen maken onder de code of de configuratie mappen.

Service Fabric voert een `xcopy` van de inhoud uit van de hoofdmap van de toepassing, dus er is geen vooraf gedefinieerde structuur voor het maken van twee meest populaire directory's, code en instellingen. (U kunt desgewenst verschillende namen kiezen. Meer informatie vindt u in de volgende sectie.)

> [!NOTE]
> Zorg ervoor dat u alle bestanden en afhankelijkheden opneemt die de toepassing nodig heeft. Service Fabric kopieert de inhoud van het toepassings pakket op alle knoop punten in het cluster waar de services van de toepassing zullen worden geïmplementeerd. Het pakket moet alle code bevatten die de toepassing nodig heeft om uit te voeren. Ga er niet van uit dat de afhankelijkheden al zijn geïnstalleerd.
>
>

### <a name="edit-the-service-manifest-file"></a>Het service manifest bestand bewerken

De volgende stap is het bewerken van het service manifest bestand voor het toevoegen van de volgende gegevens:

* De naam van het Service type. Dit is een ID die Service Fabric gebruikt voor het identificeren van een service.
* De opdracht die moet worden gebruikt om de toepassing te starten (ExeHost).
* Elk script dat moet worden uitgevoerd om de toepassing in te stellen (SetupEntrypoint).

Hier volgt een voor beeld van een `ServiceManifest.xml` bestand:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

De volgende secties gaan over de verschillende onderdelen van het bestand dat u moet bijwerken.

#### <a name="update-servicetypes"></a>ServiceTypes bijwerken

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* U kunt elke gewenste naam kiezen voor `ServiceTypeName` . De waarde wordt in het `ApplicationManifest.xml` bestand gebruikt om de service te identificeren.
* Geef `UseImplicitHost="true"` op. Met dit kenmerk wordt Service Fabric dat de service is gebaseerd op een zelfvoorziene app, dus alle Service Fabric moeten worden gestart om het te kunnen starten en de status ervan te controleren.

#### <a name="update-codepackage"></a>Code package bijwerken
Het code package-element geeft de locatie (en versie) van de code van de service op.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

Het `Name` element wordt gebruikt om de naam op te geven van de map in het toepassings pakket met de code van de service. `CodePackage` heeft ook het `version` kenmerk. Dit kan worden gebruikt om de versie van de code op te geven, en kan ook worden gebruikt om de code van de service bij te werken met behulp van de infra structuur voor toepassings levenscyclus beheer in Service Fabric.

#### <a name="optional-update-setupentrypoint"></a>Optioneel: SetupEntrypoint bijwerken

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
Het element SetupEntryPoint wordt gebruikt om een uitvoerbaar of batch bestand op te geven dat moet worden uitgevoerd voordat de code van de service wordt gestart. Het is een optionele stap, dus deze hoeft niet te worden opgenomen als er geen initialisatie vereist is. De SetupEntryPoint wordt uitgevoerd telkens wanneer de service opnieuw wordt gestart.

Er is slechts één SetupEntryPoint, dus installatie scripts moeten worden gegroepeerd in één batch bestand als voor de installatie van de toepassing meerdere scripts zijn vereist. De SetupEntryPoint kan elk type bestand uitvoeren: uitvoer bare bestanden, batch bestanden en Power shell-cmdlets. Zie [Configure SetupEntryPoint](service-fabric-application-runas-security.md)(Engelstalig) voor meer informatie.

In het vorige voor beeld voert de SetupEntryPoint een batch-bestand `LaunchConfig.cmd` uit dat is aangeroepen in de submap `scripts` van de map code (ervan uitgaande dat het element WorkingFolder is ingesteld op code base).

#### <a name="update-entrypoint"></a>Entry point bijwerken

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

Het `EntryPoint` element in het service manifest bestand wordt gebruikt om op te geven hoe de service moet worden gestart.

Het `ExeHost` element geeft het uitvoer bare bestand (en de argumenten) op dat moet worden gebruikt om de service te starten. U kunt het kenmerk eventueel toevoegen `IsExternalExecutable="true"` aan `ExeHost` om aan te geven dat het programma een extern uitvoerbaar bestand is dat buiten het code pakket is. Bijvoorbeeld `<ExeHost IsExternalExecutable="true">`.

* `Program` Hiermee geeft u de naam van het uitvoer bare bestand op waarmee de service moet worden gestart.
* `Arguments` Hiermee geeft u de argumenten op die moeten worden door gegeven aan het uitvoer bare bestand. Dit kan een lijst met para meters met argumenten zijn.
* `WorkingFolder` Hiermee geeft u de werkmap op voor het proces dat wordt gestart. U kunt drie waarden opgeven:
  * `CodeBase` geeft aan dat de werkmap wordt ingesteld op de code Directory in het toepassings pakket ( `Code` map in de voor gaande bestands structuur).
  * `CodePackage` Hiermee geeft u op dat de werkmap wordt ingesteld op de hoofdmap van het toepassings pakket ( `GuestService1Pkg` in de voor gaande bestands structuur).
    * `Work` Hiermee geeft u op dat de bestanden in een submap met de naam werk worden geplaatst.

De WorkingFolder is handig om de juiste werkmap in te stellen, zodat relatieve paden kunnen worden gebruikt door de toepassing of initialisatie scripts.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>Eind punten bijwerken en registreren bij Naming Service voor communicatie

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```

In het vorige voor beeld `Endpoint` bevat het element de eind punten waarop de toepassing kan Luis teren. In dit voor beeld luistert de Node.js-toepassing op http op poort 3000.

Daarnaast kunt u Service Fabric vragen om dit eind punt te publiceren naar de Naming Service, zodat andere services het eindpunt adres naar deze service kunnen detecteren. Zo kunt u communiceren tussen services die gast-uitvoer bare bestanden zijn.
Het gepubliceerde eindpunt adres is van het formulier `UriScheme://IPAddressOrFQDN:Port/PathSuffix` . `UriScheme` en `PathSuffix` zijn optionele kenmerken. `IPAddressOrFQDN` is het IP-adres of de Fully Qualified Domain Name van het knoop punt waarop dit uitvoer bare bestand wordt geplaatst, waarna het wordt berekend voor u.

In het volgende voor beeld, wanneer de service is geïmplementeerd, wordt in Service Fabric Explorer een eind punt weer gegeven dat vergelijkbaar is met `http://10.1.4.92:3000/myapp/` gepubliceerd voor het service-exemplaar. Of als dit een lokale computer is, ziet u `http://localhost:3000/myapp/` .

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```

U kunt deze adressen gebruiken met [omgekeerde proxy](service-fabric-reverseproxy.md) om te communiceren tussen services.

### <a name="edit-the-application-manifest-file"></a>Het manifest bestand van de toepassing bewerken

Nadat u het bestand hebt geconfigureerd `Servicemanifest.xml` , moet u enkele wijzigingen aanbrengen in het `ApplicationManifest.xml` bestand om ervoor te zorgen dat het juiste service type en deze naam worden gebruikt.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport

In het `ServiceManifestImport` -element kunt u een of meer services opgeven die u wilt toevoegen in de app. Er wordt verwezen naar Services `ServiceManifestName` , waarmee de naam van de map wordt opgegeven waarin het `ServiceManifest.xml` bestand zich bevindt.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Logboek registratie instellen

Voor uitvoer bare gast bestanden is het handig om console logboeken te bekijken om te achterhalen of er fouten in de toepassings-en configuratie scripts worden weer gegeven.
Console-omleiding kan worden geconfigureerd in het `ServiceManifest.xml` bestand met behulp van het- `ConsoleRedirection` element.

> [!WARNING]
> Gebruik nooit het beleid voor omleiding van console in een toepassing die in productie is geïmplementeerd, omdat dit van invloed kan zijn op de failover van de toepassing. Gebruik dit *alleen* voor lokale ontwikkeling en fout opsporing.  
>
>

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

`ConsoleRedirection` kan worden gebruikt om de console-uitvoer (stdout en stderr) om te leiden naar een werkmap. Dit biedt de mogelijkheid om te controleren of er geen fouten zijn tijdens het instellen of uitvoeren van de toepassing in het Service Fabric cluster.

`FileRetentionCount` Hiermee wordt bepaald hoeveel bestanden worden opgeslagen in de werkmap. Een waarde van 5 betekent bijvoorbeeld dat de logboek bestanden voor de voor gaande vijf uitvoeringen worden opgeslagen in de werkmap.

`FileMaxSizeInKb` Hiermee geeft u de maximale grootte van de logboek bestanden.

Logboek bestanden worden opgeslagen in een van de werk mappen van de service. Om te bepalen waar de bestanden zich bevinden, gebruikt u Service Fabric Explorer om te bepalen op welk knoop punt de service wordt uitgevoerd en welke werkmap wordt gebruikt. Dit proces wordt verderop in dit artikel besproken.

## <a name="deployment"></a>Implementatie

De laatste stap is het [implementeren van uw toepassing](service-fabric-deploy-remove-applications.md). Het volgende Power shell-script laat zien hoe u uw toepassing implementeert in het lokale ontwikkel cluster en een nieuwe Service Fabric-service start.

```powershell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```

>[!TIP]
> [Comprimeer het pakket](service-fabric-package-apps.md#compress-a-package) voordat u het kopieert naar het archief met installatie kopieën als het pakket groot is of veel bestanden bevat. Meer informatie is [hier](service-fabric-deploy-remove-applications.md#upload-the-application-package) beschikbaar.
>

Een Service Fabric-service kan in verschillende configuraties worden geïmplementeerd. Het kan bijvoorbeeld worden geïmplementeerd als één of meerdere instanties, of het kan zodanig worden geïmplementeerd dat er één exemplaar van de service op elk knoop punt van het Service Fabric cluster is.

De `InstanceCount` para meter van de `New-ServiceFabricService` cmdlet wordt gebruikt om op te geven hoeveel exemplaren van de service in het service Fabric cluster moeten worden gestart. U kunt de `InstanceCount` waarde instellen, afhankelijk van het type toepassing dat u implementeert. De twee meest voorkomende scenario's zijn:

* `InstanceCount = "1"`. In dit geval wordt slechts één exemplaar van de service geïmplementeerd in het cluster. De Planner van Service Fabric bepaalt op welk knoop punt de service wordt geïmplementeerd.
* `InstanceCount ="-1"`. In dit geval wordt één exemplaar van de service geïmplementeerd op elk knoop punt in het Service Fabric cluster. Het resultaat heeft één (en slechts één) exemplaar van de service voor elk knoop punt in het cluster.

Dit is een handige configuratie voor front-end toepassingen (bijvoorbeeld een REST-eind punt), omdat client toepassingen verbinding moeten maken met een van de knoop punten in het cluster om het eind punt te gebruiken. Deze configuratie kan ook worden gebruikt wanneer bijvoorbeeld alle knoop punten van het Service Fabric cluster zijn verbonden met een load balancer. Client verkeer kan vervolgens worden gedistribueerd over de service die wordt uitgevoerd op alle knoop punten in het cluster.

## <a name="check-your-running-application"></a>Uw actieve toepassing controleren
In Service Fabric Explorer identificeert u het knoop punt waar de service wordt uitgevoerd. In dit voor beeld wordt het uitgevoerd op Knooppunt1:

![Knoop punt waarop Service wordt uitgevoerd](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Als u naar het knoop punt navigeert en naar de toepassing bladert, ziet u de essentiële knooppunt informatie, met inbegrip van de locatie op schijf.

![Locatie op schijf](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Als u naar de Directory bladert met behulp van Server Explorer, kunt u de werkmap en de logboekmap van de service vinden, zoals wordt weer gegeven in de volgende scherm afbeelding:

![Locatie van het logboek](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een uitvoerbaar gast bestand kunt inpakken en implementeren op Service Fabric. Raadpleeg de volgende artikelen voor gerelateerde informatie en taken.

* [Voor beeld voor het verpakken en implementeren van een uitvoerbaar gast bestand](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), met inbegrip van een koppeling naar de Prerelease van het verpakkings programma
* [Voor beeld van twee gast uitvoer bare bestanden (C# en nodejs) die communiceren via de naamgevings service met REST](https://github.com/Azure-Samples/service-fabric-containers)
* [Uw eerste Service Fabric-toepassing maken met Visual Studio](service-fabric-tutorial-create-dotnet-app.md)
