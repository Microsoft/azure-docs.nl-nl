---
title: Verbinding maken met Azure Media Services v3 API-.NET
description: In dit artikel wordt beschreven hoe u verbinding maakt met Media Services v3 API met .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/17/2020
ms.author: inhenkel
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: e4d1ed0c015b75cc058c7d6136069a8858d835e2
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492525"
---
# <a name="connect-to-media-services-v3-api---net"></a>Verbinding maken met Media Services v3 API-.NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In dit artikel wordt beschreven hoe u verbinding maakt met de Azure Media Services v3 .NET SDK met behulp van de Service-Principal-aanmeldings methode.

## <a name="prerequisites"></a>Vereisten

- [Een Azure Media Services-account maken](./account-create-how-to.md). Zorg ervoor dat u de naam van de resource groep en de naam van het Media Services account vergeet
- Installeer een hulp programma dat u wilt gebruiken voor .NET-ontwikkeling. In de stappen in dit artikel ziet u hoe u [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)gebruikt. U kunt Visual Studio code gebruiken. Zie [werken met C#](https://code.visualstudio.com/docs/languages/csharp). U kunt ook een andere code-editor gebruiken.

> [!IMPORTANT]
> Bekijk [naamconventies](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-console-application"></a>Een consoletoepassing maken

1. Start Visual Studio. 
1. Klik in het menu **bestand** op **Nieuw**  >  **project**. 
1. Maak een **.net core** -console toepassing.

De voor beeld-app in dit onderwerp streeft naar doelen `netcoreapp2.0` . De code maakt gebruik van ' async Main ', dat beschikbaar is vanaf C# 7,1. Raadpleeg dit [blog](/archive/blogs/benwilli/async-main-is-available-but-hidden) voor meer informatie.

## <a name="add-required-nuget-packagesassemblies"></a>Vereiste NuGet-pakketten/assembly's toevoegen

1. Selecteer in Visual Studio **extra**  >  **NuGet package manager**  >  **NuGet Manager-console**.
2. In het venster **Package Manager-console** gebruikt u `Install-Package` de opdracht om de volgende NuGet-pakketten toe te voegen. Bijvoorbeeld `Install-Package Microsoft.Azure.Management.Media`.

|Pakket|Beschrijving|
|---|---|
|`Microsoft.Azure.Management.Media`|Azure Media Services SDK. <br/>Om ervoor te zorgen dat u het meest recente Azure Media Services-pakket gebruikt, controleert u [micro soft. Azure. Management. Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media).|

### <a name="other-required-assemblies"></a>Andere vereiste assembly's

- Azure. storage. blobs
- Microsoft.Extensions.Configuratie
- Microsoft.Extensions.Configuratie. Omgevings variabelen
- Microsoft.Extensions.Configuration.Jsop
- Microsoft.Rest.ClientRuntime.Azure.Authentication

## <a name="create-and-configure-the-app-settings-file"></a>Het app-instellingen bestand maken en configureren

### <a name="create-appsettingsjson"></a>appsettings.jsmaken op

1. Go-bestand voor **algemene**  >  **tekst**.
1. Noem deze appsettings.jsop.
1. Stel de eigenschap kopiëren naar uitvoermap van het JSON-bestand in op ' kopiëren indien nieuwer ' (zodat de toepassing toegang kan krijgen tot de map wanneer deze wordt gepubliceerd).

### <a name="set-values-in-appsettingsjson"></a>Waarden instellen in appsettings.jsop

Voer de `az ams account sp create` opdracht uit zoals beschreven in [Access-api's](./access-api-howto.md). De opdracht retourneert een JSON-bestand dat u moet kopiëren naar de appsettings.jsop.
 
## <a name="add-configuration-file"></a>Een configuratiebestand toevoegen

Voeg voor het gemak een configuratie bestand toe dat verantwoordelijk is voor het lezen van waarden van appsettings.jsop.

1. Voeg een nieuwe. cs-klasse toe aan uw project. Noem deze `ConfigWrapper`. 
1. Plak de volgende code in dit bestand (in dit voor beeld wordt ervan uitgegaan dat u de naam ruimte hebt `ConsoleApp1` ).

```csharp
using System;

using Microsoft.Extensions.Configuration;

namespace ConsoleApp1
{
    public class ConfigWrapper
    {
        private readonly IConfiguration _config;

        public ConfigWrapper(IConfiguration config)
        {
            _config = config;
        }

        public string SubscriptionId
        {
            get { return _config["SubscriptionId"]; }
        }

        public string ResourceGroup
        {
            get { return _config["ResourceGroup"]; }
        }

        public string AccountName
        {
            get { return _config["AccountName"]; }
        }

        public string AadTenantId
        {
            get { return _config["AadTenantId"]; }
        }

        public string AadClientId
        {
            get { return _config["AadClientId"]; }
        }

        public string AadSecret
        {
            get { return _config["AadSecret"]; }
        }

        public Uri ArmAadAudience
        {
            get { return new Uri(_config["ArmAadAudience"]); }
        }

        public Uri AadEndpoint
        {
            get { return new Uri(_config["AadEndpoint"]); }
        }

        public Uri ArmEndpoint
        {
            get { return new Uri(_config["ArmEndpoint"]); }
        }

        public string Location
        {
            get { return _config["Location"]; }
        }
    }
}
```

## <a name="connect-to-the-net-client"></a>Verbinding maken met de .NET-client

Als u wilt starten met Media Services API's met .NET, moet u een **AzureMediaServicesClient**-object maken. Als u het object wilt maken, moet u referenties opgeven die de client nodig heeft om verbinding te maken met Azure met behulp van Microsoft Azure Active Directory. In de onderstaande code maakt de functie GetCredentialsAsync het ServiceClientCredentials-object op basis van de referenties die zijn opgegeven in het lokale configuratie bestand.

1. Open `Program.cs`.
1. Plak de volgende code:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace ConsoleApp1
{
    class Program
    {
        public static async Task Main(string[] args)
        {
            
            ConfigWrapper config = new ConfigWrapper(new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddEnvironmentVariables()
                .Build());

            try
            {
                IAzureMediaServicesClient client = await CreateMediaServicesClientAsync(config);
                Console.WriteLine("connected");
            }
            catch (Exception exception)
            {
                if (exception.Source.Contains("ActiveDirectory"))
                {
                    Console.Error.WriteLine("TIP: Make sure that you have filled out the appsettings.json file before running this sample.");
                }

                Console.Error.WriteLine($"{exception.Message}");

                ApiErrorException apiException = exception.GetBaseException() as ApiErrorException;
                if (apiException != null)
                {
                    Console.Error.WriteLine(
                        $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
                }
            }

            Console.WriteLine("Press Enter to continue.");
            Console.ReadLine();
        }
 
        private static async Task<ServiceClientCredentials> GetCredentialsAsync(ConfigWrapper config)
        {
            // Use ApplicationTokenProvider.LoginSilentWithCertificateAsync or UserTokenProvider.LoginSilentAsync to get a token using service principal with certificate
            //// ClientAssertionCertificate
            //// ApplicationTokenProvider.LoginSilentWithCertificateAsync

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symmetric key
            ClientCredential clientCredential = new ClientCredential(config.AadClientId, config.AadSecret);
            return await ApplicationTokenProvider.LoginSilentAsync(config.AadTenantId, clientCredential, ActiveDirectoryServiceSettings.Azure);
        }

        private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
        {
            var credentials = await GetCredentialsAsync(config);

            return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
            {
                SubscriptionId = config.SubscriptionId,
            };
        }

    }
}
```

## <a name="next-steps"></a>Volgende stappen

- [Zelf studie: Video's uploaden, coderen en streamen-.NET](stream-files-tutorial-with-api.md) 
- [Zelf studie: Stream Live met Media Services v3-.NET](stream-live-tutorial-with-api.md)
- [Zelf studie: Video's analyseren met Media Services v3-.NET](analyze-videos-tutorial.md)
- [Een taakinvoer maken vanuit een lokaal bestand - .NET](job-input-from-local-file-how-to.md)
- [Een taakinvoer maken vanuit een HTTPS-URL - .NET](job-input-from-http-how-to.md)
- [Coderen met een aangepaste transformatie - .NET](transform-custom-presets-how-to.md)
- [Dynamische AES-128-versleuteling en de sleutelleveringsservice gebruiken - .NET](drm-playready-license-template-concept.md)
- [Dynamische DRM-versleuteling en licentielevering gebruiken - .NET](drm-protect-with-drm-tutorial.md)
- [Een ondertekeningssleutel ophalen uit het bestaand beleid - .NET](drm-get-content-key-policy-dotnet-how-to.md)
- [Filters maken met Media Services - .NET](filters-dynamic-manifest-dotnet-how-to.md)
- [Voorbeelden van geavanceerde video on demand van Azure Functions-v2 met Media Services v3](https://aka.ms/ams3functions)

## <a name="see-also"></a>Zie ook

* [Naslaginformatie over .NET](/dotnet/api/overview/azure/mediaservices/management)
* Zie de [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet) -voor beelden opslag plaats voor meer code voorbeelden.
