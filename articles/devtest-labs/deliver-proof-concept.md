---
title: Een proef van concept-Azure DevTest Labs leveren | Microsoft Docs
description: Leer hoe u een proef concept kunt leveren, zodat Azure DevTest Labs met succes kan worden opgenomen in een bedrijfs omgeving.
ms.topic: article
ms.date: 06/2/2020
ms.openlocfilehash: 9c28cf9eebd8a39a2edce48e4fb8b96dc7608d80
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87288028"
---
# <a name="deliver-a-proof-of-concept"></a>Een proef van het concept leveren 

Een van de belangrijkste scenario's voor Azure DevTest Labs is het inschakelen van ontwikkel-en test omgevingen in de Cloud. Enkele voorbeelden:

* Ontwikkel aars Bureau bladen maken in de Cloud.
* Omgevingen configureren voor testen.
* Het inschakelen van toegang tot virtuele machines en andere Azure-resources.
* Het instellen van een sandboxgebied voor ontwikkel aars om te leren en te experimenteren.

DevTest Labs biedt ook de beleids regels voor de Guardrails en kosten om de onderneming in staat te stellen om "zelf bedienstende Azure" te bieden aan ontwikkel aars die voldoen aan de beveiligings-, regelgevings-en nalevings beleidsregels van het bedrijf. 

Elke onderneming heeft verschillende vereisten voor de manier waarop Azure DevTest Labs kunnen worden opgenomen in hun omgeving. In dit artikel worden de meest voorkomende stappen beschreven die ondernemingen moeten volt ooien om te zorgen voor een geslaagde test van het concept. Een testen van het concept is de eerste stap naar een geslaagde end-to-end-implementatie. 

## <a name="getting-started"></a>Aan de slag 

Om aan de slag te gaan met het leveren van een concept. Het is belang rijk om enige tijd te best Eden aan meer informatie over Azure en DevTest Labs.  Hier volgen enkele start bronnen: 

* [Wat is de Azure Portal?](https://azure.microsoft.com/features/azure-portal/)
* [Basis beginselen van DevTest Labs](devtest-lab-overview.md)
* [Ondersteunde scenario's van DevTest Labs](devtest-lab-guidance-get-started.md)
* [Enter prise-documentatie voor DevTest Labs](devtest-lab-guidance-prescriptive-adoption.md)
* [Inleiding tot Azure-netwerken](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Vereisten 

Er zijn enkele vereisten voor het volt ooien van een pilot of proef concept met DevTest Labs: 

* **Azure-abonnement**: ondernemingen beschikken vaak over een bestaande [Enterprise overeenkomst](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) die toegang tot Azure mogelijk maakt en ze kunnen een bestaand of nieuw abonnement voor DevTest Labs gebruiken. Ondernemingen kunnen ook een [Visual Studio-abonnement](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) gebruiken tijdens de pilot (profiteren van de gratis Azure-tegoed). Als geen van deze opties beschikbaar is, kan een onderneming een [gratis Azure-account](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ)maken en gebruiken. Als er een Enterprise Overeenkomst is, is het gebruik van een [Enterprise dev/test-abonnement](https://azure.microsoft.com/offers/ms-azr-0148p/) een uitstekende optie om toegang te krijgen tot Windows 10/Windows 8,1 client-besturings systemen en kortings tarieven voor werk belastingen voor ontwikkelen en testen. 
* **Azure Active Directory Tenant**: als u gebruikers wilt beheren (bijvoorbeeld het toevoegen van gebruikers of het toevoegen van Lab-eigen aars), moeten die gebruikers deel uitmaken van de [Azure Active Directory Tenant](https://azure.microsoft.com/services/active-directory/) die wordt gebruikt in het Azure-abonnement voor de pilot. Vaak wordt een [hybride identiteit](../active-directory/hybrid/whatis-hybrid-identity.md) ingesteld om gebruikers in staat te stellen hun on-premises identiteit in de cloud te gebruiken, maar dit is niet vereist voor de DevTest Labs-pilot. 

## <a name="scoping-of-the-pilot"></a>Bereik van de pilot 

Het is belang rijk een pilot te plannen voordat u de implementatie start. U weet vooraf dat de resources niet voor onbepaalde tijd de juiste verwachtingen voor gebruikers van de pilot blijven instellen. 

> [!IMPORTANT]
> Er kan niet worden benadrukt dat het belang rijk is dat de pilot en het instellen van verwachtingen op de voor grond duidelijk worden gebereikd.

Beantwoord de volgende belang rijke vragen voordat u de pilot start: 

* Wat wilt u weten, en wat is het resultaat van de pilot? 
* Welke workloads of scenario's worden behandeld in de pilot? Het is belang rijk dat u slechts een kleine set definieert, om ervoor te zorgen dat de pilot kan worden beperkt en snel kan worden voltooid. 
* Welke resources moeten beschikbaar zijn in het lab? Bijvoorbeeld: aangepaste installatie kopieën, installatie kopieën van Marketplace, beleids regels, netwerk topologie. 
* Welke gebruikers en teams zijn er betrokken bij de pilot om de ervaring te controleren?  
* Wat is de duur van de pilot? Kies een tijds bestek dat goed is afgestemd op het geplande bereik, bijvoorbeeld twee weken of één maand. 
* Wanneer de test is voltooid, wat gebeurt er dan met de toegewezen resources die tijdens de test zijn gebruikt? Wilt u de test bronnen verwijderen? U kunt denken:
   
   "Als we kunnen plannen dat de virtuele machines en Labs aan het einde van de pilot worden verwijderd, kunnen we een enkel abonnement voor de pilot instellen en al onze werkzaamheden uitvoeren terwijl we de vragen over de schaal implementatie parallel oplossen." 

Er is een algemene tendens om de Pilot ' perfect ' te maken, zodat het exact overeenkomt met wat de uiteindelijke status is nadat de service bij het bedrijf is getotaliseerd. Dit is een onjuiste hypo these. Hoe dichter u bij ' perfect ', hoe meer u moet doen *voordat* u aan de slag gaat met de pilot. Het doel van de pilot is om de juiste beslissingen te nemen over het omhoog en uitrollen van de eind service. 

De focus van de pilot moet de minimale vereiste werk belastingen en afhankelijkheden verzamelen om de vraag te beantwoorden of Azure DevTest Labs de juiste service voor uw bedrijf is. We raden u aan de eenvoudigste werk belasting met de minste afhankelijkheden te kiezen om snel en schoon te kunnen slagen. Als dat niet mogelijk is, kiest u een most representatieve werk belasting waarmee mogelijke complexe resultaten worden weer gegeven, zodat het succes van de test fase kan worden gerepliceerd in de scale-out-fase. 

In het volgende voor beeld ziet u een goed bereik van het concept. 

## <a name="example-proof-of-concept-plan"></a>Voor beeld: test-of-concept abonnement 

In deze sectie wordt een voor beeld weer gegeven dat u kunt gebruiken voor het bereik van een concept van de pilot voor DevTest Labs. 

> [!TIP]
> We raden u ten zeerste aan dat u het voor beeld dat in deze sectie wordt beschreven, niet overs laat om de kans te verkleinen dat het project wordt ingesteld op fouten. 

### <a name="overview"></a>Overzicht 

We plannen een nieuwe omgeving in azure te ontwikkelen op basis van DevTest Labs voor leveranciers om te gebruiken als een geïsoleerde omgeving van het bedrijfs netwerk. Om te bepalen of de oplossing aan de vereisten voldoet, zullen we een proef concept ontwikkelen om de end-to-end oplossing te valideren. We hebben verschillende leveranciers opgenomen om uit te proberen en de ervaring te verifiëren. 

### <a name="outcomes"></a>Resultaten 

Bij het bouwen van een concept kunt u het beste de resultaten richten (wat er wordt geprobeerd). Aan het einde van het concept testen verwachten we het volgende: 

* Een werkende end-to-end oplossing voor leveranciers om gast accounts in Azure Active Directory (Azure AD) te gebruiken om toegang te krijgen tot een geïsoleerde omgeving in Azure. De omgeving heeft de benodigde resources om productief te zijn. 
* Mogelijke problemen die van invloed zijn op grotere schaal baarheid en goed keuring worden opgesomd en begrepen.
* De personen die betrokken zijn bij het ontwikkelen van het concept testen, hebben een goed begrip van alle code. Ze begrijpen ook het betrokken onderpand en zijn in ruimere goed keuring.

### <a name="open-questions-and-prerequisites"></a>Open vragen en vereisten 

* Is er een abonnement gemaakt dat kan worden gebruikt voor dit project? 
* Hebben we een Azure AD-Tenant en een Azure AD Global-beheerder geïdentificeerd wie hulp en richt lijnen kunnen bieden voor Azure AD-gerelateerde vragen? 
* Is er een plek om samen te werken voor de personen die aan het project werken? 

   * Bron code en scripts (zoals Azure opslag plaatsen) 
   * Documenten (zoals micro soft teams of share point)  
   * Discussies (zoals micro soft-teams) 
   * Werk items (zoals Azure-boards) 
* Wat zijn de vereiste resources voor leveranciers? Dit omvat toepassingen die beschikbaar zijn op het netwerk, zowel lokaal op de virtuele machines als op andere vereiste servers. 
* Worden de virtuele machines gekoppeld aan een domein in azure? Als dit het geval is, wordt dit Azure Active Directory Domain Services (Azure AD DS) of iets anders? 
* Hebben we het team of de leveranciers geïdentificeerd die het doel zijn van het concept testen? Wie zijn de klanten voor de omgeving?
* Welke Azure-regio wordt gebruikt voor het testen van het concept? 
* Hebben we een lijst met services die de leveranciers mogen gebruiken via DevTest Labs naast IaaS (Vm's)? 
* Hoe plan ik leveranciers/gebruikers bij het gebruik van het lab? 

### <a name="components-of-the-proof-of-concept-solution"></a>Onderdelen van de test-of-concept oplossing 

We verwachten dat de oplossing de volgende onderdelen bevat: 

* Verschillende leveranciers teams gebruiken een set Labs in Azure.
* De laboratoria zijn verbonden met een netwerk infrastructuur die ondersteuning biedt voor de vereisten.
* De leveranciers hebben toegang tot de Labs via Azure AD en roltoewijzingen.
* Leveranciers kunnen verbinding maken met hun resources. Met name met een site-naar-site-VPN kunt u rechtstreeks toegang krijgen tot virtuele machines zonder open bare IP-adressen.
* Een set artefacten behandelt de vereiste software die de leveranciers op de virtuele machines nodig hebben.

## <a name="additional-planning-and-design-decisions"></a>Aanvullende plannings-en ontwerp beslissingen 

Voordat u een volledige DevTest Labs-oplossing uitgeeft, moet u enkele belang rijke plannings-en ontwerp beslissingen nemen. De ervaring van het werken met een concept kan u helpen bij het nemen van deze beslissingen. Verdere overwegingen zijn onder andere: 

* **Topologie van abonnementen**: de vereisten op ondernemings niveau voor resources in azure kunnen buiten de [beschik bare quota's binnen één abonnement](../azure-resource-manager/management/azure-subscription-service-limits.md)worden uitgebreid. Hiervoor zijn meerdere Azure-abonnementen en/of service aanvragen vereist om de initiële abonnements limieten te verhogen. Het is belang rijk om te bepalen hoe u resources binnen verschillende abonnementen distribueert. Een waardevol middel is de [hand leiding voor abonnements beslissingen](/azure/architecture/cloud-adoption/decision-guides/subscriptions/) , omdat het lastig is om resources later te verplaatsen naar een ander abonnement. Een Lab kan bijvoorbeeld niet worden verplaatst naar een ander abonnement nadat het is gemaakt.  
* **Netwerk topologie**: de [standaard netwerk infrastructuur](../app-service/networking-features.md) die DevTest Labs automatisch maakt, voldoet mogelijk niet voldoende om te voldoen aan de vereisten en beperkingen voor de zakelijke gebruikers. Het is gebruikelijk om [Azure ExpressRoute Connected Virtual Networks](/azure/architecture/reference-architectures/hybrid-networking/), [hub en spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) te zien voor connectiviteit tussen abonnementen en zelfs [geforceerde route ring](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) om alleen lokale connectiviteit te garanderen. Met DevTest Labs kunnen bestaande virtuele netwerken met het lab worden verbonden om hun gebruik te kunnen maken wanneer u nieuwe virtuele machines in het Lab maakt. 
* **Externe toegang tot virtuele machines**: er zijn veel opties om op afstand toegang te krijgen tot de virtuele machines die zich bevinden in DevTest Labs. Het eenvoudigste is het gebruik van open bare Ip's of gedeelde open bare Ip's. Dit zijn [de instellingen die beschikbaar zijn in het lab](devtest-lab-shared-ip.md). Als deze opties niet voldoende zijn, is het gebruik van een RAS-gateway ook een optie. Deze optie wordt weer gegeven op de [DevTest Labs Enter prise-referentie architectuur](devtest-lab-reference-architecture.md) en wordt verder beschreven in de documentatie van de [extern bureau blad-gateway van DevTest Labs](configure-lab-remote-desktop-gateway.md). Ondernemingen kunnen ook ExpressRoute of een site-naar-site-VPN gebruiken om hun Labs te verbinden met hun on-premises netwerk. Met deze optie kunt u rechtstreeks extern bureau blad-of SSH-verbindingen naar de virtuele machines maken op basis van hun privé-IP-adres zonder bloot stelling aan Internet. 
* **Machtigingen voor verwerking**: de twee belang rijke machtigingen die veel worden gebruikt in DevTest Labs zijn [eigenaar en Lab-gebruiker](devtest-lab-add-devtest-user.md). Het is belang rijk om te beslissen voordat u DevTest Labs globaal uitrollen, die met elk toegangs niveau in het lab wordt toevertrouwd. Een gemeen schappelijk model is de budget eigenaar (bijvoorbeeld team lead) als Lab-eigenaar en de team leden als Lab-gebruikers. Met dit model kan de persoon (team leider) die verantwoordelijk is voor het budget, de beleids instellingen aanpassen en het team binnen het budget blijven.  

## <a name="completing-the-proof-of-concept"></a>Het testen van het concept volt ooien 

Nadat de verwachte informatie is behandeld, is het tijd om de pilot uit te voeren. Dit is de tijd om feedback van de gebruikers te verzamelen, te bepalen of de test is geslaagd en te beslissen of de organisatie gaat door op een schaal bare implementatie van DevTest Labs in de onderneming. Het is ook een goed idee om de implementatie van DevTest Labs en bijbehorende resources te automatiseren om consistentie in de schaal implementatie te garanderen. 

## <a name="next-steps"></a>Volgende stappen 

* [Enter prise-documentatie voor DevTest Labs](devtest-lab-guidance-prescriptive-adoption.md)
* [Referentie architectuur voor een onderneming](devtest-lab-reference-architecture.md)
* [Uw DevTest Labs-implementatie omhoog schalen](devtest-lab-guidance-orchestrate-implementation.md)
* [De implementatie van Azure DevTest Labs organiseren](devtest-lab-guidance-orchestrate-implementation.md)
