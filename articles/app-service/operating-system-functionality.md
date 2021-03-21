---
title: Functionaliteit van besturingssysteem
description: Meer informatie over de functionaliteit van het besturings systeem in Azure App Service in Windows. Meer informatie over de typen bestands-, netwerk-en register toegang die uw app krijgt.
ms.assetid: 39d5514f-0139-453a-b52e-4a1c06d8d914
ms.topic: article
ms.date: 10/30/2018
ms.custom: seodec18
ms.openlocfilehash: 949e408544e25cb55622cf2a1b1d2dddb92350a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001504"
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Functionaliteit van het besturings systeem op Azure App Service
In dit artikel wordt de algemene besturingssysteem functionaliteit beschreven die beschikbaar is voor alle Windows-apps die worden uitgevoerd op [Azure app service](./overview.md). Deze functionaliteit omvat bestands-, netwerk-en register toegang, en Diagnostische logboeken en-gebeurtenissen. 

> [!NOTE] 
> [Linux-apps](overview.md#app-service-on-linux) in app service worden uitgevoerd in hun eigen containers. Geen toegang tot het besturings systeem van de host is toegestaan. u hebt toegang tot de hoofd database. Voor [apps die in Windows-containers worden uitgevoerd](quickstart-custom-container.md?pivots=container-windows), hebt u ook beheerders toegang tot de container, maar geen toegang tot het hostbesturingssysteem. 
>

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>App Service plan lagen
App Service voert klant-apps uit in een omgeving met meerdere tenants. Apps die zijn geïmplementeerd in de lagen **gratis** en **gedeeld** , worden uitgevoerd in werk processen op gedeelde virtuele machines, terwijl apps die zijn geïmplementeerd in de lagen **Standard** en **Premium** , worden uitgevoerd op de virtuele machine (s) die speciaal zijn toegewezen voor de apps die zijn gekoppeld aan één klant.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Omdat App Service een naadloze schaal baarheid tussen verschillende lagen ondersteunt, blijft de beveiligings configuratie die voor App Service apps wordt afgedwongen hetzelfde. Dit zorgt ervoor dat apps niet plotseling kunnen werken en op onverwachte manieren kunnen mislukken, wanneer App Service plan overschakelt van de ene laag naar de andere.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Ontwikkelings raamwerken
App Service prijs categorieën bepalen de hoeveelheid reken bronnen (CPU, schijf opslag, geheugen en netwerk uitgaand verkeer) die beschikbaar zijn voor apps. De breedte van de Framework-functionaliteit die beschikbaar is voor apps, blijft echter hetzelfde, ongeacht de schaal lagen.

App Service ondersteunt diverse ontwikkel frameworks, waaronder ASP.NET, klassieke ASP, node.js, PHP en Python, die allemaal worden uitgevoerd als uitbrei dingen in IIS. Om beveiligings configuratie te vereenvoudigen en te normaliseren, moeten App Service-apps doorgaans de verschillende ontwikkel Frameworks uitvoeren met de standaard instellingen. Een van de methoden voor het configureren van apps kan de API surface area en de functionaliteit voor elk afzonderlijk ontwikkelings raamwerk aanpassen. App Service in plaats daarvan heeft een meer algemene benadering door een algemene basis van de functionaliteit van het besturings systeem in te scha kelen, ongeacht het ontwikkelings raamwerk van een app.

De volgende secties bevatten een overzicht van de algemene soorten functionaliteit van het besturings systeem die beschikbaar zijn voor App Service-apps.

<a id="FileAccess"></a>

## <a name="file-access"></a>Bestands toegang
Er zijn verschillende stations in App Service, met inbegrip van lokale stations en netwerk stations.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Lokale stations
App Service is een service die wordt uitgevoerd op de kern van de Azure PaaS-infra structuur (platform as a Service). Als gevolg hiervan zijn de lokale stations die zijn gekoppeld aan een virtuele machine dezelfde schijf typen die beschikbaar zijn voor alle werk rollen die worden uitgevoerd in Azure. Dit omvat:

- Een station met een besturings systeem (de D:\ stationsletter
- Een toepassings station dat Azure package cspkg-bestanden bevat die uitsluitend door App Service worden gebruikt (en die niet toegankelijk zijn voor klanten)
- Een ' gebruiker ' station (de C:\ station), waarvan de grootte varieert, afhankelijk van de grootte van de virtuele machine. 

Het is belang rijk om uw schijf gebruik te bewaken naarmate uw toepassing groeit. Als het schijf quotum is bereikt, kan dit nadelige gevolgen hebben voor uw toepassing. Bijvoorbeeld: 

- De app genereert mogelijk een fout die aangeeft dat er onvoldoende ruimte op de schijf is.
- Er worden mogelijk schijf fouten weer geven wanneer u naar de kudu-console bladert.
- Implementatie vanuit Azure DevOps of Visual Studio kan mislukken met `ERROR_NOT_ENOUGH_DISK_SPACE: Web deployment task failed. (Web Deploy detected insufficient space on disk)` .
- Uw app kan tragere prestaties verslechteren.

<a id="NetworkDrives"></a>

### <a name="network-drives-unc-shares"></a>Netwerk stations (UNC-shares)
Een van de unieke aspecten van App Service die het implementeren en onderhouden van apps eenvoudig maken, is dat alle gebruikers inhoud wordt opgeslagen op een aantal UNC-shares. Dit model is goed voor het algemene patroon van inhouds opslag dat wordt gebruikt door on-premises webhosting omgevingen met meerdere servers met gelijke taak verdeling. 

Binnen App Service is er een aantal UNC-shares gemaakt in elk Data Center. Een percentage van de gebruikers inhoud voor alle klanten in elk Data Center wordt toegewezen aan elke UNC-share. Daarnaast wordt alle bestands inhoud voor één abonnement van een klant altijd op dezelfde UNC-share geplaatst. 

Als gevolg van de werking van Azure-Services, wordt de specifieke virtuele machine die verantwoordelijk is voor het hosten van een UNC-share na verloop van tijd gewijzigd. Het is gegarandeerd dat UNC-shares worden gekoppeld door verschillende virtuele machines, omdat deze worden weer binnengebracht tijdens de normale loop periode van Azure-bewerkingen. Daarom moeten apps geen hardcoded veronderstellingen maken dat de computer gegevens in een UNC-bestandspad in de loop van de tijd stabiel blijven. In plaats daarvan moeten ze gebruikmaken van het handige *faux* absolute pad **D:\home\site** dat app service biedt. Dit faux absolute pad biedt een draag bare neutraal-methode voor het verwijzen naar een eigen app. Door **D:\home\site** te gebruiken, kunt u gedeelde bestanden van de app naar de app overdragen zonder dat u een nieuw absoluut pad hoeft te configureren voor elke overdracht.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Typen bestands toegang die aan een app worden verleend
Het abonnement van elke klant heeft een gereserveerde mapstructuur op een specifieke UNC-share binnen een Data Center. Een klant kan meerdere apps hebben gemaakt binnen een specifiek Data Center, zodat alle mappen die tot één klant abonnement behoren, worden gemaakt op dezelfde UNC-share. De share kan mappen bevatten zoals die voor inhoud, fout-en Diagnostische logboeken en eerdere versies van de app die zijn gemaakt door broncode beheer. Zoals verwacht, zijn de app-mappen van een klant beschikbaar voor lees-en schrijf toegang tijdens runtime door de toepassings code van de app.

Op de lokale stations die zijn gekoppeld aan de virtuele machine waarop een app wordt uitgevoerd, reserveert App Service een deel van de ruimte op de C:\ station voor app-specifieke tijdelijke lokale opslag. Hoewel een app volledige lees-/schrijftoegang heeft tot de eigen tijdelijke lokale opslag, is die opslag echt niet bedoeld om rechtstreeks door de toepassings code te worden gebruikt. In plaats daarvan is het doel om tijdelijke bestands opslag te bieden voor IIS-en Web Application Frameworks. App Service beperkt ook de hoeveelheid tijdelijke lokale opslag ruimte die beschikbaar is voor elke app om te voor komen dat afzonderlijke apps buitensporige aantallen lokale bestands opslag gebruiken.

Twee voor beelden van hoe App Service tijdelijke lokale opslag gebruikt, zijn de map voor tijdelijke ASP.NET-bestanden en de map voor IIS-gecomprimeerde bestanden. Het ASP.NET-compilatie systeem gebruikt de map ' Temporary ASP.NET files ' als tijdelijke compilatie cache locatie. IIS maakt gebruik van de directory ' IIS Temporary Compressed Files ' voor het opslaan van de uitvoer van gecomprimeerde antwoorden. Beide typen bestands gebruik (evenals andere) worden opnieuw toegewezen in App Service aan de tijdelijke lokale opslag per app. Deze opnieuw toewijzen zorgt ervoor dat de functionaliteit wordt voortgezet zoals verwacht.

Elke app in App Service wordt uitgevoerd als een wille keurige unieke id voor een werk proces met beperkte machtigingen, de ' identiteit van de groep van toepassingen ', zoals hier wordt beschreven: [https://www.iis.net/learn/manage/configuring-security/application-pool-identities](https://www.iis.net/learn/manage/configuring-security/application-pool-identities) . Toepassings code gebruikt deze identiteit voor basis alleen-lezen toegang tot het station van het besturings systeem (de D:\ station). Dit betekent dat de toepassings code algemene mapstructuren kan weer geven en algemene bestanden op het besturingssysteem station kan lezen. Hoewel dit een enigszins breed onderliggend toegangs niveau lijkt, zijn dezelfde mappen en bestanden toegankelijk wanneer u een werknemersrol inricht in een door Azure gehoste service en de inhoud van het station leest. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Bestands toegang op meerdere instanties
De basismap bevat de inhoud van een app en de toepassings code kan ernaar schrijven. Als een app wordt uitgevoerd op meerdere instanties, wordt de basismap gedeeld met alle instanties zodat alle instanties dezelfde directory zien. Als een app bijvoorbeeld geüploade bestanden opslaat in de basismap, zijn deze bestanden direct beschikbaar voor alle exemplaren. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Netwerktoegang
Toepassings code kan gebruikmaken van TCP/IP-en UDP-protocollen om uitgaande netwerk verbindingen te maken met internet toegankelijke eind punten die externe services beschikbaar stellen. Apps kunnen deze protocollen gebruiken om verbinding te maken met Services in azure, bijvoorbeeld door HTTPS-verbindingen tot stand te brengen met SQL Database.

Er is ook een beperkte mogelijkheid voor apps om één lokale loop back-verbinding tot stand te brengen en een app te laten Luis teren naar die lokale loop back-socket. Deze functie bestaat voornamelijk om apps in te scha kelen die Luis teren op lokale loop back-sockets als onderdeel van hun functionaliteit. Elke app ziet een ' persoonlijke ' loop back-verbinding. De app ' A ' kan niet Luis teren naar een lokale loop back-socket die is ingesteld door de app ' B '.

Named pipes worden ook ondersteund als een interproces communicatie (IPC)-mechanisme tussen verschillende processen waarmee gezamenlijk een app wordt uitgevoerd. De IIS-FastCGI-module is bijvoorbeeld afhankelijk van named pipes voor het coördineren van de afzonderlijke processen waarmee PHP-pagina's worden uitgevoerd.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>Uitvoering van code, processen en geheugen
Zoals eerder is opgemerkt, worden apps uitgevoerd in werk processen met een beperkt aantal privileges met een id van een wille keurige groep van toepassingen. De toepassings code heeft toegang tot de geheugen ruimte die is gekoppeld aan het werk proces, evenals alle onderliggende processen die kunnen worden uitgevoerd door CGI-processen of andere toepassingen. Een app heeft echter geen toegang tot het geheugen of de gegevens van een andere app, zelfs als deze zich op dezelfde virtuele machine bevindt.

Apps kunnen scripts of pagina's uitvoeren die zijn geschreven met ondersteunde Web Development-Frameworks. App Service configureert geen instellingen voor een webframework naar een beperktere modus. Bijvoorbeeld: ASP.NET-apps die worden uitgevoerd op App Service worden in volledig vertrouwen uitgevoerd in plaats van een vertrouwens modus met beperkte toegang. Web frameworks, met inbegrip van klassieke ASP en ASP.NET, kunnen COM-onderdelen (maar niet buiten het proces COM-onderdelen) aanroepen zoals ADO (ActiveX-gegevensobjecten) die standaard zijn geregistreerd in het Windows-besturings systeem.

Apps kunnen wille keurige code produceren en uitvoeren. Het is toegestaan dat een app zaken doet zoals het starten van een opdracht shell of het uitvoeren van een Power shell-script. Hoewel wille keurige code en processen kunnen worden uitgevoerd vanuit een app, zijn uitvoer bare Program ma's en scripts nog steeds beperkt tot de bevoegdheden die aan de bovenliggende groep van toepassingen worden verleend. Een app kan bijvoorbeeld een uitvoerbaar bestand maken dat een uitgaande HTTP-oproep maakt, maar hetzelfde uitvoer bare bestand kan niet proberen het IP-adres van een virtuele machine te ontkoppelen van de NIC. Het maken van een uitgaande netwerk aanroep is toegestaan voor code met weinig bevoegdheden, maar voor het opnieuw configureren van de netwerk instellingen op een virtuele machine zijn beheerders bevoegdheden vereist.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Diagnostische logboeken en gebeurtenissen
Logboek informatie is een andere set gegevens waarmee sommige apps toegang proberen te krijgen. De typen logboek gegevens die beschikbaar zijn voor code die in App Service worden uitgevoerd, bevatten diagnostische en logboek gegevens die zijn gegenereerd door een app die ook eenvoudig toegankelijk is voor de app. 

Zo zijn W3C HTTP-logboeken die zijn gegenereerd door een actieve app beschikbaar op een logboekmap op de netwerk share locatie die voor de app is gemaakt, of beschikbaar in Blob Storage als een klant W3C-logboek registratie voor opslag heeft ingesteld. De laatste optie maakt het mogelijk grote hoeveel heden logboeken te verzamelen zonder het risico te overschrijden dat de opslag limieten voor bestanden zijn gekoppeld aan een netwerk share.

In een vergelijk bare Vein kunnen realtime diagnostische gegevens van .NET-apps ook worden geregistreerd met behulp van de .NET-tracering en diagnostische infra structuur, met opties om de tracerings informatie te schrijven naar de netwerk share van de app of naar een Blob-opslag locatie.

De diagnostische logboeken en tracering die niet beschikbaar zijn voor apps zijn Windows ETW-gebeurtenissen en algemene Windows-gebeurtenis Logboeken (bijvoorbeeld systeem-, toepassings-en beveiligings gebeurtenissen). Omdat ETW-tracerings gegevens mogelijk kunnen worden weer gegeven (met de juiste Acl's), worden lees-en schrijf toegang tot ETW-gebeurtenissen geblokkeerd. Ontwikkel aars kunnen merken dat API-aanroepen voor het lezen en schrijven van ETW-gebeurtenissen en veelvoorkomende gebeurtenis logboeken van Windows worden weer gegeven, maar dat komt omdat App Service ' Faking ' is de aanroepen zodat ze goed worden weer gegeven. De toepassings code heeft in werkelijkheid geen toegang tot deze gebeurtenis gegevens.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Toegang tot het REGI ster
Apps hebben alleen-lezen toegang tot veel (maar niet alle) van het REGI ster van de virtuele machine waarop ze worden uitgevoerd. In de praktijk betekent dit dat register sleutels die alleen-lezen toegang tot de lokale gebruikers groep toestaan, toegankelijk zijn voor apps. Een gedeelte van het REGI ster dat momenteel niet wordt ondersteund voor een lees-of schrijf toegang is de HKEY \_ huidige \_ gebruikers component.

Schrijf toegang tot het REGI ster is geblokkeerd, inclusief toegang tot register sleutels per gebruiker. Vanuit het perspectief van de app mag schrijf toegang tot het REGI ster nooit worden vertrouwd in de Azure-omgeving, omdat apps kunnen (en doen) worden gemigreerd naar verschillende virtuele machines. De enige permanente schrijf bare opslag die kan worden verzorgd door een app is de inhouds mappen structuur per app die is opgeslagen op de App Service UNC-shares. 

## <a name="remote-desktop-access"></a>Toegang tot extern bureau blad

App Service biedt geen extern bureau blad toegang tot de VM-exemplaren.

## <a name="more-information"></a>Meer informatie

[Azure app Service sandbox](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) : de meest actuele informatie over de uitvoerings omgeving van app service. Deze pagina wordt rechtstreeks onderhouden door het App Service Development team.