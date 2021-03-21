---
title: Azure Cloud Services-apps converteren naar Service Fabric
description: Deze hand leiding vergelijkt Cloud Services Web-en werk rollen en Service Fabric stateless Services om te migreren van Cloud Services naar Service Fabric.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: cf593f793aabf2a0650684ed8d02fe02d756ec2b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96575734"
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Hand leiding voor het converteren van web-en werk rollen naar Service Fabric stateless Services
In dit artikel wordt beschreven hoe u uw Cloud Services-Web-en-werk rollen migreert naar Service Fabric stateless Services. Dit is het eenvoudigste migratie traject van Cloud Services naar Service Fabric voor toepassingen waarvan de algehele architectuur op ongeveer dezelfde manier blijft.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>Cloud service project to Service Fabric Application project
 Een Cloud service-project en een Service Fabric-toepassings project hebben een vergelijk bare structuur en beide vertegenwoordigen de implementatie-eenheid voor uw toepassing. dat wil zeggen dat ze elk het volledige pakket definiëren dat wordt geïmplementeerd om uw toepassing uit te voeren. Een Cloud service project bevat een of meer web-of werk rollen. Op dezelfde manier bevat een Service Fabric toepassings project een of meer services. 

Het verschil is dat het Cloud service project de implementatie van de toepassing Couples met een VM-implementatie en daarom configuratie-instellingen voor de virtuele machine bevat, terwijl het Service Fabric toepassings project alleen een toepassing definieert die wordt geïmplementeerd in een set bestaande Vm's in een Service Fabric cluster. Het Service Fabric cluster zelf wordt slechts eenmaal geïmplementeerd, hetzij via een resource manager-sjabloon hetzij via de Azure Portal, en er kunnen meerdere Service Fabric toepassingen op worden geïmplementeerd.

![Vergelijking van Service Fabric-en Cloud Services projecten][3]

## <a name="worker-role-to-stateless-service"></a>De werk rollen voor het stateless service
De rol van een werk nemer vertegenwoordigt een staatloze werk belasting, wat betekent dat elke instantie van de werk belasting identiek is en dat aanvragen op elk gewenst moment naar elke wille keurige instantie kunnen worden doorgestuurd. Voor elk exemplaar wordt niet verwacht dat het vorige verzoek wordt onthouden. De status van de werk belasting wordt beheerd door een externe status opslag, zoals Azure Table Storage of Azure Cosmos DB. In Service Fabric wordt dit type werk belasting vertegenwoordigd door een stateless service. De eenvoudigste benadering voor het migreren van een werk rollen naar Service Fabric kan worden uitgevoerd door de code van de werk rollen te converteren naar een stateless service.

![Rol van werk rollen naar stateless service][4]

## <a name="web-role-to-stateless-service"></a>stateless service van webrol
Net als bij de rol van werk nemer vertegenwoordigt een webrole ook een staatloze werk belasting, en kan deze dus niet worden toegewezen aan een Service Fabric stateless service. Maar in tegens telling tot webrollen biedt Service Fabric geen ondersteuning voor IIS. Als u een webtoepassing van een webrole naar een stateless service wilt migreren, moet u eerst verplaatsen naar een webframework dat zelf kan worden gehost en niet afhankelijk zijn van IIS of System. Web, zoals ASP.NET Core 1.

| **Toepassing** | **Ondersteund** | **Migratie traject** |
| --- | --- | --- |
| Webformulieren voor ASP.NET |Nee |Converteren naar ASP.NET Core 1 MVC |
| ASP.NET MVC |Met migratie |Upgrade uitvoeren naar ASP.NET Core 1 MVC |
| ASP.NET Web-API |Met migratie |Een zelf-hostende server of ASP.NET Core 1 gebruiken |
| ASP.NET Core 1 |Ja |N.v.t. |

## <a name="entry-point-api-and-lifecycle"></a>Ingangs punt-API en levens cyclus
De Worker-en Service Fabric service-Api's bieden vergelijk bare ingangs punten: 

| **Ingangs punt** | **Worker-rol** | **Service Fabric-service** |
| --- | --- | --- |
| Wordt verwerkt |`Run()` |`RunAsync()` |
| VM starten |`OnStart()` |N.v.t. |
| VM stoppen |`OnStop()` |N.v.t. |
| Listener voor client aanvragen openen |N.v.t. |<ul><li> `CreateServiceInstanceListener()` voor stateless</li><li>`CreateServiceReplicaListener()` voor stateful</li></ul> |

### <a name="worker-role"></a>Worker-rol
```csharp

using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
        }

        public override bool OnStart()
        {
        }

        public override void OnStop()
        {
        }
    }
}

```

### <a name="service-fabric-stateless-service"></a>Stateless service Service Fabric
```csharp

using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace Stateless1
{
    public class Stateless1 : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
        }

        protected override Task RunAsync(CancellationToken cancelServiceInstance)
        {
        }
    }
}

```

Beide hebben een primaire ' uitvoeren '-onderdrukking waarbij kan worden begonnen met de verwerking. Service Fabric Services combi neren `Run` , `Start` , en `Stop` in één ingangs punt `RunAsync` . Uw service moet aan de slag gaan wanneer `RunAsync` het programma wordt gestart, en moet niet meer werken wanneer de `RunAsync` CancellationToken van de methode wordt gesignaleerd. 

Er zijn verschillende belang rijke verschillen tussen de levens cyclus en de levens duur van werk rollen en Service Fabric Services:

* **Levens cyclus:** Het grootste verschil is dat een Werknemersrol een VM is en dat de levens cyclus is gekoppeld aan de virtuele machine, die gebeurtenissen bevat voor wanneer de virtuele machine wordt gestart en gestopt. Een Service Fabric-service heeft een levens cyclus die losstaat van de levens cyclus van de VM, dus bevat geen gebeurtenissen voor wanneer de host-VM of-machine wordt gestart en gestopt, omdat deze niet aan elkaar zijn gerelateerd.
* **Levens duur:** Een instantie van een werk rollen wordt herhaald als de `Run` methode wordt afgesloten. De `RunAsync` methode in een service Fabric-service kan echter worden uitgevoerd om te worden voltooid en het service-exemplaar blijft actief. 

Service Fabric biedt een optioneel ingangs punt voor communicatie-installatie voor services die op client aanvragen Luis teren. Zowel het RunAsync als het communicatie-invoer punt zijn optionele onderdrukkingen in Service Fabric Services: uw service kan ervoor kiezen om alleen naar client aanvragen te Luis teren of alleen een verwerkings lus uit te voeren, of beide, waarom de methode RunAsync mag worden afgesloten zonder het service-exemplaar opnieuw te starten, omdat het mogelijk blijft Luis teren naar client aanvragen.

## <a name="application-api-and-environment"></a>Toepassings-API en-omgeving
De Cloud Services Environment API biedt informatie en functionaliteit voor het huidige VM-exemplaar, evenals informatie over andere exemplaren van een virtuele machine. Service Fabric bevat informatie met betrekking tot de runtime en enige informatie over het knoop punt waarop een service momenteel wordt uitgevoerd. 

| **Omgevings taak** | **Cloudservices** | **Service Fabric** |
| --- | --- | --- |
| Configuratie-instellingen en wijzigings meldingen |`RoleEnvironment` |`CodePackageActivationContext` |
| Lokale opslag |`RoleEnvironment` |`CodePackageActivationContext` |
| Eindpunt gegevens |`RoleInstance` <ul><li>Huidige instantie: `RoleEnvironment.CurrentRoleInstance`</li><li>Andere rollen en instanties: `RoleEnvironment.Roles`</li> |<ul><li>`NodeContext` voor het huidige knooppunt adres</li><li>`FabricClient` en `ServicePartitionResolver` voor service-eindpunt detectie</li> |
| Omgevings emulatie |`RoleEnvironment.IsEmulated` |N.v.t. |
| Gelijktijdige wijzigings gebeurtenis |`RoleEnvironment` |N.v.t. |

## <a name="configuration-settings"></a>Configuratie-instellingen
Configuratie-instellingen in Cloud Services worden ingesteld voor een VM-rol en zijn van toepassing op alle exemplaren van die VM-rol. Deze instellingen zijn sleutel-waardeparen die zijn ingesteld in ServiceConfiguration. *. cscfg-bestanden en kunnen rechtstreeks via RoleEnvironment worden geopend. In Service Fabric zijn instellingen afzonderlijk van toepassing op elke service en op elke toepassing, in plaats van op een virtuele machine, omdat een VM meerdere services en toepassingen kan hosten. Een service bestaat uit drie pakketten:

* **Code:** bevat de uitvoer bare bestanden van de service, binaire bestanden, dll's en alle andere bestands die een service moet uitvoeren.
* **Configuratie:** alle configuratie bestanden en-instellingen voor een service.
* **Gegevens:** statische gegevens bestanden die zijn gekoppeld aan de service.

Elk van deze pakketten kan onafhankelijk van versie zijn en kan worden bijgewerkt. Net als bij Cloud Services is er via een API een configuratie pakket toegankelijk via een of meer gebeurtenissen waarmee de service van een wijziging in het configuratie pakket kan worden gewaarschuwd. Een Settings.xml bestand kan worden gebruikt voor de configuratie van de sleutel waarde en programmatische toegang, vergelijkbaar met de sectie app-instellingen van een App.config bestand. In tegens telling tot Cloud Services, kan een Service Fabric configuratie pakket echter alle configuratie bestanden in een wille keurige indeling bevatten, of het nu XML, JSON, YAML of een aangepaste binaire indeling is. 

### <a name="accessing-configuration"></a>Configuratie openen
#### <a name="cloud-services"></a>Cloud Services
Configuratie-instellingen van ServiceConfiguration. *. cscfg kunnen worden geopend via `RoleEnvironment` . Deze instellingen zijn wereld wijd beschikbaar voor alle rolinstanties in dezelfde Cloud service-implementatie.

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
Elke service heeft zijn eigen afzonderlijke configuratie pakket. Er is geen ingebouwd mechanisme voor globale configuratie-instellingen die toegankelijk zijn voor alle toepassingen in een cluster. Wanneer Service Fabric u een speciaal Settings.xml configuratie bestand gebruikt binnen een configuratie pakket, kunnen de waarden in Settings.xml worden overschreven op toepassings niveau, waardoor er configuratie-instellingen op toepassings niveau mogelijk zijn.

Configuratie-instellingen zijn via de service geopend binnen elk service-exemplaar `CodePackageActivationContext` .

```csharp

ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");

// Access Settings.xml
KeyedCollection<string, ConfigurationProperty> parameters = configPackage.Settings.Sections["MyConfigSection"].Parameters;

string value = parameters["Key"]?.Value;

// Access custom configuration file:
using (StreamReader reader = new StreamReader(Path.Combine(configPackage.Path, "CustomConfig.json")))
{
    MySettings settings = JsonConvert.DeserializeObject<MySettings>(reader.ReadToEnd());
}

```

### <a name="configuration-update-events"></a>Configuratie-update gebeurtenissen
#### <a name="cloud-services"></a>Cloud Services
De `RoleEnvironment.Changed` gebeurtenis wordt gebruikt om alle rolinstanties op de hoogte te stellen wanneer er een wijziging optreedt in de omgeving, zoals een configuratie wijziging. Dit wordt gebruikt om configuratie-updates te gebruiken zonder de rolinstanties te recyclen of om een werk proces opnieuw te starten.

```csharp

RoleEnvironment.Changed += RoleEnvironmentChanged;

private void RoleEnvironmentChanged(object sender, RoleEnvironmentChangedEventArgs e)
{
   // Get the list of configuration changes
   var settingChanges = e.Changes.OfType<RoleEnvironmentConfigurationSettingChange>();
foreach (var settingChange in settingChanges) 
   {
      Trace.WriteLine("Setting: " + settingChange.ConfigurationSettingName, "Information");
   }
}

```

#### <a name="service-fabric"></a>Service Fabric
Elk van de drie pakket typen in een service code, configuratie en gegevens-bevatten gebeurtenissen waarmee een service-exemplaar wordt gewaarschuwd wanneer een pakket wordt bijgewerkt, toegevoegd of verwijderd. Een service kan meerdere pakketten van elk type bevatten. Een service kan bijvoorbeeld meerdere configuratie pakketten hebben, elk afzonderlijk versie nummer en uitbreidbaar. 

Deze gebeurtenissen zijn beschikbaar om wijzigingen in service pakketten te gebruiken zonder het service-exemplaar opnieuw te starten.

```csharp

this.Context.CodePackageActivationContext.ConfigurationPackageModifiedEvent +=
                    this.CodePackageActivationContext_ConfigurationPackageModifiedEvent;

private void CodePackageActivationContext_ConfigurationPackageModifiedEvent(object sender, PackageModifiedEventArgs<ConfigurationPackage> e)
{
    this.UpdateCustomConfig(e.NewPackage.Path);
    this.UpdateSettings(e.NewPackage.Settings);
}

```

## <a name="startup-tasks"></a>Opstart taken
Opstart taken zijn acties die worden uitgevoerd voordat een toepassing wordt gestart. Een opstart taak wordt meestal gebruikt voor het uitvoeren van installatie scripts met verhoogde bevoegdheden. Zowel Cloud Services als Service Fabric ondersteunen Start taken. Het belangrijkste verschil is dat in Cloud Services een opstart taak is gekoppeld aan een virtuele machine omdat deze deel uitmaakt van een rolinstantie, terwijl in Service Fabric een opstart taak is gekoppeld aan een service die niet is gekoppeld aan een bepaalde virtuele machine.

| Service Fabric | Cloud Services |
| --- | --- |
| Configuratie locatie |ServiceDefinition. csdef |
| Bevoegdheden |"beperkt" of "verhoogde bevoegdheid" |
| Sequentiëren |eenvoudige, achtergrond, voor grond |

### <a name="cloud-services"></a>Cloud Services
In Cloud Services wordt een opstart toegangs punt geconfigureerd per rol in ServiceDefinition. csdef. 

```xml

<ServiceDefinition>
    <Startup>
        <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
            <Environment>
                <Variable name="MyVersionNumber" value="1.0.0.0" />
            </Environment>
        </Task>
    </Startup>
    ...
</ServiceDefinition>

```

### <a name="service-fabric"></a>Service Fabric
In Service Fabric wordt een opstart toegangs punt geconfigureerd per service in ServiceManifest.xml:

```xml

<ServiceManifest>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Startup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    ...
</ServiceManifest>

``` 

## <a name="a-note-about-development-environment"></a>Een opmerking over de ontwikkel omgeving
Zowel Cloud Services als Service Fabric zijn geïntegreerd met Visual Studio met Project sjablonen en ondersteuning voor fout opsporing, configuratie en implementatie van zowel lokale als Azure. Zowel Cloud Services als Service Fabric bieden ook een Local Development runtime-omgeving. Het verschil is dat tijdens de implementatie van de Cloud service de Azure-omgeving wordt geëmuleerd waarop deze wordt uitgevoerd, Service Fabric geen emulator gebruikt. het maakt gebruik van de volledige Service Fabric runtime. De Service Fabric-omgeving die u op uw lokale ontwikkel computer uitvoert, is dezelfde omgeving die in productie wordt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen
Lees meer over Service Fabric Reliable Services en de belangrijkste verschillen tussen Cloud Services en Service Fabric toepassings architectuur om te begrijpen hoe u kunt profiteren van de volledige set Service Fabric-functies.

* [Aan de slag met Service Fabric Reliable Services](service-fabric-reliable-services-quick-start.md)
* [De conceptuele hand leiding voor de verschillen tussen Cloud Services en Service Fabric](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
