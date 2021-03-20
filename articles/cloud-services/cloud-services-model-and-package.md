---
title: Wat is een Cloud service (klassiek) model en pakket | Microsoft Docs
description: Hierin worden het Cloud service model (. csdef,. cscfg) en het pakket (. cspkg) in azure beschreven
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 1cf8e966b80e005a0cb2cf7ea46f355e38cb0011
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98741533"
---
# <a name="what-is-the-cloud-service-classic-model-and-how-do-i-package-it"></a>Wat is het model Cloud service (klassiek) en hoe kan ik het pakket inpakken?

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

Een Cloud service wordt gemaakt op basis van drie onderdelen, de service definitie *(. csdef)*, de service configuratie *(. cscfg)* en een service pakket *(. cspkg)*. De bestanden **ServiceDefinition. csdef** en **ServiceConfig. cscfg** zijn gebaseerd op XML en beschrijven de structuur van de Cloud service en hoe deze zijn geconfigureerd. het model wordt gezamenlijk genoemd. **ServicePackage. cspkg** is een zip-bestand dat is gegenereerd op basis van de **ServiceDefinition. csdef** en onder andere, bevat alle vereiste op basis van binaire afhankelijkheden. Azure maakt een Cloud service van zowel **ServicePackage. cspkg** als **ServiceConfig. cscfg**.

Zodra de Cloud service wordt uitgevoerd in azure, kunt u deze opnieuw configureren via het **ServiceConfig. cscfg** -bestand, maar u kunt de definitie niet wijzigen.

## <a name="what-would-you-like-to-know-more-about"></a>Waarover wilt u meer weten?
* Ik wil meer weten over de bestanden [ServiceDefinition. csdef](#csdef) en [ServiceConfig. cscfg](#cscfg) .
* Ik weet er al over dat, ik wil graag [enkele voor beelden](#next-steps) kennen van wat ik kan configureren.
* Ik wil de [ServicePackage. cspkg](#cspkg)maken.
* Ik gebruik Visual Studio en ik wil...
  * [Een Cloud service maken][vs_create]
  * [Een bestaande Cloud service opnieuw configureren][vs_reconfigure]
  * [Een Cloud service project implementeren][vs_deploy]
  * [Extern bureau blad in een Cloud service-exemplaar][remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>ServiceDefinition. csdef
Het bestand **ServiceDefinition. csdef** geeft de instellingen op die door Azure worden gebruikt voor het configureren van een Cloud service. Het [Azure service definition-schema (csdef-bestand)](/previous-versions/azure/reference/ee758711(v=azure.100)) biedt de toegestane indeling voor een service definitie bestand. In het volgende voor beeld ziet u de instellingen die kunnen worden gedefinieerd voor de web-en werk rollen:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

U kunt het [service definitie schema](/previous-versions/azure/reference/ee758711(v=azure.100)) raadplegen voor een beter begrip van het XML-schema dat hier wordt gebruikt, maar hier volgt een korte uitleg van een aantal van de elementen:

**Sites**  
Bevat de definities voor websites of webtoepassingen die worden gehost in IIS7.

**InputEndpoints**  
Bevat de definities voor eind punten die worden gebruikt om contact op te nemen met de Cloud service.

**InternalEndpoints**  
Bevat de definities voor eind punten die worden gebruikt door rolinstanties om met elkaar te communiceren.

**ConfigurationSettings**  
Bevat de instellings definities voor functies van een specifieke rol.

**Certificaten**  
Bevat de definities voor certificaten die nodig zijn voor een rol. Het vorige code voorbeeld toont een certificaat dat wordt gebruikt voor de configuratie van Azure Connect.

**LocalResources**  
Bevat de definities voor lokale opslag resources. Een lokale opslag resource is een gereserveerde map op het bestands systeem van de virtuele machine waarin een exemplaar van een rol wordt uitgevoerd.

**Rusland**  
Bevat de definities voor geïmporteerde modules. Het vorige code voorbeeld toont de modules voor Verbinding met extern bureaublad en Azure Connect.

**Opstarten**  
Bevat taken die worden uitgevoerd wanneer de rol wordt gestart. De taken worden gedefinieerd in een. cmd-of een uitvoerbaar bestand.

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration. cscfg
De configuratie van de instellingen voor uw Cloud service wordt bepaald door de waarden in het bestand **ServiceConfiguration. cscfg** . U geeft het aantal exemplaren op dat u wilt implementeren voor elke rol in dit bestand. De waarden voor de configuratie-instellingen die u in het service definitie bestand hebt gedefinieerd, worden toegevoegd aan het service configuratie bestand. De vinger afdrukken voor beheer certificaten die zijn gekoppeld aan de Cloud service worden ook toegevoegd aan het bestand. Het [Configuratie schema van de Azure-service (. cscfg-bestand)](/previous-versions/azure/reference/ee758710(v=azure.100)) biedt de toegestane indeling voor een service configuratie bestand.

Het service configuratie bestand is niet verpakt met de toepassing, maar wordt als een afzonderlijk bestand geüpload naar Azure en wordt gebruikt om de Cloud service te configureren. U kunt een nieuw service configuratie bestand uploaden zonder uw Cloud service opnieuw te implementeren. De configuratie waarden voor de Cloud service kunnen worden gewijzigd terwijl de Cloud service wordt uitgevoerd. In het volgende voor beeld ziet u de configuratie-instellingen die kunnen worden gedefinieerd voor de web-en werk rollen:

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

U kunt het schema voor [Service configuratie](/previous-versions/azure/reference/ee758710(v=azure.100)) raadplegen voor een beter inzicht in het XML-schema dat hier wordt gebruikt, maar hier volgt een korte uitleg van de elementen:

**exemplaren**  
Hiermee configureert u het aantal actieve instanties voor de rol. Om te voor komen dat uw Cloud service mogelijk niet meer beschikbaar is tijdens upgrades, is het raadzaam om meer dan één exemplaar van de webfacing-rollen te implementeren. Door meer dan één exemplaar te implementeren, moet u zich houden aan de richt lijnen in de [Azure compute service level Agreement (Sla)](https://azure.microsoft.com/support/legal/sla/), die 99,95% externe connectiviteit voor Internet gerichte rollen garandeert wanneer twee of meer rolinstanties voor een service worden geïmplementeerd.

**ConfigurationSettings**  
Hiermee configureert u de instellingen voor de actieve instanties voor een rol. De naam van de `<Setting>` elementen moet overeenkomen met de instellings definities in het service definitie bestand.

**Certificaten**  
Hiermee configureert u de certificaten die door de service worden gebruikt. Het vorige code voorbeeld laat zien hoe u het certificaat voor de RemoteAccess-module definieert. De waarde van het kenmerk *vinger afdruk* moet worden ingesteld op de vinger afdruk van het certificaat dat moet worden gebruikt.

<p/>

> [!NOTE]
> De vinger afdruk voor het certificaat kan worden toegevoegd aan het configuratie bestand met behulp van een tekst editor. Of de waarde kan worden toegevoegd op het tabblad **certificaten** van de pagina **Eigenschappen** van de rol in Visual Studio.
> 
> 

## <a name="defining-ports-for-role-instances"></a>Poorten definiëren voor rolinstanties
Azure staat slechts één toegangs punt toe voor een webrole. Dit betekent dat alle verkeer plaatsvindt via één IP-adres. U kunt uw websites zo configureren dat deze een poort delen door de hostheader zo te configureren dat de aanvraag naar de juiste locatie wordt geleid. U kunt uw toepassingen ook configureren om te Luis teren naar bekende poorten op het IP-adres.

In het volgende voor beeld ziet u de configuratie voor een webrole met een website en web-app. De website is geconfigureerd als de standaard locatie voor invoer op poort 80 en de webtoepassingen worden geconfigureerd voor het ontvangen van aanvragen van een alternatieve host-header met de naam ' mail.mysite.cloudapp.net '.

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" port="80" />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" hostHeader="mail.mysite.cloudapp.net" />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>De configuratie van een rol wijzigen
U kunt de configuratie van uw Cloud service bijwerken terwijl deze wordt uitgevoerd in azure, zonder de service offline te halen. Als u configuratie-informatie wilt wijzigen, kunt u een nieuw configuratie bestand uploaden of het configuratie bestand op locatie bewerken en Toep assen op uw actieve service. De volgende wijzigingen kunnen worden aangebracht in de configuratie van een service:

* **De waarden van de configuratie-instellingen wijzigen**  
  Wanneer een configuratie-instelling wordt gewijzigd, kan een rolinstantie ervoor kiezen de wijziging toe te passen terwijl het exemplaar online is, of om het exemplaar op de juiste wijze te recyclen en de wijziging toe te passen terwijl het exemplaar offline is.
* **De service topologie van rolinstanties wijzigen**  
  De topologie wijzigingen hebben geen invloed op actieve instanties, behalve wanneer een exemplaar wordt verwijderd. Alle overige instanties hoeven over het algemeen niet te worden gerecycled. u kunt er echter voor kiezen om rolinstanties te recyclen als reactie op een wijziging in de topologie.
* **De vinger afdruk van het certificaat wijzigen**  
  U kunt een certificaat alleen bijwerken wanneer een rolinstantie offline is. Als een certificaat wordt toegevoegd, verwijderd of gewijzigd terwijl een rolinstantie online is, neemt Azure het exemplaar op de juiste manier offline om het certificaat bij te werken en weer online te zetten nadat de wijziging is voltooid.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Configuratie wijzigingen afhandelen met Service runtime-gebeurtenissen
De [Azure runtime library](/previous-versions/azure/reference/mt419365(v=azure.100)) bevat de naam ruimte [micro soft. WindowsAzure. ServiceRuntime](/previous-versions/azure/reference/ee741722(v=azure.100)) , die klassen biedt voor interactie met de Azure-omgeving vanuit een rol. De klasse [RoleEnvironment](/previous-versions/azure/reference/ee773173(v=azure.100)) definieert de volgende gebeurtenissen die worden gegenereerd voor en na een configuratie wijziging:

* **Gebeurtenis [wijzigen](/previous-versions/azure/reference/ee758134(v=azure.100))**  
  Dit gebeurt voordat de configuratie wijziging wordt toegepast op een opgegeven exemplaar van een rol, zodat u zo nodig de rolinstanties kunt door voeren.
* **[Gewijzigde](/previous-versions/azure/reference/ee758129(v=azure.100)) gebeurtenis**  
  Deze gebeurtenis treedt op nadat de configuratie wijziging is toegepast op een opgegeven exemplaar van een rol.

> [!NOTE]
> Omdat certificaat wijzigingen altijd de instanties van een rol offline nemen, worden de RoleEnvironment. changed of RoleEnvironment. gewijzigde gebeurtenissen niet verhoogd.
> 
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>ServicePackage. cspkg
> [!NOTE]
> De maximale pakket grootte die kan worden geïmplementeerd, is 600MB

Als u een toepassing als een Cloud service in azure wilt implementeren, moet u de toepassing eerst in de juiste indeling inpakken. U kunt het opdracht regel programma **CSPack** (geïnstalleerd met de Azure- [SDK](https://azure.microsoft.com/downloads/)) gebruiken om het pakket bestand te maken als een alternatief voor Visual Studio.

**CSPack** maakt gebruik van de inhoud van het service definitie bestand en service configuratie bestand om de inhoud van het pakket te definiëren. **CSPack** genereert een toepassings pakket bestand (. cspkg) dat u naar Azure kunt uploaden met behulp van de [Azure Portal](cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Het pakket heet standaard `[ServiceDefinitionFileName].cspkg` , maar u kunt een andere naam opgeven met behulp `/out` van de optie **CSPack**.

**CSPack** bevindt zich op  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> CSPack.exe (in Windows) is beschikbaar door de **Microsoft Azure opdracht prompt** snelkoppeling uit te voeren die met de SDK is geïnstalleerd.  
> 
> Voer het CSPack.exe-programma zelfstandig uit om documentatie over alle mogelijke switches en opdrachten weer te geven.
> 
> 

<p />

> [!TIP]
> Voer uw Cloud service lokaal uit in de **Microsoft Azure Compute-emulator** en gebruik de optie **/copyonly** . Met deze optie kopieert u de binaire bestanden voor de toepassing naar een directory-indeling van waaruit ze kunnen worden uitgevoerd in de compute emulator.
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>Voorbeeld opdracht voor het inpakken van een Cloud service
In het volgende voor beeld wordt een toepassings pakket gemaakt dat de informatie voor een webrole bevat. De opdracht geeft het service definitie bestand op dat moet worden gebruikt, de map waar binaire bestanden kunnen worden gevonden en de naam van het pakket bestand.

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

Als de toepassing zowel een webrole als een worker-rol bevat, wordt de volgende opdracht gebruikt:

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /out:[OutputFileName]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]
```

Waar de variabelen als volgt worden gedefinieerd:

| Variabele | Waarde |
| --- | --- |
| \[DirectoryName\] |De submap onder de hoofdmap van het project met het. csdef-bestand van het Azure-project. |
| \[ServiceDefinition\] |De naam van het service definitie bestand. Dit bestand heeft standaard de naam ServiceDefinition. csdef. |
| \[OutputFileName\] |De naam voor het gegenereerde pakket bestand. Dit wordt doorgaans ingesteld op de naam van de toepassing. Als er geen bestands naam is opgegeven, wordt het toepassings pakket gemaakt als \[ ApplicationName \] . cspkg. |
| \[RoleName\] |De naam van de rol zoals gedefinieerd in het service definitie bestand. |
| \[RoleBinariesDirectory] |De locatie van de binaire bestanden voor de rol. |
| \[VirtualPath\] |De fysieke mappen voor elk virtueel pad dat is gedefinieerd in de sectie sites van de service definitie. |
| \[PhysicalPath\] |De fysieke mappen van de inhoud voor elk virtueel pad dat is gedefinieerd in het knoop punt site van de service definitie. |
| \[RoleAssemblyName\] |De naam van het binaire bestand voor de rol. |

## <a name="next-steps"></a>Volgende stappen
Ik maak een Cloud service pakket en ik wil...

* [Extern bureau blad instellen voor een Cloud service-exemplaar][remotedesktop]
* [Een Cloud service project implementeren][deploy]

Ik gebruik Visual Studio en ik wil...

* [Een nieuwe Cloud service maken][vs_create]
* [Een bestaande Cloud service opnieuw configureren][vs_reconfigure]
* [Een Cloud service project implementeren][vs_deploy]
* [Extern bureau blad instellen voor een Cloud service-exemplaar][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop-new-portal.md
[vs_remote]: cloud-services-role-enable-remote-desktop-visual-studio.md
[vs_deploy]: /visualstudio/azure/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio
[vs_reconfigure]: /visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service
[vs_create]: /visualstudio/azure/vs-azure-tools-azure-project-create