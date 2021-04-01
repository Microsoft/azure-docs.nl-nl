---
title: Voor beelden van betrouw bare Services app-manifesten
description: Meer informatie over het configureren van de toepassings-en service manifest instellingen voor een betrouw bare Services Service Fabric toepassing.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: f40e54f5260f827f0b18c833d23d1f57b5ebc3a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84701095"
---
# <a name="reliable-services-application-and-service-manifest-examples"></a>Voorbeelden van toepassings- en servicemanifesten voor betrouwbare services
Hier volgen enkele voor beelden van de toepassings-en service manifesten voor een Service Fabric toepassing met een ASP.NET Core web-front-end en een stateful back-end. Het doel van deze voor beelden is om te laten zien welke instellingen beschikbaar zijn en hoe u deze kunt gebruiken. Deze toepassings-en service manifesten zijn gebaseerd op de [service Fabric .net Quick](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) start-manifesten.

De volgende functies worden weer gegeven:

|Manifest|Functies|
|---|---|
|[Manifest van de toepassing](#application-manifest)| [resource](service-fabric-resource-governance.md)beheer, [een service uitvoeren als een lokaal beheerders account](service-fabric-application-runas-security.md), [een standaard beleid Toep assen op alle service code pakketten](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages), [gebruikers-en groeps-principals maken](service-fabric-application-runas-security.md), een gegevens pakket delen tussen service-instanties, [service-eind punten overschrijven](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)| 
|FrontEndService-service manifest| [Een script uitvoeren bij het starten van de service](service-fabric-run-script-at-service-startup.md), [een HTTPS-eind punt definiëren](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest) | 
|BackEndService-service manifest| [Een configuratie pakket declareren](service-fabric-application-and-service-manifests.md), [een gegevens pakket declareren](service-fabric-application-and-service-manifests.md), [een eind punt configureren](service-fabric-service-manifest-resources.md)| 

Zie manifest elementen van de [toepassing](#application-manifest-elements), VotingWeb van de [service manifest elementen](#votingweb-service-manifest-elements)en de [VotingData](#votingdata-service-manifest-elements)

## <a name="application-manifest"></a>Manifest van de toepassing

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="Memory" DefaultValue="4084" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="Port" DefaultValue="8081" />
    <Parameter Name="Protocol" DefaultValue="tcp" />
    <Parameter Name="Type" DefaultValue="internal" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <!-- Override endpoints declared in the service manifest. -->
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="DataEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
      </Endpoints>
    </ResourceOverrides>

    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[Memory]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[Memory]" BlockIOWeight="[BlockIOWeight]" 
                                MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" 
                                MemorySwapInMB="[MemorySwapInMB]"/>

      <!-- Share the data package across multiple instances of the VotingData service-->
      <PackageSharingPolicy PackageRef="Data"/>

      <!-- Give read rights on the "DataEndpoint" endpoint to the Customer2 account.-->
      <SecurityAccessPolicy GrantRights="Read" PrincipalRef="Customer2" ResourceRef="DataEndpoint" ResourceType="Endpoint"/>         
    </Policies>
  </ServiceManifestImport>
  
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Run the setup entry point (defined in the imported service manifest) as the SetupAdminUser account 
      (declared in the following Principals section). -->
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      
    </Policies>

  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <!-- Define users and groups required to run the services and access resources.  Principals are used in the Policies section(s). -->
  <Principals>
    <!-- Declare a set of groups as security principals, which can be referenced in policies. Groups are useful if there are multiple users 
    for different service entry points and they need to have certain common privileges that are available at the group level. -->
    <Groups>
      <!-- Create a group that has administrator privileges. -->
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <!-- Declare a user and add the user to the Administrators system group. The SetupAdminUser account is used to run the 
      setup entry point of the VotingWebPkg code package (described in the preceding Policies section).-->
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
      <!-- Create a user. Local user accounts are created on the machines where the application is deployed. By default, these accounts 
      do not have the same names as those specified here. Instead, they are dynamically generated and have random passwords. -->
      <User Name="Customer1" >
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as a local user with the specified account name and password. Local user accounts are created on the machines 
      where the application is deployed. -->
      <User Name="Customer2" AccountType="LocalUser" AccountName="Customer1" Password="MyPassword">
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as NetworkService. -->
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <!-- Policies applied at the application level. -->
  <Policies>
    <!-- Specify a default user account for all code packages that don’t have a specific RunAsPolicy defined in 
    the ServiceManifestImport section(s). -->
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
    
  </Policies>
</ApplicationManifest>

```

## <a name="votingweb-service-manifest"></a>VotingWeb-service manifest

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <!-- A privileged entry point that by default runs with the same credentials as Service Fabric (typically the NetworkService account) before 
    any other entry point. Use the setup entry point to set system environment variables, give the account running the service (NETWORK SERVICE, by default) 
    access to a certificate's private key, or perform other setup tasks. In the application manifest, you can change the security permissions to run the startup script 
    under a local system account or an administrator account.  -->
    <SetupEntryPoint>
      <ExeHost>
        <!-- The setup script to run. -->
        <Program>Setup.bat</Program>
        <!-- Pass arguments to the script when it runs.-->
        <Arguments>MyValue</Arguments>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
        <!-- Warning! Do not use console redirection in a production application, only use it for local development and debugging. Redirects console output from the startup
        script to an output file in the application folder called "log" on the cluster node where the application is deployed and run. Also set the number of output files
        to retain and the maximum file size (in KB). -->
        <ConsoleRedirection FileRetentionCount="10" FileMaxSizeInKb="20480"/>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- Configure a HTTPS endpoint on port 443. This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>

```

## <a name="votingdata-service-manifest"></a>VotingData-service manifest

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingDataPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="VotingDataType"  HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingData.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Declares a folder, named by the Name attribute, under PackageRoot that contains a Settings.xml file. This file contains sections of user-defined, 
  key-value pair settings that the process can read back at run time. During an upgrade, if only the ConfigPackage version has changed, 
  then the running process is not restarted. Instead, a callback notifies the process that configuration settings have changed so they can be reloaded dynamically. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Declares a folder, named by the Name attribute, under PackageRoot which contains static data files to be consumed by the process at run time. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- Define an internal (used for intra-application communication) TCP endpoint. Since no port is specified, one is created and assigned dynamically
           to the service.-->
      <Endpoint Name="DataEndpoint" Protocol="tcp" Type="Internal" />
    </Endpoints>
  </Resources>

</ServiceManifest>

```

## <a name="application-manifest-elements"></a>Manifest elementen van toepassing
### <a name="applicationmanifest-element"></a>ApplicationManifest-element
Hiermee worden het toepassings type en de versie declaratief beschreven. Er wordt verwezen naar een of meer service manifesten van de onderdeel Services om een toepassings type samen te stellen. Configuratie-instellingen van de onderdeel Services kunnen worden overschreven met behulp van geparametriseerde toepassings instellingen. Standaard services, service sjablonen, principals, beleids regels, diagnostische instellingen en certificaten kunnen ook worden gedeclareerd op toepassings niveau. Zie het [element ApplicationManifest](service-fabric-service-model-schema-elements.md#ApplicationManifestElementApplicationManifestTypeComplexType) voor meer informatie.

### <a name="parameters-element"></a>Para meters-element
Declareert de para meters die worden gebruikt in dit manifest van de toepassing. De waarde van deze para meters kan worden opgegeven wanneer de toepassing wordt geïnstantieerd en kan worden gebruikt voor het overschrijven van de configuratie-instellingen van de toepassing of service. Zie [para meters element](service-fabric-service-model-schema-elements.md#ParametersElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType) voor meer informatie.

### <a name="parameter-element"></a>Parameter element
Een toepassings parameter die in dit manifest moet worden gebruikt. De parameter waarde kan worden gewijzigd tijdens het instantiëren van de toepassing, of als er geen waarde wordt opgegeven, wordt de standaard waarde gebruikt. Zie [para meter element](service-fabric-service-model-schema-elements.md#ParameterElementanonymouscomplexTypeComplexTypeDefinedInParameterselement) voor meer informatie.

### <a name="servicemanifestimport-element"></a>ServiceManifestImport-element
Hiermee wordt een service manifest geïmporteerd dat door de service ontwikkelaar is gemaakt. Voor elke onderdeel service in de toepassing moet een service manifest worden geïmporteerd. Overschrijvingen en beleids regels voor de configuratie kunnen worden gedeclareerd voor het service manifest. Zie het [element ServiceManifestImport](service-fabric-service-model-schema-elements.md#ServiceManifestImportElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType) voor meer informatie.

### <a name="servicemanifestref-element"></a>ServiceManifestRef-element
Hiermee importeert u het service manifest op referentie. Het manifest bestand van de service (ServiceManifest.xml) moet momenteel aanwezig zijn in het build-pakket. Zie het [element ServiceManifestRef](service-fabric-service-model-schema-elements.md#ServiceManifestRefElementServiceManifestRefTypeComplexTypeDefinedInServiceManifestImportelement) voor meer informatie.

### <a name="resourceoverrides-element"></a>ResourceOverrides-element
Hiermee geeft u de resource onderdrukkingen voor eind punten die zijn gedeclareerd in service manifest resources. Zie het [element ResourceOverrides](service-fabric-service-model-schema-elements.md#ResourceOverridesElementResourceOverridesTypeComplexTypeDefinedInServiceManifestImportelement) voor meer informatie.

### <a name="endpoints-element"></a>Element endpoints
Het eind punt (en) dat moet worden overschreven. Zie het [element endpoints](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourceOverridesTypecomplexType) voor meer informatie.

### <a name="endpoint-element"></a>Eindpunt element
Het eind punt dat is gedefinieerd in het service manifest om te overschrijven. Zie voor meer informatie [eindpunt element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

### <a name="policies-element"></a>Element beleid
Hierin worden de beleids regels (End-Point binding, Package sharing, run-as en Security Access) beschreven die moeten worden toegepast op het geïmporteerde service manifest. Zie [Policies](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement) (Engelstalig) voor meer informatie.

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy-element
Definieert het bron beheer beleid dat wordt toegepast op het niveau van het hele service pakket. Zie het [element ServicePackageResourceGovernancePolicy](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType) voor meer informatie.

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy-element
Hiermee geeft u de resource limieten voor een code package op. Zie het [element ResourceGovernancePolicy](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement) voor meer informatie.

### <a name="packagesharingpolicy-element"></a>Packages haring Policy-element
Hiermee wordt aangegeven of een code, configuratie of gegevens pakket moet worden gedeeld tussen service-exemplaren van hetzelfde service type. Zie het [element packages haring Policy](service-fabric-service-model-schema-elements.md#PackageSharingPolicyElementPackageSharingPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexType) voor meer informatie.

### <a name="securityaccesspolicy-element"></a>SecurityAccessPolicy-element
Hiermee worden toegangs machtigingen verleend aan een principal voor een bron (zoals een eind punt) die is gedefinieerd in een service manifest. Normaal gesp roken is het zeer nuttig om de toegang tot services te beheren en te beperken tot verschillende bronnen om beveiligings Risico's te minimaliseren. Dit is vooral belang rijk wanneer de toepassing wordt gebouwd op basis van een verzameling services van een Marketplace die door verschillende ontwikkel aars is ontwikkeld. Zie het [element SecurityAccessPolicy](service-fabric-service-model-schema-elements.md#SecurityAccessPolicyElementSecurityAccessPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInSecurityAccessPolicieselementDefinedInDigestedEndpointelement) voor meer informatie.

### <a name="runaspolicy-element"></a>RunAsPolicy-element
Hiermee geeft u de lokale gebruiker of het lokale systeem account op dat als service code pakket wordt uitgevoerd. Domein accounts worden ondersteund op Windows Server-implementaties waar Azure Active Directory beschikbaar is. Toepassingen worden standaard uitgevoerd onder het account waaronder het Fabric.exe-proces wordt uitgevoerd. Toepassingen kunnen ook worden uitgevoerd als andere accounts, die moeten worden gedeclareerd in de sectie principals. Als u een runas-beleid toepast op een service, en het service manifest declareert eindpunt resources met het HTTP-protocol, moet u ook een SecurityAccessPolicy opgeven om ervoor te zorgen dat de poorten die aan deze eind punten worden toegewezen, op de juiste wijze toegang hebben tot de controle die wordt vermeld voor de RunAs-gebruikers account waarmee de service wordt uitgevoerd. Voor een HTTPS-eind punt moet u ook een EndpointBindingPolicy definiëren om de naam van het certificaat aan te geven dat naar de client moet worden geretourneerd. Zie het [element RunAsPolicy](service-fabric-service-model-schema-elements.md#RunAsPolicyElementRunAsPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement) voor meer informatie.

### <a name="defaultservices-element"></a>DefaultServices-element
Declareert service-exemplaren die automatisch worden gemaakt wanneer een toepassing wordt geïnstantieerd voor dit toepassings type. Zie het [element DefaultServices](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType) voor meer informatie.

### <a name="service-element"></a>Service-element
Declareert een service die automatisch moet worden gemaakt wanneer de toepassing wordt geïnstantieerd. Zie [service-element](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType) voor meer informatie.

### <a name="statefulservice-element"></a>StatefulService-element
Hiermee definieert u een stateful service. Zie het [element StatefulService](service-fabric-service-model-schema-elements.md#StatefulServiceElementStatefulServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement) voor meer informatie.

### <a name="statelessservice-element"></a>StatelessService-element
Hiermee definieert u een stateless service. Zie het [element StatelessService](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement) voor meer informatie.

### <a name="principals-element"></a>Principals-element
Hierin worden de beveiligings-principals (gebruikers, groepen) beschreven die vereist zijn voor deze toepassing om services uit te voeren en bronnen te beveiligen. Er wordt verwezen naar principals in de secties van het beleid. Zie het [element principals](service-fabric-service-model-schema-elements.md#PrincipalsElementSecurityPrincipalsTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType) voor meer informatie.

### <a name="groups-element"></a>Element groepen
Declareert een set groepen als beveiligings-principals waarnaar kan worden verwezen in beleid. Groepen zijn handig als er meerdere gebruikers zijn voor verschillende service toegangs punten en er bepaalde algemene bevoegdheden moeten zijn die beschikbaar zijn op het groeps niveau. Zie voor meer informatie [Groups-element](service-fabric-service-model-schema-elements.md#GroupsElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="group-element"></a>Groeps element
Declareert een groep als een beveiligingsprincipal, waarnaar in het beleid kan worden verwezen. Zie voor meer informatie [groeps element](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInGroupselement)

### <a name="membership-element"></a>Membership-element
 Zie [membership element](service-fabric-service-model-schema-elements.md#MembershipElementanonymouscomplexTypeComplexTypeDefinedInGroupelement) voor meer informatie.

### <a name="systemgroup-element"></a>SystemGroup-element
 Zie het [element SystemGroup](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMembershipelement) voor meer informatie.

### <a name="users-element"></a>Element users
Declareert een set gebruikers als beveiligings-principals waarnaar kan worden verwezen in beleid. Zie voor meer informatie [gebruikers element](service-fabric-service-model-schema-elements.md#UsersElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="user-element"></a>Gebruikers element
Declareert een gebruiker als een beveiligingsprincipal, waarnaar kan worden verwezen in beleid. Zie [User element](service-fabric-service-model-schema-elements.md#UserElementanonymouscomplexTypeComplexTypeDefinedInUserselement) (Engelstalig) voor meer informatie.

### <a name="memberof-element"></a>Element MemberOf
Gebruikers kunnen worden toegevoegd aan een bestaande lidmaatschaps groep, zodat deze alle eigenschappen en beveiligings instellingen van die lidmaatschaps groep kan overnemen. De lidmaatschaps groep kan worden gebruikt voor het beveiligen van externe bronnen die toegankelijk moeten zijn voor verschillende services of dezelfde service (op een andere machine). Zie het [element MemberOf](service-fabric-service-model-schema-elements.md#MemberOfElementanonymouscomplexTypeComplexTypeDefinedInUserelement) voor meer informatie.

### <a name="systemgroup-element"></a>SystemGroup-element
De systeem groep waaraan de gebruiker moet worden toegevoegd.  De systeem groep moet worden gedefinieerd in de sectie groepen. Zie het [element SystemGroup](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement) voor meer informatie.

### <a name="group-element"></a>Groeps element
De groep waaraan de gebruiker moet worden toegevoegd.  De groep moet worden gedefinieerd in de sectie groepen. Zie voor meer informatie [groeps element](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="policies-element"></a>Element beleid
Hierin wordt het beleid beschreven (logboek verzameling, standaard uitvoeren als, status en beveiliging) die op toepassings niveau moeten worden toegepast. Zie [Policies](service-fabric-service-model-schema-elements.md#PoliciesElementApplicationPoliciesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType) (Engelstalig) voor meer informatie.

### <a name="defaultrunaspolicy-element"></a>DefaultRunAsPolicy-element
Geef een standaard gebruikers account op voor alle service code pakketten waarvoor geen specifieke RunAsPolicy is gedefinieerd in de sectie ServiceManifestImport. Zie het [element DefaultRunAsPolicy](service-fabric-service-model-schema-elements.md#DefaultRunAsPolicyElementanonymouscomplexTypeComplexTypeDefinedInApplicationPoliciesTypecomplexType) voor meer informatie.




## <a name="votingweb-service-manifest-elements"></a>VotingWeb-service manifest elementen
### <a name="servicemanifest-element"></a>ServiceManifest-element
Hiermee worden het Service type en de versie declaratief beschreven. Hierin worden de onafhankelijk code ring, configuratie en gegevens pakketten vermeld die samen een service pakket vormen ter ondersteuning van een of meer service typen. Er zijn ook resources, diagnostische instellingen en meta gegevens van de service, zoals service type, status eigenschappen en metrische taak verdeling, opgegeven. Zie het [element ServiceManifest](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType) voor meer informatie.

### <a name="servicetypes-element"></a>ServiceTypes-element
Hiermee definieert u welke service typen worden ondersteund door een code package in dit manifest. Wanneer een service wordt geïnstantieerd voor een van deze service typen, worden alle code pakketten die zijn gedeclareerd in dit manifest geactiveerd door hun toegangs punten uit te voeren. Service typen worden op manifest niveau gedeclareerd en niet op het niveau van het code pakket. Zie het [element ServiceTypes](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType) voor meer informatie.

### <a name="statelessservicetype-element"></a>StatelessServiceType-element
Beschrijft een stateless service type. Zie het [element StatelessServiceType](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType) voor meer informatie.

### <a name="codepackage-element"></a>Code package-element
Beschrijft een code pakket dat een gedefinieerd Service type ondersteunt. Wanneer een service wordt geïnstantieerd voor een van deze service typen, worden alle code pakketten die zijn gedeclareerd in dit manifest geactiveerd door hun toegangs punten uit te voeren. De resulterende processen worden verwacht de ondersteunde service typen tijdens runtime te registreren. Wanneer er meerdere code pakketten zijn, worden deze allemaal geactiveerd wanneer het systeem zoekt naar een van de gedeclareerde service typen. Zie het [element code package](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement) voor meer informatie.

### <a name="setupentrypoint-element"></a>SetupEntryPoint-element
Een geprivilegieerd ingangs punt dat standaard wordt uitgevoerd met dezelfde referenties als Service Fabric (doorgaans het netwerk service account) vóór een ander toegangs punt. Het uitvoer bare bestand dat is opgegeven met entry point is doorgaans de langlopende servicehost. Door de aanwezigheid van een afzonderlijk installatie punt wordt voor komen dat de servicehost gedurende lange tijd met hoge bevoegdheden kan worden uitgevoerd. Zie het [element SetupEntryPoint](service-fabric-service-model-schema-elements.md#SetupEntryPointElementanonymouscomplexTypeComplexTypeDefinedInCodePackageTypecomplexType) voor meer informatie.

### <a name="exehost-element"></a>ExeHost-element
 Zie het [element ExeHost](service-fabric-service-model-schema-elements.md#ExeHostElementExeHostEntryPointTypeComplexTypeDefinedInSetupEntryPointelement) voor meer informatie.

### <a name="program-element"></a>Programma-element
De naam van het uitvoer bare bestand.  Bijvoorbeeld ' MySetup.bat ' of ' MyServiceHost.exe '. Zie voor meer informatie [programma-element](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="arguments-element"></a>Element arguments
 Zie [argumenten element](service-fabric-service-model-schema-elements.md#ArgumentsElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType) voor meer informatie.

### <a name="workingfolder-element"></a>WorkingFolder-element
De werkmap voor het proces in het code pakket op het cluster knooppunt waar de toepassing wordt geïmplementeerd. U kunt drie waarden opgeven: werk (de standaard instelling), code package of code base. Code base Hiermee geeft u op dat de werkmap is ingesteld op de map waarin de EXE is gedefinieerd in het code pakket. Code package stelt de werkmap in als de hoofdmap van het code pakket, ongeacht waar de EXE is gedefinieerd in de map van het code pakket. Met werk wordt de werkmap ingesteld op een unieke map die op het knoop punt is gemaakt.  Deze map is hetzelfde voor het hele toepassings exemplaar. De werkmap van alle processen in de toepassing wordt standaard ingesteld op de werkmap van de toepassing. Dit is de plaats waar de processen de gegevens kunnen schrijven. Het schrijven van gegevens in het code pakket of de code basis wordt niet aanbevolen, omdat deze mappen kunnen worden gedeeld tussen verschillende toepassings exemplaren en kunnen worden verwijderd. Zie het [element WorkingFolder](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType) voor meer informatie.

### <a name="consoleredirection-element"></a>ConsoleRedirection-element

> [!WARNING]
> Gebruik geen console omleiding in een productie toepassing, gebruik deze alleen voor lokale ontwikkeling en fout opsporing. Omleiding van de console-uitvoer van het opstart script naar een uitvoer bestand in de toepassingsmap met de naam log op het cluster knooppunt waar de toepassing wordt geïmplementeerd en uitgevoerd. Zie het [element ConsoleRedirection](service-fabric-service-model-schema-elements.md#ConsoleRedirectionElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType) voor meer informatie.

### <a name="entrypoint-element"></a>Entry point-element
Het uitvoer bare bestand dat is opgegeven met entry point is doorgaans de langlopende servicehost. Door de aanwezigheid van een afzonderlijk installatie punt wordt voor komen dat de servicehost gedurende lange tijd met hoge bevoegdheden kan worden uitgevoerd. Het uitvoer bare bestand dat is opgegeven met entry point wordt uitgevoerd nadat de SetupEntryPoint is afgesloten. Het resulterende proces wordt gecontroleerd en opnieuw gestart (te beginnen met SetupEntryPoint) als het ooit wordt beëindigd of is vastgelopen. Zie [Entry Point element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType) (Engelstalig) voor meer informatie.

### <a name="exehost-element"></a>ExeHost-element
 Zie het [element ExeHost](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType) voor meer informatie.

### <a name="configpackage-element"></a>ConfigPackage-element
Declareert een map, met de naam van het kenmerk name, onder PackageRoot die een Settings.xml-bestand bevat. Dit bestand bevat secties van door de gebruiker gedefinieerde combi natie van sleutel waarden die het proces tijdens runtime kan lezen. Als er tijdens een upgrade alleen de ConfigPackage-versie is gewijzigd, wordt het proces dat wordt uitgevoerd, niet opnieuw gestart. In plaats daarvan wordt met een call back het proces gewaarschuwd dat de configuratie-instellingen zijn gewijzigd zodat ze dynamisch opnieuw kunnen worden geladen. Zie het [element ConfigPackage](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement) voor meer informatie.

### <a name="resources-element"></a>Resources-element
Hierin worden de resources beschreven die door deze service worden gebruikt, die kunnen worden gedeclareerd zonder gecompileerde code te wijzigen en te worden gewijzigd wanneer de service wordt geïmplementeerd. Toegang tot deze bronnen wordt geregeld via de secties principals en beleid van het toepassings manifest. Zie [resources-element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType) voor meer informatie.

### <a name="endpoints-element"></a>Element endpoints
Hiermee definieert u eind punten voor de service. Zie het [element endpoints](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType) voor meer informatie.

### <a name="endpoint-element"></a>Eindpunt element
Het eind punt dat is gedefinieerd in het service manifest om te overschrijven. Zie voor meer informatie [eindpunt element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)



## <a name="votingdata-service-manifest-elements"></a>VotingData-service manifest elementen
### <a name="servicemanifest-element"></a>ServiceManifest-element
Hiermee worden het Service type en de versie declaratief beschreven. Hierin worden de onafhankelijk code ring, configuratie en gegevens pakketten vermeld die samen een service pakket vormen ter ondersteuning van een of meer service typen. Er zijn ook resources, diagnostische instellingen en meta gegevens van de service, zoals service type, status eigenschappen en metrische taak verdeling, opgegeven. Zie het [element ServiceManifest](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType) voor meer informatie.

### <a name="servicetypes-element"></a>ServiceTypes-element
Hiermee definieert u welke service typen worden ondersteund door een code package in dit manifest. Wanneer een service wordt geïnstantieerd voor een van deze service typen, worden alle code pakketten die zijn gedeclareerd in dit manifest geactiveerd door hun toegangs punten uit te voeren. Service typen worden op manifest niveau gedeclareerd en niet op het niveau van het code pakket. Zie het [element ServiceTypes](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType) voor meer informatie.

### <a name="statefulservicetype-element"></a>StatefulServiceType-element
Beschrijft een stateful service type. Zie het [element StatefulServiceType](service-fabric-service-model-schema-elements.md#StatefulServiceTypeElementStatefulServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType) voor meer informatie.

### <a name="codepackage-element"></a>Code package-element
Beschrijft een code pakket dat een gedefinieerd Service type ondersteunt. Wanneer een service wordt geïnstantieerd voor een van deze service typen, worden alle code pakketten die zijn gedeclareerd in dit manifest geactiveerd door hun toegangs punten uit te voeren. De resulterende processen worden verwacht de ondersteunde service typen tijdens runtime te registreren. Wanneer er meerdere code pakketten zijn, worden deze allemaal geactiveerd wanneer het systeem zoekt naar een van de gedeclareerde service typen. Zie het [element code package](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement) voor meer informatie.

### <a name="entrypoint-element"></a>Entry point-element
Het uitvoer bare bestand dat is opgegeven met entry point is doorgaans de langlopende servicehost. Door de aanwezigheid van een afzonderlijk installatie punt wordt voor komen dat de servicehost gedurende lange tijd met hoge bevoegdheden kan worden uitgevoerd. Het uitvoer bare bestand dat is opgegeven met entry point wordt uitgevoerd nadat de SetupEntryPoint is afgesloten. Het resulterende proces wordt gecontroleerd en opnieuw gestart (te beginnen met SetupEntryPoint) als het ooit wordt beëindigd of is vastgelopen. Zie [Entry Point element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType) (Engelstalig) voor meer informatie.

### <a name="exehost-element"></a>ExeHost-element
 Zie het [element ExeHost](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType) voor meer informatie.

### <a name="program-element"></a>Programma-element
De naam van het uitvoer bare bestand.  Bijvoorbeeld ' MySetup.bat ' of ' MyServiceHost.exe '. Zie voor meer informatie [programma-element](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>WorkingFolder-element
De werkmap voor het proces in het code pakket op het cluster knooppunt waar de toepassing wordt geïmplementeerd. U kunt drie waarden opgeven: werk (de standaard instelling), code package of code base. Code base Hiermee geeft u op dat de werkmap is ingesteld op de map waarin de EXE is gedefinieerd in het code pakket. Code package stelt de werkmap in als de hoofdmap van het code pakket, ongeacht waar de EXE is gedefinieerd in de map van het code pakket. Met werk wordt de werkmap ingesteld op een unieke map die op het knoop punt is gemaakt.  Deze map is hetzelfde voor het hele toepassings exemplaar. De werkmap van alle processen in de toepassing wordt standaard ingesteld op de werkmap van de toepassing. Dit is de plaats waar de processen de gegevens kunnen schrijven. Het schrijven van gegevens in het code pakket of de code basis wordt niet aanbevolen, omdat deze mappen kunnen worden gedeeld tussen verschillende toepassings exemplaren en kunnen worden verwijderd. Zie het [element WorkingFolder](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType) voor meer informatie.

### <a name="configpackage-element"></a>ConfigPackage-element
Declareert een map, met de naam van het kenmerk name, onder PackageRoot die een Settings.xml-bestand bevat. Dit bestand bevat secties van door de gebruiker gedefinieerde combi natie van sleutel waarden die het proces tijdens runtime kan lezen. Als er tijdens een upgrade alleen de ConfigPackage-versie is gewijzigd, wordt het proces dat wordt uitgevoerd, niet opnieuw gestart. In plaats daarvan wordt met een call back het proces gewaarschuwd dat de configuratie-instellingen zijn gewijzigd zodat ze dynamisch opnieuw kunnen worden geladen. Zie het [element ConfigPackage](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement) voor meer informatie.

### <a name="datapackage-element"></a>Sectie gegevens pakket-element
Declareert een map, aangeduid door het kenmerk name, onder PackageRoot dat statische gegevens bestanden bevat die tijdens de uitvoering door het proces moeten worden gebruikt. Service Fabric worden alle exe en DLLHOSTs die in de host zijn opgegeven, opnieuw gerecycled en ondersteunings pakketten wanneer een van de gegevens pakketten die worden vermeld in het service manifest, worden bijgewerkt. Zie het [element sectie gegevens pakket](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement) voor meer informatie.

### <a name="resources-element"></a>Resources-element
Hierin worden de resources beschreven die door deze service worden gebruikt, die kunnen worden gedeclareerd zonder gecompileerde code te wijzigen en te worden gewijzigd wanneer de service wordt geïmplementeerd. Toegang tot deze bronnen wordt geregeld via de secties principals en beleid van het toepassings manifest. Zie [resources-element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType) voor meer informatie.

### <a name="endpoints-element"></a>Element endpoints
Hiermee definieert u eind punten voor de service. Zie het [element endpoints](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType) voor meer informatie.

### <a name="endpoint-element"></a>Eindpunt element
Het eind punt dat is gedefinieerd in het service manifest om te overschrijven. Zie voor meer informatie [eindpunt element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

