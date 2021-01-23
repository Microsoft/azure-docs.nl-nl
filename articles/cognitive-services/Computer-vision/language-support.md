---
title: Taal ondersteuning-Computer Vision
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat een lijst met natuurlijke talen die worden ondersteund door Computer Vision-functies. OCR, afbeeldings analyse.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 1abb857c9f03be502db02099383c6fe0b5110461
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736815"
---
# <a name="language-support-for-computer-vision"></a>Taal ondersteuning voor Computer Vision

Sommige functies van Computer Vision ondersteunen meerdere talen; alle functies die hier niet worden vermeld, bieden alleen ondersteuning voor Engels.

## <a name="optical-character-recognition-ocr"></a>Optische tekenherkenning (OCR)

De OCR-Api's van Computer Vision ondersteunen verschillende talen. U hoeft geen taal code op te geven. Zie [optische teken herkenning (OCR)](concept-recognizing-text.md) voor meer informatie.

|Taal| Taalcode | OCR-API | 3,0 en 3,1 lezen | Lezen v 3.2-Preview. 1 |
|:-----|:----:|:-----:|:---:|:---:|
|Arabisch | `ar`|✔ | | |
|Chinees (Vereenvoudigd) | `zh-Hans`|✔ | |✔ |
|Chinees (Traditioneel) | `zh-Hant`|✔ | | |
|Tsjechisch | `cs` |✔ | | |
|Deens | `da` |✔ | | |
|Nederlands | `nl` |✔ |✔ |✔ |
|Engels | `en` |✔ |✔ |✔ |
|Fins | `fi` |✔ | | |
|Frans | `fr` |✔ |✔ |✔ |
|Duits | `de` |✔ |✔ |✔ |
|Grieks | `el` |✔ | | |
|Hongaars | `hu` |✔ | | |
|Italiaans | `it` |✔ |✔ |✔ |
|Japans | `ja` |✔ | |✔ |
|Koreaans | `ko` |✔ | | |
|Noors | `nb` |✔ | | |
|Pools | `pl` |✔ | | |
|Portugees | `pt` |✔ |✔ |✔ |
|Roemeens | `ro` |✔ | | |
|Russisch | `ru` |✔ | | |
|Servisch (Cyrillisch) | `sr-Cyrl` |✔ | | |
|Servisch (Latijns) | `sr-Latn` |✔ | | |
|Slowaaks | `sk` |✔ | | |
|Spaans | `es` |✔ |✔ |✔ |
|Zweeds | `sw` |✔ | | |
|Turks | `tr` |✔ | | |

## <a name="image-analysis"></a>Afbeeldingsanalyse

Sommige acties van de [analyse-image-](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API kunnen resultaten retour neren in andere talen, opgegeven met de `language` query-para meter. Andere acties retour neren resultaten in het Engels, ongeacht de taal die is opgegeven, en anderen genereren een uitzonde ring voor niet-ondersteunde talen. Acties zijn opgegeven met de `visualFeatures` `details` para meters en query; Zie het [overzicht](overview.md) voor een lijst met alle acties die u kunt uitvoeren met afbeeldings analyse.

|Taal | Taalcode | Categorieën | Tags | Beschrijving | Volwassene | Merken | Kleur | Gezichten | ImageType | Objecten | Beroemdheden | Oriëntatiepunten |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Chinees | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Engels | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japans | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portugees | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Spaans | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met de Computer Vision-functies die in deze hand leiding worden beschreven.

* [Een lokale installatie kopie analyseren (REST)](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-analyze.md)
* [Afgedrukte tekst extra heren (REST)](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-print-text.md)