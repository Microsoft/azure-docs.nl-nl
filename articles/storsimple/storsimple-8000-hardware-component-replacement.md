---
title: StorSimple 8000 serie vervangen van hardware-onderdelen | Microsoft Docs
description: Hierin wordt beschreven hoe u de PCMs, accu, controller modules, EBOD-controllers, schijf stations en het chassis van een StorSimple-apparaat veilig kunt vervangen.
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
ms.date: 06/02/2017
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 12ab5a9598cc0222f5a3e64985be2e2ea9e7e2fd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014853"
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>Een hardware-onderdeel in uw StorSimple 8000 Series-apparaat vervangen

## <a name="overview"></a>Overzicht
Met de zelf studies voor het vervangen van hardware-onderdelen worden de hardwareonderdelen van uw Microsoft Azure StorSimple 8000 Series-apparaat en de benodigde stappen voor het verwijderen en vervangen van de onderdelen beschreven. In dit artikel worden de veiligheids pictogrammen beschreven, bevat verwijzingen naar de gedetailleerde zelf studies en een lijst met de onderdelen die kunnen worden vervangen.

> [!IMPORTANT]
> Voordat u een StorSimple-onderdeel probeert te verwijderen of te vervangen, controleert u of u de [veiligheids pictogrammen conventies](#safety-icon-conventions) en andere [veiligheids maatregelen](storsimple-8000-safety.md)bekijkt.

### <a name="safety-icon-conventions"></a>Conventies voor veiligheids pictogrammen

In de volgende tabel worden de veiligheids pictogrammen beschreven die worden gebruikt in deze zelf studies. Let op deze veiligheids pictogrammen wanneer u de stappen doorloopt om de onderdelen van het apparaat te verwijderen en te vervangen.

| Pictogram | Tekst | Aanvullende informatie |
|:--- |:--- |:--- |
| ![Waarschuwingspictogram](./media/storsimple-hardware-component-replacement/Warning.png) |**GEVAAR!** |Duidt op een gevaarlijke situatie die, indien dat niet wordt vermeden, leidt tot dood of ernstige schade. Dit signaal woord is beperkt tot de meest extreme situaties. |
| ![Waarschuwingspictogram](./media/storsimple-hardware-component-replacement/Warning.png) |**WAARSCHUWING!** |Duidt op een gevaarlijke situatie die, indien dat niet wordt vermeden, kan leiden tot overlijden of ernstige schade. |
| ![Pictogram Waarschuwing](./media/storsimple-hardware-component-replacement/Caution.png) |**VOORZICHTIG!** |Duidt op een gevaarlijke situatie die, indien dat niet wordt vermeden, kan leiden tot kleine of gemiddelde schade. |
| ![Pictogram Opmerking](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**Zie** |Geeft aan dat de informatie die belang rijk is, maar niet met een risico is betrokken. |
| ![Pictogram elektrische schok](./media/storsimple-hardware-component-replacement/Electric.png) |**Gevaar voor elektrische schokken** |Geeft een hoge spanning aan. |
| ![Pictogram met een dik gewicht](./media/storsimple-hardware-component-replacement/Weight.png) |**Dik gewicht** | |
| ![Er is geen pictogram voor de gebruikers-service onderdelen](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**Er zijn geen onderdelen die door de gebruiker kan worden onderhouden** |Doe geen toegang tenzij het goed is getraind. |
| ![Pictogram Lees instructies](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**Lees eerst alle instructies** | |
| ![Pictogram Tip-risico](./media/storsimple-hardware-component-replacement/TipHazard.png) |**Tip-risico** | |

### <a name="before-you-begin"></a>Voordat u begint

Raadpleeg de veiligheids informatie over uw apparaat en veiligheids pictogrammen die in deze zelf studie worden gebruikt. Ga naar [een veilig installatie en gebruik van uw StorSimple-apparaat](storsimple-8000-safety.md) voor volledige informatie. Controleer de [veiligheids maatregelen](storsimple-8000-safety.md#handling-precautions) voordat u uw StorSimple-apparaat verwerkt.

Houd rekening met de volgende informatie voordat u een onderdeel probeert te vervangen.

![Waarschuwing pictogram Waarschuwing voor ](./media/storsimple-hardware-component-replacement/Warning.png) ![ elektrisch schokken ](./media/storsimple-hardware-component-replacement/Electric.png) **!**

* U werkt goed door middel van een elektrostatische afvoer of antistatice passe-out bij het afhandelen van modules en onderdelen van uw StorSimple-apparaat.
* Geen circuits aanraken. Gebruik de opgegeven ingangen en hand leidingen bij het afhandelen van onderdelen die mogelijk een circuit hebben belicht.

![Waarschuwings pictogram meldings ](./media/storsimple-hardware-component-replacement/Warning.png) ![ pictogram ](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **:**

Wanneer u een module vervangt, **laat u nooit een lege Bay achter de behuizing**. Verkrijg een vervangings-of lege module voordat u het probleem onderdeel verwijdert.

## <a name="hardware-component-replacement-procedures"></a>Vervangings procedures voor hardwarecomponenten

Het StorSimple-apparaat van de 8000-serie bestaat uit verschillende invoeg modules in de primaire en/of EBOD-behuizingen. De 8100 heeft één primaire behuizing, terwijl 8600 een Dual Enclosure-apparaat is met een primaire behuizing en een EBOD behuizing.

De belangrijkste hardwareonderdelen op het apparaat worden in de volgende tabellen samenvatten. Klik op de koppeling in de kolom **vervangings procedure** om naar de bijbehorende zelf studie te gaan.

| Onderdelen | # Aanwezig | Module voor invoeg toepassingen? | Vervangings procedure |
|:--- |:--- |:--- |:--- |
| Chassis |1 |No |[Het chassis op uw StorSimple-apparaat vervangen](storsimple-8000-chassis-replacement.md) |
| Primaire controllers |2 |Yes |[Een controller module op uw StorSimple-apparaat vervangen](storsimple-8000-controller-replacement.md) |
| 764We voeding en koeling modules (PCMs) |2 |Yes |[Stroom- en koelmodule van StorSimple-apparaat vervangen](storsimple-8000-power-cooling-module-replacement.md) |
| Back-upbatterij |2 |Yes |[Noodaccumodule van StorSimple-apparaat vervangen](storsimple-8000-battery-replacement.md) |
| Schijfstations |12 |Yes |[Een schijf station op uw StorSimple-apparaat vervangen](storsimple-8000-disk-drive-replacement.md) |

**Tabel 1** Hardwareonderdelen in de primaire behuizing

De primaire behuizing en de EBOD-behuizing verschillen in hun I/O-modules. Daarnaast hebben de PCMs verschillende vermogen. De PCMs in de primaire behuizing zijn 764 W, terwijl deze in de EBOD-behuizing 580 W zijn. De PCMs in de primaire behuizing bevat ook een back-upbatterij module.

| Onderdelen | # Aanwezig | Module voor invoeg toepassingen? | Vervangings procedure |
|:--- |:--- |:--- |:--- |
| Chassis |1 |No |[Het chassis op uw StorSimple-apparaat vervangen](storsimple-8000-chassis-replacement.md) |
| EBOD-controllers |2 |Yes |[Een EBOD-controller op uw StorSimple-apparaat vervangen](storsimple-8000-ebod-controller-replacement.md) |
| 580We voeding en koeling modules (PCMs) |2 |Yes |[Stroom- en koelmodule van StorSimple-apparaat vervangen](storsimple-8000-power-cooling-module-replacement.md) |
| Schijfstations |12 |Yes |[Een schijf station op uw StorSimple-apparaat vervangen](storsimple-8000-disk-drive-replacement.md) |

**Tabel 2** Hardwareonderdelen in de EBOD-behuizing

De invoeg modules op het apparaat zijn gemarkeerd in de volgende voor-en achterzijde-diagrammen. U kunt deze diagrammen gebruiken om de locatie van de verschillende invoeg modules te bepalen als een vervanging vereist is. In het voorste diagram ziet u de schijf stations en de achteruitkijk diagrammen van de EBOD Enclosure en de primaire behuizing tonen de invoeg modules.

![Voor paneel van een apparaat met schijf stations](./media/storsimple-hardware-component-replacement/IC741028.png)

**Afbeelding 1** Voor zijde van het apparaat

| Label | Description |
|:--- |:--- |
| 0 - 11 |Schijf stations (in totaal 12) |

Zowel de primaire behuizing als de EBOD-behuizing hebben transport modules voor het station. Het chassis is twaalf 3,5 ' schijf stations gerangschikt in een 3-4-indeling.

![Backplane van modules voor primaire behuizing van apparaten](./media/storsimple-hardware-component-replacement/IC740994.png)

**Afbeelding 2** Terug van de primaire behuizing

| Label | Beschrijving |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Controller 0 |
| 4 |Controller 1 |

![Backplane van invoeg modules voor EBOD behuizing van apparaten](./media/storsimple-hardware-component-replacement/IC769599.png)

**Afbeelding 3** Terug van de EBOD-behuizing

| Label | Beschrijving |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |EBOD-controller 0 |
| 4 |EBOD-controller 1 |

## <a name="field-replaceable-units"></a>Vervangende eenheden van het veld

De volgende FRUs (replaceable Unit) zijn beschikbaar voor uw StorSimple-apparaat:

* Chassis (inclusief het paneel voor geïntegreerde bewerkingen)
* 764 W AC PCM
* 580 W AC PCM
* Harde schijf station met transport module voor schijven
* Controller module
* EBOD-controller module
* Module back-upbatterij
* Rail montagekit voor rack montage

Neem [contact op met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md) om een van deze vervangings eenheden te best Ellen.

## <a name="next-steps"></a>Volgende stappen

Controleer alle [veiligheids informatie](storsimple-8000-safety.md) voordat u een StorSimple-hardwareonderdeel gaat vervangen.
