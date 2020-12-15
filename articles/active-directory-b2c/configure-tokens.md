---
title: Tokens configureren-Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over het configureren van de token levensduur en compatibiliteits instellingen in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 9b5782df01cad260852fb6ee5c00e3d7669acf47
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97503523"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Tokens configureren in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

In dit artikel leert u hoe u de [levens duur en compatibiliteit van een token](tokens-overview.md) in Azure Active Directory B2C (Azure AD B2C) kunt configureren.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="token-lifetime-behavior"></a>Levens duur van token-gedrag

U kunt de levens duur van het token configureren, met inbegrip van:

- **Levens duur van toegangs-en id-token (minuten)** : de levens duur van de OAuth 2,0 Bearer token-en id-tokens. De standaard waarde is 60 minuten (1 uur). Het minimale (inclusief) is 5 minuten. Het maximum (inclusief) is 1.440 minuten (24 uur).
- **Levens duur van het vernieuwings token (dagen)** : de maximale tijds duur waarna een vernieuwings token kan worden gebruikt om een nieuw toegangs token te verkrijgen, als uw toepassing het bereik heeft toegekend `offline_access` . De standaard waarde is 14 dagen. Het minimale (inclusief) is één dag. Het maximum (inclusief) 90 dagen.
- **Vernieuwings token sliding window levens duur** : het vernieuwings token sliding window type. `Bounded` geeft aan dat het vernieuwings token kan worden uitgebreid als de **levens duur wordt opgegeven (dagen)**. `No expiry` geeft aan dat het vernieuwings token sliding window levens duur nooit verloopt.
- **Levens duur (dagen)** : nadat deze periode is verstreken, wordt de gebruiker gedwongen opnieuw te verifiëren, ongeacht de geldigheids periode van het meest recente vernieuwings token dat is verkregen door de toepassing. De waarde moet groter zijn dan of gelijk zijn aan de waarde voor de **levens duur van het vernieuwings token** .

In het volgende diagram ziet u het vernieuwings token sliding window levensduur gedrag.

![Levens duur van token vernieuwen](./media/configure-tokens/refresh-token-lifetime.png)

> [!NOTE]
> Toepassingen met één pagina die gebruikmaken van de autorisatie code stroom met PKCE, hebben altijd een vernieuwings token van 24 uur. Meer [informatie over de beveiligings implicaties van vernieuwings tokens in de browser](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).

## <a name="configure-token-lifetime"></a>Levens duur van token configureren

::: zone pivot="b2c-user-flow"

De levens duur van uw gebruikers stroom token configureren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het filter **Map + Abonnement** in het bovenste menu en kies de map die uw Azure AD B2C-tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **gebruikers stromen (beleid)**.
1. Open de gebruikers stroom die u eerder hebt gemaakt.
1. Selecteer **Eigenschappen**.
1. Pas de eigenschappen onder **levens duur** van het token aan de vereisten van uw toepassing aan.
1. Klik op **Opslaan**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Als u de instellingen voor de compatibiliteit van tokens wilt wijzigen, stelt u de [token Uitgever](jwt-issuer-technical-profile.md) van het technische profiel in de extensie in of het Relying Party bestand van het beleid dat u wilt beïnvloeden. Het technische profiel voor de token uitgever ziet er als volgt uit:

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
          <!--<Item Key="allow_infinite_rolling_refresh_token">true</Item>-->
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

De volgende waarden zijn ingesteld in het vorige voor beeld:

- levens duur van het token voor **token_lifetime_secs** toegang (seconden). De standaard waarde is 3.600 (1 uur). De minimum waarde is 300 (5 minuten). De maximum waarde is 86.400 (24 uur). 
- de levens duur van het token voor **id_token_lifetime_secs** -id's (seconden). De standaard waarde is 3.600 (1 uur). De minimum waarde is 300 (5 minuten). De maximum waarde is 86.400 (24 uur). 
- **refresh_token_lifetime_secs** De levens duur van het token vernieuwen (seconden). De standaard waarde is 120, 9600 (14 dagen). De minimum waarde is 86.400 (24 uur). De maximum waarde is 7.776.000 (90 dagen). 
- sliding window levens duur van **rolling_refresh_token_lifetime_secs** -vernieuwings token (seconden). De standaard waarde is 7.776.000 (90 dagen). De minimum waarde is 86.400 (24 uur). De maximum waarde is 31.536.000 (365 dagen). Als u geen sliding window levensduur wilt afdwingen, stelt u de waarde `allow_infinite_rolling_refresh_token` in op `true` . 
- **allow_infinite_rolling_refresh_token** -vernieuwings token sliding window levens duur nooit verloopt. 

::: zone-end


## <a name="token-compatibility-settings"></a>Compatibiliteits instellingen voor tokens

U kunt de token compatibiliteit configureren, met inbegrip van:

- **Claim verlener (ISS)** : de indeling van de toegangs-en id-token Uitgever.
- **Subject (sub-claim)** : de principal over welke het token informatie beschouwt, zoals de gebruiker van een toepassing. Deze waarde is onveranderbaar en kan niet opnieuw worden toegewezen of opnieuw worden gebruikt. Het kan worden gebruikt om autorisatie controles veilig uit te voeren, zoals wanneer het token wordt gebruikt voor toegang tot een bron. Standaard wordt de subject claim gevuld met de object-ID van de gebruiker in de Directory.
- **Claim die de gebruikers stroom vertegenwoordigt** : deze claim identificeert de uitgevoerde gebruikers stroom. Mogelijke waarden: `tfp` (standaard) of `acr` .

::: zone pivot="b2c-user-flow"

Compatibiliteits instellingen voor gebruikers stromen configureren:

1. Selecteer **gebruikers stromen (beleid)**.
1. Open de gebruikers stroom die u eerder hebt gemaakt.
1. Selecteer **Eigenschappen**.
1. Wijzig onder **instellingen voor token compatibiliteit** de eigenschappen zodat deze voldoen aan de vereisten van uw toepassing.
1. Klik op **Opslaan**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Als u de instellingen voor de compatibiliteit van tokens wilt wijzigen, stelt u de [token Uitgever](jwt-issuer-technical-profile.md) van het technische profiel in de extensie in of het Relying Party bestand van het beleid dat u wilt beïnvloeden. Het technische profiel voor de token uitgever ziet er als volgt uit:

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          ...
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

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

::: zone-end

## <a name="provide-optional-claims-to-your-app"></a>Optionele claims voor uw app opgeven

De toepassings claims zijn waarden die worden geretourneerd naar de toepassing. Werk de stroom van de gebruiker bij zodat deze de gewenste claims bevat.

::: zone pivot="b2c-user-flow"

1. Selecteer **gebruikers stromen (beleid)**.
1. Open de gebruikers stroom die u eerder hebt gemaakt.
1. Selecteer **Toepassingsclaims**.
1. Kies de claims en kenmerken die u wilt terugsturen naar uw toepassing.
1. Klik op **Opslaan**.

::: zone-end

::: zone pivot="b2c-custom-policy"

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

::: zone-end

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [aanvragen van toegangs tokens](access-tokens.md).