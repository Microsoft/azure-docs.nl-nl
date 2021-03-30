---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: db2104020e9478b1fedf68e1c9467f75e16044e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67176072"
---
#### <a name="to-cable-for-power"></a>Kabel voor energie verbruik
1. Zorg ervoor dat de Schakel opties voor elk van de energiebeheer modules (PCMs) zijn uitgeschakeld.
2. Verbind de stroom snoeren met elk van de PCMs in de primaire behuizing.
3. Koppel de netsnoeren aan de PDU (rack stroom), zoals wordt weer gegeven in de volgende afbeelding. Zorg ervoor dat de twee PCMs afzonderlijke energie bronnen gebruiken.
   
   > [!IMPORTANT]
   > Om hoge Beschik baarheid voor uw systeem te garanderen, wordt u aangeraden strikt te voldoen aan het energie bekabelings schema dat in het volgende diagram wordt weer gegeven. 
   > 
   > 
   
    ![Uw 2U-apparaat bekabelen voor energie beheer](./media/storsimple-cable-8100-for-power/HCSCableYour2UDeviceforPower.png)
   
    **Stroom kabels op een 8100-apparaat**
   
   | Label | Beschrijving |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |Controller 1 |
   | 3 |Controller 0 |
   | 4 |PCM 1 |
   | 5 |Pdu's |
4. Om het systeem in te scha kelen, spiegelt u de energie switches van beide PCMs naar de op stand.

