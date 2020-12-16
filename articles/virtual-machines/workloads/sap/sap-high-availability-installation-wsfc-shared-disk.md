---
title: SAP NetWeaver HA installeren op een Windows-failovercluster en gedeelde schijf voor een SAP ASCS/SCS-exemplaar in azure | Microsoft Docs
description: Meer informatie over het installeren van SAP NetWeaver HA op een Windows-failovercluster en gedeelde schijf voor een SAP ASCS/SCS-exemplaar.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 81721cb52035a51cffa2667568914c780b4c397b
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588655"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>SAP NetWeaver HA installeren op een Windows-failovercluster en gedeelde schijf voor een SAP ASCS/SCS-exemplaar in azure

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

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
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md



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
[sap-ha-guide-figure-3046-ers2]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ers2-virtual-name-ip.png
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

In dit artikel wordt beschreven hoe u een SAP-systeem met hoge Beschik baarheid installeert en configureert in azure met behulp van een Windows Server-failovercluster en een gedeelde cluster schijf voor het clusteren van een SAP ASCS/SCS-exemplaar. Zoals beschreven in [architectuur gids: cluster a SAP ASCS/SCS-exemplaar op een Windows-failovercluster met behulp van een gedeelde cluster schijf][sap-high-availability-guide-wsfc-shared-disk], zijn er twee alternatieven voor *gedeelde cluster schijven*:

- [Gedeelde Azure-schijven](../../disks-shared.md)
- Met de [Data keeper-cluster versie van Sios](https://us.sios.com/products/datakeeper-cluster/) gespiegelde opslag maken, waarmee geclusterde gedeelde schijf wordt gesimuleerd 

## <a name="prerequisites"></a>Vereisten

Lees de volgende documenten voordat u met de installatie begint:

* [Architectuur handleiding: een SAP ASCS/SCS-exemplaar op een Windows-failovercluster clusteren met behulp van een gedeelde cluster schijf][sap-high-availability-guide-wsfc-shared-disk]

* [De Azure-infra structuur voor SAP HA voorbereiden met behulp van een Windows-failovercluster en een gedeelde schijf voor een SAP ASCS/SCS-exemplaar][sap-high-availability-infrastructure-wsfc-shared-disk]

De DBMS-instellingen in dit artikel worden niet beschreven, omdat de instellingen variëren afhankelijk van het DBMS-systeem dat u gebruikt. We gaan ervan uit dat hoge Beschik baarheid met betrekking tot het DBMS is gericht op de functionaliteit die verschillende DBMS-leveranciers ondersteunen voor Azure. Voor beelden zijn AlwaysOn of database spiegeling voor SQL Server en Oracle Data Guard voor Oracle-data bases. De scenario's met hoge Beschik baarheid voor de DBMS worden niet behandeld in dit artikel.

Er zijn geen speciale overwegingen wanneer verschillende DBMS-services communiceren met een geclusterde SAP-ASCS of SCS-configuratie in Azure.

> [!NOTE]
> De installatie procedures van SAP NetWeaver ABAP-systemen, Java-systemen en ABAP en Java-systemen zijn bijna identiek. Het belangrijkste verschil is dat een SAP ABAP-systeem één ASCS-exemplaar heeft. Het SAP Java-systeem heeft één SCS-exemplaar. Het SAP ABAP + Java-systeem heeft een ASCS-exemplaar en een SCS-exemplaar dat wordt uitgevoerd in dezelfde micro soft failover cluster-groep. Eventuele verschillen in de installatie van elke SAP NetWeaver-installatie stack worden expliciet genoemd. U kunt ervan uitgaan dat de overige stappen hetzelfde zijn.  

## <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>SAP installeren met een ASCS/SCS-exemplaar met hoge Beschik baarheid

> [!IMPORTANT]
> Als u met SIOS een gedeelde schijf wilt presen teren, plaatst u uw pagina bestand niet op de volumes SIOS data keeper mirrored. U kunt uw wissel bestand op het tijdelijke station D van een virtuele machine van Azure laten staan. Dit is de standaard instelling. Als dit nog niet het geval is, verplaatst u het Windows-pagina bestand naar station D van uw virtuele Azure-machine.  


Het installeren van SAP met een ASCS/SCS-exemplaar met hoge Beschik baarheid omvat de volgende taken:

* Maak een naam voor de virtuele host voor het geclusterde SAP ASCS/SCS-exemplaar.
* Installeer SAP op het eerste cluster knooppunt.
* Wijzig het SAP-Profiel van het ASCS/SCS-exemplaar.
* Voeg een test poort toe.
* Open de Windows Firewall-Test poort.

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Een virtuele-hostnaam maken voor het geclusterde SAP ASCS/SCS-exemplaar

1. Maak een DNS-vermelding voor de naam van de virtuele host van het ASCS/SCS-exemplaar in Windows DNS-beheer.

   > [!IMPORTANT]
   > Het IP-adres dat u toewijst aan de naam van de virtuele host van het ASCS/SCS-exemplaar moet hetzelfde zijn als het IP-adres dat u hebt toegewezen aan Azure Load Balancer.  


   ![Afbeelding 1: Definieer de DNS-vermelding voor de virtuele naam en het TCP/IP-adres van het SAP ASCS/SCS-cluster][sap-ha-guide-figure-3046]

   _Definieer de DNS-vermelding voor de virtuele naam en het TCP/IP-adres van het SAP ASCS/SCS-cluster_

2. Als u gebruikmaakt van de nieuwe SAP-server voor in wachtrij plaatsen, die ook een geclusterd exemplaar is, moet u de naam van de virtuele host voor ERS2 ook reserveren in DNS. 

   > [!IMPORTANT]
   > Het IP-adres dat u toewijst aan de naam van de virtuele host van het ERS2-exemplaar moet het tweede IP-adres zijn dat u hebt toegewezen aan Azure Load Balancer.    


   ![Afbeelding 1A: Definieer de DNS-vermelding voor de virtuele naam en het TCP/IP-adres van het SAP ASCS/SCS-cluster][sap-ha-guide-figure-3046-ers2]

   _Definieer de DNS-vermelding voor de virtuele naam van het SAP ERS2-cluster en het TCP/IP-adres_


3. Als u het IP-adres wilt definiëren dat is toegewezen aan de naam van de virtuele host, selecteert u **DNS-beheer**  >  **domein**.

   ![Afbeelding 2: nieuwe virtuele naam en TCP/IP-adres voor SAP ASCS/SCS-cluster configuratie][sap-ha-guide-figure-3047]

   _Nieuwe virtuele naam en TCP/IP-adres voor de SAP-ASCS/SCS-cluster configuratie_

### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Het SAP eerste cluster knooppunt installeren

1. Voer de eerste cluster knooppunt optie uit op cluster knooppunt A. Selecteer:

   * **ABAP systeem**: **ASCS** -exemplaar nummer **00**
   * **Java-systeem**: **SCS** -exemplaar nummer **01**
   * **ABAP + Java-systeem**: **ASCS** -exemplaar nummer **00** en **SCS** -exemplaar nummer **01**


   > [!IMPORTANT]
   > Houd er rekening mee dat de configuratie in de Azure interne load balancer taakverdelings regels (als u de basis-SKU gebruikt) en de geselecteerde SAP-instantie nummers moeten overeenkomen.

2. Volg de door SAP beschreven installatie procedure. Zorg ervoor dat u in de installatie optie ' eerste cluster knooppunt ' kiest voor ' gedeelde cluster schijf ' als configuratie optie.

> [!TIP]
> In de SAP-installatie documentatie wordt beschreven hoe u het eerste ASCS/SCS-cluster knooppunt installeert.



### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Het SAP-Profiel van het ASCS/SCS-exemplaar wijzigen

Als u replicatie server 1 in de wachtrij hebt, voegt u de para meter SAP-profiel toe, `enque/encni/set_so_keepalive` zoals hieronder wordt beschreven. De profiel parameter voor komt dat verbindingen tussen SAP-werk processen en de bewerkings server worden afgesloten wanneer ze te lang niet actief zijn. De SAP-para meter is niet vereist voor ERS2. 

1. Voeg deze profiel parameter toe aan het SAP-exemplaar profiel voor ASCS/SCS als u gebruikmaakt van ERS1.

   ```
   enque/encni/set_so_keepalive = true
   ```

   Voor zowel ERS1 als ERS2, moet u ervoor zorgen dat de `keepalive` OS-para meters zijn ingesteld zoals beschreven in SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736).   

2. Als u de SAP-profiel parameter wijzigingen wilt Toep assen, start u het SAP ASCS/SCS-exemplaar opnieuw.

### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Een test poort toevoegen

Gebruik de test functionaliteit van de interne load balancer om ervoor te zorgen dat de volledige cluster configuratie werkt met Azure Load Balancer. De interne load balancer van Azure distribueert doorgaans de inkomende werk belasting gelijkmatig tussen de deelnemende virtuele machines.

Dit werkt echter niet in sommige cluster configuraties, omdat er slechts één exemplaar actief is. De andere instantie is passief en kan geen enkele werk belasting accepteren. Een test functionaliteit helpt wanneer de interne load balancer van Azure detecteert welk exemplaar actief is en alleen gericht is op het actieve exemplaar.  

> [!IMPORTANT]
> In dit voor beeld is de **ProbePort** ingesteld op 620 **Nr**. Voor het SAP ASCS-exemplaar met nummer **00** is 620 **00**. U moet de configuratie aanpassen zodat deze overeenkomt met uw SAP-instantie nummers en uw SAP-SID.

Als u een test poort wilt toevoegen, voert u deze Power shell-module uit op een van de cluster-Vm's:

- In het geval van een SAP ASC/SCS-exemplaar 
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID SID -ProbePort 62000
   ```

- Als ERS2 wordt gebruikt, geclusterd. Het is niet nodig om de test poort voor ERS1 te configureren, omdat deze niet is geclusterd.  
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID SID -ProbePort 62001 -IsSAPERSClusteredInstance $True
   ```

 De functie code voor `Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource` zou er als volgt uitzien:
   ```powershell
    function Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource {

    <#
    .SYNOPSIS 
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.

    .DESCRIPTION
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    It will also restart SAP Cluster group (default behavior), to activate the changes. 

    You need to run it on one of the SAP ASCS/SCS Windows cluster nodes.

    Expectation is that SAP group is installed with official SWPM installation tool, which will set default expected naming convention for:
    - SAP Cluster Group:               'SAP $SAPSID'
    - SAP Cluster IP Address Resource: 'SAP $SAPSID IP' 

    .PARAMETER SAPSID 
    SAP SID - 3 characters staring with letter.

    .PARAMETER ProbePort 
    Azure Load Balancer Health Check Probe Port.

    .PARAMETER RestartSAPClusterGroup 
    Optional parameter. Default value is '$True', so SAP cluster group will be restarted to activate the changes.

    .PARAMETER IsSAPERSClusteredInstance 
    Optional parameter.Default value is '$False'.
    If set to $True , then handle clsutered new SAP ERS2 instance.


    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP', and restart the SAP cluster group 'SAP AB1', to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 

    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP'. SAP cluster group 'SAP AB1' IS NOT restarted, therefore changes are NOT active.
    # To activate the changes you need to manualy restart 'SAP AB1' cluster group.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -RestartSAPClusterGroup $False

    .EXAMPLE 
    # Set probe port to 62001, on SAP cluster resource 'SAP AB1 ERS IP'. SAP cluster group 'SAP AB1 ERS' IS restarted, to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -IsSAPERSClusteredInstance $True

    #> 

        [CmdletBinding()]
        param(

            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]  
            [ValidateLength(3,3)]      
            [string]$SAPSID,

            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]        
            [int] $ProbePort,

            [Parameter(Mandatory=$False)] 
            [bool] $RestartSAPClusterGroup = $True,

            [Parameter(Mandatory=$False)] 
            [bool] $IsSAPERSClusteredInstance = $False
        )

        BEGIN{}

        PROCESS{
            try{                                      

                if($IsSAPERSClusteredInstance){
                    #Handle clustered SAP ERS Instance
                    $SAPClusterRoleName = "SAP $SAPSID ERS"
                    $SAPIPresourceName = "SAP $SAPSID ERS IP"            
                }else{
                    #Handle clustered SAP ASCS/SCS Instance
                    $SAPClusterRoleName = "SAP $SAPSID"
                    $SAPIPresourceName = "SAP $SAPSID IP"
                }

                $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
                $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
                $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
                $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
                $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
                $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
                $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

                $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }
                Write-Output "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" 

                Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

                Write-Output " "
                Write-Output "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." 
                Write-Output " "
                Write-Output "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." 
                Write-Output " "

                $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

                Write-Output " "

                if($RestartSAPClusterGroup){
                    Write-Output ""
                    Write-Output "Activating changes..." 

                    Write-Output " "
                    Write-Output "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..."
                    Stop-ClusterResource -Name $SAPIPresourceName
                    sleep 5

                    Write-Output "Starting SAP cluster role '$SAPClusterRoleName' ..."
                    Start-ClusterGroup -Name $SAPClusterRoleName

                    Write-Output "New ProbePort parameter is active." 
                    Write-Output " "

                    Write-Output "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" 
                    Write-Output " " 
                    Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
                }else
                {
                    Write-Output "SAP cluster role '$SAPClusterRoleName' is not restarted, therefore changes are not activated."
                }
            }
            catch{
               Write-Error  $_.Exception.Message
           }
        }
        END {}
    }

   ```

### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Open de Windows Firewall-Test poort

Open een Windows Firewall-Test poort op beide cluster knooppunten. Gebruik het volgende script om een Windows Firewall-Test poort te openen. Werk de Power shell-variabelen voor uw omgeving bij.  
Als ERS2 wordt gebruikt, moet u ook de firewall poort voor de ERS2-test poort openen.  

  ```powershell
    $ProbePort = 62000   # ProbePort of the Azure internal load balancer
    New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

## <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Het data base-exemplaar installeren

Volg het proces dat wordt beschreven in de SAP-installatie documentatie om het data base-exemplaar te installeren.

## <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Het tweede cluster knooppunt installeren

Als u het tweede cluster wilt installeren, volgt u de stappen die worden beschreven in de SAP-installatie handleiding.

## <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> De SAP-primaire toepassings server installeren

Installeer het primaire toepassings server (PAS) exemplaar \<SID\> -di-0 op de virtuele machine die u hebt ingesteld voor het hosten van de pas. Er zijn geen afhankelijkheden voor Azure. Als u SIOS gebruikt, zijn er geen data keeper instellingen.

## <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> De SAP-aanvullende toepassings server installeren

Installeer een extra SAP-toepassings server (AAS) op alle virtuele machines die u hebt aangewezen voor het hosten van een SAP-toepassings Server exemplaar. 

## <a name="test-the-sap-ascsscs-instance-failover"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> De ASCS/SCS-failover van het SAP-exemplaar testen

We gaan ervan uit dat SAP ASCS actief is op knoop punt A.  

1. Controleer of het SAP-systeem een failover kan worden uitgevoerd van knoop punt A naar knoop punt B Kies een van deze opties om een failover van de SAP- \<SID\> cluster groep van cluster knooppunt a naar cluster knooppunt b te initiëren:
    - Failoverclusterbeheer  
    - Failover cluster Power shell

    ```powershell
    $SAPSID = "PR1"     # SAP <SID>

    $SAPClusterGroup = "SAP $SAPSID"
    Move-ClusterGroup -Name $SAPClusterGroup

    ```

2. Start cluster knooppunt A opnieuw op in het Windows-gast besturingssysteem. Hiermee initieert u een automatische failover van de SAP- \<SID\> cluster groep van knoop punt A naar knoop punt B.  
3. Start het cluster knooppunt A opnieuw vanaf het Azure Portal. Hiermee initieert u een automatische failover van de SAP- \<SID\> cluster groep van knoop punt A naar knoop punt B.  
4. Start cluster knooppunt A opnieuw met behulp van Azure PowerShell. Hiermee initieert u een automatische failover van de SAP- \<SID\> cluster groep van knoop punt A naar knoop punt B.

5. Verificatie
   - Controleer na de failover of de SAP- \<SID\> cluster groep wordt uitgevoerd op cluster knooppunt B. 

      ![Afbeelding 8: in Failoverclusterbeheer wordt de SAP \< sid- \> cluster groep uitgevoerd op cluster knooppunt B][sap-ha-guide-figure-5002]

      _In Failoverclusterbeheer wordt de SAP- \<SID\> cluster groep uitgevoerd op cluster knooppunt B_

   - Controleer na de failover of de gedeelde schijf is gekoppeld aan het cluster knooppunt B. 
   - Als u na een failover wilt controleren of met SIOS data keeper de gegevens van het bron volume station S op cluster knooppunt B worden gerepliceerd naar het doel volume station S op cluster knooppunt A. 

      ![Afbeelding 9: SIOS data keeper repliceert het lokale volume van cluster knooppunt B naar cluster knooppunt A][sap-ha-guide-figure-5003]

      _SIOS data keeper kopieert het lokale volume van cluster knooppunt B naar cluster knooppunt A_