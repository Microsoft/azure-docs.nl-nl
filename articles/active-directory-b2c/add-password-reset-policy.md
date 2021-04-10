---
title: Een wachtwoord herstel stroom instellen in Azure AD B2C
titleSuffix: Azure AD B2C
description: Meer informatie over het instellen van een wachtwoord herstel stroom in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f451d08dfbde643d91705f54296e9757a51c9d88
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104798390"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Een wachtwoord herstel stroom instellen in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="password-reset-flow"></a>Wachtwoord herstel stroom

Met de [traject registreren en aanmelden](add-sign-up-and-sign-in-policy.md) kunnen gebruikers hun eigen wacht woord opnieuw instellen met de koppeling **verg eten uw wacht woord?** De stroom voor het opnieuw instellen van wacht woorden omvat de volgende stappen:

1. Op de pagina aanmelden en aanmelden klikt de gebruiker op de koppeling **wacht woord verg eten?** Azure AD B2C initieert de stroom voor het opnieuw instellen van het wacht woord. 
2. De gebruiker biedt en verifieert het e-mail adres met een tijd code die eenmaal is gewacht.
3. De gebruiker kan vervolgens een nieuw wacht woord invoeren.

![Wachtwoord herstel stroom](./media/add-password-reset-policy/password-reset-flow.png)

De stroom voor het opnieuw instellen van wacht woorden is van toepassing op lokale accounts in Azure AD B2C die gebruikmaken van een [e-mail adres](identity-provider-local.md#email-sign-in) of [gebruikers naam](identity-provider-local.md#username-sign-in) met een wacht woord voor aanmelden.

> [!TIP]
> Met de self-service voor wachtwoord herstel kunnen gebruikers hun wacht woord wijzigen wanneer de gebruiker het wacht woord vergeet en wil het opnieuw instellen. U kunt een [wijzigings stroom voor wacht woorden](add-password-change-policy.md) configureren ter ondersteuning van gevallen waarin een gebruiker het wacht woord kent en wil wijzigen.

Een veelvoorkomende procedure na de migratie van gebruikers naar Azure AD B2C met wille keurige wacht woorden is dat de gebruikers hun e-mail adressen moeten controleren en hun wacht woord opnieuw kunnen instellen tijdens de eerste aanmelding. Het is ook gebruikelijk om de gebruiker te dwingen hun wacht woord opnieuw in te stellen nadat een beheerder hun wacht woord heeft gewijzigd. Zie [geforceerd wacht woord opnieuw instellen](force-password-reset.md) om deze functie in te scha kelen.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="self-service-password-reset-recommended"></a>Self-service voor wachtwoord herstel (aanbevolen)

De nieuwe ervaring voor het opnieuw instellen van wacht woorden maakt nu deel uit van het registratie-of aanmeldings beleid. Wanneer de gebruiker de koppeling **uw wacht woord verg eten** selecteert, worden deze direct verzonden naar de verg eten wacht woord-ervaring. Uw toepassing hoeft niet langer de AADB2C90118- [fout code](#password-reset-policy-legacy)af te handelen en u hebt geen afzonderlijk beleid nodig voor het opnieuw instellen van het wacht woord.

::: zone pivot="b2c-user-flow"

De self-service voor het opnieuw instellen van wacht woorden kan worden geconfigureerd voor het **aanmelden (aanbevolen)** of **registreren en aanmelden (aanbevolen)** gebruikers stromen. Als u geen gebruikers stroom hebt, kunt u een [aanmelding maken en](add-sign-up-and-sign-in-policy.md) gebruikers stroom registreren. 

Selfservice voor het opnieuw instellen van wacht woorden inschakelen voor de stroom voor registreren of aanmelden van de gebruiker:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
1. Selecteer **gebruikers stromen**.
1. Selecteer een gebruikers stroom voor registratie of aanmelding (van het type **Aanbevolen**) die u wilt aanpassen.
1. Onder **instellingen** in het menu links selecteert u **Eigenschappen**.
1. Selecteer bij **wachtwoord complexiteit** de optie **self-service voor wacht woord opnieuw instellen**.
1. Selecteer **Opslaan**.
1. Onder **aanpassen** in het menu links selecteert u **pagina-indelingen**.
1. Kies in de versie van de **pagina-indeling** de optie **2.1.2-actueel** of hoger.
1. Selecteer **Opslaan**.

::: zone-end

::: zone pivot="b2c-custom-policy"

In de volgende secties wordt beschreven hoe u een self-service wachtwoord ervaring kunt toevoegen aan een aangepast beleid. Het voor beeld is gebaseerd op de beleids bestanden die zijn opgenomen in het [aangepaste beleids Starter Pack](./custom-policy-get-started.md). 

> [!TIP]
> U vindt een volledig voor beeld van het beleid ' Aanmelden of aanmelden met wachtwoord herstel ' op [github](https://github.com/azure-ad-b2c/samples/tree/master/policies/embedded-password-reset).

### <a name="indicate-a-user-selected-the-forgot-your-password-link"></a>Aangeven dat een gebruiker het wacht woord verg eten heb geselecteerd? gekoppeld

Om aan te geven dat de gebruiker de koppeling **verg eten uw wacht woord** is geselecteerd, definieert u een Boole-claim. Deze claim wordt gebruikt om de gebruikers door te sturen naar het profiel voor het verg eten wacht woord. Deze claim kan ook worden verleend aan het token, zodat de toepassing weet dat de gebruiker zich heeft aangemeld via de wachtwoord stroom verg eten.

U declareert uw claims in het [claims-schema](claimsschema.md). Open het bestand extensies van uw beleid. Bijvoorbeeld <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Zoek het element [BuildingBlocks](buildingblocks.md) . Als het element niet bestaat, voegt u het toe.
1. Zoek het element [ClaimsSchema](claimsschema.md) . Als het element niet bestaat, voegt u het toe.
1. Voeg de volgende claim toe aan het **ClaimsSchema** -element. 

```XML
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="isForgotPassword">
      <DisplayName>isForgotPassword</DisplayName>
      <DataType>boolean</DataType>
      <AdminHelpText>Whether the user has selected Forgot your Password</AdminHelpText>
    </ClaimType>
  <!--
  </ClaimsSchema>
</BuildingBlocks> -->
```

### <a name="upgrade-the-page-layout-version"></a>De versie van de pagina-indeling bijwerken

Versie van pagina- [indeling](contentdefinitions.md#migrating-to-page-layout) `2.1.2` is vereist om de self-service voor wachtwoord herstel in te scha kelen binnen het registratie-of aanmeldings traject.

1. Zoek het element [BuildingBlocks](buildingblocks.md) . Als het element niet bestaat, voegt u het toe.
1. Zoek het element [ContentDefinitions](contentdefinitions.md) . Als het element niet bestaat, voegt u het toe.
1. Wijzig het element **DataURI** in het element **ContentDefinition** met de id **API. signuporsignin** zoals hieronder wordt weer gegeven.

```xml
<!-- 
<BuildingBlocks>
  <ContentDefinitions> -->
    <ContentDefinition Id="api.signuporsignin">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2</DataUri>
    </ContentDefinition>
  <!-- 
  </ContentDefinitions>
</BuildingBlocks> -->
```

Voor het initiëren van de `isForgotPassword` claim wordt een technische profiel voor het transformeren van claims gebruikt. Naar dit technische profiel wordt later verwezen. Als deze wordt aangeroepen, wordt de waarde van de `isForgotPassword` claim ingesteld op `true` . Het `ClaimsProviders` element zoeken. Als het element niet bestaat, voegt u het toe. Voeg vervolgens de volgende claim provider toe:  

```xml
<!-- 
<ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>Local Account</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="ForgotPassword">
        <DisplayName>Forgot your password?</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="true" AlwaysUseDefaultValue="true"/>
        </OutputClaims>
      </TechnicalProfile>
      <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
        <Metadata>
          <Item Key="setting.forgotPasswordLinkOverride">ForgotPasswordExchange</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!-- 
</ClaimsProviders> -->
```

Met het `SelfAsserted-LocalAccountSignin-Email` technische profiel `setting.forgotPasswordLinkOverride` definieert u de uitwisseling van claims voor het opnieuw instellen van wacht woorden in uw gebruikers traject. 

### <a name="add-the-password-reset-sub-journey"></a>De subtraject voor het opnieuw instellen van het wacht woord toevoegen

Uw traject omvat nu de mogelijkheid om zich aan te melden, zich aan te melden en het opnieuw instellen van het wacht woord uit te voeren. Om het traject van de gebruiker beter te organiseren, kan een [subtraject](subjourneys.md) worden gebruikt voor het afhandelen van de wachtwoord herstel stroom.

De subtraject wordt aangeroepen vanuit de gebruikers reis en voert de specifieke stappen uit om de gebruikers ervaring voor het opnieuw instellen van het wacht woord aan te bieden. Gebruik het `Call` type subtraject zodat het besturings element wordt teruggestuurd naar de indelings stap die de subtraject heeft gestart, zodra de subtraject is voltooid.

Het `SubJourneys` element zoeken. Als het element niet bestaat, voegt u het toe na het- `User Journeys` element. Voeg vervolgens de volgende subtraject toe:

```xml
<!--
<SubJourneys>-->
  <SubJourney Id="PasswordReset" Type="Call">
    <OrchestrationSteps>
      <!-- Validate user's email address. -->
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="PasswordResetUsingEmailAddressExchange" TechnicalProfileReferenceId="LocalAccountDiscoveryUsingEmailAddress" />
        </ClaimsExchanges>
      </OrchestrationStep>

      <!-- Collect and persist a new password. -->
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
<!--
</SubJourneys>-->
```

### <a name="prepare-your-user-journey"></a>De reis van uw gebruikers voorbereiden

U moet verbinding maken met het **wacht woord verg eten?** koppeling naar het verg eten wacht woord. Als u dit wilt doen, verwijst u naar het verg eten wacht woord subtraject id binnen het element **ClaimsProviderSelection** van de stap **CombinedSignInAndSignUp** .

Als u niet beschikt over uw eigen aangepaste gebruikers traject met een **CombinedSignInAndSignUp** stap, gebruikt u de volgende procedure om een bestaande gebruikers traject voor registreren of aanmelden te dupliceren. Als dat niet het geval is, gaat u verder met de volgende sectie.

1. Open het *TrustFrameworkBase.xml* -bestand in het Starter Pack.
2. Zoek en kopieer de volledige inhoud van het **UserJourney** -element dat bevat `Id="SignUpOrSignIn"` .
3. Open de *TrustFrameworkExtensions.xml* en zoek het element **UserJourneys** . Als het element niet bestaat, voegt u er een toe.
4. Maak een onderliggend element van het **UserJourneys** -element door de volledige inhoud van het **UserJourney** -element te plakken dat u in stap 2 hebt gekopieerd.
5. Wijzig de naam van de gebruikers traject. Bijvoorbeeld `Id="CustomSignUpSignIn"`.

### <a name="connect-the-forgot-password-link-to-the-forgot-password-sub-journey"></a>Verbinding maken met de verg eten wachtwoord koppeling naar het verg eten wacht woord subtraject 

In uw gebruikers traject kunt u de verg eten wacht woord-subtraject vertegenwoordigen als een **ClaimsProviderSelection**. Door dit element toe te voegen, maakt u verbinding met de **verg eten wacht woord?** koppeling naar het verg eten wacht woord.

1. Zoek in de gebruikers reis het element Orchestration-stap dat bevat `Type="CombinedSignInAndSignUp"` of `Type="ClaimsProviderSelection"` . Doorgaans is dit de eerste indelings stap. Het **ClaimsProviderSelections** -element bevat een lijst met id-providers die een gebruiker kan gebruiken om zich aan te melden. Voeg de volgende regel toe:
    
    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ForgotPasswordExchange" />
    ```

1. Voeg in de volgende Orchestration-stap een **ClaimsExchange** -element toe. Voeg de volgende regel toe:

    ```xml
    <ClaimsExchange Id="ForgotPasswordExchange" TechnicalProfileReferenceId="ForgotPassword" />
    ```
    
1. Voeg de volgende Orchestration-stap toe tussen de huidige stap en de volgende stap. De nieuwe Orchestration-stap die u toevoegt, controleert of de `isForgotPassword` claim bestaat. Als de claim bestaat, wordt de [subtraject voor het opnieuw instellen van het wacht woord](#add-the-password-reset-sub-journey)aangeroepen. 

    ```xml
    <OrchestrationStep Order="3" Type="InvokeSubJourney">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>isForgotPassword</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <JourneyList>
        <Candidate SubJourneyReferenceId="PasswordReset" />
      </JourneyList>
    </OrchestrationStep>
    ```
    
1. Nadat u de nieuwe indelings stap hebt toegevoegd, moet u de stappen sequentieel opnieuw nummeren zonder een geheel getal tussen 1 en N over te slaan.

### <a name="set-the-user-journey-to-be-executed"></a>Instellen dat de reis van de gebruiker wordt uitgevoerd

Nu u een gebruikers traject hebt gewijzigd of gemaakt, geeft u in de **relying** Party de rit op die Azure AD B2C worden uitgevoerd voor dit aangepaste beleid. Zoek het element **DefaultUserJourney** binnen het element [RelyingParty](relyingparty.md) . Werk de  **DefaultUserJourney-ReferenceId** bij zodat deze overeenkomt met de id van de gebruikers traject waarin u de **ClaimsProviderSelections** hebt toegevoegd.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="indicate-the-forgot-password-flow-to-your-app"></a>Geef de wacht woord stroom voor verg eten aan uw app

Uw toepassing moet mogelijk bepalen of de gebruiker zich heeft aangemeld via de gebruikers stroom voor het verg eten wacht woord. De **isForgotPassword** -claim bevat een Booleaanse waarde waarmee wordt aangegeven dat deze kan worden uitgegeven in het token dat naar uw toepassing wordt verzonden. Voeg, indien nodig, toe `isForgotPassword` aan de uitvoer claims in de sectie **relying** Party. Uw toepassing kan de `isForgotPassword` claim controleren om te bepalen of de gebruiker het wacht woord opnieuw instelt.

```xml
<RelyingParty>
  <OutputClaims>
    ...
    <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="false" />
  </OutputClaims>
</RelyingParty>
```


### <a name="upload-the-custom-policy"></a>Het aangepaste beleid uploaden

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
1. Onder **beleids regels** selecteert u **identiteits ervaring-Framework**.
1. Selecteer **aangepast beleid uploaden** en upload de twee beleids bestanden die u in de onderstaande volg orde hebt gewijzigd:
   1. Het extensie beleid, bijvoorbeeld `TrustFrameworkExtensions.xml` .
   2. Het Relying Party beleid, bijvoorbeeld `SignUpSignIn.xml` .

::: zone-end

### <a name="test-the-password-reset-flow"></a>De stroom voor wachtwoord herstel testen

1. Selecteer een gebruikers stroom voor registratie of aanmelding (van het type aanbevolen) dat u wilt testen.
1. Selecteer **Gebruikersstroom uitvoeren**.
1. Selecteer voor **Toepassing** de webtoepassing met de naam *webapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Selecteer **Gebruikersstroom uitvoeren**.
1. Selecteer **uw wacht woord verg eten** op de pagina aanmelden of aanmelden.
1. Controleer het e-mail adres van het account dat u eerder hebt gemaakt en selecteer vervolgens **door gaan**.
1. U hebt nu de mogelijkheid om het wachtwoord voor de gebruiker te wijzigen. Wijzig het wachtwoord en selecteer **Doorgaan**. Het token wordt geretourneerd aan `https://jwt.ms` en moet worden weergegeven.
1. Controleer de claim waarde van het retour token `isForgotPassword` . Als deze bestaat en is ingesteld op True, wordt hiermee aangegeven dat de gebruiker het wacht woord opnieuw heeft ingesteld.

## <a name="password-reset-policy-legacy"></a>Beleid voor het opnieuw instellen van wacht woorden (verouderd)

Als de [self-service voor het opnieuw instellen van wacht woorden](#self-service-password-reset-recommended) niet is ingeschakeld, wordt door het klikken op deze koppeling niet automatisch de gebruikers stroom voor het opnieuw instellen van wacht woorden geactiveerd. In plaats daarvan wordt de fout code `AADB2C90118` geretourneerd naar uw toepassing. Uw toepassing moet deze fout code verwerken door de verificatie bibliotheek opnieuw te initialiseren om een Azure AD B2C wacht woord opnieuw in te stellen voor de gebruikers stroom.

In het volgende diagram:

1. Vanuit de toepassing klikt de gebruiker op aanmelden. De app initieert een autorisatie aanvraag, waarna de gebruiker Azure AD B2C om zich aan te melden. De autorisatie aanvraag specificeert de naam van het registratie-of aanmeldings beleid, zoals **B2C_1_signup_signin**.
1. De gebruiker selecteert de koppeling **uw wacht woord verg eten?** Azure AD B2C retourneert de AADB2C90118-fout code naar de toepassing.
1. De toepassing verwerkt de fout code en initieert een nieuwe autorisatie aanvraag. De autorisatie aanvraag specificeert de naam van het beleid voor wachtwoord herstel, zoals **B2C_1_pwd_reset**.

![Gebruikers stroom voor verouderd wacht woord opnieuw instellen](./media/add-password-reset-policy/password-reset-flow-legacy.png)

Als u een voor beeld wilt bekijken, gaat u naar een [eenvoudig ASP.net](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)-voor beeld waarin het koppelen van gebruikers stromen wordt gedemonstreerd.

::: zone pivot="b2c-user-flow"

### <a name="create-a-password-reset-user-flow"></a>Een gebruikersstroom voor het opnieuw instellen van het wachtwoord maken

Als u wilt dat gebruikers van uw toepassing hun wacht woord opnieuw kunnen instellen, moet u een gebruikers stroom voor het opnieuw instellen van wacht woorden maken.

1. Selecteer in het overzichtsmenu van de Azure AD B2C-tenant de optie **Gebruikersstromen** en selecteer vervolgens **Nieuwe gebruikersstroom**.
1. Selecteer op de pagina **Een gebruikersstroom maken** de gebruikersstroom **Wachtwoord opnieuw instellen**. 
1. Onder **Selecteer een versie** selecteert u **Aanbevolen** en vervolgens **Maken**.
1. Voer een **Naam** in voor de gebruikersstroom. Bijvoorbeeld *passwordreset1*.
1. Schakel voor de optie **Id-providers** de optie **Het wachtwoord opnieuw instellen met e-mailadres** in.
1. Onder **toepassings claims** selecteert u **meer weer geven** en kiest u de claims die u wilt retour neren in de autorisatie tokens die worden teruggestuurd naar uw toepassing. Selecteer bijvoorbeeld **Object-ID van gebruiker**.
1. Selecteer **OK**.
1. Selecteer **Maken** om de gebruikersstroom toe te voegen. Het voorvoegsel *B2C_1* wordt automatisch aan de naam toegevoegd.

### <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Selecteer de gebruikers stroom die u hebt gemaakt om de pagina overzicht te openen en selecteer vervolgens **gebruikers stroom uitvoeren**.
1. Selecteer voor **Toepassing** de webtoepassing met de naam *webapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Klik op **gebruikers stroom uitvoeren**, Controleer het e-mail adres van het account dat u eerder hebt gemaakt en selecteer vervolgens **door gaan**.
1. U kunt nu het wacht woord voor de gebruiker wijzigen. Wijzig het wachtwoord en selecteer **Doorgaan**. Het token wordt geretourneerd aan `https://jwt.ms` en moet worden weergegeven.

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="create-a-password-reset-policy"></a>Een beleid voor het opnieuw instellen van het wachtwoord maken

Aangepaste beleids regels zijn een set van XML-bestanden die u uploadt naar uw Azure AD B2C-Tenant om gebruikers trajecten te definiëren. We bieden Starter Packs met verschillende vooraf ontwikkelde beleids regels, waaronder: aanmelden en aanmelden, wacht woord opnieuw instellen en profiel bewerkings beleid. Zie [aan de slag met aangepast beleid in azure AD B2C](custom-policy-get-started.md)voor meer informatie.

::: zone-end

## <a name="next-steps"></a>Volgende stappen

Stel een [geforceerde wacht woord opnieuw](force-password-reset.md)in.
