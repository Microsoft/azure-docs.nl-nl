---
title: Zelfstudie voor het configureren van BioCatch met Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Zelfstudie voor het configureren Azure Active Directory B2C met BioCatch om riskante en frauduleuze gebruikers te identificeren
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: f15cc294a0d3d930548d7d9bdf1d05b552b60fa5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819387"
---
# <a name="tutorial-configure-biocatch-with-azure-active-directory-b2c"></a>Zelfstudie: BioCatch configureren met Azure Active Directory B2C

In deze voorbeeldzelfstudie leert u hoe u Azure Active Directory (AD) B2C-verificatie kunt integreren met [BioCatch](https://www.biocatch.com/) om uw BEVEILIGINGSstatus van Customer Identity and Access Management (HEBTM) verder te verbeteren. BioCatch analyseert het fysieke en cognitieve digitale gedrag van een gebruiker om inzichten te genereren die onderscheid maken tussen legitieme klanten en cybercriminelen.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

- Een Azure-abonnement. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).

- [Een Azure AD B2C tenant](tutorial-create-tenant.md) die is gekoppeld aan uw Azure-abonnement.

- Een [BioCatch-account.](https://www.biocatch.com/contact-us)

## <a name="scenario-description"></a>Scenariobeschrijving

BioCatch-integratie omvat de volgende onderdelen:

- **Een web-app of webservice:** de gebruiker bladert eerst naar deze webservice. Deze webservice maakt een instantie van een unieke clientsessie-id die naar BioCatch wordt verzonden. De clientsessie-id begint vervolgens onmiddellijk met het verzenden van kenmerken van gebruikersgedrag naar BioCatch.

- **Een methode:** verzendt de unieke clientsessie-id naar Azure AD B2C. In het opgegeven voorbeeld wordt JavaScript gebruikt voor het invoeren van de waarde in een verborgen HTML-veld.

- **Een Azure AD B2C ui:** verbergt een HTML-veld voor de invoer van de clientsessie-id uit JavaScript, als u de bovenstaande methode gebruikt

- **Azure AD B2C beleid maken**

  - Neemt de aangepaste clientsessie-id van de gebruikersinterface in de vorm van een claim. Dit wordt bereikt via een zelf-bevestigd technisch profiel

  - Kan worden geïntegreerd met BioCatch via een REST API claimprovider en geeft de clientsessie-id door aan het BioCatch-platform

  - Er worden meerdere aangepaste claims geretourneerd van BioCatch om vervolgens actie te ondernemen op basis van de aangepaste beleidslogica

  - Een userjourney, die een geretourneerde claim evalueert, bijvoorbeeld sessierisico, en voorwaardelijk een actie uitvoert, zoals Multi-Factor Authentication (MFA) aanroepen.

![Diagram van de bio catch-architectuur.](media/partner-biocatch/biocatch-architecture-diagram.png)

| Stap  | Beschrijving |
|:---|:-----------------------|
|1a     | De gebruiker bladert door de webservice. De webservice retourneert vervolgens HTML-, CSS- of JavaScript-waarden en configureert om de BioCatch JavaScript SDK te laden. JavaScript aan de clientzijde configureert/stelt de clientsessie-id in voor de BioCatch SDK. De webservice kan de clientsessie-id ook vooraf configureren en naar de client verzenden.        |
|1b    |  Configureer de ge instantieerde BioCatch JavaScript SDK op basis van het BioCatch-platform. Begin onmiddellijk met het verzenden van kenmerken van gebruikersgedrag naar BioCatch vanaf het clientapparaat, met behulp van de clientsessie-id uit stap 1a.    |
|2     |  De gebruiker probeert zich te registreren/aanmelden en wordt omgeleid naar Azure AD B2C.      |
|3a    | Een deel van de userjourney is een zelf-asserte claimprovider, die de clientsessie-id als invoer gebruikt. Dit veld wordt verborgen op het scherm. U kunt JavaScript gebruiken om de sessie-id in het veld in te geven. Selecteer de *volgende* knop om door te gaan met het registreren/aanmelden.|
|3b     | De clientsessie-id wordt verzonden naar het BioCatch-platform om een risicoscore te bepalen. |
|3c     | BioCatch retourneert informatie over de sessie, zoals de risicoscore, en een aanbeveling over wat u moet doen: toestaan of blokkeren |
|3D    |De userjourney heeft een voorwaardelijke controlestap, die op de geretourneerde claims handelt|
| 4   | Op basis van het resultaat van de voorwaardelijke controle wordt een actie zoals *stapsgewijze MFA* aangeroepen|
|5    | Vanaf het moment dat de gebruiker de webservicepagina voor het eerst gebruikt, kan de webservice de clientsessie-id gebruiken om een query uit te voeren op de BioCatch-API om de risicoscore en sessiegegevens in realtime te bepalen. |

## <a name="onboard-with-biocatch"></a>Onboarden met BioCatch

Neem contact [op met BioCatch](https://www.biocatch.com/contact-us) en maak een account.

## <a name="configure-the-custom-ui"></a>De aangepaste gebruikersinterface configureren

Het is raadzaam om het veld clientsessie-id te verbergen. Gebruik CSS, JavaScript of een andere methode om het veld te verbergen. Voor testdoeleinden kunt u het veld ontseken. JavaScript wordt bijvoorbeeld gebruikt om het invoerveld te verbergen als:

```
document.getElementById("clientSessionId").style.display = 'none';
```

## <a name="configure--azure-ad-b2c-identity-experience-framework-policies"></a>Beleid voor Azure AD B2C Identity Experience Framework configureren

1. Configureer de eerste [aangepaste beleidsconfiguratie](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started).

2. Maak een nieuw bestand dat wordt overgenomen van het extensiebestand.

    ```
    <BasePolicy> 

        <TenantId>tenant.onmicrosoft.com</TenantId> 

        <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId> 

      </BasePolicy> 
    ```

3. Maak een verwijzing naar de aangepaste gebruikersinterface om het invoervak onder de resource BuildingBlocks te verbergen.

    ```
    <ContentDefinitions> 

        <ContentDefinition Id="api.selfasserted"> 

            <LoadUri>https://domain.com/path/to/selfAsserted.cshtml</LoadUri> 

            <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri> 

          </ContentDefinition> 

        </ContentDefinitions>
    ```

4. Voeg de volgende claims toe onder de resource BuildingBlocks.

    ```
    <ClaimsSchema> 

          <ClaimType Id="riskLevel"> 

            <DisplayName>Session risk level</DisplayName> 

            <DataType>string</DataType>       

          </ClaimType> 

          <ClaimType Id="score"> 

            <DisplayName>Session risk score</DisplayName> 

            <DataType>int</DataType>       

          </ClaimType> 

          <ClaimType Id="clientSessionId"> 

            <DisplayName>The ID of the client session</DisplayName> 

            <DataType>string</DataType> 

            <UserInputType>TextBox</UserInputType> 

          </ClaimType> 

    <ClaimsSchema> 
    ```

5. Configureer de zelf-assertieclaimprovider voor het veld clientsessie-id.

    ```
    <ClaimsProvider> 

          <DisplayName>Client Session ID Claims Provider</DisplayName> 

          <TechnicalProfiles> 

            <TechnicalProfile Id="login-NonInteractive-clientSessionId"> 

              <DisplayName>Client Session ID TP</DisplayName> 

              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" /> 

              <Metadata> 

                <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item> 

              </Metadata> 

              <CryptographicKeys> 

                <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" /> 

              </CryptographicKeys> 

            <!—Claim we created earlier --> 

              <OutputClaims> 

                <OutputClaim ClaimTypeReferenceId="clientSessionId" Required="false" DefaultValue="100"/> 

              </OutputClaims> 

            <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" /> 

            </TechnicalProfile> 

          </TechnicalProfiles> 

        </ClaimsProvider> 
    ```

6. Configureer REST API claimprovider voor BioCatch. 

    ```
    <TechnicalProfile Id="BioCatch-API-GETSCORE"> 

          <DisplayName>Technical profile for BioCatch API to return session information</DisplayName> 

          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" /> 

          <Metadata> 

            <Item Key="ServiceUrl">https://biocatch-url.com/api/v6/score?customerID=<customerid>&amp;action=getScore&amp;uuid=<uuid>&amp;customerSessionID={clientSessionId}&amp;solution=ATO&amp;activtyType=<activity_type>&amp;brand=<brand></Item> 

            <Item Key="SendClaimsIn">Url</Item> 

            <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item> 

            <!-- Set AuthenticationType to Basic or ClientCertificate in production environments --> 

            <Item Key="AuthenticationType">None</Item> 

            <!-- REMOVE the following line in production environments --> 

            <Item Key="AllowInsecureAuthInProduction">true</Item> 

          </Metadata> 

          <InputClaims> 

            <InputClaim ClaimTypeReferenceId="clientsessionId" /> 

          </InputClaims> 

          <OutputClaims> 

            <OutputClaim ClaimTypeReferenceId="riskLevel" /> 

            <OutputClaim ClaimTypeReferenceId="score" /> 

          </OutputClaims> 

          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" /> 

        </TechnicalProfile> 

      </TechnicalProfiles>
    ```

    > [!Note]
    > BioCatch biedt u de URL, de klant-id en de unieke gebruikers-id (uuID) die u wilt configureren. De klant SessionID-claim wordt doorgegeven als een queryreeksparameter voor BioCatch. U kunt het activiteitstype kiezen, *bijvoorbeeld MAKE_PAYMENT*.

7. Configureer de userjourney; het voorbeeld volgen

   1. De clientSessionID als een claim op te halen

   1. De BioCatch-API aanroepen om de sessiegegevens op te halen

   1. Als het geretourneerde *claimrisico* gelijk is *aan laag,* slaat u de stap voor MFA over, anders dwingt u MFA van de gebruiker af

    ```
    <OrchestrationStep Order="8" Type="ClaimsExchange"> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="clientSessionIdInput" TechnicalProfileReferenceId="login-NonInteractive-clientSessionId" /> 

              </ClaimsExchanges> 

            </OrchestrationStep> 

            <OrchestrationStep Order="9" Type="ClaimsExchange"> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="BcGetScore" TechnicalProfileReferenceId=" BioCatch-API-GETSCORE" /> 

              </ClaimsExchanges> 

            </OrchestrationStep> 

            <OrchestrationStep Order="10" Type="ClaimsExchange"> 

              <Preconditions> 

                <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 

                  <Value>riskLevel</Value> 

                  <Value>LOW</Value> 

                  <Action>SkipThisOrchestrationStep</Action> 

                </Precondition> 

              </Preconditions> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" /> 

              </ClaimsExchanges>  

    ```

8. Configureren op basis van relying party-configuratie (optioneel)

    Het is handig om de door BioCatch geretourneerde informatie als claims in het token door te geven aan uw *toepassing,* met name *risklevel* en score .

    ```
    <RelyingParty> 

        <DefaultUserJourney ReferenceId="SignUpOrSignInMfa" /> 

        <UserJourneyBehaviors> 

          <SingleSignOn Scope="Tenant" KeepAliveInDays="30" /> 

          <SessionExpiryType>Absolute</SessionExpiryType> 

          <SessionExpiryInSeconds>1200</SessionExpiryInSeconds> 

          <ScriptExecution>Allow</ScriptExecution> 

        </UserJourneyBehaviors> 

        <TechnicalProfile Id="PolicyProfile"> 

          <DisplayName>PolicyProfile</DisplayName> 

          <Protocol Name="OpenIdConnect" /> 

          <OutputClaims> 

            <OutputClaim ClaimTypeReferenceId="displayName" /> 

            <OutputClaim ClaimTypeReferenceId="givenName" /> 

            <OutputClaim ClaimTypeReferenceId="surname" /> 

            <OutputClaim ClaimTypeReferenceId="email" /> 

            <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" /> 

            <OutputClaim ClaimTypeReferenceId="identityProvider" />                 

            <OutputClaim ClaimTypeReferenceId="riskLevel" /> 

            <OutputClaim ClaimTypeReferenceId="score" /> 

            <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" /> 

          </OutputClaims> 

          <SubjectNamingInfo ClaimType="sub" /> 

        </TechnicalProfile> 

      </RelyingParty> 

    ```

## <a name="integrate-with-azure-ad-b2c"></a>Integreren met Azure AD B2C

Volg deze stappen om de beleidsbestanden toe te voegen aan Azure AD B2C

1. Meld u aan bij  [**Azure Portal**](https://portal.azure.com/) globale beheerder van uw Azure AD B2C tenant.

2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C tenant. Selecteer **Directory +**   abonnementsfilter in het bovenste menu en kies de map die uw tenant bevat.

3. Kies **Alle services** in de linkerbovenhoek van de   Azure Portal, zoek en selecteer Azure AD B2C.

4. Navigeer **naar Azure AD B2C**   >  **Identity Experience Framework**

3. Upload alle beleidsbestanden naar uw tenant.

## <a name="test-the-solution"></a>De oplossing testen

1. [Een dummytoepassing registreren, die wordt omgeleid naar JWT.MS](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=app-reg-ga)  

2. Selecteer onder **Identity Experience Framework** het beleid dat u hebt gemaakt

3. Selecteer in het beleidsvenster de dummy-JWT.MS toepassing en selecteer **Nu uitvoeren**

4. Door de aanmeldingsstroom gaan en een account maken. Token dat wordt JWT.MS moet 2x claims hebben voor riskLevel en score. Volg het voorbeeld.  

    ```
    { 

      "typ": "JWT", 

      "alg": "RS256", 

      "kid": "_keyid" 

    }.{ 

      "exp": 1615872580, 

      "nbf": 1615868980, 

      "ver": "1.0", 

      "iss": "https://tenant.b2clogin.com/12345678-1234-1234-1234-123456789012/v2.0/", 

      "sub": "12345678-1234-1234-1234-123456789012", 

      "aud": "12345678-1234-1234-1234-123456789012", 

      "acr": "b2c_1a_signup_signin_biocatch_policy", 

      "nonce": "defaultNonce", 

      "iat": 1615868980, 

      "auth_time": 1615868980, 

      "name": "John Smith", 

      "email": "john.smith@contoso.com", 

      "given_name": "John", 

      "family_name": "Smith", 

      "riskLevel": "LOW", 

      "score": 275, 

      "tid": "12345678-1234-1234-1234-123456789012" 

    }.[Signature]  

    ```

## <a name="additional-resources"></a>Aanvullende bronnen

- [Aangepast beleid in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Aan de slag met aangepaste beleidsregels in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
