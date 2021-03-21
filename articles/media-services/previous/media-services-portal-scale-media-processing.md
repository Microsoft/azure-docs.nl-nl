---
title: Media verwerking schalen met behulp van de Azure Portal | Microsoft Docs
description: In deze zelf studie wordt u begeleid bij de stappen voor het schalen van media verwerking met behulp van de Azure Portal.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 49c3899b912a88605e9269cdb1c34c7e18ed5247
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009722"
---
# <a name="change-the-reserved-unit-type"></a>Het gereserveerde eenheidstype wijzigen

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Overzicht

Media Services-accounts worden gekoppeld aan een gereserveerde-eenheidstype, waarmee wordt bepaald hoe snel de mediaverwerkingstaken worden verwerkt. U kunt kiezen uit de volgende gereserveerde-eenheidstypen: **S1**, **S2** en **S3**. Een coderingstaak wordt bijvoorbeeld sneller uitgevoerd wanneer u het gereserveerde-eenheidstype **S2** gebruikt (in vergelijking met het type **S1**).

Naast het opgeven van het type gereserveerde eenheid kunt u opgeven om uw account in te richten met **gereserveerde eenheden** (RUs). Op basis van het aantal ingerichte RU's wordt bepaald hoeveel mediataken tegelijk kunnen worden verwerkt voor een bepaald account.

>[!NOTE]
>RU's werken voor het parallel verwerken van alle media, waaronder indexeringstaken via Azure Media Indexer. Indexeringstaken worden echter niet sneller verwerkt met snellere gereserveerde eenheden, terwijl dit bij coderen wel het geval is.

> [!IMPORTANT]
> Raadpleeg het onderwerp [overzicht](media-services-scale-media-processing-overview.md) voor meer informatie over het onderwerp voor het schalen van media.
> 
> 

## <a name="scale-media-processing"></a>Media verwerking schalen
Ga als volgt te werk om het gereserveerde eenheids type en het aantal gereserveerde eenheden te wijzigen:

1. Selecteer uw Azure Media Services-account in [Azure Portal](https://portal.azure.com/).
2. Selecteer in het venster **instellingen** de optie **gereserveerde media-eenheden**.
   
    Als u het aantal gereserveerde eenheden voor het geselecteerde gereserveerde eenheids type wilt wijzigen, gebruikt u de schuif regelaar voor **Media-eenheden** boven aan het scherm.
   
    Als u het **type gereserveerde eenheid** wilt wijzigen, klikt u op de balk **snelheid van gereserveerde verwerkings eenheden** . Selecteer vervolgens de prijs categorie die u nodig hebt: S1, S2 of S3.
   
3. Klik op de knop OPSLAAN om uw wijzigingen op te slaan.
   
    De nieuwe gereserveerde eenheden worden toegewezen wanneer u op Opslaan klikt.

## <a name="next-steps"></a>Volgende stappen
Media Services-leertrajecten bekijken.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
