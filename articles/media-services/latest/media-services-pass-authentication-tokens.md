---
title: Verificatie tokens door geven aan Media Services v3 | Microsoft Docs
description: Meer informatie over het verzenden van verificatie tokens van de client naar de Media Services v3 key delivery service
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 590309ad392876ba3c5cb6d21abe777ab5a93efb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572427"
---
# <a name="pass-tokens-to-the-azure-media-services-v3-key-delivery-service"></a>Tokens door geven aan de Azure Media Services v3 key delivery service

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Klanten vragen vaak hoe een speler tokens kan door geven aan de Azure Media Services key delivery service voor verificatie, zodat de speler de sleutel kan verkrijgen. Media Services ondersteunt de SWT-indelingen (Simple Web token) en JSON Web Token (JWT). Verificatie van tokens wordt toegepast op elk type sleutel, ongeacht of u gebruikmaakt van een common Encryption-of Advanced Encryption Standard (AES)-envelop versleuteling in het systeem.

 Afhankelijk van de speler en het platform waarop u het doel hebt, kunt u het token door geven aan uw speler op de volgende manieren:

## <a name="pass-a-token-through-the-http-authorization-header"></a>Een token door geven via de HTTP-autorisatie-header

> [!NOTE]
> Het voor voegsel ' Bearer ' wordt verwacht volgens de OAuth 2,0-specificaties. Als u de video bron wilt instellen, kiest u **AES (JWT-token)** of **AES (SWT-token)**. Het token wordt door gegeven via de autorisatie-header.

## <a name="pass-a-token-via-the-addition-of-a-url-query-parameter-with-tokentokenvalue"></a>Geef een token door via de toevoeging van een URL-query parameter met "token = tokenvalue."

> [!NOTE]
> Het voor voegsel ' Bearer ' wordt niet verwacht. Omdat het token via een URL wordt verzonden, moet u de token teken reeks beveiligen. Hier volgt een C#-voorbeeld code die laat zien hoe u dit doet:

```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
```

## <a name="pass-a-token-through-the-customdata-field"></a>Een token door geven via het veld CustomData

Deze optie wordt alleen gebruikt voor de aanschaf van PlayReady-licenties, via het veld CustomData van de de verwervings Challenge van PlayReady-licenties. In dit geval moet het token zich in het XML-document bevinden, zoals hier wordt beschreven:

```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
```

Plaats uw verificatie token in het element token.
