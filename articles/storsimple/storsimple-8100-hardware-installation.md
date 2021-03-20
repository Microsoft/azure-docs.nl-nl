---
title: Microsoft Azure StorSimple 8100-apparaat installeren
description: Hierin wordt beschreven hoe u het StorSimple 8100-apparaat uitpakt, koppelt en kabelt voordat u de software implementeert en configureert.
author: alkohli
ms.assetid: 6098a01e-c031-488a-a8d7-0b607ce665e1
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 113b72ddf7e5d508c8a0b577d4004d4fbd83e8e5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84699104"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Uw StorSimple 8100-apparaat uitpakken, aan het rek koppelen en bekabelen

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Overzicht
Uw Microsoft Azure StorSimple 8100 is één behuizing, een apparaat dat is gekoppeld aan het rek. In deze zelf studie wordt uitgelegd hoe u de StorSimple 8100-computerhardware uitpakt, koppelt en bekabelt voordat u het StorSimple-apparaat configureert en implementeert.

## <a name="unpack-your-storsimple-8100-device"></a>Uw StorSimple 8100-apparaat uitpakken
De volgende stappen bevatten duidelijke, gedetailleerde instructies voor het uitpakken van uw StorSimple 8100-opslag apparaat. Dit apparaat wordt verzonden in één doos.

### <a name="prepare-to-unpack-your-device"></a>Voorbereiden op het uitpakken van uw apparaat
Lees de volgende informatie voordat u uw apparaat uitpakt.

![Waarschuwing pictogram pictogram voor ](./media/storsimple-safety/IC740879.png)![ zwaar gewicht ](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **.**

1. Zorg ervoor dat er twee mensen beschikbaar zijn voor het beheren van het gewicht van de behuizing als u deze hand matig verwerkt. Een volledig geconfigureerde behuizing kan tot 32 kg (70 lbs) wegen.
2. Leg de doos op een vlak, horizontaal oppervlak.

Voer vervolgens de volgende stappen uit om uw apparaat uit te pakken.

#### <a name="to-unpack-your-device"></a>Uw apparaat uitpakken
1. Controleer de doos en het verpakkingsmateriaal op deuken, scheuren, waterschade of andere duidelijk zichtbare schade. Als de doos of het verpakkingsmateriaal ernstig beschadigd is, moet u de doos niet openen. Neem [contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md) om u te helpen bepalen of het apparaat in goede staat werkt.
2. Pak de doos uit. In de volgende afbeelding ziet u de uitgepakte weer gave van uw StorSimple-apparaat.
   
     ![Uw opslag apparaat uitpakken](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)
   
    **Uitgepakte weer gave van uw opslag apparaat**
   
   | Label | Beschrijving |
   | --- | --- |
   |   1 |Vak voor verpakking |
   |   2 |Onderste schuim |
   |   3 |Apparaat |
   |   4 |Bovenste schuim |
   |   5 |Vak voor accessoire |
3. Controleer of de inhoud van de doos bestaat uit de volgende items:
   
   * 1 apparaat met één behuizing
   * 2 stroom kabels
   * 1 Crossover Ethernet-kabel
   * 2 kabels van seriële console
   * 1 serieel USB-conversie programma voor seriële toegang
   * 1 onrecht matige proef T10 draaier
   * 4 QSFP-naar-SFP +-adapters voor gebruik met 10 GbE-netwerk interfaces
   * 1 rack montagekit (2 aan rails met koppel bare hardware)
   * Documentatie voor aan de slag
     
     Als u geen van de hierboven vermelde items hebt ontvangen, [neemt u contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md).

De volgende stap is het in het rek monteren van uw apparaat.

## <a name="rack-mount-your-storsimple-8100-device"></a>Rack: uw StorSimple 8100-apparaat koppelen
Volg de volgende stappen om uw StorSimple 8100-opslag apparaat te installeren in een standaard 19-inch rack met front-en post berichten. Het StorSimple 8100-apparaat heeft één primaire behuizing.

De installatie bestaat uit meerdere stappen, die allemaal worden besproken in de volgende procedures.

> [!IMPORTANT]
> StorSimple-apparaten moeten zijn gekoppeld aan een rek voor een goede werking.
> 
> 

### <a name="prepare-the-site"></a>De site voorbereiden
Het apparaat moet worden geïnstalleerd in een standaard 19-inch rack met zowel voor-als achterliggende berichten. Gebruik de volgende procedure om de rack installatie voor te bereiden.

#### <a name="to-prepare-the-site-for-rack-installation"></a>De site voorbereiden voor de installatie van een rek
1. Het apparaat staat veilig op een vlak, stabiel en horizontaal werkoppervlak (of vergelijkbaar).
2. Controleer of de site waar u wilt instellen, een standaard AC van een onafhankelijke bron of een PDU (netstroom distributie-eenheid) met een nood voeding (UPS) heeft.
3. Zorg ervoor dat er één 2U-sleuf beschikbaar is in het rek waarin u het apparaat wilt koppelen.

![Waarschuwing pictogram pictogram voor ](./media/storsimple-safety/IC740879.png)![ zwaar gewicht ](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **.**

Zorg ervoor dat er twee mensen beschikbaar zijn voor het beheren van het gewicht als u de apparaat-installatie hand matig uitvoert. Een volledig geconfigureerde behuizing kan tot 32 kg (70 lbs) wegen.

### <a name="rack-prerequisites"></a>Rack vereisten
De 8100-behuizing is ontworpen voor installatie in een standaard 19-inch rack behuizing met:

* Minimale diepte van 27,84 cm van het rack bericht tot post.
* Maximum gewicht van 32 kg voor het apparaat
* Maximale druk van 5 Pascal (0,5 mm vocht meter).

### <a name="rack-mounting-rail-kit"></a>Rail Kit voor rack montage
Er wordt een reeks montage rails meegeleverd voor gebruik met de 19-inch rack behuizing. De rails zijn getest om het gewicht van de maximale behuizing te verwerken. Deze rails bieden ook de mogelijkheid om meerdere behuizingen te installeren zonder dat er ruimte in het rek verloren gaat.

#### <a name="to-install-the-device-on-the-rails"></a>Het apparaat op de rails installeren
1. Voer deze stap alleen uit als de interne rails niet op uw apparaat zijn geïnstalleerd. Normaal gesp roken worden de interne rails in de fabriek geïnstalleerd. Als er geen rails zijn geïnstalleerd, installeert u de dia's uit de linker-en rechter rails aan de zijkanten van het behuizing-chassis. Ze worden aan elke zijde gekoppeld met zes metrische schroeven. Om u te helpen bij het bezorgen van de afdruk stand, worden de Rail dia's als **LH-front** en **RH-front** en wordt het uiteinde dat zich op de achterkant van de behuizing bevindt, verzegeld.<br/>
   
    ![Rail dia's koppelen aan het behuizing-chassis](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Binnenste Rail dia's aan de zijden van de behuizing koppelen**
   
    Label | Beschrijving
    ----- | -----------
    1     | M 3x4 knop-Head schroefs
    2     | Chassis dia's

2. Koppel de buitenste Rail-en buitenste rechter rails aan de verticale leden van het rek-Cabinet. De vier Kante haken zijn gemarkeerd **LH**, **RH** en **deze kant** om u door de juiste stand te leiden.
3. Zoek de pennen van de rails aan voor- en achterzijde van de rail. Breid de rails uit voor de rack berichten en plaats de pincodes in de voor-en achterliggende leden van het rek post verticaal. Zorg ervoor dat de Rail-assembly niveau is.
4. Gebruik twee van de verschafte metrische schroeven om de Rail-assembly te beveiligen met de verticale leden van het rek. Gebruik één schroef aan de voor zijde en een aan de achterzijde.
5. Herhaal deze stappen voor de andere Rail-assembly.<br/>
   
     ![Rail dia's koppelen aan een rack behuizing](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Buitenste Rail-assembly's aan het rek koppelen**
   
   | Label | Beschrijving |
   | --- | --- |
   |   1 |Schroef bout |
   |   2 |Vier Kante opening voor rack-schroef |
   |   3 |Pincode voor locatie links spoor |
   |   4 |Schroef bout |
   |   5 |Pincodes voor achtergelaten rails |

### <a name="mounting-the-device-in-the-rack"></a>Het apparaat in het rek koppelen
Voer de volgende stappen uit om het apparaat in het rek te koppelen met behulp van de rack rails die zojuist zijn geïnstalleerd.

#### <a name="to-mount-the-device"></a>Het apparaat koppelen
1. Met een assistent, til u de behuizing op en lijnt u deze uit met de Rack Rails.
2. Plaats het apparaat zorgvuldig in de rails en push het apparaat vervolgens volledig naar de rack behuizing.<br/>
   
    ![Apparaat in het rek invoegen](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Het apparaat in het rek koppelen**
3. Verwijder de linker-en rechter flens van de uiteinden door de kapitalen vrij te halen. De flens uiteinden worden simpelweg op de flens uitgelijnd.
4. Beveilig de behuizing in het rek door één van de meegeleverde Phillips via elke flens, links en rechts, te installeren.
5. Installeer de flens uiteinden door ze op de juiste plaats te zetten.<br/>
   
     ![Flens cap's installeren](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Flens cap's installeren**
   
   | Label | Beschrijving |
   | --- | --- |
   |   1 |Schroef bout behuizing |

De volgende stap is het bekabelen van uw apparaat voor energie, netwerk en seriële toegang.

## <a name="cable-your-storsimple-8100-device"></a>Uw StorSimple 8100-apparaat bekabelen
In de volgende procedures wordt uitgelegd hoe u het StorSimple 8100-apparaat kunt bekabelen voor energie-, netwerk-en seriële verbindingen.

### <a name="prerequisites"></a>Vereisten
Voordat u begint met de bekabeling van uw apparaat hebt u het volgende nodig:

* Uw opslag apparaat, volledig uitgepakt en rek gekoppeld.
* 2 stroom kabels die zijn meegeleverd met uw apparaat
* Toegang tot 2 energie distributie-eenheden (aanbevolen).
* Netwerk kabels
* Beschik bare seriële kabels
* Serieel USB-converter met het juiste stuur programma dat is geïnstalleerd op uw PC (indien nodig)
* Voorzien in 4 QSFP-naar-SFP +-adapters voor gebruik met 10 GbE-netwerk interfaces
* [Ondersteunde hardware voor de 10 GbE-netwerk interfaces op uw StorSimple-apparaat](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="power-cabling"></a>Stroom kabels
Uw apparaat bevat redundante voeding-en koel modules (PCMs). Beide PCMs moeten zijn geïnstalleerd en verbonden zijn met verschillende energie bronnen om hoge Beschik baarheid te garanderen.

Voer de volgende stappen uit om uw apparaat op de stroom te aansluiten.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Netwerk kabels
Uw apparaat is een configuratie met actief/stand-by: op elk gewenst moment is één controller module actief en worden alle schijf-en netwerk bewerkingen verwerkt terwijl de andere controller module op stand-by staat. Als een controller mislukt, wordt de stand-by-Controller onmiddellijk geactiveerd en worden alle schijf-en netwerk bewerkingen voortgezet.

Ter ondersteuning van deze redundante Controller-failover moet u het netwerk van het apparaat bekabelen zoals beschreven in de volgende stappen.

#### <a name="to-cable-for-network-connection"></a>Naar kabel voor netwerk verbinding
1. Uw apparaat heeft zes netwerk interfaces op elke controller: 4 1 Gbps en 2 10 Gbps Ethernet-poorten. Identificeer de verschillende gegevens poorten op het backplane van uw apparaat.
   
    ![Backplane van 8100-apparaat](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Weer gave van het apparaat met gegevens poorten**
   
   | Label | Beschrijving |
   | --- | --- |
   |   0, 1, 4, 5 |1 GbE-netwerk interfaces |
   |   2, 3 |10 GbE-netwerk interfaces |
   |   6 |Seriële poorten |
2. Zie het volgende diagram voor netwerk kabels. (De minimale netwerk configuratie wordt weer gegeven door middel van ononderbroken blauwe lijnen. Aanvullende configuratie die is vereist voor hoge Beschik baarheid en prestaties wordt weer gegeven door stippel lijnen.)

    ![Uw 2U-apparaat bekabelen voor netwerk](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Netwerk kabels voor uw apparaat**

   |Label | Beschrijving |
   |----- | ----------- |
   | A    | LAN met Internet toegang |
   | B    | Controller 0 |
   | C    | PCM 0 |
   | D    | Controller 1 |
   | E    | PCM 1 |
   | F, G | Hosts |
   | 0-5  | Netwerkinterfaces |



Bij het bekabelen van het apparaat vereist de minimale configuratie:

* Ten minste twee netwerk interfaces die zijn verbonden op elke controller met één voor Cloud toegang en één voor iSCSI. De poort DATA 0 wordt automatisch ingeschakeld en geconfigureerd via de seriële console van het apparaat. Naast gegevens 0 moet ook een andere gegevens poort worden geconfigureerd via de klassieke Azure-Portal. In dit geval verbindt u DATA 0-poort met het primaire LAN (netwerk met Internet toegang). De andere gegevens poorten kunnen worden aangesloten op het SAN/iSCSI LAN (VLAN)-segment van het netwerk, afhankelijk van de beoogde rol.
* Identieke interfaces op elke controller die zijn verbonden met hetzelfde netwerk om te zorgen voor Beschik baarheid als er een failover voor de controller plaatsvindt. Als u bijvoorbeeld gegevens 0 en DATA 3 voor een van de controllers wilt verbinden, moet u de overeenkomende gegevens 0 en DATA 3 op de andere controller verbinden.

Denk eraan voor hoge Beschik baarheid en prestaties:

* Configureer, indien mogelijk, een paar netwerk interface voor Cloud toegang (1 GbE) en een ander paar voor iSCSI (10 GbE aanbevolen) op elke controller.
* Als dat mogelijk is, kunt u netwerk interfaces van elke controller verbinden met twee verschillende switches om de beschik baarheid van een switch-fout te garanderen. De afbeelding illustreert de 2 10 GbE-netwerk interfaces, DATA 2 en DATA 3, van elke controller die is verbonden met twee verschillende switches.

Raadpleeg de **netwerk interfaces** onder de [vereisten voor hoge Beschik baarheid voor uw StorSimple-apparaat](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple)voor meer informatie.

> [!NOTE]
> Als u met uw 10 GbE-netwerk interfaces met SFP + transceivers werkt, gebruikt u de meegeleverde QSFP-SFP +-adapters. Voor meer informatie gaat u naar [ondersteunde hardware voor de 10 GbE-netwerk interfaces op uw StorSimple-apparaat](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Bekabeling van seriële poort
Voer de volgende stappen uit om uw seriële poort te bekabelen.

#### <a name="to-cable-for-serial-connection"></a>Naar kabel voor seriële verbinding
1. Uw apparaat heeft een seriële poort op elke controller die wordt geïdentificeerd door een moersleutel-pictogram. Raadpleeg de afbeelding in het gedeelte [netwerk bekabeling](#network-cabling) om de seriële poorten van de backplane van uw apparaat te zoeken.
2. Identificeer de actieve controller op de backplane van uw apparaat. Een knipperende blauwe LED geeft aan dat de controller actief is.
3. Gebruik de meegeleverde seriële kabels (indien nodig, het USB-serieve conversie programma voor uw laptop) en verbind uw console of computer (met terminal emulatie op het apparaat) met de seriële poort van de actieve controller.
4. Installeer de seriële USB-Stuur programma's (geleverd bij het apparaat) op uw computer.
5. Stel de seriële verbinding als volgt in: 115.200 baud, 8 databits, 1 stopbit, geen pariteit en datatransport besturing ingesteld op geen.
6. Controleer of de verbinding werkt door op ENTER op de console te drukken. Er moet een seriële console worden weer gegeven.

> [!NOTE]
> **Licht beheer**: wanneer het apparaat is geïnstalleerd in een extern Data Center of in een computer ruimte met beperkte toegang, moet u ervoor zorgen dat de seriële verbindingen naar beide controllers altijd zijn verbonden met een seriële console-switch of vergelijk bare apparatuur. Dit maakt buiten-band beheer op afstand en ondersteunings bewerkingen mogelijk als er netwerk onderbrekingen of onverwachte storingen zijn.
> 
> 

Uw apparaat is nu bekabeld voor voeding, netwerk toegang en seriële connectiviteit. De volgende stap is het configureren van de software en het implementeren van uw apparaat.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [implementeren en configureren van uw on-premises StorSimple-apparaat](storsimple-8000-deployment-walkthrough-u2.md).

