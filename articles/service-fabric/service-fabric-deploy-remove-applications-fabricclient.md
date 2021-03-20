---
title: Azure Service Fabric-implementatie met FabricClient
description: Gebruik de FabricClient-Api's om toepassingen te implementeren en te verwijderen in Service Fabric.
ms.topic: conceptual
ms.date: 01/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 343a37c983b1d64a4b1986913d9d6fd648a113fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785546"
---
# <a name="deploy-and-remove-applications-using-fabricclient"></a>Toepassingen implementeren en verwijderen met FabricClient
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric-CLI](service-fabric-application-lifecycle-sfctl.md)
> * [FabricClient-API's](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

Zodra een [toepassings type is verpakt][10], kunt u het implementeren in een Azure service Fabric-cluster. De implementatie omvat de volgende drie stappen:

1. Upload het toepassings pakket naar de installatie kopie opslag
2. Het toepassings type registreren
3. Het toepassings pakket uit het archief met installatie kopieën verwijderen
4. Het toepassings exemplaar maken

Nadat u een toepassing hebt geïmplementeerd en een exemplaar in het cluster hebt uitgevoerd, kunt u het toepassings exemplaar en het toepassings type ervan verwijderen. Verwijder een toepassing volledig uit het cluster door de volgende stappen uit te voeren:

1. Het actieve toepassings exemplaar verwijderen (of verwijderen)
2. Hef de registratie van het toepassings type op als u het niet meer nodig hebt

Als u Visual Studio gebruikt voor het implementeren en opsporen van fouten in toepassingen op uw lokale ontwikkel cluster, worden alle voor gaande stappen automatisch uitgevoerd via een Power shell-script.  Dit script bevindt zich in de map *scripts* van het toepassings project. Dit artikel bevat achtergrond informatie over het uitvoeren van het script, zodat u dezelfde bewerkingen kunt uitvoeren buiten Visual Studio. 
 
## <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster
Maak verbinding met het cluster door een [FabricClient](/dotnet/api/system.fabric.fabricclient) -exemplaar te maken voordat u een van de code voorbeelden in dit artikel uitvoert. Zie [verbinding maken met een beveiligd cluster](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis)voor voor beelden van het maken van verbinding met een lokaal ontwikkelings cluster of een extern cluster of een cluster dat is beveiligd met Azure Active Directory, x509-certificaten of Windows Active Directory. Voer het volgende voor beeld uit om verbinding te maken met het lokale ontwikkel cluster:

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>Het toepassings pakket uploaden
Stel dat u een toepassing met de naam *mijn toepassing* in Visual Studio bouwt en inpakt. De naam van het toepassings type die in de ApplicationManifest.xml wordt vermeld, is standaard ' MyApplicationType '.  Het toepassings pakket, dat het benodigde toepassings manifest, service manifesten en code/config/gegevens pakketten bevat, bevindt zich in *C:\Users \& lt; gebruikers naam &gt; \Documents\Visual Studio 2019 \ Projects\MyApplication\MyApplication\pkg\Debug*.

Als u het toepassings pakket uploadt, wordt het op een locatie die toegankelijk is voor de interne Service Fabric-onderdelen. Service Fabric controleert het toepassings pakket tijdens de registratie van het toepassings pakket. Als u het toepassings pakket echter lokaal wilt controleren (dat wil zeggen, voordat u dit uploadt), gebruikt u de cmdlet [test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) .

De [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) -API uploadt het toepassings pakket naar de Cluster installatie kopie opslag. 

Als het toepassings pakket groot is en/of veel bestanden heeft, kunt u [Dit comprimeren](service-fabric-package-apps.md#compress-a-package) en kopiëren naar het archief met installatie kopieën met behulp van Power shell. De compressie vermindert de grootte en het aantal bestanden.

Zie [de afbeeldings store Connection String](service-fabric-image-store-connection-string.md) voor aanvullende informatie over de archief-en installatie kopie-Archief Connection String.

## <a name="register-the-application-package"></a>Het toepassings pakket registreren
Het toepassings type en de versie die in het manifest van de toepassing zijn gedeclareerd, kunnen worden gebruikt wanneer het toepassings pakket wordt geregistreerd. Het systeem leest het pakket dat in de vorige stap is geüpload, controleert het pakket, verwerkt de inhoud van het pakket en kopieert het verwerkte pakket naar een interne systeem locatie.  

De [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) -API registreert het toepassings type in het cluster en maakt deze beschikbaar voor implementatie.

De [GetApplicationTypeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync) -API biedt informatie over alle geregistreerde toepassings typen. U kunt deze API gebruiken om te bepalen wanneer de registratie is voltooid.

## <a name="remove-an-application-package-from-the-image-store"></a>Een toepassings pakket verwijderen uit het archief met installatie kopieën
Het is raadzaam het toepassings pakket te verwijderen nadat de toepassing is geregistreerd.  Als u toepassings pakketten uit de installatie kopie opslag verwijdert, worden de systeem bronnen vrijgemaakt.  Het houden van ongebruikte toepassings pakketten verbruikt schijf opslag en leidt tot prestatie problemen van toepassingen. Verwijder het toepassings pakket uit het archief met installatie kopieën met behulp van de [RemoveApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage) -API.

## <a name="create-an-application-instance"></a>Een toepassings exemplaar maken
U kunt een toepassing instantiëren vanuit elk toepassings type dat met succes is geregistreerd met behulp van de [CreateApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync) -API. De naam van elke toepassing moet beginnen met het schema *' Fabric: '* en moet uniek zijn voor elke toepassings instantie (binnen een cluster). Sstandaardservices gedefinieerd in het toepassingsmanifest van het doeltoepassingstype worden ook aangemaakt.

Er kunnen meerdere toepassings exemplaren worden gemaakt voor een bepaalde versie van een geregistreerd toepassings type. Elk exemplaar van de toepassing wordt uitgevoerd in isolatie, met een eigen werkmap en een set processen.

Als u wilt zien welke benoemde toepassingen en services worden uitgevoerd in het cluster, voert u de [GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) -en [GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync) -api's uit.

## <a name="create-a-service-instance"></a>Een service-exemplaar maken
U kunt een service van een service type instantiëren met behulp van de [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) -API.  Als de service is gedeclareerd als een standaard service in het toepassings manifest, wordt de service geïnstantieerd wanneer de toepassing wordt geïnstantieerd.  Het aanroepen van de [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) -API voor een service die al is geïnstantieerd, retourneert een uitzonde ring van het type FabricException. De uitzonde ring bevat een fout code met de waarde FabricErrorCode. ServiceAlreadyExists.

## <a name="remove-a-service-instance"></a>Een service-exemplaar verwijderen
Wanneer een service-exemplaar niet meer nodig is, kunt u het verwijderen uit het actieve exemplaar van de toepassing door de [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync) -API aan te roepen.  

> [!WARNING]
> Deze bewerking kan niet ongedaan worden gemaakt en de service status kan niet worden hersteld.

## <a name="remove-an-application-instance"></a>Een toepassings exemplaar verwijderen
Wanneer een toepassings exemplaar niet meer nodig is, kunt u dit met behulp van de [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) -API permanent verwijderen met de naam. [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) verwijdert automatisch alle services die deel uitmaken van de toepassing. ook wordt alle service status permanent verwijderd.

> [!WARNING]
> Deze bewerking kan niet ongedaan worden gemaakt en de status van de toepassing kan niet worden hersteld.

## <a name="unregister-an-application-type"></a>Registratie van een toepassings type ongedaan maken
Wanneer een bepaalde versie van een toepassings type niet meer nodig is, moet u de registratie van die bepaalde versie van het toepassings type ongedaan maken met behulp van de API [unregister-ServiceFabricApplicationType](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync) . Bij het ongedaan maken van de registratie van ongebruikte versies van toepassings typen wordt de opslag ruimte die wordt gebruikt door de installatie kopie opslag De registratie van een versie van een toepassings type kan ongedaan worden gemaakt zolang er geen toepassingen worden geconcretiseerd op die versie van het toepassings type. Daarnaast kan het toepassings type geen wachtende toepassings upgrades hebben die verwijzen naar die versie van het toepassings type.

## <a name="troubleshooting"></a>Problemen oplossen
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage vraagt om een ImageStoreConnectionString
De Service Fabric SDK-omgeving moet al de juiste standaard instellingen hebben ingesteld. Maar als dat nodig is, moet de ImageStoreConnectionString voor alle opdrachten overeenkomen met de waarde die het Service Fabric cluster gebruikt. U kunt de ImageStoreConnectionString vinden in het cluster manifest, opgehaald met de opdrachten [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest) en Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

De cmdlet **Get-ImageStoreConnectionStringFromClusterManifest** , die deel uitmaakt van de Power shell-module service Fabric SDK, wordt gebruikt voor het ophalen van de installatie kopie van de archief Connection String.  Als u de SDK-module wilt importeren, voert u de volgende handelingen uit:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```


De ImageStoreConnectionString is te vinden in het cluster manifest:

```xml
<ClusterManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Zie [de afbeeldings store Connection String](service-fabric-image-store-connection-string.md) voor aanvullende informatie over de archief-en installatie kopie-Archief Connection String.

### <a name="deploy-large-application-package"></a>Omvang rijk toepassings pakket implementeren
Probleem: [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API time-out voor een groot toepassings pakket (volg orde van GB).
Probeer:
- Geef een grotere time-out op voor de methode [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) met de `timeout` para meter. De time-out is standaard 30 minuten.
- Controleer de netwerk verbinding tussen de bron computer en het cluster. Als de verbinding langzaam is, kunt u overwegen om een computer met een betere netwerk verbinding te gebruiken.
Als de client computer zich in een andere regio bevindt dan het cluster, kunt u overwegen om een client computer in een dichter of dezelfde regio als het cluster te gebruiken.
- Controleer of u externe restricties kunt door lopen. Als het archief met installatie kopieën bijvoorbeeld is geconfigureerd voor het gebruik van Azure Storage, wordt het uploaden mogelijk beperkt.

Probleem: Upload pakket is voltooid, maar er is een time-out opgetreden voor de [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) -API. Meld
- [Comprimeer het pakket](service-fabric-package-apps.md#compress-a-package) voordat u het kopieert naar het archief met installatie kopieën.
De compressie vermindert de grootte en het aantal bestanden, waardoor de hoeveelheid verkeer wordt verminderd en het werk dat Service Fabric moet worden uitgevoerd. De upload bewerking kan langzamer verlopen (met name als u de compressie tijd opneemt), maar registratie en registratie van het toepassings type worden sneller uitgevoerd.
- Geef een grotere time-out op voor de [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) -API met de `timeout` para meter.

### <a name="deploy-application-package-with-many-files"></a>Een toepassings pakket met veel bestanden implementeren
Probleem: [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) is een time-out voor een toepassings pakket met veel bestanden (volg orde van duizenden).
Probeer:
- [Comprimeer het pakket](service-fabric-package-apps.md#compress-a-package) voordat u het kopieert naar het archief met installatie kopieën. De compressie vermindert het aantal bestanden.
- Geef een grotere time-out op voor [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) met de `timeout` para meter.

## <a name="code-example"></a>Voorbeeld van code
In het volgende voor beeld wordt een toepassings pakket naar de installatie kopie opslag gekopieerd en wordt het toepassings type ingericht. In het voor beeld wordt vervolgens een exemplaar van de toepassing gemaakt en een service-exemplaar gemaakt. Ten slotte verwijdert het voor beeld het toepassings exemplaar, wordt het toepassings type oningericht en wordt het toepassings pakket uit het archief met installatie kopieën verwijderd.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Reflection;
using System.Threading.Tasks;

using System.Fabric;
using System.Fabric.Description;
using System.Threading;

namespace ServiceFabricAppLifecycle
{
class Program
{
static void Main(string[] args)
{

    string clusterConnection = "localhost:19000";
    string appName = "fabric:/MyApplication";
    string appType = "MyApplicationType";
    string appVersion = "1.0.0";
    string serviceName = "fabric:/MyApplication/Stateless1";
    string imageStoreConnectionString = "file:C:\\SfDevCluster\\Data\\ImageStoreShare";
    string packagePathInImageStore = "MyApplication";
    string packagePath = "C:\\Users\\username\\Documents\\Visual Studio 2019\\Projects\\MyApplication\\MyApplication\\pkg\\Debug";
    string serviceType = "Stateless1Type";

    // Connect to the cluster.
    FabricClient fabricClient = new FabricClient(clusterConnection);

    // Copy the application package to a location in the image store
    try
    {
        fabricClient.ApplicationManager.CopyApplicationPackage(imageStoreConnectionString, packagePath, packagePathInImageStore);
        Console.WriteLine("Application package copied to {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package copy to Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Provision the application.  "MyApplicationV1" is the folder in the image store where the application package is located. 
    // The application type with name "MyApplicationType" and version "1.0.0" (both are found in the application manifest) 
    // is now registered in the cluster.            
    try
    {
        fabricClient.ApplicationManager.ProvisionApplicationAsync(packagePathInImageStore).Wait();

        Console.WriteLine("Provisioned application type {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Provision Application Type failed:");

        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete the application package from a location in the image store.
    try
    {
        fabricClient.ApplicationManager.RemoveApplicationPackage(imageStoreConnectionString, packagePathInImageStore);
        Console.WriteLine("Application package removed from {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package removal from Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    //  Create the application instance.
    try
    {
        ApplicationDescription appDesc = new ApplicationDescription(new Uri(appName), appType, appVersion);
        fabricClient.ApplicationManager.CreateApplicationAsync(appDesc).Wait();
        Console.WriteLine("Created application instance of type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Create the stateless service description.  For stateful services, use a StatefulServiceDescription object.
    StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
    serviceDescription.ApplicationName = new Uri(appName);
    serviceDescription.InstanceCount = 1;
    serviceDescription.PartitionSchemeDescription = new SingletonPartitionSchemeDescription();
    serviceDescription.ServiceName = new Uri(serviceName);
    serviceDescription.ServiceTypeName = serviceType;

    // Create the service instance.  If the service is declared as a default service in the ApplicationManifest.xml,
    // the service instance is already running and this call will fail.
    try
    {
        fabricClient.ServiceManager.CreateServiceAsync(serviceDescription).Wait();
        Console.WriteLine("Created service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete a service instance.
    try
    {
        DeleteServiceDescription deleteServiceDescription = new DeleteServiceDescription(new Uri(serviceName));

        fabricClient.ServiceManager.DeleteServiceAsync(deleteServiceDescription);
        Console.WriteLine("Deleted service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete an application instance from the application type.
    try
    {
        DeleteApplicationDescription deleteApplicationDescription = new DeleteApplicationDescription(new Uri(appName));

        fabricClient.ApplicationManager.DeleteApplicationAsync(deleteApplicationDescription).Wait();
        Console.WriteLine("Deleted application instance {0}", appName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Un-provision the application type.
    try
    {
        fabricClient.ApplicationManager.UnprovisionApplicationAsync(appType, appVersion).Wait();
        Console.WriteLine("Un-provisioned application type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Un-provision application type failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    Console.WriteLine("Hit enter...");
    Console.Read();
}        
}
}

```

## <a name="next-steps"></a>Volgende stappen
[Upgrade van toepassing Service Fabric](service-fabric-application-upgrade.md)

[Inleiding van Service Fabric status](service-fabric-health-introduction.md)

[Een Service Fabric service diagnosticeren en problemen oplossen](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Een toepassing model leren in Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
