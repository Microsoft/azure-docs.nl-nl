---
title: Logica voor opnieuw proberen in de Media Services SDK voor .NET | Microsoft Docs
description: Het onderwerp bevat een overzicht van de logica voor opnieuw proberen in de Media Services SDK voor .NET.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: feda0ccfa1dc6d02153b98ad084bd775a055e9e3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012901"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Logica voor opnieuw proberen in de Media Services SDK voor .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Wanneer u werkt met Microsoft Azure Services, kunnen er tijdelijke fouten optreden. Als er een tijdelijke fout optreedt, wordt in de meeste gevallen na een paar pogingen de bewerking geslaagd. De Media Services-SDK voor .NET implementeert de pogings logica voor het verwerken van tijdelijke fouten die zijn gekoppeld aan uitzonde ringen en fouten die worden veroorzaakt door webaanvragen, het uitvoeren van query's, het opslaan van wijzigingen en opslag bewerkingen.  De Media Services SDK voor .NET voert standaard vier nieuwe pogingen uit voordat de uitzonde ring opnieuw wordt gegenereerd voor uw toepassing. De code in uw toepassing moet vervolgens deze uitzonde ring op de juiste manier afhandelen.  

 Hier volgt een korte richt lijn voor het beleid voor webaanvraag-, opslag-, query-en Save Changes:  

* Het opslag beleid wordt gebruikt voor Blob Storage-bewerkingen (uploads of het downloaden van Asset-bestanden).  
* Het beleid voor webaanvragen wordt gebruikt voor algemene webaanvragen (bijvoorbeeld voor het ophalen van een verificatie token en het oplossen van het eind punt gebruikers cluster).  
* Het query beleid wordt gebruikt voor het uitvoeren van query's op entiteiten van REST (bijvoorbeeld mediaContext. assets. where (...)).  
* Het beleid voor bewerkingen wordt gebruikt voor het uitvoeren van alle gegevens in de service (bijvoorbeeld het maken van een entiteit die een entiteit bijwerkt en een service functie aanroept voor een bewerking).  
  
  In dit onderwerp vindt u een lijst met uitzonderings typen en fout codes die worden verwerkt door de Media Services SDK voor .NET-pogingen.  

## <a name="exception-types"></a>Uitzonderings typen
In de volgende tabel worden de uitzonde ringen beschreven die worden verwerkt door de Media Services SDK voor .NET of worden niet verwerkt voor bewerkingen die kunnen leiden tot tijdelijke fouten.  

| Uitzondering | Webaanvraag | Storage | Query | Change |
| --- | --- | --- | --- | --- |
| WebException<br/>Zie de sectie [webexcept-status codes](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) voor meer informatie. |Ja |Ja |Ja |Ja |
| DataServiceClientException<br/> Zie [HTTP-fout status codes](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)voor meer informatie. |Nee |Ja |Ja |Ja |
| DataServiceQueryException<br/> Zie [HTTP-fout status codes](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)voor meer informatie. |Nee |Ja |Ja |Ja |
| DataServiceRequestException<br/> Zie [HTTP-fout status codes](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)voor meer informatie. |Nee |Ja |Ja |Ja |
| DataServiceTransportException |Nee |Nee |Ja |Ja |
| TimeoutException |Ja |Ja |Ja |Nee |
| SocketException |Ja |Ja |Ja |Ja |
| StorageException |Nee |Ja |Nee |Nee |
| IOException |Nee |Ja |Nee |Nee |

### <a name="webexception-status-codes"></a><a name="WebExceptionStatus"></a> Webexcept-status codes
In de volgende tabel wordt weer gegeven voor welke uitzonderings fout codes de logica voor opnieuw proberen wordt geïmplementeerd. De [WebExceptionStatus](/dotnet/api/system.net.webexceptionstatus?view=netcore-3.1) -opsomming definieert de status codes.  

| Status | Webaanvraag | Storage | Query | Change |
| --- | --- | --- | --- | --- |
| ConnectFailure |Ja |Ja |Ja |Ja |
| NameResolutionFailure |Ja |Ja |Ja |Ja |
| ProxyNameResolutionFailure |Ja |Ja |Ja |Ja |
| SendFailure |Ja |Ja |Ja |Ja |
| PipelineFailure |Ja |Ja |Ja |Nee |
| ConnectionClosed |Ja |Ja |Ja |Nee |
| KeepAliveFailure |Ja |Ja |Ja |Nee |
| UnknownError |Ja |Ja |Ja |Nee |
| ReceiveFailure |Ja |Ja |Ja |Nee |
| RequestCanceled |Ja |Ja |Ja |Nee |
| Time-out |Ja |Ja |Ja |Nee |
| ProtocolError <br/>De nieuwe poging op ProtocolError wordt bepaald door de verwerking van de HTTP-status code. Zie [HTTP-fout status codes](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)voor meer informatie. |Ja |Ja |Ja |Ja |

### <a name="http-error-status-codes"></a><a name="HTTPStatusCode"></a> Status codes voor HTTP-fouten
Wanneer de bewerkingen query en Save Changes DataServiceClientException, DataServiceQueryException of DataServiceQueryException genereren, wordt de status code van de HTTP-fout geretourneerd in de eigenschap Property status.  In de volgende tabel ziet u voor welke fout codes de logica voor opnieuw proberen wordt geïmplementeerd.  

| Status | Webaanvraag | Storage | Query | Change |
| --- | --- | --- | --- | --- |
| 401 |Nee |Ja |Nee |Nee |
| 403 |Nee |Ja<br/>Verwerken van nieuwe pogingen met langere wacht tijden. |Nee |Nee |
| 408 |Ja |Ja |Ja |Ja |
| 429 |Ja |Ja |Ja |Ja |
| 500 |Ja |Ja |Ja |Nee |
| 502 |Ja |Ja |Ja |Nee |
| 503 |Ja |Ja |Ja |Ja |
| 504 |Ja |Ja |Ja |Nee |

Zie [Azure-SDK-for-Media Services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling)(Engelstalig) als u de daad werkelijke implementatie wilt bekijken van de Media Services SDK voor .net-pogingen.

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
