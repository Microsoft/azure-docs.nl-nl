---
title: Een volume terugzetten vanuit een back-up in een StorSimple 8000-serie | Microsoft Docs
description: Hierin wordt uitgelegd hoe u de StorSimple Apparaatbeheer service Backup Catalog kunt gebruiken om een StorSimple-volume te herstellen vanuit een back-upset.
services: storsimple
documentationcenter: NA
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.date: 07/15/2020
ms.author: alkohli
ms.openlocfilehash: ff70df225b5ed27960c96889d409d7005f0fbcc4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94950725"
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Een StorSimple-volume herstellen op basis van een back-upset

## <a name="overview"></a>Overzicht

In deze zelf studie wordt de herstel bewerking beschreven die wordt uitgevoerd op een StorSimple 8000 serie apparaat met behulp van een bestaande back-upset. Gebruik de Blade **back-upcatalogus** om een volume terug te zetten van een lokale of een back-up van de Cloud. Op de Blade **back-upcatalogus** worden alle back-upsets weer gegeven die worden gemaakt wanneer hand matig of automatisch maken van back-ups worden uitgevoerd. Met de herstel bewerking van een back-upset wordt het volume onmiddellijk beschikbaar wanneer gegevens op de achtergrond worden gedownload.

## <a name="before-you-restore"></a>Voordat u herstelt

Controleer de volgende opmerkingen voordat u een herstel bewerking start:

* **U moet het volume offline halen** : Neem het volume offline op de host en het apparaat voordat u de herstel bewerking start. Hoewel de herstel bewerking het volume automatisch online brengt op het apparaat, moet u het apparaat hand matig online plaatsen op de host. U kunt het volume online plaatsen op de host zodra het volume online is op het apparaat. (U hoeft niet te wachten totdat de herstel bewerking is voltooid.) Voor procedures gaat u naar [een volume offline halen](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline).

* **Volume type na het herstellen** : verwijderde volumes worden hersteld op basis van het type in de moment opname. dat wil zeggen dat volumes die lokaal zijn vastgemaakt, worden teruggezet als lokaal vastgemaakte volumes en volumes die zijn trapsgewijs worden teruggezet als gelaagde volumes.

    Voor bestaande volumes overschrijft het huidige gebruiks type van het volume het type dat is opgeslagen in de moment opname. Als u bijvoorbeeld een volume herstelt op basis van een moment opname die is gemaakt toen het volume type werd gelaagd en dat volume type nu lokaal is vastgemaakt (vanwege een conversie bewerking die is uitgevoerd), wordt het volume teruggezet als een lokaal vastgemaakt volume. En als een bestaand lokaal vastgemaakt volume is uitgevouwen en vervolgens wordt hersteld vanuit een oudere moment opname die is gemaakt toen het volume kleiner werd, behoudt het herstelde volume de huidige uitgebreide grootte.

    U kunt een volume niet converteren van een gelaagd volume naar een lokaal vastgemaakt volume of van een lokaal vastgemaakt volume naar een gelaagd volume terwijl het volume wordt hersteld. Wacht tot de herstel bewerking is voltooid en converteer het volume vervolgens naar een ander type. Ga voor informatie over het converteren van een volume naar [het wijzigen van het volume type](storsimple-8000-manage-volumes-u2.md#change-the-volume-type). 

* **De grootte van het volume wordt weer gegeven in het herstelde volume** : dit is een belang rijke overweging als u een lokaal vastgemaakt volume herstelt dat is verwijderd (omdat lokaal vastgemaakte volumes volledig zijn ingericht). Zorg ervoor dat u voldoende ruimte hebt voordat u probeert een lokaal vastgemaakt volume te herstellen dat eerder is verwijderd.

* Het **is niet mogelijk om een volume uit te breiden terwijl het wordt hersteld** : wacht tot de herstel bewerking is voltooid voordat u het volume probeert uit te breiden. Ga voor meer informatie over het uitbreiden van een volume naar [het wijzigen van een volume](storsimple-8000-manage-volumes-u2.md#modify-a-volume).

* **U kunt een back-up uitvoeren terwijl u een lokaal volume herstelt** – voor procedures gaat u naar [de StorSimple Apparaatbeheer-service gebruiken om het back-upbeleid te beheren](storsimple-8000-manage-backup-policies-u2.md).

* **U kunt een herstel bewerking annuleren** : als u de herstel taak annuleert, wordt het volume teruggedraaid naar de status waarin het zich bevond voordat u de herstel bewerking startte. Ga voor procedures naar [een taak annuleren](storsimple-8000-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Hoe werkt herstellen

Voor apparaten met update 4 of hoger wordt een herstel op basis van Heatmap geïmplementeerd. Wanneer de host verzoekt om toegang tot gegevens te krijgen, worden deze aanvragen gevolgd en wordt er een heatmap gemaakt. Hoge aanvraag frequentie resulteert in gegevens segmenten met hogere hitte, terwijl lagere aanvraag frequenties worden omgezet in segmenten met een lagere hitte. U moet ten minste twee keer toegang hebben tot de gegevens om als _dynamisch_ te worden gemarkeerd. Een bestand dat wordt gewijzigd, wordt ook gemarkeerd als _dynamisch_. Zodra u de herstel bewerking initieert, worden de proactieve Hydration van gegevens op basis van de heatmap. Voor oudere versies dan update 4 worden de gegevens tijdens het herstellen gedownload op basis van alleen toegang.

De volgende voor behoud zijn van toepassing op heatmap herstel:

* Heatmap tracering is alleen ingeschakeld voor gelaagde volumes en lokaal vastgemaakte volumes worden niet ondersteund.

* Herstel op basis van Heatmap wordt niet ondersteund bij het klonen van een volume naar een ander apparaat. 

* Als er een in-place herstel bewerking is en een lokale moment opname voor het volume op het apparaat moet worden hersteld, wordt niet opnieuw gehydrateerd (omdat de gegevens al lokaal beschikbaar zijn). 

* Wanneer u herstelt, worden de rehydratatie-taken standaard gestart, waarbij de gegevens op basis van de heatmap proactief opnieuw worden gehydrateerd. 

In update 4 kunnen Windows Power shell-cmdlets worden gebruikt om een query uit te voeren op rehydratatie-taken, een rehydratatie-taak te annuleren en de status van de rehydratatie-taak op te halen.

* `Get-HcsRehydrationJob` -Met deze cmdlet wordt de status van de rehydratatie-taak opgehaald. Eén rehydratatie-taak wordt geactiveerd voor één volume.

* `Set-HcsRehydrationJob` -Met deze cmdlet kunt u de rehydratatie-taak onderbreken, stoppen en hervatten wanneer de rehydratatie wordt uitgevoerd.

Voor meer informatie over rehydratatie-cmdlets gaat u naar [Windows Power shell cmdlet Reference voor StorSimple](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps).

Met automatische rehydratatie is er meestal meer tijdelijke Lees prestaties verwacht. De werkelijke omvang van verbeteringen is afhankelijk van verschillende factoren, zoals het toegangs patroon, het verloop van gegevens en het gegevens type. 

Als u een rehydratatie-taak wilt annuleren, kunt u de Power shell-cmdlet gebruiken. Als u rehydratatie-taken permanent wilt uitschakelen voor alle toekomstige herstel bewerkingen, neemt u [contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md).

## <a name="how-to-use-the-backup-catalog"></a>De back-catalogus gebruiken

De Blade **back-upcatalogus** bevat een query waarmee u de selectie van de back-upset kunt beperken. U kunt de back-upsets filteren die worden opgehaald op basis van de volgende para meters:

* **Tijds bereik** : het datum-en tijds bereik waarop de back-upset is gemaakt.
* **Apparaat** : het apparaat waarop de back-upset is gemaakt.
* **Back-upbeleid** of **volume** : het back-upbeleid of het volume dat is gekoppeld aan deze back-upset.

De gefilterde back-upsets worden vervolgens getabeld op basis van de volgende kenmerken:

* **Naam** : de naam van het back-upbeleid of het volume dat is gekoppeld aan de back-upset.
* **Type** : back-upsets kunnen lokale moment opnamen of Cloud momentopnamen zijn. Een lokale moment opname is een back-up van alle volume gegevens die lokaal zijn opgeslagen op het apparaat, terwijl een Cloud momentopname verwijst naar de back-up van volume gegevens die zich in de cloud bevinden. Lokale moment opnamen bieden snellere toegang, terwijl Cloud momentopnamen worden gekozen voor gegevens tolerantie.
* **Grootte** : de werkelijke grootte van de back-upset.
* **Gemaakt op** : de datum en tijd waarop de back-ups zijn gemaakt. 
* **Volumes** : het aantal volumes dat is gekoppeld aan de back-upset.
* **Gestart** : de back-ups kunnen automatisch worden geïnitieerd volgens een planning of hand matig door een gebruiker. (U kunt een back-upbeleid gebruiken om back-ups te plannen. U kunt ook de optie **back-up** maken gebruiken om een interactieve of on-demand back-up te maken.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Uw StorSimple-volume terugzetten vanuit een back-up

U kunt de Blade **back-up** van de catalogus gebruiken om uw StorSimple-volume te herstellen op basis van een specifieke back-up. Houd er echter rekening mee dat bij het herstellen van een volume het volume wordt teruggezet naar de status waarin de back-up werd gemaakt. Alle gegevens die zijn toegevoegd na de back-upbewerking, gaan verloren.

> [!WARNING]
> Wanneer u een back-up herstelt, worden de bestaande volumes vervangen door de back-up. Dit kan leiden tot verlies van gegevens die zijn geschreven nadat de back-up werd gemaakt.


### <a name="to-restore-your-volume"></a>Uw volume herstellen
1. Ga naar de StorSimple-Apparaatbeheer service en klik vervolgens op **back-upcatalogus**.

2. Selecteer een back-upset als volgt:
   
   1. Geef het tijds bereik op.
   2. Selecteer het betreffende apparaat.
   3. Kies in de vervolg keuzelijst het volume of back-upbeleid voor de back-up die u wilt selecteren.
   4. Klik op **Toep assen** om deze query uit te voeren.

      De back-ups die zijn gekoppeld aan het geselecteerde volume of back-upbeleid, moeten worden weer gegeven in de lijst met back-upsets.
   
      ![Lijst met back-upsets](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. Vouw de back-upset uit om de bijbehorende volumes weer te geven. Deze volumes moeten offline worden gehaald op de host en het apparaat voordat u ze kunt herstellen. Open de volumes op de Blade **volumes** van uw apparaat en volg de stappen in [een volume offline halen](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) om ze offline te zetten.
   
   > [!IMPORTANT]
   > Zorg ervoor dat u de volumes eerst offline hebt gezet op de host voordat u de volumes offline zet op het apparaat. Als u de volumes niet offline zet op de host, kan dit leiden tot beschadigde gegevens.
   
4. Ga terug naar het tabblad **back-upcatalogus** en selecteer een back-upset. Klik met de rechter muisknop en selecteer vervolgens **herstellen** in het context menu.

    ![Lijst met back-upsets 2](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. U wordt gevraagd om een bevestiging. Controleer de herstel gegevens en schakel vervolgens het selectie vakje bevestiging in.
   
    ![Bevestigingspagina](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7. Klik op **herstellen**. Hiermee initieert u een herstel taak die u kunt weer geven door de pagina **taken** te openen.

   ![Bevestigings pagina 2](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. Nadat het herstellen is voltooid, controleert u of de inhoud van uw volumes worden vervangen door volumes van de back-up.


## <a name="if-the-restore-fails"></a>Als de herstel bewerking is mislukt

U ontvangt een waarschuwing als de herstel bewerking om een of andere reden is mislukt. Als dit het geval is, moet u de back-uplijst vernieuwen om te controleren of de back-up nog geldig is. Als de back-up geldig is en u herstelt vanuit de Cloud, wordt het probleem mogelijk veroorzaakt door verbindings problemen.

Als u de herstel bewerking wilt volt ooien, neemt u het volume offline op de host en voert u de herstel bewerking opnieuw uit. Houd er rekening mee dat wijzigingen in de volume gegevens die tijdens het herstel proces werden uitgevoerd, verloren gaan.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [beheren van StorSimple-volumes](storsimple-8000-manage-volumes-u2.md).
* Meer informatie over [het gebruik van de StorSimple Apparaatbeheer-service voor het beheren van uw StorSimple-apparaat](storsimple-8000-manager-service-administration.md).