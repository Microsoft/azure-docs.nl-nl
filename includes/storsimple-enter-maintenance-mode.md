---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d35f0ef783a2c48f8211657bc8829635c19495aa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67176062"
---
#### <a name="to-enter-maintenance-mode"></a>Onderhouds modus activeren
1. Kies in het menu van de seriële console optie 1, **Meld u aan met volledige toegang**.
2. Typ het wacht woord. Het standaardapparaatwachtwoord is **Wachtwoord1**.
3. Typ bij de opdracht prompt
   
     `Enter-HcsMaintenanceMode`
4. Er wordt een waarschuwings bericht weer gegeven met de melding dat de onderhouds modus alle I/O-aanvragen verstoort en de verbinding met de klassieke Azure-Portal verkrijgt en u wordt gevraagd om te bevestigen. Typ **Y** om de onderhouds modus in te voeren.
   
    Beide controllers worden opnieuw opgestart. Wanneer het opnieuw opstarten is voltooid, wordt er nog een bericht weer gegeven dat aangeeft dat het apparaat zich in de onderhouds modus bevindt.

