---
title: Automatische gebruikers inrichting inschakelen voor multi tenant-toepassingen-Azure AD
description: Een hand leiding voor onafhankelijke software leveranciers voor het inschakelen van automatische inrichting
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 07/23/2019
ms.author: kenwith
ms.reviewer: zhchia
ms.openlocfilehash: 7bd0fc634109beb6cc674d89f56666e7035d33ef
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255692"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application"></a>Automatische gebruikers inrichting inschakelen voor uw toepassing met meerdere tenants

Automatische gebruikers inrichting is het proces van het automatiseren van het maken, onderhouden en verwijderen van gebruikers identiteiten in doel systemen zoals uw software-as-a-service toepassingen.

## <a name="why-enable-automatic-user-provisioning"></a>Waarom het automatisch inrichten van gebruikers inschakelen?

Toepassingen waarvoor een gebruikers record vereist is in de toepassing voordat de eerste aanmelding van een gebruiker vereist is. Er zijn voor delen voor u als service provider en voor delen van uw klanten.

### <a name="benefits-to-you-as-the-service-provider"></a>Voor delen als service provider

* Verbeter de beveiliging van uw toepassing met behulp van het micro soft Identity-platform.

* Beperk de werkelijke en waargenomen inspanningen van de klant om uw toepassing te nemen.

* Verminder de kosten voor de integratie met meerdere id-providers (id) voor het automatisch inrichten van gebruikers met behulp van systeem voor SCIM-inrichting (Cross-Domain Identity Management).

* Verminder de ondersteunings kosten door uitgebreide logboeken te bieden om klanten te helpen bij het oplossen van problemen met de gebruikers inrichting.

* Verg root de zicht baarheid van uw toepassing in de [Azure AD-App-galerie](https://azuremarketplace.microsoft.com/marketplace/apps).

* Haal een lijst met prioriteiten op op de pagina met zelf studies voor apps.

### <a name="benefits-to-your-customers"></a>Voor delen voor uw klanten

* Verbeter de beveiliging door automatisch de toegang tot uw toepassing te verwijderen voor gebruikers die de rollen wijzigen of de organisatie naar uw toepassing te laten gaan.

* Vereenvoudig het beheer van gebruikers voor uw toepassing door te voor komen dat Human Error en repeterend werk worden geassocieerd met hand matige inrichting.

* Verminder de kosten voor het hosten en onderhouden van aangepast ontwikkelde inrichtings oplossingen.

## <a name="choose-a-provisioning-method"></a>Een inrichtingsmethode kiezen

Azure AD biedt verschillende integratie paden om het automatisch inrichten van gebruikers voor uw toepassing in te scha kelen.

* De [Azure AD-inrichtings service](../app-provisioning/user-provisioning.md) beheert het inrichten en ongedaan maken van de inrichting van gebruikers van Azure AD voor uw toepassing (uitgaande inrichting) en vanuit uw toepassing naar Azure AD (inkomend inrichten). De service maakt verbinding met het systeem voor SCIM-gebruikers beheer API-eind punten die zijn opgenomen in uw toepassing.

* Wanneer u de [Microsoft Graph](/graph/)gebruikt, beheert uw toepassing de inkomende en uitgaande toewijzing van gebruikers en groepen vanuit Azure AD naar uw toepassing door de Microsoft Graph API te doorzoeken.

* De gebruikers inrichting van de just-in-time (SAML JIT)-Security Assertion Markup Language kan worden ingeschakeld als uw toepassing gebruikmaakt van SAML voor Federatie. Er worden claim gegevens gebruikt die in het SAML-token worden verzonden om gebruikers in te richten.

Raadpleeg de vergelijkings tabel op hoog niveau en zie de gedetailleerde informatie over elke optie om te bepalen welke integratie optie voor uw toepassing moet worden gebruikt.

| Mogelijkheden die zijn ingeschakeld of worden uitgebreid met automatische inrichting| Azure AD-inrichtings service (SCIM 2,0)| Microsoft Graph-API (OData v 4.0)| SAML JIT |
|---|---|---|---|
| Gebruikers-en groeps beheer in azure AD| √| √| Alleen gebruiker |
| Gebruikers en groepen beheren die zijn gesynchroniseerd vanuit on-premises Active Directory| √*| √*| Alleen gebruiker * |
| Toegang tot gegevens buiten gebruikers en groepen tijdens het inrichten van toegang tot Microsoft 365 gegevens (teams, share point, E-mail, agenda, documenten, enz.)| X +| √| X |
| Gebruikers maken, lezen en bijwerken op basis van bedrijfs regels| √| √| √ |
| Gebruikers verwijderen op basis van bedrijfs regels| √| √| X |
| Automatische gebruikers inrichting beheren voor alle toepassingen vanuit de Azure Portal| √| X| √ |
| Ondersteuning voor meerdere id-providers| √| X| √ |
| Gast accounts (B2B) ondersteunen| √| √| √ |
| Niet-ondernemings accounts ondersteunen (B2C)| X| √| √ |

<sup>*</sup> – Azure AD Connect Setup is vereist voor het synchroniseren van gebruikers van AD naar Azure AD.  
<sup>+</sup >: Het gebruik van SCIM voor het inrichten verhindert niet dat u uw toepassing kunt integreren met Microsoft Graph voor andere doel einden.

## <a name="azure-ad-provisioning-service-scim"></a>Azure AD-inrichtings service (SCIM)

Azure AD Provisioning Services maakt gebruik van [scim](https://aka.ms/SCIMOverview), een industrie standaard voor het inrichten van een groot aantal id-providers (id) en toepassingen (bijvoorbeeld een toegestane vertraging, g Suite, Dropbox). U wordt aangeraden de Azure AD-inrichtings service te gebruiken als u naast Azure AD id wilt ondersteunen, omdat elke IdP die compatibel is met SCIM, verbinding kan maken met uw SCIM-eind punt. Als u een eenvoudig/User-eind punt bouwt, kunt u inrichting inschakelen zonder dat u uw eigen synchronisatie-engine hoeft te onderhouden. 

Zie voor meer informatie over hoe de Azure AD Provisioning Service-gebruikers SCIM: 

* [Meer informatie over de SCIM-standaard](https://aka.ms/SCIMOverview)

* [Systeem gebruiken voor Identity Management (SCIM) van meerdere domeinen om automatisch gebruikers en groepen in te richten van Azure Active Directory naar toepassingen](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [Meer informatie over de Azure AD SCIM-implementatie](../app-provisioning/use-scim-to-provision-users-and-groups.md)

## <a name="microsoft-graph-for-provisioning"></a>Microsoft Graph voor inrichting

Wanneer u Microsoft Graph gebruikt voor het inrichten, hebt u toegang tot alle uitgebreide gebruikers gegevens die beschikbaar zijn in Graph. Naast de details van gebruikers en groepen kunt u ook aanvullende informatie ophalen, zoals de rollen van de gebruiker, de Manager en de directe rapporten, het eigendom en de geregistreerde apparaten, en honderden andere gegevens die beschikbaar zijn in de [Microsoft Graph](/graph/api/overview). 

Meer dan 15.000.000 organisaties en 90% van Fortune 500-bedrijven gebruiken Azure AD bij het abonneren op micro soft-Cloud Services, zoals Microsoft 365, Microsoft Azure of ENTER prise Mobility Suite. U kunt Microsoft Graph gebruiken om uw app te integreren met beheer werk stromen, zoals het onboarden van werk nemers (en beëindiging), profiel onderhoud en meer. 

Meer informatie over het gebruik van Microsoft Graph voor het inrichten van:

* [Start pagina van Microsoft Graph](https://developer.microsoft.com/graph)

* [Overzicht van Microsoft Graph](/graph/overview)

* [Overzicht van Microsoft Graph auth](/graph/auth/)

* [Aan de slag met Microsoft Graph](https://developer.microsoft.com/graph/get-started)

## <a name="using-saml-jit-for-provisioning"></a>SAML JIT gebruiken voor het inrichten

Als u gebruikers alleen wilt inrichten als u zich voor het eerst aanmeldt bij uw toepassing en de inrichting van gebruikers niet automatisch wilt ongedaan maken, is SAML JIT een optie. Uw toepassing moet SAML 2,0 ondersteunen als een Federatie protocol voor het gebruik van SAML JIT.

SAML JIT gebruikt de informatie over claims in het SAML-token om gebruikers gegevens in de toepassing te maken en bij te werken. Klanten kunnen deze vereiste claims zo nodig in de Azure AD-toepassing configureren. Soms moet de JIT-inrichting worden ingeschakeld aan de kant van de toepassing, zodat de klant deze functie kan gebruiken. SAML JIT is handig voor het maken en bijwerken van gebruikers, maar het is niet mogelijk om de gebruikers in de toepassing te verwijderen of deactiveren.

## <a name="next-steps"></a>Volgende stappen

* [Eenmalige aanmelding inschakelen voor uw toepassing](../develop/v2-howto-app-gallery-listing.md)

* [Dien de aanbieding](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) en partner van uw toepassing in bij micro soft om documentatie te maken op de site van micro soft.

* [Word lid van de Microsoft Partner Network (gratis) en maak uw Go to Market-abonnement](https://partner.microsoft.com/explore/commercial).
