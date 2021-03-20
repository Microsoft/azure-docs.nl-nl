---
title: TypingDNA met Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Meer informatie over het integreren van Azure AD B2C verificatie met TypingDNA om de identiteits verificatie en controle op basis van het door de gebruiker getypte patroon te helpen, biedt oplossingen voor ID-verificatie waarbij multi-factor Authentication wordt afgedwongen en waarmee kan worden voldaan aan de algemene vereisten voor de betalings Services-instructie 2 (PSD2).
author: gargi-sinha
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 68617d86fda940c5d3752f2389088a8c729aebec
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97108345"
---
# <a name="tutorial-for-configuring-typingdna-with-azure-active-directory-b2c"></a>Zelf studie voor het configureren van TypingDNA met Azure Active Directory B2C

In dit overzicht leert u hoe u een voor beeld van een online-betalings toepassing kunt integreren in Azure Active Directory B2C met de APP TypingDNA. Met behulp van de TypingDNA-app kunnen Azure AD B2C klanten voldoen aan de PSD2-transactie vereisten ( [betalings Services](https://www.typingdna.com/use-cases/sca-strong-customer-authentication) ) door middel van toetsaanslag dynamiek en sterke klant authenticatie. Meer informatie over TypingDNA vindt u [hier](https://www.typingdna.com/).

 Azure AD B2C maakt gebruik van de technologieën van TypingDNA om de gebruikers kenmerken vast te leggen en ze te laten registreren en analyseren voor de kennis van elke authenticatie. Hiermee wordt een beveiligingslaag toegevoegd met betrekking tot de riskiness van een verificatie en worden de risico niveaus geëvalueerd. Azure AD B2C kunt andere mechanismen aanroepen om het vertrouwen van de gebruiker te verzorgen dat ze worden claimd door Azure AD MFA aan te roepen, e-mail verificatie te forceren of enige andere aangepaste logica voor uw scenario.

>[!NOTE]
> Dit voorbeeld beleid is gebaseerd op [SocialAndLocalAccountsWithMfa](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) Starter Pack.

## <a name="scenario-description"></a>Scenariobeschrijving

![Scherm afbeelding van TypingDNA-architectuur diagram](./media/partner-typingdna/typingdna-architecture-diagram.png)

### <a name="sign-up"></a>Registratie

1. Azure AD B2C pagina's gebruiken de Java script-bibliotheek van TypingDNA om het type patroon van de gebruiker vast te leggen. De gebruikers naam en het wacht woord worden bijvoorbeeld vastgelegd bij het aanmelden voor de eerste inschrijving en vervolgens bij elke aanmelding voor verificatie.

2. Wanneer de gebruiker de pagina verzendt, wordt de type kenmerk van de gebruiker door de TypingDNA-bibliotheek berekend. Daarna voegt u de gegevens in een verborgen tekst veld in dat Azure AD B2C heeft gerenderd. Dit veld is verborgen met CSS.  

    Het voor [Beeld bevat HTML-bestanden](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignUp.cshtml) met de Java script-en CSS-wijzigingen, waarnaar wordt verwezen door de- `api.selfasserted.tdnasignin` en- `api.selfasserted.tdnasignup` inhouds definities. Raadpleeg [de pagina-inhoud](./customize-ui-with-html.md#hosting-the-page-content) hosten om uw HTML-bestanden te hosten.

3. Azure AD B2C heeft nu het type patroon binnen de claim verzameling wanneer de gebruiker zijn of haar referenties verzendt. Dit moet een API (u) aanroepen om deze gegevens door te geven aan het TypingDNA-REST API-eind punt. Deze API is opgenomen in het voor [beeld (typingDNA-API-interface)](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface).
4. De middelste laag-API geeft de type patroon gegevens door aan TypingDNA REST API. Bij het aanmelden wordt het [eind punt van de controle gebruiker](https://api.typingdna.com/index.html#api-API_Services-GetUser) aangeroepen om te bevestigen dat de gebruiker niet bestaat en vervolgens wordt het eind punt van het [opslag patroon](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) aangeroepen om het eerste type patroon van de gebruiker op te slaan.

> [!NOTE]
> Alle aanroepen naar het TypingDNA REST API-eind punt verzenden een gebruikers-id. Dit moet een gehashte waarde zijn. Azure AD B2C maakt gebruik `HashObjectIdWithEmail` van de trans formatie van claims om het e-mail bericht te hashen met een wille keurig zout en geheim.  

De REST API-aanroepen worden gemodelleerd `validationTechnicalProfiles` in `LocalAccountSignUpWithLogonEmail-TDNA` :

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail-TDNA" />
    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="true"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser"/>

</ValidationTechnicalProfiles>

```

### <a name="sign-in"></a>Aanmelden

Bij de volgende aanmelding wordt het type patroon van de gebruiker op dezelfde manier berekend als bij het aanmelden met behulp van de [aangepaste HTML](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignIn.cshtml). Wanneer het type profiel zich in de Azure AD B2C claim Bag bevindt, roept Azure AD B2C uw API aan om het REST API-eind punt van TypingDNA aan te roepen. Het [controle gebruikers](https://api.typingdna.com/index.html#api-API_Services-GetUser) eindpunt wordt aangeroepen om te bevestigen dat de gebruiker bestaat. Controleer vervolgens [of](https://api.typingdna.com/index.html#api-API_Services-verifyTypingPattern) het eind punt eindpunt wordt aangeroepen om de te retour neren `net_score` . Hier `net_score` wordt aangegeven hoe dicht het type patroon het origineel was bij het aanmelden.

Dit type patroon is gemodelleerd met `validationTechnicalProfiles` binnen `SelfAsserted-LocalAccountSignin-Email-TDNA` :

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="login-NonInteractive"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="false"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-VerifyUser"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser">

        <Preconditions>

            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>saveTypingPattern</Value>

            <Value>False</Value>

            <Action>SkipThisValidationTechnicalProfile</Action>

            </Precondition>

        </Preconditions>

    </ValidationTechnicalProfile>

</ValidationTechnicalProfiles>

```

 Als de gebruiker een type patroon verkrijgt dat een hoog niveau heeft `net_score` , kunt u dit opslaan met behulp van het [type patroon TypingDNA opslaan](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) .  

Uw API moet een claim retour neren  `saveTypingPattern` Als u wilt dat het type patroon-eind punt TypingDNA opslaan wordt aangeroepen door Azure AD B2C (via uw API).

Het voor beeld in de opslag plaats bevat een API (TypingDNA-API-interface) die is geconfigureerd met de volgende eigenschappen.

- Trainings modus: als de gebruiker minder dan twee patronen heeft opgeslagen, vraagt u altijd om MFA.

- Als de gebruiker 2-5 patronen heeft opgeslagen en de `net_score` is lager dan 50, vraagt u om MFA.

- Als de gebruiker 5 + patronen heeft opgeslagen en de `net_score` is lager dan 65, vraagt u om MFA.

Deze drempel waarden moeten worden aangepast aan uw use-case.

- Nadat uw API de heeft geëvalueerd `net_score` , moet deze een Booleaanse claim retour neren naar B2C- `promptMFA` .

- De `promptMFA` claim wordt binnen een pre-voor waarde gebruikt om Azure AD MFA voorwaardelijk uit te voeren.

```xml

<OrchestrationStep Order="3" Type="ClaimsExchange">

    <Preconditions>

        <Precondition Type="ClaimsExist" ExecuteActionsIf="true">

            <Value>isActiveMFASession</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>promptMFA</Value>

            <Value>False</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

    </Preconditions>

    <ClaimsExchanges>

        <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" />

    </ClaimsExchanges>

</OrchestrationStep>

```

## <a name="onboard-with-typingdna"></a>Onboard met TypingDNA

1. Meld u [hier](https://www.typingdna.com/) aan voor TypingDNA
2. Meld u aan bij TypingDNA dash board en verkrijg de **API-sleutel** en het **API-geheim**. Dit is later vereist in de API-interface-instellingen

## <a name="integrate-typingdna-with-azure-ad-b2c"></a>TypingDNA integreren met Azure AD B2C

1. Host de [TypingDNA-API-interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) bij de door u gewenste hosting provider
2. Alle exemplaren van `apiKey` en `apiSecret` in [TypingDNA-API-interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) oplossing vervangen door de referenties van uw TypingDNA-dash board
3. Host de HTML-bestanden van uw provider van de keuze volgens de [vereisten voor](./customize-ui-with-html.md#3-configure-cors) CORS
4. Vervang de LoadURI-elementen voor de `api.selfasserted.tdnasignup` en de `api.selfasserted.tdnasignin` inhouds definities in het `TrustFrameworkExtensions.xml` bestand respectievelijk naar de URI van uw gehoste HTML-bestanden.
5. Maak een B2C-beleids sleutel onder Identity experience Framework op de Blade Azure AD in de **Azure Portal**. Gebruik de `Generate` optie en geef deze sleutel een naam `tdnaHashedId` .
6. De TenantId in de beleids bestanden vervangen
7. Vervang de ServiceURLs in alle TypingDNA REST API technische profielen (REST-TDNA-VerifyUser, REST-TDNA-SaveUser, REST-TDNA-CheckUser) door het eind punt voor uw [TypingDNA-API-interface-API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface).
8. Upload [beleids bestanden](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/policy) naar uw Tenant.

## <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Open de B2C-Tenant en kies identiteits ervaring Framework.
2. Selecteer de eerder gemaakte **gebruikers stroom**.
3. Gebruikers stroom **uitvoeren** selecteren

    a. **Toepassing** -Selecteer de geregistreerde app (voor beeld is JWT)

    b. **Antwoord-URL** : de omleidings-URL selecteren

    c. Selecteer **Gebruikersstroom uitvoeren**.
  
4. Ga door naar de registratie stroom en maak een account
5. Afmelden
6. De aanmeldings stroom door lopen
7. Het resulterende JWT-resultaat geeft de TypingDNA-resultaten weer

## <a name="live-version"></a>Live-versie

• MFA is uitgeschakeld in deze test versie, maar u kunt zien of er MFA is gevraagd door de claim `promptMFA` na verificatie.

• [Meld u hier](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SU_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login) aan en meld u [hier](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SI_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login) aan

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie:

- [Aangepast beleid in AAD B2C](./custom-policy-overview.md)

- [Aan de slag met aangepast beleid in AAD B2C](./custom-policy-get-started.md?tabs=applications)