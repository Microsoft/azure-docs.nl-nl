---
title: 'Een ONNX-model gebruiken met Windows ML: Custom Vision Service'
titleSuffix: Azure Cognitive Services
description: Lees hoe u een Windows UWP-app maakt die gebruikmaakt van een ONNX-model dat uit Azure Cognitive Services is geëxporteerd.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: pafarley
ms.openlocfilehash: 58ced0c45d66223ac3e40112126e92a4539db32d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94616073"
---
# <a name="use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Een ONNX-model uit Custom Vision gebruiken met Windows ML (preview)

Lees hoe u een ONNX-model gebruikt dat uit de Custom Vision Service is geëxporteerd met Windows ML (preview).

In deze hand leiding vindt u informatie over het gebruik van een ONNX-bestand dat is geëxporteerd uit de Custom Vision Service met Windows ML. U gebruikt de voor beeld-UWP-toepassing met uw eigen getrainde afbeeldings classificatie.

## <a name="prerequisites"></a>Vereisten

* Windows 10 versie 1809 of hoger
* Windows SDK voor Build 17763 of hoger
* Visual Studio 2017 versie 15.7 of later waarbij de __ontwikkelworkload van Universal Windows Platform__ is ingeschakeld.
* De ontwikkelaars modus is ingeschakeld op uw PC. Zie [uw apparaat inschakelen voor ontwikkeling](/windows/uwp/get-started/enable-your-device-for-development)voor meer informatie.

## <a name="about-the-example-app"></a>Over de voorbeeld-app

De meegeleverde toepassing is een algemene Windows UWP-app. Hiermee kunt u een installatie kopie van uw computer selecteren, die vervolgens wordt verwerkt door een lokaal opgeslagen classificatie model. De tags en scores die door het model worden geretourneerd, worden weergegeven naast de afbeelding.

## <a name="get-the-example-code"></a>De voorbeeldcode halen

De voorbeeld toepassing is beschikbaar op de opslag plaats [COGNITIVE Services ONNX Custom Vision voor beeld](https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample) van github. Kloon het naar uw lokale computer en open *SampleOnnxEvaluationApp. SLN* in Visual Studio.

## <a name="test-the-application"></a>De toepassing testen

1. Gebruik de `F5`-sleutel om de toepassing te starten vanuit Visual Studio. U wordt mogelijk gevraagd de Ontwikkelmodus in te schakelen.
1. Wanneer de toepassing wordt gestart, moet u de knop gebruiken om een installatiekopie voor het scoren te selecteren. Het standaard ONNX-model wordt getraind voor het classificeren van verschillende typen plankton.

## <a name="use-your-own-model"></a>Uw eigen model gebruiken

Als u uw eigen afbeeldings classificatie model wilt gebruiken, volgt u deze stappen:

1. Maak en train een classificatie met de Custom Vision Service. Zie [een classificatie maken en trainen](./getting-started-build-a-classifier.md)voor instructies over hoe u dit doet. Gebruik een van de **compacte** domeinen, zoals **Algemeen (compact)**. 
   * Als u een bestaande classificatie hebt die gebruikmaakt van een ander domein, kunt u deze converteren naar **comprimeren** in de project instellingen. Train vervolgens het project opnieuw voordat u doorgaat.
1. Exporteer uw model. Ga naar het tabblad prestaties en selecteer een herhaling die is getraind met een **compact** domein. Selecteer de knop **exporteren** die wordt weer gegeven. Selecteer vervolgens **ONNX** en vervolgens **exporteren**. Zodra het bestand gereed is, selecteert u de knop **Downloaden**. Zie [uw model exporteren](./export-your-model.md)voor meer informatie over export opties.
1. Open het gedownloade *zip* -bestand en pak het bestand *model. onnx* uit. Dit bestand bevat uw classificatie model.
1. Klik in de Solution Explorer in Visual Studio met de rechter muisknop op de map **assets** en selecteer __bestaand item toevoegen__. Selecteer uw ONNX-bestand.
1. In Solution Explorer klikt u met de rechter muisknop op het ONNX-bestand en selecteert u **Eigenschappen**. Wijzig de volgende eigenschappen voor het bestand:
   * __Actie maken__  ->  __Inhoud__
   * __Naar uitvoermap kopiëren__  ->  __Kopiëren indien nieuwer__
1. Open vervolgens _MainPage. xaml. cs_ en wijzig de waarde van `_ourOnnxFileName` in de naam van het ONNX-bestand.
1. Gebruik de `F5` om het project te bouwen en uit te voeren.
1. Klik op de knop om de afbeelding die u wilt evalueren te selecteren.

## <a name="next-steps"></a>Volgende stappen

Als u andere manieren wilt ontdekken om een Custom Vision-model te exporteren en te gebruiken, raadpleegt u de volgende documenten:

* [Uw model exporteren](./export-your-model.md)
* [Geëxporteerd Tensorflow-model in een Android-toepassing gebruiken](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Geëxporteerd CoreML-model in een Swift iOS-toepassing gebruiken](https://go.microsoft.com/fwlink/?linkid=857726)
* [Geëxporteerd CoreML-model in een iOS-toepassing gebruiken met Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Zie voor meer informatie over het gebruik van ONNX-modellen met Windows ML [een model in uw app integreren met Windows ml](/windows/ai/windows-ml/integrate-model).