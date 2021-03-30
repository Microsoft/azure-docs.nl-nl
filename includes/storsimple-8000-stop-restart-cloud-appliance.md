---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a01f91a81629800d3f03b907c65f05433b6163e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93376255"
---
#### <a name="to-stop-and-start-a-cloud-appliance"></a>Een cloudapparaat starten en stoppen

1. Als u een cloudapparaat wilt stoppen, gaat u naar de virtuele machine voor uw cloudapparaat.
    ![StorSimple-cloudapparaat - virtuele machine](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart1.png)

2. Klik vanuit de opdrachtbalk op **Stoppen**.

    ![Virtuele machine StorSimple Cloud Appliance 2](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart2.png)

3. Klik op **Ja** als u om bevestiging wordt gevraagd.

    ![StorSimple Cloud Appliance virtuele machine 3](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart3.png)

4. Wanneer u een virtuele machine stopt, wordt de toewijzing ervan opgeheven. Terwijl het cloudapparaat wordt gestopt, is de status ervan **Toewijzing ongedaan maken**. Nadat het cloudapparaat is gestopt, is de status ervan **Gestopt (toewijzing opgeheven)**.

    ![StorSimple Cloud Appliance virtuele machine 4](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart4.png)

5. Nadat een virtuele machine is gestopt, klikt u op **Starten** (knop wordt beschikbaar) om de virtuele machine te starten. Nadat het cloudapparaat is gestart, wordt de status ervan **Gestart**.

    ![StorSimple Cloud Appliance virtuele machine 5](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart5.png)

Gebruik de volgende cmdlets om een cloudapparaat te stoppen en te starten.

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

#### <a name="to-restart-a-cloud-appliance"></a>Een cloudapparaat opnieuw opstarten

Als u een cloudapparaat opnieuw wilt opstarten, gaat u naar de virtuele machine voor uw cloudapparaat. Klik vanuit de opdrachtbalk op **Opnieuw starten**. Bevestig het opnieuw starten als u daarom wordt gevraagd. Wanneer het cloudapparaat gereed is voor gebruik, is de status **Wordt uitgevoerd**.

![StorSimple Cloud Appliance virtuele machine 6](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart6.png)

Gebruik de volgende cmdlet om een cloudapparaat opnieuw op te starten.

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

