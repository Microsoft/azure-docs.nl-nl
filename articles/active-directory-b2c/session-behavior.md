---
title: Gedrag van sessies configureren-Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over het configureren van het gedrag van sessies in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/04/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 3a3cdb93ee4cbf4a2e15540b9daf78b6c231d393
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579736"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Sessiegedrag in Azure Active Directory B2C configureren

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Eenmalige aanmelding (SSO) voegt beveiligings-en gebruiks gemak toe wanneer gebruikers zich aanmelden bij verschillende toepassingen in Azure Active Directory B2C (Azure AD B2C). In dit artikel worden de methoden voor eenmalige aanmelding beschreven die worden gebruikt in Azure AD B2C en kunt u de meest geschikte SSO-methode kiezen voor het configureren van uw beleid.

Met eenmalige aanmelding kunnen gebruikers zich eenmaal aanmelden met één account en toegang krijgen tot meerdere toepassingen. De toepassing kan een web-, mobiele of toepassing met één pagina zijn, ongeacht het platform of de domein naam.

Wanneer de gebruiker zich voor het eerst aanmeldt bij een toepassing, wordt Azure AD B2C een cookie op basis van cookies. Bij volgende verificatie aanvragen Azure AD B2C leest en valideert de cookie op basis van cookies, en wordt een toegangs token uitgegeven zonder dat de gebruiker wordt gevraagd om zich opnieuw aan te melden. Als de cookie-gebaseerde sessie verloopt of ongeldig wordt, wordt de gebruiker gevraagd zich opnieuw aan te melden.  

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="azure-ad-b2c-session-overview"></a>Overzicht van Azure AD B2C-sessie

Integratie met Azure AD B2C omvat drie typen SSO-sessies:

- **Azure AD B2C** -sessie beheerd door Azure AD B2C
- **Federatieve id-provider** : sessie beheerd door de ID-provider, bijvoorbeeld Facebook, sales force of Microsoft-account
- **Toepassings** sessie beheerd door de toepassing Web, mobiel of Eén pagina

![SSO-sessie](media/session-behavior/sso-session-types.png)

### <a name="azure-ad-b2c-session"></a>Azure AD B2C sessie 

Wanneer een gebruiker is geverifieerd met een lokaal of sociaal account, Azure AD B2C een cookie op basis van cookies opgeslagen op de browser van de gebruiker. De cookie wordt opgeslagen onder de domein naam van de Azure AD B2C Tenant, zoals `https://contoso.b2clogin.com` .

Als een gebruiker zich voor het eerst aanmeldt met een federatief account en vervolgens tijdens het sessie tijd venster (time-to-Live of TTL) zich aanmeldt bij dezelfde app of een andere app, probeert Azure AD B2C een nieuw toegangs token op te halen uit de federatieve id-provider. Als de sessie van de federatieve id-provider is verlopen of ongeldig is, vraagt de federatieve id-provider de gebruiker om referenties. Als de sessie nog steeds actief is (of als de gebruiker zich heeft aangemeld met een lokaal account in plaats van een Federatie account), wordt de gebruiker door Azure AD B2C geautoriseerd en worden verdere prompts overbodig.

U kunt het gedrag van de sessie configureren, met inbegrip van de sessie-TTL en hoe Azure AD B2C de sessie over beleids regels en toepassingen deelt.

### <a name="federated-identity-provider-session"></a>Sessie van federatieve id-provider

Een sociaal-of ENTER prise-ID-provider beheert zijn eigen sessie. De cookie wordt opgeslagen onder de domein naam van de identiteits provider, zoals `https://login.salesforce.com` . Azure AD B2C heeft geen invloed op de sessie van de federatieve id-provider. In plaats daarvan wordt het gedrag van de sessie bepaald door de federatieve id-provider. 

Denkt u zich het volgende scenario eens in:

1. Een gebruiker meldt zich aan bij Facebook om de feed te controleren.
2. Later opent de gebruiker uw toepassing en start het aanmeldings proces. De gebruiker wordt door de toepassing omgeleid naar Azure AD B2C om het aanmeldings proces te volt ooien.
3. Op de Azure AD B2C registratie-of aanmeldings pagina wordt de gebruiker gekozen om u aan te melden met hun Facebook-account. De gebruiker wordt omgeleid naar Facebook. Als er een actieve sessie op Facebook is, wordt de gebruiker niet gevraagd om de referenties op te geven en wordt deze direct omgeleid naar Azure AD B2C met een Facebook-token.

### <a name="application-session"></a>Toepassings sessie

Een web-, mobiele of toepassing met één pagina kan worden beveiligd door OAuth-toegang, ID-tokens of SAML-tokens. Wanneer een gebruiker probeert toegang te krijgen tot een beveiligde bron op de app, controleert de app of er een actieve sessie aan de kant van de toepassing is. Als er geen app-sessie is of de sessie is verlopen, wordt de gebruiker door de app Azure AD B2C op de aanmeldings pagina.

De toepassings sessie kan een op cookies gebaseerde sessie zijn die is opgeslagen onder de naam van het toepassings domein, zoals `https://contoso.com` . Mobiele toepassingen kunnen de sessie op een andere manier opslaan, maar met een vergelijk bare methode.

## <a name="configure-azure-ad-b2c-session-behavior"></a>Gedrag van Azure AD B2C-sessie configureren

U kunt het gedrag van de Azure AD B2C-sessie configureren, met inbegrip van:

- **Levens duur van de web-app (minuten)** : de hoeveelheid tijd die de cookie van de Azure AD B2C sessie wordt opgeslagen in de browser van de gebruiker nadat de verificatie is geslaagd. U kunt de levens duur van de sessie Maxi maal 24 uur instellen.

- **Sessietime-out van web-app** : Hiermee wordt aangegeven hoe een sessie wordt uitgebreid met de instelling voor de duur van de sessie of de instelling aangemeld blijven (KMSI).
  - **Rolling** -geeft aan dat de sessie wordt uitgebreid telkens wanneer de gebruiker een verificatie op basis van cookies uitvoert (standaard).
  - **Absoluut** : geeft aan dat de gebruiker na de opgegeven tijds periode opnieuw moet worden geverifieerd.

- **Configuratie van eenmalige aanmelding** : de Azure AD B2C-sessie kan worden geconfigureerd met de volgende scopes.
  - **Tenant** : dit is de standaard instelling. Met deze instelling kunnen meerdere toepassingen en gebruikers stromen in uw B2C-Tenant dezelfde gebruikers sessie delen. Wanneer een gebruiker zich bijvoorbeeld bij een toepassing aanmeldt, kan de gebruiker zich bij het openen van de app ook probleemloos aanmelden.
  - **Toepassing** : met deze instelling kunt u een gebruikers sessie alleen voor een toepassing, onafhankelijk van andere toepassingen, onderhouden. U kunt deze instelling bijvoorbeeld gebruiken als u wilt dat de gebruiker zich aanmeldt bij Contoso apotheek, ongeacht of de gebruiker al is aangemeld bij Contoso-boodschappen.
  - **Beleid** : met deze instelling kunt u een gebruikers sessie alleen voor een gebruikers stroom onderhouden, onafhankelijk van de toepassingen die er gebruik van maken. Als de gebruiker zich al heeft aangemeld en een MFA-stap (multi-factor Authentication) heeft voltooid, kan de gebruiker toegang krijgen tot hogere beveiligings onderdelen van meerdere toepassingen, zolang de sessie die aan de gebruikers stroom is gekoppeld, niet verloopt.
  - **Onderdrukt** : deze instelling zorgt ervoor dat de gebruiker tijdens elke uitvoering van het beleid de hele gebruikers stroom uitvoert.
- **Aangemeld blijven (KMSI)** : Hiermee breidt u de levens duur van de sessie uit door het gebruik van een permanente cookie. Als deze functie is ingeschakeld en de gebruiker deze selecteert, blijft de sessie actief, zelfs nadat de gebruiker de browser heeft gesloten en opnieuw opent. De sessie wordt alleen ingetrokken wanneer de gebruiker zich afmeldt. De functie KMSI is alleen van toepassing op aanmelden met lokale accounts. De functie KMSI heeft voor rang op de levens duur van de sessie.

::: zone pivot="b2c-user-flow"

Het gedrag van de sessie configureren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zorg ervoor dat u de map met uw Azure AD B2C-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Azure AD B2C Tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **gebruikers stromen**.
1. Open de gebruikers stroom die u eerder hebt gemaakt.
1. Selecteer **Eigenschappen**.
1. Configureer de duur van de **Web-app-sessie (minuten)**, **time-out voor de web-app sessie**, **configuratie van eenmalige aanmelding** en **vereisen een id-token in afmeldings aanvragen** , indien nodig.
1. Klik op **Opslaan**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Als u uw sessie-en SSO-configuraties wilt wijzigen, voegt u een **UserJourneyBehaviors** -element toe in het element [RelyingParty](relyingparty.md) .  Het **UserJourneyBehaviors** -element moet direct volgen op de **DefaultUserJourney**. Het **UserJourneyBehavors** -element moet er als volgt uitzien:

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
::: zone-end

## <a name="enable-keep-me-signed-in-kmsi"></a>Aangemeld blijven inschakelen (KMSI)

U kunt de functie KMSI inschakelen voor gebruikers van uw web-en systeem eigen toepassingen die lokale accounts hebben in uw Azure AD B2C Directory. Wanneer u de functie inschakelt, kunnen gebruikers ervoor kiezen om aangemeld te blijven, zodat de sessie actief blijft nadat de browser is gesloten. Vervolgens kunnen ze de browser opnieuw openen zonder dat u wordt gevraagd om de gebruikers naam en het wacht woord opnieuw in te voeren. Deze toegang wordt ingetrokken wanneer een gebruiker zich afmeldt.

![Voor beeld van aanmeldings pagina voor aanmelding met het selectie vakje aangemeld blijven](./media/session-behavior/keep-me-signed-in.png)


::: zone pivot="b2c-user-flow"

KMSI kan worden geconfigureerd op het niveau van de afzonderlijke gebruikers stroom. Voordat u KMSI inschakelt voor uw gebruikers stromen, moet u rekening houden met het volgende:

- KMSI wordt alleen ondersteund voor de **Aanbevolen** versies van registratie en aanmelding (Susi), aanmelding en profiel bewerkings gebruikers stromen. Als u momenteel **een preview-** versie van de **standaard** -of oudere versies van deze gebruikers stromen hebt en u KMSI wilt inschakelen, moet u nieuwe, **Aanbevolen** versies van deze gebruikers stromen maken.
- KMSI wordt niet ondersteund bij het opnieuw instellen van het wacht woord of het registreren van gebruikers stromen.
- Als u KMSI wilt inschakelen voor alle toepassingen in uw Tenant, raden we u aan om KMSI in te scha kelen voor alle gebruikers stromen in uw Tenant. Omdat een gebruiker tijdens een sessie meerdere beleids regels kan weer gegeven, is het mogelijk dat ze een KMSI hebben die niet is ingeschakeld, waardoor de KMSI cookie uit de sessie wordt verwijderd.
- KMSI moet niet zijn ingeschakeld op open bare computers.

### <a name="configure-kmsi-for-a-user-flow"></a>KMSI voor een gebruikers stroom configureren

KMSI inschakelen voor uw gebruikers stroom:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het filter **Directory + abonnement**   in het bovenste menu en kies de map die uw Azure AD B2C Tenant bevat.
3. Kies **alle services**   in de linkerbovenhoek van de Azure Portal en zoek en selecteer **Azure AD B2C**.
4. Selecteer **gebruikers stromen (beleid)**.
5. Open de gebruikers stroom die u eerder hebt gemaakt.
6. Selecteer **Eigenschappen**.

7. Onder  **gedrag van sessie** selecteert u aanmelden **bij sessie inschakelen**. Voer een waarde tussen 1 en 90 **in om het** aantal dagen op te geven dat een sessie geopend mag blijven.


   ![Aangemelde sessie inschakelen](media/session-behavior/enable-keep-me-signed-in.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Gebruikers moeten deze optie niet inschakelen op open bare computers.

### <a name="configure-the-page-identifier"></a>De pagina-id configureren

Als u KMSI wilt inschakelen, stelt u het inhouds definitie- `DataUri` element in op [pagina-id](contentdefinitions.md#datauri) `unifiedssp` en [pagina versie](page-layout.md) *1.1.0* of hoger.

1. Open het extensie bestand van uw beleid. Bijvoorbeeld <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> . Dit extensie bestand is een van de beleids bestanden in het aangepaste beleids Starter Pack, die u in de vereiste moet hebben verkregen, aan de [slag met aangepast beleid](custom-policy-get-started.md).
1. Zoek het element **BuildingBlocks** . Als het element niet bestaat, voegt u het toe.
1. Voeg het element **ContentDefinitions** toe aan het element **BuildingBlocks** van het beleid.

    Het aangepaste beleid moet eruitzien zoals in het volgende code fragment:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

### <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>De meta gegevens toevoegen aan het zelf-bebevestigde technische profiel

Als u het selectie vakje KMSI wilt toevoegen aan de registratie-en aanmeldings pagina, stelt `setting.enableRememberMe` u de meta gegevens in op waar. Overschrijf de technische profielen van de SelfAsserted-LocalAccountSignin-e-mail in het extensie bestand.

1. Zoek het element ClaimsProviders. Als het element niet bestaat, voegt u het toe.
1. Voeg de volgende claim provider toe aan het ClaimsProviders-element:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. Sla het bestand met extensies op.

### <a name="configure-a-relying-party-file"></a>Een Relying Party-bestand configureren

Werk het Relying Party (RP)-bestand bij waarmee de door u gemaakte gebruikers traject wordt gestart. Met de para meter keepAliveInDays kunt u configureren hoe lang de cookie voor het bewaren van aangemeld (KMSI) in de sessie moet blijven staan. Als u de waarde bijvoorbeeld instelt op 30, blijft de KMSI-sessie cookie 30 dagen lang. Het bereik voor de waarde ligt tussen 1 en 90 dagen.

1. Open uw aangepaste beleids bestand. Bijvoorbeeld *SignUpOrSignin.xml*.
1. Als deze nog niet bestaat, voegt `<UserJourneyBehaviors>` u een onderliggend knoop punt toe aan het `<RelyingParty>` knoop punt. Het moet direct na worden geplaatst `<DefaultUserJourney ReferenceId="User journey Id" />` , bijvoorbeeld: `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />` .
1. Voeg het volgende knoop punt toe als onderliggend item van het `<UserJourneyBehaviors>` element.

    ```xml
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

We raden u aan de waarde van SessionExpiryInSeconds in te stellen op een korte periode (1200 seconden), terwijl de waarde van KeepAliveInDays kan worden ingesteld op een relatief lange periode (30 dagen), zoals wordt weer gegeven in het volgende voor beeld:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
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
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

::: zone-end

## <a name="sign-out"></a>Afmelden

Wanneer u de gebruiker van de toepassing wilt ondertekenen, is het niet voldoende om de cookies van de toepassing te wissen of de sessie te beëindigen met de gebruiker. U moet de gebruiker omleiden naar Azure AD B2C om u af te melden. Anders is het mogelijk dat de gebruiker zich opnieuw kan aanmelden bij uw toepassingen zonder dat u de referenties opnieuw hoeft in te voeren.

Bij een afmeldings aanvraag Azure AD B2C:

1. Hiermee wordt de Azure AD B2C cookie op basis van cookies ongeldig gemaakt.
::: zone pivot="b2c-user-flow"
2. Pogingen om zich af te melden bij federatieve id-providers
::: zone-end
::: zone pivot="b2c-custom-policy"
3. Pogingen om u af te melden bij federatieve id-providers:
   - OpenID Connect Connect-als het-eind punt van de ID-provider met de bekende configuratie een `end_session_endpoint` locatie aangeeft. De afmeldings aanvraag geeft niet de `id_token_hint` para meter door. Als deze para meter is vereist voor de federatieve id-provider, mislukt de afmeldings aanvraag.
   - OAuth2: als de [meta gegevens van de identiteits provider](oauth2-technical-profile.md#metadata) de `end_session_endpoint` locatie bevatten.
   - SAML: als de [meta gegevens van de identiteits provider](identity-provider-generic-saml.md) de `SingleLogoutService` locatie bevatten.
4. U kunt zich optioneel afmelden bij andere toepassingen. Zie de sectie voor [eenmalige afmelding](#single-sign-out) voor meer informatie.

> [!NOTE]
> U kunt de afmelding bij federatieve id-providers uitschakelen door de meta gegevens van het technische profiel van de identiteits provider `SingleLogoutEnabled` in te stellen op `false` .
::: zone-end

Met de afmelding wordt de status voor eenmalige aanmelding van de gebruiker met Azure AD B2C gewist, maar de gebruiker kan de gebruikers naam niet van de provider sessie van de sociale id ondertekenen. Als de gebruiker tijdens een volgende aanmelding dezelfde id-provider selecteert, kunnen ze opnieuw worden geverifieerd zonder hun referenties in te voeren. Als een gebruiker zich wil afmelden bij de toepassing, betekent dit niet noodzakelijkerwijs dat ze zich willen afmelden bij hun Facebook-account. Als lokale accounts echter worden gebruikt, wordt de sessie van de gebruiker op de juiste wijze beëindigd.

::: zone pivot="b2c-custom-policy"

### <a name="single-sign-out"></a>Eenmalige afmelding 

Wanneer u de gebruiker omleidt naar het eind punt van de Azure AD B2C-afmelding (voor zowel OAuth2-als SAML-protocollen), Azure AD B2C de gebruikers sessie wissen uit de browser. De gebruiker kan echter nog steeds zijn aangemeld bij andere toepassingen die Azure AD B2C voor verificatie gebruiken. Om ervoor te zorgen dat deze toepassingen de gebruiker tegelijk kunnen ondertekenen, stuurt Azure AD B2C een HTTP GET-aanvraag naar de registratie `LogoutUrl` van alle toepassingen waarbij de gebruiker momenteel is aangemeld.

Toepassingen moeten reageren op deze aanvraag door een wille keurige sessie te wissen waarmee de gebruiker wordt geïdentificeerd en een antwoord wordt geretourneerd `200` . Als u eenmalige afmelding in uw toepassing wilt ondersteunen, moet u een `LogoutUrl` in de code van uw toepassing implementeren. 

Voor de ondersteuning van eenmalige afmelding moeten voor de token Uitgever technische profielen voor zowel JWT als SAML worden opgegeven:

- De protocol naam, zoals `<Protocol Name="OpenIdConnect" />`
- De verwijzing naar het profiel voor technische sessie, zoals `UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />` .

In het volgende voor beeld ziet u de JWT-en SAML-token verleners met eenmalige afmelding:

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

::: zone-end

### <a name="secure-your-logout-redirect"></a>Uw afmeldings omleiding beveiligen

Na afmelden wordt de gebruiker omgeleid naar de URI die is opgegeven in de `post_logout_redirect_uri` para meter, ongeacht de antwoord-url's die zijn opgegeven voor de toepassing. Als er echter een geldig `id_token_hint` wordt door gegeven en het token voor het **vereisen van een id in afmeldings aanvragen** is ingeschakeld, wordt door Azure AD B2C gecontroleerd of de waarde `post_logout_redirect_uri` overeenkomt met een van de geconfigureerde omleidings-uri's van de toepassing voordat de omleiding wordt uitgevoerd. Als er geen overeenkomende antwoord-URL voor de toepassing is geconfigureerd, wordt een fout bericht weer gegeven en wordt de gebruiker niet omgeleid. 

::: zone pivot="b2c-user-flow"

Een ID-token in afmeldings aanvragen vereisen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zorg ervoor dat u de map met uw Azure AD B2C-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Azure AD B2C Tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **gebruikers stromen**.
1. Open de gebruikers stroom die u eerder hebt gemaakt.
1. Selecteer **Eigenschappen**.
1. Schakel het **token vereisen een id in voor afmeldings aanvragen**.
1. Ga terug naar  **Azure AD B2C**.
1. Selecteer **app-registraties** en selecteer vervolgens uw toepassing.
1. Selecteer **Verificatie**.
1. Typ in het tekstvak **Afmeldings-URL** de omleidings-URI voor post afmelden en selecteer vervolgens **Opslaan**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Als u een ID-token in afmeldings aanvragen wilt vereisen, voegt u een **UserJourneyBehaviors** -element toe in het element [RelyingParty](relyingparty.md) . Stel vervolgens de **EnforceIdTokenHintOnLogout** van het **SingleSignOn** -element in op `true` . Het **UserJourneyBehaviors** -element moet er als volgt uitzien:

```xml
<UserJourneyBehaviors>
  <SingleSignOn Scope="Tenant" EnforceIdTokenHintOnLogout="true"/>
</UserJourneyBehaviors>
```

::: zone-end

De afmeldings-URL van de toepassing configureren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zorg ervoor dat u de map met uw Azure AD B2C-Tenant gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw Azure AD B2C Tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **app-registraties** en selecteer vervolgens uw toepassing.
1. Selecteer **Verificatie**.
1. Typ in het tekstvak **Afmeldings-URL** de omleidings-URI voor post afmelden en selecteer vervolgens **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [configureren van tokens in azure AD B2C](configure-tokens.md).
