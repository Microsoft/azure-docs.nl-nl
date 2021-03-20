---
title: Een Azure-toepassing-aanbieding testen en publiceren
description: Gebruik het partner centrum om uw Azure-toepassings aanbieding in te dienen voor een preview-versie, een voor beeld van uw aanbieding te bekijken, te testen en deze vervolgens te publiceren naar micro soft Commercial Marketplace.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 461f9354bb3a6eae0af186de8fe9f39c6b5fff2c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96620916"
---
# <a name="how-to-test-and-publish-an-azure-application-offer"></a>Een Azure-toepassing-aanbieding testen en publiceren

In dit artikel wordt uitgelegd hoe u partner Center kunt gebruiken om uw Azure-toepassing aanbieding te verzenden, een voor beeld van uw aanbieding te bekijken, deze te testen en deze vervolgens te publiceren naar de commerciële Marketplace. U moet al een aanbieding hebben gemaakt die u wilt publiceren.

## <a name="submit-your-offer-for-publishing"></a>Uw aanbieding voor publicatie verzenden

1. Meld u aan bij het dash board voor commerciële Marketplace in het [partner centrum](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).
1. Selecteer op de pagina **overzicht** de aanbieding die u wilt publiceren.
1. Selecteer in de rechter bovenhoek van de portal **controleren en publiceren**.
1. Zorg ervoor dat de kolom **status** voor elke pagina **is voltooid**. De drie mogelijke statussen zijn als volgt:
    - **Niet gestart** : de pagina is niet volledig.
    - **Onvolledig** : in de pagina ontbreekt de vereiste informatie of fouten die moeten worden opgelost. U moet terug naar de pagina gaan en deze bijwerken.
    - **Voltooid** : de pagina is voltooid. Alle vereiste gegevens zijn opgegeven en er zijn geen fouten.
1. Als een van de pagina's een andere status dan **voltooid** heeft, selecteert u de pagina naam, corrigeert u het probleem, slaat u de pagina op en selecteert u vervolgens opnieuw **controleren en publiceren** om terug te gaan naar deze pagina.
1. Nadat alle pagina's zijn voltooid, geeft u in het vak **notities voor certificering** test instructies op voor het certificerings team om ervoor te zorgen dat uw app correct wordt getest. Geef aanvullende notities op die nuttig zijn voor het leren van uw app.
1. Selecteer **publiceren** om het publicatie proces voor uw aanbieding te starten. De pagina **overzicht van aanbieding** wordt weer gegeven en toont de **publicatie status** van de aanbieding.

De publicatie status van uw aanbieding verandert wanneer deze door het publicatie proces wordt verplaatst. Zie [validatie-en publicatie stappen](review-publish-offer.md#validation-and-publishing-steps)voor meer informatie over dit proces.

## <a name="preview-and-test-your-offer"></a>Uw aanbieding bekijken en testen

Wanneer de aanbieding klaar is voor uw afmelding, ontvangt u een e-mail waarin u wordt gevraagd om uw aanbiedings voorbeeld te controleren en goed te keuren. U kunt ook de pagina **overzicht van aanbieding** in uw browser vernieuwen om te zien of uw aanbieding de afmeldings fase van de uitgever heeft bereikt. Als dat het geval is, is de knop **Live go** en de preview-koppeling beschikbaar. Als u ervoor kiest om uw aanbieding via micro soft te verkopen, kan iedereen die is toegevoegd aan de preview-doel groep de aanschaf en implementatie van uw aanbieding testen om ervoor te zorgen dat deze aan uw vereisten voldoet tijdens deze fase.

De volgende scherm afbeelding toont de **overzichts** pagina van de aanbieding voor een aanbieding, met twee preview-koppelingen onder de knop **Go Live** . Welke validatie stappen u op deze pagina ziet, hangt af van de selecties die u hebt gemaakt tijdens het maken van de aanbieding.

[![Illustreert de pagina overzicht van aanbieding voor een aanbieding in partner centrum. De knop Go Live en de preview-koppelingen worden weer gegeven.](media/create-new-azure-app-offer/azure-app-publish-status.png)](media/create-new-azure-app-offer/azure-app-publish-status.png#lightbox)

Gebruik de volgende stappen om een voor beeld van uw aanbieding te bekijken.

1. Selecteer op de pagina **overzicht van aanbieding** een preview-koppeling onder de knop **Go Live** . 

1. Als u de end-to-end aankoop-en configuratie stroom wilt valideren, koopt u uw aanbieding terwijl u een preview-versie hebt. Informeer eerst micro soft met een [ondersteunings ticket](https://aka.ms/marketplacesupport) om ervoor te zorgen dat er geen kosten worden verwerkt.

1. Als uw Azure-toepassing [facturering ondersteunt met behulp van de commerciële Marketplace-meet service](./partner-center-portal/azure-app-metered-billing.md), kunt u de aanbevolen procedures voor testen bekijken en volgen die worden beschreven in [Marketplace-facturering-api's met data limieten](./partner-center-portal/marketplace-metering-service-apis.md#development-and-testing-best-practices).

1. Als u wijzigingen moet aanbrengen na het voorvertonen en testen van de aanbieding, kunt u een nieuwe preview-versie bewerken en opnieuw indienen. Zie [een bestaande aanbieding in de commerciële Marketplace bijwerken](./partner-center-portal/update-existing-offer.md)voor meer informatie.

## <a name="publish-your-offer-live"></a>Uw aanbieding Live publiceren

Nadat u alle tests voor uw Preview hebt voltooid, selecteert u **Live** om uw aanbieding Live naar de commerciële Marketplace te publiceren.

   > [!TIP]
   > Als uw aanbieding al Live is in de commerciële Marketplace, worden de updates die u aanbrengt pas live als u **Live go** selecteert.

Nu u ervoor hebt gekozen om uw aanbieding beschikbaar te stellen in de commerciële Marketplace, voeren we een reeks definitieve validatie controles uit om ervoor te zorgen dat de Live-aanbieding is geconfigureerd, net als de preview-versie van de aanbieding. Zie voor meer informatie over deze validatie controles de [fase publiceren](review-publish-offer.md#publish-phase).

Nadat deze validatie controles zijn voltooid, is uw aanbieding Live in de Marketplace.

### <a name="errors-and-review-feedback"></a>Fouten en feedback bekijken

De **hand matige validatie** stap in het publicatie proces vertegenwoordigt een uitgebreide beoordeling van uw aanbieding en de bijbehorende technische activa (met name de Azure Resource Manager-sjabloon) problemen worden doorgaans gepresenteerd als pull-aanvraag (PR)-koppelingen. Zie [afhandeling van feedback](partner-center-portal/azure-apps-review-feedback.md)bekijken voor meer informatie over het weer geven en reageren op deze pull.

Als er fouten zijn opgetreden in een of meer van de publicatie stappen, corrigeert u deze voordat u uw aanbieding opnieuw publiceert.

## <a name="next-step"></a>Volgende stap

- [Toegang krijgen tot analytische rapporten voor de commerciële Marketplace in het partner centrum](partner-center-portal/analytics.md)
- Meer informatie [over het verkopen van uw Azure-toepassing-aanbod](create-new-azure-apps-offer-marketing.md) via het samen verkopen met micro soft en door verkopen via csp's-Program ma's.
