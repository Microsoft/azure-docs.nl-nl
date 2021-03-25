---
title: Twee of meer video bestanden samen voegen met .NET | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u twee of meer video bestanden kunt samen voegen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 5eacc366961d3101a7eaf8877a34ef2d462ea76b
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105111437"
---
# <a name="how-to-stitch-two-or-more-video-files-with-net"></a>Twee of meer video bestanden samen voegen met .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="stitch-two-or-more-video-files"></a>Twee of meer video bestanden samen voegen

In het volgende voor beeld ziet u hoe u een voor instelling kunt genereren voor het samen voegen van twee of meer video bestanden. Het meest voorkomende scenario is wanneer u een kop of een trailer wilt toevoegen aan de hoofd video.

> [!NOTE]
> Video bestanden die samen worden bewerkt, moeten eigenschappen delen (video resolutie, frame frequentie, audio track Count, enzovoort). Houd er rekening mee dat u geen Video's van verschillende frame snelheden kunt combi neren of met een ander aantal audio sporen.

## <a name="prerequisites"></a>Vereisten

Kloon of down load de [Media Services .net](https://github.com/Azure-Samples/media-services-v3-dotnet/)-voor beelden.  De code waarnaar wordt verwezen, bevindt zich in de [map EncodingWithMESCustomStitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets/Program.cs).

### <a name="net-code"></a>.NET-code

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoEncoding/EncodingWithMESCustomStitchTwoAssets/Program.cs)]
