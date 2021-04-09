---
title: Een StorSimple 8600 EBOD-controller vervangen | Microsoft Docs
description: Hierin wordt uitgelegd hoe u een of beide EBOD-controllers op een StorSimple 8600-apparaat kunt verwijderen en vervangen.
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
ms.openlocfilehash: bdafb4288bf818928aba43d09f15d4dd75092b24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014836"
---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Een EBOD-controller op uw StorSimple-apparaat vervangen

## <a name="overview"></a>Overzicht
In deze zelf studie wordt uitgelegd hoe u een defecte EBOD-controller module op uw Microsoft Azure StorSimple apparaat vervangt. Als u een EBOD-controller module wilt vervangen, moet u het volgende doen:

* De defecte EBOD-controller verwijderen
* Een nieuwe EBOD-controller installeren

Houd rekening met de volgende informatie voordat u begint:

* Lege EBOD-modules moeten in alle ongebruikte sleuven worden geplaatst. De behuizing wordt niet goed afkoelen als er een sleuf geopend is.
* De EBOD-controller is hot-swappable en kan worden verwijderd of vervangen. Verwijder een defecte module pas nadat u een vervanging hebt uitgevoerd. Wanneer u het vervangings proces initieert, moet u het binnen tien minuten volt ooien.

> [!IMPORTANT]
> Voordat u een StorSimple-onderdeel probeert te verwijderen of te vervangen, controleert u of u de [veiligheids pictogrammen conventies](storsimple-8000-safety.md#safety-icon-conventions) en andere [veiligheids maatregelen](storsimple-8000-safety.md)bekijkt.

## <a name="remove-an-ebod-controller"></a>Een EBOD-controller verwijderen
Voordat u de defecte EBOD-controller module in uw StorSimple-apparaat vervangt, moet u ervoor zorgen dat de andere EBOD-controller module actief is en wordt uitgevoerd. In de volgende procedure en tabel wordt uitgelegd hoe u de EBOD-controller module verwijdert.

#### <a name="to-remove-an-ebod-module"></a>Een EBOD-module verwijderen
1. Open Azure Portal.
2. Ga naar het apparaat en navigeer naar **instellingen**  >  **Hardware Health** en controleer of de status van de LED voor de actieve EBOD-controller module groen is en of de LED voor de defecte EBOD-controller module rood is.
3. Zoek de module mislukte EBOD-controller op de achterkant van het apparaat.
4. Verwijder de kabels die de EBOD-controller module verbindt met de controller voordat u de EBOD-module uit het systeem neemt.
5. Noteer de exacte SAS-poort van de EBOD-controller module die is verbonden met de controller. U moet het systeem naar deze configuratie herstellen nadat u de EBOD-module hebt vervangen.
   
   > [!NOTE]
   > Dit is normaal gesp roken poort A, die als host wordt aangeduid **in** het volgende diagram.
   
    ![Backplane van EBOD controller](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **Afbeelding 1** Terug van de EBOD-module
   
   | Label | Beschrijving |
   |:--- |:--- |
   | 1 |LED voor fout |
   | 2 |LED voor stroom |
   | 3 |SAS-connectors |
   | 4 |SAS-Led's |
   | 5 |Seriële poorten voor alleen fabrieks gebruik |
   | 6 |Poort A (host in) |
   | 7 |Poort B (hosten) |
   | 8 |Poort C (alleen voor gebruik in de fabriek) |

## <a name="install-a-new-ebod-controller"></a>Een nieuwe EBOD-controller installeren
In de volgende procedure en tabel wordt uitgelegd hoe u een EBOD-controller module installeert in uw StorSimple-apparaat.

#### <a name="to-install-an-ebod-controller"></a>Een EBOD-controller installeren
1. Controleer het EBOD-apparaat op beschadiging, met name voor de Interface connector. Installeer de nieuwe EBOD-controller niet als de pincodes zijn gebogen.
2. Verplaats met de hendels in de open positie de module naar de behuizing totdat de hendels zijn ingeschakeld.
   
    ![De EBOD-controller installeren](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **Afbeelding 2**  De EBOD-controller module installeren
3. Sluit de vergren deling. U moet een klik horen wanneer de vergren deling wordt ingeschakeld.
   
    ![EBOD-vergren deling vrijgeven](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **Afbeelding 3**  De vergren deling van de EBOD-module sluiten
4. Verbind de kabels opnieuw. Gebruik de exacte configuratie die aanwezig was vóór de vervanging. Zie het volgende diagram en de tabel voor meer informatie over hoe u de kabels verbindt.
   
    ![Uw 4U-apparaat bekabelen voor energie beheer](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **Afbeelding 4**. Kabels opnieuw verbinden
   
   | Label | Beschrijving |
   |:--- |:--- |
   | 1 |Primaire behuizing |
   | 2 |PCM 0 |
   | 3 |PCM 1 |
   | 4 |Controller 0 |
   | 5 |Controller 1 |
   | 6 |EBOD-controller 0 |
   | 7 |EBOD-controller 1 |
   | 8 |EBOD-behuizing |
   | 9 |Energie distributie-eenheden |

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [vervangen van StorSimple](storsimple-8000-hardware-component-replacement.md).

