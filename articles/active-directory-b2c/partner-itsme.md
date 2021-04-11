---
title: Itsme OpenID connect verbinding maken met Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Meer informatie over het integreren van Azure AD B2C verificatie met Itsme OIDC met behulp van client_secret beleid voor gebruikers stromen. Itsme is een digitale ID-app. Zo kunt u veilig inloggen zonder kaart lezers, wacht woorden, twee ledige verificatie en meerdere PINCODEs.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2560c294b63d0554708eda1f804bf279d16d7d74
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257956"
---
# <a name="configure-itsme-openid-connect-oidc-with-azure-active-directory-b2c"></a>Itsme OpenID Connect Connect (OIDC) configureren met Azure Active Directory B2C

Met de Itsme digitale ID-app kunt u veilig inloggen zonder kaart lezers, wacht woorden, verificatie met twee factoren of meerdere PINCODEs. De Itsme-app biedt een sterke klant verificatie met een geverifieerde identiteit. In dit artikel vindt u informatie over het integreren van Azure AD B2C verificatie met Itsme OpenID Connect Connect (OIDC) met behulp van een gebruikers stroom beleid voor client geheim.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* [Een Azure AD B2C-Tenant](tutorial-create-tenant.md) die is gekoppeld aan uw Azure-abonnement.
* Uw client-ID, ook wel bekend als partner code, die wordt verschaft door Itsme.
* De service code die wordt verschaft door Itsme.
* Uw client geheim voor uw Itsme-account.

## <a name="scenario-description"></a>Scenariobeschrijving

![diagram van Itsme-architectuur](media/partner-itsme/itsme-architecture-diagram.png)

<!--
Please clarify step 1 in the description below - we don't have steps in this tutorial for "adapting in the Azure AD B2C Custom Policy- User Journeys" - should this be added somewhere?
-->

| Stap | Beschrijving |
|------|------|
|1     | Voeg op uw website of toepassing de knop **Aanmelden met Itsme** toe door het aanpassen van de Azure AD B2C gebruikers stroom. De interactie stroom wordt gestart wanneer de gebruiker op deze knop klikt.  |
|2     | Azure AD B2C start de OpenID Connect Connect-stroom door een autorisatie aanvraag te verzenden naar de Itsme client Secret-API. Een goed bekend/OpenID Connect-configuratie-eind punt is beschikbaar met informatie over de eind punten.  |
|3     | De Itsme-omgeving leidt de gebruiker naar de pagina Itsme identificeren uzelf, zodat de gebruiker het telefoon nummer kan invullen.  |
|4     | De Itsme-omgeving ontvangt het telefoon nummer van de gebruiker en valideert de juistheid.  |
|5     | Als het telefoon nummer deel uitmaakt van een actieve Itsme-gebruiker, wordt een actie gemaakt voor de Itsme-app.  |
|6     | De gebruiker opent de app Itsme, controleert de aanvraag en bevestigt de actie.  |
|7     |  De app informeert de Itsme-omgeving. de actie is bevestigd. |
|8     |  De Itsme-omgeving retourneert de OAuth-autorisatie code aan Azure AD B2C. |
|9     |  Met de machtigings code wordt de Azure AD B2C een token aanvraag. |
| 10 | De Itsme-omgeving controleert de token aanvraag en retourneert als dat nog geldig is het OAuth-toegangs token en het ID-token met de aangevraagde gebruikers gegevens. |
| 11 | Ten slotte wordt de gebruiker omgeleid naar de omleidings-URL als geverifieerde gebruiker.  |
|   |   |

## <a name="onboard-with-itsme"></a>Onboard met Itsme

1. Als u een account met Itsme wilt maken, gaat u naar Itsme op de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace).

2. Activeer uw Itsme-account door een e-mail te verzenden naar onboarding@itsme.be . U ontvangt een **partner code** en **service code** die nodig zijn voor uw B2C-installatie.

3. Nadat u uw Itsme-partner account hebt geactiveerd, ontvangt u een e-mail met een eenmalige koppeling naar het **client geheim**.

4. Volg de instructies op [Itsme](https://business.itsme.be/en) om de configuratie te volt ooien.

## <a name="integrate-with-azure-ad-b2c"></a>Integreren met Azure AD B2C

### <a name="set-up-a-new-identity-provider-in-azure-ad-b2c"></a>Een nieuwe ID-provider instellen in Azure AD B2C

> [!NOTE]
> Als u nog geen account hebt, [maakt u een Azure AD B2C-Tenant](tutorial-create-tenant.md) die is gekoppeld aan uw Azure-abonnement.

1. Zorg ervoor dat u de map gebruikt die de Azure AD B2C-tenant bevat. Selecteer het filter **Map + Abonnement** in het bovenste menu en kies de map die uw Azure AD B2C-tenant bevat.

2. Selecteer **Azure AD B2C** onder **Azure-Services**(of selecteer **meer services** en gebruik het zoekvak **alle services** om te zoeken naar *Azure AD B2C*).

3. Selecteer **Id-providers** en selecteer vervolgens **Nieuwe OpenID Connect-provider**.

4. Vul het formulier in met de volgende informatie:

   |Eigenschap | Waarde |
   |------------ |------- |
   | Naam | itsme |
   | Metagegevens-URL | `https://oidc.<environment>.itsme.services/clientsecret-oidc/csapi/v0.1/.well-known/openid-configuration` <br>waar `<environment>` is `e2e` (test omgeving) of `prd` (productie)  |
   | ClientID     | Uw **client-id**, ook wel bekend als **partner code**  |
   | Clientgeheim | Uw **client_secret** |
   | Bereik  | OpenID Connect-service: e-mail adres van YOURSERVICECODE-profiel [telefoon] [adres]  |
   |Antwoord type | code |
   |Antwoord modus | query |
   |Domein Hint | *U kunt dit leeg laten* |
   |UserID | sub |
   |Weergavenaam | naam |
   |Voornaam | given_name |
   |Achternaam | family_name |
   |E-mail | e-mail|

5. Selecteer **Opslaan**.

### <a name="configure-a-user-flow"></a>Een gebruikers stroom configureren

1. Selecteer in uw Azure AD B2C-Tenant onder **beleids regels** **gebruikers stromen**.

2. Selecteer **nieuwe gebruikers stroom**.

3. Selecteer **Aanmelden en aanmelden**, selecteer een versie en selecteer vervolgens **maken**.

4. Voer een **naam** in.

5. Selecteer in de sectie **id-providers** de optie **Itsme**.

6. Selecteer **Maken**.

7. Open de zojuist gemaakte gebruikers stroom door de naam van de gebruikers stroom te selecteren.

8. Selecteer **Eigenschappen** en pas de volgende waarden aan:

   * Wijzig de **toegangs & ID-levens duur van token (minuten)** in **5**.
   * **Sliding window levens duur van het vernieuwings token** wijzigen in **geen verval datum**.

### <a name="register-an-application"></a>Een toepassing registreren

1. Selecteer in uw B2C-Tenant onder **beheren** de optie **app-registraties**  >  **nieuwe registratie**.

2. Geef een **naam** op voor de toepassing en voer de **omleidings-URI** in. Voer in voor test doeleinden `https://jwt.ms` .

3. Zorg ervoor dat multi-factor Authentication is **uitgeschakeld**.

4. Selecteer **Registreren**.

   a. Voor test doeleinden selecteert u **verificatie** en selecteert u onder **impliciete toekenning** de selectie vakjes **toegangs tokens** en **id-tokens** .  

   b. Selecteer **Opslaan**.

## <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. In uw B2C-Tenant selecteert u onder **beleids regels** **gebruikers stromen**.

2. Selecteer de eerder gemaakte gebruikers stroom.

3. Selecteer **Gebruikersstroom uitvoeren**.

   a. **Toepassing**: *Selecteer de geregistreerde app*

   b. **Antwoord-URL**: *de omleidings-URL selecteren*

4. De pagina Itsme **Identify uzelf** wordt weer gegeven.  

5. Voer uw mobiele telefoon nummer in en selecteer **verzenden**.

6. Bevestig de actie in de Itsme-app.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie:

* [Aangepast beleid in Azure AD B2C](custom-policy-overview.md)

* [Aan de slag met aangepast beleid in Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)