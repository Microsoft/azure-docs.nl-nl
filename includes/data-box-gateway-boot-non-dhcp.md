---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: db4e27c0972a93d870d0a6565f1bd3212146df62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582163"
---
Als u opstart in een niet-DHCP-omgeving, voert u de volgende stappen uit om de virtuele machine te implementeren voor uw Data Box Gateway.

1. [Maak verbinding met de Windows Power shell-interface van het apparaat](#connect-to-the-powershell-interface).
2. Gebruik de `Get-HcsIpAddress` cmdlet om de netwerk interfaces weer te geven die zijn ingeschakeld op het virtuele apparaat. Als voor uw apparaat één netwerkinterface is ingeschakeld, wordt `Ethernet` als standaardnaam aan deze interface toegewezen.

    In het volgende voor beeld ziet u het gebruik van deze cmdlet:

    ```
    [10.100.10.10]: PS>Get-HcsIpAddress

    OperationalStatus : Up
    Name              : Ethernet
    UseDhcp           : True
    IpAddress         : 10.100.10.10
    Gateway           : 10.100.10.1
    ```

3. Gebruik de cmdlet `Set-HcsIpAddress` om het netwerk te configureren. Zie het volgende voorbeeld:

    ```
    Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1
    ```

