---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9b9922602218280d58331a755ed0dfed7df96f40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67176071"
---
#### <a name="to-cable-your-device-for-power"></a>Uw apparaat bekabelen voor energie beheer
> [!NOTE]
> Beide behuizingen op uw StorSimple-apparaat bevatten redundante PCMs. Voor elke behuizing moet de PCMs zijn geïnstalleerd en verbonden met verschillende energie bronnen om hoge Beschik baarheid te garanderen.
> 
> 

1. Zorg ervoor dat de energie switches op alle PCMs zich in de buiten stand bevinden.
2. Op de primaire behuizing verbindt u de stroom kabels met beide PCMs. De stroom snoeren worden rood aangeduid in het onderstaande diagram voor energie bekabeling.
3. Zorg ervoor dat de twee PCMs op de primaire behuizing afzonderlijke energie bronnen gebruiken.
4. Koppel de netsnoeren aan de kracht van de rack distributie-eenheden, zoals wordt weer gegeven in het diagram energie bekabeling.
5. Herhaal stap 2 t/m 4 voor de EBOD behuizing.
6. Schakel de EBOD-behuizing in door de aan/uit-schakelaar op elke PCM te spie gelen.
7. Controleer of de EBOD-behuizing is ingeschakeld door te controleren of de groene Led's aan de achterkant van de EBOD-controller zijn ingeschakeld.
8. Schakel de primaire behuizing in door elke PCM-switch naar de op positie te spie gelen.
9. Controleer of het systeem is ingeschakeld door de Led's van de controller in te scha kelen.
10. Zorg ervoor dat de verbinding tussen de EBOD-controller en de apparaat-controller actief is door te controleren of de vier Led's naast de SAS-poort op de EBOD-controller groen zijn.
    
    > [!IMPORTANT]
    > Om hoge Beschik baarheid voor uw systeem te garanderen, wordt u aangeraden strikt te voldoen aan het energie bekabelings schema dat in het volgende diagram wordt weer gegeven.
    > 
    > 
    
    ![Uw 4U-apparaat bekabelen voor energie beheer](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **Stroom kabels**
    
    | Label | Beschrijving |
    |:--- |:--- |
    | 1 |Primaire behuizing |
    | 2 |PCM 0 |
    | 3 |PCM 1 |
    | 4 |Controller 0 |
    | 5 |Controller 1 |
    | 6 |EBOD-controller 0 |
    | 7 |EBOD-controller 1 |
    | 8 |EBOD-behuizing |
    | 9 |Pdu's |

