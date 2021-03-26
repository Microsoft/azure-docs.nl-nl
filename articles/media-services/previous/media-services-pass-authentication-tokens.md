---
title: Verificatie tokens door geven aan Azure Media Services | Microsoft Docs
description: Meer informatie over het verzenden van verificatie tokens van de client naar de Azure Media Services key delivery service
services: media-services
keywords: inhouds beveiliging, DRM, token verificatie
author: IngridAtMicrosoft
manager: femila
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 1fe692e1eb20956f339c9b861f50163cee9c5063
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564650"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Meer informatie over hoe clients tokens door geven aan de Azure Media Services key delivery service

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Klanten vragen vaak hoe een speler tokens kan door geven aan de Azure Media Services key delivery service voor verificatie, zodat de speler de sleutel kan verkrijgen. Media Services ondersteunt de SWT-indelingen (Simple Web token) en JSON Web Token (JWT). Verificatie van tokens wordt toegepast op elk type sleutel, ongeacht of u gebruikmaakt van een common Encryption-of Advanced Encryption Standard (AES)-envelop versleuteling in het systeem.

 Afhankelijk van de speler en het platform waarop u het doel hebt, kunt u het token door geven aan uw speler op de volgende manieren:

- Via de HTTP-autorisatie-header.
    > [!NOTE]
    > Het voor voegsel ' Bearer ' wordt verwacht volgens de OAuth 2,0-specificaties. Als u de video bron wilt instellen, kiest u **AES (JWT-token)** of **AES (SWT-token)**. Het token wordt door gegeven via de autorisatie-header.

- Via de toevoeging van een URL-query parameter met "token = tokenvalue."  
    > [!NOTE]
    > Het voor voegsel ' Bearer ' wordt niet verwacht. Omdat het token via een URL wordt verzonden, moet u de token teken reeks beveiligen. Hier volgt een C#-voorbeeld code die laat zien hoe u dit doet:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Via het veld CustomData.
Deze optie wordt alleen gebruikt voor de aanschaf van PlayReady-licenties, via het veld CustomData van de de verwervings Challenge van PlayReady-licenties. In dit geval moet het token zich in het XML-document bevinden, zoals hier wordt beschreven:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Plaats uw verificatie token in het element token.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
