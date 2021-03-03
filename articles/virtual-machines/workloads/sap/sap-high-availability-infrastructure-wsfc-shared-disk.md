---
title: Azure-infra structuur voor SAP ASCS/SCS met WSFC&-gedeelde schijf | Microsoft Docs
description: Meer informatie over het voorbereiden van de Azure-infra structuur voor SAP HA met behulp van een Windows-failovercluster en gedeelde schijf voor een SAP ASCS/SCS-exemplaar.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4218b4c00b79d78965eaf6e73028e63f52b1ff17
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673628"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>De Azure-infra structuur voor SAP HA voorbereiden met behulp van een Windows-failovercluster en een gedeelde schijf voor SAP ASCS/SCS

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f


[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Multi-SID-configuratie met hoge Beschik baarheid van SAP)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows OS][Logo_Windows] Windows


In dit artikel worden de stappen beschreven die u moet uitvoeren om de Azure-infra structuur voor te bereiden voor het installeren en configureren van een High-Availability SAP ASCS/SCS-exemplaar op een Windows-failovercluster met behulp van een *gedeelde cluster schijf* als optie voor het clusteren van een SAP ASCS-exemplaar.
In de documentatie worden twee alternatieven voor *gedeelde cluster schijven* weer gegeven:

- [Gedeelde Azure-schijven](../../disks-shared.md)
- Met de [Data keeper-cluster versie van Sios](https://us.sios.com/products/datakeeper-cluster/) gespiegelde opslag maken, waarmee geclusterde gedeelde schijf wordt gesimuleerd 

De gepresenteerde configuratie is afhankelijk van [Azure proximity placement groups (PPG)](./sap-proximity-placement-scenarios.md) om een optimale netwerk LATENTIE voor SAP-workloads te krijgen. De documentatie heeft geen betrekking op de laag van de data base.  

> [!NOTE]
> Voor Azure proximity placement groups gelden de vereisten voor het gebruik van een gedeelde Azure-schijf.
 

## <a name="prerequisites"></a>Vereisten

Lees dit artikel voordat u met de installatie begint:

* [Architectuur handleiding: een SAP ASCS/SCS-exemplaar op een Windows-failovercluster clusteren met behulp van een gedeelde cluster schijf][sap-high-availability-guide-wsfc-shared-disk]

## <a name="create-the-ascs-vms"></a>De ASCS Vm's maken

Voor SAP ASCS/SCS-cluster implementeren twee virtuele machines in de Beschikbaarheidsset van Azure. Implementeer de virtuele machines in dezelfde plaatsings groep voor nabijheid. Zodra de Vm's zijn geïmplementeerd:  
- Azure Internal Load Balancer maken voor het SAP ASCS/SCS-exemplaar 
- Windows-Vm's toevoegen aan het AD-domein

De namen van de hosts en de IP-adressen voor het weer gegeven scenario zijn:

| Host-naam functie | Hostnaam | Statisch IP-adres | Beschikbaarheidsset | Proximity-plaatsings groep |
| --- | --- | --- |---| ---|
| ASCS/SCS-cluster van 1e cluster node |PR1-ascs-10 |10.0.0.4 |PR1-ascs-avset |PR1PPG |
| 2e cluster knooppunt ASCS/SCS-cluster |PR1-ascs-11 |10.0.0.5 |PR1-ascs-avset |PR1PPG |
| Naam van het clusternetwerk | pr1clust |10.0.0.42 (**alleen** voor het Win 2016-cluster) | n.v.t. | n.v.t. |
| ASCS-cluster netwerk naam | pr1-ascscl |10.0.0.43 | n.v.t. | n.v.t. |
| ERS-cluster netwerk naam (**alleen** voor ERS2) | pr1-erscl |10.0.0.44 | n.v.t. | n.v.t. |


## <a name="create-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Interne Azure-load balancer maken

SAP ASCS, SAP SCS en de nieuwe SAP ERS2, gebruiken virtuele hostnamen en virtuele IP-adressen. In Azure is een [Load Balancer](../../../load-balancer/load-balancer-overview.md) vereist voor het gebruik van een virtueel IP-adres. Het is raadzaam om [standaard Load Balancer](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md)te gebruiken. 

> [!IMPORTANT]
> Zwevend IP wordt niet ondersteund voor een secundaire IP-configuratie in een NIC in scenario's voor taak verdeling. Zie beperkingen voor [Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations)voor meer informatie. Als u een extra IP-adres voor de virtuele machine nodig hebt, implementeert u een tweede NIC.    


De volgende lijst bevat de configuratie van de (A) SCS/ERS-load balancer. De configuratie voor zowel SAP ASCS als ERS2 wordt uitgevoerd in dezelfde Azure-load balancer.  

**Één SCS**
- Front-end configuratie
    - Statisch ASCS/SCS IP-adres **10.0.0.43**
- Back-end-configuratie  
    Voeg alle virtuele machines toe die deel moeten uitmaken van het (A) SCS/ERS-cluster. In dit voor beeld Vm's **PR1-ascs-10** en **PR1-ascs-11**.
- Test poort
    - Poort 620 **Nr** . de standaard optie voor protocol (TCP), interval (5), beschadigde drempel waarde (2)
- Taakverdelingsregels
    - Als u Standard Load Balancer gebruikt, selecteert u HA-poorten
    - Als u basis Load Balancer gebruikt, maakt u regels voor taak verdeling voor de volgende poorten
        - 32 **nr** TCP
        - 36 **nr** TCP
        - 39 **nr** TCP
        - 81 **nr** TCP
        - 5 **nr** 13 TCP
        - 5 **nr** 14 TCP
        - 5 **nr** 16 TCP

    - Zorg ervoor dat time-out voor inactiviteit (minuten) is ingesteld op de maximum waarde 30 en dat zwevend IP-adres (Direct Server Return) is ingeschakeld.

**ERS2**

Als de wachtrij voor het plaatsen van replicatie Server 2 (ERS2) ook is geclusterd, moet het virtuele IP-adres van ERS2 ook worden geconfigureerd op Azure ILB naast het bovenstaande SAP ASCS/SCS IP. Deze sectie is alleen van toepassing als u de architectuur voor het plaatsen van replicatie Server 2 gebruikt.  
- 2e front-end-configuratie
    - Statisch SAP ERS2 IP-adres **10.0.0.44**

- Back-end-configuratie  
  De virtuele machines zijn al toegevoegd aan de back-ILB.  

- 2e test poort
    - Poort 621 **Nr**  
    De standaard optie voor protocol (TCP), interval (5), beschadigde drempel waarde (2) behouden

- 2e regels voor taak verdeling
    - Als u Standard Load Balancer gebruikt, selecteert u HA-poorten
    - Als u basis Load Balancer gebruikt, maakt u regels voor taak verdeling voor de volgende poorten
        - 32 **nr** TCP
        - 33 **nr** TCP
        - 5 **nr** 13 TCP
        - 5 **nr** 14 TCP
        - 5 **nr** 16 TCP

    - Zorg ervoor dat time-out voor inactiviteit (minuten) is ingesteld op de maximum waarde 30 en dat zwevend IP-adres (Direct Server Return) is ingeschakeld.


> [!TIP]
> Met de [Azure Resource Manager sjabloon voor WSFC voor SAP ASCS/SCS-exemplaar met een gedeelde Azure-schijf](https://github.com/robotechredmond/301-shared-disk-sap)kunt u de infrastructuur voorbereiding automatiseren met behulp van een gedeelde Azure-schijf voor een SAP-sid met ERS1.  
> Met de Azure ARM-sjabloon maakt u twee Windows 2019-of 2016-Vm's, maakt u een gedeelde Azure-schijf en koppelt u deze aan de Vm's. Er wordt ook een intern Load Balancer van Azure gemaakt en geconfigureerd. Zie de ARM-sjabloon voor meer informatie. 

## <a name="add-registry-entries-on-both-cluster-nodes-of-the-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Voeg register vermeldingen toe aan cluster knooppunten van het ASCS/SCS-exemplaar

Azure Load Balancer kunnen verbindingen worden gesloten als de verbindingen gedurende een periode inactief zijn en de time-out voor inactiviteit overschrijden. De SAP-werk processen openen verbindingen met het SAP-bewerkings proces zodra de eerste aanvraag voor het in de wachtrij plaatsen/verwijderen moet worden verzonden. Om te voor komen dat deze verbindingen worden onderbroken, wijzigt u de TCP/IP-waarden voor KeepAliveTime en KeepAliveInterval op beide cluster knooppunten. Als u ERS1 gebruikt, is het ook nodig om SAP-profiel parameters toe te voegen, zoals verderop in dit artikel wordt beschreven.
De volgende Register vermeldingen moeten op beide cluster knooppunten worden gewijzigd:

- KeepAliveTime
- KeepAliveInterval

| Pad| Naam van de variabele | Type variabele  | Waarde | Documentatie |
| --- | --- | --- |---| ---|
| HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |KeepAliveTime |REG_DWORD (decimaal) |120000 |[KeepAliveTime](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)) |
| HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |KeepAliveInterval |REG_DWORD (decimaal) |120000 |[KeepAliveInterval](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) |


Start beide cluster knooppunten opnieuw op om de wijzigingen toe te passen.
  
## <a name="add-the-windows-vms-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> De Windows-Vm's toevoegen aan het domein
Nadat u vaste IP-adressen aan de virtuele machines hebt toegewezen, voegt u de virtuele machines toe aan het domein. 

## <a name="install-and-configure--windows-failover-cluster"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Windows-failovercluster installeren en configureren 

### <a name="install-the-windows-failover-cluster-feature"></a>De functie Windows-failovercluster installeren

Voer deze opdracht uit op een van de cluster knooppunten:

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # Install Windows features.
    # After the feature installs, manually reboot both nodes
    Invoke-Command $ClusterNodes {Install-WindowsFeature Failover-Clustering, FS-FileServer -IncludeAllSubFeature -IncludeManagementTools }
   ```

Zodra de installatie van de functie is voltooid, start u beide cluster knooppunten opnieuw op.  

### <a name="test-and-configure-windows-failover-cluster"></a>Windows-failovercluster testen en configureren 

In Windows 2019 herkent het cluster automatisch dat het wordt uitgevoerd in Azure en als standaard optie voor cluster beheer IP, wordt gebruikgemaakt van de gedistribueerde netwerk naam. Daarom worden de lokale IP-adressen van cluster knooppunten gebruikt. Als gevolg hiervan is er geen specifieke (virtuele) netwerk naam voor het cluster nodig en hoeft u dit IP-adres niet te configureren op de interne Azure-Load Balancer.

Zie voor meer informatie [Windows Server 2019 failover clustering nieuwe functies](https://techcommunity.microsoft.com/t5/failover-clustering/windows-server-2019-failover-clustering-new-features/ba-p/544029) deze opdracht uitvoeren op een van de cluster knooppunten:

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # IP adress for cluster network name is needed ONLY on Windows Server 2016 cluster
    $ClusterStaticIPAddress = "10.0.0.42"
        
    # Test cluster
    Test-Cluster –Node $ClusterNodes -Verbose
    
    $ComputerInfo = Get-ComputerInfo
    
    $WindowsVersion = $ComputerInfo.WindowsProductName
    
    if($WindowsVersion -eq "Windows Server 2019 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2019 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes -Verbose
    }elseif($WindowsVersion -eq "Windows Server 2016 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2016 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes –StaticAddress $ClusterStaticIPAddress -Verbose 
    }else{
        Write-Error "Not supported Windows version!"
    }
   ```

### <a name="configure-cluster-cloud-quorum"></a>Het cluster Cloud quorum configureren
Als u Windows Server 2016 of 2019 gebruikt, wordt u aangeraden [Azure-cloudwitness](/windows-server/failover-clustering/deploy-cloud-witness)als cluster quorum te configureren.

Voer deze opdracht uit op een van de cluster knooppunten:

   ```powershell
    $AzureStorageAccountName = "cloudquorumwitness"
    Set-ClusterQuorum –CloudWitness –AccountName $AzureStorageAccountName -AccessKey <YourAzureStorageAccessKey> -Verbose
   ```

### <a name="tuning-the-windows-failover-cluster-thresholds"></a>De drempel waarden voor het Windows-failovercluster afstemmen
 
Nadat u het Windows-failovercluster hebt geïnstalleerd, moet u enkele drempel waarden aanpassen, zodat deze geschikt zijn voor clusters die zijn geïmplementeerd in Azure. De para meters die moeten worden gewijzigd, worden beschreven in de [drempel waarden voor het afstemmen van failover cluster netwerk](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834). Ervan uitgaande dat uw twee virtuele machines waaruit de configuratie van het Windows-cluster wordt opgebouwd voor ASCS/SCS zich in hetzelfde subnet bevinden, wijzigt u de volgende para meters in deze waarden:
- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RoutingHistoryLength = 30

Deze instellingen zijn getest met klanten en bieden een goede inbreuk. Ze zijn robuust genoeg, maar ze bieden ook failover die snel genoeg is voor echte fout situaties in SAP-workloads of een VM-fout.  

## <a name="configure-azure-shared-disk"></a>Gedeelde Azure-schijf configureren
Deze sectie is alleen van toepassing als u gebruikmaakt van een gedeelde Azure-schijf. 

### <a name="create-and-attach-azure-shared-disk-with-powershell"></a>Een gedeelde Azure-schijf maken en koppelen met Power shell
Voer deze opdracht uit op een van de cluster knooppunten. U moet de waarden aanpassen voor uw resource groep, Azure-regio, SAPSID, enzovoort.  

   ```powershell
    #############################
    # Create Azure Shared Disk
    #############################
    
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyAzureRegion"
    $SAPSID = "PR1"
    
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    
    # With parameter '-MaxSharesCount', we define the maximum number of cluster nodes to attach the shared disk
    $NumberOfWindowsClusterNodes = 2
            
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "$SAPSID-ascs-10"
    
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "$SAPSID-ascs-11"
    
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```

### <a name="format-the-shared-disk-with-powershell"></a>De gedeelde schijf Format teren met Power shell
1. Haal het schijf nummer op. Voer deze Power shell-opdrachten uit op een van de cluster knooppunten:

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 2      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. Format teer de schijf. In dit voor beeld is het schijf nummer 2. 

   ```powershell
    # Format SAP ASCS Disk number '2', with drive letter 'S'
    $SAPSID = "PR1"
    $DiskNumber = 2
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR1SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. Controleer of de schijf nu als een cluster schijf wordt weer gegeven.  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : 88ff1d94-0cf1-4c70-89ae-cbbb2826a484
    # Name       : Cluster Disk 1
    # Number     : 2
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk2\Partition2\}
   ```
4. Registreer de schijf in het cluster.  
   ```powershell
    # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output     
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 1 Online Available Storage Physical Disk
   ```

## <a name="sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> SIOS data keeper cluster Edition voor de SAP ASCS/SCS-cluster share schijf
Deze sectie is alleen van toepassing als u gebruikmaakt van de software van derden SIOS data keeper cluster Edition voor het maken van een gespiegelde opslag die de gedeelde cluster schijf simuleert.  

Nu hebt u een werkende Windows Server failover clustering-configuratie in Azure. Als u een SAP ASCS/SCS-exemplaar wilt installeren, hebt u een gedeelde schijf resource nodig. Een van de opties is het gebruik van SIOS data keeper cluster Edition is een oplossing van derden die u kunt gebruiken om gedeelde schijf bronnen te maken.  

U moet de volgende taken uitvoeren om de data keeper-cluster versie van SIOS te installeren voor de SAP ASCS/SCS-cluster share disk:
- Voeg Microsoft .NET Framework toe, indien nodig. Zie de [SIOS Documentation] (( https://us.sios.com/products/datakeeper-cluster/) voor de meest recente .NET Framework-vereisten 
- SIOS data keeper installeren
- SIOS data keeper configureren

### <a name="install-sios-datakeeper"></a>SIOS data keeper installeren
Installeer de data keeper-cluster versie van SIOS op elk knoop punt in het cluster. Als u virtuele gedeelde opslag met SIOS data keeper wilt maken, maakt u een gesynchroniseerde mirror en simuleert u vervolgens gedeelde cluster opslag.

Voordat u de SIOS-software installeert, maakt u de domein gebruiker DataKeeperSvc.

> [!NOTE]
> Voeg de domein gebruiker DataKeeperSvc toe aan de lokale groep Administrators op beide cluster knooppunten.
>

1. Installeer de SIOS-software op beide cluster knooppunten.

   ![SIOS-installatie programma][sap-ha-guide-figure-3030]

   ![Afbeelding 31: eerste pagina van de data keeper-installatie van SIOS][sap-ha-guide-figure-3031]

   _Eerste pagina van de data keeper-installatie van SIOS_

2. Selecteer **Ja** in het dialoog venster.

   ![Afbeelding 32: data keeper informeert dat een service wordt uitgeschakeld][sap-ha-guide-figure-3032]

   _Data keeper informeert u dat een service wordt uitgeschakeld_

3. In het dialoog venster wordt u aangeraden domein- **of Server account** te selecteren.

   ![Afbeelding 33: selectie van de gebruiker voor SIOS data keeper][sap-ha-guide-figure-3033]

   _Gebruikers selectie voor SIOS data keeper_

4. Voer de gebruikers naam en het wacht woord voor het domein account in dat u hebt gemaakt voor SIOS data keeper.

   ![Afbeelding 34: Voer de gebruikers naam en het wacht woord van het domein in voor de data keeper-installatie van SIOS][sap-ha-guide-figure-3034]

   _Voer de gebruikers naam en het wacht woord van het domein in voor de data keeper-installatie van SIOS_

5. Installeer de licentie sleutel voor uw SIOS data keeper-exemplaar, zoals wordt weer gegeven in afbeelding 35.

   ![Afbeelding 35: Voer uw data keeper-licentie sleutel voor SIOS in][sap-ha-guide-figure-3035]

   _Voer uw data keeper-licentie sleutel voor SIOS in_

6. Start de virtuele machine opnieuw op wanneer dit wordt gevraagd.

### <a name="configure-sios-datakeeper"></a>SIOS data keeper configureren
Nadat u SIOS data keeper op beide knoop punten hebt geïnstalleerd, start u de configuratie. Het doel van de configuratie is het synchroon repliceren van gegevens tussen de extra schijven die aan de virtuele machines zijn gekoppeld.

1. Start het hulp programma data keeper beheer en configuratie en selecteer vervolgens **verbinding maken met server**.

   ![Afbeelding 36: data keeper-beheer en configuratie hulpprogramma voor SIOS][sap-ha-guide-figure-3036]

   _Data keeper-beheer en configuratie hulpprogramma voor SIOS_

2. Voer de naam of het TCP/IP-adres in van het eerste knoop punt waarvan het beheer-en configuratie hulpprogramma verbinding moet maken en, in een tweede stap, het tweede knoop punt.

   ![Afbeelding 37: Voer de naam of het TCP/IP-adres in van het eerste knoop punt waarvan het beheer-en configuratie hulpprogramma verbinding moet maken, en in een tweede stap is het tweede knoop punt][sap-ha-guide-figure-3037]

   _Voeg de naam of het TCP/IP-adres in van het eerste knoop punt waarmee het beheer-en configuratie hulpprogramma verbinding moet maken, en in een tweede stap het tweede knoop punt_

3. Maak de replicatie taak tussen de twee knoop punten.

   ![Afbeelding 38: een replicatie taak maken][sap-ha-guide-figure-3038]

   _Een replicatie taak maken_

   Een wizard leidt u door het proces van het maken van een replicatie taak.

4. Definieer de naam van de replicatie taak.

   ![Afbeelding 39: de naam van de replicatie taak definiëren][sap-ha-guide-figure-3039]

   _De naam van de replicatie taak definiëren_

   ![Afbeelding 40: de basis gegevens definiëren voor het knoop punt, dat het huidige bron knooppunt moet zijn][sap-ha-guide-figure-3040]

   _Definieer de basis gegevens voor het knoop punt. dit moet het huidige bron knooppunt zijn_

5. Definieer de naam, het TCP/IP-adres en het schijf volume van het doel knooppunt.

   ![Afbeelding 41: de naam, het TCP/IP-adres en het schijf volume van het huidige doel knooppunt definiëren][sap-ha-guide-figure-3041]

   _Definieer de naam, het TCP/IP-adres en het schijf volume van het huidige doel knooppunt_

6. Definieer de compressie algoritmen. In ons voor beeld raden we u aan de replicatie stroom te comprimeren. Met name bij hersynchronisaties situaties vermindert de compressie van de replicatie stroom aanzienlijk minder hersynchronisaties tijd. Compressie maakt gebruik van de CPU-en RAM-bronnen van een virtuele machine. Naarmate de compressie frequentie toeneemt, neemt het volume van de CPU-resources die worden gebruikt, dus toe. U kunt deze instelling later aanpassen.

7. Een andere instelling die u moet controleren, is of de replicatie asynchroon of synchroon plaatsvindt. Wanneer u SAP ASCS/SCS-configuraties beveiligt, moet u synchrone replicatie gebruiken.  

   ![Afbeelding 42: replicatie Details definiëren][sap-ha-guide-figure-3042]

   _Replicatie Details definiëren_

8. Definiëren of het volume dat door de replicatie taak wordt gerepliceerd, moet worden weer gegeven als een configuratie voor een Windows Server-failovercluster als een gedeelde schijf. Voor de SAP-ASCS/SCS-configuratie selecteert u **Ja** zodat het gerepliceerde volume door het Windows-cluster wordt gezien als een gedeelde schijf die als een cluster volume kan worden gebruikt.

   ![Afbeelding 43: Selecteer Ja om het gerepliceerde volume als cluster volume in te stellen][sap-ha-guide-figure-3043]

   _Selecteer **Ja** om het gerepliceerde volume als cluster volume in te stellen_

   Nadat het volume is gemaakt, wordt in het data keeper-beheer en configuratie programma weer gegeven dat de replicatie taak actief is.

   ![Afbeelding 44: data keeper synchrone mirroring voor de SAP ASCS/SCS-share schijf is actief][sap-ha-guide-figure-3044]

   _Data keeper synchrone mirroring voor de SAP ASCS/SCS-share schijf is actief_

   Failoverclusterbeheer wordt nu de schijf weer gegeven als een Data keeper-schijf, zoals wordt weer gegeven in afbeelding 45:

   ![Afbeelding 45: Failoverclusterbeheer toont de schijf die data keeper is gerepliceerd][sap-ha-guide-figure-3045]

   _Failoverclusterbeheer toont de schijf die data keeper gerepliceerd_


## <a name="next-steps"></a>Volgende stappen

* [SAP NetWeaver HA installeren met behulp van een Windows-failovercluster en een gedeelde schijf voor een SAP ASCS/SCS-exemplaar][sap-high-availability-installation-wsfc-shared-disk]