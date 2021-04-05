---
title: Een script uitvoeren wanneer een Azure Service Fabric-service wordt gestart
description: Meer informatie over het configureren van een beleid voor een invoer punt voor een Service Fabric service-installatie en het uitvoeren van een script bij het starten van de service.
author: athinanthny
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: atsenthi
ms.openlocfilehash: a25f16f08ab8ae9564363f179d19d4b30c5315fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96012524"
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Het opstartscript van een service uitvoeren als lokale gebruiker of systeemaccount
Voordat een uitvoer bare Service Fabric-service wordt gestart, kan het nodig zijn om een configuratie of installatie uit te voeren.  Zo kunt u omgevings variabelen configureren. U kunt opgeven dat een script moet worden uitgevoerd voordat het uitvoer bare bestand van de service wordt gestart in het service manifest voor de service. Door een runas-beleid voor het toegangs punt voor de service-instelling te configureren, kunt u het account wijzigen waarmee het uitvoer bare installatie programma wordt uitgevoerd.  Met een afzonderlijk installatie-ingangs punt kunt u een configuratie met hoge bevoegdheden uitvoeren voor een korte periode, zodat het uitvoer bare bestand van de servicehost niet langer hoeft te worden uitgevoerd met hoge bevoegdheden voor langere Peri Oden.

Het installatie toegangs punt (**SetupEntryPoint** in het [service manifest](service-fabric-application-and-service-manifests.md)) is een geprivilegieerd toegangs punt dat standaard wordt uitgevoerd met dezelfde referenties als service Fabric (meestal het *netwerk service* account) vóór een ander toegangs punt. Het uitvoer bare bestand dat is opgegeven met **Entry Point** is doorgaans de langlopende servicehost. Het uitvoer bare bestand **Entry Point** wordt uitgevoerd nadat het uitvoer bare bestand van **SetupEntryPoint** is afgesloten. Het resulterende proces wordt gecontroleerd en opnieuw gestart, en begint opnieuw met **SetupEntryPoint** als het ooit wordt beëindigd of is vastgelopen. 

## <a name="configure-the-service-setup-entry-point"></a>Het toegangspunt voor service-instellingen configureren
Hier volgt een voor beeld van een eenvoudig service manifest voor een stateless service waarin een installatie script wordt opgegeven *MySetup.bat* in de service **SetupEntryPoint**.  **Argumenten** worden gebruikt om argumenten door te geven aan het script wanneer het wordt uitgevoerd.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyStatelessServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest.</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyStatelessServiceType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <Arguments>MyValue</Arguments>
        <WorkingFolder>Work</WorkingFolder>        
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyStatelessService.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Het beleid voor een toegangs punt voor een service-instelling configureren
Het uitvoer bare bestand van het service-installatie programma wordt standaard uitgevoerd onder dezelfde referenties als Service Fabric (meestal het *netwerk service* account).  In het toepassings manifest kunt u de beveiligings machtigingen wijzigen om het opstart script uit te voeren onder een lokaal systeem account of een beheerders account.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>Het beleid configureren met behulp van een lokaal systeem account
In het volgende voor beeld van een toepassings manifest ziet u hoe u het toegangs punt voor de service-instelling configureert om te worden uitgevoerd onder het beheerders account van de gebruiker (SetupAdminUser).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

Maak eerst een sectie **principals** met een gebruikers naam, zoals SetupAdminUser. Het SetupAdminUser-gebruikers account is lid van de systeem groep Administrators.

Configureer vervolgens onder de sectie **ServiceManifestImport** een beleid om deze principal toe te passen op **SetupEntryPoint**. Dit beleid vertelt Service Fabric dat wanneer het **MySetup.bat** -bestand wordt uitgevoerd, moet worden uitgevoerd als SetupAdminUser (met beheerders bevoegdheden). Omdat u *geen* beleid hebt toegepast op het hoofd toegangs punt, wordt de code in **MyServiceHost.exe** uitgevoerd onder het systeem **netwerk service** account. Dit is het standaard account dat alle service-invoer punten worden uitgevoerd als.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Het beleid configureren met behulp van lokale systeem accounts
Vaak is het raadzaam om het opstart script uit te voeren met een lokaal systeem account in plaats van een beheerders account. Het uitvoeren van het runas-beleid als lid van de groep Administrators werkt normaal gesp roken niet goed, omdat voor computers standaard gebruikers Access Control (UAC) zijn ingeschakeld. In dergelijke gevallen is het de aanbeveling om de SetupEntryPoint uit te voeren als LocalSystem, in plaats van een lokale gebruiker die is toegevoegd aan de groep Administrators. In het volgende voor beeld ziet u hoe de SetupEntryPoint moet worden uitgevoerd als LocalSystem:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
```

> [!NOTE]
> Voor Linux-clusters kunt u een service of het toegangs punt voor Setup als **root** uitvoeren door het  **account** type op te geven als **LocalSystem**.

## <a name="run-a-script-from-the-setup-entry-point"></a>Een script uitvoeren vanuit het installatie toegangs punt
Voeg nu een opstart script toe aan het project om uit te voeren onder Administrator bevoegdheden. 

Klik in Visual Studio met de rechter muisknop op het service project en voeg een nieuw bestand toe met de naam *MySetup.bat*.

Controleer vervolgens of het *MySetup.bat* bestand is opgenomen in het service pakket. Dit is standaard niet. Selecteer het bestand, klik met de rechter muisknop om het context menu weer te geven en kies **Eigenschappen**. Zorg er in het dialoog venster Eigenschappen voor dat **kopiëren naar uitvoermap** is ingesteld op **kopiëren indien nieuwer**. Zie de volgende schermafbeelding.

![Visual Studio CopyToOutput voor SetupEntryPoint-batch bestand][image1]

Bewerk nu het *MySetup.bat* -bestand en voeg de volgende opdrachten toe: Stel een systeem omgevings variabele in en uitvoer een tekst bestand.

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Vervolgens bouwt en implementeert u de oplossing in een lokaal ontwikkelings cluster. Nadat de service is gestart, zoals wordt weer gegeven in [service Fabric Explorer](service-fabric-visualizing-your-cluster.md), kunt u zien dat het MySetup.bat bestand op twee manieren is geslaagd. Open een Power shell-opdracht prompt en typ het volgende:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Noteer vervolgens de naam van het knoop punt waarop de service is geïmplementeerd en start in [service Fabric Explorer](service-fabric-visualizing-your-cluster.md). Bijvoorbeeld, knoop punt 2. Ga vervolgens naar de map werk van het toepassings exemplaar om het out.txt bestand te vinden waarin de waarde van **TestVariable** wordt weer gegeven. Als deze service bijvoorbeeld is geïmplementeerd op knoop punt 2, kunt u naar dit pad gaan voor de **MyApplicationType**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>Power shell-opdrachten uitvoeren vanaf een invoer punt voor Setup
Als u Power shell wilt uitvoeren vanaf het **SetupEntryPoint** -punt, kunt u **PowerShell.exe** uitvoeren in een batch-bestand dat verwijst naar een Power shell-bestand. Voeg eerst een Power shell-bestand toe aan het service project, bijvoorbeeld **MySetup.ps1**. Vergeet niet om de eigenschap *copy als nieuwer* in te stellen, zodat het bestand ook in het service pakket is opgenomen. In het volgende voor beeld ziet u een batch bestand met een Power shell-bestand met de naam MySetup.ps1, waarmee een systeem omgevings variabele wordt ingesteld met de naam **TestVariable**.

MySetup.bat een Power shell-bestand te starten:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

Voeg in het Power shell-bestand het volgende toe om een systeem omgevings variabele in te stellen:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Wanneer het batch bestand wordt uitgevoerd, wordt standaard de toepassingsmap met de naam **werk** voor bestanden weer gegeven. Als MySetup.bat wordt uitgevoerd, willen we in dit geval het MySetup.ps1-bestand in dezelfde map vinden. Dit is de map van het toepassings **code pakket** . Als u deze map wilt wijzigen, stelt u de werkmap in:
> 
> 

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
```

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>Een opstart script lokaal oplossen met console omleiding
In sommige gevallen is het handig voor fout opsporing om de console-uitvoer te zien van het uitvoeren van een installatie script. U kunt een beleid voor het omleiden van console instellen op het installatie punt in het service manifest, waarmee de uitvoer naar een bestand wordt geschreven. De bestands uitvoer wordt geschreven naar de toepassingsmap **logboek** op het cluster knooppunt waar de toepassing wordt geïmplementeerd en uitgevoerd. 

> [!WARNING]
> Gebruik nooit het beleid voor omleiding van console in een toepassing die in productie is geïmplementeerd, omdat dit van invloed kan zijn op de failover van de toepassing. Gebruik dit *alleen* voor lokale ontwikkeling en fout opsporing.  
> 
> 

In het volgende service manifest voorbeeld ziet u hoe u de console omleiding instelt met een FileRetentionCount-waarde:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Als u nu het MySetup.ps1 bestand wijzigt om een **echo** opdracht te schrijven, wordt dit naar het uitvoer bestand geschreven voor fout opsporing:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> Nadat u het script hebt opgespoord, verwijdert u dit beleid voor omleiding van de console direct.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over de beveiliging van toepassingen en services](service-fabric-application-and-service-security.md)
* [Inzicht in het toepassings model](service-fabric-application-model.md)
* [Resources opgeven in een service manifest](service-fabric-service-manifest-resources.md)
* [Een app implementeren](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
