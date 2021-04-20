---
title: Migreren naar de nieuwe ontwikkelaarsportal vanuit de verouderde ontwikkelaarsportal
titleSuffix: Azure API Management
description: Meer informatie over het migreren van de verouderde ontwikkelaarsportal naar de nieuwe ontwikkelaarsportal in API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: apimpm
ms.openlocfilehash: e4f9f3822b58886f7d453d52402b078d8401133f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738878"
---
# <a name="migrate-to-the-new-developer-portal"></a>Migreren naar de nieuwe ontwikkelaarsportal

In dit artikel worden de stappen beschreven die u moet nemen om te migreren van de afgeschafte verouderde portal naar de nieuwe ontwikkelaarsportal in API Management.

> [!IMPORTANT]
> De verouderde ontwikkelaarsportal is nu afgeschaft en ontvangt alleen beveiligingsupdates. U kunt deze zoals gebruikelijk blijven gebruiken tot de buitengebruikstelling in oktober 2023, wanneer de portal wordt verwijderd uit alle API Management Services.

![API Management-ontwikkelaarsportal](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="improvements-in-new-developer-portal"></a>Verbeteringen in de nieuwe ontwikkelaarsportal

De nieuwe ontwikkelaarsportal biedt een aantal beperkingen van de afgeschafte portal. Het bevat een [visuele editor voor slepen en neerzetten voor](api-management-howto-developer-portal-customize.md) het bewerken van inhoud en een speciaal deelvenster voor ontwerpers om de website vorm te geven. Pagina's, aanpassingen en configuratie worden opgeslagen als Azure Resource Manager resources in uw API Management service, waarmee u [portalimplementaties kunt automatiseren.](automate-portal-deployments.md) Ten laatste is de codebasis van de portal opensource, zodat [u deze kunt uitbreiden met aangepaste functionaliteit.](api-management-howto-developer-portal.md#managed-vs-self-hosted)

## <a name="how-to-migrate-to-new-developer-portal"></a>Migreren naar nieuwe ontwikkelaarsportal

De nieuwe ontwikkelaarsportal is niet compatibel met de afgeschafte portal en geautomatiseerde migratie is niet mogelijk. U moet de inhoud (pagina's, tekst, mediabestanden) handmatig opnieuw maken en het uiterlijk van de nieuwe portal aanpassen. De precieze stappen zijn afhankelijk van de aanpassingen en complexiteit van uw portal. Raadpleeg de [zelfstudie over de ontwikkelaarsportal](api-management-howto-developer-portal-customize.md) voor hulp. Resterende configuratie, zoals de lijst met API's, producten, gebruikers en id-providers, wordt automatisch gedeeld in beide portals.

> [!IMPORTANT]
> Als u de nieuwe ontwikkelaarsportal al eerder hebt gestart, maar u nog geen wijzigingen hebt aangebracht, stelt u de standaardinhoud opnieuw in om deze bij te werken naar de nieuwste versie.

Wanneer u migreert vanuit de afgeschafte portal, moet u rekening houden met de volgende wijzigingen:

- Als u uw ontwikkelaarsportal beschikbaar maakt via een aangepast domein, wijst [u een domein toe](configure-custom-domain.md) aan de nieuwe ontwikkelaarsportal. Gebruik de **optie Ontwikkelaarsportal** in de vervolgkeuzepagina in Azure Portal.
- [Pas een CORS-beleid toe](developer-portal-faq.md#cors) op uw API's om de interactieve testconsole in teschakelen.
- Als u aangepaste CSS in de stijl van de portal injecteert, moet u de stijl repliceren met behulp [van het ingebouwde ontwerppaneel](api-management-howto-developer-portal-customize.md). CSS-injectie is niet toegestaan in de nieuwe portal.
- U kunt aangepaste JavaScript alleen in de [zelf-hostende versie van de nieuwe portal injecteren.](api-management-howto-developer-portal.md#managed-vs-self-hosted)
- Als uw API Management zich in een virtueel netwerk en via Application Gateway beschikbaar is gesteld aan [internet,](api-management-howto-integrate-internal-vnet-appgateway.md) raadpleegt u dit documentatieartikel voor nauwkeurige configuratiestappen. U moet het volgende doen:

    - Schakel connectiviteit met het API Management van de API Management in.
    - Connectiviteit met het nieuwe portal-eindpunt inschakelen.
    - Geselecteerde regels Web Application Firewall uitschakelen.

- Als u de standaardsjablonen voor e-mailmeldingen hebt gewijzigd om een expliciet gedefinieerde afgeschafte portal-URL op te nemen, wijzigt u deze in de parameter portal-URL of wijs deze naar de nieuwe portal-URL. Als de sjablonen in plaats daarvan de ingebouwde URL-parameter van de portal gebruiken, zijn er geen wijzigingen vereist.
- *Problemen* en *toepassingen* worden niet ondersteund in de nieuwe ontwikkelaarsportal.
- Directe integratie met Facebook, Microsoft, Twitter en Google als id-providers wordt niet ondersteund in de nieuwe ontwikkelaarsportal. U kunt integreren met deze providers via Azure AD B2C.
- Als u delegatie gebruikt, wijzigt u de retour-URL in uw toepassingen en gebruikt u het API-eindpunt Gedeelde toegangs [ *token*](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken) op halen in plaats van het *eindpunt SSO-URL* genereren.
- Als u Azure AD als id-provider gebruikt:

    - Wijzig de retour-URL in uw toepassing om te wijzen naar het nieuwe domein van de ontwikkelaarsportal.
    - Wijzig het achtervoegsel van de retour-URL in uw toepassing van `/signin-aad` in `/signin` .

- Als u Azure AD B2C id-provider gebruikt:

    - Wijzig de retour-URL in uw toepassing om te wijzen naar het nieuwe domein van de ontwikkelaarsportal.
    - Wijzig het achtervoegsel van de retour-URL in uw toepassing van `/signin-aad` in `/signin` .
    - Neem *de voornaam,* *achternaam* en *object-id van de gebruiker op* in de toepassingsclaims.

- Als u OAuth 2.0 gebruikt in de interactieve testconsole, wijzigt u de retour-URL in uw toepassing om te wijzen naar het nieuwe domein van de ontwikkelaarsportal en wijzigt u het achtervoegsel:

    - Van `/docs/services/[serverName]/console/oauth2/authorizationcode/callback` naar voor de stroom voor het verlenen van `/signin-oauth/code/callback/[serverName]` autorisatiecode.
    - Van `/docs/services/[serverName]/console/oauth2/implicit/callback` naar voor de `/signin-oauth/implicit/callback` impliciete toekenningsstroom.
- Als u OpenID Connect in de interactieve testconsole gebruikt, wijzigt u de retour-URL in uw toepassing om te wijzen naar het nieuwe domein van de ontwikkelaarsportal en wijzigt u het achtervoegsel:

    - Van `/docs/services/[serverName]/console/openidconnect/authorizationcode/callback` naar voor de stroom voor het verlenen van `/signin-oauth/code/callback/[serverName]` autorisatiecode.
    - Van `/docs/services/[serverName]/console/openidconnect/implicit/callback` naar voor de `/signin-oauth/implicit/callback` impliciete toekenningsstroom.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de ontwikkelaarsportal:

- [Overzicht van de Azure API Management-ontwikkelaarsportal](api-management-howto-developer-portal.md)
- [De ontwikkelaarsportal openen en aanpassen](api-management-howto-developer-portal-customize.md)