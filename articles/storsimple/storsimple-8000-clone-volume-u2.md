---
title: Een volume klonen op de StorSimple 8000-serie | Microsoft Docs
description: Hierin worden de verschillende typen klonen en het gebruik beschreven en wordt uitgelegd hoe u een back-upset kunt gebruiken om een afzonderlijk volume te klonen op een StorSimple 8000 Series-apparaat.
services: storsimple
documentationcenter: NA
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.date: 07/15/2020
ms.author: alkohli
ms.openlocfilehash: 5ebb7f1d9440bc7a8b75afe64403762532213c1d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91767575"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>StorSimple Device Manager-service in Azure-portal gebruiken om een volume te klonen

## <a name="overview"></a>Overzicht

In deze zelf studie wordt beschreven hoe u een back-upset kunt gebruiken om een afzonderlijk volume te klonen via de Blade **back-upcatalogus** . Er wordt ook uitgelegd wat het verschil is tussen *tijdelijke* en *permanente* klonen. De richt lijnen in deze zelf studie zijn van toepassing op alle StorSimple 8000 Series-apparaten met update 3 of hoger.

Op de Blade StorSimple Apparaatbeheer service **back-upcatalogus** worden alle back-upsets weer gegeven die worden gemaakt wanneer hand matig of automatisch maken van back-ups worden uitgevoerd. U kunt vervolgens een volume in een back-upset selecteren dat u wilt klonen.

 ![Lijst met back-upsets](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>Overwegingen voor het klonen van een volume

Houd rekening met de volgende informatie bij het klonen van een volume.

- Een kloon gedraagt zich op dezelfde manier als een gewoon volume. Elke bewerking die mogelijk is op een volume, is beschikbaar voor de kloon.

- Bewaking en standaard back-ups worden automatisch uitgeschakeld op een gekloond volume. U moet een gekloond volume configureren voor back-ups.

- Een lokaal vastgemaakt volume wordt gekloond als een gelaagd volume. Als u wilt dat het gekloonde volume lokaal is vastgemaakt, kunt u de kloon converteren naar een lokaal vastgemaakt volume nadat de kloon bewerking is voltooid. Voor informatie over het converteren van een gelaagd volume naar een lokaal vastgemaakt volume, gaat u naar [het volume type wijzigen](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).

- Als u probeert een gekloond volume te converteren van gelaagd naar lokaal vastgemaakt, direct na het klonen (wanneer het nog steeds een tijdelijke kloon is), mislukt de conversie met het volgende fout bericht:

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    Deze fout wordt alleen ontvangen als u op een ander apparaat kloont. U kunt het volume converteren naar lokaal vastgemaakt als u de tijdelijke kloon eerst converteert naar een permanente kloon. Maak een Cloud momentopname van de tijdelijke kloon om deze om te zetten in een permanente kloon.

## <a name="create-a-clone-of-a-volume"></a>Een kloon van een volume maken

U kunt een kloon maken op hetzelfde apparaat, op een ander apparaat of zelfs op een Cloud apparaat door een lokale of een Cloud momentopname te gebruiken.

In de volgende procedure wordt beschreven hoe u een kloon maakt vanuit de back-catalogus.  

Voer de volgende stappen uit om een kloon van uw volume te maken uit de back-upcatalogus.

#### <a name="to-clone-a-volume"></a>Een volume klonen

1. Ga naar de StorSimple-Apparaatbeheer service en klik vervolgens op **back-upcatalogus**.

2. Selecteer een back-upset als volgt:
   
   1. Selecteer het betreffende apparaat.
   2. Kies in de vervolg keuzelijst het volume of back-upbeleid voor de back-up die u wilt selecteren.
   3. Geef het tijds bereik op.
   4. Klik op **Toep assen** om deze query uit te voeren.

      De back-ups die zijn gekoppeld aan het geselecteerde volume of back-upbeleid, moeten worden weer gegeven in de lijst met back-upsets.
   
      ![Lijst met back-upsets 2](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. Vouw de back-upset uit om het bijbehorende volume weer te geven en selecteer een volume in een back-upset. Klik met de rechter muisknop en selecteer vervolgens **klonen** in het context menu.

   ![Lijst met back-upsets 3](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. Voer de volgende stappen uit op de Blade **klonen** :
   
   1. Identificeer een doel apparaat. Dit is de locatie waar de kloon wordt gemaakt. U kunt hetzelfde apparaat kiezen of een ander apparaat opgeven.

      > [!NOTE]
      > Zorg ervoor dat de capaciteit die is vereist voor de kloon lager is dan de capaciteit die beschikbaar is op het doel apparaat.
       
   2. Geef een unieke volume naam op voor de kloon. De naam moet tussen de 3 en 127 tekens bevatten.
      
       > [!NOTE]
       > Het veld **kloon volume als** wordt **getierd** , zelfs als u een lokaal vastgemaakt volume kloont. U kunt deze instelling niet wijzigen. Als u echter wilt dat het gekloonde volume ook lokaal is vastgemaakt, kunt u de kloon converteren naar een lokaal vastgemaakt volume nadat u de kloon hebt gemaakt. Voor informatie over het converteren van een gelaagd volume naar een lokaal vastgemaakt volume, gaat u naar [het volume type wijzigen](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).
          
   3. Geef onder **verbonden hosts** een Access Control record op (ACR) voor de kloon. U kunt een nieuwe ACR toevoegen of uit de bestaande lijst kiezen. De ACR bepaalt welke hosts toegang tot deze kloon hebben.
      
       ![Lijst met back-upsets 4](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

   4. Klik op **klon** om de bewerking te volt ooien.

4. Er wordt een kloon taak gestart en u wordt gewaarschuwd wanneer de kloon is gemaakt. Klik op de taak melding of ga naar de Blade **taken** om de kloon taak te controleren.

    ![Lijst met back-upsets 5](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. Nadat de kloon taak is voltooid, gaat u naar het apparaat en klikt u vervolgens op **volumes**. In de lijst met volumes ziet u de kloon die zojuist is gemaakt in dezelfde volume container die het bron volume heeft.

    ![Lijst met back-upsets 6](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

Een kloon die op deze manier wordt gemaakt, is een tijdelijke kloon. Zie [tijdelijke en permanente klonen](#transient-vs-permanent-clones)voor meer informatie over typen klonen.


## <a name="transient-vs-permanent-clones"></a>Tijdelijke versus permanente klonen
Tijdelijke klonen worden alleen gemaakt wanneer u naar een ander apparaat kloont. U kunt een specifiek volume vanuit een back-upset klonen op een ander apparaat dat wordt beheerd door de StorSimple-Apparaatbeheer. De tijdelijke kloon bevat verwijzingen naar de gegevens in het oorspronkelijke volume en gebruikt die gegevens om lokaal op het doel apparaat te lezen en te schrijven.

Nadat u een Cloud momentopname van een tijdelijke kloon hebt gemaakt, is de resulterende kloon een *permanente* kloon. Tijdens dit proces wordt een kopie van de gegevens in de Cloud gemaakt en wordt de tijd voor het kopiëren van deze gegevens bepaald door de grootte van de gegevens en de Azure-latentie (dit is een Azure-naar-Azure-kopie). Dit proces kan dagen tot weken duren. De tijdelijke kloon wordt een permanente kloon en heeft geen verwijzingen naar de oorspronkelijke volume gegevens waarvan deze is gekloond.

## <a name="scenarios-for-transient-and-permanent-clones"></a>Scenario's voor tijdelijke en permanente klonen
In de volgende secties worden voorbeeld situaties beschreven waarin tijdelijke en permanente klonen kunnen worden gebruikt.

### <a name="item-level-recovery-with-a-transient-clone"></a>Herstel op item niveau met een tijdelijke kloon
U moet een oud micro soft power point-presentatie bestand van één jaar herstellen. De IT-beheerder identificeert de specifieke back-up vanaf die tijd en filtert vervolgens het volume. De beheerder kloont vervolgens het volume, zoekt naar het bestand dat u zoekt en biedt dit aan u. In dit scenario wordt een tijdelijke kloon gebruikt.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Testen in de productie omgeving met een permanente kloon
U moet een test fout controleren in de productie omgeving. U maakt een kloon van het volume in de productie omgeving en maakt vervolgens een Cloud momentopname van deze kloon om een onafhankelijk gekloond volume te maken. In dit scenario wordt een permanente kloon gebruikt.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [herstellen van een StorSimple-volume vanuit een back-upset](storsimple-8000-restore-from-backup-set-u2.md).
* Meer informatie over [het gebruik van de StorSimple Apparaatbeheer-service voor het beheren van uw StorSimple-apparaat](storsimple-8000-manager-service-administration.md).

