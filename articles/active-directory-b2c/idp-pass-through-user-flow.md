---
title: Geef een toegangs token voor de identiteits provider door aan uw app
titleSuffix: Azure AD B2C
description: Meer informatie over het door geven van een toegangs token voor OAuth 2,0-id-providers als een claim in een gebruikers stroom in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a99d41f5f9fc9538aaf563bd3ae56075d269c94a
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584643"
---
# <a name="pass-an-identity-provider-access-token-to-your-application-in-azure-active-directory-b2c"></a>Een toegangs token voor de identiteits provider door geven aan uw toepassing in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Een [gebruikers stroom](user-flow-overview.md) in Azure Active Directory B2C (Azure AD B2C) biedt gebruikers van uw toepassing de mogelijkheid zich aan te melden of zich aan te melden met een id-provider. Wanneer de rit wordt gestart, ontvangt Azure AD B2C een [toegangs token](tokens-overview.md) van de ID-provider. Azure AD B2C gebruikt dat token om informatie over de gebruiker op te halen. U schakelt een claim in uw gebruikers stroom in om het token door te geven aan de toepassingen die u registreert in Azure AD B2C.

::: zone pivot="b2c-user-flow"

Azure AD B2C ondersteunt het door geven van het toegangs token van [OAuth 2,0](add-identity-provider.md) -id-providers, zoals [Facebook](identity-provider-facebook.md) en [Google](identity-provider-google.md). Voor alle andere id-providers wordt de claim leeg geretourneerd.

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure AD B2C ondersteunt het door geven van het toegangs token van [OAuth 2,0](authorization-code-flow.md) -en [OpenID Connect Connect](openid-connect.md) -id-providers. Voor alle andere id-providers wordt de claim leeg geretourneerd.

::: zone-end

In het volgende diagram ziet u hoe een id-provider token wordt geretourneerd aan uw app: 

![Doorvoer stroom van ID-provider](./media/idp-pass-through-user-flow/identity-provider-pass-through-flow.png)

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="enable-the-claim"></a>Claim inschakelen

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies de map die uw Tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer **gebruikers stromen (beleid)** en selecteer vervolgens uw gebruikers stroom. Bijvoorbeeld **B2C_1_signupsignin1**.
5. Selecteer **Toepassingsclaims**.
6. Schakel de claim van het **toegangs token van de identiteits provider** in.

    ![De claim van het toegangs token van de identiteits provider inschakelen](./media/idp-pass-through-user-flow/identity-provider-pass-through-app-claim.png)

7. Klik op **Opslaan** om de gebruikers stroom op te slaan.

## <a name="test-the-user-flow"></a>De gebruikersstroom testen

Bij het testen van uw toepassingen in Azure AD B2C kan het nuttig zijn om het Azure AD B2C-token te retour neren om `https://jwt.ms` de claims erin te controleren.

1. Selecteer op de pagina overzicht van de gebruikers stroom de optie **gebruikers stroom uitvoeren**.
2. Selecteer voor **toepassing** de toepassing die u eerder hebt geregistreerd. Voor een overzicht van het token in het onderstaande voor beeld moet de **antwoord-URL** worden weer gegeven `https://jwt.ms` .
3. Klik op **gebruikers stroom uitvoeren** en meld u aan met uw account referenties. U ziet het toegangs token van de ID-provider in de **idp_access_token** claim.

    Het volgende voor beeld ziet er ongeveer uit:

    ![Gedecodeer token in jwt.ms met idp_access_token blok gemarkeerd](./media/idp-pass-through-user-flow/identity-provider-pass-through-token.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-the-claim-elements"></a>De claim elementen toevoegen

1. Open uw *TrustframeworkExtensions.xml* -bestand en voeg het volgende **claim** type-element toe met een id van `identityProviderAccessToken` aan het element **ClaimsSchema** :

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Voeg het **output claim** -element toe aan het **TechnicalProfile** -element voor elke OAuth 2,0-ID-provider waarvoor u het toegangs token voor wilt gebruiken. In het volgende voor beeld ziet u het element dat is toegevoegd aan het technische profiel voor Facebook:

    ```xml
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. Sla het *TrustframeworkExtensions.xml* bestand op.
4. Open uw Relying Party-beleids bestand, zoals *SignUpOrSignIn.xml*, en voeg het element **output claim** toe aan de **TechnicalProfile**:

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. Sla het beleids bestand op.

## <a name="test-your-policy"></a>Uw beleid testen

Bij het testen van uw toepassingen in Azure AD B2C kan het nuttig zijn om het Azure AD B2C-token te retour neren om `https://jwt.ms` de claims daarin te kunnen bekijken.

### <a name="upload-the-files"></a>De bestanden uploaden

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C Tenant bevat door te klikken op het filter **Directory + abonnement** in het bovenste menu en de map te kiezen die uw Tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer een **Framework voor identiteits ervaring**.
5. Klik op het tabblad Aangepaste beleids regels op **beleid uploaden**.
6. Selecteer **het beleid overschrijven als dit bestaat**, en zoek en selecteer het *TrustframeworkExtensions.xml* bestand.
7. Selecteer **Uploaden**.
8. Herhaal stap 5 tot en met 7 voor het Relying Party bestand, zoals *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>Het beleid uitvoeren

1. Open het beleid dat u hebt gewijzigd. Bijvoorbeeld *B2C_1A_signup_signin*.
2. Selecteer voor **toepassing** de toepassing die u eerder hebt geregistreerd. Voor een overzicht van het token in het onderstaande voor beeld moet de **antwoord-URL** worden weer gegeven `https://jwt.ms` .
3. Selecteer **nu uitvoeren**.

    Het volgende voor beeld ziet er ongeveer uit:

    ![Gedecodeer token in jwt.ms met idp_access_token blok gemarkeerd](./media/idp-pass-through-user-flow/identity-provider-pass-through-token-custom.png)

::: zone-end

## <a name="next-steps"></a>Volgende stappen

Meer informatie vindt u in het [overzicht van Azure AD B2C-tokens](tokens-overview.md).
