---
title: Volume containers voor StorSimple 8000 Series-apparaten beheren
description: In dit artikel wordt uitgelegd hoe u de pagina StorSimple Apparaatbeheer service volume containers kunt gebruiken om een volume container toe te voegen, te wijzigen of te verwijderen.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 6f0a0fd48e3717d14a714e42c5566cd7bcf090d5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100545283"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>Gebruik de StorSimple-apparaatbeheerfunctie om StorSimple-volumecontainers te beheren

## <a name="overview"></a>Overzicht
In deze zelf studie wordt uitgelegd hoe u de StorSimple Apparaatbeheer-service kunt gebruiken om StorSimple-volume containers te maken en te beheren.

Een volume container in een Microsoft Azure StorSimple-apparaat bevat een of meer volumes die de instellingen van het opslag account, versleuteling en band breedte delen. Een apparaat kan meerdere volume containers voor alle volumes hebben. 

Een volume container heeft de volgende kenmerken:

* **Volumes** : de gelaagde of lokaal vastgemaakte StorSimple-volumes die deel uitmaken van de volume container. 
* **Versleuteling** : een versleutelings sleutel die voor elke volume container kan worden gedefinieerd. Deze sleutel wordt gebruikt voor het versleutelen van de gegevens die van uw StorSimple-apparaat naar de cloud worden verzonden. Er wordt een militaire AES-256-bits sleutel gebruikt met de door de gebruiker ingevoerde sleutel. Als u uw gegevens wilt beveiligen, raden we u aan om de versleuteling van Cloud opslag altijd in te scha kelen.
* **Opslag account** : het Azure-opslag account dat wordt gebruikt voor het opslaan van de gegevens. Alle volumes in een volume container delen dit opslag account. U kunt een opslag account kiezen uit een bestaande lijst of een nieuw account maken wanneer u de volume container maakt en vervolgens de toegangs referenties voor dat account opgeeft.
* **Cloud bandbreedte** : de band breedte die wordt gebruikt door het apparaat wanneer de gegevens van het apparaat naar de cloud worden verzonden. U kunt een besturings element voor de band breedte afdwingen door een waarde tussen 1 Mbps en 1.000 Mbps op te geven wanneer u deze container maakt. Als u wilt dat het apparaat alle beschik bare band breedte verbruikt, stelt u dit veld in op **onbeperkt**. U kunt ook een bandbreedte sjabloon maken en Toep assen om band breedte op basis van schema toe te wijzen.

In de volgende procedures wordt uitgelegd hoe u de Blade StorSimple- **volume containers** kunt gebruiken om de volgende algemene bewerkingen uit te voeren:

* Een volume container toevoegen
* Een volume container wijzigen
* Een volume container verwijderen

## <a name="add-a-volume-container"></a>Een volume container toevoegen
Voer de volgende stappen uit om een volume container toe te voegen.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>Een volume container wijzigen
Voer de volgende stappen uit om een volume container te wijzigen.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Een volume container verwijderen
Er zijn volumes in een volume container. Deze kan alleen worden verwijderd als alle volumes in het bestand eerst worden verwijderd. Voer de volgende stappen uit om een volume container te verwijderen.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [beheren van StorSimple-volumes](storsimple-8000-manage-volumes-u2.md). 
* Meer informatie over [het gebruik van de StorSimple Apparaatbeheer-service voor het beheren van uw StorSimple-apparaat](storsimple-8000-manager-service-administration.md).

