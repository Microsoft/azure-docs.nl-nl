---
title: Long-Running bewerkingen pollen | Microsoft Docs
description: Azure Media Services biedt Api's die aanvragen verzenden naar Media Services om bewerkingen te starten (bijvoorbeeld het maken, starten, stoppen of verwijderen van een kanaal), deze bewerkingen worden uitgevoerd. In dit onderwerp wordt uitgelegd hoe u langlopende bewerkingen kunt controleren.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 7926f7aaa427d49d13cab5e13f5153bcd22e5898
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013819"
---
# <a name="delivering-live-streaming-with-azure-media-services"></a>Live streamen leveren met Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Overzicht

Microsoft Azure Media Services biedt Api's die aanvragen verzenden naar Media Services om bewerkingen te starten (bijvoorbeeld: een kanaal maken, starten, stoppen of verwijderen). Deze bewerkingen worden uitgevoerd.

De Media Services .NET SDK biedt Api's die de aanvraag verzenden en wachten op het volt ooien van de bewerking (intern worden de Api's gecontroleerd op de voortgang van de bewerking). Wanneer u bijvoorbeeld een kanaal aanroept. Start (), de methode wordt geretourneerd nadat het kanaal is gestart. U kunt ook de asynchrone versie gebruiken: await Channel. StartAsync () (Zie [tikken](./media-services-mes-schema.md)) (voor informatie over asynchroon patroon op basis van een taak). Api's die een bewerkings aanvraag verzenden en vervolgens naar de status pollen totdat de bewerking is voltooid, worden ' polling methoden ' genoemd. Deze methoden (met name de async-versie) worden aanbevolen voor uitgebreide client toepassingen en/of stateful Services.

Er zijn scenario's waarin een toepassing niet kan wachten op een langlopende HTTP-aanvraag en wil hand matig pollen voor de voortgang van de bewerking. Een typisch voor beeld is een browser interactie met een stateless webservice: wanneer de browser een kanaal wil maken, wordt een langlopende bewerking door de webservice gestart en wordt de bewerkings-ID naar de browser geretourneerd. De browser kan vervolgens de webservice vragen de bewerkings status op te halen op basis van de ID. De Media Services .NET SDK biedt Api's die nuttig zijn voor dit scenario. Deze Api's worden "niet-polling methoden" genoemd.
De "niet-polling methoden" hebben het volgende naamgevings patroon: de bewerking voor het verzenden van de *bewerking*(bijvoorbeeld SendCreateOperation). De bewerkings methoden van de verzend *bewerking* retour neren het **IOperation** -object; het geretourneerde object bevat informatie die kan worden gebruikt om de bewerking bij te houden. De OperationAsync-methoden van de verzend *bewerking* retour neren een **taak \<IOperation>**.

Momenteel ondersteunen de volgende klassen niet-polling methoden:  **Channel**, **StreamingEndpoint** en **Program**.

Als u wilt pollen voor de bewerkings status, gebruikt u de methode **GetOperation** voor de klasse **OperationBaseCollection** . Gebruik de volgende intervallen om de bewerkings status te controleren: voor **Channel** -en **StreamingEndpoint** -bewerkingen gebruikt u 30 seconden; gebruik 10 seconden voor **programma** bewerkingen.

## <a name="create-and-configure-a-visual-studio-project"></a>Maak en configureer een Visual Studio-project.

Stel uw ontwikkel omgeving in en vul het app.config bestand in met verbindings informatie, zoals beschreven in [Media Services ontwikkeling met .net](media-services-dotnet-how-to-use.md).

## <a name="example"></a>Voorbeeld

In het volgende voor beeld wordt een klasse gedefinieerd met de naam **ChannelOperations**. Deze klassedefinitie kan een start punt voor de definitie van de Web-Service klasse zijn. Voor de eenvoud van de volgende voor beelden worden de niet-async-versies van methoden gebruikt.

In het voor beeld ziet u ook hoe de client deze klasse kan gebruiken.

### <a name="channeloperations-class-definition"></a>ChannelOperations-klassen definitie

```csharp
using Microsoft.WindowsAzure.MediaServices.Client;
using System;
using System.Collections.Generic;
using System.Configuration;
using System.Net;

/// <summary> 
/// The ChannelOperations class only implements 
/// the Channel’s creation operation. 
/// </summary> 
public class ChannelOperations
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

    // Field for service context.
    private static CloudMediaContext _context = null;

    public ChannelOperations()
    {
        AzureAdTokenCredentials tokenCredentials = 
            new AzureAdTokenCredentials(_AADTenantDomain,
                new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                AzureEnvironments.AzureCloudEnvironment);

        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
    }

    /// <summary>  
    /// Initiates the creation of a new channel.  
    /// </summary>  
    /// <param name="channelName">Name to be given to the new channel</param>  
    /// <returns>  
    /// Operation Id for the long running operation being executed by Media Services. 
    /// Use this operation Id to poll for the channel creation status. 
    /// </returns> 
    public string StartChannelCreation(string channelName)
    {
        var operation = _context.Channels.SendCreateOperation(
            new ChannelCreationOptions
            {
                Name = channelName,
                Input = CreateChannelInput(),
                Preview = CreateChannelPreview(),
                Output = CreateChannelOutput()
            });

        return operation.Id;
    }

    /// <summary> 
    /// Checks if the operation has been completed. 
    /// If the operation succeeded, the created channel Id is returned in the out parameter.
    /// </summary> 
    /// <param name="operationId">The operation Id.</param> 
    /// <param name="channel">
    /// If the operation succeeded, 
    /// the created channel Id is returned in the out parameter.</param>
    /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
    public bool IsCompleted(string operationId, out string channelId)
    {
        IOperation operation = _context.Operations.GetOperation(operationId);
        bool completed = false;

        channelId = null;

        switch (operation.State)
        {
            case OperationState.Failed:
                // Handle the failure. 
                // For example, throw an exception. 
                // Use the following information in the exception: operationId, operation.ErrorMessage.
                break;
            case OperationState.Succeeded:
                completed = true;
                channelId = operation.TargetEntityId;
                break;
            case OperationState.InProgress:
                completed = false;
                break;
        }
        return completed;
    }

    private static ChannelInput CreateChannelInput()
    {
        return new ChannelInput
        {
            StreamingProtocol = StreamingProtocol.RTMP,
            AccessControl = new ChannelAccessControl
            {
                IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelInput001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
            }
        };
    }

    private static ChannelPreview CreateChannelPreview()
    {
        return new ChannelPreview
        {
            AccessControl = new ChannelAccessControl
            {
                IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelPreview001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
            }
        };
    }

    private static ChannelOutput CreateChannelOutput()
    {
        return new ChannelOutput
        {
            Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
        };
    }
}
```

### <a name="the-client-code"></a>De client code

```csharp
ChannelOperations channelOperations = new ChannelOperations();
string opId = channelOperations.StartChannelCreation("MyChannel001");

string channelId = null;
bool isCompleted = false;

while (isCompleted == false)
{
    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
    isCompleted = channelOperations.IsCompleted(opId, out channelId);
}

// If we got here, we should have the newly created channel id.
Console.WriteLine(channelId);
```

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]