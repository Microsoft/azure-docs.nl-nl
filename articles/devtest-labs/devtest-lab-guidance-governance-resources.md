---
title: Governance van Azure DevTest Labs-infra structuur-resource
description: Dit artikel heeft betrekking op de uitlijning en het beheer van resources voor Azure DevTest Labs binnen uw organisatie.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1e470da5cd317d49f0d0734caa11eed6630d3f32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85480912"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---resources"></a>Governance van Azure DevTest Labs-infra structuur-bronnen
Dit artikel heeft betrekking op de uitlijning en het beheer van resources voor DevTest Labs binnen uw organisatie. 

## <a name="align-within-an-azure-subscription"></a>Uitlijnen binnen een Azure-abonnement 

### <a name="question"></a>Vraag
Hoe kan ik DevTest Labs-resources in een Azure-abonnement uitlijnen?

### <a name="answer"></a>Antwoord
Voordat een organisatie Azure voor algemene toepassings ontwikkeling begint te gebruiken, moeten IT-planners eerst nalezen hoe u de mogelijkheden kunt introduceren als onderdeel van hun algemene Port Folio van services. Gebieden voor beoordeling moeten de volgende problemen verhelpen:

- Hoe kan ik de kosten meten die zijn gekoppeld aan de levens cyclus van de toepassings ontwikkeling?
- Hoe lijnt de organisatie de voorgestelde service aanbieding uit met het beveiligings beleid van het bedrijf? 
- Is segmentatie vereist voor het scheiden van de ontwikkelings-en productie omgevingen? 
- Welke besturings elementen zijn er voor lange termijn gemak voor beheer, stabiliteit en groei?

De **eerste aanbevolen procedure** is het beoordelen van de Azure-taxonomie van organisaties waarbij de afdelingen tussen productie-en ontwikkelings abonnementen worden beschreven. In het volgende diagram maakt de voorgestelde taxonomie gebruik van een logische schei ding van ontwikkel-en test-en productie omgevingen. Met deze methode kan een organisatie facturerings codes introduceren voor het bijhouden van de kosten voor elke omgeving afzonderlijk. Zie voor meer informatie beheer van voorlichtend [abonnement](/azure/architecture/cloud-adoption/appendix/azure-scaffold). Daarnaast kunt u [Azure Tags](../azure-resource-manager/management/tag-resources.md) gebruiken om resources te organiseren voor tracking-en facturerings doeleinden.

De **tweede aanbevolen procedure** is het inschakelen van het DevTest-abonnement in de Azure Enter prise Portal. Hiermee kan een organisatie client besturingssystemen uitvoeren die normaal gesp roken niet beschikbaar zijn in een Azure-Bedrijfs abonnement. Gebruik vervolgens de Enter prise-software waarbij u alleen betaalt voor de reken kracht en u hoeft zich geen zorgen te maken over licenties. Het zorgt ervoor dat de facturering voor aangewezen services, inclusief galerij afbeeldingen in IaaS zoals Microsoft SQL Server, alleen is gebaseerd op verbruik. [Hier](https://azure.microsoft.com/offers/ms-azr-0148p/) vindt u meer informatie over het Azure DevTest-abonnement dat u hier kunt vinden voor [klanten van Enterprise overeenkomst](https://azure.microsoft.com/offers/ms-azr-0023p/) (EA) en hoe u deze voor betaalt.

![Resource-uitlijning met abonnementen](./media/devtest-lab-guidance-governance/resource-alignment-with-subscriptions.png)

Dit model biedt een organisatie de flexibiliteit om Azure DevTest Labs op schaal te implementeren. Een organisatie kan honderden Labs ondersteunen voor verschillende bedrijfs eenheden met 100 tot 1000 virtuele machines die parallel worden uitgevoerd. Hiermee wordt het principe van een gecentraliseerde onderneming Lab-oplossing gepromoot die dezelfde principes van configuratie beheer en beveiligings controles kan delen.

Dit model zorgt er ook voor dat de organisatie de resource limieten die zijn gekoppeld aan hun Azure-abonnement niet uitgeput. Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer informatie over de limieten voor abonnementen en services. Het DevTest Labs-inrichtings proces kan een groot aantal resource groepen gebruiken. U kunt aanvragen voor limieten die worden verhoogd via een ondersteunings aanvraag in het Azure DevTest-abonnement. De resources in het productie abonnement worden niet beïnvloed omdat het ontwikkel abonnement groeit. Zie [quota en limieten in DevTest Labs schalen](devtest-lab-scale-lab.md)voor meer informatie over het schalen van DevTest Labs.

Een algemene limiet voor abonnements niveau die moet worden verwerkt, is hoe de toewijzingen van het netwerk-IP-bereik worden toegewezen voor de ondersteuning van productie-en ontwikkelings abonnementen. Deze toewijzingen moeten rekening houdt met de groei in de loop van de tijd (uitgaande van on-premises connectiviteit of een andere netwerk topologie waarvoor de onderneming de netwerk stack moet beheren in plaats van standaard de implementatie van Azure). De aanbevolen procedure is om een aantal virtuele netwerken met een groot IP-adres voorvoegsel toe te wijzen en te delen met veel grote subnetten, in plaats van meerdere virtuele netwerken met kleine subnetten te hebben. Met 10 abonnementen kunt u bijvoorbeeld 10 virtuele netwerken definiëren (één voor elk abonnement). Alle Labs die geen isolatie vereisen, kunnen hetzelfde subnet delen in het vnet van het abonnement.

## <a name="maintain-naming-conventions"></a>Naam conventies onderhouden

### <a name="question"></a>Vraag
Hoe kan ik een naamgevings Conventie in mijn DevTest Labs-omgeving behouden?

### <a name="answer"></a>Antwoord
U kunt de huidige ondernemings naamgevings conventies uitbreiden naar Azure-bewerkingen en ze consistent maken in de DevTest Labs-omgeving.

Wanneer u DevTest Labs implementeert, is het raadzaam om specifiek start beleid te gebruiken. U implementeert dit beleid door een centraal script en JSON-sjabloon om consistentie af te dwingen. Naamgevings beleid kan worden geïmplementeerd via Azure-beleid dat wordt toegepast op het abonnements niveau. Zie Azure Policy-voor [beelden](../governance/policy/samples/index.md)voor json-voor beelden voor Azure Policy.

## <a name="number-of-users-per-lab-and-labs-per-organization"></a>Aantal gebruikers per Lab en Labs per organisatie

### <a name="question"></a>Vraag 
Hoe kan ik het bepalen van de verhouding van gebruikers per Lab en het totale aantal Labs dat nodig is voor een hele organisatie?

### <a name="answer"></a>Antwoord
We raden u aan om de bedrijfs units en ontwikkelings groepen die zijn gekoppeld aan hetzelfde ontwikkel project te koppelen aan hetzelfde lab. U kunt dezelfde typen beleids regels, installatie kopieën en afsluit beleid Toep assen op beide groepen. 

Mogelijk moet u ook geografische grenzen overwegen. Ontwikkel aars in het Noordoost-Verenigde Staten (Verenigde Staten) kunnen bijvoorbeeld een Lab gebruiken dat is ingericht in Oost-VS2. En ontwikkel aars in Amsterdam, Texas en Denver kunnen Colorado worden omgeleid om een resource in VS Zuid-Centraal te gebruiken. Als er sprake is van gezamenlijke inspanningen met een externe derde partij, kunnen ze worden toegewezen aan een lab dat niet wordt gebruikt door interne ontwikkel aars. 

U kunt ook een Lab voor een specifiek project in azure DevOps-projecten gebruiken. Vervolgens past u beveiliging toe via een opgegeven Azure Active Directory groep, waarmee u toegang kunt krijgen tot beide sets resources. Het virtuele netwerk dat aan het lab is toegewezen, kan een andere grens zijn om gebruikers te consolideren.

## <a name="deletion-of-resources"></a>Verwijdering van resources

### <a name="question"></a>Vraag
Hoe kan ik voor komen dat resources binnen een Lab worden verwijderd?

### <a name="answer"></a>Antwoord
We raden u aan om de juiste machtigingen op het lab-niveau in te stellen, zodat alleen geautoriseerde gebruikers resources kunnen verwijderen of het lab-beleid wijzigen. Ontwikkel aars moeten worden geplaatst in de **DevTest Labs-gebruikers** groep. De lead ontwikkelaar of de lead van de infra structuur moet de eigenaar van de **DevTest Labs** zijn. We raden u aan slechts twee Lab-eigen aars te hebben. Dit beleid strekt zich naar de code opslagplaats om beschadiging te voor komen. Lab maakt gebruik van rechten voor het gebruik van resources, maar kan geen Lab-beleids regels bijwerken. Raadpleeg het volgende artikel waarin de rollen en rechten worden vermeld die elke ingebouwde groep binnen een Lab heeft: [eigen aren en gebruikers toevoegen in azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="move-lab-to-another-resource-group"></a>Lab verplaatsen naar een andere resource groep 

### <a name="question"></a>Vraag
Wordt het ondersteund om een lab te verplaatsen naar een andere resource groep?

### <a name="answer"></a>Antwoord
Ja. Ga naar de pagina resource groep op de start pagina van uw Lab. Vervolgens selecteert u **verplaatsen** op de werk balk en selecteert u het lab dat u wilt verplaatsen naar een andere resource groep. Wanneer u een Lab maakt, wordt automatisch een resource groep voor u gemaakt. Het is echter mogelijk dat u het Lab wilt verplaatsen naar een andere resource groep die voldoet aan de naamgevings conventies voor ondernemingen. 

## <a name="next-steps"></a>Volgende stappen
Zie [kosten en eigendom beheren](devtest-lab-guidance-governance-cost-ownership.md).
