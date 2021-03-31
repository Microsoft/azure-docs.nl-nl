---
title: Azure Event Grid-partner evenementen
description: Verzend gebeurtenissen van derden Event Grid SaaS-en PaaS-partners rechtstreeks naar Azure-Services met Azure Event Grid.
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 31a5fe611871eb4734b6a68e3818592028ebc75c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94506142"
---
# <a name="partner-events-in-azure-event-grid-preview"></a>Partner gebeurtenissen in Azure Event Grid (preview-versie)
Met de functie voor **partner gebeurtenissen** kan een SaaS-provider van derden gebeurtenissen van zijn Services publiceren zodat gebruikers zich op deze gebeurtenissen kunnen abonneren. Deze functie biedt een eerste ervaring voor gebeurtenis bronnen van derden door een [onderwerp](concepts.md#topics) , een **partner onderwerp**, weer te geven. Abonnees maken abonnementen op dit onderwerp om gebeurtenissen te gebruiken. Het biedt ook een schoon pub-submodel door problemen en eigendom van resources die worden gebruikt door gebeurtenis uitgevers en abonnees, te scheiden.

> [!NOTE]
> Als u geen ervaring hebt met het gebruik van Event Grid, raadpleegt u [overzicht](overview.md), [concepten](concepts.md)en [gebeurtenis-handlers](event-handlers.md).

## <a name="what-is-partner-events-to-a-publisher"></a>Wat zijn partner gebeurtenissen voor een uitgever?
Voor een gebeurtenis Uitgever kunnen uitgevers de volgende taken uitvoeren met de functie voor partner gebeurtenissen:

- Hun gebeurtenis bronnen onboarden naar Event Grid
- Een naam ruimte (eind punt) maken waarnaar gebeurtenissen kunnen worden gepubliceerd
- Onderwerpen over partners maken in azure die eigenaar zijn en gebruiken om gebeurtenissen te consumeren

## <a name="what-is-partner-events-to-a-subscriber"></a>Wat zijn partner gebeurtenissen voor een abonnee?
Bij een abonnee kunnen ze met de functie partner Events partner onderwerpen maken in azure om gebeurtenissen van derden te gebruiken. Het gebruik van gebeurtenissen wordt gerealiseerd door gebeurtenis abonnementen te maken die gebeurtenissen verzenden (push) naar de gebeurtenis-handler van een abonnee.

## <a name="why-should-i-use-partner-events"></a>Waarom moet ik partner gebeurtenissen gebruiken?
U kunt de partner gebeurtenissen gebruiken als u een of meer van de volgende vereisten hebt.

### <a name="for-publishers"></a>Voor uitgevers

- U wilt dat een mechanisme uw gebeurtenissen beschikbaar maakt in Azure. Uw gebruikers kunnen deze gebeurtenissen filteren en routeren met behulp van partner onderwerpen en gebeurtenis abonnementen die ze hebben en beheren. U kunt andere integratie benaderingen zoals [onderwerpen](custom-topics.md) en [domeinen](event-domains.md)gebruiken. Maar ze zouden geen duidelijke schei ding van de resource (de onderwerpen van de partner) voor de eigendom, het beheer en de facturering tussen uitgevers en abonnees toestaan. Deze benadering biedt ook een intuïtieve gebruikers ervaring waardoor het gemakkelijk is om partner onderwerpen te ontdekken en te gebruiken.
- U wilt gebeurtenissen publiceren naar een enkel eind punt, het eind punt van de naam ruimte. En u de mogelijkheid wilt geven om deze gebeurtenissen te filteren zodat alleen een subset ervan beschikbaar is. 
- U wilt inzicht hebben in de metrische gegevens die zijn gerelateerd aan gepubliceerde gebeurtenissen.
- U wilt [Cloud gebeurtenissen 1,0](https://cloudevents.io/) -schema voor uw gebeurtenissen gebruiken.

### <a name="for-subscribers"></a>Voor abonnees

- U wilt zich abonneren op gebeurtenissen van [uitgevers](#available-third-party-event-publishers) van derden en de gebeurtenissen afhandelen met gebeurtenis-handlers die zich op Azure of ergens anders bevinden.
- U wilt profiteren van de uitgebreide set routerings functies en [bestemmingen/gebeurtenis-handlers](overview.md#event-handlers) voor het verwerken van gebeurtenissen van bronnen van derden. 
- U wilt los gekoppelde architecturen implementeren waarbij uw abonnee/gebeurtenis-handler niet weet welk van de gebruikte Message Broker bestaat. 
- U hebt een robuust push-leverings mechanisme nodig met ondersteuning voor verzenden en opnieuw proberen en ten minste eenmaal.
- U wilt [Cloud gebeurtenissen 1,0](https://cloudevents.io/) -schema voor uw gebeurtenissen gebruiken. 


## <a name="available-third-party-event-publishers"></a>Beschik bare gebeurtenis uitgevers van derden
Een uitgever van een gebeurtenis van derden moet een [voorbereidings proces](partner-onboarding-overview.md) door lopen voordat een abonnee de gebeurtenissen kan gaan gebruiken. 

Als u een abonnee bent en u wilt dat een service van derden de gebeurtenissen weergeeft via Event Grid, 

### <a name="auth0"></a>Auth0
**Auth0** is de eerste uitgever van de partner. U kunt een [Auth0-partner onderwerp](auth0-overview.md) maken om uw Auth0-en Azure-accounts te verbinden. Met deze integratie kunt u Auth0-gebeurtenissen in realtime reageren, registreren en controleren. Zie [Azure Event grid integreren met Auto0](auth0-how-to.md) om het uit te proberen.

Als u wilt dat een service van derden de gebeurtenissen weergeeft via Event Grid, dient u het idee in te dienen op de [gebruikers spraak Portal](https://feedback.azure.com/forums/909934-azure-event-grid).
 
## <a name="resources-managed-by-event-publishers"></a>Bronnen die worden beheerd door gebeurtenis uitgevers
Gebeurtenis uitgevers maken en beheren van de volgende resources:

### <a name="partner-registration"></a>Partnerregistratie
Een registratie bevat algemene informatie over een uitgever. Het definieert een type partner onderwerp dat in de Azure Portal als optie wordt weer gegeven wanneer gebruikers een partner onderwerp proberen te maken. Een uitgever kan meer dan een of meer typen partner onderwerpen beschikbaar maken, zodat deze voldoet aan de behoeften van de abonnees. Dat wil zeggen dat een uitgever afzonderlijke registraties (typen partner onderwerpen) kan maken voor gebeurtenissen uit verschillende services. Bijvoorbeeld: voor de HR-Service (Human Resources) kan Publisher een partner onderwerp definiëren voor gebeurtenissen zoals lid zijn van een werk nemer, gepromoveerd werk nemers en werk nemers het bedrijf verlaten. 

Houd rekening met de volgende belangrijke punten:

- Alleen door Azure goedgekeurde partner registraties zijn zichtbaar. 
- Registraties zijn algemeen. Dat wil zeggen dat ze niet zijn gekoppeld aan een bepaalde Azure-regio.
- Een registratie is een optionele resource. Maar we raden u aan (als een uitgever) een registratie te maken. Hiermee kunnen gebruikers uw onderwerpen vinden op de pagina **partner onderwerp maken** in de [Azure Portal](https://portal.azure.com/#create/Microsoft.EventGridPartnerTopic). Vervolgens kan de gebruiker gebeurtenis typen selecteren (bijvoorbeeld toegevoegd aan werk nemers, werk nemers links, enzovoort) tijdens het maken van gebeurtenis abonnementen.

### <a name="namespace"></a>Naamruimte
Net als [aangepaste onderwerpen](custom-topics.md) en [domeinen](event-domains.md)is een partner naam ruimte een regionaal eind punt voor het publiceren van gebeurtenissen. Het gaat hierbij om naam ruimten die uitgevers gebeurtenis kanalen maken en beheren. Een naam ruimte fungeert ook als de container resource voor gebeurtenis kanalen.

### <a name="event-channels"></a>Gebeurtenis kanalen
Een gebeurtenis kanaal is een gespiegelde resource naar een partner onderwerp. Wanneer een uitgever een gebeurtenis kanaal maakt in het Azure-abonnement van de uitgever, wordt er ook een partner onderwerp gemaakt onder het Azure-abonnement van een abonnee. De bewerkingen die worden uitgevoerd voor een gebeurtenis kanaal (behalve GET), worden toegepast op het bijbehorende Subscriber-partner onderwerp, zelfs voor verwijdering. Alleen partner onderwerpen zijn echter het soort resources waarvoor abonnementen en de levering van gebeurtenissen kunnen worden geconfigureerd.

## <a name="resources-managed-by-subscribers"></a>Resources die worden beheerd door abonnees 
Abonnees kunnen de onderwerpen van de partner gebruiken die zijn gedefinieerd door een uitgever en het enige type resource dat ze zien en beheren. Zodra een partner onderwerp is gemaakt, kan een abonnee van een gebruiker gebeurtenis abonnementen maken om filter regels te definiëren voor [bestemmingen/gebeurtenis-handlers](overview.md#event-handlers). Voor abonnees bieden een partner onderwerp en de bijbehorende gebeurtenis abonnementen dezelfde uitgebreide functionaliteit als [aangepaste onderwerpen](custom-topics.md) en zijn gerelateerde abonnementen met één belang rijk verschil: partner onderwerpen ondersteunen alleen het [schema voor Cloud gebeurtenissen 1,0](cloudevents-schema.md), dat een uitgebreidere set mogelijkheden biedt dan andere ondersteunde schema's.

In de volgende afbeelding wordt de stroom van besturings vlak bewerkingen weer gegeven.

:::image type="content" source="./media/partner-events-overview/partner-control-plane-flow.png" alt-text="Partner gebeurtenissen-vliegtuig stroom beheren":::

1. Er wordt een **partner registratie** gemaakt. Partner registraties zijn algemeen. Dat wil zeggen dat ze niet zijn gekoppeld aan een bepaalde Azure-regio. Deze stap is optioneel.
1. Er wordt een **partner naam ruimte** in een specifieke regio gemaakt.
1. Wanneer abonnee 1 probeert een partner onderwerp te maken, wordt eerst een **gebeurtenis kanaal**, gebeurtenis kanaal 1, gemaakt in het Azure-abonnement van de uitgever.
1. Vervolgens wordt er **een partner onderwerp,** partner onderwerp 1, gemaakt in het Azure-abonnement van de abonnee. De abonnee moet het partner onderwerp activeren. 
1. Abonnee 1 maakt een **Azure Logic apps abonnement** op partner onderwerp 1.
1. Abonnee 1 maakt een **Azure Blob Storage-abonnement** op onderwerp 1 van de partner. 
1. Wanneer abonnee 2 probeert een partner onderwerp te maken, wordt eerst een ander **gebeurtenis kanaal**, gebeurtenis kanaal 2, gemaakt in het Azure-abonnement van de uitgever. 
1. Het **partner** onderwerp, partner onderwerp 2, wordt vervolgens gemaakt in het Azure-abonnement van de tweede abonnee. De abonnee moet het partner onderwerp activeren. 
1. Abonnee 2 maakt een **Azure functions abonnement** op partner onderwerp 2. 

## <a name="pricing"></a>Prijzen
Partner onderwerpen worden in rekening gebracht op basis van het aantal bewerkingen dat wordt uitgevoerd wanneer Event Grid wordt gebruikt. Zie [Event grid prijzen](https://azure.microsoft.com/pricing/details/event-grid/)voor meer informatie over alle soorten bewerkingen die worden gebruikt als basis voor facturerings-en gedetailleerde prijs informatie.

## <a name="limits"></a>Limieten
Zie [Event Grid Service limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits) voor gedetailleerde informatie over de limieten voor de onderwerpen van partners.


## <a name="next-steps"></a>Volgende stappen

- [Auth0-partner onderwerp](auth0-overview.md)
- [Het Auth0-partner onderwerp gebruiken](auth0-how-to.md)
- [Word een Event Grid partner](partner-onboarding-overview.md)