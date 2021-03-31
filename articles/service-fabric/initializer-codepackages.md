---
title: Initialisatie functie CodePackages in Service Fabric
description: Beschrijft initialisatie functie-CodePackages in Service Fabric.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: shsha
ms.openlocfilehash: 3be079b97c2660437344f88203fdda06cc6d6740
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86258974"
---
# <a name="initializer-codepackages"></a>CodePackages voor initialisatiefunctie

Vanaf versie 7,1 ondersteunt Service Fabric **initialisatie functie CodePackages** voor [containers][containers-introduction-link] en [uitvoer bare gast][guest-executables-introduction-link] toepassingen. Initialisatie functie CodePackages bieden de mogelijkheid om initialisaties uit te voeren in het ServicePackage-bereik voordat andere CodePackages worden gestart. Hun relatie met een ServicePackage is vergelijkbaar met wat een [SetupEntryPoint][setup-entry-point-link] voor een code package is.

Voordat u verder gaat met dit artikel, raden we u aan vertrouwd te raken met het [service Fabric-toepassings model][application-model-link] en het [service Fabric hosting model][hosting-model-link].

> [!NOTE]
> Initialisatie functie CodePackages worden momenteel niet ondersteund voor services die zijn geschreven met behulp van het [reliable Services][reliable-services-link] -programmeer model.
 
## <a name="semantics"></a>Semantiek

Er wordt verwacht dat een initialisatie functie-code package wordt uitgevoerd op **geslaagde voltooiing (afsluit code 0)**. Een mislukte initialisatie functie-code package wordt opnieuw gestart totdat deze is voltooid. Meerdere initialisatie-CodePackages zijn toegestaan en worden uitgevoerd tot een **geslaagde voltooiing**, **opeenvolgend** **in een opgegeven volg orde** voordat andere CodePackages in de ServicePackage worden gestart.

## <a name="specifying-initializer-codepackages"></a>Initialisatie functie CodePackages opgeven
U kunt een code package markeren als initializer door het **initialisatie** kenmerk in te stellen op **True** in de ServiceManifest. Wanneer er meerdere initialisatie-CodePackages zijn, kan de volg orde van uitvoering worden opgegeven via het kenmerk **ExecOrder** . **ExecOrder** moet een niet-negatief geheel getal zijn en is alleen geldig voor initializer CodePackages. Initialisatie functie CodePackages met lagere waarden van **ExecOrder** worden eerst uitgevoerd. Als **ExecOrder** niet is opgegeven voor een initialisatie functie code Package, wordt aangenomen dat de standaard waarde 0 is. De relatieve uitvoerings volgorde van de initialisatie functie-CodePackages met dezelfde waarde van **ExecOrder** is niet opgegeven.

Het volgende ServiceManifest-code fragment beschrijft drie CodePackages twee die zijn gemarkeerd als initialen. Als deze ServicePackage is geactiveerd, wordt *InitCodePackage0* eerst uitgevoerd, omdat deze de laagste waarde van **ExecOrder** heeft. Als de voltooiing is voltooid (afsluit code 0) van *InitCodePackage0*, wordt *InitCodePackage1* uitgevoerd. Ten slotte wordt *WorkloadCodePackage* uitgevoerd wanneer *InitCodePackage1* is voltooid.

```xml
<CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
  ...
</CodePackage>

<CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
  ...
</CodePackage>

<CodePackage Name="WorkloadCodePackage" Version="1.0">
  ...
</CodePackage>
```
## <a name="complete-example-using-initializer-codepackages"></a>Volledig voor beeld met behulp van initializer CodePackages

We kijken naar een volledig voor beeld met behulp van initializer CodePackages.

> [!IMPORTANT]
> In het volgende voor beeld wordt ervan uitgegaan dat u bekend bent met het maken van [Windows-container toepassingen met Service Fabric en docker][containers-getting-started-link].
>
> In dit voor beeld wordt verwezen naar mcr.microsoft.com/windows/nanoserver:1809. Windows Server-containers zijn niet compatibel in alle versies van een host-besturings systeem. Zie compatibiliteit met Windows- [container versie](/virtualization/windowscontainers/deploy-containers/version-compatibility)voor meer informatie.

De volgende ServiceManifest.xml bouwt voort op het ServiceManifest-code fragment dat eerder is beschreven. *InitCodePackage0*, *InitCodePackage1* en *WorkloadCodePackage* zijn CodePackages die containers vertegenwoordigen. Na activering wordt *InitCodePackage0* eerst uitgevoerd. Er wordt een bericht in een bestand geregistreerd en afgesloten. Vervolgens wordt *InitCodePackage1* uitgevoerd, waarmee ook een bericht wordt geregistreerd bij een bestand en wordt afgesloten. Ten slotte wordt de *WorkloadCodePackage* uitgevoerd. Ook wordt er een bericht naar een bestand geregistreerd, wordt de inhoud van het bestand naar **stdout** uitgevoerd en wordt vervolgens de opdracht ' altijd '.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsInitCodePackageServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows Init CodePackage Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsInitCodePackageServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage0. &gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage1. &gt;&gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="WorkloadCodePackage" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from WorkloadCodePackage. &gt;&gt; C:\WorkspaceOnContainer\log.txt &amp;&amp; type C:\WorkspaceOnContainer\log.txt &amp;&amp; ping -t 127.0.0.1 &gt; nul</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

In de volgende ApplicationManifest.xml wordt een toepassing beschreven op basis van de ServiceManifest.xml hierboven beschreven. Houd er rekening mee dat het dezelfde **volume** koppeling voor alle containers bevat, dat wil zeggen dat **C:\WorkspaceOnHost** wordt gekoppeld aan **C:\WorkspaceOnContainer** op alle drie de containers. Het net-effect is dat alle containers naar hetzelfde logboek bestand schrijven in de volg orde waarin ze zijn geactiveerd.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsInitCodePackageApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows Init CodePackage Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsInitCodePackageServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ContainerHostPolicies CodePackageRef="InitCodePackage0" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

     <ContainerHostPolicies CodePackageRef="InitCodePackage1" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

      <ContainerHostPolicies CodePackageRef="WorkloadCodePackage" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsInitCodePackageService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsInitCodePackageServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
Zodra de ServicePackage is geactiveerd, moet de inhoud van **C:\WorkspaceOnHost\log.txt** het volgende zijn.

```console
C:\Users\test>type C:\WorkspaceOnHost\log.txt
Hi from InitCodePackage0.
Hi from InitCodePackage1.
Hi from WorkloadCodePackage.
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor gerelateerde informatie.

* [Service Fabric en containers.][containers-introduction-link]
* [Uitvoer bare bestanden van Service Fabric en gast.][guest-executables-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[containers-getting-started-link]: service-fabric-get-started-containers.md
[guest-executables-introduction-link]: service-fabric-guest-executables-introduction.md
[reliable-services-link]: service-fabric-reliable-services-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
