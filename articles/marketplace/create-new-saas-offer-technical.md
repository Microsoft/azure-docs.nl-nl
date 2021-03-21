---
title: Technische gegevens voor uw SaaS-aanbieding toevoegen in micro soft Partner Center
description: Meer informatie over het bieden van technische gegevens voor uw software als een service (SaaS)-aanbieding voor micro soft Commercial Marketplace.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 28c0be40387f411286230f94c19fa23a80e650af
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96746400"
---
# <a name="how-to-add-technical-details-for-your-saas-offer"></a>Technische gegevens voor uw SaaS-aanbieding toevoegen

In dit artikel wordt beschreven hoe u technische gegevens kunt invoeren waarmee micro soft Commercial Marketplace verbinding kan maken met uw oplossing. Met deze verbinding kunnen wij uw aanbieding voor de klant inrichten als ze ervoor kiezen om deze te verkrijgen en te beheren. Zie [technische informatie](plan-saas-offer.md#technical-information)voor meer informatie over deze instellingen.

> [!NOTE]
> Als u ervoor kiest om trans acties onafhankelijk te verwerken, wordt deze optie niet weer geven. Ga in plaats daarvan naar [de verkoop van uw SaaS-aanbieding](create-new-saas-offer-marketing.md).

## <a name="technical-configuration"></a>Technische configuratie

Op het tabblad **technische configuratie** definieert u de technische details die de commerciële Marketplace gebruikt om te communiceren met uw SaaS-toepassing of-oplossing. 

- **URL van de landings pagina** (vereist): de URL van de SaaS-website definiëren (bijvoorbeeld: `https://contoso.com/signup` ) waar klanten mee te maken krijgen nadat zij uw aanbieding van de commerciële Marketplace hebben ontvangen en het configuratie proces van het zojuist gemaakte SaaS-abonnement activeren.

  > [!IMPORTANT]
  > Uw landings pagina moet 24/7 zijn. Dit is de enige manier waarop u wordt gewaarschuwd over nieuwe aankopen van uw SaaS-aanbiedingen die zijn gemaakt in de commerciële Marketplace of configuratie aanvragen van een actief abonnement op een aanbieding.

- **Verbindings-webhook** (vereist): voor alle asynchrone gebeurtenissen die micro soft naar u moet sturen (bijvoorbeeld SaaS-abonnement is geannuleerd), moet u een URL voor de verbindings-webhook opgeven. Deze URL wordt gebeld om u op de hoogte te stellen van de gebeurtenis.

  > [!IMPORTANT]
  > Uw webhook moet 24/7, omdat dit de enige manier is waarop u wordt geïnformeerd over updates over de SaaS-abonnementen van uw klanten die zijn gekocht via de Comercial Marketplace.

- **Azure Active Directory Tenant-id** (vereist): als u de Tenant-id voor uw Azure Active Directory Azure AD-App wilt vinden, gaat u naar de blade [app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) in azure Active Directory. Selecteer de app in de kolom **weergave naam** . Zoek vervolgens naar de **map (Tenant) ID** die wordt weer gegeven (bijvoorbeeld `50c464d3-4930-494c-963c-1e951d15360e` ).

- **Azure Active Directory toepassings-id** (vereist): als u de [toepassings-id](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)wilt vinden, gaat u naar de blade [app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) in azure Active Directory. Selecteer de app in de kolom **weergave naam** . Zoek vervolgens naar het ID-nummer van de toepassing (client) in de lijst (bijvoorbeeld `50c464d3-4930-494c-963c-1e951d15360e` ).

Selecteer **concept opslaan** voordat u doorgaat naar het volgende tabblad: plan overzicht.

## <a name="next-steps"></a>Volgende stappen

- [Plannen maken voor uw SaaS-aanbieding](create-new-saas-offer-plans.md).