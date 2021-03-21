---
title: Streaming-eind punten beheren met .NET SDK. | Microsoft Docs
description: In dit artikel wordt beschreven hoe u streaming-eind punten beheert met .NET SDK.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.assetid: 0da34a97-f36c-48d0-8ea2-ec12584a2215
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 5c84e7c753e6d1eb1d357320857e4c159cc13cfc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013751"
---
# <a name="manage-streaming-endpoints-with-net-sdk"></a>Streaming-eind punten beheren met .NET SDK

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

>[!NOTE]
>Zorg ervoor dat u het artikel [overzicht](media-services-streaming-endpoints-overview.md) bekijkt. Lees ook [StreamingEndpoint](/rest/api/media/operations/streamingendpoint).

De code in dit artikel laat zien hoe u de volgende taken kunt uitvoeren met behulp van de Azure Media Services .NET SDK:

- Controleer het standaard streaming-eind punt.
- Nieuw streaming-eind punt maken/toevoegen.

    Mogelijk wilt u meerdere streaming-eind punten hebben als u van plan bent verschillende Cdn's of een CDN en directe toegang te hebben.

    > [!NOTE]
    > Er worden alleen kosten in rekening gebracht wanneer het streaming-eind punt wordt uitgevoerd.
    
- Werk het streaming-eind punt bij.
    
    Zorg ervoor dat u de functie update () aanroept.

- Het streaming-eind punt verwijderen.

    >[!NOTE]
    >Het standaard-streaming-eind punt kan niet worden verwijderd.

Zie [Dit](media-services-portal-scale-streaming-endpoints.md) artikel voor meer informatie over het schalen van het streaming-eind punt.

## <a name="create-and-configure-a-visual-studio-project"></a>Maak en configureer een Visual Studio-project.

Stel uw ontwikkel omgeving in en vul het app.config bestand in met verbindings informatie, zoals beschreven in [Media Services ontwikkeling met .net](media-services-dotnet-how-to-use.md). 

## <a name="add-code-that-manages-streaming-endpoints"></a>Code toevoegen die streaming-eind punten beheert
    
Vervang de code in het programma. cs door de volgende code:

```csharp
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.Live;

namespace AMSStreamingEndpoint
{
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
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            var defaultStreamingEndpoint = _context.StreamingEndpoints.Where(s => s.Name.Contains("default")).FirstOrDefault();
            ExamineStreamingEndpoint(defaultStreamingEndpoint);

            IStreamingEndpoint newStreamingEndpoint = AddStreamingEndpoint();
            UpdateStreamingEndpoint(newStreamingEndpoint);
            DeleteStreamingEndpoint(newStreamingEndpoint);
        }

        static public void ExamineStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
        {
            Console.WriteLine(streamingEndpoint.Name);
            Console.WriteLine(streamingEndpoint.StreamingEndpointVersion);
            Console.WriteLine(streamingEndpoint.FreeTrialEndTime);
            Console.WriteLine(streamingEndpoint.ScaleUnits);
            Console.WriteLine(streamingEndpoint.CdnProvider);
            Console.WriteLine(streamingEndpoint.CdnProfile);
            Console.WriteLine(streamingEndpoint.CdnEnabled);
        }

        static public IStreamingEndpoint AddStreamingEndpoint()
        {
            var name = "StreamingEndpoint" + DateTime.UtcNow.ToString("hhmmss");
            var option = new StreamingEndpointCreationOptions(name, 1)
            {
                StreamingEndpointVersion = new Version("2.0"),
                CdnEnabled = true,
                CdnProfile = "CdnProfile",
                CdnProvider = CdnProviderType.PremiumVerizon
            };

            var streamingEndpoint = _context.StreamingEndpoints.Create(option);

            return streamingEndpoint;
        }

        static public void UpdateStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
        {
            if (streamingEndpoint.StreamingEndpointVersion == "1.0")
                streamingEndpoint.StreamingEndpointVersion = "2.0";

            streamingEndpoint.CdnEnabled = false;
            streamingEndpoint.Update();
        }

        static public void DeleteStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
        {
            streamingEndpoint.Delete();
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen
Media Services-leertrajecten bekijken.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
