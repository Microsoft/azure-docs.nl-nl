---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8c60e0275853f3c879db22f5414f0fbbbdb47b85
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86050258"
---
## <a name="preparing-for-updates"></a>Updates voorbereiden
U moet de volgende stappen uitvoeren voordat u de Update scant en toepast:

1. Maak een Cloud momentopname van de gegevens van het apparaat.
2. Zorg ervoor dat de vaste IP-adressen van de controller routeerbaar zijn en verbinding kunnen maken met internet. Deze vaste IP-adressen worden gebruikt om updates voor uw apparaat te onderhouden. U kunt dit testen door de volgende cmdlet uit te voeren op elke controller vanuit de Windows Power shell-interface van het apparaat:
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network>`
   
    **Voorbeeld uitvoer voor Test-Connection als vaste IP-adressen kunnen verbinding maken met Internet**

    ```output
    Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com

    Source      Destination     IPV4Address      IPV6Address
    ----------------- -----------  -----------
    HCSNODE0  bing.com        204.79.197.200
    HCSNODE0  bing.com        204.79.197.200
    HCSNODE0  bing.com        204.79.197.200
    HCSNODE0  bing.com        204.79.197.200

    Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

    Source      Destination       IPV4Address    IPV6Address
    ----------------- -----------  -----------
    HCSNODE0  204.79.197.200  204.79.197.200
    HCSNODE0  204.79.197.200  204.79.197.200
    HCSNODE0  204.79.197.200  204.79.197.200
    HCSNODE0  204.79.197.200  204.79.197.200
    ```

Nadat u deze hand matige controle vooraf hebt voltooid, kunt u door gaan met het scannen en installeren van de updates.

