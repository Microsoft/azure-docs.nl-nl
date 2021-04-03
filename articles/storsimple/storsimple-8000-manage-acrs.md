---
title: Access Control records beheren in StorSimple | Microsoft Docs
description: Hierin wordt beschreven hoe u een ACRs-record (Access Control records) gebruikt om te bepalen welke hosts verbinding kunnen maken met een volume op het StorSimple-apparaat.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: alkohli
ms.openlocfilehash: 15c35fb314af27b1ced129a12f752d0a2794e0f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91949901"
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>De StorSimple Manager-service gebruiken voor het beheren van records voor Access Control

## <a name="overview"></a>Overzicht
Met Access Control records (ACRs) kunt u opgeven welke hosts verbinding kunnen maken met een volume op het StorSimple-apparaat. ACRs worden ingesteld op een specifiek volume en bevatten de iSCSI-gekwalificeerde namen (IQNs) van de hosts. Wanneer een host verbinding probeert te maken met een volume, controleert het apparaat de ACR die aan dat volume is gekoppeld voor de IQN-naam. als er een overeenkomst wordt gevonden, wordt de verbinding tot stand gebracht. Met de Access Control records in de **configuratie** sectie van de Blade StorSimple Apparaatbeheer service worden alle Access Control records met de bijbehorende IQNs van de hosts weer gegeven.

In deze zelf studie worden de volgende algemene taken met betrekking tot ACR uitgelegd:

* Een Access Control Record toevoegen
* Een Access Control Record bewerken
* Een Access Control Record verwijderen

> [!IMPORTANT]
> * Wanneer u een ACR aan een volume toewijst, moet u ervoor zorgen dat het volume niet gelijktijdig wordt geopend door meer dan één niet-geclusterde host, omdat dit het volume kan beschadigen.
> * Wanneer u een ACR van een volume verwijdert, moet u ervoor zorgen dat de bijbehorende host geen toegang heeft tot het volume omdat het verwijderen kan leiden tot een lees-schrijf onderbreking.

## <a name="get-the-iqn"></a>De IQN ophalen

Voer de volgende stappen uit om de IQN van een Windows-host te verkrijgen waarop Windows Server 2012 wordt uitgevoerd.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Een Access Control Record toevoegen
U gebruikt de **configuratie** sectie op de Blade StorSimple Apparaatbeheer service om ACRs toe te voegen. Normaal gesp roken koppelt u één ACR aan één volume.

Voer de volgende stappen uit om een ACR toe te voegen.

#### <a name="to-add-an-acr"></a>Een ACR toevoegen

1. Ga naar de StorSimple-Apparaatbeheer service, dubbel klik op de service naam en klik vervolgens in de sectie **configuratie** op **Access Control records**.
2. Klik op de Blade **toegangs beheer records** op **+ ACR toevoegen**.

    ![Klik op ACR toevoegen](./media/storsimple-8000-manage-acrs/createacr1.png)

3. Voer op de Blade **ACR toevoegen** de volgende stappen uit:

    1. Geef een naam op voor uw ACR.
    
    2. Geef de IQN-naam van uw Windows Server-host op onder **iSCSI-initiator naam (IQN)**.

    3. Klik op **toevoegen** om de ACR te maken.

        ![Klik op ACR toevoegen 2.](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  De zojuist toegevoegde ACR wordt weer gegeven in de lijst in tabel vorm van ACRs.

    ![Klik op ACR toevoegen 3](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Een Access Control Record bewerken
U gebruikt de **configuratie** sectie op de Blade StorSimple Apparaatbeheer service om ACRs te bewerken.

> [!NOTE]
> Het is raadzaam dat u alleen de ACRs wijzigt die momenteel niet in gebruik zijn. Als u een ACR wilt bewerken die is gekoppeld aan een volume dat momenteel in gebruik is, moet u eerst het volume offline halen.

Voer de volgende stappen uit om een ACR te bewerken.

#### <a name="to-edit-an-access-control-record"></a>Een Access Control Record bewerken
1.  Ga naar de StorSimple-Apparaatbeheer service, dubbel klik op de service naam en klik vervolgens in de sectie **configuratie** op **Access Control records**.

    ![Ga naar Access Control records](./media/storsimple-8000-manage-acrs/createacr1.png)

2. In de lijst in tabel vorm van de Access Control records klikt en selecteert u de ACR die u wilt wijzigen.

    ![Access Control records bewerken](./media/storsimple-8000-manage-acrs/editacr1.png)

3. Geef op de Blade **bewerken Access Control record** een andere IQN op die overeenkomt met een andere host.

    ![Access Control records bewerken 2](./media/storsimple-8000-manage-acrs/editacr2.png)

4. Klik op **Opslaan**. Klik op **Ja** als u om bevestiging wordt gevraagd. 

    ![Access Control records bewerken 3](./media/storsimple-8000-manage-acrs/editacr3.png)

5. U wordt gewaarschuwd wanneer de ACR wordt bijgewerkt. De lijst in tabel vorm wordt ook bijgewerkt om de wijziging weer te geven.

   
## <a name="delete-an-access-control-record"></a>Een Access Control Record verwijderen
U gebruikt de **configuratie** sectie op de Blade StorSimple Apparaatbeheer service om ACRs te verwijderen.

> [!NOTE]
> U kunt alleen de ACRs verwijderen die momenteel niet in gebruik zijn. Als u een ACR wilt verwijderen die is gekoppeld aan een volume dat momenteel in gebruik is, moet u eerst het volume offline halen.

Voer de volgende stappen uit om een Access Control Record te verwijderen.

#### <a name="to-delete-an-access-control-record"></a>Een Access Control Record verwijderen
1.  Ga naar de StorSimple-Apparaatbeheer service, dubbel klik op de service naam en klik vervolgens in de sectie **configuratie** op **Access Control records**.

    ![Ga naar Access Control records 1B](./media/storsimple-8000-manage-acrs/createacr1.png)

2. In de lijst in tabel vorm van de Access Control records klikt en selecteert u de ACR die u wilt verwijderen.

    ![Ga naar Access Control records 2](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Klik met de rechter muisknop om het context menu aan te roepen en selecteer **verwijderen**.

    ![Ga naar Access Control records 3](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. Als u om bevestiging wordt gevraagd, controleert u de informatie en klikt u vervolgens op **verwijderen**.

    ![Ga naar Access Control records 4](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. U wordt gewaarschuwd wanneer het verwijderen is voltooid. De lijst in tabel vorm wordt bijgewerkt om de verwijdering weer te geven.

    ![Ga naar Access Control records 5](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [beheren van StorSimple-volumes](storsimple-8000-manage-volumes-u2.md).
* Meer informatie over [het gebruik van de StorSimple Manager-service voor het beheren van uw StorSimple-apparaat](storsimple-8000-manager-service-administration.md).

