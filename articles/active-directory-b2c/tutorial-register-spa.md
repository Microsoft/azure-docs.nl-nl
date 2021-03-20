---
title: 'Zelfstudie: Een toepassing met één pagina registreren'
titleSuffix: Azure AD B2C
description: Volg deze zelfstudie voor meer informatie over het registreren van een toepassing met één pagina (SPA) in Azure Active Directory B2C met behulp van Azure Portal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 08/19/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d2eaf1dce432821dcfc693dc69dcf975a3d8be8d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92503858"
---
# <a name="tutorial-register-a-single-page-application-spa-in-azure-active-directory-b2c"></a>Zelfstudie: Een toepassing met één pagina (SPA) registreren in Azure Active Directory B2C

Voordat uw [toepassingen](application-types.md) kunnen communiceren met Azure Active Directory B2C (Azure AD B2C), moeten deze worden geregistreerd in een tenant die u beheert. Deze zelfstudie laat zien hoe u een toepassing met één pagina (SPA) registreert met behulp van Azure Portal.

## <a name="overview-of-authentication-options"></a>Overzicht van verificatieopties

Veel moderne webtoepassingen zijn gebouwd als toepassingen met één pagina (SPA’s) aan de clientzijde. Ontwikkelaars schrijven deze met JavaScript of een SPA-framework, zoals Angular, Vue en React. Deze toepassingen worden uitgevoerd in een webbrowser en hebben andere verificatiekenmerken dan traditionele webtoepassingen aan de serverzijde.

Azure AD B2C biedt **twee** opties om toepassingen met één pagina te gebruiken om gebruikers aan te melden en tokens op te halen voor toegang tot back-endservices of web-API’s:

### <a name="authorization-code-flow-with-pkce"></a>Autorisatiecodestroom (met PKCE)
- [OAuth 2.0-autorisatiecodestroom (met PKCE)](./authorization-code-flow.md). De autorisatiecodestroom stelt de toepassing in staat een autorisatiecode uit te wisselen voor **id-tokens**, om de geverifieerde gebruiker te vertegenwoordigen, en de benodigde **toegangstokens** om beveiligde API’s aan te roepen. Daarnaast worden met deze stroom **vernieuwingstokens** geretourneerd, die namens gebruikers langetermijntoegang bieden tot resources, zonder dat interactie met deze gebruikers is vereist. 

Dit is de **aanbevolen** methode. Vernieuwingstokens met een beperkte levensduur helpen om uw toepassing aan te passen aan de [privacybeperkingen van cookies van de moderne browser](../active-directory/develop/reference-third-party-cookies-spas.md), zoals Safari ITP.

De toepassing kan een verificatiebibliotheek gebruiken, zoals [MSAL.js 2.x](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) om te profiteren van deze stroom. 

![Toepassingen met één pagina - verificatie](./media/tutorial-single-page-app/spa-app-auth.svg)

### <a name="implicit-grant-flow"></a>Impliciete toekenningsstroom
- [Impliciete OAuth 2.0-stroom](implicit-flow-single-page-application.md). Sommige frameworks, zoals [MSAL.js 1.x](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp), bieden alleen ondersteuning voor de impliciete toekenningsstroom. De impliciete toekenningsstroom stelt de toepassing in staat om **id-tokens** en **toegangstokens** op te halen. In tegenstelling tot de autorisatiecodestroom retourneert de impliciete toekenningsstroom geen **vernieuwingstoken**. 

![Toepassingen met één pagina - impliciet](./media/tutorial-single-page-app/spa-app.svg)

In deze verificatiestroom zijn geen toepassingsscenario's opgenomen waarin wordt gebruikgemaakt van platformoverschrijdende JavaScript-frameworks, zoals Electron en React-Native. Deze scenario's vereisen extra mogelijkheden voor interactie met de systeemeigen platformen.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Als u nog geen eigen [Azure AD B2C-tenant](tutorial-create-tenant.md) hebt gemaakt, maakt u er nu een. U kunt een bestaande Azure AD B2C-tenant gebruiken.

## <a name="register-the-spa-application"></a>De SPA-toepassing registreren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
1. Selecteer **App-registraties** en selecteer vervolgens **Nieuwe registratie**.
1. Voer een **naam** in voor de toepassing. Bijvoorbeeld *spaapp1*.
1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een id-provider of organisatieadreslijst (voor het verifiëren van gebruikers met gebruikersstromen)**
1. Selecteer onder **Omleidings-URI** de optie **Toepassing met één pagina (SPA)** en voer vervolgens `https://jwt.ms` in het URL-tekstvak in.

    De omleidings-URI is het eindpunt waarnaar de gebruiker wordt gestuurd door de autorisatieserver (Azure AD B2C in dit geval) nadat de interactie met de gebruiker is voltooid en waaraan een toegangstoken of autorisatiecode wordt verzonden wanneer de autorisatie is geslaagd. In een productietoepassing is dit doorgaans een openbaar toegankelijk eindpunt waarop uw app wordt uitgevoerd, zoals `https://contoso.com/auth-response`. Voor testdoeleinden, zoals deze zelfstudie, kunt u dit instellen op `https://jwt.ms`, een webtoepassing van Microsoft die de gedecodeerde inhoud van een token weergeeft (de inhoud van het token verlaat nooit uw browser). Tijdens het ontwikkelen van apps kunt u het eindpunt daar toevoegen waar uw toepassing lokaal luistert, zoals `http://localhost:5000`. U kunt op elk gewenst moment omleidings-URI's toevoegen en wijzigen in uw geregistreerde toepassingen.

    De volgende beperkingen zijn van toepassing op omleidings-URI's:

    * De antwoord-URL moet beginnen met het schema `https`, tenzij `localhost` wordt gebruikt.
    * De antwoord-URL is hoofdlettergevoelig. Het hoofdlettergebruik moet overeenkomen met het URL-pad van de actieve toepassing. Als uw toepassing bijvoorbeeld `.../abc/response-oidc` als deel van het pad bevat, geeft u `.../ABC/response-oidc` niet op in de antwoord-URL. Omdat de webbrowser paden als hoofdlettergevoelig behandelt, kunnen cookies die zijn gekoppeld aan `.../abc/response-oidc` worden uitgesloten als ze worden omgeleid naar de qua hoofdlettergebruik niet-overeenkomende URL `.../ABC/response-oidc`.

1. Selecteer in **Machtigingen** het selectievakje *Beheerdersgoedkeuring verlenen aan machtigingen van OpenID en offline_access*.
1. Selecteer **Registreren**.


## <a name="enable-the-implicit-flow"></a>De impliciete stroom inschakelen
Als u de impliciete stroom gebruikt, moet u de impliciete toekenningsstroom inschakelen in de app-registratie.

1. Selecteer hiervoor **Verificatie** in het linkermenu onder **Beheren**.
1. Schakel onder **Impliciete toekenning** de selectievakjes **Toegangstokens** en **Id-tokens** in.
1. Selecteer **Opslaan**.

## <a name="migrate-from-the-implicit-flow"></a>Migreren vanuit de impliciete stroom

Als u een bestaande toepassing hebt die gebruikmaakt van de impliciete stroom, raden we u aan de autorisatiecodestroom te gebruiken, door gebruik te maken van een framework dat hier ondersteuning voor biedt, zoals [MSAL.js 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser).

Als al uw productietoepassingen met één pagina die worden vertegenwoordigd door een app-registratie, de autorisatiecodestroom gebruiken, schakelt u de instellingen voor de impliciete toekenningsstroom uit. 

1. Selecteer hiervoor **Verificatie** in het linkermenu onder **Beheren**.
1. Schakel onder **Impliciete toekenning** de selectievakjes **Toegangstokens** en **Id-tokens** uit.
1. Selecteer **Opslaan**.

Toepassingen die gebruikmaken van de impliciete stroom, kunnen blijven functioneren als u de impliciete stroom ingeschakeld houdt (aangevinkt).

* * *

## <a name="next-steps"></a>Volgende stappen

U gaat nu leren hoe u gebruikersstromen kunt maken om gebruikers in staat te stellen zich te registreren, zich aan te melden en hun profielen te beheren.

> [!div class="nextstepaction"]
> [Gebruikersstromen maken in Azure Active Directory B2C >](tutorial-create-user-flows.md)
