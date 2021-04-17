---
title: Zelfstudie voor het Azure Active Directory B2C met Microsoft Dynamics 365 Fraud Protection
titleSuffix: Azure AD B2C
description: Zelfstudie voor het configureren Azure Active Directory B2C met Microsoft Dynamics 365 Fraud Protection om riskante en frauduleuze account te identificeren
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 8c9d760ed888eb194ad8f282f180a634e3c09538
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587813"
---
# <a name="tutorial-configure-microsoft-dynamics-365-fraud-protection-with-azure-active-directory-b2c"></a>Zelfstudie: Microsoft Dynamics 365 Fraud Protection configureren met Azure Active Directory B2C

In deze voorbeeldzelfstudie bieden we richtlijnen voor het integreren van [Microsoft Dynamics 365 Fraud Protection](https://docs.microsoft.com/dynamics365/fraud-protection/overview) (DFP) met de Azure Active Directory (AD) B2C.

Microsoft DFP biedt clients de mogelijkheid om te beoordelen of het risico van pogingen om nieuwe accounts te maken en pogingen om zich aan te melden bij het ecosysteem van de client frauduleus is. Microsoft DFP-evaluatie kan door de klant worden gebruikt om verdachte pogingen om nieuwe valse accounts te maken of bestaande accounts in gevaar te brengen, te blokkeren of op te vragen. Accountbeveiliging omvat onder andere kunstmatige intelligentie voor vingerafdrukken van apparaten, API's voor realtime risicoanalyse, regel- en lijstervaring om de risicostrategie te optimaliseren volgens de bedrijfsbehoeften van de klant, en een scorecard om de effectiviteit en trends van fraudebeveiliging in het clientecosysteem te bewaken.

In dit voorbeeld integreren we de accountbeveiligingsfuncties van Microsoft DFP met een Azure AD B2C gebruikersstroom. De service neemt elke aanmeldings- of aanmeldingspoging extern af en let op verdacht gedrag in het verleden of aanwezig. Azure AD B2C roept een beslissings-eindpunt van Microsoft DFP, die een resultaat retourneert op basis van alle eerdere en huidige gedrag van de geïdentificeerde gebruiker, en ook de aangepaste regels die zijn opgegeven in de Microsoft DFP-service. Azure AD B2C maakt een goedkeuringsbeslissing op basis van dit resultaat en geeft hetzelfde door aan Microsoft DFP.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

- Een Azure-abonnement. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).

- Een [Azure AD B2C tenant](./tutorial-create-tenant.md). De tenant is gekoppeld aan uw Azure-abonnement.

- Een Microsoft DFP-abonnement [aanschaffen.](https://dynamics.microsoft.com/pricing/#Sales) U kunt ook een [proefversie van de client](https://dynamics.microsoft.com/ai/fraud-protection/signin/?RU=https%3A%2F%2Fdfp.microsoft.com%2Fsignin) instellen.

## <a name="scenario-description"></a>Scenariobeschrijving

Microsoft DFP-integratie omvat de volgende onderdelen:

- **Azure AD B2C tenant:** verifieert de gebruiker en fungeert als een client van Microsoft DFP. Host een vingerafdrukscript dat identificatie- en diagnostische gegevens verzamelt van elke gebruiker die een doelbeleid uitvoert. Latere blokken of uitdagingen aanmelding of aanmeldingspogingen als Microsoft DFP vindt ze verdacht.

- **Aangepaste app-service:** een webtoepassing voor twee doeleinden.

  - Html-pagina's worden gebruikt als Identity Experience Framework gebruikersinterface van uw bedrijf. Verantwoordelijk voor het insluiten van het Microsoft Dynamics 365-vingerafdrukscript.

  - Een API-controller met RESTful-eindpunten die Microsoft DFP verbindt met Azure AD B2C. De verwerking en structuur van gegevens verwerken en voldoen aan de beveiligingsvereisten van beide.

- **Microsoft DFP fingerprinting service:** dynamisch ingesloten script, waarmee apparaat-telemetrie en zelf-asserte gebruikersgegevens worden logboeken om een uniek identificeerbare vingerafdruk te maken voor de gebruiker die later in het besluitvormingsproces kan worden gebruikt.

- **Microsoft DFP API-eindpunten:** biedt het beslissingsresultaat en accepteert een definitieve status die de bewerking weerspiegelt die door de clienttoepassing wordt uitgevoerd. Azure AD B2C communiceert niet rechtstreeks met de eindpunten vanwege verschillende vereisten voor beveiliging en API-nettoladingen, maar gebruikt de App Service als tussenliggende service.

In het volgende architectuurdiagram ziet u de implementatie.

![Afbeelding toont het diagram van de microsoft dynamics365-architectuur voor fraudebeveiliging](./media/partner-dynamics365-fraud-protection/microsoft-dynamics-365-fraud-protection-diagram.png)

|Stap | Beschrijving |
|:-----| :-----------|
| 1. | De gebruiker komt aan op een aanmeldingspagina. Gebruikers selecteren registreren om een nieuw account te maken en voeren gegevens in op de pagina. Azure AD B2C verzamelt gebruikerskenmerken.
| 2. | Azure AD B2C roept de API voor de middelste laag aan en geeft de gebruikerskenmerken door.
| 3. | Api voor middelste laag verzamelt gebruikerskenmerken en transformeert deze in een indeling die microsoft DFP API kan gebruiken. Vervolgens na verzendt het naar Microsoft DFP API.
| 4. | Nadat Microsoft DFP API de informatie heeft verbruikt en verwerkt, wordt het resultaat naar de API voor de middelste laag terug gegeven.
| 5. | De API voor de middelste laag verwerkt de informatie en stuurt relevante informatie terug naar Azure AD B2C.
| 6. | Azure AD B2C ontvangt informatie terug van de API voor de middelste laag. Als er een foutbericht wordt weergegeven, wordt er een foutbericht weergegeven voor de gebruiker. Als het antwoord Geslaagd wordt weer gegeven, wordt de gebruiker geverifieerd en naar de map geschreven.

## <a name="set-up-the-solution"></a>De oplossing instellen

1. [Maak een Facebook-toepassing die](./identity-provider-facebook.md#create-a-facebook-application) is geconfigureerd om federatie toe te staan Azure AD B2C.
2. [Voeg het Facebook-geheim toe](./tutorial-create-user-flows.md?pivots=b2c-custom-policy#create-the-facebook-key) dat u hebt gemaakt als Identity Experience Framework-beleidssleutel.

## <a name="configure-your-application-under-microsoft-dfp"></a>Uw toepassing configureren onder Microsoft DFP

[Stel uw Azure AD-tenant in](/dynamics365/fraud-protection/integrate-real-time-api) voor het gebruik van Microsoft DFP.

## <a name="deploy-to-the-web-application"></a>Implementeren in de webtoepassing

### <a name="implement-microsoft-dfp-service-fingerprinting"></a>Vingerafdruk van Microsoft DFP-service implementeren

[Microsoft DFP apparaat fingerprinting](/dynamics365/fraud-protection/device-fingerprinting) is een vereiste voor Microsoft DFP accountbeveiliging.

>[!NOTE]
>Naast de Azure AD B2C ui-pagina's kan de klant de fingerprinting-service ook implementeren in app-code voor uitgebreidere profilering van apparaten. De fingerprinting-service in app-code is niet opgenomen in dit voorbeeld.

### <a name="deploy-the-azure-ad-b2c-api-code"></a>De API Azure AD B2C code implementeren

Implementeer de [opgegeven API-code](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/API) in een Azure-service. De code kan worden [gepubliceerd vanuit Visual Studio](/visualstudio/deployment/quickstart-deploy-to-azure).

CORS instellen, toegestane **oorsprong toevoegen**`https://{your_tenant_name}.b2clogin.com`

>[!NOTE]
>U hebt later de URL van de geïmplementeerde service nodig om Azure AD te configureren met de vereiste instellingen.

Zie [App Service-documentatie](../app-service/app-service-web-tutorial-rest-api.md) voor meer informatie.

### <a name="add-context-dependent-configuration-settings"></a>Contextafhankelijke configuratie-instellingen toevoegen

Configureer de toepassingsinstellingen in [de App Service in Azure](../app-service/configure-common.md#configure-app-settings). Hierdoor kunnen instellingen veilig worden geconfigureerd zonder ze in te checken in een opslagplaats. De REST API heeft de volgende instellingen nodig:

| Toepassingsinstellingen | Bron | Notities |
| :-------- | :------------| :-----------|
|FraudProtectionSettings:InstanceId | Microsoft DFP-configuratie |     |
|FraudProtectionSettings:DeviceFingerprintingCustomerId | De klant-id van uw Microsoft-apparaat met vingerafdruk |     |
| FraudProtectionSettings:ApiBaseUrl |  Uw basis-URL van de Microsoft DFP-portal   | Verwijder '-int' om in plaats daarvan de productie-API aan te roepen|
|  TokenProviderConfig: Resource  | Uw basis-URL- https://api.dfp.dynamics-int.com     | Verwijder '-int' om in plaats daarvan de productie-API aan te roepen|
|   TokenProviderConfig:ClientId       |De id van uw Zakelijke Azure AD-client-app voor Fraudebeveiliging      |       |
| TokenProviderConfig:Authority | https://login.microsoftonline.com/<directory_ID> | Uw Azure AD-tenantinstantie voor fraudebeveiliging |
| TokenProviderConfig:CertificateThumbprint* | De vingerafdruk van het certificaat dat moet worden gebruikt voor verificatie bij uw zakelijke Azure AD-client-app |
| TokenProviderConfig:ClientSecret* | Het geheim voor uw zakelijke Azure AD-client-app | Aanbevolen om een geheimenbeheer te gebruiken |

*Stel slechts 1 van de 2 gemarkeerde parameters in, afhankelijk van of u zich verifieert met een certificaat of een geheim, zoals een wachtwoord.

## <a name="azure-ad-b2c-configuration"></a>Azure AD B2C configureren

### <a name="replace-the-configuration-values"></a>De configuratiewaarden vervangen

Zoek in [het opgegeven aangepaste beleid](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies)de volgende tijdelijke aanduidingen en vervang deze door de bijbehorende waarden van uw exemplaar.

| Tijdelijke aanduiding | Vervangen door | Notities |
| :-------- | :------------| :-----------|
|{your_tenant_name} | Korte naam van uw tenant |  'yourtenant' van yourtenant.onmicrosoft.com   |
|{your_tenantId} | Tenant-id van uw Azure AD B2C tenant |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_IdentityExperienceFramework_appid}    |   App-id van de identityExperienceFramework-app die is geconfigureerd in uw Azure AD B2C tenant    |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_ ProxyIdentityExperienceFramework _appid}     |  App-id van de app ProxyIdentityExperienceFramework die is geconfigureerd in uw Azure AD B2C tenant      |   01234567-89ab-cdef-0123-456789abcdef     |
|  {your_tenant_extensions_appid}   |  App-id van de opslagtoepassing van uw tenant   |  01234567-89ab-cdef-0123-456789abcdef  |
|   {your_tenant_extensions_app_objectid}  | Object-id van de opslagtoepassing van uw tenant    | 01234567-89ab-cdef-0123-456789abcdef   |
|   {your_app_insights_instrumentation_key}  |   Instrumentatiesleutel van uw App Insights-exemplaar*  |   01234567-89ab-cdef-0123-456789abcdef |
|  {your_ui_base_url}   | Eindpunt in uw app-service van waar uw UI-bestanden worden bediend    | `https://yourapp.azurewebsites.net/B2CUI/GetUIPage`   |
|   {your_app_service_url}  | URL van uw app-service    |  `https://yourapp.azurewebsites.net`  |
|   {your-facebook-app-id}  |  App-id van de Facebook-app die u hebt geconfigureerd voor federatie met Azure AD B2C   | 000000000000000   |
|  {your-facebook-app-secret}   |  Naam van de beleidssleutel die u het app-geheim van Facebook hebt opgeslagen als   | B2C_1A_FacebookAppSecret   |

*App-inzichten kunnen zich in een andere tenant. Deze stap is optioneel. Verwijder de bijbehorende TechnicalProfiles en OrechestrationSteps als dat niet nodig is.

### <a name="call-microsoft-dfp-label-api"></a>Microsoft DFP-label-API aanroepen

Klanten moeten [label-API implementeren.](/dynamics365/fraud-protection/integrate-ap-api) Zie [Microsoft DFP API](https://apidocs.microsoft.com/services/dynamics365fraudprotection#/AccountProtection/v1.0) voor meer informatie.

`URI: < API Endpoint >/v1.0/label/account/create/<userId>`

De waarde van de userID moet gelijk zijn aan de waarde in de bijbehorende Azure AD B2C -configuratiewaarde (ObjectID).

>[!NOTE]
>Voeg toestemmingsmeldingen toe aan de pagina kenmerkverzameling. Informeer dat de telemetrie- en gebruikersidentiteitsgegevens van de gebruikers worden vastgelegd voor accountbeveiligingsdoeleinden.

## <a name="configure-the-azure-ad-b2c-policy"></a>Het beleid voor Azure AD B2C configureren

1. Ga naar het [Azure AD B2C beleid](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies) in de map Beleid.

2. Volg dit [document om](./tutorial-create-user-flows.md?pivots=b2c-custom-policy?tabs=applications#custom-policy-starter-pack) het [LocalAccounts Starter Pack te downloaden](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. Configureer het beleid voor de Azure AD B2C tenant.

>[!NOTE]
>Werk de opgegeven beleidsregels bij met betrekking tot uw specifieke tenant.

## <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Open de Azure AD B2C tenant en selecteer onder Beleid de **Identity Experience Framework**.

2. Selecteer uw eerder gemaakte **SignUpSignIn**.

3. Selecteer **Gebruikersstroom uitvoeren** en selecteer de instellingen:

   a. **Toepassing:** selecteer de geregistreerde app (voorbeeld is JWT)

   b. **Antwoord-URL:** selecteer de **omleidings-URL**

   c. Selecteer **Gebruikersstroom uitvoeren**.

4. Door de aanmeldingsstroom gaan en een account maken

5. De Microsoft DFP-service wordt aangeroepen tijdens de stroom, nadat het gebruikerskenmerk is gemaakt. Als de stroom onvolledig is, controleert u of de gebruiker niet is opgeslagen in de map.

>[!NOTE]
>Werk regels rechtstreeks bij in de Microsoft DFP-portal als u [microsoft DFP-regelen engine gebruikt.](/dynamics365/fraud-protection/rules)

## <a name="next-steps"></a>Volgende stappen

Lees de volgende artikelen voor meer informatie:

- [Microsoft DFP-voorbeelden](https://github.com/Microsoft/Dynamics-365-Fraud-Protection-Samples)

- [Aangepast beleid in Azure AD B2C](./custom-policy-overview.md)

- [Aan de slag met aangepaste beleidsregels in Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)
