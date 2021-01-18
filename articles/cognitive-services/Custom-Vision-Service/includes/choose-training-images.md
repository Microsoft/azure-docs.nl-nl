---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: d07a5da3b9013700694f6c20102ef2e8c5066087
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98256459"
---
U wordt ten zeerste aangeraden minimaal dertig afbeeldingen per tag in de eerste trainingsset te gebruiken. U dient ook een paar extra afbeeldingen te verzamelen om uw model te testen zodra het is getraind.

Gebruik afbeeldingen met optische variaties om uw model effectief te kunnen trainen. Selecteer afbeeldingen die variëren in:
* camerahoek
* belichting
* background
* visuele stijl
* afzonderlijke/gegroepeerde onderwerpen
* grootte
* type

Zorg er bovendien voor dat al uw trainingsafbeeldingen aan de volgende criteria voldoen:
* JPG-, PNG-, BMP- of GIF-indeling
* niet groter dan 6 MB (4 MB voor voorspellingsafbeeldingen)
* een korte zijde van minimaal 256 pixels; afbeeldingen die korter zijn dan deze worden automatisch omhoog geschaald

> [!NOTE]
> Met Trove, een Microsoft Garage-project, kunt u sets afbeeldingen verzamelen en aanschaffen voor trainingsdoeleinden. Wanneer u uw afbeeldingen hebt verzameld kunt u ze downloaden en importeren naar uw Custom Vision-project. Ga naar de pagina [Trove](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3) voor meer informatie.