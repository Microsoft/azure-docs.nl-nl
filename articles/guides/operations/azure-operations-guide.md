---
title: Introductiehandleiding voor Azure-IT-uitvoerders | Microsoft Docs
description: Introductiehandleiding voor Azure-IT-uitvoerders
author: RicksterCDN
ms.author: rclaus
tags: azure-resource-manager
ms.service: azure
ms.topic: overview
ms.workload: infrastructure
ms.date: 08/24/2018
ms.openlocfilehash: 1eb0bf2c0e214c3e076895903176d15ee792c70c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101093041"
---
# <a name="get-started-for-azure-it-operators"></a>Introductie voor Azure-IT-uitvoerders

In deze handleiding worden kernconcepten geïntroduceerd die betrekking hebben op de implementatie en het beheer van een Microsoft Azure-infrastructuur. Als u geen ervaring hebt met cloud-computing of Azure zelf, kunt u met deze handleiding snel aan de slag met concepten, implementatie en beheerdetails. In veel secties van deze handleiding wordt een bewerking besproken, zoals het implementeren van een virtuele machine, en vervolgens een links naar gedetailleerde technische details gegeven.

## <a name="cloud-computing-overview"></a>Overzicht van cloud-computing

Cloud-computing is een modern alternatief voor het traditionele on-premises datacentrum. Leveranciers van openbare clouds bieden en beheren de hele computing-infrastructuur en de onderliggende beheersoftware. Deze leveranciers bieden een groot aantal cloudservices. Een cloudservice kan in dit geval een virtuele machine, een webserver of een in de cloud gehoste database-engine zijn. Als klant van een cloudprovider least u deze cloudservices op basis van uw behoeften. Op die manier kunt u de kapitaalkosten van hardware-onderhoud omzetten in een operationele uitgave. Een cloudservice biedt ook de volgende voordelen:

- Snelle implementatie van grote rekenomgevingen

- Snel toewijzingen opheffen van systemen die niet meer nodig zijn

- Eenvoudige implementatie van doorgaans complexe systemen, zoals load balancers

- Mogelijkheid om flexibele rekencapaciteit te bieden of te schalen wanneer dit nodig is

- Voordeligere rekenomgevingen

- Overal toegang met een webgebaseerde portal of programmatische automatisering

- Cloudservices die voldoen aan de meeste reken- en toepassingsbehoeften

Met on-premises infrastructuur hebt u volledige controle over de hardware en software die wordt geïmplementeerd. Dit heeft in het verleden geleid tot aankoopbeslissingen voor hardware die zich richten op het omhoog schalen. Een voorbeeld is het kopen van een server met meer kernen om te voldoen aan de prestatievereisten. Helaas kan deze infrastructuur buiten een tijdvenster van veel vraag te weinig worden gebruikt. Met Azure kunt u alleen de infrastructuur die u nodig hebt implementeren en die op elk gewenst moment omhoog of omlaag schalen. Dit leidt tot een focus op omhoog schalen via de implementatie van extra rekenknooppunten om te voldoen aan de prestatiebehoeften. Het omhoog schalen van cloudservices is rendabeler dan het omhoog schalen van dure hardware.

Microsoft heeft veel Azure-datacentrums overal ter wereld geïmplementeerd, en er zijn meer gepland. Daarnaast verhoogt Microsoft het aantal onafhankelijke clouds in regio's zoals China en Duitsland. Alleen de grootste wereldwijde ondernemingen kunnen datacentrums op deze manier implementeren, dus gebruik van Azure maakt het voor ondernemingen van elke omvang gemakkelijk om hun services dicht bij hun klanten te implementeren.

Voor kleine bedrijven biedt Azure een voordelig beginpunt, met de mogelijkheid om snel te schalen als de vraag naar rekenkracht toeneemt. Dit voorkomt een grote investering vooraf in infrastructuur en biedt flexibiliteit om systemen te bouwen en opnieuw te bouwen indien nodig. Het gebruik van cloud-computing past goed bij het 'scale-fast fail-fast'-model van startups.

Zie [Azure-regio's](https://azure.microsoft.com/regions/) voor meer informatie over de beschikbare Azure-regio's.

### <a name="cloud-computing-model"></a>Model voor cloud-computing

Azure maakt gebruik van een model voor cloud-computing op basis van de servicecategorieën die aan klanten worden aangeboden. De drie servicecategorieën zijn Infrastructure as a Service (IaaS), Platform as a Service (PaaS) en Software as a Service (SaaS). Leveranciers delen enige of alle verantwoordelijkheid voor onderdelen in de computing-stack in elk van deze categorieën. Laten we eens kijken naar de categorieën voor cloud-computing.
![Vergelijking van stack voor cloud-computing](./media/cloud-computing-comparison.png)

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: Infrastructure as a Service

Een IaaS-cloudprovider voert alle fysieke computing-resources en de vereiste software om computervirtualisatie mogelijk te maken uit en beheert ze. Een klant van deze service implementeert virtuele machines in deze gehoste datacentrums. Hoewel de virtuele machines zich in een extern datacentrum bevinden, heeft de IaaS-klant controle over de configuratie en het beheer van het besturingssysteem, waarbij de cloudprovider de onderliggende infrastructuur beheert.

Azure bevat verschillende IaaS-oplossingen, zoals virtuele machines, virtuele-machineschaalsets en de gerelateerde netwerkinfrastructuur. Virtuele machines zijn een populaire keuze voor het migreren van services naar Azure, omdat hiermee het migratie model "lift en verschuiving" is ingeschakeld. U kunt een virtuele machine configureren als de infrastructuur waarop uw services momenteel in uw datacentrum worden uitgevoerd, en vervolgens uw software naar de nieuwe virtuele machine migreren. Het kan zijn dat u de configuratie moet bijwerken, bijvoorbeeld met URL's naar andere services of opslag, maar u kunt op deze manier veel toepassingen migreren.

Virtuele-machineschaalsets zijn gebouwd op virtuele Azure-machines en bieden een eenvoudige manier om clusters van identieke virtuele machines te implementeren. Virtuele-machineschaalsets bieden ook ondersteuning voor automatisch schalen, zodat nieuwe virtuele machines wanneer nodig kunnen worden geïmplementeerd. Dit zorgt ervoor dat virtuele-machineschaalsets een ideaal platform zijn voor het hosten van microservice-rekenclusters op een hoger niveau, zoals Azure Service Fabric en Azure Container Service.

#### <a name="paas-platform-as-a-service"></a>PaaS: Platform as a Service

Met PaaS implementeert u uw toepassing in een omgeving die de leverancier van de cloudservice biedt. De leverancier doet al het infrastructuurbeheer, zodat u zich kunt concentreren op het ontwikkelen van toepassingen en gegevensbeheer.

Azure biedt verschillende oplossingen voor PaaS-computing, zoals de Web Apps-functie van Azure App Service en Azure Cloud Services (web- en werkrollen). In beide gevallen hebben ontwikkelaars meerdere manieren om hun toepassing te implementeren zonder dat ze kennis van de infrastructuur erachter nodig hebben. Ontwikkel aars hoeven geen virtuele machines (Vm's) te maken, gebruik Remote Desktop Protocol (RDP) om u aan te melden bij elke computer of om de toepassing te installeren. Ze hoeven alleen maar op een knop (of ernaast) te klikken, en de hulpprogramma's van Microsoft richten de VM's in, implementeren ze en installeren de toepassing op de VM's.

#### <a name="saas-software-as-a-service"></a>SaaS: Software as a Service

SaaS is software die centraal wordt gehost en beheerd. Het is doorgaans gebaseerd op een multi tenant-architectuur: er wordt één versie van de toepassing gebruikt voor alle klanten. De software kan worden uitgebreid naar meerdere instanties om voor de beste prestaties op alle locaties te zorgen. SaaS-software wordt doorgaans in licentie afgegeven via een maandelijks of jaarlijks abonnement. SaaS-softwareleveranciers zijn verantwoordelijk voor alle onderdelen van de software-stack. U beheert dus alleen de geleverde services.

Microsoft 365 is een goed voorbeeld van SaaS. Abonnees betalen een maandelijks of jaarlijks tarief en ze ontvangen Microsoft Exchange, Microsoft OneDrive en de rest van de Microsoft Office-suite als een service. Abonnees krijgen altijd de meest recente versie en de Exchange-server wordt voor u beheerd. Vergeleken met Office elk jaar installeren en upgraden, is dit gemakkelijker en voordeliger.

## <a name="azure-services"></a>Azure-services

Azure biedt veel services op het bijbehorende cloud-computing-platform. Deze services omvatten de volgende.

### <a name="compute-services"></a>Compute services

Services voor het hosten en uitvoeren van de werkbelasting van toepassingen:

- Virtuele Azure-machines: zowel Linux als Windows

- App Services (Web Apps, Mobile Apps, Logic Apps, API Apps en Function Apps)

- Azure Batch (voor grootschalig parallelle en batchrekentaken uitvoeren)

- Azure Service Fabric

- Azure Container Service

### <a name="data-services"></a>Data-services

Services voor het opslaan en beheren van gegevens:

- Azure Storage (omvat de services Azure Blob, Queue, Table en File)

- Azure SQL Database

- Azure Cosmos DB

- Microsoft Azure StorSimple

- Azure Cache voor Redis

### <a name="application-services"></a>Toepassingsservices

Services voor het maken en beheren van toepassingen:

- Azure Active Directory (Azure AD)

- Azure Service Bus voor het verbinden van gedistribueerde systemen

- Azure HDInsight voor het verwerken van big data

- Azure Logic Apps voor integratie- en indelingswerkstromen

- Azure Media Services

### <a name="network-services"></a>Netwerkservices

Services voor netwerken in Azure en tussen Azure en on-premises datacentrums:

- Azure Virtual Network

- Azure ExpressRoute

- Door Azure geleverd DNS

- Azure Traffic Manager

- Azure Content Delivery Network

Zie de [Azure-servicedocumentatie](/azure) voor gedetailleerde documentatie over Azure-services.

## <a name="azure-key-concepts"></a>Basisconcepten voor Azure

### <a name="datacenters-and-regions"></a>Datacentrums en regio's

Azure is een wereldwijd cloudplatform dat algemeen beschikbaar is in veel regio's over de hele wereld. Wanneer u een service, toepassing of virtuele machine inricht in Azure, wordt u gevraagd een regio te selecteren. De geselecteerde regio vertegenwoordigt een specifiek Data Center waarin uw toepassing wordt uitgevoerd. Zie [Azure-regio's](https://azure.microsoft.com/regions/) voor meer informatie.

Een van de voor delen van het gebruik van Azure is dat u uw toepassingen kunt implementeren in verschillende data centers over de hele wereld. De regio die u kiest, kan invloed hebben op de prestaties van uw toepassing. Het is optimaal om een regio te kiezen die dichter bij de meeste klanten ligt om de latentie in netwerk aanvragen te verminderen. U kunt ook een bepaalde regio selecteren om te voldoen aan de wettelijke vereisten voor het distribueren van uw app in bepaalde landen/regio's.

### <a name="azure-portal"></a>Azure Portal

De Azure-portal is een webtoepassing die kan worden gebruikt voor het maken, beheren en verwijderen van Azure-resources en -services. De Azure-portal bevindt zich op [portal.azure.com](https://portal.azure.com). Deze bevat een aanpasbaar dashboard en hulpprogramma's voor het beheer van Azure-resources. De portal biedt ook informatie over facturering en abonnementen. Zie [Overzicht van Microsoft Azure-portal](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) en [Azure-resources beheren via de portal](../../azure-resource-manager/management/manage-resources-portal.md) voor meer informatie.

### <a name="resources"></a>Resources

Azure-resources zijn afzonderlijke services voor computing, netwerken, gegevens of app-hosting die zijn geïmplementeerd in een Azure-abonnement. Enkele bekende resources zijn virtuele machines, opslagaccounts en SQL-databases. Azure-services bestaan vaak uit meerdere gerelateerde Azure-resources. Een virtuele machine van Azure kan bijvoorbeeld een VM, opslagaccount, netwerkadapter en openbaar IP-adres bevatten. Deze resources kunnen afzonderlijk of als groep worden gemaakt, beheerd en verwijderd. Azure-resources worden verderop in deze handleiding uitgebreid besproken.

### <a name="resource-groups"></a>Resourcegroepen

Een Azure-resourcegroep is een container met gerelateerde resources voor een Azure-oplossing. De resourcegroep kan alle resources voor de oplossing bevatten of enkel de resources die u als groep wilt beheren. Azure-resourcegroepen worden verderop in deze handleiding uitgebreid besproken.

### <a name="resource-manager-templates"></a>Resource Manager-sjablonen

Een Azure Resource Manager-sjabloon is een JSON-bestand (JavaScript Object Notation) dat één of meer resources definieert die worden geïmplementeerd naar een resourcegroep. Deze definieert ook de afhankelijkheden tussen de geïmplementeerde resources. Resource Manager-sjablonen worden verderop in deze handleiding uitgebreid besproken.

### <a name="automation"></a>Automation

U kunt resources maken, beheren en verwijderen met behulp van de Azure-portal, maar u kunt deze activiteiten ook automatiseren met PowerShell of opdrachtregelinterface (CLI) van Azure.

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell is een set modules die cmdlets biedt voor het beheren van Azure. U kunt de cmdlets gebruiken voor het maken, beheren en verwijderen van Azure-services. De cmdlets kunnen u helpen bij het behalen van consistente, herhaalbare en automatische implementaties. Zie [Azure PowerShell installeren en configureren](/powershell/azure/install-Az-ps) voor meer informatie.

#### <a name="azure-command-line-interface"></a>Azure-opdrachtregelinterface

De opdrachtregelinterface van Azure is een hulpprogramma dat u kunt gebruiken om Azure-resources te maken, te beheren en te verwijderen via de opdrachtregel. De Azure-CLI is beschikbaar voor Linux, Mac OS X en Windows. Zie [Azure-CLI installeren](/cli/azure/install-azure-cli) voor meer informatie en technische gegevens.

#### <a name="rest-apis"></a>REST-API’s

Azure is gebouwd op een set REST API's die de gebruikersinterface van de Azure-portal ondersteunen. De meeste van deze REST API's worden ook ondersteund om uw Azure-resources en -apps programmatisch in te richten en te beheren vanaf elk apparaat met een internetverbinding. Zie de [Azure REST SDK-naslaginformatie](/rest/api/index) voor meer informatie.

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

Beheerders hebben toegang tot Azure PowerShell en de Azure-CLI via een browserervaring met de naam Azure Cloud Shell. Deze interactieve interface biedt Linux- en Windows-beheerders een flexibele manier om de gewenste opdrachtregelinterface, Bash of PowerShell, te gebruiken. Azure Cloud Shell kunnen worden geopend via de portal, als een zelfstandige webinterface op [shell.Azure.com](https://shell.azure.com)of vanuit een aantal andere toegangs punten. Zie [Overzicht van Azure Cloud Shell](../../cloud-shell/overview.md) voor meer informatie.

## <a name="azure-subscriptions"></a>Azure-abonnementen

Een abonnement is een logische groepering van Azure-services die is gekoppeld aan een Azure-account. Eén Azure-account kan meerdere abonnementen bevatten. Facturering voor Azure-services wordt uitgevoerd per abonnement. Azure-abonnementen hebben een accountbeheerder, die volledige controle heeft over het abonnement, en een servicebeheerder, die de controle heeft over alle services in het abonnement. Zie [Azure-abonnementsbeheerders toevoegen of wijzigen](../../cost-management-billing/manage/add-change-subscription-administrator.md) voor meer informatie over klassieke abonnementsbeheerders. Naast beheerders kunnen individuele accounts gedetailleerde controle krijgen over Azure-resources met behulp van [Azure RBAC (op rollen gebaseerd toegangsbeheer)](../../role-based-access-control/overview.md).

### <a name="select-and-enable-an-azure-subscription"></a>Een Azure-abonnement selecteren en inschakelen

Om Azure-services te kunnen gebruiken, hebt u een abonnement nodig. Er zijn verschillende typen abonnementen beschikbaar.

**Gratis accounts**: De link om u aan te melden voor een gratis account, vindt u op de [Azure-website](https://azure.microsoft.com/). Hiermee krijgt u een tegoed van 30 dagen voor het uitproberen van verschillende resources in Azure. Als u uw tegoed overschrijdt, wordt uw toegang tot het account tijdelijk ingetrokken. Wanneer de proefversie is afgelopen, worden uw services buiten gebruik gesteld en werken ze niet meer. U kunt op elk moment upgraden naar een abonnement met betalen per gebruik.

**MSDN-abonnementen**: Als u een MSDN-abonnement hebt, ontvangt u elke maand een specifiek Azure-tegoed. Als u bijvoorbeeld een abonnement voor Microsoft Visual Studio Enterprise met MSDN hebt, krijgt u \$ 150 per maand aan Azure-tegoed.

Als u het tegoed overschrijdt, wordt uw service uitgeschakeld totdat de volgende maand wordt gestart. U kunt de bestedingslimiet uitschakelen en een creditcard toevoegen die wordt gebruikt voor eventuele extra kosten. Sommige van deze kosten worden niet in rekening gebracht op MSDN-accounts. U betaalt bijvoorbeeld de Linux-prijs voor VM's waarop Windows Server wordt uitgevoerd en er worden geen extra kosten in rekening gebracht voor Microsoft-servers, zoals Microsoft SQL Server. Dit maakt MSDN-accounts ideaal voor ontwikkelings- en testscenario's.

**BizSpark-accounts**: Het Microsoft BizSpark-programma biedt veel voordelen voor startups. Een van deze voordelen is toegang tot alle Microsoft-software voor ontwikkelings- en testomgevingen voor maximaal vijf MSDN-accounts. U krijgt $ 150 aan Azure-tegoed voor elk van deze vijf MSDN-accounts en u betaalt gereduceerde tarieven voor verschillende Azure-services, zoals Virtual Machines.

**Betalen per gebruik**: Met dit abonnement betaalt u voor wat u gebruikt door een creditcard of debitcard aan het account te koppelen. Als u een organisatie bent, kunt u ook worden goedgekeurd voor facturering.

**Enterprise-overeenkomsten**: Met een enterprise-overeenkomst kunt u voor een jaar een bepaald aantal services in Azure gebruiken en betaalt u dat bedrag van tevoren. Het overeengekomen aantal wordt gedurende het hele jaar verbruikt. Als u het overeengekomen aantal overschrijdt, kunt u de overschrijding achteraf betalen. Afhankelijk van het overeengekomen aantal, krijgt u korting op de services in Azure.

### <a name="grant-administrative-access-to-an-azure-subscription"></a>Beheerders toegang verlenen tot een Azure-abonnement

Azure RBAC heeft verschillende ingebouwde rollen die u kunt gebruiken om machtigingen toe te wijzen. Als u een gebruiker een beheerder van een Azure-abonnement wilt maken, wijst u de rol [Eigenaar](../../role-based-access-control/built-in-roles.md#owner) aan deze persoon toe voor het abonnementsbereik. De rol van eigenaar geeft de gebruiker volledige toegang tot alle resources in het abonnement, waaronder het recht om toegang aan anderen te delegeren.

Zie [Azure-rollen toewijzen met behulp van de Azure Portal](../../role-based-access-control/role-assignments-portal.md)voor meer informatie.

### <a name="view-billing-information-in-the-azure-portal"></a>Factureringsgegevens weergeven in de Azure-portal

Een belangrijk onderdeel van Azure is de mogelijkheid om factureringsgegevens te bekijken. De Azure-portal biedt gedetailleerd inzicht in de factureringsgegevens van Azure.

Voor meer informatie bekijkt u [Uw Azure-factuur en de dagelijkse gebruiksgegevens downloaden](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md).

### <a name="get-billing-information-from-billing-apis"></a>Factureringsgegevens ophalen vanuit facturering-API's

U kunt factureringsgegevens niet alleen in de portal bekijken. U kunt de factureringsgegevens ook openen met behulp van een script of programma via de Azure Billing REST API's van Azure:

- U kunt de Azure Usage API toepassen om uw gebruiksgegevens op te halen. U kunt de gegevens voor facturering en gebruik verfijnen door verwante Azure-resources te taggen. U kunt bijvoorbeeld elke resource in een resourcegroep taggen met een afdelingsnaam of projectnaam en vervolgens de kosten voor die ene tag volgen.

- U kunt het overzicht van de [Azure-verbruiks-API](../../cost-management-billing/manage/consumption-api-overview.md) gebruiken om alle beschik bare bronnen samen met de meta gegevens weer te geven. Zie [overzicht van Azure](/rest/api/cost-management/retail-prices/azure-retail-prices)-verkoop prijzen voor meer informatie over prijzen.

### <a name="forecast-cost-with-the-pricing-calculator"></a>Prognose van kosten maken met de prijscalculator

De prijzen voor elke service in Azure zijn verschillend. Veel Azure-services bieden de categorieën Basic, Standard en Premium. Normaal gesproken heeft elke categorie verschillende prijs- en prestatieniveaus. Met de [online prijscalculator](https://azure.microsoft.com/pricing/calculator) kunt u een schatting van de prijs maken. De calculator kan de geschatte kosten voor een enkele resource en voor een groep resources berekenen.

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager is een mechanisme voor het implementeren, beheren en ordenen van Azure-resources. Met Resource Manager kunt u veel afzonderlijke resources samenvoegen in een resourcegroep.

Resource Manager bevat ook implementatiemogelijkheden die aanpasbare implementatie en configuratie van gerelateerde resources mogelijk maken. U kunt met behulp van Resource Manager bijvoorbeeld een toepassing die bestaat uit meerdere virtuele machines, een load balancer en een database in Azure SQL Database als één eenheid implementeren. U kunt deze implementaties ontwikkelen met behulp van een Resource Manager-sjabloon.

Resource Manager biedt diverse voordelen:

- U kunt alle resources voor uw oplossing implementeren, beheren en bewaken als groep, in plaats van deze resources afzonderlijk te verwerken.

- U kunt gedurende de ontwikkelingscyclus uw oplossing herhaaldelijk implementeren en erop vertrouwen dat uw resources consistent worden geïmplementeerd.

- U kunt uw infrastructuur beheren via declaratieve sjablonen in plaats van scripts.

- U kunt de afhankelijkheden tussen resources zo definiëren dat deze in de juiste volgorde worden geïmplementeerd.

- U kunt toegangsbeheer toepassen op alle services in de resourcegroep omdat Azure RBAC is geïntegreerd in het beheerplatform.

- U kunt tags toepassen op resources om alle resources in uw abonnement op een logische manier te organiseren.

- U kunt de facturering van uw organisatie verduidelijken door de kosten voor een groep resources met dezelfde tag te bekijken.

### <a name="tips-for-creating-resource-groups"></a>Tips voor het maken van resourcegroepen

Houd rekening met de volgende tips wanneer u beslissingen neemt over uw resource groepen:

- Alle services binnen een resourcegroep horen dezelfde levenscyclus te hebben.

- U kunt een resource slechts aan één groep tegelijk toewijzen.

- U kunt een resource op elk gewenst moment toevoegen aan of verwijderen uit een resourcegroep. Elke resource moet tot een resourcegroep behoren. Dus als u een resource uit een groep verwijdert, moet u deze resource aan een andere groep toevoegen.

- U kunt de meeste typen resources op elk gewenst moment naar een andere resourcegroep verplaatsen.

- De resources in een resourcegroep kunnen zich in verschillende regio's bevinden.

- U kunt een resourcegroep gebruiken om de toegang voor de resources in de groep te beheren.

### <a name="building-resource-manager-templates"></a>Resource Manager-sjablonen maken

Resource Manager-sjablonen definiëren de resources en resourceconfiguraties declaratief die in één resourcegroep worden geïmplementeerd. U kunt Resource Manager-sjablonen gebruiken om complexe implementaties uit te voeren zonder veel scripts of handmatige configuratie. Nadat u een sjabloon hebt ontwikkeld, kunt u deze meerdere keren implementeren: elke keer met een identieke uitkomst.

Een Resource Manager-sjabloon bestaat uit vier gedeeltes:

- **Parameters**: Dit zijn invoeren voor de implementatie. Parameterwaarden kunnen via een handmatig of een geautomatiseerd proces worden verkregen. Een parameter kan bijvoorbeeld een beheerdersnaam en -wachtwoord voor een Windows-VM zijn. De parameter waarden worden gebruikt tijdens de implementatie wanneer ze worden opgegeven.

- **Variabelen**: Deze worden gebruikt om waarden op te slaan die tijdens de hele implementatie worden gebruikt. In tegenstelling tot parameters wordt de waarde van een variabele niet opgegeven tijdens de implementatie. In plaats daarvan wordt de code vastgelegd of dynamisch gegenereerd.

- **Resources**: In dit gedeelte van de sjabloon worden de te implementeren resources gedefinieerd, zoals virtuele machines, opslagaccounts en virtuele netwerken.

- **Uitvoer**: Nadat een implementatie is voltooid, kan Resource Manager gegevens retourneren zoals dynamisch gegenereerde verbindingsreeksen.

De volgende mechanismen zijn beschikbaar voor automatisering van de implementatie:

- **Functies**: U kunt verschillende functies gebruiken in Resource Manager-sjablonen. Dit zijn bewerkingen als het converteren van een tekenreeks naar kleine letters, het implementeren van meerdere exemplaren van een gedefinieerde resource en het dynamisch retourneren van de doelresourcegroep. Met Resource Manager-functies kunt u dynamische implementaties bouwen.

- **Bron afhankelijkheden**: wanneer u meerdere resources implementeert, hebben sommige resources afhankelijk van andere bronnen. Als u de implementatie wilt vereenvoudigen, kunt u een afhankelijkheidsdeclaratie gebruiken zodat afhankelijke resources vóór de andere worden geïmplementeerd.

- **Sjabloon koppelen**: U kunt vanuit de ene Resource Manager-sjabloon een koppeling naar een andere sjabloon maken. Op deze manier kan de implementatie worden uitgevouwen in een set doelgerichte sjablonen.

U kunt Resource Manager-sjablonen in elke teksteditor samenstellen. De Azure SDK voor Visual Studio bevat echter hulpmiddelen die u kunnen helpen. Met Visual Studio kunt u via een wizard resources toevoegen aan de sjabloon en vervolgens de sjabloon rechtstreeks vanuit Visual Studio implementeren en fouten hierin opsporen. Zie [Azure Resource Manager-sjablonen maken](../../azure-resource-manager/templates/template-syntax.md) voor meer informatie.

Ten slotte kunt u bestaande resourcegroepen converteren naar een herbruikbare sjabloon vanuit de Azure-portal. Dit kan handig zijn als u een implementeerbare sjabloon van een bestaande resourcegroep wilt maken of als u alleen de onderliggende JSON wilt onderzoeken. Als u een resource groep wilt exporteren, selecteert u de knop **Automation-script** uit de instellingen van de resource groep.

## <a name="security-of-azure-resources-azure-rbac"></a>Beveiliging van Azure-resources (Azure RBAC)

U kunt operationele toegang verlenen aan gebruikersaccounts voor een opgegeven bereik: abonnement, resourcegroep of afzonderlijke resource. Dit betekent dat u een set resources kunt implementeren in een resourcegroep, zoals een virtuele machine en alle gerelateerde resources, en machtigingen kunt verlenen aan een specifieke gebruiker of groep. Deze benadering beperkt de toegang tot alleen de resources die bij de doelresourcegroep horen. U kunt ook toegang verlenen tot één resource, zoals een virtuele machine of een virtueel netwerk.

Als u toegang wilt verlenen, wijst u een rol toe aan de gebruiker of gebruikersgroep. Er zijn veel vooraf gedefinieerde rollen. U kunt ook uw eigen aangepaste rollen definiëren.

Hier volgen enkele voor beelden van [ingebouwde rollen in azure](../../role-based-access-control/built-in-roles.md):

- **Eigenaar**: Een gebruiker met deze rol kan alles beheren, inclusief toegang.

- **Lezer**: een gebruiker met deze rol kan resources van alle typen (met uitzonde ring van geheimen) lezen, maar kan geen wijzigingen aanbrengen.

- **Inzender voor virtuele machines**: een gebruiker met deze rol kan virtuele machines beheren, maar kan niet het virtuele netwerk beheren waarmee ze zijn verbonden of het opslag account waarin het VHD-bestand zich bevindt.

- **Inzender voor SQL-databases**: Een gebruiker met deze rol kan SQL-databases beheren, maar niet het bijbehorende beveiligingsbeleid.

- **SQL-beveiligingsbeheerder**: Een gebruiker met deze rol kan het beveiligingsbeleid van SQL-servers en -databases beheren.

- **Inzender voor opslagaccounts**: Een gebruiker met deze rol kan opslagaccounts beheren, maar niet de toegang tot de opslagaccounts.

Zie [Azure-rollen toewijzen met behulp van de Azure Portal](../../role-based-access-control/role-assignments-portal.md)voor meer informatie.

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

Azure Virtual Machines is een van de centrale IaaS-services in Azure. Azure Virtual Machines ondersteunt de implementatie van virtuele Windows- en Linux-machines in een Microsoft Azure-datacentrum. Met Azure Virtual Machines hebt u volledige controle over de configuratie van virtuele machines en bent u verantwoordelijk voor de installatie, de configuratie en het onderhoud van de software.

Wanneer u een virtuele machine van Azure implementeert, kunt u een installatie kopie selecteren in azure Marketplace of kunt u een eigen gegeneraliseerde installatie kopie opgeven. Deze installatiekopie wordt gebruikt om het besturingssysteem en de initiële configuratie toe te passen. Tijdens de implementatie worden bepaalde configuratie-instellingen door Resource Manager verwerkt, zoals het toewijzen van de computernaam, de beheerdersreferenties en de netwerkconfiguratie. U kunt extensies van virtuele Azure-machines gebruiken om configuraties, zoals software-installatie, antivirusconfiguratie en bewakingsoplossingen, verder te automatiseren.

U kunt virtuele machines in veel verschillende grootten maken. De grootte van de virtuele machine bepaalt de resourcetoewijzing, zoals de verwerking, het geheugen en de opslagcapaciteit. In sommige gevallen zijn specifieke functies, zoals netwerkadapters met RDMA-functionaliteit en SSD-schijven, alleen beschikbaar voor bepaalde VM-grootten. Zie ' grootten voor virtuele machines in azure ' voor [Windows](../../virtual-machines/sizes.md) en [Linux](../../virtual-machines/sizes.md)voor een volledige lijst met VM-grootten en-mogelijkheden.

### <a name="use-cases"></a>Gebruiksvoorbeelden

Omdat virtuele machines van Azure volledige controle over de configuratie bieden, zijn ze ideaal voor een groot aantal serverwerkbelastingen die niet in een PaaS-model passen. Serverwerkbelastingen zoals databaseservers (SQL Server, Oracle of MongoDB), Windows Server Active Directory, Microsoft SharePoint en veel meer kunnen worden uitgevoerd op het Microsoft Azure-platform. Indien gewenst kunt u dergelijke werkbelastingen van een on-premises datacentrum naar een of meer Azure-regio's verplaatsen, zonder dat er veel herconfiguratie nodig is.

### <a name="deployment-of-virtual-machines"></a>Implementatie van virtuele machines

U kunt virtuele Azure-machines implementeren met behulp van de Azure-portal, met behulp van automatisering met de Azure PowerShell-module, of met behulp van automatisering met de platformoverschrijdende CLI.

#### <a name="portal"></a>Portal

Voor het implementeren van een virtuele machine met behulp van de Azure-portal zijn alleen een actief Azure-abonnement en toegang tot een webbrowser vereist. U kunt veel verschillende installatiekopieën van een besturingssysteem selecteren met verschillende configuraties. Alle opslag- en netwerkvereisten worden tijdens de implementatie geconfigureerd. Zie "een virtuele machine maken in de Azure Portal" voor [Windows](../../virtual-machines/windows/quick-create-portal.md) en [Linux](../../virtual-machines/linux/quick-create-portal.md)voor meer informatie.

U kunt vanuit de Azure-portal niet alleen een virtuele machine implementeren, maar ook een Azure Resource Manager-sjabloon. Hiermee worden alle resources geïmplementeerd en geconfigureerd, zoals gedefinieerd in de sjabloon. Zie [Resources implementeren met Resource Manager-sjablonen en de Azure-portal](../../azure-resource-manager/templates/deploy-portal.md) voor meer informatie.

#### <a name="powershell"></a>PowerShell

Als u een virtuele machine van Azure implementeert met behulp van PowerShell, kunt u de implementatie van alle gerelateerde resources voor virtuele machines, waaronder opslag en netwerken, volledig automatiseren. Zie [Een virtuele Windows-machine maken met behulp van Resource Manager en PowerShell](../../virtual-machines/windows/quick-create-powershell.md) voor meer informatie.

Naast het individueel implementeren van computing-resources in Azure, kunt u de Azure PowerShell-module ook gebruiken om een Azure Resource Manager-sjabloon te implementeren. Raadpleeg voor meer informatie [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md).

#### <a name="command-line-interface-cli"></a>Opdrachtregelinterface (CLI)

Net als de PowerShell-module biedt de Azure-opdrachtregelinterface automatisering van implementatie. De interface kan worden gebruikt op Windows-, OS X- en Linux-systemen. Wanneer u de Azure CLI VM-opdracht voor **snel maken** gebruikt, worden alle gerelateerde resources van de virtuele machine (inclusief opslag en netwerken) en de virtuele machine zelf geïmplementeerd. Zie [Create a Linux VM in Azure by using the CLI](../../virtual-machines/linux/quick-create-cli.md) (Een virtuele Linux-machine maken in Azure met behulp van de CLI) voor meer informatie.

Op dezelfde manier kunt u de Azure-CLI gebruiken om een Azure Resource Manager-sjabloon te implementeren. Zie [Resources implementeren met Resource Manager-sjablonen en Azure CLI](../../azure-resource-manager/templates/deploy-cli.md) voor meer informatie.

### <a name="access-and-security-for-virtual-machines"></a>Toegang en beveiliging voor virtuele machines

Voor toegang tot een virtuele machine via internet moet de bijbehorende netwerkinterface, of load balancer indien van toepassing, worden geconfigureerd met een openbaar IP-adres. Het openbare IP-adres bevat een DNS-naam die wordt omgezet naar de virtuele machine of load balancer. Zie [IP-adressen in Azure](../../virtual-network/public-ip-addresses.md) voor meer informatie.

U beheert de toegang tot de virtuele machine via het openbare IP-adres met behulp van een netwerkbeveiligingsgroep-resource (NSG). Een NSG fungeert als een firewall en staat verkeer toe of weigert het op de netwerkinterface of het subnet via een reeks gedefinieerde poorten. Als u bijvoorbeeld een extern bureaublad-sessie wilt maken met een virtuele machine van Azure, moet u de NSG zo configureren dat inkomend verkeer op poort 3389 wordt toegestaan. Zie [Poorten openen voor een virtuele machine in Azure met behulp van de Azure-portal](../../virtual-machines/windows/nsg-quickstart-portal.md) voor meer informatie.

Ten slotte moet u, zoals bij het beheer van elk computersysteem, beveiliging leveren voor een virtuele Azure-machine op het besturingssysteem met behulp van beveiligingsreferenties en softwarefirewalls.

## <a name="azure-storage"></a>Azure Storage

Azure Storage is een door Microsoft beheerde service die robuuste, schaalbare en redundante opslag biedt. U kunt een Azure-opslagaccount als resource toevoegen aan elke resourcegroep met behulp van een resource-implementatiemethode. Azure bevat vier opslagtypen: Blob-opslag, bestandsopslag, tabelopslag en wachtrijopslag. Bij het implementeren van een opslagaccount zijn twee accounttypen beschikbaar: algemeen gebruik en blob-opslag. Een algemeen opslagaccount biedt toegang tot alle vier de opslagtypen. Blob-opslagaccounts zijn vergelijkbaar met algemene accounts, maar bevatten speciale blobs met dynamische en statische toegangslagen. Zie [Azure Blob Storage](../../storage/blobs/storage-blob-storage-tiers.md) voor meer informatie over blob-opslag.

Azure-opslagaccounts kunnen worden geconfigureerd met verschillende redundantieniveaus:

- **Lokaal redundante opslag** biedt hoge beschikbaarheid door ervoor te zorgen dat er synchroon drie kopieën van alle gegevens worden gemaakt voordat een schrijfbewerking wordt voltooid. Deze kopieën worden in één faciliteit in één regio opgeslagen. De replica's bevinden zich in afzonderlijke foutdomeinen en upgradedomeinen. Dit betekent dat de gegevens ook beschikbaar zijn als een opslag knooppunt dat uw gegevens bevat, mislukt of offline wordt gezet om te worden bijgewerkt.

- **Geografisch redundante opslag** maakt drie synchrone kopieën van de gegevens in de primaire regio voor hoge beschikbaarheid en maakt vervolgens asynchroon drie replica's in een gekoppelde regio voor herstel na noodgevallen.

- **Geografisch redundante opslag met leestoegang** is geografisch redundante opslag plus de mogelijkheid om de gegevens in de secundaire regio te lezen. Deze mogelijkheid maakt het geschikt voor gedeeltelijk herstel na noodgevallen. Als er een probleem is met de primaire regio, kunt u uw toepassing wijzigen zodat deze alleen-lezen toegang heeft tot de gekoppelde regio.

### <a name="use-cases"></a>Gebruiksvoorbeelden

Elk opslagtype is geschikt voor een andere situatie.

#### <a name="blob-storage"></a>Blob Storage

Het woord *blob* is een acroniem voor *binary large object*. Blobs zijn niet gestructureerd, zoals de bestanden die u op uw computer opslaat. In Blob Storage kan elk type tekst of binaire gegevens, zoals een document, mediabestand of toepassingsinstallatieprogramma, worden opgeslagen. U kunt Blob Storage zien als een vorm van objectopslag. Azure Blob-opslag bevat ook gegevensschijven van virtuele Azure-machines.

Azure Storage ondersteunt drie soorten blobs:

- **Blok-blobs** worden gebruikt voor het opslaan van normale bestanden met een grootte van maximaal 195 GB (4 MB × 50.000 blokken). De primaire toepassing van blok-blobs is het opslaan van bestanden die van begin tot eind worden gelezen, zoals mediabestanden of afbeeldingsbestanden voor websites. Ze worden blok-blobs genoemd omdat bestanden die groter zijn dan 64 MB als kleine blokken moeten worden geüpload. Deze blokken worden vervolgens geconsolideerd (of doorgevoerd) in de uiteindelijke blob.

- **Pagina-blobs** worden gebruikt voor het opslaan van bestanden voor willekeurige toegang tot maximaal 1 TB in grootte. Pagina-blobs worden voornamelijk gebruikt als de back-upopslag voor VHD's die duurzame schijven bieden voor virtuele Azure-machines, de IaaS-computing-service in Azure. Ze worden pagina-blobs genoemd omdat ze willekeurige lees- en schrijftoegang tot pagina's van 512 bytes bieden.

- **Toevoeg-blobs** bestaan uit blokken zoals blok-blobs, maar zijn geoptimaliseerd voor toevoegbewerkingen. Deze worden vaak gebruikt voor het vastleggen van gegevens van een of meerdere bronnen in dezelfde blob. U kunt bijvoorbeeld al uw traceer logboek registratie naar dezelfde toevoeg-BLOB schrijven voor een toepassing die wordt uitgevoerd op meerdere Vm's. Eén toevoeg-blob kan maximaal 195 GB zijn.

Zie [Aan de slag met Azure Blob Storage met .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md) voor meer informatie.

#### <a name="file-storage"></a>File Storage

Azure File Storage is een service die bestandsshares in de cloud aanbiedt met behulp van het standaard Server Message Block-protocol (SMB). De service ondersteunt zowel SMB 2.1 als SMB 3.0. Met Azure File Storage kunt u oudere die afhankelijk zijn van bestandsshares snel en zonder kostbare regeneraties naar Azure migreren. Toepassingen die worden uitgevoerd op virtuele machines of in cloudservices van Azure, of op on-premises clients, kunnen een bestandsshare in de cloud koppelen. Dit is vergelijkbaar met de manier waarop een bureaubladtoepassing een gewone SMB-share koppelt. Daarna kan een willekeurig aantal toepassingsonderdelen de File Storage-share tegelijkertijd koppelen en gebruiken.

Omdat een File Storage-share een standaard SMB-bestandsshare is, hebben toepassingen die in Azure worden uitgevoerd toegang tot de gegevens in de share via I/O API's van het bestandssysteem. Dat betekent dat ontwikkelaars bestaande code en vaardigheden kunnen gebruiken voor het migreren van bestaande toepassingen. IT-professionals kunnen PowerShell-cmdlets gebruiken om File Storage-shares te maken, te koppelen en te beheren als onderdeel van het beheer van Azure-toepassingen.

Zie [Aan de slag met Azure File Storage in Windows](../../storage/files/storage-how-to-use-files-windows.md) of [Azure File Storage gebruiken met Linux](../../storage/files/storage-how-to-use-files-linux.md) voor meer informatie.

#### <a name="table-storage"></a>Table Storage

Azure Table Storage is een service waarmee gestructureerde NoSQL-gegevens worden opgeslagen in de cloud. Table Storage is een sleutel-/kenmerkopslag met een schemaloos ontwerp. Omdat Table Storage schemaloos is, kunt u uw gegevens eenvoudig aanpassen naarmate de behoeften van uw toepassing veranderen. De toegang tot gegevens verloopt snel en kostenefficiënt voor alle soorten toepassingen. Table Storage is doorgaans aanzienlijk goedkoper dan traditionele SQL voor vergelijkbare gegevensvolumes.

U kunt Table Storage gebruiken voor het opslaan van flexibele gegevenssets, zoals gebruikersgegevens voor webtoepassingen, adresboeken, apparaatgegevens en alle overige typen metagegevens die uw service nodig heeft. U kunt een willekeurig aantal entiteiten in een tabel opslaan. Een opslagaccount kan een onbeperkt aantal tabellen bevatten, tot de capaciteitslimiet van het opslagaccount is bereikt.

Zie [Aan de slag met Azure Table Storage](../../cosmos-db/tutorial-develop-table-dotnet.md) voor meer informatie.

#### <a name="queue-storage"></a>Queue Storage

Azure Queue Storage biedt uitwisseling van berichten tussen toepassingsonderdelen in de cloud. Bij het ontwerpen van schaalbare toepassingen worden toepassingsonderdelen vaak ontkoppeld, zodat ze onafhankelijk van elkaar kunnen worden geschaald. Queue Storage biedt asynchrone uitwisseling van berichten voor communicatie tussen toepassingsonderdelen, of deze nu worden uitgevoerd in de cloud, op een desktopcomputer, op een on-premises server of op een mobiel apparaat. Queue Storage biedt ook ondersteuning voor het beheren van asynchrone taken en het samenstellen van proceswerkstromen.

Zie [Aan de slag met Azure Queue Storage](../../storage/queues/storage-dotnet-how-to-use-queues.md) voor meer informatie.

### <a name="deploying-a-storage-account"></a>Een opslagaccount implementeren

Er zijn verschillende opties voor het implementeren van een opslagaccount.

#### <a name="portal"></a>Portal

Voor het implementeren van een opslagaccount met behulp van de Azure-portal zijn alleen een actief Azure-abonnement en toegang tot een webbrowser vereist. U kunt een nieuw opslagaccount implementeren in een nieuwe of bestaande resourcegroep. Nadat u het opslag account hebt gemaakt, kunt u een BLOB of bestands share maken met behulp van de portal. U kunt tabel- en wachtrij-opslagentiteiten programmatisch maken. Zie [Een opslagaccount maken](../../storage/common/storage-account-create.md) voor meer informatie.

U kunt vanuit de Azure-portal niet alleen een opslagaccount implementeren, maar ook een Azure Resource Manager-sjabloon. Hiermee worden alle resources geïmplementeerd en geconfigureerd zoals gedefinieerd in de sjabloon, inclusief eventuele opslagaccounts. Zie [Resources implementeren met Resource Manager-sjablonen en de Azure-portal](../../azure-resource-manager/templates/deploy-portal.md) voor meer informatie.

#### <a name="powershell"></a>PowerShell

Als u een Azure-opslagaccount implementeert met behulp van PowerShell, kunt u de implementatie van het opslagaccount volledig automatiseren. Zie [Azure PowerShell gebruiken met Azure Storage](/powershell/module/az.storage/) voor meer informatie.

Naast het individueel implementeren van resources in Azure, kunt u de Azure PowerShell-module ook gebruiken om een Azure Resource Manager-sjabloon te implementeren. Raadpleeg voor meer informatie [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md).

#### <a name="command-line-interface-cli"></a>Opdrachtregelinterface (CLI)

Net als de PowerShell-module biedt de Azure-opdrachtregelinterface automatisering van implementatie. De interface kan worden gebruikt op Windows-, OS X- en Linux-systemen. U kunt de opdracht **storage account create** in de Azure-CLI gebruiken om een opslagaccount te maken. Zie [De Azure CLI gebruiken met Azure Storage](../../storage/blobs/storage-quickstart-blobs-cli.md) voor meer informatie.

Op dezelfde manier kunt u de Azure-CLI gebruiken om een Azure Resource Manager-sjabloon te implementeren. Zie [Resources implementeren met Resource Manager-sjablonen en Azure CLI](../../azure-resource-manager/templates/deploy-cli.md) voor meer informatie.

### <a name="access-and-security-for-azure-storage"></a>Toegang en beveiliging voor Azure Storage

Azure Storage kan op verschillende manieren worden geopend, waaronder via de Azure-portal, tijdens het maken en uitvoeren van een VM, en via de Storage-clientbibliotheken.

#### <a name="virtual-machine-disks"></a>Virtuele-machineschijven

Wanneer u een virtuele machine implementeert, moet u ook een opslag account maken om de schijf met het besturings systeem van de virtuele machine en eventuele extra gegevens schijven op te slaan. U kunt een bestaand opslagaccount selecteren of een nieuwe maken. Omdat de maximale grootte van een blob 1024 GB is, heeft één VM-schijf een maximale grootte van 1023 GB. Als u een grotere gegevensschijf wilt configureren, kunt u meerdere gegevensschijven naar de virtuele machine sturen en deze samenvoegen als één logische schijf. Zie "Azure-schijven beheren" voor [Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) en [Linux](../../virtual-machines/linux/tutorial-manage-disks.md)voor meer informatie.

#### <a name="storage-tools"></a>Hulpprogramma's voor Storage

Azure-opslagaccounts kunnen via veel verschillende opslagverkenners, zoals Visual Studio Cloud Explorer, worden geopend. Met deze hulpprogramma's kunt u door opslagaccounts en gegevens bladeren. Zie [Azure Storage client tools](../../storage/common/storage-explorers.md) (Clienthulpprogramma's voor Azure Storage) voor meer informatie en een lijst met beschikbare opslagverkenners.

#### <a name="storage-api"></a>Opslag-API

Storage-resources zijn toegankelijk voor elke taal waarvoor HTTP/HTTPS-aanvragen mogelijk zijn. Daarnaast biedt Azure Storage programmeringsbibliotheken voor verschillende veelgebruikte talen. Deze bibliotheken vereenvoudigen het werken met Azure Storage door het verwerken van gegevens zoals synchrone en asynchrone aanroepen, batch verwerking van bewerkingen, uitzonderings beheer en automatische nieuwe pogingen. Zie [REST API-naslaginformatie voor Azure Storage-services](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference) voor meer informatie.

#### <a name="storage-access-keys"></a>Toegangssleutels voor opslag

Elk opslagaccount heeft twee verificatiesleutels, een primaire en een secundaire. Beide kunnen worden gebruikt voor opslagtoegangsbewerkingen. Deze opslagsleutels worden gebruikt voor het beveiligen van een opslagaccount en zijn vereist om programmatisch toegang te krijgen tot gegevens. Twee sleutels maken het incidenteel wisselen van sleutel mogelijk om de beveiliging te verbeteren. Het is essentieel om de sleutels veilig te bewaren, aangezien iemand met deze sleutels en de accountnaam onbeperkte toegang heeft tot alle gegevens in het opslagaccount.

#### <a name="shared-access-signatures"></a>Shared Access Signatures

Als u beheerde toegang tot uw opslagresources moet toestaan voor uw gebruikers, kunt u een Shared Access Signature maken. Een Shared Access Signature is een token die kan worden toegevoegd aan een URL waarmee gedelegeerde toegang tot een opslagresource mogelijk wordt gemaakt. Iedereen die over het token beschikt, heeft toegang tot de bron waar deze naar verwijst met de machtigingen die ze opgeeft, voor de periode dat deze geldig is. Zie [Handtekeningen voor gedeelde toegang gebruiken](../../storage/common/storage-sas-overview.md) voor meer informatie.

## <a name="azure-virtual-network"></a>Azure Virtual Network

Virtuele netwerken zijn nodig om communicatie tussen virtuele machines te ondersteunen. U kunt subnetten, aangepaste IP-adressen, DNS-instellingen, beveiligingsfilters en taakverdeling definiëren. Azure biedt ondersteuning voor verschillende toepassingen: cloudnetwerken en hybride virtuele netwerken.

### <a name="cloud-only-virtual-networks"></a>Virtuele netwerken in de cloud

Een virtueel Azure-netwerk is standaard alleen toegankelijk voor resources die zijn opgeslagen in Azure. Resources die aan hetzelfde virtuele netwerk zijn gekoppeld, kunnen met elkaar communiceren. U kunt netwerkinterfaces en load balancers van een virtuele machine aan een openbaar IP-adres koppelen om de virtuele machine toegankelijk te maken via internet. U kunt de toegang tot de openbare resources beveiligen met behulp van een netwerkbeveiligingsgroep.

![Virtueel Azure-netwerk voor een webtoepassing met twee lagen](/azure/load-balancer/media/load-balancer-internal-overview/ic744147.png)

### <a name="hybrid-virtual-networks"></a>Hybride virtuele netwerken

U kunt een on-premises netwerk met een virtueel Azure-netwerk koppelen met behulp van ExpressRoute of een site-to-site-VPN-verbinding. In deze configuratie is het virtuele Azure-netwerk in feite een cloudextensie van uw on-premises netwerk.

Omdat het virtuele Azure-netwerk is gekoppeld aan uw on-premises netwerk, moeten cross-premises virtuele netwerken een uniek deel uitmaken van de adresruimte die uw organisatie gebruikt. Op dezelfde manier als aan verschillende bedrijfslocaties een specifiek IP-subnet toegewezen, wordt Azure een andere locatie wanneer u uw netwerk uitbreidt.
Er zijn verschillende opties voor het implementeren van een virtueel netwerk.

- [Portal](../..//virtual-network/quick-create-portal.md)

- [PowerShell](../../virtual-network/quick-create-powershell.md)

- [Opdrachtregelinterface (CLI)](../../virtual-network/quick-create-cli.md)

- Azure Resource Manager-sjablonen

> **Wanneer gebruiken**: Wanneer u met VM's werkt in Azure, werkt u met virtuele netwerken. Dit maakt het mogelijk om uw VM's te segmenteren in openbare en privésubnetten, vergelijkbaar met on-premises datacentrums.
>
> **Aan de slag**: Voor het implementeren van een virtueel Azure-netwerk met behulp van de Azure-portal zijn alleen een actief Azure-abonnement en toegang tot een webbrowser vereist. U kunt een nieuw virtueel netwerk in een nieuwe of bestaande resourcegroep implementeren. Wanneer u een nieuwe virtuele machine maakt vanuit de portal, kunt u een bestaand virtueel netwerk selecteren of een nieuwe maken. Aan de slag met [een virtueel netwerk maken met de Azure-portal](../../virtual-network/quick-create-portal.md).

### <a name="access-and-security-for-virtual-networks"></a>Toegang en beveiliging voor virtuele netwerken

U kunt virtuele Azure-netwerken beveiligen met behulp van een netwerkbeveiligingsgroep (NSG). Een NSG bevat een lijst met ACL-regels (Access Control List, toegangsbeheerlijst) waarmee netwerkverkeer voor uw VM-exemplaren in een virtueel netwerk wordt toegestaan of geweigerd. NSG's kunnen worden gekoppeld aan subnetten of afzonderlijke VM-exemplaren in dat subnet. Als u een NSG aan een subnet koppelt, zijn de ACL-regels van toepassing op alle VM-exemplaren in dat subnet. Daarnaast kunt u het verkeer naar een afzonderlijke virtuele machine verder beperken door een NSG rechtstreeks aan die VM te koppelen. Raadpleeg [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](../../virtual-network/network-security-groups-overview.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Een Windows-VM maken](../../virtual-machines/windows/quick-create-portal.md)
- [Een Linux-VM maken](../../virtual-machines/linux/quick-create-portal.md)