---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 5508199212b788e13a27b3c97b8fb7174dc92f32
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99531685"
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
> Hebt u een grotere set installatie kopieën nodig om uw training te volt ooien? Met Trove, een Microsoft Garage-project, kunt u sets afbeeldingen verzamelen en aanschaffen voor trainingsdoeleinden. Wanneer u uw afbeeldingen hebt verzameld kunt u ze downloaden en importeren naar uw Custom Vision-project. Ga naar de pagina [Trove](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3) voor meer informatie.