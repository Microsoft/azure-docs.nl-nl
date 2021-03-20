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
ms.openlocfilehash: f7f14b91dc69eeba4ac06f6608f6151634dc38d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100103495"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Quickstart: Aanmelden met OpenID Connect bij een Node.js-webtoepassing toevoegen

In deze quickstart downloadt u een codevoorbeeld en voert u dit uit. Het codevoorbeeld laat zien hoe u OpenID Connect-verificatie kunt instellen in een webtoepassing die is gebouwd met behulp van Node.js met Express. Het voorbeeld is ontworpen om te worden uitgevoerd op elk platform.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js](https://nodejs.org/en/download/).

## <a name="register-your-application"></a>Uw toepassing registreren

1. Meld u aan bij de <a href="https://portal.azure.com/" target="_blank">Azure-portal</a>.
1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.
1. Zoek en selecteer de optie **Azure Active Directory**.
1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen.
1. Selecteer in de sectie **Ondersteunde accounttypen** de optie **Accounts in alle organisatiemappen en persoonlijke Microsoft-accounts (bijvoorbeeld Skype, Xbox, Outlook.com)** .

    Als er meer dan een omleidings-Uri's zijn, voegt u deze toe vanaf het tabblad **verificatie** later nadat de app is gemaakt.

1. Selecteer **Registreren** om de app te maken.
1. Zoek de waarde **Toepassings-ID (client)** op de app-pagina **Overzicht** voor later. U hebt deze waarde nodig om de toepassing later in dit project te configureren.
1. Selecteer **Verificatie** onder **Beheren**.
1. Selecteer **Een platform toevoegen** > **Web**. 
1. Voer in de sectie **Omleidings-URI's** `http://localhost:3000/auth/openid/return` in.
1. Voer een **URL voor het afmelden** van het kanaal in `https://localhost:3000` .
1. Selecteer in de sectie **impliciete toekenning en hybride stromen** **id-tokens** als voor dit voor beeld moet de [impliciete toekennings stroom](./v2-oauth2-implicit-grant-flow.md) zijn ingeschakeld om de gebruiker aan te melden.
1. Selecteer **Configureren**.
1. Selecteer onder **Beheren** achtereenvolgens **Certificaten en geheimen** > **Nieuw clientgeheim**.
1. Voer een beschrijving in (bijvoorbeeld app-geheim).
1. Selecteer een sleutelduur van **1 jaar, 2 jaar** of **Verloopt nooit**.
1. Selecteer **Toevoegen**. De sleutelwaarde wordt weergegeven. Kopieer de sleutelwaarde en sla deze op een veilige plek op.


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