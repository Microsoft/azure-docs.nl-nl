---
title: Implementatie van Oracle Azure Virtual Machines DBMS voor SAP-workload | Microsoft Docs
description: DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor Oracle
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, azure, Oracle, Data Guard
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/18/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b4cf2e79acf4cd58ff94a2e90f07202341672a1d
ms.sourcegitcommit: 9d9221ba4bfdf8d8294cf56e12344ed05be82843
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98569433"
---
# <a name="azure-virtual-machines-oracle-dbms-deployment-for-sap-workload"></a>Azure Virtual Machines Oracle DBMS-implementatie voor SAP-workload

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


Dit document bevat verschillende gebieden waarmee u rekening moet houden wanneer u Oracle Database voor SAP-werk belasting in azure IaaS implementeert. Voordat u dit document leest, raden we u aan Lees [overwegingen voor Azure virtual machines DBMS-implementatie voor SAP-werk belasting](dbms_guide_general.md)te lezen. We raden u ook aan andere hand leidingen in de [SAP-workload op de Azure-documentatie](./get-started.md)te lezen. 

U vindt informatie over Oracle-versies en de bijbehorende versies van besturings systemen die worden ondersteund voor het uitvoeren van SAP op Oracle op Azure in SAP Note [2039619].

Algemene informatie over het uitvoeren van SAP Business Suite op Oracle vindt u op [SAP op Oracle](https://www.sap.com/community/topic/oracle.html).
Oracle-software wordt door Oracle ondersteund om te worden uitgevoerd op Microsoft Azure. Raadpleeg de [Veelgestelde vragen over Oracle en Microsoft Azure](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)voor meer informatie over algemene ondersteuning voor Windows Hyper-V en Azure. 

## <a name="sap-notes-relevant-for-oracle-sap-and-azure"></a>SAP-opmerkingen die relevant zijn voor Oracle, SAP en Azure 

De volgende SAP-opmerkingen zijn gerelateerd aan SAP on Azure.

| Nummer van notitie | Titel |
| --- | --- |
| [1928533] |SAP-toepassingen op Azure: ondersteunde producten en Azure VM-typen |
| [2015553] |SAP op Microsoft Azure: vereisten voor ondersteuning |
| [1999351] |Problemen met verbeterde Azure-bewaking voor SAP oplossen |
| [2178632] |Belangrijkste meet waarden voor SAP op Microsoft Azure |
| [2191498] |SAP op Linux met Azure: uitgebreide bewaking |
| [2039619] |SAP-toepassingen op Microsoft Azure met behulp van de Oracle-Data Base: ondersteunde producten en versies |
| [2243692] |Linux on Microsoft Azure (IaaS) VM: SAP-licentie problemen |
| [2069760] |Oracle Linux 7. x SAP-installatie en-upgrade |
| [1597355] |Aanbeveling voor wissel geheugen voor Linux |
| [2171857] |Ondersteuning voor Oracle Database 12c-bestands systeem op Linux |
| [1114181] |Ondersteuning voor Oracle Database 11g-bestands systeem op Linux |

De exacte configuraties en functionaliteit die worden ondersteund door Oracle en SAP on Azure worden beschreven in SAP Note [#2039619](https://launchpad.support.sap.com/#/notes/2039619).

Windows en Oracle Linux zijn de enige besturings systemen die worden ondersteund door Oracle en SAP on Azure. De veelgebruikte SLES-en RHEL Linux-distributies worden niet ondersteund voor het implementeren van Oracle-onderdelen in Azure. Oracle-onderdelen bevatten de Oracle Database-client, die door SAP-toepassingen wordt gebruikt om verbinding te maken met de Oracle DBMS. 

Uitzonde ringen, volgens SAP-notitie [#2039619](https://launchpad.support.sap.com/#/notes/2039619), zijn SAP-onderdelen die de Oracle database-client niet gebruiken. Dergelijke SAP-onderdelen zijn zelfstandige in de wachtrij van SAP, berichten server, replicatie Services in de wachtrij, webdispatcher en SAP-gateway.  

Zelfs als u uw Oracle DBMS-en SAP-toepassings exemplaren uitvoert op Oracle Linux, kunt u uw SAP Central-Services uitvoeren op SLES of RHEL en deze beveiligen met een pacemaker-cluster. Pacemaker als een Framework met hoge Beschik baarheid wordt niet ondersteund op Oracle Linux.

## <a name="specifics-for-oracle-database-on-windows"></a>Details voor Oracle Database in Windows

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-windows"></a>Oracle-configuratie richtlijnen voor SAP-installaties in azure-Vm's in Windows

In overeenstemming met de SAP-installatie handleiding moeten Oracle-gerelateerde bestanden niet worden geïnstalleerd of zich bevinden op de besturingssysteem schijf van de virtuele machine (station c:). Virtuele machines met verschillende grootten kunnen een wisselend aantal gekoppelde schijven ondersteunen. Kleinere typen virtuele machines kunnen een kleiner aantal gekoppelde schijven ondersteunen. 

Als u kleinere vm's hebt en de limiet bereikt van het aantal schijven dat u aan de virtuele machine kunt koppelen, kunt u Oracle Home, stage,,, `saptrace` ,, `saparch` `sapbackup` `sapcheck` of `sapreorg` naar de besturingssysteem schijf installeren. Deze onderdelen van Oracle DBMS-onderdelen zijn niet te intensief op I/O en I/O-door voer. Dit betekent dat de besturingssysteem schijf de I/O-vereisten kan verwerken. De standaard grootte van de besturingssysteem schijf moet 127 GB zijn. 

Logboek bestanden Oracle Database en opnieuw moeten worden opgeslagen op afzonderlijke gegevens schijven. Er is een uitzonde ring voor de tijdelijke tabel ruimte van Oracle. `Tempfiles` kan worden gemaakt op D:/ (niet-permanente schijf). De niet-permanente D:\ Station biedt ook betere I/O-latentie en door Voer (met uitzonde ring van Vm's van de A-serie). 

Als u de juiste hoeveelheid ruimte voor de `tempfiles` wilt bepalen, kunt u de grootte van de `tempfiles` op bestaande systemen controleren.

### <a name="storage-configuration"></a>Opslagconfiguratie
Alleen Oracle met één instantie die NTFS-geformatteerde schijven gebruikt, wordt ondersteund. Alle database bestanden moeten worden opgeslagen in het NTFS-bestands systeem op Managed Disks (aanbevolen) of op Vhd's. Deze schijven zijn gekoppeld aan de virtuele machine van Azure en zijn gebaseerd op [Azure page Blob Storage](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) of [Azure Managed disks](../../managed-disks-overview.md). 

Bekijk het artikel [Azure Storage typen voor SAP-werk belasting](./planning-guide-storage.md) voor meer informatie over de specifieke Azure Block-opslag typen die geschikt zijn voor DBMS-workloads.

Het is raadzaam [Azure Managed disks](../../managed-disks-overview.md)te gebruiken. We raden u ook ten zeerste aan [Azure Premium Storage of Azure Ultra disk te](../../disks-types.md) gebruiken voor uw Oracle database-implementaties.

Netwerk stations of externe shares zoals Azure File Services worden niet ondersteund voor Oracle Database-bestanden. Zie voor meer informatie:

- [Introducing Microsoft Azure File Service (Introductie van Microsoft Azure File-service)](/archive/blogs/windowsazurestorage/introducing-microsoft-azure-file-service)

- [Persisting connections to Microsoft Azure Files (Permanente verbindingen met Microsoft Azure-bestanden)](/archive/blogs/windowsazurestorage/persisting-connections-to-microsoft-azure-files)


Als u schijven gebruikt die zijn gebaseerd op Azure page Blob Storage of Managed Disks, zijn de instructies [voor de implementatie van azure virtual machines DBMS voor SAP-werk belasting](dbms_guide_general.md) ook van toepassing op implementaties met Oracle database.

Quota voor IOPS-door Voer voor Azure-schijven bestaan. Dit concept wordt uitgelegd in [overwegingen voor Azure virtual machines DBMS-implementatie voor SAP-workload](dbms_guide_general.md). De exacte quota's zijn afhankelijk van het VM-type dat u gebruikt. Een lijst met VM-typen met hun quota vindt u [in groottes voor virtuele Windows-machines in azure][virtual-machines-sizes-windows].

Zie SAP Note [1928533]om de ondersteunde typen Azure VM te identificeren.

De minimale configuratie is als volgt: 

| Onderdeel | Schijf | Caching | Opslag groep |
| --- | ---| --- | --- |
| \oracle \<SID> \origlogaA & mirrlogB | Premium of Ultra Disk | Geen | Niet nodig |
| \oracle \<SID> \origlogaB & mirrlogA | Premium of Ultra Disk | Geen | Niet nodig |
| \oracle \<SID> \sapdata1... nvt | Premium of Ultra Disk | Alleen-lezen | Kan worden gebruikt voor Premium |
| \oracle \<SID> \oraarch | Standard | Geen | Niet nodig |
| Oracle Home, `saptrace` ,... | BESTURINGSSYSTEEM schijf (Premium) | | Niet nodig |


Schijven selecteren voor het hosten van online logboeken voor opnieuw uitvoeren moet worden aangestuurd door IOPS-vereisten. Het is mogelijk om alle sapdata1 op te slaan... n (tablespaces) op één gekoppelde schijf zolang de grootte, IOPS en door Voer voldoen aan de vereisten. 

De configuratie van de prestaties is als volgt:

| Onderdeel | Schijf | Caching | Opslag groep |
| --- | ---| --- | --- |
| \oracle \<SID> \origlogaA | Premium of Ultra Disk | Geen | Kan worden gebruikt voor Premium  |
| \oracle \<SID> \origlogaB | Premium of Ultra Disk | Geen | Kan worden gebruikt voor Premium |
| \oracle \<SID> \mirrlogAB | Premium of Ultra Disk | Geen | Kan worden gebruikt voor Premium |
| \oracle \<SID> \mirrlogBA | Premium of Ultra Disk | Geen | Kan worden gebruikt voor Premium |
| \oracle \<SID> \sapdata1... nvt | Premium of Ultra Disk | Alleen-lezen | Aanbevolen voor Premium  |
| \oracle\SID\sapdata (n + 1) * | Premium of Ultra Disk | Geen | Kan worden gebruikt voor Premium |
| \oracle \<SID> \oraarch * | Premium of Ultra Disk | Geen | Niet nodig |
| Oracle Home, `saptrace` ,... | BESTURINGSSYSTEEM schijf (Premium) | Niet nodig |

* (n + 1): host systeem, TEMP en ongedaan maken van tablespaces. Het I/O-patroon van systeem-en ongedaan maken tablespaces verschillen van andere tablespaces-hosting toepassings gegevens. Caching is niet de beste optie voor het uitvoeren van de prestaties van het systeem en het ongedaan maken van tablespaces.

* oraarch: opslag groep is niet nodig van het prestatie punt van de weer gave. Het kan worden gebruikt om meer ruimte te krijgen.

Als er meer IOPS vereist zijn in het geval van Azure Premium-opslag, raden we u aan Windows-opslag groepen (alleen beschikbaar in Windows Server 2012 en hoger) te gebruiken om één groot logisch apparaat te maken voor meerdere gekoppelde schijven. Deze aanpak vereenvoudigt de beheer overhead voor het beheren van de schijf ruimte en helpt u bij het hand matig distribueren van bestanden op meerdere gekoppelde schijven te voor komen.


#### <a name="write-accelerator"></a>Write Accelerator
Voor virtuele machines uit de M-serie van Azure kan de latentie die in de online logboeken voor opnieuw uitvoeren wordt geschreven, worden verminderd met factoren in vergelijking met Azure Premium Storage. Schakel Azure Write Accelerator in voor de schijven (Vhd's) op basis van Azure Premium Storage die worden gebruikt voor online logboek bestanden voor opnieuw uitvoeren. Zie [Write Accelerator](../../how-to-enable-write-accelerator.md)voor meer informatie. Of gebruik Azure Ultra disk voor het online logboek volume voor opnieuw uitvoeren.


### <a name="backuprestore"></a>Back-up/herstel
Voor de functionaliteit voor back-up/herstel worden de SAP BR *-Hulpprogram Ma's voor Oracle ondersteund op dezelfde manier als bij de standaard Windows Server-besturings systemen. Oracle Recovery Manager (RMAN) wordt ook ondersteund voor back-ups naar schijf en terugzetten van schijf.

U kunt Azure Backup ook gebruiken om een toepassings consistente back-up van de virtuele machine uit te voeren. In het artikel wordt [uw VM-back-upinfrastructuur gepland in azure](../../../backup/backup-azure-vms-introduction.md) wordt uitgelegd hoe Azure backup de Windows VSS-functionaliteit gebruikt voor het uitvoeren van toepassings consistente back-ups. De Oracle-DBMS-releases die worden ondersteund in azure door SAP, kunnen gebruikmaken van de VSS-functionaliteit voor back-ups. Zie de [basis concepten van de Oracle-documentatie van database back-ups en-herstel met VSS](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/ntqrf/basic-concepts-of-database-backup-and-recovery-with-vss.html#GUID-C085101B-237F-4773-A2BF-1C8FD040C701)voor meer informatie.


### <a name="high-availability"></a>Hoge beschikbaarheid
Oracle Data Guard wordt ondersteund voor hoge Beschik baarheid en herstel na nood gevallen. Als u automatische failover in Data Guard wilt uitvoeren, moet u Fast-Start failover (FSFA) gebruiken. De waarnemer (FSFA) activeert de failover. Als u FSFA niet gebruikt, kunt u alleen een hand matige failover-configuratie gebruiken.

Zie [herstel na nood gevallen voor een Oracle database 12c-data base in een Azure-omgeving](../oracle/oracle-disaster-recovery.md)voor meer informatie over herstel na nood gevallen voor Oracle-data bases in Azure.

### <a name="accelerated-networking"></a>Versneld netwerken
Voor Oracle-implementaties in Windows wordt het beste versneld netwerken aanbevolen, zoals beschreven in [Azure versneld netwerken](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Houd ook rekening met de aanbevelingen die worden gedaan in [overwegingen voor Azure virtual machines DBMS-implementatie voor SAP-workloads](dbms_guide_general.md). 
### <a name="other"></a>Anders
[Overwegingen voor azure virtual machines DBMS-implementatie voor SAP-workload](dbms_guide_general.md) beschrijft andere belang rijke concepten met betrekking tot implementaties van vm's met Oracle database, inclusief Azure-beschikbaarheids sets en SAP-bewaking.

## <a name="specifics-for-oracle-database-on-oracle-linux"></a>Details voor Oracle Database op Oracle Linux
Oracle-software wordt door Oracle ondersteund om te worden uitgevoerd op Microsoft Azure met Oracle Linux als gast besturingssysteem. Zie de [Veelgestelde vragen over Azure en Oracle](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)voor meer informatie over algemene ondersteuning voor Windows Hyper-V en Azure. 

Het specifieke scenario van SAP-toepassingen met Oracle-data bases wordt ook ondersteund. Meer informatie vindt u in het volgende gedeelte van het document.

### <a name="oracle-version-support"></a>Ondersteuning voor Oracle-versie
Zie SAP Note [2039619]voor informatie over welke Oracle-versies en de bijbehorende versies van het besturings systeem worden ondersteund voor het uitvoeren van SAP op Oracle op Azure virtual machines.

Algemene informatie over het uitvoeren van SAP Business Suite op Oracle vindt u op de [pagina SAP op Oracle Community](https://www.sap.com/community/topic/oracle.html).

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-linux"></a>Oracle-configuratie richtlijnen voor SAP-installaties in azure Vm's op Linux

In overeenstemming met SAP-installatie handleidingen kunnen Oracle-gerelateerde bestanden niet worden geïnstalleerd of bevinden zich in systeem Stuur Programma's voor de opstart schijf van een virtuele machine. Verschillende grootten van virtuele machines ondersteunen een wisselend aantal gekoppelde schijven. Kleinere typen virtuele machines kunnen een kleiner aantal gekoppelde schijven ondersteunen. 

In dit geval kunt u het beste de installatie/locatie van Oracle Home, stage,,, `saptrace` `saparch` `sapbackup` , `sapcheck` of `sapreorg` naar de opstart schijf installeren. Deze onderdelen van Oracle DBMS-onderdelen zijn niet intens over I/O en I/O-door voer. Dit betekent dat de besturingssysteem schijf de I/O-vereisten kan verwerken. De standaard grootte van de besturingssysteem schijf is 30 GB. U kunt de opstart schijf uitbreiden met behulp van de Azure Portal, Power shell of CLI. Nadat de opstart schijf is uitgevouwen, kunt u een extra partitie voor Oracle binaire bestanden toevoegen.


### <a name="storage-configuration"></a>Opslagconfiguratie

De bestands systemen van ext4, xfs, NFSv 4.1 (alleen op Azure NetApp Files (ANF)) of Oracle ASM (Zie SAP Note [#2039619](https://launchpad.support.sap.com/#/notes/2039619) voor release/version-vereisten) worden ondersteund voor Oracle database-bestanden in Azure. Alle database bestanden moeten worden opgeslagen op deze bestands systemen op basis van Vhd's, Managed Disks of ANF. Deze schijven zijn gekoppeld aan de virtuele machine van Azure en zijn gebaseerd op [Azure page Blob Storage](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs), [azure Managed disks](../../managed-disks-overview.md)of [Azure NetApp files](https://azure.microsoft.com/services/netapp/).

Lijst met minimum vereisten, zoals: 

- Voor Oracle Linux UEK-kernels is mini maal UEK versie 4 vereist voor de ondersteuning van [Azure Premium ssd's](../../premium-storage-performance.md#disk-caching).
- Voor Oracle met ANF wordt de mini maal ondersteunde Oracle Linux 8,2.
- Voor Oracle met ANF wordt de mini maal ondersteunde Oracle-versie 19c (19.8.0.0)

Het artikel [Azure Storage typen voor SAP-werk belasting](./planning-guide-storage.md) uitchecken voor meer informatie over de specifieke Azure Block-opslag typen die geschikt zijn voor DBMS-workloads.

Met Azure Block Storage wordt het ten zeerste aanbevolen [Azure Managed disks](../../managed-disks-overview.md) en [Azure Premium ssd's](../../disks-types.md) te gebruiken voor uw Oracle database-implementaties.

Behalve Azure NetApp Files worden andere gedeelde schijven, netwerk stations of externe shares, zoals Azure File Services (AFS), niet ondersteund voor Oracle Database-bestanden. Raadpleeg de volgende artikelen voor meer informatie: 

- [Introducing Microsoft Azure File Service (Introductie van Microsoft Azure File-service)](/archive/blogs/windowsazurestorage/introducing-microsoft-azure-file-service)

- [Persisting connections to Microsoft Azure Files (Permanente verbindingen met Microsoft Azure-bestanden)](/archive/blogs/windowsazurestorage/persisting-connections-to-microsoft-azure-files)

Als u schijven gebruikt op basis van Azure-pagina-Blob-opslag of Managed Disks, zijn de instructies [voor de implementatie van azure virtual machines DBMS voor SAP-werk belasting](dbms_guide_general.md) ook van toepassing op implementaties met Oracle database.

Quota voor IOPS-door Voer voor Azure-schijven bestaan. Dit concept wordt uitgelegd in [overwegingen voor Azure virtual machines DBMS-implementatie voor SAP-workload](dbms_guide_general.md). De exacte quota's zijn afhankelijk van het type virtuele machine dat wordt gebruikt. Zie [grootten voor virtuele Linux-machines in azure][virtual-machines-sizes-linux]voor een lijst met VM-typen met hun quota.

Zie SAP Note [1928533]om de ondersteunde typen Azure VM te identificeren.

Minimale configuratie:

| Onderdeel | Schijf | Caching | Moeten ontdaan |
| --- | ---| --- | --- |
| /Oracle/ \<SID> /origlogaA & mirrlogB | Premium, Ultra disk of ANF | Geen | Niet nodig |
| /Oracle/ \<SID> /origlogaB & mirrlogA | Premium, Ultra disk of ANF | Geen | Niet nodig |
| /Oracle/ \<SID> /sapdata1... nvt | Premium, Ultra disk of ANF | Alleen-lezen | Kan worden gebruikt voor Premium |
| /Oracle/ \<SID> /oraarch | Standard-of ANF | Geen | Niet nodig |
| Oracle Home, `saptrace` ,... | BESTURINGSSYSTEEM schijf (Premium) | | Niet nodig |

* Verwijderen: LVM Stripe of MDADM met RAID0

De schijf selectie voor het hosten van het online opnieuw uitvoeren van Oracle-logboeken moet worden aangestuurd door IOPS-vereisten. Het is mogelijk om alle sapdata1 op te slaan... n (tablespaces) op één gekoppelde schijf zolang het volume, IOPS en door Voer voldoen aan de vereisten. 

Configuratie van prestaties:

| Onderdeel | Schijf | Caching | Moeten ontdaan |
| --- | ---| --- | --- |
| /Oracle/ \<SID> /origlogaA | Premium, Ultra disk of ANF | Geen | Kan worden gebruikt voor Premium  |
| /Oracle/ \<SID> /origlogaB | Premium, Ultra disk of ANF | Geen | Kan worden gebruikt voor Premium |
| /Oracle/ \<SID> /mirrlogAB | Premium, Ultra disk of ANF | Geen | Kan worden gebruikt voor Premium |
| /Oracle/ \<SID> /mirrlogBA | Premium, Ultra disk of ANF | Geen | Kan worden gebruikt voor Premium |
| /Oracle/ \<SID> /sapdata1... nvt | Premium, Ultra disk of ANF | Alleen-lezen | Aanbevolen voor Premium  |
| /Oracle/ \<SID> /sapdata (n + 1) * | Premium, Ultra disk of ANF | Geen | Kan worden gebruikt voor Premium |
| /Oracle/ \<SID> /oraarch * | Premium, Ultra disk of ANF | Geen | Niet nodig |
| Oracle Home, `saptrace` ,... | BESTURINGSSYSTEEM schijf (Premium) | Niet nodig |

* Verwijderen: LVM Stripe of MDADM met RAID0

* (n + 1): hostsysteem, TEMP en ongedaan maken van tablespaces: het I/O-patroon van systeem en tablespaces ongedaan maken verschilt van andere tablespaces-hosting toepassings gegevens. Caching is niet de beste optie voor het uitvoeren van de prestaties van het systeem en het ongedaan maken van tablespaces.

* oraarch: opslag groep is niet nodig van het prestatie punt van de weer gave.


Als er meer IOPS vereist zijn bij het gebruik van Azure Premium Storage, raden we u aan LVM (Logical Volume Manager) of MDADM te gebruiken om één grote logische volume te maken op meerdere gekoppelde schijven. Zie [overwegingen voor Azure virtual machines DBMS-implementatie voor SAP-workload](dbms_guide_general.md) met betrekking tot richt lijnen en aanwijzers voor het gebruik van LVM of MDADM voor meer informatie. Deze aanpak vereenvoudigt de beheer overhead van het beheren van de schijf ruimte en helpt u om te voor komen dat bestanden hand matig worden gedistribueerd over meerdere gekoppelde schijven.

Als u Azure NetApp Files wilt gebruiken, moet u ervoor zorgen dat de dNFS-client correct is geconfigureerd. Het gebruik van dNFS is verplicht voor een ondersteunde omgeving. De configuratie van dNFS wordt beschreven in het artikel [een Oracle database maken op direct NFS](https://docs.oracle.com/en/database/oracle/oracle-database/19/ntdbi/creating-an-oracle-database-on-direct-nfs.html#GUID-2A0CCBAB-9335-45A8-B8E3-7E8C4B889DEA).

Een voor beeld van het gebruik van Azure NetApp Files NFS voor Oracle-data bases wordt weer gegeven in de blog [DEPLOY SAP AnyDB (Oracle 19c) met Azure NetApp files](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-anydb-oracle-19c-with-azure-netapp-files/ba-p/2064043).


#### <a name="write-accelerator"></a>Write Accelerator
Voor virtuele machines uit de M-serie van Azure, wanneer u Azure Write Accelerator gebruikt, kan de latentie die in de online logboeken voor opnieuw uitvoeren wordt geschreven, worden verminderd met de factoren die Azure Premium Storage gebruiken. Schakel Azure Write Accelerator in voor de schijven (Vhd's) op basis van Azure Premium Storage die worden gebruikt voor online logboek bestanden voor opnieuw uitvoeren. Zie [Write Accelerator](../../how-to-enable-write-accelerator.md)voor meer informatie. Of gebruik Azure Ultra disk voor het online logboek volume voor opnieuw uitvoeren.


### <a name="backuprestore"></a>Back-up/herstel
Voor de functionaliteit voor back-up/herstel worden de SAP BR *-Hulpprogram Ma's voor Oracle ondersteund op dezelfde manier als op bare metal-en Hyper-V. Oracle Recovery Manager (RMAN) wordt ook ondersteund voor back-ups naar schijf en terugzetten van schijf.

Zie een [back-up maken van een Oracle database 12c-Data Base op een virtuele machine van Azure Linux](../oracle/oracle-overview.md)voor meer informatie over hoe u Azure backup en herstel Services kunt gebruiken voor het maken van back-ups van Oracle-data bases en het herstellen ervan.

### <a name="high-availability"></a>Hoge beschikbaarheid
Oracle Data Guard wordt ondersteund voor hoge Beschik baarheid en herstel na nood gevallen. Als u automatische failover in Data Guard wilt uitvoeren, moet u Fast-Start failover (FSFA) gebruiken. De functionaliteit van de waarnemer (FSFA) activeert de failover. Als u FSFA niet gebruikt, kunt u alleen een hand matige failover-configuratie gebruiken. Zie [Oracle Data Guard implementeren op een virtuele machine van Azure Linux](../oracle/configure-oracle-dataguard.md)voor meer informatie.


Nood herstel aspecten voor Oracle-data bases in Azure worden weer gegeven in het artikel [herstel na nood gevallen voor een Oracle database 12c-data base in een Azure-omgeving](../oracle/oracle-disaster-recovery.md).

### <a name="accelerated-networking"></a>Versneld netwerken
Ondersteuning voor versneld Azure-netwerken in Oracle Linux wordt geboden met Oracle Linux 7 Update 5 (Oracle Linux 7,5). Als u niet kunt upgraden naar de nieuwste versie van Oracle Linux 7,5, is er mogelijk een tijdelijke oplossing met behulp van de RedHat-compatibele kernel (RHCK) in plaats van de Oracle UEK-kernel. 

Het gebruik van de RHEL-kernel binnen Oracle Linux wordt ondersteund volgens SAP Note [#1565179](https://launchpad.support.sap.com/#/notes/1565179). Voor versneld Azure-netwerken moet de minimale versie van de RHCKL-kernel 3.10.0-862.13.1. EL7 zijn. Als u de UEK-kernel in Oracle Linux gebruikt in combi natie met [Azure versneld netwerken](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/), moet u Oracle UEK kernel versie 5 gebruiken.

Als u Vm's implementeert vanuit een installatie kopie die niet is gebaseerd op Azure Marketplace, moet u aanvullende configuratie bestanden naar de virtuele machine kopiëren door de volgende code uit te voeren: 
<pre><code># Copy settings from GitHub to the correct place in the VM
sudo curl -so /etc/udev/rules.d/68-azure-sriov-nm-unmanaged.rules https://raw.githubusercontent.com/LIS/lis-next/master/hv-rhel7.x/hv/tools/68-azure-sriov-nm-unmanaged.rules 
</code></pre>


## <a name="next-steps"></a>Volgende stappen
Lees het artikel 

- [Overwegingen voor de implementatie van Azure Virtual Machines DBMS voor SAP-workloads](dbms_guide_general.md)
