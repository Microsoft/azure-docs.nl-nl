---
title: Gebruikers gedrag bijhouden met behulp van Application Insights
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
ms.openlocfilehash: 2cde44ddb49ede8002b8a25ab47ae92ccd602a9d
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226367"
---
# <a name="track-user-behavior-in-azure-ad-b2c-by-using-application-insights"></a>Gebruikers gedrag bijhouden in Azure AD B2C met behulp van Application Insights

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

In Azure Active Directory B2C (Azure AD B2C) kunt u gebeurtenis gegevens rechtstreeks naar [Application Insights](../azure-monitor/app/app-insights-overview.md) verzenden met behulp van de instrumentatie sleutel die aan Azure AD B2C is gegeven. Met een Application Insights technisch profiel kunt u gedetailleerde en aangepaste gebeurtenis logboeken ophalen voor uw gebruikers trajecten:

- Krijg inzichten op het gedrag van gebruikers.
- Los uw eigen beleid in ontwikkeling of productie op.
- Prestaties meten.
- Meldingen van Application Insights maken.

## <a name="overview"></a>Overzicht

Als u aangepaste gebeurtenis logboeken wilt inschakelen, voegt u een Application Insights technisch profiel toe. In het technische profiel definieert u de Application Insights instrumentatie sleutel, de naam van de gebeurtenis en de claims die u wilt opnemen. Als u een gebeurtenis wilt plaatsen, voegt u het technische profiel toe als een indelings stap in een [gebruikers traject](userjourneys.md).

Wanneer u Application Insights gebruikt, moet u rekening houden met het volgende:

- Er is een korte vertraging, meestal minder dan vijf minuten voordat er nieuwe logboeken beschikbaar zijn in Application Insights.
- Met Azure AD B2C kunt u kiezen welke claims u wilt opnemen. Neem geen claims met persoons gegevens op.
- Als u een gebruikers sessie wilt vastleggen, kunt u een correlatie-ID gebruiken om gebeurtenissen te verenigen.
- Bel het technische profiel van Application Insights rechtstreeks vanuit een [gebruikers traject](userjourneys.md) of een [subtraject](subjourneys.md). Gebruik niet een Application Insights technisch profiel als een [technisch profiel voor validatie](validation-technical-profile.md).

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-application-insights-resource"></a>Een Application Insights-resource maken

Wanneer u Application Insights met Azure AD B2C gebruikt, hoeft u alleen maar een resource te maken en de instrumentatie sleutel op te halen. Zie [een Application Insights resource maken](../azure-monitor/app/create-new-resource.md)voor meer informatie.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map met uw Azure-abonnement gebruikt. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies de map die uw Azure-abonnement bevat. Deze Tenant is niet uw Azure AD B2C-Tenant.
1. Kies **een resource maken** in de linkerbovenhoek van de Azure Portal, en zoek en selecteer **Application Insights**.
1. Selecteer **Maken**.
1. Voer bij **naam** een naam in voor de resource.
1. Selecteer voor **toepassings Type** **ASP.NET-webtoepassing**.
1. Voor **resource groep** selecteert u een bestaande groep of voert u een naam in voor een nieuwe groep.
1. Selecteer **Maken**.
1. Open de nieuwe Application Insights resource, vouw **essentiële** elementen uit en kopieer de instrumentatie sleutel.

![Scherm opname van de instrumentatie sleutel op het tabblad Application Insights overzicht.](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Claims definiëren

Een claim biedt tijdelijke opslag van gegevens tijdens het uitvoeren van een Azure AD B2C beleid. U declareert uw claims in het [ClaimsSchema-element](claimsschema.md).

1. Open het bestand extensies van uw beleid. Het bestand kan er ongeveer als volgt uitzien `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** .
1. Zoek het element [BuildingBlocks](buildingblocks.md) . Als u het element niet ziet, voegt u dit toe.
1. Zoek het element **ClaimsSchema** . Als u het element niet ziet, voegt u dit toe.
1. Voeg de volgende claims toe aan het **ClaimsSchema** -element:

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

Technische profielen kunnen worden beschouwd als functies in het aangepaste beleid. Deze functies gebruiken de [insluitings](technicalprofiles.md#include-technical-profile) benadering van het technische profiel, waarbij een technisch profiel een ander technisch profiel bevat en instellingen wijzigt of nieuwe functionaliteit toevoegt. De volgende tabel definieert de technische profielen die worden gebruikt voor het openen van een sessie en het plaatsen van gebeurtenissen.

| Technisch profiel | Taak |
| ----------------- | -----|
| AppInsights-Common | Het algemene technische profiel met een standaard configuratie. Het bevat de Application Insights instrumentatie sleutel, een verzameling van te registreren claims en ontwikkelaars modus. De andere technische profielen zijn onder andere het algemene technische profiel en meer claims toe te voegen, zoals de naam van de gebeurtenis. |
| AppInsights-SignInRequest | Registreert een **SignInRequest** -gebeurtenis met een set claims wanneer een aanmeldings aanvraag is ontvangen. |
| AppInsights-UserSignUp | Registreert een **UserSignUp** -gebeurtenis wanneer de gebruiker de registratie optie activeert bij een registratie-of aanmeldings traject. |
| AppInsights-SignInComplete | Registreert een **SignInComplete** -gebeurtenis bij geslaagde verificatie, wanneer een token is verzonden naar de Relying Party toepassing. |

Open het *TrustFrameworkExtensions.xml* -bestand in het Starter Pack. Voeg de technische profielen toe aan het **ClaimsProvider** -element:

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key, which you use for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is the property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
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

Nieuwe Orchestration-stappen toevoegen die verwijzen naar de technische profielen.

> [!IMPORTANT]
> Nadat u de nieuwe indelings stappen hebt toegevoegd, moet u de stappen sequentieel opnieuw nummeren zonder een geheel getal tussen 1 en N over te slaan.

1. Roep `AppInsights-SignInRequest` de tweede Orchestration-stap aan. Deze stap houdt in dat er een registratie-of aanmeldings aanvraag is ontvangen.

   ```xml
   <!-- Track that we have received a sign in request -->
   <OrchestrationStep Order="2" Type="ClaimsExchange">
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

1. Voeg vóór de `SendClaims` Orchestration-stap een nieuwe stap toe die aanroept `AppInsights-UserSignup` . Het wordt geactiveerd wanneer de gebruiker de registratie knop selecteert in een inschrijving of traject voor aanmelden.

   ```xml
   <!-- Handles the user selecting the sign-up link in the local account sign-in page -->
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

1. Nadat de `SendClaims` Orchestration-stap is aangeroepen `AppInsights-SignInComplete` . In deze stap ziet u dat de reis is voltooid.

   ```xml
   <!-- Track that we have successfully sent a token -->
   <OrchestrationStep Order="10" Type="ClaimsExchange">
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

## <a name="upload-your-file-run-the-policy-and-view-events"></a>Upload uw bestand, voer het beleid uit en Bekijk gebeurtenissen

Sla het *TrustFrameworkExtensions.xml* bestand op en upload het. Bel vervolgens het Relying Party-beleid vanuit uw toepassing of gebruik **nu uitvoeren** in de Azure Portal. Wacht tot uw gebeurtenissen beschikbaar zijn in Application Insights.

1. Open de **Application Insights** -resource in uw Azure Active Directory-Tenant.
1. Selecteer **gebruik** en selecteer vervolgens **gebeurtenissen**.
1. Stel **in** op **vorig uur** en **met** **3 minuten**. Mogelijk moet u het venster vernieuwen om de resultaten te bekijken.

![Scherm opname van Application Insights gebeurtenis statistieken.](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="collect-more-data"></a>Meer gegevens verzamelen

Als u wilt voldoen aan uw bedrijfs behoeften, wilt u mogelijk meer claims registreren. Als u een claim wilt toevoegen, moet u eerst [een claim definiëren](#define-claims)en vervolgens de claim toevoegen aan de claim verzameling voor invoer. Claims die u aan het **AppInsights-algemene** technische profiel toevoegt, worden in alle gebeurtenissen weer gegeven. Claims die u toevoegt aan een specifiek technisch profiel, worden alleen in dat geval weer gegeven. Het invoer claim element bevat de volgende kenmerken:

- **ClaimTypeReferenceId** is de verwijzing naar een claim type.
- **PartnerClaimType** is de naam van de eigenschap die wordt weer gegeven in azure Insights. Gebruik de syntaxis van `{property:NAME}` , waarbij `NAME` een eigenschap wordt toegevoegd aan de gebeurtenis.
- **DefaultValue** is een vooraf gedefinieerde waarde die moet worden vastgelegd, zoals een gebeurtenis naam. Als een claim die wordt gebruikt in de gebruikers traject leeg is, wordt de standaard waarde gebruikt. De `identityProvider` claim wordt bijvoorbeeld ingesteld door de technische profielen voor Federatie, zoals Facebook. Als de claim leeg is, wordt hiermee aangegeven dat de gebruiker zich heeft aangemeld met een lokaal account. De standaard waarde is dus ingesteld op **lokaal**. U kunt ook een [claim resolver](claim-resolver-overview.md) opnemen met een contextuele waarde, zoals de toepassings-id of het IP-adres van de gebruiker.

### <a name="manipulate-claims"></a>Claims manipuleren

U kunt [invoer claim transformaties](custom-policy-overview.md#manipulating-your-claims) gebruiken om de invoer claims te wijzigen of nieuwe te genereren voordat u ze naar Application Insights verzendt. In het volgende voor beeld bevat het technische profiel de `CheckIsAdmin` invoer claims trans formatie.

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

Als u een gebeurtenis wilt toevoegen, maakt u een nieuw technisch profiel dat het `AppInsights-Common` technische profiel bevat. Voeg vervolgens het nieuwe technische profiel als een indelings stap toe aan de [gebruikers reis](custom-policy-overview.md#orchestration-steps). Gebruik het voor [waarde](userjourneys.md#preconditions) -element om de gebeurtenis te activeren wanneer u klaar bent. Meld de gebeurtenis bijvoorbeeld alleen wanneer gebruikers door multi-factor Authentication worden uitgevoerd.

```xml
<TechnicalProfile Id="AppInsights-MFA-Completed">
  <InputClaims>
     <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="MFA-Completed" />
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

>[!Important]
>Wanneer u een gebeurtenis aan de reis van de gebruiker toevoegt, moet u de Orchestration-stappen sequentieel opnieuw nummeren.

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

Wanneer u Application Insights gebruikt om gebeurtenissen te definiëren, kunt u aangeven of de ontwikkelaars modus is ingeschakeld. De ontwikkelaars modus bepaalt hoe gebeurtenissen worden gebufferd. In een ontwikkel omgeving met mini maal gebeurtenis volume is het inschakelen van de ontwikkelaars modus tot gevolg dat gebeurtenissen direct naar Application Insights worden verzonden. De standaardwaarde is `false`. Schakel de ontwikkelaars modus niet in in productie omgevingen.

Als u de ontwikkelaars modus wilt inschakelen, wijzigt `DeveloperMode` u de meta gegevens `true` in het `AppInsights-Common` technische profiel:

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DeveloperMode">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="disable-telemetry"></a>Telemetrie uitschakelen

Als u Application Insights logboeken wilt uitschakelen, wijzigt `DisableTelemetry` u de meta gegevens `true` in het `AppInsights-Common` technische profiel:

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DisableTelemetry">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van aangepaste KPI-Dash boards met behulp van Azure-toepassing Insights](../azure-monitor/app/tutorial-app-dashboards.md).

::: zone-end