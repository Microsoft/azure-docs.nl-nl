---
title: 'Quickstart: Speech-SDK voor het C# Unity-platform instellen - Speech-service'
titleSuffix: Azure Cognitive Services
description: Gebruik deze handleiding om uw platform in te stellen voor het gebruik van C# Unity met de Speech-service-SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: fd592b6f565cb23d7a922af2a68e6328911c2dc0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188274"
---
In deze gids ontdekt u hoe u de [Speech-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) kunt installeren voor [Unity](https://unity3d.com/).

> [!NOTE]
> De Speech-SDK voor Unity ondersteunt Windows Desktop (x86 en x64) of Universeel Windows-platform (x86, x64, ARM/ARM64), Android (x86, ARM32/64) en iOS (x64-simulator, ARM32 en ARM64)

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

- [Unity 2018.3 of hoger](https://store.unity.com/) met [Unity 2019.1 voor extra ondersteuning voor UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Versie 15.9 of hoger van Visual Studio 2017 wordt ook geaccepteerd.
- Installeer voor Windows ARM64-ondersteuning de [optionele bouwhulpprogramma's voor ARM64 en de Windows 10-SDK voor ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Volg deze stappen om de Speech-SDK te installeren voor Unity:

1. Download en open de [Speech-SDK voor Unity](https://aka.ms/csspeech/unitypackage). De SDK is ingepakt als een pakket met Unity-assets (.unitypackage) en is al aan Unity gekoppeld. Als u het assetpakket opent, wordt het dialoogvenster **Unity-pakket importeren** weergegeven. Mogelijk moet u voor deze stap eerst een leeg project maken en openen.

   [![Het dialoogvenster Unity-pakket importeren in de Unity Editor](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png#lightbox)

1. Zorg ervoor dat alle bestanden zijn geselecteerd en selecteer **Importeren**. Na enkele momenten wordt het Unity-assetpakket in uw project geïmporteerd.

Raadpleeg de [Unity-documentatie](https://docs.unity3d.com/Manual/AssetPackages.html) voor meer informatie over het importeren van assetpakketten in Unity.

U kunt nu verdergaan met [Volgende stappen](#next-steps) hieronder.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [windows](../quickstart-list.md)]
