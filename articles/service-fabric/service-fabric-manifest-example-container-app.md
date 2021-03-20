---
title: Voor beelden van Azure Service Fabric container toepassings manifest
description: Meer informatie over het configureren van de toepassings-en service manifest instellingen voor een toepassing met meerdere containers Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: pepogors
ms.openlocfilehash: 5efdbd17db20e69ace33b379ddbb99b2c4a20e69
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84701163"
---
# <a name="multi-container-application-and-service-manifest-examples"></a>Voorbeelden van toepassings- en servicemanifesten voor toepassingen met meerdere containers
Hier volgen enkele voor beelden van de toepassings-en service manifesten voor een Service Fabric toepassing met meerdere containers. Het doel van deze voor beelden is om te laten zien welke instellingen beschikbaar zijn en hoe u deze kunt gebruiken. Deze toepassings-en service manifesten zijn gebaseerd op de voorbeeld manifesten van de [Windows Server 2016-container](https://github.com/Azure-Samples/service-fabric-containers/tree/master/Windows) .

De volgende functies worden weer gegeven:

|Manifest|Functies|
|---|---|
|[Manifest van de toepassing](#application-manifest)| [omgevings variabelen negeren](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [container poort-naar-host-toewijzing configureren](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery), [container register verificatie configureren](service-fabric-get-started-containers.md#configure-container-repository-authentication), [resource](service-fabric-resource-governance.md)beheer, [isolatie modus instellen](service-fabric-get-started-containers.md#configure-isolation-mode), [specifieke container installatie kopieën voor het besturings systeem opgeven](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)| 
|[FrontEndService-service manifest](#frontendservice-service-manifest)| [omgevings variabelen instellen](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [een eind punt configureren](service-fabric-get-started-containers.md#configure-communication), opdrachten door geven aan de container, [een certificaat importeren in een container](service-fabric-securing-containers.md)| 
|[BackEndService-service manifest](#backendservice-service-manifest)|[omgevings variabelen instellen](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [een eind punt configureren, het](service-fabric-get-started-containers.md#configure-communication) [stuur programma voor het volume configureren](service-fabric-containers-volume-logging-drivers.md)| 

Zie manifest elementen van de [toepassing](#application-manifest-elements), FrontEndService van de [service manifest elementen](#frontendservice-service-manifest-elements)en de [BackEndService](#backendservice-service-manifest-elements)

## <a name="application-manifest"></a>Manifest van de toepassing

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Container.ApplicationType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="BackEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="FrontEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="MaximumIOps" DefaultValue="20"/>
    <Parameter Name="MemoryFront" DefaultValue="4084" />
    <Parameter Name="MemoryBack" DefaultValue="2048" />
    <Parameter Name="CertThumbprint" DefaultValue=""/>
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="BackEndServicePkg" ServiceManifestVersion="1.0.0" />    
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryFront]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[MemoryFront]" BlockIOWeight="[BlockIOWeight]" MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" MemorySwapInMB="[MemorySwapInMB]"/>
      
      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
        
        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>
        
        <!-- This binds the port the container is listening on (8905 in this sample) to an endpoint resource named "BackEndServiceTypeEndpoint", which is defined in the service manifest.  -->
        <PortBinding ContainerPort="8905" EndpointRef="BackEndServiceTypeEndpoint"/>
        
        <!-- Configure the Azure Files volume plugin.  Bind the source folder on the host VM or a remote share to the destination folder within the running container. -->
        <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
          <!-- Driver options to be passed to driver. The Azure Files volume plugin supports the following driver options:
            shareName (the Azure Files file share that provides the volume for the container), storageAccountName (the Azure storage account
            that contains the Azure Files file share), storageAccountKey (Access key for the Azure storage account that contains the Azure Files file share).
            These three driver options are required. -->
          <DriverOption Name="shareName" Value="" />
          <DriverOption Name="storageAccountName" Value="MY-STORAGE-ACCOUNT-NAME" />
          <DriverOption Name="storageAccountKey" Value="MY-STORAGE-ACCOUNT-KEY" />
        </Volume>
        
        <!-- Windows Server containers may not be compatible across different versions of the OS.  You can specify multiple OS images per container and tag 
        them with the build versions of the OS. Get the build version of the OS by running "winver" at a Windows command prompt. -->
        <ImageOverrides>
          <!-- If the underlying OS is build version 16299 (Windows Server version 1709), Service Fabric picks the container image tagged with Os="16299". -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_1709" Os="16299" />
          
          <!-- An untagged container image is assumed to work across all versions of the OS and overrides the image specified in the service manifest. -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_default" />          
        </ImageOverrides>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <!-- Policies to be applied to the imported service manifest. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
    
    <!-- This enables you to provide different values for environment variables when creating a FrontEndService
         Theses environment variables are declared in the FrontEndServiceType service manifest-->
    <EnvironmentOverrides CodePackageRef="Code">
      <EnvironmentVariable Name="BackendServiceName" Value="Container.Application/BackEndService"/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentOverrides>
    
    <!-- This policy maps the  port of the container (80) to the endpoint declared in the service, 
         FrontEndServiceTypeEndpoint which is exposed as port 80 on the host-->    
    <Policies>

      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryBack]"/>

      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">

        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>

        <!-- Binds an endpoint resource (declared in the service manifest) to the exposed container port. -->
        <PortBinding ContainerPort="80" EndpointRef="FrontEndServiceTypeEndpoint"/>

        <!-- Import a certificate into the container.  The certificate must be installed in the LocalMachine store of all the cluster nodes.
          When the application starts, the runtime reads the certificate and generates a PFX file and password (on Windows) or a PEM file (on Linux).
          The PFX file and password are accessible in the container using the Certificates_ServicePackageName_CodePackageName_CertName_PFX and 
          Certificates_ServicePackageName_CodePackageName_CertName_Password environment variables. The PEM file is accessible in the container using the 
          Certificates_ServicePackageName_CodePackageName_CertName_PEM and Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey environment variables.-->
        <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[CertThumbprint]" />

        <!-- If the certificate is already in PFX or PEM form, you can create a data package inside your application and reference that certificate here. -->
        <CertificateRef Name="MyCert2" DataPackageRef="Data" DataPackageVersion="1.0.0" RelativePath="MyCert2.PFX" Password="ENCRYPTED-PASSWORD" IsPasswordEncrypted="true"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
        
    <Service Name="FrontEndService" >
      <StatelessService ServiceTypeName="FrontEndServiceType" InstanceCount="[FrontEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
        <Service Name="BackEndService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="BackEndServiceType" InstanceCount="[BackEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="frontendservice-service-manifest"></a>FrontEndService-service manifest

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="FrontEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="FrontEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricfrontendservice:v1</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container or exe.  These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="IsContainer" Value=""/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Data package is the contents of the Data directory under PackageRoot that contains an 
       independently-updateable and versioned static data that's consumed by the process at runtime. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme and port 80 -->
      <Endpoint Name="FrontEndServiceTypeEndpoint" UriScheme="http" Port="80"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="backendservice-service-manifest"></a>BackEndService-service manifest

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="BackEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="BackEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricbackendservice:v1</ImageName>
        
        <!-- Pass comma delimited commands to your container. -->
        <Commands> dotnet, myproc.dll, 5 </Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container. These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the host port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme. In this case since no port is specified, one is created and assigned dynamically
           to the service. This dynamically assigned host port is mapped to the container port (8905 in this sample),
            which was specified in the application manifest.-->
      <Endpoint Name="BackEndServiceTypeEndpoint" UriScheme="http" />
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

### <a name="policies-element"></a>Element beleid
Hierin worden de beleids regels (End-Point binding, Package sharing, run-as en Security Access) beschreven die moeten worden toegepast op het geïmporteerde service manifest. Zie [Policies](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement) (Engelstalig) voor meer informatie.

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy-element
Definieert het bron beheer beleid dat wordt toegepast op het niveau van het hele service pakket. Zie het [element ServicePackageResourceGovernancePolicy](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType) voor meer informatie.

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy-element
Hiermee geeft u de resource limieten voor een code pakket op. Zie het [element ResourceGovernancePolicy](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement) voor meer informatie.

### <a name="containerhostpolicies-element"></a>ContainerHostPolicies-element
Hiermee geeft u beleid voor het activeren van container-hosts. Zie het [element ContainerHostPolicies](service-fabric-service-model-schema-elements.md#ContainerHostPoliciesElementContainerHostPoliciesTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement) voor meer informatie.

### <a name="repositorycredentials-element"></a>RepositoryCredentials-element
Referenties voor opslag plaats van container installatie kopie waaruit installatie kopieën moeten worden opgehaald. Zie het [element RepositoryCredentials](service-fabric-service-model-schema-elements.md#RepositoryCredentialsElementRepositoryCredentialsTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType) voor meer informatie.

### <a name="portbinding-element"></a>PortBinding-element
Hiermee geeft u de eindpunt resource die moet worden gekoppeld aan de weer gegeven container poort. Zie het [element PortBinding](service-fabric-service-model-schema-elements.md#PortBindingElementPortBindingTypeComplexTypeDefinedInServicePackageContainerPolicyTypecomplexTypeDefinedInContainerHostPoliciesTypecomplexType) voor meer informatie.

### <a name="volume-element"></a>Volume-element
Hiermee geeft u het volume moet worden gebonden aan de container. Zie [volume-element](service-fabric-service-model-schema-elements.md#VolumeElementContainerVolumeTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType) voor meer informatie.

### <a name="driveroption-element"></a>DriverOption-element
Stuur programma-opties die moeten worden door gegeven aan het stuur programma. Zie het [element DriverOption](service-fabric-service-model-schema-elements.md#DriverOptionElementDriverOptionTypeComplexTypeDefinedInContainerLoggingDriverTypecomplexTypeDefinedInContainerVolumeTypecomplexType) voor meer informatie.

### <a name="imageoverrides-element"></a>ImageOverrides-element
Windows Server-containers zijn mogelijk niet compatibel in verschillende versies van het besturings systeem.  U kunt meerdere installatie kopieën van besturings systemen per container opgeven en ze labelen met de build-versies van het besturings systeem. Haal de build-versie van het besturings systeem op door ' winver ' uit te voeren op een Windows-opdracht prompt. Als het onderliggende besturings systeem build versie 16299 (Windows Server versie 1709) is, Service Fabric de container installatie kopie die is gelabeld met OS = "16299" wordt geselecteerd. Er wordt van uitgegaan dat een niet-gecodeerde container installatie kopie in alle versies van het besturings systeem wordt gebruikt en de installatie kopie die is opgegeven in het service manifest, wordt overschreven. Zie het [element ImageOverrides](service-fabric-service-model-schema-elements.md#ImageOverridesElementImageOverridesTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType) voor meer informatie.

### <a name="image-element"></a>Afbeeldings element
Container installatie kopie die overeenkomt met het versie nummer van het besturings systeem dat moet worden gestart. Als het kenmerk van het besturings systeem niet is opgegeven, wordt ervan uitgegaan dat de container installatie kopie wordt gebruikt in alle versies van het besturings systeem en overschrijft de installatie kopie die is opgegeven in het service manifest. Zie [afbeeldings element](service-fabric-service-model-schema-elements.md#ImageElementImageTypeComplexTypeDefinedInImageOverridesTypecomplexType) voor meer informatie.

### <a name="environmentoverrides-element"></a>EnvironmentOverrides-element
 Zie het [element EnvironmentOverrides](service-fabric-service-model-schema-elements.md#EnvironmentOverridesElementEnvironmentOverridesTypeComplexTypeDefinedInServiceManifestImportelement) voor meer informatie.

### <a name="environmentvariable-element"></a>EnvironmentVariable-element
Omgevings variabele. Zie het [element EnvironmentVariable](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType) voor meer informatie.

### <a name="certificateref-element"></a>CertificateRef-element
Hiermee geeft u informatie op over een x509-certificaat dat moet worden blootgesteld aan de container omgeving. Het certificaat moet worden geïnstalleerd in de LocalMachine-opslag van alle cluster knooppunten.
Wanneer de toepassing wordt gestart, leest de runtime het certificaat en wordt er een PFX-bestand en-wacht woord (in Windows) of een PEM-bestand (in Linux) gegenereerd.
Het PFX-bestand en het wacht woord zijn toegankelijk in de container met behulp van de omgevings variabelen Certificates_ServicePackageName_CodePackageName_CertName_PFX en Certificates_ServicePackageName_CodePackageName_CertName_Password. Het PEM-bestand is toegankelijk in de container met behulp van de omgevings variabelen Certificates_ServicePackageName_CodePackageName_CertName_PEM en Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey. Zie het [element CertificateRef](service-fabric-service-model-schema-elements.md#CertificateRefElementContainerCertificateTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType) voor meer informatie.

### <a name="defaultservices-element"></a>DefaultServices-element
Declareert service-exemplaren die automatisch worden gemaakt wanneer een toepassing wordt geïnstantieerd voor dit toepassings type. Zie het [element DefaultServices](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType) voor meer informatie.

### <a name="service-element"></a>Service-element
Declareert een service die automatisch moet worden gemaakt wanneer de toepassing wordt geïnstantieerd. Zie [service-element](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType) voor meer informatie.

### <a name="statelessservice-element"></a>StatelessService-element
Hiermee definieert u een stateless service. Zie het [element StatelessService](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement) voor meer informatie.


## <a name="frontendservice-service-manifest-elements"></a>FrontEndService-service manifest elementen
### <a name="servicemanifest-element"></a>ServiceManifest-element
Hiermee worden het Service type en de versie declaratief beschreven. Hierin worden de onafhankelijk code ring, configuratie en gegevens pakketten vermeld die samen een service pakket vormen ter ondersteuning van een of meer service typen. Er zijn ook resources, diagnostische instellingen en meta gegevens van de service, zoals service type, status eigenschappen en metrische taak verdeling, opgegeven. Zie het [element ServiceManifest](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType) voor meer informatie.

### <a name="servicetypes-element"></a>ServiceTypes-element
Hiermee definieert u welke service typen worden ondersteund door een code package in dit manifest. Wanneer een service wordt geïnstantieerd voor een van deze service typen, worden alle code pakketten die zijn gedeclareerd in dit manifest geactiveerd door hun toegangs punten uit te voeren. Service typen worden op manifest niveau gedeclareerd en niet op het niveau van het code pakket. Zie het [element ServiceTypes](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType) voor meer informatie.

### <a name="statelessservicetype-element"></a>StatelessServiceType-element
Beschrijft een stateless service type. Zie het [element StatelessServiceType](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType) voor meer informatie.

### <a name="codepackage-element"></a>Code package-element
Beschrijft een code pakket dat een gedefinieerd Service type ondersteunt. Wanneer een service wordt geïnstantieerd voor een van deze service typen, worden alle code pakketten die zijn gedeclareerd in dit manifest geactiveerd door hun toegangs punten uit te voeren. De resulterende processen worden verwacht de ondersteunde service typen tijdens runtime te registreren. Wanneer er meerdere code pakketten zijn, worden deze allemaal geactiveerd wanneer het systeem zoekt naar een van de gedeclareerde service typen. Zie het [element code package](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement) voor meer informatie.

### <a name="entrypoint-element"></a>Entry point-element
Het uitvoer bare bestand dat is opgegeven met entry point is doorgaans de langlopende servicehost. Door de aanwezigheid van een afzonderlijk installatie punt wordt voor komen dat de servicehost gedurende lange tijd met hoge bevoegdheden kan worden uitgevoerd. Het uitvoer bare bestand dat is opgegeven met entry point wordt uitgevoerd nadat de SetupEntryPoint is afgesloten. Het resulterende proces wordt gecontroleerd en opnieuw gestart (te beginnen met SetupEntryPoint) als het ooit wordt beëindigd of is vastgelopen. Zie [Entry Point element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType) (Engelstalig) voor meer informatie.

### <a name="containerhost-element"></a>ContainerHost-element
 Zie het [element ContainerHost](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType) voor meer informatie.

### <a name="imagename-element"></a>Element Imagenaam
De opslag plaats en installatie kopie op [https://hub.docker.com](https://hub.docker.com) of Azure container Registry. Zie het [element afbeeldings](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType) voor meer informatie.

### <a name="environmentvariables-element"></a>Omgevings variabelen-element
U kunt omgevings variabelen door geven aan uw container of exe.  Zie het [element omgevings variabelen](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType) voor meer informatie.

### <a name="environmentvariable-element"></a>EnvironmentVariable-element
Omgevings variabele. Zie het [element EnvironmentVariable](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType) voor meer informatie.

### <a name="configpackage-element"></a>ConfigPackage-element
Declareert een map met de naam van het kenmerk name, dat een Settings.xml bestand bevat. Dit bestand bevat secties van door de gebruiker gedefinieerde combi natie van sleutel waarden die het proces tijdens runtime kan lezen. Als er tijdens een upgrade alleen de ConfigPackage-versie is gewijzigd, wordt het proces dat wordt uitgevoerd, niet opnieuw gestart. In plaats daarvan wordt met een call back het proces gewaarschuwd dat de configuratie-instellingen zijn gewijzigd zodat ze dynamisch opnieuw kunnen worden geladen. Zie het [element ConfigPackage](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement) voor meer informatie.

### <a name="datapackage-element"></a>Sectie gegevens pakket-element
Declareert een map, met de naam van het kenmerk name, dat statische gegevens bestanden bevat. Service Fabric worden alle exe en DLLHOSTs die in de host zijn opgegeven, opnieuw gerecycled en ondersteunings pakketten wanneer een van de gegevens pakketten die worden vermeld in het service manifest, worden bijgewerkt. Zie het [element sectie gegevens pakket](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement) voor meer informatie.

### <a name="resources-element"></a>Resources-element
Hierin worden de resources beschreven die door deze service worden gebruikt, die kunnen worden gedeclareerd zonder gecompileerde code te wijzigen en te worden gewijzigd wanneer de service wordt geïmplementeerd. Toegang tot deze bronnen wordt geregeld via de secties principals en beleid van het toepassings manifest. Zie [resources-element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType) voor meer informatie.

### <a name="endpoints-element"></a>Element endpoints
Hiermee definieert u eind punten voor de service. Zie het [element endpoints](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType) voor meer informatie.

### <a name="endpoint-element"></a>Eindpunt element
Zie voor meer informatie [eindpunt element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)


## <a name="backendservice-service-manifest-elements"></a>BackEndService-service manifest elementen
### <a name="servicemanifest-element"></a>ServiceManifest-element
Hiermee worden het Service type en de versie declaratief beschreven. Hierin worden de onafhankelijk code ring, configuratie en gegevens pakketten vermeld die samen een service pakket vormen ter ondersteuning van een of meer service typen. Er zijn ook resources, diagnostische instellingen en meta gegevens van de service, zoals service type, status eigenschappen en metrische taak verdeling, opgegeven. Zie het [element ServiceManifest](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType) voor meer informatie.

### <a name="servicetypes-element"></a>ServiceTypes-element
Hiermee definieert u welke service typen worden ondersteund door een code package in dit manifest. Wanneer een service wordt geïnstantieerd voor een van deze service typen, worden alle code pakketten die zijn gedeclareerd in dit manifest geactiveerd door hun toegangs punten uit te voeren. Service typen worden op manifest niveau gedeclareerd en niet op het niveau van het code pakket. Zie het [element ServiceTypes](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType) voor meer informatie.

### <a name="statelessservicetype-element"></a>StatelessServiceType-element
Beschrijft een stateless service type. Zie het [element StatelessServiceType](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType) voor meer informatie.

### <a name="codepackage-element"></a>Code package-element
Beschrijft een code pakket dat een gedefinieerd Service type ondersteunt. Wanneer een service wordt geïnstantieerd voor een van deze service typen, worden alle code pakketten die zijn gedeclareerd in dit manifest geactiveerd door hun toegangs punten uit te voeren. De resulterende processen worden verwacht de ondersteunde service typen tijdens runtime te registreren. Wanneer er meerdere code pakketten zijn, worden deze allemaal geactiveerd wanneer het systeem zoekt naar een van de gedeclareerde service typen. Zie het [element code package](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement) voor meer informatie.

### <a name="entrypoint-element"></a>Entry point-element
Het uitvoer bare bestand dat is opgegeven met entry point is doorgaans de langlopende servicehost. Door de aanwezigheid van een afzonderlijk installatie punt wordt voor komen dat de servicehost gedurende lange tijd met hoge bevoegdheden kan worden uitgevoerd. Het uitvoer bare bestand dat is opgegeven met entry point wordt uitgevoerd nadat de SetupEntryPoint is afgesloten. Het resulterende proces wordt gecontroleerd en opnieuw gestart (te beginnen met SetupEntryPoint) als het ooit wordt beëindigd of is vastgelopen. Zie [Entry Point element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType) (Engelstalig) voor meer informatie.

### <a name="containerhost-element"></a>ContainerHost-element
Zie het [element ContainerHost](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType) voor meer informatie.

### <a name="imagename-element"></a>Element Imagenaam
De opslag plaats en installatie kopie op [https://hub.docker.com](https://hub.docker.com) of Azure container Registry. Zie het [element afbeeldings](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType) voor meer informatie.

### <a name="commands-element"></a>Opdrachten element
Een door komma's gescheiden lijst met opdrachten door geven aan de container. Zie [opdrachten element](service-fabric-service-model-schema-elements.md#CommandsElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType) voor meer informatie.

### <a name="environmentvariables-element"></a>Omgevings variabelen-element
U kunt omgevings variabelen door geven aan uw container of exe.  Zie het [element omgevings variabelen](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType) voor meer informatie.

### <a name="environmentvariable-element"></a>EnvironmentVariable-element
Omgevings variabele. Zie het [element EnvironmentVariable](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType) voor meer informatie.

### <a name="configpackage-element"></a>ConfigPackage-element
Declareert een map met de naam van het kenmerk name, dat een Settings.xml bestand bevat. Dit bestand bevat secties van door de gebruiker gedefinieerde combi natie van sleutel waarden die het proces tijdens runtime kan lezen. Als er tijdens een upgrade alleen de ConfigPackage-versie is gewijzigd, wordt het proces dat wordt uitgevoerd, niet opnieuw gestart. In plaats daarvan wordt met een call back het proces gewaarschuwd dat de configuratie-instellingen zijn gewijzigd zodat ze dynamisch opnieuw kunnen worden geladen. Zie het [element ConfigPackage](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement) voor meer informatie.

### <a name="resources-element"></a>Resources-element
Hierin worden de resources beschreven die door deze service worden gebruikt, die kunnen worden gedeclareerd zonder gecompileerde code te wijzigen en te worden gewijzigd wanneer de service wordt geïmplementeerd. Toegang tot deze bronnen wordt geregeld via de secties principals en beleid van het toepassings manifest. Zie [resources-element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType) voor meer informatie.

### <a name="endpoints-element"></a>Element endpoints
Hiermee definieert u eind punten voor de service. Zie het [element endpoints](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType) voor meer informatie.

### <a name="endpoint-element"></a>Eindpunt element
 Zie voor meer informatie [eindpunt element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

