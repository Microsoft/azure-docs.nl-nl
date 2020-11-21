---
title: Azure Lab Services-beheerders handleiding | Microsoft Docs
description: Deze hand leiding helpt beheerders die Lab-accounts maken en beheren met behulp van Azure Lab Services.
ms.topic: article
ms.date: 10/20/2020
ms.openlocfilehash: 08d2fea719ad67f666ea9da09721dc3f7ab54768
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024633"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services-beheerders handleiding
IT-beheerders die de cloud resources van een universiteit beheren, zijn normaal gesp roken verantwoordelijk voor het instellen van het lab-account voor hun school. Nadat ze een Lab-account hebben ingesteld, maken beheerders of docenten de Labs die zijn opgenomen in het account. Dit artikel bevat een overzicht op hoog niveau van de betrokken Azure-resources en de richt lijnen voor het maken van deze.

![Diagram van een weer gave op hoog niveau van Azure-resources in een Lab-account.](./media/administrator-guide/high-level-view.png)

- Labs wordt gehost in een Azure-abonnement dat eigendom is van Azure Lab Services.
- Lab-accounts, een galerie met gedeelde afbeeldingen en installatie kopie versies worden gehost in uw abonnement.
- U kunt uw Lab-account en de galerie met gedeelde installatie kopieën in dezelfde resource groep hebben. In dit diagram bevinden ze zich in verschillende resource groepen.

Zie de beginselen van de [architectuur van Labs](./classroom-labs-fundamentals.md)voor meer informatie over de architectuur.

## <a name="subscription"></a>Abonnement
Uw universiteit kan een of meer Azure-abonnementen hebben. U gebruikt abonnementen voor het beheren van facturering en beveiliging voor alle Azure-resources en-services die binnen IT worden gebruikt, inclusief Lab-accounts.

De relatie tussen een Lab-account en het abonnement is belang rijk omdat:

- Facturering wordt gerapporteerd via het abonnement dat het lab-account bevat.
- U kunt gebruikers in het Azure Active Directory (Azure AD) Tenant toegang van het abonnement verlenen aan Azure Lab Services. U kunt een gebruiker toevoegen als eigenaar of bijdrager van een Lab-account, of als een Lab-maker of een Lab-eigenaar.

Labs en hun virtuele machines (Vm's) worden beheerd en worden gehost voor u binnen een abonnement dat Azure Lab Services is.

## <a name="resource-group"></a>Resourcegroep
Een abonnement bevat een of meer resource groepen. Resource groepen worden gebruikt voor het maken van logische groeperingen van Azure-resources die samen worden gebruikt binnen dezelfde oplossing.  

Wanneer u een Lab-account maakt, moet u de resource groep configureren die het lab-account bevat. 

Een resource groep is ook vereist wanneer u een [Galerie met gedeelde afbeeldingen](#shared-image-gallery)maakt. U kunt uw Lab-account en de galerie met gedeelde afbeeldingen in dezelfde resource groep of in twee afzonderlijke resource groepen plaatsen. U kunt deze tweede benadering gebruiken als u van plan bent om de galerie met installatie kopieën te delen in verschillende oplossingen.

Wanneer u een Lab-account maakt, kunt u automatisch een galerie met gedeelde installatie kopieën maken en koppelen.  Met deze optie worden het lab-account en de galerie met gedeelde afbeeldingen gemaakt in afzonderlijke resource groepen. U ziet dit gedrag bij het volgen van de stappen die worden beschreven in de [Galerie gedeelde installatie kopieën configureren op het moment dat het account wordt gemaakt](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation) . In de installatie kopie aan het begin van dit artikel wordt deze configuratie gebruikt. 

We raden u aan om tijd vooraf te investeren om de structuur van uw resource groepen te plannen, omdat het *niet* mogelijk is om een test account of een resource groep voor de gedeelde installatie kopie galerie te wijzigen nadat deze is gemaakt. Als u de resource groep voor deze resources moet wijzigen, moet u het lab-account of de galerie met gedeelde afbeeldingen verwijderen en opnieuw maken.

## <a name="lab-account"></a>Lab-account

Een Lab-account fungeert als een container voor een of meer Labs. Wanneer u aan de slag gaat met Azure Lab Services, is het het meest gebruikelijk om één Lab-account te hebben. Als uw test gebruik wordt geschaald, kunt u later meer Lab-accounts maken.

De volgende lijst geeft een overzicht van scenario's waarin meer dan één Lab-account mogelijk nuttig is:

- **Verschillende beleids vereisten in Labs beheren**

    Wanneer u een Lab-account instelt, stelt u beleids regels in die van toepassing zijn op *alle* Labs onder het lab-account, zoals:
    - Het virtuele Azure-netwerk met gedeelde bronnen waartoe het lab toegang heeft. Stel dat u een set Labs hebt die toegang nodig heeft tot een gedeelde gegevensset in een virtueel netwerk.
    - De installatie kopieën van de virtuele machine die door de Labs kunnen worden gebruikt om Vm's te maken. U hebt bijvoorbeeld een set met laboratoria die toegang nodig hebben tot de [Data Science VM voor Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) Azure Marketplace-installatie kopie.

    Als elk van uw Labs unieke beleids vereisten heeft, kan het nuttig zijn om afzonderlijke Lab-accounts te maken voor het afzonderlijk beheren van elk lab.

- **Een apart budget toewijzen aan elk lab-account**
  
    In plaats van alle Lab-kosten te rapporteren via één Lab-account, hebt u mogelijk een duidelijker budget nodig. U kunt bijvoorbeeld afzonderlijke Lab-accounts maken voor de reken kundige afdeling van uw universiteit, de afdeling computer wetenschappen, enzovoort, om de begroting over afdelingen te verdelen.  U kunt vervolgens de kosten voor elk individueel Lab-account weer geven met behulp van [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md).

- **Prototype Labs isoleren van actieve of productie Labs**
  
    Het kan voor komen dat u beleids wijzigingen wilt testen voor een Lab-account zonder dat dit van invloed is op uw actieve of productie Labs. In dit type scenario kunt u met het maken van een afzonderlijke Lab-account voor test doeleinden wijzigingen isoleren. 

## <a name="lab"></a>Lab

Een Lab bevat Vm's die elk zijn toegewezen aan één student.  Over het algemeen kunt u het volgende verwachten:

- U hebt één Lab voor elke klasse.
- Maak een nieuwe set Labs voor elk semester, kwar taal of ander academisch systeem dat u gebruikt. Voor klassen die dezelfde installatie kopie moeten gebruiken, moet u een galerie met [gedeelde afbeeldingen](#shared-image-gallery)gebruiken. Op deze manier kunt u installatie kopieën hergebruiken in Labs-en academische perioden.

Houd rekening met de volgende punten wanneer u bepaalt hoe u uw Labs structureert:

- **Alle Vm's binnen een Lab worden geïmplementeerd met dezelfde installatie kopie die wordt gepubliceerd**

    Als u een klasse hebt waarvoor verschillende Lab-installatie kopieën moeten worden gepubliceerd, moet u als gevolg hiervan een afzonderlijk Lab maken voor elke installatie kopie.
  
- **Het gebruiks quotum is ingesteld op het lab-niveau en is van toepassing op alle gebruikers in het lab**

    Als u verschillende quota wilt instellen voor gebruikers, moet u afzonderlijke Labs maken. Het is echter mogelijk om meer uren aan specifieke gebruikers toe te voegen nadat u het quotum hebt ingesteld.
  
- **Het opstart-of afsluit schema is ingesteld op het lab-niveau en is van toepassing op alle virtuele machines in het lab**

    Net als bij de quotum instelling moet u voor elk schema een afzonderlijke Lab maken voor de instellingen van de gebruiker.

Elk lab heeft standaard een eigen virtueel netwerk.  Als u Virtual Network-peering hebt ingeschakeld, heeft elk lab een eigen subnet dat is gekoppeld aan het opgegeven virtuele netwerk.

## <a name="shared-image-gallery"></a>Galerie met gedeelde afbeeldingen

Een galerie met gedeelde installatie kopieën is gekoppeld aan een Lab-account en fungeert als een centrale opslag plaats voor het opslaan van installatie kopieën. Een afbeelding wordt in de galerie opgeslagen wanneer een docent ervoor kiest om deze te exporteren vanuit de VM van een Lab-sjabloon. Telkens wanneer een docent wijzigingen aanbrengt in de sjabloon-VM en deze exporteert, worden nieuwe versies van de installatie kopie opgeslagen en worden de vorige versies bewaard.

Docenten kunnen een installatie kopie versie publiceren vanuit de galerie met gedeelde afbeeldingen wanneer ze een nieuw lab maken. Hoewel de galerie meerdere versies van een afbeelding opslaat, kunnen docenten alleen de nieuwste versie selecteren tijdens het maken van het lab.

De service voor de galerie van gedeelde afbeeldingen is een optionele resource die u mogelijk niet onmiddellijk nodig hebt als u met slechts enkele lessen begint. De galerie met gedeelde afbeeldingen biedt echter veel voor delen die handig zijn wanneer u omhoog schaalt naar aanvullende Labs:

- **U kunt versies van een sjabloon-VM-installatie kopie opslaan en beheren**

    Het is handig om een aangepaste installatie kopie te maken of wijzigingen (software, configuratie, enzovoort) door te voeren in een installatie kopie van de open bare Azure Marketplace-galerie.  Het is bijvoorbeeld gebruikelijk dat docenten andere software of hulpprogram ma's moeten installeren. In plaats van dat cursisten zelf deze vereiste onderdelen hand matig moeten installeren, kunnen er verschillende versies van de sjabloon-VM-installatie kopie worden geëxporteerd naar een galerie met gedeelde installatie kopieën. U kunt deze installatie kopie versies vervolgens gebruiken bij het maken van nieuwe Labs.

- **U kunt sjabloon-VM-installatie kopieën in Labs delen en opnieuw gebruiken**

    U kunt een installatie kopie opslaan en opnieuw gebruiken zodat u deze niet telkens opnieuw hoeft te configureren wanneer u een nieuw Lab maakt. Als meerdere klassen bijvoorbeeld dezelfde afbeelding moeten gebruiken, kunt u deze eenmaal maken en exporteren naar de galerie met gedeelde afbeeldingen, zodat deze kan worden gedeeld in Labs.

- **De beschik baarheid van de afbeelding wordt gegarandeerd via automatische replicatie**

    Wanneer u een afbeelding van een Lab opslaat in de galerie met gedeelde afbeeldingen, wordt deze automatisch gerepliceerd naar andere [regio's binnen dezelfde geografie](https://azure.microsoft.com/global-infrastructure/regions/). Als er een storing optreedt in een regio, wordt het publiceren van de installatie kopie naar uw Lab niet beïnvloed, omdat een installatie kopie replica van een andere regio kan worden gebruikt.  Het publiceren van Vm's vanuit meerdere replica's kan ook helpen bij de prestaties.

Als u gedeelde installatie kopieën logisch wilt groeperen, kunt u een van de volgende handelingen uitvoeren:

- Meerdere gemeen schappelijke afbeeldings galerieën maken. Elk lab-account kan verbinding maken met slechts één gedeelde installatie kopie galerie. Daarom moet u voor deze optie ook meerdere Lab-accounts maken.
- Gebruik één gedeelde installatie kopie galerie die wordt gedeeld door meerdere Lab-accounts. In dit geval kan elk lab-account alleen installatie kopieën inschakelen die van toepassing zijn op de Labs in dat account.

## <a name="naming"></a>Naamgeving

Wanneer u aan de slag gaat met Azure Lab Services, raden we u aan om naam conventies voor resource groepen, Lab-accounts, Labs en de galerie met gedeelde afbeeldingen te bepalen. Hoewel de naamgevings regels die u instelt, uniek zijn voor de behoeften van uw organisatie, biedt de volgende tabel algemene richt lijnen:

| Resourcetype | Rol | Voorgesteld patroon | Voorbeelden |
| ------------- | ---- | ----------------- | -------- | 
| Resourcegroep | Bevat een of meer Lab-accounts en een of meer galerie met gedeelde afbeeldingen | \<organization short name\>-\<environment\>-RG<ul><li>De **korte naam** van een organisatie duidt de naam van de organisatie aan die door de resource groep wordt ondersteund.</li><li>- **Omgeving** identificeert de omgeving voor de resource, zoals *pilot* of *productie*.</li><li>**RG** staat voor de *resource groep* resource type.</li></ul> | contosouniversitylabs-RG<br/>contosouniversitylabs-pilot-RG<br/>contosouniversitylabs-Prod-RG |
| Lab-account | Bevat een of meer Labs | \<organization short name\>-\<environment\>-La<ul><li>De **korte naam** van een organisatie duidt de naam van de organisatie aan die door de resource groep wordt ondersteund.</li><li>- **Omgeving** identificeert de omgeving voor de resource, zoals *pilot* of *productie*.</li><li>**La** staat voor het lab- *account* van het resource type.</li></ul> | contosouniversitylabs-La<br/>mathdeptlabs-La<br/>sciencedeptlabs-pilot-La<br/>sciencedeptlabs-Prod-La |
| Lab | Bevat een of meer virtuele machines |\<class name\>-\<timeframe\>-\<educator identifier\><ul><li>**Klassenaam** identificeert de naam van de klasse die door het lab wordt ondersteund.</li><li>**Tijds bestek** duidt de periode aan waarin de klasse wordt aangeboden.</li>Met de **id voor docenten** worden de docenten geïdentificeerd die eigenaar zijn van het lab.</li></ul> | CS1234-fall2019-JANJANSEN<br/>CS1234-spring2019-JANJANSEN |
| Galerie met gedeelde afbeeldingen | Bevat een of meer versies van VM-installatie kopieën | \<organization short name\>sjablonen | contosouniversitylabsgallery |

Zie [naamgevings conventies voor Azure-resources](/azure/architecture/best-practices/naming-conventions)voor meer informatie over het benoemen van andere Azure-resources.

## <a name="regionslocations"></a>Regions\locations

Wanneer u uw Azure Lab Services-resources instelt, moet u een regio of locatie opgeven van het Data Center dat als host fungeert voor de resources. In de volgende secties wordt beschreven hoe een regio of locatie van invloed kan zijn op elke resource die betrokken is bij het instellen van een lab.

### <a name="resource-group"></a>Resourcegroep

De regio geeft het Data Center aan waar informatie over een resource groep wordt opgeslagen. Azure-resources in de resource groep kunnen zich bevinden in een andere regio dan die van het bovenliggende item.

### <a name="lab-account"></a>Lab-account

De locatie van een test account geeft aan in welke regio een resource zich bevindt.  

### <a name="lab"></a>Lab

De locatie waar een Lab zich bevindt, is afhankelijk van de volgende factoren:

  - **Het lab-account is gekoppeld aan een virtueel netwerk**
  
    U kunt [een Lab-account met een virtueel netwerk peeren](./how-to-connect-peer-virtual-network.md) wanneer deze zich in dezelfde regio bevinden.  Wanneer een Lab-account is gekoppeld aan een virtueel netwerk, worden er automatisch Labs gemaakt in dezelfde regio als het lab-account en het virtuele netwerk.

    > [!NOTE]
    > Wanneer een Lab-account is gekoppeld aan een virtueel netwerk, wordt de instelling Lab- **Maker voor het kiezen van Lab-locatie toestaan** uitgeschakeld. Zie voor meer informatie [toestaan dat Lab Creator de locatie voor het lab selecteert](./allow-lab-creator-pick-lab-location.md).
    
  - **Geen enkel virtueel netwerk is gekoppeld *en* Lab-makers mogen de locatie van het lab niet kiezen**
  
    Als *er geen* virtueel netwerk is gekoppeld aan het lab-account en [Lab-makers *mogen* de Lab-locatie niet kiezen](./allow-lab-creator-pick-lab-location.md), worden er automatisch Labs gemaakt in een regio die beschik bare VM-capaciteit heeft.  Met name Azure Lab Services zoekt naar Beschik baarheid in [regio's die zich binnen dezelfde geografie bevinden als het lab-account](https://azure.microsoft.com/global-infrastructure/regions).

  - **Geen enkel virtueel netwerk is gekoppeld *en* Lab-makers mogen de locatie van het lab kiezen**
       
    Als *er geen* peers worden gekoppeld aan een virtueel netwerk en [Lab-makers de locatie van het lab *mogen* kiezen](./allow-lab-creator-pick-lab-location.md), zijn de locaties die kunnen worden geselecteerd door de maker van het lab afhankelijk van de beschik bare capaciteit.

> [!NOTE]
> Om ervoor te zorgen dat een regio voldoende VM-capaciteit heeft, is het belang rijk om eerst capaciteit aan te vragen via het lab-account bij het maken van het lab.

Een algemene regel is het instellen van de regio van een resource die het dichtst bij de gebruikers ligt. Voor Labs betekent dit dat u het Lab maakt dat zich het dichtst bij uw studenten bevindt. Voor online cursussen waarvan de studenten zich overal ter wereld bevinden, kunt u de beste beslissing nemen om een lab te maken dat zich centraal bevindt. U kunt ook een klasse in meerdere Labs splitsen op basis van de regio's van uw studenten.

## <a name="vm-sizing"></a>VM-grootte

Wanneer beheerders of Lab-makers een lab maken, kunnen ze kiezen uit verschillende VM-grootten, afhankelijk van de behoeften van hun klas lokaal. Houd er rekening mee dat de beschik baarheid van de berekenings grootte afhankelijk is van de regio waarin uw Lab-account zich bevindt.

| Grootte | Specificaties | Reeks | Voorgesteld gebruik |
| ---- | ----- | ------ | ------------- |
| Klein| <ul><li>2 &nbsp; kernen</li><li>3,5 gigabyte (GB) RAM-geheugen</li> | [Standard_A2_v2](../virtual-machines/av2-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) | Het meest geschikt voor de opdracht regel, het openen van webbrowser, webservers met weinig verkeer, kleine tot middel grote data bases. |
| Normaal | <ul><li>4 &nbsp; kernen</li><li>7 &nbsp; GB &nbsp; RAM-geheugen</li> | [Standard_A4_v2](../virtual-machines/av2-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) | Het meest geschikt voor relationele data bases, caching in het geheugen en analyse. |
| Gemiddeld (geneste virtualisatie) | <ul><li>4 &nbsp; kernen</li><li>16 &nbsp; GB &nbsp; RAM-geheugen</li></ul> | [Standard_D4s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json#dsv3-series) | Het meest geschikt voor relationele data bases, caching in het geheugen en analyse.
| Groot | <ul><li>8 &nbsp; kernen</li><li>16 &nbsp; GB &nbsp; RAM-geheugen</li></ul>  | [Standard_A8_v2](../virtual-machines/av2-series.md) | Het meest geschikt voor toepassingen die snellere Cpu's nodig hebben, betere prestaties van de lokale schijf, grote data bases, grote geheugen caches.  Deze grootte biedt ook ondersteuning voor geneste virtualisatie. |
| Groot (geneste virtualisatie) | <ul><li>8 &nbsp; kernen</li><li>32 &nbsp; GB &nbsp; RAM-geheugen</li></ul>  | [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json#dsv3-series) | Het meest geschikt voor toepassingen die snellere Cpu's nodig hebben, betere prestaties van de lokale schijf, grote data bases, grote geheugen caches. |
| Kleine GPU (visualisatie) | <ul><li>6 &nbsp; kernen</li><li>56 &nbsp; GB &nbsp; RAM-geheugen</li>  | [Standard_NV6](../virtual-machines/nv-series.md) | Het meest geschikt voor externe visualisatie, streaming, games en code ring met behulp van frameworks zoals OpenGL en DirectX. |
| Kleine GPU (Compute) | <ul><li>6 &nbsp; kernen</li><li>56 &nbsp; GB &nbsp; RAM-geheugen</li></ul>  | [Standard_NC6](../virtual-machines/nc-series.md) |Het meest geschikt voor computer-intensieve toepassingen, zoals AI en diepe learning. |
| Gemiddelde GPU (visualisatie) | <ul><li>12 &nbsp; kernen</li><li>112 &nbsp; GB &nbsp; RAM-geheugen</li></ul>  | [Standard_NV12](../virtual-machines/nv-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) | Het meest geschikt voor externe visualisatie, streaming, games en code ring met behulp van frameworks zoals OpenGL en DirectX. |

## <a name="manage-identity"></a>Identiteit beheren

Door gebruik te maken van op [rollen gebaseerd toegangs beheer (RBAC)](../role-based-access-control/overview.md) voor toegang tot Lab-accounts en-Labs kunt u de volgende rollen toewijzen:

- **Eigenaar** van Lab-account

    Aan een beheerder die een Lab-account maakt, wordt automatisch de rol van de Lab-account eigenaar toegewezen. De rol van eigenaar kan:
     - Wijzig de instellingen van het lab-account.
     - Ken andere beheerders toegang tot het lab-account toe als eigenaar of bijdrager.
     - Bied docenten toegang tot Labs als maker, eigenaar of bijdrager.
     - Alle Labs in het lab-account maken en beheren.

- **Inzender** voor Lab-account

    Een beheerder die de rol Inzender heeft toegewezen, kan:
    - Wijzig de instellingen van het lab-account.
    - Alle Labs in het lab-account maken en beheren.

    De Inzender kan echter *geen* andere gebruikers toegang verlenen tot Lab-accounts of Labs.

- **Lab-Maker**

    Als u Labs wilt maken binnen een Lab-account, moeten docenten lid zijn van de rol Lab Creator.  Een docent die een Lab maakt, wordt automatisch toegevoegd als een Lab-eigenaar. Zie [een gebruiker toevoegen aan de rol Lab Creator](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)voor meer informatie. 

- **Eigenaar** of **bijdrager** van Lab
  
    Een docent in een Lab-eigenaar of Inzender rol kan de instellingen van een Lab bekijken en wijzigen. De persoon moet ook lid zijn van de rol van de test account-lezer.

    Een belang rijk verschil tussen de rollen Lab-eigenaar en Inzender is dat alleen een eigenaar andere gebruikers toegang kan verlenen tot het beheren van een lab. Een mede werker *kan* geen toegang verlenen aan andere gebruikers om een lab te beheren.

- **Galerie met gedeelde afbeeldingen**

    Wanneer u een galerie met gedeelde installatie kopieën koppelt aan een Lab-account, krijgen de eigen aars van het lab-account en de mede werkers en de makers van Lab-, Lab-eigen aars en Lab-mede werkers automatisch toegang tot het weer geven en opslaan van afbeeldingen in de galerie.

Wanneer u rollen toewijst, is het handig om de volgende tips te volgen:

   - Normaal gesp roken moeten alleen beheerders lid zijn van een Lab-account eigenaar of de rol Inzender. Het lab-account kan meer dan één eigenaar of Inzender hebben.
   - Als u docenten de mogelijkheid wilt geven om nieuwe Labs te maken en de door hen gemaakte Labs te beheren, moet u deze alleen toewijzen aan de rol van Lab Creator.
   - Om docenten de mogelijkheid te bieden specifieke Labs te beheren, maar *niet* de mogelijkheid om nieuwe Labs te maken, wijst u de rol eigenaar of bijdrager toe voor elk lab dat ze beheert. Het is bijvoorbeeld mogelijk dat u een docent en een onderwijs assistent wilt toestaan die gezamenlijk een Lab heeft. Zie [eigen aren toevoegen aan een Lab](./how-to-add-user-lab-owner.md)voor meer informatie.

## <a name="pricing"></a>Prijzen

### <a name="azure-lab-services"></a>Azure Lab-Services

Zie [Azure Lab Services prijzen](https://azure.microsoft.com/pricing/details/lab-services/)voor meer informatie over prijzen.


### <a name="shared-image-gallery"></a>Galerie met gedeelde installatiekopieën

U moet ook rekening houden met de prijzen voor de service voor de galerie van gedeelde afbeeldingen als u van plan bent om gedeelde afbeeldings galerieën te gebruiken voor het opslaan en beheren van installatie kopieën. 

Het is gratis om een galerie met gedeelde afbeeldingen te maken en deze toe te voegen aan uw Lab-account. Er worden geen kosten in rekening gebracht totdat u een afbeeldings versie in de galerie opslaat. De prijzen voor het gebruik van een galerie met gedeelde afbeeldingen zijn normaal gesp roken redelijk te verwaarlozen, maar het is belang rijk om te begrijpen hoe het wordt berekend, omdat deze niet is opgenomen in de prijzen voor Azure Lab Services.  

#### <a name="storage-charges"></a>Opslag kosten

Voor het opslaan van installatie kopie versies gebruikt een galerie met gedeelde installatie kopieën standaard harde-schijf stations (HDD), Managed disks. De grootte van de schijf die wordt gebruikt voor de HDD, is afhankelijk van de grootte van de installatie kopie versie die wordt opgeslagen. Zie [prijzen van Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/)voor meer informatie over prijzen.

#### <a name="replication-and-network-egress-charges"></a>Kosten voor replicatie en netwerk uitgaand verkeer

Wanneer u een versie van een installatie kopie opslaat met behulp van een Lab-sjabloon-VM, Azure Lab Services deze eerst opgeslagen in een bron regio en wordt de versie van de bron installatie kopie automatisch gerepliceerd naar een of meer doel regio's. 

Het is belang rijk te weten dat Azure Lab Services de versie van de bron installatie kopie automatisch repliceert naar alle [doel regio's binnen de geografie](https://azure.microsoft.com/global-infrastructure/regions/) waar het lab zich bevindt. Als uw Lab zich bijvoorbeeld in de Verenigde Staten bevindt, wordt er een afbeeldings versie gerepliceerd naar elk van de acht regio's in de VS.

Wanneer een versie van een installatie kopie wordt gerepliceerd van de bron regio naar extra doel regio's, treedt er een uitvulling van het netwerk op. De kosten worden berekend op basis van de grootte van de versie van de installatie kopie wanneer de gegevens van de afbeelding in eerste instantie uitgaande van de bron regio worden verzonden.  Zie [prijs informatie voor band breedte](https://azure.microsoft.com/pricing/details/bandwidth/)voor meer informatie over prijzen.

Voor uitstaande kosten kunnen klanten van [onderwijs oplossingen](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) worden kwijtgescholden. Neem contact op met uw account manager voor meer informatie. 

Zie "welke Program ma's voor gegevens overdracht bestaan voor academische klanten en hoe kom ik in aanmerking?" voor meer informatie. in het gedeelte Veelgestelde vragen van de pagina [Program ma's voor onderwijs instellingen](https://azure.microsoft.com/pricing/details/bandwidth/) .

#### <a name="pricing-example"></a>Prijs voorbeeld

Laten we eens kijken naar een voor beeld van de kosten voor het opslaan van een sjabloon VM-installatie kopie naar een galerie met gedeelde installatie kopieën. Stel dat de volgende scenario's zijn:

- U hebt één aangepaste VM-installatie kopie.
- U slaat twee versies van de installatie kopie op.
- Uw lab bevindt zich in de Verenigde Staten, met in totaal acht regio's.
- Elke afbeeldings versie is 32 GB groot. Als gevolg hiervan is de prijs voor de schijf met harde schijven $1,54 per maand.

De totale kosten per maand worden geschat als:

* *Aantal installatie kopieën aantal &times; versies &times; van aantal door Replicas &times; beheerde schijf prijzen = totale kosten per maand*

In dit voor beeld zijn de kosten:

* 1 aangepaste installatie kopie (32 GB) &times; 2 versies &times; 8 amerikaanse regio's &times; $1,54 = $24,64 per maand

> [!NOTE]
> De voor gaande berekening is alleen bedoeld als voor beeld. Dit omvat opslag kosten die zijn gekoppeld aan het gebruik van de galerie met gedeelde afbeeldingen en bevat *geen* uitgangs kosten. Zie [Managed disks prijzen](https://azure.microsoft.com/en-us/pricing/details/managed-disks/)voor de werkelijke prijzen voor opslag.

#### <a name="cost-management"></a>Kostenbeheer

Het is belang rijk dat Lab-account beheerders de kosten beheren door de niet-vereiste installatie kopieën in de galerie regel matig te verwijderen. 

Verwijder replicatie niet naar specifieke regio's als een manier om de kosten te verlagen, hoewel deze optie in de galerie met gedeelde afbeeldingen voor komt. Replicatie wijzigingen kunnen een nadelige invloed hebben op de mogelijkheid van Azure Lab Services om Vm's te publiceren vanuit afbeeldingen die zijn opgeslagen in een galerie met gedeelde afbeeldingen.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het instellen en beheren van Labs:

- [Installatie handleiding voor het lab-account](account-setup-guide.md)  
- [Hand leiding voor Lab-installatie](setup-guide.md)  
- [Kostenbeheer voor labs](cost-management-guide.md)  
- [Azure Lab Services gebruiken in teams](lab-services-within-teams-overview.md)
