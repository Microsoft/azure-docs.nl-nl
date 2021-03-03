---
title: API-verificatie en-autorisatie-Azure Time Series Insights | Microsoft Docs
description: In dit artikel wordt beschreven hoe u verificatie en autorisatie configureert voor een aangepaste toepassing die de Azure Time Series Insights-API aanroept.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/23/2021
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 58c0f408e3ad80109efd3db79d6e4a0d881aed78
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724168"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Verificatie en autorisatie voor Azure Time Series Insights-API

Afhankelijk van de behoeften van uw bedrijf bevat uw oplossing mogelijk een of meer client toepassingen die u gebruikt om te communiceren met de [api's](https://docs.microsoft.com/en-us/rest/api/time-series-insights/reference-data-access-overview)van uw Azure time series Insights-omgeving. Azure Time Series Insights voert verificatie uit met behulp [van Azure AD-beveiligings tokens op basis van OAUTH 2,0](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims). Als u uw client (s) wilt verifiëren, moet u een Bearer-token met de juiste machtigingen krijgen en dit samen met uw API-aanroepen door geven. In dit document worden verschillende methoden voor het verkrijgen van referenties beschreven die u kunt gebruiken om een Bearer-token en verificatie te verkrijgen.


  een app registreren in Azure Active Directory met behulp van de nieuwe Azure Active Directory-Blade. Met apps die zijn geregistreerd in Azure Active Directory kunnen gebruikers verifiëren en worden gemachtigd om de Azure time series Insight-API te gebruiken die is gekoppeld aan een Azure Time Series Insights omgeving.

## <a name="managed-identities"></a>Beheerde identiteiten

In de volgende secties wordt beschreven hoe u een beheerde identiteit van Azure Active Directory (Azure AD) gebruikt om toegang te krijgen tot de Azure Time Series Insights-API. In Azure elimineren beheerde identiteiten de noodzaak voor ontwikkelaars om referenties te beheren door een identiteit voor de Azure-resource in Azure AD op te geven en deze te gebruiken voor het verkrijgen van Azure Active Directory-tokens (Azure AD). Hier volgen enkele van de voordelen van het gebruik van beheerde identiteiten:

- U hoeft geen referenties te beheren. Referenties zijn voor u zelfs niet toegankelijk.
- U kunt de beheerde identiteiten gebruiken voor verificatie bij alle Azure-services die ondersteuning bieden voor Azure AD-verificatie, inclusief Key Vault.
- Beheerde identiteiten kunnen worden gebruikt zonder extra kosten.

Lees [wat beheerde identiteiten zijn voor Azure-resources](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) voor meer informatie over de twee typen beheerde identiteiten?

U kunt beheerde identiteiten gebruiken in uw:

- Azure-VM's
- Azure App Services
- Azure Functions
- Azure container instances
- en nog veel meer...

Zie [Azure-Services die beheerde identiteiten voor Azure-resources ondersteunen](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-managed-identities-for-azure-resources) voor de volledige lijst.

## <a name="azure-active-directory-app-registration"></a>App-registratie Azure Active Directory

U kunt het beste Managed Identities gebruiken als dat mogelijk is, zodat u geen referenties hoeft te beheren. Als uw client toepassing niet wordt gehost op een Azure-service die beheerde identiteiten ondersteunt, kunt u uw toepassing registreren bij een Azure AD-Tenant. Wanneer u uw toepassing registreert bij Azure AD, maakt u een identiteits configuratie voor uw toepassing waarmee deze kan worden geïntegreerd met Azure AD. Wanneer u een app registreert in de [Azure Portal](https://portal.azure.com/), kiest u of deze één Tenant is (alleen toegankelijk in uw Tenant) of multi tenant (toegankelijk in andere tenants) en kunt u eventueel een omleidings-URI instellen (waarbij het toegangs token wordt verzonden naar).

Wanneer u de app-registratie hebt voltooid, hebt u een wereld wijd unieke instantie van de app (het toepassings object) die zich in uw thuis Tenant of-map bevindt. U hebt ook een wereld wijd unieke ID voor uw app (de app of client-ID). In de portal kunt u geheimen of certificaten en scopes toevoegen om uw app te laten werken, de huis stijl van uw app aan te passen in het dialoog venster voor aanmelden en nog veel meer.

Als u een toepassing registreert in de portal, worden er automatisch een toepassings object en een Service-Principal-object gemaakt in uw thuis Tenant. Als u een toepassing registreert/maakt met behulp van de Microsoft Graph Api's, is het maken van het Service-Principal-object een afzonderlijke stap. Een Service-Principal-object is vereist voor het aanvragen van tokens.

Controleer de [beveiligings](https://docs.microsoft.com/azure/active-directory/develop/identity-platform-integration-checklist#security) Checklist voor uw toepassing. Als best practice moet u [certificaat referenties](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials)gebruiken, niet de wachtwoord referenties (client geheimen).

Zie [toepassings-en Service-Principal-objecten in azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) voor meer informatie.

## <a name="step-1-create-your-managed-identity-or-app-registration"></a>Stap 1: uw beheerde identiteit of app-registratie maken

Zodra u hebt vastgesteld of u een beheerde identiteit of app-registratie wilt gebruiken, is de volgende stap het inrichten van één.

### <a name="managed-identity"></a>Beheerde identiteit

De stappen die u gebruikt voor het maken van een beheerde identiteit variëren, afhankelijk van waar de code zich bevindt en of u nu een door het systeem toegewezen of door de gebruiker toegewezen identiteit maakt. Lees [beheerde identiteits typen](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview#managed-identity-types) om het verschil te begrijpen. Wanneer u het identiteits type hebt geselecteerd, zoekt u de juiste zelf studie in de [documentatie over](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/)door Azure AD beheerde identiteiten. Hier vindt u instructies voor het configureren van beheerde identiteiten voor:

- [Azure-VM's](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#enable-system-assigned-managed-identity-during-creation-of-a-vm)
- [App Service en Azure Functions](https://docs.microsoft.com/azure/app-service/overview-managed-identity)
- [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-managed-identity)
- en nog veel meer...

### <a name="application-registration"></a>Een toepassing registreren

Volg de stappen in [een toepassing registreren](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app#register-an-application).

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="step-2-grant-access"></a>Stap 2: toegang verlenen

Wanneer uw Azure Time Series Insights-omgeving een aanvraag ontvangt, wordt eerst de Bearer-token van de beller gevalideerd. Als de validatie is geslaagd, is de aanroeper geverifieerd en vervolgens wordt er een andere controle uitgevoerd om te controleren of de aanroeper is gemachtigd om de aangevraagde actie uit te voeren. Als u een gebruiker of Service-Principal wilt autoriseren, moet u deze eerst toegang verlenen tot de omgeving door hen de rol lezer of Inzender toe te wijzen.

- Als u toegang wilt verlenen via de [Azure Portal](https://portal.azure.com/) -gebruikers interface, volgt u de instructies in het artikel [granting Data Access to a Environment](https://docs.microsoft.com/azure/time-series-insights/concepts-access-policies) . Wanneer u de gebruiker selecteert, kunt u zoeken naar de beheerde identiteit of de registratie van de app op basis van de naam of de ID.

- Als u toegang wilt verlenen met behulp van de Azure CLI, voert u de volgende opdracht uit. Raadpleeg de documentatie [hier](https://docs.microsoft.com/cli/azure/ext/timeseriesinsights/tsi/access-policy?view=azure-cli-latest) voor de volledige lijst met opdrachten die beschikbaar zijn voor het beheren van de toegang.

   ```azurecli-interactive
   az tsi access-policy create --name "ap1" --environment-name "env1" --description "some description" --principal-object-id "aGuid" --roles Reader Contributor --resource-group "rg1"
   ```

> [!Note]
> Voor de timeseriesinsights-extensie voor Azure CLI is versie 2.11.0 of hoger vereist. De uitbrei ding wordt automatisch geïnstalleerd wanneer u de eerste keer dat u een opdracht AZ TSI Access-Policy uitvoert. Meer [informatie](https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview) over uitbrei dingen.

## <a name="step-3-requesting-tokens"></a>Stap 3: tokens aanvragen

Zodra de beheerde identiteit of app-registratie is ingericht en een rol is toegewezen, bent u klaar om deze te gaan gebruiken om OAuth 2,0 Bearer-tokens aan te vragen. De methode die u gebruikt om een token op te halen, is afhankelijk van waar de code wordt gehost en de taal van uw keuze. Wanneer u de resource opgeeft (ook wel het ' publiek ' genoemd), kunt u Azure Time Series Insights identificeren op basis van de URL of GUID:

* `https://api.timeseries.azure.com/`
* `120d688d-1518-4cf7-bd38-182f158850b6`

> [!IMPORTANT]
> Als u de URL als resource-ID gebruikt, moet het token precies aan worden uitgegeven `https://api.timeseries.azure.com/` . De afsluitende slash is vereist.

> * Als u [postman](https://www.getpostman.com/) gebruikt, is uw **AuthURL** daarom: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com//.default`
> * `https://api.timeseries.azure.com/` is geldig, maar `https://api.timeseries.azure.com` niet.

### <a name="managed-identities"></a>Beheerde identiteiten

Volg de richt lijnen in de [tokens voor het verkrijgen van Azure-resources](https://docs.microsoft.com/azure/app-service/overview-managed-identity)wanneer u toegang hebt tot Azure app service of functies.

> [!TIP]
> Voor .NET-toepassingen en-functies is de eenvoudigste manier om te werken met een beheerde identiteit via de [Azure Identity client-bibliotheek](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme) voor .net. 

Voor .NET-toepassingen en-functies is de eenvoudigste manier om te werken met een beheerde identiteit via het pakket micro soft. Azure. Services. AppAuthentication. Dit pakket is populair vanwege de eenvoud en beveiligings voordelen. Ontwikkel aars kunnen code eenmaal schrijven en de client bibliotheek laten bepalen hoe verificatie wordt uitgevoerd op basis van de toepassings omgeving-of op een ontwikkel werkstation met behulp van een ontwikkelaars account of geïmplementeerd in azure met behulp van een beheerde service-identiteit. Voor migratie richtlijnen vanuit de voorafgaande AppAuthentication-bibliotheek Lees [AppAuthentication naar Azure. richt lijnen voor identiteits migratie](https://docs.microsoft.com/dotnet/api/overview/azure/app-auth-migration?view=azure-dotnet).

Een token aanvragen voor Azure Time Series Insights met behulp van C# en de Azure Identity client-bibliotheek voor .NET:

    ```csharp
    using Azure.Identity;
    // ...
    var credential = new DefaultAzureCredential();
    var token = credential.GetToken(
    new Azure.Core.TokenRequestContext(
        new[] { "https://api.timeseries.azure.com/" }));
   var accessToken = token. Token
    ```

### <a name="app-registration"></a>App-registratie

* Ontwikkel aars kunnen gebruikmaken van de [micro soft Authentication Library](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) (MSAL) om tokens voor app-registraties te verkrijgen.

MSAL kan worden gebruikt in veel toepassings scenario's, waaronder, maar niet beperkt tot:

* [Toepassingen met één pagina (Java script)](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-overview.md)
* [Webtoepassing voor het aanmelden bij een gebruiker en het aanroepen van een web-API namens de gebruiker](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-overview.md)
* [Web-API die een andere stroomafwaartse Web-API aanroept namens de aangemelde gebruiker](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-overview.md)
* [Bureaublad toepassing die een web-API aanroept namens de aangemelde gebruiker](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-overview.md)
* [Mobiele toepassing die een web-API aanroept namens de gebruiker die zich interactief aanmeldt](https://docs.microsoft.com/azure/active-directory/develop/scenario-mobile-overview.md).
* [Desktop/service daemon-toepassing die een web-API aanroept namens zichzelf](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-overview.md)

Voor voorbeeld code van C# die laat zien hoe u een token kunt verkrijgen als app-registratie en query's kunt uitvoeren vanuit een Gen2-omgeving, bekijkt u de voor beeld-app op [github](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/Program.cs)

> [!IMPORTANT]
> Als u [Azure Active Directory Authentication Library (ADAL) gebruikt,](../active-directory/azuread-dev/active-directory-authentication-libraries.md) lees dan over [het migreren naar MSAL](../active-directory/develop/msal-net-migration.md).

## <a name="common-headers-and-parameters"></a>Algemene kopteksten en para meters

In deze sectie worden algemene HTTP-aanvraag headers en-para meters beschreven die worden gebruikt om query's uit te voeren op de Azure Time Series Insights gen1-en Gen2-Api's. API-specifieke vereisten worden uitgebreid beschreven in de documentatie over het [Azure Time Series Insights rest API](/rest/api/time-series-insights/).

> [!TIP]
> Lees de [Referentie van Azure rest API](/rest/api/azure/) voor meer informatie over het gebruik van rest-api's, het maken van HTTP-aanvragen en het afhandelen van http-antwoorden.

### <a name="http-headers"></a>HTTP-headers

De vereiste aanvraag headers worden hieronder beschreven.

| Header vereiste aanvraag | Beschrijving |
| --- | --- |
| Autorisatie | Als u wilt verifiëren met Azure Time Series Insights, moet een geldig OAuth 2,0 Bearer-token worden door gegeven in de [autorisatie-header](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate). |

> [!TIP]
> Lees de voor [beeld-visualisatie](https://tsiclientsample.azurewebsites.net/) van de gehoste Azure time series Insights client-SDK voor meer informatie over de verificatie met de Azure time series Insights api's programmatisch met behulp van de [Java script-client-SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) samen met grafieken en grafieken.

De optionele aanvraag headers worden hieronder beschreven.

| Optionele aanvraagheader | Beschrijving |
| --- | --- |
| Inhouds type | alleen `application/json` wordt ondersteund. |
| x-MS-Client-Request-id | Een client aanvraag-ID. Deze waarde wordt vastgelegd door de service. Hiermee kan de service bewerkingen volgen tussen services. |
| x-MS-client-sessie-id | Een client sessie-ID. Deze waarde wordt vastgelegd door de service. Hiermee kan de service een groep gerelateerde bewerkingen in verschillende services traceren. |
| x-MS-Client-toepassings naam | De naam van de toepassing die deze aanvraag heeft gegenereerd. Deze waarde wordt vastgelegd door de service. |

Optioneel, maar aanbevolen antwoord headers worden hieronder beschreven.

| Reactie header | Beschrijving |
| --- | --- |
| Inhouds type | Alleen `application/json` wordt ondersteund. |
| x-MS-Request-id | Door de server gegenereerde aanvraag-ID. Kan worden gebruikt om contact op te nemen met micro soft om een aanvraag te onderzoeken. |
| x-MS-eigenschap-niet gevonden-gedrag | Kop van optionele API-reactie. Mogelijke waarden zijn `ThrowError` (standaard) of `UseNull` . |

### <a name="http-parameters"></a>HTTP-para meters

> [!TIP]
> Meer informatie over vereiste en optionele query gegevens vindt u in de [referentie documentatie](/rest/api/time-series-insights/).

De vereiste URL-query teken reeks parameters zijn afhankelijk van de API-versie.

| Release | API-versie waarden |
| --- |  --- |
| Gen1 | `api-version=2016-12-12`|
| Gen2 | `api-version=2020-07-31`|

Optionele URL-query teken reeks parameters zijn onder andere het instellen van een time-out voor het uitvoeren van HTTP-aanvragen.

| Optionele query parameter | Beschrijving | Versie |
| --- |  --- | --- |
| `timeout=<timeout>` | Server-side-time-out voor het uitvoeren van HTTP-aanvragen. Alleen van toepassing op de api's [Get Environment Events](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) en [Get Environment aggregaties](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api) . De time-outwaarde moet de ISO 8601-duur notatie hebben, bijvoorbeeld `"PT20S"` en moet in het bereik liggen `1-30 s` . De standaardwaarde is `30 s`. | Gen1 |
| `storeType=<storeType>` | Voor Gen2 omgevingen waarin warme opslag is ingeschakeld, kan de query worden uitgevoerd op de `WarmStore` of `ColdStore` . Met deze para meter in de query wordt gedefinieerd in welk archief de query moet worden uitgevoerd. Als deze niet is gedefinieerd, wordt de query uitgevoerd in de koude opslag. Als u een query wilt uitvoeren voor de warme Store, moet **storeType** worden ingesteld op `WarmStore` . Als deze niet is gedefinieerd, wordt de query uitgevoerd op basis van de koude opslag. | Gen2 |

## <a name="next-steps"></a>Volgende stappen

* Lees [query gen1-gegevens met C#](./time-series-insights-query-data-csharp.md)voor voorbeeld code die de gen1 Azure time series INSIGHTS-API aanroept.

* Lees [query Gen2-gegevens met C#](./time-series-insights-update-query-data-csharp.md)voor voorbeeld code voor het aanroepen van de GEN2 Azure time series Insights API-code voorbeelden.

* Lees de naslag documentatie over de [query-API](/rest/api/time-series-insights/reference-query-apis) voor naslag informatie over de API.