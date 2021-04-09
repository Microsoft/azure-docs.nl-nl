---
title: Externe verbinding maken met een Azure Service Fabric-cluster knooppunt
description: Meer informatie over hoe u extern verbinding maakt met een exemplaar van een schaalset (een Service Fabric cluster knooppunt).
ms.topic: conceptual
ms.date: 03/23/2018
ms.openlocfilehash: 98d573af4fc2026134e75d4caf24a09e57e52c87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96012541"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Externe verbinding maken met een virtuele-machine Scale set-exemplaar of een cluster knooppunt
In een Service Fabric cluster dat in azure wordt uitgevoerd, stelt elk type cluster knooppunt dat u definieert [een afzonderlijke schaal in voor een virtuele machine](service-fabric-cluster-nodetypes.md).  U kunt externe verbinding maken met specifieke instanties van de schaalset (cluster knooppunten).  In tegens telling tot Vm's met één exemplaar hebben instanties van een schaalset niet hun eigen virtuele IP-adressen. Dit kan lastig zijn wanneer u zoekt naar een IP-adres en poort die u kunt gebruiken om extern verbinding te maken met een specifiek exemplaar.

Voer de volgende stappen uit om een IP-adres en poort te vinden die u kunt gebruiken om extern verbinding te maken met een specifiek exemplaar.

1. De binnenkomende NAT-regels voor Remote Desktop Protocol (RDP) ophalen.

    Normaal gesp roken heeft elk knooppunt type dat in uw cluster is gedefinieerd, een eigen virtueel IP-adres en een toegewezen load balancer. Standaard wordt de load balancer voor een knooppunt type met de volgende indeling genoemd: *lb-{cluster name}-{Node-type}*; bijvoorbeeld *lb-mycluster-front-end*. 
    
    Selecteer op de pagina voor uw Load Balancer in azure Portal **instellingen**  >  **binnenkomende NAT-regels**: 

    ![Scherm opname van een load balancer pagina in de Azure Portal. Selecteer in het menu links onder instellingen de optie binnenkomende NAT-regels.](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    De volgende scherm afbeelding toont de binnenkomende NAT-regels voor een knooppunt type met de naam front-end: 

    ![Scherm afbeelding met de binnenkomende NAT-regels voor een load balancer. De naam, de IP-versie, de bestemming, het doel en de service worden voor elke regel weer gegeven.](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    Voor elk knoop punt wordt het IP-adres weer gegeven in de **doel** kolom, de **doel** kolom bevat het exemplaar van de schaalset en de kolom **service** geeft het poort nummer. Voor externe verbinding worden poorten toegewezen aan elk knoop punt in oplopende volg orde, beginnend met poort 3389.

    U kunt ook de binnenkomende NAT-regels vinden in de `Microsoft.Network/loadBalancers` sectie van de Resource Manager-sjabloon voor uw cluster.
    
2. Als u de poort toewijzing van de binnenkomende poort voor een knoop punt wilt bevestigen, klikt u op de bijbehorende regel en kijkt u naar de waarde van de **doel poort** . De volgende scherm afbeelding toont de binnenkomende NAT-regel voor het front **-End-knoop punt (instance 1)** in de vorige stap. U ziet dat, hoewel het (inkomend) poort nummer 3390 is, de doel poort wordt toegewezen aan poort 3389, de poort voor de RDP-service op het doel.  

    ![Doel poort toewijzing](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    Voor Windows-clusters is de doel poort standaard poort 3389, die wordt toegewezen aan de RDP-service op het doel knooppunt. Voor Linux-clusters is poort 22 de doel poort, die wordt toegewezen aan de Secure shell-service (SSH).

3. Extern verbinding maken met het specifieke knoop punt (scale set-exemplaar). U kunt de gebruikers naam en het wacht woord gebruiken die u hebt ingesteld tijdens het maken van het cluster of andere referenties die u hebt geconfigureerd. 

    De volgende scherm afbeelding toont het gebruik van Verbinding met extern bureaublad om verbinding te maken met het front **-End (exemplaar 1)-** knoop punt in een Windows-cluster:
    
    ![Verbinding met extern bureaublad](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    Op Linux-knoop punten kunt u verbinding maken met SSH (in het volgende voor beeld worden hetzelfde IP-adres en dezelfde poort opnieuw gebruikt voor de boog):

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


Lees de volgende artikelen voor de volgende stappen:
* Zie het [overzicht van de functie overal implementeren en een vergelijking met door Azure beheerde clusters](service-fabric-deploy-anywhere.md).
* Meer informatie over [cluster beveiliging](service-fabric-cluster-security.md).
* [De waarden voor het RDP-poort bereik bijwerken na de](./scripts/service-fabric-powershell-change-rdp-port-range.md) implementatie van cluster-vm's
* [De gebruikers naam en het wacht woord van de beheerder](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) voor cluster-vm's wijzigen

