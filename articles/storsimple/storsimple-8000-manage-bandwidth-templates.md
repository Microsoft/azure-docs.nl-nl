---
title: Bandbreedte sjablonen beheren voor de StorSimple 8000-serie | Microsoft Docs
description: Hierin wordt beschreven hoe u StorSimple-bandbreedte sjablonen beheert, waarmee u het bandbreedte gebruik kunt beheren.
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
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 56170ffbbfe14248bcfd1f94549a3565873f8646
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005891"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-bandwidth-templates"></a>De StorSimple Apparaatbeheer-service gebruiken voor het beheren van StorSimple-bandbreedte sjablonen

## <a name="overview"></a>Overzicht

Met band breedte sjablonen kunt u het gebruik van netwerk bandbreedte in meerdere dagen plannen, zodat de gegevens van het StorSimple-apparaat in de cloud worden gelaagd.

Met planningen voor bandbreedte regeling kunt u het volgende doen:

* Geef aangepaste bandbreedte schema's op, afhankelijk van het gebruik van het werkbelasting netwerk.
* Centraliseer het beheer en hergebruik de planningen op een eenvoudige en naadloze manier op meerdere apparaten.

> [!NOTE]
> Deze functie is alleen beschikbaar voor fysieke StorSimple-apparaten (modellen 8100 en 8600) en niet voor StorSimple-Cloud apparaten (modellen 8010 en 8020).


## <a name="the-bandwidth-templates-blade"></a>De Blade bandbreedte sjablonen

De Blade **bandbreedte sjablonen** bevat alle bandbreedte sjablonen voor uw service in een tabel indeling en bevat de volgende informatie:

* **Naam** : een unieke naam die is toegewezen aan de sjabloon voor de band breedte wanneer deze is gemaakt.
* **Schema** : het aantal schema's dat is opgenomen in een bepaalde bandbreedte sjabloon.
* **Gebruikt door** : het aantal volumes met behulp van de bandbreedte sjablonen.

U kunt ook aanvullende informatie vinden om bandbreedte sjablonen te configureren in:

* [Vragen en antwoorden over bandbreedte sjablonen](#questions-and-answers-about-bandwidth-templates)
* [Aanbevolen procedures voor bandbreedte sjablonen](#best-practices-for-bandwidth-templates)

## <a name="add-a-bandwidth-template"></a>Een bandbreedte sjabloon toevoegen

Voer de volgende stappen uit om een nieuwe bandbreedte sjabloon te maken.

#### <a name="to-add-a-bandwidth-template"></a>Een band breedte sjabloon toevoegen

1. Ga naar de StorSimple-Apparaatbeheer service, klik op **bandbreedte sjablonen** en klik vervolgens op **+ bandbreedte sjabloon toevoegen**.

    ![Klik op + band breedte sjabloon toevoegen](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp1.png)

2. Voer de volgende stappen uit op de Blade **bandbreedte sjabloon toevoegen** :
   
    1. Geef een unieke naam op voor de sjabloon voor de band breedte.
    2. Definieer een bandbreedte schema. Een planning maken:
   
        1. Kies in de vervolg keuzelijst de **dagen** van de week waarvoor het schema is geconfigureerd. U kunt meerdere dagen selecteren.        
        
        2. Geef een **begin tijd** op in de notatie _uu: mm_ . Dit is het moment waarop de planning wordt gestart.

        3. Geef een **eind tijd** op in de notatie _uu: mm_ . Dit is het moment waarop de planning wordt gestopt.
      
           > [!NOTE]
           > Overlappende schema's zijn niet toegestaan. Als de begin-en eind tijd resulteren in een overlappende planning, wordt er een fout bericht weer gegeven.

        4. Geef het **bandbreedte aantal** op. Dit is de band breedte in megabits per seconde (Mbps) die door uw StorSimple-apparaat wordt gebruikt in bewerkingen met betrekking tot de Cloud (zowel uploads als down Loads). Geef voor dit veld een waarde op tussen 1 en 1000.

            ![Bandbreedte schema definiëren](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp2.png)
         
            Herhaal de bovenstaande stappen om meerdere schema's voor uw sjabloon te definiëren totdat u klaar bent.

        5. Klik op **toevoegen** om een band breedte sjabloon te maken. De sjabloon die u hebt gemaakt, wordt toegevoegd aan de lijst met bandbreedte sjablonen.
      

## <a name="edit-a-bandwidth-template"></a>Een bandbreedte sjabloon bewerken

Voer de volgende stappen uit om een bandbreedte sjabloon te bewerken.

### <a name="to-edit-a-bandwidth-template"></a>Een bandbreedte sjabloon bewerken

1. Ga naar de StorSimple-Apparaatbeheer service en klik op **bandbreedte sjablonen**.
2. Selecteer in de lijst met band breedte sjablonen de sjabloon die u wilt verwijderen. Klik met de rechter muisknop en selecteer **verwijderen** in het context menu.
3. Klik op **OK** als u om bevestiging wordt gevraagd. Hiermee verwijdert u de sjabloon voor de band breedte. 
4. De lijst met bandbreedte sjablonen wordt bijgewerkt om het verwijderen weer te geven.

> [!NOTE]
> U kunt de wijzigingen niet opslaan als het bewerkte schema overlapt met een bestaand schema in de bandbreedte sjabloon die u wijzigt.

## <a name="delete-a-bandwidth-template"></a>Een bandbreedte sjabloon verwijderen

Voer de volgende stappen uit om een bandbreedte sjabloon te verwijderen.

#### <a name="to-delete-a-bandwidth-template"></a>Een bandbreedte sjabloon verwijderen

1. Ga naar de StorSimple-Apparaatbeheer service en klik op **bandbreedte sjablonen**.
2. Selecteer in de lijst met band breedte sjablonen de sjabloon die u wilt verwijderen. Klik met de rechter muisknop en selecteer verwijderen in het context menu.
3. Klik op **OK** als u om bevestiging wordt gevraagd. Hiermee verwijdert u de sjabloon voor de band breedte.
4. De lijst met bandbreedte sjablonen wordt bijgewerkt om het verwijderen weer te geven.

Als de sjabloon wordt gebruikt door een of meer volumes, is het niet toegestaan om deze te verwijderen. Er wordt een fout bericht weer gegeven waarin wordt aangegeven dat de sjabloon wordt gebruikt. Er wordt een dialoog venster met een fout bericht weer gegeven waarin u wordt geadviseerd dat alle verwijzingen naar de sjabloon moeten worden verwijderd.

U kunt alle verwijzingen naar de sjabloon verwijderen door toegang te krijgen tot de **volume containers** pagina en de volume containers te wijzigen die deze sjabloon gebruiken, zodat ze een andere sjabloon gebruiken of een aangepaste of onbeperkte band breedte-instelling gebruiken. Wanneer alle verwijzingen zijn verwijderd, kunt u de sjabloon verwijderen.

## <a name="use-a-default-bandwidth-template"></a>Een standaard sjabloon voor band breedte gebruiken

Er wordt een standaard sjabloon voor band breedte gegeven en wordt door volume containers standaard gebruikt voor het afdwingen van bandbreedte controles bij het openen van de Cloud. De standaard sjabloon fungeert ook als een naslag werk voor gebruikers die hun eigen sjablonen maken. De details van deze standaard sjabloon zijn:

* **Naam** : onbeperkt aantal nachten en weekenden
* **Planning** : een enkele planning van maandag tot en met vrijdag die een bandbreedte verhouding van 1 Mbps voor de apparaattijd van 8 uur en 5 pm toepast. De band breedte is ingesteld op onbeperkt voor de rest van de week.

De standaard sjabloon kan worden bewerkt. Het gebruik van deze sjabloon (inclusief bewerkte versies) wordt bijgehouden.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Een sjabloon voor een hele dag-band breedte maken die op een opgegeven tijdstip begint

Volg deze procedure om een planning te maken die op een opgegeven tijdstip begint en die de hele dag wordt uitgevoerd. In het voor beeld begint de planning om 9 uur 's morgens en wordt uitgevoerd tot 9 de volgende ochtend. Het is belang rijk te weten dat de begin-en eind tijden voor een bepaald schema moeten zijn opgenomen in hetzelfde schema van 24 uur en dat ze niet meerdere dagen kunnen omvatten. Als u bandbreedte sjablonen wilt instellen die meerdere dagen omvatten, moet u meerdere planningen gebruiken (zoals weer gegeven in het voor beeld).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Een sjabloon voor een hele dag-band breedte maken

1. Maak een planning die begint op 9 uur 's morgens en wordt uitgevoerd tot middernacht.
2. Een andere planning toevoegen. Stel in dat het tweede schema wordt uitgevoerd vanaf middernacht tot 9 uur 's morgens.
3. Sla de sjabloon voor de band breedte op.

De samengestelde planning begint vervolgens op een tijdstip van uw keuze en voert de hele dag uit.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Vragen en antwoorden over bandbreedte sjablonen

**V**. Wat gebeurt er met bandbreedte regeling wanneer u tussen de planningen bent? (Een schema is beëindigd en een ander abonnement is nog niet gestart.)

**A**. In dergelijke gevallen worden er geen bandbreedte controles gebruikt. Dit betekent dat het apparaat onbeperkte band breedte kan gebruiken bij het trapsgewijs scha kelen van gegevens naar de Cloud.

**V**. Kunt u bandbreedte sjablonen op een offline apparaat wijzigen?

**A**. U kunt band breedte sjablonen op volume containers niet wijzigen als het bijbehorende apparaat offline is.

**V**. Kunt u een bandbreedte sjabloon bewerken die is gekoppeld aan een volume container wanneer de bijbehorende volumes offline zijn?

**A**. U kunt een bandbreedte sjabloon die is gekoppeld aan een volume container waarvan de volumes offline zijn, wijzigen. Houd er rekening mee dat wanneer volumes offline zijn, er geen gegevens worden getierd van het apparaat naar de Cloud.

**V**. Kunt u een standaard sjabloon verwijderen?

**A**. Hoewel u een standaard sjabloon kunt verwijderen, is het geen goed idee om dit te doen. Het gebruik van een standaard sjabloon, inclusief bewerkte versies, wordt bijgehouden. De tracerings gegevens worden geanalyseerd en in de loop van de tijd, wordt gebruikt om de standaard sjabloon te verbeteren.

**V**. Hoe kunt u bepalen dat uw bandbreedte sjablonen moeten worden gewijzigd?

**A**. Een van de tekens die u nodig hebt om de bandbreedte sjablonen te wijzigen, is wanneer u begint met het netwerk te vertragen of meerdere keren op een dag te dichten. Als dit het geval is, controleert u het opslag-en gebruiks netwerk door te kijken naar de grafieken I/O-prestaties en netwerk doorvoer.

Bepaal op basis van de netwerk doorvoer gegevens de tijd van de dag en de volume containers waarin het netwerk knelpunt optreedt. Als dit gebeurt wanneer gegevens worden getierd naar de Cloud (deze gegevens ophalen van I/O-prestaties voor alle volume containers voor het apparaat naar de Cloud), moet u de bandbreedte sjablonen wijzigen die zijn gekoppeld aan uw volume containers.

Nadat de gewijzigde sjablonen in gebruik zijn, moet u het netwerk opnieuw controleren op belang rijke latenties. Als deze nog bestaan, moet u uw bandbreedte sjablonen opnieuw bezoeken.

**V**. Wat gebeurt er als meerdere volume containers op mijn apparaat schema's hebben die elkaar overlappen, maar waarvoor verschillende limieten van toepassing zijn?

**A**. Stel dat u een apparaat hebt met 3 volume containers. De planningen die zijn gekoppeld aan deze containers overlappen elkaar volledig. Voor elk van deze containers zijn de gebruikte bandbreedte limieten respectievelijk 5, 10 en 15 Mbps. Wanneer I/O op al deze containers tegelijk plaatsvindt, kunnen de minimum van de 3 bandbreedte limieten worden toegepast: in dit geval 5 Mbps als deze uitgaande I/O-aanvragen dezelfde wachtrij delen.

## <a name="best-practices-for-bandwidth-templates"></a>Aanbevolen procedures voor bandbreedte sjablonen

Volg deze aanbevolen procedures voor uw StorSimple-apparaat:

* Configureer bandbreedte sjablonen op het apparaat om variabele beperking van de netwerk doorvoer door het apparaat op verschillende tijdstippen van de dag in te scha kelen. Deze bandbreedte sjablonen in combi natie met back-upscheman kunnen een extra netwerk bandbreedte gebruiken voor Cloud bewerkingen tijdens daluren.
* Bereken de werkelijke band breedte die is vereist voor een bepaalde implementatie op basis van de grootte van de implementatie en de vereiste herstel tijd (RTO).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het gebruik van de StorSimple Apparaatbeheer-service voor het beheren van uw StorSimple-apparaat](storsimple-8000-manager-service-administration.md).

