---
title: Azure Service Fabric-apps en-services beschrijven
description: Beschrijft hoe manifesten worden gebruikt voor het beschrijven van Service Fabric toepassingen en-services.
ms.topic: conceptual
ms.date: 8/12/2019
ms.openlocfilehash: fcf4c7611f0a6f52c28b234717b9244ac58ad2d4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86248217"
---
# <a name="service-fabric-application-and-service-manifests"></a>Toepassings-en service manifesten Service Fabric
In dit artikel wordt beschreven hoe Service Fabric toepassingen en-services worden gedefinieerd en geversied met behulp van de ApplicationManifest.xml en ServiceManifest.xml bestanden.  Zie voor beelden van toepassings- [en service manifesten](service-fabric-manifest-examples.md)voor meer gedetailleerde voor beelden.  Het XML-schema voor deze manifest bestanden wordt beschreven in de [documentatie bij ServiceFabricServiceModel. XSD-schema](service-fabric-service-model-schema.md).

> [!WARNING]
> Het XML-manifest bestand schema afdwingt de juiste volg orde van onderliggende elementen af.  Als gedeeltelijke tijdelijke oplossing opent u ' C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd ' in Visual Studio tijdens het ontwerpen of wijzigen van een van de Service Fabric-manifesten. Zo kunt u de volg orde van de onderliggende elementen controleren en beschikt.

## <a name="describe-a-service-in-servicemanifestxml"></a>Een service in ServiceManifest.xml beschrijven
Het service manifest definieert declaratief het Service type en de versie. Hiermee worden de meta gegevens van de service, zoals service type, eigenschappen van de taak verdeling, metrische gegevens over de belasting en configuratie bestanden opgegeven.  Een andere manier, hierin worden de code, configuratie en gegevens pakketten beschreven voor het samen stellen van een service pakket ter ondersteuning van een of meer service typen. Een service manifest kan meerdere code-, configuratie-en gegevens pakketten bevatten, die onafhankelijk van de versie kunnen worden verwerkt. Hier vindt u een service manifest voor de ASP.NET Core web-front-end-service van de [toepassing stem voorbeeld](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (en hier vindt u een aantal [gedetailleerde voor beelden](service-fabric-manifest-examples.md)):

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
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

**Versie** kenmerken zijn ongestructureerde teken reeksen en worden niet geparseerd door het systeem. Versie kenmerken worden gebruikt voor het versie nummer van elk onderdeel voor upgrades.

**ServiceTypes** declareert welke service typen worden ondersteund door **CodePackages** in dit manifest. Wanneer een service wordt geïnstantieerd voor een van deze service typen, worden alle code pakketten die zijn gedeclareerd in dit manifest geactiveerd door hun toegangs punten uit te voeren. De resulterende processen worden verwacht de ondersteunde service typen tijdens runtime te registreren. Service typen worden op manifest niveau gedeclareerd en niet op het niveau van het code pakket. Als er meerdere code pakketten zijn, worden deze allemaal geactiveerd wanneer het systeem zoekt naar een van de gedeclareerde service typen.

Het uitvoer bare bestand dat is opgegeven met **Entry Point** is doorgaans de langlopende servicehost. **SetupEntryPoint** is een geprivilegieerd toegangs punt dat wordt uitgevoerd met dezelfde referenties als service Fabric (doorgaans de *LocalSystem* -account) vóór een ander toegangs punt.  Door de aanwezigheid van een afzonderlijk installatie punt wordt voor komen dat de servicehost gedurende lange tijd met hoge bevoegdheden kan worden uitgevoerd. Het uitvoer bare bestand dat is opgegeven met **Entry Point** wordt uitgevoerd nadat de **SetupEntryPoint** is afgesloten. Als het proces ooit wordt beëindigd of vastloopt, wordt het resulterende proces gecontroleerd en opnieuw gestart (opnieuw starten met **SetupEntryPoint**).  

Typische scenario's voor het gebruik van **SetupEntryPoint** zijn wanneer u een uitvoerbaar bestand uitvoert voordat de service wordt gestart of een bewerking uitvoert met verhoogde bevoegdheden. Bijvoorbeeld:

* Instellen en initialiseren van omgevings variabelen die voor het uitvoer bare bestand van de service nodig zijn. Dit is niet beperkt tot uitvoer bare bestanden die zijn geschreven via de Service Fabric-programmeer modellen. npm.exe hebt bijvoorbeeld bepaalde omgevings variabelen nodig die zijn geconfigureerd voor het implementeren van een node.js toepassing.
* Toegangs beheer instellen door beveiligings certificaten te installeren.

Zie voor meer informatie over het configureren van de SetupEntryPoint [het beleid configureren voor een toegangs punt voor een service-instelling](service-fabric-application-runas-security.md)

**Omgevings variabelen** (niet ingesteld in het voor gaande voor beeld) bevat een lijst met omgevings variabelen die zijn ingesteld voor dit code pakket. Omgevings variabelen kunnen worden overschreven in de `ApplicationManifest.xml` om verschillende waarden te bieden voor verschillende service-exemplaren. 

**Sectie gegevens pakket** (niet ingesteld in het voor gaande voor beeld) declareert een map, met de **naam** kenmerk, die wille keurige statische gegevens bevat die tijdens de uitvoering door het proces moeten worden gebruikt.

**ConfigPackage** declareert een map met de naam van het kenmerk **name** , dat een *Settings.xml* bestand bevat. Het instellingen bestand bevat secties van door de gebruiker gedefinieerde combi natie van sleutel waarden waarmee het proces tijdens runtime terugkeert. Als er tijdens een upgrade alleen de **ConfigPackage** - **versie** is gewijzigd, wordt het proces dat wordt uitgevoerd, niet opnieuw gestart. In plaats daarvan wordt met een call back het proces gewaarschuwd dat de configuratie-instellingen zijn gewijzigd zodat ze dynamisch opnieuw kunnen worden geladen. Hier volgt een voor beeld *Settings.xml* bestand:

```xml
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

Een Service Fabric service- **eind punt** is een voor beeld van een service Fabric resource. Een Service Fabric resource kan worden gedeclareerd/gewijzigd zonder de gecompileerde code te wijzigen. Toegang tot de Service Fabric resources die zijn opgegeven in het service manifest kan worden beheerd via de **beveiligings groep** in het manifest van de toepassing. Wanneer een eindpunt resource is gedefinieerd in het service manifest, Service Fabric wijst poorten toe uit het gereserveerde poort bereik van de toepassing wanneer een poort niet expliciet is opgegeven. Meer informatie over het [opgeven of overschrijven van eindpunt resources](service-fabric-service-manifest-resources.md).

 
> [!WARNING]
> Door statische poorten voor het ontwerp mogen niet overlappen met het toepassings poort bereik dat is opgegeven in de ClusterManifest. Als u een statische poort opgeeft, dient u deze buiten het toepassings poort bereik toe te wijzen, anders leidt dit tot poort conflicten. Met release 6.5 CU2 zullen we een **status waarschuwing** uitgeven wanneer een dergelijk conflict wordt gedetecteerd, maar de implementatie met het gedistribueerde 6,5-gedrag kan worden voortgezet. We kunnen er echter voor zorgen dat de implementatie van de toepassing van de volgende grote releases wordt voor komen.
>

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application-in-applicationmanifestxml"></a>Een toepassing beschrijven in ApplicationManifest.xml
Het toepassings manifest beschrijft declaratief het toepassings type en de versie. Hierin worden de meta gegevens van de service compositie opgegeven, zoals stabiele namen, het partitioneren van het schema, het aantal exemplaren/de replicatie factor, het beveiligings beleid, de plaatsings beperkingen, de configuratie-onderdrukkingen en de service typen van het onderdeel. De taakverdelings domeinen waarin de toepassing wordt geplaatst, worden ook beschreven.

Een toepassings manifest beschrijft dus elementen op toepassings niveau en verwijst naar een of meer service manifesten om een toepassings type samen te stellen. Hier volgt het toepassings manifest voor de [toepassing stem voorbeeld](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (en hier vindt u een aantal [gedetailleerde voor beelden](service-fabric-manifest-examples.md)):

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
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
         <PlacementConstraints>(NodeType==NodeType0)</PlacementConstraints
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

Evenals service manifesten zijn **versie** -kenmerken ongestructureerde teken reeksen en worden ze niet geparseerd door het systeem. Versie kenmerken worden ook gebruikt voor het versie nummer van elk onderdeel voor upgrades.

**Para meters** definieert de para meters die worden gebruikt in het toepassings manifest. De waarden van deze para meters kunnen worden opgegeven wanneer de toepassing wordt geïnstantieerd en de configuratie-instellingen van de toepassing of service kunnen worden overschreven.  De standaard parameter waarde wordt gebruikt als de waarde niet wordt gewijzigd tijdens het instantiëren van de toepassing. Zie [toepassings parameters voor meerdere omgevingen beheren](service-fabric-manage-multiple-environment-app-configuration.md)voor meer informatie over het onderhouden van verschillende toepassings-en service parameters voor afzonderlijke omgevingen.

**ServiceManifestImport** bevat verwijzingen naar service manifesten die dit toepassings type vormen. Een toepassings manifest kan meerdere service manifest-import bewerkingen bevatten, die elk afzonderlijk kunnen worden geversied. Geïmporteerde service manifesten bepalen welke service typen geldig zijn binnen dit toepassings type. Binnen de ServiceManifestImport overschrijft u de configuratie waarden in Settings.xml en omgevings variabelen in ServiceManifest.xml-bestanden. **Beleids regels** (die niet in het voor gaande voor beeld zijn ingesteld) voor binding van eind punten, beveiliging en toegang en het delen van pakketten kunnen worden ingesteld voor geïmporteerde service manifesten.  Zie [beveiligings beleid voor uw toepassing configureren](service-fabric-application-runas-security.md)voor meer informatie.

**DefaultServices** declareert service-exemplaren die automatisch worden gemaakt wanneer een toepassing wordt geïnstantieerd voor dit toepassings type. Standaard services zijn slechts een gemak en gedragen zich als normale Services in elk opzicht nadat ze zijn gemaakt. Ze worden samen met andere services in het toepassings exemplaar bijgewerkt en kunnen ook worden verwijderd. Een toepassings manifest kan meerdere standaard services bevatten.

**Certificaten** (niet ingesteld in het voor gaande voor beeld) declareert de certificaten die worden gebruikt voor het [instellen van HTTPS-eind punten](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) of [het versleutelen van geheimen in het toepassings manifest](service-fabric-application-secret-management.md).

**Plaatsings beperkingen** zijn de instructies die bepalen waar services moeten worden uitgevoerd. Deze instructies zijn gekoppeld aan afzonderlijke services die u voor een of meer knooppunt eigenschappen selecteert. Zie [plaatsing van beperkingen en syntaxis van knooppunt eigenschappen](./service-fabric-cluster-resource-manager-cluster-description.md#placement-constraints-and-node-property-syntax) voor meer informatie.

**Beleids regels** (niet ingesteld in het voor gaande voor beeld) hierin worden de logboek verzameling, het standaard beleid voor [uitvoeren als](service-fabric-application-runas-security.md), de [status](service-fabric-health-introduction.md#health-policies)en [beveiligings toegang](service-fabric-application-runas-security.md) beschreven die op toepassings niveau moeten worden ingesteld, inclusief of de service (s) toegang hebben tot de service Fabric runtime.

> [!NOTE] 
> Service Fabric-toepassingen hebben standaard toegang tot de Service Fabric runtime, in de vorm van een eind punt dat toepassingsspecifieke aanvragen accepteert, en omgevings variabelen die verwijzen naar bestands paden op de host die Fabric-en toepassingsspecifieke bestanden bevatten. U kunt deze toegang uitschakelen wanneer de toepassing niet-vertrouwde code host (dat wil zeggen code waarvan de herkomst onbekend is, of die de eigenaar van de toepassing weet dat deze niet veilig kan worden uitgevoerd). Zie [Aanbevolen procedures voor beveiliging in service Fabric](service-fabric-best-practices-security.md#platform-isolation)voor meer informatie. 
>

**Principals** (niet ingesteld in het voor gaande voor beeld) beschrijven de beveiligings-principals (gebruikers of groepen) die zijn vereist voor het [uitvoeren van services en het beveiligen van service bronnen](service-fabric-application-runas-security.md).  Er wordt verwezen naar principals in de secties van het **beleid** .





<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>Volgende stappen
- [Verpakken een toepassing](service-fabric-package-apps.md) en maak deze klaar om te implementeren.
- [Toepassingen implementeren en verwijderen](service-fabric-deploy-remove-applications.md).
- [Configureer para meters en omgevings variabelen voor verschillende toepassings exemplaren](service-fabric-manage-multiple-environment-app-configuration.md).
- [Configureer beveiligings beleid voor uw toepassing](service-fabric-application-runas-security.md).
- [Https-eind punten instellen](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service).
- [Geheimen in het toepassings manifest versleutelen](service-fabric-application-secret-management.md)

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png
