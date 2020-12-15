---
title: SSO en het aanpassen van tokens beheren met aangepaste beleids regels
titleSuffix: Azure AD B2C
description: Meer informatie over het beheren van SSO en het aanpassen van tokens met aangepaste beleids regels in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6bfabd13c43501f7539eb3756ffbd06802cee823
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97503468"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>SSO en het aanpassen van tokens beheren met aangepaste beleids regels in Azure Active Directory B2C

In dit artikel vindt u informatie over het beheren van uw token-, sessie-en SSO-configuraties (single sign-on) met behulp van [aangepast beleid](custom-policy-overview.md) in Azure Active Directory B2C (Azure AD B2C).

## <a name="jwt-token-lifetimes-and-claims-configuration"></a>De levens duur van de JWT-tokens en claims configureren

Als u de instellingen voor de levens duur van uw tokens wilt wijzigen, voegt u een [ClaimsProviders](claimsproviders.md) -element toe in het Relying Party bestand van het beleid dat u wilt beïnvloeden.  Het element **ClaimsProviders** is een onderliggend item van het element [TrustFrameworkPolicy](trustframeworkpolicy.md) .

Voeg het ClaimsProviders-element toe tussen het element BasePolicy en het element RelyingParty van het bestand Relying Party.

In moet u de informatie over de levens duur van uw tokens plaatsen. De XML ziet eruit als in dit voor beeld:

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          <Item Key="token_lifetime_secs">3600</Item>
          <Item Key="id_token_lifetime_secs">3600</Item>
          <Item Key="refresh_token_lifetime_secs">1209600</Item>
          <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

De volgende waarden zijn ingesteld in het vorige voor beeld:

- **Toegangs token levensduur** : de waarde voor de levens duur van het toegangs token is ingesteld met **token_lifetime_secs** meta gegevens item. De standaard waarde is 3600 seconden (60 minuten).
- **Levens duur van id-token** : de waarde voor de levens duur van het id-token is ingesteld met het **id_token_lifetime_secs** meta gegevens item. De standaard waarde is 3600 seconden (60 minuten).
- **Levens duur van het token vernieuwen** : de waarde voor de levens duur van het vernieuwings token is ingesteld met het **refresh_token_lifetime_secs** meta gegevens item. De standaard waarde is 1209600 seconden (14 dagen).
- De **levens duur van het token sliding window vernieuwen** : als u een sliding window levensduur wilt instellen voor het vernieuwings token, stelt u de waarde van **rolling_refresh_token_lifetime_secs** meta gegevens item in. De standaard waarde is 7776000 (90 dagen). Als u geen sliding window levensduur wilt afdwingen, vervangt u het item door `<Item Key="allow_infinite_rolling_refresh_token">True</Item>` .
- **Claim verlener (ISS)** : de claim verlener (ISS) is ingesteld met het meta gegevens item **IssuanceClaimPattern** . De toepasselijke waarden zijn `AuthorityAndTenantGuid` en `AuthorityWithTfp` .
- **Claim instellen die beleids-id vertegenwoordigt** : de opties voor het instellen van deze waarde zijn `TFP` (vertrouwens raamwerk beleid) en `ACR` (Naslag informatie over de verificatie context). `TFP` is de aanbevolen waarde. Stel **AuthenticationContextReferenceClaimPattern** in met de waarde `None` .

    Voeg dit element toe aan het element **ClaimsSchema** :

    ```xml
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    Voeg dit element toe aan het **OutputClaims** -element:

    ```xml
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Voor ACR verwijdert u het item **AuthenticationContextReferenceClaimPattern** .

- **Onderwerp (sub) claim** : deze optie wordt standaard ingesteld op ObjectID. Als u deze instelling wilt wijzigen in `Not Supported` , vervangt u deze regel:

    ```xml
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    met deze regel:

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

> [!NOTE]
> Toepassingen met één pagina die gebruikmaken van de autorisatie code stroom met PKCE, hebben altijd een vernieuwings token van 24 uur. Meer [informatie over de beveiligings implicaties van vernieuwings tokens in de browser](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).

## <a name="provide-optional-claims-to-your-app"></a>Optionele claims voor uw app opgeven

Het [technische profiel](relyingparty.md#technicalprofile) uitvoer claims van het Relying Party beleid zijn waarden die worden geretourneerd naar een toepassing. Bij het toevoegen van uitvoer claims worden de claims in het token uitgegeven na een geslaagde gebruikers reis en verzonden naar de toepassing. Wijzig het technische profiel element in het gedeelte Relying Party om de gewenste claims toe te voegen als een uitvoer claim.

1. Open uw aangepaste beleids bestand. Bijvoorbeeld SignUpOrSignin.xml.
1. Zoek het element OutputClaims. Voeg de output claim toe die u wilt opnemen in het token. 
1. De uitvoer claim kenmerken instellen. 

In het volgende voor beeld wordt de `accountBalance` claim toegevoegd. De claim accountBalance wordt als een saldo verzonden naar de toepassing. 

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <!--Add the optional claims here-->
      <OutputClaim ClaimTypeReferenceId="accountBalance" DefaultValue="" PartnerClaimType="balance" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

Het output claim-element bevat de volgende kenmerken:

  - **ClaimTypeReferenceId** : de id van een claim type dat al is gedefinieerd in de sectie [ClaimsSchema](claimsschema.md) in het beleids bestand of het bovenliggende beleids bestand.
  - **PartnerClaimType** -Hiermee kunt u de naam van de claim in het token wijzigen. 
  - **DefaultValue** : een standaard waarde. U kunt de standaard waarde ook instellen op een [claim resolver](claim-resolver-overview.md), zoals een Tenant-id.
  - **AlwaysUseDefaultValue** : het gebruik van de standaard waarde afdwingen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure AD B2C sessie](session-overview.md).
- Meer informatie over het [configureren van het gedrag van sessies in aangepaste beleids regels](session-behavior-custom-policy.md).
- Verwijzing: [JwtIssuer](jwt-issuer-technical-profile.md).
