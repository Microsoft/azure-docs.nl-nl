---
title: Werk stroom van de architectuur van de Windows Azure-VM | Microsoft Docs
description: In dit artikel vindt u een overzicht van de werk stroom processen wanneer u een service implementeert.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 606510940460db963a2aa63deb57b6dba77de3ac
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700130"
---
# <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Werk stroom van de klassieke Windows Azure-VM-architectuur 

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

Dit artikel bevat een overzicht van de werk stroom processen die zich voordoen wanneer u een Azure-resource, zoals een virtuele machine, implementeert of bijwerkt. 

> [!NOTE]
>Azure heeft twee verschillende implementatie modellen voor het maken van en werken met resources: Resource Manager en klassiek. Dit artikel gaat over het gebruik van het klassieke implementatiemodel.

Het volgende diagram toont de architectuur van Azure-resources.

:::image type="content" source="./media/cloud-services-workflow-process/workflow.jpg" alt-text="<ALT de afbeelding over Azure workflow>":::

## <a name="workflow-basics"></a>Basis principes van werk stromen
   
**A**. RDFE/FFE is het communicatie traject van de gebruiker naar de infra structuur. RDFE (RedDog Front End) is de open bare API die de front-end is voor de Beheerportal en de Service Management-API, zoals Visual Studio, Azure MMC, enzovoort.  Alle aanvragen van de gebruiker passeren de RDFE. FFE (infra structuur-front-end) is de laag waarmee aanvragen van RDFE worden vertaald in infrastructuur opdrachten. Alle aanvragen van RDFE gaan door de FFE om de infrastructuur controllers te bereiken.

**B**. De infrastructuur controller is verantwoordelijk voor het onderhouden en bewaken van alle resources in het Data Center. Het communiceert met Fabric host agents in het Fabric-besturings systeem, zoals de versie van het gast besturingssysteem, het service pakket, de service configuratie en de service status.

**C**. De Hosta Gent bevindt zich op het hostbesturingssysteem en is verantwoordelijk voor het instellen van het gast besturingssysteem en het communiceren met de gast agent (WindowsAzureGuestAgent) om de rol bij te werken naar een beoogde doel status en heartbeat-controles uit te voeren met de gast agent. Als de Hosta Gent gedurende tien minuten geen heartbeat-antwoord ontvangt, wordt het gast besturingssysteem door de host-agent opnieuw gestart.

**C2**. WaAppAgent is verantwoordelijk voor het installeren, configureren en bijwerken van WindowsAzureGuestAgent.exe.

**D**.  WindowsAzureGuestAgent is verantwoordelijk voor het volgende:

1. Het gast besturingssysteem configureren, met inbegrip van Firewall, Acl's, LocalStorage resources, service pakket en configuratie en certificaten.
2. Instellen van de SID voor het gebruikers account waarvoor de rol wordt uitgevoerd.
3. De status van de rol te communiceren met de infra structuur.
4. WaHostBootstrapper starten en controleren om er zeker van te zijn dat de functie de doel status heeft.

**E**. WaHostBootstrapper is verantwoordelijk voor:

1. Het lezen van de functie configuratie en het starten van alle juiste taken en processen om de rol te configureren en uit te voeren.
2. Alle onderliggende processen bewaken.
3. Het verhogen van de gebeurtenis StatusCheck in het hostproces van de rol.

**F**. IISConfigurator wordt uitgevoerd als de rol is geconfigureerd als een volledige IIS-webrole. Het is verantwoordelijk voor:

1. De standaard IIS-services starten
2. De module herschrijven in de Webconfiguratie configureren
3. De AppPool instellen voor de geconfigureerde rol in het service model
4. IIS-logboek registratie instellen om te verwijzen naar de map DiagnosticStore LocalStorage
5. Machtigingen en Acl's configureren
6. De website bevindt zich in% roleroot%: \sitesroot\0 en de AppPool verwijst naar deze locatie om IIS uit te voeren. 

**G**. Opstart taken worden gedefinieerd door het Role Model en gestart door WaHostBootstrapper. Opstart taken kunnen asynchroon op de achtergrond worden uitgevoerd en de Boots Trapper start de opstart taak en gaat vervolgens door met andere opstart taken. Opstart taken kunnen ook worden geconfigureerd om te worden uitgevoerd in de modus eenvoudig (standaard), waarbij de host Boots Trapper wacht totdat de opstart taak is voltooid en retour neren een geslaagde (0) afsluit code voordat u verdergaat met de volgende opstart taak.

**H**. Deze taken maken deel uit van de SDK en worden gedefinieerd als invoeg toepassingen in de service definitie (. csdef) van de functie. Bij het uitvouwen van opstart taken zijn de **DiagnosticsAgent** en **RemoteAccessAgent** uniek in die gebruikers elk twee opstart taken definiëren, een regel matig en een die een **/blockStartup** -para meter heeft. De normale opstart taak wordt gedefinieerd als een opstart taak op de achtergrond zodat deze op de achtergrond kan worden uitgevoerd terwijl de rol zelf wordt uitgevoerd. De opstart taak **/blockStartup** wordt gedefinieerd als een eenvoudige opstart taak, zodat WaHostBootstrapper wacht totdat deze is afgesloten voordat u doorgaat. De taak **/blockStartup** wacht totdat de normale taak is geïnitialiseerd en vervolgens wordt afgesloten en de host Boots Trapper kan door gaan. Dit wordt gedaan, zodat diagnostische gegevens en RDP-toegang kunnen worden geconfigureerd voordat de functie wordt gestart (dit wordt gedaan via de taak/blockStartup). Op deze manier kunnen diagnostische gegevens en RDP-toegang blijven worden uitgevoerd nadat de opstart taken zijn voltooid door de Boots Trapper van de host (dit gebeurt via de normale taak).

**I**. WaWorkerHost is het standaard hostproces voor normale werk rollen. Dit hostproces host alle Dll's van de rol en de code van het toegangs punt, zoals onstart en run.

**J**. WaIISHost is het hostproces voor de functie-invoer punt code voor webrollen die gebruikmaken van volledige IIS. Dit proces laadt de eerste gevonden DLL die de klasse **RoleEntryPoint** gebruikt en voert de code uit deze klasse uit (onstart, run, OnStop). Alle **RoleEnvironment** -gebeurtenissen (zoals StatusCheck en gewijzigd) die in de RoleEntryPoint-klasse zijn gemaakt, worden in dit proces gegenereerd.

**K**. W3WP is het standaard-IIS-werk proces dat wordt gebruikt als de rol is geconfigureerd voor het gebruik van volledige IIS. Hiermee wordt de AppPool uitgevoerd die is geconfigureerd vanuit IISConfigurator. Alle RoleEnvironment-gebeurtenissen (zoals StatusCheck en gewijzigd) die hier worden gemaakt, worden in dit proces gegenereerd. Houd er rekening mee dat RoleEnvironment-gebeurtenissen worden gestart op beide locaties (WaIISHost en w3wp.exe) als u zich abonneert op gebeurtenissen in beide processen.

## <a name="workflow-processes"></a>Werk stroom processen

1. Een gebruiker doet een aanvraag, zoals het uploaden van de bestanden '. cspkg ' en '. cscfg ', waarmee een bron wordt gewaarschuwd om te stoppen of een configuratie wijziging aan te brengen, enzovoort. Dit kan worden gedaan via de Azure Portal of een hulp programma dat gebruikmaakt van de Service Management-API, zoals de functie voor het publiceren van Visual Studio. Met deze aanvraag gaat u naar RDFE om alle werkzaamheden aan het abonnement uit te voeren en vervolgens de aanvraag door te geven aan FFE. De rest van deze werk stroom stappen is om een nieuw pakket te implementeren en te starten.
2. FFE vindt de juiste machine groep (op basis van invoer van de klant, zoals de affiniteits groep of geografische locatie plus de invoer van de infra structuur, zoals de beschik baarheid van de machine) en communiceert met de Master Fabric-controller in die machine groep.
3. De infrastructuur controller vindt een host met beschik bare CPU-kernen (of draait een nieuwe host op). Het service pakket en de configuratie worden gekopieerd naar de host en de infrastructuur controller communiceert met de Hosta Gent op het hostbesturingssysteem om het pakket te implementeren (spannings dips, poorten, gast besturingssysteem configureren, enzovoort).
4. De Hosta Gent start het gast besturingssysteem en communiceert met de gast agent (WindowsAzureGuestAgent). De host verzendt heartbeats naar de gast om er zeker van te zijn dat de rol werkt naar de doel status.
5. WindowsAzureGuestAgent stelt het gast besturingssysteem (firewall, Acl's, LocalStorage, enzovoort) in, kopieert een nieuw XML-configuratie bestand naar c:\Config en start vervolgens het WaHostBootstrapper proces.
6. Voor volledige IIS-webfuncties start WaHostBootstrapper IISConfigurator en geeft het aan dat alle bestaande AppPools voor de webrole uit IIS moeten worden verwijderd.
7. WaHostBootstrapper leest de **opstart** taken van E:\RoleModel.xml en begint met het uitvoeren van opstart taken. WaHostBootstrapper wacht totdat alle eenvoudige opstart taken zijn voltooid en het bericht ' geslaagd ' heeft geretourneerd.
8. Voor volledige IIS-webfuncties vertelt WaHostBootstrapper IISConfigurator het configureren van de IIS-AppPool en verwijst de site naar `E:\Sitesroot\<index>` , waar `<index>` is een op nul gebaseerde index in het aantal elementen dat is `<Sites>` gedefinieerd voor de service.
9. WaHostBootstrapper start het host proces, afhankelijk van het type rol:
    1. **Worker-rol**: WaWorkerHost.exe is gestart. WaHostBootstrapper voert de methode onstart () uit. Nadat deze is geretourneerd, begint WaHostBootstrapper met het uitvoeren van de methode Run () en wordt de functie vervolgens gelijktijdig gemarkeerd als gereed en wordt deze in de load balancer draaiing geplaatst (als InputEndpoints zijn gedefinieerd). WaHostBootsrapper gaat vervolgens naar een lus voor het controleren van de functie status.
    2. **Volledige IIS-webrole**: aIISHost is gestart. WaHostBootstrapper voert de methode onstart () uit. Nadat deze is geretourneerd, wordt de methode Run () gestart en wordt de functie vervolgens gelijktijdig gemarkeerd als gereed en wordt deze in de load balancer draaiing geplaatst. WaHostBootsrapper gaat vervolgens naar een lus voor het controleren van de functie status.
10. Binnenkomende webaanvragen voor een volledige IIS-webfunctie activeert IIS om het W3WP-proces te starten en de aanvraag te behandelen, op dezelfde manier als in een on-premises IIS-omgeving.

## <a name="log-file-locations"></a>Locaties van logboek bestanden

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
Dit logboek bevat wijzigingen in de service, waaronder start-, stop-en nieuwe configuraties. Als de service niet wordt gewijzigd, kunt u verwachten dat er grote onderbrekingen in dit logboek bestand worden weer geven.
- C:\Logs\WaAppAgent.Log.  
Dit logboek bevat status updates en heartbeat-meldingen en wordt elke 2-3 seconden bijgewerkt.  Dit logboek bevat een historisch overzicht van de status van het exemplaar en u krijgt een melding wanneer het exemplaar niet de status gereed heeft.
 
**WaHostBootstrapper**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**WaIISHost**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**IISConfigurator**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**IIS-logboeken**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**Windows-gebeurtenis logboeken**

`D:\Windows\System32\Winevt\Logs`
