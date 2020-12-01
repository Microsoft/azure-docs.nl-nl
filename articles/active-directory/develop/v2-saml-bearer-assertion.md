---
title: Micro soft Identity platform &-bevestigings stroom voor SAML Bearer | Azure
description: Meer informatie over het ophalen van gegevens uit Microsoft Graph zonder de gebruiker om referenties te vragen met behulp van de SAML Bearer-bevestigings stroom.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: kenwith
ms.reviewer: paulgarn
ms.openlocfilehash: 47b036f558628d51242a78c00d2ee17332816d25
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348756"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Micro soft Identity platform en OAuth 2,0-bevestiging stroom voor SAML Bearer
Met de OAuth 2,0-bevestigings stroom voor SAML Bearer kunt u een OAuth-toegangs token aanvragen met behulp van een SAML-verklaring wanneer een client een bestaande vertrouwens relatie moet gebruiken. De hand tekening die wordt toegepast op de SAML-verklaring biedt verificatie van de geautoriseerde app. Een SAML-verklaring is een XML-beveiligings token dat is uitgegeven door een id-provider en wordt gebruikt door een service provider. De service provider vertrouwt op basis van de inhoud om het onderwerp van de bewering te identificeren voor beveiligings doeleinden.

De SAML-bevestiging wordt naar het OAuth-token eindpunt verzonden.  Het eind punt verwerkt de bevestiging en geeft een toegangs token uit op basis van voorafgaande goed keuring van de app. De client hoeft geen vernieuwings token te hebben of op te slaan, noch het client geheim moet worden door gegeven aan het token eindpunt.

De bevestigings stroom van de SAML Bearer is handig bij het ophalen van gegevens uit Microsoft Graph Api's (die alleen gedelegeerde machtigingen ondersteunen) zonder dat de gebruiker om referenties wordt gevraagd. In dit scenario werkt de client referenties verleend, die de voor keur heeft voor achtergrond processen.

U kunt een OAuth-aanvraag voor het verkrijgen van een toegangs token voor de API maken voor toepassingen die interactieve aanmelding op basis van een browser uitvoeren om een SAML-bevestiging te verkrijgen en vervolgens toegang toe te voegen aan een met OAuth beveiligde API (zoals Microsoft Graph). Wanneer de browser wordt omgeleid naar Azure AD om de gebruiker te verifiëren, wordt de sessie door de browser opgehaald via de SAML-aanmelding en hoeft de gebruiker geen referenties in te voeren.

De OAuth-Bearer-bevestigings stroom wordt alleen ondersteund voor gebruikers die zijn geverifieerd met id-providers, zoals Active Directory Federation Services (ADFS), Federated to Azure Active Directory.  De SAML-bevestiging die is verkregen van ADFS, kan worden gebruikt in een OAuth-stroom om de gebruiker te verifiëren.

![OAuth-stroom](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>Grafiek aanroepen met behulp van een SAML Bearer-bevestiging
Laat ons nu weten hoe we de SAML-bewering programmatisch kunnen ophalen. Deze aanpak wordt getest met ADFS. Dit werkt echter met een id-provider die het retour neren van de SAML-bewering programmatisch ondersteunt. Het basis proces is: een SAML-bevestiging ophalen, een toegangs Token ophalen en toegang krijgen tot Microsoft Graph.

### <a name="prerequisites"></a>Vereisten

Een vertrouwens relatie tot stand brengen tussen de autorisatie server/omgeving (Microsoft 365) en de ID-provider, of de verlener van de SAML 2,0 Bearer-verklaring (ADFS). Als u ADFS wilt configureren voor eenmalige aanmelding en als id-provider, kunt u [dit artikel](/archive/blogs/canitpro/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365)raadplegen.

Registreer de toepassing in de [Portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade):
1. Meld u aan bij de [Blade app-registratie van de portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) (u gebruikt de v 2.0-eind punten voor Graph API en moet de toepassing daarom registreren in deze portal. Anders kunnen we de registraties in azure Active Directory gebruiken. 
1. Selecteer **Nieuwe registratie**.
1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in: 
    1. **Naam**: voer een beschrijvende toepassingsnaam in. Deze wordt zichtbaar voor gebruikers van de app.
    1. **Ondersteunde accounttypen**: selecteer voor welke accounts de toepassing ondersteuning moet bieden.
    1. **Omleidings-URI (optioneel)** : Selecteer het type app dat u wilt maken, Web of open bare client (mobiele & bureau blad) en voer vervolgens de omleidings-URI (of antwoord-URL) in voor uw toepassing.
    1. Selecteer **Registreren** wanneer u klaar bent.
1. Noteer de toepassings-ID van de toepassing (client).
1. Selecteer in het linkerdeel venster **certificaten & geheimen**. Klik op **Nieuw client geheim** in het gedeelte **client geheimen** . Kopieer het nieuwe client geheim, u kunt niet ophalen wanneer u de Blade verlaat.
1. Selecteer in het linkerdeel venster **API-machtigingen** en **Voeg vervolgens een machtiging toe**. Selecteer **Microsoft Graph**, vervolgens **gedelegeerde machtigingen** en selecteer vervolgens **Tasks. Read** sinds u de Outlook-Graph API wilt gebruiken. 

Installeer [postman](https://www.getpostman.com/), een hulp programma dat vereist is om de voorbeeld aanvragen te testen.  Later kunt u de aanvragen omzetten in code.

### <a name="get-the-saml-assertion-from-adfs"></a>De SAML-verklaring ophalen van ADFS
Maak een POST-aanvraag naar het ADFS-eind punt met behulp van SOAP-envelop om de SAML-bewering op te halen:

![SAML-bevestiging ophalen](./media/v2-saml-bearer-assertion/2.png)

Header waarden:

![Header waarden](./media/v2-saml-bearer-assertion/3.png)

AD FS-aanvraag tekst:

![AD FS-aanvraag tekst](./media/v2-saml-bearer-assertion/4.png)

Zodra deze aanvraag is verzonden, ontvangt u een SAML-verklaring van ADFS. Alleen de gegevens van het **SAML: Assertion** -label zijn vereist. Converteer deze naar base64-code ring voor gebruik in verdere aanvragen.

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>Het OAuth2-Token ophalen met behulp van de SAML-bewering 
In deze stap haalt u een OAuth2-token op met behulp van het ADFS-bevestigings antwoord.

1. Maak een POST-aanvraag, zoals hieronder wordt weer gegeven met de header waarden:

    ![POST-aanvraag](./media/v2-saml-bearer-assertion/5.png)
1. Vervang **client_id**, **client_secret** en **Assertion** in de hoofd tekst van de aanvraag (de met base64 gecodeerde SAML-bewering heeft de vorige stap verkregen):

    ![Aanvraagbody](./media/v2-saml-bearer-assertion/6.png)
1. Wanneer de aanvraag is voltooid, ontvangt u een toegangs token van Azure Active Directory.

### <a name="get-the-data-with-the-oauth-token"></a>De gegevens ophalen met het OAuth-token

Nadat u het toegangs token hebt ontvangen, roept u de Graph-Api's aan (Outlook-taken in dit voor beeld). 

1. Maak een GET-aanvraag met het toegangs token dat u in de vorige stap hebt opgehaald:

    ![GET-aanvraag](./media/v2-saml-bearer-assertion/7.png)

1. Wanneer de aanvraag is voltooid, ontvangt u een JSON-antwoord.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de verschillende [verificatie stromen en toepassings scenario's](authentication-flows-app-scenarios.md).
