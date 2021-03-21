---
title: Taak verdeling op meerdere IP-configuraties-Azure Portal
titleSuffix: Azure Load Balancer
description: In dit artikel vindt u informatie over de taak verdeling over de primaire en secundaire IP-configuraties met behulp van de Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: se0dec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 3d28946aad263af635a0139e68d424a77a1eab25
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103417821"
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Taak verdeling op meerdere IP-configuraties met behulp van de Azure Portal

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [CLI](load-balancer-multiple-ip-cli.md)

In dit artikel laten we u zien hoe u Azure Load Balancer kunt gebruiken met meerdere IP-adressen op een secundaire netwerk interface controller (NIC). In het volgende diagram ziet u het scenario:

![Load balancer-scenario](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

In ons scenario gebruiken we de volgende configuratie:

- Twee virtuele machines (Vm's) die Windows uitvoeren.
- Elke VM heeft een primaire en een secundaire NIC.
- Elke secundaire NIC heeft twee IP-configuraties.
- Elke VM fungeert als host voor twee websites: contoso.com en fabrikam.com.
- Elke website is gebonden aan een IP-configuratie op de secundaire NIC.
- Azure Load Balancer wordt gebruikt om twee front-end-IP-adressen beschikbaar te maken, één voor elke website. De front-end-adressen worden gebruikt voor het distribueren van verkeer naar de respectieve IP-configuratie voor elke website.
- Hetzelfde poort nummer wordt gebruikt voor de IP-adressen van de front-end-IP en de back-end-pool.

## <a name="prerequisites"></a>Vereisten

In ons scenario wordt ervan uitgegaan dat u een resource groep met de naam **contosofabrikam** hebt die als volgt is geconfigureerd:

- De resource groep bevat een virtueel netwerk met de naam **myVNet**.
- Het **myVNet** -netwerk bevat twee virtuele machines met de naam **VM1** en **VM2**.
- VM1 en VM2 bevinden zich in dezelfde beschikbaarheidsset met de naam **myAvailset**. 
- VM1 en VM2 hebben respectievelijk een primaire NIC met de naam **VM1NIC1** en **VM2NIC1**. 
- VM1 en VM2 hebben respectievelijk een secundaire NIC met de naam **VM1NIC2** en **VM2NIC2**.

Zie [een virtuele machine met meerdere Nic's maken met behulp van Power shell](../virtual-machines/windows/multiple-nics.md)voor meer informatie over het maken van vm's met meerdere nic's.

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>Taak verdeling uitvoeren op meerdere IP-configuraties

Voer de volgende stappen uit om het scenario te vervolledigen dat in dit artikel wordt beschreven.

### <a name="step-1-configure-the-secondary-nics"></a>Stap 1: de secundaire Nic's configureren

Voor elke virtuele machine in uw virtuele netwerk voegt u de IP-configuratie voor de secundaire NIC toe:  

1. Blader naar de Azure Portal: https://portal.azure.com . Meld u aan met uw Azure-account.

2. Selecteer in de linkerbovenhoek van het scherm het pictogram van de **resource groep** . Selecteer vervolgens de resource groep waar uw virtuele machines zich bevinden (bijvoorbeeld **contosofabrikam**). In het deel venster **resource groepen** worden alle resources en nic's voor de virtuele machines weer gegeven.

3. Voor de secundaire NIC van elke virtuele machine voegt u de IP-configuratie toe:

    1. Selecteer de secundaire NIC die u wilt configureren.
    
    2. Selecteer **IP-configuraties**. Selecteer in het volgende deel venster boven aan de bovenkant de optie **toevoegen**.

    3. Voeg onder **IP-configuraties toevoegen** een tweede IP-configuratie toe aan de NIC: 

        1. Voer een naam in voor de secundaire IP-configuratie. (Geef bijvoorbeeld voor VM1 en VM2 de IP-configuratie **VM1NIC2-ipconfig2** en **VM2NIC2-ipconfig2**.)

        2. Selecteer voor het **privé-IP-adres** **toewijzings** instelling **statisch**.

        3. Selecteer **OK**.

Nadat de tweede IP-configuratie voor de secundaire NIC is voltooid, wordt deze weer gegeven onder de instellingen voor de **IP-** configuratie voor de desbetreffende NIC.

### <a name="step-2-create-the-load-balancer"></a>Stap 2: De load balancer maken

Maak uw load balancer voor de configuratie:

1. Blader naar de Azure Portal: https://portal.azure.com . Meld u aan met uw Azure-account.

2. Selecteer in de linkerbovenhoek van het scherm **een resource maken**  >  **netwerk**  >  **Load Balancer**. Selecteer vervolgens **maken**.

3. Typ onder **Load Balancer maken** een naam voor uw Load Balancer. In dit scenario gebruiken we de naam **mylb**.

4. Maak onder **openbaar IP-adres** een nieuwe open bare IP met de naam **PublicIP1**.

5. Onder **resource groep** selecteert u de bestaande resource groep voor uw virtuele machines (bijvoorbeeld **contosofabrikam**). Selecteer de locatie waar u uw load balancer wilt implementeren en selecteer vervolgens **OK**.

De implementatie van de load balancer wordt gestart. Het kan een paar minuten duren voordat de implementatie is voltooid. Nadat de implementatie is voltooid, wordt de load balancer als resource weer gegeven in de resource groep.

### <a name="step-3-configure-the-front-end-ip-pool"></a>Stap 3: de front-end-IP-adres groep configureren

Configureer voor elke website (contoso.com en fabrikam.com) de front-end-IP-adres groep op uw load balancer:

1. Selecteer in de portal **meer services**. Typ **openbaar IP-adres** in het vak filteren en selecteer vervolgens **open bare IP-adressen**. Selecteer in het volgende deel venster boven aan de bovenkant de optie **toevoegen**.

2. Configureer twee open bare IP-adressen (**PublicIP1** en **PublicIP2**) voor beide websites (contoso.com en fabrikam.com):

   1. Typ een naam voor het IP-adres van de front-end.

   2. Voor **resource groep** selecteert u de bestaande resource groep voor uw virtuele machines (bijvoorbeeld **contosofabrikam**).

   3. Voor **locatie** selecteert u dezelfde locatie als de vm's.

   4. Selecteer **OK**.

      Nadat de open bare IP-adressen zijn gemaakt, worden ze weer gegeven onder de **open bare IP-** adressen.

3. <a name="step3-3"></a>Selecteer in de portal **meer services**. In het vak filter typt u **Load Balancer** en selecteert u vervolgens **Load Balancer**. 

4. Selecteer de load balancer (**mylb**) waaraan u de front-end-IP-groep wilt toevoegen.

5. Selecteer bij **instellingen** de optie Front **-end-IP-configuratie**. Selecteer in het volgende deel venster boven aan de bovenkant de optie **toevoegen**.

6. Typ een naam voor uw front-end-IP-adres (bijvoorbeeld **contosofe** of **fabrikamfe**).

7. <a name="step3-7"></a>Selecteer **IP-adres**. Selecteer onder **openbaar IP-adres kiezen** de IP-adressen voor uw front-end (**PublicIP1** of **PublicIP2**).

8. Maak het tweede front-end-IP-adres door <a href="#step3-3">stap 3</a> te herhalen in <a href="#step3-7">stap 7</a> in deze sectie.

Nadat de front-end-pool is geconfigureerd, worden de IP-adressen weer gegeven onder de **IP-configuratie-** instellingen van uw Load Balancer frontend. 
    
### <a name="step-4-configure-the-back-end-pool"></a>Stap 4: de back-end-pool configureren

Configureer voor elke website (contoso.com en fabrikam.com) de back-end-adres groep op uw load balancer:
        
1. Selecteer in de portal **meer services**. In het vak filter typt u **Load Balancer** en selecteert u vervolgens **Load Balancer**.

2. Selecteer de load balancer (**mylb**) waaraan u de back-end-pool wilt toevoegen.

3. Selecteer **back-Pools** onder **instellingen**. Typ een naam voor de back-end-pool (bijvoorbeeld **contosopool** of **fabrikampool**). Selecteer in het volgende deel venster boven aan de bovenkant de optie **toevoegen**. 

4. Selecteer voor **gekoppeld aan** de optie **beschikbaarheidsset**.

5. Voor **beschikbaarheidsset** selecteert u **myAvailset**.

6. Voeg de IP-configuraties van het doelnet netwerk voor beide Vm's toe: 

    ![Back-endservers voor load balancer configureren](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. Selecteer voor doel-VM de **virtuele machine** die u wilt toevoegen aan de back-end-pool (bijvoorbeeld **VM1** of **VM2**).

    2. Voor **netwerk-IP-configuratie** selecteert u de IP-configuratie van de secundaire NIC voor de virtuele machine die u in de vorige stap hebt geselecteerd (bijvoorbeeld **VM1NIC2-ipconfig2** of **VM2NIC2-ipconfig2**).

7. Selecteer **OK**.

Nadat de back-end-pool is geconfigureerd, worden de adressen weer gegeven onder de instellingen voor de **back-endadresgroep** van Load Balancer.

### <a name="step-5-configure-the-health-probe"></a>Stap 5: de status test configureren

Configureer een status test voor uw load balancer:

1. Selecteer in de portal **meer services**. In het vak filter typt u **Load Balancer** en selecteert u vervolgens **Load Balancer**.

2. Selecteer de load balancer (**mylb**) waaraan u de status test wilt toevoegen.

3. Onder **instellingen** selecteert u **status test**. Selecteer in het volgende deel venster boven aan de bovenkant de optie **toevoegen**. 

4. Typ een naam voor de status test (bijvoorbeeld **http**). Selecteer **OK**.

### <a name="step-6-configure-load-balancing-rules"></a>Stap 6: taakverdelings regels configureren

Configureer voor elke website (contoso.com en fabrikam.com) de taakverdelings regels:
    
1. <a name="step6-1"></a>Klik onder **instellingen** op **taakverdelings regels**. Selecteer in het volgende deel venster boven aan de bovenkant de optie **toevoegen**. 

2. Typ bij **naam** een naam voor de taakverdelings regel (bijvoorbeeld **HTTPc** voor contoso.com of **HTTPf** voor fabrikam.com).

3. Selecteer bij **frontend-IP-adres** het front-end-IP-adres dat u eerder hebt gemaakt (bijvoorbeeld **contosofe** of **fabrikamfe**).

4. Voor **poort** -en **backend-poort**, behoud de standaard waarde **80**.

5. Selecteer voor **zwevend IP (direct server return)** **uitgeschakeld**.

6. <a name="step6-6"></a>Selecteer **OK**.

7. Maak de tweede load balancer regel door <a href="#step6-1">stap 1</a> tot en met <a href="#step6-6">6</a> in deze sectie te herhalen.

Nadat de regels zijn geconfigureerd, worden ze weer gegeven onder de instellingen voor de load balancer taakverdelings **regels** .

### <a name="step-7-configure-dns-records"></a>Stap 7: DNS-records configureren

Als de laatste stap moet u uw DNS-bron records zo configureren dat deze verwijzen naar de respectieve front-end-IP-adressen voor uw load balancer. U kunt uw domeinen hosten in Azure DNS. Zie [Azure DNS gebruiken met andere Azure-Services](../dns/dns-for-azure-services.md)voor meer informatie over het gebruik van Azure DNS met Load Balancer.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het combi neren van Load Balancing-Services in azure [met behulp van taakverdelings Services in azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Meer informatie over het gebruik van verschillende typen logboeken voor het beheren van en het oplossen van problemen met load balancer in [Azure monitor logboeken voor Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
