---
title: SQL Server Azure Virtual Machines DBMS-implementatie voor SAP-workload | Microsoft Docs
description: DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor SQL Server
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: Azure, SQL Server, SAP, AlwaysOn
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4eb7e64065e311dc18f33dffb169d5c27a34008d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101673042"
---
# <a name="sql-server-azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>SQL Server Azure Virtual Machines DBMS-implementatie voor SAP net-Weaver

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide_general.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms_guide_sqlserver.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d 
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c 
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md 
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab 
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e 
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e 
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc 
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d 
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f 
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca 
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b 
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d 
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

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
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd 
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f 

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam 
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/eresources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md




In dit document worden verschillende gebieden besproken waarmee u rekening moet houden bij het implementeren van SQL Server voor SAP-workload in azure IaaS. Als een voor waarde voor dit document, moet u de document [overwegingen voor Azure virtual machines DBMS-implementatie van de SAP-werk belasting](./dbms_guide_general.md) en andere hand leidingen in de [SAP-werk belasting op de Azure-documentatie](./get-started.md)lezen. 



> [!IMPORTANT]
> Het bereik van dit document is de Windows-versie op SQL Server. SAP biedt geen ondersteuning voor de Linux-versie van SQL Server met een van de SAP-software. Het document bespreekt niet Microsoft Azure SQL Database, een platform als een service aanbod van het Microsoft Azure platform. De discussie in dit artikel is van toepassing op het uitvoeren van het SQL Server product omdat het bekend is voor on-premises implementaties in azure Virtual Machines, waarbij de infra structuur als een service mogelijkheid van Azure wordt gebruikt. De mogelijkheden en functionaliteit van de data base tussen deze twee aanbiedingen zijn verschillend en mogen niet met elkaar worden gecombineerd. Zie ook: <https://azure.microsoft.com/services/sql-database/>
> 
>

Over het algemeen kunt u overwegen de meest recente SQL Server releases te gebruiken voor het uitvoeren van SAP-werk belasting in azure IaaS. De nieuwste SQL Server releases bieden een betere integratie in een aantal van de Azure-Services en-functies. Of u hebt wijzigingen die de bewerkingen in een Azure IaaS-infra structuur optimaliseren.

Het is raadzaam om het artikel te lezen [wat is SQL Server op Azure Virtual Machines (Windows)] [ https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview ] voordat u doorgaat.

In de volgende secties worden delen van onderdelen van de documentatie onder de bovenstaande koppeling geaggregeerd en vermeld. De details rond SAP worden ook vermeld en sommige concepten worden uitvoerig beschreven. Het is echter raadzaam de bovenstaande documentatie eerst te gebruiken voordat u de SQL Server-specifieke documentatie leest.

Er zijn enkele SQL Server in IaaS specifieke informatie die u moet weten voordat u doorgaat:

* **SQL-versie ondersteuning**: voor SAP-klanten wordt SQL Server 2008 R2 en hoger ondersteund op Microsoft Azure virtuele machine. Eerdere versies worden niet ondersteund. Lees deze algemene [ondersteunings verklaring](https://support.microsoft.com/kb/956893) voor meer informatie. In het algemeen wordt SQL Server 2008 ook ondersteund door micro soft. Als gevolg van belang rijke functionaliteit voor SAP, dat is geïntroduceerd met SQL Server 2008 R2, is SQL Server 2008 R2 de minimale release voor SAP. Over het algemeen kunt u overwegen de meest recente SQL Server releases te gebruiken voor het uitvoeren van SAP-werk belasting in azure IaaS. De nieuwste SQL Server releases bieden een betere integratie in een aantal van de Azure-Services en-functies. Of u hebt wijzigingen die de bewerkingen in een Azure IaaS-infra structuur optimaliseren. Daarom is het papier beperkt tot SQL Server 2016 en SQL Server 2017.
* **SQL-prestaties**: Microsoft Azure gehoste virtual machines goed uit te voeren in vergelijking met andere open bare Cloud virtualisatie-aanbiedingen, maar afzonderlijke resultaten kunnen variëren. Bekijk de [Best practices voor de artikel prestaties voor SQL Server in Azure virtual machines](../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md).
* **Installatie kopieën van Azure Marketplace**: de snelste manier om een nieuwe Microsoft Azure virtuele machine te implementeren, is door gebruik te maken van een installatie kopie van Azure Marketplace. De Azure Marketplace bevat afbeeldingen die de meest recente SQL Server releases bevatten. De installatie kopieën waarbij SQL Server al is geïnstalleerd, kunnen niet direct worden gebruikt voor SAP NetWeaver-toepassingen. De reden hiervoor is dat de standaard SQL Server sortering wordt geïnstalleerd binnen die installatie kopieën en niet op de door SAP NetWeaver-systemen vereiste sortering. Als u dergelijke installatie kopieën wilt gebruiken, raadpleegt u de stappen in hoofd stuk [een SQL Server installatie kopie uit de Microsoft Azure Marketplace][dbms-guide-5.6]. 


## <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Aanbevelingen voor de VM-en VHD-structuur voor SAP-gerelateerde SQL Server-implementaties
In overeenstemming met de algemene beschrijving, het besturings systeem, SQL Server uitvoer bare bestanden en in het geval van SAP 2-laag systemen, moeten de SAP-uitvoer bare bestanden worden gevonden of afzonderlijke Azure-schijven zijn geïnstalleerd. Doorgaans worden de meeste van de SQL Server-systeem databases niet op hoog niveau gebruikt door de werk belasting van SAP NetWeaver. Niettemin moeten de systeem databases van SQL Server (Master, msdb en model) samen met de andere SQL Server directory's op een afzonderlijke Azure-schijf zijn. SQL Server TempDB moet zich bevinden op de nonperisisted D:\ station of op een afzonderlijke schijf.


* Met alle SAP-gecertificeerde VM-typen (Zie SAP-opmerking [1928533]), met uitzonde ring van vm's van de A-serie, tempdb-gegevens en logboek bestanden kunnen worden geplaatst op de niet-persistente D:\ stationsletter. 
* Voor oudere versies van SQL Server, waarbij SQL Server TempDB met één gegevens bestand installeert, is het raadzaam om meerdere TempDB-gegevens bestanden te gebruiken. Let op D:\ de volumes van het station wijken af op basis van het VM-type. Voor exacte grootte van de D:\ van de verschillende Vm's, controleert u de artikel [grootten voor virtuele Windows-machines in azure](../../sizes.md).

Met deze configuraties kunnen TempDB meer ruimte en meer belang rijkere IOPS en opslag bandbreedte gebruiken dan het systeem station kan bieden. De niet-permanente D:\ Station biedt ook betere I/O-latentie en door Voer (met uitzonde ring van Vm's van de A-serie). Als u de juiste TempDB-grootte wilt bepalen, kunt u de tempdb-grootte op bestaande systemen controleren. 

>[!NOTE]
> Als u TempDB-gegevens bestanden en logboek bestand plaatst in een map op D:\ station dat u hebt gemaakt, moet u ervoor zorgen dat de map bestaat nadat de VM opnieuw is opgestart. Sinds de D:\ station is automatisch geïnitialiseerd na het opnieuw opstarten van een VM. alle bestanden en mapstructuren worden gewist. Een mogelijkheid tot het opnieuw maken van uiteindelijke mapstructuren op D:\ station voordat de SQL Server-service wordt gestart, wordt in [dit artikel](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)beschreven.

Een VM-configuratie, die SQL Server uitvoert met een SAP-data base en waar het logboek bestand TempDB-gegevens en tempdb worden geplaatst op de D:\ station zou er als volgt uitzien:

![Diagram van eenvoudige VM-schijf configuratie voor SQL Server](./media/dbms_sqlserver_deployment_guide/Simple_disk_structure.PNG)

In het bovenstaande diagram wordt een eenvoudige Case weer gegeven. Als eluded in het artikel [overwegingen voor azure virtual machines DBMS-implementatie voor de SAP-werk belasting](dbms_guide_general.md), is het type, het aantal en de grootte van de schijven afhankelijk van verschillende factoren. Maar in het algemeen raden we het volgende aan:

- Een groot volume gebruiken dat de SQL Server gegevens bestanden bevat. De reden achter deze configuratie is dat er in Real-Life talrijke SAP-data bases met verschillende omvang database bestanden met verschillende I/O-werk belastingen zijn.
- Gebruik de D:\drive voor TempDB, zolang de prestaties goed zijn. Als de totale werk belasting wordt beperkt door TempDB op de D:\ u moet mogelijk overwegen om TempDB te verplaatsen naar een afzonderlijke Azure Premium-opslag of Ultra Disk-schijven, zoals aanbevolen in [dit artikel](../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md).


### <a name="special-for-m-series-vms"></a>Speciaal voor Vm's uit de M-serie
Voor virtuele machines uit de M-serie van Azure kan de latentie die in het transactie logboek wordt geschreven, worden verminderd met factoren, vergeleken met de prestaties van Azure Premium Storage, wanneer u Azure Write Accelerator gebruikt. Daarom moet u Azure Write Accelerator implementeren voor de VHD (s) die het volume vormen voor het SQL Server transactie logboek. Details kunnen worden gelezen in het document [Write Accelerator](../../how-to-enable-write-accelerator.md).
  

### <a name="formatting-the-disks"></a>Schijven Format teren
Voor SQL Server moet de NTFS-blok grootte voor schijven met SQL Server gegevens en logboek bestanden 64 KB zijn. Het is niet nodig om de D:\ te Format teren stationsletter. Dit station wordt vooraf geformatteerd.

Om ervoor te zorgen dat het terugzetten of het maken van data bases de gegevens bestanden niet initialiseert door de inhoud van de bestanden op te nul te zetten, moet u ervoor zorgen dat de gebruikers context waarin de SQL Server-service wordt uitgevoerd, een bepaalde machtiging heeft. Meestal hebben gebruikers in de groep Windows-beheerders deze machtigingen. Als de SQL Server-service wordt uitgevoerd in de gebruikers context van niet-Windows-beheerder, moet u deze gebruiker het gebruikers recht het **volume onderhouds taken uitvoeren**.  Zie de details in dit micro soft Knowledge Base-artikel: <https://support.microsoft.com/kb/2574695>

### <a name="impact-of-database-compression"></a>Impact van database compressie
Bij configuraties waarbij de I/O-band breedte een beperkende factor kan worden, kan elke meting, die het aantal IOPS reduceert, de werk belasting die in een IaaS-scenario zoals Azure kan worden uitgevoerd, worden uitgerekt. Als u dit nog niet hebt gedaan, wordt het Toep assen van SQL Server-pagina compressie aanbevolen door SAP en micro soft voordat u een bestaande SAP-data base naar Azure uploadt.

De aanbeveling om database compressie uit te voeren voordat uploadt naar Azure heeft twee redenen:

* De hoeveelheid gegevens die moet worden geüpload, is lager.
* De duur van het uitvoeren van de compressie is korter, ervan uitgaande dat een grotere hardware kan gebruiken met meer Cpu's of een hogere I/O-band breedte of minder I/o-latentie on-premises.
* Kleinere database grootten kunnen leiden tot minder kosten voor schijf toewijzing

Database compressie werkt ook in een Azure-Virtual Machines zoals het on-premises. Raadpleeg het artikel [verbeterde SAP-compressie HULPPROGRAMMA'S MSSCOMPRESS](/archive/blogs/saponsqlserver/improved-sap-compression-tool-msscompress)voor meer informatie over het comprimeren van bestaande SAP netweaver SQL server-data bases. 

## <a name="sql-server-2014-and-more-recent---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 en meer recent: database bestanden rechtstreeks op Azure opslaan Blob Storage
SQL Server 2014 en hoger geeft u de mogelijkheid om database bestanden rechtstreeks op te slaan in de Azure Blob Store zonder de wrapper van een VHD. Met name bij het gebruik van standaard Azure Storage of kleinere VM-typen, kunt u met dit type implementatie de limieten van IOPS beperken die worden afgedwongen door een beperkt aantal schijven dat kan worden gekoppeld aan bepaalde kleinere VM-typen. Deze manier van implementeren werkt voor gebruikers databases echter niet voor systeem databases van SQL Server. Het werkt ook voor gegevens-en logboek bestanden van SQL Server. Als u een SAP SQL Server-Data Base op deze manier wilt implementeren in plaats van ' in Vhd's ' in te zetten, moet u in het volgende letten:

* Het gebruikte opslag account moet zich in dezelfde Azure-regio bevinden als de locatie die wordt gebruikt voor het implementeren van de virtuele machine SQL Server wordt uitgevoerd in.
* De eerder vermelde overwegingen met betrekking tot de verdeling van Vhd's via verschillende Azure Storage-accounts zijn ook van toepassing op deze implementatie methode. Betekent dat het aantal I/O-bewerkingen overeenkomt met de limieten van het Azure Storage-account.
* In plaats van rekening houdend met de opslag-I/O-quota van de virtuele machine, wordt het verkeer voor opslag-blobs die de SQL Server gegevens en logboek bestanden vertegenwoordigen, verwerkt in de netwerk bandbreedte van de virtuele machine van het specifieke VM-type. Voor netwerk-en opslag bandbreedte van een bepaald VM-type raadpleegt u de artikel [grootten voor virtuele Windows-machines in azure](../../sizes.md).
* Als gevolg van het pushen van bestands-I/O via het netwerk quotum, bent u de opslag quota voornamelijk en met die de totale band breedte van de virtuele machine slechts gedeeltelijk gebruiken.
* De prestatie doelen IOPS en I/O-doorvoer snelheid die Azure Premium Storage heeft voor de verschillende schijf grootten, zijn niet meer van toepassing. Zelfs als de blobs die u hebt gemaakt, zich bevinden op Azure Premium Storage. De doelen zijn een documentatie van het artikel [high-performance Premium Storage en Managed disks voor vm's](../../disks-types.md#premium-ssd). Als gevolg van het plaatsen van SQL Server gegevens bestanden en logboek bestanden rechtstreeks op blobs die zijn opgeslagen in azure Premium Storage, kunnen de prestatie kenmerken verschillen ten opzichte van Vhd's in azure Premium Storage.
* Op de host gebaseerde caching als beschikbaar voor Azure Premium Storage schijven is niet beschikbaar wanneer u SQL Server gegevens bestanden rechtstreeks op Azure-blobs plaatst.
* Op virtuele machines uit de M-serie kan Azure Write Accelerator niet worden gebruikt voor de ondersteuning van submilliseconde schrijf bewerkingen voor het SQL Server transactie logboek bestand. 

Meer informatie over deze functionaliteit vindt u in het artikel [SQL Server gegevens bestanden in Microsoft Azure](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure)

Aanbeveling voor productie systemen is om deze configuratie te vermijden en in plaats daarvan de plaatsingen van SQL Server gegevens en logboek bestanden in azure Premium Storage Vhd's te kiezen in plaats van rechtstreeks op Azure-blobs.


## <a name="sql-server-2014-buffer-pool-extension"></a>Extensie van SQL Server 2014-buffer groep
SQL Server 2014 heeft een nieuwe functie geïntroduceerd, die een [buffergroepuitbreiding wordt genoemd.](/sql/database-engine/configure-windows/buffer-pool-extension) Deze functie breidt de buffer groep van SQL Server uit, die in het geheugen wordt bewaard met een cache van het tweede niveau die wordt ondersteund door de lokale Ssd's van een server of virtuele machine. Met de buffergroepuitbreiding kan een grotere werkset gegevens in het geheugen worden bewaard. Vergeleken met het openen van Azure Standard-opslag is de toegang tot de uitbrei ding van de buffer groep, die is opgeslagen op de lokale Ssd's van een Azure-VM, veel factoren sneller. Als u de extensie van de buffer groep vergelijkt met Azure Premium Storage Lees cache, zoals aanbevolen voor SQL Server gegevens bestanden, worden er geen aanzienlijke voor delen verwacht voor de extensies van de buffer groep. De reden hiervoor is dat beide caches (SQL Server Buffergroepuitbreiding en Premium Storage Lees cache) gebruikmaken van de lokale schijven van het Azure Compute-knoop punt.

Ervaringen die in de tussen tijd worden opgedaan met SQL Server-Buffergroepuitbreiding met SAP-werk belasting, worden gecombineerd en blijven geen duidelijke aanbevelingen voor het gebruik van de groep in alle gevallen. Het ideale geval is dat de werkset die de SAP-toepassing nodig heeft in het hoofd geheugen past. Met Azure ondertussen bieden Vm's die Maxi maal 4 TB aan geheugen hebben geleverd, het kan worden behaald om de werkset in het geheugen te blijven gebruiken. Daarom is het gebruik van de extensie van de buffer groep beperkt tot enkele zeldzame gevallen en mag het geen gang bare zaak zijn.  

## <a name="backuprecovery-considerations-for-sql-server"></a>Overwegingen voor back-up/herstel voor SQL Server
Wanneer u SQL Server in azure implementeert, moet uw back-upmethodologie worden gecontroleerd. Zelfs als het systeem geen productie systeem is, moet u regel matig een back-up maken van de SAP-data base die wordt gehost door SQL Server. Omdat Azure Storage drie installatie kopieën houdt, is een back-up nu minder belang rijk voor het compenseren van een storing in de opslag. De prioriteits reden voor het onderhouden van een correct back-up-en herstel plan is meer dat u kunt compenseren voor logische/hand matige fouten door herstel mogelijkheden voor het punt op te geven. Het doel is dus back-ups te gebruiken om de data base terug te zetten naar een bepaald tijdstip of om de back-ups in azure te gebruiken om een ander systeem te seeden door de bestaande Data Base te kopiëren. 

Lees het artikel [back-up en herstel voor SQL Server in azure virtual machines](../../../azure-sql/virtual-machines/windows/backup-restore.md)om te kijken naar verschillende SQL Server back-upmogelijkheden in Azure. In het artikel worden verschillende mogelijkheden besproken.

### <a name="manual-backups"></a>Hand matige back-ups
U hebt verschillende mogelijkheden om hand matig back-ups uit te voeren:

1. Conventioneel SQL Server back-ups uitvoeren op direct gekoppelde Azure-schijven. Deze methode heeft het voor deel dat u de back-ups snel beschikbaar hebt voor het vernieuwings proces van het systeem en nieuwe systemen kunt bouwen als kopieën van bestaande SAP-systemen
2.  SQL Server 2012 CU4 en hoger kunnen back-ups maken van data bases naar een Azure Storage-URL.
3.  File-Snapshot back-ups voor database bestanden in Azure Blob Storage. Deze methode werkt alleen als uw SQL Server gegevens-en logboek bestanden zich in Azure Blob Storage bevinden

De eerste methode is goed bekend en wordt ook in veel gevallen in de on-premises wereld toegepast. Niettemin verlaat het de taak om de back-uplocatie voor langere termijn op te lossen. Omdat u uw back-ups gedurende 30 of meer dagen niet wilt bewaren in de lokaal gekoppelde Azure Storage, moet u Azure Backup Services of een ander hulp programma voor back-up/herstel van derden gebruiken, waaronder het beheer en de retentie van uw back-ups. Of u bouwt een grote Bestands server in azure met behulp van Windows-opslag ruimten.

De tweede methode wordt dichter in het artikel beschreven [SQL Server back-up naar URL](../../../azure-sql/virtual-machines/windows/backup-restore.md). Verschillende versies van SQL Server hebben enkele variaties in deze functionaliteit. Daarom moet u de documentatie raadplegen voor uw specifieke SQL Server versie controle. Het is belang rijk te weten dat dit artikel een groot aantal beperkingen vermeldt. U hebt de mogelijkheid om de back-up uit te voeren op:

- Eén Azure-pagina-blob, die vervolgens de grootte van de back-up beperkt tot 1000 GB. Deze beperking beperkt ook de door Voer die u kunt behaalt.
- Meerdere (tot 64) Azure-blok-blobs, waarmee een theoretische back-up van 12 TB wordt ingeschakeld. Tests met klant databases hebben echter aangetoond dat de maximale back-upgrootte kleiner kan zijn dan de theoretische limiet. In dit geval bent u verantwoordelijk voor het beheren van het bewaren van back-ups en de toegang tot de back-ups.


### <a name="automated-backup-for-sql-server"></a>Automatische back-up voor SQL Server
Automatische back-up biedt een automatische back-upservice voor SQL Server Standard-en Enter prise-edities die worden uitgevoerd in een Windows-VM in Azure. Deze service wordt verzorgd door de [SQL Server IaaS agent-extensie](../../../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md), die automatisch wordt geïnstalleerd op SQL Server installatie kopieën van virtuele Windows-machines in de Azure Portal. Als u uw eigen installatie kopieën van het besturings systeem implementeert met SQL Server geïnstalleerd, moet u de VM-extensies afzonderlijk installeren. De benodigde stappen zijn beschreven in dit [artikel](../../../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md).

Meer informatie over de mogelijkheden van deze methode vindt u in de volgende artikelen:

- SQL Server 2014: [geautomatiseerde back-up voor SQL Server 2014 virtual machines (Resource Manager)](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md)
- SQL Server 2016/2017: [automatische back-up v2 voor Azure virtual machines (Resource Manager)](../../../azure-sql/virtual-machines/windows/automated-backup.md)

Als u de documentatie wilt raadplegen, kunt u zien dat de functionaliteit van de meest recente SQL Server releases verbeterd. Meer informatie over SQL Server automatische back-ups vindt u in het artikel [SQL Server beheerde back-up naar Microsoft Azure](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure). De maximale grootte van de theoretische back-up is 12 TB.  De automatische back-ups kunnen een goede methode zijn voor back-upgroottes van Maxi maal 12 TB. Aangezien er parallel meerdere blobs worden geschreven, kunt u een door Voer van meer dan 100 MB/seconde verwachten. 
 

### <a name="azure-backup-for-sql-server-vms"></a>Azure Backup voor SQL Server Vm's
Deze nieuwe methode voor het SQL Server van back-ups wordt aangeboden vanaf juni 2018 als open bare preview door Azure Backup Services. De methode voor het maken van een back-up SQL Server is hetzelfde als andere hulpprogram ma's van derden, namelijk de SQL Server VSS/VDI-interface voor het streamen van back-ups naar een doel locatie. In dit geval is de doel locatie de Azure Recovery service-kluis.

Een meer dan een gedetailleerde beschrijving van deze back-upmethode, waarmee talloze voor delen van centrale back-upconfiguraties, bewaking en beheer worden toegevoegd, is [hier](../../../backup/backup-azure-sql-database.md)beschikbaar. 


### <a name="third-party-backup-solutions"></a>Back-upoplossing van derden
Voor een zeer aantal SAP-klanten was het niet mogelijk om te beginnen en nieuwe back-upoplossingen te introduceren voor het onderdeel van hun SAP-landschap dat werd uitgevoerd op Azure. Als gevolg hiervan zijn de bestaande back-upoplossingen die nodig zijn om te worden gebruikt en uitgebreid naar Azure. Het uitbreiden van bestaande back-upoplossingen in azure werkt normaal gesp roken goed met de meeste hoofd leveranciers in deze ruimte. 


## <a name="using-a-sql-server-image-out-of-the-microsoft-azure-marketplace"></a><a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Een SQL Server afbeelding uit de Microsoft Azure Marketplace gebruiken
Micro soft biedt Vm's in azure Marketplace die al versies van SQL Server bevatten. Voor SAP-klanten die licenties nodig hebben voor SQL Server en Windows, kan het gebruik van deze installatie kopieën een kans zijn om licenties te maken door Vm's te bedekken waarop SQL Server al is geïnstalleerd. Als u dergelijke installatie kopieën voor SAP wilt gebruiken, moet u rekening houden met de volgende overwegingen:

* De SQL Server niet-evaluatie versies krijgen hogere kosten dan een Windows-VM die is geïmplementeerd vanuit Azure Marketplace. Zie de volgende artikelen voor het vergelijken van prijzen: <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> en <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/> . 
* U kunt alleen SQL Server releases gebruiken die worden ondersteund door SAP.
* De sortering van de SQL Server-instantie, die is geïnstalleerd in de Vm's die worden aangeboden in de Azure Marketplace, is niet vereist voor het uitvoeren van de SQL Server-instantie. U kunt de sortering wijzigen met de instructies in de volgende sectie.

### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>De SQL Server collatie van een micro soft Windows/SQL Server VM wijzigen
Omdat de SQL Server-installatie kopieën in azure Marketplace niet zijn ingesteld voor het gebruik van de sortering, die vereist is voor SAP NetWeaver-toepassingen, moet deze onmiddellijk na de implementatie worden gewijzigd. Voor SQL Server kan deze wijziging van de sortering worden uitgevoerd met de volgende stappen zodra de virtuele machine is geïmplementeerd en een beheerder zich kan aanmelden bij de geïmplementeerde VM:

* Open een Windows-opdracht venster als Administrator.
* Wijzig de Directory in C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Voer de opdracht uit: Setup.exe/QUIET/ACTION = REBUILDDATABASE/INSTANCENAME = MSSQLserver/SQLSYSADMINACCOUNTS = `<local_admin_account_name`>/SQLCOLLATION = SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> is het account dat is gedefinieerd als het beheerders account wanneer de virtuele machine voor het eerst via de galerie wordt geïmplementeerd.

Het proces kan slechts enkele minuten duren. Voer de volgende stappen uit om te controleren of de stap met het juiste resultaat is beëindigd:

* Open SQL Server Management Studio.
* Open een query venster.
* Voer de opdracht uit sp_helpsort in de data base SQL Server.

Het gewenste resultaat moet er als volgt uitzien:

```output
Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data
```

Als het resultaat afwijkt, stopt u het implementeren van SAP en onderzoekt u waarom de installatie opdracht niet werkt zoals verwacht. De implementatie van SAP NetWeaver-toepassingen op SQL Server-exemplaar met verschillende SQL Server code tabellen die hierboven worden vermeld, wordt **niet** ondersteund.

## <a name="sql-server-high-availability-for-sap-in-azure"></a>SQL Server High-Availability voor SAP in azure
Als u SQL Server gebruikt in azure IaaS-implementaties voor SAP, hebt u verschillende mogelijkheden om toe te voegen om de DBMS-laag Maxi maal beschikbaar te maken. Zoals beschreven in [overwegingen voor azure virtual machines DBMS-implementatie voor SAP-werk belasting](dbms_guide_general.md) , biedt Azure verschillende eenmalige service overeenkomsten voor één virtuele machine en een combi natie van vm's die zijn geïmplementeerd in een Azure-beschikbaarheidsset. Hypo these is dat u zich aanmeldt voor de up-time-SLA voor uw productie-implementaties waarvoor de implementatie in azure-beschikbaarheids sets is vereist. In dat geval moet u Mini maal twee Vm's implementeren in een dergelijke Beschikbaarheidsset. Met één virtuele machine wordt het actieve SQL Server-exemplaar uitgevoerd. Het passieve exemplaar wordt uitgevoerd op de andere VM

### <a name="sql-server-clustering-using-windows-scale-out-file-server-or-azure-shared-disk"></a>SQL Server clustering met behulp van scale-out Bestands server of gedeelde Azure-schijf
Met Windows Server 2016 heeft micro soft [opslagruimten direct](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)geïntroduceerd. Op basis van Opslagruimten Direct-implementatie wordt SQL Server FCI-clustering in het algemeen ondersteund. Azure biedt ook [gedeelde Azure-schijven](../../disks-shared-enable.md?tabs=azure-cli) die kunnen worden gebruikt voor Windows-Clustering. Voor SAP-workload ondersteunen we deze HA-opties niet. 

### <a name="sql-server-log-shipping"></a>SQL Server logboek verzending
Een van de methoden van hoge Beschik baarheid (HA) is SQL Server logboek verzending. Als de virtuele machines die deel uitmaken van de HA-configuratie een naam omzetting hebben, is er geen probleem en is de installatie in azure niet afhankelijk van de instellingen die on-premises worden uitgevoerd. Met betrekking tot het instellen van de verzen ding van Logboeken en de principes voor het vastleggen van de back-upfunctie Details over SQL Server logboek verzending vindt u in het artikel [over het verzenden van Logboeken (SQL Server)](/sql/database-engine/log-shipping/about-log-shipping-sql-server).

De functie voor het verzenden van SQL Server-Logboeken is in azure nauwelijks gebruikt voor een hoge Beschik baarheid binnen een Azure-regio. In de volgende scenario's gebruiken SAP-klanten echter een geslaagde back-ups van het logboek in combi natie met Azure:

- Scenario's voor herstel na nood gevallen van de ene Azure-regio naar een andere Azure-regio
- Configuratie van herstel na nood gevallen van on-premises naar een Azure-regio
- Scenario's voor uitsnede van on-premises naar Azure. In dergelijke gevallen wordt de functie voor het vastleggen van Logboeken gebruikt voor het synchroniseren van de nieuwe DBMS-implementatie in azure met het actieve productie systeem op locatie. Op het moment van de overschakeling wordt de productie afgesloten en er wordt gecontroleerd of de laatste en laatste transactie logboek back-ups zijn overgebracht naar de Azure DBMS-implementatie. Vervolgens wordt de Azure-DBMS-implementatie geopend voor productie.  



### <a name="database-mirroring"></a>Databases spiegelen
Het spie gelen van data bases zoals ondersteund door SAP (Zie SAP Note [965908]) is afhankelijk van het definiëren van een failover-partner in de SAP-Connection String. Voor de cross-premises-cases wordt ervan uitgegaan dat de twee virtuele machines zich in hetzelfde domein bevinden en dat de gebruikers context dat de twee SQL Server exemplaren worden uitgevoerd onder een domein gebruiker en voldoende bevoegdheden hebben in de twee SQL Server-instanties betrokken. Daarom verschilt de installatie van het spie gelen van data bases in azure niet tussen een typische on-premises installatie/configuratie.

Vanaf Cloud-Only implementaties is de eenvoudigste methode om een andere domein instelling in azure te hebben om deze DBMS-Vm's (en in het ideale geval specifieke SAP-Vm's) binnen één domein te hebben.

Als een domein niet mogelijk is, kan één ook certificaten voor de data base mirroring-eind punten gebruiken zoals hier wordt beschreven: <https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Een zelf studie voor het instellen van het spie gelen van data bases in azure vindt u hier: <https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

### <a name="sql-server-always-on"></a>SQL Server Always On
Zoals always on wordt ondersteund voor SAP on-premises (Zie SAP Note [1772688]), wordt dit ondersteund in combi natie met SAP in Azure. Er zijn enkele belang rijke aandachtspunten voor het implementeren van de SQL Server beschikbaarheids groep-listener (niet te verwarren met de Azure-Beschikbaarheidsset), omdat Azure op dit moment niet toestaat dat er een AD/DNS-object wordt gemaakt omdat het on-premises mogelijk is. Daarom zijn sommige verschillende installatie stappen nodig om het specifieke gedrag van Azure te overwinnen.

Enkele overwegingen bij het gebruik van een beschikbaarheids groep-listener zijn:

* Het gebruik van een beschikbaarheids groep-listener is alleen mogelijk met Windows Server 2012 of hoger als gast besturingssysteem van de virtuele machine. Voor Windows Server 2012 moet u er zeker van zijn dat deze patch wordt toegepast: <https://support.microsoft.com/kb/2854082> 
* Voor Windows Server 2008 R2 bestaat deze patch niet en moet always altijd op dezelfde manier worden gebruikt als het spie gelen van data bases door een failover-partner op te geven in de verbindings reeks (uitgevoerd via de SAP-standaard waarde. PFL para meter db's/MSS/server-Zie SAP-opmerking [965908]).
* Wanneer u een beschikbaarheids groep-listener gebruikt, moeten de data base-Vm's zijn verbonden met een toegewezen Load Balancer. Om te voor komen dat Azure nieuwe IP-adressen toewijst in gevallen waarbij beide Vm's incidenteel worden afgesloten, moet een statisch IP-adres worden toegewezen aan de netwerk interfaces van deze virtuele machines in de always-configuratie (het definiëren van een statisch IP-adres wordt beschreven in [Dit][virtual-networks-reserved-private-ip] artikel).
* Er zijn speciale stappen vereist bij het bouwen van de WSFC-cluster configuratie waarbij een speciaal IP-adres aan het cluster moet worden toegewezen, omdat Azure met de huidige functionaliteit de cluster naam zou toewijzen aan hetzelfde IP-adres als het knoop punt waarop het cluster is gemaakt. Dit gedrag houdt in dat een hand matige stap moet worden uitgevoerd om een ander IP-adres toe te wijzen aan het cluster.
* De listener voor de beschikbaarheids groep wordt gemaakt in azure met TCP/IP-eind punten, die zijn toegewezen aan de virtuele machines waarop de primaire en secundaire replica's van de beschikbaarheids groep worden uitgevoerd.
* Het moet mogelijk zijn om deze eind punten te beveiligen met Acl's.

Gedetailleerde documentatie over het implementeren van always on with SQL Server in azure Vm's-lijsten, zoals:

- [Introductie van SQL Server AlwaysOn-beschikbaarheids groepen op virtuele machines van Azure](../../../azure-sql/virtual-machines/windows/availability-group-overview.md).
- [Een always on-beschikbaarheids groep configureren op virtuele machines van Azure in verschillende regio's](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-multiple-regions.md).
- [Een Load Balancer configureren voor een AlwaysOn-beschikbaarheids groep in azure](../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md).

>[!NOTE]
> Als u de Azure-load balancer configureert voor het virtuele IP-adres van de listener van de beschikbaarheids groep, controleert u of de DirectServerReturn is geconfigureerd. Als u deze optie configureert, wordt de netwerk retour latentie tussen de SAP-toepassingsobjectlaag en de DBMS-laag verminderd. 

SQL Server always on is de meest voorkomende gebruikte functionaliteit voor hoge Beschik baarheid en herstel na nood gevallen die wordt gebruikt in azure voor SAP-werkbelasting implementaties. De meeste klanten gebruiken altijd voor hoge Beschik baarheid binnen één Azure-regio. Als de implementatie is beperkt tot twee knoop punten, hebt u twee opties voor de connectiviteit:

- De beschikbaarheids groep-listener gebruiken. Met de beschikbaarheids groep-listener moet u een Azure-load balancer implementeren. Op deze manier wordt de standaard implementatie methode gebruikt. SAP-toepassingen worden geconfigureerd om verbinding te maken met de beschikbaarheids groep-listener en niet op één knoop punt
- Met de verbindings parameters van SQL Server database spiegeling. In dit geval moet u de connectiviteit van de SAP-toepassingen configureren op een manier waar beide namen van knoop punten worden genoemd. Nauw keurige Details van een dergelijke SAP-configuratie worden beschreven in SAP Note [#965908](https://launchpad.support.sap.com/#/notes/965908). Als u deze optie gebruikt, hoeft u geen listener voor een beschikbaarheids groep te configureren. En zonder Azure-load balancer voor de SQL Server hoge Beschik baarheid. Als gevolg hiervan is de netwerk latentie tussen de SAP-toepassingsobjectlaag en de DBMS laag lager, omdat het binnenkomende verkeer naar het SQL Server-exemplaar niet via de Azure-load balancer wordt doorgestuurd. Maar intrekken, werkt deze optie alleen als u de beschikbaarheids groep beperkt tot twee exemplaren. 

Er zijn veel klanten die gebruikmaken van de SQL Server altijd on-functioneel voor extra functionaliteit voor herstel na nood gevallen tussen Azure-regio's. Diverse klanten gebruiken ook de mogelijkheid om back-ups uit te voeren vanaf een secundaire replica. 

## <a name="sql-server-transparent-data-encryption"></a>SQL Server Transparent Data Encryption
Er zijn een aantal klanten die SQL Server [transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) gebruiken bij het implementeren van hun SAP SQL server-data bases in Azure. De functionaliteit van de SQL Server TDE wordt volledig ondersteund door SAP (Zie SAP Note [#1380493](https://launchpad.support.sap.com/#/notes/1380493)). 

### <a name="applying-sql-server-tde"></a>SQL Server TDE Toep assen
In gevallen waarin u een heterogene migratie uitvoert vanuit een ander DBMS, on-premises uitvoert naar Windows/SQL Server die in Azure worden uitgevoerd, moet u de lege doel database in SQL Server van tevoren maken. Als volgende stap past u SQL Server TDE-functionaliteit toe. Terwijl u nog steeds uw productie systeem op locatie uitvoert. De reden dat u in deze volg orde wilt uitvoeren, is dat het proces van het versleutelen van de lege data base heel even kan duren. De SAP-import processen worden vervolgens tijdens de downtime-fase geïmporteerd in de versleutelde data base. De overhead van importeren in een versleutelde data base heeft een manier om de data base te versleutelen na de export fase in de fase van de runtime. Er zijn negatieve ervaringen gemaakt bij het Toep assen van TDE met een SAP-werk belasting die boven op de data base wordt uitgevoerd. Daarom behandelt de aanbeveling de implementatie van TDE als een activiteit die moet worden uitgevoerd zonder SAP-werk belasting voor de betreffende data base.

In gevallen waarin u SAP SQL Server-data bases van on-premises naar Azure verplaatst, wordt u aangeraden de versleuteling te testen op de infra structuur die u het snelst kunt Toep assen. Houd daarbij de volgende feiten in acht:

- U kunt niet definiëren hoeveel threads worden gebruikt om gegevens versleuteling toe te passen op de-data base. Het aantal threads is aanzienlijk afhankelijk van het aantal schijf volumes waarvan de SQL Server gegevens en logboek bestanden worden gedistribueerd. Betekent de meer DISTINCT-volumes (stationsletters), hoe meer threads parallel worden uitgevoerd om de versleuteling uit te voeren. Een dergelijke configuratie is een conflict met een eerdere schijf configuratie suggestie op het bouwen van een of een kleiner aantal opslag ruimten voor de SQL Server database bestanden in azure Vm's. Een configuratie met een klein aantal volumes zou leiden tot een klein aantal threads dat de versleuteling uitvoert. Het versleutelen van één thread is het lezen van 64 KB-gebieden, versleutelt deze en schrijft vervolgens een record naar het transactie logboek bestand. Dit betekent dat de omvang is versleuteld. Als gevolg hiervan wordt de belasting van het transactie logboek gematigd.
- In oudere versies van SQL Server heeft back-upcompressie geen efficiency meer meer wanneer u uw SQL Server-Data Base versleutelde. Dit gedrag kan zich voordoen als uw abonnement uw SQL Server-data base on-premises versleutelt en vervolgens een back-up naar Azure kopieert om de data base in azure te herstellen. SQL Server back-upcompressie wordt meestal een compressie ratio van factor 4 gerealiseerd.
- Met SQL Server 2016 heeft SQL Server nieuwe functionaliteit geïntroduceerd waarmee versleutelde data bases op een efficiënte manier kunnen worden gecomprimeerd. Zie [deze blog](/archive/blogs/sqlcat/sqlsweet16-episode-1-backup-compression-for-tde-enabled-databases) voor meer informatie.
 
Als u de toepassing van TDE-versleuteling zonder of weinig SAP-werk belasting wilt behandelen, moet u in uw specifieke configuratie testen of het beter is om TDE toe te passen op uw SAP-data base on-premises of in Azure. In azure hebt u meer flexibiliteit met betrekking tot de infra structuur over het inrichten en het verkleinen van de infra structuur nadat TDE is toegepast.

### <a name="using-azure-key-vault"></a>Azure Key Vault gebruiken
Azure biedt de service van een [Key Vault](https://azure.microsoft.com/services/key-vault/) om versleutelings sleutels op te slaan. SQL Server aan de andere kant bieden een connector om gebruik te maken van Azure Key Vault als Store voor de TDE-certificaten.

Meer informatie over het gebruik van Azure Key Vault voor SQL Server TDE-lijsten, zoals:

- [Extensible Key Management met behulp van Azure Key Vault (SQL Server)](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).
- [SQL Server TDe voor uitbreid bare sleutels met behulp van Azure Key Vault-installatie stappen](/sql/relational-databases/security/encryption/setup-steps-for-extensible-key-management-using-the-azure-key-vault).
- [SQL Server-connector onderhoud & probleem oplossing](/sql/relational-databases/security/encryption/sql-server-connector-maintenance-troubleshooting).
- [Meer vragen van klanten over SQL Server transparent Data Encryption – TDe + Azure Key Vault](/archive/blogs/saponsqlserver/more-questions-from-customers-about-sql-server-transparent-data-encryption-tde-azure-key-vault).


>[!IMPORTANT]
>Met SQL Server TDE, met name met Azure Key kluis, is het raadzaam om de meest recente patches van SQL Server 2014, SQL Server 2016 en SQL Server 2017 te gebruiken. Reden is dat op basis van feedback van klanten, optimalisaties en oplossingen die zijn toegepast op de code. Controleer bijvoorbeeld [KBA #4058175](https://support.microsoft.com/help/4058175/tde-enabled-backup-and-restore-slow-if-encryption-key-is-stored-in-ekm).
>  

## <a name="general-sql-server-for-sap-on-azure-summary"></a><a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Algemene SQL Server voor SAP on Azure samen vatting
Er zijn veel aanbevelingen in deze hand leiding. u wordt aangeraden deze meer dan één keer te lezen voordat u uw Azure-implementatie plant. In het algemeen moet u er echter voor zorgen dat u de meest algemene DBMS volgt op de aanbevelingen van Azure:

1. Gebruik de meest recente DBMS-versie, zoals SQL Server 2017, die de meeste voor delen heeft in Azure. 
2. Plan zorgvuldig uw SAP-systeem in azure om de indeling van het gegevens bestand en Azure-beperkingen te verdelen:
   * Zorg dat u niet te veel schijven hebt, maar voldoende hebt om ervoor te zorgen dat u uw vereiste IOPS kunt bereiken.
   * Als u Managed Disks niet gebruikt, moet u er rekening mee houden dat IOPS ook beperkt zijn per Azure Storage account en dat opslag accounts beperkt zijn binnen elk Azure-abonnement ([meer informatie][azure-resource-manager/management/azure-subscription-service-limits]). 
   * Sleep alleen over schijven als u een hogere door voer wilt uitvoeren.
3. Nooit software installeren of bestanden die persistentie op de D:\ vereisen, plaatsen station omdat het niet permanent is en alles op dit station verloren is gegaan bij het opnieuw opstarten van Windows.
4. Gebruik schijf cache niet voor Azure Standard-opslag.
5. Gebruik Azure Standard-opslag accounts met geo-replicatie niet.  Gebruik lokaal redundant voor DBMS-workloads.
6. Gebruik de HA/DR-oplossing van uw DBMS-leverancier om database gegevens te repliceren.
7. Gebruik altijd naam omzetting, vertrouw niet op IP-adressen.
8. Gebruik SQL Server TDE om de meest recente SQL Server patches toe te passen.
9. Gebruik de hoogste database compressie mogelijk. Wat is pagina compressie voor SQL Server.
10. Wees voorzichtig met het gebruik van SQL Server installatie kopieën van de Azure Marketplace. Als u de SQL Server één gebruikt, moet u de exemplaar sortering wijzigen voordat u een SAP NetWeaver-systeem installeert.
11. Installeer en configureer de SAP host-bewaking voor Azure, zoals beschreven in de [implementatie handleiding][deployment-guide].


## <a name="next-steps"></a>Volgende stappen
Lees het artikel 

- [Overwegingen voor de implementatie van Azure Virtual Machines DBMS voor SAP-workloads](dbms_guide_general.md)