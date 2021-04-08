---
title: Abonnementen in azure API Management | Microsoft Docs
description: Meer informatie over het concept van abonnementen in azure API Management. Gebruikers krijgen toegang tot Api's met behulp van abonnementen in azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: cdc7668b06308bd5532f4885d0e1365fc34ae989
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97511243"
---
# <a name="subscriptions-in-azure-api-management"></a>Abonnementen maken in Azure API Management

Abonnementen vormen een belang rijk concept in azure API Management. Ze zijn de meest voorkomende manier voor API-gebruikers om toegang te krijgen tot Api's die zijn gepubliceerd via een API Management-exemplaar. Dit artikel bevat een overzicht van het concept.

## <a name="what-are-subscriptions"></a>Wat zijn abonnementen?

Wanneer u Api's publiceert via API Management, is het eenvoudig en gebruikelijk om de toegang tot die Api's te beveiligen met behulp van abonnements sleutels. Ontwikkel aars die de gepubliceerde Api's moeten gebruiken, moeten een geldige abonnements sleutel in HTTP-aanvragen voor het aanroepen van deze Api's hebben. Anders worden de aanroepen onmiddellijk door de API Management Gateway afgewezen. Ze worden niet doorgestuurd naar de back-end-services.

Als u een abonnements sleutel voor toegang tot Api's wilt ophalen, is een abonnement vereist. Een-abonnement is in feite een benoemde container voor een paar abonnements sleutels. Ontwikkel aars die de gepubliceerde Api's moeten gebruiken, kunnen abonnementen ophalen. En ze hebben geen goed keuring nodig van API-uitgevers. API-uitgevers kunnen ook rechtstreeks abonnementen voor API-gebruikers maken.

> [!TIP]
> API Management ondersteunt ook andere mechanismen voor het beveiligen van de toegang tot Api's, met inbegrip van de volgende voor beelden:
> - [OAuth 2.0](api-management-howto-protect-backend-with-aad.md)
> - [Client certificaten](api-management-howto-mutual-certificates-for-clients.md)
> - [IP-adressen van beller beperken](./api-management-access-restriction-policies.md#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Bereik van abonnementen

Abonnementen kunnen worden gekoppeld aan verschillende bereiken: product, alle Api's of een afzonderlijke API.

### <a name="subscriptions-for-a-product"></a>Abonnementen voor een product

Normaal gesp roken zijn abonnementen in API Management altijd gekoppeld aan één [API-product](api-management-terminology.md) bereik. Ontwikkel aars hebben een lijst met producten gevonden op de ontwikkelaars Portal. Daarna verzenden ze abonnements aanvragen voor de producten die ze willen gebruiken. Nadat een abonnements aanvraag is goedgekeurd, hetzij automatisch of door API-uitgevers, kan de ontwikkelaar de sleutels erin gebruiken om toegang te krijgen tot alle Api's in het product. Op dit moment toont de ontwikkelaars Portal alleen de product Scope abonnementen onder het gebruikers profiel gedeelte. 

![Productabonnementen](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> Onder bepaalde scenario's is het mogelijk dat API-uitgevers een API-product willen publiceren naar het openbaar zonder dat er abonnementen zijn vereist. Ze kunnen de selectie van de optie **abonnement vereisen** op de pagina **instellingen** van het product in de Azure Portal opheffen. Als gevolg hiervan kunnen alle Api's onder het product worden geopend zonder een API-sleutel.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Abonnementen voor alle Api's of een afzonderlijke API

Toen we de [verbruiks](https://aka.ms/apimconsumptionblog) tier van API Management introducden, hebben we enkele wijzigingen aangebracht in het stroom lijnen van het sleutel beheer:
- Eerst hebben we twee meer abonnements bereiken toegevoegd: alle Api's en één API. Het bereik van abonnementen is niet langer beperkt tot een API-product. Het is nu mogelijk om sleutels te maken die toegang verlenen tot een API of alle Api's binnen een API Management-exemplaar, zonder dat er een product hoeft te worden gemaakt en de Api's eerst aan het object moeten worden toegevoegd. Elke API Management-exemplaar wordt nu ook geleverd met een onveranderbaar abonnement met alle Api's. Dit abonnement maakt het eenvoudiger en gemakkelijker om Api's in de test console te testen en fouten op te sporen.

- Ten tweede API Management nu **zelfstandige** abonnementen. Abonnementen hoeven niet langer te worden gekoppeld aan een ontwikkelaars account. Deze functie is handig in scenario's, bijvoorbeeld wanneer verschillende ontwikkel aars of teams een abonnement delen.

- Tot slot kunnen API-uitgevers nu rechtstreeks in het Azure Portal [abonnementen maken](api-management-howto-create-subscriptions.md) :

    ![Flexibele abonnementen](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Volgende stappen
Meer informatie over API Management:

+ Meer informatie over andere [concepten](api-management-terminology.md) in API management.
+ Volg onze [zelf studies](import-and-publish.md) voor meer informatie over API management.
+ Raadpleeg onze [pagina met veelgestelde](api-management-faq.md) vragen voor veelgestelde vragen.
