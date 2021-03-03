---
title: Azure Vm's HA-architectuur en-scenario's voor SAP NetWeaver | Microsoft Docs
description: Architectuur met hoge Beschik baarheid en scenario's voor SAP NetWeaver op Azure Virtual Machines
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/26/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d689db4916758e13311ad181fb3b5d7ca9a85260
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675322"
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>Architectuur en scenario's met hoge Beschik baarheid voor SAP net-Weaver

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


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
[sap-ascs-ha-multi-sid-wsfc-file-share]:sap-ascs-ha-multi-sid-wsfc-file-share.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-suse-ascs-ha-anf]:high-availability-guide-suse-netapp-files.md
[sap-higher-availability]:sap-higher-availability-architecture-scenarios.md

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92

[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md

[sap-ha-bc-virtual-env-hyperv-vmware-white-paper]:https://scn.sap.com/docs/DOC-44415
[sap-ha-partner-information]:https://scn.sap.com/docs/DOC-8541
[azure-sla]:https://azure.microsoft.com/support/legal/sla/
[azure-virtual-machines-manage-availability]:../../windows/manage-availability.md
[azure-storage-redundancy]:https://azure.microsoft.com/documentation/articles/storage-redundancy/
[azure-storage-managed-disks-overview]:https://docs.microsoft.com/azure/storage/storage-managed-disks-overview

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/planning-monitoring-overview-2502.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-2901]:media/virtual-machines-shared-sap-planning-guide/2901-azure-ha-sap-ha-md.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-3201]:media/virtual-machines-shared-sap-planning-guide/3201-sap-ha-with-sql-md.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Multi-SID-configuratie met hoge Beschik baarheid van SAP)


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

[virtual-machines-manage-availability]:../../manage-availability.md


## <a name="terminology-definitions"></a>Terminologie definities

**Hoge Beschik baarheid**: verwijst naar een reeks technologieën die de IT-verstoringen minimaliseren door middel van een bedrijfs CONTINUÏTEIT van IT-Services via redundante, fout tolerante of met failover beveiligde onderdelen binnen *hetzelfde* Data Center. In ons geval bevindt het data centrum zich in één Azure-regio.

**Herstel na nood** geval heeft ook betrekking op het minimaliseren van de onderbreking van IT-Services en hun herstel, maar in *verschillende* data centers die mogelijk honderden kilo meters van elkaar zijn. In ons geval kunnen de data centers zich in verschillende Azure-regio's binnen dezelfde geopolitieke regio bevinden of op locaties die door u als klant zijn ingesteld.


## <a name="overview-of-high-availability"></a>Overzicht van hoge Beschik baarheid
SAP hoge Beschik baarheid in azure kan worden onderverdeeld in drie typen:

* **Hoge Beschik baarheid van Azure-infra structuur**: 

    Hoge Beschik baarheid kan bijvoorbeeld bestaan uit reken capaciteit (Vm's), netwerk of opslag en de voor delen voor het verg Roten van de beschik baarheid van SAP-toepassingen.

* **VM van Azure-infra structuur opnieuw opstarten voor een *hogere beschik BAARHEID* van SAP-toepassingen**: 

    Als u besluit geen functies zoals Windows Server Failover Clustering (WSFC) of pacemaker in Linux te gebruiken, wordt het opnieuw starten van Azure VM gebruikt. Het beschermt SAP-systemen tegen geplande en ongeplande downtime van de fysieke Azure-server infrastructuur en het algehele onderliggende Azure-platform.

* **Hoge Beschik baarheid van SAP-toepassing**: 

    Als u maximale Beschik baarheid van een volledig SAP-systeem wilt behalen, moet u alle essentiële SAP-systeem onderdelen beveiligen. Bijvoorbeeld:
    * Redundante SAP-toepassings servers.
    * Unieke onderdelen. Een voor beeld hiervan is een Single Point of Failure-onderdeel (SPOF), zoals een SAP ASCS/SCS-exemplaar of een Database Management System (DBMS).

SAP hoge Beschik baarheid in azure wijkt af van de hoge Beschik baarheid van SAP in een on-premises fysieke of virtuele omgeving. De volgende paper [SAP NetWeaver hoge Beschik baarheid en bedrijfs continuïteit in virtuele omgevingen met VMware en Hyper-V op micro soft Windows][sap-ha-bc-virtual-env-hyperv-vmware-white-paper] beschrijft standaard-SAP-configuraties met hoge Beschik baarheid in gevirtualiseerde omgevingen in Windows.

Er is geen sapinst-geïntegreerde SAP-configuratie met hoge Beschik baarheid voor Linux als voor Windows. Zie voor meer informatie over de hoge Beschik baarheid van SAP voor Linux on-premises [gegevens over hoge Beschik baarheid van partners][sap-ha-partner-information].

## <a name="azure-infrastructure-high-availability"></a>Hoge Beschik baarheid van Azure-infra structuur

### <a name="sla-for-single-instance-virtual-machines"></a>SLA voor virtuele machines met één exemplaar

Er is momenteel een SLA van 99,9% voor één VM met Premium Storage. Om een idee te krijgen van de beschik baarheid van één virtuele machine, kunt u het product bouwen van de verschillende beschik bare [Azure Service Level Agreements][azure-sla].

De basis voor de berekening is 30 dagen per maand of 43.200 minuten. Een downtime van 0,05% komt bijvoorbeeld overeen met 21,6 minuten. Zoals gebruikelijk, wordt de beschik baarheid van de verschillende services op de volgende manier berekend:

(Beschikbaarheids service #1/100) * (beschikbaarheids Service #2/100) * (beschikbaarheids service #3/100) \* ...

Bijvoorbeeld:

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 of een algehele Beschik baarheid van 99,75%.

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>Meerdere exemplaren van virtuele machines in dezelfde beschikbaarheidsset
Voor alle virtuele machines waarvoor twee of meer instanties zijn geïmplementeerd in dezelfde *beschikbaarheidsset*, garanderen we dat u ten minste 99,95% van de tijd verbinding hebt met de virtuele machine voor ten minste één exemplaar.

Wanneer twee of meer virtuele machines deel uitmaken van dezelfde beschikbaarheidsset, wordt aan elke virtuele machine in de beschikbaarheidsset een *update domein* en een *fout domein* toegewezen door het onderliggende Azure-platform.

* **Update domeinen** garanderen dat meerdere vm's op hetzelfde moment niet opnieuw worden opgestart tijdens het geplande onderhoud van een Azure-infra structuur. Er wordt slechts één virtuele machine tegelijk opnieuw opgestart.

* **Fout domeinen** garanderen dat vm's worden geïmplementeerd op hardwareonderdelen die geen veelvoorkomende voedings bron en netwerk switch delen. Als servers, een netwerk switch of een energie bron een ongeplande downtime ondergaan, geldt er slechts één virtuele machine.

Zie [de beschik baarheid van virtuele Windows-machines beheren in azure][azure-virtual-machines-manage-availability]voor meer informatie.

Er wordt een beschikbaarheidsset gebruikt voor het bereiken van hoge Beschik baarheid van:

* Redundante SAP-toepassings servers.  
* Clusters met twee of meer knoop punten (bijvoorbeeld Vm's) die SPOFs beveiligen, zoals een SAP ASCS/SCS-exemplaar of een DBMS.


### <a name="azure-availability-zones"></a>Azure-beschikbaarheidszones
Azure is bezig met het implementeren van een concept van [Azure-beschikbaarheidszones](../../../availability-zones/az-overview.md) in verschillende [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/). In azure-regio's waar Beschikbaarheidszones worden aangeboden, hebben de Azure-regio's meerdere data centers, die onafhankelijk zijn van de levering van voedings bronnen, koeling en netwerk. Reden voor het aanleveren van verschillende zones binnen één Azure-regio is om te zorgen dat u toepassingen kunt implementeren op twee of drie Beschikbaarheidszones worden aangeboden. Ervan uitgaande dat problemen in energie bronnen en/of netwerk van invloed zijn op één infra structuur voor de beschikbaarheids zone, is de implementatie van uw toepassing binnen een Azure-regio nog steeds volledig functioneel. Uiteindelijk met enige gereduceerde capaciteit is het mogelijk dat sommige Vm's in de ene zone verloren zijn gegaan. Virtuele machines in de andere twee zones zijn echter nog actief. De Azure-regio's die zones aanbieden, worden weer gegeven in [Azure-beschikbaarheidszones](../../../availability-zones/az-overview.md).

Er zijn enkele dingen die u kunt overwegen om Beschikbaarheidszones te gebruiken. De lijst met overwegingen zoals:

- U kunt geen Azure-beschikbaarheids sets implementeren binnen een beschikbaarheids zone. U moet een beschikbaarheids zone of een Beschikbaarheidsset voor een VM kiezen.
- U kunt de [basis Load Balancer](../../../load-balancer/load-balancer-overview.md) niet gebruiken om failover-cluster oplossingen te maken op basis van Windows Failover Cluster-Services of Linux-pacemaker. In plaats daarvan moet u de [Azure Standard load BALANCER SKU](../../../load-balancer/load-balancer-standard-availability-zones.md) gebruiken
- Azure-beschikbaarheidszones geen garanties geven voor bepaalde afstanden tussen de verschillende zones binnen één regio
- De netwerk latentie tussen de verschillende Azure-beschikbaarheidszones binnen de verschillende Azure-regio's kan verschillen van de Azure-regio naar de regio. Er zijn gevallen waarin u als klant redelijkerwijs de SAP-toepassingslaag kunt uitvoeren die in verschillende zones is geïmplementeerd, omdat de netwerk latentie van de ene zone naar de actieve DBMS-VM nog steeds acceptabel is voor een bedrijfs proces. Overwegende dat er klanten scenario's zijn waarbij de latentie tussen de actieve DBMS-VM in de ene zone en een SAP-toepassings exemplaar in een virtuele machine in een andere zone te opvallend en niet acceptabel voor de SAP-bedrijfs processen is. Als gevolg hiervan moeten de implementatie architecturen verschillen met een actieve/actieve architectuur voor de toepassing of de actieve/passieve architectuur als de latentie te hoog is.
- Het gebruik van [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) is verplicht voor de implementatie in azure-beschikbaarheidszones 


### <a name="planned-and-unplanned-maintenance-of-virtual-machines"></a>Gepland en ongepland onderhoud van virtuele machines

Twee typen Azure-platform gebeurtenissen kunnen van invloed zijn op de beschik baarheid van uw virtuele machines:

* **Geplande onderhouds** gebeurtenissen zijn periodieke updates die door micro soft zijn aangebracht in het onderliggende Azure-platform. De updates verbeteren de algehele betrouw baarheid, prestaties en beveiliging van de platform infrastructuur waarop uw virtuele machines worden uitgevoerd.

* Niet- **geplande onderhouds** gebeurtenissen treden op wanneer de onderliggende hardware of fysieke infra structuur op uw virtuele machine op een of andere manier is mislukt. Het kan bestaan uit lokale netwerk fouten, storingen in de lokale schijf of andere storingen op rack niveau. Wanneer een dergelijke fout wordt gedetecteerd, wordt uw virtuele machine door het Azure-platform automatisch gemigreerd van de beschadigde fysieke server die als host fungeert voor uw virtuele machine naar een gezonde fysieke server. Dergelijke gebeurtenissen zijn zeldzaam, maar kunnen er ook voor zorgen dat uw virtuele machine opnieuw wordt opgestart.

Zie [de beschik baarheid van virtuele Windows-machines beheren in azure][azure-virtual-machines-manage-availability]voor meer informatie.

### <a name="azure-storage-redundancy"></a>Azure Storage-redundantie
De gegevens in uw opslag account worden altijd gerepliceerd om duurzaamheid en hoge Beschik baarheid te garanderen, die voldoen aan de Azure Storage SLA, zelfs in het geval van tijdelijke hardwarefouten.

Omdat Azure Storage standaard drie installatie kopieën van de gegevens houdt, is het gebruik van RAID 5 of RAID 1 op meerdere Azure-schijven overbodig.

Zie [Azure Storage-replicatie][azure-storage-redundancy] voor meer informatie.

### <a name="azure-managed-disks"></a>Azure Managed Disks
Managed Disks is een resource type in Azure Resource Manager dat wordt aanbevolen om te worden gebruikt in plaats van virtuele harde schijven (Vhd's) die zijn opgeslagen in azure Storage-accounts. Beheerde schijven worden automatisch uitgelijnd met een Azure-beschikbaarheidsset van de virtuele machine waaraan deze zijn gekoppeld. Ze verg Roten de beschik baarheid van uw virtuele machine en de services die erop worden uitgevoerd.

Zie  [overzicht van Azure Managed disks][azure-storage-managed-disks-overview]voor meer informatie.

We raden u aan Managed disks te gebruiken, omdat de implementatie en het beheer van uw virtuele machines worden vereenvoudigd.



## <a name="utilizing-azure-infrastructure-high-availability-to-achieve-higher-availability-of-sap-applications"></a>Hoge Beschik baarheid van Azure-infra structuur gebruiken om een *hogere Beschik baarheid* van SAP-toepassingen te krijgen

Als u besluit geen functies zoals WSFC of pacemaker in Linux te gebruiken (momenteel alleen ondersteund voor SUSE Linux Enterprise Server [SLES] 12 en hoger), wordt Azure VM opnieuw gestart. Het beschermt SAP-systemen tegen geplande en ongeplande downtime van de fysieke Azure-server infrastructuur en het algehele onderliggende Azure-platform.

Voor meer informatie over deze methode raadpleegt [u Azure Infrastructure VM opnieuw opstarten gebruiken om een hogere Beschik baarheid van het SAP-systeem te bereiken][sap-higher-availability].

## <a name="high-availability-of-sap-applications-on-azure-iaas"></a><a name="baed0eb3-c662-4405-b114-24c10a62954e"></a> Hoge Beschik baarheid van SAP-toepassingen op Azure IaaS

Als u maximale Beschik baarheid van een volledig SAP-systeem wilt behalen, moet u alle essentiële SAP-systeem onderdelen beveiligen. Bijvoorbeeld:
  * Redundante SAP-toepassings servers.
  * Unieke onderdelen. Een voor beeld hiervan is een Single Point of Failure-onderdeel (SPOF), zoals een SAP ASCS/SCS-exemplaar of een Database Management System (DBMS).

In de volgende secties wordt uitgelegd hoe u hoge Beschik baarheid kunt beheersen voor alle drie essentiële SAP-systeem onderdelen.

### <a name="high-availability-architecture-for-sap-application-servers"></a>Architectuur met hoge Beschik baarheid voor SAP-toepassings servers

> Deze sectie is van toepassing op:
>
> ![Windows-logo.][Logo_Windows] Windows en ![Linux-logo.][Logo_Linux] Linux
>

Normaal gesp roken hebt u geen specifieke oplossing voor hoge Beschik baarheid nodig voor de SAP-toepassings server en dialoogvenster exemplaren. U behaalt hoge Beschik baarheid door redundantie en configureert meerdere dialoogvenster exemplaren in verschillende exemplaren van Azure virtual machines. Er moeten ten minste twee SAP-toepassings exemplaren zijn geïnstalleerd in twee exemplaren van Azure virtual machines.

![Afbeelding 1: een SAP-toepassings server met hoge Beschik baarheid][sap-ha-guide-figure-2000]

_**Afbeelding 1:** SAP-toepassings server met hoge Beschik baarheid_

U moet alle virtuele machines die SAP Application Server-exemplaren hosten in dezelfde Azure-beschikbaarheidsset plaatsen. Een beschikbaarheidsset van Azure zorgt ervoor dat:

* Alle virtuele machines maken geen deel uit van hetzelfde update domein.  
    Een update domein zorgt ervoor dat de virtuele machines niet tegelijkertijd worden bijgewerkt tijdens de uitval tijd voor gepland onderhoud.

    De basis functionaliteit, die voortbouwt op verschillende update-en fout domeinen binnen een Azure-schaal eenheid, is al geïntroduceerd in de sectie [Update domeinen][planning-guide-3.2.2] .

* Alle virtuele machines maken geen deel uit van hetzelfde fout domein.  
    Een fout domein zorgt ervoor dat virtuele machines worden geïmplementeerd, zodat er geen Single Point of Failure invloed heeft op de beschik baarheid van alle virtuele machines.

Het aantal update-en fout domeinen dat kan worden gebruikt door een Azure-beschikbaarheidsset binnen een Azure-schaal eenheid is eindig. Als u Vm's aan één beschikbaarheidsset toevoegt, worden er uiteindelijk twee of meer Vm's in hetzelfde fout-of update domein beëindigd.

Als u een paar SAP-toepassings server exemplaren in hun specifieke Vm's implementeert, ervan uitgaande dat er vijf update domeinen zijn, komt de volgende afbeelding aan de orde. Het werkelijke maximum aantal update-en fout domeinen in een beschikbaarheidsset kan in de toekomst worden gewijzigd:

![Afbeelding 2: hoge Beschik baarheid van SAP-toepassings servers in een Azure Availability set- ][planning-guide-figure-3000]
 _**afbeelding 2:** hoge Beschik baarheid van SAP-toepassings servers in een Azure-beschikbaarheidsset_

Zie [de beschik baarheid van virtuele Windows-machines beheren in azure][azure-virtual-machines-manage-availability]voor meer informatie.

Zie de sectie [Azure Availability sets][planning-guide-3.2.3] van de Azure virtual machines planning and implementation for SAP NetWeaver-document voor meer informatie.

**Alleen niet-beheerde schijven:** Omdat het Azure-opslag account een mogelijke Single Point of Failure is, is het belang rijk dat u ten minste twee Azure-opslag accounts hebt, waarin ten minste twee virtuele machines worden gedistribueerd. In een ideale configuratie worden de schijven van elke virtuele machine waarop een SAP-dialoog exemplaar wordt uitgevoerd, geïmplementeerd in een ander opslag account.

> [!IMPORTANT]
> We raden u ten zeerste aan Azure Managed disks te gebruiken voor de installaties met hoge Beschik baarheid van uw SAP. Omdat beheerde schijven automatisch worden uitgelijnd met de beschikbaarheidsset van de virtuele machine waaraan ze zijn gekoppeld, wordt de beschik baarheid van de virtuele machine en de services die erop worden uitgevoerd, door de computers verhoogd.  
>

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-windows"></a>Architectuur met hoge Beschik baarheid voor een SAP ASCS/SCS-exemplaar in Windows

> ![Windows-logo.][Logo_Windows] Windows
>

U kunt een WSFC-oplossing gebruiken om het SAP ASCS/SCS-exemplaar te beveiligen. De oplossing heeft twee varianten:

* **Het SAP ASCS/SCS-exemplaar met geclusterde gedeelde schijven clusteren**: Zie [een SAP ASCS/SCS-exemplaar op een Windows-failovercluster clusteren met behulp van een gedeelde cluster schijf][sap-high-availability-guide-wsfc-shared-disk]voor meer informatie over deze architectuur.   

* **Het SAP ASCS/SCS-exemplaar met behulp van de bestands share clusteren**: Zie [een SAP ASCS/SCS-exemplaar op een Windows-failovercluster clusteren met behulp van de bestands share][sap-high-availability-guide-wsfc-file-share]voor meer informatie over deze architectuur.

* **De SAP ASCS/SCS-instantie clusteren met behulp van ANF SMB-share**: Zie cluster [cluster a SAP ASCS/SCS instance op een Windows-failovercluster met behulp van ANF SMB-bestands share](./high-availability-guide-windows-netapp-files-smb.md)voor meer informatie over deze architectuur.

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-linux"></a>Architectuur met hoge Beschik baarheid voor een SAP ASCS/SCS-instantie in Linux

> ![Linux-logo.][Logo_Linux] Linux
> 
> Zie voor meer informatie over het clusteren van het SAP ASCS/SCS-exemplaar met behulp van het SLES-Cluster Framework [hoge Beschik baarheid voor SAP NetWeaver op Azure vm's op SuSE Linux Enterprise Server voor SAP-toepassingen][sap-suse-ascs-ha]. Voor een alternatieve architectuur voor SLES, waarvoor geen Maxi maal beschik bare NFS vereist is, raadpleegt u de [hand leiding met hoge Beschik baarheid voor SAP NetWeaver op SuSE Linux Enterprise Server met Azure NetApp files voor SAP-toepassingen][sap-suse-ascs-ha-anf].

Voor meer informatie over het clusteren van het SAP ASCS/SCS-exemplaar met behulp van het Red Hat-Cluster Framework, Zie [Azure virtual machines hoge Beschik baarheid voor SAP NetWeaver op Red Hat Enterprise Linux](./high-availability-guide-rhel.md)


### <a name="sap-netweaver-multi-sid-configuration-for-a-clustered-sap-ascsscs-instance"></a>Multi-SID-configuratie van SAP net-Weaver voor een geclusterde SAP ASCS/SCS-instantie

> ![Windows-logo.][Logo_Windows] Windows
> 
> Multi-SID wordt ondersteund door WSFC, met behulp van bestands share en gedeelde schijf.
> 
> Zie voor meer informatie over architectuur met een hoge Beschik baarheid voor multi-SID in Windows:

* [SAP ASCS/SCS instance multi-SID hoge Beschik baarheid voor Windows Server Failover Clustering en file share][sap-ascs-ha-multi-sid-wsfc-file-share]

* [SAP ASCS/SCS instance multi-SID hoge Beschik baarheid voor Windows Server-Failover Clustering en gedeelde schijf][sap-ascs-ha-multi-sid-wsfc-shared-disk]

> ![Linux-logo.][Logo_Linux] Linux
> 
> Multi-SID clustering wordt ondersteund op Linux pacemaker-clusters voor SAP ASCS/ERS, beperkt tot **vijf** SAP-sid's op hetzelfde cluster.
> Zie voor meer informatie over architectuur met een hoge Beschik baarheid voor multi-SID op Linux:

* [HA voor SAP NW op Azure Vm's op SLES voor SAP-toepassingen multi-SID-hand leiding](./high-availability-guide-suse-multi-sid.md)
* [HA voor SAP NW op Azure Vm's op RHEL voor SAP-toepassingen multi-SID-hand leiding](./high-availability-guide-rhel-multi-sid.md)

### <a name="high-availability-dbms-instance"></a>DBMS-exemplaar met hoge Beschik baarheid

Het DBMS is ook een enkel contact punt in een SAP-systeem. U moet deze beveiligen met behulp van een oplossing met hoge Beschik baarheid. In de volgende afbeelding ziet u een SQL Server AlwaysOn-oplossing met hoge Beschik baarheid in azure, met Windows Server Failover Clustering en de interne load balancer van Azure. SQL Server AlwaysOn repliceert DBMS-gegevens en logboek bestanden met behulp van de eigen DBMS-replicatie. In dit geval hebt u geen gedeelde cluster schijf nodig, waardoor de volledige installatie wordt vereenvoudigd.

![Afbeelding 3: voor beeld van een hoge Beschik baarheid SAP DBMS met SQL Server AlwaysOn][sap-ha-guide-figure-2003]

_**Afbeelding 3:** Voor beeld van een SAP-DBMS met hoge Beschik baarheid, met SQL Server AlwaysOn_

Zie de volgende artikelen voor meer informatie over het clusteren SQL Server DBMS in azure met behulp van het Azure Resource Manager-implementatie model:

* [Hand matig een AlwaysOn-beschikbaarheids groep configureren in azure virtual machines met behulp van Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [Een interne Azure-load balancer voor een AlwaysOn-beschikbaarheids groep configureren in azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

Zie [hoge Beschik baarheid van SAP Hana op Azure virtual machines (vm's)][sap-hana-ha]voor meer informatie over het clusteren van SAP Hana DBMS in azure met behulp van het Azure Resource Manager-implementatie model.
