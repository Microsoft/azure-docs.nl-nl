---
title: Migreren naar de nieuwe ontwikkelaars Portal vanuit de verouderde ontwikkelaars Portal
titleSuffix: Azure API Management
description: Meer informatie over hoe u migreert van de verouderde ontwikkelaars Portal naar de nieuwe ontwikkelaars Portal in API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: f5105c685de4b3ccdffe69eec8ee8eeb32976c1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92325992"
---
# <a name="migrate-to-the-new-developer-portal"></a>Migreren naar de nieuwe ontwikkelaars Portal

In dit artikel worden de stappen beschreven die u moet uitvoeren om te migreren van de afgeschafte verouderde Portal naar de nieuwe ontwikkelaars Portal in API Management.

> [!IMPORTANT]
> De verouderde ontwikkelaars Portal is nu afgeschaft en er worden alleen beveiligings updates ontvangen. U kunt deze zoals gebruikelijk blijven gebruiken tot de buitengebruikstelling in oktober 2023, wanneer de portal wordt verwijderd uit alle API Management Services.

![API Management ontwikkelaars Portal](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="improvements-in-new-developer-portal"></a>Verbeteringen in de nieuwe ontwikkelaars Portal

De nieuwe ontwikkelaars Portal biedt een aantal beperkingen van de afgeschafte Portal. Het bevat een [visuele editor voor slepen en neerzetten voor het bewerken van inhoud](api-management-howto-developer-portal-customize.md) en een speciaal paneel voor ontwerpers om de website te voorzien van een stijl. Pagina's, aanpassingen en configuratie worden opgeslagen als Azure Resource Manager resources in uw API Management-service, waarmee u de [Portal-implementaties kunt automatiseren](api-management-howto-developer-portal.md#automate). Ten slotte is de code base van de portal open-source, zodat [u deze kunt uitbreiden met aangepaste functionaliteit](api-management-howto-developer-portal.md#managed-vs-self-hosted).

## <a name="how-to-migrate-to-new-developer-portal"></a>Migreren naar een nieuwe ontwikkelaars Portal

De nieuwe ontwikkelaars Portal is niet compatibel met de afgeschafte Portal en de geautomatiseerde migratie is niet mogelijk. U moet de inhoud hand matig opnieuw maken (pagina's, tekst, media bestanden) en het uiterlijk van de nieuwe portal aanpassen. Nauw keurige stappen variëren afhankelijk van de aanpassingen en complexiteit van uw portal. Raadpleeg [de zelf studie over de ontwikkelaars Portal](api-management-howto-developer-portal-customize.md) voor hulp. De resterende configuratie, zoals de lijst met Api's, producten, gebruikers, id-providers, wordt automatisch gedeeld via beide portals.

> [!IMPORTANT]
> Als u de nieuwe ontwikkelaars Portal eerder hebt gestart, maar u geen wijzigingen hebt aangebracht, [herstelt u de standaard inhoud](api-management-howto-developer-portal.md#preview-to-ga) om deze bij te werken naar de meest recente versie.

Wanneer u migreert van de afgeschafte Portal, moet u de volgende wijzigingen aanbrengen:

- Als u uw ontwikkelaars portal via een aangepast domein beschikbaar maakt, [wijst u een domein](configure-custom-domain.md) toe aan de nieuwe ontwikkelaars Portal. Gebruik de optie **ontwikkelaars Portal** in de vervolg keuzelijst in de Azure Portal.
- [Pas een CORS-beleid](api-management-howto-developer-portal.md#cors) op uw api's toe om de interactieve test console in te scha kelen.
- Als u aangepaste CSS indeelt om de stijl van de portal te maken, moet u [de stijlen repliceren met behulp van het ingebouwde ontwerp paneel](api-management-howto-developer-portal-customize.md). CSS-injectie is niet toegestaan in de nieuwe portal.
- U kunt aangepaste Java script alleen invoegen in de [zelf-hostende versie van de nieuwe portal](api-management-howto-developer-portal.md#managed-vs-self-hosted).
- Als uw API Management zich in een virtueel netwerk bevindt en via Application Gateway wordt blootgesteld aan Internet, [raadpleegt u dit documentatie artikel](api-management-howto-integrate-internal-vnet-appgateway.md) voor een nauw keurige configuratie stap. U moet het volgende doen:

    - Connectiviteit met het beheer eindpunt van de API Management inschakelen.
    - Connectiviteit met het nieuwe portal-eind punt inschakelen.
    - Geselecteerde firewall regels voor webtoepassingen uitschakelen.

- Als u de standaard sjablonen voor e-mail meldingen hebt gewijzigd om een expliciet gedefinieerde afgeschafte Portal-URL op te geven, wijzigt u deze in de para meter Portal-URL of gaat u naar de nieuwe Portal-URL. Als de sjablonen in plaats daarvan de ingebouwde URL-para meter van de portal gebruiken, zijn er geen wijzigingen vereist.
- *Problemen* en *toepassingen* worden niet ondersteund in de nieuwe ontwikkelaars Portal.
- Directe integratie met Facebook, micro soft, Twitter en Google als id-providers wordt niet ondersteund in de nieuwe ontwikkelaars Portal. U kunt met deze providers integreren via Azure AD B2C.
- Als u delegering gebruikt, wijzigt u de retour-URL in uw toepassingen en gebruikt u het eind punt van het API- [ *gedeelde toegangs Token ophalen*](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken) in plaats van de *URL voor SSO-url's genereren* .
- Als u Azure AD als id-provider gebruikt:

    - Wijzig de retour-URL in uw toepassing zodat deze verwijst naar het nieuwe ontwikkelaars Portal-domein.
    - Wijzig het achtervoegsel van de retour-URL in uw toepassing van in `/signin-aad` `/signin` .

- Als u Azure AD B2C als een id-provider gebruikt:

    - Wijzig de retour-URL in uw toepassing zodat deze verwijst naar het nieuwe ontwikkelaars Portal-domein.
    - Wijzig het achtervoegsel van de retour-URL in uw toepassing van in `/signin-aad` `/signin` .
    - Geef de *opgegeven naam* *, de naam en* de *object-id* van de gebruiker op in de toepassings claims.

- Als u OAuth 2,0 in de interactieve test console gebruikt, wijzigt u de retour-URL in uw toepassing zodat deze verwijst naar het nieuwe ontwikkelaars Portal-domein en wijzigt u het achtervoegsel:

    - Van `/docs/services/[serverName]/console/oauth2/authorizationcode/callback` naar `/signin-oauth/code/callback/[serverName]` voor de toekennings stroom van de autorisatie code.
    - Van `/docs/services/[serverName]/console/oauth2/implicit/callback` naar `/signin-oauth/implicit/callback` voor de impliciete toekennings stroom.
- Als u OpenID Connect Connect gebruikt in de interactieve test console, wijzigt u de retour-URL in uw toepassing zodat deze verwijst naar het nieuwe ontwikkelaars Portal-domein en wijzigt u het achtervoegsel:

    - Van `/docs/services/[serverName]/console/openidconnect/authorizationcode/callback` naar `/signin-oauth/code/callback/[serverName]` voor de toekennings stroom van de autorisatie code.
    - Van `/docs/services/[serverName]/console/openidconnect/implicit/callback` naar `/signin-oauth/implicit/callback` voor de impliciete toekennings stroom.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de ontwikkelaarsportal:

- [Overzicht van de Azure API Management-ontwikkelaarsportal](api-management-howto-developer-portal.md)
- [De ontwikkelaars Portal openen en aanpassen](api-management-howto-developer-portal-customize.md)