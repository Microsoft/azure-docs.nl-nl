---
title: Problemen met aangepaste beleid oplossen met Application Insights
titleSuffix: Azure AD B2C
description: Application Insights instellen om de uitvoering van uw aangepaste beleids regels te traceren.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/16/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d4a68b492bad4ac091b4600c9ec81ac0de27cc05
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572894"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Azure Active Directory B2C-logboeken met Application Insights verzamelen

In dit artikel worden de stappen beschreven voor het verzamelen van logboeken van Active Directory B2C (Azure AD B2C), zodat u problemen met uw aangepaste beleids regels kunt vaststellen. Application Insights biedt een manier om uitzonde ringen te diagnosticeren en prestatie problemen van toepassingen te visualiseren. Azure AD B2C bevat een functie voor het verzenden van gegevens naar Application Insights.

De gedetailleerde activiteiten logboeken die hier worden beschreven, moeten **alleen** worden ingeschakeld tijdens de ontwikkeling van uw aangepaste beleids regels.

> [!WARNING]
> Stel de to niet `DeploymentMode` `Development` in in productie omgevingen. In Logboeken worden alle claims verzameld die worden verzonden naar en van id-providers. U bent als ontwikkelaar verantwoordelijk voor alle persoons gegevens die in uw Application Insights-logboeken worden verzameld. Deze gedetailleerde logboeken worden alleen verzameld wanneer het beleid in de **ontwikkelaars modus** wordt geplaatst.

## <a name="set-up-application-insights"></a>Application Insights instellen

Als u er nog geen hebt, maakt u een instantie van Application Insights in uw abonnement.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het filter **Directory + abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure-abonnement bevat (niet uw Azure AD B2C Directory).
1. Selecteer **een resource maken** in het navigatie menu aan de linkerkant.
1. Zoek en selecteer **Application Insights** en selecteer vervolgens **maken**.
1. Vul het formulier in, selecteer **controleren + maken** en selecteer vervolgens **maken**.
1. Zodra de implementatie is voltooid, selecteert **u naar resource**.
1. Onder **configureren** in Application Insights menu, selecteert u **Eigenschappen**.
1. Noteer de **instrumentatie sleutel** voor gebruik in een latere stap.

## <a name="configure-the-custom-policy"></a>Het aangepaste beleid configureren

1. Open het Relying Party (RP)-bestand, bijvoorbeeld *SignUpOrSignin.xml*.
1. Voeg de volgende kenmerken toe aan het `<TrustFrameworkPolicy>` element:

   ```xml
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Als deze nog niet bestaat, voegt `<UserJourneyBehaviors>` u een onderliggend knoop punt toe aan het `<RelyingParty>` knoop punt. Deze moet direct na worden geplaatst `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />` .
1. Voeg het volgende knoop punt toe als onderliggend item van het `<UserJourneyBehaviors>` element. Zorg ervoor dat u vervangt door `{Your Application Insights Key}` de Application Insights **instrumentatie sleutel** die u eerder hebt vastgelegd.

    ```xml
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"` vertelt ApplicationInsights om de telemetrie te versnellen door de verwerkings pijplijn. Goed voor ontwikkeling, maar beperkt op hoge volumes. Stel in productie de in `DeveloperMode` op `false` .
    * `ClientEnabled="true"` Hiermee wordt het ApplicationInsights-client script verzonden voor het bijhouden van de pagina weergave en fouten aan de client zijde. U kunt deze weer geven in de tabel **browserTimings** in de Application Insights Portal. Als u deze instelling inschakelt `ClientEnabled= "true"` , voegt u Application Insights toe aan uw pagina script en krijgt u een tijds duur van het laden van pagina's en Ajax-aanroepen, tellingen, Details van browser uitzonderingen en Ajax-fouten, en het aantal gebruikers en sessies. Dit veld is **optioneel** en is standaard ingesteld op `false` .
    * `ServerEnabled="true"` Hiermee wordt de bestaande UserJourneyRecorder-JSON als aangepaste gebeurtenis verzonden naar Application Insights.

    Bijvoorbeeld:

    ```xml
    <TrustFrameworkPolicy
      ...
      TenantId="fabrikamb2c.onmicrosoft.com"
      PolicyId="SignUpOrSignInWithAAD"
      DeploymentMode="Development"
      UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
    >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="UserJourney ID from your extensions policy, or equivalent (for example: SignUpOrSigninWithAzureAD)" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
    </TrustFrameworkPolicy>
    ```

1. Upload het beleid.

## <a name="see-the-logs-in-application-insights"></a>Raadpleeg de logboeken in Application Insights

Er is een korte vertraging, meestal minder dan vijf minuten, voordat u nieuwe logboeken in Application Insights kunt zien.

1. Open de Application Insights resource die u hebt gemaakt in de [Azure Portal](https://portal.azure.com).
1. Selecteer op de pagina **overzicht** de optie **Logboeken**.
1. Open een nieuw tabblad in Application Insights.

Hier volgt een lijst met query's die u kunt gebruiken om de logboeken weer te geven:

| Query’s uitvoeren | Description |
|---------------------|--------------------|
`traces` | Alle logboeken weer geven die zijn gegenereerd door Azure AD B2C |
`traces | where timestamp > ago(1d)` | Alle logboeken weer geven die zijn gegenereerd door Azure AD B2C voor de afgelopen dag

De invoer kan lang zijn. Exporteren naar CSV voor een betere blik.

Zie [overzicht van logboek query's in azure monitor](../azure-monitor/logs/log-query-overview.md)voor meer informatie over het uitvoeren van query's.

## <a name="configure-application-insights-in-production"></a>Application Insights in productie configureren

Als u de prestaties van uw productie omgeving en betere gebruikers ervaring wilt verbeteren, is het belang rijk dat u uw beleid configureert om berichten te negeren die niet belang rijk zijn. Gebruik de volgende configuratie om alleen kritieke fout berichten naar uw Application Insights te verzenden. 

1. Stel het `DeploymentMode` kenmerk van de [TrustFrameworkPolicy](trustframeworkpolicy.md) in op `Production` . 

   ```xml
   <TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_signup_signin"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin"
   DeploymentMode="Production"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights">
   ```

1. Stel de `DeveloperMode` [JourneyInsights](relyingparty.md#journeyinsights) in op `false` .

   ```xml
   <UserJourneyBehaviors>
     <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="false" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
   </UserJourneyBehaviors>
   ```
   
1. Upload en test uw beleid.

## <a name="next-steps"></a>Volgende stappen

De community heeft een reis viewer voor gebruikers ontwikkeld om identiteits ontwikkelaars te helpen. Het leest van uw Application Insights-exemplaar en biedt een goed gestructureerde weer gave van de reis gebeurtenissen van de gebruiker. U verkrijgt de bron code en implementeert deze in uw eigen oplossing.

De gebruikers reis speler wordt niet ondersteund door micro soft en wordt strikt beschikbaar gesteld.

U kunt de versie van de viewer vinden die gebeurtenissen van Application Insights op GitHub leest:

[Azure-samples/Active-Directory-B2C-Advanced-policies](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)
