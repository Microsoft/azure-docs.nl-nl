---
title: Vereisten voor wachtwoord complexiteit configureren
titleSuffix: Azure AD B2C
description: Complexiteits vereisten configureren voor wacht woorden die door consumenten worden verstrekt in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 11a45adfda306b2ab843725b6aaa28a5e6c026a6
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97614248"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Complexiteits vereisten configureren voor wacht woorden in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) ondersteunt het wijzigen van de complexiteits vereisten voor wacht woorden die door een eind gebruiker worden verstrekt bij het maken van een account. Azure AD B2C maakt standaard gebruik van **sterke** wacht woorden. Azure AD B2C biedt ook ondersteuning voor configuratie opties voor het beheren van de complexiteit van wacht woorden die klanten kunnen gebruiken.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="password-rule-enforcement"></a>Wachtwoord regel afdwingen

Tijdens het registreren of opnieuw instellen van wacht woorden moet een eind gebruiker een wacht woord opgeven dat voldoet aan de complexiteits regels. Regels voor wachtwoord complexiteit worden afgedwongen per gebruikers stroom. Het is mogelijk dat er voor één gebruikers stroom een pincode van vier cijfers is vereist tijdens het aanmelden terwijl een andere gebruikers stroom een teken reeks van acht tekens vereist tijdens de registratie. U kunt bijvoorbeeld een gebruikers stroom met verschillende wachtwoord complexiteit voor volwassenen gebruiken dan voor kinderen.

Wachtwoord complexiteit wordt nooit afgedwongen tijdens het aanmelden. Gebruikers worden nooit gevraagd tijdens het aanmelden om hun wacht woord te wijzigen, omdat het niet voldoet aan de huidige complexiteits vereisten.

Wachtwoord complexiteit kan worden geconfigureerd in de volgende typen gebruikers stromen:

- Gebruikers stroom registreren of aanmelden
- Gebruikers stroom voor wacht woord opnieuw instellen

Als u aangepast beleid gebruikt, kunt u[de complexiteit van het wacht woord configureren in een aangepast beleid](password-complexity.md).

## <a name="configure-password-complexity"></a>Wachtwoord complexiteit configureren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
3. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
4. Selecteer **gebruikers stromen**.
2. Selecteer een gebruikers stroom en klik op **Eigenschappen**.
3. Wijzig onder **wachtwoord complexiteit** de complexiteit van het wacht woord voor deze gebruikers stroom naar **eenvoudig**, **sterk** of **aangepast**.

### <a name="comparison-chart"></a>Vergelijkings grafiek

| Complexiteit | Beschrijving |
| --- | --- |
| Eenvoudig | Een wacht woord van ten minste 8 tot 64 tekens. |
| Sterk | Een wacht woord van ten minste 8 tot 64 tekens. Hiervoor zijn 3 van de 4 van kleine letters, hoofd letters, cijfers of symbolen vereist. |
| Aangepast | Deze optie biedt de meeste controle over regels voor wachtwoord complexiteit.  Hiermee kan een aangepaste lengte worden geconfigureerd.  Ook kunt u alleen aantal wacht woorden (pincodes) accepteren. |

## <a name="custom-options"></a>Aangepaste opties

### <a name="character-set"></a>Tekenset

Hiermee kunt u alleen cijfers (pincodes) of de volledige tekenset accepteren.

- **Getallen mogen alleen** cijfers (0-9) bevatten tijdens het invoeren van een wacht woord.
- **Alle** letter, cijfer of symbool worden toegestaan.

### <a name="length"></a>Lengte

Hiermee kunt u de lengte vereisten van het wacht woord bepalen.

- De **minimum lengte** moet mini maal 4 zijn.
- De **maximum lengte** moet groter zijn dan of gelijk zijn aan de minimum lengte en mag maxi maal 64 tekens lang zijn.

### <a name="character-classes"></a>Teken klassen

Hiermee kunt u de verschillende teken typen beheren die in het wacht woord worden gebruikt.

- **2 van 4: kleine letters, hoofd letters, cijfer (0-9), symbool** zorgt ervoor dat het wacht woord ten minste twee teken typen bevat. Bijvoorbeeld een numeriek teken en een kleine letter.
- **3 van 4: kleine letters, hoofd letters, cijfer (0-9), symbool** zorgt ervoor dat het wacht woord ten minste drie teken typen bevat. Bijvoorbeeld een getal, een kleine letter en een hoofd letter.
- **4 van 4: kleine letters, hoofd letters, cijfer (0-9), symbool** zorgt ervoor dat het wacht woord alle tekens voor teken typen bevat.

    > [!NOTE]
    > Het vereisen **van 4 van 4** kan leiden tot frustraties van eind gebruikers. Sommige onderzoeken hebben laten zien dat deze vereiste de wachtwoord entropie niet verbetert. Zie [NIST-wachtwoord richtlijnen](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="password-predicate-validation"></a>Validatie van het predikaat wacht woord

Als u de wachtwoord complexiteit wilt configureren, overschrijft `newPassword` u de `reenterPassword` [claim typen](claimsschema.md) en maakt u een verwijzing naar [predikaten-validaties](predicates.md#predicatevalidations). Met het element PredicateValidations wordt een set predikaten gegroepeerd om een gebruikers invoer validatie te vormen die kan worden toegepast op een claim type. Open het bestand extensies van uw beleid. Bijvoorbeeld <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Zoek het element [BuildingBlocks](buildingblocks.md) . Als het element niet bestaat, voegt u het toe.
1. Zoek het element [ClaimsSchema](claimsschema.md) . Als het element niet bestaat, voegt u het toe.
1. Voeg de `newPassword` claims en toe `reenterPassword` aan het **ClaimsSchema** -element.

    ```xml
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. [Predikaten](predicates.md) definieert een basis validatie om de waarde van een claim type te controleren en retourneert waar of onwaar. De validatie wordt uitgevoerd met behulp van een opgegeven methode-element en een set para meters die relevant zijn voor de methode. Voeg de volgende predikaten toe aan het **BuildingBlocks** -element, direct na het sluiten van het `</ClaimsSchema>` element:

    ```xml
    <Predicates>
      <Predicate Id="LengthRange" Method="IsLengthRange">
        <UserHelpText>The password must be between 6 and 64 characters.</UserHelpText>
        <Parameters>
          <Parameter Id="Minimum">6</Parameter>
          <Parameter Id="Maximum">64</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Lowercase" Method="IncludesCharacters">
        <UserHelpText>a lowercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">a-z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="IncludesCharacters">
        <UserHelpText>an uppercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">A-Z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="IncludesCharacters">
        <UserHelpText>a digit</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">0-9</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="IncludesCharacters">
        <UserHelpText>a symbol</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

1. Voeg de volgende predikaten-validaties toe aan het element **BuildingBlocks** direct na het sluiten van het `</Predicates>` element:

    ```xml
    <PredicateValidations>
      <PredicateValidation Id="CustomPassword">
        <PredicateGroups>
          <PredicateGroup Id="LengthGroup">
            <PredicateReferences MatchAtLeast="1">
              <PredicateReference Id="LengthRange" />
            </PredicateReferences>
          </PredicateGroup>
          <PredicateGroup Id="CharacterClasses">
            <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
            <PredicateReferences MatchAtLeast="3">
              <PredicateReference Id="Lowercase" />
              <PredicateReference Id="Uppercase" />
              <PredicateReference Id="Number" />
              <PredicateReference Id="Symbol" />
            </PredicateReferences>
          </PredicateGroup>
        </PredicateGroups>
      </PredicateValidation>
    </PredicateValidations>
    ```

## <a name="disable-strong-password"></a>Sterk wacht woord uitschakelen 

De volgende technische profielen zijn [Active Directory technische profielen](active-directory-technical-profile.md), waarmee gegevens worden gelezen en geschreven naar Azure Active Directory. Vervang deze technische profielen in het extensie bestand. Gebruiken `PersistedClaims` om het beleid voor sterke wacht woorden uit te scha kelen. Zoek het element **ClaimsProviders** .  Voeg de volgende claim providers als volgt toe:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
      </PersistedClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
      </PersistedClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Sla het beleids bestand op.

## <a name="test-your-policy"></a>Uw beleid testen

### <a name="upload-the-files"></a>De bestanden uploaden

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer een **Framework voor identiteits ervaring**.
5. Klik op het tabblad Aangepaste beleids regels op **beleid uploaden**.
6. Selecteer **het beleid overschrijven als dit bestaat**, en zoek en selecteer het *TrustFrameworkExtensions.xml* bestand.
7. Klik op **Uploaden**.

### <a name="run-the-policy"></a>Het beleid uitvoeren

1. Open het registratie-of aanmeldings beleid. Bijvoorbeeld *B2C_1A_signup_signin*.
2. Selecteer voor **toepassing** de toepassing die u eerder hebt geregistreerd. Om het token weer te geven, moet de **antwoord-URL** worden weer gegeven `https://jwt.ms` .
3. Klik op **Nu uitvoeren**.
4. Selecteer **nu aanmelden**, voer een e-mail adres in en voer een nieuw wacht woord in. Richt lijnen worden weer gegeven voor wachtwoord beperkingen. Voer de gebruikers gegevens in en klik vervolgens op **maken**. De inhoud van het geretourneerde token moet worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [configureren van wachtwoord wijzigingen in azure Active Directory B2C](add-password-change-policy.md).
- Meer informatie over de [predikaten](predicates.md) en [PredicateValidations](predicates.md#predicatevalidations) -elementen vindt u in de IEF-verwijzing.


::: zone-end