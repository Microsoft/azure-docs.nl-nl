---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: f08a6b3f7abfc79bff6baff2a339053905612535
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96026452"
---
#### <a name="to-attach-the-sas-cables"></a>De SAS-kabels koppelen
1. De primaire en de EBOD-behuizingen identificeren. U kunt de twee behuizingen identificeren door te kijken naar hun respectieve back-upplannen. Raadpleeg de volgende afbeelding voor hulp. 
   
    ![Achtergrond vlak van primaire en EBOD Behuizingen](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)
   
    **Weer gave van primaire en EBOD Behuizingen**
   
   | Label | Beschrijving |
   |:--- |:--- |
   | 1 |Primaire behuizing |
   | 2 |EBOD-behuizing |
2. Zoek de serie nummers op de primaire en de EBOD-behuizingen. De sticker met het serie nummer wordt op de achterkant van elke behuizing aangebracht. De serie nummers moeten op beide behuizingen identiek zijn. [Neem direct contact op met Microsoft ondersteuning](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) als de serie nummers niet overeenkomen. Bekijk de volgende afbeelding om de serie nummers te vinden.
   
    ![Achteraanzicht van de behuizing met het serie nummer](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)
   
    **Locatie van sticker voor serie nummer**
   
   | Label | Beschrijving |
   |:--- |:--- |
   | 1 |Het oor van de behuizing |
3. Gebruik de meegeleverde SAS-kabels om de EBOD-behuizing als volgt aan de primaire behuizing te koppelen:
   
   1. Identificeer de vier SAS-poorten op de primaire behuizing en de EBOD-behuizing. De SAS-poorten worden aangeduid als EBOD op de primaire behuizing en komen overeen met poort A in de EBOD-behuizing, zoals wordt weer gegeven in de afbeelding voor SAS-bekabeling hieronder.
   2. Gebruik de meegeleverde SAS-kabels om de EBOD-poort aan poort A te koppelen.
   3. De EBOD-poort op controller 0 moet zijn verbonden met de poort A op de EBOD-controller 0. De EBOD-poort op controller 1 moet zijn verbonden met de poort A op EBOD controller 1. Raadpleeg de volgende afbeelding voor hulp. 
      
      ![SAS-bekabeling voor uw apparaat](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)
      
      **SAS-bekabeling**
      
      | Label | Beschrijving |
      |:--- |:--- |
      | A |Primaire behuizing |
      | B |EBOD-behuizing |
      | 1 |Controller 0 |
      | 2 |Controller 1 |
      | 3 |EBOD-controller 0 |
      | 4 |EBOD-controller 1 |
      | 5, 6 |SAS-poorten op primaire behuizing (gelabelde EBOD) |
      | 7, 8 |SAS-poorten op EBOD Enclosure (poort A) |