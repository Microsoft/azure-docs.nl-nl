---
title: bestand opnemen
description: bestand opnemen
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 386a39d76bf47da45f07054124886e174104849e
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545293"
---
Als u een volume container wilt verwijderen, moet u
 - Verwijder de volumes in de volume container. Als er volumes aan de volume container zijn gekoppeld, moet u deze volumes eerst offline halen. Volg de stappen in [een volume offline halen](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline). Nadat de volumes offline zijn, kunt u ze verwijderen. 
 - het gekoppelde back-upbeleid en de Cloud momentopnamen verwijderen. Controleer of er back-upbeleid en Cloud momentopnamen zijn gekoppeld aan de volume container. Als dit het geval is, verwijdert u vervolgens [het back-upbeleid](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy). Hierdoor worden ook de Cloud momentopnamen verwijderd. 
 
Wanneer de volume container geen gekoppelde volumes, back-upbeleid en Cloud momentopnamen heeft, kunt u deze verwijderen. Voer de volgende procedure uit om een volume container te verwijderen.

#### <a name="to-delete-a-volume-container"></a>Een volume container verwijderen

1. Ga naar de StorSimple-apparaatbeheerservice en klik op **Apparaten**. Selecteer en klik op het apparaat en ga vervolgens naar **instellingen > > volume containers te beheren**.

    ![Blade volume containers](./media/storsimple-8000-delete-volume-container/create-volume-container.png)

2. Selecteer in de lijst in tabel vorm van volume containers de volume container die u wilt verwijderen, klik met de rechter muisknop **...** en selecteer vervolgens **verwijderen**.

    ![Volume container verwijderen](./media/storsimple-8000-delete-volume-container/delete-volume-container-01.png)

3. Als een volume container geen gekoppelde volumes, een back-upbeleid en Cloud momentopnamen heeft, kan deze worden verwijderd. Als u om bevestiging wordt gevraagd, controleert en selecteert u het selectie vakje met de impact van het verwijderen van de volume container. Klik op **verwijderen** om de volume container te verwijderen.

    ![Verwijdering bevestigen](./media/storsimple-8000-delete-volume-container/delete-volume-container-02.png)

De lijst met volume containers wordt bijgewerkt om de verwijderde volume container weer te geven.

![Scherm afbeelding van de pagina met de volume container. De tabel lijst met volume containers bevat de verwijderde container niet meer.](./media/storsimple-8000-delete-volume-container/delete-volume-container-05.png)
