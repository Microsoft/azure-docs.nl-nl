---
title: Controle lijst voor migratie van v2 naar v3 Media Services
description: Dit artikel is een controle lijst die u de minimale migratie van Azure Media Services versie v2 naar v3 helpt.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: Azure Media Services, migratie, stream, broadcast, Live, SDK
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2021
ms.author: inhenkel
ms.openlocfilehash: bd1488a1e89bb7d5c8a3a2dedda60bd5a226e02e
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898253"
---
# <a name="step-3---set-up-to-migrate-to-the-v3-rest-api-or-client-sdk"></a>Stap 3: instellen om te migreren naar de V3-REST API of client-SDK

![logo van de migratie handleiding](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![migratie stappen 2](./media/migration-guide/steps-3.svg)

Hieronder worden de stappen beschreven die u moet uitvoeren om uw omgeving in te stellen voor het gebruik van de Media Services v3 API.

## <a name="sdk-model"></a>SDK-model

In de v2-API waren er twee verschillende client-Sdk's, één voor de beheer-API, waarvoor het programmatisch maken van accounts en een voor resource beheer is toegestaan.

Voorheen werken ontwikkel aars met een Azure Service Principal-client-ID en client geheim, samen met een specifiek v2 REST API-eind punt voor hun AMS-account.

De V3 API is Azure Resource Management (ARM) op basis van. De Service-Principal-Id's en-sleutels van Azure Active Directory (Azure AD) worden gebruikt om verbinding te maken met de API. Ontwikkel aars moeten service-principals of beheerde identiteiten maken om verbinding maken met de API. In de V3 API gebruikt de API standaard ARM-eind punten, maakt gebruik van een vergelijkbaar en consistent model voor alle andere Azure-Services.

Klanten die voorheen de 2015-10-01-versie van de ARM-beheer-API voor het beheer van hun v2-accounts gebruiken, moeten de 2020-05-01-versie van de ARM-beheer-API gebruiken die wordt ondersteund voor v3 API-toegang.

## <a name="create-a-new-media-services-account-for-testing"></a>Een nieuw Media Services-account maken voor test doeleinden

Volg de Quick Start-stappen voor het [instellen van uw omgeving](how-to-set-azure-subscription.md?tabs=portal) met behulp van de Azure Portal. Selecteer API-toegang en Service-Principal-verificatie om een nieuwe Azure AD-toepassings-ID en geheimen te genereren voor gebruik met dit test account.

[Maak een Media Services-account](create-account-howto.md?tabs=portal).
[Referenties voor toegang tot Media Services-API ophalen](access-api-howto.md?tabs=portal).

## <a name="download-client-sdk-of-your-choice-and-set-up-your-environment"></a>Down load de client-SDK van uw keuze en stel uw omgeving in

- Sdk's beschikbaar voor [.net](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet&preserve-view=true), .net Core, [Node.js](https://docs.microsoft.com/javascript/api/overview/azure/mediaservices/management?view=azure-node-latest&preserve-view=true), [python](https://docs.microsoft.com/python/api/overview/azure/mediaservices/management?view=azure-python&preserve-view=true), [Java](https://docs.microsoft.com/java/api/overview/azure/mediaservices/management?view=azure-java-stable&preserve-view=true), [Go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media)en [ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md).
- [Azure cli](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest&preserve-view=true)   integratie voor eenvoudige script ondersteuning.

> [!NOTE]
> Een community PHP SDK is niet meer beschikbaar voor Azure Media Services op v3. Als u PHP op v2 gebruikt, moet u rechtstreeks in uw code naar de REST API migreren.

## <a name="open-api-specifications"></a>API-specificaties openen

- V3 is gebaseerd op een uniform API-Opper vlak, waarmee zowel de functionaliteit van het beheer als de operations-functie op Azure Resource Manager wordt weer gegeven. Azure Resource Manager sjablonen kunnen worden gebruikt voor het maken en implementeren van trans formaties, streaming-eind punten, Live-gebeurtenissen en meer.

- In het document van de [OpenAPI-specificatie](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01) (voorheen Swagger) wordt het schema voor alle service onderdelen uitgelegd.

- Alle client-Sdk's worden afgeleid en gegenereerd op basis van de open API-specificatie gepubliceerd op GitHub. Op het moment van publicatie van dit artikel worden de meest recente open API-specificaties openbaar onderhouden in GitHub. De 2020-05-01-versie is de [nieuwste stabiele release](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01).

## <a name="rest"></a>[REST](#tab/rest)

Gebruik [postman](https://docs.microsoft.com/azure/media-services/latest/media-rest-apis-with-postman) voor Media Services v3-rest API-aanroepen.
Lees de [rest API-referentie pagina's](https://docs.microsoft.com/rest/api/media/).

U moet de teken reeks 2020-05-01 versie in de Postman-verzameling gebruiken.

## <a name="net"></a>[.NET](#tab/net)

Lees het artikel, [Maak verbinding met Media Services v3 API met .net](configure-connect-dotnet-howto.md) om uw omgeving in te stellen.

Als u de nieuwste SDK alleen wilt installeren met behulp van PackageManager, gebruikt u de volgende opdracht:

```Install-Package Microsoft.Azure.Management.Media```

Als u de nieuwste SDK wilt installeren met behulp van de .NET CLI, gebruikt u de volgende opdracht:

```dotnet add package Microsoft.Azure.Management.Media```

Daarnaast zijn volledige .NET-voor beelden beschikbaar in [Azure-samples/Media-Services-v3-DotNet](https://github.com/Azure-Samples/media-services-v3-dotnet) voor verschillende scenario's. De projecten in deze opslag plaats laten zien hoe u verschillende Azure Media Services scenario's implementeert met behulp van de V3-versie.

### <a name="get-started-adjusting-your-code"></a>Aan de slag met het aanpassen van uw code

Zoek de code base op basis van exemplaren van het `CloudMediaContext` gebruik om het upgrade proces te starten naar de V3 API.

De volgende code laat zien hoe de v2 API eerder is geopend met behulp van de v2 .NET SDK. Ontwikkel aars beginnen met het maken van een `CloudMediaContext` en maken een instantie met een- `AzureAdTokenCredentials` object.

```dotnet

class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

```

## <a name="java"></a>[Java](#tab/java)

Lees het artikel, [Maak verbinding met Media Services v3 API met Java](configure-connect-java-howto.md) om uw omgeving in te stellen.

## <a name="python"></a>[Python](#tab/python)

Lees het artikel, [Maak verbinding met Azure Media Services v3 API-python](configure-connect-python-howto.md) om uw omgeving in te stellen.

## <a name="nodejs"></a>[Node.js](#tab/nodejs)

Lees het artikel [verbinding maken met Azure Media Services v3 API-Node.js](configure-connect-nodejs-howto.md) om uw omgeving in te stellen.

## <a name="ruby"></a>[Ruby](#tab/ruby)

- Down load de [ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md) -SDK op github.

## <a name="go"></a>[Go](#tab/go)

Down load de [Go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media) -SDK.

---

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]