---
title: Veelgestelde vragen over SaaS-fulfillment-micro soft Commercial Marketplace
description: Meer informatie over een aantal integratie vereisten voor de micro soft Commercial Marketplace, waarmee Azure-klanten zich kunnen abonneren op SaaS-aanbiedingen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 4c5d8b438764fa9aa3838b2225c63d412afc519b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88606802"
---
# <a name="common-questions-about-saas-fulfillment-apis"></a>Veelgestelde vragen over Api's voor SaaS-uitvoering

In dit artikel wordt een aantal integratie vereisten voor de micro soft Commercial Marketplace beschreven, waarmee Azure-klanten zich kunnen abonneren op SaaS-aanbiedingen.

## <a name="discovery-experience"></a>Detectie-ervaring

Zodra een SaaS-aanbieding is gepubliceerd, kunnen Azure-gebruikers deze in azure Marketplace ontdekken. Uw klanten kunnen aanbiedingen filteren op basis van product type (SaaS) en de SaaS-services ontdekken waar ze geïnteresseerd zijn.

## <a name="purchase-experience"></a>Ervaring kopen

Zodra een gebruiker is geïnteresseerd in een specifieke SaaS-service, kan de gebruiker zich abonneren op Azure Marketplace.

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Wat betekent het dat een Azure-gebruiker zich abonneert op een SaaS-aanbieding in azure Marketplace?

Dit betekent dat een gebruiker de gebruiksrecht overeenkomst en privacyverklaring kan bekijken die is gekoppeld aan de SaaS-service en ermee akkoord gaat om deze te betalen volgens de door u ingestelde facturerings voorwaarden, de uitgever van de SaaS-aanbieding op de factuur van micro soft. Gebruikers kunnen hun bestaande betalings profiel in azure gebruiken om te betalen voor het SaaS-service verbruik.

Deze mogelijkheid is op verschillende manieren nuttig. Klanten kunnen nu op één plek ontdekken en zich abonneren met Microsoft Cloud platform als een vertrouwde bron, zonder dat ze elke ISV-Software moeten gebruiken die ze nodig hebben. Klanten kunnen ook hun bestaande betalings profiel gebruiken zonder dat ze expliciet afzonderlijke ISV-software hoeven te betalen.

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>Worden de gebruikers automatisch in rekening gebracht wanneer de aanbieding is geabonneerd?

Tijdens het abonneren op de SaaS-aanbieding heeft de gebruiker besloten om te betalen voor het gebruik van de SaaS-service via het micro soft-platform. Kosten worden echter alleen gestart wanneer de aanbieding wordt verbruikt. De gebruiker moet naar uw SaaS-aanbieding gaan en het aanmaken van accounts bevestigen om te beginnen met het gebruik van de aanbieding. Vervolgens meldt u micro soft om te beginnen met de facturering voor dit klant SaaS-abonnement.

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>Hoe wordt u gewaarschuwd wanneer een gebruiker zich abonneert op uw SaaS-aanbieding?

Nadat u zich hebt geabonneerd op een aanbieding, kan de Azure-gebruiker al hun aanbiedingen in azure detecteren en beheren. Standaard wordt de status van een nieuw geabonneerde SaaS-aanbieding weer gegeven als **inrichting, in afwachting** van de uitvoering. In deze status wordt de Azure-gebruiker gevraagd om het **account te configureren**, om te bladeren naar de beheer ervaring van het SaaS-abonnement in de Azure Portal.

Wanneer de gebruiker **account configureren** selecteert, wordt deze omgeleid naar de website van de SaaS-service. De uitgever heeft de URL geconfigureerd op het moment van publicatie van de aanbieding. Deze pagina wordt de landings pagina van de uitgever genoemd. Azure-gebruikers melden zich aan bij de SaaS-landings pagina op basis van hun bestaande AAD-referenties in Azure.

Wanneer de Azure-gebruiker wordt omgeleid naar de landings pagina, wordt een token toegevoegd aan de query-URL. Dit token is korte tijd en geldig voor een tijds duur van 24 uur. U kunt vervolgens de aanwezigheid van dit token detecteren en de API van micro soft aanroepen om meer context te verkrijgen die aan het token is gekoppeld.

![Abonnements stroom van de klant](media/saas-metering-service-integration-flow-a.png)

Zie [SaaS fulfillment API](pc-saas-fulfillment-api-v2.md)voor meer informatie over het API-contract voor het verwerken van Transact-scenario's in de levens cyclus van een SaaS-aanbieding.

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>Hoe weet u wat de SaaS-aanbieding is waarmee de gebruiker zich abonneert in azure?

De reactie op de `Resolve` API omvat de aanbieding en plan informatie die is gekoppeld aan het SaaS-abonnement.

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Hoe kan de Azure-gebruiker het abonnement wijzigen dat is gekoppeld aan dit Azure-abonnement?

* De Azure-gebruiker kan het abonnement dat is gekoppeld aan het SaaS-abonnement rechtstreeks wijzigen in de SaaS-ervaring of via het micro soft-platform.

* Conversies kunnen op elk gewenst moment worden uitgevoerd in de facturerings cyclus. U wordt gevraagd om eventuele conversie te bevestigen. deze wordt van kracht zodra deze wordt bevestigd.

* Tarieven voor het vooraf betaalde abonnement (**maandelijks** of **jaarlijks**) zijn Pro-beoordeeld. Alle overschrijding die tot de tijd van de conversie worden verzonden, worden in de volgende factuur in rekening gebracht. Nieuwe overschotten worden verzonden op basis van het nieuwe abonnement.

>[!Note]
>U kunt downgrades blok keren als u geen specifieke conversie paden wilt ondersteunen.

In de onderstaande volg orde wordt de stroom vastgelegd wanneer een Azure-klant een abonnement in de SaaS-ervaring wijzigt:

![Wijzigings stroom van klant abonnement](media/saas-metering-service-integration-flow-b.png)

In de onderstaande volg orde wordt de stroom vastgelegd wanneer een Azure-klant een abonnement in de online winkel van micro soft wijzigt:

![Wijzigings stroom klant Online Store-plan](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Hoe kan de Azure-gebruiker zich afmelden voor het plan dat is gekoppeld aan het Azure-abonnement?

Een Azure-gebruiker kan zich afmelden bij een gekochte SaaS-aanbieding, hetzij rechtstreeks in de SaaS-ervaring, hetzij via het micro soft-platform. Zodra de gebruiker zich afmeldt, worden er geen kosten meer in rekening gebracht voor de volgende facturerings cyclus.

In de onderstaande volg orde wordt de stroom vastgelegd wanneer een Azure-klant zich afmeldt bij de SaaS-aanbieding in de SaaS-ervaring:

![Klant heeft zich afgemeld bij de SaaS-ervaring](media/saas-metering-service-integration-flow-d.png)

In de onderstaande volg orde wordt de stroom vastgelegd wanneer de Azure-gebruiker zich afmeldt in de online winkel van micro soft:

![Klant heeft zich afgemeld in de online winkel van micro soft](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>Volgende stappen

[Service-API's voor Marketplace-meting](./marketplace-metering-service-apis.md)
