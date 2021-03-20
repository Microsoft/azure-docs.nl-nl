---
title: Een service uitvoeren onder systeem-en lokale beveiligings accounts
description: Meer informatie over het uitvoeren van een Service Fabric-toepassing onder systeem-en lokale beveiligings accounts.  Maak beveiligings-principals en pas het Run-As-beleid toe om uw services veilig uit te voeren.
ms.topic: conceptual
ms.date: 03/29/2018
ms.openlocfilehash: 53212f8636602705899834b6db1d3f0d80b5fe4f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75610115"
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Een service uitvoeren als een lokaal gebruikers account of lokaal systeem account
Door Azure Service Fabric te gebruiken, kunt u toepassingen die in het cluster worden uitgevoerd, beveiligen onder verschillende gebruikers accounts. Service Fabric toepassingen worden standaard uitgevoerd onder het account dat door het Fabric.exe proces wordt uitgevoerd. Service Fabric biedt ook de mogelijkheid om toepassingen uit te voeren onder een lokale gebruiker of systeem account. Ondersteunde typen lokale systeem accounts zijn **lokalegebruiker**, **Network Service**, **LocalService** en **LocalSystem**.  Als u Service Fabric uitvoert op een zelfstandige Windows-cluster, kunt u een service uitvoeren onder [Active Directory domein accounts](service-fabric-run-service-as-ad-user-or-group.md) of door de [groep beheerde service accounts](service-fabric-run-service-as-gmsa.md).

In het manifest van de toepassing definieert u de gebruikers accounts die vereist zijn voor het uitvoeren van services of het beveiligen van resources in de sectie **principals** . U kunt ook gebruikers groepen definiëren en maken, zodat een of meer gebruikers samen kunnen worden beheerd. Dit is handig wanneer er meerdere gebruikers zijn voor verschillende service toegangs punten en er algemene bevoegdheden nodig zijn op het groeps niveau.  Er wordt naar de gebruikers verwezen in een runas-beleid dat wordt toegepast op een specifieke service of voor alle services in de toepassing. 

Standaard wordt het runas-beleid toegepast op het hoofd toegangs punt.  U kunt ook een runas-beleid Toep assen op het installatie toegangs punt als u [bepaalde instellingen voor het instellen van hoge bevoegdheden wilt uitvoeren onder een systeem account](service-fabric-run-script-at-service-startup.md)of voor de hoofd-en Setup-toegangs punten.  

> [!NOTE] 
> Als u een runas-beleid toepast op een service en het service manifest declareert eindpunt resources met het HTTP-protocol, moet u een **SecurityAccessPolicy** opgeven.  Zie [beleid voor beveiligings toegang toewijzen voor HTTP-en HTTPS-eind punten](service-fabric-assign-policy-to-endpoint.md)voor meer informatie. 
>

## <a name="run-a-service-as-a-local-user"></a>Een service uitvoeren als een lokale gebruiker
U kunt een lokale gebruiker maken die kan worden gebruikt om een service in de toepassing te beveiligen. Wanneer een **lokalegebruiker** -account type is opgegeven in de sectie principals van het manifest van de toepassing, maakt service Fabric lokale gebruikers accounts op computers waarop de toepassing wordt geïmplementeerd. Standaard hebben deze accounts niet dezelfde namen als die zijn opgegeven in het manifest van de toepassing (bijvoorbeeld *Customer3* in het volgende voor beeld van een manifest van de toepassing). In plaats daarvan worden ze dynamisch gegenereerd en hebben ze wille keurige wacht woorden.

Geef in de sectie **RunAsPolicy** voor een **ServiceManifestImport** het gebruikers account op in de sectie **principals** om het service code pakket uit te voeren.  In het volgende voor beeld ziet u hoe u een lokale gebruiker maakt en een runas-beleid toepast op het hoofd toegangs punt:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="Customer3" />
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="create-a-local-user-group"></a>Een lokale gebruikers groep maken
U kunt gebruikers groepen maken en een of meer gebruikers aan de groep toevoegen. Dit is handig als er meerdere gebruikers zijn voor verschillende service toegangs punten en er bepaalde algemene bevoegdheden moeten zijn die beschikbaar zijn op het groeps niveau. In het volgende voor beeld van een toepassings manifest wordt een lokale groep met de naam *LocalAdminGroup* met Administrator bevoegdheden weer gegeven. Twee gebruikers, *Customer1* en *Customer2*, maken deel uit van deze lokale groep. In de sectie **ServiceManifestImport** wordt een runas-beleid toegepast om het *Stateful1Pkg* -code pakket uit te voeren als *Customer2*.  Er wordt een ander runas-beleid toegepast om het *Web1Pkg* -code pakket uit te voeren als *Customer1*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer2" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="Stateful1" ServicePackageActivationMode="ExclusiveProcess">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Groups>
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <User Name="Customer1">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <User Name="Customer2">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Een standaard beleid Toep assen op alle service code pakketten
U gebruikt de sectie **DefaultRunAsPolicy** om een standaard gebruikers account op te geven voor alle code pakketten waarvoor geen specifieke **RunAsPolicy** is gedefinieerd. Als de meeste code pakketten die zijn opgegeven in het service manifest dat door een toepassing wordt gebruikt, onder dezelfde gebruiker moeten worden uitgevoerd, kan de toepassing alleen een standaard runas-beleid definiëren met dat gebruikers account. In het volgende voor beeld wordt aangegeven dat als er voor een code pakket geen **RunAsPolicy** is opgegeven, het code pakket moet worden uitgevoerd onder de **MyDefaultAccount** -gebruiker die is opgegeven in de sectie principals.  Ondersteunde account typen zijn Lokalegebruiker, Network Service, LocalSystem en LocalService.  Als u een lokale gebruiker of service gebruikt, geeft u ook de account naam en het wacht woord op.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <Policies>
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
  </Policies>
</ApplicationManifest>
```

## <a name="debug-a-code-package-locally-using-console-redirection"></a>Fouten opsporen in een code pakket lokaal met behulp van console omleiding
In sommige gevallen is het handig voor fout opsporing om de console-uitvoer van een actieve service te bekijken. U kunt een beleid voor omleiding van console instellen op het toegangs punt in het service manifest, waarmee de uitvoer naar een bestand wordt geschreven. De bestands uitvoer wordt geschreven naar de toepassingsmap **logboek** op het cluster knooppunt waar de toepassing wordt geïmplementeerd en uitgevoerd. 

> [!WARNING]
> Gebruik nooit het beleid voor omleiding van console in een toepassing die in productie is geïmplementeerd, omdat dit van invloed kan zijn op de failover van de toepassing. Gebruik dit *alleen* voor lokale ontwikkeling en fout opsporing.  
> 
> 

In het volgende voor beeld van een service manifest wordt het inschakelen van console omleiding met een FileRetentionCount-waarde weer gegeven:

```xml
<CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
        <ConsoleRedirection FileRetentionCount="10"/>
      </ExeHost>
    </EntryPoint>
</CodePackage>

```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Volgende stappen
* [Inzicht in het toepassings model](service-fabric-application-model.md)
* [Resources opgeven in een service manifest](service-fabric-service-manifest-resources.md)
* [Een app implementeren](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
