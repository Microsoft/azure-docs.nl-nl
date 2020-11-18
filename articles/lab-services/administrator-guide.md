---
title: Azure Lab Services-beheerders handleiding | Microsoft Docs
description: Deze hand leiding helpt beheerders die Lab-accounts maken en beheren met behulp van Azure Lab Services.
ms.topic: article
ms.date: 10/20/2020
ms.openlocfilehash: b1fadc58926b00c75ab888dad86e45b181059a38
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659842"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services-beheerders handleiding
IT-beheerders die de cloud resources van een universiteit beheren, zijn doorgaans verantwoordelijk voor het instellen van het lab-account voor hun school. Zodra een Lab-account is ingesteld, maken beheerders of docenten Labs die zijn opgenomen in het lab-account. Dit artikel bevat een overzicht op hoog niveau van de betrokken Azure-resources en de richt lijnen voor het maken van deze.

![Weer gave op hoog niveau van Azure-resources in een Lab-account](./media/administrator-guide/high-level-view.png)

- Labs worden gehost in een Azure-abonnement dat eigendom is van Azure Lab Services.
- Lab-accounts, Galerie met gedeelde afbeeldingen en installatie kopie versies worden gehost in uw abonnement.
- U kunt uw Lab-account en de galerie met gedeelde installatie kopieën in dezelfde resource groep hebben. In dit diagram bevinden ze zich in verschillende resource groepen.

Meer informatie over de architectuur vindt u in het artikel: [beginselen](./classroom-labs-fundamentals.md) van de architectuur van Labs

## <a name="subscription"></a>Abonnement
Uw universiteit heeft een of meer Azure-abonnementen. Een abonnement wordt gebruikt voor het beheren van facturering en beveiliging voor alle Azure-resources\services die erin worden gebruikt, met inbegrip van Lab-accounts.

De relatie tussen een Lab-account en het abonnement is belang rijk omdat:

- Facturering wordt gerapporteerd via het abonnement dat het lab-account bevat.
- U kunt gebruikers in de Tenant toegang van de Azure Active Directory (AD) van het abonnement geven om Azure Lab Services. U kunt een gebruiker toevoegen als een Lab-account owner\contributor, een Lab-maker of een Lab-eigenaar.

Labs en hun virtuele machines (Vm's) worden beheerd en worden gehost voor u binnen een abonnement dat eigendom is van Azure Lab Services.

## <a name="resource-group"></a>Resourcegroep
Een abonnement bevat een of meer resource groepen. Resource groepen worden gebruikt voor het maken van logische groeperingen van Azure-resources die samen worden gebruikt binnen dezelfde oplossing.  

Wanneer u een Lab-account maakt, moet u de resource groep configureren die het lab-account bevat. 

Een resource groep is ook vereist bij het maken van een [Galerie met gedeelde installatie kopieën](#shared-image-gallery). U kunt ervoor kiezen om uw Lab-account en de galerie met gedeelde afbeeldingen in twee afzonderlijke resource groepen te plaatsen, wat gebruikelijk is als u van plan bent de galerie met installatie kopieën te delen in verschillende oplossingen. U kunt er ook voor kiezen om deze in dezelfde resource groep te plaatsen.

Wanneer u een Lab-account maakt, kunt u automatisch een galerie met gedeelde installatie kopieën maken en koppelen.  Met deze optie worden het lab-account en de galerie met gedeelde afbeeldingen gemaakt in afzonderlijke resource groepen. U ziet dit gedrag wanneer u de stappen in deze zelf studie gebruikt: de [Galerie gedeelde afbeeldingen configureren op het moment dat het account wordt gemaakt](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). In de afbeelding boven aan dit artikel wordt deze configuratie ook gebruikt. 

U wordt aangeraden tijd vooraf in te zetten om de structuur van uw resource groepen te plannen, omdat het *niet* mogelijk is om de resource groep van een test account of de gedeelde installatie kopie galerie te wijzigen nadat deze is gemaakt. Als u de resource groep voor deze resources moet wijzigen, moet u de en/of-galerie met gedeelde afbeeldingen van uw Lab-account verwijderen en opnieuw maken.

## <a name="lab-account"></a>Lab-account

Een Lab-account fungeert als een container voor een of meer Labs. Wanneer u aan de slag gaat met Azure Lab Services, is het gebruikelijk dat u slechts één Lab-account hebt. U kunt later meer Lab-accounts maken naarmate uw test gebruik wordt geschaald.

De volgende lijst geeft een overzicht van scenario's waarin meer dan één Lab-account nuttig kan zijn:

- **Verschillende beleids vereisten in Labs beheren**

    Wanneer u een Lab-account instelt, stelt u beleids regels in die van toepassing zijn op *alle* Labs onder het lab-account, zoals:
    - Het virtuele Azure-netwerk met gedeelde bronnen waartoe het lab toegang heeft. Stel dat u een set Labs hebt die toegang nodig heeft tot een gedeelde gegevensset in een virtueel netwerk.
    - De virtuele machine (VM)-installatie kopieën die door de Labs kunnen worden gebruikt om virtuele machines te maken. Stel dat u een set Labs hebt die toegang nodig heeft tot de installatie kopie [van de data Science VM voor Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) Marketplace.

    Als u laboratoria hebt die unieke beleids vereisten van elkaar hebben, kan het nuttig zijn om afzonderlijke Lab-accounts te maken om deze Labs afzonderlijk te beheren.

- **Afzonderlijk budget per Lab-account**
  
    In plaats van alle Lab-kosten te rapporteren via één Lab-account, hebt u mogelijk een beter, gescheiden budget nodig. U kunt bijvoorbeeld Lab-accounts maken voor de reken kundige afdeling van uw universiteit, de afdeling computer wetenschappen, enzovoort, om het budget tussen afdelingen te scheiden.  U kunt vervolgens de kosten voor elk individueel Lab-account weer geven met behulp van [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md).

- **Prototype Labs isoleren van active\production Labs**
  
    Het kan voor komen dat u beleids wijzigingen voor een Lab-account wilt testen zonder dat dit van invloed is op active\production Labs. In dit type scenario kunt u met het maken van een afzonderlijke Lab-account voor test doeleinden wijzigingen isoleren. 

## <a name="lab"></a>Lab

Een Lab bevat virtuele machines (Vm's) die elk zijn toegewezen aan één student.  Over het algemeen kunt u het volgende verwachten:

- U hebt één Lab voor elke klasse.
- Maak elk semester een nieuwe set van Labs (of voor elke periode waarin uw klasse wordt aangeboden). Voor klassen die dezelfde installatie kopie nodig hebben, moet u meestal een [Galerie met gedeelde installatie](#shared-image-gallery) kopieën gebruiken om installatie kopieën in Labs en semesters opnieuw te gebruiken.

Houd rekening met de volgende punten bij het bepalen van de structuur van uw Labs:

- **Alle Vm's binnen een Lab worden geïmplementeerd met dezelfde installatie kopie die wordt gepubliceerd**

    Als u een klasse hebt waarvoor verschillende Lab-installatie kopieën moeten worden gepubliceerd, moeten er dus afzonderlijke Labs worden gemaakt.
  
- **Gebruiks quotum is ingesteld op het lab-niveau en is van toepassing op alle gebruikers in het lab**

    Als u verschillende quota wilt instellen voor gebruikers, moet u afzonderlijke Labs maken. Het is echter mogelijk om meer uren aan een specifieke gebruiker toe te voegen nadat u het quotum hebt ingesteld.
  
- **Het opstart-of afsluit schema is ingesteld op het lab-niveau en is van toepassing op alle virtuele machines in het lab**

    Net als bij het vorige punt, moet u, als u verschillende planningen wilt instellen voor gebruikers, afzonderlijke Labs maken.

Elk lab heeft standaard een eigen virtueel netwerk.  Als vnet-peering is ingeschakeld, heeft elk lab een eigen subnet dat is gekoppeld aan het opgegeven virtuele netwerk.

## <a name="shared-image-gallery"></a>Galerie met gedeelde afbeeldingen

Een galerie met gedeelde installatie kopieën is gekoppeld aan een Lab-account en fungeert als een centrale opslag plaats voor het opslaan van installatie kopieën. Een afbeelding wordt in de galerie opgeslagen wanneer een docent de sjabloon exporteert van de virtuele machine (VM) van een lab. Telkens wanneer een docent wijzigingen aanbrengt in de sjabloon-VM en-export, worden nieuwe versies van de installatie kopie opgeslagen tijdens het behoud van eerdere versies.

Docenten kunnen een installatie kopie versie publiceren vanuit de galerie met gedeelde afbeeldingen wanneer ze een nieuw lab maken. Hoewel in de galerie meerdere versies van een installatie kopie worden opgeslagen, kunnen docenten alleen de nieuwste versie selecteren tijdens het maken van het lab.

De galerie met gedeelde afbeeldingen is een optionele bron die u mogelijk niet direct nodig hebt bij het starten met slechts enkele Labs. Het gebruik van de galerie met gedeelde afbeeldingen heeft echter veel voor delen die handig zijn wanneer u schaalt naar meer Labs:

- **Hiermee kunt u versies van een sjabloon-VM-installatie kopie opslaan en beheren**

    Het is handig om een aangepaste installatie kopie te maken of wijzigingen (software, configuratie, enzovoort) door te voeren in een installatie kopie van de open bare Marketplace-galerie.  Het is bijvoorbeeld gebruikelijk dat docenten verschillende software\tooling moeten installeren. In plaats van te vereisen dat cursisten hun eigen vereisten hand matig installeren, kunnen verschillende versies van de sjabloon-VM-installatie kopie worden geëxporteerd naar een galerie met gedeelde installatie kopieën. Deze installatie kopie versies kunnen vervolgens worden gebruikt bij het maken van nieuwe Labs.
- **Hiermee wordt sharing\reuse van sjabloon-VM-installatie kopieën in Labs ingeschakeld**

    U kunt een installatie kopie opslaan en opnieuw gebruiken zodat u niet elke keer dat u een nieuw Lab maakt de installatie kopie hoeft te configureren. Als er bijvoorbeeld meerdere klassen worden aangeboden die dezelfde installatie kopie nodig hebben, hoeft deze installatie kopie slechts eenmaal te worden gemaakt en naar de galerie met gedeelde afbeeldingen te worden geëxporteerd, zodat deze kan worden gedeeld in Labs.
- **Hiermee wordt de beschik baarheid van afbeeldingen via replicatie gegarandeerd**

    Wanneer u vanuit een Lab opslaat in de galerie met gedeelde afbeeldingen, wordt uw installatie kopie automatisch gerepliceerd naar andere [regio's binnen dezelfde geografie](https://azure.microsoft.com/global-infrastructure/regions/). Als er een storing optreedt in een regio, is het publiceren van de installatie kopie naar uw Lab niet van invloed omdat een installatie kopie replica van een andere regio kan worden gebruikt.  Het publiceren van Vm's vanuit meerdere replica's kan ook helpen bij de prestaties.

U hebt een aantal opties om gedeelde installatie kopieën logisch te groeperen:

- Meerdere gemeen schappelijke afbeeldings galerieën maken. Elk lab-account kan alleen verbinding maken met één gedeelde installatie kopie galerie. Daarom moet u voor deze optie ook meerdere Lab-accounts maken.
- U kunt ook één gedeelde installatie kopie galerie gebruiken die wordt gedeeld door meerdere Lab-accounts. In dit geval kan elk lab-account alleen de installatie kopieën inschakelen die van toepassing zijn op de Labs die het bevat.

## <a name="naming"></a>Naamgeving

Wanneer u aan de slag gaat met Azure Lab Services, raden we u aan om naam conventies voor resource groepen, Lab-accounts, Labs en de galerie met gedeelde afbeeldingen te bepalen. Hoewel de naamgevings regels die u instelt, uniek zijn voor de behoeften van uw organisatie, wordt in de volgende tabel een overzicht gegeven van de algemene richt lijnen.

| Resourcetype | Rol | Voorgesteld patroon | Voorbeelden |
| ------------- | ---- | ----------------- | -------- | 
| Resourcegroep | Bevat een of meer Lab-accounts en een of meer galerie met gedeelde afbeeldingen | \<organization short name\>-\<environment\>-RG<ul><li>**Korte naam** van de organisatie identificeert de naam van de organisatie die de resource groep ondersteunt</li><li>**Omgeving** identificeert de omgeving voor de resource, zoals pilot of productie</li><li>**RG** staat voor het resource type: resource groep.</li></ul> | contosouniversitylabs-RG<br/>contosouniversitylabs-pilot-RG<br/>contosouniversitylabs-Prod-RG |
| Lab-account | Bevat een of meer Labs | \<organization short name\>-\<environment\>-La<ul><li>**Korte naam** van de organisatie identificeert de naam van de organisatie die de resource groep ondersteunt</li><li>**Omgeving** identificeert de omgeving voor de resource, zoals pilot of productie</li><li>**La** staat voor het resource type: Lab-account.</li></ul> | contosouniversitylabs-La<br/>mathdeptlabs-La<br/>sciencedeptlabs-pilot-La<br/>sciencedeptlabs-Prod-La |
| Lab | Bevat een of meer virtuele machines |\<class name\>-\<timeframe\>-\<educator identifier\><ul><li>**Klassenaam** identificeert de naam van de klasse die door het lab wordt ondersteund.</li><li>**Tijds bestek** duidt de periode aan waarin de klasse wordt aangeboden.</li>**Onderwijs-id** identificeert de docenten die eigenaar zijn van het lab.</li></ul> | CS1234-fall2019-JANJANSEN<br/>CS1234-spring2019-JANJANSEN |
| Galerie met gedeelde afbeeldingen | Bevat een of meer versies van VM-installatie kopieën | \<organization short name\>sjablonen | contosouniversitylabsgallery |

Zie [naamgevings conventies voor Azure-resources](/azure/architecture/best-practices/naming-conventions)voor meer informatie over het benoemen van andere Azure-resources.

## <a name="regionslocations"></a>Regions\locations

Wanneer u de resources van uw Azure Lab Services instelt, moet u een regio (of locatie) opgeven van het Data Center dat als host fungeert voor de resource. Hier vindt u meer informatie over de invloed van de regio op elk van de resources die betrokken zijn bij het instellen van een lab.

### <a name="resource-group"></a>Resourcegroep

In de regio wordt het data centrum opgegeven waarin informatie over de resource groep wordt opgeslagen. Azure-resources die deel uitmaken van de resource groep, kunnen zich in verschillende regio's van hun bovenliggende regio bevinden.

### <a name="lab-account"></a>Lab-account

De locatie van een test account geeft aan in welke regio deze resource zich bevindt.  

### <a name="lab"></a>Lab

De locatie waar een Lab zich bevindt, is afhankelijk van de volgende factoren:

  - **Het lab-account is gekoppeld aan een virtueel netwerk (VNet)**
  
    Een Lab-account kan worden [gekoppeld aan een VNet](./how-to-connect-peer-virtual-network.md) wanneer ze zich in dezelfde regio bevinden.  Wanneer een Lab-account is gekoppeld aan een VNet, worden er automatisch Labs gemaakt in dezelfde regio als het lab-account en het VNet.

    > [!NOTE]
    > Wanneer een Lab-account is gekoppeld aan een VNet, wordt de instelling voor het kiezen van Lab- **Maker voor de optie Lab-locatie toestaan** uitgeschakeld. Meer informatie over deze instelling vindt u in het artikel: [Lab Creator toestaan om de locatie voor het lab te kiezen](./allow-lab-creator-pick-lab-location.md).
    
  - * * Er zijn geen VNet-peered **_en_* _ Lab-makers mogen geen lab-location_ kiezen.
  
    Als er **geen** VNet is dat is gekoppeld aan het lab-account *en* [Lab-makers mogen de Lab-locatie **niet** kiezen](./allow-lab-creator-pick-lab-location.md), worden er automatisch Labs gemaakt in een regio die beschik bare VM-capaciteit heeft.  Met name Azure Lab Services zoekt naar Beschik baarheid in [regio's die zich binnen dezelfde geografie bevinden als het lab-account](https://azure.microsoft.com/global-infrastructure/regions).

  - * * Er zijn geen VNet-peered **_en_* _ Lab-makers mogen de test omgeving kiezen location_ *
       
    Als er **geen** VNet-peered en [Lab-makers zijn toegestaan om de Lab-locatie te kiezen](./allow-lab-creator-pick-lab-location.md), zijn de locaties die kunnen worden geselecteerd door de Lab-Maker gebaseerd op de beschik bare capaciteit.

> [!NOTE]
> Om ervoor te zorgen dat er voldoende VM-capaciteit voor een regio is, is het belang rijk dat u eerst capaciteit aanvraagt via het lab-account of bij het maken van het lab.

Een algemene regel is het instellen van de regio van een resource die het dichtst bij de gebruikers ligt. Dit houdt in dat het lab dat het dichtst bij uw studenten ligt, wordt gemaakt. Voor online cursussen waar studenten zich overal ter wereld bevinden, moet u uw beste arrest gebruiken om een lab te maken dat zich centraal bevindt. U kunt ook een klasse in meerdere Labs splitsen op basis van de regio van uw studenten.

### <a name="shared-image-gallery"></a>Galerie met gedeelde afbeeldingen

De regio geeft de bron regio aan waar de eerste versie van de installatie kopie wordt opgeslagen voordat deze automatisch wordt gerepliceerd naar doel regio's.

## <a name="vm-sizing"></a>VM-grootte

Wanneer beheerders of Lab-makers een lab maken, kunnen ze kiezen uit de volgende VM-grootten op basis van de behoeften van hun klas lokaal. Houd er rekening mee dat de beschik bare reken grootten afhankelijk zijn van de regio waarin uw Lab-account zich bevindt:

| Grootte | Specificaties | Reeks | Voorgesteld gebruik |
| ---- | ----- | ------ | ------------- |
| Klein| <ul><li>2 kernen</li><li>3,5 GB RAM-GEHEUGEN</li> | [Standard_A2_v2](../virtual-machines/av2-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) | Deze grootte is het meest geschikt voor de opdracht regel, de webbrowser openen, webservers met weinig verkeer, kleine tot middel grote data bases. |
| Normaal | <ul><li>4 kernen</li><li>7 GB RAM-GEHEUGEN</li> | [Standard_A4_v2](../virtual-machines/av2-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) | Deze grootte is het meest geschikt voor relationele data bases, caching in het geheugen en analyse. |
| Gemiddeld (geneste virtualisatie) | <ul><li>4 kernen</li><li>16 GB RAM-GEHEUGEN</li></ul> | [Standard_D4s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json#dsv3-series) | Deze grootte is het meest geschikt voor relationele data bases, caching in het geheugen en analyse.
| Groot | <ul><li>8 kernen</li><li>16 GB RAM-GEHEUGEN</li></ul>  | [Standard_A8_v2](../virtual-machines/av2-series.md) | Deze grootte is het meest geschikt voor toepassingen die snellere Cpu's nodig hebben, betere prestaties van de lokale schijf, grote data bases, grote geheugen caches.  Deze grootte biedt ook ondersteuning voor geneste virtualisatie. |
| Groot (geneste virtualisatie) | <ul><li>8 kernen</li><li>32 GB RAM</li></ul>  | [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json#dsv3-series) | Deze grootte is het meest geschikt voor toepassingen die snellere Cpu's nodig hebben, betere prestaties van de lokale schijf, grote data bases, grote geheugen caches. |
| Kleine GPU (visualisatie) | <ul><li>6 kernen</li><li>56 GB RAM-GEHEUGEN</li>  | [Standard_NV6](../virtual-machines/nv-series.md) | Deze grootte is het meest geschikt voor externe visualisatie, streaming, games, code ring met behulp van frameworks zoals OpenGL en DirectX. |
| Kleine GPU (Compute) | <ul><li>6 kernen</li><li>56 GB RAM-GEHEUGEN</li></ul>  | [Standard_NC6](../virtual-machines/nc-series.md) |Deze grootte is het meest geschikt voor computer-intensieve toepassingen, zoals kunst matige intelligentie en diep gaande lessen. |
| Gemiddelde GPU (visualisatie) | <ul><li>12 kernen</li><li>112 GB RAM-GEHEUGEN</li></ul>  | [Standard_NV12](../virtual-machines/nv-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) | Deze grootte is het meest geschikt voor externe visualisatie, streaming, games, code ring met behulp van frameworks zoals OpenGL en DirectX. |

## <a name="manage-identity"></a>Identiteit beheren

Met behulp [van Azure RBAC (op rollen gebaseerd toegangs beheer)](../role-based-access-control/overview.md)kunnen de volgende rollen worden toegewezen om toegang te geven tot Lab-accounts en-Labs:

- **Labeigenaar**

    De beheerder die het lab-account maakt, wordt automatisch toegevoegd aan de rol **eigenaar** van het lab-account.  Een beheerder die de rol **eigenaar** heeft toegewezen, kan:
     - De instellingen van het lab-account wijzigen.
     - Geef andere beheerders toegang tot het lab-account als eigen aren of mede werkers.
     - Bied docenten toegang tot Labs als makers, eigen aren of mede werkers.
     - Maak en beheer alle Labs in het lab-account.

- **Inzender voor Lab-account**

    Een beheerder die de rol **Inzender** heeft toegewezen, kan:
    - De instellingen van het lab-account wijzigen.
    - Alle Labs binnen het lab-account maken en beheren.

    Ze kunnen echter *geen* andere gebruikers toegang geven tot Lab-accounts of Labs.

- **Labmaker**

    Als u Labs wilt maken binnen een Lab-account, moeten docenten lid zijn van de rol **Lab Creator** .  Wanneer een docent een Lab maakt, worden deze automatisch toegevoegd als een eigenaar van het lab.  Raadpleeg de zelf studie over het [toevoegen van een gebruiker aan de rol **Lab Creator**](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role). 

- **Lab-owner\contributor**
  
    Een docent kan de instellingen van een Lab weer geven en wijzigen wanneer deze lid zijn van de rol **eigenaar** of **Inzender** van een Lab; ze moeten ook lid zijn van de rol **lezer** van het lab-account.

    Een belang rijk verschil tussen de **eigenaar** van een lab en **Inzender** rollen is dat een mede werker andere gebruikers *geen toegang kan* geven tot het beheer van de alleen de Lab-eigen aren kan andere gebruikers toegang geven tot het beheer van het lab.

    Daarnaast kan een docent *geen* nieuwe Labs maken, tenzij ze ook lid zijn van de rol **Lab Creator** .

- **Galerie met gedeelde afbeeldingen**

    Wanneer u een galerie met gedeelde installatie kopieën koppelt aan een Lab-account, krijgen owners\contributors en Lab creators\owners\contributors automatisch toegang tot het weer geven en opslaan van installatie kopieën in de galerie.

Hier volgen enkele tips voor het toewijzen van rollen:
   - Normaal gesp roken moeten alleen beheerders lid zijn van de **eigenaar** van een Lab-account of **Inzender** rollen. mogelijk hebt u meer dan één owner\contributor.
   - Om ervoor te zorgen dat een docent nieuwe Labs kan maken en de door hen gemaakte Labs kan beheren. u hoeft alleen toegang toe te wijzen aan de rol **Lab Creator** .
   - Om ervoor te zorgen dat een docent specifieke Labs kan beheren, maar *niet* de mogelijkheid om nieuwe Labs te maken. u moet toegang tot de rol **eigenaar** of **Inzender** toewijzen voor elk van de Labs die ze zullen beheren.  U kunt bijvoorbeeld zowel een docent als een onderwijs assistent toestaan om een lab te maken.  Raadpleeg de hand leiding voor het [toevoegen van een gebruiker als eigenaar van een Lab](./how-to-add-user-lab-owner.md).

## <a name="pricing"></a>Prijzen

### <a name="azure-lab-services"></a>Azure Lab-Services

De prijzen voor Azure Lab Services worden beschreven in het volgende artikel: [Azure Lab Services prijzen](https://azure.microsoft.com/pricing/details/lab-services/).

U moet ook rekening houden met de prijzen voor de galerie met gedeelde afbeeldingen als u deze wilt gebruiken voor het opslaan en beheren van installatie kopieën. 

### <a name="shared-image-gallery"></a>Galerie met gedeelde afbeeldingen

Het is gratis om een galerie met gedeelde afbeeldingen te maken en deze toe te voegen aan uw Lab-account. Er worden geen kosten in rekening gebracht totdat u een afbeeldings versie in de galerie opslaat. Normaal gesp roken is de prijs voor het gebruik van een galerie met gedeelde afbeeldingen redelijk te verwaarlozen, maar het is belang rijk om te begrijpen hoe deze wordt berekend omdat deze niet is opgenomen in de prijzen voor Azure Lab Services.  

#### <a name="storage-charges"></a>Opslag kosten

Voor het opslaan van afbeeldings versies gebruikt een galerie met gedeelde installatie kopieën standaard schijven die met schijven worden beheerd. De grootte van de schijf die wordt gebruikt voor de HDD, is afhankelijk van de grootte van de installatie kopie versie die wordt opgeslagen. Raadpleeg het volgende artikel voor het weer geven van de prijzen: [prijs informatie Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).

#### <a name="replication-and-network-egress-charges"></a>Kosten voor replicatie en netwerk uitgaand verkeer

Wanneer u een versie van een installatie kopie opslaat met behulp van de virtuele machine (VM) van een lab, wordt deze door Azure Lab Services eerst opgeslagen in een bron regio en wordt de versie van de bron installatie kopie automatisch gerepliceerd naar een of meer doel regio's. Het is belang rijk te weten dat Azure Lab Services de versie van de bron installatie kopie automatisch repliceert naar alle doel [regio's binnen de geografie](https://azure.microsoft.com/global-infrastructure/regions/) waar het lab zich bevindt. Als uw Lab zich bijvoorbeeld in de Verenigde Staten bevindt, wordt er een afbeeldings versie gerepliceerd naar elk van de acht regio's in de VS

Wanneer een versie van een installatie kopie wordt gerepliceerd van de bron regio naar extra doel regio's, treedt er een uitvulling van het netwerk op. De kosten worden berekend op basis van de grootte van de versie van de installatie kopie wanneer de gegevens van de afbeelding in eerste instantie uitgaande van de bron regio worden verzonden.  Raadpleeg het volgende artikel voor prijs informatie: [prijs informatie voor band breedte](https://azure.microsoft.com/pricing/details/bandwidth/).

[Educatie-oplossingen](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) klanten kunnen worden kwijtgescholden van de kosten voor het betalen van uitgaand verkeer. Neem contact op met uw account manager voor meer informatie.  Zie voor meer informatie **Raadpleeg de sectie Veelgestelde vragen** in het gekoppelde document, met name de vraag "welke Program ma's voor gegevens overdracht bestaan voor academische klanten en hoe kom ik in aanmerking?".

#### <a name="pricing-example"></a>Prijs voorbeeld

Voor het samen vatting van de prijzen die hierboven worden beschreven, bekijken we een voor beeld van het opslaan van de VM-installatie kopie van de sjabloon naar de galerie met gedeelde installatie kopieën. Stel dat de volgende scenario's zijn:

- U hebt één aangepaste VM-installatie kopie.
- U slaat twee versies van de installatie kopie op.
- Uw lab bevindt zich in de Verenigde Staten, met in totaal acht regio's.
- Elke afbeeldings versie is 32 GB groot. Als gevolg hiervan is de prijs voor de schijf met harde schijven $1,54 per maand.

De totale kosten worden geschat als:

Aantal installatie kopieën × het aantal versies × het aantal replica's × de beheerde schijf prijs

In dit voor beeld zijn de kosten:

1 aangepaste installatie kopie (32 GB) x 2 versies x 8 Amerikaanse regio's x $1,54 = $24,64 per maand

#### <a name="cost-management"></a>Kostenbeheer

Het is belang rijk dat een Lab-account beheerder kosten kan beheren door de niet-vereiste installatie kopieën in de galerie regel matig te verwijderen. 

Het is niet mogelijk om replicatie naar specifieke regio's te verwijderen als een manier om de kosten te verlagen (deze optie komt voor in de galerie met gedeelde afbeeldingen). Wijzigingen in de replicatie kunnen nadelige gevolgen hebben voor de mogelijkheid van de Azure Lab-service om Vm's te publiceren vanuit afbeeldingen die zijn opgeslagen in een galerie met gedeelde installatie kopieën.

## <a name="next-steps"></a>Volgende stappen

De volgende stappen zijn gebruikelijk voor het instellen van een test omgeving.

- [Installatie handleiding voor het lab-account](account-setup-guide.md)
- [Hand leiding voor Lab-installatie](setup-guide.md)
- [Kostenbeheer voor labs](cost-management-guide.md)
- [Azure Lab Services binnen teams gebruiken](lab-services-within-teams-overview.md)