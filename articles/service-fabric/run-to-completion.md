---
title: RunToCompletion-semantiek in Service Fabric
description: Beschrijft RunToCompletion-semantiek in Service Fabric.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: 6f2f6aa4380fcf6909957118bf682275350ce68c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000263"
---
# <a name="runtocompletion"></a>RunToCompletion

Vanaf versie 7,1 biedt Service Fabric ondersteuning voor **RunToCompletion** -semantiek voor [containers][containers-introduction-link] en [uitvoer bare gast][guest-executables-introduction-link] toepassingen. Met deze semantiek kunnen toepassingen en services die een taak uitvoeren en worden afgesloten, in tegens telling tot, altijd uitgevoerde toepassingen en services.

Voordat u verder gaat met dit artikel, raden we u aan vertrouwd te raken met het [service Fabric-toepassings model][application-model-link] en het [service Fabric hosting model][hosting-model-link].

> [!NOTE]
> RunToCompletion-semantiek wordt momenteel niet ondersteund voor services die zijn geschreven met behulp van het [reliable Services][reliable-services-link] -programmeer model.
 
## <a name="runtocompletion-semantics-and-specification"></a>RunToCompletion-semantiek en-specificatie
RunToCompletion-semantiek kunnen worden opgegeven als een **ExecutionPolicy** bij [het importeren van de ServiceManifest][application-and-service-manifests-link]. Het opgegeven beleid wordt overgenomen door alle CodePackages die de ServiceManifest omvatten. Het volgende ApplicationManifest.xml fragment bevat een voor beeld.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
**ExecutionPolicy** maakt de volgende twee kenmerken mogelijk:
* **Type:** **RunToCompletion** is momenteel de enige toegestane waarde voor dit kenmerk.
* **Opnieuw opstarten:** Met dit kenmerk geeft u het beleid voor opnieuw opstarten op dat wordt toegepast op CodePackages die bestaan uit de ServicePackage, bij fout. Een code package die wordt afgesloten met een **afsluit code die niet gelijk** is aan nul, wordt gezien als mislukt. Toegestane waarden voor dit kenmerk zijn **OnFailure** en **nooit** met **OnFailure** de standaard instelling.

Wanneer het beleid voor opnieuw opstarten is ingesteld op **OnFailure**, wordt het opnieuw gestart met een back-up tussen herhaalde fouten als een code package mislukt **(niet-nul afsluit code)**. Wanneer het beleid voor opnieuw opstarten niet is ingesteld op **nooit**, wordt de implementatie status van de DeployedServicePackage als **mislukt** gemarkeerd, maar andere CodePackages zijn toegestaan om door te gaan met de uitvoering. Als alle CodePackages waaruit de ServicePackage wordt uitgevoerd, zijn voltooid **(afsluit code 0)**, wordt de implementatie status van de DeployedServicePackage als **RanToCompletion** gemarkeerd. 

## <a name="complete-example-using-runtocompletion-semantics"></a>Volledig voor beeld met behulp van RunToCompletion-semantiek

Laten we eens kijken naar een volledig voor beeld met behulp van RunToCompletion-semantiek.

> [!IMPORTANT]
> In het volgende voor beeld wordt ervan uitgegaan dat u bekend bent met het maken van [Windows-container toepassingen met Service Fabric en docker][containers-getting-started-link].
>
> In dit voor beeld wordt verwezen naar mcr.microsoft.com/windows/nanoserver:1809. Windows Server-containers zijn niet compatibel in alle versies van een host-besturings systeem. Zie compatibiliteit met Windows- [container versie](/virtualization/windowscontainers/deploy-containers/version-compatibility)voor meer informatie.

In de volgende ServiceManifest.xml wordt een ServicePackage beschreven dat bestaat uit twee CodePackages, die containers vertegenwoordigen. *RunToCompletionCodePackage1* meldt alleen een bericht aan **stdout** en wordt afgesloten. *RunToCompletionCodePackage2* pingt het loop back-adres voor een tijdje en sluit af met een afsluit code van **0**, **1** of **2**.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsRunToCompletionServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows RunToCompletion Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsRunToCompletionServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="RunToCompletionCodePackage1" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from RunToCompletionCodePackage1 &amp;&amp; exit 0</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="RunToCompletionCodePackage2" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/v,/c,ping 127.0.0.1 &amp;&amp; set /a exitCode=%random% % 3 &amp;&amp; exit !exitCode!</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

In de volgende ApplicationManifest.xml wordt een toepassing beschreven op basis van de ServiceManifest.xml hierboven beschreven. Hiermee wordt **RunToCompletion** **ExecutionPolicy** voor *WindowsRunToCompletionServicePackage* opgegeven met het beleid voor opnieuw opstarten van **OnFailure**. Bij de activering van *WindowsRunToCompletionServicePackage* wordt het onderdeel CodePackages gestart. *RunToCompletionCodePackage1* moet bij de eerste activering worden afgesloten. *RunToCompletionCodePackage2* kan echter mislukken **(niet-nul afsluit code)**, in dat geval wordt de computer opnieuw opgestart nadat het beleid voor opnieuw opstarten is **OnFailure**.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsRunToCompletionApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows RunToCompletion Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsRunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsRunToCompletionService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsRunToCompletionServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="querying-deployment-status-of-a-deployedservicepackage"></a>De implementatie status van een DeployedServicePackage opvragen
De implementatie status van een DeployedServicePackage kan worden opgevraagd vanuit Power shell met [Get-ServiceFabricDeployedServicePackage][deployed-service-package-link] of vanuit C# met behulp van [FabricClient][fabric-client-link] API [GetDeployedServicePackageListAsync (teken reeks, URI, teken reeks)][deployed-service-package-fabricclient-link]

## <a name="considerations-when-using-runtocompletion-semantics"></a>Overwegingen bij het gebruik van RunToCompletion-semantiek

De volgende punten moeten worden genoteerd voor de huidige RunToCompletion-ondersteuning.
* Deze semantiek worden alleen ondersteund voor [containers][containers-introduction-link] en [uitvoer bare gast][guest-executables-introduction-link] toepassingen.
* Upgrade scenario's voor toepassingen met RunToCompletion-semantiek zijn niet toegestaan. Gebruikers moeten deze toepassingen, indien nodig, verwijderen en opnieuw maken.
* Failover-gebeurtenissen kunnen ervoor zorgen dat CodePackages opnieuw wordt uitgevoerd nadat de voltooiing is voltooid, op hetzelfde knoop punt of op andere knoop punten van het cluster. Voor beelden van failover-gebeurtenissen zijn het opnieuw opstarten van knoop punten en het Service Fabric van runtime-upgrades op een knoop punt.

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
[application-and-service-manifests-link]: service-fabric-application-and-service-manifests.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
[deployed-service-package-working-with-link]: service-fabric-hosting-model.md#work-with-a-deployed-service-package
[deployed-code-package-link]: /powershell/module/servicefabric/get-servicefabricdeployedcodepackage
[deployed-service-package-link]: /powershell/module/servicefabric/get-servicefabricdeployedservicepackage
[fabric-client-link]: /dotnet/api/system.fabric.fabricclient
[deployed-service-package-fabricclient-link]: /dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync
