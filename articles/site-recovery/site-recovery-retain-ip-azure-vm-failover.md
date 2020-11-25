---
title: IP-adressen na failover van Azure VM met Azure Site Recovery blijven gebruiken
description: Hierin wordt beschreven hoe u IP-adressen kunt behouden bij het mislukken van Azure-Vm's voor nood herstel naar een secundaire regio met Azure Site Recovery
ms.service: site-recovery
ms.date: 4/9/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 650fb7f0877a98ef53ed3868550f9c084ecb5885
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023547"
---
# <a name="retain-ip-addresses-during-failover"></a>IP-adressen behouden tijdens een failover

[Azure site Recovery](site-recovery-overview.md) kunt herstel na nood gevallen voor Azure-vm's uitvoeren door vm's te repliceren naar een andere Azure-regio, als er een failover optreedt als er zich een storing voordoet en de primaire regio niet meer wordt weer gegeven wanneer de items weer normaal zijn.

Tijdens de failover kunt u de IP-adres Sering in de doel regio op dezelfde manier blijven als de bron regio:

- Wanneer u herstel na nood gevallen voor virtuele Azure-machines inschakelt, maakt Site Recovery doel resources op basis van bron bron instellingen. Voor virtuele Azure-machines die zijn geconfigureerd met statische IP-adressen, probeert Site Recovery hetzelfde IP-adres in te richten voor de doel-VM als deze niet in gebruik is. [Lees dit artikel](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms)voor een volledige uitleg over de wijze waarop site Recovery het afhandelen van adressen.
- Voor eenvoudige toepassingen is de standaard configuratie voldoende. Voor complexere apps moet u mogelijk extra resources inrichten om ervoor te zorgen dat de connectiviteit werkt zoals verwacht na een failover.


Dit artikel bevat enkele voor beelden voor het bewaren van IP-adressen in complexere voorbeeld scenario's. De voor beelden zijn:

- Failover voor een bedrijf met alle resources die worden uitgevoerd in azure
- Failover voor een bedrijf met een hybride implementatie en bronnen die zowel on-premises als in Azure worden uitgevoerd

## <a name="resources-in-azure-full-failover"></a>Resources in Azure: volledige failover

Bedrijf A heeft alle apps die worden uitgevoerd in Azure.

### <a name="before-failover"></a>Voor failover

Hier is de architectuur voor failover.

- Bedrijf A heeft identieke netwerken en subnetten in de bron-en doel-Azure-regio's.
- Om de beoogde herstel tijd (RTO) te reduceren, gebruikt het bedrijf replica knooppunten voor SQL Server altijd op domein controllers, enzovoort. Deze replica knooppunten bevinden zich in een ander VNet in de doel regio, zodat ze een VPN-site-naar-site-verbinding kunnen maken tussen de bron-en doel regio's. Dit is niet mogelijk als dezelfde IP-adres ruimte wordt gebruikt in de bron en het doel.  
- Voor failover is de netwerk architectuur als volgt:
    - Primaire regio is Azure Azië-oost
        - Azië-oost heeft een VNet (**Source vnet**) met adres ruimte 10.1.0.0/16.
        - Azië-oost heeft workloads verdeeld over drie subnetten in het VNet:
            - **Subnet 1**: 10.1.1.0/24
            - **Subnet 2**: 10.1.2.0/24
            - **Subnet 3**: 10.1.3.0/24
    - Secundaire regio (doel) is Azure Zuidoost-Azië
        - Zuidoost-Azië heeft een herstel-VNet (**herstel-vnet**) dat identiek is aan het **bron-vnet**.
        - Zuidoost-Azië heeft een extra VNet (**Azure vnet**) met adres ruimte 10.2.0.0/16.
        - **Azure VNet** bevat een subnet (**subnet 4**) met adres ruimte 10.2.4.0/24.
        - Replica knooppunten voor SQL Server always on, domein controller etc. bevinden zich in **subnet 4**.
    - **Bron-vnet** en **Azure VNet** zijn verbonden met een VPN-verbinding tussen sites.
    - Het **herstel VNet** is niet verbonden met een ander virtueel netwerk.
    - **Bedrijf A** wijst/VERIFIEERT IP-doel adressen voor gerepliceerde items. Het doel-IP is hetzelfde als het bron-IP-adres voor elke virtuele machine.

![Resources in azure vóór een volledige failover](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Na een failover

Als er sprake is van een regionale storing in de bron, kan bedrijf A een failover uitvoeren voor alle resources naar de doel regio.

- Als de doel-IP-adressen al aanwezig zijn vóór de failover, kan bedrijf A de failover organiseren en automatisch verbindingen tot stand brengen na een failover tussen het **herstel VNet** en **Azure VNet**. Dit wordt geïllustreerd in het volgende diagram...
- Afhankelijk van de app-vereisten, kunnen verbindingen tussen de twee VNets (**Recovery VNet** en **Azure VNet**) in de doel regio worden ingesteld vóór, tijdens (als een tussen stap) of na de failover.
  - Het bedrijf kan [herstel plannen](site-recovery-create-recovery-plans.md) gebruiken om op te geven wanneer verbindingen tot stand worden gebracht.
  - Ze kunnen verbinding maken tussen de VNets met behulp van VNet-peering of site-naar-site-VPN.
      - Bij VNET-peering wordt geen VPN-gateway gebruikt en er gelden diverse beperkingen voor.
      - De [prijzen](https://azure.microsoft.com/pricing/details/virtual-network) voor vnet-peering worden anders berekend dan het vnet-naar-vnet VPN gateway [prijzen](https://azure.microsoft.com/pricing/details/vpn-gateway). Voor failovers wordt in het algemeen geadviseerd om dezelfde connectiviteits methode te gebruiken als bron netwerken, met inbegrip van het verbindings type, om onvoorspelbare netwerk incidenten te minimaliseren.

    ![Resources in volledige Azure-failover](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Resources in Azure: failover van een geïsoleerde app

Mogelijk moet u een failover uitvoeren op het niveau van de app. Bijvoorbeeld om een failover uit te laten zien van een specifieke app of app-laag die zich in een toegewijd subnet bevindt.

- In dit scenario is het niet raadzaam om IP-adres Sering te behouden, omdat de kans op inconsistenties van connectiviteit wordt verg root. U verliest ook de subnet connectiviteit met andere subnetten binnen hetzelfde Azure-VNet.
- Een betere manier om failover op subnetniveau uit te voeren, is door gebruik te maken van verschillende doel-IP-adressen voor failover (als u verbinding nodig hebt met andere subnetten op bron-VNet) of als u elke app wilt isoleren in een eigen toegewezen VNet in de bron regio. Met deze laatste benadering kunt u verbinding maken tussen netwerken in de bron regio en hetzelfde gedrag emuleren wanneer u een failover naar de doel regio maakt.  

In dit voor beeld plaatst bedrijf A apps in de bron regio in toegewezen VNets en wordt er verbinding gemaakt tussen die VNets. Met dit ontwerp kunnen ze geïsoleerde app-failover uitvoeren en de privé-IP-adressen van de bron bewaren in het doel netwerk.

### <a name="before-failover"></a>Voor failover

Voor failover is de architectuur als volgt:

- Toepassings-Vm's worden gehost in de regio van de primaire Azure-Azië-oost:
    - **App1** Vm's bevinden zich in VNet- **bron-vnet 1**: 10.1.0.0/16.
    - **App2** Vm's bevinden zich in VNet- **bron-vnet 2**: 10.2.0.0/16.
    - Het **bron-VNet 1** heeft twee subnetten.
    - De **bron-VNet 2** heeft twee subnetten.
- Secundaire regio (doel) is Azure Zuidoost-Azië-Zuidoost-Azië bevat een herstel VNets (**herstel vnet 1** en **herstel-vnet 2**) die identiek zijn aan de **bron-vnet 1** en de **bron-vnet 2**.
        - **De herstel-vnet 1** en het **herstel-vnet 2** hebben elk twee subnetten die overeenkomen met de subnetten in de **bron-vnet 1** en het **bron-vnet 2** -Zuidoost-Azië heeft een extra VNet (**Azure vnet**) met adres ruimte 10.3.0.0/16.
        - **Azure VNet** bevat een subnet (**subnet 4**) met adres ruimte 10.3.4.0/24.
        -Replica knooppunten voor SQL Server always on, domein controller etc. bevinden zich in **subnet 4**.
- Er zijn een aantal site-naar-site-VPN-verbindingen: 
    - **Bron-vnet 1** en **Azure VNet**
    - **Bron-vnet 2** en **Azure VNet**
    - **Bron-vnet 1** en **bron-vnet 2** zijn verbonden met VPN site-naar-site
- **Herstel vnet 1** en **herstel-vnet 2** zijn niet verbonden met een andere VNets.
- **Bedrijf A** configureert VPN-gateways op **herstel Vnet 1** en **Recovery vnet 2** om RTO te verminderen.  
- De **herstel-VNet1** en de **herstel VNet2** zijn niet verbonden met een ander virtueel netwerk.
- Om de beoogde herstel tijd (RTO) te verminderen, worden VPN-gateways geconfigureerd op **Recovery VNet1** en **Recovery VNet2** vóór failover.

    ![Resources in azure vóór een failover van de app](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>Na een failover

In het geval van een storing of probleem die van invloed is op één app (in * * source VNet 2 in ons voor beeld), kan bedrijf A de betreffende app als volgt herstellen:


- Verbreek de verbinding tussen VPN-verbindingen tussen de **bron-VNet1** en de **bron VNet2** en tussen de **bron VNet2** en **Azure VNet** .
- VPN-verbindingen tot stand brengen tussen de **bron-VNet1** en de **herstel VNet2** en tussen **herstel VNet2** en **Azure VNet**.
- Failover van virtuele machines in **bron VNet2** naar **Recovery VNet2**.

![Resources in azure app failover](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- Dit voor beeld kan worden uitgebreid om meer toepassingen en netwerk verbindingen te bevatten. De aanbeveling is om zoveel mogelijk een soortgelijk verbindings model te volgen bij een failover van bron naar doel.
- VPN-gateways maken gebruik van open bare IP-adressen en gateway-hops om verbindingen tot stand te brengen. Als u geen open bare IP-adressen wilt gebruiken of als u extra hops wilt voor komen, kunt u [Azure VNet-peering](../virtual-network/virtual-network-peering-overview.md) gebruiken om virtuele netwerken te peeren over [ondersteunde Azure-regio's](../virtual-network/virtual-network-manage-peering.md#cross-region).

## <a name="hybrid-resources-full-failover"></a>Hybride bronnen: volledige failover

In dit scenario voert **bedrijf B** een hybride onderneming uit met een deel van de toepassings infrastructuur die op Azure wordt uitgevoerd en de rest die on-premises wordt uitgevoerd. 

### <a name="before-failover"></a>Voor failover

Hier ziet u hoe de netwerk architectuur eruitziet voor failover.

- Toepassings-Vm's worden gehost in azure Azië-oost.
- Azië-oost heeft een VNet (**Source vnet**) met adres ruimte 10.1.0.0/16.
  - Azië-oost heeft werk belastingen verdeeld over drie subnetten in het **bron-VNet**:
    - **Subnet 1**: 10.1.1.0/24
    - **Subnet 2**: 10.1.2.0/24
    - **Subnet 3**: 10.1.3.0/24, waarbij gebruik wordt gemaakt van een virtueel Azure-netwerk met adres ruimte 10.1.0.0/16. Dit virtuele netwerk heet **VNet-bron**
      - De secundaire regio (doel) is Azure Zuidoost-Azië:
  - Zuidoost-Azië heeft een herstel-VNet (**herstel-vnet**) dat identiek is aan het **bron-vnet**.
- Vm's in Azië-oost zijn verbonden met een on-premises Data Center met Azure ExpressRoute of site-naar-site-VPN.
- Om RTO te verminderen, worden in de Azure Zuidoost-Azië gateways voor het herstel VNet ingericht voor de failover.
- Bedrijf B wijst/controleert doel-IP-adressen voor gerepliceerde Vm's. Het doel-IP-adres is hetzelfde als het bron-IP-adres voor elke virtuele machine.


![Connectiviteit van on-premises naar Azure vóór failover](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Na een failover


Als er sprake is van een regionale storing in de bron, kan bedrijf B failover van alle resources naar de doel regio uitvoeren.

- Als de doel-IP-adressen al aanwezig zijn vóór de failover, kan bedrijf B de failover organiseren en automatisch verbindingen tot stand brengen na een failover tussen het **herstel VNet** en **Azure VNet**.
- Afhankelijk van de app-vereisten, kunnen verbindingen tussen de twee VNets (**Recovery VNet** en **Azure VNet**) in de doel regio worden ingesteld vóór, tijdens (als een tussen stap) of na de failover. Het bedrijf kan [herstel plannen](site-recovery-create-recovery-plans.md) gebruiken om op te geven wanneer verbindingen tot stand worden gebracht.
- De oorspronkelijke verbinding tussen Azure Azië-oost en het on-premises Data Center moet worden verbroken voordat u verbinding tot stand brengt tussen Azure Zuidoost-Azië en on-premises Data Center.
- De on-premises route ring wordt opnieuw geconfigureerd zodat deze verwijst naar de doel regio en de gateways na de failover.

![Connectiviteit van on-premises naar Azure na een failover](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>Hybride bronnen: failover van een geïsoleerde app

Bedrijf B kan geen failover uitvoeren voor geïsoleerde apps op subnetniveau. Dit komt doordat de adres ruimte op de bron-en herstel VNets hetzelfde is en dat de oorspronkelijke bron naar een on-premises verbinding actief is.

 - Voor app-tolerantie bedrijf B moet elke app in een eigen toegewezen Azure-VNet worden geplaatst.
 - Bij elke app in een afzonderlijk VNet kan bedrijf B een failover uitvoeren voor geïsoleerde apps en bron verbindingen naar de doel regio routeren.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [herstel plannen](site-recovery-create-recovery-plans.md).
