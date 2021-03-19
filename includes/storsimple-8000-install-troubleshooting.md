---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: ebe112103bc3eb30239e80095db9bb91a33bebf3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67176230"
---
## <a name="troubleshooting-update-failures"></a>Problemen oplossen met mislukte updates
**Wat moet ik doen als ik een melding zie dat de controles vóór de upgrade zijn mislukt?**

Als een controle vooraf mislukt, bekijkt u de gedetailleerde meldingsbalk onder aan de pagina. Hierin kunt u zien welke controle vooraf is mislukt. U ontvangt bijvoorbeeld een melding dat de status controle van de controller en het hardware-onderdeel is mislukt. Ga naar **Monitor > hardware Health**. U moet ervoor zorgen dat beide controllers in orde zijn en online zijn. U moet er ook voor zorgen dat alle hardware-onderdelen in het StorSimple-apparaat in orde zijn in deze Blade. U kunt daarna proberen de updates te installeren. Als het niet lukt om de problemen met de hardwareonderdelen op te lossen, neemt u contact op met Microsoft-ondersteuning voor de volgende stappen.

**Wat moet ik doen als ik de foutmelding "Kan geen updates installeren" zie en de aanbeveling krijg de gids voor het oplossen van problemen met updates te raadplegen om de oorzaak van het probleem vast te stellen?**

Een waarschijnlijke oorzaak hiervan is dat u geen verbinding hebt met de Microsoft Update-servers. Dit is een handmatige controle die moet worden uitgevoerd. Als u geen verbinding meer hebt met de updateserver, mislukt de updatetaak. U kunt de verbinding controleren door de volgende cmdlet uit te voeren vanuit de Windows PowerShell-interface van uw StorSimple-apparaat:

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

Voer de cmdlet uit op beide controllers.

Als blijkt dat u wel verbinding hebt en het probleem blijft bestaan, neemt u contact op met Microsoft-ondersteuning voor de volgende stappen.

**Wat moet ik doen als ik een updatefout krijg tijdens het bijwerken van mijn apparaat naar Update 4 en op beide controllers Update 4 wordt uitgevoerd?**

Vanaf Update 4 gaan de controllers niet in de herstelmodus als op beide controllers dezelfde softwareversie wordt uitgevoerd en er een updatefout optreedt. Deze situatie kan zich voordoen als de hotfix voor de apparaatsoftware (update met eerste prioriteit) op beide domeincontrollers wordt uitgevoerd maar andere hotfixes (tweede en derde prioriteit) nog moeten worden toegepast. Vanaf Update 4 gaan de controllers alleen in de herstelmodus als op de twee controllers verschillende softwareversies worden uitgevoerd. 

Als u een updatefout krijgt en op beide controllers Update 4 wordt uitgevoerd, raden wij u aan een paar minuten te wachten en daarna de update opnieuw uit te voeren. Als de nieuwe poging ook mislukt, neemt u contact op met Microsoft-ondersteuning.
