---
title: Problemen oplossen met aangepaste beleids regels in Azure Active Directory B2C
description: Meer informatie over benaderingen voor het oplossen van fouten bij het werken met aangepaste beleids regels in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bbb3bc0e34ad596c39aebb49124bb72d0b3efe6f
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103906"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies"></a>Problemen met Azure AD B2C aangepast beleid oplossen

Als u Azure Active Directory B2C (Azure AD B2C) [aangepast beleid](custom-policy-overview.md)gebruikt, kunt u problemen ondervinden met de XML-indeling van de beleids taal of door runtime. In dit artikel worden enkele hulp middelen en tips beschreven die u kunnen helpen bij het detecteren en oplossen van problemen. 

In dit artikel vindt u informatie over het oplossen van problemen met de Azure AD B2C aangepaste beleids configuratie. Er wordt geen oplossing voor de Relying Party toepassing of de bijbehorende id-bibliotheek.

## <a name="azure-ad-b2c-correlation-id-overview"></a>Overzicht van Azure AD B2C correlatie-ID

Azure AD B2C correlatie-ID is een unieke id-waarde die is gekoppeld aan autorisatie aanvragen. Er worden alle Orchestration-stappen door gegeven die een gebruiker heeft doorgevoerd. Met de correlatie-ID kunt u het volgende doen:

- Identificeer aanmeldings activiteiten in uw toepassing en volg de prestaties van uw beleid.
- Zoek de Azure-toepassing Insights-logboeken van de aanmeldings aanvraag.
- Geef de correlatie-ID door aan uw REST API en gebruik deze om de aanmeldings stroom te identificeren. 

De correlatie-ID wordt gewijzigd telkens wanneer een nieuwe sessie tot stand is gebracht. Zorg ervoor dat u de bestaande browser tabbladen sluit bij het opsporen van fouten in uw beleid. Of open een nieuwe in-private modus browser.

### <a name="get-the-azure-ad-b2c-correlation-id"></a>De correlatie-ID van de Azure AD B2C ophalen

U kunt de correlatie-ID vinden op de Azure AD B2C registratie-of aanmeldings pagina. Selecteer **bron weer geven** in uw browser. De correlatie wordt weer gegeven als een opmerking boven aan de pagina.

![Scherm afbeelding van de bron voor de Azure AD B2C-aanmeldings pagina.](./media/troubleshoot-custom-policies/find-azure-ad-b2c-correlation-id.png)

Kopieer de correlatie-ID en blijf vervolgens door gaan met de aanmeldings stroom. Gebruik de correlatie-ID om het aanmeldings gedrag te observeren. Zie [problemen oplossen met Application Insights](#troubleshooting-with-application-insights)voor meer informatie.

### <a name="echo-the-azure-ad-b2c-correlation-id"></a>De correlatie-ID van de Azure AD B2C echo

U kunt de correlatie-ID in uw Azure AD B2C-tokens toevoegen. De correlatie-ID toevoegen:

1. Open het bestand extensies van uw beleid. Bijvoorbeeld <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Zoek het element [BuildingBlocks](buildingblocks.md) . Als het element niet bestaat, voegt u het toe.
1. Zoek het element [ClaimsSchema](claimsschema.md) . Als het element niet bestaat, voegt u het toe.
1. Voeg de stads claim toe aan het **ClaimsSchema** -element.  

    ```xml
    <!-- 
    <BuildingBlocks>
      <ClaimsSchema> -->
        <ClaimType Id="correlationId">
          <DisplayName>correlation ID</DisplayName>
          <DataType>string</DataType>
        </ClaimType>
      <!-- 
      </ClaimsSchema>
    </BuildingBlocks>-->
    ```

1. Open het Relying Party-bestand van uw beleid. Bijvoorbeeld <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> bestand. De uitvoer claim wordt toegevoegd aan het token na een succes volle gebruikers reis en verzonden naar de toepassing. Wijzig het technische profiel element in het gedeelte Relying Party om de plaats toe te voegen als een uitvoer claim.
 
    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          ...
          <OutputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```


## <a name="troubleshooting-with-application-insights"></a>Problemen oplossen met Application Insights

Gebruik [Application Insights](troubleshoot-with-application-insights.md)om problemen met uw aangepaste beleids regels op te sporen. Application Insights traceert de activiteit van de gebruikers traject van uw aangepaste beleid. Het biedt een manier om uitzonde ringen te diagnosticeren en de uitwisseling van claims tussen Azure AD B2C en de verschillende claim providers te observeren die worden gedefinieerd door technische profielen, zoals id-providers, API-gebaseerde services, de Azure AD B2C gebruikers lijst en andere services.  

U wordt aangeraden de [Azure AD B2C-uitbrei ding](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) voor [VS code](https://code.visualstudio.com/)te installeren. Met de extensie Azure AD B2C worden de logboeken voor u geordend op basis van de beleids naam, correlatie-ID (Application Insights het eerste cijfer van de correlatie-ID) en de logboek tijds tempel. Met deze functie kunt u het relevante logboek vinden op basis van de lokale tijds tempel en zien hoe de gebruikers ritten worden uitgevoerd door Azure AD B2C. 

> [!NOTE]
> - Er is een korte vertraging, meestal minder dan vijf minuten, voordat u nieuwe logboeken in Application Insights kunt zien.
> - De community heeft de Visual Studio code-extensie voor Azure AD B2C ontwikkeld om identiteits ontwikkelaars te helpen. De extensie wordt niet ondersteund door micro soft en wordt alleen ter beschikking gesteld.

Eén aanmeldings stroom kan meer dan één Azure-toepassing Insights-tracering oplossen. In de volgende scherm afbeelding heeft het *B2C_1A_signup_signin* -beleid drie logboeken. Elk logboek vertegenwoordigt een deel van de aanmeldings stroom.

In de volgende scherm afbeelding ziet u de Azure AD B2C-extensie voor VS code met Azure-toepassing Insights Trace Explorer.

![Scherm afbeelding van Azure AD B2C extensie voor VS-code met Azure-toepassing Insights-tracering.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-trace.png)

### <a name="filter-the-trace-log"></a>Het traceer logboek filteren

Met de focus op de Azure AD B2C Trace Explorer, begint u met het typen van het eerste cijfer van de correlatie-ID of een tijd die u wilt zoeken. In de rechter bovenhoek van de Azure AD B2C Trace Explorer ziet u een filter vak waarin wordt weer gegeven wat u tot nu toe hebt getypt en de overeenkomende traceer logboeken worden gemarkeerd.  

![Scherm afbeelding van de uitbrei ding van de Azure AD B2C extensie Azure AD B2C het markeren van het tracerings Verkenner.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-highlight.png)

Als u de muis aanwijzer boven het filter vakje houdt en **filter inschakelen op type** selecteert, worden alleen de overeenkomende traceer logboeken weer gegeven. Gebruik de **knop ' X ' wissen** om het filter te wissen.

![Scherm opname van Azure AD B2C extensie Azure AD B2C traceer Verkenner-filter.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-filter.png)

### <a name="application-insights-trace-log-details"></a>Details van traceer logboek Application Insights

Wanneer u een Azure-toepassing Insights-traceer selecteert, wordt in de uitbrei ding het venster **Application Insights Details** geopend met de volgende informatie:

- **Application Insights** -algemene informatie over het tracerings logboek, met inbegrip van de beleids naam, correlatie-id, Azure-toepassing Insights-traceer-id en traceer tijds tempel.
- **Technische profielen** : een lijst met technische profielen die worden weer gegeven in het tracerings logboek.
- **Claims** -alfabetische lijst met claims die worden weer gegeven in het tracerings logboek en hun waarden. Als een claim meerdere keren wordt weer gegeven in het tracerings logboek met verschillende waarden, `=>` wijst een teken de nieuwste waarde aan. U kunt deze claims bekijken om te bepalen of verwachte claim waarden correct zijn ingesteld. Als u bijvoorbeeld een voor waarde hebt waarmee een claim waarde wordt gecontroleerd, kan de sectie claims u helpen om te bepalen waarom een verwachte stroom zich anders gedraagt.
- **Trans formatie van claims** : lijst met claim transformaties die worden weer gegeven in het tracerings logboek. Elke claim transformatie bevat de invoer claims, invoer parameters en uitvoer claims. De sectie claim transformatie geeft inzicht in de gegevens die zijn verzonden in en het resultaat van de trans formatie van claims.
- **Tokens** -lijst met tokens die worden weer gegeven in het tracerings logboek. De tokens bevatten de onderliggende tokens van de federatieve OAuth-en OpenID Connect Connect-ID-provider. Het token van de federatieve id-provider bevat details over de manier waarop de ID-provider de claims retourneert naar Azure AD B2C zodat u de claim voor het technische profiel uitvoer van de identiteits provider kunt toewijzen. 
- **Uitzonde ringen** : lijst met uitzonde ringen of fatale fouten die worden weer gegeven in het tracerings logboek.
- **Application INSIGHTS JSON** : de onbewerkte gegevens worden geretourneerd uit de Application Insights.

De volgende scherm afbeelding toont een voor beeld van het venster Details van Application Insights traceer logboek.  

![Scherm opname van Azure AD B2C extensie Azure AD B2C traceer rapport.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-report.png)

## <a name="troubleshoot-jwt-tokens"></a>Problemen met JWT-tokens oplossen

Voor validatie-en fout opsporing via JWT-tokens kunt u JWTs decoderen met behulp van een site zoals [https://jwt.ms](https://jwt.ms) . Een test toepassing maken die kan worden omgeleid naar `https://jwt.ms` voor token inspectie. Als u dit nog niet hebt gedaan, [registreert u een webtoepassing](tutorial-register-applications.md)en schakelt u de [impliciete toekenning van id-tokens in](tutorial-register-applications.md#enable-id-token-implicit-grant). 

![Scherm opname van JWT-token preview.](./media/troubleshoot-custom-policies/jwt-token-preview.png)

Gebruik **nu uitvoeren** en `https://jwt.ms` om uw beleids regels onafhankelijk van uw web-of mobiele toepassing te testen. Deze website fungeert als een Relying Party-toepassing. De inhoud van het JSON-webtoken (JWT) dat wordt gegenereerd door uw Azure AD B2C-beleid wordt weer gegeven.

## <a name="troubleshoot-saml-protocol"></a>Problemen met SAML-protocol oplossen

Als u de integratie met uw service provider wilt configureren en fouten wilt opsporen, kunt u een browser extensie voor het SAML-protocol gebruiken, bijvoorbeeld [SAML devtools extension](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) for Chrome, [SAML-tracering](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) voor Firefox of [Edge of IE-ontwikkel hulpprogramma's](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

In de volgende scherm afbeelding ziet u hoe de SAML-aanvraag wordt weer gegeven Azure AD B2C worden verzonden naar de ID-provider en het SAML-antwoord.

![Scherm opname van het logboek voor SAML-protocol tracering.](./media/troubleshoot-custom-policies/saml-protocol-trace.png)

Met deze hulpprogram ma's kunt u de integratie van uw toepassing en Azure AD B2C controleren. Bijvoorbeeld:

- Controleer of de SAML-aanvraag een hand tekening bevat en bepaal welk algoritme wordt gebruikt om de autorisatie aanvraag te ondertekenen.
- Controleer of Azure AD B2C een fout bericht retourneert.
- Controleer of de bevestigings sectie is versleuteld.
- De naam van de claims ophalen retourneert de ID-provider.

U kunt ook de uitwisseling van berichten tussen uw client browser en Azure AD B2C traceren, met [Fiddler](https://www.telerik.com/fiddler). Het kan u helpen een indicatie te krijgen van waar uw gebruikers zich niet meer in de Orchestration-stappen bevinden.

## <a name="troubleshoot-policy-validity"></a>Beleids geldigheid oplossen

Nadat u klaar bent met het ontwikkelen van uw beleid, uploadt u het beleid naar Azure AD B2C. Er zijn mogelijk problemen met uw beleid. Gebruik de volgende methoden om de integriteit/geldigheid van uw beleid te garanderen.

De meest voorkomende fout bij het instellen van aangepast beleid is XML met een onjuiste indeling. Een goede XML-editor is bijna essentieel. Er wordt een XML-bestand met inhoud, kleur codes, vooraf gevulde termen en XML-elementen geïndexeerd en kan worden gevalideerd tegen een XML-schema.

We raden u aan [Visual Studio code](https://code.visualstudio.com/)te gebruiken. Installeer vervolgens een XML-extensie, zoals [XML-taal ondersteuning door Red Hat](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-xml). Met de XML-extensie kunt u het XML-schema valideren voordat u uw XML-bestand uploadt met behulp van een aangepaste [xsd](https://raw.githubusercontent.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/master/TrustFrameworkPolicy_0.3.0.0.xsd) -schema definitie.

U kunt de XML File Association-strategie gebruiken om het XML-bestand te binden aan de XSD door de volgende instellingen toe te voegen aan uw VS-code `settings.json` bestand. Hiervoor doet u het volgende:

1. Van VS code, klikt u op de **instellingen**. Zie [gebruikers-en werk ruimte-instellingen](https://code.visualstudio.com/docs/getstarted/settings)voor meer informatie.
1. Zoek naar **fileAssociations** en selecteer vervolgens de **XML** onder de **extensie**.
1. Selecteer **bewerken in settings.jsop**.

    ![Scherm opname van XML-schema validatie van VS-code.](./media/troubleshoot-custom-policies/xml-validation.png)
1. Voeg in het settings.jsop de volgende JSON-code toe:

    ```json
    "xml.fileAssociations": [
      {
        "pattern": "**.xml",
        "systemId": "https://raw.githubusercontent.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/master/TrustFrameworkPolicy_0.3.0.0.xsd"
      }
    ]
    ```

In het volgende voor beeld ziet u een XML-validatie fout. Wanneer u de muis aanwijzer over de naam van het element beweegt, wordt in de uitbrei ding de verwachte elementen weer geven.

![Scherm opname van de XML-schema validatie fout indicator versus code.](./media/troubleshoot-custom-policies/xml-validation-error.png)

In het volgende geval is het `DisplayName` element juist. Maar in de verkeerde volg orde. De  `DisplayName` moet voor het `Protocol` element liggen. U kunt het probleem oplossen door de muis aanwijzer over het `DisplayName` element te verplaatsen naar de juiste volg orde van de elementen.

![Scherm opname van fout met versus interxml-schema validatie volgorde van VS code.](./media/troubleshoot-custom-policies/xml-validation-order-error.png)

## <a name="upload-policies-and-policy-validation"></a>Beleids regels en beleids validatie uploaden

Validatie van het XML-beleids bestand wordt automatisch uitgevoerd bij het uploaden. De meeste fouten zorgen ervoor dat het uploaden mislukt. Validatie bevat het beleids bestand dat u uploadt. Het bevat ook de keten van bestanden waarnaar het upload bestand verwijst (het Relying Party-beleids bestand, het extensie bestand en het basis bestand).

> [!TIP]
> Azure AD B2C wordt extra validatie uitgevoerd voor Relying Party-beleid. Wanneer u een probleem met uw beleid ondervindt, zelfs als u alleen het uitbreidings beleid bewerkt, is het een goed idee om ook het Relying Party beleid te uploaden. 

Deze sectie bevat de algemene validatie fouten en mogelijke oplossingen.

### <a name="schema-validation-error-found-has-invalid-child-element-name"></a>Schema validatie fout gevonden... heeft ongeldig onderliggend element {name}

Het beleid bevat een ongeldig XML-element, of het XML-element is geldig, maar lijkt niet op de juiste volg orde te staan. Raadpleeg de sectie [geldigheid van beleid voor het oplossen van problemen](#troubleshoot-policy-validity) om dit type fout op te lossen.

### <a name="there-is-a-duplicate-key-sequence-number"></a>Er is een dubbele sleutel volgorde {Number} 

Een gebruikers [traject](userjourneys.md) of een [subtraject](subjourneys.md) bestaat uit een geordende lijst met Orchestration-stappen die in volg orde worden uitgevoerd. Nadat u uw traject hebt gewijzigd, moet u de stappen sequentieel opnieuw nummeren zonder een geheel getal tussen 1 en N over te slaan.

> [!TIP]
> U kunt de opdracht [uitbrei ding voor Azure AD B2C](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) de [VS-code](https://code.visualstudio.com/) gebruiken `(Shift+Ctrl+r)` om alle gebruikers namen en subtrajecten te hernummeren in uw beleid.

### <a name="was-expected-to-have-step-with-order-number-but-it-was-not-found"></a>... verwacht werd de stap met de volg orde {Number} te hebben, maar deze is niet gevonden...

Controleer de vorige fout.

### <a name="orchestration-step-order-number-in-user-journey-name-is-followed-by-a-claims-provider-selection-step-and-must-be-a-claims-exchange-but-it-is-of-type"></a>De indeling {Number} van de Orchestration-stap in de gebruikers traject {name}... gevolgd door de selectie stap claim provider en moet een claim uitwisseling zijn, maar is van het type...

De indelings stappen type van `ClaimsProviderSelection` en `CombinedSignInAndSignUp` bevatten een lijst met opties waaruit een gebruiker kan kiezen. Het moet worden gevolgd door `ClaimsExchange` een of meer claims uitwisseling.

De volgende Orchestration-stappen veroorzaken dit type of deze fout. De tweede Orchestration-stap moet van het type zijn `ClaimsExchange` , niet `ClaimsProviderSelection` .

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
        <ClaimsProviderSelections>
          <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange"/>
          <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange"/>
        </ClaimsProviderSelections>
        <ClaimsExchanges>
          <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email"/>
        </ClaimsExchanges>
      </OrchestrationStep> 
      
      <OrchestrationStep Order="2" Type="ClaimsProviderSelection">
        ...
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="step-number-with-2-claims-exchanges-it-must-be-preceded-by-a-claims-provider-selection-in-order-to-determine-which-claims-exchange-can-be-used"></a>... stap {Number} met 2 claim uitwisselingen. Deze moet worden voorafgegaan door een claim provider selectie om te bepalen welke claim uitwisseling kan worden gebruikt

Een indelings stap type van `ClaimsExchange` moet een enkele hebben `ClaimsExchange` , tenzij de vorige stap type of is `ClaimsProviderSelection` `CombinedSignInAndSignUp` . Dit type fout wordt veroorzaakt door de volgende indelings stappen. De zesde stap bevat twee claim uitwisseling. 

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="5" Type="ClaimsExchange">
        ...
        <ClaimsExchanges>
          <ClaimsExchange Id="SelfAsserted-Social" TechnicalProfileReferenceId="SelfAsserted-Social"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="6" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-First-API" TechnicalProfileReferenceId="Call-REST-First-API"/>
          <ClaimsExchange Id="Call-REST-Second-API" TechnicalProfileReferenceId="Call-REST-Second-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

Om dit type fout op te lossen, moet u twee Orchestration-stappen gebruiken. Elke Orchestration-stap met één claim uitwisseling.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="5" Type="ClaimsExchange">
        ...
        <ClaimsExchanges>
          <ClaimsExchange Id="SelfAsserted-Social" TechnicalProfileReferenceId="SelfAsserted-Social"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="6" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-First-API" TechnicalProfileReferenceId="Call-REST-First-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-Second-API" TechnicalProfileReferenceId="Call-REST-Second-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="there-is-a-duplicate-key-sequence-name"></a>Er is een dubbele sleutel volgorde {name}

Een traject heeft meerdere `ClaimsExchange` met hetzelfde `Id` . De volgende stappen veroorzaken dit type fout. De ID *AADUserWrite* wordt twee keer weer gegeven in de gebruikers traject.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="AAD-UserWriteUsingAlternativeSecurityId"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="8" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="Call-REST-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

Om dit type fout op te lossen, wijzigt u de achtste Orchestration-stappen ' claim uitwisseling van een unieke naam, zoals *call-rest-API*.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="AAD-UserWriteUsingAlternativeSecurityId"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="8" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-API" TechnicalProfileReferenceId="Call-REST-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="makes-a-reference-to-claimtype-with-id-claim-name-but-neither-the-policy-nor-any-of-its-base-policies-contain-such-an-element"></a>... Hiermee wordt een verwijzing naar claim type met de id {claim naam}, maar het beleid en het basis beleid ervan bevatten een dergelijk element niet

Dit type fout treedt op wanneer uw beleid een verwijzing maakt naar een claim die niet is gedeclareerd in het [claims-schema](claimsschema.md). Claims moeten worden gedefinieerd in ten minste één van de bestanden in het beleid. 

Een voor beeld: een technisch profiel met de uitvoer claim *schoolId* . Maar de uitvoer claim *schoolId* wordt nooit gedeclareerd in het beleid of in een voor gaande beleids regel.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="schoolId" />
  ...
</OutputClaims>
```

Om dit type fout op te lossen, controleert u of de `ClaimTypeReferenceId` waarde verkeerd is gespeld of niet bestaat in het schema. Als de claim is gedefinieerd in het uitbrei ding beleid, maar deze ook wordt gebruikt in het basis beleid. Zorg ervoor dat de claim is gedefinieerd in het beleid dat wordt gebruikt of in een beleid op het hoogste niveau.

Bij het toevoegen van de claim aan het claim schema is dit type fout opgelost.

```xml
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="schoolId">
      <DisplayName>School name</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Enter your school name</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks> -->
```

### <a name="makes-a-reference-to-a-claimstransformation-with-id"></a>... Hiermee wordt een verwijzing naar een ClaimsTransformation met ID...

De oorzaak van deze fout is vergelijkbaar met die voor de claim fout. Controleer de vorige fout.

### <a name="user-is-currently-logged-as-a-user-of-yourtenantonmicrosoftcom-tenant"></a>De gebruiker is momenteel aangemeld als een gebruiker van de Tenant yourtenant.onmicrosoft.com...

U meldt zich aan met een account van een Tenant die afwijkt van het beleid dat u wilt uploaden. U meldt zich bijvoorbeeld aan met admin@contoso.onmicrosoft.com , terwijl uw beleid `TenantId` is ingesteld op `fabrikam.onmicrosoft.com` .

```xml
<TrustFrameworkPolicy ...
  TenantId="fabrikam.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://fabrikam.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>fabrikam.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

- Controleer of de `TenantId` waarde in de `<TrustFrameworkPolicy\>` `<BasePolicy\>` elementen en overeenkomt met uw doel Azure AD B2C Tenant.

### <a name="claim-type-name-is-the-output-claim-of-the-relying-partys-technical-profile-but-it-is-not-an-output-claim-in-any-of-the-steps-of-user-journey"></a>Claim type {name} is de uitvoer claim van het technische profiel van de Relying Party, maar is geen uitvoer claim in een van de stappen van de gebruikers traject...

In een Relying Party beleid hebt u een uitvoer claim toegevoegd, maar de uitvoer claim is geen uitvoer claim in een van de stappen van de gebruikers traject. Azure AD B2C kan de claim waarde niet lezen uit de verzameling claims.

In het volgende voor beeld is de claim *schoolId* een uitvoer claim van het technische profiel van de Relying Party, maar het is geen uitvoer claim in een van de stappen van *SignUpOrSignIn* -gebruikers traject.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="schoolId" />
      ...
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
``` 

Om dit type fout op te lossen, moet u ervoor zorgen dat de uitvoer claims worden weer gegeven in ten minste één indelings claim voor technische profielen voor het uitvoeren van een verzameling. Als uw gebruikers reis de claim niet kan uitvoeren, stelt u in het technische profiel van Relying Party een standaard waarde in, zoals een lege teken reeks.  

```xml
<OutputClaim ClaimTypeReferenceId="schoolId" DefaultValue="" />
```

### <a name="input-string-was-not-in-a-correct-format"></a>De invoer teken reeks heeft niet de juiste indeling

U hebt het verkeerde waardetype ingesteld op een claim van een ander type. U kunt bijvoorbeeld een integer-claim definiëren.

```xml
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="age">
      <DisplayName>Age</DisplayName>
      <DataType>int</DataType>
    </ClaimType>
  <!--
  </ClaimsSchema>
</BuildingBlocks> -->
```

Vervolgens probeert u een teken reeks waarde in te stellen:

```xml
<OutputClaim ClaimTypeReferenceId="age" DefaultValue="ABCD" />
```

Om dit type fout op te lossen, moet u ervoor zorgen dat u de juiste waarde instelt, zoals `DefaultValue="0"` .


### <a name="tenant-name-already-has-a-policy-with-id-name-another-policy-with-the-same-id-cannot-be-stored"></a>De Tenant {name} heeft al een beleid met de id {name}. Er kan geen ander beleid met dezelfde id worden opgeslagen

U probeert een beleid naar uw Tenant te uploaden, maar er is al een beleid met dezelfde naam geüpload naar uw Tenant. 

Als u dit type fout wilt oplossen, schakelt u het selectie vakje **het aangepaste beleid overschrijven als dit al bestaat** in wanneer u het beleid uploadt.

![Scherm afbeelding waarin wordt getoond hoe het aangepaste beleid moet worden overschreven als dit al bestaat.](./media/troubleshoot-custom-policies/overwrite-custom-policy-if-exists.png)


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [verzamelen van Azure Active Directory B2C-logboeken met Application Insights](troubleshoot-with-application-insights.md).

