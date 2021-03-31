---
title: Uw StorSimple 8000 Series-apparaat in-of uitschakelen
description: In dit artikel wordt uitgelegd hoe u een nieuw StorSimple-apparaat inschakelt, een apparaat inschakelt dat is afgesloten of verloren is gegaan en u een actief apparaat uitschakelt.
author: alkohli
ms.assetid: 8e9c6e6c-965c-4a81-81bd-e1c523a14c82
ms.service: storsimple
ms.topic: how-to
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c8f4269235c494ff9dd8d1bf8e0ef940562f8927
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85515263"
---
# <a name="turn-on-or-turn-off-your-storsimple-8000-series-device"></a>Het StorSimple 8000 Series-apparaat in-of uitschakelen

## <a name="overview"></a>Overzicht
Het afsluiten van een Microsoft Azure StorSimple apparaat is niet vereist als onderdeel van de normale werking van het systeem. Het is echter mogelijk dat u een nieuw apparaat of een apparaat moet inschakelen dat moet worden afgesloten. Normaal gesp roken is een afsluiting vereist in gevallen waarin het nodig is om hardware te vervangen, een eenheid fysiek te verplaatsen of een apparaat buiten gebruik te maken. In deze zelf studie wordt de vereiste procedure beschreven voor het inschakelen en afsluiten van uw StorSimple-apparaat in verschillende scenario's.

## <a name="turn-on-a-new-device"></a>Een nieuw apparaat inschakelen
De stappen voor het inschakelen van een StorSimple-apparaat voor de eerste keer variëren, afhankelijk van het feit of het apparaat een 8100-of 8600-model is. De 8100 heeft één primaire behuizing, terwijl 8600 een apparaat met twee behuizingen is met een primaire behuizing en een EBOD behuizing. De gedetailleerde stappen voor beide modellen worden in de volgende secties besproken.

* [Nieuw apparaat met alleen primaire behuizing](#new-device-with-primary-enclosure-only)
* [Nieuw apparaat met EBOD Enclosure](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Nieuw apparaat met alleen primaire behuizing
Het StorSimple 8100-model is een apparaat met één behuizing. Uw apparaat bevat redundante voeding-en koel modules (PCMs). Beide PCMs moeten zijn geïnstalleerd en verbonden zijn met verschillende energie bronnen om hoge Beschik baarheid te garanderen.

Voer de volgende stappen uit om uw apparaat op de stroom te aansluiten.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

> [!NOTE]
> Ga naar [uw StorSimple 8100-apparaat installeren voor de](storsimple-8100-hardware-installation.md)volledige installatie-en bekabelings instructies van het apparaat. Zorg ervoor dat u de instructies precies volgt.
> 
> 

### <a name="new-device-with-ebod-enclosure"></a>Nieuw apparaat met EBOD Enclosure
Het StorSimple 8600-model heeft zowel een primaire behuizing als een EBOD-behuizing. Hiervoor moeten de eenheden worden verbonden met een SAS-verbinding (Serial Attached SCSI) en kracht.

Wanneer u dit apparaat voor de eerste keer instelt, voert u de stappen voor SAS-bekabeling eerst uit en voert u de stappen voor energie bekabeling uit.

[!INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

> [!NOTE]
> Ga naar [uw StorSimple 8600-apparaat installeren voor de](storsimple-8600-hardware-installation.md)volledige installatie-en bekabelings instructies van het apparaat. Zorg ervoor dat u de instructies precies volgt.

## <a name="turn-on-a-device-after-shutdown"></a>Een apparaat inschakelen na afsluiten
De stappen voor het inschakelen van een StorSimple-apparaat nadat het is uitgeschakeld, zijn afhankelijk van het feit of het apparaat een 8100-of 8600-model is. De 8100 heeft één primaire behuizing, terwijl 8600 een apparaat met twee behuizingen is met een primaire behuizing en een EBOD behuizing.

* [Apparaat met alleen primaire behuizing](#device-with-primary-enclosure-only)
* [Apparaat met EBOD Enclosure](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Apparaat met alleen primaire behuizing
Gebruik na het afsluiten de volgende procedure om een StorSimple-apparaat in te scha kelen met een primaire behuizing en zonder EBOD behuizing.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Een apparaat met alleen primaire behuizing inschakelen
1. Zorg ervoor dat de energie switches voor zowel energie-als koel modules (PCMs) in de buiten stand staan. Als de switches niet zijn uitgeschakeld, spiegelt u ze naar de buiten stand en wacht u totdat de lampjes uitvallen.
2. Schakel het apparaat in door de Schakel opties voor beide PCMs te spie gelen naar de op-positie. Het apparaat moet worden ingeschakeld.
3. Controleer het volgende om te controleren of het apparaat volledig is:
   
   1. De OK-Led's op beide PCM-modules zijn groen.
   2. De status-Led's op beide controllers zijn groen.
   3. De blauwe LED op een van de controllers knippert, wat aangeeft dat de controller actief is.
      
      Als aan een van deze voor waarden niet wordt voldaan, is uw apparaat niet in orde. Neem [contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>Apparaat met EBOD Enclosure
Gebruik na het afsluiten de volgende procedure om een StorSimple-apparaat in te scha kelen met een primaire behuizing en een EBOD-behuizing. Voer elke stap in de juiste volg orde uit zoals beschreven. Als u dit niet doet, kan dit leiden tot gegevens verlies.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Een apparaat met een primaire en een EBOD-behuizing inschakelen
1. Zorg ervoor dat de EBOD-behuizing is verbonden met de primaire behuizing. Zie [uw StorSimple 8600-apparaat installeren](storsimple-8600-hardware-installation.md)voor meer informatie.
2. Zorg ervoor dat de voeding en koel modules (PCMs) op zowel de EBOD als de primaire behuizingen zich in de buiten stand bevinden. Als de switches niet zijn uitgeschakeld, spiegelt u ze naar de buiten stand en wacht u totdat de lampjes uitvallen.
3. Schakel eerst de EBOD-behuizing in door de Schakel opties voor beide PCMs te spie gelen. De PCM-Led's moeten groen zijn. Een groene EBOD-controller in deze eenheid geeft aan dat de EBOD behuizing is ingeschakeld.
4. Schakel de primaire behuizing in door de Schakel opties voor beide PCMs te spie gelen naar de op-positie. Het hele systeem moet nu zijn ingeschakeld.
5. Controleer of de SAS-Led's groen zijn. Dit zorgt ervoor dat de verbinding tussen de EBOD behuizing en de primaire behuizing goed is.

## <a name="turn-on-a-device-after-a-power-loss"></a>Een apparaat inschakelen na een stroom storing
Een stroom storing of-onderbreking kan een StorSimple-apparaat afsluiten. De stroom storing kan optreden bij een van de voedingen of beide energie-eenheden. De herstels tappen verschillen, afhankelijk van of het apparaat een 8100-of 8600-model is. De 8100 heeft één primaire behuizing, terwijl 8600 een apparaat met twee behuizingen is met een primaire behuizing en een EBOD behuizing. In deze sectie wordt de herstel procedure voor elk scenario beschreven.

* [Apparaat met alleen primaire behuizing](#8100)
* [Apparaat met EBOD Enclosure](#8600)

### <a name="device-with-primary-enclosure-only"></a>Apparaat met alleen primaire behuizing <a name="8100"></a>
Het systeem kan de normale werking voortzetten als er sprake is van stroom storingen in een van de energie-eenheden. Als u echter een hoge Beschik baarheid van het apparaat wilt garanderen, moet u zo snel mogelijk de kracht van de voeding herstellen.

Als er sprake is van een stroom storing of een stroom onderbreking voor beide voedingen, wordt het systeem op een ordelijk en gecontroleerde manier afgesloten. Wanneer de stroom wordt hersteld, wordt het systeem automatisch ingeschakeld.

### <a name="device-with-ebod-enclosure"></a>Apparaat met EBOD Enclosure <a name="8600"></a>
#### <a name="power-loss-on-one-power-supply"></a>Energie verlies bij één voeding
Het systeem kan de normale werking voortzetten als er sprake is van stroom uitval van een van de voedingen op de primaire behuizing of de EBOD-behuizing. Als u echter een hoge Beschik baarheid van het apparaat wilt garanderen, moet u zo snel mogelijk de stroom naar de voeding herstellen.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Stroom uitval voor beide voedings eenheden op primaire en EBOD Behuizingen
Als er sprake is van een stroom storing of een stroom onderbreking voor beide voedingen, wordt de EBOD Enclosure onmiddellijk afgesloten en wordt de primaire behuizing op een ordelijk en gecontroleerde manier afgesloten. Wanneer de stroom wordt hersteld, wordt het apparaat automatisch gestart.

Als de stroom hand matig is uitgeschakeld, voert u de volgende stappen uit om de kracht van het systeem te herstellen.

1. Schakel de EBOD-behuizing in.
2. Wanneer de EBOD-behuizing is ingeschakeld, schakelt u de primaire behuizing in.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Stroom uitval voor beide voedingen op EBOD behuizing
Wanneer u de kabels hebt ingesteld, moet u ervoor zorgen dat de EBOD nooit alleen op een afzonderlijke PDU is aangesloten. Als de EBOD en de primaire behuizing op hetzelfde moment mislukken, zal het systeem herstellen.

Als alleen de EBOD behuizing van beide voedingen mislukt, wordt het systeem niet automatisch hersteld. Voer de volgende stappen uit om het systeem in te scha kelen en terug te zetten naar een goede status:

1. Als de primaire behuizing is ingeschakeld, schakelt u zowel energie-als koel modules (PCMs) uit.
2. Wacht een paar minuten totdat het systeem is afgesloten.
3. Schakel de EBOD-behuizing in.
4. Wanneer de EBOD-behuizing is ingeschakeld, schakelt u de primaire behuizing in.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Een apparaat inschakelen nadat de primaire en EBOD behuizing verbinding is verbroken
Als de verbinding tussen de stand-by-controller en de bijbehorende EBOD-controller is verbroken, blijft het apparaat werken. Als de verbinding tussen de systeem actieve controller en de bijbehorende EBOD-controller verloren gaat, moet de failover optreden en moet het apparaat normaal blijven werken.

Wanneer beide SAS-kabels (Serial Attached SCSI) worden verwijderd of de verbinding tussen de EBOD-behuizing en de primaire behuizing ernstig is, werkt het apparaat niet meer. Voer op dit moment de volgende stappen uit.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Het apparaat inschakelen nadat de verbinding is verbroken
1. Toegang tot de achtergrond van het apparaat.
2. Als de SAS-kabel verbinding tussen de EBOD behuizing en de primaire behuizing is verbroken, worden alle SAS Lane-Led's op de EBOD-behuizing uitgeschakeld.
3. Sluit zowel energie-als koel modules (PCMs) op de EBOD behuizing en de primaire behuizing af.
4. Wacht totdat alle lampjes op de achtergrond van de behuizing zijn uitgeschakeld.
5. Plaats de SAS-kabels opnieuw en zorg ervoor dat er een goede verbinding is tussen de EBOD behuizing en de primaire behuizing.
6. Schakel eerst de EBOD-behuizing in door beide PCM-switches naar de op positie te spie gelen.
7. Controleer of de EBOD-behuizing is ingeschakeld door te controleren of de groene LED is ingeschakeld.
8. Schakel de primaire behuizing in.
9. Controleer of de primaire behuizing is ingeschakeld door te controleren of de LED voor de controller groene is ingeschakeld.
10. Controleer of de EBOD Enclosure-verbinding met de primaire behuizing goed is door te controleren of de SAS Lane-Led's (vier per EBOD-controller) zijn.

> [!IMPORTANT]
> Als de SAS-kabels defect zijn of de verbinding tussen de EBOD behuizing en de primaire behuizing niet goed is, wordt de herstel modus ingeschakeld wanneer u het systeem inschakelt. Neem [contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md) als dit het geval is.


## <a name="turn-off-a-running-device"></a>Een actief apparaat uitschakelen
Een actief StorSimple-apparaat moet mogelijk worden afgesloten als het wordt verplaatst, buiten gebruik wordt gesteld of een defect onderdeel heeft dat moet worden vervangen. De stappen verschillen, afhankelijk van het feit of het StorSimple-apparaat een 8100-of 8600-model is. De 8100 heeft één primaire behuizing, terwijl 8600 een apparaat met twee behuizingen is met een primaire behuizing en een EBOD behuizing. In deze sectie vindt u informatie over de stappen voor het afsluiten van een actief apparaat.

* [Apparaat met primaire behuizing](#8100a)
* [Apparaat met EBOD Enclosure](#8600a)

### <a name="device-with-primary-enclosure"></a>Apparaat met primaire behuizing <a name="8100a"></a>
Als u het apparaat op een ordelijk en gecontroleerde manier wilt afsluiten, kunt u dit doen via de Azure Portal of via de Windows PowerShell voor StorSimple. 

> [!IMPORTANT]
> Sluit een actief apparaat niet af met behulp van de aan/uit-knop aan de achterkant van het apparaat.
> 
> Voordat u het apparaat afsluit, controleert u of alle onderdelen van het apparaat in orde zijn. Navigeer in het Azure Portal naar **apparaten**  >    >  **Hardware-status** controleren en controleer of de status van alle onderdelen groen is. Dit geldt alleen voor een goed systeem. Als het systeem wordt afgesloten om een defect onderdeel te vervangen, ziet u de status mislukt (rood) of gedegradeerd (geel) voor het respectieve onderdeel in de **Hardware-status**.
> 
> 

Nadat u toegang hebt tot de Windows PowerShell voor StorSimple of de Azure Portal, volgt u de stappen in [een StorSimple-apparaat afsluiten](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure"></a>Apparaat met EBOD Enclosure <a name="8600a"></a>
> [!IMPORTANT]
> Voordat u de primaire behuizing en de EBOD-behuizing afsluit, moet u ervoor zorgen dat alle onderdelen van het apparaat in orde zijn. Navigeer in het Azure Portal naar **apparaten**  >    >  **Hardware-status** controleren en controleer of alle onderdelen in orde zijn.


#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Een actief apparaat afsluiten met EBOD Enclosure
1. Volg alle stappen in [een StorSimple-apparaat afsluiten](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) voor de primaire behuizing.
2. Nadat de primaire behuizing is afgesloten, sluit u de EBOD af door te spie gelen van zowel de Schakel opties voor energie als koel module (PCM).
3. Als u wilt controleren of de EBOD is uitgeschakeld, controleert u of alle lampjes aan de achterkant van de EBOD-behuizing zijn uitgeschakeld.

> [!NOTE]
> De SAS-kabels die worden gebruikt om de EBOD-behuizing aan de primaire behuizing te koppelen, moeten pas worden verwijderd nadat het systeem is afgesloten.

## <a name="next-steps"></a>Volgende stappen
[Neem contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md) als u problemen ondervindt bij het inschakelen of afsluiten van een StorSimple-apparaat.

