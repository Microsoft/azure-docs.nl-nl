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
ms.date: 04/05/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 074bffb8614be1f71ba1956fd5a238bc19354c58
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028732"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Azure Active Directory B2C-logboeken met Application Insights verzamelen

In dit artikel worden de stappen beschreven voor het verzamelen van logboeken van Active Directory B2C (Azure AD B2C), zodat u problemen met uw aangepaste beleids regels kunt vaststellen. AppInsights biedt een manier om uitzonderingen te diagnosticeren en prestatieproblemen van toepassingen te visualiseren. Azure AD B2C bevat een functie voor het verzenden van gegevens naar AppInsights.

De gedetailleerde activiteiten logboeken die hier worden beschreven, moeten **alleen** worden ingeschakeld tijdens de ontwikkeling van uw aangepaste beleids regels.

> [!WARNING]
> Stel de to niet `DeploymentMode` `Development` in in productie omgevingen. In Logboeken worden alle claims verzameld die worden verzonden naar en van id-providers. U bent als ontwikkelaar verantwoordelijk voor alle persoons gegevens die in uw Application Insights-logboeken worden verzameld. Deze gedetailleerde logboeken worden alleen verzameld wanneer het beleid in de **ontwikkelaars modus** wordt geplaatst.

## <a name="set-up-application-insights"></a>Application Insights instellen

Als u er nog geen hebt, maakt u een instantie van Application Insights in uw abonnement.

> [!TIP]
> Eén exemplaar van Application Insights kan worden gebruikt voor meerdere Azure AD B2C-tenants. In uw query kunt u vervolgens filteren op de Tenant of de naam van het beleid. [Zie de logboeken in Application Insights](#see-the-logs-in-application-insights) voor beelden voor meer informatie.

Voer de volgende stappen uit om een exemplaar van Application Insights in uw abonnement te verlaten:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het filter **Directory + abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure-abonnement bevat (niet uw Azure AD B2C Directory).
1. Open de Application Insights resource die u eerder hebt gemaakt.
1. Op de pagina **overzicht** en noteer de **instrumentatie sleutel**

Voer de volgende stappen uit om een exemplaar van Application Insights in uw abonnement te maken:

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

1. Als deze nog niet bestaat, voegt `<UserJourneyBehaviors>` u een onderliggend knoop punt toe aan het `<RelyingParty>` knoop punt. Het moet zich bevinden na `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />` .
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

| Query | Description |
|---------------------|--------------------|
| `traces` | Alle logboeken ophalen die zijn gegenereerd door Azure AD B2C |
| `traces | where timestamp > ago(1d)` | Alle logboeken die zijn gegenereerd door Azure AD B2C, ophalen voor de afgelopen dag.|
| `traces | where message contains "exception" | where timestamp > ago(2h)`|  Alle logboeken ophalen met fouten in de afgelopen twee uur.|
| `traces | where customDimensions.Tenant == "contoso.onmicrosoft.com" and customDimensions.UserJourney  == "b2c_1a_signinandup"` | Alle logboeken ophalen die zijn gegenereerd door Azure AD B2C *contoso.onmicrosoft.com* -Tenant en gebruikers traject is *b2c_1a_signinandup*. |
| `traces | where customDimensions.CorrelationId == "00000000-0000-0000-0000-000000000000"`| Alle door Azure AD B2C gegenereerde logboeken ophalen voor een correlatie-ID. Vervang de correlatie-ID door uw correlatie-ID. | 

De invoer kan lang zijn. Exporteren naar CSV voor een betere blik.

Zie [overzicht van logboek query's in azure monitor](../azure-monitor/logs/log-query-overview.md)voor meer informatie over het uitvoeren van query's.

## <a name="see-the-logs-in-vs-code-extension"></a>Raadpleeg de logboeken in VS code extension

U wordt aangeraden de [Azure AD B2C-uitbrei ding](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) voor [VS code](https://code.visualstudio.com/)te installeren. Met de uitbrei ding Azure AD B2C worden de logboeken voor u geordend op basis van de naam van het beleid, correlatie-ID (de Application Insights geeft het eerste cijfer van de correlatie-ID) en het logboek tijds tempel. Deze functie helpt u bij het zoeken naar het relevante logboek op basis van het lokale tijds tempel en de gebruikers traject te zien zoals uitgevoerd door Azure AD B2C.

> [!NOTE]
> De community heeft de VS code-extensie voor Azure AD B2C ontwikkeld om identiteits ontwikkelaars te helpen. De extensie wordt niet ondersteund door micro soft en wordt strikt beschikbaar gesteld.

### <a name="set-application-insights-api-access"></a>Application Insights API-toegang instellen

Nadat u de Application Insights hebt ingesteld en het aangepaste beleid hebt geconfigureerd, moet u uw Application Insights **API-id** ophalen en de **API-sleutel** maken. Zowel de API-id als de API-sleutel worden gebruikt door Azure AD B2C extensie om de Application Insights-gebeurtenissen (webelementen) te lezen. Uw API-sleutels moeten worden beheerd zoals wacht woorden. Bewaar het geheim.

> [!NOTE]
> Application Insights instrumentatie sleutel die u eerder maakt, wordt gebruikt door Azure AD B2C om er webelementen naar Application Insights te sturen. U gebruikt de instrumentatie sleutel alleen in uw Azure AD B2C beleid, niet in de VS code-uitbrei ding.

Application Insights-ID en-sleutel ophalen:

1. Open in Azure Portal de Application Insights resource voor uw toepassing.
1. Selecteer **instellingen** en selecteer vervolgens **API-toegang**.
1. De **toepassings-id** kopiëren
1. Selecteer **API-sleutel maken**
1. Schakel het selectie vakje **telemetrie lezen** in.
1. Kopieer de **sleutel** voordat u de BLADe API-sleutel maken sluit en sla deze op een veilige plek op. Als u de sleutel kwijtraakt, moet u een nieuwe maken.

    ![Scherm afbeelding die laat zien hoe u API-toegangs sleutel maakt.](./media/troubleshoot-with-application-insights/application-insights-api-access.png)

### <a name="set-up-azure-ad-b2c-vs-code-extension"></a>Azure AD B2C VS-code-uitbrei ding instellen

Nu u Azure-toepassing Insights-API-ID en-sleutel hebt, kunt u de VS code-extensie configureren om de logboeken te lezen. Azure AD B2C VS code-extensie biedt twee bereiken voor instellingen:

- **Globale instellingen van gebruiker** -instellingen die wereld wijd van toepassing zijn op alle instanties van VS code die u opent.
- **Werkruimte instellingen** : instellingen die zijn opgeslagen in uw werk ruimte en die alleen van toepassing zijn wanneer de werk ruimte wordt geopend (met behulp van de **open map** VS code).

1. Klik in de **Azure AD B2C Trace** Explorer op het pictogram **instellingen** .

    ![Scherm afbeelding die laat zien hoe u de Application Insights-instellingen selecteert.](./media/troubleshoot-with-application-insights/app-insights-settings.png)

1. Geef de Azure-toepassing Insights **-id en-** **sleutel** op.
1. Klik op **Opslaan**.

Nadat u de instellingen hebt opgeslagen, worden de Application Insights-logboeken weer gegeven in het venster **Azure AD B2C tracering (app Insights)** .

![Scherm opname van Azure AD B2C extensie voor vscode, die de Azure-toepassing Insights-tracering presenteert.](./media/troubleshoot-with-application-insights/vscode-extension-application-insights-trace.png)


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

- Meer informatie over het [oplossen van Azure AD B2C aangepaste beleids regels](troubleshoot-custom-policies.md)
