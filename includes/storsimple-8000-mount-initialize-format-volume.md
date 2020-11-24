---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 23d0f16e025727f8d733f973a2c751ba7114fa49
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557650"
---
#### <a name="to-mount-initialize-and-format-a-volume"></a>Een volume koppelen, initialiseren en formatteren
1. Start de Microsoft iSCSI-initiator.
2. Klik in het venster **Eigenschappen iSCSI-initiator** op het tabblad **Detectie** op **Portal detecteren**.
3. Geef in het dialoogvenster **Doelportaal detecteren** het IP-adres op van de netwerkinterface met iSCSI-functionaliteit en klik vervolgens op **OK**. 
4. Ga in het venster **Eigenschappen iSCSI-initiator** op het tabblad **Doelen** naar **Gedetecteerde doelen**. De status van het apparaat moet worden weergegeven als **Inactief**.
5. Selecteer het doelapparaat en klik vervolgens op **Verbinden**. Nadat het apparaat is verbonden, moet de status zijn gewijzigd in **Verbonden**. (Zie [Installing and Configuring Microsoft iSCSI Initiator][1] (Microsoft iSCSI-initiator installeren en configureren) voor meer informatie over het gebruik van de Microsoft iSCSI-initiator.)
6. Druk op uw Windows-hots op de Windows-toets + X en klik vervolgens op **Uitvoeren**. 
7. Typ in het dialoogvenster **Uitvoeren****Diskmgmt.msc**. Klik op **OK**. Het dialoogvenster **Schijfbeheer** wordt weergegeven. In het rechterdeelvenster worden de volumes op uw host weergeven.
8. In het venster **Schijfbeheer** worden de gekoppelde volumes weergegeven zoals in de volgende afbeelding. Klik met de rechtermuisknop op het gedetecteerde volume (klik op de naam van de schijf) en klik vervolgens op **Online**.
   
     ![Opmaakvolume initialiseren](./media/storsimple-8000-mount-initialize-format-volume/step7initializeformatvolume.png) 
9. Klik met de rechtermuisknop op het volume (klik op de naam van de schijf) en klik vervolgens op **Initialiseren**.
10. Om een eenvoudig volume te formatteren, moet u de volgende stappen uitvoeren:
    
    1. Selecteer het volume, klik er met de rechtermuisknop op (klik op het juiste gebied) en klik vervolgens op **Nieuw eenvoudig volume**.
    2. Geef in de wizard Nieuw eenvoudig volume de volumegrootte en de stationsletter op en configureer het volume als een NTFS-bestandssysteem.
    3. Geef een clustergrootte van 64 kB op. Deze clustergrootte werkt goed samen met de ontdubbelingsalgoritmen die worden gebruikt in de StorSimple-oplossing.
    4. Voer een snelle formattering uit.

<!--Link references-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee338480(v=ws.10)