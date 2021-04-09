---
title: Toepassings patronen op Vm's SQL Server | Microsoft Docs
description: In dit artikel worden toepassings patronen behandeld voor SQL Server op Azure Virtual Machines. Het biedt oplossingen architecten en ontwikkel aars een basis voor een goede toepassings architectuur en-ontwerp.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 41863c8d-f3a3-4584-ad86-b95094365e05
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mathoma
ms.openlocfilehash: e877f775c6a7bc83ce6c4a559c9f74b606c952fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359486"
---
# <a name="application-patterns-and-development-strategies-for-sql-server-on-azure-virtual-machines"></a>Toepassings patronen en ontwikkelings strategieën voor het SQL Server op Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="summary"></a>Samenvatting:
Het is belang rijk om te bepalen welk toepassings patroon of patronen moet worden gebruikt voor uw SQL Server-toepassingen in een Azure-omgeving. Dit is een goede goed keuring vereist van de manier waarop SQL Server en elk infrastructuur onderdeel van Azure samen werken. Met SQL Server in azure Infrastructure Services kunt u uw bestaande SQL Server toepassingen die op Windows Server zijn gebouwd, eenvoudig migreren, onderhouden en controleren op virtuele machines (Vm's) in Azure.

Het doel van dit artikel is om Solution Architects en ontwikkel aars een basis te bieden voor een goede toepassings architectuur en-ontwerp, die ze kunnen volgen bij het migreren van bestaande toepassingen naar Azure en het ontwikkelen van nieuwe toepassingen in Azure.

Voor elk toepassings patroon vindt u een on-premises scenario, de respectieve Cloud oplossing en de bijbehorende technische aanbevelingen. Daarnaast worden in het artikel Azure-specifieke ontwikkelings strategieën beschreven, zodat u uw toepassingen op de juiste manier kunt ontwerpen. Vanwege de vele mogelijke toepassings patronen is het raadzaam dat architecten en ontwikkel aars het meest geschikte patroon kiezen voor hun toepassingen en gebruikers.

**Technische mede** werkers: Luis Carlos Vargas haring, madhan Arumugam Ramakrishnan

**Technische revisoren:** Corey Sanders, getekend McDaniel, Narayan Annamalai, NIR Mashkowski, Sanjay Mishra, Silvano Coriani, Stefan Schackow, Tim Hickey, Tim Wieman, Xin Jin

## <a name="introduction"></a>Introductie
U kunt veel typen n-tier-toepassingen ontwikkelen door de onderdelen van de verschillende toepassings lagen op verschillende computers en in afzonderlijke onderdelen te scheiden. U kunt bijvoorbeeld de onderdelen van de client toepassing en de bedrijfs regels op de ene computer, de front-end-weblaag en de onderdelen van de Data Access-laag op een andere computer en een back-enddatabase op een andere computer plaatsen. Dit type structurering helpt bij het isoleren van elke laag van elkaar. Als u wijzigt waar gegevens vandaan komen, hoeft u de client of de webtoepassing niet te wijzigen, maar alleen de onderdelen van de Data Access-laag.

Een typische toepassing met *n-tier* omvat de presentatielaag, de zakelijke laag en de gegevenslaag:

| Laag | Beschrijving |
| --- | --- |
| **Stijl** |De *presentatielaag (* weblaag, front-end-laag) is de laag waarin gebruikers communiceren met een toepassing. |
| **Business** |De *bedrijfslaag* (middelste laag) is de laag die de presentatielaag en de gegevenslaag gebruiken om met elkaar te communiceren en bevat de kern functionaliteit van het systeem. |
| **Gegevens** |De *gegevenslaag* is in principe de server waarop de gegevens van een toepassing worden opgeslagen (bijvoorbeeld een server met SQL Server). |

Toepassings lagen beschrijven de logische groeperingen van de functionaliteit en onderdelen in een toepassing; terwijl lagen beschrijven de fysieke distributie van de functionaliteit en onderdelen op afzonderlijke fysieke servers, computers, netwerken of externe locaties. De lagen van een toepassing kunnen zich op dezelfde fysieke computer (dezelfde laag) bevinden of kunnen worden gedistribueerd via afzonderlijke computers (n-tier), en de onderdelen in elke laag communiceren met onderdelen in andere lagen via goed gedefinieerde interfaces. U kunt de term laag beschouwen als verwijzingen naar fysieke distributie patronen zoals twee lagen, drie lagen en n-tier. Een **toepassings patroon** met twee lagen bevat meerdere toepassings lagen: toepassings server en database server. De directe communicatie tussen de toepassings server en de database server plaatsvindt. De toepassings server bevat zowel de weblaag als de onderdelen van de zakelijke laag. In de **3-tier-toepassings patroon** zijn er drie toepassings lagen: webserver, toepassings server, die de Business Logic-laag en/of de Data Access-onderdelen voor bedrijfs lagen en de database server bevat. De communicatie tussen de webserver en de database server vindt plaats via de toepassings server. Zie de [hand leiding voor micro soft-toepassings architectuur](/previous-versions/msp-n-p/ff650706(v=pandp.10))voor meer informatie over toepassings lagen en-lagen.

Voordat u begint met het lezen van dit artikel, moet u kennis hebben van de basis concepten van SQL Server en Azure. Zie [SQL Server Books Online](/sql/sql-server/) [SQL Server op Azure virtual machines](sql-server-on-azure-vm-iaas-what-is-overview.md) en [Azure.com](https://azure.microsoft.com/)voor meer informatie.

In dit artikel worden verschillende toepassings patronen beschreven die geschikt kunnen zijn voor uw eenvoudige toepassingen, evenals de zeer complexe zakelijke toepassingen. Voordat u elk patroon bijwerkt, raden we u aan om vertrouwd te raken met de beschik bare Services voor gegevens opslag in azure, zoals [Azure Storage](../../../storage/common/storage-introduction.md), [Azure SQL database](../../database/sql-database-paas-overview.md)en [SQL Server in een virtuele machine van Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Als u de beste ontwerp beslissingen voor uw toepassingen wilt maken, moet u weten wanneer u de gegevens opslag service duidelijk wilt gebruiken.

### <a name="choose-sql-server-on-azure-virtual-machines-when"></a>Kies SQL Server in azure Virtual Machines wanneer:

* U hebt controle over SQL Server en Windows. Dit kan bijvoorbeeld de SQL Server versie, speciale hotfixes, prestatie configuratie, enzovoort zijn.
* U hebt een volledige compatibiliteit met SQL Server nodig en u wilt bestaande toepassingen verplaatsen naar Azure.
* U wilt gebruikmaken van de mogelijkheden van de Azure-omgeving, maar Azure SQL Database biedt geen ondersteuning voor alle functies die uw toepassing nodig heeft. Dit kan de volgende gebieden omvatten:
  
  * **Data Base-grootte**: op het moment dat dit artikel is bijgewerkt, ondersteunt SQL database een Data Base van Maxi maal 1 TB aan gegevens. Als uw toepassing meer dan 1 TB aan gegevens vereist en u geen aangepaste sharding-oplossingen wilt implementeren, is het raadzaam om SQL Server te gebruiken in een virtuele Azure-machine. Zie voor de meest recente informatie [Uitschalen Azure SQL database](/previous-versions/azure/dn495641(v=azure.100)), [op DTU gebaseerd inkoop model](../../database/service-tiers-dtu.md)en [inkoop model op basis van vCore](../../database/service-tiers-vcore.md)(preview).
  * **HIPAA-naleving**: gezondheids zorg klanten en Independent Software Vendors (isv's) kunnen kiezen [SQL Server op Azure virtual machines](sql-server-on-azure-vm-iaas-what-is-overview.md) in plaats van [Azure SQL database](../../database/sql-database-paas-overview.md) , omdat SQL Server op Azure virtual machines valt onder overeenkomst van HIPAA business partner (BAA). Zie het [vertrouwens centrum van Microsoft Azure: compatibiliteit](https://azure.microsoft.com/support/trust-center/compliance/)voor meer informatie over naleving.
  * **Functies op exemplaar niveau**: op dit moment biedt SQL database geen ondersteuning voor functies die zich buiten de data base bevinden (zoals gekoppelde servers, Agent taken, FileStream, Service Broker, enzovoort). Zie [Azure SQL database richt lijnen en beperkingen](/previous-versions/azure/ff394102(v=azure.100))voor meer informatie.

## <a name="1-tier-simple-single-virtual-machine"></a>1-laag (eenvoudig): één virtuele machine
In dit toepassings patroon implementeert u uw SQL Server-toepassing en-Data Base op een zelfstandige virtuele machine in Azure. Dezelfde virtuele machine bevat uw client/webtoepassing, zakelijke onderdelen, Gegevenstoegangslaag en de database server. De code van de presentatie, het bedrijf en de gegevens toegang worden logisch gescheiden, maar bevinden zich fysiek op een computer met één server. De meeste klanten beginnen met dit toepassings patroon en worden vervolgens uitgebreid door meer webrollen of virtuele machines aan hun systeem toe te voegen.

Dit toepassings patroon is nuttig wanneer:

* U wilt een eenvoudige migratie naar het Azure-platform uitvoeren om te evalueren of het platform de vereisten van uw toepassing beantwoordt of niet.
* U wilt alle toepassings lagen die worden gehost op dezelfde virtuele machine in hetzelfde Azure-Data Center houden om de latentie tussen lagen te verminderen.
* U wilt snel ontwikkel-en test omgevingen inrichten voor korte Peri Oden.
* U wilt stress testen uitvoeren op verschillende workload-niveaus, maar tegelijkertijd geen eigen fysieke machines meer wilt maken en onderhouden.

In het volgende diagram ziet u een eenvoudig on-premises scenario en hoe u de Cloud oplossing kunt implementeren in één virtuele machine in Azure.

![patroon met één laag toepassing](./media/application-patterns-development-strategies/IC728008.png)

De implementatie van de zakelijke laag (bedrijfs logica en onderdelen voor gegevens toegang) op dezelfde fysieke laag als de presentatielaag kan de prestaties van de toepassing maximaliseren, tenzij u een afzonderlijke laag moet gebruiken vanwege schaal baarheid of beveiligings problemen.

Aangezien dit een zeer gangbaar patroon is om te beginnen met, kunt u het volgende artikel over het migreren van uw gegevens naar uw SQL Server VM: [een Data Base migreren naar SQL Server op een virtuele machine van Azure](migrate-to-vm-from-sql-server.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>3 lagen (eenvoudig): meerdere virtuele machines
In dit toepassings patroon implementeert u een toepassing met drie lagen in azure door elke toepassingslaag in een andere virtuele machine te plaatsen. Dit biedt een flexibele omgeving voor het eenvoudig omhoog schalen en schalen van scenario's. Als één virtuele machine uw client/webtoepassing bevat, wordt de andere host gehost op uw zakelijke onderdelen en de andere een host van de database server.

Dit toepassings patroon is nuttig wanneer:

* U wilt een migratie uitvoeren van complexe database toepassingen naar Azure Virtual Machines.
* U wilt dat verschillende toepassings lagen worden gehost in verschillende regio's. U kunt bijvoorbeeld gedeelde data bases hebben die zijn geïmplementeerd in meerdere regio's voor rapportage doeleinden.
* U wilt bedrijfs toepassingen van on-premises gevirtualiseerde platformen verplaatsen naar Azure Virtual Machines. Zie [Wat is een bedrijfs toepassing](/previous-versions/visualstudio/aa267045(v=vs.60))? voor een gedetailleerde bespreking van bedrijfs toepassingen.
* U wilt snel ontwikkel-en test omgevingen inrichten voor korte Peri Oden.
* U wilt stress testen uitvoeren op verschillende workload-niveaus, maar tegelijkertijd geen eigen fysieke machines meer wilt maken en onderhouden.

In het volgende diagram ziet u hoe u een eenvoudige toepassing met drie lagen in azure kunt plaatsen door elke toepassingslaag in een andere virtuele machine te plaatsen.

![3-tier-toepassings patroon](./media/application-patterns-development-strategies/IC728009.png)

In dit toepassings patroon bevindt zich slechts één virtuele machine in elke laag. Als u meerdere virtuele machines in azure hebt, raden we u aan om een virtueel netwerk in te stellen. Er wordt door [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) een vertrouwde beveiligings grens gemaakt en ook kunnen vm's communiceren tussen het privé-IP-adres. Bovendien moet u er altijd voor zorgen dat alle Internet verbindingen alleen naar de presentatielaag verwijzen. Wanneer u dit toepassings patroon volgt, beheert u de regels voor de netwerk beveiligings groep om de toegang te beheren. Zie [externe toegang tot uw virtuele machine toestaan met behulp van de Azure Portal](../../../virtual-machines/windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie.

In het diagram kunnen Internet protocollen TCP, UDP, HTTP of HTTPS zijn.

> [!NOTE]
> Het instellen van een virtueel netwerk in Azure is gratis. Er worden echter kosten in rekening gebracht voor de VPN-gateway die verbinding maakt met on-premises. Deze kosten zijn gebaseerd op de hoeveelheid tijd die de verbinding heeft ingericht en beschikbaar is.
> 
> 

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>2 lagen en 3 lagen met uitschalen van de presentatielaag.
In dit toepassings patroon implementeert u een database toepassing met twee lagen of drie lagen voor Azure Virtual Machines door elke toepassingslaag in een andere virtuele machine te plaatsen. Daarnaast kunt u de presentatielaag uitschalen vanwege een groter aantal binnenkomende client aanvragen.

Dit toepassings patroon is nuttig wanneer:

* U wilt bedrijfs toepassingen van on-premises gevirtualiseerde platformen verplaatsen naar Azure Virtual Machines.
* U wilt de presentatielaag uitschalen vanwege een groter volume aan binnenkomende client aanvragen.
* U wilt snel ontwikkel-en test omgevingen inrichten voor korte Peri Oden.
* U wilt stress testen uitvoeren op verschillende workload-niveaus, maar tegelijkertijd geen eigen fysieke machines meer wilt maken en onderhouden.
* U wilt eigenaar zijn van een infrastructuur omgeving die op aanvraag omhoog of omlaag kan worden geschaald.

In het volgende diagram ziet u hoe u de toepassings lagen in meerdere virtuele machines in azure kunt plaatsen door de presentatielaag uit te breiden vanwege een groter aantal binnenkomende client aanvragen. Zoals in het diagram wordt weer gegeven, is Azure Load Balancer verantwoordelijk voor het distribueren van verkeer over meerdere virtuele machines en het bepalen van de webserver waarmee verbinding moet worden gemaakt. Meerdere exemplaren van de webservers achter een load balancer zorgt voor een hoge Beschik baarheid van de presentatielaag.

![Toepassings patroon-uitschalen van de presentatielaag-laag](./media/application-patterns-development-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Aanbevolen procedures voor tweetiere, 3-tier of n-tier-patronen met meerdere virtuele machines in één laag
Het is raadzaam om de virtuele machines die deel uitmaken van dezelfde laag te plaatsen in dezelfde Cloud service en in dezelfde beschikbaarheidsset. Plaats bijvoorbeeld een set webservers in **CloudService1** en **AvailabilitySet1** en een set database servers in **CloudService2** en **AvailabilitySet2**. Met een beschikbaarheidsset in azure kunt u de knoop punten met hoge Beschik baarheid plaatsen in afzonderlijke fout domeinen en upgrade domeinen.

Als u meerdere VM-exemplaren van een laag wilt benutten, moet u Azure Load Balancer configureren tussen toepassings lagen. Als u Load Balancer in elke laag wilt configureren, maakt u een eind punt met gelijke taak verdeling op de Vm's van elke laag afzonderlijk. Voor een specifieke laag maakt u eerst Vm's in dezelfde Cloud service. Dit zorgt ervoor dat ze hetzelfde open bare virtuele IP-adres hebben. Maak vervolgens een eind punt op een van de virtuele machines in die laag. Wijs vervolgens hetzelfde eind punt toe aan de andere virtuele machines op die laag voor taak verdeling. Door een set met gelijke taak verdeling te maken, distribueert u verkeer over meerdere virtuele machines en kunt u ook de Load Balancer om te bepalen welk knoop punt er moet worden verbonden wanneer een back-end-VM-knoop punt mislukt. Een voor beeld: met meerdere exemplaren van de webservers achter een load balancer zorgt u voor een hoge Beschik baarheid van de presentatielaag.

Als best practice moet u er altijd voor zorgen dat alle Internet verbindingen eerst naar de presentatielaag gaan. De presentatielaag heeft toegang tot de zakelijke laag en vervolgens opent de bedrijfslaag de gegevenslaag. Zie [externe toegang tot uw virtuele machine toestaan met behulp van de Azure Portal](../../../virtual-machines/windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie over het toestaan van toegang tot de presentatielaag.

Houd er rekening mee dat de Load Balancer in azure vergelijkbaar is met load balancers in een on-premises omgeving. Zie [taak verdeling voor Azure Infrastructure Services](../../../virtual-machines/windows/tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie.

Daarnaast wordt u aangeraden een privé-netwerk voor uw virtuele machines in te stellen met behulp van Azure Virtual Network. Hierdoor kunnen ze communiceren tussen het privé-IP-adres. Zie [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)voor meer informatie.

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>2 lagen en 3 lagen met scale-out op bedrijfs niveau
In dit toepassings patroon implementeert u een database toepassing met twee lagen of drie lagen naar Azure Virtual Machines door elke toepassingslaag te plaatsen in een andere virtuele machine. Daarnaast wilt u mogelijk de onderdelen van de toepassings server distribueren naar meerdere virtuele machines vanwege de complexiteit van uw toepassing.

Dit toepassings patroon is nuttig wanneer:

* U wilt bedrijfs toepassingen van on-premises gevirtualiseerde platformen verplaatsen naar Azure Virtual Machines.
* U wilt de onderdelen van de toepassings server distribueren naar meerdere virtuele machines vanwege de complexiteit van uw toepassing.
* U wilt bedrijfs logica zware, on-premises LOB-toepassingen (line-of-Business) verplaatsen naar Azure Virtual Machines. LOB-toepassingen zijn een set essentiële computer toepassingen die essentieel zijn voor het uitvoeren van een onderneming, zoals Accounting, Human resources, Payroll, supply chain management en toepassingen voor het plannen van resources.
* U wilt snel ontwikkel-en test omgevingen inrichten voor korte Peri Oden.
* U wilt stress testen uitvoeren op verschillende workload-niveaus, maar tegelijkertijd geen eigen fysieke machines meer wilt maken en onderhouden.
* U wilt eigenaar zijn van een infrastructuur omgeving die op aanvraag omhoog of omlaag kan worden geschaald.

In het volgende diagram ziet u een on-premises scenario en de bijbehorende Cloud oplossing. In dit scenario plaatst u de toepassings lagen in meerdere virtuele machines in azure door de bedrijfs laag te verg Roten, die de bedrijfs logica-laag en de onderdelen voor gegevens toegang bevat. Zoals in het diagram wordt weer gegeven, is Azure Load Balancer verantwoordelijk voor het distribueren van verkeer over meerdere virtuele machines en het bepalen van de webserver waarmee verbinding moet worden gemaakt. Als er meerdere exemplaren van de toepassings servers achter een load balancer, zorgt u ervoor dat de zakelijke laag Maxi maal beschikbaar is. Zie [Aanbevolen procedures voor toepassings patronen met twee lagen, drie lagen of n-tier met meerdere virtuele machines in één laag](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier)voor meer informatie.

![Toepassings patroon met scale-out bedrijfs laag](./media/application-patterns-development-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>2 lagen en 3 lagen met presentaties en bedrijfs lagen scale-out en HADR
In dit toepassings patroon implementeert u een database toepassing met twee lagen of drie lagen naar Azure Virtual Machines door de presentatie tier (webserver) en de onderdelen van de bedrijfslaag (toepassings server) te distribueren naar meerdere virtuele machines. Daarnaast implementeert u oplossingen voor hoge Beschik baarheid en herstel na nood gevallen (HADR) voor uw data bases in azure Virtual Machines.

Dit toepassings patroon is nuttig wanneer:

* U wilt bedrijfs toepassingen van on-premises virtuele machines naar Azure verplaatsen door SQL Server mogelijkheden voor hoge Beschik baarheid en herstel na nood gevallen te implementeren.
* U wilt de presentatielaag en de zakelijke laag uitschalen vanwege een groter aantal inkomende client aanvragen en de complexiteit van uw toepassing.
* U wilt snel ontwikkel-en test omgevingen inrichten voor korte Peri Oden.
* U wilt stress testen uitvoeren op verschillende workload-niveaus, maar tegelijkertijd geen eigen fysieke machines meer wilt maken en onderhouden.
* U wilt eigenaar zijn van een infrastructuur omgeving die op aanvraag omhoog of omlaag kan worden geschaald.

In het volgende diagram ziet u een on-premises scenario en de bijbehorende Cloud oplossing. In dit scenario kunt u de presentatielaag en de onderdelen van de bedrijfslaag uitschalen in meerdere virtuele machines in Azure. Daarnaast implementeert u HADR-technieken (hoge Beschik baarheid en herstel na nood gevallen) voor SQL Server-data bases in Azure.

Wanneer u meerdere exemplaren van een toepassing op verschillende Vm's uitvoert, moet u ervoor zorgen dat er taak verdelings aanvragen voor zijn. Wanneer u meerdere virtuele machines hebt, moet u ervoor zorgen dat al uw Vm's op één punt in de tijd toegankelijk zijn en worden uitgevoerd. Als u taak verdeling configureert, traceert Azure Load Balancer de status van Vm's en stuurt binnenkomende oproepen naar de goede functionerende VM-knoop punten correct. Zie [taak verdeling voor Azure Infrastructure Services](../../../virtual-machines/windows/tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie over het instellen van de taak verdeling van de virtuele machines. Meerdere exemplaren van de web-en toepassings servers achter een load balancer zorgt voor een hoge Beschik baarheid van de presentatie en bedrijfs lagen.

![Uitschalen en hoge Beschik baarheid](./media/application-patterns-development-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Aanbevolen procedures voor toepassings patronen die SQL HADR vereisen
Wanneer u SQL Server oplossingen voor hoge Beschik baarheid en herstel na nood gevallen instelt in azure Virtual Machines, is het instellen van een virtueel netwerk voor uw virtuele machines met [azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) verplicht. Virtuele machines binnen een Virtual Network hebben een stabiel privé IP-adres, zelfs na uitval van de service, zodat u de tijd die nodig is voor het omzetten van de DNS-naam te voor komen. Daarnaast kunt u met het virtuele netwerk uw on-premises netwerk uitbreiden naar Azure en een vertrouwde beveiligings grens maken. Als uw toepassing bijvoorbeeld beperkingen voor het bedrijfs domein heeft (zoals Windows-verificatie, Active Directory), is het instellen van [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) nodig.

De meeste klanten, die productie code op Azure uitvoeren, behouden zowel primaire als secundaire replica's in Azure.

Zie [hoge Beschik baarheid en herstel na nood gevallen voor SQL Server op Azure virtual machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md)voor uitgebreide informatie en zelf studies over technieken voor hoge Beschik baarheid en herstel na nood gevallen.

## <a name="2-tier-and-3-tier-using-azure-virtual-machines-and-cloud-services"></a>2 lagen en 3 lagen met Azure Virtual Machines en Cloud Services
In dit toepassings patroon implementeert u een toepassing met twee lagen of drie lagen op Azure met behulp van [azure Cloud Services](../../../cloud-services/cloud-services-choose-me.md) (web-en werk rollen-platform as a Service (PaaS)) en [Azure virtual machines](../../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Infrastructure as a Service (IaaS)). Het gebruik van [azure Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) voor de tier/Business-laag en SQL Server in [Azure virtual machines](../../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) voor de gegevenslaag is nuttig voor de meeste toepassingen die worden uitgevoerd op Azure. De reden hiervoor is dat een reken instantie die wordt uitgevoerd op Cloud Services een eenvoudig beheer, implementatie, bewaking en uitschalen biedt.

Met Cloud Services onderhoudt Azure de infra structuur voor u, het uitvoeren van routine onderhoud, het patchen van de besturings systemen en pogingen tot het herstellen van service-en hardwarestoringen. Wanneer uw toepassing uitschalen, automatische en hand matige uitschaal opties beschikbaar heeft voor uw Cloud service project, neemt het aantal exemplaren of virtuele machines dat door uw toepassing wordt gebruikt, toe. Daarnaast kunt u on-premises Visual Studio gebruiken om uw toepassing te implementeren in een Cloud service project in Azure.

Als u in samen vatting geen behoefte hebt aan een uitgebreide administratieve taak voor de presentatie/zakelijke laag en uw toepassing geen complexe configuratie van software of het besturings systeem vereist, gebruikt u Azure Cloud Services. Als Azure SQL Database niet alle functies die u zoekt ondersteunt, gebruikt u SQL Server in een virtuele Azure-machine voor de gegevenslaag. Het uitvoeren van een toepassing op Azure Cloud Services en het opslaan van gegevens in azure Virtual Machines combineert de voor delen van beide services. Zie de sectie in dit onderwerp over het [vergelijken van ontwikkelings strategieën in azure](#comparing-web-development-strategies-in-azure)voor een gedetailleerde vergelijking.

In dit toepassings patroon bevat de presentatielaag een webrole, een Cloud Services onderdeel dat wordt uitgevoerd in de Azure Execution Environment en het is aangepast voor de programmering van webtoepassingen zoals ondersteund door IIS en ASP.NET. De laag zakelijk of back-end bevat een werk rollen, een Cloud Services onderdeel dat wordt uitgevoerd in de Azure Execution Environment en het is handig voor een algemene ontwikkeling en kan achtergrond verwerking uitvoeren voor een webrol. De database laag bevindt zich in een SQL Server virtuele machine in Azure. De communicatie tussen de presentatielaag en de gegevenslaag van de data base gebeurt rechtstreeks of via de onderdelen van de worker-rol.

Dit toepassings patroon is nuttig wanneer:

* U wilt bedrijfs toepassingen van on-premises virtuele machines naar Azure verplaatsen door SQL Server mogelijkheden voor hoge Beschik baarheid en herstel na nood gevallen te implementeren.
* U wilt eigenaar zijn van een infrastructuur omgeving die op aanvraag omhoog of omlaag kan worden geschaald.
* Azure SQL Database biedt geen ondersteuning voor alle functies die uw toepassing of data base nodig heeft.
* U wilt stress testen uitvoeren op verschillende workload-niveaus, maar tegelijkertijd geen eigen fysieke machines meer wilt maken en onderhouden.

In het volgende diagram ziet u een on-premises scenario en de bijbehorende Cloud oplossing. In dit scenario plaatst u de presentatielaag in webrollen, de zakelijke laag in werk rollen, maar de gegevenslaag in virtuele machines in Azure. Als u meerdere exemplaren van de presentatielaag in verschillende webrollen uitvoert, zorgt u ervoor dat er op deze manier aanvragen worden gebalanceerd. Wanneer u Azure Cloud Services combineert met Azure Virtual Machines, raden we u aan om ook [azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) in te stellen. Met [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)kunt u stabiele en permanente privé-IP-adressen binnen dezelfde Cloud service in de Cloud hebben. Zodra u een virtueel netwerk voor uw virtuele machines en Cloud Services hebt gedefinieerd, kan de communicatie tussen de privé-IP-adressen worden gestart. Daarnaast biedt virtuele machines en Azure Web/Worker-rollen in dezelfde [Azure-Virtual Network](../../../virtual-network/virtual-networks-overview.md) een lage latentie en een veiligere connectiviteit. Zie [Wat is een Cloud service](../../../cloud-services/cloud-services-choose-me.md)? voor meer informatie.

Zoals in het diagram wordt weer gegeven, distribueert Azure Load Balancer verkeer over meerdere virtuele machines en bepaalt ook met welke webserver of toepassings server verbinding moet worden gemaakt. Meerdere exemplaren van de web-en toepassings servers achter een load balancer zorgt voor een hoge Beschik baarheid van de presentatielaag en de zakelijke laag. Zie [Aanbevolen procedures voor toepassings patronen die SQL HADR vereisen](#best-practices-for-application-patterns-requiring-sql-hadr)voor meer informatie.

![Diagram bevat on-premises fysieke of virtuele machines die zijn verbonden met exemplaren van webrollen in een virtueel Azure-netwerk via Azure load balancer.](./media/application-patterns-development-strategies/IC728013.png)

Een andere benadering voor het implementeren van dit toepassings patroon is het gebruik van een geconsolideerde webrol die de onderdelen van de presentatie tier en de bedrijfslaag bevat, zoals in het volgende diagram wordt weer gegeven. Dit toepassings patroon is handig voor toepassingen waarvoor een stateful ontwerp is vereist. Omdat Azure stateless Compute-knoop punten in web-en werk rollen heeft, raden we u aan een logica te implementeren om de sessie status op te slaan met behulp van een van de volgende technologieën: [Azure caching](https://azure.microsoft.com/documentation/services/azure-cache-for-redis/), [azure Table Storage](../../../cosmos-db/tutorial-develop-table-dotnet.md) of [Azure SQL database](../../database/sql-database-paas-overview.md).

![Diagram bevat on-premises fysieke of virtuele machines die zijn verbonden met geconsolideerde web-of worker-instanties in een virtueel Azure-netwerk.](./media/application-patterns-development-strategies/IC728014.png)

## <a name="pattern-with-azure-virtual-machines-azure-sql-database-and-azure-app-service-web-apps"></a>Patroon met Azure Virtual Machines, Azure SQL Database en Azure App Service (Web Apps)
Het primaire doel van dit toepassings patroon is om te laten zien hoe u Azure Infrastructure as a service-onderdelen (IaaS) kunt combi neren met Azure Platform-as-a-service-onderdelen (PaaS) in uw oplossing. Dit patroon is gericht op Azure SQL Database voor relationele gegevens opslag. Het bevat geen SQL Server in een virtuele machine van Azure, die deel uitmaakt van de Azure-infra structuur als een service aanbieding.

In dit toepassings patroon implementeert u een database toepassing naar Azure door de presentatie en bedrijfs lagen in dezelfde virtuele machine te plaatsen en toegang te krijgen tot een data base in Azure SQL Database servers (SQL Database). U kunt de presentatielaag implementeren met behulp van traditionele weboplossingen op basis van IIS. U kunt ook een gecombineerde presentatie en bedrijfs laag implementeren met behulp van [Azure app service](https://azure.microsoft.com/documentation/services/app-service/web/).

Dit toepassings patroon is nuttig wanneer:

* U hebt al een bestaande SQL Database Server in azure geconfigureerd en wilt uw toepassing snel testen.
* U wilt de mogelijkheden van de Azure-omgeving testen.
* U wilt snel ontwikkel-en test omgevingen inrichten voor korte Peri Oden.
* Uw bedrijfs logica en gegevens toegangs onderdelen kunnen zich in een webtoepassing op zichzelf bevinden.

In het volgende diagram ziet u een on-premises scenario en de bijbehorende Cloud oplossing. In dit scenario plaatst u de toepassings lagen in één virtuele machine in Azure en hebt u toegang tot gegevens in Azure SQL Database.

![Patroon voor gemengde toepassing](./media/application-patterns-development-strategies/IC728015.png)

Als u ervoor kiest om een gecombineerde web-en toepassingslaag te implementeren met behulp van Azure Web Apps, raden we u aan om de middelste laag of de toepassingslaag als Dynamic-Link Libraries (Dll's) te houden in de context van een webtoepassing.

Lees daarnaast de aanbevelingen die worden vermeld in de sectie strategieën voor het [ontwikkelen van webontwikkeling in azure](#comparing-web-development-strategies-in-azure) aan het einde van dit artikel voor meer informatie over programmeer technieken.

## <a name="n-tier-hybrid-application-pattern"></a>N-tier hybride toepassings patroon
In een n-tier hybride toepassings patroon implementeert u uw toepassing in meerdere lagen die worden verdeeld tussen on-premises en Azure. Daarom maakt u een flexibel en herbruikbaar hybride systeem, waarmee u een specifieke laag kunt aanpassen of toevoegen zonder de andere lagen te wijzigen. Als u uw bedrijfs netwerk wilt uitbreiden naar de Cloud, gebruikt u [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) service.

Dit hybride toepassings patroon is nuttig wanneer:

* U wilt toepassingen bouwen die deels in de Cloud en gedeeltelijk op locatie worden uitgevoerd.
* U wilt een aantal of alle elementen van een bestaande on-premises toepassing migreren naar de Cloud.
* U wilt bedrijfs toepassingen van on-premises gevirtualiseerde platformen verplaatsen naar Azure.
* U wilt eigenaar zijn van een infrastructuur omgeving die op aanvraag omhoog of omlaag kan worden geschaald.
* U wilt snel ontwikkel-en test omgevingen inrichten voor korte Peri Oden.
* U wilt een rendabele manier om back-ups te maken voor zakelijke database toepassingen.

In het volgende diagram ziet u een hybride toepassings patroon van n-tier dat over on-premises en Azure overspant. Zoals in het diagram wordt weer gegeven, bevat de on-premises infra structuur [Active Directory Domain Services](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831484(v=ws.11)) domein controller voor de ondersteuning van verificatie en autorisatie van gebruikers. Houd er rekening mee dat in het diagram een scenario wordt gedemonstreerd, waarbij sommige onderdelen van de gegevenslaag in een on-premises Data Center wonen, terwijl sommige onderdelen van de gegevenslaag in azure actief zijn. Afhankelijk van de behoeften van uw toepassing, kunt u verschillende andere hybride scenario's implementeren. U kunt bijvoorbeeld de presentatielaag en de zakelijke laag in een on-premises omgeving, maar de gegevenslaag in azure blijven.

![N-tier-toepassings patroon](./media/application-patterns-development-strategies/IC728016.png)

In azure kunt u Active Directory gebruiken als een zelfstandige Cloud Directory voor uw organisatie. u kunt ook bestaande on-premises Active Directory integreren met [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Zoals u in het diagram ziet, hebben de onderdelen van de bedrijfslaag toegang tot meerdere gegevens bronnen, zoals het [SQL Server in azure](sql-server-on-azure-vm-iaas-what-is-overview.md) via een particulier intern IP-adres, naar on-premises SQL Server via [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)of om [SQL database](../../database/sql-database-paas-overview.md) te gebruiken voor het gebruik van de .NET Framework gegevens provider technologieën. In dit diagram is Azure SQL Database een optionele service voor gegevens opslag.

In een n-tier hybride toepassings patroon kunt u de volgende werk stroom in de opgegeven volg orde implementeren:

1. Bedrijfs database toepassingen identificeren die naar de Cloud moeten worden verplaatst met behulp van de [micro soft Assessment and planning (kaart) Toolkit](https://microsoft.com/map). De kaart Toolkit verzamelt inventaris-en prestatie gegevens van computers die u overweegt voor virtualisatie en biedt aanbevelingen voor de capaciteits-en evaluatie planning.
2. Plan de resources en configuratie die nodig zijn op het Azure-platform, zoals opslag accounts en virtuele machines.
3. Stel de netwerk verbinding in tussen het bedrijfs netwerk on-premises en [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md). Als u de verbinding tussen het bedrijfs netwerk on-premises en een virtuele machine in azure wilt instellen, gebruikt u een van de volgende twee methoden:
   
   1. Een verbinding tot stand brengen tussen on-premises en Azure via open bare eind punten op een virtuele machine in Azure. Deze methode biedt een eenvoudige configuratie en stelt u in staat om SQL Server verificatie in uw virtuele machine te gebruiken. Daarnaast stelt u de regels voor de netwerk beveiligings groep in om het open bare verkeer naar de virtuele machine te beheren. Zie [externe toegang tot uw virtuele machine toestaan met behulp van de Azure Portal](../../../virtual-machines/windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie.
   2. Verbinding maken tussen on-premises en Azure via een VPN-tunnel (virtueel particulier netwerk) van Azure. Met deze methode kunt u domein beleid uitbreiden naar een virtuele machine in Azure. Daarnaast kunt u firewall regels instellen en Windows-verificatie gebruiken in uw virtuele machine. Op dit moment ondersteunt Azure beveiligde site-naar-site VPN-en punt-naar-site-VPN-verbindingen:
      
      * Met beveiligde site-naar-site-verbinding kunt u een netwerk verbinding maken tussen uw on-premises netwerk en het virtuele netwerk in Azure. Het wordt aanbevolen om uw on-premises Datacenter omgeving te koppelen aan Azure.
      * Met een beveiligde punt-naar-site-verbinding kunt u een netwerk verbinding tot stand brengen tussen uw virtuele netwerk in Azure en uw afzonderlijke computers die op een wille keurige locatie worden uitgevoerd. Dit wordt meestal aanbevolen voor ontwikkelings-en test doeleinden.
      
      Zie [verbinding maken met een SQL Server virtuele machine in azure](ways-to-connect-to-sql.md)voor informatie over het maken van verbinding met SQL Server in Azure.
4. Geplande taken en waarschuwingen instellen voor het maken van back-ups van on-premises gegevens in een virtuele-machine schijf in Azure. Zie [SQL Server Backup en Restore with Azure Blob Storage-service](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) en [back-up en herstel voor SQL Server op Azure virtual machines](../../../azure-sql/virtual-machines/windows/backup-restore.md)voor meer informatie.
5. Afhankelijk van de behoeften van uw toepassing, kunt u een van de volgende drie veelvoorkomende scenario's implementeren:
   
   1. U kunt uw webserver-, toepassings server-en ingevoelige gegevens in een database server in azure blijven gebruiken, terwijl u de gevoelige gegevens on-premises blijft.
   2. U kunt uw webserver en toepassings server on-premises laten, terwijl de database server op een virtuele machine in Azure.
   3. U kunt uw database server, webserver en toepassings server on-premises laten staan, terwijl u de database replica's in virtuele machines in azure behoudt. Met deze instelling kunt u de on-premises webservers of rapportage toepassingen gebruiken om toegang te krijgen tot de database replica's in Azure. Daarom kunt u de werk belasting in een on-premises data base verlagen. We raden u aan dit scenario te implementeren voor zware Lees-en ontwikkelings doeleinden. Zie AlwaysOn-beschikbaarheidsgroepen met [hoge Beschik baarheid en herstel na nood gevallen voor SQL Server op azure virtual machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md)voor meer informatie over het maken van database Replica's in Azure.

## <a name="comparing-web-development-strategies-in-azure"></a>Strategieën voor webontwikkeling in azure vergelijken
Als u een op meerdere lagen SQL Server gebaseerde toepassing wilt implementeren en implementeren in azure, kunt u een van de volgende twee programmeer methoden gebruiken:

* Stel een traditionele webserver (IIS-Internet Information Services) in Azure in en krijg toegang tot data bases in SQL Server op Azure Virtual Machines.
* Implementeer en implementeer een Cloud service naar Azure. Zorg er vervolgens voor dat deze Cloud service toegang kan krijgen tot data bases in SQL Server op Azure Virtual Machines. Een Cloud service kan meerdere web-en werk rollen bevatten.

De volgende tabel bevat een vergelijking van de traditionele webontwikkeling met Azure Cloud Services en Azure Web Apps met betrekking tot SQL Server op Azure Virtual Machines. De tabel bevat Azure Web Apps, omdat het mogelijk is om SQL Server in een Azure-VM te gebruiken als een gegevens bron voor Azure Web Apps via het open bare virtuele IP-adres of de DNS-naam.

|  | Traditionele web-ontwikkeling in azure Virtual Machines | Cloud Services in azure | Webhosting met Azure Web Apps |
| --- | --- | --- | --- |
| **Toepassings migratie van on-premises** |Bestaande toepassingen. |Toepassingen hebben web-en werk rollen nodig. |Bestaande toepassingen zijn, maar geschikt voor zelf-opgenomen webtoepassingen en webservices waarvoor snelle schaal baarheid is vereist. |
| **Ontwikkeling en implementatie** |Visual Studio, webmatrix, Visual Web Developer, webdeploy, FTP, TFS, IIS-beheer, Power shell. |Visual Studio, Azure SDK, TFS, Power shell. Elke Cloud service heeft twee omgevingen waarop u uw service pakket en configuratie kunt implementeren: fase ring en productie. U kunt een Cloud service implementeren naar de faserings omgeving om deze te testen voordat u deze naar productie gaat promo veren. |Visual Studio, webmatrix, Visual Web Developer, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub, mercurial, TFS, Web Deploy, Power shell. |
| **Beheer en installatie** |U bent zelf verantwoordelijk voor beheer taken voor de toepassing, gegevens, firewall regels, het virtuele netwerk en het besturings systeem. |U bent zelf verantwoordelijk voor beheer taken voor de toepassing, de gegevens, de firewall regels en het virtuele netwerk. |U bent zelf verantwoordelijk voor beheer taken voor de toepassing en de gegevens. |
| **Hoge Beschik baarheid en herstel na nood gevallen (HADR)** |We raden aan dat u virtuele machines in dezelfde beschikbaarheidsset en in dezelfde Cloud service plaatst. Als u uw virtuele machines in dezelfde beschikbaarheidsset houdt, kan Azure de Maxi maal beschik bare knoop punten in afzonderlijke fout domeinen en upgrade domeinen plaatsen. Op dezelfde manier zorgt u ervoor dat taak verdeling en virtuele machines rechtstreeks met elkaar kunnen communiceren via het lokale netwerk binnen een Azure-Data Center, zodat de virtuele machines in dezelfde Cloud service worden bewaard.<br/><br/>U bent verantwoordelijk voor het implementeren van een oplossing voor hoge Beschik baarheid en herstel na nood gevallen voor SQL Server op Azure Virtual Machines om uitval tijd te voor komen. Voor ondersteunde HADR-technologieën raadpleegt u [hoge Beschik baarheid en herstel na nood gevallen voor SQL Server op Azure virtual machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md).<br/><br/>U bent zelf verantwoordelijk voor het maken van back-ups van uw eigen gegevens en toepassingen.<br/><br/>Azure kan uw virtuele machines verplaatsen als de hostmachine in het Data Center mislukt als gevolg van hardwareproblemen. Bovendien kan er een geplande uitval tijd van uw virtuele machine optreden wanneer de hostmachine wordt bijgewerkt voor beveiligings-of software-updates. Daarom raden we u aan ten minste twee virtuele machines in elke toepassingslaag te onderhouden om de voortdurende Beschik baarheid te garanderen. Azure biedt geen SLA voor één virtuele machine. |Azure beheert de fouten die voortkomen uit de onderliggende software van de hardware of het besturings systeem. We raden u aan meerdere exemplaren van een web-of worker-rol te implementeren om ervoor te zorgen dat uw toepassing Maxi maal beschikbaar is. Zie [Cloud Services, virtual machines en Virtual Network Service Level Agreement](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)voor meer informatie.<br/><br/>U bent zelf verantwoordelijk voor het maken van back-ups van uw eigen gegevens en toepassingen.<br/><br/>Voor data bases in een SQL Server-data base in een Azure-VM bent u verantwoordelijk voor het implementeren van een oplossing voor hoge Beschik baarheid en herstel na nood gevallen om uitval tijd te voor komen. Voor ondersteunde HDAR-technologieën raadpleegt u hoge Beschik baarheid en herstel na nood gevallen voor SQL Server op Azure Virtual Machines.<br/><br/>**SQL Server database spiegeling**: gebruik met Azure Cloud Services (Web/werk rollen). SQL Server Vm's en een Cloud service project kunnen zich in dezelfde Azure-Virtual Network bevindt. Als SQL Server virtuele machine zich niet in dezelfde Virtual Network bevindt, moet u een SQL Server-alias maken om de communicatie met het exemplaar van SQL Server te routeren. Daarnaast moet de naam van de alias overeenkomen met de naam van de SQL Server. |Hoge Beschik baarheid wordt overgenomen van Azure worker-rollen, Azure Blob-opslag en Azure SQL Database. Azure Storage houdt bijvoorbeeld drie replica's van alle BLOB-, tabel-en wachtrij gegevens bij. Azure SQL Database houdt op elk moment drie replica's van gegevens actief, één primaire replica en twee secundaire replica's. Zie [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) en [Azure SQL database](../../database/sql-database-paas-overview.md)voor meer informatie.<br/><br/>Wanneer u SQL Server in een Azure-VM gebruikt als gegevens bron voor Azure Web Apps, moet u er wel voor zorgen dat Azure Web Apps geen ondersteuning biedt voor Azure Virtual Network. Met andere woorden, alle verbindingen van Azure Web Apps naar SQL Server Vm's in azure moeten via open bare eind punten van virtuele machines gaan. Dit kan enkele beperkingen veroorzaken voor hoge Beschik baarheid en herstel na nood gevallen. Bijvoorbeeld: de client toepassing op Azure Web Apps verbinding maken met SQL Server VM met behulp van database spiegeling, zou geen verbinding kunnen maken met de nieuwe primaire server omdat het spie gelen van de data base vereist dat u Azure Virtual Network instelt tussen virtuele machines van SQL Server host in Azure. Daarom wordt het gebruik van **SQL Server Data Base mirroring** met Azure web apps op dit moment niet ondersteund.<br/><br/>**SQL Server AlwaysOn-beschikbaarheidsgroepen**: u kunt AlwaysOn-beschikbaarheidsgroepen instellen wanneer u Azure Web Apps gebruikt met SQL Server Vm's in Azure. Maar u moet de AlwaysOn-beschikbaarheids groep-listener configureren voor het door sturen van de communicatie naar de primaire replica via open bare eind punten met taak verdeling. |
| **Cross-premises-connectiviteit** |U kunt Azure Virtual Network gebruiken om verbinding te maken met on-premises. |U kunt Azure Virtual Network gebruiken om verbinding te maken met on-premises. |Azure Virtual Network wordt ondersteund. Zie [Web Apps Virtual Network-integratie](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)voor meer informatie. |
| **Schaalbaarheid** |Omhoog schalen is beschikbaar door de grootte van de virtuele machines te verhogen of door meer schijven toe te voegen. Zie [Virtual Machine sizes for Azure](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)(Engelstalig) voor meer informatie over de grootte van virtuele machines.<br/><br/>**Voor database server**: scale-out is beschikbaar via data base-partitionering technieken en SQL Server AlwaysOn-beschikbaarheids groepen.<br/><br/>Voor zware Lees werkbelastingen kunt u [AlwaysOn-beschikbaarheidsgroepen](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) gebruiken op meerdere secundaire knoop punten en SQL Server replicatie.<br/><br/>Voor zware werk belastingen kunt u horizontale partitionering van gegevens op meerdere fysieke servers implementeren om toepassings scale-out te bieden.<br/><br/>Daarnaast kunt u een scale-out implementeren met behulp van [SQL Server met gegevens afhankelijke route ring](/previous-versions/sql/sql-server-2005/administrator/cc966448(v=technet.10)). Met gegevens afhankelijke route ring (DDR) moet u het partitioneren-mechanisme in de client toepassing implementeren, doorgaans in de laag van de zakelijke laag, om de database aanvragen naar meerdere SQL Server-knoop punten te routeren. De bedrijfslaag bevat toewijzingen voor het partitioneren van de gegevens en van het knoop punt dat de gegevens bevat.<br/><br/>U kunt toepassingen schalen die virtuele machines uitvoeren. Zie [een toepassing schalen](../../../cloud-services/cloud-services-how-to-scale-portal.md)voor meer informatie.<br/><br/>**Belang rijke Opmerking**: met de functie voor automatisch **schalen** in azure kunt u de virtuele machines die worden gebruikt door uw toepassing, verg Roten of verkleinen. Deze functie zorgt ervoor dat de eindgebruikers ervaring tijdens piek perioden niet negatief wordt beïnvloed en dat Vm's worden afgesloten wanneer de vraag laag is. Het is raadzaam dat u de optie voor automatisch schalen niet instelt voor uw Cloud service als deze SQL Server Vm's bevat. De functie voor automatisch schalen biedt Azure de mogelijkheid om een virtuele machine in te scha kelen wanneer het CPU-gebruik in die VM hoger is dan een bepaalde drempel waarde en om een virtuele machine uit te scha kelen wanneer het CPU-gebruik lager is dan het geval. De functie voor automatisch schalen is handig voor stateless toepassingen, zoals webservers, waarbij elke virtuele machine de werk belasting kan beheren zonder enige verwijzing naar een eerdere status. De functie voor automatisch schalen is echter niet nuttig voor stateful toepassingen, zoals SQL Server, waarbij slechts één instantie schrijven naar de data base toestaat. |Omhoog schalen is beschikbaar met behulp van meerdere web-en werk rollen. Zie [Configure sizes for Cloud Services](../../../cloud-services/cloud-services-sizes-specs.md)voor meer informatie over de grootte van virtuele machines voor webrollen en werk rollen.<br/><br/>Wanneer u **Cloud Services** gebruikt, kunt u meerdere rollen definiëren voor het distribueren van de verwerking en ook het flexibele schalen van uw toepassing. Elke Cloud service bevat een of meer webrollen en/of werk rollen, elk met een eigen toepassings bestand en configuratie. U kunt een Cloud service schalen door het aantal rolinstanties (virtuele machines) te verhogen dat voor een rol is geïmplementeerd en een Cloud service te schalen door het aantal rolinstanties te verlagen. Zie [Azure Execution models](../../../cloud-services/cloud-services-choose-me.md)voor gedetailleerde informatie.<br/><br/>Uitschalen is beschikbaar via de ingebouwde ondersteuning voor maximale Beschik baarheid van Azure via [Cloud Services, virtual machines en Virtual Network Service Level Agreement](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) en Load Balancer.<br/><br/>Voor een toepassing met meerdere lagen raden we u aan om de toepassing Web/Worker-rollen te koppelen aan Data Base Server-Vm's via Azure Virtual Network. Daarnaast biedt Azure taak verdeling voor virtuele machines in dezelfde Cloud service, waarbij gebruikers aanvragen over hen worden verspreid. Virtuele machines die op deze manier zijn verbonden, kunnen rechtstreeks communiceren met elkaar via het lokale netwerk binnen een Azure-Data Center.<br/><br/>U kunt **automatisch schalen** instellen voor de Azure Portal en de plannings tijden. Zie voor meer informatie [automatisch schalen configureren voor een Cloud service in de portal](../../../cloud-services/cloud-services-how-to-scale-portal.md). |**Omhoog en omlaag schalen**: u kunt de grootte van de instantie (VM) die voor uw website is gereserveerd, verg Roten of verkleinen.<br/><br/>Uitschalen: u kunt meer gereserveerde instanties (Vm's) toevoegen voor uw website.<br/><br/>U kunt **automatisch schalen** instellen op de portal en de plannings tijden. Zie [Web apps schalen](../../../app-service/manage-scale-up.md)voor meer informatie. |

Zie [Azure web apps, Cloud Services en vm's: wanneer u wilt gebruiken](/azure/architecture/guide/technology-choices/compute-decision-tree)voor meer informatie over het kiezen van deze programmeer methoden.

## <a name="next-steps"></a>Volgende stappen
Zie [SQL Server in azure virtual machines Overview](sql-server-on-azure-vm-iaas-what-is-overview.md)voor meer informatie over het uitvoeren van SQL Server op Azure virtual machines.