---
title: Een SaaS-aanbieding testen en publiceren naar micro soft Commercial Marketplace
description: Gebruik het partner centrum om uw SaaS-aanbieding te verzenden naar een preview-versie, een voor beeld van uw aanbieding te bekijken, te testen en deze vervolgens te publiceren naar micro soft Commercial Marketplace.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 03/10/2021
ms.openlocfilehash: ea647591194dfd6ba0bf818f4ce4d3a2005f0bc4
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102630776"
---
# <a name="how-to-test-and-publish-a-saas-offer-to-the-commercial-marketplace"></a>Een SaaS-aanbieding testen en publiceren naar de commerciële Marketplace

In dit artikel wordt uitgelegd hoe u partner centrum gebruikt om uw SaaS-aanbieding te verzenden, een voor beeld van uw aanbieding te bekijken, deze te testen en deze vervolgens te publiceren naar de commerciële Marketplace. U moet al een aanbieding hebben gemaakt die u wilt publiceren.

## <a name="submit-your-offer-for-publishing"></a>Uw aanbieding voor publicatie verzenden

1. Meld u aan bij het dash board voor commerciële Marketplace in het [partner centrum](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).
1. Selecteer op de pagina **overzicht** de aanbieding die u wilt publiceren.
1. Selecteer in de rechter bovenhoek van de portal **controleren en publiceren**.
2. Zorg ervoor dat de kolom **status** voor elke pagina **is voltooid**. De drie mogelijke statussen zijn als volgt:

   - **Niet gestart** : de pagina is niet volledig.
   - **Onvolledig** : in de pagina ontbreekt de vereiste informatie of fouten die moeten worden opgelost. U moet terug naar de pagina gaan en deze bijwerken.
   - **Voltooid** : de pagina is voltooid. Alle vereiste gegevens zijn opgegeven en er zijn geen fouten.

1. Als een van de pagina's een andere status dan **voltooid** heeft, selecteert u de pagina naam, corrigeert u het probleem, slaat u de pagina op en selecteert u vervolgens opnieuw **controleren en publiceren** om terug te gaan naar deze pagina.
1. Nadat alle pagina's zijn voltooid, geeft u in het vak **notities voor certificering** test instructies op voor het certificerings team om ervoor te zorgen dat uw app correct wordt getest. Geef aanvullende notities op die nuttig zijn voor het leren van uw app.
1. Selecteer **publiceren** om het publicatie proces voor uw aanbieding te starten. De pagina **overzicht van aanbieding** wordt weer gegeven en toont de **publicatie status** van de aanbieding.

De publicatie status van uw aanbieding verandert wanneer deze door het publicatie proces wordt verplaatst. Zie [validatie-en publicatie stappen](review-publish-offer.md#validation-and-publishing-steps)voor meer informatie over dit proces.

## <a name="preview-and-test-your-offer"></a>Uw aanbieding bekijken en testen

Wanneer de aanbieding klaar is voor uw afmelding, ontvangt u een e-mail waarin u wordt gevraagd om uw aanbiedings voorbeeld te controleren en goed te keuren. U kunt ook de pagina **overzicht van aanbieding** in uw browser vernieuwen om te zien of uw aanbieding de afmeldings fase van de uitgever heeft bereikt. Als dat het geval is, is de knop **Live** en voor beeld-koppelingen beschikbaar. Er is een koppeling voor Microsoft AppSource preview, de preview van Azure Marketplace of beide, afhankelijk van de opties die u hebt gekozen bij het maken van uw aanbieding. Als u ervoor kiest om uw aanbieding via micro soft te verkopen, kan iedereen die is toegevoegd aan de preview-doel groep de aanschaf en implementatie van uw aanbieding testen om ervoor te zorgen dat deze aan uw vereisten voldoet tijdens deze fase.

De volgende scherm afbeelding toont de pagina **overzicht van aanbieding** voor een SaaS-aanbieding, met twee preview-koppelingen onder de knop **Go Live** . Welke validatie stappen u op deze pagina ziet, hangt af van de selecties die u hebt gemaakt tijdens het maken van de aanbieding.

![Illustreert de pagina overzicht van aanbieding voor een aanbieding in partner centrum. De knop Go Live en de preview-koppelingen worden weer gegeven. De koppeling weergave validatie rapport wordt ook weer gegeven onder Automatische validatie.](./media/review-publish-offer/publish-status-saas.png)

Gebruik de volgende stappen om een voor beeld van uw aanbieding te bekijken.

1. Selecteer op de pagina **overzicht van aanbieding** een preview-koppeling onder de knop **Go Live** .

1. Als u de end-to-end aankoop-en configuratie stroom wilt valideren, koopt u uw aanbieding terwijl u een preview-versie hebt. Informeer eerst micro soft met een [ondersteunings ticket](https://aka.ms/marketplacesupport) om ervoor te zorgen dat er geen kosten worden verwerkt.

1. Als uw SaaS-aanbod ondersteuning biedt voor [het gebruik van een Data limiet met behulp van de commerciële Marketplace meter service](./partner-center-portal/saas-metered-billing.md), kunt u de aanbevolen procedures voor testen bekijken en volgen die in [Marketplace-facturerings api's](./partner-center-portal/marketplace-metering-service-apis.md#development-and-testing-best-practices)worden beschreven.

1. Bekijk en volg de instructies voor het testen van [SaaS fulfillment api's versie 2 in micro soft Commercial Marketplace](./partner-center-portal/pc-saas-fulfillment-api-v2.md#development-and-testing) om ervoor te zorgen dat uw aanbieding met de api's is geïntegreerd voordat u uw aanbieding Live publiceert.

1. Als de stap voor de validatie van het aanbod heeft geresulteerd in waarschuwingen, wordt een koppeling naar het **rapport weergave validatie** weer gegeven op de **overzichts** pagina Controleer het rapport en los de problemen op voordat u de knop **Go Live** selecteert. Anders mislukt de certificering waarschijnlijk en vertraagt uw aanbieding van Live.

1. Als u wijzigingen moet aanbrengen na het voorvertonen en testen van de aanbieding, kunt u een nieuwe preview-versie bewerken en opnieuw indienen. Zie [een bestaande aanbieding in de commerciële Marketplace bijwerken](./partner-center-portal/update-existing-offer.md)voor meer informatie.

## <a name="publish-your-offer-live"></a>Uw aanbieding Live publiceren

Nadat u alle tests voor uw Preview hebt voltooid, selecteert u **Live** om uw aanbieding Live naar de commerciële Marketplace te publiceren.

   > [!TIP]
   > Als uw aanbieding al Live is in de commerciële Marketplace, worden de updates die u aanbrengt pas live als u **Live go** selecteert.

Nu u ervoor hebt gekozen om uw aanbieding beschikbaar te stellen in de commerciële Marketplace, voeren we een reeks definitieve validatie controles uit om ervoor te zorgen dat de Live-aanbieding is geconfigureerd, net als de preview-versie van de aanbieding. Zie voor meer informatie over deze validatie controles de [fase publiceren](review-publish-offer.md#publish-phase).

Nadat deze validatie controles zijn voltooid, is uw aanbieding Live in de Marketplace.

## <a name="next-steps"></a>Volgende stappen

- [Toegang krijgen tot analytische rapporten voor de commerciële Marketplace in het partner centrum](./partner-center-portal/analytics.md)
