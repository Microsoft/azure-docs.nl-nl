---
title: Streaming-eind punten schalen met de Azure Portal | Microsoft Docs
description: In deze zelf studie wordt u begeleid bij de stappen voor het schalen van streaming-eind punten met de Azure Portal.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 4db53d64131e6fb43ce425cf579d79e62775754a
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009745"
---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Streaming-eindpunten schalen met Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Overzicht

> [!NOTE]
> U hebt een Azure-account nodig om deze zelfstudie te voltooien. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie. 
> 
> 

**Premium**-streaming-eindpunten zijn geschikt voor geavanceerde workloads omdat er gebruik wordt gemaakt van toegewezen, schaalbare bandbreedtecapaciteit. Klanten met een **Premium**-streaming-eindpunt krijgen standaard één streaming-eenheid (SU). Het streaming-eindpunt kan worden geschaald door SU's toe te voegen. Elke SU biedt extra bandbreedtecapaciteit voor de toepassing. Zie het onderwerp [overzicht van streaming-eind punten](media-services-streaming-endpoints-overview.md) voor meer informatie over streaming-eindpunt typen en CDN-configuratie.
 
In dit onderwerp wordt uitgelegd hoe u een streaming-eind punt kunt schalen.

Zie [Media Services Pricing Details](https://azure.microsoft.com/pricing/details/media-services/) (Informatie over Media Services-prijzen) voor informatie over prijzen.

## <a name="scale-streaming-endpoints"></a>Streaming-eind punten schalen

Ga als volgt te werk om het aantal streaming-eenheden te wijzigen:

1. Selecteer uw Azure Media Services-account in [Azure Portal](https://portal.azure.com/).
2. Selecteer in het venster **instellingen** **streaming-eind punten**.
3. Klik op het streaming-eind punt dat u wilt schalen. 

    > [!NOTE] 
    > U kunt alleen **Premium** streaming-eind punten schalen.

4. Verplaats de schuif regelaar om het aantal streaming-eenheden op te geven.

    ![Streaming-eindpunt](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>Volgende stappen
Media Services-leertrajecten bekijken.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

