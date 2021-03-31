---
title: Access Control records beheren voor de virtuele StorSimple-matrix | Microsoft Docs
description: Hierin wordt beschreven hoe u de Access Control records (ACRs) beheert om te bepalen welke hosts verbinding kunnen maken met een volume op de virtuele StorSimple-matrix.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: e154bb4f-faab-4d92-a593-900c3ddc9595
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bad0d7adfd77dff53b1582e63a91f2cd87a9233d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85507616"
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>StorSimple Apparaatbeheer gebruiken voor het beheren van Access Control records voor StorSimple Virtual array

## <a name="overview"></a>Overzicht

Met de Access Control records (ACRs) kunt u opgeven welke hosts verbinding kunnen maken met een volume op de virtuele StorSimple-matrix (ook wel bekend als het StorSimple on-premises virtuele apparaat). ACRs worden ingesteld op een specifiek volume en bevatten de iSCSI-gekwalificeerde namen (IQNs) van de hosts. Wanneer een host verbinding probeert te maken met een volume, controleert het apparaat de ACR die aan dat volume is gekoppeld voor de IQN-naam. als er een overeenkomst wordt gevonden, wordt de verbinding tot stand gebracht. Op de Blade **toegangs beheer records** in de **configuratie** sectie van uw Apparaatbeheer-service worden alle Access Control records weer gegeven met de bijbehorende IQNs van de hosts.

![Access Control records beheren](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

In deze zelf studie worden de volgende algemene taken met betrekking tot ACR uitgelegd:

* De IQN ophalen
* Een Access Control Record toevoegen
* Een Access Control Record bewerken
* Een Access Control Record verwijderen

> [!IMPORTANT]
> 
> * Wanneer u een ACR aan een volume toewijst, moet u ervoor zorgen dat het volume niet gelijktijdig wordt geopend door meer dan één niet-geclusterde host, omdat dit het volume kan beschadigen.
> * Wanneer u een ACR van een volume verwijdert, moet u ervoor zorgen dat de bijbehorende host geen toegang heeft tot het volume omdat het verwijderen kan leiden tot een lees-schrijf onderbreking.


## <a name="get-the-iqn"></a>De IQN ophalen

Voer de volgende stappen uit om de IQN van een Windows-host te verkrijgen waarop Windows Server 2012 wordt uitgevoerd.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Een ACR toevoegen

U kunt de Blade **toegangs beheer records** gebruiken in de **configuratie** sectie van uw StorSimple Apparaatbeheer-service om ACRs toe te voegen. Normaal gesp roken koppelt u één ACR aan één volume.

Voor informatie over het koppelen van een ACR aan een volume, gaat u naar [een volume toevoegen](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> Wanneer u een ACR aan een volume toewijst, moet u ervoor zorgen dat het volume niet gelijktijdig wordt geopend door meer dan één niet-geclusterde host, omdat dit het volume kan beschadigen.


Voer de volgende stappen uit om een ACR toe te voegen.

#### <a name="to-add-an-acr"></a>Een ACR toevoegen

1. Selecteer uw service op de pagina landings aanvoer, dubbel klik op de service naam en klik vervolgens in de sectie **configuratie** op **Access Control records**.
2. Klik op de Blade **toegangs beheer records** op **toevoegen**.
3. Ga op de Blade **ACR toevoegen** als volgt te werk:
   
    1. Geef een **naam** voor uw ACR op.
    
    2. Geef onder **naam van iSCSI-initiator** de IQN-naam van uw Windows-host op. Ga als volgt te werk om de IQN van uw Windows Server-host op te halen:
   
    3. Start de Microsoft iSCSI-initiator op uw Windows-host. Selecteer en kopieer in het venster Eigenschappen iSCSI-initiator op het tabblad **Configuratie** de tekenreeks in het veld **Naam van initiator**.
    Plak deze teken reeks in het veld **IQN** op de Blade **ACR toevoegen** .
   
    6. Klik op **toevoegen** om de ACR toe te voegen.  
   
        ![Access Control records toevoegen](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. De lijst in tabel vorm wordt bijgewerkt om deze toevoeging weer te geven.

## <a name="edit-an-acr"></a>Een ACR bewerken

U gebruikt de Blade **toegangs beheer records** in de **configuratie** sectie van uw Apparaatbeheer-service in de Azure Portal om ACRs te bewerken.

> [!NOTE]
> U moet geen ACR wijzigen die momenteel in gebruik is. Als u een ACR wilt bewerken die is gekoppeld aan een volume dat momenteel in gebruik is, moet u het volume eerst offline halen.


Voer de volgende stappen uit om een ACR te bewerken.

#### <a name="to-edit-an-acr"></a>Een ACR bewerken

1. Selecteer uw service op de pagina landings aanvoer, dubbel klik op de service naam en klik vervolgens in de sectie **configuratie** op **Access Control records**.
2. Dubbel klik in de Blade **toegangs beheer records** in de lijst in tabel vorm van de Access Control records op de ACR die u wilt wijzigen.
3. Ga als volgt te werk op de Blade **toegangs beheer records bewerken** :
   
    1. Geef de IQN op voor de ACR.
   
    2. Klik op **Opslaan** boven aan de Blade om de gewijzigde ACR op te slaan. U ziet het volgende bevestigings bericht:
   
        ![Access Control records bewerken](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>Een Access Control Record verwijderen

U gebruikt de pagina **configuratie** in het Azure Portal om ACRs te verwijderen.

> [!NOTE]
> 
> * U moet geen ACR verwijderen die momenteel in gebruik is. Als u een ACR wilt verwijderen die is gekoppeld aan een volume dat momenteel in gebruik is, moet u het volume eerst offline halen.
> * Wanneer u een ACR van een volume verwijdert, moet u ervoor zorgen dat de bijbehorende host geen toegang heeft tot het volume omdat het verwijderen kan leiden tot een lees-schrijf onderbreking.


Voer de volgende stappen uit om een Access Control Record te verwijderen.

#### <a name="to-delete-an-access-control-record"></a>Een Access Control Record verwijderen

1. Selecteer uw service op de pagina landings aanvoer, dubbel klik op de service naam en klik vervolgens in de sectie **configuratie** op **Access Control records**.

2. Dubbel klik in de Blade **toegangs beheer records** in de lijst in tabel vorm van de Access Control records op de ACR die u wilt verwijderen.

3. Klik op de Blade toegangs beheer records bewerken op **verwijderen**.
   
    ![ACRS verwijderen](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. Wanneer u om bevestiging wordt gevraagd, klikt u op **verwijderen** om door te gaan met het verwijderen. De lijst in tabel vorm wordt bijgewerkt om de verwijdering weer te geven.
   
   ![Waarschuwings bericht](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [toevoegen van volumes en het configureren van ACRs](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

