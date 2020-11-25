---
title: Een StorSimple-back-up van een virtuele matrix klonen | Microsoft Docs
description: Meer informatie over het klonen van een back-up en het herstellen van een bestand vanuit uw StorSimple Virtual array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: af6e979c-55e3-477c-b53e-a76a697f80c9
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: b6cc89cb082cd0ed32abd88e3a6683c60a27ba90
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023037"
---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>Klonen vanuit een back-up van uw virtuele StorSimple-matrix

## <a name="overview"></a>Overzicht

In dit artikel wordt stapsgewijs beschreven hoe u een back-upset van uw shares of volumes op uw Microsoft Azure StorSimple virtuele matrix kloont. De gekloonde back-up wordt gebruikt om een verwijderd of verloren bestand te herstellen. Het artikel bevat ook gedetailleerde stappen voor het uitvoeren van een herstel op item niveau voor uw virtuele StorSimple-matrix die is geconfigureerd als een bestands server.

## <a name="clone-shares-from-a-backup-set"></a>Shares van een back-upset klonen

**Voordat u probeert om shares te klonen, moet u ervoor zorgen dat u voldoende ruimte op het apparaat hebt om deze bewerking te volt ooien.** Als u wilt klonen vanuit een back-up, voert u de volgende stappen uit in het [Azure Portal](https://portal.azure.com/).

#### <a name="to-clone-a-share"></a>Een share klonen

1. Blader naar de Blade **apparaten** . Selecteer en klik op uw apparaat en klik vervolgens op **shares**. Selecteer de share die u wilt klonen, klik met de rechter muisknop op de share om het context menu aan te roepen. Selecteer **Klonen**.
   
   ![Een back-up klonen](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. Klik in de Blade **klon** op **back-up > Selecteer** en doe het volgende: 
   
   a.    Een back-up op dit apparaat filteren op basis van het tijds bereik. U kunt kiezen uit **afgelopen zeven dagen**, **afgelopen 30 dagen** en **afgelopen jaar**.
   
   b.    Selecteer in de lijst met gefilterde back-ups een back-up waarvan u wilt klonen.
   
   c.    Klik op **OK**.
   
   ![Back-up maken 2 klonen](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. Klik in de Blade **klon** op **doel instellingen** en ga vervolgens als volgt te werk:
   
   a.    Geef een naam op voor de share. De share naam moet 3-127 tekens bevatten.
   
   b.    Geef eventueel een beschrijving op voor de gekloonde share.
   
   c.    U kunt het type van de share waarnaar u wilt herstellen, niet wijzigen. Een gelaagde share wordt gekloond als een gelaagde en lokaal vastgemaakte share die lokaal is vastgemaakt.
   
   d.    De capaciteit wordt ingesteld op de grootte van de share die u wilt klonen.
   
   e.    Wijs de beheerders voor deze share toe. Nadat de kloon is voltooid, kunt u de eigenschappen van de share wijzigen via Verkenner.
   
   f.    Klik op **OK**.
   
   ![Een back-up klonen 3](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. Klik op **klonen** om een kloon taak te starten. Nadat de taak is voltooid, wordt de kloon bewerking gestart en wordt u hiervan op de hoogte gebracht. Als u de voortgang van de kloon wilt controleren, gaat u naar de Blade **taken** en klikt u op de taak om de taak details weer te geven.
5. Nadat de kloon is gemaakt, gaat u terug naar de Blade **shares** op het apparaat.
6. U kunt nu de nieuwe gekloonde share weer geven in de lijst met shares op uw apparaat. Een gelaagde share wordt gekloond als gelaagd en een lokaal vastgemaakte share als een lokaal vastgemaakte share.
   
   ![Een back-up kopiëren 4](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>Volumes van een back-upset klonen

Als u vanuit een back-up wilt klonen, moet u in het Azure Portal stappen uitvoeren die vergelijkbaar zijn met die van een share. De kloon bewerking kloont de back-up naar een nieuw volume op hetzelfde virtuele apparaat. u kunt niet klonen naar een ander apparaat.

#### <a name="to-clone-a-volume"></a>Een volume klonen

1. Blader naar de Blade **apparaten** . Selecteer en klik op uw apparaat en klik vervolgens op **volumes**. Selecteer het volume dat u wilt klonen, klik met de rechter muisknop op het volume om het context menu te openen. Selecteer **Klonen**.
   
   ![Een volume klonen](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. Klik op de Blade **klonen** op **back-up** en ga vervolgens als volgt te werk: 
   
   a.    Een back-up op dit apparaat filteren op basis van het tijds bereik. U kunt kiezen uit **afgelopen zeven dagen**, **afgelopen 30 dagen** en **afgelopen jaar**. 
   
   b.    Selecteer in de lijst met gefilterde back-ups een back-up waarvan u wilt klonen.
   
   c.    Klik op **OK**.
   
   ![Een volume 2 klonen](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. Klik in de Blade **klon** op instellingen van het **doel volume** en ga vervolgens als volgt te werk:
   
   a. De naam van het apparaat wordt automatisch ingevuld.
   
   b. Geef een volume naam op voor het **gekloonde volume**. De volume naam moet 3 tot 127 tekens bevatten.
   
   c. Het volume type wordt automatisch ingesteld op het oorspronkelijke volume. Een gelaagd volume wordt gekloond als gelaagd en lokaal vastgemaakt volume als lokaal vastgemaakt.
   
   d. Klik voor de **verbonden hosts** op **selecteren**.
   
   ![Een volume klonen 3](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. Selecteer op de Blade **verbonden hosts** een bestaande ACR of Voeg een nieuwe ACR toe. Als u een nieuwe ACR wilt toevoegen, moet u een ACR-naam en de IQN van de host opgeven. Klik op **Selecteren**.
   
   ![Een volume kopiëren 4](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. Klik op **klon** om een kloon taak te starten.
   
   ![Een volume klonen](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. Nadat de kloon taak is gemaakt, wordt het klonen gestart. Zodra de kloon is gemaakt, wordt deze weer gegeven op de Blade volumes op het apparaat. Houd er rekening mee dat een gelaagd volume wordt gekloond als gelaagd en dat een lokaal vastgemaakt volume wordt gekloond als lokaal vastgemaakt volume.
   
   ![Een volume klonen 6](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. Zodra het volume online wordt weer gegeven op de lijst met volumes, is het volume beschikbaar voor gebruik. Vernieuw de lijst met doelen in het venster Eigenschappen van iSCSI-initiator op de iSCSI-initiator. Een nieuw doel met de naam van het gekloonde volume moet als ' inactief ' worden weer gegeven onder de kolom Status.
8. Selecteer het doel en klik op **verbinden**. Nadat de initiator is verbonden met het doel, wordt de status gewijzigd in **verbonden**.
9. In het venster **schijf beheer** worden de gekoppelde volumes weer gegeven zoals in de volgende afbeelding. Klik met de rechtermuisknop op het gedetecteerde volume (klik op de naam van de schijf) en klik vervolgens op **Online**.

> [!IMPORTANT]
> Wanneer u probeert een volume of een share uit een back-upset te klonen en de kloon taak mislukt, kan er nog steeds een doel volume of-share in de portal worden gemaakt. Het is belang rijk dat u dit doel volume of de share in de portal verwijdert om toekomstige problemen die voortvloeien uit dit element te minimaliseren.
> 
> 

## <a name="item-level-recovery-ilr"></a>Herstel op itemniveau (ILR, item-level recovery)

Deze release introduceert het herstel op item niveau (ILR) op een virtuele StorSimple-matrix die is geconfigureerd als een bestands server. Met deze functie kunt u nauw keuriger herstel van bestanden en mappen uitvoeren vanuit een back-up van alle shares op het StorSimple-apparaat. U kunt verwijderde bestanden ophalen uit recente back-ups met behulp van een self-service model.

Elke share heeft een map *. backups* die de meest recente back-ups bevat. U kunt naar de gewenste back-up gaan, relevante bestanden en mappen kopiëren van de back-up en deze herstellen. Met deze functie worden de aanroepen van beheerders voor het terugzetten van bestanden uit back-ups overbodig.

1. Bij het uitvoeren van de ILR kunt u de back-ups bekijken via Verkenner. Klik op de specifieke share die u wilt bekijken met de back-up voor. U ziet een map *. backups* die zijn gemaakt onder de share waarin alle back-ups worden opgeslagen. Vouw de map *. backups* uit om de back-ups weer te geven. De map toont de uitgelichte weer gave van de volledige back-uphiërarchie. Deze weer gave wordt op aanvraag gemaakt en duurt doorgaans slechts een paar seconden.
   
   De laatste vijf back-ups worden op deze manier weer gegeven en kunnen worden gebruikt om een herstel op item niveau uit te voeren. De vijf recente back-ups bevatten zowel de standaard geplande als de hand matige back-ups.
   
   * **Geplande back-ups** met de naam &lt; apparaatnaam &gt; instelling DAILYSCHEDULE-JJJMMDD-hhmmss-UTC.
   * **Hand matige back-ups** met de naam ad-hoc-JJJMMDD-hhmmss-UTC.
     
     ![Scherm opname van bestanden Verkenner met de map. backups. In die map wordt de map hand matige back-ups geselecteerd.](./media/storsimple-virtual-array-clone/image14.png)

2. De back-up met de meest recente versie van het verwijderde bestand identificeren. Hoewel de mapnaam een UTC-tijds tempel bevat in elk van de voor gaande gevallen, is het tijdstip waarop de map is gemaakt de werkelijke apparaattijd waarop de back-up is gestart. Gebruik de time stamp van de map om de back-ups te zoeken en te identificeren.

3. Ga naar de map of het bestand dat u wilt herstellen in de back-up die u in de vorige stap hebt geïdentificeerd. Opmerking: u kunt alleen de bestanden of mappen weer geven waarvoor u machtigingen hebt. Als u geen toegang hebt tot bepaalde bestanden of mappen, neemt u contact op met de beheerder van shares. De beheerder kan bestanden Verkenner gebruiken om de share machtigingen te bewerken en toegang geven tot het specifieke bestand of de opgegeven map. Het is een aanbevolen best practice dat de share beheerder een gebruikers groep is in plaats van één gebruiker.

4. Kopieer het bestand of de map naar de juiste share op uw StorSimple-Bestands server.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe u [uw virtuele StorSimple-matrix beheert met behulp van de lokale webgebruikersinterface](storsimple-ova-web-ui-admin.md).

