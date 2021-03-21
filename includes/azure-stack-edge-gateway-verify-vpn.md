---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: efa9e996cbfbc8954c294b4da8900b1d5bcbeeed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96466643"
---
Als u VPN wilt controleren, kunt u een opslag account maken dat alleen toegankelijk is voor het virtuele netwerk dat u hebt gemaakt. Volg deze stappen om dit opslag account te maken en te koppelen aan het virtuele netwerk dat u hebt gemaakt:

1. Een opslagaccount maken. U kunt het opslag account gebruiken dat u wilt gebruiken met uw Azure Stack edge-apparaat. Probeer toegang te krijgen tot het opslag account vanuit een extern netwerk (buiten het geselecteerde netwerk). Het opslag account moet toegankelijk zijn.
2. Ga in het Azure Portal naar het opslag account. 
3. Ga naar **firewalls en virtuele netwerken**. Kies in het rechterdeel venster voor **toegang toestaan van** **geselecteerde netwerken**. Kies in het **beveiligde onze opslag account met virtuele netwerken** **+ bestaand virtueel netwerk toevoegen.**

    ![VPN 1 controleren](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-1.png)

4. Op de Blade **netwerken toevoegen** :

    - Selecteer het abonnement. Dit is hetzelfde abonnement dat is gekoppeld aan uw Azure Stack EDGE/Data Box Gateway resource. 
    - Kies in de vervolg keuzelijst het virtuele netwerk dat u wilt koppelen aan dit opslag account. Selecteer het virtuele netwerk dat u in de vorige stap hebt gemaakt.
    - Kies in **subnetten** de **_standaard_* _ en de _GatewaySubnet *. De service-eind punten worden ingeschakeld voor deze combi Naties van virtuele netwerken en subnetten. Het inschakelen van toegang duurt Maxi maal 15 minuten.
    - Selecteer **Inschakelen**.

    ![VPN 2 controleren](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-2.png)
    
4. **Instellingen** opslaan.

    ![VPN 3 controleren](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-3.png)

5. Nadat de instellingen zijn opgeslagen, ziet u de subnetten waarvoor het virtuele netwerk is ingeschakeld.

    ![VPN 4 verifiëren](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-4.png)

5. Voer de volgende stappen uit om te controleren of de gegevens nu alleen via VPN worden uitgevoerd: 
    - Probeer toegang te krijgen tot het opslag account vanuit een extern netwerk (buiten het geselecteerde netwerk). Het opslag account is niet toegankelijk. 
    - Probeer toegang te krijgen tot het opslag account van het virtuele netwerk/subnetten dat u hebt ingeschakeld in geselecteerde netwerken. Het opslag account moet toegankelijk zijn. 
 
U hebt alleen toegang tot dit opslag account als de VPN-verbinding is ingeschakeld. Als u VPN uitschakelt, moet u ook de netwerk instellingen van het opslag account aanpassen. 

Ga voor meer informatie naar [Azure Storage firewalls en virtuele netwerken configureren](../articles/storage/common/storage-network-security.md). 

