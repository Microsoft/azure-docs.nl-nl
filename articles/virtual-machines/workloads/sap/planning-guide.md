---
title: 'SAP on Azure: hand leiding voor planning en implementatie'
description: Azure Virtual Machines planning en implementatie voor SAP net-Weaver
author: MSSedusch
manager: juergent
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/17/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: bd45b0e1070efae7ae69a74ad96e1fa94a136006
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "96019392"
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>Azure Virtual Machines planning en implementatie voor SAP net-Weaver

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
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

[msdn-set-Azvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
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

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-az-ps
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
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md
[storage-premium-storage-preview-portal]:../../disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines/windows/index.yml
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-windows.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-windows-capture-image-resource-manager]:../../windows/capture-image.md
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-create-upload-vhd-oracle]:../../linux/oracle-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#format-and-mount-the-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics-windows]:../../windows/multiple-nics.md
[virtual-networks-multiple-nics-linux]:../../linux/multiple-nics.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-about-vpngateways.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md
[capture-image-linux-step-2-create-vm-image]:../../linux/capture-image.md#step-2-create-vm-image



Met Microsoft Azure kunnen bedrijven reken-en opslag resources binnen minimale tijd verkrijgen zonder langdurige aankoop cycli. Met de Azure Virtual Machine-Service kunnen bedrijven klassieke toepassingen implementeren, zoals toepassingen op basis van SAP netweave in Azure en hun betrouw baarheid en beschik baarheid uitbreiden zonder dat er meer bronnen on-premises beschikbaar zijn. Azure virtual machine Services biedt ook ondersteuning voor cross-premises connectiviteit, waarmee bedrijven Azure Virtual Machines actief kunnen integreren in hun on-premises domeinen, hun persoonlijke Clouds en hun SAP-systeem.
In dit technisch document worden de grond beginselen van Microsoft Azure virtuele machine beschreven en vindt u een overzicht van de plannings-en implementatie overwegingen voor SAP NetWeaver-installaties in Azure. dit moet als zodanig worden gelezen voordat de werkelijke implementaties van SAP NetWeaver op Azure worden gestart.
Het artikel vormt een aanvulling op de SAP-installatie documentatie en SAP-notities, die de primaire resources vertegenwoordigen voor installaties en implementaties van SAP-software op bepaalde platformen.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="summary"></a>Samenvatting
Cloud Computing is een veelgebruikte periode, die meer en meer belang rijker is dan de IT-branche, van kleine bedrijven tot grote en meertalige bedrijven.

Microsoft Azure is het Cloud Services platform van micro soft, dat een breed scala aan nieuwe mogelijkheden biedt. Klanten kunnen nu snel toepassingen inrichten en inleveren als een service in de Cloud, zodat ze niet beperkt zijn tot technische of budget beperkingen. In plaats van het investeren van tijd en budget in de hardware-infra structuur kunnen bedrijven zich richten op de toepassing, bedrijfs processen en de voor delen van klanten en gebruikers.

Met Microsoft Azure-services voor virtuele machines biedt Microsoft een uitgebreid IaaS-platform (Infrastructure as a Service). SAP NetWeaver-toepassingen worden ondersteund op virtuele Azure-machines (IaaS). In dit document wordt beschreven hoe u toepassingen met SAP netweaving plant en implementeert binnen Microsoft Azure als platform van keuze.

Het papier zelf is gericht op twee belang rijke aspecten:

* In het eerste deel worden twee ondersteunde implementatie patronen beschreven voor toepassingen op basis van SAP netweave op Azure. Er wordt ook een algemene verwerking van Azure met SAP-implementaties beschreven.
* De tweede deel gegevens die de verschillende scenario's implementeren die in het eerste deel worden beschreven.

Zie voor aanvullende bronnen hoofdstuk [bronnen][planning-guide-1.2] in dit document.

### <a name="definitions-upfront"></a>Definities vooraf
In het hele document gebruiken we de volgende voor waarden:

* IaaS: Infrastructure as a Service
* PaaS: platform as a Service
* SaaS: Software as a Service
* SAP-onderdeel: een afzonderlijke SAP-toepassing, zoals ECC, BW, Solution Manager of S/4HANA.  SAP-onderdelen kunnen worden gebaseerd op traditionele ABAP of Java-technologieën of op een toepassing die niet op netweave is gebaseerd, zoals bedrijfs objecten.
* SAP-omgeving: een of meer SAP-onderdelen die logisch zijn gegroepeerd om een zakelijke functie uit te voeren, zoals ontwikkeling, QAS, training, DR of productie.
* SAP liggend: deze term verwijst naar de volledige SAP-assets in het IT-landschap van een klant. Het SAP-landschap omvat alle productie-en niet-productie omgevingen.
* SAP-systeem: de combi natie van DBMS-laag en-toepassingslaag van, bijvoorbeeld een SAP ERP-ontwikkelings systeem, SAP BW test systeem, SAP CRM-productie systeem, enzovoort. In azure-implementaties wordt het niet ondersteund om deze twee lagen te verdelen over on-premises en Azure. Betekent dat een SAP-systeem on-premises is geïmplementeerd of wordt geïmplementeerd in Azure. U kunt de verschillende systemen van een SAP-landschap echter implementeren in azure of on-premises. U kunt bijvoorbeeld het ontwikkel-en test systeem van SAP CRM implementeren in azure, maar het productie systeem op locatie van SAP.
* Cross-premises of hybride: hierin wordt een scenario beschreven waarin Vm's worden geïmplementeerd naar een Azure-abonnement met site-naar-site-, multi-site-of ExpressRoute-connectiviteit tussen de on-premises Data Center (s) en Azure. In algemene documentatie over Azure worden dit soort implementaties ook beschreven als cross-premises of hybride scenario's. De reden hiervoor is om on-premises domeinen, on-premises Active Directory-OpenLDAP en on-premises DNS uit te breiden naar Azure. De on-premises liggende lands worden uitgebreid naar de Azure-assets van het abonnement. Met deze extensie kunnen Vm's deel uitmaken van het on-premises domein. Domein gebruikers van het on-premises domein hebben toegang tot de servers en kunnen services uitvoeren op deze Vm's (zoals DBMS-Services). Communicatie en naam omzetting tussen Vm's die on-premises en Azure geïmplementeerde Vm's zijn geïmplementeerd, is mogelijk. Dit is het meest voorkomende en bijna exclusieve geval dat SAP-assets in Azure worden geïmplementeerd. Zie [Dit][vpn-gateway-cross-premises-options] artikel en [deze][vpn-gateway-site-to-site-create]voor meer informatie.
* Azure-bewakings uitbreiding, verbeterde bewaking en Azure-extensie voor SAP: beschrijven een en hetzelfde item. Hierin wordt een VM-extensie beschreven die moet worden geïmplementeerd door u om enkele basis gegevens over de Azure-infra structuur te verstrekken aan de SAP host-agent. SAP in SAP-notities kan worden gebruikt als bewakings uitbreiding of uitgebreide bewaking. In azure wordt hiernaar verwezen als **Azure-extensie voor SAP**.

> [!NOTE]
> Cross-premises of hybride implementaties van SAP-systemen waarbij Azure Virtual Machines SAP-systemen worden uitgevoerd, worden lid van een on-premises domein ondersteund voor SAP-productie systemen. Cross-premises of hybride configuraties worden ondersteund voor het implementeren van onderdelen of het volt ooien van SAP-landschappen in Azure. Zelfs als u het volledige SAP-landschap uitvoert in azure, moeten deze Vm's deel uitmaken van het on-premises domein en ADS/OpenLDAP.
>
>



### <a name="resources"></a><a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Resources
De documentatie voor het ingangs punt voor SAP-werk belasting op Azure vindt u [hier](./get-started.md). Vanaf dit ingangs punt vindt u veel artikelen die betrekking hebben op de onderwerpen van:

- SAP NetWeaver en Business One op Azure
- SAP DBMS-hand leidingen voor verschillende DBMS-systemen in azure
- Hoge Beschik baarheid en herstel na nood geval voor SAP-workload op Azure
- Specifieke richt lijnen voor het uitvoeren van SAP HANA op Azure
- Richt lijnen die specifiek zijn voor Azure HANA grote instanties voor de SAP HANA DBMS


> [!IMPORTANT]
> Waar mogelijk wordt een koppeling naar de verwijzende SAP-installatie handleidingen of andere SAP-documentatie gebruikt (referentie InstGuide-01, Zie <http://service.sap.com/instguides> ). Wanneer het gaat om de vereisten, het installatie proces of Details van specifieke SAP-functionaliteit, moeten de SAP-documentatie en-hand leidingen altijd zorgvuldig worden gelezen, omdat de micro soft-documenten alleen specifieke taken omvatten voor SAP-software die is geïnstalleerd en wordt uitgevoerd op een Microsoft Azure virtuele machine.
>
>

De volgende SAP-opmerkingen zijn gerelateerd aan het onderwerp van SAP on Azure:

| Nummer van notitie | Titel |
| --- | --- |
| [1928533] |SAP-toepassingen op Azure: ondersteunde producten en grootte |
| [2015553] |SAP op Microsoft Azure: vereisten voor ondersteuning |
| [1999351] |Problemen met verbeterde Azure-bewaking voor SAP oplossen |
| [2178632] |Belangrijkste meet waarden voor SAP op Microsoft Azure |
| [1409604] |Virtualisatie op Windows: uitgebreide bewaking |
| [2191498] |SAP op Linux met Azure: uitgebreide bewaking |
| [2243692] |Linux on Microsoft Azure (IaaS) VM: SAP-licentie problemen |
| [1984787] |SUSE LINUX Enter prise Server 12: installatie notities |
| [2002167] |Red Hat Enterprise Linux 7. x: installatie en upgrade |
| [2069760] |Oracle Linux 7. x SAP-installatie en-upgrade |
| [1597355] |Aanbeveling voor wissel geheugen voor Linux |

Lees ook de [SCN-wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) die alle SAP-opmerkingen voor Linux bevat.

In [dit artikel][azure-resource-manager/management/azure-subscription-service-limits-subscription]vindt u algemene standaard beperkingen en maximum beperkingen voor Azure-abonnementen.

## <a name="possible-scenarios"></a>Mogelijke Scenario's
SAP wordt vaak gezien als een van de meest essentiële toepassingen binnen ondernemingen. De architectuur en bewerkingen van deze toepassingen zijn voornamelijk complex en zorgen ervoor dat u voldoet aan de vereisten op het niveau van Beschik baarheid en prestaties van belang.

Daarom moeten bedrijven zorgvuldig nadenken over de Cloud provider om te kiezen voor het uitvoeren van dergelijke bedrijfs kritieke bedrijfs processen op. Azure is het ideale open bare Cloud platform voor zakelijke essentiële SAP-toepassingen en bedrijfs processen. Gezien de grote verscheidenheid aan Azure-infra structuur, kunnen bijna alle bestaande SAP NetWeaver en S/4HANA-systemen worden gehost in Azure. Azure biedt Vm's met veel terabyte geheugen en meer dan 200 Cpu's. Verder biedt Azure [Hana grote instanties](./hana-overview-architecture.md), waarmee u Hana-implementaties van Maxi maal 24 TB kunt uitbreiden en SAP Hana scale-out implementaties van maxi maal 120 TB. Een van de statussen kan vandaag al zijn dat vrijwel alle on-premises SAP-scenario's in azure ook kunnen worden uitgevoerd.

Zie voor een ruwe beschrijving van de scenario's en enkele scenario's die niet worden ondersteund, de [SAP-workload van het document op ondersteunde scenario's voor virtuele Azure-machines](./sap-planning-supported-configurations.md).

Controleer deze scenario's en enkele voor waarden die zijn benoemd als niet ondersteund in de documentatie waarnaar wordt verwezen tijdens de planning en de ontwikkeling van uw architectuur die u in azure wilt implementeren.

Algemeen het meest voorkomende implementatie patroon is een cross-premises scenario zoals wordt weer gegeven

![VPN met site-naar-site-connectiviteit (cross-premises)][planning-guide-figure-300]

Reden voor veel klanten bij het Toep assen van een cross-premises implementatie patroon is dat het het meest transparant is voor alle toepassingen om on-premises in azure uit te breiden met Azure ExpressRoute en Azure als virtueel Data Center te behandelen. Naarmate meer en meer assets worden verplaatst naar Azure, zullen de Azure geïmplementeerde infra structuur en netwerk infrastructuur groeien en de on-premises activa zullen worden verminderd. Alles transparant voor gebruikers en toepassingen.

Als u SAP-systemen wilt implementeren in azure IaaS of IaaS in het algemeen, is het belang rijk om inzicht te krijgen in de belang rijke verschillen tussen de aanbiedingen van traditionele uitbesteers of gastheerers en IaaS-aanbiedingen. Terwijl de traditionele hoster of de uitvoerder de infra structuur (netwerk, opslag en server type) toewijst aan de werk belasting die een klant wil hosten, is dit in plaats daarvan de verantwoordelijkheid van de klant of de partner om de werk belasting te kenmerken en de juiste Azure-onderdelen van Vm's, opslag en netwerk te kiezen voor IaaS-implementaties.

Als u gegevens wilt verzamelen voor het plannen van uw implementatie in azure, is het belang rijk om het volgende te doen:

- Evalueren welke SAP-producten worden ondersteund op virtuele machines in azure
- Evalueren welke specifieke releases van het besturings systeem worden ondersteund met specifieke Azure-Vm's voor deze SAP-producten
- Evalueren welke DBMS-releases worden ondersteund voor uw SAP-producten met specifieke Azure-Vm's
- Evalueren of voor sommige van de vereiste OS/DBMS-releases een SAP-release, een ondersteunings pakket en kernel-upgrades moeten worden uitgevoerd om naar een ondersteunde configuratie te gaan
- Bepaal of u moet overstappen op andere besturings systemen om te implementeren in Azure.

Details over ondersteunde SAP-onderdelen op Azure, ondersteunde Azure-infrastructuur eenheden en gerelateerde versies van besturings systemen en DBMS-releases worden uitgelegd in het artikel [wat SAP-software ondersteunt voor Azure-implementaties](./sap-supported-product-on-azure.md). De resultaten van de evaluatie van geldige SAP-releases, besturings systemen en DBMS-releases hebben een grote invloed op de inspanningen die SAP-systemen verplaatsen naar Azure. De resultaten van deze evaluatie zijn het bepalen of er aanzienlijke voorbereidings inspanningen kunnen zijn in gevallen waarin SAP-release-upgrades of wijzigingen van besturings systemen nodig zijn.


## <a name="azure-regions"></a><a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Azure-regio's
De Azure-Services van micro soft worden verzameld in azure-regio's. Een Azure-regio is een verzameling van data centers die de hardware en infra structuur bevatten die de verschillende Azure-Services uitvoert en host. Deze infra structuur bevat een groot aantal knoop punten die als reken knooppunten of opslag knooppunten functioneren of netwerk functionaliteit uitvoeren.

Voor een lijst van de verschillende Azure-regio's raadpleegt u het artikel [Azure-geografs](https://azure.microsoft.com/global-infrastructure/geographies/). Niet alle Azure-regio's bieden dezelfde services. Afhankelijk van het SAP-product dat u wilt uitvoeren en het besturings systeem en het DBMS dat erin is gerelateerd, kunt u een situatie ontvangen dat een bepaalde regio niet de VM-typen bevat die u nodig hebt. Dit geldt met name voor het uitvoeren van SAP HANA, waarbij u doorgaans Vm's van de M/Mv2 VM-serie nodig hebt. Deze VM-families worden alleen geïmplementeerd in een subset van de regio's. U kunt meer informatie vinden over de exacte VM, typen, Azure-opslag typen of andere Azure-Services die beschikbaar zijn in welke regio's, met behulp van de [beschik bare site producten per regio](https://azure.microsoft.com/global-infrastructure/services/). Wanneer u de planning start en bepaalde regio's in acht neemt als primaire regio en uiteindelijk secundaire regio, moet u eerst onderzoeken of de benodigde services in die regio's beschikbaar zijn.

### <a name="availability-zones"></a>Beschikbaarheidszones
Verschillende Azure-regio's hebben een concept geïmplementeerd met de naam Beschikbaarheidszones. Beschikbaarheidszones zijn fysiek gescheiden locaties binnen een Azure-regio. Elke beschikbaarheidszone bestaat uit een of meer datacentra die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerk. Als u bijvoorbeeld twee virtuele machines in twee Beschikbaarheidszones van Azure implementeert en een Framework met hoge Beschik baarheid implementeert voor uw SAP DBMS-systeem of de SAP Central-Services, beschikt u over de beste SLA in Azure. Voor deze specifieke SLA voor virtuele machines in azure, controleert u de meest recente versie van de service overeenkomst van de [virtuele](https://azure.microsoft.com/support/legal/sla/virtual-machines/)machine. Omdat Azure-regio's snel in de afgelopen jaren zijn ontwikkeld en uitgebreid, kan de topologie van de Azure-regio's, het aantal fysieke data centers, de afstand tussen deze data centers en de afstand tussen de Azure-beschikbaarheidszones verschillend zijn. En met de netwerk latentie.

Het principe van Beschikbaarheidszones is niet van toepassing op de HANA-specifieke service van [Hana grote instanties](./hana-overview-architecture.md). Service overeenkomsten voor HANA grote instanties vindt u in het artikel [Sla voor SAP Hana on Azure large instances](https://azure.microsoft.com/support/legal/sla/sap-hana-large/)


### <a name="fault-domains"></a><a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Fout domeinen
Fout domeinen vertegenwoordigen een fysieke fout eenheid, nauw gerelateerd aan de fysieke infra structuur in data centers en terwijl een fysieke Blade of rek kan worden beschouwd als een fout domein, is er geen directe een-op-een-toewijzing tussen de twee.

Wanneer u meerdere Virtual Machines implementeert als onderdeel van één SAP-systeem in Microsoft Azure virtuele-machine Services, kunt u de Azure Fabric-controller beïnvloeden om uw toepassing te implementeren in verschillende fout domeinen, waardoor er meer vereisten zijn voor beschikbaarheids Sla's. De distributie van fout domeinen via een Azure-schaal eenheid (verzameling van honderden reken knooppunten of opslag knooppunten en netwerken) of de toewijzing van Vm's aan een specifiek fout domein is echter iets waarover u geen directe controle hebt. Als u de Azure Fabric-controller wilt richten op het implementeren van een set virtuele machines in verschillende fout domeinen, moet u een Azure-beschikbaarheidsset toewijzen aan de Vm's tijdens de implementatie. Zie voor meer informatie over Azure-beschikbaarheids sets hoofd stuk [Azure-beschikbaarheids sets][planning-guide-3.2.3] in dit document.


### <a name="upgrade-domains"></a><a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Upgrade domeinen
Upgrade domeinen vertegenwoordigen een logische eenheid waarmee u kunt bepalen hoe een virtuele machine binnen een SAP-systeem, die bestaat uit SAP-exemplaren die worden uitgevoerd in meerdere Vm's, wordt bijgewerkt. Wanneer een upgrade wordt uitgevoerd, loopt Microsoft Azure het proces van het bijwerken van deze upgrade domeinen één voor één. Door Vm's te verspreiden tijdens de implementatie via verschillende upgrade domeinen, kunt u uw SAP-systeem gedeeltelijk beschermen tegen potentiële downtime. Als u Azure wilt dwingen de Vm's van een SAP-systeem distributie over verschillende upgrade domeinen te implementeren, moet u een specifiek kenmerk instellen tijdens de implementatie tijd van elke virtuele machine. Net als bij fout domeinen is een Azure-schaal eenheid onderverdeeld in meerdere upgrade domeinen. Als u de Azure Fabric-controller wilt richten op het implementeren van een set Vm's via verschillende upgrade domeinen, moet u een Azure-Beschikbaarheidsset toewijzen aan de Vm's tijdens de implementatie. Zie voor meer informatie over Azure-beschikbaarheids sets hoofd stuk [Azure-beschikbaarheids sets][planning-guide-3.2.3] hieronder.


### <a name="azure-availability-sets"></a><a name="18810088-f9be-4c97-958a-27996255c665"></a>Azure-beschikbaarheids sets
Azure Virtual Machines binnen één Azure-beschikbaarheidsset worden gedistribueerd door de Azure Fabric-controller via verschillende fout-en upgrade domeinen. Het doel van de distributie over verschillende fout-en upgrade domeinen is om te voor komen dat alle Vm's van een SAP-systeem worden afgesloten in het geval van infrastructuur onderhoud of een storing binnen één fout domein. Standaard maken Vm's geen deel uit van een beschikbaarheidsset. De deelname van een virtuele machine in een beschikbaarheidsset wordt gedefinieerd tijdens de implementatie tijd of later op basis van een herconfiguratie en herimplementatie van een virtuele machine.

Lees [dit artikel][virtual-machines-manage-availability]voor meer informatie over het concept van Azure-beschikbaarheids sets en de manier waarop beschikbaarheids sets betrekking hebben op fout-en upgrade domeinen.

Bij het definiëren van beschikbaarheids sets en het combi neren van verschillende virtuele machines van verschillende VM-families binnen één beschikbaarheidsset, kunnen er problemen optreden waardoor u een bepaald VM-type niet in een dergelijke beschikbaarheidsset kunt gebruiken. De reden hiervoor is dat de beschikbaarheidsset is gebonden aan de schaal eenheid die een bepaald type Compute-hosts bevat. Met een bepaald type Compute-host kunnen alleen bepaalde typen VM-families worden uitgevoerd. Als u bijvoorbeeld een beschikbaarheidsset maakt en de eerste VM in de beschikbaarheidsset implementeert en u een VM-type van de Esv3-serie kiest en vervolgens probeert te implementeren als tweede virtuele machine van de M-serie, wordt u in de tweede toewijzing geweigerd. De reden hiervoor is dat de Vm's van de Esv3-serie niet op dezelfde host worden uitgevoerd als de virtuele machines van de M-serie. Hetzelfde probleem kan zich voordoen wanneer u de grootte van Vm's probeert te wijzigen en probeert een virtuele machine uit de Esv3-serie te verplaatsen naar een VM-type van de M-serie. In het geval van het wijzigen van de grootte van een VM-serie die niet op dezelfde host kan worden gehost, moet u alle virtuele machines in de beschikbaarheidsset afsluiten en de grootte ervan aanpassen zodat deze kan worden uitgevoerd op het type van de andere host. Voor Sla's van Vm's die in de beschikbaarheidsset worden geïmplementeerd, raadpleegt u het artikel service-Sla's van de [virtuele machine](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

Het principe van de beschikbaarheidsset en gerelateerde update en het fout domein zijn niet van toepassing op de HANA-specifieke service van [Hana grote instanties](./hana-overview-architecture.md). Service overeenkomsten voor HANA grote instanties vindt u in het artikel [Sla voor SAP Hana on Azure large instances](https://azure.microsoft.com/support/legal/sla/sap-hana-large/).

> [!IMPORTANT]
> De concepten van Azure-beschikbaarheidszones-en Azure-beschikbaarheids sets sluiten elkaar wederzijds uit. Dit betekent dat u een paar of meerdere Vm's kunt implementeren in een specifieke beschikbaarheids zone of een Azure-beschikbaarheidsset. Maar niet beide.

### <a name="azure-paired-regions"></a>Gekoppelde Azure-regio's
Azure biedt Azure-regio paren waarin de replicatie van bepaalde gegevens tussen deze vaste regio paren is ingeschakeld. De regio koppeling wordt beschreven in het artikel [bedrijfs continuïteit en herstel na nood gevallen (BCDR): gekoppelde Azure-regio's](../../../best-practices-availability-paired-regions.md). Zoals beschreven in het artikel, is de replicatie van gegevens gebonden Azure-opslag typen die kunnen worden geconfigureerd door u te repliceren naar de gekoppelde regio. Zie ook de [redundantie van het artikel opslag in een secundaire regio](../../../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region). De opslag typen die dergelijke replicatie toestaan, zijn opslag typen, die niet geschikt zijn voor de DBMS-werk belasting. Omdat de bruikbaarheid van de Azure storage-replicatie beperkt is tot Azure Blob-opslag (zoals voor back-updoeleinden) of andere opslag scenario's met hoge latentie. Wanneer u controleert op gekoppelde regio's en de services die u wilt gebruiken als uw primaire of secundaire regio, kunnen er situaties optreden waarbij Azure-Services en/of VM-typen die u wilt gebruiken in uw primaire regio, niet beschikbaar zijn in de gekoppelde regio. Het is ook mogelijk dat er een situatie optreedt waarbij de gekoppelde Azure-regio niet wordt geaccepteerd vanwege de naleving van de gegevens. In dergelijke gevallen moet u een niet-gekoppelde regio gebruiken als secundaire/nood herstel regio. In dat geval moet u nadenken over de replicatie van een deel van de gegevens die door Azure zelf zouden worden gerepliceerd. Een voor beeld van het repliceren van uw Active Directory en DNS naar uw nood herstel regio wordt beschreven in het artikel herstel [na nood geval instellen voor Active Directory en DNS](../../../site-recovery/site-recovery-active-directory.md)


## <a name="azure-virtual-machine-services"></a>Azure virtual machine-Services
Azure biedt een groot aantal verschillende virtuele machines die u kunt selecteren om te implementeren. Er zijn geen technologieën van voor-en infra structuur nodig. De Azure VM service-aanbieding vereenvoudigt het onderhoud en de exploitatie van toepassingen met behulp van Compute en opslag op aanvraag om webtoepassingen en verbonden toepassingen te hosten, te schalen en te beheren. Infrastructuur beheer is geautomatiseerd met een platform dat is ontworpen voor hoge Beschik baarheid en dynamisch schalen om gebruik te kunnen afstemmen op de behoeften van verschillende prijs modellen.

![Plaatsing van Microsoft Azure Services voor virtuele machines][planning-guide-figure-400]

Met Azure virtual machines kunt u met micro soft aangepaste server installatie kopieën naar Azure implementeren als IaaS-exemplaren. Of u kunt kiezen uit een uitgebreide selectie installatie kopieën van het besturings systeem uit de galerie van Azure-installatie kopieën.

Vanuit een operationeel perspectief biedt de Azure Virtual Machine-Service vergelijk bare ervaringen als virtuele machines die op locatie zijn geïmplementeerd. U bent verantwoordelijk voor het beheer, de bewerkingen en de patches van het specifieke besturings systeem, dat wordt uitgevoerd in een Azure-VM en de bijbehorende toepassingen in die VM. Micro soft biedt niet meer services die buiten de host vallen van de virtuele machine op de Azure-infra structuur (infra structuur als een service-IaaS). Voor SAP-werk belasting die u als een klant implementeert, heeft micro soft geen aanbiedingen meer dan de IaaS-aanbiedingen.

Het Microsoft Azure platform is een platform met meerdere tenants. Als gevolg van opslag-, netwerk-en reken bronnen die virtuele machines van Azure hosten, zijn er een paar uitzonde ringen die worden gedeeld tussen tenants. Intelligente beperking en quotum logica wordt gebruikt om te voor komen dat een Tenant op een drastische manier de prestaties van een andere Tenant (ruis op de buur) beïnvloedt. Met name voor het certificeren van het Azure-platform voor SAP HANA moet micro soft de bron isolatie bewijzen voor gevallen waarin meerdere Vm's regel matig op dezelfde host kunnen worden uitgevoerd. Hoewel logica in azure probeert om afwijkingen in de band breedte te blijven gebruiken, hebben veel gedeelde platformen vaak grotere afwijkingen in de beschik baarheid van resources en band breedte, dan kunnen klanten zich in hun on-premises implementaties voordoen. De kans dat een SAP-systeem op Azure grotere afwijkingen kan ondervinden dan in een on-premises systeem moet in aanmerking worden genomen.

### <a name="azure-virtual-machines-for-sap-workload"></a>Azure virtual machines voor SAP-workload

Voor SAP-workload is de selectie beperkt tot verschillende VM-families die geschikt zijn voor SAP-workload en SAP HANA workload specifiek. De manier waarop u het juiste VM-type vindt en de mogelijkheid om te werken met de SAP-workload wordt beschreven in het document [wat SAP-software wordt ondersteund voor Azure-implementaties](./sap-supported-product-on-azure.md).

> [!NOTE]
> De VM-typen die zijn gecertificeerd voor SAP-werk belasting, er is geen overinrichting van CPU-en geheugen bronnen.

Naast de geselecteerde VM-typen die worden ondersteund, moet u ook controleren of deze VM-typen beschikbaar zijn in een bepaalde regio op basis van de site [producten die beschikbaar zijn per regio](https://azure.microsoft.com/global-infrastructure/services/). Maar belang rijker u moet evalueren of:

- CPU-en geheugen resources van verschillende VM-typen
- Band breedte voor IOPS van verschillende VM-typen
- Netwerk mogelijkheden van verschillende VM-typen
- Aantal schijven dat kan worden bijgevoegd
- De mogelijkheid om bepaalde typen Azure-opslag te gebruiken

aan uw behoeften voldoen. De meeste van deze gegevens vindt u [hier (Linux)][virtual-machines-sizes-linux] en [hier (Windows)][virtual-machines-sizes-windows] voor een bepaald VM-type.

Als prijs model hebt u verschillende prijs opties die er als volgt uitzien:

- Betalen per gebruik
- Een jaar gereserveerd
- Drie jaar gereserveerd
- Spotprijzen

De prijs van elk van de verschillende aanbiedingen met verschillende service aanbiedingen rond besturings systemen en verschillende regio's is beschikbaar op de site [Linux virtual machines prijzen](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) en [Windows virtual machines prijzen](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Raadpleeg de volgende artikelen voor meer informatie en flexibiliteit van één jaar en drie gereserveerde exemplaren van een jaar:

- [Wat zijn Azure-reserveringen?](../../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Flexibiliteit van de VM-grootte met gereserveerde VM-instanties](../../reserved-vm-instance-size-flexibility.md)
- [Toepassing van de reserveringskorting op virtuele machines](../../../cost-management-billing/manage/understand-vm-reservation-charges.md)

Lees het artikel [Azure spot virtual machines](https://azure.microsoft.com/pricing/spot/)voor meer informatie over de prijzen. De prijzen van hetzelfde VM-type kunnen ook verschillen tussen de verschillende Azure-regio's. Voor sommige klanten was het waard om te worden geïmplementeerd in een goedkopere Azure-regio.

Daarnaast biedt Azure de concepten van een specifieke host. Het speciale host-concept geeft u meer controle over patch-cycli die door Azure worden uitgevoerd. U kunt de patch op basis van uw eigen planningen laten duren. Deze aanbieding is specifiek gericht op klanten met werk belasting die mogelijk niet de normale werk belasting volgen. Lees voor meer informatie over de concepten van voor Azure exclusieve host aanbiedingen het artikel [Azure dedicated host](../../dedicated-hosts.md). Het gebruik van deze aanbieding wordt ondersteund voor SAP-werk belasting en wordt gebruikt door verschillende SAP-klanten die meer controle moeten hebben over patching van de infra structuur en uiteindelijk onderhouds plannen van micro soft. Lees het artikel [onderhoud voor virtuele machines in azure](../../maintenance-and-updates.md)voor meer informatie over de manier waarop micro soft de Azure-infra structuur die als host fungeert voor virtuele machines, kan lezen en onderhouden.

#### <a name="generation-1-and-generation-2-virtual-machines"></a>Virtuele machines van de eerste en tweede generatie
De Hyper Visor van micro soft kan twee verschillende generaties virtuele machines verwerken. Deze indelingen worden **generatie 1** en **generatie 2** genoemd. **Generatie 2** is geïntroduceerd in het jaar 2012 met Windows Server 2012 Hyper Visor. Azure is gestart met virtuele machines van de eerste generatie. Wanneer u virtuele machines van Azure implementeert, is de standaard nog steeds de indeling van de eerste generatie. Ondertussen kunt u ook VM-indelingen van de tweede generatie implementeren. In het artikel [ondersteuning voor virtuele machines van de tweede generatie op Azure](../../generation-2.md) worden de Azure VM-families weer gegeven die kunnen worden geïmplementeerd als virtuele machine van de tweede generatie. Dit artikel bevat ook een overzicht van de belangrijkste functionele verschillen van virtuele machines van de 2e generatie, zoals ze kunnen worden uitgevoerd op de privécloud van Hyper-V en Azure. Meer belang rijker dit artikel bevat ook functionele verschillen tussen virtuele machines van de eerste generatie en Vm's van de tweede generatie, omdat deze worden uitgevoerd in Azure.

> [!NOTE]
> Er zijn functionele verschillen tussen virtuele machines van de eerste en tweede generatie die worden uitgevoerd in Azure. Lees de artikel  [ondersteuning voor virtuele machines van de tweede generatie op Azure](../../generation-2.md) om een lijst met deze verschillen te bekijken.

Het is niet mogelijk om een bestaande virtuele machine van de ene generatie naar de andere generatie te verplaatsen. Als u de generatie van virtuele machines wilt wijzigen, moet u een nieuwe VM van de gewenste generatie implementeren en de software die u uitvoert opnieuw installeren op de virtuele machine van de generatie. Deze wijziging is alleen van invloed op de VHD-basis installatie kopie van de virtuele machine en heeft geen invloed op de gegevens schijven of gekoppelde NFS-of SMB-shares. Gegevens schijven, NFS of SMB-shares die oorspronkelijk zijn toegewezen aan, bijvoorbeeld op een virtuele machine van de eerste generatie.

> [!NOTE]
> Het implementeren van de virtuele machines van Mv1 VM-serie als generatie 2 Vm's zijn mogelijk vanaf het begin van mei 2020. Met een schijnbaar lager en overweeg tussen Mv1 en Mv2-serie Vm's is mogelijk.


### <a name="storage-microsoft-azure-storage-and-data-disks"></a><a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Opslag: Microsoft Azure Storage en gegevens schijven
Microsoft Azure Virtual Machines gebruik verschillende opslag typen. Wanneer u SAP on Azure virtuele machine Services implementeert, is het belang rijk om inzicht te krijgen in de verschillen tussen deze twee hoofd typen opslag:

* Niet-permanente, vluchtige opslag.
* Permanente opslag.

Azure Vm's bieden niet-permanente schijven nadat een virtuele machine is geïmplementeerd. Als een VM opnieuw wordt opgestart, worden alle inhoud op deze stations gewist. Daarom is het een gegeven dat de gegevens bestanden en de bestanden van de data base, onder geen enkele omstandigheden, op deze niet-blijvende stations moeten worden opgeslagen. Er zijn mogelijk uitzonde ringen voor sommige data bases, waarbij deze niet-permanente schijven geschikt zijn voor TempDB en tijdelijke tablespaces. Vermijd het gebruik van deze stations voor Vm's uit de A-serie, omdat deze niet-persistente schijven beperkt zijn bij de door Voer met die VM-serie. Lees voor meer informatie het artikel [inzicht in het tijdelijke station op virtuele Windows-machines in azure](/archive/blogs/mast/understanding-the-temporary-drive-on-windows-azure-virtual-machines)

---
> ![Windows-logo.][Logo_Windows] Windows
>
> Station D:\ een Azure-VM is een niet-persistent station dat wordt ondersteund door een aantal lokale schijven op het Azure Compute-knoop punt. Omdat het niet-persistent is, betekent dit dat alle wijzigingen die zijn aangebracht in de inhoud van de D:\ het station gaat verloren wanneer de virtuele machine opnieuw wordt opgestart. Door alle wijzigingen, zoals opgeslagen bestanden, mappen die zijn gemaakt, toepassingen geïnstalleerd, enzovoort.
>
> ![Linux-logo.][Logo_Linux] Linux
>
> Linux Azure-Vm's koppelen automatisch een station op/mnt/resource dat een niet-persistent station is dat wordt ondersteund door lokale schijven op het Azure Compute-knoop punt. Omdat het niet-persistent is, betekent dit dat alle wijzigingen die zijn aangebracht in inhoud in/mnt/resource verloren gaan wanneer de virtuele machine opnieuw wordt opgestart. Door wijzigingen, zoals opgeslagen bestanden, mappen die zijn gemaakt, toepassingen geïnstalleerd, enzovoort.
>
>

#### <a name="azure-storage-accounts"></a>Azure Storage-accounts

Bij het implementeren van services of Vm's in azure, worden de implementatie van Vhd's en VM-installatie kopieën ingedeeld in eenheden die Azure Storage accounts worden genoemd. [Azure Storage-accounts](../../../storage/common/storage-account-overview.md) hebben beperkingen in IOPS, door Voer of grootten die kunnen bevatten. In de eerder genoemde beperkingen, die worden beschreven in:

- [Schaalbaarheids doelen voor standaard opslag accounts](../../../storage/common/scalability-targets-standard-account.md)
- [Schaalbaarheids doelen voor Premium-pagina-Blob Storage-accounts](../../../storage/blobs/scalability-targets-premium-page-blobs.md)

speelt een belang rijke rol bij het plannen van een SAP-implementatie in Azure. U kunt het aantal permanente schijven in een opslag account beheren. U moet de opslag accounts beheren en uiteindelijk nieuwe opslag accounts maken om meer persistente schijven te maken.

In de afgelopen jaren heeft de introductie van [Azure Managed disks](../../managed-disks-overview.md) u van deze taken ontheven. De aanbeveling voor SAP-implementaties is om gebruik te maken van Azure Managed disks in plaats van Azure Storage-accounts zelf te beheren. Met Azure Managed disks worden schijven verdeeld over verschillende opslag accounts, zodat de limieten van de afzonderlijke opslag accounts niet worden overschreden.

Binnen een opslag account hebt u een type map met de naam containers die kan worden gebruikt om bepaalde schijven te groeperen in specifieke containers.

In azure volgt een schijf/VHD-naam de volgende naam verbinding die een unieke naam moet opgeven voor de VHD in Azure:

`http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>`

De bovenstaande teken reeks moet een unieke identificatie hebben van de schijf/VHD die is opgeslagen op Azure Storage.


#### <a name="azure-persisted-storage-types"></a>Persistente opslag typen van Azure
Azure biedt een verscheidenheid aan permanente opslag optie die kan worden gebruikt voor SAP-workload en specifieke onderdelen van de SAP-stack. Lees voor meer informatie het document  [Azure Storage voor SAP-workloads](./planning-guide-storage.md).


### <a name="microsoft-azure-networking"></a><a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Microsoft Azure netwerken

Microsoft Azure biedt een netwerk infrastructuur waarmee de toewijzing van alle scenario's wordt toegestaan, die we willen realiseren met SAP-software. De mogelijkheden zijn:

* Toegang vanaf buiten, rechtstreeks naar de virtuele machines via Windows Terminal Services of SSH/VNC
* Toegang tot services en specifieke poorten die worden gebruikt door toepassingen binnen de virtuele machines
* Interne communicatie en naam omzetting tussen een groep virtuele machines die worden geïmplementeerd als Azure-Vm's
* Cross-premises connectiviteit tussen het on-premises netwerk van een klant en het Azure-netwerk
* Meerdere Azure-regio's of Data Center-connectiviteit tussen Azure-sites

Meer informatie vindt u hier: <https://azure.microsoft.com/documentation/services/virtual-network/>

Er zijn veel verschillende mogelijkheden voor het configureren van naam-en IP-omzetting in Azure. Er is ook een Azure DNS-service, die kan worden gebruikt in plaats van uw eigen DNS-server in te stellen. Meer informatie vindt u in [dit artikel][virtual-networks-manage-dns-in-vnet] en op [Deze pagina](https://azure.microsoft.com/services/dns/).

Voor cross-premises of hybride scenario's wordt gebruikgemaakt van het feit dat de on-premises AD/OpenLDAP/DNS is uitgebreid via een VPN-of particuliere verbinding met Azure. Voor bepaalde scenario's zoals hier wordt beschreven, kan het nodig zijn om een AD/OpenLDAP-replica in azure te installeren.

Omdat netwerken en naam omzetting een belang rijk onderdeel zijn van de implementatie van de Data Base voor een SAP-systeem, wordt dit concept uitvoeriger besproken in de [DBMS-implementatie handleiding][dbms-guide].

##### <a name="azure-virtual-networks"></a>Virtuele netwerken van Azure

Als u een Azure-Virtual Network bouwt, kunt u het adres bereik definiëren van de privé-IP-adressen die zijn toegewezen door de Azure DHCP-functionaliteit. In het geval van cross-premises scenario's wordt het IP-adres bereik dat is gedefinieerd, nog steeds toegewezen met DHCP door Azure. Domain Name Resolution wordt echter on-premises uitgevoerd (ervan uitgaande dat de Vm's deel uitmaken van een on-premises domein) en kunnen daarom adressen omzetten die niet langer zijn dan de verschillende Azure-Cloud Services.

Elke virtuele machine in azure moet worden verbonden met een Virtual Network.

Meer informatie vindt u in [dit artikel][resource-groups-networking] en op [Deze pagina](https://azure.microsoft.com/documentation/services/virtual-network/).


> [!NOTE]
> Zodra een virtuele machine is geïmplementeerd, kunt u de configuratie van de Virtual Network standaard niet wijzigen. De TCP/IP-instellingen moeten naar de Azure DHCP-server worden overgelaten. Standaard gedrag is dynamische IP-toewijzing.
>
>

Het MAC-adres van de virtuele netwerk kaart kan worden gewijzigd, bijvoorbeeld na het wijzigen van de grootte en het Windows-of Linux-gast besturingssysteem wordt de nieuwe netwerk kaart opgehaald en automatisch DHCP gebruikt voor het toewijzen van de IP-en DNS-adressen in dit geval.

##### <a name="static-ip-assignment"></a>Statische IP-toewijzing
Het is mogelijk om vaste of gereserveerde IP-adressen toe te wijzen aan Vm's binnen een Azure-Virtual Network. Als u de virtuele machines in een Azure-Virtual Network uitvoert, wordt deze functionaliteit zo nodig gebruikt of vereist voor bepaalde scenario's. De IP-toewijzing blijft geldig gedurende het bestaan van de virtuele machine, ongeacht of de virtuele machine wordt uitgevoerd of afgesloten. Als gevolg hiervan moet u het totale aantal Vm's (actieve en gestopte Vm's) in aanmerking nemen bij het definiëren van het bereik van IP-adressen voor de Virtual Network. Het IP-adres blijft toegewezen, totdat de virtuele machine en de bijbehorende netwerk interface worden verwijderd of totdat het IP-adres opnieuw wordt toegewezen. Lees [dit artikel][virtual-networks-static-private-ip-arm-pportal]voor meer informatie.

> [!NOTE]
> Wijs statische IP-adressen via Azure toe aan afzonderlijke Vnic's. Wijs geen vaste IP-adressen in het gast besturingssysteem toe aan een vNIC. Sommige Azure-Services, zoals Azure Backup-Service, zijn afhankelijk van het feit dat ten minste de primaire vNIC is ingesteld op DHCP en niet op statische IP-adressen. Zie ook het document [problemen met back-up van Azure Virtual Machine oplossen](../../../backup/backup-azure-vms-troubleshoot.md#networking).
>
>

##### <a name="multiple-nics-per-vm"></a>Meerdere Nic's per VM

U kunt meerdere virtuele netwerk interface kaarten (vNIC) definiëren voor een virtuele machine van Azure. Met de mogelijkheid om meerdere Vnic's in te stellen, kunt u beginnen met het instellen van netwerk verkeer, waarbij bijvoorbeeld client verkeer via één vNIC wordt doorgestuurd en het back-upverkeer via een tweede vNIC wordt doorgestuurd. Afhankelijk van het type VM zijn er verschillende beperkingen voor het aantal Vnic's dat aan een virtuele machine kan worden toegewezen. Precieze details, functionaliteit en beperkingen vindt u in de volgende artikelen:

* [Een Windows-VM met meerdere Nic's maken][virtual-networks-multiple-nics-windows]
* [Een virtuele Linux-machine met meerdere Nic's maken][virtual-networks-multiple-nics-linux]
* [Multi-NIC-Vm's implementeren met behulp van een sjabloon][virtual-network-deploy-multinic-arm-template]
* [Multi-NIC-Vm's implementeren met Power shell][virtual-network-deploy-multinic-arm-ps]
* [Multi-NIC-Vm's implementeren met behulp van de Azure CLI][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Site-naar-site-connectiviteit

Cross-premises bestaat uit virtuele machines van Azure en on-premises die zijn gekoppeld aan een transparante en permanente VPN-verbinding. Het wordt verwacht het meest voorkomende SAP-implementatie patroon te worden in Azure. De veronderstelling is dat operationele procedures en processen met SAP-exemplaren in azure transparant moeten werken. Dit betekent dat u deze systemen moet kunnen afdrukken en het SAP-transport systeem (TMS) kunt gebruiken voor het transporteren van wijzigingen van een ontwikkelings systeem in azure naar een test systeem, dat on-premises wordt geïmplementeerd. Meer documentatie over site-naar-site vindt u in [dit artikel][vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>VPN-tunnel apparaat

Als u een site-naar-site-verbinding (on-premises Data Center naar Azure Data Center) wilt maken, moet u een VPN-apparaat aanschaffen en configureren, of RRAS (Routing and Remote Access service) gebruiken dat is geïntroduceerd als een software onderdeel met Windows Server 2012.

* [Een virtueel netwerk maken met een site-naar-site-VPN-verbinding met behulp van Power shell][vpn-gateway-create-site-to-site-rm-powershell]
* [Informatie over VPN-apparaten voor verbinding met site-naar-site-VPN-gateways][vpn-gateway-about-vpn-devices]
* [Veelgestelde vragen VPN-gateways][vpn-gateway-vpn-faq]

![Site-naar-site-verbinding tussen on-premises en Azure][planning-guide-figure-600]

In de bovenstaande afbeelding ziet u dat er voor twee Azure-abonnementen subbereiken voor IP-adressen zijn gereserveerd voor gebruik in virtuele netwerken in Azure. De connectiviteit van het on-premises netwerk naar Azure wordt tot stand gebracht via VPN.

#### <a name="point-to-site-vpn"></a>Punt-naar-site-VPN

Voor punt-naar-site-VPN moet elke client computer verbinding maken met een eigen VPN in Azure. Voor de SAP-scenario's kijken we naar een punt-naar-site-verbinding is niet praktisch. Daarom worden er geen verdere verwijzingen gegeven voor punt-naar-site-VPN-connectiviteit.

Meer informatie vindt u hier
* [Een punt-naar-site-verbinding met een VNet configureren met Azure Portal](../../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [Een punt-naar-site-verbinding met een VNet configureren met behulp van PowerShell](../../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

#### <a name="multi-site-vpn"></a>VPN op meerdere locaties

Azure tegenwoordig biedt ook de mogelijkheid om multi-site VPN-connectiviteit te maken voor één Azure-abonnement. Er is eerder één abonnement beperkt tot één site-naar-site-VPN-verbinding. Deze beperking is verdwenen met multi-site VPN-verbindingen voor één abonnement. Dit maakt het mogelijk om meer dan één Azure-regio voor een specifiek abonnement te gebruiken via cross-premises configuraties.

Raadpleeg [dit artikel][vpn-gateway-create-site-to-site-rm-powershell] voor meer documentatie

#### <a name="vnet-to-vnet-connection"></a>VNet-naar-VNet-verbinding

Met VPN op meerdere locaties moet u een afzonderlijke Azure-Virtual Network in elk van de regio's configureren. Vaak hebt u echter de vereiste dat de software onderdelen in de verschillende regio's met elkaar moeten communiceren. In het ideale geval mag deze communicatie niet worden gerouteerd van de ene Azure-regio naar de lokale locatie en van daaruit naar de andere Azure-regio. Met de snelkoppeling biedt Azure de mogelijkheid om een verbinding te configureren van een Azure-Virtual Network in een regio naar een andere Azure-Virtual Network die wordt gehost in een andere regio. Deze functionaliteit wordt VNet-naar-VNet-verbinding genoemd. Meer informatie over deze functionaliteit vindt u hier: <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/> .

#### <a name="private-connection-to-azure-expressroute"></a>Particuliere verbinding met Azure ExpressRoute

Met Microsoft Azure ExpressRoute kunt u particuliere verbindingen maken tussen Azure-data centers en de on-premises infra structuur van de klant of in een co-locatie omgeving. ExpressRoute wordt aangeboden door verschillende MPLS (pakket switches) VPN-providers of andere netwerk serviceproviders. ExpressRoute-verbindingen gaan niet via het openbare internet. ExpressRoute-verbindingen bieden betere beveiliging, meer betrouw baarheid via meerdere parallelle circuits, hogere snelheden en lagere latenties dan typische verbindingen via internet.

Vind hier meer informatie over Azure ExpressRoute en-aanbiedingen:

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

Met Express route kunnen meerdere Azure-abonnementen via één ExpressRoute-circuit worden gemaakt, zoals hier wordt beschreven

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>Geforceerde Tunneling in het geval van cross-premises
Voor Vm's die on-premises domeinen toevoegen via site-naar-site-, punt-naar-site-of ExpressRoute, moet u ervoor zorgen dat de Internet proxy instellingen ook worden geïmplementeerd voor alle gebruikers in deze Vm's. Standaard worden software die wordt uitgevoerd in deze Vm's of gebruikers die een browser gebruiken voor toegang tot het Internet, niet via de bedrijfs proxy door lopen, maar direct via Azure verbinding maken met internet. Maar zelfs de proxy-instelling is geen 100%-oplossing om het verkeer via de bedrijfs proxy door te sturen, omdat het verantwoordelijk is voor de software en services om te controleren of er een proxy is. Als de software die in de virtuele machine wordt uitgevoerd, niet doet dat of een beheerder de instellingen manipuleert, kan verkeer naar Internet opnieuw worden uitgecheckt rechtstreeks via Azure naar Internet.

Als u een dergelijke directe Internet verbinding wilt voor komen, kunt u geforceerde tunneling configureren met site-naar-site-connectiviteit tussen on-premises en Azure. De gedetailleerde beschrijving van de functie voor geforceerde tunneling wordt hier gepubliceerd <https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

Geforceerde tunneling met ExpressRoute wordt ingeschakeld door klanten die een standaard route via de BGP-peering-sessies van ExpressRoute adverteren.

#### <a name="summary-of-azure-networking"></a>Samen vatting van Azure-netwerken

Dit hoofd stuk bevat veel belang rijke punten over Azure-netwerken. Hier volgt een samen vatting van de belangrijkste punten:

* Met virtuele Azure-netwerken kunt u een netwerk structuur in uw Azure-implementatie plaatsen. VNets kan worden geïsoleerd tegen elkaar of met behulp van netwerk beveiligings groepen verkeer tussen VNets kan worden beheerd.
* Virtuele netwerken van Azure kunnen worden gebruikt voor het toewijzen van IP-adresbereiken aan Vm's of het toewijzen van vaste IP-adressen aan Vm's
* Als u een site-naar-site-of punt-naar-site-verbinding wilt instellen, moet u eerst een Azure-Virtual Network maken
* Nadat een virtuele machine is geïmplementeerd, is het niet meer mogelijk om de Virtual Network die aan de VM zijn toegewezen, te wijzigen

### <a name="quotas-in-azure-virtual-machine-services"></a>Quota in azure virtual machine-Services
We moeten duidelijk zijn dat de opslag-en netwerk infrastructuur wordt gedeeld tussen Vm's met een verscheidenheid aan services in de Azure-infra structuur. Net als bij de eigen data centers van de klant is het over een zekere mate van het inrichten van een deel van de infrastructuur resources van kracht. Het Microsoft Azure-platform gebruikt schijf, CPU, netwerk en andere quota's om het Resource verbruik te beperken en om consistente en deterministische prestaties te behouden.  De verschillende VM-typen (A5, A6, enzovoort) hebben verschillende quota voor het aantal schijven, CPU, RAM en netwerk.

> [!NOTE]
> De CPU-en geheugen resources van de VM-typen die worden ondersteund door SAP, worden vooraf toegewezen op de host-knoop punten. Dit betekent dat als de virtuele machine is geïmplementeerd, de bronnen op de host beschikbaar zijn zoals gedefinieerd in het VM-type.
>
>

Bij het plannen en aanpassen van SAP on Azure oplossingen, moeten de quota's voor elke grootte van de virtuele machine worden overwogen. De VM-quota worden [hier (Linux)][virtual-machines-sizes-linux] en [hier (Windows)][virtual-machines-sizes-windows]beschreven.

De beschreven quota's vertegenwoordigen de theoretische maximum waarden.  De limiet voor IOPS per schijf kan worden bereikt met kleine I/O's (8 KB), maar mogelijk niet met een grote hoeveelheid I/O's (1 MB).  De limiet voor IOPS wordt afgedwongen voor de granulatie van één schijf.

Als een ruwe beslissings structuur om te bepalen of een SAP-systeem in azure virtual machine-Services en de mogelijkheden ervan past, of of een bestaand systeem anders moet worden geconfigureerd om het systeem in azure te kunnen implementeren, kan de onderstaande beslissings structuur worden gebruikt:

![Beslissings structuur om te bepalen of u SAP on Azure wilt implementeren][planning-guide-figure-700]

1. De belangrijkste informatie om mee te beginnen is de SAP'S-vereiste voor een bepaald SAP-systeem. De SAP'S-vereisten moeten worden gescheiden in het DBMS-onderdeel en het SAP-toepassings deel, zelfs als het SAP-systeem al on-premises is geïmplementeerd in een configuratie met twee lagen. Voor bestaande systemen kan de SAP'S die betrekking heeft op de hardware die vaak wordt gebruikt, worden bepaald of geschat op basis van bestaande SAP-benchmarks. De resultaten vindt u [hier](https://sap.com/about/benchmark.html). Voor nieuw geïmplementeerde SAP-systemen hebt u een schaal oefening nodig om de SAP'S-vereisten van het systeem te bepalen.
1. Voor bestaande systemen moet het I/O-volume en I/O-bewerkingen per seconde op de DBMS-server worden gemeten. Voor nieuwe geplande systemen moet de grootte van het nieuwe systeem ook een ruwe ideeën geven van de I/O-vereisten aan de DBMS-zijde. Als dat niet het geval is, moet u uiteindelijk het concept testen.
1. Vergelijk de SAP'S-vereiste voor de DBMS-server met de SAP'S de verschillende VM-typen van Azure kunnen bieden. De informatie over de SAP'S van de verschillende Azure VM-typen wordt beschreven in SAP Note [1928533]. De focus moet eerst op de DBMS-VM worden uitgevoerd, omdat de laag van de data base de laag is in een SAP NetWeaver-systeem dat niet kan worden uitgeschaald in het meren deel van de implementaties. De SAP-toepassingslaag daarentegen kan worden uitgeschaald. Als geen van de door SAP ondersteunde Azure VM-typen de vereiste SAP'S kan leveren, kan de werk belasting van het geplande SAP-systeem niet worden uitgevoerd op Azure. U moet het systeem on-premises implementeren of u moet het werkbelasting volume voor het systeem wijzigen.
1. Zoals hier wordt beschreven [(Linux)][virtual-machines-sizes-linux] en [hier (Windows)][virtual-machines-sizes-windows], DWINGt Azure een IOPS-quotum per schijf onafhankelijk af of u gebruikmaakt van standaard opslag of Premium Storage. Afhankelijk van het type virtuele machine, is het aantal gegevens schijven dat kan worden gekoppeld, afhankelijk van elkaar. Als gevolg hiervan kunt u een maximum aantal IOPS berekenen dat kan worden behaald met elk van de verschillende VM-typen. Afhankelijk van de bestands indeling van de Data Base kunt u schijven verwijderen om één volume in het gast besturingssysteem te worden. Als het huidige IOPS-volume van een geïmplementeerd SAP-systeem echter de berekende limieten overschrijdt van het grootste VM-type van Azure en als er geen kans is om te compenseren met meer geheugen, kan de werk belasting van het SAP-systeem ernstig worden beïnvloed. In dergelijke gevallen kunt u een punt aanraken waar u het systeem niet in azure moet implementeren.
1. Met name in SAP-systemen, die on-premises worden geïmplementeerd in configuraties met twee lagen, is de kans groot dat het systeem mogelijk moet worden geconfigureerd op Azure in een configuratie met drie lagen. In deze stap moet u controleren of er een onderdeel is in de SAP-toepassingslaag, die niet kan worden geschaald en niet in de CPU en het geheugen bronnen van de verschillende typen Azure VM-Services passen. Als er inderdaad een dergelijk onderdeel is, kan het SAP-systeem en de werk belasting niet in Azure worden geïmplementeerd. Maar als u de onderdelen van de SAP-toepassing in meerdere virtuele machines van Azure kunt schalen, kan het systeem in Azure worden geïmplementeerd.

Als de onderdelen DBMS en SAP Application Layer kunnen worden uitgevoerd in virtuele machines van Azure, moet de configuratie worden gedefinieerd met betrekking tot:

* Aantal virtuele Azure-machines
* VM-typen voor de afzonderlijke onderdelen
* Aantal Vhd's in DBMS-VM om voldoende IOPS te bieden

## <a name="managing-azure-assets"></a>Azure-assets beheren

### <a name="azure-portal"></a>Azure Portal

De Azure Portal is een van de drie interfaces voor het beheren van Azure VM-implementaties. De basis beheer taken, zoals het implementeren van Vm's uit installatie kopieën, kunnen worden uitgevoerd via de Azure Portal. Daarnaast zijn het maken van opslag accounts, virtuele netwerken en andere onderdelen van Azure ook taken die de Azure Portal kunnen verwerken. Functies zoals het uploaden van Vhd's van on-premises naar Azure of het kopiëren van een VHD binnen Azure zijn echter taken, waarvoor hulpprogram ma's van derden of beheer via Power shell of CLI zijn vereist.

![Microsoft Azure-portal-overzicht van virtuele machines][planning-guide-figure-800]


Beheer-en configuratie taken voor het exemplaar van de virtuele machine zijn mogelijk vanuit het Azure Portal.

Naast het opnieuw opstarten en afsluiten van een virtuele machine kunt u ook gegevens schijven voor het exemplaar van de virtuele machine koppelen, ontkoppelen en maken, om het exemplaar voor het voorbereiden van installatie kopieën vast te leggen en de grootte van het exemplaar van de virtuele machine te configureren.

De Azure Portal biedt basis functionaliteit voor het implementeren en configureren van Vm's en vele andere Azure-Services. Niet alle beschik bare functionaliteit wordt echter gedekt door de Azure Portal. In de Azure Portal is het niet mogelijk om taken uit te voeren zoals:

* Vhd's uploaden naar Azure
* Vm's kopiëren


### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Beheer via Microsoft Azure PowerShell-cmdlets

Windows Power shell is een krachtig en uitbreidbaar Framework dat veel is aangenomen door klanten die een groter aantal systemen in azure implementeren. Na de installatie van Power shell-cmdlets op een bureau blad, laptop of speciaal beheer station kunnen de Power shell-cmdlets op afstand worden uitgevoerd.

Het proces voor het inschakelen van een lokale desktop/laptop voor het gebruik van Azure PowerShell-cmdlets en het configureren van die voor het gebruik met de Azure-abonnement (en) wordt beschreven in [dit artikel][powershell-install-configure].

Meer gedetailleerde stappen voor het installeren, bijwerken en configureren van de Azure PowerShell-cmdlets vindt u in [dit hoofd stuk van de implementatie handleiding][deployment-guide-4.1].

De ervaring van de klant is tot nu toe dat Power shell (PS) het krachtige hulp middel is voor het implementeren van Vm's en het maken van aangepaste stappen in de implementatie van Vm's. Alle klanten die SAP-exemplaren in azure uitvoeren, maken gebruik van PS-cmdlets voor het aanvullen van beheer taken die worden uitgevoerd in de Azure Portal of die zelfs gebruik maken van PS-cmdlets om hun implementaties in azure te beheren. Omdat de Azure-specifieke cmdlets dezelfde naam Conventie delen als de meer dan 2000 Windows-gerelateerde cmdlets, is het een eenvoudige taak voor Windows-beheerders om deze cmdlets te gebruiken.

Bekijk hier het voor beeld: <https://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>


De implementatie van de Azure-extensie voor SAP (zie hoofd stuk [Azure-extensie voor SAP][planning-guide-9.1] in dit document) is alleen mogelijk via Power shell of cli. Daarom is het verplicht om Power shell of CLI in te stellen en te configureren bij het implementeren of beheren van een SAP net-Weaver-systeem in Azure.

Naarmate Azure meer functionaliteit biedt, worden er nieuwe PS-cmdlets toegevoegd waarvoor een update van de cmdlets is vereist. Daarom is het zinvol om de Azure-download site ten minste één keer per maand te controleren <https://azure.microsoft.com/downloads/> voor een nieuwe versie van de cmdlets. De nieuwe versie wordt boven op de oudere versie geïnstalleerd.

Voor een algemene lijst met Azure-gerelateerde Power shell-opdrachten raadpleegt u: <https://docs.microsoft.com/powershell/azure/> .

### <a name="management-via-microsoft-azure-cli-commands"></a>Beheer via Microsoft Azure CLI-opdrachten

Voor klanten die Linux gebruiken en Azure resources Power shell willen beheren, is mogelijk geen optie. Micro soft biedt Azure CLI als alternatief.
De Azure CLI bevat een aantal open source-en platformoverschrijdende opdrachten voor het werken met het Azure-platform. De Azure CLI biedt veel van de functionaliteit die in de Azure Portal is gevonden.

Zie voor informatie over de installatie, configuratie en het gebruik van CLI-opdrachten voor het uitvoeren van Azure tasks.

* [De klassieke Azure CLI installeren][xplat-cli]
* [Virtuele machines implementeren en beheren met behulp van Azure Resource Manager sjablonen en de Azure CLI] [.. /.. /linux/create-ssh-secured-vm-from-template.md]
* [Gebruik de klassieke Azure CLI voor Mac, Linux en Windows met Azure Resource Manager][xplat-cli-azure-resource-manager]

Lees ook hoofd stuk [Azure CLI voor Linux-vm's][deployment-guide-4.5.2] in de [implementatie handleiding][planning-guide] voor het gebruik van Azure CLI voor het implementeren van de Azure-extensie voor SAP.


## <a name="first-steps-planning-a-deployment"></a>Eerste stappen voor het plannen van een implementatie
De eerste stap in de implementatie planning is niet controleren op Vm's die beschikbaar zijn om SAP uit te voeren. De eerste stap is een tijd die tijdrovend is, maar het belangrijkste is dat u met nalevings-en beveiligings teams in uw bedrijf kunt werken met de grens waarden voor het implementeren van het type SAP-werk belasting of het bedrijfs proces in een open bare Cloud. Als uw bedrijf andere software vóór Azure heeft geïmplementeerd, kan het proces gemakkelijk zijn. Als uw bedrijf meer aan het begin van de reis is, zijn er mogelijk grotere discussies nodig om de grens waarden en beveiligings voorwaarden te achterhalen, waardoor bepaalde SAP-gegevens en SAP-bedrijfs processen kunnen worden gehost in een open bare Cloud.

Als handige Help kunt u naar [micro soft compliance-aanbiedingen](/microsoft-365/compliance/offering-home) verwijzen voor een lijst met compatibiliteits aanbiedingen die micro soft kan bieden.

Andere aspecten van problemen zoals gegevens versleuteling voor data-by-rest of andere versleuteling in de Azure-service wordt beschreven in [Azure Encryption Overview](../../../security/fundamentals/encryption-overview.md)(Engelstalig).

Te laag inschatten deze fase van het project niet in uw planning. Alleen als u akkoord en regels rond dit onderwerp hebt, moet u naar de volgende stap gaan. Dit is de planning van de netwerk architectuur die u in azure implementeert.


## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Verschillende manieren om Vm's voor SAP in azure te implementeren

In dit hoofd stuk vindt u informatie over de verschillende manieren om een virtuele machine in azure te implementeren. Meer voorbereidende procedures en de verwerking van Vhd's en virtuele machines in azure zijn in dit hoofd stuk opgenomen.

### <a name="deployment-of-vms-for-sap"></a>Implementatie van Vm's voor SAP

Microsoft Azure biedt meerdere manieren om Vm's en gekoppelde schijven te implementeren. Het is dus belang rijk om inzicht te krijgen in de verschillen sinds de voor bereidingen van de Vm's kunnen variëren, afhankelijk van de implementatie methode. In het algemeen bekijken we de volgende scenario's:

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a><a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Een virtuele machine verplaatsen van on-premises naar Azure met een niet-gegeneraliseerde schijf

U bent van plan een specifiek SAP-systeem van on-premises naar Azure te verplaatsen. Dit kan worden gedaan door het uploaden van de VHD, die het besturings systeem, de binaire bestanden van SAP en de binaire DBMS-bestanden bevat, plus de Vhd's met de gegevens en logbestanden van het DBMS naar Azure. In tegens telling tot [scenario #2 hieronder][planning-guide-5.1.2], de HOSTNAAM, SAP sid en SAP-gebruikers accounts in de virtuele Azure-machine blijven zoals ze zijn geconfigureerd in de on-premises omgeving. Daarom is het niet nodig om de installatie kopie te generaliseren. Zie hoofd stukken [voor bereiding voor het verplaatsen van een virtuele machine van on-premises naar Azure met een niet-algemene schijf][planning-guide-5.2.1] van dit document voor on-premises voorbereidende stappen en het uploaden van niet-gegeneraliseerde Vm's of Vhd's naar Azure. Lees hoofd stuk [3: een virtuele machine verplaatsen van on-premises met behulp van een niet-gegeneraliseerde Azure VHD met SAP][deployment-guide-3.4] in de [implementatie handleiding][deployment-guide] voor gedetailleerde stappen voor het implementeren van een dergelijke installatie kopie in Azure.

Een andere optie die niet meer in deze hand leiding wordt beschreven, maakt gebruik van Azure Site Recovery om SAP NetWeaver-toepassings servers en SAP NetWeaver-centrale Services naar Azure te repliceren. Het is niet raadzaam om Azure Site Recovery te gebruiken voor de data base-laag en liever data base-specifieke replicatie mechanismen te gebruiken, zoals HANA-systeem replicatie. Zie voor meer informatie hoofd stuk [beveiligen SAP](../../../site-recovery/site-recovery-workload.md#protect-sap) van de hand leiding [over nood herstel voor on-premises apps](../../../site-recovery/site-recovery-workload.md) .

#### <a name="deploying-a-vm-with-a-customer-specific-image"></a><a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Een VM implementeren met een klantspecifieke installatie kopie

Vanwege specifieke patch vereisten van uw besturings systeem of DBMS-versie is het mogelijk dat de opgegeven installatie kopieën in azure Marketplace niet aan uw behoeften voldoen. Daarom moet u mogelijk een virtuele machine maken met behulp van uw eigen privé-installatie kopie voor het besturings systeem/DBMS-VM, die meermaals kan worden geïmplementeerd. Om een dergelijke persoonlijke installatie kopie voor te bereiden voor duplicatie, moeten de volgende items in overweging worden genomen:

---
> ![Windows-logo.][Logo_Windows] Windows
>
> Meer informatie vindt u hier: <https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed> de Windows-instellingen (zoals Windows sid en hostname) moeten worden abstracted/gegeneraliseerd op de on-premises VM via de Sysprep-opdracht.
>
>
> ![Linux-logo.][Logo_Linux] Linux
>
> Volg de stappen die worden beschreven in deze artikelen voor [SuSE][virtual-machines-linux-create-upload-vhd-suse], [Red Hat][virtual-machines-linux-redhat-create-upload-vhd]of [Oracle Linux][virtual-machines-linux-create-upload-vhd-oracle], om een VHD voor te bereiden die naar Azure moet worden geüpload.
>
>

---
Als u SAP-inhoud al hebt geïnstalleerd in uw on-premises VM (met name voor systemen met twee lagen), kunt u de SAP-systeem instellingen na de implementatie van de Azure-VM aanpassen via de procedure voor het wijzigen van de naam van het exemplaar dat wordt ondersteund door de SAP software Provisioning Manager (SAP-notitie [1619720]). Zie hoofd stukken [voorbereiden voor het implementeren van een virtuele machine met een klantspecifieke installatie kopie voor SAP][planning-guide-5.2.2] en [het uploaden van een VHD van on-premises naar Azure][planning-guide-5.3.2] van dit document voor on-premises voorbereidende stappen en het uploaden van een gegeneraliseerde VM naar Azure. Lees hoofd stuk [scenario 2: een virtuele machine implementeren met een aangepaste installatie kopie voor SAP][deployment-guide-3.3] in de [implementatie handleiding][deployment-guide] voor gedetailleerde stappen voor het implementeren van een dergelijke installatie kopie in Azure.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Een virtuele machine implementeren vanuit Azure Marketplace

U wilt een VM-installatie kopie van micro soft of een derde partij gebruiken uit Azure Marketplace om uw virtuele machine te implementeren. Nadat u uw virtuele machine in azure hebt geïmplementeerd, volgt u dezelfde richt lijnen en hulpprogram ma's voor het installeren van de SAP-software en/of het DBMS in uw VM, net zoals u zou doen in een on-premises omgeving. Zie voor meer gedetailleerde beschrijving van de implementatie hoofd stuk [scenario 1: een virtuele machine implementeren vanuit de Azure Marketplace voor SAP][deployment-guide-3.2] in de [implementatie handleiding][deployment-guide].

### <a name="preparing-vms-with-sap-for-azure"></a><a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Vm's voorbereiden met SAP voor Azure

Voordat u Vm's uploadt naar Azure, moet u ervoor zorgen dat de virtuele machines en Vhd's aan bepaalde vereisten voldoen. Er zijn kleine verschillen, afhankelijk van de implementatie methode die wordt gebruikt.

#### <a name="preparation-for-moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a><a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Voor bereiding voor het verplaatsen van een virtuele machine van on-premises naar Azure met een niet-gegeneraliseerde schijf

Een veelvoorkomende implementatie methode is het verplaatsen van een bestaande virtuele machine, waarmee een SAP-systeem van on-premises naar Azure wordt uitgevoerd. Deze VM en het SAP-systeem in de virtuele machine moeten net in Azure worden uitgevoerd met dezelfde hostnaam en waarschijnlijk dezelfde SAP-SID. In dit geval moet het gast besturingssysteem van de virtuele machine niet worden gegeneraliseerd voor meerdere implementaties. Als het on-premises netwerk in Azure is uitgebreid, kunnen zelfs dezelfde domein accounts in de virtuele machine worden gebruikt als die voor on-premises zijn gebruikt.

De vereisten voor het voorbereiden van uw eigen Azure VM-schijf zijn:

* Oorspronkelijk kan de VHD met het besturings systeem een maximum grootte hebben van 127 GB. Deze beperking is eind maart 2015. De VHD met het besturings systeem kan nu Maxi maal 1 TB groot zijn als andere Azure Storage gehoste VHD.
* Deze moet de vaste VHD-indeling hebben. Dynamische Vhd's of Vhd's in VHDx-indeling worden nog niet ondersteund in Azure. Dynamische Vhd's worden geconverteerd naar statische Vhd's wanneer u de VHD uploadt met Power shell Commandlets of CLI
* Vhd's die zijn gekoppeld aan de virtuele machine en in azure opnieuw moeten worden gekoppeld aan de virtuele machine moeten ook een vaste VHD-indeling hebben. Lees [dit artikel](../../managed-disks-overview.md) voor maximale grootte van gegevens schijven. Dynamische Vhd's worden geconverteerd naar statische Vhd's wanneer u de VHD uploadt met Power shell Commandlets of CLI
* Voeg nog een lokaal account met beheerders bevoegdheden toe, dat kan worden gebruikt door micro soft-ondersteuning of dat kan worden toegewezen als context voor services en toepassingen om uit te voeren totdat de virtuele machine wordt geïmplementeerd en er meer geschikte gebruikers kunnen worden gebruikt.
* Voeg andere lokale accounts toe, omdat deze mogelijk nodig zijn voor het specifieke implementatie scenario.

---
> ![Windows-logo.][Logo_Windows] Windows
>
> In dit scenario is geen generalisatie (Sysprep) van de VM vereist voor het uploaden en implementeren van de virtuele machine in Azure.
> Zorg ervoor dat het station D:\ wordt niet gebruikt.
> Stel de schijf automatisch koppelen voor gekoppelde schijven in, zoals wordt beschreven in het hoofd stuk [instelling automatisch koppelen voor gekoppelde schijven][planning-guide-5.5.3] in dit document.
>
> ![Linux-logo.][Logo_Linux] Linux
>
> In dit scenario is geen generalisatie (waagent-deprovision) van de VM vereist voor het uploaden en implementeren van de virtuele machine in Azure.
> Zorg ervoor dat/mnt/resource niet wordt gebruikt en dat alle schijven zijn gekoppeld via uuid. Voor de besturingssysteem schijf moet u ervoor zorgen dat de bootloader-vermelding ook de op uuid gebaseerde koppeling aangeeft.
>
>

---
#### <a name="preparation-for-deploying-a-vm-with-a-customer-specific-image-for-sap"></a><a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Voor bereiding voor het implementeren van een virtuele machine met een klantspecifieke installatie kopie voor SAP

VHD-bestanden met een gegeneraliseerd besturings systeem worden opgeslagen in containers op Azure Storage accounts of als beheerde schijf installatie kopieën. U kunt een nieuwe VM van een dergelijke installatie kopie implementeren door te verwijzen naar de VHD-of beheerde schijf kopie als bron in de sjabloon bestanden van uw implementatie, zoals wordt beschreven in hoofd stuk [scenario 2: een virtuele machine implementeren met een aangepaste installatie kopie voor SAP][deployment-guide-3.3] van de [implementatie handleiding][deployment-guide].

De vereisten voor het voorbereiden van uw eigen Azure VM-installatie kopie zijn:

* Oorspronkelijk kan de VHD met het besturings systeem een maximum grootte hebben van 127 GB. Deze beperking is eind maart 2015. De VHD met het besturings systeem kan nu Maxi maal 1 TB groot zijn als andere Azure Storage gehoste VHD.
* Deze moet de vaste VHD-indeling hebben. Dynamische Vhd's of Vhd's in VHDx-indeling worden nog niet ondersteund in Azure. Dynamische Vhd's worden geconverteerd naar statische Vhd's wanneer u de VHD uploadt met Power shell Commandlets of CLI
* Vhd's die zijn gekoppeld aan de virtuele machine en in azure opnieuw moeten worden gekoppeld aan de virtuele machine moeten ook een vaste VHD-indeling hebben. Lees [dit artikel](../../managed-disks-overview.md) voor maximale grootte van gegevens schijven. Dynamische Vhd's worden geconverteerd naar statische Vhd's wanneer u de VHD uploadt met Power shell Commandlets of CLI
* Voeg andere lokale accounts toe, omdat deze mogelijk nodig zijn voor het specifieke implementatie scenario.
* Als de installatie kopie een SAP net-computer bevat en de naam van de hostnaam van de oorspronkelijke naam op het punt van de Azure-implementatie wordt gewijzigd, wordt het aanbevolen om de nieuwste versies van de SAP software Provisioning manager-DVD naar de sjabloon te kopiëren. Zo kunt u eenvoudig de SAP-functie naamswijziging gebruiken om de gewijzigde hostnaam en/of de SID van het SAP-systeem binnen de geïmplementeerde VM-installatie kopie aan te passen zodra er een nieuwe kopie wordt gestart.

---
> ![Windows-logo.][Logo_Windows] Windows
>
> Zorg ervoor dat het station D:\ wordt niet gebruikt om schijf automatisch koppelen in te stellen voor gekoppelde schijven, zoals wordt beschreven in het hoofd stuk [instelling automatisch koppelen voor gekoppelde schijven][planning-guide-5.5.3] in dit document.
>
> ![Linux-logo.][Logo_Linux] Linux
>
> Zorg ervoor dat/mnt/resource niet wordt gebruikt en dat alle schijven zijn gekoppeld via uuid. Zorg ervoor dat voor de besturingssysteem schijf ook de op uuid gebaseerde koppeling wordt weer gegeven.
>
>

---
* SAP-gebruikers interface (voor beheer-en installatie doeleinden) kan in een dergelijke sjabloon vooraf worden geïnstalleerd.
* Andere software die nodig is voor het uitvoeren van de virtuele machines in cross-premises scenario's kan worden geïnstalleerd zolang deze software kan werken met de naam van de virtuele machine.

Als de virtuele machine voldoende is voor het algemeen en uiteindelijk onafhankelijk van accounts/gebruikers die niet beschikbaar zijn in het beoogde implementatie scenario van Azure, wordt de laatste voorbereidende stap van het generaliseren van een dergelijke installatie kopie uitgevoerd.

##### <a name="generalizing-a-vm"></a>Een virtuele machine generaliseren
---
> ![Windows-logo.][Logo_Windows] Windows
>
> De laatste stap bestaat uit het aanmelden bij een virtuele machine met een beheerders account. Open een Windows-opdracht venster als *beheerder*. Ga naar%windir%\Windows\System32\Sysprep en voer sysprep.exe uit.
> Er wordt een klein venster weer gegeven. Het is belang rijk om de optie **generalize** te controleren (de standaard instelling is uitgeschakeld) en de afsluit optie te wijzigen van de standaard waarde voor opnieuw opstarten in afsluiten. Bij deze procedure wordt ervan uitgegaan dat het Sysprep-proces on-premises wordt uitgevoerd in het gast besturingssysteem van een virtuele machine.
> Als u de procedure wilt uitvoeren met een virtuele machine die al in azure wordt uitgevoerd, volgt u de stappen die in [dit artikel](../../windows/capture-image-resource.md)worden beschreven.
>
> ![Linux-logo.][Logo_Linux] Linux
>
> [Een virtuele Linux-machine vastleggen om deze als Resource Manager-sjabloon te gebruiken][capture-image-linux-step-2-create-vm-image]
>
>

---
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Vm's en Vhd's verplaatsen tussen on-premises naar Azure
Omdat het uploaden van VM-installatie kopieën en schijven naar Azure niet mogelijk is via de Azure Portal, moet u Azure PowerShell-cmdlets of CLI gebruiken. Een andere mogelijkheid is het gebruik van het hulp programma ' AzCopy '. Het hulp programma kan Vhd's kopiëren tussen on-premises en Azure (in beide richtingen). Er kunnen ook Vhd's tussen Azure-regio's worden gekopieerd. Raadpleeg [deze documentatie][storage-use-azcopy] voor meer informatie over het downloaden en gebruiken van AzCopy.

Een derde alternatief zou gebruikmaken van verschillende GUI-hulpprogram ma's van derden. Zorg er echter voor dat deze hulpprogram ma's Azure-pagina-blobs ondersteunen. Voor onze doel einden moeten we Azure page BLOB Store gebruiken (de verschillen worden hier beschreven: <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs> ). De hulpprogram ma's die worden meegeleverd door Azure, zijn ook efficiënt bij het comprimeren van de Vm's en Vhd's, die moeten worden geüpload. Dit is belang rijk omdat deze efficiency in compressie de upload tijd vermindert (wat nu varieert, afhankelijk van de upload koppeling naar Internet vanaf de on-premises faciliteit en de beoogde Azure-implementatie regio). Het is een billijke veronderstelling dat het uploaden van een virtuele machine of VHD van een Europese locatie naar de in de VS gebaseerde Azure-data centers langer duurt dan het uploaden van dezelfde Vm's/Vhd's naar de Europese Azure-data centers.

#### <a name="uploading-a-vhd-from-on-premises-to-azure"></a><a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Een VHD uploaden van on-premises naar Azure
Voor het uploaden van een bestaande virtuele machine of VHD vanuit het on-premises netwerk, een virtuele machine of VHD moet voldoen aan de vereisten zoals vermeld in hoofdstuk [voorbereiding voor het verplaatsen van een virtuele machine van on-premises naar Azure met een niet-gegeneraliseerde schijf][planning-guide-5.2.1] van dit document.

Een dergelijke virtuele machine hoeft niet te worden gegeneraliseerd en kan worden geüpload in de status en de vorm die is na het afsluiten aan de on-premises kant. Dit geldt ook voor extra Vhd's, die geen besturings systeem bevatten.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Een VHD uploaden en hiervan een Azure-schijf maken
In dit geval willen we een VHD uploaden, hetzij met ofwel zonder een besturings systeem, en deze koppelen aan een virtuele machine als een gegevens schijf of gebruiken als besturingssysteem schijf. Dit is een proces met meerdere stappen

**PowerShell**

* Meld u aan bij uw abonnement met *Connect-AzAccount*
* Stel het abonnement van uw context in met *set-AzContext* en para meter SubscriptionId of subscriptionname-Zie <https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Upload de VHD met *add-AzVhd* naar een Azure Storage-account-Zie <https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* Beschrijving Een beheerde schijf maken op basis van de VHD met *New-AzDisk*  -Zie <https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk>
* De besturingssysteem schijf van een nieuwe VM-configuratie instellen op de VHD of beheerde schijf met *set-AzVMOSDisk* -Zie <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
* Een nieuwe virtuele machine maken op basis van de configuratie van de virtuele machine met *New-AzVM* -Zie <https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>
* Een gegevens schijf toevoegen aan een nieuwe virtuele machine met *add-AzVMDataDisk* -Zie <https://docs.microsoft.com/powershell/module/az.compute/add-Azvmdatadisk>

**Azure CLI**

* Meld u aan bij uw abonnement met *AZ login*
* Selecteer uw abonnement met *AZ account set--Subscription `<subscription name or id` >*
* De VHD uploaden met *AZ Storage BLOB upload* -Zie [de Azure CLI gebruiken met Azure Storage][storage-azure-cli]
* Beschrijving Een beheerde schijf maken op basis van de VHD met *AZ Disk Create* -Zie https://docs.microsoft.com/cli/azure/disk
* Een nieuwe VM maken met de geüploade VHD of Managed Disk als besturingssysteem schijf met *AZ VM Create* en para meter *--attach-OS-Disk*
* Een gegevens schijf toevoegen aan een nieuwe virtuele machine met *AZ VM Disk attach* en para meter *--New*

**Sjabloon**

* De VHD uploaden met Power shell of Azure CLI
* Beschrijving Een beheerde schijf maken op basis van de VHD met Power shell, Azure CLI of de Azure Portal
* Implementeer de virtuele machine met een JSON-sjabloon die verwijst naar de VHD, zoals wordt weer gegeven in [dit voor beeld-JSON-sjabloon](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) of met Managed disks zoals weer gegeven in [dit voor beeld JSON-sjabloon](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json).

#### <a name="deployment-of-a-vm-image"></a>Implementatie van een VM-installatie kopie
Als u een bestaande virtuele machine of VHD vanuit het on-premises netwerk wilt uploaden, moet u deze gebruiken als een Azure VM-installatie kopie, zoals een virtuele machine of VHD die voldoet aan de vereisten die worden vermeld in hoofdstuk [voorbereiding voor het implementeren van een virtuele machine met een klantspecifieke installatie kopie voor SAP][planning-guide-5.2.2] van dit document.

* Gebruik *Sysprep* op Windows of *waagent-deprovisioning* in Linux om uw VM te generaliseren: Zie [technische Naslag informatie over Sysprep](/previous-versions/windows/it-pro/windows-vista/cc766049(v=ws.10)) voor Windows of [hoe u een virtuele Linux-machine vastlegt voor gebruik als Resource Manager-sjabloon][capture-image-linux-step-2-create-vm-image] voor Linux
* Meld u aan bij uw abonnement met *Connect-AzAccount*
* Stel het abonnement van uw context in met *set-AzContext* en para meter SubscriptionId of subscriptionname-Zie <https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* Upload de VHD met *add-AzVhd* naar een Azure Storage-account-Zie <https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* Beschrijving Een beheerde schijf installatie kopie maken van de VHD met *New-AzImage*  -Zie <https://docs.microsoft.com/powershell/module/az.compute/new-Azimage>
* Stel de besturingssysteem schijf van een nieuwe VM-configuratie in op de
  * VHD met *set-AzVMOSDisk-SourceImageUri-CreateOption fromImage* -Zie <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
  * Beheerde schijf installatie kopie *instellen-AzVMSourceImage* -Zie <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmsourceimage>
* Een nieuwe virtuele machine maken op basis van de configuratie van de virtuele machine met *New-AzVM* -Zie <https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>

**Azure CLI**

* Gebruik *Sysprep* op Windows of *waagent-deprovisioning* in Linux om uw VM te generaliseren: Zie [technische Naslag informatie over Sysprep](/previous-versions/windows/it-pro/windows-vista/cc766049(v=ws.10)) voor Windows of [hoe u een virtuele Linux-machine vastlegt voor gebruik als Resource Manager-sjabloon][capture-image-linux-step-2-create-vm-image] voor Linux
* Meld u aan bij uw abonnement met *AZ login*
* Selecteer uw abonnement met *AZ account set--Subscription `<subscription name or id` >*
* De VHD uploaden met *AZ Storage BLOB upload* -Zie [de Azure CLI gebruiken met Azure Storage][storage-azure-cli]
* Beschrijving Een beheerde schijf installatie kopie maken van de VHD met *AZ image Create* -Zie https://docs.microsoft.com/cli/azure/image
* Een nieuwe VM maken met de geüploade VHD of de beheerde schijf installatie kopie als besturingssysteem schijf met *AZ VM Create* and para meter *--Image*

**Sjabloon**

* Gebruik *Sysprep* op Windows of *waagent-deprovisioning* in Linux om uw VM te generaliseren: Zie [technische Naslag informatie over Sysprep](/previous-versions/windows/it-pro/windows-vista/cc766049(v=ws.10)) voor Windows of [hoe u een virtuele Linux-machine vastlegt voor gebruik als Resource Manager-sjabloon][capture-image-linux-step-2-create-vm-image] voor Linux
* De VHD uploaden met Power shell of Azure CLI
* Beschrijving Een beheerde schijf installatie kopie maken van de VHD met Power shell, Azure CLI of de Azure Portal
* Implementeer de virtuele machine met een JSON-sjabloon die verwijst naar de afbeelding VHD, zoals wordt weer gegeven in [dit voor beeld-JSON-sjabloon](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) of met behulp van de beheerde schijf installatie kopie, zoals wordt weer gegeven in [dit voor beeld](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json)

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>Vhd's of Managed Disks op on-premises downloaden
De Azure-infra structuur als een service is geen eenrichtings straat met alleen Vhd's en SAP-systemen kunnen worden geüpload. U kunt SAP-systemen ook verplaatsen van Azure naar de on-premises wereld.

Tijdens de download tijd kan de Vhd's of de Managed Disks niet actief zijn. Zelfs bij het downloaden van schijven die zijn gekoppeld aan Vm's, moet de virtuele machine worden afgesloten en opnieuw worden toegewezen. Als u de inhoud van de data base alleen wilt downloaden, en dat vervolgens moet worden gebruikt voor het instellen van een nieuw systeem on-premises en als het acceptabel is dat tijdens het downloaden en de installatie van het nieuwe systeem nog steeds operationeel kunnen zijn, kunt u een lange uitval tijd voor komen door een gecomprimeerde back-up van de data base in een schijf uit te voeren en alleen die schijf te downloaden in plaats van de basis-VM van het besturings systeem

#### <a name="powershell"></a>PowerShell

* Als u een beheerde schijf wilt downloaden, moet u eerst toegang krijgen tot de onderliggende blob van de beheerde schijf. Vervolgens kunt u de onderliggende BLOB kopiëren naar een nieuw opslag account en de BLOB downloaden van dit opslag account.

  ```powershell
  $access = Grant-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name> -Access Read -DurationInSecond 3600
  $key = (Get-AzStorageAccountKey -ResourceGroupName <resource group> -Name <storage account name>)[0].Value
  $destContext = (New-AzStorageContext -StorageAccountName <storage account name -StorageAccountKey $key)
  Start-AzStorageBlobCopy -AbsoluteUri $access.AccessSAS -DestContainer <container name> -DestBlob <blob name> -DestContext $destContext
  # Wait for blob copy to finish
  Get-AzStorageBlobCopyState -Container <container name> -Blob <blob name> -Context $destContext
  Save-AzVhd -SourceUri <blob in new storage account> -LocalFilePath <local file path> -StorageKey $key
  # Wait for download to finish
  Revoke-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name>
  ```

* Een VHD downloaden zodra het SAP-systeem is gestopt en de VM wordt afgesloten, kunt u de Power shell-cmdlet `Save-AzVhd` op het on-premises doel gebruiken om de VHD-schijven terug te downloaden naar de on-premises wereld. Hiervoor hebt u de URL van de VHD nodig, die u kunt vinden in de sectie opslag van de Azure Portal (moet u navigeren naar het opslag account en de opslag container waar de VHD is gemaakt) en u moet weten waar de VHD moet worden gekopieerd.

  Vervolgens kunt u gebruikmaken van de opdracht door de para meter SourceUri te definiëren als de URL van de VHD die u wilt downloaden en de LocalFilePath als de fysieke locatie van de VHD (inclusief de naam). De opdracht kan er als volgt uitzien:

  ```powershell
  Save-AzVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  Zie hier voor meer informatie over de Save-AzVhd-cmdlet <https://docs.microsoft.com/powershell/module/az.compute/save-Azvhd> .

#### <a name="azure-cli"></a>Azure CLI
* Als u een beheerde schijf wilt downloaden, moet u eerst toegang krijgen tot de onderliggende blob van de beheerde schijf. Vervolgens kunt u de onderliggende BLOB kopiëren naar een nieuw opslag account en de BLOB downloaden van dit opslag account.

  ```azurecli
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

* Een VHD downloaden zodra het SAP-systeem is gestopt en de VM wordt afgesloten, kunt u de Azure CLI-opdracht `_azure storage blob download_` op het on-premises doel gebruiken om de VHD-schijven terug te downloaden naar de on-premises wereld. Hiervoor hebt u de naam en de container van de VHD nodig. deze kunt u vinden in de sectie opslag van de Azure Portal (Navigeer naar het opslag account en de opslag container waar de VHD is gemaakt). u moet weten waar de VHD moet worden gekopieerd.

  Vervolgens kunt u gebruikmaken van de opdracht door de para meters Blob en container van de VHD te definiëren die u wilt downloaden en de bestemming als de fysieke doel locatie van de VHD (inclusief de naam). De opdracht kan er als volgt uitzien:

  ```azurecli
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>Vm's en schijven verplaatsen binnen Azure

#### <a name="copying-sap-systems-within-azure"></a>SAP-systemen kopiëren in azure

Een SAP-systeem of zelfs een toegewezen DBMS-server die een SAP-toepassingslaag ondersteunt, bestaat waarschijnlijk uit verschillende schijven, die het besturings systeem bevatten met de binaire bestanden of de gegevens en logboek bestanden van de SAP-data base. De functionaliteit van het kopiëren van schijven en de functionaliteit van het opslaan van schijven op een lokale schijf met behulp van een synchronisatie mechanisme, waarbij meerdere schijven op een consistente manier worden gearchiveerd. Daarom zou de status van de gekopieerde of opgeslagen schijven, zelfs als deze zijn gekoppeld aan dezelfde VM, anders zijn. Dit betekent dat de data base in het einde van het beton niet consistent is, in het geval van een andere gegevens en logboek bestanden die zich op de verschillende schijven bevinden.

**Conclusie: om schijven te kopiëren of op te slaan, die deel uitmaken van een SAP-systeem configuratie, moet u het SAP-systeem stoppen en moet u ook de geïmplementeerde VM afsluiten. U kunt de set schijven alleen kopiëren of downloaden om een kopie van het SAP-systeem in azure of on-premises te maken.**

Gegevens schijven kunnen worden opgeslagen als VHD-bestanden in een Azure Storage-account en kunnen rechtstreeks worden gekoppeld aan een virtuele machine of als een installatie kopie worden gebruikt. In dit geval wordt de VHD gekopieerd naar een andere locatie voordat deze wordt gekoppeld aan de virtuele machine. De volledige naam van het VHD-bestand in azure moet uniek zijn binnen Azure. Zoals eerder vermeld, is de naam een soort naam die uit drie delen bestaat, zoals:

`http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>`

Gegevens schijven kunnen ook worden Managed Disks. In dit geval wordt de beheerde schijf gebruikt om een nieuwe beheerde schijf te maken voordat deze wordt gekoppeld aan de virtuele machine. De naam van de beheerde schijf moet uniek zijn binnen een resource groep.

##### <a name="powershell"></a>PowerShell

U kunt Azure PowerShell-cmdlets gebruiken om een VHD te kopiëren zoals wordt weer gegeven in [dit artikel][storage-powershell-guide-full-copy-vhd]. Als u een nieuwe beheerde schijf wilt maken, gebruikt u New-AzDiskConfig en New-AzDisk, zoals in het volgende voor beeld wordt weer gegeven.

```powershell
$config = New-AzDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="azure-cli"></a>Azure CLI

U kunt Azure CLI gebruiken om een VHD te kopiëren. Gebruik *AZ Disk Create* zoals wordt weer gegeven in het volgende voor beeld om een nieuwe beheerde schijf te maken.

```azurecli
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Azure Storage-hulpprogram ma's

* <https://storageexplorer.com/>

Professional-edities van Azure Storage Explorers vindt u hier:

* <https://www.cerebrata.com/>
* <https://clumsyleaf.com/products/cloudxplorer>

Het exemplaar van een VHD zelf in een opslag account is een proces dat slechts enkele seconden duurt (vergelijkbaar met SAN-hardware die moment opnamen maakt met vertraagd kopiëren en kopiëren bij schrijven). Nadat u een kopie van het VHD-bestand hebt, kunt u dit koppelen aan een virtuele machine of als installatie kopie gebruiken om kopieën van de VHD aan virtuele machines te koppelen.

##### <a name="powershell"></a>PowerShell

```powershell
# attach a vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -ManagedDiskId <managed disk id> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a copy of the vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name <disk name> -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption fromImage
$vm | Update-AzVM

# attach a copy of the managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$diskConfig = New-AzDiskConfig -Location $vm.Location -CreateOption Copy -SourceUri <source managed disk id>
$disk = New-AzDisk -DiskName <disk name> -Disk $diskConfig -ResourceGroupName <resource group name>
$vm = Add-AzVMDataDisk -VM $vm -Caching <caching option> -Lun <lun, for example 0> -CreateOption attach -ManagedDiskId $disk.Id
$vm | Update-AzVM
```

##### <a name="azure-cli"></a>Azure CLI

```azurecli

# attach a vhd to a vm
az vm unmanaged-disk attach --resource-group <resource group name> --vm-name <vm name> --vhd-uri <path to vhd>

# attach a managed disk to a vm
az vm disk attach --resource-group <resource group name> --vm-name <vm name> --disk <managed disk id>

# attach a copy of the vhd to a vm
# this scenario is currently not possible with Azure CLI. A workaround is to manually copy the vhd to the destination.

# attach a copy of a managed disk to a vm
az disk create --name <new disk name> --resource-group <resource group name> --location <location of target virtual machine> --source <source managed disk id>
az vm disk attach --disk <new disk name or managed disk id> --resource-group <resource group name> --vm-name <vm name> --caching <caching option> --lun <lun, for example 0>
```

#### <a name="copying-disks-between-azure-storage-accounts"></a><a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Schijven kopiëren tussen Azure Storage accounts
Deze taak kan niet worden uitgevoerd op de Azure Portal. U kunt Azure PowerShell-cmdlets, Azure CLI of een opslag browser van derden gebruiken. De Power shell-cmdlets of CLI-opdrachten kunnen blobs maken en beheren, waaronder de mogelijkheid om blobs asynchroon te kopiëren tussen opslag accounts en andere regio's binnen het Azure-abonnement.

##### <a name="powershell"></a>PowerShell
U kunt ook Vhd's kopiëren tussen abonnementen. Lees [dit artikel][storage-powershell-guide-full-copy-vhd]voor meer informatie.

De basis stroom van de PS-cmdlet-logica ziet er als volgt uit:

* Een opslag account context maken voor het **bron** opslag account met *New-AzStorageContext* -Zie <https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* Een opslag account context maken voor het **doel** -opslag account met *New-AzStorageContext* -Zie <https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* De kopie starten met

```powershell
Start-AzStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* De status van de kopie in een lus controleren met

```powershell
Get-AzStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Koppel de nieuwe VHD aan een virtuele machine zoals hierboven wordt beschreven.

Zie [dit artikel][storage-powershell-guide-full-copy-vhd]voor voor beelden.

##### <a name="azure-cli"></a>Azure CLI
* De kopie starten met

```azurecli
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* De status controleren als de kopie zich nog in een lus bevindt met

```azurecli
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* Koppel de nieuwe VHD aan een virtuele machine zoals hierboven wordt beschreven.

### <a name="disk-handling"></a>Schijf verwerking

#### <a name="vmdisk-structure-for-sap-deployments"></a><a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>VM/schijf-structuur voor SAP-implementaties

In het ideale geval is de verwerking van de structuur van een virtuele machine en de bijbehorende schijven eenvoudig. Bij on-premises installaties ontwikkelde klanten veel manieren voor het structureren van een server installatie.

* Eén basis schijf, die het besturings systeem en alle binaire bestanden van het DBMS en/of SAP bevat. Sinds 2015 maart kan deze schijf Maxi maal 1 TB groot zijn in plaats van eerder ingestelde beperkingen tot 127 GB.
* Een of meer schijven, die het DBMS-logboek bestand van de SAP-data base en het logboek bestand van het tijdelijke DBMS-opslag gebied bevatten (als het DBMS dit ondersteunt). Als de vereisten voor IOPS van het database logboek hoog zijn, moet u meerdere schijven verwijderen om het IOPS-volume te bereiken dat vereist is.
* Een aantal schijven met een of twee database bestanden van de SAP-data base en de tijdelijke DBMS-gegevens bestanden ook (als het DBMS dit ondersteunt).

![Referentie configuratie van Azure IaaS VM voor SAP][planning-guide-figure-1300]


---
> ![Windows-logo.][Logo_Windows] Windows
>
> Bij veel klanten hebben we configuraties gezien waarbij bijvoorbeeld de binaire bestanden van SAP en DBMS niet zijn geïnstalleerd op de c:\ station waarop het besturings systeem is geïnstalleerd. Er zijn verschillende redenen hiervoor, maar wanneer we terugvallen op de hoofdmap, was het meestal dat de stations klein waren en upgrades voor het besturings systeem meer ruimte 10-15 jaar geleden nodig hadden. Voor beide voor waarden gelden deze dagen te vaak niet meer. De c:\ het station kan worden toegewezen op grote volume schijven of Vm's. Om implementaties eenvoudig in hun structuur te houden, wordt aanbevolen het volgende implementatie patroon te volgen voor SAP NetWeaver-systemen in azure
>
> Het wissel bestand van het Windows-besturings systeem moet zich op het station D: (niet-permanente schijf)
>
> ![Linux-logo.][Logo_Linux] Linux
>
> Plaats de Linux-swapfile onder/mnt/mnt/resource op Linux zoals beschreven in [dit artikel][virtual-machines-linux-agent-user-guide]. Het wissel bestand kan worden geconfigureerd in het configuratie bestand van de Linux-agent/etc/waagent.conf. De volgende instellingen toevoegen of wijzigen:
>
>

```console
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Als u de wijzigingen wilt activeren, moet u de Linux-agent opnieuw starten met

```console
sudo service waagent restart
```

Lees SAP-opmerking [1597355] voor meer informatie over de aanbevolen wissel bestands grootte

---
Het aantal schijven dat wordt gebruikt voor de DBMS-gegevens bestanden en het type Azure Storage deze schijven worden gehost op, moet worden bepaald door de IOPS-vereisten en de vereiste latentie. In [dit artikel (Linux)][virtual-machines-sizes-linux] en [dit artikel (Windows)][virtual-machines-sizes-windows]worden exacte quota's beschreven.

Ervaar de ervaring van SAP-implementaties in de afgelopen twee jaar. Dit zijn enkele lessen die kunnen worden samengevoegd als:

* IOPS-verkeer naar verschillende gegevens bestanden is niet altijd hetzelfde, omdat bestaande klanten systemen mogelijk verschillende gegevens bestanden hebben die hun SAP-data bases vertegenwoordigen. Als gevolg hiervan is het beter om een RAID-configuratie op meerdere schijven te gebruiken om de gegevensbestanden Lun's gehaald te plaatsen. Er zijn situaties, met name bij Azure Standard-opslag waarbij een IOPS-rate het quotum van één schijf in het DBMS-transactie logboek bereikt. In dergelijke scenario's wordt het gebruik van Premium Storage aanbevolen of kunt u ook meerdere standaard opslag schijven samen voegen met een software strip.

---
> ![Windows-logo.][Logo_Windows] Windows
>
> * [Aanbevolen procedures voor prestaties voor SQL Server op virtuele machines van Azure][virtual-machines-sql-server-performance-best-practices]
>
> ![Linux-logo.][Logo_Linux] Linux
>
> * [Software-RAID op Linux configureren][virtual-machines-linux-configure-raid]
> * [LVM configureren op een virtuele Linux-machine in azure][virtual-machines-linux-configure-lvm]
>
>

---
* Premium Storage worden aanzienlijk betere prestaties weer gegeven, met name voor het schrijven van kritieke transactie Logboeken. Voor SAP-scenario's die naar verwachting productie leveren, zoals prestaties, is het raadzaam om VM-Series te gebruiken die gebruikmaken van Azure Premium Storage.

Houd er wel van uit dat de schijf, die het besturings systeem bevat, wordt aanbevolen, de binaire bestanden van SAP en de data base (basis-VM) ook niet langer beperkt tot 127 GB. Het kan nu Maxi maal 1 TB groot zijn. Dit moet voldoende ruimte zijn om alle benodigde bestanden op te slaan, waaronder bijvoorbeeld SAP batch-taak Logboeken.

Raadpleeg de [DBMS-implementatie handleiding][dbms-guide] voor meer suggesties en meer informatie, met name voor DBMS-vm's.

#### <a name="disk-handling"></a>Schijf verwerking

In de meeste scenario's moet u extra schijven maken om de SAP-data base in de virtuele machine te implementeren. We hebben het gehad over de overwegingen voor het aantal schijven in hoofd stuk [VM/schijf structuur voor SAP-implementaties][planning-guide-5.5.1] van dit document. Met de Azure Portal kunt u schijven koppelen en ontkoppelen nadat een basis-VM is geïmplementeerd. De schijven kunnen worden bijgevoegd of ontkoppeld wanneer de virtuele machine actief is, en wanneer deze wordt gestopt. Wanneer u een schijf koppelt, biedt de Azure Portal een lege schijf of een bestaande schijf die op dit moment niet aan een andere virtuele machine is gekoppeld.

**Opmerking**: schijven kunnen op elk gewenst moment slechts aan één virtuele machine worden gekoppeld.

![Schijven koppelen/loskoppelen met Azure Standard-opslag][planning-guide-figure-1400]

Tijdens de implementatie van een nieuwe virtuele machine kunt u bepalen of u Managed Disks wilt gebruiken of uw schijven op Azure Storage accounts wilt plaatsen. Als u Premium Storage wilt gebruiken, raden we u aan Managed Disks te gebruiken.

Vervolgens moet u beslissen of u een nieuwe en lege schijf wilt maken of dat u een bestaande schijf wilt selecteren die eerder is geüpload en nu moet worden gekoppeld aan de VM.

**Belang rijk**: u wilt caching van host **niet** gebruiken met Azure Standard-opslag. U moet de voor keuren van de host-cache op de standaard waarde geen wijzigen. Met Azure Premium Storage moet u lees cache inschakelen als het I/O-kenmerk voornamelijk wordt gelezen zoals normaal I/O-verkeer met database gegevens bestanden. In het geval van een database transactie logboek bestand wordt geen cache aanbevolen.

---
> ![Windows-logo.][Logo_Windows] Windows
>
> [Een gegevens schijf koppelen in de Azure Portal][virtual-machines-linux-attach-disk-portal]
>
> Als er schijven zijn gekoppeld, moet u zich aanmelden bij de virtuele machine om Windows schijf beheer te openen. Als automatisch koppelen niet is ingeschakeld, zoals aanbevolen in het hoofd stuk [instellen automatisch koppelen voor gekoppelde schijven][planning-guide-5.5.3], moet het nieuwe bijgevoegde volume online worden gezet en geïnitialiseerd.
>
> ![Linux-logo.][Logo_Linux] Linux
>
> Als er schijven zijn gekoppeld, moet u zich aanmelden bij de virtuele machine en de schijven initialiseren zoals beschreven in [dit artikel][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]
>
>

---
Als de nieuwe schijf een lege schijf is, moet u ook de schijf Format teren. Voor opmaak, met name voor DBMS-gegevens en-logboek bestanden, gelden dezelfde aanbevelingen als voor bare-metal implementaties van het DBMS.

Een Azure Storage-account biedt geen oneindige resources in termen van I/O-volume, IOPS en gegevens volume. Normaal gesp roken worden DBMS-Vm's het meest beïnvloed. U kunt het beste een afzonderlijk opslag account voor elke virtuele machine gebruiken als u een aantal virtuele machines van I/O-volumes wilt implementeren om de limiet van het volume van het Azure Storage-account te blijven instellen. Anders moet u zien hoe u deze Vm's tussen verschillende opslag accounts kunt verdelen zonder de limiet van elk enkel opslag account te hoeven aanraken. Meer informatie vindt u in de [DBMS-implementatie handleiding][dbms-guide]. Houd er ook rekening mee dat deze beperkingen gelden voor de pure SAP-toepassings server Vm's of andere Vm's, waarvoor uiteindelijk extra Vhd's nodig zijn. Deze beperkingen zijn niet van toepassing als u beheerde schijven gebruikt. Als u Premium Storage wilt gebruiken, raden we u aan Managed disk te gebruiken.

Een ander onderwerp, dat relevant is voor opslag accounts, is of de Vhd's in een opslag account geo-replicatie krijgen. Geo-replicatie is ingeschakeld of uitgeschakeld op het niveau van het opslag account en niet op het VM-niveau. Als geo-replicatie is ingeschakeld, worden de Vhd's binnen het opslag account gerepliceerd naar een ander Azure-Data Center binnen dezelfde regio. Voordat u besluit om dit te doen, moet u rekening houden met de volgende beperking:

Azure geo-replicatie werkt lokaal op elke VHD in een VM en repliceert het I/O's niet in chronologische volg orde over meerdere Vhd's in een VM. Daarom worden de VHD die de basis-VM vertegenwoordigt en eventuele extra virtuele harde schijven die aan de virtuele machine zijn gekoppeld, onafhankelijk van elkaar gerepliceerd. Dit betekent dat er geen synchronisatie is tussen de wijzigingen in de verschillende Vhd's. Het feit dat de I/O's onafhankelijk van de volg orde waarin ze zijn geschreven worden gerepliceerd, betekent dat geo-replicatie niet van waarde is voor database servers die hun data bases over meerdere Vhd's hebben verdeeld. Naast het DBMS kunnen er ook andere toepassingen zijn waar processen schrijven of gegevens manipuleren in verschillende Vhd's en waar het belang rijk is om de volg orde van de wijzigingen te hand haven. Als dat een vereiste is, mag geo-replicatie in azure niet worden ingeschakeld. Afhankelijk van of u geo-replicatie voor een set Vm's wilt, maar niet voor een andere set, kunt u virtuele machines en de bijbehorende Vhd's al categoriseren in verschillende opslag accounts waarvoor geo-replicatie is ingeschakeld of uitgeschakeld.

#### <a name="setting-automount-for-attached-disks"></a><a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>Automatisch koppelen voor gekoppelde schijven instellen
---
> ![Windows-logo.][Logo_Windows] Windows
>
> Voor virtuele machines die worden gemaakt op basis van eigen installatie kopieën of schijven, is het nodig om de automount-para meter in te checken en mogelijk in te stellen. Door deze para meter in te stellen, kan de virtuele machine na een herstart of opnieuw worden geïmplementeerd in azure om de gekoppelde/gekoppelde stations automatisch te koppelen.
> De para meter is ingesteld voor de installatie kopieën van micro soft in de Azure Marketplace.
>
> Als u de automatisch koppelen wilt instellen, raadpleegt u de documentatie van het uitvoer bare bestand van de opdracht regel diskpart.exe hier:
>
> * [Opties voor Disk Part Command-Line](/previous-versions/windows/it-pro/windows-xp/bb490893(v=technet.10))
> * [Automount](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc753703(v=ws.11))
>
> Het Windows-opdracht regel venster moet als beheerder worden geopend.
>
> Als er schijven zijn gekoppeld, moet u zich aanmelden bij de virtuele machine om Windows schijf beheer te openen. Als automatisch koppelen niet is ingeschakeld, zoals aanbevolen in het hoofd stuk [instellen automatisch koppelen voor gekoppelde schijven][planning-guide-5.5.3], moet het nieuw gekoppelde volume >online worden genomen en geïnitialiseerd.
>
> ![Linux-logo.][Logo_Linux] Linux
>
> U moet een nieuw gekoppelde lege schijf initialiseren, zoals wordt beschreven in [dit artikel][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux].
> U moet ook nieuwe schijven toevoegen aan de/etc/fstab.
>
>

---
### <a name="final-deployment"></a>Definitieve implementatie

Raadpleeg de [implementatie handleiding][deployment-guide]voor de laatste implementatie en de exacte stappen, met name ten aanzien van de implementatie van de Azure-extensie voor SAP.

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Toegang tot SAP-systemen die worden uitgevoerd in azure Vm's

Voor scenario's waarbij u via het open bare Internet met SAP GUI verbinding wilt maken met deze SAP-systemen, moeten de volgende procedures worden toegepast.

Verderop in het document bespreken we het andere belang rijke scenario, waarbij verbinding wordt gemaakt met SAP-systemen in cross-premises implementaties, die een site-naar-site-verbinding (VPN-tunnel) of Azure ExpressRoute-verbinding hebben tussen de on-premises systemen en Azure-systemen.

### <a name="remote-access-to-sap-systems"></a>Externe toegang tot SAP-systemen

Met Azure Resource Manager zijn er geen standaard eindpunten meer, zoals in het klassieke model. Alle poorten van een Azure Resource Manager-VM zijn geopend zolang:

1. Er is geen netwerk beveiligings groep gedefinieerd voor het subnet of de netwerk interface. Netwerk verkeer naar virtuele Azure-machines kan worden beveiligd via ' netwerk beveiligings groepen '. Zie voor meer informatie [Wat is een netwerkbeveiligingsgroep (NSG)?][virtual-networks-nsg]
2. Er is geen Azure Load Balancer gedefinieerd voor de netwerk interface

Bekijk het architectuur verschil tussen het klassieke model en de ARM zoals beschreven in [dit artikel][virtual-machines-azure-resource-manager-architecture].

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-over-the-internet"></a>Configuratie van SAP-en SAP-GUI-connectiviteit via Internet

Zie dit artikel, waarin de details van dit onderwerp worden beschreven:

<https://docs.microsoft.com/archive/blogs/saponsqlserver/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure>

#### <a name="changing-firewall-settings-within-vm"></a>Firewall instellingen in VM wijzigen

Het kan nodig zijn om de firewall op uw virtuele machines te configureren om inkomend verkeer naar uw SAP-systeem toe te staan.

---
> ![Windows-logo.][Logo_Windows] Windows
>
> De Windows Firewall binnen een Azure geïmplementeerde VM is standaard ingeschakeld. U moet nu toestaan dat de SAP-poort wordt geopend, anders kan de SAP-gebruikers interface geen verbinding maken.
> Om dit te doen:
>
> * Open Control Panel\System en Beveiliging\windows Firewall voor **Geavanceerde instellingen**.
> * Klik nu met de rechter muisknop op regels voor binnenkomende verbindingen en kies **nieuwe regel**.
> * In de volgende wizard hebt gekozen voor het maken van een nieuwe **poort** regel.
> * In de volgende stap van de wizard, sluit u de instelling op TCP en typt u het poort nummer dat u wilt openen. Omdat onze SAP-exemplaar-ID 00 is, hebben we 3200 geduurd. Als uw exemplaar een ander exemplaar nummer heeft, moet de poort die u eerder hebt gedefinieerd op basis van het exemplaar nummer worden geopend.
> * In het volgende deel van de wizard moet u het item de optie **verbinding toestaan** controleren laten staan.
> * In de volgende stap van de wizard moet u opgeven of de regel van toepassing is op domein, privé en openbaar netwerk. Pas het zo nodig aan uw behoeften aan. Als u echter via het open bare netwerk verbinding maakt met SAP-gebruikers interface, moet u de regel Toep assen op het open bare netwerk.
> * Geef in de laatste stap van de wizard de regel een naam en sla op door op **volt ooien** te klikken.
>
> De regel wordt direct van kracht.
>
> ![Poort regel definitie][planning-guide-figure-1600]
>
> ![Linux-logo.][Logo_Linux] Linux
>
> De Linux-installatie kopieën in de Azure Marketplace scha kelen de iptables-firewall niet standaard in en de verbinding met uw SAP-systeem zou moeten werken. Als u iptables of een andere firewall hebt ingeschakeld, raadpleegt u de documentatie van iptables of de gebruikte firewall om binnenkomend TCP-verkeer naar poort 32xx toe te staan (waarbij xx het systeem nummer is van uw SAP-systeem).
>
>

---
#### <a name="security-recommendations"></a>Aanbevelingen voor beveiliging

De SAP-gebruikers interface maakt niet onmiddellijk verbinding met een van de SAP-instanties (poort 32xx) die worden uitgevoerd, maar maakt eerst verbinding via de poort die wordt geopend in het SAP Message Server-proces (poort 36xx). In het verleden werd dezelfde poort door de berichten server gebruikt voor de interne communicatie met de toepassings exemplaren. Om te voor komen dat lokale toepassings servers per ongeluk communiceren met een bericht server in azure, kunnen de interne communicatie poorten worden gewijzigd. Het wordt ten zeerste aanbevolen de interne communicatie tussen de SAP-berichten server en de bijbehorende toepassings exemplaren te wijzigen in een ander poort nummer op systemen die zijn gekloond van on-premises systemen, zoals een kloon van de ontwikkeling van project tests, enzovoort. U kunt dit doen met de para meter standaard profiel:

> rdisp/msserv_internal
>
>

zoals beschreven in [beveiligings instellingen voor de SAP-berichten server](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm)


### <a name="single-vm-with-sap-netweaver-demotraining-scenario"></a><a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>Eén virtuele machine met SAP NetWeaver demo/training-scenario

![Uitvoeren van één VM SAP-demo systemen met dezelfde VM-namen, geïsoleerd in azure Cloud Services][planning-guide-figure-1700]

In dit scenario implementeren we een typisch trainings-en demo systeem scenario waarbij het volledige trainings-en demo scenario zich in één virtuele machine bevindt. We gaan ervan uit dat de implementatie wordt uitgevoerd via VM-installatie kopie sjablonen. Er wordt ook van uitgegaan dat meerdere van deze demo's/trainingen-Vm's moeten worden geïmplementeerd met de virtuele machines die dezelfde naam hebben. De hele trainings systemen hebben geen verbinding met uw on-premises assets en vormen een tegenovergestelde implementatie.

De veronderstelling is dat u een VM-installatie kopie hebt gemaakt, zoals beschreven in sommige secties van hoofd stuk [Vm's voorbereiden met SAP voor Azure][planning-guide-5.2] in dit document.

De volg orde van de gebeurtenissen voor het implementeren van het scenario ziet er als volgt uit:

##### <a name="powershell"></a>PowerShell

* Een nieuwe resource groep maken voor elke training/demo-landschap

```powershell
$rgName = "SAPERPDemo1"
New-AzResourceGroup -Name $rgName -Location "North Europe"
```

* Een nieuw opslag account maken als u Managed Disks niet wilt gebruiken

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Maak een nieuw virtueel netwerk voor elke training/demo-landschap om het gebruik van dezelfde hostnaam en IP-adressen in te scha kelen. Het virtuele netwerk wordt beveiligd door een netwerk beveiligings groep die alleen verkeer naar poort 3389 toestaat om Extern bureaublad toegang en poort 22 voor SSH in te scha kelen.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Een nieuw openbaar IP-adres maken dat kan worden gebruikt voor toegang tot de virtuele machine via Internet

```powershell
# Create a public IP address with a DNS name
$pip = New-AzPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Een nieuwe netwerk interface maken voor de virtuele machine

```powershell
# Create a new Network Interface
$nic = New-AzNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Hiermee maakt u een virtuele machine. Voor dit scenario krijgt elke VM dezelfde naam. De SAP-SID van de SAP NetWeaver-exemplaren in deze virtuele machines is ook hetzelfde. In de Azure-resource groep moet de naam van de virtuele machine uniek zijn, maar in verschillende Azure-resource groepen kunt u virtuele machines met dezelfde naam uitvoeren. Het standaard Administrator-account van Windows of root voor Linux zijn ongeldig. Daarom moet een nieuwe gebruikers naam voor de beheerder worden gedefinieerd in combi natie met een wacht woord. Ook moet de grootte van de virtuele machine worden gedefinieerd.

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES-SAP" -Skus "12-SP1" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "Oracle" -Offer "Oracle-Linux" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a Managed Disk Image
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMSourceImage -VM $vmconfig -Id <Id of Managed Disk Image>
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* Voeg eventueel extra schijven toe en herstel de benodigde inhoud. Alle BLOB-namen (Url's naar de blobs) moeten uniek zijn in Azure.

```powershell
# Optional: Attach additional VHD data disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzVM

# Optional: Attach additional Managed Disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
Add-AzVMDataDisk -VM $vm -Name datadisk -DiskSizeInGB 1023 -CreateOption empty -Lun 0 | Update-AzVM
```

##### <a name="cli"></a>CLI

De volgende voorbeeld code kan worden gebruikt in Linux. Gebruik voor Windows Power shell zoals hierboven wordt beschreven of pas het voor beeld aan om% rgName% in plaats van $rgName te gebruiken en stel de omgevings variabele in met behulp van de Windows-opdracht *reeks*.

* Een nieuwe resource groep maken voor elke training/demo-landschap

```azurecli
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* Een nieuw opslagaccount maken

```azurecli
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* Maak een nieuw virtueel netwerk voor elke training/demo-landschap om het gebruik van dezelfde hostnaam en IP-adressen in te scha kelen. Het virtuele netwerk wordt beveiligd door een netwerk beveiligings groep die alleen verkeer naar poort 3389 toestaat om Extern bureaublad toegang en poort 22 voor SSH in te scha kelen.

```azurecli
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* Een nieuw openbaar IP-adres maken dat kan worden gebruikt voor toegang tot de virtuele machine via Internet

```azurecli
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* Een nieuwe netwerk interface maken voor de virtuele machine

```azurecli
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* Hiermee maakt u een virtuele machine. Voor dit scenario krijgt elke VM dezelfde naam. De SAP-SID van de SAP NetWeaver-exemplaren in deze virtuele machines is ook hetzelfde. In de Azure-resource groep moet de naam van de virtuele machine uniek zijn, maar in verschillende Azure-resource groepen kunt u virtuele machines met dezelfde naam uitvoeren. Het standaard Administrator-account van Windows of root voor Linux zijn ongeldig. Daarom moet een nieuwe gebruikers naam voor de beheerder worden gedefinieerd in combi natie met een wacht woord. Ook moet de grootte van de virtuele machine worden gedefinieerd.

```azurecli
#####
# Create virtual machines using storage accounts
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password

#####
# Create virtual machines using Managed Disks
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
```

```azurecli
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Windows --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Linux --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd> --authentication-type password

#####
# Create a new virtual machine with a Managed Disk Image
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id> --authentication-type password
```

* Voeg eventueel extra schijven toe en herstel de benodigde inhoud. Alle BLOB-namen (Url's naar de blobs) moeten uniek zijn in Azure.

```azurecli
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Sjabloon

U kunt de voorbeeld sjablonen gebruiken in de opslag plaats Azure-Quick Start-sjablonen op GitHub.

* [Eenvoudige virtuele Linux-machine](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Eenvoudige Windows-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [VM van installatie kopie](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-that-communicate-within-azure"></a>Een set virtuele machines implementeren die communiceren binnen Azure

Dit niet-hybride scenario is een typisch scenario voor training en demonstratie doeleinden, waarbij de software die het demo-en trainings scenario vertegenwoordigt over meerdere Vm's wordt verspreid. De verschillende onderdelen die zijn geïnstalleerd op de verschillende Vm's, moeten met elkaar communiceren. In dit scenario is er geen on-premises netwerk communicatie of cross-premises scenario nodig.

Dit scenario is een uitbrei ding van de installatie die wordt beschreven in hoofd stuk [Single VM met SAP NetWeaver-scenario voor demonstratie/training][planning-guide-7.1] van dit document. In dit geval worden er meer virtuele machines toegevoegd aan een bestaande resource groep. In het volgende voor beeld bestaat het landschap van de training uit een SAP-ASCS/SCS-VM, een virtuele machine met een DBMS en een SAP-exemplaar-VM van het toepassings server.

Voordat u dit scenario bouwt, moet u nadenken over de basis instellingen die al eerder in het scenario zijn uitgeoefend.

#### <a name="resource-group-and-virtual-machine-naming"></a>Naamgeving van resource groepen en virtuele machines

Alle namen van resource groepen moeten uniek zijn. Ontwikkel uw eigen naamgevings schema voor uw resources, zoals `<rg-name`>-achtervoegsel.

De naam van de virtuele machine moet uniek zijn binnen de resource groep.

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>Netwerk instellen voor communicatie tussen de verschillende Vm's

![Een set virtuele machines in een Azure-Virtual Network][planning-guide-figure-1900]

Als u conflicten met klonen van hetzelfde onderwijs/demo land wilt voor komen, moet u een Azure-Virtual Network voor elk landschap maken. DNS-naam omzetting wordt verzorgd door Azure of u kunt uw eigen DNS-server configureren buiten Azure (deze worden hier niet verder besproken). In dit scenario configureren we onze eigen DNS niet. Voor alle virtuele machines binnen een Azure-Virtual Network wordt communicatie via hostnamen ingeschakeld.

De redenen voor het scheiden van training of demo landschappen per virtuele netwerken en niet alleen voor resource groepen kunnen zijn:

* Het SAP-landschap dat is ingesteld, heeft een eigen AD-OpenLDAP en een domein server moet deel uitmaken van elk van de landschappen.
* Het SAP-landschap is ingesteld op onderdelen die moeten werken met vaste IP-adressen.

Meer informatie over virtuele Azure-netwerken en hoe u deze kunt definiëren, vindt u in [dit artikel][virtual-networks-create-vnet-arm-pportal].

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>Implementeren van SAP-Vm's met een bedrijfs netwerk verbinding (cross-premises)

U voert een SAP-landschap uit en wilt de implementatie verdelen tussen bare-metal servers, on-premises gevirtualiseerde omgevingen voor toepassings lagen en kleinere, op twee lagen geconfigureerde SAP-systemen en Azure IaaS. De basis veronderstelling is dat SAP-systemen binnen één SAP-landschap moeten communiceren met elkaar en met veel andere software onderdelen die in het bedrijf zijn geïmplementeerd, onafhankelijk van hun implementatie formulier. Er moeten ook geen verschillen worden geïntroduceerd in het implementatie formulier voor de eind gebruiker die verbinding maakt met de SAP-GUI of andere interfaces. Aan deze voor waarden kan alleen worden voldaan als de on-premises Active Directory/OpenLDAP-en DNS-services zijn uitgebreid naar de Azure-systemen via site-naar-site/multi-site connectiviteit of particuliere verbindingen zoals Azure ExpressRoute.



### <a name="scenario-of-an-sap-landscape"></a>Scenario van een SAP-landschap

Het cross-premises of hybride scenario kan worden beschreven, zoals in de onderstaande afbeeldingen:

![Site-naar-site-connectiviteit tussen on-premises en Azure-assets][planning-guide-figure-2100]

De minimale vereiste is het gebruik van beveiligde communicatie protocollen, zoals SSL/TLS, voor browser toegang of VPN-verbindingen voor systeem toegang tot de Azure-Services. De veronderstelling is dat bedrijven de VPN-verbinding tussen hun bedrijfs netwerk en Azure anders afhandelen. Sommige bedrijven kunnen alle poorten leeg openen. Sommige andere bedrijven willen wellicht nauw keurig zijn in welke poorten ze moeten worden geopend, enzovoort.

In de tabel hieronder worden typische SAP-communicatie poorten weer gegeven. Het is eigenlijk voldoende om de SAP-gateway poort te openen.

<!-- sapms is prefix of a SAP service name and not a spelling error -->

| Service | Poortnaam | Voor beeld `<nn`> = 01 | Standaard bereik (min-max) | Opmerking |
| --- | --- | --- | --- | --- |
| Dispatcher |sapdp `<nn>` weer geven * |3201 |3200-3299 |SAP-verzender die wordt gebruikt door de SAP-GUI voor Windows en Java |
| Bericht server |sapms `<sid`> weer geven * * |3600 |gratis sapms`<anySID`> |sid = SAP-systeem-ID |
| Gateway |sapgw `<nn`> weer geven * |3301 |macro's |SAP-gateway, gebruikt voor CPIC en RFC-communicatie |
| SAP-router |sapdp99 |3299 |macro's |Alleen CI-service namen (Central instance) kunnen opnieuw worden toegewezen in/etc/services naar een wille keurige waarde na de installatie. |

*) nn = SAP-instantie nummer

* *) sid = SAP-systeem-ID

Meer gedetailleerde informatie over de poorten die zijn vereist voor verschillende SAP-producten of-services van SAP-producten vindt u hier <https://scn.sap.com/docs/DOC-17124> .
Met dit document moet u toegewezen poorten kunnen openen in het VPN-apparaat dat nodig is voor specifieke SAP-producten en-scenario's.

Andere beveiligings maatregelen bij het implementeren van Vm's in een dergelijk scenario kunnen zijn het maken van een [netwerk beveiligings groep][virtual-networks-nsg] voor het definiëren van toegangs regels.



#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Afdrukken op een lokale netwerk printer vanuit een SAP-exemplaar in azure

##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Afdrukken via TCP/IP in cross-premises scenario

Het instellen van uw on-premises netwerk printers op basis van TCP/IP in een Azure-VM is in het algemeen hetzelfde als in uw bedrijfs netwerk, ervan uitgaande dat er een VPN-site-naar-site-tunnel of ExpressRoute-verbinding tot stand is gebracht.

---
> ![Windows-logo.][Logo_Windows] Windows
>
> Om dit te doen:
>
> * Sommige netwerk printers worden geleverd met een configuratie wizard waarmee u eenvoudig uw printer in een Azure-VM kunt instellen. Als er geen wizard software is gedistribueerd met de printer, is de hand matige manier om de printer in te stellen, het maken van een nieuwe TCP/IP-printer poort.
> * Open configuratie scherm-> apparaten en printers-> een printer toevoegen
> * Kies een printer toevoegen met een TCP/IP-adres of hostnaam
> * Typ het IP-adres van de printer
> * Printer poort standaard 9100
> * Installeer zo nodig het juiste printer stuur programma hand matig.
>
> ![Linux-logo.][Logo_Linux] Linux
>
> * net als bij Windows volgt u gewoon de standaard procedure voor het installeren van een netwerk printer
> * Volg gewoon de open bare Linux-gidsen voor [SuSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) of [Red Hat en Oracle Linux](https://access.redhat.com/documentation/red_hat_enterprise_linux/6/html/deployment_guide/sec-printer_configuration) over het toevoegen van een printer.
>
>

---
![Afdrukken via het netwerk][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Host-gebaseerde printer via SMB (gedeelde printer) in cross-premises scenario

Printers op basis van een host zijn niet compatibel met het netwerk. Maar een op een host gebaseerde printer kan worden gedeeld tussen computers in een netwerk, mits de printer is aangesloten op een ingeschakelde computer. Verbind uw bedrijfs netwerk met site-naar-site-of ExpressRoute en deel uw lokale printer. Het SMB-protocol gebruikt NetBIOS in plaats van DNS als naam service. De NetBIOS-hostnaam kan afwijken van de naam van de DNS-host. Het standaard geval is dat de NetBIOS-hostnaam en de naam van de DNS-host identiek zijn. Het DNS-domein is niet zinvol in de NetBIOS-naam ruimte. De volledig gekwalificeerde DNS-hostnaam die bestaat uit de naam van de DNS-hostnaam en het DNS-domein, mag daarom niet worden gebruikt in de NetBIOS-naam ruimte.

De printer share wordt aangeduid met een unieke naam in het netwerk:

* De hostnaam van de SMB-host (altijd nodig).
* De naam van de share (altijd nodig).
* Naam van het domein als de printer share zich niet in hetzelfde domein als SAP-systeem bevindt.
* Daarnaast is er mogelijk een gebruikers naam en wacht woord vereist voor toegang tot de printer share.

Procedure:

---
> ![Windows-logo.][Logo_Windows] Windows
>
> Uw lokale printer delen.
> Open in de Azure-VM de Windows Verkenner en typ de share naam van de printer.
> Een wizard Printer installatie leidt u door het installatie proces.
>
> ![Linux-logo.][Logo_Linux] Linux
>
> Hier volgen enkele voor beelden van documentatie over het configureren van netwerk printers in Linux of het opnemen van een hoofd stuk over afdrukken in Linux. Het werkt op dezelfde manier als in een Azure Linux-VM zolang de VM deel uitmaakt van een VPN:
>
> * SLES <https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL of Oracle Linux <https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html-single/system_administrators_guide/index#sec-Starting_Print_Settings_Config>
>
>

---
##### <a name="usb-printer-printer-forwarding"></a>USB-printer (printer door sturen)

In Azure is de mogelijkheid van de Extern bureaublad-services om gebruikers de toegang tot hun lokale printer apparaten te bieden in een externe sessie niet beschikbaar.

---
> ![Windows-logo.][Logo_Windows] Windows
>
> Meer informatie over afdrukken met Windows vindt u hier: <https://technet.microsoft.com/library/jj590748.aspx> .
>
>

---
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Integratie van SAP Azure-systemen in een cross-premises-en transport systeem (TMS)

Het SAP-wijzigings-en transport systeem (TMS) moet worden geconfigureerd voor het exporteren en importeren van transport aanvragen over systemen in het landschap. We gaan ervan uit dat de ontwikkelings instanties van een SAP-systeem (DEV) zich in azure bevinden, terwijl de kwaliteits garantie (QA) en de productieve systemen (PRD) on-premises zijn. Daarnaast wordt ervan uitgegaan dat er een centrale transport Directory is.

##### <a name="configuring-the-transport-domain"></a>Het transport domein configureren

Configureer uw transport domein op het systeem dat u als de transport domein controller hebt opgegeven, zoals beschreven in [de transport domein controller configureren](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm). Er wordt een TMSADM van het systeem gebruiker gemaakt en de vereiste RFC-bestemming wordt gegenereerd. U kunt deze RFC'S-verbindingen controleren met behulp van de trans actie-SM59. Hostname-omzetting moet zijn ingeschakeld in uw transport domein.

Procedure:

* In ons scenario hebben we besloten dat het on-premises QAS systeem de CTS-domein controller is. STMS van oproepende trans actie. Het dialoog venster TMS wordt weer gegeven. Het dialoog venster transport domein configureren wordt weer gegeven. (Dit dialoog venster wordt alleen weer gegeven als u nog geen transport domein hebt geconfigureerd.)
* Zorg ervoor dat de automatisch gemaakte gebruiker TMSADM geautoriseerd is (SM59-> ABAP-verbinding-> TMSADM@E61.DOMAIN_E61 -> Details-> Utilities (M)-> autorisatie test). In het eerste scherm van de trans actie STMS moet worden aangegeven dat dit SAP-systeem nu werkt als de controller van het transport domein, zoals hier wordt weer gegeven:

![Eerste scherm van trans actie-STMS op de domein controller][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>SAP-systemen opnemen in het transport domein

De volg orde van het opnemen van een SAP-systeem in een transport domein ziet er als volgt uit:

* Ga in het ontwikkel systeem in azure naar het Trans Port System (client 000) en roep Trans Action STMS. Kies andere configuratie in het dialoog venster en ga door met systeem in domein toevoegen. Geef de domein controller op als doelhost ([inclusief SAP-systemen in het transport domein](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). Het systeem wacht nu om te worden opgenomen in het transport domein.
* Uit veiligheids overwegingen moet u vervolgens terugkeren naar de domein controller om uw aanvraag te bevestigen. Kies systeem overzicht en goed keuring van het wacht systeem. Bevestig vervolgens de prompt en de configuratie wordt gedistribueerd.

Dit SAP-systeem bevat nu de benodigde informatie over alle andere SAP-systemen in het transport domein. Terzelfder tijd worden de adres gegevens van het nieuwe SAP-systeem verzonden naar alle andere SAP-systemen en het SAP-systeem wordt ingevoerd in het transport Profiel van het transport beheersysteem. Controleer of de Rfc's en toegang tot de transport Directory van het domein werken.

Ga door met de configuratie van uw transport systeem zoals beschreven in de documentatie [wijzigen en het transport systeem](https://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm).

Procedure:

* Zorg ervoor dat uw STMS on-premises correct is geconfigureerd.
* Zorg ervoor dat de hostnaam van de transport domein controller kan worden omgezet door uw virtuele machine in Azure en vice Visa.
* STMS aanroepen-> andere configuratie-> systeem in domein opnemen.
* Bevestig de verbinding in het on-premises TMS-systeem.
* Configureer op de gebruikelijke wijze transport routes,-groepen en-lagen.

In site-naar-site verbonden cross-premises-scenario's kan de latentie tussen on-premises en Azure nog steeds aanzienlijk zijn. Als we de volg orde van het Trans Port van objecten volgen via ontwikkelings-en test systemen naar productie of als u nadenkt over het Toep assen van trans porten of het ondersteunen van pakketten naar de verschillende systemen, realiseert u zich dat, afhankelijk van de locatie van de centrale transport Directory, sommige systemen een hoge latentie hebben bij het lezen of schrijven van gegevens in de centrale transport Directory De situatie is vergelijkbaar met SAP-landschap-configuraties waarbij de verschillende systemen worden verspreid via verschillende data centers met een grote afstand tussen de data centers.

Om een dergelijke latentie te omzeilen en de systemen snel te lezen of te schrijven naar of van de transport Directory, kunt u twee STMS-transport domeinen instellen (één voor on-premises en één met de systemen in Azure en de transport domeinen koppelen. Raadpleeg deze documentatie voor meer informatie over de beginselen achter dit concept in het SAP-TMS: <https://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm> .

Procedure:

* Een transport domein instellen op elke locatie (on-premises en Azure) met behulp van trans actie STMS <https://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Koppel de domeinen aan een domein koppeling en bevestig de koppeling tussen de twee domeinen.
  <https://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* Distribueer de configuratie naar het gekoppelde systeem.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>RFC-verkeer tussen SAP-instanties in Azure en on-premises (cross-premises)

RFC-verkeer tussen systemen, die on-premises zijn en in azure, moeten werken. Voor het instellen van een SM59 voor verbindings oproep in een bron systeem waar u een RFC-verbinding moet definiëren op het doel systeem. De configuratie is vergelijkbaar met de standaard instelling van een RFC-verbinding.

In het cross-premises scenario wordt ervan uitgegaan dat de virtuele machines, die SAP-systemen uitvoeren die met elkaar moeten communiceren, zich in hetzelfde domein bevinden. Daarom is het instellen van een RFC-verbinding tussen SAP-systemen niet verschillen van de installatie stappen en-invoer in on-premises scenario's.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Toegang tot lokale bestands shares vanuit SAP-exemplaren die zich in azure bevinden of andersom

SAP-exemplaren die zich in azure bevinden, hebben toegang tot bestands shares die zich binnen de bedrijfs locatie bevinden. Daarnaast moeten on-premises SAP-exemplaren toegang hebben tot bestands shares die zich in azure bevinden. Als u de bestands shares wilt inschakelen, moet u de opties voor machtigingen en delen configureren op het lokale systeem. Zorg ervoor dat u de poorten opent op de VPN-of ExpressRoute-verbinding tussen Azure en uw Data Center.

## <a name="supportability"></a>Ondersteuning

### <a name="azure-extension-for-sap"></a><a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Azure-extensie voor SAP

Als u een deel van de gegevens van de Azure-infra structuur van essentiële SAP-systemen wilt belichten bij de SAP host agent-exemplaren die in Vm's zijn geïnstalleerd, moet een Azure (VM)-extensie voor SAP worden geïnstalleerd voor de geïmplementeerde Vm's. Omdat de vereisten van SAP specifiek zijn voor SAP-toepassingen, heeft micro soft besloten de vereiste functionaliteit niet algemeen in azure te implementeren, maar laat het klanten de benodigde VM-extensie en configuraties implementeren op hun Virtual Machines die in Azure worden uitgevoerd. Implementatie-en levenscyclus beheer van de Azure VM-extensie voor SAP wordt voornamelijk geautomatiseerd door Azure.

#### <a name="solution-design"></a>Ontwerp van de oplossing

De oplossing die is ontwikkeld om SAP host agent in te scha kelen, is gebaseerd op de architectuur van de Azure VM-agent en extensie Framework. Het idee van de Azure VM-agent en extensie Framework is het toestaan van de installatie van de software toepassing (en) die beschikbaar zijn in de Azure VM-extensie galerie binnen een VM. Het principe dat zich achter dit concept bevindt, is het toestaan van (in gevallen zoals de Azure-extensie voor SAP), de implementatie van speciale functionaliteit in een virtuele machine en de configuratie van dergelijke software tijdens de implementatie.

De ' Azure VM-agent ' die het verwerken van specifieke Azure VM-extensies in de virtuele machine mogelijk maakt, wordt standaard in virtuele Windows-machines ingevoegd in de Azure Portal. In het geval van SUSE, Red Hat of Oracle Linux maakt de VM-agent al deel uit van de Azure Marketplace-installatie kopie. Als er een virtuele Linux-machine van on-premises wordt geüpload naar Azure, moet de VM-agent hand matig worden geïnstalleerd.

De basis bouwstenen van de oplossing voor het leveren van Azure-infrastructuur gegevens aan de SAP host agent in azure ziet er als volgt uit:

![Microsoft Azure extensie onderdelen][planning-guide-figure-2400]

Zoals wordt weer gegeven in het bovenstaande blok diagram, wordt één deel van de oplossing gehost in de Azure VM-installatie kopie en de Azure-extensie galerie. Dit is een globaal gerepliceerde opslag plaats die wordt beheerd door Azure-bewerkingen. Het is de verantwoordelijkheid van het gezamenlijke SAP/MS-team dat samenwerkt met de Azure-implementatie van SAP om te werken met Azure-bewerkingen om nieuwe versies van de Azure-extensie voor SAP te publiceren.

Wanneer u een nieuwe virtuele Windows-machine implementeert, wordt de Azure VM-agent automatisch toegevoegd aan de VM. De functie van deze agent is het coördineren van het laden en configureren van de Azure-extensies van de Vm's. Voor virtuele Linux-machines is de Azure VM-agent al onderdeel van de Azure Marketplace-installatie kopie van het besturings systeem.

Er is echter een stap die nog steeds door de klant moet worden uitgevoerd. Dit is de activering en configuratie van de prestatie verzameling. Het proces dat is gerelateerd aan de configuratie wordt geautomatiseerd door een Power shell-script of CLI-opdracht. Het Power shell-script kan worden gedownload in het Microsoft Azure Script Center zoals beschreven in de [implementatie handleiding][deployment-guide].

De algemene architectuur van de Azure-extensie voor SAP ziet er als volgt uit:

![Azure-extensie voor SAP ][planning-guide-figure-2500]

**Volg de instructies in de [implementatie handleiding][deployment-guide]voor de exacte procedures en voor gedetailleerde stappen voor het gebruik van deze Power shell-cmdlets of cli-opdracht tijdens implementaties.**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Integratie van het met Azure gevonden SAP-exemplaar in SAProuter

SAP-exemplaren die worden uitgevoerd in azure, moeten ook toegankelijk zijn vanuit SAProuter.

![SAP-Router netwerk verbinding][planning-guide-figure-2600]

Een SAProuter maakt de TCP/IP-communicatie tussen deelnemende systemen mogelijk als er geen directe IP-verbinding is. Dit biedt het voor deel dat er geen end-to-end-verbinding tussen de communicatie partners nodig is op het netwerk niveau. De SAProuter luistert standaard op poort 3299.
Als u SAP-exemplaren wilt verbinden via een SAProuter, moet u de teken reeks en hostnaam van de SAProuter voorzien van elke poging om verbinding te maken.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver als Java

De focus van het document is tot nu toe SAP NetWeaver in het algemeen of de SAP NetWeaver ABAP-stack. In deze kleine sectie worden specifieke aandachtspunten voor de SAP Java-stack weer gegeven. Een van de belangrijkste SAP NetWeaver Java-toepassingen die exclusief zijn gebaseerd is de SAP-Enterprise Portal. Andere op SAP NetWeaver gebaseerde toepassingen zoals SAP PI en SAP Solution Manager gebruiken zowel de SAP net-ABAP als Java-stacks. Daarom moet u ook rekening houden met specifieke aspecten met betrekking tot de SAP NetWeaver Java-stack.

### <a name="sap-enterprise-portal"></a>SAP-Enterprise Portal

Het instellen van een SAP-Portal in een virtuele Azure-machine verschilt niet van een on-premises installatie als u in verschillende scenario's implementeert. Omdat de DNS door on-premises wordt uitgevoerd, kunnen de poort instellingen van de afzonderlijke instanties worden uitgevoerd, zoals on-premises geconfigureerd. De aanbevelingen en beperkingen die in dit document worden beschreven, zijn tot nu toe van toepassing op toepassingen zoals SAP Enterprise Portal of de SAP NetWeaver Java-stack in het algemeen.

![Blootgestelde SAP-Portal][planning-guide-figure-2700]

Een speciaal implementatie scenario voor sommige klanten is de rechtstreekse bloot stelling van de SAP-Enterprise Portal op internet terwijl de virtuele-machinehost is verbonden met het bedrijfs netwerk via site-naar-site VPN-tunnel of ExpressRoute. U moet ervoor zorgen dat specifieke poorten zijn geopend en niet worden geblokkeerd door een firewall of netwerk beveiligings groep.

De initiële Portal-URI is http (s): `<Portalserver`>:5XX00/irj, waarbij de poort wordt gevormd door SAP in <https://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm> .

![Eindpunt configuratie][planning-guide-figure-2800]

Als u de URL en/of poorten van uw SAP Enterprise Portal wilt aanpassen, raadpleegt u de volgende documentatie:

* [Portal-URL wijzigen](https://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Standaard poort nummers wijzigen, poort nummers van de portal](https://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Hoge Beschik baarheid (HA) en herstel na nood gevallen (DR) voor SAP net-Weaver die wordt uitgevoerd op Azure Virtual Machines

### <a name="definition-of-terminologies"></a>Definitie van terminologie

De term **hoge Beschik baarheid (ha)** is in het algemeen gerelateerd aan een set technologieën die IT-onderbrekingen minimaliseert door bedrijfs CONTINUÏTEIT van IT-Services te bieden via redundante, fout tolerante of met failover beveiligde onderdelen binnen **hetzelfde** Data Center. In ons geval binnen één Azure-regio.

**Herstel na nood gevallen (Dr)** is ook gericht op het minimaliseren van de onderbreking van de IT-Services en de herstel bewerking, maar in **verschillende** data centers, die meestal honderden kilo meters bevinden. In ons geval doorgaans tussen verschillende Azure-regio's binnen dezelfde geopolitieke regio of als een klant.

### <a name="overview-of-high-availability"></a>Overzicht van hoge Beschik baarheid

We kunnen de discussie over SAP hoge Beschik baarheid in Azure in twee delen onderscheiden:

* **Azure-infra structuur hoge Beschik baarheid**, bijvoorbeeld ha of COMPUTE (vm's), netwerk, opslag, enzovoort en de voor delen voor het uitbreiden van de beschik BAARHEID van SAP-toepassingen.
* **Hoge Beschik baarheid van SAP-toepassing**, bijvoorbeeld ha van SAP-software onderdelen:
  * SAP-toepassings servers
  * SAP ASCS/SCS-instantie
  * DB-server

en hoe het kan worden gecombineerd met Azure-infrastructuur HA.

SAP hoge Beschik baarheid in azure heeft een aantal verschillen ten opzichte van SAP-hoge Beschik baarheid in een on-premises fysieke of virtuele omgeving. In het volgende document van SAP worden standaard configuraties met hoge Beschik baarheid van SAP beschreven in gevirtualiseerde omgevingen in Windows: <https://scn.sap.com/docs/DOC-44415> . Er is geen sapinst geïntegreerde SAP-HA-configuratie voor Linux zoals deze voor Windows bestaat. Meer informatie vindt u hier op met betrekking tot SAP HA on-premises voor Linux: <https://scn.sap.com/docs/DOC-8541> .

### <a name="azure-infrastructure-high-availability"></a>Hoge Beschik baarheid van Azure-infra structuur

Er is momenteel een SLA van 99,9% voor één VM. Als u wilt weten hoe de beschik baarheid van één virtuele machine eruit kan zien, kunt u het product bouwen van de verschillende beschik bare Azure-Sla's: <https://azure.microsoft.com/support/legal/sla/> .

De basis voor de berekening is 30 dagen per maand of 43200 minuten. Daarom komt 0,05% downtime overeen met 21,6 minuten. Zoals gebruikelijk, wordt de beschik baarheid van de verschillende services op de volgende manier vermenigvuldigd:

(Beschikbaarheids service #1/100) * (beschikbaarheids Service #2/100) * (beschikbaarheids service #3/100)

Zo

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 of een algehele Beschik baarheid van 99,75%.

#### <a name="virtual-machine-vm-high-availability"></a>Virtuele machine (VM) hoge Beschik baarheid

Er zijn twee typen Azure-platform gebeurtenissen die van invloed kunnen zijn op de beschik baarheid van uw virtuele machines: gepland onderhoud en niet-gepland onderhoud.

* Geplande onderhouds gebeurtenissen zijn periodieke updates die door micro soft zijn aangebracht in het onderliggende Azure-platform om de algehele betrouw baarheid, prestaties en beveiliging van de platform infrastructuur waarop uw virtuele machines worden uitgevoerd, te verbeteren.
* Niet-gepland onderhoud vindt plaats wanneer de hardware of de fysieke infrastructuur die uw virtuele machine ondersteunt defect raakt. Voorbeelden hiervan zijn lokale netwerkproblemen, lokale schijfdefecten of andere defecten op rack-niveau. Wanneer een dergelijke fout wordt gedetecteerd, migreert het Azure-platform uw virtuele machine automatisch van de beschadigde fysieke server die als host fungeert voor uw virtuele machine naar een in orde zijnde fysieke server. Dergelijke gebeurtenissen zijn zeldzaam, maar kunnen er ook toe leiden dat uw virtuele machine opnieuw moet opstarten.

Zie [Beschik baarheid van virtuele Windows-machines in azure](../../manage-availability.md) en [Beschik baarheid van virtuele Linux-machines in azure](../../manage-availability.md)voor meer informatie.

#### <a name="azure-storage-redundancy"></a>Azure Storage redundantie

De gegevens in uw Microsoft Azure Storage-account worden altijd gerepliceerd om duurzaamheid en hoge Beschik baarheid te garanderen, maar u kunt ook aan de Azure Storage SLA voldoen, zelfs in het geval van tijdelijke hardwarefouten.

Omdat Azure Storage standaard drie installatie kopieën van de gegevens houdt, zijn er geen RAID5-of RAID1 meer nodig voor meerdere Azure-schijven.

Zie [Azure Storage redundantie](../../../storage/common/storage-redundancy.md)voor meer informatie.

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>VM van Azure-infra structuur opnieuw opstarten voor een hogere Beschik baarheid van SAP-toepassingen

Als u besluit geen functies zoals Windows Server Failover Clustering (WSFC) of pacemaker in Linux (momenteel alleen ondersteund voor SLES 12 en hoger) te gebruiken, wordt het opnieuw opstarten van Azure VM gebruikt voor het beveiligen van een SAP-systeem tegen geplande en ongeplande downtime van de fysieke Azure-server infrastructuur en het algehele onderliggende Azure-platform.

> [!NOTE]
> Het is belang rijk om te vermelden dat Azure VM restart voornamelijk Vm's en niet-toepassingen beveiligt. Het opnieuw opstarten van de VM biedt geen hoge Beschik baarheid voor SAP-toepassingen, maar biedt een bepaald niveau van Beschik baarheid van de infra structuur en daarom een indirect hogere Beschik baarheid van SAP-systemen. Er is ook geen SLA voor de tijd die nodig is om een virtuele machine opnieuw op te starten na een geplande of ongeplande host-onderbreking. Deze methode van hoge Beschik baarheid is daarom niet geschikt voor essentiële onderdelen van een SAP-systeem zoals (A) SCS of DBMS.
>
>

Een ander belang rijk infrastructuur element voor hoge Beschik baarheid is opslag. Azure Storage SLA is bijvoorbeeld 99,9% Beschik baarheid. Als een van de virtuele machines met de schijven in één Azure Storage account implementeert, is het Azure Storage mogelijk dat de niet-beschik baarheid van alle virtuele machines die in dat Azure Storage account worden geplaatst en ook alle SAP-onderdelen die in die virtuele machines worden uitgevoerd, niet beschikbaar zijn.

In plaats van alle Vm's in één Azure Storage account te plaatsen, kunt u ook specifieke opslag accounts voor elke virtuele machine gebruiken, en op deze manier de totale Beschik baarheid van de virtuele machine en de SAP-toepassing verhogen met behulp van meerdere onafhankelijke Azure Storage-accounts.

Azure Managed disks worden automatisch geplaatst in het fout domein van de virtuele machine waaraan ze zijn gekoppeld. Als u twee virtuele machines in een beschikbaarheidsset plaatst en Managed Disks gebruikt, zal het platform er ook voor zorgen dat de Managed Disks naar verschillende fout domeinen worden gedistribueerd. Als u van plan bent Premium Storage te gebruiken, raden wij u ten zeerste aan om ook beheer schijven te gebruiken.

Een voor beeld van een architectuur van een SAP NetWeaver-systeem dat gebruikmaakt van Azure-infrastructuur HA en opslag accounts, ziet er als volgt uit:

![Diagram met een SAP NetWeaver-systeem dat gebruikmaakt van Azure-infrastructuur HA en-opslag accounts.][planning-guide-figure-2900]

Een voor beeld van een architectuur van een SAP NetWeaver-systeem dat gebruikmaakt van Azure-infrastructuur HA en Managed Disks kan er als volgt uitzien:

![Azure-infrastructuur HA gebruiken om een hogere Beschik baarheid van SAP-toepassingen te garanderen][planning-guide-figure-2901]

Voor kritieke SAP-onderdelen hebben we het volgende tot nu toe behaald:

* Hoge Beschik baarheid van SAP-toepassings servers (als)

  SAP-toepassings server exemplaren zijn redundante onderdelen. Elk SAP AS-exemplaar wordt geïmplementeerd op een eigen virtuele machine, die wordt uitgevoerd in een andere Azure-fout en een upgrade domein (zie hoofd [domeinen][planning-guide-3.2.1] en [upgrade domeinen][planning-guide-3.2.2]). Dit wordt gegarandeerd met behulp van Azure-beschikbaarheids sets (zie hoofd stuk [Azure-beschikbaarheids sets][planning-guide-3.2.3]). Potentiële geplande of niet-geplande niet-beschik baarheid van een Azure-fout of een upgrade domein veroorzaakt niet-beschik baarheid van een beperkt aantal Vm's met hun SAP als instanties.

  Elk SAP als-exemplaar wordt opgeslagen in een eigen Azure Storage account-potentiële niet-beschik baarheid van een Azure Storage account leidt er niet toe dat er slechts één VM met het SAP als exemplaar wordt Beschik baarheid. Houd er echter rekening mee dat er een limiet van Azure Storage accounts binnen één Azure-abonnement is. Om ervoor te zorgen dat het (A) SCS-exemplaar na het opnieuw opstarten van de VM automatisch wordt gestart, moet u ervoor zorgen dat u de para meter autostart instelt in (A) het start Profiel van SCS instance dat wordt beschreven in het hoofd stuk [using auto start for SAP instances][planning-guide-11.5]
  Lees ook hoofd stuk [hoge Beschik baarheid voor SAP-toepassings servers][planning-guide-11.4.1] voor meer informatie.

  Zelfs als u Managed Disks gebruikt, worden deze schijven ook opgeslagen in een Azure Storage account en kunnen ze niet beschikbaar zijn in een geval van een storing in de opslag.

* *Hoger* Beschik baarheid van SAP (A) SCS-exemplaar

  Hier gebruiken we de Azure VM opnieuw te starten om de VM te beveiligen met het geïnstalleerde SAP (A) SCS-exemplaar. In het geval van geplande of ongeplande downtime van Azure-servers worden Vm's opnieuw gestart op een andere beschik bare server. Zoals eerder vermeld, beveiligt Azure VM restart voornamelijk Vm's en niet-toepassingen, in dit geval het (A) SCS-exemplaar. Door de VM opnieuw op te starten, hebben we een indirect hogere Beschik baarheid van SAP (A) SCS-exemplaar. Als u na het opnieuw opstarten van de VM het automatisch starten van (A) SCS-exemplaar wilt verzekeren, moet u ervoor zorgen dat u de para meter autostart instelt in (A) het start Profiel van de SCS-instantie die wordt beschreven in het hoofd stuk [met automatisch starten voor SAP][planning-guide-11.5] Dit betekent dat het (A) SCS-exemplaar als een single point of failure (SPOF) wordt uitgevoerd in één virtuele machine de afsluitende factor is voor de beschik baarheid van het hele SAP-landschap.

* *Hoger* Beschik baarheid van DBMS-server

  Net als bij de SAP (A) SCS instance use-case gebruiken we Azure VM opnieuw opstarten om de VM te beschermen met geïnstalleerde DBMS-software, en zorgen we voor een hogere Beschik baarheid van DBMS-software via het opnieuw opstarten van de VM.
  DBMS dat wordt uitgevoerd in één virtuele machine is ook een SPOF en is de afsluitende factor voor de beschik baarheid van het hele SAP-landschap.

### <a name="sap-application-high-availability-on-azure-iaas"></a>Hoge Beschik baarheid van SAP-toepassing op Azure IaaS

Voor een maximale Beschik baarheid van een volledig SAP-systeem moeten alle essentiële SAP-systeem onderdelen, bijvoorbeeld redundante SAP-toepassings servers, en unieke onderdelen (bijvoorbeeld single point of failure), zoals SAP (A) SCS-exemplaar en DBMS, worden beveiligd.

#### <a name="high-availability-for-sap-application-servers"></a><a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Hoge Beschik baarheid voor SAP-toepassings servers

Voor de SAP-toepassings servers/-dialoog instanties is het niet nodig om te denken over een specifieke oplossing voor hoge Beschik baarheid. Hoge Beschik baarheid wordt bereikt door redundantie en daardoor voldoende te hebben op verschillende virtuele machines. Ze moeten allemaal in dezelfde Azure-beschikbaarheidsset worden geplaatst om te voor komen dat de virtuele machines tegelijkertijd worden bijgewerkt tijdens de uitval tijd voor gepland onderhoud. De basis functionaliteit, die voortbouwt op verschillende upgrade-en fout domeinen binnen een Azure-schaal eenheid is al geïntroduceerd in hoofdstuk- [upgrade domeinen][planning-guide-3.2.2]. Azure-beschikbaarheids sets werden gepresenteerd in hoofd stuk [Azure-beschikbaarheids sets][planning-guide-3.2.3] van dit document.

Er is geen oneindig aantal fout-en upgrade domeinen dat kan worden gebruikt door een Azure-beschikbaarheidsset binnen een Azure-schaal eenheid. Dit betekent dat een aantal Vm's in één beschikbaarheidsset wordt geplaatst, dat eerder of later meer dan één virtuele machine in hetzelfde fout-of upgrade domein eindigt.

Implementatie van een paar SAP-toepassings server exemplaren in hun specifieke Vm's en ervan uitgaande dat er vijf upgrade domeinen zijn ontvangen, komt de volgende afbeelding aan het einde aan het eind. Het daad werkelijke maximum aantal fout-en update domeinen binnen een beschikbaarheidsset kan in de toekomst worden gewijzigd:

![HA van SAP-toepassings servers in azure][planning-guide-figure-3000]


#### <a name="high-availability-for-sap-central-services-on-azure"></a>Hoge Beschik baarheid voor SAP Central-Services op Azure

Voor een architectuur met hoge Beschik baarheid van SAP Central-Services op Azure, raadpleegt u de architectuur van het artikel met [hoge Beschik baarheid en scenario's voor SAP NetWeaver](./sap-high-availability-architecture-scenarios.md) als vermelding. Het artikel verwijst naar meer gedetailleerde beschrijvingen voor de specifieke besturings systemen.

#### <a name="high-availability-for-the-sap-database-instance"></a>Hoge Beschik baarheid voor de SAP-data base-instantie

De standaard instelling van de SAP DBMS-HA is gebaseerd op twee DBMS-Vm's waarbij de functionaliteit voor hoge Beschik baarheid van DBMS wordt gebruikt om gegevens van het actieve DBMS-exemplaar te repliceren naar de tweede virtuele machine naar een passief DBMS-exemplaar.

De functionaliteit voor hoge Beschik baarheid en herstel na nood gevallen voor DBMS in het algemeen, evenals specifieke DBMS, worden beschreven in de [DBMS-implementatie handleiding][dbms-guide].

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>End-to-end hoge Beschik baarheid voor het volledige SAP-systeem

Hier volgen twee voor beelden van een volledige SAP NetWeaver HA-architectuur in azure, een voor Windows en één voor Linux.

Alleen niet-beheerde schijven: de concepten zoals hieronder uitgelegd, moeten mogelijk een beetje worden aangetast wanneer u veel SAP-systemen implementeert en het aantal geïmplementeerde Vm's overschrijdt de maximale limiet van opslag accounts per abonnement. In dergelijke gevallen moeten Vhd's met virtuele machines in één opslag account worden gecombineerd. Dit doet u meestal door Vhd's van SAP Application Layer-Vm's van verschillende SAP-systemen te combi neren.  We hebben ook verschillende Vhd's van verschillende DBMS-Vm's van verschillende SAP-systemen gecombineerd in één Azure Storage-account. De IOPS-limieten van Azure Storage accounts in acht houden ( <https://azure.microsoft.com/documentation/articles/storage-scalability-targets> )


##### <a name="windows-logologo_windows-ha-on-windows"></a>![Windows-logo.][Logo_Windows] HA op Windows

![Diagram waarin de architectuur van de SAP NetWeaver-toepassing HA wordt weer gegeven met SQL Server in azure IaaS.][planning-guide-figure-3200]

De volgende Azure-constructs worden gebruikt voor het SAP NetWeaver-systeem om de impact te minimaliseren door problemen met de infra structuur en host-patching:

* Het volledige systeem wordt geïmplementeerd op Azure (vereist-DBMS-laag, (A) SCS-exemplaar en het volt ooien van de toepassingslaag moet op dezelfde locatie worden uitgevoerd).
* Het volledige systeem wordt binnen één Azure-abonnement uitgevoerd (vereist).
* Het volledige systeem wordt binnen één Azure-Virtual Network uitgevoerd (vereist).
* De virtuele machines van een SAP-systeem kunnen niet worden gescheiden in drie beschikbaarheids sets, zelfs met alle virtuele machines die deel uitmaken van hetzelfde Virtual Network.
* Elke laag (bijvoorbeeld DBMS, ASCS, toepassings servers) moet een specifieke beschikbaarheidsset gebruiken.
* Alle Vm's waarop DBMS-exemplaren van één SAP-systeem worden uitgevoerd, bevinden zich in één beschikbaarheidsset. We gaan ervan uit dat er meer dan één virtuele machine met DBMS-instanties per systeem is, aangezien de functies voor hoge Beschik baarheid van systeem eigen DBMS worden gebruikt, zoals SQL Server AlwaysOn of Oracle Data Guard.
* Alle Vm's waarop DBMS-instanties worden uitgevoerd, gebruiken hun eigen opslag account. DBMS-gegevens en-logboek bestanden worden gerepliceerd van het ene opslag account naar een ander opslag account met behulp van DBMS-functies met hoge Beschik baarheid waarmee de gegevens worden gesynchroniseerd. Niet-beschik baarheid van één opslag account veroorzaakt een niet-beschik baarheid van één SQL Windows-cluster knooppunt, maar niet de volledige SQL Server service.
* Alle virtuele machines met (A) SCS-exemplaren van één SAP-systeem bevinden zich in één beschikbaarheidsset. Er wordt een Windows Server failover cluster (WSFC) geconfigureerd in de virtuele machines om het (A) SCS-exemplaar te beveiligen.
* Alle virtuele machines met (A) SCS-instanties gebruiken hun eigen opslag account. Één SCS-exemplaar bestanden en SAP Global-map worden gerepliceerd van het ene opslag account naar een ander opslag account met behulp van SIOS data keeper-replicatie. Niet-beschik baarheid van één opslag account veroorzaakt een niet-beschik baarheid van een (A) SCS Windows-cluster knooppunt, maar niet de hele (A) SCS-service.
* ALLE virtuele machines die de SAP Application Server-laag vertegenwoordigen, bevinden zich in een derde beschikbaarheidsset.
* ALLE virtuele machines waarop SAP-toepassings servers worden uitgevoerd, gebruiken hun eigen opslag account. Niet-beschik baarheid van één opslag account leidt ertoe dat er niet meer dan één SAP-toepassings server beschikbaar is, waarbij andere SAP-toepassings servers blijven uitvoeren.

In de volgende afbeelding ziet u hetzelfde landschap met Managed Disks.

![Architectuur van de Application HA van SAP net-Weaver met SQL Server in azure IaaS][planning-guide-figure-3201]

##### <a name="linux-logologo_linux-ha-on-linux"></a>![Linux-logo.][Logo_Linux] HA op Linux

De architectuur voor SAP HA onder Linux op Azure is in principe hetzelfde als voor Windows zoals hierboven beschreven. Raadpleeg SAP Note [1928533] voor een lijst met ondersteunde oplossingen met hoge Beschik baarheid.

### <a name="using-autostart-for-sap-instances"></a><a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Automatisch starten voor SAP-instanties gebruiken

SAP heeft de functionaliteit aangeboden om SAP-instanties direct na het starten van het besturings systeem in de virtuele machine te starten. De exacte stappen zijn beschreven in het SAP Knowledge Base-artikel [1909114]. SAP wordt echter niet aanbevolen om de instelling meer te gebruiken omdat er geen besturings element in de volg orde van het opnieuw opstarten van het exemplaar is, ervan uitgaande dat er meer dan één virtuele machine is betrokken of meerdere exemplaren per VM zijn uitgevoerd. Uitgaande van een typische Azure-scenario van één SAP Application Server-exemplaar in een VM en het geval van één VM uiteindelijk opnieuw wordt gestart, is automatisch starten niet kritiek en kan worden ingeschakeld door het toevoegen van deze para meter:

`Autostart = 1`

In het begin Profiel van het SAP-ABAP en/of Java-exemplaar.

> [!NOTE]
> De auto start-para meter kan ook een of meer downfalls hebben. Met de para meter wordt het starten van een SAP ABAP-of Java-exemplaar geactiveerd wanneer de verwante Windows/Linux-service van het exemplaar wordt gestart. Dat is zeker het geval wanneer het besturings systeem wordt opgestart. Het opnieuw starten van SAP-Services is echter ook een gang bare manier om de beheer functionaliteit voor SAP-software levenscyclus, zoals som of andere updates of upgrades. In deze functies wordt niet verwacht dat een exemplaar helemaal opnieuw wordt gestart. Daarom moet de para meter autostart worden uitgeschakeld voordat deze taken worden uitgevoerd. De para meter automatisch starten moet ook niet worden gebruikt voor SAP-instanties die zijn geclusterd, zoals ASCS/SCS/CI.
>
>

Zie hier voor meer informatie over automatisch starten voor SAP-instanties:

* [Het starten/stoppen van SAP samen met de UNIX-server starten/stoppen](https://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [SAP NetWeaver-beheer agenten starten en stoppen](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [Automatisch starten van HANA-data base inschakelen](http://sapbasisinfo.com/blog/2016/08/15/enabling-autostart-of-sap-hana-database-on-server-boot-situation/)

### <a name="larger-3-tier-sap-systems"></a>Grotere SAP-systemen met 3 lagen
High-Availability aspecten van 3-tier SAP-configuraties zijn al besproken in eerdere secties. Maar hoe weet het systeem waar de vereisten voor de DBMS-server te groot zijn om deze te hebben in azure, maar de SAP-toepassingslaag kan in Azure worden geïmplementeerd?

#### <a name="location-of-3-tier-sap-configurations"></a>Locatie van de 3-tier SAP-configuraties
Het wordt niet ondersteund om de toepassingslaag zelf of de toepassing en de DBMS-laag te splitsen tussen on-premises en Azure. Een SAP-systeem is volledig geïmplementeerd op locatie of in Azure. Het is ook niet mogelijk om sommige toepassings servers on-premises en andere in azure uit te voeren. Dat is het begin punt van de discussie. We ondersteunen ook niet dat de DBMS-onderdelen van een SAP-systeem en de SAP Application Server-laag in twee verschillende Azure-regio's zijn geïmplementeerd. Bijvoorbeeld: DBMS in VS-West en SAP-toepassingslaag in VS-midden. De reden voor het niet ondersteunen van dergelijke configuraties is de latentie gevoeligheid van de SAP NetWeaver-architectuur.

In de loop van de afgelopen jaren hebben partners van data centers echter co-locaties ontwikkeld naar Azure-regio's. Deze co-locaties zijn vaak dicht bij de fysieke Azure-data centers in een Azure-regio. De korte afstand en verbinding van assets in de co-locatie via ExpressRoute in azure kan resulteren in een latentie van minder dan 2 milliseconden. In dergelijke gevallen is het mogelijk om de DBMS-laag (inclusief opslag-SAN/NAS) in een dergelijke co-locatie en de SAP-toepassingslaag in azure te vinden. [Hana grote instanties](./hana-overview-architecture.md).

### <a name="offline-backup-of-sap-systems"></a>Offline back-up van SAP-systemen
Afhankelijk van de gekozen SAP-configuratie (2 of 3 lagen) moet er een back-up worden gemaakt. De inhoud van de virtuele machine zelf plus een back-up van de data base. De DBMS-gerelateerde back-ups worden naar verwachting uitgevoerd met database methoden. Een gedetailleerde beschrijving van de verschillende data bases vindt u in de [DBMS-hand leiding][dbms-guide]. Aan de andere kant kunt u een back-up maken van de SAP-gegevens, zoals wordt beschreven in deze sectie of online, zoals wordt beschreven in de volgende sectie.

De offline back-up vereist in principe dat de virtuele machine wordt afgesloten via de Azure Portal en een kopie van de basis-VM-schijf en alle gekoppelde schijven aan de virtuele machine. Hiermee wordt een punt in de tijd van de virtuele machine en de bijbehorende schijf bewaard. Het is raadzaam om de back-ups te kopiëren naar een ander Azure Storage-account. De procedure die wordt beschreven in het hoofd stuk [kopiëren van schijven tussen Azure Storage accounts][planning-guide-5.4.2] van dit document, is daarom ook van toepassing.


Een herstel bewerking van die staat bestaat uit het verwijderen van de basis-VM, evenals de oorspronkelijke schijven van de basis-VM en gekoppelde schijven, waarbij de opgeslagen schijven naar het oorspronkelijke opslag account of de resource groep voor beheerde schijven worden gekopieerd en het systeem vervolgens opnieuw kan implementeren.
In dit artikel wordt een voor beeld gegeven van het script voor dit proces in Power shell: <https://www.westerndevs.com/_/azure-snapshots/>

Zorg ervoor dat u een nieuwe SAP-licentie installeert, omdat u een VM-back-up herstelt zoals hierboven is beschreven. Hiermee maakt u een nieuwe hardware-sleutel.

### <a name="online-backup-of-an-sap-system"></a>Online back-up van een SAP-systeem

Er wordt een back-up van het DBMS uitgevoerd met DBMS-specifieke methoden zoals beschreven in de [DBMS-hand leiding][dbms-guide].

Er kan een back-up worden gemaakt van andere Vm's in het SAP-systeem met de back-upfunctie van Azure virtual machine. Back-up van Azure virtual machine is een standaard methode voor het maken van een back-up van een volledige VM in Azure. Azure Backup slaat de back-ups op in Azure en maakt het herstellen van een VM opnieuw mogelijk.

> [!NOTE]
> Vanaf dec 2015 met back-up van de VM wordt de unieke VM-ID die wordt gebruikt voor SAP-licentie verlening niet bewaard. Dit betekent dat bij een terugzet bewerking vanuit een VM-back-up een nieuwe SAP-licentie sleutel moet worden geïnstalleerd als de herstelde VM wordt beschouwd als een nieuwe virtuele machine en niet als vervanging van de eerste opgeslagen versie.
>
> ![Windows-logo.][Logo_Windows] Windows
>
> In theorie kan een back-up op een consistente manier worden gemaakt voor virtuele machines die data bases uitvoeren, ook als het DBMS-systeem de Windows VSS (Volume Shadow Copy Service) ondersteunt, bijvoorbeeld <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx> SQL Server.
> Houd er echter rekening mee dat op basis van back-ups van Azure-VM'S Point-in-time herstel bewerkingen van data bases niet mogelijk zijn. Daarom is het aanbeveling om back-ups van data bases met de DBMS-functionaliteit uit te voeren in plaats van te vertrouwen op Azure VM-back-ups.
>
> Begin hier om vertrouwd te raken met de back-up van Azure virtual machine: <https://docs.microsoft.com/azure/backup/backup-azure-vms> .
>
> Andere mogelijkheden zijn het gebruik van een combi natie van micro soft Data Protection Manager die is geïnstalleerd in een Azure-VM en Azure Backup naar data bases voor back-up/herstel. Meer informatie vindt u hier: <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction> .
>
> ![Linux-logo.][Logo_Linux] Linux
>
> Er is geen equivalent voor Windows VSS in Linux. Daarom zijn alleen bestands consistente back-ups mogelijk, maar geen toepassings consistente back-ups. De back-up van de SAP-DBMS moet worden uitgevoerd met behulp van DBMS-functionaliteit. Het bestands systeem dat de SAP-gerelateerde gegevens bevat, kan worden opgeslagen, bijvoorbeeld met behulp van teer, zoals hier wordt beschreven: <https://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Azure als DR-site voor productie-SAP landschappen

Sinds mid 2014, uitbrei dingen voor verschillende onderdelen van Hyper-V, System Center en Azure, wordt het gebruik van Azure als DR-site ingeschakeld voor Vm's die on-premises worden uitgevoerd op basis van Hyper-V.

Een blog waarin wordt uitgelegd hoe u deze oplossing implementeert, is hier gedocumenteerd: <https://docs.microsoft.com/archive/blogs/saponsqlserver/protecting-sap-solutions-with-azure-site-recovery> .

## <a name="summary-for-high-availability-for-sap-systems"></a>Samen vatting voor hoge Beschik baarheid voor SAP-systemen

De belangrijkste punten van hoge Beschik baarheid voor SAP-systemen in azure zijn:

* Op dit moment kan de SAP-Single Point of Failure niet exact op dezelfde manier worden beveiligd als voor on-premises implementaties. De reden hiervoor is dat gedeelde schijf clusters nog niet in azure zijn ingebouwd zonder dat hiervoor software van derden wordt gebruikt.
* Voor de DBMS-laag moet u DBMS-functionaliteit gebruiken die niet afhankelijk is van de gedeelde schijf cluster technologie. Details worden beschreven in de [DBMS-hand leiding][dbms-guide].
* Als u de gevolgen van problemen binnen fout domeinen in de Azure-infra structuur of het onderhoud van hosts wilt minimaliseren, gebruikt u Azure-beschikbaarheids sets:
  * U wordt aangeraden één beschikbaarheidsset voor de SAP-toepassingslaag te hebben.
  * U wordt aangeraden een afzonderlijke beschikbaarheidsset te hebben voor de SAP-DBMS-laag.
  * Het wordt niet aangeraden dezelfde beschikbaarheidsset toe te passen voor Vm's van verschillende SAP-systemen.
  * Het is raadzaam om Premium-Managed Disks te gebruiken.
* Raadpleeg de [DBMS-hand leiding][dbms-guide]voor back-updoeleinden van de SAP-DBMS-laag.
* Het maken van een back-up van SAP-dialoog instanties maakt weinig zinnig, omdat het doorgaans sneller is om eenvoudige dialoogvenster instanties opnieuw te implementeren.
* Het maken van een back-up van de virtuele machine, die de algemene map van het SAP-systeem bevat en alle profielen van de verschillende instanties, is zinvol en moet worden uitgevoerd met Windows back-up of, bijvoorbeeld teer op Linux. Omdat er verschillen zijn tussen Windows Server 2008 (R2) en Windows Server 2012 (R2), waardoor het eenvoudiger is om een back-up te maken van de meest recente versies van Windows Server, wordt u aangeraden Windows Server 2012 (R2) uit te voeren als Windows-gast besturingssysteem.

## <a name="next-steps"></a>Volgende stappen
Lees de artikelen:

- [Azure Virtual Machines-implementatie voor SAP NetWeaver](./deployment-guide.md)
- [Overwegingen voor de implementatie van Azure Virtual Machines DBMS voor SAP-workloads](./dbms_guide_general.md)
- [Configuraties en bewerkingen van SAP HANA-infrastructuur in Azure](/- azure/virtual-machines/workloads/sap/hana-vm-operations)
