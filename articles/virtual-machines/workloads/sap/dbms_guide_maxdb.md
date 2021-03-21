---
title: Implementatie van SAP MaxDB, liveCache en Content Server op virtuele Azure-machines | Microsoft Docs
description: Implementatie van SAP MaxDB, liveCache en Content Server op Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e15b592aaa780ea80b6931c6d44fbf99e2f3a730
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101673058"
---
# <a name="sap-maxdb-livecache-and-content-server-deployment-on-azure-vms"></a>Implementatie van SAP MaxDB, liveCache en Content Server op virtuele machines van Azure

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

[dbms-guide]:dbms-guide.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
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
[dbms-guide-figure-900]:media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png

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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/resources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
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




In dit document worden verschillende gebieden besproken waarmee u rekening moet houden bij het implementeren van MaxDB, liveCache en inhouds server in azure IaaS. Als een voor waarde voor dit document, moet u de document [overwegingen voor Azure virtual machines DBMS-implementatie van de SAP-werk belasting](dbms_guide_general.md) en andere hand leidingen in de [SAP-werk belasting op de Azure-documentatie](./get-started.md)lezen. 

## <a name="specifics-for-the-sap-maxdb-deployments-on-windows"></a>Details voor de SAP MaxDB-implementaties in Windows
### <a name="sap-maxdb-version-support-on-azure"></a>Ondersteuning voor SAP MaxDB-versie op Azure
SAP ondersteunt momenteel SAP MaxDB-versie 7,9 of hoger voor gebruik met SAP NetWeaver-gebaseerde producten in Azure. Alle updates voor SAP MaxDB-server-of JDBC-en ODBC-stuur Programma's die worden gebruikt met SAP NetWeaver-gebaseerde producten, worden uitsluitend via de SAP-Service Marketplace op beschreven <https://support.sap.com/swdc> .
Algemene informatie over het uitvoeren van SAP NetWeaver op SAP MaxDB vindt u op <https://www.sap.com/community/topic/maxdb.html> .

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>Ondersteunde micro soft Windows-versies en Azure VM-typen voor SAP MaxDB DBMS
Zie de volgende informatie om de ondersteunde micro soft Windows-versie voor SAP MaxDB DBMS op Azure te vinden:

* [SAP-product beschikbaarheids matrix (PAM)][sap-pam]
* SAP-opmerking [1928533]

Het is raadzaam om de nieuwste versie van het besturings systeem micro soft Windows te gebruiken. Dit is micro soft Windows 2016.

### <a name="available-sap-maxdb-documentation-for-maxdb"></a>Beschik bare SAP MaxDB-documentatie voor MaxDB
U kunt de bijgewerkte lijst met SAP MaxDB-documentatie vinden in de volgende SAP-notitie [767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP MaxDB-configuratie richtlijnen voor SAP-installaties in azure-Vm's
#### <a name="storage-configuration"></a><a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Opslagconfiguratie
Aanbevolen procedures voor Azure Storage voor SAP MaxDB Volg de algemene aanbevelingen die worden vermeld in de hoofd stuk [opslag structuur van een virtuele machine voor RDBMS-implementaties](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64).

> [!IMPORTANT]
> Net als andere data bases bevat SAP MaxDB ook gegevens en logboek bestanden. In SAP MaxDB is de juiste term echter "volume" (niet "bestand"). Er zijn bijvoorbeeld SAP MaxDB-gegevens volumes en logboek volumes. Verwar deze niet met besturingssysteem schijf volumes. 
> 
> 

Kortom, u hebt het volgende nodig:

* Als u Azure Storage accounts gebruikt, stelt u het Azure-opslag account in met de SAP MaxDB-gegevens en-logboek volumes (gegevens en logboek bestanden) naar **lokale redundante opslag (LRS)** , zoals is opgegeven in [overwegingen voor Azure virtual machines DBMS-implementatie voor SAP-workloads](dbms_guide_general.md).
* Scheid het IO-pad voor SAP MaxDB-gegevens volumes (gegevens bestanden) van het i/o-pad voor logboek volumes (logboek bestanden). Dit betekent dat SAP MaxDB-gegevens volumes (gegevens bestanden) op één logisch station moeten worden geïnstalleerd en dat logboek volumes van SAP MaxDB (logboek bestanden) moeten worden geïnstalleerd op een andere logische schijf.
* Stel het juiste cache type voor elke schijf in, afhankelijk van of u dit gebruikt voor SAP MaxDB-gegevens of-logboek volumes (gegevens-en logboek bestanden) en of u Azure Standard of Azure Premium Storage gebruikt, zoals beschreven in [overwegingen voor azure virtual machines DBMS-implementatie voor SAP-workloads](dbms_guide_general.md).
* Zolang het huidige quotum voor IOPS per schijf voldoet aan de vereisten, is het mogelijk om alle gegevens volumes op één gekoppelde schijf op te slaan en alle database logboek volumes op een andere, enkele gekoppelde schijf op te slaan.
* Als meer IOPS en/of ruimte vereist zijn, is het raadzaam micro soft Window-opslag groepen (alleen beschikbaar in micro soft Windows Server 2012 en hoger) te gebruiken om één groot logisch apparaat te maken voor meerdere gekoppelde schijven. Zie ook [aandachtspunten voor Azure virtual machines DBMS-implementatie voor SAP-werk belasting](dbms_guide_general.md)voor meer informatie. Deze aanpak vereenvoudigt de beheer overhead voor het beheren van de schijf ruimte en voor komt het hand matig distribueren van bestanden over meerdere gekoppelde schijven.
* het is raadzaam Azure Premium Storage te gebruiken voor MaxDB-implementaties. 

![Referentie configuratie van Azure IaaS VM voor SAP MaxDB DBMS](./media/dbms_maxdb_deployment_guide/Simple_disk_structure_maxdb.PNG)


#### <a name="backup-and-restore"></a><a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Back-ups maken en herstellen
Wanneer u SAP MaxDB in azure implementeert, moet u uw back-upmethodologie controleren. Zelfs als het systeem geen productief systeem is, moet u regel matig een back-up maken van de SAP-data base die wordt gehost door SAP MaxDB. Omdat Azure Storage drie installatie kopieën houdt, is een back-up nu minder belang rijk voor de bescherming van uw systeem tegen opslag storingen en belang rijke operationele of administratieve storingen. De belangrijkste reden voor het onderhouden van een juiste back-up-en herstel planning is dat u logische of hand matige fouten kunt compenseren door herstel mogelijkheden op basis van een bepaald tijdstip te bieden. Het doel is dus om back-ups te maken om de data base te herstellen naar een bepaald tijdstip of om de back-ups in azure te gebruiken voor het seeden van een ander systeem door de bestaande Data Base te kopiëren. 

Het maken van een back-up en het herstellen van een data base in azure werkt op dezelfde manier als voor on-premises systemen, zodat u Standard SAP MaxDB-hulpprogram ma's voor back-up/herstel kunt gebruiken, die worden beschreven in een van de SAP MaxDB-documentatie documenten die worden vermeld in SAP Note [767598]. 

#### <a name="performance-considerations-for-backup-and-restore"></a><a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Prestatie overwegingen voor back-up en herstel
Net als bij bare-metal implementaties zijn de prestaties van back-up en herstel afhankelijk van het aantal volumes dat parallel kan worden gelezen en de door Voer van die volumes. Daarom kan een ervan uitgaan:

* Hoe minder schijven er worden gebruikt om de database apparaten op te slaan, hoe lager de algemene Lees doorvoer
* Hoe minder doelen (Stripe-Directory's, schijven) om de back-up naar te schrijven, hoe lager de door Voer

Als u het aantal doelen wilt verg Roten om naar te schrijven, zijn er twee opties die u kunt gebruiken, eventueel in combi natie, afhankelijk van uw behoeften:

* Afzonderlijke volumes voor back-up reserveren
* Het back-updoel volume over meerdere gekoppelde schijven verwijderen om de IOPS-door Voer op het gestripte schijf volume te verbeteren
* Met afzonderlijke toegewezen logische schijf apparaten voor:
  * SAP MaxDB-back-upvolumes (bestanden)
  * SAP MaxDB-gegevens volumes (bijvoorbeeld bestanden)
  * SAP MaxDB-logboek volumes (bijvoorbeeld bestanden)

Het verwijderen van een volume over meerdere gekoppelde schijven is eerder besproken in [overwegingen bij de implementatie van Azure virtual machines DBMS voor SAP-workloads](dbms_guide_general.md). 

#### <a name="other-considerations"></a><a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Andere overwegingen
Alle andere algemene gebieden, zoals Azure-beschikbaarheids sets of SAP-bewaking, zijn ook van toepassing zoals beschreven in [overwegingen voor azure virtual machines DBMS-implementatie voor SAP-workloads](dbms_guide_general.md).  voor implementaties van Vm's met de SAP MaxDB-data base.
Andere SAP MaxDB-specifieke instellingen zijn transparant voor virtuele Azure-machines en worden beschreven in verschillende documenten die worden vermeld in SAP Note [767598] en in deze SAP-opmerkingen:

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-deployments-on-windows"></a>Details voor SAP liveCache-implementaties in Windows
### <a name="sap-livecache-version-support"></a>Ondersteuning voor SAP liveCache-versie
Minimale versie van SAP liveCache die wordt ondersteund in azure Virtual Machines is **SAP LC/LCAPPS 10,0 SP 25** inclusief **LiveCache 7.9.08.31** en **LCA-Build 25**, uitgebracht voor **EhP 2 voor SAP SCM 7,0** en latere releases.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>Ondersteunde micro soft Windows-versies en Azure VM-typen voor SAP liveCache DBMS
Zie voor informatie over de ondersteunde micro soft Windows-versie voor SAP liveCache op Azure:

* [SAP-product beschikbaarheids matrix (PAM)][sap-pam]
* SAP-opmerking [1928533]

Het is raadzaam om de nieuwste versie van het besturings systeem micro soft Windows Server te gebruiken. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP liveCache-configuratie richtlijnen voor SAP-installaties in azure-Vm's
#### <a name="recommended-azure-vm-types-for-livecache"></a>Aanbevolen Azure VM-typen voor liveCache
Als SAP-liveCache is een toepassing die enorme berekeningen uitvoert, zijn de hoeveelheid en snelheid van RAM en CPU een grote invloed op de prestaties van SAP liveCache. 

Voor de Azure VM-typen die worden ondersteund door SAP (SAP Note [1928533]), worden alle virtuele CPU-resources die aan de virtuele machine zijn toegewezen, ondersteund door toegewezen fysieke CPU-resources van de Hyper Visor. Er vindt geen overbelasting plaats (en daarom is er geen competitie voor CPU-Resources).

Op dezelfde manier wordt voor alle typen Azure-VM-exemplaren die worden ondersteund door SAP, het geheugen van de virtuele machine 100% toegewezen aan de fysieke geheugen-overinrichting (meer dan vastleg ging), bijvoorbeeld niet.

Vanuit dit perspectief wordt het ten zeerste aanbevolen om de meest recente Vm's voor dv2, Dv3, Ev3 en M-Series te gebruiken. De keuze van de verschillende VM-typen is afhankelijk van het geheugen dat u nodig hebt voor liveCache en de CPU-bronnen die u nodig hebt. Net als bij alle andere DBMS-implementaties is het raadzaam Azure Premium Storage te gebruiken voor essentiële volumes van de prestaties.

#### <a name="storage-configuration-for-livecache-in-azure"></a>Opslag configuratie voor liveCache in azure
Als SAP liveCache is gebaseerd op de SAP MaxDB-technologie, zijn alle Azure Storage-best practice aanbevelingen die in dit document worden genoemd, ook geldig voor SAP liveCache. 

#### <a name="dedicated-azure-vm-for-livecache-scenario"></a>Specifieke Azure VM voor liveCache-scenario
Als SAP liveCache intensief gebruikmaakt van reken kracht, is het raadzaam om te implementeren op een specifieke virtuele machine van Azure. 

![Exclusieve Azure VM voor liveCache voor productief gebruik](./media/dbms_maxdb_deployment_guide/700-livecach-prod.PNG)


#### <a name="backup-and-restore-for-livecache-in-azure"></a>Back-ups maken en herstellen voor liveCache in azure
back-ups maken en herstellen, met inbegrip van prestatie overwegingen, worden in de relevante SAP MaxDB-hoofd stukken in dit document al beschreven. 

#### <a name="other-considerations"></a>Andere overwegingen
Alle andere algemene gebieden worden al beschreven in het relevante SAP MaxDB-hoofd stuk. 

## <a name="specifics-for-the-sap-content-server-deployment-on-windows-in-azure"></a>Details voor de implementatie van de SAP-inhouds server in Windows in azure
De SAP-inhouds server is een afzonderlijk op server gebaseerd onderdeel voor het opslaan van inhoud zoals elektronische documenten in verschillende indelingen. De SAP-inhouds server wordt ontwikkeld door ontwikkeling van technologie en moet worden gebruikt voor elke SAP-toepassing. Deze wordt geïnstalleerd op een afzonderlijk systeem. Typische inhoud is trainings materiaal en documentatie van kennis magazijn of technische tekeningen afkomstig van het mySAP PLM-document beheersysteem. 

### <a name="sap-content-server-version-support-for-azure-vms"></a>Versie ondersteuning voor SAP-inhouds server voor Azure-Vm's
SAP ondersteunt momenteel:

* **SAP-inhouds server** met versie **6,50 (en hoger)**
* **SAP MaxDB-versie 7,9**
* **Micro soft IIS (Internet Information Server) versie 8,0 (en hoger)**

Het is raadzaam om de nieuwste versie van de SAP-inhouds server en de nieuwste versie van **micro soft IIS** te gebruiken. 

Controleer de meest recente ondersteunde versies van SAP-inhouds server en micro soft IIS in de [SAP-product beschikbaarheids matrix (PAM)][sap-pam].

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>Ondersteunde typen micro soft Windows-en Azure VM voor SAP-inhouds server
Zie voor meer informatie over de ondersteunde Windows-versie voor SAP-inhouds server op Azure:

* [SAP-product beschikbaarheids matrix (PAM)][sap-pam]
* SAP-opmerking [1928533]

Het is raadzaam om de nieuwste versie van micro soft Windows Server te gebruiken.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Richt lijnen voor de configuratie van SAP-inhouds servers voor SAP-installaties in azure-Vm's
#### <a name="storage-configuration-for-content-server-in-azure"></a>Opslag configuratie voor de inhouds server in azure
Als u SAP-inhouds server configureert om bestanden op te slaan in de SAP MaxDB-data base, zijn alle aanbevelingen voor de aanbevolen procedures voor Azure Storage voor SAP MaxDB in dit document ook geldig voor het SAP-inhouds Server scenario. 

Als u de SAP-inhouds server configureert voor het opslaan van bestanden in het bestands systeem, wordt u aangeraden een specifiek logisch station te gebruiken. Met Windows-opslag ruimten kunt u ook de grootte van de logische schijf en IOPS-door Voer verhogen, zoals beschreven in [overwegingen voor Azure virtual machines DBMS-implementatie voor SAP-workload](dbms_guide_general.md). 

#### <a name="sap-content-server-location"></a>Locatie van SAP-inhouds server
SAP-inhouds server moet worden geïmplementeerd in dezelfde Azure-regio en Azure VNET waar het SAP-systeem wordt geïmplementeerd. U kunt beslissen of u onderdelen van de SAP-inhouds server wilt implementeren op een specifieke virtuele machine van Azure of op dezelfde VM waarop het SAP-systeem wordt uitgevoerd. 

![Exclusieve Azure-VM voor SAP-inhouds server](./media/dbms_maxdb_deployment_guide/800-azure-vm-sap-content-server.png)


#### <a name="sap-cache-server-location"></a>Locatie van SAP-cache server
De SAP-cache server is een aanvullend Server onderdeel voor het lokaal openen van documenten (in de cache). De SAP-cache server slaat de documenten van een SAP-inhouds server op in de cache. Dit is om netwerk verkeer te optimaliseren als documenten meer dan één keer van verschillende locaties moeten worden opgehaald. De algemene regel is dat de SAP-cache server fysiek moet worden afgesloten met de client die toegang heeft tot de SAP-cache server. 

Hier hebt u twee opties:

1. **Client is een back-end SAP-systeem** Als een back-end SAP-systeem is geconfigureerd voor toegang tot de SAP-inhouds server, is dat SAP-systeem een-client. Omdat SAP systeem-en SAP-inhouds server worden geïmplementeerd in dezelfde Azure-regio, in hetzelfde Azure-Data Center, zijn ze fysiek dicht bij elkaar. Daarom is er geen speciale SAP-cache server nodig. SAP-gebruikers interface-clients (SAP-GUI of webbrowser) hebben rechtstreeks toegang tot het SAP-systeem en het SAP-systeem haalt documenten op van de SAP-inhouds server.
2. **De client is een on-premises webbrowser** De SAP-inhouds server kan worden geconfigureerd om rechtstreeks te worden geopend door de webbrowser. In dit geval is een webbrowser die on-premises wordt uitgevoerd, een client van de SAP-inhouds server. On-premises Data Center en Azure Data Center worden op verschillende fysieke locaties geplaatst (in het ideale geval dicht bij elkaar). Uw on-premises Data Center is verbonden met Azure via Azure site-to-site VPN of ExpressRoute. Hoewel beide opties een beveiligde VPN-netwerk verbinding naar Azure bieden, biedt site-naar-site-netwerk verbinding geen netwerk bandbreedte en latentie SLA tussen het on-premises Data Center en het Azure-Data Center. Als u de toegang tot documenten wilt versnellen, kunt u een van de volgende handelingen uitvoeren:
   1. Installeer de SAP-cache server on-premises, sluit de on-premises webbrowser (optie in afbeelding hieronder)
   2. Configureer Azure ExpressRoute. Dit biedt een speciale netwerk verbinding met hoge snelheid en lage latentie tussen on-premises Data Center en Azure Data Center.

![Optie voor het on-premises installeren van een SAP-cache server](./media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png)
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Back-up maken/herstellen
Als u de SAP-inhouds server configureert om bestanden op te slaan in de SAP MaxDB-data base, worden de procedures voor het maken en herstellen van back-ups en prestaties al beschreven in de hoofd stukken van SAP MaxDB van dit document. 

Als u de SAP-inhouds server configureert voor het opslaan van bestanden in het bestands systeem, moet u een hand matige back-up/herstel bewerking uitvoeren van de volledige bestands structuur waarin de documenten zich bevinden. Net als bij SAP MaxDB Backup/Restore, wordt u aangeraden een speciaal schijf volume te hebben voor back-updoeleinden. 

#### <a name="other"></a>Anders
Andere instellingen voor de SAP-inhouds server zijn transparant voor virtuele Azure-machines en worden beschreven in diverse documenten en SAP-opmerkingen:

* <https://service.sap.com/contentserver> 
* SAP-opmerking [1619726]  
