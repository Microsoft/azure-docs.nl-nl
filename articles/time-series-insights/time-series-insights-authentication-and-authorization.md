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
ms.date: 10/02/2020
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: d1bd3c5796658663b6111723829cbe620346002c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016238"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Verificatie en autorisatie voor Azure Time Series Insights-API

In dit document wordt beschreven hoe u een app in Azure Active Directory kunt registreren met behulp van de nieuwe Azure Active Directory-Blade. Met apps die zijn geregistreerd in Azure Active Directory kunnen gebruikers verifiëren en worden gemachtigd om de Azure time series Insight-API te gebruiken die is gekoppeld aan een Azure Time Series Insights omgeving.

## <a name="service-principal"></a>Service-principal

In de volgende secties wordt beschreven hoe u een toepassing kunt configureren voor toegang tot de Azure Time Series Insights-API namens een app. De toepassing kan vervolgens met behulp van de referenties van de eigen toepassing een query uitvoeren op of publiceren naar referentie gegevens in de Azure Time Series Insights omgeving via Azure Active Directory.

## <a name="summary-and-best-practices"></a>Samen vatting en aanbevolen procedures

De registratie stroom van de Azure Active Directory-app bestaat uit drie belang rijke stappen.

1. [Een toepassing registreren](#azure-active-directory-app-registration) in azure Active Directory.
1. De toepassing machtigen om [gegevens toegang te geven tot de Azure time series Insights omgeving](#granting-data-access).
1. Gebruik de **toepassings-id** en het **client geheim** om een token te verkrijgen `https://api.timeseries.azure.com/` in uw [client-app](#client-app-initialization). Het token kan vervolgens worden gebruikt om de Azure Time Series Insights-API aan te roepen.

In het volgende voor **stap 3** kunt u met behulp van de referenties van uw toepassing en uw gebruikers gegevens:

* Wijs machtigingen toe aan de app-identiteit die verschilt van uw eigen machtigingen. Deze machtigingen zijn doorgaans beperkt tot wat de app nodig heeft. U kunt bijvoorbeeld toestaan dat de app alleen gegevens uit een bepaalde Azure Time Series Insights omgeving kan lezen.
* De beveiliging van de app isoleren van de verificatie referenties van de gebruiker met behulp van een **client geheim** of beveiligings certificaat. Als gevolg hiervan zijn de referenties van de toepassing niet afhankelijk van de referenties van een specifieke gebruiker. Als de rol van de gebruiker wordt gewijzigd, is voor de toepassing niet noodzakelijkerwijs nieuwe referenties of verdere configuratie vereist. Als de gebruiker het wacht woord wijzigt, zijn geen nieuwe referenties of sleutels vereist voor toegang tot de toepassing.
* Voer een script zonder toezicht uit met behulp van een **client geheim** of beveiligings certificaat in plaats van een specifieke gebruikers referenties (vereist dat ze aanwezig zijn).
* Gebruik een beveiligings certificaat in plaats van een wacht woord om de toegang tot uw Azure Time Series Insights-API te beveiligen.

> [!IMPORTANT]
> Volg het principe van de **schei ding van de problemen** (beschreven in dit scenario hierboven) bij het configureren van uw Azure time series Insights-beveiligings beleid.

> [!NOTE]

> * Het artikel richt zich op een toepassing met één Tenant waarbij de toepassing in slechts één organisatie is bedoeld om te worden uitgevoerd.
> * Normaal gesp roken gebruikt u toepassingen met één Tenant voor line-of-business-toepassingen die worden uitgevoerd in uw organisatie.

## <a name="detailed-setup"></a>Gedetailleerde installatie

### <a name="azure-active-directory-app-registration"></a>App-registratie Azure Active Directory

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Toegang verlenen tot gegevens

1. Selecteer voor de Azure Time Series Insights omgeving **Data Access policies** en selecteer **toevoegen**.

   [![Nieuw beleid voor gegevens toegang toevoegen aan de Azure Time Series Insights omgeving](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. Plak in het dialoog venster **gebruiker selecteren** de naam van de **toepassing** of de **toepassings-id** in het gedeelte Azure Active Directory app-registratie.

   [![Een toepassing zoeken in het dialoog venster gebruiker selecteren](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Selecteer de rol. Selecteer **lezer** om gegevens op te vragen of **Inzender** om gegevens op te vragen en referentie gegevens te wijzigen. Selecteer **OK**.

   [![Lezer of Inzender kiezen in het dialoog venster gebruikersrol selecteren](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Sla het beleid op door **OK** te selecteren.

   > [!TIP]
   > Lees [granting Data Access](./concepts-access-policies.md)(geavanceerde opties voor gegevens toegang).

### <a name="client-app-initialization"></a>Initialisatie van client-app

* Ontwikkel aars kunnen de [micro soft Authentication Library (MSAL) gebruiken om te verifiëren met Azure Time Series Insights.

* Verificatie met behulp van MSAL:

   1. Gebruik de **toepassings-id** en het **client geheim** (toepassings sleutel) uit de sectie app-registratie van Azure Active Directory om het token namens de toepassing te verkrijgen.

   1. In C# kan de volgende code het token namens de toepassing verkrijgen. Lees [query gegevens met C#](time-series-insights-query-data-csharp.md)voor een volledig voor beeld over het opvragen van gegevens uit een gen1-omgeving.

        Zie de [Azure time series Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen1-sample/csharp-tsi-gen1-sample/Program.cs)] opslag plaats voor toegang tot de C#-code.

   1. Het token kan vervolgens worden door gegeven in de `Authorization` header wanneer de toepassing de Azure time series Insights-API aanroept.

> [!IMPORTANT]
> Als u [Azure Active Directory Authentication Library (ADAL) gebruikt,](../active-directory/azuread-dev/active-directory-authentication-libraries.md) lees dan over [het migreren naar MSAL](../active-directory/develop/msal-net-migration.md).

## <a name="common-headers-and-parameters"></a>Algemene kopteksten en para meters

In deze sectie worden algemene HTTP-aanvraag headers en-para meters beschreven die worden gebruikt om query's uit te voeren op de Azure Time Series Insights gen1-en Gen2-Api's. API-specifieke vereisten worden uitgebreid beschreven in de documentatie over het [Azure Time Series Insights rest API](/rest/api/time-series-insights/).

> [!TIP]
> Lees de [Referentie van Azure rest API](/rest/api/azure/) voor meer informatie over het gebruik van rest-api's, het maken van HTTP-aanvragen en het afhandelen van http-antwoorden.

### <a name="authentication"></a>Verificatie

Als u geverifieerde query's wilt uitvoeren op de [Azure time series INSIGHTS rest api's](/rest/api/time-series-insights/), moet er een geldig OAuth 2,0 Bearer-token worden door gegeven in de [autorisatie-header](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate) met behulp van een rest-client van uw keuze (Postman, java script, C#).

> [!TIP]
> Lees de voor [beeld-visualisatie](https://tsiclientsample.azurewebsites.net/) van de gehoste Azure time series Insights client-SDK voor meer informatie over de verificatie met de Azure time series Insights api's programmatisch met behulp van de [Java script-client-SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) samen met grafieken en grafieken.

### <a name="http-headers"></a>HTTP-headers

De vereiste aanvraag headers worden hieronder beschreven.

| Header vereiste aanvraag | Description |
| --- | --- |
| Autorisatie | Als u wilt verifiëren met Azure Time Series Insights, moet een geldig OAuth 2,0 Bearer-token worden door gegeven in de **autorisatie** -header. |

> [!IMPORTANT]
> Het token moet exact worden uitgegeven aan de `https://api.timeseries.azure.com/` resource (ook wel bekend als het ' publiek ' van het token).

> * Uw [postman](https://www.getpostman.com/) **AuthURL** is daarom: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com//.default`
> * `https://api.timeseries.azure.com/` is geldig, maar `https://api.timeseries.azure.com` niet.

De optionele aanvraag headers worden hieronder beschreven.

| Optionele aanvraagheader | Description |
| --- | --- |
| Inhouds type | alleen `application/json` wordt ondersteund. |
| x-MS-Client-Request-id | Een client aanvraag-ID. Deze waarde wordt vastgelegd door de service. Hiermee kan de service bewerkingen volgen tussen services. |
| x-MS-client-sessie-id | Een client sessie-ID. Deze waarde wordt vastgelegd door de service. Hiermee kan de service een groep gerelateerde bewerkingen in verschillende services traceren. |
| x-MS-Client-toepassings naam | De naam van de toepassing die deze aanvraag heeft gegenereerd. Deze waarde wordt vastgelegd door de service. |

Optioneel, maar aanbevolen antwoord headers worden hieronder beschreven.

| Reactie header | Description |
| --- | --- |
| Inhouds type | Alleen `application/json` wordt ondersteund. |
| x-MS-Request-id | Door de server gegenereerde aanvraag-ID. Kan worden gebruikt om contact op te nemen met micro soft om een aanvraag te onderzoeken. |
| x-MS-eigenschap-niet gevonden-gedrag | Kop van optionele API-reactie. Mogelijke waarden zijn `ThrowError` (standaard) of `UseNull` . |

### <a name="http-parameters"></a>HTTP-para meters

> [!TIP]
> Meer informatie over vereiste en optionele query gegevens vindt u in de [referentie documentatie](/rest/api/time-series-insights/).

De vereiste URL-query teken reeks parameters zijn afhankelijk van de API-versie.

| Release | Mogelijke API-versie waarden |
| --- |  --- |
| Gen1 | `api-version=2016-12-12`|
| Gen2 | `api-version=2020-07-31` en `api-version=2018-11-01-preview`|

> [!IMPORTANT]
>
> De `api-version=2018-11-01-preview` versie zal binnenkort worden afgeschaft. Gebruikers wordt aangeraden om over te scha kelen naar de nieuwere versie.

Optionele URL-query teken reeks parameters zijn onder andere het instellen van een time-out voor het uitvoeren van HTTP-aanvragen.

| Optionele query parameter | Beschrijving | Versie |
| --- |  --- | --- |
| `timeout=<timeout>` | Server-side-time-out voor het uitvoeren van HTTP-aanvragen. Alleen van toepassing op de api's [Get Environment Events](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) en [Get Environment aggregaties](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api) . De time-outwaarde moet de ISO 8601-duur notatie hebben, bijvoorbeeld `"PT20S"` en moet in het bereik liggen `1-30 s` . De standaardwaarde is `30 s`. | Gen1 |
| `storeType=<storeType>` | Voor Gen2 omgevingen waarin warme opslag is ingeschakeld, kan de query worden uitgevoerd op de `WarmStore` of `ColdStore` . Met deze para meter in de query wordt gedefinieerd in welk archief de query moet worden uitgevoerd. Als deze niet is gedefinieerd, wordt de query uitgevoerd in de koude opslag. Als u een query wilt uitvoeren voor de warme Store, moet **storeType** worden ingesteld op `WarmStore` . Als deze niet is gedefinieerd, wordt de query uitgevoerd op basis van de koude opslag. | Gen2 |

## <a name="next-steps"></a>Volgende stappen

* Lees [query gen1-gegevens met C#](./time-series-insights-query-data-csharp.md)voor voorbeeld code die de gen1 Azure time series INSIGHTS-API aanroept.

* Lees [query Gen2-gegevens met C#](./time-series-insights-update-query-data-csharp.md)voor voorbeeld code voor het aanroepen van de GEN2 Azure time series Insights API-code voorbeelden.

* Lees de naslag documentatie over de [query-API](/rest/api/time-series-insights/gen1-query-api) voor naslag informatie over de API.

* Meer informatie over het [maken van een Service-Principal](../active-directory/develop/howto-create-service-principal-portal.md).