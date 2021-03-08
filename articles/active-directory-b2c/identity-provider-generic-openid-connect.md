---
title: Registratie instellen en aanmelden met OpenID Connect Connect
titleSuffix: Azure AD B2C
description: Stel registratie in en meld u aan met een OpenID Connect Connect-ID-provider (IdP) in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9434bd4042798dc05a33401e1884e11a73774936
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448333"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Registratie instellen en aanmelden met OpenID Connect Connect met behulp van Azure Active Directory B2C

[OpenID Connect Connect](openid-connect.md) is een verificatie protocol dat is gebaseerd op OAuth 2,0 en dat kan worden gebruikt voor het aanmelden van beveiligde gebruikers. De meeste id-providers die gebruikmaken van dit protocol worden ondersteund in Azure AD B2C. In dit artikel wordt uitgelegd hoe u aangepaste OpenID Connect Connect-id-providers kunt toevoegen aan uw gebruikers stromen.

## <a name="add-the-identity-provider"></a>De ID-provider toevoegen

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C Tenant bevat door te klikken op het filter **Directory + abonnement** in het bovenste menu en de map te kiezen die uw Tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **Id-providers** en selecteer vervolgens **Nieuwe OpenID Connect-provider**.
1. Voer een **naam** in. Typ bijvoorbeeld *Contoso*.

## <a name="configure-the-identity-provider"></a>De ID-provider configureren

Elke OpenID Connect Connect-ID-provider beschrijft een meta gegevens document met de meeste informatie die nodig is om de aanmelding uit te voeren. Dit omvat informatie zoals de Url's die moeten worden gebruikt en de locatie van de open bare handtekeningen sleutels van de service. Het OpenID Connect Connect meta data-document bevindt zich altijd op een eind punt dat eindigt op `.well-known/openid-configuration` . Voer de meta gegevens-URL in voor de OpenID Connect Connect-ID-provider die u wilt toevoegen.

## <a name="client-id-and-secret"></a>Client-ID en geheim

Om gebruikers toe te staan zich aan te melden, vereist de ID-provider ontwikkel aars om een toepassing in hun service te registreren. Deze toepassing heeft een ID die de **client-id** en een **client geheim** wordt genoemd. Kopieer deze waarden van de ID-provider en geef ze op in de bijbehorende velden.

> [!NOTE]
> Het client geheim is optioneel. U moet echter een client geheim invoeren als u de [autorisatie code stroom](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)wilt gebruiken, die het geheim gebruikt voor het uitwisselen van de code voor het token.

## <a name="scope"></a>Bereik

Met het bereik worden de informatie en machtigingen gedefinieerd die u wilt verzamelen uit uw aangepaste ID-provider. OpenID Connect Connect-aanvragen moeten de `openid` bereik waarde bevatten om het ID-token van de ID-provider te ontvangen. Zonder het ID-token kunnen gebruikers zich niet aanmelden bij Azure AD B2C met behulp van de aangepaste ID-provider. Andere bereiken kunnen worden toegevoegd, gescheiden door een spatie. Raadpleeg de documentatie van de aangepaste ID-provider om te zien welke andere bereiken mogelijk beschikbaar zijn.

## <a name="response-type"></a>Antwoord type

Het antwoord type beschrijft wat voor soort informatie wordt weer gegeven in de eerste aanroep van de `authorization_endpoint` aangepaste ID-provider. De volgende antwoord typen kunnen worden gebruikt:

* `code`: Volgens de [autorisatie code stroom](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)wordt een code geretourneerd naar Azure AD B2C. Azure AD B2C opbrengsten om de `token_endpoint` code aan te roepen voor het token.
* `id_token`: Er is een ID-token geretourneerd naar Azure AD B2C van de aangepaste ID-provider.

## <a name="response-mode"></a>Antwoord modus

De antwoord modus definieert de methode die moet worden gebruikt om de gegevens terug van de aangepaste ID-provider naar Azure AD B2C te verzenden. De volgende antwoord modi kunnen worden gebruikt:

* `form_post`: Deze antwoord modus wordt aanbevolen voor de beste beveiliging. Het antwoord wordt verzonden via de HTTP- `POST` methode, waarbij de code of het token wordt gecodeerd in de hoofd tekst met de- `application/x-www-form-urlencoded` indeling.
* `query`: De code of het token wordt geretourneerd als een query parameter.

## <a name="domain-hint"></a>Domein Hint

De domein hint kan worden gebruikt om rechtstreeks over te slaan op de aanmeldings pagina van de opgegeven id-provider, in plaats van dat de gebruiker een selectie maakt tussen de lijst met beschik bare id-providers. Als u dit gedrag wilt toestaan, voert u een waarde in voor de domein hint. Als u naar de aangepaste ID-provider wilt gaan, voegt u de para meter `domain_hint=<domain hint value>` toe aan het einde van uw aanvraag wanneer u Azure AD B2C aanroept om u aan te melden.

## <a name="claims-mapping"></a>Claim toewijzing

Wanneer de aangepaste ID-provider een ID-token terugstuurt naar Azure AD B2C, moet Azure AD B2C de claims van het ontvangen token kunnen toewijzen aan de claims die Azure AD B2C herkent en gebruikt. Raadpleeg de documentatie van de Custom ID-provider voor elk van de volgende toewijzingen om inzicht te krijgen in de claims die terug worden geretourneerd in de tokens van de identiteits provider:

* **Gebruikers-id**: Geef de claim op die de *unieke id* voor de aangemelde gebruiker levert.
* **Weergave naam**: Geef de claim op die de *weergave naam* of de *volledige naam* voor de gebruiker levert.
* Voor **naam**: Voer de claim in die de *voor naam* van de gebruiker levert.
* **Naam**: Voer de claim in die de *Achternaam* van de gebruiker levert.
* **E-mail**: Voer de claim in die het *e-mail adres* van de gebruiker levert.

## <a name="add-the-identity-provider-to-a-user-flow"></a>De ID-provider toevoegen aan een gebruikers stroom 

1. Selecteer in uw Azure AD B2C-Tenant **gebruikers stromen**.
1. Klik op de gebruikers stroom die u wilt toevoegen aan de ID-provider. 
1. Selecteer de ID-provider die u hebt toegevoegd onder de **id-providers voor sociale netwerken**. Bijvoorbeeld *Contoso*.
1. Selecteer **Opslaan**.
1. Als u het beleid wilt testen, selecteert u **gebruikers stroom uitvoeren**.
1. Selecteer voor **toepassing** de webtoepassing met de naam *testapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Selecteer de knop **gebruikers stroom uitvoeren** .
1. Selecteer op de pagina aanmelden of aanmelden de ID-provider waarvoor u zich wilt aanmelden. Bijvoorbeeld *Contoso*.

Als het aanmeldings proces is geslaagd, wordt uw browser omgeleid naar `https://jwt.ms` , waarin de inhoud wordt weer gegeven van het token dat is geretourneerd door Azure AD B2C.
