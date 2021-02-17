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
ms.openlocfilehash: 58ce8dc67488c42485f2fac73e514c5639b11cf9
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551310"
---
In deze gids ontdekt u hoe u de [Speech-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) kunt installeren voor [Unity](https://unity3d.com/).

> [!NOTE]
> De Speech-SDK voor Unity ondersteunt Windows Desktop (x86 en x64) of Universeel Windows-platform (x86, x64, ARM/ARM64), Android (x86, ARM32/64) en iOS (x64-simulator, ARM32 en ARM64)

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

- In Windows hebt u het [Microsoft Visual C++ Redistributable for Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) voor uw platform nodig. Bij een eerste installatie is mogelijk een herstart vereist.
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
