---
title: Overzicht van de implementatie van Azure Firewall Manager
description: Meer informatie over de implementatiestappen op hoog niveau die vereist zijn voor Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: ceb6e84b31067f7289b9e003a4fb273ce717de33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079095"
---
# <a name="azure-firewall-manager-deployment-overview"></a>Overzicht van de implementatie van Azure Firewall Manager

U kunt Azure Firewall Manager om meerdere manieren implementeren, maar het volgende algemene proces verdient aanbeveling.

## <a name="general-deployment-process"></a>Algemeen implementatieproces

### <a name="hub-virtual-networks"></a>Virtuele netwerken van hub

1.  Een firewallbeleid maken

    - Een nieuw beleid maken
<br>*or*<br>
    - Een basisbeleid afleiden en een lokaal beleid aanpassen
<br>*or*<br>
    - Regels importeren vanuit een bestaande Azure-firewall. Verwijder de NAT-regels uit beleidsregels die moeten worden toegepast op meerdere firewalls
1. Een hub en spoke-architectuur maken
   - Maak een virtueel hubnetwerk met behulp van Azure Firewall Manager en verbindt er twee virtuele spoke-netwerken aan met behulp van peering van virtuele netwerken
<br>*or*<br>
    - Maak een virtueel netwerk en voeg er virtuele netwerkverbindingen aan toe; verbindt er virtuele spoke-netwerken aan met behulp van peering van virtuele netwerken

3. Selecteer beveiligingsproviders en koppel een firewallbeleid. Momenteel is alleen Azure Firewall een ondersteunde provider.

   - Dit wordt gedaan tijdens het maken van een virtueel hubnetwerk
<br>*or*<br>
    - Converteer een bestaand virtueel netwerk naar een virtueel hubnetwerk. Het is ook mogelijk om meerdere virtuele netwerken te converteren.

4. Configureer door gebruiker gedefinieerde routes om verkeer naar de firewall van uw virtuele hubnetwerk om te leiden.


### <a name="secured-virtual-hubs"></a>Beveiligde virtuele hubs

1. Een hub en spoke-architectuur maken

   - Maak een beveiligde virtuele hub met behulp van Azure Firewall Manager en voeg virtuele netwerkverbindingen toe.<br>*or*<br>
   - Maak een virtuele WAN-hub en voeg virtuele netwerkverbindingen toe.
2. Beveiligingsproviders selecteren

   - Uitgevoerd tijdens het maken van een beveiligde virtuele hub.<br>*or*<br>
   - Converteer een bestaande virtuele WAN-hub naar een beveiligde virtuele hub.
3. Firewallbeleid maken en dit koppelen aan uw hub

   - Alleen van toepassing als Azure Firewall wordt gebruikt.
   - SECaaS-beleidsregels (Security as a Service) van derden worden geconfigureerd via de beheerervaring van partners.
4. Routerinstellingen configureren om verkeer om te leiden naar uw beveiligde hub

   - Leid met behulp van de pagina voor het instellen van routes voor beveiligde virtuele hubs verkeer eenvoudig om naar uw beveiligde hub om het te filteren en in een logboek vast te leggen zonder UDR's (door de gebruiker gedefinieerde routes) in virtuele spoke-netwerken.

> [!NOTE]
> - U mag niet meer dan één hub per virtuele WAN per regio hebben. Maar u kunt wel meerdere virtuele WAN's in de regio toevoegen om dit voor elkaar te krijgen.
> - U mag geen overlappende IP-ruimten voor hubs in een vWAN hebben.
> - De VNet-verbindingen voor uw hub moeten zich in dezelfde regio bevinden als de hub.
>
> Zie voor meer bekende problemen [Wat is Azure Firewall Manager?](overview.md#known-issues)

## <a name="convert-virtual-networks"></a>Virtuele netwerken converteren

De volgende informatie is van toepassing als u een bestaand virtueel netwerk converteert naar een virtueel netwerk van de hub:

- Als het virtuele netwerk een bestaande Azure-firewall heeft, selecteert u een firewallbeleid om aan de bestaande firewall te koppelen. De inrichtingsstatus van de firewall wordt bijgewerkt terwijl het firewallbeleid firewallregels vervangt. Tijdens de inrichtingsstatus blijft de firewall verkeer verwerken en treedt er geen downtime op. U kunt bestaande regels in een firewallbeleid importeren met Firewall Manager of Azure PowerShell.
- Als het virtuele netwerk geen gekoppelde Azure-firewall heeft, wordt er een firewall geïmplementeerd en word het firewallbeleid aan de nieuwe firewall gekoppeld.

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: uw cloudnetwerk beveiligen met Azure Firewall Manager via de Azure-portal](secure-cloud-network.md)