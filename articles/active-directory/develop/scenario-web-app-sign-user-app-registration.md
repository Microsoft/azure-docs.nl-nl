---
title: Een web-app registreren die zich aanmeldt bij gebruikers | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het registreren van een web-app die wordt aangemeld bij gebruikers
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 920249aa252469c3db2be284fc010d775d04c921
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578274"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>Web-app die zich aanmeldt bij gebruikers: app-registratie

In dit artikel worden de stappen voor het registreren van apps beschreven voor een web-app die gebruikers aantekent.

Als u uw toepassing wilt registreren, kunt u het volgende gebruiken:

- De [Snelstartgids voor web-apps](#register-an-app-by-using-the-quickstarts). Naast een fantastische eerste ervaring met het maken van een toepassing, kunnen Quick starts in de Azure Portal een knop bevatten met de naam **deze wijziging voor mij maken**. U kunt deze knop gebruiken om de eigenschappen in te stellen die u nodig hebt, zelfs voor een bestaande app. Pas de waarden van deze eigenschappen aan uw eigen case aan. Met name de Web API-URL voor uw app is waarschijnlijk anders dan de voorgestelde standaard waarde, die ook van invloed is op de afmeldings-URI.
- De Azure Portal [uw toepassing hand matig te registreren](#register-an-app-by-using-the-azure-portal).
- Power shell en opdracht regel Programma's.

## <a name="register-an-app-by-using-the-quickstarts"></a>Een app registreren met behulp van de Quick starts

U kunt deze koppelingen gebruiken om het maken van uw webtoepassing te Boots trappen:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>Een app registreren met behulp van de Azure Portal

> [!NOTE]
> De te gebruiken Portal verschilt, afhankelijk van of uw toepassing wordt uitgevoerd in de Microsoft Azure open bare Cloud of in een nationale of soevereine Cloud. Zie [National Clouds](./authentication-national-cloud.md#app-registration-endpoints)(Engelstalig) voor meer informatie.


1. Meld u aan bij de <a href="https://portal.azure.com/" target="_blank">Azure-portal</a>. 
1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.
1. Zoek en selecteer de optie **Azure Active Directory**.
1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
   1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen.
   1. Kies de ondersteunde account typen voor uw toepassing. (Zie [ondersteunde account typen](./v2-supported-account-types.md).)
   1. Voor **omleidings-URI** voegt u het type toepassing toe en de URI-bestemming die geretourneerde token Reacties accepteert na een geslaagde verificatie. Voer bijvoorbeeld `https://localhost:44321` in.
   1. Selecteer **Registreren**.
1. Selecteer onder **beheren** de optie **verificatie** en voeg de volgende gegevens toe:
   1. Voeg in het gedeelte **Web** `https://localhost:44321/signin-oidc` een **omleidings-URI** toe.
   1. Voer in **voor de afmeldings-URL voor het kanaal** `https://localhost:44321/signout-oidc` .
   1. Onder **impliciete toekenning en hybride stromen** selecteert u **id-tokens**.
   1. Selecteer **Opslaan**.
   
# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
   1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen.
   1. Kies de ondersteunde account typen voor uw toepassing. (Zie [ondersteunde account typen](./v2-supported-account-types.md).)
   1. Selecteer in de sectie de **omleidings-URI (optioneel)** **Web** in de keuze lijst met invoervak en voer een **omleidings-URI** van in `https://localhost:44326/` .
   1. Selecteer **Registreren** om de toepassing te maken.
1. Selecteer **Verificatie** onder **Beheren**.
1. Selecteer in de sectie **impliciete toekenning en hybride stromen** **id-tokens**. Voor dit voor beeld moet de [impliciete toekennings stroom](v2-oauth2-implicit-grant-flow.md) zijn ingeschakeld om u aan te melden bij de gebruiker.
1. Selecteer **Opslaan**.

# <a name="java"></a>[Java](#tab/java)

1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in: 
    1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen. 
    1. Selecteer **accounts in een organisatorische map en persoonlijke micro soft-accounts (bijvoorbeeld Skype, Xbox, Outlook.com)**.
    1. Selecteer **registreren** om de toepassing te registreren.
1. Selecteer onder **Beheren** achtereenvolgens **Verificatie** > **Een platform toevoegen**.
1. Selecteer **Web**.
1. Voer voor **omleidings-URI** hetzelfde host-en poort nummer in, gevolgd door `/msal4jsample/secure/aad` voor de aanmeldings pagina. 
1. Selecteer **Configureren**.
1. Gebruik in de sectie **Web** het host-en poort nummer, gevolgd door `/msal4jsample/graph/me` een **omleidings-URI** voor de pagina met gebruikers gegevens.
Het voor beeld maakt standaard gebruik van:
   - `http://localhost:8080/msal4jsample/secure/aad`
   - `http://localhost:8080/msal4jsample/graph/me`

1. Selecteer **Opslaan**.
1. Selecteer onder **Beheren** de optie **Certificaten en geheimen**.
1. Selecteer in de sectie **client geheimen** de optie **Nieuw client geheim** en voer vervolgens de volgende handelingen uit:

   1. Voer een beschrijving van de sleutel in.
   1. Selecteer de sleutel duur **in 1 jaar**.
   1. Selecteer **Toevoegen**.
   1. Wanneer de sleutel waarde wordt weer gegeven, kopieert u deze voor later. Deze waarde wordt niet opnieuw weer gegeven of kan op een andere manier worden opgehaald.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
   1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen.
   1. Wijzig **ondersteunde account typen** **in accounts in een organisatorische map en persoonlijke micro soft-accounts (bijvoorbeeld Skype, Xbox, Outlook.com)**.
   1. Selecteer in de sectie de **omleidings-URI (optioneel)** **Web** in de keuze lijst met invoervak en voer de volgende omleidings-URI in: `http://localhost:3000/redirect` .
   1. Selecteer **Registreren** om de toepassing te maken.
1. Zoek de waarde **Toepassings-ID (client)** op de app-pagina **Overzicht** voor later. U hebt deze nodig voor het configureren van het configuratie bestand voor dit project.
1. Selecteer onder **Beheren** de optie **Certificaten en geheimen**.
1. Selecteer in de sectie **client geheimen** de optie **Nieuw client geheim** en voer vervolgens de volgende handelingen uit:
   1. Voer een beschrijving van de sleutel in.
   1. Selecteer een sleutelduur van **Over 1 jaar**.
   1. Selecteer **Toevoegen**.
   1. Wanneer de sleutel waarde wordt weer gegeven, kopieert u deze. U hebt deze later nodig.

# <a name="python"></a>[Python](#tab/python)

1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
   1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen.
   1. Wijzig **ondersteunde account typen** **in accounts in een organisatorische map en persoonlijke micro soft-accounts (bijvoorbeeld Skype, Xbox, Outlook.com)**.
   1. Selecteer in de sectie de **omleidings-URI (optioneel)** **Web** in de keuze lijst met invoervak en voer de volgende omleidings-URI in: `http://localhost:5000/getAToken` .
   1. Selecteer **Registreren** om de toepassing te maken.
1. Zoek de waarde **Toepassings-ID (client)** op de app-pagina **Overzicht** voor later. U hebt deze nodig om het Visual Studio-configuratiebestand van dit project te configureren.
1. Selecteer onder **Beheren** de optie **Certificaten en geheimen**.
1. Selecteer in de sectie **client geheimen** de optie **Nieuw client geheim** en voer vervolgens de volgende handelingen uit:
   1. Voer een beschrijving van de sleutel in.
   1. Selecteer een sleutelduur van **Over 1 jaar**.
   1. Selecteer **Toevoegen**.
   1. Wanneer de sleutel waarde wordt weer gegeven, kopieert u deze. U hebt deze later nodig.
---

## <a name="register-an-app-by-using-powershell"></a>Een app registreren met behulp van Power shell

> [!NOTE]
> Op dit moment maakt Azure AD Power shell toepassingen met alleen de volgende ondersteunde account typen:
>
> - MyOrg (alleen accounts in deze organisatie Directory)
> - AnyOrg (accounts in elke organisatie Directory)
>
> U kunt een toepassing maken die zich aanmeldt bij gebruikers met hun persoonlijke micro soft-accounts (bijvoorbeeld Skype, Xbox of Outlook.com). Maak eerst een multi tenant-toepassing. Ondersteunde account typen zijn accounts in elke organisatie Directory. Wijzig vervolgens de [`accessTokenAcceptedVersion`](./reference-app-manifest.md#accesstokenacceptedversion-attribute) eigenschap in **2** en de [`signInAudience`](./reference-app-manifest.md#signinaudience-attribute) eigenschap `AzureADandPersonalMicrosoftAccount` in het manifest van de [toepassing](./reference-app-manifest.md) van de Azure Portal. Zie [stap 1,3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) in de ASP.net core zelf studie voor meer informatie. U kunt deze stap generaliseren voor web-apps in elke gewenste taal.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel in dit scenario, [de code configuratie van de app](scenario-web-app-sign-user-app-configuration.md).
