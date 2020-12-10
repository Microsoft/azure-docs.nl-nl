---
title: Nieuwe App-registraties ervaring in Azure AD B2C
description: Een inleiding tot de nieuwe app-registratie ervaring in Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/25/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7bb38b382add2a0ea3506c31c5e6e76a985df6a1
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96936468"
---
# <a name="the-new-app-registrations-experience-for-azure-active-directory-b2c"></a>De nieuwe App-registraties ervaring voor Azure Active Directory B2C

De nieuwe **[app-registraties](https://aka.ms/b2cappregistrations)** -ervaring voor Azure Active Directory B2C (Azure AD B2C) is nu algemeen beschikbaar. Als u bekend bent met de ervaring van **toepassingen** voor het registreren van toepassingen voor Azure AD B2C, wordt dit hier beschreven als ' verouderde ervaring ' in deze hand leiding kunt u aan de slag met de nieuwe ervaring.

## <a name="overview"></a>Overzicht
Voorheen moest u uw Azure AD B2C consumenten toepassingen onafhankelijk van de rest van uw apps beheren met behulp van de oude ervaring. Dat heeft verschillende ervaring voor het maken van apps op verschillende locaties in Azure.

De nieuwe ervaring toont alle Azure AD B2C app-registraties en Azure AD-App-registraties op één locatie en biedt een consistente manier om deze te beheren. Van het maken van een klant gerichte app voor het beheren van een app met Microsoft Graph machtigingen voor resource beheer, hoeft u slechts één manier te leren om dingen te doen.

U kunt de nieuwe ervaring bereiken door te navigeren naar **app-registraties** in een Azure AD B2C-Tenant van de **Azure AD B2C** of de **Azure Active Directory** Services in de Azure Portal.

De Azure AD B2C App-registraties-ervaring is gebaseerd op de [ervaring](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) voor het registreren van algemene apps voor elke Azure AD-Tenant, maar is afgestemd op Azure AD B2C-tenants.

## <a name="whats-not-changing"></a>Wat wordt er niet gewijzigd?
- Uw toepassingen en gerelateerde configuraties kunnen worden gevonden in de nieuwe ervaring. U hoeft de toepassingen niet opnieuw te registreren, en gebruikers van uw toepassingen hoeven zich niet opnieuw aan te melden.

> [!NOTE]
> Als u alle eerder gemaakte toepassingen wilt weer geven, gaat u naar de Blade **app-registraties** en selecteert u het tabblad **alle toepassingen** . Hiermee worden apps weer gegeven die zijn gemaakt in de verouderde ervaring, de nieuwe ervaring en de toepassingen die zijn gemaakt in de Azure AD-service.

## <a name="key-new-features"></a>Belang rijke nieuwe functies

-   In een **lijst met geïntegreerde apps** worden alle toepassingen weer gegeven die worden geverifieerd met Azure AD B2C en Azure AD op één handige locatie. Daarnaast kunt u gebruikmaken van functies **die** al beschikbaar zijn voor Azure AD-toepassingen, zoals de datum, de **certificaten & de geheimen** , de zoek balk en nog veel meer.

-   Bij het registreren van **gecombineerde apps** kunt u snel een app registreren, of het nu gaat om een klant gerichte app of een app voor toegang tot Microsoft Graph.

- Met het deel venster **eind punten** kunt u snel de relevante eind punten voor uw scenario identificeren, zoals OpenID Connect Connect-configuratie, SAML-meta gegevens, Microsoft Graph-API en [OAuth 2,0-gebruikers stroom eind punten](tokens-overview.md#endpoints).

- **API-machtigingen** en bieden **een API** voor uitgebreidere scope-, machtigings-en toestemming beheer. U kunt nu ook MS Graph en Azure AD Graph-machtigingen toewijzen aan een app.

-   **Eigen aars** en **manifest** zijn nu beschikbaar voor apps die met Azure AD B2C worden geverifieerd. U kunt eigen aren toevoegen voor uw registraties en toepassings eigenschappen rechtstreeks bewerken [met behulp van de manifest editor](../active-directory/develop/reference-app-manifest.md).


## <a name="new-supported-account-types"></a>Nieuwe ondersteunde account typen

In de nieuwe ervaring selecteert u een type ondersteunings account uit de volgende opties:
- Alleen accounts in deze organisatiemap
- Accounts in elke organisatie Directory (een Azure AD-Directory – multi tenant)
- Accounts in een id-provider of organisatie Directory (voor het verifiëren van gebruikers met gebruikers stromen)

Als u meer wilt weten over de verschillende typen accounts, selecteert u **Help mij kiezen** tijdens het maken.

In de verouderde ervaring werden apps altijd gemaakt als klant gerichte toepassingen. Voor deze apps wordt het account type ingesteld op **accounts in een id-provider of organisatie Directory (voor verificatie van gebruikers met gebruikers stromen)**.
> [!NOTE]
> Deze optie is vereist om Azure AD B2C gebruikers stromen te kunnen uitvoeren om gebruikers voor deze toepassing te verifiëren. Meer informatie [over het registreren van een toepassing voor gebruik met gebruikers stromen.](tutorial-register-applications.md)

U kunt deze optie ook gebruiken om Azure AD B2C als een SAML-service provider te gebruiken. [Meer informatie](identity-provider-adfs.md).

## <a name="applications-for-devops-scenarios"></a>Toepassingen voor DevOps-scenario's
U kunt de andere account typen gebruiken om een app te maken voor het beheren van uw DevOps-scenario's, zoals het gebruik van Microsoft Graph voor het uploaden van identiteits experience Framework-beleids regels of het inrichten van gebruikers. Meer informatie [over hoe u een Microsoft Graph-toepassing registreert om Azure AD B2C resources te beheren](microsoft-graph-get-started.md).

U ziet mogelijk niet alle Microsoft Graph machtigingen, omdat veel van deze machtigingen niet van toepassing zijn op gebruikers van Azure B2C Consumer. [Meer informatie over het beheren van gebruikers met behulp van Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="admin-consent-and-offline_accessopenid-scopes"></a>Beheerders toestemming en offline_access + OpenID Connect-bereiken
<!-- Azure AD B2C doesn't support user consent. That is, when a user signs into an application, the user doesn't see a screen requesting consent for the application permissions. All permissions have to be granted through admin consent.  -->

Het bereik van de **OpenID Connect** is nodig zodat Azure AD B2C gebruikers in een app kunnen ondertekenen. Het **offline_access** bereik is vereist voor het uitgeven van vernieuwings tokens voor een gebruiker. Deze bereiken zijn eerder toegevoegd en de beheerder heeft standaard toestemming gegeven. U kunt nu eenvoudig machtigingen voor deze bereiken toevoegen tijdens het maken van het proces door ervoor te zorgen dat de optie **verlenende beheerder toestemming geeft voor OpenID Connect en offline_access machtigingen** is geselecteerd. Anders kunnen de Microsoft Graph machtigingen worden toegevoegd met beheerders toestemming in de instellingen voor **API-machtigingen** voor een bestaande app.

Meer informatie over [machtigingen en toestemming](../active-directory/develop/v2-permissions-and-consent.md).

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Platformen/authenticatie: antwoord-Url's/omleidings-Uri's
In de verouderde ervaring zijn de verschillende platform typen beheerd onder **Eigenschappen** als antwoord-url's voor web-apps/api's en omleidings-URI voor native clients. ' Native clients ' zijn ook bekend als ' open bare clients ' en bevatten apps voor iOS-, macOS-, Android-en andere typen mobiele en desktop toepassingen.

In de nieuwe ervaring worden antwoord-Url's en omleidings-Uri's beide aangeduid als omleidings-Uri's. Dit vindt u in de sectie **verificatie** van een app. App-registraties zijn niet beperkt tot een web-app of een systeem eigen toepassing. U kunt dezelfde app-registratie voor al deze platform typen gebruiken door de respectieve omleidings-Uri's te registreren.

Omleidings-Uri's moeten worden gekoppeld aan een app-type, hetzij Web ofwel openbaar (mobiel en desktop). [Meer informatie over omleidings-Uri's](../active-directory/develop/quickstart-register-app.md#add-a-redirect-uri)

<!-- Whether an application should be treated as a public client is inferred at run-time from the Redirect URI platform type, if possible. The **Treat application as a public client** setting should be set to **Yes** for flows that might not use a redirect URI, such as ROPC flows. -->

De **IOS/macOS-** en **Android** -platformen zijn een type open bare client. Ze bieden een eenvoudige manier om iOS/macOS-of Android-apps met bijbehorende omleidings-Uri's te configureren voor gebruik met MSAL. Meer informatie over de [configuratie opties](../active-directory/develop/msal-client-applications.md)voor de toepassing.


## <a name="application-certificates--secrets"></a>Toepassings certificaten & geheimen

In de nieuwe ervaring, in plaats van **sleutels**, gebruikt u de Blade **certificaten & geheimen** voor het beheren van certificaten en geheimen. Met certificaten & geheimen kunnen toepassingen zichzelf identificeren bij de verificatie service bij het ontvangen van tokens op een webadresseer bare locatie (met behulp van een HTTPS-schema). U wordt aangeraden een certificaat te gebruiken in plaats van een client geheim voor client referentie scenario's bij het verifiëren van Azure AD. Certificaten kunnen niet worden gebruikt om te verifiëren tegen Azure AD B2C.


## <a name="features-not-applicable-in-azure-ad-b2c-tenants"></a>Functies die niet van toepassing zijn in Azure AD B2C tenants
De volgende Azure AD-App-registratie mogelijkheden zijn niet van toepassing op of beschikbaar in Azure AD B2C tenants:
- **Rollen en beheerders** : momenteel niet beschikbaar voor Azure AD B2C.
- **Branding** -gebruikers interface/UX-aanpassing is geconfigureerd in de **huis stijl** van het bedrijf of als onderdeel van een gebruikers stroom. Meer informatie over [het aanpassen van de gebruikers interface in azure Active Directory B2C](customize-ui-overview.md).
- **Verificatie** van de uitgever van het domein-uw app is geregistreerd op *. onmicrosoft.com*, dat geen geverifieerd domein is. Daarnaast wordt het domein van de uitgever voornamelijk gebruikt voor het verlenen van toestemming van de gebruiker, wat niet van toepassing is op Azure AD B2C-apps voor gebruikers verificatie. [Meer informatie over het Publisher-domein](../active-directory/develop/howto-configure-publisher-domain.md).
- **Token configuratie** -het token is geconfigureerd als onderdeel van een gebruikers stroom in plaats van een app.
- De **Quick** starts zijn momenteel niet beschikbaar voor Azure AD B2C-tenants.
<!-- - The **Integration assistant** blade is currently not available for Azure AD B2C tenants. -->

## <a name="limitations"></a>Beperkingen
De nieuwe ervaring heeft de volgende beperkingen:
- Op dit moment kan Azure AD B2C geen onderscheid maken tussen het verlenen van toegang of ID-tokens voor impliciete stromen. beide typen tokens zijn beschikbaar voor impliciete toekennings stroom als de optie **id-tokens** is geselecteerd op de Blade **authenticatie** .
<!-- - Azure AD B2C doesn't currently support the single-page application "SPA" app type.  -->
- Het wijzigen van de waarde voor ondersteunde accounts wordt niet ondersteund in de gebruikers interface. U moet het app-manifest gebruiken, tenzij u overschakelt tussen Azure AD en multi tenant.

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan met de nieuwe app registratie-ervaring:
* Meer informatie [over het registreren van een webtoepassing](tutorial-register-applications.md).
* Meer informatie [over het registreren van een web-API](add-web-api-application.md).
* Meer informatie [over het registreren van een systeem eigen client toepassing](add-native-application.md).
* Meer informatie [over hoe u een Microsoft Graph-toepassing registreert om Azure AD B2C resources te beheren](microsoft-graph-get-started.md).
* Meer informatie [over het gebruik van Azure AD B2C als een SAML-service provider.](identity-provider-adfs.md)
* Meer informatie over [toepassings typen](application-types.md).