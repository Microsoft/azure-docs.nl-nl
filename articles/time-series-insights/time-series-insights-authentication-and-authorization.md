---
title: API-verificatie en -autorisatie - Azure Time Series Insights | Microsoft Docs
description: In dit artikel wordt beschreven hoe u verificatie en autorisatie configureert voor een aangepaste toepassing die de api Azure Time Series Insights aanroept.
ms.service: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/23/2021
ms.custom: seodec18, has-adal-ref, devx-track-azurecli
ms.openlocfilehash: 8e50b650eaffe3d0ec8d3d2cd1841bd139d33750
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867508"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Verificatie en autorisatie voor Azure Time Series Insights-API

Afhankelijk van uw bedrijfsbehoeften kan uw oplossing een of meer clienttoepassingen bevatten die u [](/rest/api/time-series-insights/reference-data-access-overview)gebruikt om te communiceren met de API's Azure Time Series Insights uw omgeving. Azure Time Series Insights voert verificatie uit met [behulp van Azure AD-beveiligingstokens op basis van OAUTH 2.0.](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) Als u uw client(s) wilt verifiëren, moet u een bearer-token met de juiste machtigingen krijgen en dit doorgeven samen met uw API-aanroepen. In dit document worden verschillende methoden beschreven voor het verkrijgen van referenties die u kunt gebruiken om een bearer-token op te halen en te verifiëren, waaronder het gebruik van beheerde identiteit en Azure Active Directory app-registratie.

## <a name="managed-identities"></a>Beheerde identiteiten

In de volgende secties wordt beschreven hoe u een beheerde identiteit van Azure Active Directory (Azure AD) gebruikt om toegang te krijgen tot Azure Time Series Insights API. In Azure elimineren beheerde identiteiten de noodzaak voor ontwikkelaars om referenties te beheren door een identiteit voor de Azure-resource in Azure AD op te geven en deze te gebruiken voor het verkrijgen van Azure Active Directory-tokens (Azure AD). Hier volgen enkele van de voordelen van het gebruik van beheerde identiteiten:

- U hoeft geen referenties te beheren. Referenties zijn voor u zelfs niet toegankelijk.
- U kunt de beheerde identiteiten gebruiken voor verificatie bij alle Azure-services die ondersteuning bieden voor Azure AD-verificatie, inclusief Key Vault.
- Beheerde identiteiten kunnen worden gebruikt zonder extra kosten.

Lees Wat zijn beheerde identiteiten voor Azure-resources? voor meer informatie over de [twee typen beheerde identiteiten.](../active-directory/managed-identities-azure-resources/overview.md)

U kunt beheerde identiteiten gebruiken van uw:

- Azure-VM's
- Azure App Services
- Azure Functions
- Azure Container Instances
- en meer...

Zie [Azure-services die beheerde identiteiten voor Azure-resources ondersteunen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) voor de volledige lijst.

## <a name="azure-active-directory-app-registration"></a>Azure Active Directory-app registreren

U wordt aangeraden waar mogelijk beheerde identiteiten te gebruiken, zodat u referenties niet hoeft te beheren. Als uw clienttoepassing niet wordt gehost op een Azure-service die beheerde identiteiten ondersteunt, kunt u uw toepassing registreren bij een Azure AD-tenant. Wanneer u uw toepassing registreert bij Azure AD, maakt u een identiteitsconfiguratie voor uw toepassing waarmee deze kan worden geïntegreerd met Azure AD. Wanneer u een app registreert in de Azure Portal , kiest u of het een enkele tenant is (alleen toegankelijk in uw tenant) [of](https://portal.azure.com/)meerdere tenants (toegankelijk in andere tenants) en kunt u desgewenst een omleidings-URI instellen (waar het toegangsken naar wordt verzonden).

Wanneer u de registratie van de app hebt voltooid, hebt u een wereldwijd uniek exemplaar van de app (het toepassingsobject) dat zich in uw basis-tenant of -directory behuist. U hebt ook een wereldwijd unieke id voor uw app (de app of client-id). In de portal kunt u vervolgens geheimen of certificaten en scopes toevoegen om uw app te laten werken, de huisstijl van uw app aan te passen in het aanmeldingsdialoogvenster en meer.

Als u een toepassing registreert in de portal, worden er automatisch een toepassingsobject en een service-principal-object gemaakt in uw basis-tenant. Als u een toepassing registreert/maakt met behulp van Microsoft Graph API's, is het maken van het service-principal-object een afzonderlijke stap. Een service-principal-object is vereist om tokens aan te vragen.

Controleer de controlelijst [voor beveiliging voor](../active-directory/develop/identity-platform-integration-checklist.md#security) uw toepassing. Als best practice moet u [certificaatreferenties gebruiken,](../active-directory/develop/active-directory-certificate-credentials.md)geen wachtwoordreferenties (clientgeheimen).

Zie [Toepassings- en service-principalobjecten in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md) voor meer informatie.

## <a name="step-1-create-your-managed-identity-or-app-registration"></a>Stap 1: uw beheerde identiteit of app-registratie maken

Zodra u hebt vastgesteld of u een beheerde identiteit of app-registratie gebruikt, is de volgende stap het inrichten van een identiteit.

### <a name="managed-identity"></a>Beheerde identiteit

De stappen die u gebruikt om een beheerde identiteit te maken, variëren afhankelijk van waar uw code zich bevindt en of u een door het systeem toegewezen of door de gebruiker toegewezen identiteit maakt. Lees [Beheerde identiteitstypen om](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) het verschil te begrijpen. Nadat u uw identiteitstype hebt geselecteerd, zoekt en volgt u de juiste zelfstudie in de documentatie voor door Azure AD beheerde [identiteiten.](../active-directory/managed-identities-azure-resources/index.yml) Hier vindt u instructies voor het configureren van beheerde identiteiten voor:

- [Azure-VM's](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-during-creation-of-a-vm)
- [App Service en Azure Functions](../app-service/overview-managed-identity.md)
- [Azure Container Instances](../container-instances/container-instances-managed-identity.md)
- en meer ...

### <a name="application-registration"></a>Een toepassing registreren

Volg de stappen in [Een toepassing registreren.](../active-directory/develop/quickstart-register-app.md#register-an-application)

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="step-2-grant-access"></a>Stap 2: Toegang verlenen

Wanneer uw Azure Time Series Insights een aanvraag ontvangt, wordt eerst het bearer-token van de aanroeper gevalideerd. Als de validatie is uitgevoerd, is de aanroeper geverifieerd en wordt er een andere controle uitgevoerd om ervoor te zorgen dat de aanroeper is gemachtigd om de aangevraagde actie uit te voeren. Als u een gebruiker of service-principal wilt machtigen, moet u deze eerst toegang verlenen tot de omgeving door deze de rol Lezer of Inzender toe te wijzen.

- Als u toegang wilt verlenen via [Azure Portal](https://portal.azure.com/) gebruikersinterface, volgt u de instructies in het artikel [Gegevens toegang verlenen tot een omgeving.](concepts-access-policies.md) Wanneer u de gebruiker selecteert, kunt u zoeken naar de beheerde identiteit of app-registratie op naam of id.

- Voer de volgende opdracht uit om toegang te verlenen met behulp van de Azure CLI. Bekijk hier de documentatie [voor](/cli/azure/tsi/access-policy) de volledige lijst met opdrachten die beschikbaar zijn voor het beheren van toegang.

   ```azurecli-interactive
   az tsi access-policy create --name "ap1" --environment-name "env1" --description "some description" --principal-object-id "aGuid" --roles Reader Contributor --resource-group "rg1"
   ```

> [!Note]
> Voor de timeseriesinsights-extensie voor Azure CLI is versie 2.11.0 of hoger vereist. De extensie wordt automatisch geïnstalleerd wanneer u de eerste keer een opdracht az tsi access-policy hebt uitgevoerd. [Meer informatie over](/cli/azure/azure-cli-extensions-overview) extensies.

## <a name="step-3-requesting-tokens"></a>Stap 3: Tokens aanvragen

Zodra uw beheerde identiteit of app-registratie is ingericht en een rol is toegewezen, kunt u deze gaan gebruiken om OAuth 2.0-bearer-tokens aan te vragen. De methode die u gebruikt om een token te verkrijgen, verschilt, afhankelijk van waar uw code wordt gehost en de taal van uw keuze. Wanneer u de resource opgeeft (ook wel bekend als de 'doelgroep' van het token), kunt u de Azure Time Series Insights door de URL of GUID:

* `https://api.timeseries.azure.com/`
* `120d688d-1518-4cf7-bd38-182f158850b6`

> [!IMPORTANT]
> Als u de URL als de resource-id gebruikt, moet het token exact worden uitgegeven aan `https://api.timeseries.azure.com/` . De schuine streep is vereist.

> * Als u [Postman gebruikt,](https://www.getpostman.com/) is **uw AuthURL** daarom: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com//.default`
> * `https://api.timeseries.azure.com/` is geldig, `https://api.timeseries.azure.com` maar niet.

### <a name="managed-identities"></a>Beheerde identiteiten

Volg bij het openen Azure App Service of Functions de richtlijnen in [Tokens voor Azure-resources verkrijgen.](../app-service/overview-managed-identity.md)

Voor .NET-toepassingen en -functies is de eenvoudigste manier om met een beheerde identiteit te werken via de [Azure Identity-clientbibliotheek](/dotnet/api/overview/azure/identity-readme) voor .NET. Deze clientbibliotheek is populair vanwege de eenvoud en de beveiligingsvoordelen. Ontwikkelaars kunnen één keer code schrijven en de clientbibliotheek laten bepalen hoe verificatie moet worden uitgevoerd op basis van de toepassingsomgeving, of dit nu op een ontwikkelaarswerkstation is met behulp van een ontwikkelaarsaccount of in Azure is geïmplementeerd met behulp van een beheerde service-identiteit. Lees AppAuthentication to Azure.Identity Migration Guidance (Richtlijnen voor migratie van [AppAuthentication naar Azure.Identity Migration Guidance)](/dotnet/api/overview/azure/app-auth-migration)voor hulp bij de migratie van de voorganger AppAuthentication-bibliotheek.

Vraag een token aan voor Azure Time Series Insights C# en de Azure Identity-clientbibliotheek voor .NET:

   ```csharp
   using Azure.Identity;
   // ...
   var credential = new DefaultAzureCredential();
   var token = credential.GetToken(
   new Azure.Core.TokenRequestContext(
       new[] { "https://api.timeseries.azure.com/" }));
   var accessToken = token.Token;
   ```

### <a name="app-registration"></a>App-registratie

* Ontwikkelaars kunnen de [Microsoft Authentication Library](../active-directory/develop/msal-overview.md) (MSAL) gebruiken om tokens voor app-registraties te verkrijgen.

MSAL kan worden gebruikt in veel toepassingsscenario's, waaronder, maar niet beperkt tot:

* [Toepassingen met één pagina (JavaScript)](../active-directory/develop/scenario-spa-overview.md)
* [Webtoepassing voor het aanmelden van een gebruiker en het aanroepen van een web-API namens de gebruiker](../active-directory/develop/scenario-web-app-call-api-overview.md)
* [Web-API die een andere downstream web-API aanroept namens de aangemelde gebruiker](../active-directory/develop/scenario-web-api-call-api-overview.md)
* [Bureaubladtoepassing die een web-API aanroept namens de aangemelde gebruiker](../active-directory/develop/scenario-desktop-overview.md)
* [Mobiele toepassing die een web-API aanroept namens de gebruiker die](../active-directory/develop/scenario-mobile-overview.md)interactief is aangemeld.
* [Desktop-/service daemon-toepassing die web-API namens zichzelf aanroept](../active-directory/develop/scenario-daemon-overview.md)

Voor C#-voorbeeldcode die laat zien hoe u een token kunt verkrijgen als een app-registratie en gegevens opvraagt uit een Gen2-omgeving, bekijkt u de voorbeeld-app op [GitHub](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/Program.cs)

> [!IMPORTANT]
> Als u gebruik maakt [Azure Active Directory Authentication Library (ADAL)](../active-directory/azuread-dev/active-directory-authentication-libraries.md) lees dan [over migreren naar MSAL.](../active-directory/develop/msal-net-migration.md)

## <a name="common-headers-and-parameters"></a>Algemene headers en parameters

In deze sectie worden veelgebruikte HTTP-aanvraagheaders en -parameters beschreven die worden gebruikt om query's uit te Azure Time Series Insights gen1- en Gen2-API's. API-specifieke vereisten worden uitvoeriger behandeld in de Azure Time Series Insights REST API [referentiedocumentatie](/rest/api/time-series-insights/).

> [!TIP]
> Lees de [Naslag voor Azure REST API voor](/rest/api/azure/) meer informatie over het gebruik van REST API's, het maken van HTTP-aanvragen en het verwerken van HTTP-antwoorden.

### <a name="http-headers"></a>HTTP-headers

Vereiste aanvraagheaders worden hieronder beschreven.

| Vereiste aanvraagheader | Description |
| --- | --- |
| Autorisatie | Voor verificatie met Azure Time Series Insights moet een geldig OAuth 2.0 Bearer-token worden doorgegeven in de [autorisatie-header](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate). |

> [!TIP]
> Lees de voorbeeldvisualisatie van de [SDK](https://tsiclientsample.azurewebsites.net/) voor de gehoste Azure Time Series Insights-client voor meer informatie over het programmatisch verifiëren met de Azure Time Series Insights-API's met behulp van de [JavaScript Client SDK,](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) samen met grafieken en grafieken.

Optionele aanvraagheaders worden hieronder beschreven.

| Optionele aanvraagheader | Description |
| --- | --- |
| Inhoudstype | wordt `application/json` alleen ondersteund. |
| x-ms-client-request-id | Een clientaanvraag-id. De service registreert deze waarde. Hiermee kan de service de werking van services traceren. |
| x-ms-client-session-id | Een clientsessie-id. De service registreert deze waarde. Hiermee kan de service een groep gerelateerde bewerkingen in services traceren. |
| x-ms-client-application-name | Naam van de toepassing die deze aanvraag heeft gegenereerd. De service registreert deze waarde. |

Optionele maar aanbevolen antwoordheaders worden hieronder beschreven.

| Antwoordheader | Description |
| --- | --- |
| Inhoudstype | Alleen `application/json` wordt ondersteund. |
| x-ms-request-id | Door de server gegenereerde aanvraag-id. Kan worden gebruikt om contact op te nemen met Microsoft om een aanvraag te onderzoeken. |
| x-ms-property-not-found-behavior | Ga-API optionele antwoordheader. Mogelijke waarden zijn `ThrowError` (standaard) of `UseNull` . |

### <a name="http-parameters"></a>HTTP-parameters

> [!TIP]
> Meer informatie over vereiste en optionele querygegevens vindt u in de [referentiedocumentatie](/rest/api/time-series-insights/).

Vereiste url-queryreeksparameters zijn afhankelijk van de API-versie.

| Release | API-versiewaarden |
| --- |  --- |
| Gen1 | `api-version=2016-12-12`|
| Gen2 | `api-version=2020-07-31`|

Optionele url-queryreeksparameters omvatten het instellen van een time-out voor uitvoeringstijden van HTTP-aanvragen.

| Optionele queryparameter | Beschrijving | Versie |
| --- |  --- | --- |
| `timeout=<timeout>` | Time-out aan serverzijde voor het uitvoeren van HTTP-aanvragen. Alleen van toepassing op de [API's](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) [Omgevingsgebeurtenissen op halen en Omgevingsaggregaten](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api) op halen. De time-outwaarde moet bijvoorbeeld de ISO 8601-duurnotatie hebben en `"PT20S"` moet binnen het bereik `1-30 s` zijn. De standaardwaarde is `30 s`. | Gen1 |
| `storeType=<storeType>` | Voor Gen2-omgevingen waarin warm-opslag is ingeschakeld, kan de query worden uitgevoerd op `WarmStore` de of `ColdStore` . Deze parameter in de query definieert op welke opslag de query moet worden uitgevoerd. Als deze niet is gedefinieerd, wordt de query uitgevoerd in de koude opslag. Als u een query wilt uitvoeren op de warme opslag, moet **storeType** worden ingesteld op `WarmStore` . Als deze niet is gedefinieerd, wordt de query uitgevoerd op de koude opslag. | Gen2 |

## <a name="next-steps"></a>Volgende stappen

* Lees Query Gen1-gegevens met C# voor voorbeeldcode die de Gen1 Azure Time Series Insights API [aanroept.](./time-series-insights-query-data-csharp.md)

* Lees Query Gen2 data using C# (Gen2-gegevens opvragen met C# ) voor voorbeeldcode die de codevoorbeelden van de Gen2 Azure Time Series Insights API [aanroept.](./time-series-insights-update-query-data-csharp.md)

* Lees de naslagdocumentatie over de [Query-API voor api-naslaginformatie.](/rest/api/time-series-insights/reference-query-apis)
