---
title: Netwerkfuncties
description: Meer informatie over de netwerk functies in Azure App Service en informatie over de functies die u nodig hebt voor beveiliging of andere functionaliteit.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 10/18/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 730f26039db0f5441563ac7bf5d6b0ab536cbcd2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593126"
---
# <a name="app-service-networking-features"></a>App Service-netwerk functies

U kunt toepassingen op verschillende manieren implementeren in Azure App Service. Apps die in App Service worden gehost, zijn standaard rechtstreeks toegankelijk via internet en kunnen alleen eind punten met internet-hosting bereiken. Voor veel toepassingen moet u echter het inkomende en uitgaande netwerk verkeer beheren. Er zijn verschillende functies in App Service die u helpen om aan deze behoeften te voldoen. De uitdaging is te weten welke functie u moet gebruiken om een bepaald probleem op te lossen. Dit artikel helpt u te bepalen welke functie u moet gebruiken, op basis van enkele voor beelden van gebruiks voorbeelden.

Er zijn twee hoofd implementatie typen voor Azure App Service: 
- De multi tenant-open bare service fungeert als host voor App Service plannen in de gratis, gedeelde, Basic-, Standard-, Premium-, PremiumV2-en PremiumV3-prijzen-Sku's. 
- De single-tenant App Service Environment (ASE) host een geïsoleerde SKU App Service-abonnementen rechtstreeks in uw Azure Virtual Network. 

De functies die u gebruikt, zijn afhankelijk van of u zich in de multi tenant-service of in een ASE bevindt. 

## <a name="multitenant-app-service-networking-features"></a>Multi tenant-App Service-netwerk functies 

Azure App Service is een gedistribueerd systeem. De rollen die binnenkomende HTTP-of HTTPS-aanvragen verwerken, worden *front-ends* genoemd. De rollen die als host fungeren voor de werk belasting van de klant, worden *werk nemers* genoemd. Alle functies in een App Service-implementatie bestaan in een multi tenant netwerk. Omdat er veel verschillende klanten zijn in dezelfde App Service Scale-eenheid, kunt u het App Service netwerk niet rechtstreeks verbinden met uw netwerk. 

In plaats van de netwerken te verbinden, hebt u functies nodig om de verschillende aspecten van toepassings communicatie te verwerken. De functies voor het afhandelen van aanvragen *voor* uw app kunnen niet worden gebruikt om problemen op te lossen wanneer u *vanuit* uw app aanroepen uitvoert. Op dezelfde manier kunnen de functies voor het oplossen van problemen met aanroepen vanuit uw app niet worden gebruikt om problemen met uw app op te lossen.  

| Binnenkomende functies | Uitgaande functies |
|---------------------|-------------------|
| App-toegewezen adres | Hybride verbindingen |
| Toegangs beperkingen | Gateway-vereiste VNet-integratie |
| Service-eindpunten | VNet-integratie |
| Privé-eindpunten ||

Behalve dat uitzonde ringen worden vermeld, kunt u al deze functies samen gebruiken. U kunt de functies combi neren om uw problemen op te lossen.

## <a name="use-cases-and-features"></a>Use cases en functies

Voor een wille keurig gebruik is het mogelijk dat u een aantal manieren hebt om het probleem op te lossen. Het kiezen van de beste functie gaat soms buiten het gebruiks voorbeeld. In de volgende inkomende use-cases wordt uitgelegd hoe u App Service-netwerk functies kunt gebruiken om problemen op te lossen met het beheer van verkeer naar uw app:
 
| Inkomende use-case | Functie |
|---------------------|-------------------|
| Ondersteuning voor op IP gebaseerde SSL-behoeften voor uw app | App-toegewezen adres |
| Ondersteuning voor niet-gedeeld toegewezen inkomend adres voor uw app | App-toegewezen adres |
| De toegang tot uw app beperken vanuit een reeks goed gedefinieerde adressen | Toegangs beperkingen |
| De toegang tot uw app beperken tegen resources in een virtueel netwerk | Service-eindpunten </br> ILB ASE </br> Privé-eindpunten |
| Uw app beschikbaar maken op een privé-IP in uw virtuele netwerk | ILB ASE </br> Privé-eindpunten </br> Particulier IP-adres voor inkomend verkeer op een Application Gateway-exemplaar met Service-eind punten |
| Uw app beveiligen met een Web Application Firewall (WAF) | Application Gateway en ILB ASE </br> Application Gateway met persoonlijke eind punten </br> Application Gateway met service-eindpunten </br> Azure front deur met toegangs beperkingen |
| Taak verdeling van verkeer naar uw apps in verschillende regio's | Azure front deur met toegangs beperkingen | 
| Taak verdeling van verkeer in dezelfde regio | [Application Gateway met service-eindpunten][appgwserviceendpoints] | 

In de volgende uitgaande use-cases wordt uitgelegd hoe u App Service-netwerk functies kunt gebruiken om de vereiste voor het oplossen van uitgaande toegang voor uw app:

| Uitgaande use-case | Functie |
|---------------------|-------------------|
| Toegang tot resources in een virtueel Azure-netwerk in dezelfde regio | VNet-integratie </br> ASE |
| Toegang tot resources in een virtueel Azure-netwerk in een andere regio | Gateway-vereiste VNet-integratie </br> ASE en de peering van virtuele netwerken |
| Toegang tot resources die zijn beveiligd met Service-eind punten | VNet-integratie </br> ASE |
| Toegang tot bronnen in een particulier netwerk dat niet is verbonden met Azure | Hybride verbindingen |
| Toegang tot resources in azure ExpressRoute-circuits | VNet-integratie </br> ASE | 
| Uitgaand verkeer van uw web-app beveiligen | VNet-integratie en netwerk beveiligings groepen </br> ASE | 
| Uitgaand verkeer van uw web-app door sturen | VNet-integratie en route tabellen </br> ASE | 


### <a name="default-networking-behavior"></a>Standaard netwerk gedrag

Azure App Service schaal eenheden ondersteunen veel klanten in elke implementatie. Met de gratis en gedeelde SKU worden workloads van klanten gehost op werk nemers met meerdere tenants. De werk belastingen van de host van de basis-en hogere abonnementen die zijn toegewezen aan slechts één App Service plan. Als u een Standard App Service-abonnement hebt, worden alle apps in dat plan uitgevoerd op dezelfde werk nemer. Als u de werk nemer uitbreidt, worden alle apps in dat App Service plan gerepliceerd naar een nieuwe werk nemer voor elk exemplaar in uw App Service plan. 

#### <a name="outbound-addresses"></a>Uitgaande adressen

De virtuele machines van de werk nemers zijn onderverdeeld in grote delen door de App Service plannen. De abonnementen gratis, gedeeld, basis, standaard en Premium gebruiken allemaal hetzelfde VM-type werk nemer. Het PremiumV2-abonnement maakt gebruik van een ander VM-type. PremiumV3 gebruikt nog een ander VM-type. Wanneer u de VM-serie wijzigt, krijgt u een andere set uitgaande adressen. Als u van standaard naar PremiumV2 schaalt, worden uw uitgaande adressen gewijzigd. Als u schaalt van PremiumV2 naar PremiumV3, worden uw uitgaande adressen gewijzigd. In sommige oudere schaal eenheden worden de inkomende en uitgaande adressen gewijzigd wanneer u van standaard naar PremiumV2 schaalt. 

Er zijn een aantal adressen die worden gebruikt voor uitgaande oproepen. De uitgaande adressen die door uw app worden gebruikt voor het maken van uitgaande oproepen, worden weer gegeven in de eigenschappen voor uw app. Deze adressen worden gedeeld door alle apps die worden uitgevoerd op dezelfde VM-familie van werk nemers in de App Service-implementatie. Als u alle adressen wilt zien die uw app kan gebruiken in een schaal eenheid, wordt de eigenschap met de naam weer geven `possibleOutboundAddresses` . 

![Scherm opname waarin de app-eigenschappen worden weer gegeven.](media/networking-features/app-properties.png)

App Service heeft een aantal eind punten die worden gebruikt om de service te beheren.  Deze adressen worden gepubliceerd in een afzonderlijk document en bevinden zich ook in het `AppServiceManagement` IP-service label. Het `AppServiceManagement` label wordt alleen gebruikt in app service omgevingen waarin u dergelijk verkeer moet toestaan. De App Service inkomende adressen worden bijgehouden in de `AppService` IP-service-tag. Er is geen IP-servicetag die de uitgaande adressen bevat die door App Service worden gebruikt. 

![Diagram dat App Service binnenkomend en uitgaand verkeer wordt weer gegeven.](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>App-toegewezen adres 

De functie voor het toegewezen adres van de app is een offshoot van de op IP gebaseerde SSL-mogelijkheid. U kunt deze openen door SSL in te stellen met uw app. U kunt deze functie gebruiken voor SSL-aanroepen op basis van IP. U kunt dit ook gebruiken om uw app een adres te geven dat alleen in het geval is. 

![Diagram dat het toegewezen adres van de app illustreert.](media/networking-features/app-assigned-address.png)

Wanneer u een door de app toegewezen adres gebruikt, loopt uw verkeer nog steeds door dezelfde front-end-rollen die alle binnenkomend verkeer naar de App Service Scale-eenheid verwerken. Het adres dat is toegewezen aan uw app, wordt alleen gebruikt door uw app. Gebruiks voorbeelden voor deze functie:

* Ondersteuning voor op IP gebaseerde SSL-behoeften voor uw app.
* Stel een toegewezen adres in voor uw app die niet wordt gedeeld.

Zie [een TLS/SSL-certificaat toevoegen in azure app service][appassignedaddress]voor meer informatie over het instellen van een adres in uw app. 

### <a name="access-restrictions"></a>Toegangs beperkingen 

Met toegangs beperkingen kunt u *inkomende* aanvragen filteren. De filter actie vindt plaats op de front-end-rollen die upstream zijn van de werk rollen waar uw apps worden uitgevoerd. Omdat de front-end-rollen upstream van de werk nemers zijn, kunt u de toegangs beperkingen zien als beveiliging op netwerk niveau voor uw apps. 

Met deze functie kunt u een lijst met regels voor toestaan en weigeren maken die in volg orde van prioriteit worden geëvalueerd. Het is vergelijkbaar met de functie netwerk beveiligings groep (NSG) in azure-netwerken. U kunt deze functie gebruiken in een ASE of in de multi tenant-service. Wanneer u deze gebruikt met een ILB-ASE of een persoonlijk eind punt, kunt u de toegang beperken via de blokken van een persoonlijk adres.
> [!NOTE]
> Maxi maal 512 toegangs beperkings regels kunnen per app worden geconfigureerd. 

![Diagram dat de toegangs beperkingen illustreert.](media/networking-features/access-restrictions.png)

#### <a name="ip-based-access-restriction-rules"></a>Regels voor toegangs beperking op basis van IP

De functie toegangs beperkingen op basis van IP helpt bij het beperken van de IP-adressen die kunnen worden gebruikt om uw app te bereiken. Zowel IPv4 als IPv6 worden ondersteund. Enkele gebruiks voorbeelden voor deze functie:
* Beperk de toegang tot uw app vanuit een reeks goed gedefinieerde adressen. 
* Beperk de toegang tot verkeer dat afkomstig is van een externe service voor taak verdeling of andere netwerk apparaten met bekende uitstaande IP-adressen. 

Zie [toegangs beperkingen configureren][iprestrictions]voor meer informatie over het inschakelen van deze functie.

> [!NOTE]
> Regels voor toegangs beperking op basis van IP verwerken alleen de adresbereiken van het virtuele netwerk wanneer uw app in een App Service Environment is. Als uw app zich in de multi tenant-service bevindt, moet u [service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md) gebruiken om het verkeer te beperken tot het selecteren van subnetten in uw virtuele netwerk.

#### <a name="access-restriction-rules-based-on-service-endpoints"></a>Toegangs beperkings regels op basis van service-eind punten 

Met Service-eind punten kunt u *inkomende* toegang tot uw app vergren delen zodat het bron adres moet afkomstig zijn uit een set subnetten die u selecteert. Deze functie werkt samen met IP-toegangs beperkingen. Service-eind punten zijn niet compatibel met externe fout opsporing. Als u fout opsporing op afstand wilt gebruiken met uw app, mag uw client zich niet in een subnet bevinden waarop Service-eind punten zijn ingeschakeld. Het proces voor het instellen van service-eind punten is vergelijkbaar met het proces voor het instellen van IP-toegangs beperkingen. U kunt een lijst met toegestane/geweigerde toegangs regels maken die open bare adressen en subnetten in uw virtuele netwerken bevatten. 

Enkele gebruiks voorbeelden voor deze functie:

* Stel een toepassings gateway in met uw app om inkomend verkeer naar uw app te vergren delen.
* Beperk de toegang tot uw app tot resources in uw virtuele netwerk. Deze resources kunnen Vm's, as of zelfs andere apps bevatten die gebruikmaken van VNet-integratie. 

![Diagram dat het gebruik van service-eind punten met Application Gateway illustreert.](media/networking-features/service-endpoints-appgw.png)

Zie [Azure app service-toegangs beperkingen][serviceendpoints]voor meer informatie over het configureren van service-eind punten met uw app.
#### <a name="access-restriction-rules-based-on-service-tags-preview"></a>Toegangs beperkings regels op basis van service tags (preview-versie)
[Azure-service Tags][servicetags] zijn duidelijk gedefinieerde sets met IP-adressen voor Azure-Services. Service Tags groep de IP-bereiken die worden gebruikt in verschillende Azure-Services en is vaak ook gericht op specifieke regio's. Hiermee kunt u *Inkomend* verkeer filteren vanuit specifieke Azure-Services. 

Voor een volledige lijst met tags en meer informatie gaat u naar de bovenstaande service label koppeling. Zie [toegangs beperkingen configureren][iprestrictions]voor meer informatie over het inschakelen van deze functie.
#### <a name="http-header-filtering-for-access-restriction-rules-preview"></a>Filteren van http-headers voor toegangs beperkings regels (preview-versie)
U kunt voor elke toegangs beperkings regel extra http-header filters toevoegen. Zo kunt u de inkomende aanvraag en het filter verder controleren op basis van de specifieke waarden van de http-header. Elke kop kan Maxi maal 8 waarden per regel bevatten. De volgende lijst met HTTP-headers wordt momenteel ondersteund: 
* X-doorgestuurd-voor
* X-doorgestuurd-host
* X-Azure-FDID
* X-FD-HealthProbe

Enkele use cases voor http-header filters zijn:
* De toegang tot verkeer van proxy servers die de hostnaam door sturen beperken
* Beperk de toegang tot een specifiek exemplaar van de Azure-front-deur met een servicetag regel en X-Azure-FDID-header beperking
### <a name="private-endpoint"></a>Privé-eindpunt

Privé-eind punt is een netwerk interface waarmee u privé en veilig verbinding maakt met uw web-app met behulp van een persoonlijke Azure-koppeling. Persoonlijk eind punt maakt gebruik van een privé-IP-adres van uw virtuele netwerk, waardoor de web-app effectief in uw virtuele netwerk wordt gezet. Deze functie is alleen voor *inkomende* stromen naar uw web-app.
Zie voor meer informatie [privé-eind punten gebruiken voor Azure-web-app][privateendpoints].

Enkele gebruiks voorbeelden voor deze functie:

* Beperk de toegang tot uw app tot resources in een virtueel netwerk. 
* Maak uw app beschikbaar op een privé-IP in uw virtuele netwerk. 
* Bescherm uw app met een WAF.

Persoonlijke eind punten voor komen gegevens exfiltration omdat het enige wat u binnen het persoonlijke eind punt kunt bereiken, is de app waarmee het is geconfigureerd. 
 
### <a name="hybrid-connections"></a>Hybride verbindingen

Met App Service Hybride verbindingen kunnen uw apps *uitgaande* aanroepen naar opgegeven TCP-eind punten maken. Het eind punt kan on-premises, in een virtueel netwerk of een wille keurige locatie zijn die uitgaand verkeer naar Azure toestaat op poort 443. Als u de functie wilt gebruiken, moet u een relay-agent met de naam Hybrid Connection Manager op een Windows Server 2012-of nieuwere host installeren. Hybrid Connection Manager moet Azure Relay kunnen bereiken op poort 443. U kunt Hybrid Connection Manager downloaden via de App Service Hybride verbindingen gebruikers interface in de portal. 

![Diagram waarin de Hybride verbindingen netwerk stroom wordt weer gegeven.](media/networking-features/hybrid-connections.png)

App Service Hybride verbindingen is gebaseerd op de mogelijkheid van Azure Relay Hybride verbindingen. App Service maakt gebruik van een speciale vorm van de functie die alleen ondersteuning biedt voor het maken van uitgaande oproepen vanuit uw app naar een TCP-host en-poort. Deze host en poort hoeven alleen te worden omgezet op de host waarop Hybrid Connection Manager is geïnstalleerd. 

Wanneer de app in App Service een DNS-zoek opdracht op de host en poort die is gedefinieerd in uw hybride verbinding, wordt het verkeer automatisch omgeleid om door de hybride verbinding en uit Hybrid Connection Manager te gaan. Zie [App Service hybride verbindingen][hybridconn]voor meer informatie.

Deze functie wordt vaak gebruikt voor het volgende:

* Toegang tot bronnen in particuliere netwerken die niet met Azure zijn verbonden met een VPN-of ExpressRoute.
* Biedt ondersteuning voor de migratie van on-premises apps naar App Service zonder dat ondersteunende data bases hoeven te worden verplaatst.  
* Bieden toegang met verbeterde beveiliging voor één host en poort per hybride verbinding. De meeste netwerk functies openen toegang tot een netwerk. Met Hybride verbindingen kunt u slechts één host en poort bereiken.
* Voor beelden van scenario's die niet worden gedekt door andere uitgaande connectiviteits methoden.
* Ontwikkel App Service op een manier waarmee de apps eenvoudig on-premises resources kunnen gebruiken. 

Omdat deze functie toegang tot on-premises bronnen mogelijk maakt zonder een binnenkomende firewall, is het populair bij ontwikkel aars. De andere uitgaande App Service-netwerk functies zijn gerelateerd aan Azure Virtual Network. Hybride verbindingen is niet afhankelijk van een virtueel netwerk. Het kan worden gebruikt voor een groter aantal netwerk behoeften. 

Houd er rekening mee dat App Service Hybride verbindingen niet weet wat u er precies op doet. U kunt deze gebruiken om toegang te krijgen tot een Data Base, een webservice of een wille keurige TCP-socket op een mainframe. De functie tunnelt in feite TCP-pakketten. 

Hybride verbindingen is populair voor ontwikkeling, maar het wordt ook gebruikt in productie toepassingen. Het is ideaal voor het verkrijgen van toegang tot een webservice of Data Base, maar is niet geschikt voor situaties waarbij veel verbindingen worden gemaakt. 

### <a name="gateway-required-vnet-integration"></a>Gateway-vereiste VNet-integratie 

Door de gateway vereiste App Service VNet-integratie kan uw app *uitgaande* aanvragen indienen in een virtueel Azure-netwerk. De functie werkt door verbinding te maken met de host waarop uw app wordt uitgevoerd op een Virtual Network gateway in het virtuele netwerk met behulp van een punt-naar-site-VPN. Wanneer u de functie configureert, haalt uw app een van de punt-naar-site-adressen die zijn toegewezen aan elk exemplaar. Met deze functie kunt u toegang krijgen tot resources in een klassieke of Azure Resource Manager virtuele netwerken in elke regio. 

![Diagram dat de gateway-vereiste VNet-integratie illustreert.](media/networking-features/gw-vnet-integration.png)

Met deze functie kunt u het probleem met het openen van bronnen in andere virtuele netwerken oplossen. Het kan zelfs worden gebruikt om verbinding te maken via een virtueel netwerk met andere virtuele netwerken of op een on-premises locatie. Het werkt niet met virtuele ExpressRoute-netwerken, maar is wel geschikt voor site-naar-site VPN-verbonden netwerken. Het is doorgaans niet geschikt voor het gebruik van deze functie vanuit een app in een App Service Environment (ASE), omdat de ASE zich al in uw virtuele netwerk bevindt. Gebruiks voorbeelden voor deze functie:

* Krijg toegang tot bronnen op privé Ip's in uw Azure Virtual Networks. 
* On-premises toegang tot bronnen als er sprake is van een site-naar-site-VPN. 
* Toegang krijgen tot bronnen in gekoppelde virtuele netwerken. 

Als deze functie is ingeschakeld, gebruikt uw app de DNS-server waarop het virtuele doelnet werken is geconfigureerd. Zie [app service VNet-integratie][vnetintegrationp2s]voor meer informatie over deze functie. 

### <a name="vnet-integration"></a>VNet-integratie

Gateway-vereiste VNet-integratie is handig, maar het probleem van het openen van resources tussen ExpressRoute wordt niet opgelost. Op het punt om ExpressRoute-verbindingen te bereiken, is het nodig dat apps aanroepen kunnen uitvoeren naar services die zijn beveiligd door service-eind punten. Een andere integratie mogelijkheid voor VNet kan aan deze vereisten voldoen. 

Met de nieuwe functie VNet-integratie kunt u de back-end van uw app in een subnet in een virtueel netwerk van Resource Manager plaatsen in dezelfde regio als uw app. Deze functie is niet beschikbaar vanuit een App Service Environment, dat zich al in een virtueel netwerk bevindt. Gebruiks voorbeelden voor deze functie:

* Toegang tot resources in virtuele netwerken van resource manager in dezelfde regio.
* Toegang tot bronnen die zijn beveiligd met Service-eind punten. 
* Toegang tot bronnen die toegankelijk zijn via ExpressRoute of VPN-verbindingen.
* Helpen al het uitgaande verkeer te beveiligen. 
* Geforceerde tunnel alle uitgaand verkeer. 

![Diagram dat VNet-integratie illustreert.](media/networking-features/vnet-integration.png)

Zie [app service VNet-integratie][vnetintegration]voor meer informatie.

### <a name="app-service-environment"></a>App Service-omgeving 

Een App Service Environment (ASE) is een implementatie met één Tenant van de Azure App Service die worden uitgevoerd in uw virtuele netwerk. Enkele voor beelden van deze functie:

* Toegang tot resources in uw virtuele netwerk.
* Toegang tot resources over ExpressRoute.
* Maak uw apps beschikbaar met een privé adres in uw virtuele netwerk. 
* Toegang tot resources via service-eind punten. 

Met een ASE hoeft u geen functies zoals VNet-integratie of service-eind punten te gebruiken omdat de ASE zich al in het virtuele netwerk bevindt. Als u toegang wilt krijgen tot resources zoals SQL of Azure Storage over service-eind punten, schakelt u service-eind punten in op het ASE-subnet. Als u toegang wilt krijgen tot resources in het virtuele netwerk, hoeft u geen aanvullende configuratie uit te voeren. Als u toegang wilt krijgen tot bronnen in ExpressRoute, bent u al in het virtuele netwerk en hoeft u niets te configureren op de ASE of de apps in de service. 

Omdat de apps in een ILB-ASE kunnen worden weer gegeven op een privé-IP-adres, kunt u eenvoudig WAF-apparaten toevoegen om alleen de apps die u wilt Internet beschikbaar te maken en de rest veilig te houden. Deze functie kan helpen de ontwikkeling van toepassingen met meerdere lagen eenvoudiger te maken. 

Sommige dingen zijn momenteel niet mogelijk van de multi tenant-service, maar zijn wel mogelijk via een ASE. Hier volgen enkele voorbeelden:

* Maak uw apps beschikbaar op een privé-IP-adres.
* Beveilig al het uitgaande verkeer met netwerk besturings elementen die geen deel uitmaken van uw app.
* Host uw apps in een service met één Tenant. 
* U kunt Maxi maal meer exemplaren schalen dan mogelijk zijn in de multi tenant-service. 
* Certificaten voor persoonlijke certificerings instanties laden voor gebruik door uw apps met door particuliere certificerings instanties beveiligde eind punten.
* Forceer TLS 1,1 voor alle apps die in het systeem worden gehost zonder dat dit op het niveau van de app kan worden uitgeschakeld. 
* Geef een toegewezen uitgaand adres op voor alle apps in uw ASE die niet met klanten worden gedeeld. 

![Diagram dat een ASE in een virtueel netwerk illustreert.](media/networking-features/app-service-environment.png)

De ASE biedt het beste verhaal rond geïsoleerde en toegewezen app-hosting, maar heeft wel enkele beheer problemen. Enkele zaken die u moet overwegen voordat u een operationele ASE gebruikt:
 
 * Een ASE wordt uitgevoerd in uw virtuele netwerk, maar heeft wel afhankelijkheden buiten het virtuele netwerk. Deze afhankelijkheden moeten zijn toegestaan. Zie [netwerk overwegingen voor een app service Environment][networkinfo]voor meer informatie.
 * Een ASE schaalt niet direct zoals de multi tenant-service. U moet anticiperen op schaal behoeften in plaats van opnieuw te schalen. 
 * Een ASE heeft een hogere kosten vooraf. Om optimaal gebruik te maken van uw ASE, moet u een groot aantal workloads in één ASE plaatsen, in plaats van deze te gebruiken voor kleine inspanningen.
 * De apps in een ASE kunnen de toegang tot sommige apps in de ASE niet selectief beperken en niet voor andere.
 * Een ASE bevindt zich in een subnet en alle netwerk regels zijn van toepassing op alle verkeer van en naar die ASE. Als u regels voor binnenkomend verkeer voor slechts één app wilt toewijzen, gebruikt u toegangs beperkingen. 

## <a name="combining-features"></a>Functies combi neren 

De functies die worden vermeld voor de multi tenant-service kunnen samen worden gebruikt voor het oplossen van meer uitgebreide use-cases. Twee van de meest voorkomende gebruiks voorbeelden worden hier beschreven, maar ze zijn slechts voor beelden. Als u wilt weten wat de verschillende functies doen, kunt u voldoen aan de vereisten van bijna al uw systeem architectuur.

### <a name="place-an-app-into-a-virtual-network"></a>Een app in een virtueel netwerk plaatsen

U vraagt zich misschien af hoe u een app in een virtueel netwerk plaatst. Als u uw app in een virtueel netwerk plaatst, bevinden de inkomende en uitgaande eind punten voor de app zich in het virtuele netwerk. Een ASE is de beste manier om dit probleem op te lossen. Maar u kunt aan de meeste vereisten in de multi tenant-service voldoen door functies te combi neren. U kunt bijvoorbeeld intranet toepassingen hosten met persoonlijke inkomende en uitgaande adressen:

* Een toepassings gateway maken met persoonlijke inkomende en uitgaande adressen.
* Het beveiligen van inkomend verkeer naar uw app met Service-eind punten. 
* Met de nieuwe VNet-integratie functie bevindt de back-end van uw app zich in het virtuele netwerk. 

Met deze implementatie stijl krijgt u geen toegewezen adres voor uitgaand verkeer naar Internet of de mogelijkheid om al het uitgaande verkeer van uw app te vergren delen. Hiermee krijgt u een groot deel van wat u alleen op een andere manier zou kunnen bereiken met een ASE. 

### <a name="create-multitier-applications"></a>Toepassingen met meerdere lagen maken

Een toepassing met meerdere lagen is een toepassing waarin de API-back-end-apps alleen kunnen worden geopend vanuit de front-end-laag. Er zijn twee manieren om een toepassing met meerdere lagen te maken. Beide beginnen met VNet-integratie om uw front-end-web-app te verbinden met een subnet in een virtueel netwerk. Hierdoor kan uw web-app aanroepen naar uw virtuele netwerk. Nadat de front-end-app is verbonden met het virtuele netwerk, moet u bepalen hoe u de toegang tot uw API-toepassing wilt vergren delen. U kunt:

* Host zowel de front-end als de API-app in dezelfde ILB ASE en maak de front-end-app beschikbaar op internet met behulp van een toepassings gateway.
* Host de front-end in de multi tenant-service en de back-end in een ILB-ASE.
* Host zowel de front-end als de API-app in de multi tenant-service.

Als u de front-end-en API-app host voor een toepassing met meerdere lagen, kunt u het volgende doen:

- Maak uw API-toepassing zichtbaar door gebruik te maken van privé-eind punten in uw virtuele netwerk:

  ![Diagram dat het gebruik van privé-eind punten in een app met twee lagen illustreert.](media/networking-features/multi-tier-app-private-endpoint.png)

- Gebruik service-eind punten om ervoor te zorgen dat inkomend verkeer naar uw API-app alleen afkomstig is van het subnet dat door de front-end web-app wordt gebruikt:

  ![Diagram dat het gebruik van service-eind punten illustreert om een app te beveiligen.](media/networking-features/multi-tier-app.png)

Hier volgen enkele aandachtspunten waarmee u kunt bepalen welke methode u wilt gebruiken:

* Wanneer u service-eind punten gebruikt, hoeft u alleen verkeer naar uw API-app te beveiligen naar het integratie subnet. Zo kunt u de API-app beveiligen, maar u kunt nog steeds gegevens exfiltration van uw front-end-app naar andere apps in de app service.
* Wanneer u privé-eind punten gebruikt, hebt u twee subnetten tijdens het afspelen, waardoor de complexiteit toevoegt. Het persoonlijke eind punt is ook een resource op het hoogste niveau en voegt beheer overhead toe. Het voor deel van het gebruik van privé-eind punten is dat u niet beschikt over de mogelijkheid om gegevens exfiltration te maken. 

Beide methoden werken met meerdere front-ends. Service-eind punten zijn op kleine schaal eenvoudiger te gebruiken omdat u eenvoudigweg service-eind punten voor de API-app op het subnet front-end-integratie inschakelt. Wanneer u meer front-end-apps toevoegt, moet u elke API-app aanpassen zodat deze service-eind punten met het integratie subnet bevat. Wanneer u privé-eind punten gebruikt, is er meer complexiteit, maar u hoeft niets te wijzigen op uw API-apps nadat u een persoonlijk eind punt hebt ingesteld. 

### <a name="line-of-business-applications"></a>Line-of-business-toepassingen

LOB-toepassingen (line-of-Business) zijn interne toepassingen die normaal gesp roken niet toegankelijk zijn voor toegang via internet. Deze toepassingen worden aangeroepen vanuit bedrijfs netwerken waar toegang strikt kan worden beheerd. Als u een ILB-ASE gebruikt, is het eenvoudig om uw line-of-business-toepassingen te hosten. Als u de multi tenant-service gebruikt, kunt u persoonlijke eind punten gebruiken of service-eind punten gebruiken in combi natie met een toepassings gateway. Er zijn twee redenen om een toepassings gateway met Service-eind punten te gebruiken in plaats van privé-eind punten te gebruiken:
* U hebt WAF-beveiliging nodig voor uw LOB-apps.
* U wilt taak verdeling naar meerdere exemplaren van uw LOB-apps.

Als geen van deze behoeften van toepassing is, kunt u beter gebruikmaken van privé-eind punten. Met persoonlijke eind punten die beschikbaar zijn in App Service, kunt u uw apps zichtbaar maken op particuliere adressen in uw virtuele netwerk. Het persoonlijke eind punt dat u in uw virtuele netwerk plaatst, kan worden bereikt via ExpressRoute en VPN-verbindingen. 

Als u persoonlijke eind punten configureert, worden uw apps beschikbaar op een privé adres, maar u moet DNS configureren om het adres van on-premises te bereiken. Als u deze configuratie wilt laten werken, moet u de Azure DNS privé zone die uw privé-eind punten bevat, door sturen naar uw on-premises DNS-servers. Azure DNS particuliere zones ondersteunen zone forwarding niet, maar u kunt wel zone forwarding ondersteunen met behulp van een DNS-server voor dat doel. Met de sjabloon voor [DNS-doorstuur](https://azure.microsoft.com/resources/templates/301-dns-forwarder/) servers kunt u uw Azure DNS privé zone gemakkelijker door sturen naar uw on-PREMISES DNS-servers.

## <a name="app-service-ports"></a>App Service poorten

Als u App Service scant, vindt u verschillende poorten die beschikbaar zijn voor binnenkomende verbindingen. Het is niet mogelijk om de toegang tot deze poorten in de multi tenant-service te blok keren of te beheren. Hier ziet u de lijst met beschik bare poorten:

| Gebruik | Poort of poorten |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  Beheer | 454, 455 |
|  FTP-FTPS    | 21, 990, 10001-10020 |
|  Visual Studio externe fout opsporing  |  4020, 4022, 4024 |
|  Web Deploy-Service | 8172 |
|  Gebruik van infra structuur | 7654, 1221 |

<!--Links-->
[appassignedaddress]: ./configure-ssl-certificate.md
[iprestrictions]: ./app-service-ip-restrictions.md
[serviceendpoints]: ./app-service-ip-restrictions.md
[hybridconn]: ./app-service-hybrid-connections.md
[vnetintegrationp2s]: ./web-sites-integrate-with-vnet.md
[vnetintegration]: ./web-sites-integrate-with-vnet.md
[networkinfo]: ./environment/network-info.md
[appgwserviceendpoints]: ./networking/app-gateway-with-service-endpoints.md
[privateendpoints]: ./networking/private-endpoint.md
[servicetags]: ../virtual-network/service-tags-overview.md