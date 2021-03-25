---
title: Zelf studie voor het configureren van Azure Active Directory B2C met micro soft Dynamics 365 fraude beveiliging
titleSuffix: Azure AD B2C
description: Zelf studie voor het configureren van Azure Active Directory B2C met micro soft Dynamics 365 fraude bescherming voor het identificeren van een Risk ante en frauduleuze account
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 8b725b7fcde8ad24934d74d3ce849260312d2f5f
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043611"
---
# <a name="tutorial-configure-microsoft-dynamics-365-fraud-protection-with-azure-active-directory-b2c"></a>Zelf studie: micro soft Dynamics 365 fraude beveiliging configureren met Azure Active Directory B2C

In deze voorbeeld zelfstudie bieden we richt lijnen voor het integreren van [micro soft Dynamics 365 fraude Protection](/dynamics365/fraud-protection/overview) (DFP) met de Azure Active Directory (AD) B2C.

Micro soft DFP biedt clients de mogelijkheid om te beoordelen of het risico van pogingen om nieuwe accounts te maken en om zich aan te melden bij het ecosysteem van de client frauduleus is. De evaluatie van micro soft DFP kan door de klant worden gebruikt om verdachte pogingen om nieuwe valse accounts te maken of om bestaande accounts te misbruiken. Account beveiliging omvat kunst matige intelligentie voor het bewaken van apparaten, Api's voor realtime risico beoordeling, regel-en lijst ervaring om de risico strategie te optimaliseren zoals de bedrijfs behoeften van de client, en een score card voor het controleren van de effectiviteit en tendensen van fraude beveiliging in het ecosysteem van de client.

In dit voor beeld worden de account beveiligings functies van micro soft DFP geïntegreerd met een Azure AD B2C gebruikers stroom. De service vingerafdrukt extern elke aanmelding of meldt zich aan en kijkt naar een verleden of aanwezig verdacht gedrag. Azure AD B2C roept een beslissings eindpunt aan van micro soft DFP, waarmee een resultaat wordt geretourneerd op basis van het oude en huidige gedrag van de geïdentificeerde gebruiker, en ook de aangepaste regels die zijn opgegeven in de micro soft DFP-service. Azure AD B2C maakt een goedkeurings besluit op basis van dit resultaat en stuurt hetzelfde terug naar micro soft DFP.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

- Een Azure-abonnement. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).

- Een [Azure AD B2C-Tenant](./tutorial-create-tenant.md). De Tenant is gekoppeld aan uw Azure-abonnement.

- Een micro soft DFPe- [abonnement](https://dynamics.microsoft.com/pricing/#Sales)ophalen. U kunt ook een [proef versie](https://dynamics.microsoft.com/ai/fraud-protection/signin/?RU=https%3A%2F%2Fdfp.microsoft.com%2Fsignin) van de client instellen.

## <a name="scenario-description"></a>Scenariobeschrijving

Micro soft DFP-integratie omvat de volgende onderdelen:

- **Azure AD B2C Tenant**: verifieert de gebruiker en fungeert als een client van micro soft DFP. Host een vingerafdruk script voor het verzamelen van identificatie-en diagnostische gegevens van elke gebruiker die een doel beleid uitvoert. Later blokken of nieuwe pogingen om zich aan te melden of zich aan te melden als micro soft DFP ze verdacht vindt.

- **Aangepaste app service**: een webtoepassing die twee doelen speelt.

  - Fungeert als HTML-pagina's voor gebruik als de gebruikers interface van het identiteits ervarings raamwerk. Verantwoordelijk voor het insluiten van het micro soft Dynamics 365-vingerafdruk script.

  - Een API-controller met REST-eind punten die micro soft DFP verbindt met Azure AD B2C. De gegevens verwerking, de structuur en de beveiliging van de ingang voldoen aan de beveiligings vereisten van beide.

- **Micro soft DFP-vingerafdruk service**: een dynamisch Inge sloten script, dat telemetrie van het apparaat registreert en zelfondertekende gebruikers gegevens om een unieke Identificeer bare vinger afdruk te maken, zodat de gebruiker deze later kan gebruiken in het besluitvormings proces.

- **Micro soft DFP API-eind punten**: levert het beslissings resultaat en accepteert een eind status die de bewerking weergeeft die door de client toepassing wordt uitgevoerd. Azure AD B2C niet rechtstreeks met de eind punten communiceert vanwege verschillende vereisten voor beveiliging en API-nettolading, wordt in plaats daarvan de app service als een tussenliggend gebruikt.

In het volgende architectuur diagram wordt de implementatie weer gegeven.

![Afbeelding toont het micro soft dynamics365e-architectuur diagram voor fraude beveiliging](./media/partner-dynamics365-fraud-protection/microsoft-dynamics-365-fraud-protection-diagram.png)

|Stap | Beschrijving |
|:-----| :-----------|
| 1. | De gebruiker ontvangt op een aanmeldings pagina. Gebruikers selecteren registreren om een nieuw account te maken en gegevens in te voeren op de pagina. Azure AD B2C gebruikers kenmerken worden verzameld.
| 2. | Azure AD B2C roept de middelste laag-API aan en geeft de gebruikers kenmerken door.
| 3. | Met de middelste laag-API worden gebruikers kenmerken verzameld en getransformeerd in een indeling die door micro soft DFP API kan worden verbruikt. Daarna wordt het verzonden naar micro soft DFP API.
| 4. | Nadat de micro soft DFP-API de informatie heeft verbruikt en verwerkt, wordt het resultaat geretourneerd naar de middelste laag-API.
| 5. | De middelste laag-API verwerkt de informatie en stuurt relevante informatie terug naar Azure AD B2C.
| 6. | Azure AD B2C ontvangt informatie terug via de API van de middelste laag. Als er een fout melding wordt weer gegeven, wordt er een fout bericht voor de gebruiker getoond. Als er een reactie op succes wordt weer gegeven, wordt de gebruiker geverifieerd en naar de map geschreven.

## <a name="set-up-the-solution"></a>De oplossing instellen

1. [Maak een Facebook-toepassing](./identity-provider-facebook.md#create-a-facebook-application) die is geconfigureerd om Federatie Azure AD B2C toe te staan.
2. [Voeg het Facebook-geheim toe](./custom-policy-get-started.md#create-the-facebook-key) dat u hebt gemaakt als een framework-beleids sleutel voor identiteits ervaring.

## <a name="configure-your-application-under-microsoft-dfp"></a>Uw toepassing configureren onder micro soft DFP

[Stel uw Azure AD-Tenant](/dynamics365/fraud-protection/integrate-real-time-api) in om micro soft DFP te gebruiken.

## <a name="deploy-to-the-web-application"></a>Implementeren in de webtoepassing

### <a name="implement-microsoft-dfp-service-fingerprinting"></a>Micro soft DFP service-vinger afdruk implementeren

[Micro soft DFP vinger afdruk van apparaten](/dynamics365/fraud-protection/device-fingerprinting) is een vereiste voor de beveiliging van micro soft DFP-accounts.

>[!NOTE]
>Naast Azure AD B2C-UI-pagina's kan de klant ook de vingerafdruk service in app-code implementeren voor uitgebreidere profilering van apparaten. De service voor het maken van vinger afdrukken in app code is niet opgenomen in dit voor beeld.

### <a name="deploy-the-azure-ad-b2c-api-code"></a>De API-code van de Azure AD B2C implementeren

Implementeer de [meegeleverde API-code](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/API) voor een Azure-service. De code kan worden [gepubliceerd vanuit Visual Studio](/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

CORS instellen, **toegestane oorsprong** toevoegen `https://{your_tenant_name}.b2clogin.com`

>[!NOTE]
>Later hebt u de URL van de geïmplementeerde service nodig om Azure AD te configureren met de vereiste instellingen.

Raadpleeg de [documentatie van app service](../app-service/app-service-web-tutorial-rest-api.md) voor meer informatie.

### <a name="add-context-dependent-configuration-settings"></a>Context afhankelijke configuratie-instellingen toevoegen

Configureer de toepassings instellingen in de [app service in azure](../app-service/configure-common.md#configure-app-settings). Hierdoor kunnen instellingen veilig worden geconfigureerd zonder dat deze in een opslag plaats worden gecontroleerd. De rest-API moet de volgende instellingen hebben:

| Toepassingsinstellingen | Bron | Notities |
| :-------- | :------------| :-----------|
|FraudProtectionSettings: InstanceId | Micro soft DFP-configuratie |     |
|FraudProtectionSettings:DeviceFingerprintingCustomerId | Uw klant-ID voor micro soft-apparaten met vinger afdruk |     |
| FraudProtectionSettings:ApiBaseUrl |  Uw basis-URL van micro soft DFP Portal   | -Int verwijderen om de productie-API aan te roepen|
|  TokenProviderConfig: resource  |     | -Int verwijderen om de productie-API aan te roepen|
|   TokenProviderConfig: ClientId       |De Azure AD-client-ID van uw zakelijke fraude beveiliging      |       |
| TokenProviderConfig: instantie | https://login.microsoftonline.com/<directory_ID> | Uw zakelijke Azure AD-Tenant instantie voor fraude beveiliging |
| TokenProviderConfig: CertificateThumbprint * | De vinger afdruk van het certificaat dat moet worden gebruikt voor verificatie bij uw zakelijke Azure AD-client-app |
| TokenProviderConfig: ClientSecret * | Het geheim voor uw zakelijke Azure AD-client-app | Aanbevolen om een geheimen Manager te gebruiken |

* Alleen set 1 van de 2 gemarkeerde para meters, afhankelijk van of u zich verifieert met een certificaat of een geheim, zoals een wacht woord.

## <a name="azure-ad-b2c-configuration"></a>Azure AD B2C configuratie

### <a name="replace-the-configuration-values"></a>De configuratie waarden vervangen

Zoek in het meegeleverde [aangepaste beleid](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies)de volgende tijdelijke aanduidingen en vervang ze door de bijbehorende waarden van uw exemplaar.

| Tijdelijke aanduiding | Vervangen door | Notities |
| :-------- | :------------| :-----------|
|{your_tenant_name} | De korte naam van uw Tenant |  "yourtenant" van yourtenant.onmicrosoft.com   |
|{your_tenantId} | Tenant-ID van uw Azure AD B2C-Tenant |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_IdentityExperienceFramework_appid}    |   App-ID van de IdentityExperienceFramework-app die is geconfigureerd in uw Azure AD B2C-Tenant    |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_ ProxyIdentityExperienceFramework _appid}     |  App-ID van de ProxyIdentityExperienceFramework-app die is geconfigureerd in uw Azure AD B2C-Tenant      |   01234567-89ab-cdef-0123-456789abcdef     |
|  {your_tenant_extensions_appid}   |  App-ID van de opslag toepassing van uw Tenant   |  01234567-89ab-cdef-0123-456789abcdef  |
|   {your_tenant_extensions_app_objectid}  | Object-ID van de opslag toepassing van uw Tenant    | 01234567-89ab-cdef-0123-456789abcdef   |
|   {your_app_insights_instrumentation_key}  |   Instrumentatie sleutel van uw app Insights-exemplaar *  |   01234567-89ab-cdef-0123-456789abcdef |
|  {your_ui_base_url}   | Eind punt in uw app service van waaruit uw gebruikers interface bestanden worden bediend    | https://yourapp.azurewebsites.net/B2CUI/GetUIPage   |
|   {your_app_service_url}  | De URL van uw app service    |  https://yourapp.azurewebsites.net  |
|   {uw-Facebook-App-ID}  |  App-ID van de Facebook-app die u hebt geconfigureerd voor Federatie met Azure AD B2C   | 000000000000000   |
|  {uw-Facebook-app-Secret}   |  Naam van de beleids sleutel die u hebt opgeslagen app-geheim van Facebook als   | B2C_1A_FacebookAppSecret   |

* App Insights kan zich in een andere Tenant bevindt. Deze stap is optioneel. Verwijder, indien nodig, de bijbehorende TechnicalProfiles en OrechestrationSteps.

### <a name="call-microsoft-dfp-label-api"></a>Micro soft DFP-label-API aanroepen

Klanten moeten [Label-API implementeren](/dynamics365/fraud-protection/integrate-ap-api). Zie [micro soft DFP API](https://apidocs.microsoft.com/services/dynamics365fraudprotection#/AccountProtection/v1.0) voor meer informatie.

`URI: < API Endpoint >/v1.0/label/account/create/<userId>`

De waarde van de gebruikers-id moet hetzelfde zijn als het item in de bijbehorende Azure AD B2C Configuration-waarde (ObjectID).

>[!NOTE]
>Bericht over toestemming toevoegen aan de pagina kenmerk verzameling. Meld dat de gegevens van de telemetrie en gebruikers identiteit van de gebruikers worden vastgelegd voor account beveiliging.

## <a name="configure-the-azure-ad-b2c-policy"></a>Het Azure AD B2C-beleid configureren

1. Ga naar het [Azure AD B2C-beleid](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies) in de map beleid.

2. Volg dit [document](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) om [LocalAccounts Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) te downloaden

3. Configureer het beleid voor de Azure AD B2C Tenant.

>[!NOTE]
>Update de beleids regels die zijn opgegeven om te koppelen aan uw specifieke Tenant.

## <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Open de Azure AD B2C Tenant en selecteer onder beleids regels het **Framework identiteits ervaring** selecteren.

2. Selecteer uw eerder gemaakte **SignUpSignIn**.

3. Selecteer **gebruikers stroom uitvoeren** en selecteer de instellingen:

   a. **Toepassing**: Selecteer de geregistreerde app (voor beeld is JWT)

   b. **Antwoord-URL**: de **omleidings-URL** selecteren

   c. Selecteer **Gebruikersstroom uitvoeren**.

4. Ga door naar de registratie stroom en maak een account

5. De service micro soft DFP wordt aangeroepen tijdens de stroom, nadat het gebruikers kenmerk is gemaakt. Als de stroom onvolledig is, controleert u of de gebruiker niet is opgeslagen in de map.

>[!NOTE]
>Regels rechtstreeks bijwerken in micro soft DFP portal als u de [micro soft DFP-regel engine](/dynamics365/fraud-protection/rules)gebruikt.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie:

- [Voor beelden van micro soft DFP](https://github.com/Microsoft/Dynamics-365-Fraud-Protection-Samples)

- [Aangepast beleid in Azure AD B2C](./custom-policy-overview.md)

- [Aan de slag met aangepast beleid in Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
