---
title: Chassis vervangen in StorSimple Series-apparaat van 8000 | Microsoft Docs
description: Hierin wordt beschreven hoe u het chassis voor uw StorSimple primaire behuizing of EBOD Enclosure kunt verwijderen en vervangen.
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
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: de0f6299f35f9d76fdade976bf70456426e5ec51
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85513436"
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>Het chassis op uw StorSimple-apparaat vervangen
## <a name="overview"></a>Overzicht
In deze zelf studie wordt uitgelegd hoe u een chassis in een StorSimple 8000 Series-apparaat kunt verwijderen en vervangen. Het StorSimple 8100-model is een apparaat met één behuizing (één chassis), terwijl het 8600 een apparaat met dubbele behuizing (twee chassis) is. Voor een 8600-model zijn er mogelijk twee chassis die kan mislukken op het apparaat: het chassis voor de primaire behuizing of het chassis voor de EBOD behuizing.

In beide gevallen is het vervangende chassis dat door micro soft wordt geleverd, leeg. Er worden geen voedings-en koel modules (PCMs), controller modules, Solid State Disk-schijven (Ssd's), vasteschijfstations (Hdd's) of EBOD modules opgenomen.

> [!IMPORTANT]
> Voordat u het chassis verwijdert en vervangt, raadpleegt u de veiligheids informatie in [StorSimple vervanging](storsimple-8000-hardware-component-replacement.md)van het hardware-onderdeel.


## <a name="remove-the-chassis"></a>Het chassis verwijderen
Voer de volgende stappen uit om het chassis op uw StorSimple-apparaat te verwijderen.

#### <a name="to-remove-a-chassis"></a>Een chassis verwijderen
1. Zorg ervoor dat het StorSimple-apparaat is afgesloten en de verbinding met alle energie bronnen is verbroken.
2. Verwijder alle netwerk-en SAS-kabels, indien van toepassing.
3. Verwijder de eenheid uit het rek.
4. Verwijder alle stations en noteer de sleuven van waaruit ze worden verwijderd. Zie [het schijf station verwijderen](storsimple-8000-disk-drive-replacement.md#remove-the-disk-drive)voor meer informatie.
5. Verwijder de EBOD-controller modules in de EBOD-behuizing (als dit het chassis is dat is mislukt). Zie [een EBOD-controller verwijderen](storsimple-8000-ebod-controller-replacement.md#remove-an-ebod-controller)voor meer informatie.
   
    Verwijder de controllers op de primaire behuizing (als dit het chassis mislukt) en noteer de sleuven van waaruit ze worden verwijderd. Zie [een controller verwijderen](storsimple-8000-controller-replacement.md#remove-a-controller)voor meer informatie.

## <a name="install-the-chassis"></a>Het chassis installeren
Voer de volgende stappen uit om het chassis op uw StorSimple-apparaat te installeren.

#### <a name="to-install-a-chassis"></a>Een chassis installeren
1. Koppel het chassis in het rek. Zie voor meer informatie [rek-koppel uw StorSimple 8100-apparaat](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) of [rack-koppel uw StorSimple 8600-apparaat](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).
2. Nadat het chassis is gekoppeld in het rek, installeert u de controller modules op dezelfde posities als waarop ze eerder zijn geïnstalleerd.
3. Installeer de stations op dezelfde posities en sleuven als waarop ze eerder zijn geïnstalleerd.
   
   > [!NOTE]
   > We raden u aan eerst de Ssd's in de sleuven te installeren en vervolgens de Hdd's te installeren.
  
4. Wanneer het apparaat is gekoppeld aan het rek en de onderdelen zijn geïnstalleerd, verbindt u uw apparaat met de juiste energie bronnen en schakelt u het apparaat in. Zie [uw StorSimple 8100-apparaat aansluiten](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) op uw [StorSimple 8600-apparaat](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [vervangen van StorSimple](storsimple-8000-hardware-component-replacement.md).

