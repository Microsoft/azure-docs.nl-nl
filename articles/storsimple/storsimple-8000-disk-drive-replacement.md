---
title: Een schijf station in een StorSimple 8000 Series-apparaat vervangen | Microsoft Docs
description: Hierin wordt uitgelegd hoe u een schijf station vervangt in een StorSimple primaire behuizing of een EBOD-behuizing.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 8/25/2017
ms.author: alkohli
ms.openlocfilehash: a5a7a31c174271f963bb005f10b8376a59133d36
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014870"
---
# <a name="replace-a-disk-drive-on-your-storsimple-8000-series-device"></a>Schijfstation vervangen van apparaat uit StorSimple 8000-serie

## <a name="overview"></a>Overzicht
In deze zelf studie wordt uitgelegd hoe u een defecte of defecte harde schijf op een Microsoft Azure StorSimple apparaat kunt verwijderen en vervangen. Als u een schijf station wilt vervangen, moet u het volgende doen:

* De antitamper-vergren deling opheffen
* Het schijf station verwijderen
* Het vervangende schijf station installeren

> [!IMPORTANT]
> Voordat u een schijf station verwijdert en vervangt, raadpleegt u de beveiligings informatie in [StorSimple vervanging](storsimple-8000-hardware-component-replacement.md)van het hardware-onderdeel.
 

## <a name="disengage-the-antitamper-lock"></a>De antitamper-vergren deling opheffen
In deze procedure wordt uitgelegd hoe de antitamper-vergren delingen op uw StorSimple-apparaat kunnen worden ingeschakeld of uitgeschakeld wanneer u de schijf stations vervangt. De antitamper-vergren delingen zijn aangebracht in de draag signaal handgrepen en zijn toegankelijk via een kleine opening in het gedeelte hendel van de ingang. Stations worden geleverd met de vergren delingen die zijn ingesteld op de vergrendelde positie.

#### <a name="to-unlock-the-antitamper-lock"></a>De antitamper-vergren deling ontgrendelen
1. Plaats de vergrendelings sleutel (een "tamperproof" T10-draaier die door micro soft is meegeleverd) zorgvuldig in de opening in de greep en in de socket. 
   
   Als de antitamper-vergren deling is geactiveerd, is de rode indicator zichtbaar in de opening.
  
    ![Vergrendeld schijf station](./media/storsimple-disk-drive-replacement/IC741056.png)
   
    **Afbeelding 1** Anti-knoei vergrendeling ingeschakeld
   
   | Label | Beschrijving |
   |:--- |:--- |
   | 1 |Lens opening |
   | 2 |Antitamper-vergren deling |
2. Draai de toets in de richting van de linksom totdat de rode indicator niet zichtbaar is in de opening boven de sleutel.
3. Verwijder de sleutel.
   
    ![Ontgrendeld schijf station](./media/storsimple-disk-drive-replacement/IC741057.png)
   
    **Afbeelding 2** Ontgrendeld schijf station
4. Het schijf station kan nu worden verwijderd.

Volg de stappen in omgekeerde om de vergren deling te benaderen.

## <a name="remove-the-disk-drive"></a>Het schijf station verwijderen
Uw StorSimple-apparaat ondersteunt een RAID 10-achtige configuratie voor opslag ruimten. Dit betekent dat het normaal kan worden uitgevoerd met één defecte schijf, SSD (Solid-State Drive) of harde schijf (HDD).

> [!IMPORTANT]
> * Als uw systeem meer dan één defecte schijf heeft, mag u niet meer dan één SSD of HDD van het systeem op elk gewenst moment verwijderen. Dit kan leiden tot verlies van gegevens.
> * Zorg ervoor dat u een vervangen SSD in een sleuf plaatst die eerder een SSD bevatte. U kunt ook een vervangende harde schijf in een sleuf plaatsen waar eerder een HDD is opgenomen.
> * In de Azure Portal zijn sleuven genummerd van 0 tot 11. Als er in de portal wordt aangegeven dat een schijf in sleuf 2 is mislukt, zoekt u op het apparaat naar de defecte schijf in de derde sleuf vanaf de linkerbovenhoek.
> 
> 

Stations kunnen worden verwijderd en vervangen als het systeem actief is.

#### <a name="to-remove-a-drive"></a>Een station verwijderen
1. Als u de defecte schijf wilt identificeren, gaat u in het Azure Portal naar uw Apparaatinstellingen **> hardware-status**. Omdat een schijf kan mislukken in de primaire behuizing en/of in een EBOD-behuizing (als u een 8600-model gebruikt), kijkt u naar de status van de schijven onder **gedeelde onderdelen** en onder **gedeelde onderdelen van EBOD**. Een niet-werkende schijf in de behuizing wordt weer gegeven met een rode status.
2. Zoek de stations aan de voor zijde van de primaire behuizing of de EBOD-behuizing. 
3. Als de schijf is ontgrendeld, gaat u verder met de volgende stap. Als de schijf is vergrendeld, ontgrendelt u deze door de procedure te volgen in [de antitamper-vergren deling](#disengage-the-antitamper-lock).
4. Druk op de zwarte hendel op de transport module voor schijven en haal de draag signaal ingang uit en weg van de voor zijde van het chassis.
   
    ![Handle van schijf station vrijgeven](./media/storsimple-disk-drive-replacement/IC741051.png)
   
    **Afbeelding 3** De schijf ingang loslaten
5. Wanneer de draag signaal ingang volledig is uitgebreid, verschuift u de draag signaal van het chassis naar een andere schijf. 
   
    ![Schijf station verschuiven](./media/storsimple-disk-drive-replacement/IC741052.png)
   
    **Afbeelding 4** Het schijf station buiten de provider schuiven

## <a name="install-the-replacement-disk-drive"></a>Het vervangende schijf station installeren
Nadat een station op uw StorSimple-apparaat is mislukt en u het hebt verwijderd, volgt u deze procedure om het te vervangen door een nieuw station.

#### <a name="to-insert-a-drive"></a>Een station invoegen
1. Zorg ervoor dat de draag signaal ingang volledig is uitgebreid, zoals wordt weer gegeven in de volgende afbeelding.
   
    ![Schijf station met ingangs uitbreiding](./media/storsimple-disk-drive-replacement/IC741044.png)
   
    **Afbeelding 5** Station met ingangs uitbreiding
2. Schuif de schijf-Carrier helemaal naar het chassis.
   
    ![Schijf naar schijf station verschuiven](./media/storsimple-disk-drive-replacement/IC741045.png)
   
    **Afbeelding 6**  De transporteur naar het chassis schuiven
3. Sluit de expediteur van het station dat u hebt toegevoegd, terwijl u doorgaat met het pushen van de draaggolf van het station naar het chassis, totdat de vervoerders afhandelt in een vergrendelde positie.
4. Gebruik de Lock-toets die door micro soft is verschaft (tamperproof Torx draaier) om de draag kracht te beveiligen door de vergren deling van een kwart lijn op de klok in te scha kelen.
5. Controleer of de vervanging is geslaagd en of het station operationeel is. Ga naar de Azure Portal en navigeer naar **Apparaatinstellingen**  >  **Hardware Health**. Onder **gedeelde onderdelen** of **gedeelde EBOD-onderdelen** moet de status van het station groen zijn om aan te geven dat dit in orde is.

   
   > [!NOTE]
   > Het kan enkele uren duren voordat de schijf status groen is nadat de vervanging is uitgevoerd.
  
## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [vervangen van StorSimple](storsimple-8000-hardware-component-replacement.md).

