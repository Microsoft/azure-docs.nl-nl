---
title: Problemen met aanmelden bij apps die zijn geconfigureerd voor eenmalige aanmelding op basis van SAML
description: Richt lijnen voor de specifieke fouten bij het aanmelden bij een toepassing die u hebt geconfigureerd voor op SAML gebaseerde federatieve eenmalige aanmelding met Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/18/2019
ms.author: kenwith
ms.reviewer: luleon, asteen
ms.custom: contperf-fy21q2
ms.openlocfilehash: 62b828a23805c6d3ab544dd2b9f78b45a3327cde
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053394"
---
# <a name="problems-signing-in-to-saml-based-single-sign-on-configured-apps"></a>Problemen met aanmelden bij apps die zijn geconfigureerd voor eenmalige aanmelding op basis van SAML
Voor het oplossen van de onderstaande aanmeldings problemen raden we het volgende aan om de oplossings stappen beter te diagnosticeren en te automatiseren:

- Installeer de [browseruitbreiding Mijn beveiligde apps](my-apps-deployment-plan.md) om Azure AD (Azure Active Directory) te helpen betere diagnose en oplossingen te bieden bij het gebruik van de testversie in de Azure-portal.
- Reproduceer de fout via de testversie op de app-configuratiepagina in de Azure-portal. Meer informatie over het [opsporen van op SAML gebaseerde toepassingen voor eenmalige aanmelding](./debug-saml-sso-issues.md)

Als u de [test ervaring](./debug-saml-sso-issues.md) in de Azure Portal met de beveiligde browser uitbreiding van mijn apps gebruikt, hoeft u de onderstaande stappen niet hand matig uit te voeren om de configuratie pagina voor eenmalige aanmelding op basis van SAML te openen.

Ga als volgt te werk om de configuratiepagina voor eenmalige aanmelding op basis van SAML te openen:
1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **coadmin**.
1.  Open de **uitbrei ding Azure Active Directory** door **alle services** boven aan het hoofd navigatie menu te selecteren.
1.  Typ **' Azure Active Directory '** in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.
1.  Selecteer **Bedrijfstoepassingen** in het linkernavigatiemenu van Azure Active Directory.
1.  Selecteer **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

    Als u de gewenste toepassing niet ziet, gebruikt u het besturingselement **Filter** bovenaan de **lijst Alle toepassingen**, en stelt u de optie **Weergeven** in op **Alle toepassingen**.

1.  Selecteer de toepassing die u wilt configureren voor eenmalige aanmelding.
1. Zodra de toepassing is geladen, selecteert u **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.
1. Selecteer Eenmalige aanmelding op basis van SAML.

## <a name="application-not-found-in-directory"></a>Kan de toepassing niet vinden in de map
`Error AADSTS70001: Application with Identifier 'https:\//contoso.com' was not found in the directory.`

**Mogelijke oorzaak**

Het `Issuer` kenmerk dat vanuit de toepassing naar Azure AD wordt verzonden in de SAML-aanvraag komt niet overeen met de id-waarde die is geconfigureerd voor de toepassing in azure AD.

**Oplossing**

Zorg ervoor dat het `Issuer` kenmerk in de SAML-aanvraag overeenkomt met de id-waarde die is geconfigureerd in azure AD.

Controleer op de pagina op SAML gebaseerde SSO-configuratie in de sectie **basis configuratie van SAML** of de waarde in het tekstvak id overeenkomt met de waarde voor de id-waarde die wordt weer gegeven in de fout.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>Het antwoord adres komt niet overeen met de antwoord adressen die voor de toepassing zijn geconfigureerd
`Error AADSTS50011: The reply address 'https:\//contoso.com' does not match the reply addresses configured for the application.`

**Mogelijke oorzaak**

De `AssertionConsumerServiceURL` waarde in de SAML-aanvraag komt niet overeen met de antwoord-URL-waarde of het patroon dat is geconfigureerd in azure AD. De `AssertionConsumerServiceURL` waarde in de SAML-aanvraag is de URL die u in de fout ziet.

**Oplossing**

Zorg ervoor dat de `AssertionConsumerServiceURL` waarde in de SAML-aanvraag overeenkomt met de antwoord-URL-waarde die is geconfigureerd in azure AD. 

Controleer of werk de waarde in het tekstvak antwoord-URL in op overeenkomst met de `AssertionConsumerServiceURL` waarde in de SAML-aanvraag.   

Nadat u de waarde voor de antwoord-URL in azure AD hebt bijgewerkt en deze overeenkomt met de waarde die door de toepassing in de SAML-aanvraag is verzonden, moet u zich kunnen aanmelden bij de toepassing.

## <a name="user-not-assigned-a-role"></a>Gebruiker heeft geen rol toegewezen
`Error AADSTS50105: The signed in user 'brian\@contoso.com' is not assigned to a role for the application.`

**Mogelijke oorzaak**

De gebruiker heeft in Azure AD geen toegang gekregen tot de toepassing.

**Oplossing**

Als u een of meer gebruikers rechtstreeks aan een toepassing wilt toewijzen, raadpleegt u [Quick Start: gebruikers toewijzen aan een app](add-application-portal-assign-users.md).

## <a name="not-a-valid-saml-request"></a>Geen geldige SAML-aanvraag
`Error AADSTS75005: The request is not a valid Saml2 protocol message.`

**Mogelijke oorzaak**

Azure AD biedt geen ondersteuning voor de SAML-aanvraag die met de toepassing is verzonden voor eenmalige aanmelding. Enkele veelvoorkomende problemen zijn:
- Ontbrekende vereiste velden in de SAML-aanvraag
- Gecodeerde methode voor SAML-aanvraag

**Oplossing**

1. Leg de SAML-aanvraag vast. Volg de zelf studie voor het [opsporen van op SAML gebaseerde eenmalige aanmelding bij toepassingen in azure AD](./debug-saml-sso-issues.md) voor meer informatie over het vastleggen van de SAML-aanvraag.
1. Neem contact op met de leverancier van de toepassing en verstrek de volgende gegevens:
    - SAML-aanvraag
    - [Vereisten voor het SAML-protocol voor eenmalige aanmelding voor Azure AD](../develop/single-sign-on-saml-protocol.md)

De leverancier van de toepassing moet valideren dat de Azure AD SAML-implementatie voor eenmalige aanmelding wordt ondersteund.

## <a name="misconfigured-application"></a>Onjuist geconfigureerde toepassing
`Error AADSTS650056: Misconfigured application. This could be due to one of the following: The client has not listed any permissions in the requested permissions in the client's application registration. Or, The admin has not consented in the tenant. Or, Check the application identifier in the request to ensure it matches the configured client application identifier. Please contact your admin to fix the configuration or consent on behalf of the tenant.`

**Mogelijke oorzaak**

Het `Issuer` kenmerk dat vanuit de toepassing naar Azure AD is verzonden in de SAML-aanvraag komt niet overeen met de id-waarde die is geconfigureerd voor de toepassing in azure AD.

**Oplossing**

Zorg ervoor dat het `Issuer` kenmerk in de SAML-aanvraag overeenkomt met de id-waarde die is geconfigureerd in azure AD. 

Controleer of de waarde in het tekstvak id overeenkomt met de waarde voor de id-waarde die wordt weer gegeven in de fout.

## <a name="certificate-or-key-not-configured"></a>Het certificaat of de sleutel is niet geconfigureerd
`Error AADSTS50003: No signing key configured.`

**Mogelijke oorzaak**

Het toepassings object is beschadigd en het certificaat dat is geconfigureerd voor de toepassing wordt niet herkend door Azure AD.

**Oplossing**

Voer de volgende stappen uit om een nieuw certificaat te verwijderen en te maken:
1. Selecteer in het configuratie scherm op SAML gebaseerde SSO de optie **Nieuw certificaat maken** onder de sectie **SAML-handtekening certificaat** .
1. Selecteer verval datum en klik vervolgens op **Opslaan**.
1. Schakel het selectie vakje **Nieuw certificaat actief maken** in om het actieve certificaat te onderdrukken. Klik vervolgens op **Opslaan** boven in het deelvenster en accepteer het rollovercertificaat om het te activeren.
1. Klik onder de sectie **SAML-handtekening certificaat** op **verwijderen** om het **ongebruikte** certificaat te verwijderen.

## <a name="saml-request-not-present-in-the-request"></a>SAML-aanvraag is niet aanwezig in de aanvraag
`Error AADSTS750054: SAMLRequest or SAMLResponse must be present as query string parameters in HTTP request for SAML Redirect binding.`

**Mogelijke oorzaak**

Azure AD kan de SAML-aanvraag niet identificeren binnen de URL-para meters in de HTTP-aanvraag. Dit kan gebeuren als de toepassing geen gebruik maakt van een binding voor HTTP-omleiding bij het verzenden van de SAML-aanvraag naar Azure AD.

**Oplossing**

De toepassing moet de SAML-aanvraag die is gecodeerd naar de locatie header verzenden met behulp van binding van HTTP-omleiding. Lees de sectie over binding voor HTTP-omleidingen in het [specificatiedocument over het SAML-protocol](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD stuurt het token naar een onjuist eind punt
**Mogelijke oorzaak**

Als de aanmeldings aanvraag bij eenmalige aanmelding geen expliciete antwoord-URL (assertion Consumer Service-URL) bevat, selecteert Azure AD een van de geconfigureerde antwoord-Url's voor die toepassing. Zelfs als de toepassing een expliciete antwoord-URL heeft geconfigureerd, kan de gebruiker worden omgeleid https://127.0.0.1:444 . 

Toen de toepassing werd toegevoegd als niet-galerie-app, is deze antwoord-URL in Azure Active Directory gemaakt als standaardwaarde. Dit gedrag is gewijzigd en deze URL wordt niet meer standaard toegevoegd in Azure Active Directory. 

**Oplossing**

Verwijder de ongebruikte antwoord-Url's die voor de toepassing zijn geconfigureerd.

Op de configuratie pagina op SAML gebaseerde SSO gaat u naar de sectie **antwoord-URL (assertion Consumer Service-URL)** en verwijdert u ongebruikte of standaard antwoord-url's die zijn gemaakt door het systeem. Bijvoorbeeld `https://127.0.0.1:444/applications/default.aspx`.


## <a name="authentication-method-by-which-the-user-authenticated-with-the-service-doesnt-match-requested-authentication-method"></a>Verificatie methode waarbij de gebruiker die is geverifieerd met de service, niet overeenkomt met de aangevraagde verificatie methode
`Error: AADSTS75011 Authentication method by which the user authenticated with the service doesn't match requested authentication method 'AuthnContextClassRef'. `

**Mogelijke oorzaak**

De `RequestedAuthnContext` bevindt zich in de SAML-aanvraag. Dit betekent dat de app de `AuthnContext` opgegeven door heeft verwacht `AuthnContextClassRef` . De gebruiker heeft echter al een verificatie uitgevoerd voordat de toepassing wordt geopend en de `AuthnContext` (verificatie methode) die wordt gebruikt voor de vorige verificatie verschilt van het aangevraagde. Bijvoorbeeld, een federatieve gebruiker heeft toegang tot MyApps en WIA. De `AuthnContextClassRef` zal zijn `urn:federation:authentication:windows` . AAD voert geen nieuwe verificatie aanvraag uit, gebruikt de verificatie context die door de IdP (ADFS of een andere Federation service in dit geval) is door gegeven. Daarom is er een niet-overeenkomend verzoek als de app andere aanvragen dan `urn:federation:authentication:windows` . Een ander scenario is wanneer meerdere feiten zijn gebruikt: `'X509, MultiFactor` .

**Oplossing**


`RequestedAuthnContext` is een optionele waarde. Als dat mogelijk is, vraagt u de toepassing als deze kan worden verwijderd.

Een andere optie is om er zeker van te zijn dat de `RequestedAuthnContext` wordt gehonoreerd. Dit wordt gedaan door een nieuwe verificatie aan te vragen. Als u dit doet, wordt er een nieuwe verificatie uitgevoerd wanneer de SAML-aanvraag wordt verwerkt `AuthnContext` . Als u een nieuwe verificatie wilt aanvragen, bevat de SAML-aanvraag de waarde `forceAuthn="true"` . 



## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Probleem bij het aanpassen van de SAML-claims die worden verzonden naar een toepassing
Zie [claims toewijzen in azure Active Directory](../develop/active-directory-claims-mapping.md)voor meer informatie over het aanpassen van de SAML-kenmerk claims die naar uw toepassing worden verzonden.

## <a name="errors-related-to-misconfigured-apps"></a>Fouten met betrekking tot onjuist geconfigureerde apps
Controleer of beide configuraties in de portal overeenkomen met wat u in uw app hebt. Vergelijk met name de client/toepassings-ID, antwoord-Url's, client geheimen/sleutels en de URI van de App-ID.

Vergelijk de resource waarmee u toegang vraagt in code met de geconfigureerde machtigingen op het tabblad **vereiste resources** om ervoor te zorgen dat u alleen de resources opvraagt die u hebt geconfigureerd.

## <a name="next-steps"></a>Volgende stappen
- [Quickstartreeks over toepassingsbeheer](add-application-portal-assign-users.md)
- [Fout opsporing op SAML gebaseerde eenmalige aanmelding bij toepassingen in azure AD](./debug-saml-sso-issues.md)
- [Vereisten voor het SAML-protocol voor eenmalige aanmelding voor Azure AD](../develop/single-sign-on-saml-protocol.md)