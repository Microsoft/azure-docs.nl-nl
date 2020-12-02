---
title: 'Snelstart: Aanmelden toevoegen aan een Node.js-web-app | Azure'
titleSuffix: Microsoft identity platform
description: In deze quickstart vindt u meer informatie over de implementatie van verificatie in een Node.js-webtoepassing met behulp van OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, devx-track-js
ms.openlocfilehash: 643305057490cc550a5a8e39a892297b000cbc8e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96169406"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Quickstart: Aanmelden met OpenID Connect bij een Node.js-webtoepassing toevoegen

In deze quickstart downloadt u een codevoorbeeld en voert u dit uit. Het codevoorbeeld laat zien hoe u OpenID Connect-verificatie kunt instellen in een webtoepassing die is gebouwd met behulp van Node.js met Express. Het voorbeeld is ontworpen om te worden uitgevoerd op elk platform.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js](https://nodejs.org/en/download/).

## <a name="register-your-application"></a>Uw toepassing registreren
1. Meld u bij de [Azure-portal](https://portal.azure.com/) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Als uw account in meerdere Azure AD-tenants aanwezig is:
    - Selecteer uw profiel in het menu in de rechterbovenhoek van de pagina en klik op **Schakelen tussen directory's**.
    - Wijzig uw sessie in de Azure AD-tenant waar u de toepassing wilt maken.

1. Ga naar [Azure Active Directory > App-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) om uw app te registreren.

1. Selecteer **Nieuwe registratie.**

1. Wanneer de pagina **Een toepassing registreren** wordt geopend, voert u de registratiegegevens van uw toepassing in:
    - Voer in de sectie **Naam** een beschrijvende naam in. Deze wordt aan gebruikers van de app getoond. Bijvoorbeeld: MyWebApp
    - Selecteer in de sectie **Ondersteunde accounttypen** de optie **Accounts in alle organisatiemappen en persoonlijke Microsoft-accounts (bijvoorbeeld Skype, Xbox, Outlook.com)** .

    Als er meerdere omleidings-URI's zijn, moet u deze later, nadat de app is gemaakt, toevoegen via het tabblad **Verificatie**.

1. Selecteer **Registreren** om de app te maken.

1. Zoek de waarde **Toepassings-ID (client)** op de app-pagina **Overzicht** voor later. U hebt deze waarde nodig om de toepassing later in dit project te configureren.

1. Selecteer in de lijst met pagina’s voor de app de optie **Verificatie**.
    - Selecteer in de sectie **Omleidings-URI's** de optie **Web** in de keuzelijst en voer de volgende omleidings-URI in: `http://localhost:3000/auth/openid/return`
    - Bij **Geavanceerde instellingen** stelt u de **afmeldings-URL** in op `https://localhost:3000`.
    - Schakel in de sectie **Geavanceerde instellingen > Impliciete toekenning** de optie **ID-tokens** in, aangezien voor dit voorbeeld vereist is dat de [Impliciete toekenningsstroom](./v2-oauth2-implicit-grant-flow.md) is ingeschakeld om aanmelding van de gebruiker mogelijk te maken.

1. Selecteer **Opslaan**.

1. Selecteer op de pagina **Certificaten en geheimen** in de sectie **Clientgeheimen** de optie **Nieuw clientgeheim**.
    - Voer een beschrijving in (bijvoorbeeld app-geheim).
    - Selecteer een sleutelduur van **1 jaar, 2 jaar** of **Verloopt nooit**.
    - Wanneer u op de knop **Toevoegen** klikt, wordt de sleutelwaarde weergegeven. Kopieer de sleutelwaarde en sla hem op een veilige plek op.

    U hebt deze sleutel later nodig om de toepassing te configureren. Deze sleutelwaarde wordt niet opnieuw weergegeven en kan niet op een andere manier worden opgehaald. Noteer de waarde daarom zodra deze wordt weergegeven in Azure Portal.

## <a name="download-the-sample-application-and-modules"></a>Download de voorbeeldtoepassing en -modules

Kloon vervolgens de voorbeeldopslagplaats en installeer de NPM-modules.

Vanuit uw shell of opdrachtregel:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

of

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

Voer de volgende opdracht uit in de hoofdmap van het project:

`$ npm install`

## <a name="configure-the-application"></a>De toepassing configureren

Geef de parameters volgens de instructies op in `exports.creds` in config.js.

* Werk `<tenant_name>` in `exports.identityMetadata` bij met de naam van de Azure AD-tenant in de indeling \*.onmicrosoft.com.
* Werk `exports.clientID` bij met de toepassings-id die wordt vermeld in de app-registratie.
* Werk `exports.clientSecret` bij met het toepassingsgeheim dat is vermeld in de app-registratie.
* Werk `exports.redirectUrl` bij met de omleidings-URI die wordt vermeld in de app-registratie.

**Optionele configuratie voor productietoepassingen:**

* Werk `exports.destroySessionUrl` bij in config.js als u een andere `post_logout_redirect_uri` wilt gebruiken.

* Stel `exports.useMongoDBSessionStore` in config.js in op Waar als u [mongoDB](https://www.mongodb.com) of een andere met [ compatibele sessiearchieven wilt gebruiken](https://github.com/expressjs/session#compatible-session-stores).
Het standaard sessiearchief in dit voorbeeld is `express-session`. Het standaard sessiearchief is niet geschikt voor productie.

* Werk `exports.databaseUri` bij als u het mongoDB-sessiearchief en een andere database-URI wilt gebruiken.

* Werk `exports.mongoDBSessionMaxAge` bij. Hier kunt u opgeven hoe lang u een sessie in mongoDB wilt houden. De eenheid is seconde(n).

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

Start de mongoDB-service. Als u het mongoDB-sessiearchief in deze app gebruikt, moet u [mongoDB installeren](http://www.mongodb.org/) en eerst de service starten. Als u het standaard sessiearchief gebruikt, kunt u deze stap overslaan.

Voer de app uit met de volgende opdracht vanaf uw opdrachtregel.

```
$ node app.js
```

**Is de serveruitvoer moeilijk te begrijpen?:** We gebruiken `bunyan` om dit voorbeeld aan het logboek toe te voegen. De console zal niet veel zin hebben, tenzij u ook bunyan installeert en de server zoals hierboven uitvoert, maar deze door de bunyan binaire pijplijn uitvoert:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>U bent klaar!

U hebt een server die wordt uitgevoerd op `http://localhost:3000`.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het web-appscenario dat het Microsoft Identity-platform ondersteunt:
> [!div class="nextstepaction"]
> [Web-app waarmee het gebruikersscenario wordt aangemeld](scenario-web-app-sign-user-overview.md)