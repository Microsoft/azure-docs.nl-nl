---
title: Gebruikers gedrag bijhouden met Application Insights
titleSuffix: Azure AD B2C
description: Meer informatie over het inschakelen van gebeurtenis Logboeken in Application Insights van Azure AD B2C gebruikers reizen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 01/29/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ce80e3376482ef44b466757cf7e345c4bcf186ad
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218550"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Gebruikers gedrag bijhouden in Azure Active Directory B2C met behulp van Application Insights

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure Active Directory B2C (Azure AD B2C) ondersteunt het rechtstreeks verzenden van gebeurtenis gegevens naar [Application Insights](../azure-monitor/app/app-insights-overview.md) door gebruik te maken van de instrumentatie sleutel die aan Azure AD B2C is gegeven. Met een Application Insights technisch profiel kunt u gedetailleerde en aangepaste gebeurtenis logboeken ophalen voor uw gebruikers trajecten:

* Krijg inzichten op het gedrag van gebruikers.
* Los uw eigen beleid in ontwikkeling of productie op.
* Prestaties meten.
* Meldingen van Application Insights maken.

## <a name="overview"></a>Overzicht

Als u aangepaste gebeurtenis logboeken wilt inschakelen, voegt u een Application Insights technisch profiel toe. In het technische profiel definieert u de Application Insights instrumentatie sleutel, gebeurtenis naam en de claims die u wilt opnemen. Als u een evenement wilt plaatsen, wordt het technische profiel vervolgens toegevoegd als een indelings stap in een [gebruikers traject](userjourneys.md).

Houd rekening met het volgende wanneer u de Application Insights gebruikt:

- Er is een korte vertraging, meestal minder dan vijf minuten, vóór nieuwe logboeken die beschikbaar zijn in Application Insights.
- Met Azure AD B2C kunt u de claims kiezen die moeten worden vastgelegd. Neem geen claims met persoons gegevens op.
- Voor het vastleggen van een gebruikers sessie kunnen gebeurtenissen worden gecombineerd met behulp van een correlatie-ID. 
- Bel het technische profiel van Application Insights rechtstreeks vanuit een [gebruikers traject](userjourneys.md) of een [subtraject](subjourneys.md). Gebruik Application Insights technisch profiel niet als een [technisch profiel voor validatie](validation-technical-profile.md).

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-application-insights-resource"></a>Een Application Insights-resource maken

Wanneer u Application Insights met Azure AD B2C gebruikt, hoeft u alleen maar een resource te maken en de instrumentatie sleutel op te halen. Zie [een Application Insights resource maken](../azure-monitor/app/create-new-resource.md) voor meer informatie

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map met uw Azure-abonnement gebruikt door het filter **Directory + abonnement** te selecteren in het bovenste menu en de map te kiezen die uw abonnement bevat. Deze Tenant is niet uw Azure AD B2C-Tenant.
3. Kies **een resource maken** in de linkerbovenhoek van de Azure Portal en zoek en selecteer **Application Insights**.
4. Klik op **Create**.
5. Voer een **naam** in voor de resource.
6. Selecteer voor **toepassings Type** **ASP.NET-webtoepassing**.
7. Voor **resource groep** selecteert u een bestaande groep of voert u een naam in voor een nieuwe groep.
8. Klik op **Create**.
4. Nadat u de Application Insights resource hebt gemaakt, opent u deze, vouwt u de **essentiële** elementen uit en kopieert u de instrumentatie sleutel.

![Application Insights overzicht en instrumentatie sleutel](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Claims definiëren

Een claim biedt een tijdelijke opslag van gegevens tijdens het uitvoeren van een Azure AD B2C beleid. Het [claim schema](claimsschema.md) is de plaats waar u uw claims declareert.

1. Open het bestand extensies van uw beleid. Bijvoorbeeld <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Zoek het element [BuildingBlocks](buildingblocks.md) . Als het element niet bestaat, voegt u het toe.
1. Zoek het element [ClaimsSchema](claimsschema.md) . Als het element niet bestaat, voegt u het toe.
1. Voeg de volgende claims toe aan het **ClaimsSchema** -element. 

```xml
<ClaimType Id="EventType">
  <DisplayName>Event type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="EventTimestamp">
  <DisplayName>Event timestamp</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="PolicyId">
  <DisplayName>Policy Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="Culture">
  <DisplayName>Culture ID</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="CorrelationId">
  <DisplayName>Correlation Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="federatedUser">
  <DisplayName>Federated user</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
<ClaimType Id="parsedDomain">
  <DisplayName>Domain name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>The domain portion of the email address.</UserHelpText>
</ClaimType>
<ClaimType Id="userInLocalDirectory">
  <DisplayName>userInLocalDirectory</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
```

## <a name="add-new-technical-profiles"></a>Nieuwe technische profielen toevoegen

Technische profielen kunnen worden beschouwd als functies in het aangepaste beleid. In deze tabel worden de technische profielen gedefinieerd die worden gebruikt voor het openen van een sessie en het plaatsen van gebeurtenissen. De oplossing maakt gebruik van de aanpak voor het [opnemen van technische profielen](technicalprofiles.md#include-technical-profile) . Wanneer een technisch profiel een ander technisch profiel bevat om instellingen te wijzigen of nieuwe functionaliteit toe te voegen.

| Technisch profiel | Taak |
| ----------------- | -----|
| AppInsights-Common | Het algemene technische profiel met de gemeen schappelijke set configuratie. Inclusief, de Application Insights instrumentatie sleutel, verzameling van te registreren claims en de ontwikkelaars modus. De volgende technische profielen bevatten het algemene technische profiel en voegen meer claims toe, zoals de naam van de gebeurtenis. |
| AppInsights-SignInRequest | Registreert een `SignInRequest` gebeurtenis met een set claims wanneer een aanmeldings aanvraag is ontvangen. |
| AppInsights-UserSignUp | Registreert een `UserSignUp` gebeurtenis wanneer de gebruiker de registratie optie activeert in een traject voor registreren/aanmelden. |
| AppInsights-SignInComplete | Registreert een `SignInComplete` gebeurtenis wanneer een verificatie is voltooid, wanneer er een token naar de Relying Party-toepassing is verzonden. |

Voeg de profielen toe aan het *TrustFrameworkExtensions.xml* -bestand van het Starter Pack. Deze elementen toevoegen aan het **ClaimsProviders** -element:

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="EventTimestamp" PartnerClaimType="{property:EventTimestamp}" DefaultValue="{Context:DateTimeInUtc}" />
        <InputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="{property:TenantId}" DefaultValue="{Policy:TrustFrameworkTenantId}" />
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-UserSignUp">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignUp" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
    
    <TechnicalProfile Id="AppInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
        <InputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="{property:IDP}" DefaultValue="Local" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Wijzig de instrumentatie sleutel in het `AppInsights-Common` technische profiel in de GUID die uw Application Insights resource levert.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Voeg de technische profielen toe als Orchestration-stappen

Aanroep `AppInsights-SignInRequest` als Orchestration-stap 2 om bij te houden dat er een aanvraag voor aanmelden/registreren is ontvangen:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Voeg onmiddellijk *vóór* de `SendClaims` Orchestration-stap een nieuwe stap toe die aanroept `AppInsights-UserSignup` . Het wordt geactiveerd wanneer de gebruiker de registratie knop selecteert in een traject voor registreren/aanmelden.

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
      <Value>newUser</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>newUser</Value>
      <Value>false</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AppInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Bel direct na de `SendClaims` Orchestration-stap `AppInsights-SignInComplete` . In deze stap ziet u dat de reis is voltooid.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Nadat u de nieuwe indelings stappen hebt toegevoegd, moet u de stappen sequentieel opnieuw nummeren zonder een geheel getal tussen 1 en N over te slaan.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Upload uw bestand, voer het beleid uit en Bekijk gebeurtenissen

Sla het *TrustFrameworkExtensions.xml* bestand op en upload het. Roep vervolgens het Relying Party-beleid aan vanuit uw toepassing of gebruik **nu uitvoeren** in de Azure Portal. Wacht een minuut of dus en uw gebeurtenissen zijn beschikbaar in Application Insights.

1. Open de **Application Insights** -resource in uw Azure Active Directory-Tenant.
2. Selecteer **gebruik** en selecteer vervolgens **gebeurtenissen**.
3. Stel **in** op **vorig uur** en **met** **3 minuten**.  Mogelijk moet u **vernieuwen** selecteren om de resultaten weer te geven.

![Application Insights USAGE-Events blase](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="collect-more-data"></a>Meer gegevens verzamelen

Als u uw bedrijfs behoeften wilt afstemmen, kunt u meer claims registreren. Als u een claim wilt toevoegen, moet u eerst [een claim definiëren](#define-claims)en vervolgens de claim toevoegen aan de claim verzameling voor invoer. Claims die u toevoegt aan het *AppInsights-gemeen schappelijke* technische profiel, worden weer gegeven in alle gebeurtenissen. Claims die u toevoegt aan een specifiek technisch profiel, worden alleen in dat geval weer gegeven. Het invoer claim element bevat de volgende kenmerken:

- **ClaimTypeReferenceId** : is de verwijzing naar een claim type. 
- **PartnerClaimType** : dit is de naam van de eigenschap die wordt weer gegeven in azure Insights. Gebruik de syntaxis van `{property:NAME}` , waarbij de `NAME` eigenschap wordt toegevoegd aan de gebeurtenis.
- **DefaultValue** : een vooraf gedefinieerde waarde die moet worden vastgelegd, zoals de naam van de gebeurtenis. Een claim die wordt gebruikt in de gebruikers traject, zoals de naam van de ID-provider. Als de claim leeg is, wordt de standaard waarde gebruikt. De `identityProvider` claim wordt bijvoorbeeld ingesteld door de technische profielen voor Federatie, zoals Facebook. Als de claim leeg is, wordt hiermee aangegeven dat de gebruiker zich aanmeldt met een lokaal account. De standaard waarde is dus ingesteld op *lokaal*. U kunt ook een [claim resolver](claim-resolver-overview.md) opnemen met een contextuele waarde, zoals de toepassings-id of het IP-adres van de gebruiker.

### <a name="manipulating-claims"></a>Claims bewerken

U kunt [invoer claim transformaties](custom-policy-trust-frameworks.md#manipulating-your-claims) gebruiken om de invoer claims te wijzigen of nieuwe te genereren voordat u naar Application Insights verzendt. In het volgende voor beeld bevat het technische profiel de *CheckIsAdmin* -invoer claims trans formatie. 

```xml
<TechnicalProfile Id="AppInsights-SignInComplete">
  <InputClaimsTransformations>  
    <InputClaimsTransformation ReferenceId="CheckIsAdmin" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isAdmin" PartnerClaimType="{property:IsAdmin}"  />
    ...
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

### <a name="add-events"></a>Gebeurtenissen toevoegen

Als u een gebeurtenis wilt toevoegen, maakt u een nieuw technisch profiel dat het *AppInsights-algemene* technische profiel bevat. Voeg vervolgens het technische profiel als Orchestration-stap toe aan de [gebruikers reis](custom-policy-trust-frameworks.md#orchestration-steps). Gebruik de voor [waarde](userjourneys.md#preconditions) om de gebeurtenis indien gewenst te activeren. Rapport de gebeurtenis bijvoorbeeld alleen wanneer gebruikers door MFA worden uitgevoerd.

```xml
<TechnicalProfile Id="AppInsights-MFA-Completed">
  <InputClaims>
     <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="MFA-Completed" />
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

Nu u een technisch profiel hebt, voegt u de gebeurtenis toe aan de reis van de gebruiker. Vervolgens worden de stappen opeenvolgend opnieuw genummerd zonder gehele getallen van 1 tot en met N over te slaan.

```xml
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
    <Value>isActiveMFASession</Value>
    <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserMfaCompleted" TechnicalProfileReferenceId="AppInsights-MFA-Completed" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="enable-developer-mode"></a>Ontwikkelaars modus inschakelen

Wanneer u de Application Insights gebruikt om gebeurtenissen te definiëren, kunt u aangeven of de ontwikkelaars modus is ingeschakeld. De ontwikkelaars modus bepaalt hoe gebeurtenissen worden gebufferd. In een ontwikkel omgeving met mini maal gebeurtenis volume is het inschakelen van de ontwikkelaars modus tot gevolg dat gebeurtenissen direct naar Application Insights worden verzonden. De standaardwaarde is `false`. Schakel de ontwikkelaars modus niet in in productie omgevingen.

Als u de ontwikkelaars modus wilt inschakelen, wijzigt u in het *AppInsights-algemene* technische profiel de `DeveloperMode` meta gegevens in `true` : 

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DeveloperMode">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="disable-telemetry"></a>Telemetrie uitschakelen

Als u de inzicht logboeken voor toepassingen wilt uitschakelen, wijzigt u in het *AppInsights-algemene* technische profiel de `DisableTelemetry` meta gegevens in `true` : 

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DisableTelemetry">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [maken van aangepaste KPI-Dash boards met behulp van Azure-toepassing Insights](../azure-monitor/learn/tutorial-app-dashboards.md). 

::: zone-end