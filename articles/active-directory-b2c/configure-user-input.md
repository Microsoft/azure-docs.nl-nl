---
title: Gebruikers kenmerken toevoegen en gebruikers invoer aanpassen
titleSuffix: Azure AD B2C
description: Meer informatie over het aanpassen van de gebruikers invoer en het toevoegen van gebruikers kenmerken aan de traject voor registreren of aanmelden in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 53f5346030e3c5a247d1ad2a73b23882e83ed41a
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97508115"
---
#  <a name="add-user-attributes-and-customize-user-input-in-azure-active-directory-b2c"></a>Gebruikers kenmerken toevoegen en gebruikers invoer aanpassen in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

In dit artikel verzamelt u een nieuw kenmerk tijdens het registreren van de Azure Active Directory B2C (Azure AD B2C). U krijgt de plaats van de gebruiker, configureert deze als een vervolg keuzelijst en definieert of het moet worden opgegeven.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="add-user-attributes-your-user-flow"></a>Gebruikers kenmerken van uw gebruikers stroom toevoegen

1. Selecteer in uw Azure AD B2C-Tenant **gebruikers stromen**.
1. Selecteer uw beleid (bijvoorbeeld ' B2C_1_SignupSignin ') om het te openen.
1. Selecteer **gebruikers kenmerken** en selecteer vervolgens het gebruikers kenmerk (bijvoorbeeld ' City '). 
1. Selecteer **Opslaan**.

## <a name="provide-optional-claims-to-your-app"></a>Optionele claims voor uw app opgeven

De toepassings claims zijn waarden die worden geretourneerd naar de toepassing. Werk de stroom van de gebruiker bij zodat deze de gewenste claims bevat.

1. Selecteer uw beleid (bijvoorbeeld ' B2C_1_SignupSignin ') om het te openen.
1. Selecteer **Toepassingsclaims**.
1. Selecteer de kenmerken die u wilt terugsturen naar uw toepassing (bijvoorbeeld ' City ').
1. Selecteer **Opslaan**.
 
## <a name="configure-user-attributes-input-type"></a>Invoer type voor gebruikers kenmerken configureren

1. Selecteer uw beleid (bijvoorbeeld ' B2C_1_SignupSignin ') om het te openen.
1. Selecteer **pagina-indelingen**.
1. Selecteer de **pagina voor het registreren van een lokaal account**.
1. Onder **gebruikers kenmerken** selecteert u **plaats**.
    1. Selecteer in de vervolg keuzelijst **invoer type gebruiker** de optie **DropdownSingleSelect**.
    1. Selecteer in de vervolg keuzelijst **optioneel** de optie **Nee**.
1. Selecteer **Opslaan**. 

### <a name="provide-a-list-of-values-by-using-localized-collections"></a>Een lijst met waarden opgeven met behulp van gelokaliseerde verzamelingen

Een set-lijst met waarden opgeven voor het kenmerk City: 

1. [Taal aanpassing inschakelen op de gebruikers stroom](language-customization.md#support-requested-languages-for-ui_locales)
1. Selecteer uw beleid (bijvoorbeeld ' B2C_1_SignupSignin ') om het te openen.
1. Selecteer op de pagina **talen** voor de gebruikers stroom de taal die u wilt aanpassen.
1. Selecteer de **registratie pagina voor het lokale account** onder **bronnen bestanden op pagina niveau**.
1. Selecteer **standaard waarden downloaden** (of **down loads negeren** als u deze taal eerder hebt bewerkt).
1. Maak een `LocalizedCollections` kenmerk.

De `LocalizedCollections` is een matrix van `Name` en- `Value` paren. De volg orde van de items is de volg orde waarin ze worden weer gegeven. 

* `ElementId` is het gebruikers kenmerk waarvan dit `LocalizedCollections` kenmerk een antwoord is.
* `Name` is de waarde die wordt weer gegeven voor de gebruiker.
* `Value` is wat wordt geretourneerd in de claim wanneer deze optie is geselecteerd.

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType": "ClaimType",
      "ElementId": "city",
      "TargetCollection": "Restriction",
      "Override": false,
      "Items": [
        {
          "Name": "Berlin",
          "Value": "Berlin"
        },
        {
          "Name": "London",
          "Value": "London"
        },
        {
          "Name": "Seattle",
          "Value": "Seattle"
        }
      ]
    }
  ]
}
```

#### <a name="upload-your-changes"></a>Uw wijzigingen uploaden

1. Nadat u de wijzigingen in het JSON-bestand hebt voltooid, gaat u terug naar de B2C-Tenant.
1. Selecteer **gebruikers stromen** en selecteer uw beleid (bijvoorbeeld ' B2C_1_SignupSignin ') om het te openen.
1. Selecteer **talen**.
1. Selecteer de taal waarin u wilt vertalen.
1. Selecteer de **registratie pagina van het lokale account**.
1. Selecteer het mappictogram en selecteer het JSON-bestand dat u wilt uploaden. De wijzigingen worden automatisch opgeslagen in de stroom van uw gebruikers.

## <a name="test-your-user-flow"></a>Uw gebruikers stroom testen

1. Selecteer uw beleid (bijvoorbeeld ' B2C_1_SignupSignin ') om het te openen.
1. Als u het beleid wilt testen, selecteert u **gebruikers stroom uitvoeren**.
1. Selecteer voor **toepassing** de webtoepassing met de naam *testapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Klik op **gebruikers stroom uitvoeren**

::: zone-end

::: zone pivot="b2c-custom-policy"

> [!NOTE]
> In dit voor beeld wordt het ingebouwde claim ' City ' gebruikt. In plaats daarvan kunt u een van de ondersteunde [Azure AD B2C ingebouwde kenmerken](user-profile-attributes.md) of een aangepast kenmerk kiezen. Als u een aangepast kenmerk wilt gebruiken, [schakelt u aangepaste kenmerken in](user-flow-custom-attributes.md). Als u een ander ingebouwd of aangepast kenmerk wilt gebruiken, vervangt u ' City ' door het gewenste kenmerk, bijvoorbeeld het ingebouwde kenmerk *jobTitle* of een aangepast kenmerk zoals *extension_loyaltyId*.  

U kunt initiële gegevens van uw gebruikers verzamelen door gebruik te maken van de traject registratie of aanmeldings gebruiker. Aanvullende claims kunnen later worden verzameld met behulp van een gebruikers traject voor het bewerken van profielen. Elke keer dat Azure AD B2C informatie rechtstreeks van de gebruiker verzamelt, gebruikt het Framework voor identiteits ervaring het [zelfondertekende technische profiel](self-asserted-technical-profile.md). In dit voor beeld hebt u het volgende:

1. Definieer een ' City ' claim. 
1. Vraag de gebruiker om een plaats.
1. Behoud de plaats voor het gebruikers profiel in de map Azure AD B2C.
1. Lees de plaats claim uit de Azure AD B2C Directory op elke aanmelding.
1. Ga terug naar de plaats van uw Relying Party-toepassing nadat u zich hebt aangemeld of zich hebt aangemeld.  

## <a name="define-a-claim"></a>Een claim definiëren

Een claim biedt een tijdelijke opslag van gegevens tijdens het uitvoeren van een Azure AD B2C beleid. Het [claim schema](claimsschema.md) is de plaats waar u uw claims declareert. De volgende elementen worden gebruikt voor het definiëren van de claim:

- **DisplayName** : een teken reeks die het op de gebruiker gerichte label definieert.
- [Data type](claimsschema.md#datatype) : het type claim.
- **UserHelpText** : helpt de gebruiker te begrijpen wat er nodig is.
- [UserInputType](claimsschema.md#userinputtype) : het type besturings element voor invoer, zoals tekstvak, keuze rondje, vervolg keuzelijst of meerdere selecties.

Open het bestand extensies van uw beleid. Bijvoorbeeld <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Zoek het element [BuildingBlocks](buildingblocks.md) . Als het element niet bestaat, voegt u het toe.
1. Zoek het element [ClaimsSchema](claimsschema.md) . Als het element niet bestaat, voegt u het toe.
1. Voeg de stads claim toe aan het **ClaimsSchema** -element.  

```xml
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

## <a name="add-a-claim-to-the-user-interface"></a>Een claim toevoegen aan de gebruikers interface

De volgende technische profielen worden [automatisch bevestigd](self-asserted-technical-profile.md), die worden aangeroepen wanneer een gebruiker naar verwachting invoer levert:

- **LocalAccountSignUpWithLogonEmail** : de aanmeldings stroom voor het lokale account.
- **SelfAsserted-** account voor de eerste keer dat gebruikers zich aanmelden met sociale Federatie.
- **SelfAsserted-ProfileUpdate** -profiel stroom bewerken.

Als u de stads claim tijdens het aanmelden wilt verzamelen, moet deze worden toegevoegd als een uitvoer claim aan het `LocalAccountSignUpWithLogonEmail` technische profiel. Negeer dit technische profiel in het extensie bestand. Geef de volledige lijst met uitvoer claims op om de volg orde te bepalen waarin de claims worden weer gegeven op het scherm. Zoek het element **ClaimsProviders** . Voeg als volgt een nieuwe ClaimsProviders toe:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <!--Local account sign-up page-->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
       <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="displayName" />
       <OutputClaim ClaimTypeReferenceId="givenName" />
       <OutputClaim ClaimTypeReferenceId="surName" />
       <OutputClaim ClaimTypeReferenceId="city"/>
     </OutputClaims>
   </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Als u de stads claim wilt verzamelen na de eerste aanmelding met een federatief account, moet u deze als een uitvoer claim aan het `SelfAsserted-Social` technische profiel toevoegen. Als gebruikers van lokale en federatieve accounts hun profiel gegevens later kunnen bewerken, voegt u de uitvoer claim toe aan het `SelfAsserted-ProfileUpdate` technische profiel. Vervang deze technische profielen in het extensie bestand. Geef de volledige lijst van de uitvoer claims op om de volg orde te bepalen waarin de claims op het scherm worden weer gegeven. Zoek het element **ClaimsProviders** . Voeg als volgt een nieuwe ClaimsProviders toe:

```xml
<ClaimsProvider>
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="read-and-write-a-claim"></a>Een claim lezen en schrijven

De volgende technische profielen zijn [Active Directory technische profielen](active-directory-technical-profile.md), waarmee gegevens worden gelezen en geschreven naar Azure Active Directory.  
Gebruiken `PersistedClaims` om gegevens naar het gebruikers profiel te schrijven en `OutputClaims` gegevens uit het gebruikers profiel te lezen in de respectieve Active Directory technische profielen.

Vervang deze technische profielen in het extensie bestand. Zoek het element **ClaimsProviders** .  Voeg als volgt een nieuwe ClaimsProviders toe:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <!-- Write data during a local account sign-up flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during a federated account first-time sign-in flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during edit profile flow. -->
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="include-a-claim-in-the-token"></a>Een claim in het token toevoegen 

Voeg een uitvoer claim toe aan het bestand om de plaats claim terug te retour neren aan de Relying Party-toepassing <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> . De uitvoer claim wordt toegevoegd aan het token na een geslaagde gebruikers reis en wordt naar de toepassing verzonden. Wijzig het technische profiel element in het gedeelte Relying Party om de plaats toe te voegen als een uitvoer claim.
 
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
      <OutputClaim ClaimTypeReferenceId="city" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Het aangepaste beleid testen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD-tenant bevat door in het bovenste menu op het filter **Map en abonnement** te klikken en de map te kiezen waarin de Azure AD-tenant zich bevindt.
3. Kies linksboven in de Azure Portal **Alle services**, zoek **App-registraties** en selecteer deze.
4. Selecteer een **Framework voor identiteits ervaring**.
5. Selecteer **aangepast beleid uploaden** en upload vervolgens de twee beleids bestanden die u hebt gewijzigd.
2. Selecteer het registratie-of aanmeldings beleid dat u hebt geüpload en klik op de knop **nu uitvoeren** .
3. U moet zich kunnen aanmelden met een e-mail adres.

::: zone-end

Het aanmeldings scherm moet er ongeveer uitzien als in de volgende scherm afbeelding:

![Scherm afbeelding van gewijzigde registratie optie](./media/configure-user-input/signup-with-city-claim-drop-down-example.png)

Het token dat teruggestuurd naar uw toepassing bevat de `city` claim.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1583500140,
  "nbf": 1583496540,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1583496540,
  "auth_time": 1583496540,
  "name": "Emily Smith",
  "email": "joe@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "city": "Bellevue"
  ...
}
```

::: zone pivot="b2c-custom-policy"

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het element [ClaimsSchema](claimsschema.md) vindt u in de IEF-verwijzing.
- Meer informatie over het [gebruik van aangepaste kenmerken in azure AD B2C](user-flow-custom-attributes.md).

::: zone-end