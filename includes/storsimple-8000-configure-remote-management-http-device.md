---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 473bc0a58fe49c7f454c81402b57ddce7fc745b2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67176096"
---
#### <a name="to-configure-remote-management-on-cloud-appliance"></a>Extern beheer configureren op het cloudapparaat

1. Klik in uw StorSimple-apparaatbeheerfunctie op **Apparaten**. Selecteer en klikt u op uw cloudapparaat in de lijst met apparaten die zijn verbonden met de service.
    ![StorSimple- cloudapparaat selecteren](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage1.png)

2. Ga naar **Instellingen > Beveiliging** om de blade **Beveiligingsinstellingen** te openen.

     ![StorSimple-beveiligingsinstellingen](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage2.png)

3. Ga naar de sectie **Extern beheer**. Klik op het vak **Extern beheer**.
     ![Extern beheer van StorSimple](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage3.png)

4. Doe het volgende op de blade **Extern beheer**:

    1. Controleer of **Extern beheer inschakelen** is ingeschakeld.
    2. De standaardinstelling is verbinding maken via HTTPS. U kunt er nu voor kiezen verbinding te maken via HTTP. Verbinding maken via HTTP is alleen acceptabel in vertrouwde netwerken. Controleer of HTTP is ingeschakeld.
    3. Klik in de opdracht balk boven aan de Blade op **... Meer** en klik vervolgens op **certificaat downloaden** om een certificaat voor extern beheer te downloaden. U kunt een locatie opgeven waar dit bestand moet worden opgeslagen. Dit certificaat moet vervolgens worden geïnstalleerd op de client of hostcomputer die u gebruikt voor de verbinding met het cloudapparaat.

        ![De blade Extern beheer](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage4.png)
5. Klik op **Opslaan** en bevestig de wijzigingen wanneer u daarom wordt gevraagd.