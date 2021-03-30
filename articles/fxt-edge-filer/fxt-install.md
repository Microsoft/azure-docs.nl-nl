---
title: 'Zelfstudie: Een fysiek Azure FXT Edge Filer-apparaat installeren'
description: Het uitpakken, inbouwen en bekabelen van het fysieke apparaatonderdeel van de Microsoft Azure FXT Edge Filer-hybride opslagcache
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 48ee6de28bcd76d4c484b77c981062bad1a3754d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219748"
---
# <a name="tutorial-install-azure-fxt-edge-filer"></a>Zelfstudie: Azure FXT Edge Filer installeren

In deze zelfstudie wordt beschreven hoe u een hardware-knooppunt installeert voor de Azure FXT Edge Filer-hybride opslagcache. U moet ten minste drie hardwareknooppunten installeren om een Azure FXT Edge Filer-cluster te maken.

Bij de installatieprocedure wordt het apparaat uitgepakt en ingebouwd en worden de CMA (kabelmanagement-arm) en de omlijsting vooraan bevestigd. In een afzonderlijke zelfstudie wordt uitgelegd hoe netwerkkabels worden gekoppeld en de stroom wordt aangesloten.

Het duurt ongeveer een uur om een Azure FXT Edge Filer-knooppunt te installeren.

De zelfstudie omvat deze installatiestappen:

> [!div class="checklist"]
>
> * Het apparaat uitpakken
> * Det apparaat in een rek inbouwen
> * De omlijsting vooraan installeren (optioneel)

## <a name="installation-prerequisites"></a>Installatievereisten

Voordat u begint, moet u ervoor zorgen dat het datacentrum en het rek dat u wilt gebruiken, over de volgende functies beschikken:

* Een vrij 1U-slot in het rek waarin u het apparaat wilt monteren.
* Netvoeding en koelsystemen die voldoen aan de behoeften van de Azure FXT Edge Filer. (Lees [Specificaties voor voeding en thermische eigenschappen](fxt-specs.md#power-and-thermal-specifications) voor hulp bij het plannen en aanpassen van de installatie.)  

  > [!NOTE]
  > Als u optimaal gebruik wilt maken van de twee redundante voedingseenheden (PSUs), gebruikt u energie distributie-eenheden op twee verschillende vertakkingscircuits bij het koppelen van netstroom. Lees [Stroomkabels aansluiten](fxt-network-power.md#connect-power-cables) voor meer informatie.  

## <a name="unpack-the-hardware-node"></a>Het hardware-knooppunt uitpakken

Elk Azure FXT Edge Filer-knooppunt wordt in één doos verzonden. Voltooi deze stappen om een apparaat uit te pakken.

1. Leg de doos op een vlak, horizontaal oppervlak.

2. Controleer de doos en het verpakkingsmateriaal op deuken, scheuren, waterschade of andere duidelijk zichtbare schade. Als de doos of het verpakkingsmateriaal ernstig beschadigd is, moet u de doos niet openen. Neem contact op met Microsoft Support om in overleg te bepalen of het apparaat nog in goede staat zal zijn.

3. Pak de doos uit. Zorg ervoor dat dit de volgende items bevat:
   * Eén FXT-apparaat met enkele behuizing
   * Twee netsnoeren
   * Eén omlijsting voorkant en sleutel
   * Eén railkit-assembly
   * Een kabelmanagement-arm (CMA)
   * Boekje met CMA-installatie-instructies
   * Boekje met installatie-instructies voor inbouw in rek
   * Het boekje Informatie over veiligheid, milieu en regelgeving

Als u niet alle items in de lijst hebt ontvangen, neemt u contact op met de leverancier van het apparaat voor ondersteuning.

Zorg ervoor dat het apparaat voldoende tijd heeft om dezelfde temperatuur te bereiken als de ruimte voordat u dit installeert of inschakelt. Als u op welk deel van het apparaat dan ook condensatie ziet, wacht u ten minste 24 uur voordat u de installatie uitvoert.

De volgende stap is het in het rek monteren van uw apparaat.

## <a name="rack-the-device"></a>Het apparaat in het rek monteren

Het Azure FXT Edge Filer-apparaat moet worden geïnstalleerd in een standaard 19-inch rek.

The Azure FXT Edge Filer-hybride opslagcache bestaat uit drie of meer Azure FXT Edge-bestandsapparaten. Herhaal de stappen voor de installatie van het rek voor elk apparaat dat deel uitmaakt van uw systeem.

### <a name="rack-install-prerequisites"></a>Vereisten voor installatie in rek

* Lees voordat u begint de veiligheidsinstructies in het boekje Informatie over Veiligheid, milieu en regelgeving dat is verzonden met uw apparaat.

  > [!NOTE]
  > Gebruik altijd twee personen bij het tillen van het knooppunt, ook wanneer u het in een rek installeert of uit het rek verwijdert.

* Bepaal het type railinstallatie dat wordt gebruikt voor uw materiaalrek.
  * Volg de gereedschapsloze instructies voor het gebruik van inklikrekken met vierkante of ronde gaten.
  * Volg de instructies voor gereedschap voor rekken met gaten met schroefdraad.
  
    Voor de configuratie van railmontage met gereedschap heeft u acht schroeven nodig, type 10-32, 12-24, M5 of M6. De kopdiameter van de schroeven moet kleiner zijn dan 10 mm (0,4").

### <a name="identify-the-rail-kit-contents"></a>De inhoud van de railkit identificeren

Zoek de onderdelen voor het installeren van de railkit-assembly:

1. Two A7 Dell ReadyRails II sliding rail-assembly's (1)
1. Twee Hook and Loop-riemen (2)

![Genummerde tekening van de inhoud van de railkit](media/fxt-install/identify-rail-kit-contents-400.png)

### <a name="rail-assembly---tool-less-rails-square-hole-or-round-hole-racks"></a>Rail-assembly - gereedschapsloze rails (rekken met vierkante of ronde gaten)

Voor racks met vierkante of ronde inklikgaten, volgt u deze procedure om de rails samen te stellen en te installeren.

1. Plaats de eindstukken van de linker- en rechter rails met het label **FRONT** naar de binnenkant. Plaats elk eindstuk zodanig dat het in de gaten aan de voorzijde van de verticale rekrichels rust. (1)

2. Lijn elk eind stuk in de onderste en bovenste gaten van het rek uit in de ruimte waarin u het wilt monteren. (2)

3. Beweeg de achterkant van de rail totdat deze volledig rust de verticale rekrichel en de vergrendeling klikt vast. Herhaal deze stappen om het voorste gedeelte op de verticale rekrichel te positioneren en te laten rusten. (3)

> [!TIP]
> Als u de rails wilt verwijderen, trek u de knop uit om de vergrendeling los te maken op het eindpunt van het gedeelte (4) en maakt u elke rail los.

![Diagram van het installeren en verwijderen van gereedschapsloze rails, met genummerde stappen](media/fxt-install/installing-removing-tool-less-rails-400.png)

### <a name="rail-assembly---tooled-rails-threaded-hole-racks"></a>Railassemblage - rails met gereedschap (rekken met gaten met schroefdraad)

Voor racks met gaten met schroefdraad, volgt u deze procedure om de rails samen te stellen en te installeren.

1. Verwijder de pinnen van de monteerhaakjes voor- en achter met een vlakke schroevendraaier. (1)
1. Trek en draai de subassemblages van de railvergrendeling om ze te verwijderen van de monteerhaken. (2)
1. Bevestig de linker- en rechter-montagerails aan de verticale rekrichels aan de voorzijde met behulp van twee paar schroeven. (3)
1. Schuif de linker- en rechterhaakjes naar voren tegen de verticale rekrichel en voeg deze toe met behulp van twee paar schroeven. (4)

![Diagram van het installeren en verwijderen van rails met gereedschap, met genummerde stappen](media/fxt-install/installing-removing-tooled-rails-400.png)

### <a name="install-the-system-in-the-rack"></a>Het systeem in het rek installeren

Volg deze stappen om het Azure FXT Edge Filer-apparaat in het rek te monteren.

1. Trek de binnenste schuifrails uit het rek totdat ze zijn vergrendeld. (1)
1. Zoek de achterste railpinnen aan de achterzijde van het apparaat en laat deze in de eerste J-sleuven op de schuifassemblage zakken. (2)
1. Draai het apparaat omlaag totdat alle railpinnen in de J-sleuven rusten. (3)
1. Duw het apparaat naar binnen totdat de vergrendelingen vastklikken.
1. Druk op de vergrendelingsknoppen van de schuif op beide rails (4) en schuif het apparaat het rek in.

![Installeer het systeem in een rekdiagram, waarbij de stappen zijn genummerd](media/fxt-install/installing-system-rack-400.png)

### <a name="remove-the-system-from-the-rack"></a>Het systeem uit het rek verwijderen

Als u het apparaat uit het rek wilt verwijderen, volgt u deze procedure.

1. Zoek de vergrendelingen aan de zijkanten van de binnenkant van de rails (1).
2. Ontgrendel elke hendel door deze naar de vrijgavepositie te verplaatsen (2).
3. Grijp de zijkanten van het systeem stevig vast en trek het naar voren, totdat de railpinnen vooraan in de J-sleuven staan. Til het systeem omhoog en weg van het rek en plaats het op een vlakke oppervlakte (3).

![Afbeelding van het verwijderen van een systeem uit het rek, waarbij de stappen zijn genummerd](media/fxt-install/removing-system-rack-400.png)

### <a name="engage-the-slam-latch"></a>Het slagslot activeren

1. Zoek vanaf de voorkant het slagslot (1) aan beide zijden van het systeem.
2. De sloten worden automatisch ingeschakeld wanneer het systeem het rek in wordt geduwd.

Als u de sloten wilt vrijgeven bij het verwijderen van het systeem, trekt u deze omhoog (2).

Er zijn optionele schroeven voorzien voor harde bevestiging ter beveiliging van het systeem in het rek voor verzending of in andere instabiele omgevingen. Zoek de schroef onder elk slot en verschuif ze met een #2 kruiskopschroevendraaier (3).

![Genummerde afbeelding van het aanbrengen en vrijgeven van het slagslot](media/fxt-install/engaging-releasing-slam-latch-400.png)

### <a name="install-the-cable-management-arm"></a>Installeer de kabelmanagement-arm

Er wordt een optionele kabelmanagement-arm (CMA) met de FXT Edge Filer meegeleverd. Gedrukte instructies voor de installatie hiervan zijn opgenomen in het pakket.

1. Pak de onderdelen van de kabelmanagement-arm-kit uit en identificeer ze:
   * CMA-lade (1)
   * CMA (2)
   * Nylon kabelbinders (3)
   * CMA-bevestigingshaken (4)
   * Statusindicator-kabel (5)

   > [!TIP]
   > Als u de CMA voor verzending in het rek wilt beveiligen, moet u de kabelbinders rond zowel de manden als de lade binden en deze goed vastzetten. Als u de CMA op deze manier beveiligt, wordt uw systeem ook in instabiele omgevingen beveiligd.

   ![Afbeelding van CMA-onderdelen](media/fxt-install/cma-kit-400.png)

2. Installeer de CMA-lade.

   De CMA-lade biedt ondersteuning en fungeert als houder voor de CMA.

   1. Lijn de zijkanten van de lade uit en koppel ze met de ontvangsthaken aan de binnenranden van de rails.
   1. Duw de lade voorwaarts tot deze vastklikt. (1)
   1. Als u de lade wilt verwijderen, knijpt u de vergrendelingsknoppen naar het midden en trekt u de lade uit de ontvangsthaken (2).

   ![Afbeelding van de installatie van de CMA-lade](media/fxt-install/cma-tray-install-400.png)

3. Installeer de CMA-bevestigingshaken.

   > [!NOTE]
   >
   > * U kunt de CMA zowel koppelen aan de rechter als aan de linker montagerail, afhankelijk van hoe u kabels van het systeem wilt leiden.
   > * Voor het gemak koppelt u de CMA aan de kant tegenover de voeding (kant A). Als deze aan kant B is gekoppeld, moet de CMA worden losgekoppeld om de buitenste voeding te kunnen verwijderen.
   > * Verwijder de lade altijd voordat u de voedingen verwijdert.

   ![Afbeelding van de installatie van de CMA-haken](media/fxt-install/cma-bracket-l-r-install-400.png)

   1. Selecteer de juiste CMA-bevestigingshaak voor de kant waar u de CMA wilt monteren (kant B of kant A).
   1. Installeer de CMA-bevestigingshaak met de overeenkomstige kant A of kant B-markering aan de achterkant van de schuifrail.
   1. Lijn de gaten op het haakje uit met de pinnen op de schuifrail. Duw het haakje omlaag totdat het is vergrendeld.

4. Installeer de CMA.

   1. Aan de achterkant van het systeem plaatst u de vergrendeling aan de voorkant van de CMA op de binnenste haak van de schuif-assembly tot de vergrendeling vastgrijpt (1).
   1. Plaats de andere vergrendeling aan het einde van het buitenste haakje tot de vergrendeling vastgrijpt (2).
   1. Als u de CMA wilt verwijderen, moet u beide vergrendelingen opheffen door op de vrijgaveknoppen van de CMA te drukken boven aan de binnenkant van de binnenste en buitenste grendels (3).

   ![Afbeelding van de installatie van de hoofd-CMA-lade](media/fxt-install/cma-install-400.png)

   De CMA kan worden geroteerd vanuit het systeem voor toegang en service. Til de CMA aan de kant met de scharnieren weg van de lade uit zijn positie (1). Nadat hij is verwijderd uit de lade, wordt de CMA van het systeem weggedraaid (2).

   ![Afbeelding van CMA geroteerd open voor service](media/fxt-install/cma-swing-over-tray-400.png)

## <a name="install-the-front-bezel-optional"></a>De omlijsting vooraan installeren (optioneel)

In deze sectie wordt uitgelegd hoe u de vooromlijsting (faceplate) voor de Azure FXT Edge Filer-hardware kunt installeren en verwijderen.

De omlijsting vooraan installeren:

1. Zoek en verwijder de omlijstingssleutel, die wordt geleverd in de verpakking van de omlijsting.
1. Lijn de omlijsting met de voorzijde van het chassis uit en plaats de pinnen aan de rechterkant van de omlijsting in de gaten op de rechter koppelingsrichel van het knooppunt.
1. Plaats de linkerkant van de omlijsting op het chassis. Druk op de omlijsting totdat de knop aan de linkerkant vastklikt.
1. Vergrendel de omlijsting met de sleutel.

De omlijsting vooraan verwijderen:

1. Ontgrendel de omlijsting met behulp van de omlijstingssleutel.
1. Klik aan de linkerkant op de vrijgaveknop en trek de linkerkant van de omlijsting weg van het chassis.
1. Haak de rechterkant uit en verwijder de omlijsting.

   ![Afbeelding met de vrijgaveknop aan de linkerkant van de omlijsting en hoe u deze kunt verwijderen door vanuit de linkerkant naar buiten te trekken](media/fxt-install/remove-bezel-edited-600.png)

## <a name="next-steps"></a>Volgende stappen

Nadat u het apparaat hebt uitgepakt en in het rek hebt gebouwd, gaat u door met de installatie door netwerkkabels te koppelen en netstroom te verbinden met de Azure FXT Edge Filer.

> [!div class="nextstepaction"]
> [De netwerkpoorten bekabelen en stroom leveren](fxt-network-power.md)
