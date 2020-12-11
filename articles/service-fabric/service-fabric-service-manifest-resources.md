---
title: Service Fabric service-eind punten opgeven
description: Eindpunt resources beschrijven in een service manifest, inclusief het instellen van HTTPS-eind punten
ms.topic: conceptual
ms.date: 09/16/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 0ed5a4aa8993f52d42b97288cd143e6114ff36ff
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033303"
---
# <a name="specify-resources-in-a-service-manifest"></a>Resources opgeven in een service manifest
## <a name="overview"></a>Overzicht
Service Fabric-toepassingen en-services worden gedefinieerd en geversiond met behulp van manifest bestanden. Zie [service Fabric toepassings-en service manifesten](service-fabric-application-and-service-manifests.md)voor een overzicht op een hoger niveau van ServiceManifest.xml en ApplicationManifest.xml.

Met het service manifest kunnen resources die door de service worden gebruikt, worden gedeclareerd of gewijzigd, zonder de gecompileerde code te wijzigen. Service Fabric ondersteunt de configuratie van eindpunt resources voor de service. De toegang tot de resources die zijn opgegeven in het service manifest kan worden beheerd via de beveiligings groep in het manifest van de toepassing. Met de declaratie van resources kunnen deze resources tijdens de implementatie worden gewijzigd, wat betekent dat de service geen nieuw configuratie mechanisme hoeft te introduceren. De schema definitie voor het ServiceManifest.xml bestand wordt geïnstalleerd met de Service Fabric SDK en hulpprogram ma's in *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.XSD*, en wordt beschreven in de documentatie van het [ServiceFabricServiceModel. XSD-schema](service-fabric-service-model-schema.md).

## <a name="endpoints"></a>Eindpunten
Wanneer een eindpunt resource is gedefinieerd in het service manifest, Service Fabric wijst poorten toe uit het gereserveerde poort bereik van de toepassing wanneer een poort niet expliciet is opgegeven. Bekijk bijvoorbeeld het eind punt *ServiceEndpoint1* dat is opgegeven in het manifest fragment dat wordt gegeven na deze alinea. Daarnaast kunnen services ook een specifieke poort aanvragen in een bron. Er kunnen verschillende poort nummers worden toegewezen aan service replica's die worden uitgevoerd op verschillende cluster knooppunten, terwijl replica's van een service die op hetzelfde knoop punt wordt uitgevoerd, de poort delen. De service replica's kunnen deze poorten vervolgens gebruiken als nodig voor replicatie en Luis teren naar client aanvragen.

Bij het activeren van een service die een HTTPS-eind punt opgeeft, stelt Service Fabric de vermelding van het toegangs beheer voor de poort in, koppelt u het opgegeven server certificaat aan de poort en verleent u ook de identiteit dat de service wordt uitgevoerd als machtigingen voor de persoonlijke sleutel van het certificaat. De activerings stroom wordt elke keer aangeroepen Service Fabric wordt gestart, of wanneer de certificaat declaratie van de toepassing wordt gewijzigd via een upgrade. Het eindpunt certificaat wordt ook gecontroleerd op wijzigingen/vernieuwingen en de machtigingen worden zo nodig periodiek opnieuw toegepast.

Wanneer de service wordt beëindigd, wordt de toegangs beheer vermelding van het endpoint door Service Fabric opgeschoond en wordt de certificaat binding verwijderd. Machtigingen die zijn toegepast op de persoonlijke sleutel van het certificaat, worden echter niet opgeschoond.

> [!WARNING] 
> Door statische poorten voor het ontwerp mogen niet overlappen met het toepassings poort bereik dat is opgegeven in de ClusterManifest. Als u een statische poort opgeeft, dient u deze buiten het toepassings poort bereik toe te wijzen, anders leidt dit tot poort conflicten. Met release 6.5 CU2 zullen we een **status waarschuwing** uitgeven wanneer een dergelijk conflict wordt gedetecteerd, maar de implementatie met het gedistribueerde 6,5-gedrag kan worden voortgezet. We kunnen er echter voor zorgen dat de implementatie van de toepassing van de volgende grote releases wordt voor komen.
>
> In release 7,0 wordt een **status waarschuwing** gegeven wanneer het gebruik van het toepassings poort bereik wordt gedetecteerd. dit wordt veroorzaakt door meer dan HostingConfig:: ApplicationPortExhaustThresholdPercentage (standaard 80%).
>

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Als er meerdere code pakketten aanwezig zijn in één service pakket, moet er ook naar het code pakket worden verwezen in de sectie met **eind punten** .  Als bijvoorbeeld **ServiceEndpoint2a** en **ServiceEndpoint2b** eind punten zijn van hetzelfde service pakket dat verwijst naar verschillende code pakketten, wordt het code pakket dat overeenkomt met elk eind punt als volgt verduidelijkt:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Raadpleeg [stateful reliable Services configureren](service-fabric-reliable-services-configuration.md) voor meer informatie over het verwijzen naar eind punten uit het bestand config package settings (settings.xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Voor beeld: een HTTP-eind punt opgeven voor uw service
Het volgende service manifest definieert één TCP-eindpunt resource en twee HTTP-eindpunt resources in het &lt; &gt; element resources.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
      <Endpoint Name="ServiceEndpoint4" Protocol="https" Port="14023"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Voor beeld: een HTTPS-eind punt opgeven voor uw service
Het HTTPS-protocol biedt Server verificatie en wordt ook gebruikt voor het versleutelen van client-server communicatie. Als u HTTPS wilt inschakelen op uw Service Fabric-service, geeft u het protocol op in de sectie *resources-> eind punten-> eindpunt* van het service manifest, zoals eerder is weer gegeven voor het eind punt *ServiceEndpoint3*.

> [!NOTE]
> Het Protocol van een service kan niet worden gewijzigd tijdens het bijwerken van de toepassing. Als deze tijdens de upgrade wordt gewijzigd, is dit een belang rijke wijziging.
> 

> [!WARNING] 
> Wanneer u HTTPS gebruikt, moet u niet dezelfde poort en hetzelfde certificaat gebruiken voor verschillende service-exemplaren (onafhankelijk van de toepassing) die op hetzelfde knoop punt zijn geïmplementeerd. Wanneer u twee verschillende services met dezelfde poort in verschillende toepassings exemplaren bijwerkt, resulteert dit in een upgrade fout. Zie [upgrades uitvoeren voor meerdere toepassingen met https-eind punten ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints)voor meer informatie.
>

Hier volgt een voor beeld van een ApplicationManifest die de configuratie demonstreert die vereist is voor een HTTPS-eind punt. Het server/eindpunt certificaat kan worden gedeclareerd door de vinger afdruk of de algemene naam van het onderwerp en er moet een waarde worden opgegeven. De EndpointRef is een verwijzing naar EndpointResource in ServiceManifest en waarvan het protocol moet zijn ingesteld op het HTTPS-protocol. U kunt meer dan één EndpointCertificate toevoegen.  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint3"/>
      <EndpointBindingPolicy CertificateRef="SslCertByCN" EndpointRef="ServiceEndpoint4"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_ ]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="SslCertByTP" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
    <EndpointCertificate Name="SslCertByCN" X509FindType="FindBySubjectName" X509FindValue="ServiceFabric-EndpointCertificateBinding-Test" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

Voor Linux-clusters wordt **mijn** winkel standaard ingesteld op de map **/var/lib/sfcerts**.

Zie [een HTTPS-eind punt toevoegen aan een ASP.net core web API-front-end-service met Kestrel](./service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest)voor een voor beeld van een volledige toepassing die gebruikmaakt van een HTTPS-eind punt.

## <a name="port-acling-for-http-endpoints"></a>Poort Acl's voor voor HTTP-eind punten
Service Fabric worden automatisch de door de HTTP (S) standaard-eind punten die zijn opgegeven, ACL'S. Er wordt **geen** automatische acl's voor uitgevoerd als aan een eind punt geen [SecurityAccessPolicy](service-fabric-assign-policy-to-endpoint.md) is gekoppeld en service Fabric is geconfigureerd om te worden uitgevoerd met een account met Administrator bevoegdheden.

## <a name="overriding-endpoints-in-servicemanifestxml"></a>Eind punten in ServiceManifest.xml overschrijven

Voeg in het ApplicationManifest een sectie ResourceOverrides toe, die deel uitmaakt van de sectie ConfigOverrides op hetzelfde niveau. In deze sectie kunt u de overschrijving opgeven voor de sectie endpoints in het gedeelte resources dat is opgegeven in het service manifest. Het overschrijven van eind punten wordt ondersteund in runtime 5.7.217/SDK 2.7.217 en hoger.

Als u het eind punt in ServiceManifest wilt overschrijven met behulp van Application parameters, wijzigt u de ApplicationManifest als volgt:

Voeg in de sectie ServiceManifestImport een nieuwe sectie ' ResourceOverrides ' toe.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
        <Endpoint Name="ServiceEndpoint1" Port="[Port1]" Protocol="[Protocol1] "/>
      </Endpoints>
    </ResourceOverrides>
        <Policies>
           <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint"/>
        </Policies>
  </ServiceManifestImport>
```

In de onderstaande para meters toevoegen:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

Tijdens de implementatie van de toepassing kunt u deze waarden door geven als Application parameters.  Bijvoorbeeld:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Opmerking: als de waarde die is opgegeven voor een opgegeven ApplicationParameter leeg is, gaan we terug naar de standaard waarde die is opgegeven in het ServiceManifest voor de bijbehorende eind punt.

Bijvoorbeeld:

Als in de ServiceManifest die u hebt opgegeven

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

Stel dat de waarde Port1 en Protocol1 voor toepassings parameters null of leeg is. De poort wordt bepaald door ServiceFabric en het protocol is TCP.

Stel dat u een verkeerde waarde opgeeft. Stel dat u voor poort een teken reeks waarde ' foo ' hebt opgegeven in plaats van een int.  New-ServiceFabricApplication opdracht mislukt met een fout: `The override parameter with name 'ServiceEndpoint1' attribute 'Port1' in section 'ResourceOverrides' is invalid. The value specified is 'Foo' and required is 'int'.`

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe u eind punten kunt definiëren in het service manifest van Service Fabric. Zie voor meer gedetailleerde voor beelden:

> [!div class="nextstepaction"]
> [Voorbeelden van toepassings- en servicemanifesten](service-fabric-manifest-examples.md)

Zie voor een door loop-through verpakking en het implementeren van een bestaande toepassing op een Service Fabric cluster:

> [!div class="nextstepaction"]
> [Een bestaand uitvoerbaar bestand inpakken en implementeren in Service Fabric](service-fabric-deploy-existing-app.md)