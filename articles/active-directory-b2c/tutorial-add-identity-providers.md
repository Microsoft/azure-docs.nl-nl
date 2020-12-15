---
title: 'Zelfstudie: Id-providers toevoegen aan uw apps'
titleSuffix: Azure AD B2C
description: Volg deze zelfstudie voor meer informatie over het toevoegen van id-providers aan uw toepassingen in Azure Active Directory B2C via Azure Portal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 166bdb7a2cf15a84e1b826a9a798042c568bb227
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608228"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Zelfstudie: Id-providers toevoegen aan uw toepassingen in Azure Active Directory B2C

Het is wellicht wenselijk dat uw gebruikers zich in uw toepassingen bij verschillende id-providers kunnen aanmelden. Een *id-provider* maakt, onderhoudt en beheert id-gegevens en biedt tegelijkertijd verificatieservices voor toepassingen. U kunt met behulp van Azure Portal id-providers aan uw [gebruikers stromen](user-flow-overview.md) toevoegen die worden ondersteund door Azure Active Directory B2C (Azure AD B2C).

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Toepassingen voor de id-provider maken
> * Id-providers toevoegen aan uw tenant; zowel op Facebook als in Azure Active Directory
> * De id-providers toevoegen aan uw gebruikersstroom

Gewoonlijk gebruikt u slechts één id-provider in uw toepassingen, maar u kunt er eventueel meer toevoegen. Deze zelfstudie laat zien hoe u een Azure AD-id-provider en een Facebook-id-provider aan uw toepassing kunt toevoegen. Eventueel kunt u beide id-providers aan de toepassing toevoegen. U kunt ook andere id-providers toevoegen, zoals [Amazon](identity-provider-amazon.md), [GitHub](identity-provider-github.md), [Google](identity-provider-google.md), [LinkedIn](identity-provider-linkedin.md), [Microsoft](identity-provider-microsoft-account.md) of [Twitter](identity-provider-twitter.md).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

[Maak een gebruikersstroom](tutorial-create-user-flows.md) zodat gebruikers zich bij uw toepassing kunnen registreren en aanmelden.

## <a name="create-applications"></a>Toepassingen maken

Id-providertoepassingen bieden de id en de sleutel om communicatie met uw Azure AD B2C-tenant mogelijk te maken. In deze sectie van de zelfstudie maakt u een Azure AD-toepassing en een Facebook-toepassing waaruit u id's en sleutels ophaalt om de id-providers aan uw tenant toe te voegen. Als u slechts een van de id-providers toevoegt, hoeft u alleen de toepassing voor die provider te maken.

### <a name="create-an-azure-active-directory-application"></a>Een Azure Active Directory-toepassing maken

Als u het aanmelden voor gebruikers vanuit Azure AD wilt inschakelen, moet u een toepassing registreren in de Azure AD-tenant. De Azure AD-tenant is niet dezelfde als uw Azure AD B2C-tenant.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD-tenant bevat door in het bovenste menu op het filter **Map en abonnement** te klikken en de map te kiezen waarin de Azure AD-tenant zich bevindt.
1. Kies linksboven in de Azure Portal **Alle services**, zoek **App-registraties** en selecteer deze.
1. Selecteer **Nieuwe registratie**.
1. Voer een naam in voor de toepassing. Bijvoorbeeld `Azure AD B2C App`.
1. Accepteer de selectie van **Alleen accounts in deze organisatiemap** voor deze toepassing.
1. Accepteer voor de **omleidings-URI** de waarde voor **Web** en voer de volgende URL in kleine letters in, waarbij u `your-B2C-tenant-name` vervangt door de naam van uw Azure AD B2C-tenant.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Bijvoorbeeld `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Alle URL's moeten nu gebruikmaken van [b2clogin.com](b2clogin.md).

1. Selecteer **Registreren** en noteer vervolgens de **Toepassings(client)-id** die u in een latere stap gebruikt.
1. Selecteer onder **Beheren** in het toepassingsmenu de optie **Certificaten en geheimen** en selecteer vervolgens **Nieuw clientgeheim**.
1. Voer een **beschrijving** voor het clientgeheim in. Bijvoorbeeld `Azure AD B2C App Secret`.
1. Selecteer de vervalperiode. Accepteer voor deze toepassing de selectie van **Na één jaar**.
1. Selecteer **Toevoegen** en noteer de waarde van het nieuwe clientgeheim. U gebruikt deze in een latere stap.

### <a name="create-a-facebook-application"></a>Een Facebook-toepassing maken

Als u een Facebook-account als een id-provider in Azure AD B2C wilt gebruiken, moet u een toepassing maken op Facebook. Als u nog geen Facebook-account hebt, kunt u deze maken op [https://www.facebook.com/](https://www.facebook.com/).

1. Meld u aan bij [Facebook for developers](https://developers.facebook.com/) (Facebook voor ontwikkelaars) met de referenties van uw Facebook-account.
1. Als u dit nog niet hebt gedaan, moet u zich registreren als Facebook-ontwikkelaar. Als u dit wilt doen, selecteert u **Get Started** in de rechter bovenhoek van de pagina, accepteert u het beleid van Facebook en voert u de registratiestappen uit.
1. Selecteer **My Apps** en vervolgens **Create App**.
1. Voer een **weergavenaam** en een geldig **e-mailadres van de contactpersoon** in.
1. Klik op **Create App ID**. Hiervoor moet u mogelijk het beleid voor het Facebook-platform accepteren en een onlinebeveiligingscontrole uitvoeren.
1. Selecteer **Settings** > **Basic**.
1. Kies een **categorie**, bijvoorbeeld `Business and Pages`. Deze waarde is vereist voor Facebook, maar wordt niet gebruikt door Azure AD B2C.
1. Selecteer onder aan de pagina **Add platform** en selecteer vervolgens **Website**.
1. Voer in **Site URL** `https://your-tenant-name.b2clogin.com/` in en vervang `your-tenant-name` door de naam van uw tenant.
1. Voer een URL in voor **Privacy Policy URL**, bijvoorbeeld `http://www.contoso.com/`. De URL van het privacybeleid is een pagina die u kunt onderhouden voor het verstrekken van privacyinformatie voor uw toepassing.
1. Selecteer **Save changes**.
1. Aan de bovenkant van de pagina noteert u de waarde van **App ID**.
1. Selecteer naast **App Secret** **Show** en noteer de waarde ervan. U gebruikt zowel de App-id als het app-geheim voor het configureren van Facebook als een id-provider in uw tenant. **App-geheim** is een belangrijke beveiligingsreferentie die u veilig moet opslaan.
1. Selecteer het plusteken naast **PRODUCTS** en selecteer vervolgens onder **Facebook Login** **Set up**.
1. Selecteer onder **Facebook Login** in het menu aan de linkerkant de optie **Settings**.
1. Voer in **Valid OAuth redirect URIs** `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` in. Vervang `your-tenant-name` door de naam van uw tenant. Selecteer **Save changes** onder aan de pagina.
1. Als u uw Facebook-toepassing beschikbaar wilt maken voor Azure AD B2C, klikt u rechtsboven op de pagina op de selector **Status**. Stel deze in op **On** om de toepassing openbaar te maken en klik op **Confirm**. Op dit moment moet de status worden gewijzigd van **Development** in **Live**.

## <a name="add-the-identity-providers"></a>Id-providers toevoegen

Nadat u de toepassing hebt gemaakt voor de id-provider die u wilt toevoegen, voegt u de id-provider aan uw tenant toe.

### <a name="add-the-azure-active-directory-identity-provider"></a>Azure Active Directory-id-provider toevoegen

1. Zorg ervoor dat u de map gebruikt die de Azure AD B2C-tenant bevat. Selecteer het filter **Map + Abonnement** in het bovenste menu en kies de map die uw Azure AD B2C-tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **Id-providers** en selecteer vervolgens **Nieuwe OpenID Connect-provider**.
1. Voer een **naam** in. Voer bijvoorbeeld *Contoso Azure AD* in.
1. Voor **URL voor metagegevens** voert u de volgende URL in, waarbij `{tenant}` wordt vervangen door de domeinnaam van uw Azure AD-tenant:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Bijvoorbeeld `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
    Bijvoorbeeld `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`.

1. Voor **Client-id** voert u de toepassings-id in die u eerder hebt genoteerd.
1. Voor **Clientgeheim** voert u het clientgeheim in dat u eerder hebt genoteerd.
1. Voer de `openid profile` in voor **Bereik**.
1. Behoud de standaardwaarden voor **Antwoordtype** en **Antwoordmodus**.
1. Voer `contoso.com` in voor **Domeinhint** (optioneel). Raadpleeg [Direct aanmelden met behulp van Azure Active Directory B2C instellen](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. Selecteer onder **Claimstoewijzing voor id-provider** de volgende claims:

    * **Gebruikers-id**: *oid*
    * **Weergavenaam**: *name*
    * **Voornaam**: *given_name*
    * **Achternaam**: *family_name*
    * **E-mail**: *unique_name*

1. Selecteer **Opslaan**.

### <a name="add-the-facebook-identity-provider"></a>De Facebook-id-provider toevoegen

1. Selecteer **Id-providers** en vervolgens **Facebook**.
1. Voer een **naam** in. Bijvoorbeeld *Facebook*.
1. Voer voor **Client-id** de app-id in van de Facebook-toepassing die u eerder hebt gemaakt.
1. Voer voor **Clientgeheim** het app-geheim in dat u hebt genoteerd.
1. Selecteer **Opslaan**.

## <a name="update-the-user-flow"></a>Gebruikersstroom bijwerken

In de zelfstudie die u als onderdeel van de vereisten hebt voltooid, hebt u een gebruikersstroom voor registreren en aanmelden gemaakt met de naam *B2C_1_signupsignin1*. In deze sectie voegt u de id-providers toe aan de gebruikersstroom *B2C_1_signupsignin1*.

1. Selecteer **Gebruikersstromen** en vervolgens de gebruikersstroom *B2C_1_signupsignin1*.
2. Selecteer **Id-providers**, selecteer de id-providers **Facebook** en **Contoso Azure AD** die u hebt toegevoegd.
3. Selecteer **Opslaan**.

## <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Selecteer op de pagina Overzicht van de gebruikersstroom die u hebt gemaakt de optie **Gebruikersstroom uitvoeren**.
1. Selecteer voor **Toepassing** de webtoepassing met de naam *webapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Selecteer **Gebruikersstroom uitvoeren** en meld u vervolgens aan met een id-provider die u eerder hebt toegevoegd.
1. Herhaal stap 1 tot en met 3 voor de andere id-providers die u hebt toegevoegd.

Als de aanmelding is geslaagd, wordt u omgeleid naar `https://jwt.ms`, waarin het gedecodeerde token wordt weergegeven, soortgelijk aan:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "<key-ID>"
}.{
  "exp": 1562346892,
  "nbf": 1562343292,
  "ver": "1.0",
  "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
  "sub": "20000000-0000-0000-0000-000000000000",
  "aud": "30000000-0000-0000-0000-000000000000",
  "nonce": "defaultNonce",
  "iat": 1562343292,
  "auth_time": 1562343292,
  "name": "User Name",
  "idp": "facebook.com",
  "postalCode": "12345",
  "tfp": "B2C_1_signupsignin1"
}.[Signature]
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u het volgende geleerd:

> [!div class="checklist"]
> * Toepassingen voor de id-provider maken
> * De id-providers toevoegen aan uw tenant
> * De id-providers toevoegen aan uw gebruikersstroom

Leer vervolgens hoe u de gebruikersinterface aanpast van de pagina's die aan gebruikers als onderdeel van hun identiteitservaring in uw toepassingen worden weergegeven:

> [!div class="nextstepaction"]
> [De gebruikersinterface van uw toepassingen in Azure Active Directory B2C aanpassen](tutorial-customize-ui.md)
