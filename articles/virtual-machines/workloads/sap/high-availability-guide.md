---
title: Hoge Beschik baarheid voor SAP net-Weaver op Azure-Vm's
description: Hand leiding voor hoge Beschik baarheid voor SAP NetWeaver op Azure Virtual Machines
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/24/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6aedaf829df941a225cd0684318f28bb06ba89d8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674612"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms"></a>Hoge Beschik baarheid voor SAP net-Weaver op Azure-Vm's

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
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:https://docs.microsoft.com/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:dbms_guide_general.md
[dbms-guide-2.1]:dbms_guide_general.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f
[dbms-guide-2.2]:dbms_guide_general.md#c8e566f9-21b7-4457-9f7f-126036971a91
[dbms-guide-2.3]:dbms_guide_general.md#10b041ef-c177-498a-93ed-44b3441ab152
[dbms-guide-2]:dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64
[dbms-guide-3]:dbms_guide_general.md#871dfc27-e509-4222-9370-ab1de77021c3
[dbms-guide-5.5.1]:dbms_guide_general.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268
[dbms-guide-5.5.2]:dbms_guide_general.md#f9071eff-9d72-4f47-9da4-1852d782087b
[dbms-guide-5.6]:dbms_guide_general.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8
[dbms-guide-5.8]:dbms_guide_general.md#9053f720-6f3b-4483-904d-15dc54141e30
[dbms-guide-5]:dbms_guide_general.md#3264829e-075e-4d25-966e-a49dad878737
[dbms-guide-8.4.1]:dbms_guide_general.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573
[dbms-guide-8.4.2]:dbms_guide_general.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d
[dbms-guide-8.4.3]:dbms_guide_general.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c
[dbms-guide-8.4.4]:dbms_guide_general.md#f77c1436-9ad8-44fb-a331-8671342de818
[dbms-guide-900-sap-cache-server-on-premises]:dbms_guide_general.md#642f746c-e4d4-489d-bf63-73e80177a0a8

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

[deploy-template-cli]:../../../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]:../../../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]:../../../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:../../virtual-machines-windows-sap-get-started.md
[getting-started-dbms]:../../virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:../../virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:../../virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]:high-availability-guide.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

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

[sap-ha-guide]:high-availability-guide.md
[sap-ha-guide-1]:high-availability-guide.md#217c5479-5595-4cd8-870d-15ab00d4f84c
[sap-ha-guide-2]:high-availability-guide.md#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-3]:high-availability-guide.md#42156640c6-01cf-45a9-b225-4baa678b24f1
[sap-ha-guide-3.1]:high-availability-guide.md#f76af273-1993-4d83-b12d-65deeae23686
[sap-ha-guide-3.2]:high-availability-guide.md#3e85fbe0-84b1-4892-87af-d9b65ff91860
[sap-ha-guide-4]:high-availability-guide.md#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-4.1]:high-availability-guide.md#1a3c5408-b168-46d6-99f5-4219ad1b1ff2
[sap-ha-guide-5]:high-availability-guide.md#fdfee875-6e66-483a-a343-14bbaee33275
[sap-ha-guide-5.1]:high-availability-guide.md#be21cf3e-fb01-402b-9955-54fbecf66592
[sap-ha-guide-5.2]:high-availability-guide.md#ff7a9a06-2bc5-4b20-860a-46cdb44669cd
[sap-ha-guide-6]:high-availability-guide.md#2ddba413-a7f5-4e4e-9a51-87908879c10a
[sap-ha-guide-6.1]:high-availability-guide.md#1a464091-922b-48d7-9d08-7cecf757f341
[sap-ha-guide-6.2]:high-availability-guide.md#44641e18-a94e-431f-95ff-303ab65e0bcb
[sap-ha-guide-7]:high-availability-guide.md#2e3fec50-241e-441b-8708-0b1864f66dfa
[sap-ha-guide-7.1]:high-availability-guide.md#93faa747-907e-440a-b00a-1ae0a89b1c0e
[sap-ha-guide-7.2]:high-availability-guide.md#f559c285-ee68-4eec-add1-f60fe7b978db
[sap-ha-guide-7.2.1]:high-availability-guide.md#b5b1fd0b-1db4-4d49-9162-de07a0132a51
[sap-ha-guide-7.3]:high-availability-guide.md#ddd878a0-9c2f-4b8e-8968-26ce60be1027
[sap-ha-guide-7.4]:high-availability-guide.md#045252ed-0277-4fc8-8f46-c5a29694a816
[sap-ha-guide-8]:high-availability-guide.md#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:high-availability-guide.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.2]:high-availability-guide.md#7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310
[sap-ha-guide-8.3]:high-availability-guide.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a
[sap-ha-guide-8.4]:high-availability-guide.md#b22d7b3b-4343-40ff-a319-097e13f62f9e
[sap-ha-guide-8.5]:high-availability-guide.md#9fbd43c0-5850-4965-9726-2a921d85d73f
[sap-ha-guide-8.6]:high-availability-guide.md#84c019fe-8c58-4dac-9e54-173efd4b2c30
[sap-ha-guide-8.7]:high-availability-guide.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e
[sap-ha-guide-8.8]:high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.10]:high-availability-guide.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:high-availability-guide.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:high-availability-guide.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.2]:high-availability-guide.md#e49a4529-50c9-4dcf-bde7-15a0c21d21ca
[sap-ha-guide-8.12.2.1]:high-availability-guide.md#06260b30-d697-4c4d-b1c9-d22c0bd64855
[sap-ha-guide-8.12.2.2]:high-availability-guide.md#4c08c387-78a0-46b1-9d27-b497b08cac3d
[sap-ha-guide-8.12.3]:high-availability-guide.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:high-availability-guide.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:high-availability-guide.md#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-ha-guide-9.1.2]:high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0
[sap-ha-guide-9.1.3]:high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-ha-guide-9.1.4]:high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-ha-guide-9.1.5]:high-availability-guide.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-ha-guide-9.2]:high-availability-guide.md#85d78414-b21d-4097-92b6-34d8bcb724b7
[sap-ha-guide-9.3]:high-availability-guide.md#8a276e16-f507-4071-b829-cdc0a4d36748
[sap-ha-guide-9.4]:high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-ha-guide-9.5]:high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5
[sap-ha-guide-9.6]:high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772
[sap-ha-guide-10]:high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9
[sap-ha-guide-10.1]:high-availability-guide.md#65fdef0f-9f94-41f9-b314-ea45bbfea445
[sap-ha-guide-10.2]:high-availability-guide.md#5e959fa9-8fcd-49e5-a12c-37f6ba07b916
[sap-ha-guide-10.3]:high-availability-guide.md#755a6b93-0099-4533-9f6d-5c9a613878b5

[sap-ha-multi-sid-guide]:high-availability-multi-sid.md (Multi-SID-configuratie met hoge Beschik baarheid van SAP)


[sap-ha-guide-figure-1000]:media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam (SAP-product beschikbaarheids matrix)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
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
[virtual-machines-windows-attach-disk-portal]:../../virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../azure-resource-manager/management/overview.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-capture]:../../linux/capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]:../../virtual-machines-windows-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../windows/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:../../virtual-machines-windows-sizes.md
[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
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
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md


Azure Virtual Machines is de oplossing voor organisaties die behoefte hebben aan reken-, opslag-en netwerk bronnen, op minimale tijd en zonder langdurige inkoop cycli. U kunt Azure Virtual Machines gebruiken om klassieke toepassingen te implementeren, zoals op SAP NetWeaver gebaseerde ABAP, Java en een ABAP + Java-stack. Betrouw baarheid en beschik baarheid uitbreiden zonder extra on-premises resources. Azure Virtual Machines ondersteunt cross-premises-connectiviteit, zodat u Azure Virtual Machines kunt integreren in de on-premises domeinen, persoonlijke Clouds en het SAP-systeem van uw organisatie.

In dit artikel worden de stappen beschreven die u kunt nemen om SAP-systemen met hoge Beschik baarheid in azure te implementeren met behulp van het Azure Resource Manager-implementatie model. We helpen u bij het uitvoeren van deze grote taken:

* Zoek de juiste SAP-opmerkingen en installatie handleidingen die worden weer gegeven in de sectie [resources][sap-ha-guide-2] . Dit artikel vormt een aanvulling op de SAP-installatie documentatie en SAP-notities, die de primaire bronnen zijn die u kunnen helpen bij het installeren en implementeren van SAP-software op specifieke platforms.
* Meer informatie over de verschillen tussen het Azure Resource Manager-implementatie model en het klassieke Azure-implementatie model.
* Meer informatie over de Windows Server failover clustering-quorum modi, zodat u het model kunt selecteren dat geschikt is voor uw Azure-implementatie.
* Meer informatie over de gedeelde opslag in Azure-Services in Windows Server Failover Clustering.
* Meer informatie over het beveiligen van single-point-of-failure-onderdelen, zoals Advanced Business Application Programming (ABAP) SAP Central Services (ASCS)/SAP Central Services (SCS) en database beheersystemen (DBMS) en redundante onderdelen als SAP-toepassings server, in Azure.
* Volg een stapsgewijze voor beeld van een installatie en configuratie van een SAP-systeem met hoge Beschik baarheid in een Windows Server failover clustering-cluster in azure met behulp van Azure Resource Manager.
* Meer informatie over extra stappen die vereist zijn voor het gebruik van Windows Server Failover Clustering in azure, maar die niet nodig zijn in een on-premises implementatie.

Ter vereenvoudiging van de implementatie en configuratie gebruiken we in dit artikel de SAP-sjablonen met een hoge Beschik baarheid van de 3-tier met drie lagen. Met de sjablonen wordt de implementatie van de volledige infra structuur geautomatiseerd die u nodig hebt voor een SAP-systeem met hoge Beschik baarheid. De infra structuur biedt ook ondersteuning voor de SAP'S-grootte (SAP Application Performance Standard) van uw SAP-systeem.

## <a name="prerequisites"></a><a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Vereisten
Voordat u begint, moet u ervoor zorgen dat u voldoet aan de vereisten die worden beschreven in de volgende secties. Zorg er ook voor dat alle resources die worden vermeld in de sectie [resources][sap-ha-guide-2] , worden gecontroleerd.

In dit artikel gebruiken we Azure Resource Manager sjablonen voor een [SAP NetWeaver met drie lagen](https://github.com/Azure/azure-quickstart-templates/). Zie [SAP Azure Resource Manager-sjablonen](/archive/blogs/saponsqlserver/azure-quickstart-templates-for-sap)voor een nuttige overzicht van sjablonen.

## <a name="resources"></a><a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> Resources
Deze artikelen behandelen SAP-implementaties in Azure:

* [Azure Virtual Machines planning en implementatie voor SAP net-Weaver][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP NetWeaver][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP net-Weaver][dbms-guide]
* [Azure Virtual Machines hoge Beschik baarheid voor SAP NetWeaver (deze hand leiding)][sap-ha-guide]

> [!NOTE]
> Als dat mogelijk is, geeft u een koppeling naar de verwijzende SAP-installatie handleiding (Zie [SAP-installatie][sap-installation-guides]handleidingen). Voor vereisten en informatie over het installatie proces is het een goed idee om de SAP NetWeaver-installatie handleidingen zorgvuldig te lezen. In dit artikel worden alleen specifieke taken behandeld voor SAP NetWeaver-systemen die u kunt gebruiken met Azure Virtual Machines.
>
>

Deze SAP-opmerkingen zijn gerelateerd aan het onderwerp van SAP in Azure:

| Nummer van notitie | Titel |
| --- | --- |
| [1928533] |SAP-toepassingen op Azure: ondersteunde producten en grootte |
| [2015553] |SAP op Microsoft Azure: vereisten voor ondersteuning |
| [1999351] |Verbeterde Azure-bewaking voor SAP |
| [2178632] |Belangrijkste meet waarden voor SAP op Microsoft Azure |
| [1999351] |Virtualisatie op Windows: uitgebreide bewaking |
| [2243692] |Gebruik van Azure Premium-SSD Storage voor SAP DBMS-exemplaar |

Meer informatie over de [beperkingen van Azure-abonnementen][azure-resource-manager/management/azure-subscription-service-limits-subscription], met inbegrip van algemene standaard beperkingen en maximum beperkingen.

## <a name="high-availability-sap-with-azure-resource-manager-vs-the-azure-classic-deployment-model"></a><a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>SAP met hoge Beschik baarheid met Azure Resource Manager versus het klassieke Azure-implementatie model
De Azure Resource Manager-en klassieke implementatie modellen van Azure verschillen op de volgende gebieden:

- Resourcegroepen
- Azure interne load balancer afhankelijkheid van de Azure-resource groep
- Ondersteuning voor SAP multi-SID-scenario's

### <a name="resource-groups"></a><a name="f76af273-1993-4d83-b12d-65deeae23686"></a> Resource groepen
In Azure Resource Manager kunt u resource groepen gebruiken om alle toepassings resources in uw Azure-abonnement te beheren. Een geïntegreerde benadering, in een resource groep, hebben alle resources dezelfde levens cyclus. Zo worden bijvoorbeeld alle resources tegelijk gemaakt en worden deze tegelijkertijd verwijderd op hetzelfde moment. Meer informatie over [resourcegroepen](../../../azure-resource-manager/management/overview.md#resource-groups).

### <a name="azure-internal-load-balancer-dependency-on-the-azure-resource-group"></a><a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Azure interne load balancer afhankelijkheid van de Azure-resource groep

In het klassieke Azure-implementatie model bevindt zich een afhankelijkheid tussen de Azure Internal load balancer (Azure Load Balancer-service) en de Cloud service groep. Elke interne load balancer vereist één Cloud service groep.

In Azure Resource Manager hebt u geen Azure-resource groep nodig om Azure Load Balancer te gebruiken. De omgeving is eenvoudiger en flexibeler.

### <a name="support-for-sap-multi-sid-scenarios"></a>Ondersteuning voor SAP multi-SID-scenario's

In Azure Resource Manager kunt u meerdere SAP System Identifier (SID) ASCS/SCS-exemplaren in één cluster installeren. Multi-SID-exemplaren zijn mogelijk vanwege ondersteuning voor meerdere IP-adressen voor elke interne Azure-load balancer.

Als u het klassieke Azure-implementatie model wilt gebruiken, volgt u de procedures in [SAP Netweave in Azure: Clustering SAP ASCS/SCS instances met behulp van Windows Server Failover Clustering in azure met SIOS data keeper](https://go.microsoft.com/fwlink/?LinkId=613056).

> [!IMPORTANT]
> We raden u ten zeerste aan het Azure Resource Manager-implementatie model voor uw SAP-installaties te gebruiken. Het biedt veel voor delen die niet beschikbaar zijn in het klassieke implementatie model. Meer informatie over Azure- [implementatie modellen][virtual-machines-azure-resource-manager-architecture-benefits-arm].   
>
>

## <a name="windows-server-failover-clustering"></a><a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Windows Server Failover Clustering
Windows Server Failover Clustering is de basis van een High-Availability SAP-ASCS/SCS-installatie en DBMS in Windows.

Een failovercluster is een groep van 1 + n onafhankelijke servers (knoop punten) die samen werken om de beschik baarheid van toepassingen en services te verg Roten. Als er een storing optreedt in een knoop punt, berekent Windows Server failover clustering het aantal fouten dat kan optreden tijdens het behoud van een goed cluster voor het leveren van toepassingen en services. U kunt kiezen uit verschillende quorum modi om failover clustering te bezorgen.

### <a name="quorum-modes"></a><a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Quorum modi
U kunt kiezen uit vier quorum modi wanneer u Windows Server Failover Clustering gebruikt:

* **Knooppunt meerderheid**. Elk knoop punt van het cluster kan stemmen. Het cluster functioneert alleen met een meerderheid van stemmen, dat wil zeggen, met meer dan de helft van de stemmen. We raden u aan deze optie te kiezen voor clusters met een oneven aantal knoop punten. Bijvoorbeeld: drie knoop punten in een cluster met zeven knoop punten kunnen mislukken en het cluster is nog steeds een meerderheid en blijft actief.  
* **Knoop punt-en schijf meerderheid**. Elk knoop punt en een toegewezen schijf (een schijfwitness) in de cluster opslag kan stemmen wanneer deze beschikbaar zijn en in communicatie. Het cluster functioneert alleen met een meerderheid van stemmen, dat wil zeggen, met meer dan de helft van de stemmen. Deze modus is handig in een cluster omgeving met een even aantal knoop punten. Als de helft van de knoop punten en de schijf online zijn, behoudt het cluster de status in orde.
* **Knoop punt-en bestands share meerderheid**. Elk knoop punt plus een aangewezen bestands share (een bestandssharewitness) die de beheerder maakt kan stemmen, ongeacht of de knoop punten en de bestands share beschikbaar en in communicatie zijn. Het cluster functioneert alleen met een meerderheid van stemmen, dat wil zeggen, met meer dan de helft van de stemmen. Deze modus is handig in een cluster omgeving met een even aantal knoop punten. Het is vergelijkbaar met de modus voor het knoop punt en de schijf meerderheid, maar maakt gebruik van een bestandssharewitness in plaats van een witness-schijf. Deze modus is eenvoudig te implementeren, maar als de bestands share zelf niet Maxi maal beschikbaar is, kan het een Single Point of Failure worden.
* **Geen meerderheid: alleen schijf**. Het cluster heeft een quorum als één knoop punt beschikbaar is en in communicatie met een specifieke schijf in de cluster opslag. Alleen de knoop punten die zich ook in de communicatie met die schijf bevinden, kunnen lid worden van het cluster. U wordt aangeraden deze modus niet te gebruiken.

## <a name="windows-server-failover-clustering-on-premises"></a><a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Windows Server failover clustering on-premises
In afbeelding 1 ziet u een cluster met twee knoop punten. Als de netwerk verbinding tussen de knoop punten uitvalt en beide knoop punten weer actief blijven, bepaalt een quorum schijf of bestands share welk knoop punt de toepassingen en services van het cluster blijft leveren. Het knoop punt dat toegang heeft tot de quorum schijf of de bestands share is het knoop punt dat ervoor zorgt dat de services worden voortgezet.

Omdat in dit voor beeld een cluster met twee knoop punten wordt gebruikt, gebruiken we de quorum modus knoop punt-en bestands share meerderheid. Het knoop punt en de schijf meerderheid is ook een geldige optie. In een productie omgeving wordt u aangeraden een quorum schijf te gebruiken. U kunt de technologie voor netwerk-en opslag systemen gebruiken om deze Maxi maal beschikbaar te maken.

![Afbeelding 1: voor beeld van een Windows Server failover clustering-configuratie voor SAP ASCS/SCS in azure][sap-ha-guide-figure-1000]

_**Afbeelding 1:** Voor beeld van een configuratie van Windows Server Failover Clustering voor SAP ASCS/SCS in azure_

### <a name="shared-storage"></a><a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> Gedeelde opslag
In afbeelding 1 ziet u ook een gedeeld opslag cluster met twee knoop punten. In een on-premises gedeeld opslag cluster detecteren alle knoop punten in het cluster gedeelde opslag. Een vergrendelings mechanisme beveiligt de gegevens tegen beschadiging. Alle knoop punten kunnen detecteren of een ander knoop punt uitvalt. Als één knoop punt mislukt, wordt het resterende knoop punt eigenaar van de opslag bronnen en wordt de beschik baarheid van services gegarandeerd.

> [!NOTE]
> U hebt geen gedeelde schijven nodig voor hoge Beschik baarheid met bepaalde DBMS-toepassingen, zoals bij SQL Server. SQL Server altijd DBMS-gegevens en logboek bestanden van de lokale schijf van een cluster knooppunt repliceren naar de lokale schijf van een ander cluster knooppunt. In dat geval heeft de configuratie van het Windows-cluster geen gedeelde schijf nodig.
>
>

### <a name="networking-and-name-resolution"></a><a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Netwerken en naam omzetting
Client computers bereiken het cluster via een virtueel IP-adres en de naam van een virtuele host die door de DNS-server wordt verstrekt. De on-premises knoop punten en de DNS-server kunnen meerdere IP-adressen verwerken.

In een typische installatie gebruikt u twee of meer netwerk verbindingen:

* Een speciale verbinding met de opslag
* Een interne netwerk verbinding voor de heartbeat
* Een openbaar netwerk dat clients gebruiken om verbinding te maken met het cluster

## <a name="windows-server-failover-clustering-in-azure"></a><a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Windows Server Failover Clustering in azure
Vergeleken met bare metal of particuliere Cloud implementaties, vereist Azure Virtual Machines extra stappen voor het configureren van Windows Server Failover Clustering. Wanneer u een gedeelde cluster schijf bouwt, moet u verschillende IP-adressen en namen van virtuele hosts instellen voor het SAP-exemplaar ASCS/SCS.

In dit artikel bespreken we belang rijke concepten en de aanvullende stappen die nodig zijn om een SAP-cluster met hoge Beschik baarheid te bouwen in Azure. We laten u zien hoe u het hulp programma van derden data keeper kunt instellen en hoe u de interne Azure-load balancer kunt configureren. U kunt deze hulpprogram ma's gebruiken om een Windows-failovercluster te maken met een bestands share-Witness in Azure.

![Afbeelding 2: configuratie van Windows Server Failover Clustering in azure zonder een gedeelde schijf][sap-ha-guide-figure-1001]

_**Afbeelding 2:** Configuratie van Windows Server Failover Clustering in azure zonder een gedeelde schijf_

### <a name="shared-disk-in-azure-with-sios-datakeeper"></a><a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> Gedeelde schijf in azure met SIOS data keeper
U hebt gedeelde cluster opslag nodig voor een High-Availability SAP-ASCS/SCS-exemplaar. Vanaf september 2016 biedt Azure geen gedeelde opslag die u kunt gebruiken om een gedeeld opslag cluster te maken. U kunt software van derden SIOS data keeper cluster Edition gebruiken om een gespiegelde opslag te maken die gedeelde cluster opslag simuleert. De SIOS-oplossing biedt synchrone gegevens replicatie in realtime. Zo kunt u een gedeelde schijf bron voor een cluster maken:

1. Koppel een extra virtuele harde schijf (VHD) van Azure aan elk van de virtuele machines (Vm's) in een configuratie van een Windows-cluster.
2. Voer SIOS data keeper cluster Edition uit op beide VM-knoop punten.
3. Configureer de data keeper-cluster versie van SIOS zodat deze de inhoud van het extra VHD-gekoppelde volume van de virtuele bron machine spiegelt naar het extra VHD-volume van de virtuele doel machine. SIOS data keeper maakt samen vatting van de bron-en doel volumes en geeft deze vervolgens weer als één gedeelde schijf aan Windows Server Failover Clustering.

Meer informatie over [SIOS data keeper](https://us.sios.com/products/datakeeper-cluster/).

![Afbeelding 3: configuratie van Windows Server Failover Clustering in azure met SIOS data keeper][sap-ha-guide-figure-1002]

_**Afbeelding 3:** Configuratie van Windows Server Failover Clustering in azure met SIOS data keeper_

> [!NOTE]
> U hebt geen gedeelde schijven nodig voor hoge Beschik baarheid met enkele DBMS-producten, zoals SQL Server. SQL Server altijd DBMS-gegevens en logboek bestanden van de lokale schijf van een cluster knooppunt repliceren naar de lokale schijf van een ander cluster knooppunt. In dit geval heeft de configuratie van het Windows-cluster geen gedeelde schijf nodig.
>
>

### <a name="name-resolution-in-azure"></a><a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Naam omzetting in azure
Het Azure-Cloud platform biedt geen optie voor het configureren van virtuele IP-adressen, zoals zwevende IP-adressen. U hebt een alternatieve oplossing nodig om een virtueel IP-adres in te stellen om de cluster bron in de cloud te bereiken.
Azure heeft een interne load balancer in de Azure Load Balancer-service. Met de interne load balancer bereiken clients het cluster via het virtuele IP-adres van het cluster.
U moet de interne load balancer implementeren in de resource groep die de cluster knooppunten bevat. Configureer vervolgens alle benodigde regels voor het door sturen van poorten met de test poorten van de interne load balancer.
De clients kunnen verbinding maken via de naam van de virtuele host. De DNS-server zet het IP-adres van het cluster op en de interne load balancer verwerkt poort door sturen naar het actieve knoop punt van het cluster.

## <a name="sap-netweaver-high-availability-in-azure-infrastructure-as-a-service-iaas"></a><a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> SAP NetWeaver hoge Beschik baarheid in azure Infrastructure-as-a-Service (IaaS)
Voor een hoge Beschik baarheid van SAP-toepassingen, zoals voor SAP-software onderdelen, moet u de volgende onderdelen beveiligen:

* SAP Application Server-exemplaar
* SAP ASCS/SCS-instantie
* DBMS-server

Meer informatie over het beveiligen van SAP-onderdelen in scenario's met hoge Beschik baarheid vindt u in [Azure virtual machines planning en implementatie van SAP NetWeaver](planning-guide.md).

### <a name="high-availability-sap-application-server"></a><a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> SAP-toepassings server met hoge Beschik baarheid
Normaal gesp roken hebt u geen specifieke oplossing voor hoge Beschik baarheid nodig voor de SAP-toepassings server en dialoogvenster exemplaren. U behaalt hoge Beschik baarheid door redundantie en configureert meerdere dialoog exemplaren in verschillende exemplaren van Azure Virtual Machines. Er moeten ten minste twee SAP-toepassings exemplaren zijn geïnstalleerd in twee exemplaren van Azure Virtual Machines.

![Afbeelding 4: een SAP-toepassings server met hoge Beschik baarheid][sap-ha-guide-figure-2000]

_**Afbeelding 4:** SAP-toepassings server met hoge Beschik baarheid_

U moet alle virtuele machines die SAP Application Server-exemplaren hosten in dezelfde Azure-beschikbaarheidsset plaatsen. Een beschikbaarheidsset van Azure zorgt ervoor dat:

* Alle virtuele machines maken deel uit van hetzelfde upgrade domein. Een upgrade domein zorgt er bijvoorbeeld voor dat de virtuele machines niet tegelijkertijd worden bijgewerkt tijdens de uitval tijd voor gepland onderhoud.
* Alle virtuele machines maken deel uit van hetzelfde fout domein. Een fout domein zorgt er bijvoorbeeld voor dat virtuele machines worden geïmplementeerd, zodat er geen Single Point of Failure invloed heeft op de beschik baarheid van alle virtuele machines.

Meer informatie over [het beheren van de beschik baarheid van virtuele machines][virtual-machines-manage-availability].

Omdat het Azure-opslag account een mogelijke Single Point of Failure is, is het belang rijk dat u ten minste twee Azure-opslag accounts hebt, waarin ten minste twee virtuele machines worden gedistribueerd. In een ideale configuratie worden de schijven van elke virtuele machine waarop een SAP-dialoog exemplaar wordt uitgevoerd, geïmplementeerd in een ander opslag account.

### <a name="high-availability-sap-ascsscs-instance"></a><a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> SAP ASCS/SCS-instantie met hoge Beschik baarheid
Afbeelding 5 is een voor beeld van een SAP ASCS/SCS-exemplaar met hoge Beschik baarheid.

![Afbeelding 5: High-Availability SAP ASCS/SCS-exemplaar][sap-ha-guide-figure-2001]

_**Afbeelding 5:** SAP ASCS/SCS-instantie met hoge Beschik baarheid_

#### <a name="sap-ascsscs-instance-high-availability-with-windows-server-failover-clustering-in-azure"></a><a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> SAP ASCS/SCS-instantie hoge Beschik baarheid met Windows Server Failover Clustering in azure
Vergeleken met bare metal of particuliere Cloud implementaties, vereist Azure Virtual Machines extra stappen voor het configureren van Windows Server Failover Clustering. Als u een Windows-failovercluster wilt bouwen, hebt u een gedeelde cluster schijf, meerdere IP-adressen, verschillende namen van virtuele hosts en een interne Azure-load balancer nodig voor het clusteren van een SAP ASCS/SCS-exemplaar. Verderop in dit artikel vindt u meer informatie over dit onderwerp.

![Afbeelding 6: Windows Server Failover Clustering voor een SAP ASCS/SCS-configuratie in azure met behulp van SIOS data keeper][sap-ha-guide-figure-1002]

_**Afbeelding 6:** Windows Server Failover Clustering voor een SAP ASCS/SCS-configuratie in azure met SIOS data keeper_

### <a name="high-availability-dbms-instance"></a><a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>DBMS-exemplaar met hoge Beschik baarheid
Het DBMS is ook een enkel contact punt in een SAP-systeem. U moet deze beveiligen met behulp van een oplossing met hoge Beschik baarheid. Afbeelding 7 toont een SQL Server altijd een oplossing met hoge Beschik baarheid in azure, met Windows Server Failover Clustering en de interne load balancer van Azure. SQL Server altijd DBMS-gegevens en logboek bestanden repliceert met behulp van de eigen DBMS-replicatie. In dit geval hebt u geen gedeelde cluster schijven nodig, waardoor de volledige installatie wordt vereenvoudigd.

![Afbeelding 7: voor beeld van een hoge Beschik baarheid SAP DBMS, met SQL Server always on][sap-ha-guide-figure-2003]

_**Afbeelding 7:** Voor beeld van een SAP-DBMS met hoge Beschik baarheid, met SQL Server always on_

Zie de volgende artikelen voor meer informatie over het clusteren SQL Server in azure met behulp van het Azure Resource Manager-implementatie model:

* [Always on-beschikbaarheids groep in azure Virtual Machines hand matig configureren met Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Een interne Azure-load balancer configureren voor een AlwaysOn-beschikbaarheids groep in azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="end-to-end-high-availability-deployment-scenarios"></a><a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> End-to-end implementatie scenario's met hoge Beschik baarheid

### <a name="deployment-scenario-using-architectural-template-1"></a>Implementatie scenario met de architectuur sjabloon 1

In afbeelding 8 ziet u een voor beeld van een architectuur met een hoge Beschik baarheid van de SAP-Weaver in azure voor **één** SAP-systeem. Dit scenario wordt als volgt ingesteld:

- Er wordt één toegewezen cluster gebruikt voor het SAP ASCS/SCS-exemplaar.
- Er wordt één toegewezen cluster gebruikt voor het DBMS-exemplaar.
- SAP Application Server-exemplaren worden geïmplementeerd in hun eigen toegewezen Vm's.

![Afbeelding 8: architectuur sjabloon met hoge Beschik baarheid van SAP 1, met toegewezen cluster voor ASCS/SCS en DBMS][sap-ha-guide-figure-2004]

_**Afbeelding 8:** SAP-architectuur sjabloon met hoge Beschik baarheid 1, toegewezen clusters voor ASCS/SCS en DBMS_

### <a name="deployment-scenario-using-architectural-template-2"></a>Implementatie scenario met architectuur sjabloon 2

In afbeelding 9 ziet u een voor beeld van een architectuur met een hoge Beschik baarheid van de SAP-Weaver in azure voor **één** SAP-systeem. Dit scenario wordt als volgt ingesteld:

- Er wordt één toegewezen cluster gebruikt voor **zowel** het SAP ASCS/SCS-exemplaar als het DBMS.
- SAP Application Server-exemplaren worden geïmplementeerd op eigen vaste Vm's.

![Afbeelding 9: architectuur sjabloon met hoge Beschik baarheid van SAP 2, met een toegewezen cluster voor ASCS/SCS en een toegewezen cluster voor DBMS][sap-ha-guide-figure-2005]

_**Afbeelding 9:** SAP-architectuur sjabloon voor hoge Beschik baarheid 2, met een toegewezen cluster voor ASCS/SCS en een toegewezen cluster voor DBMS_

### <a name="deployment-scenario-using-architectural-template-3"></a>Implementatie scenario met architectuur sjabloon 3

In afbeelding 10 ziet u een voor beeld van een architectuur met een hoge Beschik baarheid van de SAP-Weaver in azure voor **twee** SAP-systemen, met &lt; SID1 &gt; en &lt; SID2 &gt; . Dit scenario wordt als volgt ingesteld:

- Eén toegewezen cluster wordt gebruikt voor **zowel** het SAP ASCS/SCS SID1-exemplaar *als* het SAP ASCS/SCS SID2-exemplaar (één cluster).
- Er wordt één toegewezen cluster gebruikt voor DBMS-SID1 en een ander toegewijd cluster wordt gebruikt voor DBMS SID2 (twee clusters).
- SAP-toepassings server exemplaren voor het SAP-systeem SID1 hebben hun eigen toegewezen Vm's.
- SAP-toepassings server exemplaren voor het SAP-systeem SID2 hebben hun eigen toegewezen Vm's.

![Afbeelding 10: architectuur sjabloon met hoge Beschik baarheid van SAP 3, met een toegewezen cluster voor verschillende ASCS/SCS-instanties][sap-ha-guide-figure-6003]

_**Afbeelding 10:** SAP-architectuur sjabloon met hoge Beschik baarheid 3, met een toegewezen cluster voor verschillende ASCS/SCS-instanties_

## <a name="prepare-the-infrastructure"></a><a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> De infra structuur voorbereiden

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>De infra structuur voorbereiden voor de architectuur sjabloon 1
Met Azure Resource Manager sjablonen voor SAP kunt u de implementatie van vereiste resources vereenvoudigen.

De sjablonen met drie lagen in Azure Resource Manager bieden ook ondersteuning voor scenario's met hoge Beschik baarheid, zoals in de architectuur sjabloon 1, die twee clusters heeft. Elk cluster is een SAP-Single Point of Failure voor SAP ASCS/SCS en DBMS.

Hier kunt u Azure Resource Manager sjablonen ophalen voor het voorbeeld scenario dat in dit artikel wordt beschreven:

* [Azure Marketplace-installatie kopie](https://github.com/Azure/azure-quickstart-templates/)  
* [Aangepaste installatiekopie](https://github.com/Azure/azure-quickstart-templates/)

De infra structuur voorbereiden voor de architectuur sjabloon 1:

- Selecteer in de Azure Portal op de Blade **para meters** in het vak **SYSTEMAVAILABILITY** de optie **ha**.

  ![Afbeelding 11: SAP-Azure Resource Manager-para meters met hoge Beschik baarheid instellen][sap-ha-guide-figure-3000]

_**Afbeelding 11:** SAP-Azure Resource Manager-para meters met hoge Beschik baarheid instellen_


  De sjablonen maken:

  * **Virtuele machines**:
    * SAP-toepassings server virtuele machines: <*SAPSystemSID*>-di-<*nummer*>
    * ASCS/SCS cluster virtual machines: <*SAPSystemSID*>-ASCS-<*nummer*>
    * DBMS-cluster: <*SAPSystemSID*>-db-<*nummer*>

  * **Netwerk kaarten voor alle virtuele machines, met de bijbehorende IP-adressen**:
    * <*SAPSystemSID*>-NIC-di-<*nummer*>
    * <*SAPSystemSID*>-NIC-ascs-<*nummer*>
    * <*SAPSystemSID*>-NIC-db-<*nummer*>

  * **Azure-opslagaccounts**

  * **Beschikbaarheids groepen** voor:
    * SAP-toepassings server virtuele machines: <*SAPSystemSID*>-avset-di
    * SAP ASCS/SCS cluster virtual machines: <*SAPSystemSID*>-avset-ASCS
    * Virtuele machines in DBMS-cluster: <*SAPSystemSID*>-avset-db

  * **Interne Load Balancer van Azure**:
    * Met alle poorten voor het ASCS/SCS-exemplaar en IP-adres <*SAPSystemSID*>-lb-ASCS
    * Met alle poorten voor de SQL Server DBMS en het IP-adres <*SAPSystemSID*>-lb-db

  * **Netwerk beveiligings groep**: <*SAPSystemSID*>-NSG-ascs-0  
    * Met een open externe Remote Desktop Protocol (RDP)-poort aan de <*SAPSystemSID*>-ascs-0-virtuele machine

> [!NOTE]
> Alle IP-adressen van de netwerk kaarten en interne load balancers van Azure zijn standaard **dynamisch** . Wijzig deze in **vaste** IP-adressen. Verderop in dit artikel wordt beschreven hoe u dit doet.
>
>

### <a name="deploy-virtual-machines-with-corporate-network-connectivity-cross-premises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Implementeer virtuele machines met de connectiviteit van een bedrijfs netwerk (cross-premises) voor gebruik in productie
Voor productie-SAP-systemen implementeert u virtuele Azure-machines met connectiviteit van het bedrijfs netwerk met behulp van Azure site-naar-site VPN of Azure ExpressRoute.

> [!NOTE]
> U kunt uw Azure Virtual Network-exemplaar gebruiken. Het virtuele netwerk en het subnet zijn al gemaakt en voor bereid.
>
>

1. Selecteer in het Azure Portal op de Blade **para meters** in het vak **NEWOREXISTINGSUBNET** de optie **bestaande**.
2. Voeg in het vak **SUBNETID** de volledige teken reeks toe van uw voor bereide Azure-netwerk SUBNETID waar u uw virtuele Azure-machines wilt implementeren.
3. Voer deze Power shell-opdracht uit om een lijst met alle subnetten van Azure Network op te halen:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   In het veld **id** wordt de **SUBNETID** weer gegeven.
4. Voer de volgende Power shell-opdracht uit om een lijst met alle **SUBNETID** -waarden weer te geven:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   De **SUBNETID** ziet er als volgt uit:

   ```
   /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
   ```

### <a name="deploy-cloud-only-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Alleen Cloud SAP-instanties implementeren voor test en demo
U kunt uw SAP-systeem met hoge Beschik baarheid implementeren in een alleen-Cloud implementatie model. Dit soort implementatie is vooral nuttig voor demonstratie-en test voorbeelden. Het is niet geschikt voor productie-use cases.

- Selecteer in het Azure Portal op de Blade **para meters** in het vak **NEWOREXISTINGSUBNET** de optie **Nieuw**. Laat het veld **SUBNETID** leeg.

  De SAP-Azure Resource Manager sjabloon maakt automatisch het virtuele netwerk van Azure en het subnet.

> [!NOTE]
> U moet ook ten minste één toegewezen virtuele machine implementeren voor Active Directory en DNS in hetzelfde exemplaar van Azure Virtual Network. Deze virtuele machines worden niet gemaakt met de sjabloon.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>De infra structuur voorbereiden voor de architectuur sjabloon 2

U kunt deze Azure Resource Manager sjabloon voor SAP gebruiken om de implementatie van vereiste infrastructuur resources voor SAP-architectuur sjabloon 2 te vereenvoudigen.

Hier kunt u Azure Resource Manager sjablonen voor dit implementatie scenario ophalen:

* [Azure Marketplace-installatie kopie](https://github.com/Azure/azure-quickstart-templates/)  
* [Aangepaste installatiekopie](https://github.com/Azure/azure-quickstart-templates/)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>De infra structuur voorbereiden voor de architectuur sjabloon 3

U kunt de infra structuur voorbereiden en SAP configureren voor **multi-sid**. U kunt bijvoorbeeld een extra SAP ASCS/SCS-exemplaar toevoegen aan een *bestaande* cluster configuratie. Zie [een extra SAP ASCS/SCS-instantie configureren in een bestaande cluster configuratie voor het maken van een SAP-multi-sid-configuratie in azure Resource Manager][sap-ha-multi-sid-guide]voor meer informatie.

Als u een nieuw cluster met meerdere SID'S wilt maken, kunt u gebruikmaken van de Quick Start- [sjablonen voor](https://github.com/Azure/azure-quickstart-templates)multi-sid op github.
Als u een nieuw cluster met meerdere SID'S wilt maken, moet u de volgende drie sjablonen implementeren:

* [ASCS/SCS-sjabloon](#ASCS-SCS-template)
* [Database sjabloon](#database-template)
* [Sjabloon toepassings servers](#application-servers-template)

De volgende secties bevatten meer informatie over de sjablonen en de para meters die u moet opgeven in de sjablonen.

#### <a name="ascsscs-template"></a><a name="ASCS-SCS-template"></a> ASCS/SCS-sjabloon

De ASCS/SCS-sjabloon implementeert twee virtuele machines die u kunt gebruiken om een Windows Server-failovercluster te maken dat meerdere ASCS/SCS-instanties host.

Als u de sjabloon ASCS/SCS multi-SID wilt instellen, voert u in de [sjabloon ASCS/SCS multi-sid][sap-templates-3-tier-multisid-xscs-marketplace-image]waarden in voor de volgende para meters:

  - **Resource voorvoegsel**.  Stel het resource voorvoegsel in, dat wordt gebruikt voor het voor voegsel van alle resources die tijdens de implementatie zijn gemaakt. Omdat de resources niet deel uitmaken van één SAP-systeem, is het voor voegsel van de resource niet de SID van een SAP-systeem.  Het voor voegsel moet tussen de **drie en zes tekens lang** zijn.
  - **Stack-type**. Selecteer het stack type van het SAP-systeem. Afhankelijk van het type stack heeft Azure Load Balancer één (alleen ABAP of Java) of twee (ABAP + Java) privé-IP-adressen per SAP-systeem.
  -  **Type besturings systeem**. Selecteer het besturings systeem van de virtuele machines.
  -  **Aantal SAP-systemen**. Selecteer het aantal SAP-systemen dat u wilt installeren in dit cluster.
  -  **Systeem beschikbaarheid**. Selecteer **ha**.
  -  **Gebruikers naam en beheerders wachtwoord** voor de beheerder. Maak een nieuwe gebruiker die kan worden gebruikt om u aan te melden bij de computer.
  -  **Nieuw of bestaand subnet**. Stel in of een nieuw virtueel netwerk en subnet moet worden gemaakt of dat een bestaand subnet moet worden gebruikt. Als u al een virtueel netwerk hebt dat is verbonden met uw on-premises netwerk, selecteert u **bestaande**.
  -  **Subnet-id**. Als u de virtuele machine wilt implementeren in een bestaand VNet waarvoor u een subnet hebt gedefinieerd, moet de virtuele machine worden toegewezen aan, de ID van het specifieke subnet benoemen. De ID ziet er ongeveer als volgt uit:/Subscriptions/<*abonnements-ID*>/resourceGroups/<naam van de *resource groep*>/providers/Microsoft.Network/virtualNetworks/<naam van het *virtuele netwerk*>/subnets/<naam van het *subnet*>

De sjabloon implementeert een Azure Load Balancer-exemplaar dat meerdere SAP-systemen ondersteunt.

- De ASCS-exemplaren worden geconfigureerd voor instantie nummer 00, 10, 20...
- De SCS-exemplaren zijn geconfigureerd voor instantie nummer 01, 11, 21...
- De exemplaren van de ASCS-replicatie server (ERS) (alleen voor Linux) worden geconfigureerd voor instantie nummer 02, 12, 22...
- De exemplaren van de SCS ERS (alleen Linux) worden geconfigureerd voor instantie nummer 03, 13, 23...

De load balancer bevat 1 (2 voor Linux) VIP (s), 1x VIP voor ASCS/SCS en 1x VIP voor ERS (alleen voor Linux).

De volgende lijst bevat alle regels voor taak verdeling (waarbij x het nummer van het SAP-systeem is, bijvoorbeeld 1, 2, 3...):
- Windows-specifieke poorten voor elk SAP-systeem: 445, 5985
- ASCS-poorten (instantie nummer x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- SCS-poorten (exemplaar nummer x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- ASCS ERS-poorten op Linux (exemplaar nummer x2): 33x2, 5x213, 5x214, 5x216
- SCS ERS-poorten op Linux (exemplaar nummer x3): 33x3, 5x313, 5x314, 5x316

De load balancer is geconfigureerd voor het gebruik van de volgende test poorten (waarbij x het nummer van het SAP-systeem is, bijvoorbeeld 1, 2, 3...):
- ASCS/SCS interne load balancer test poort: 620x0
- ERS interne load balancer test poort (alleen Linux): 621x2

#### <a name="database-template"></a><a name="database-template"></a> Database sjabloon

De database sjabloon implementeert een of twee virtuele machines die u kunt gebruiken om de relationele Database Management System (RDBMS) voor één SAP-systeem te installeren. Als u bijvoorbeeld een ASCS/SCS-sjabloon voor vijf SAP-systemen implementeert, moet u deze sjabloon vijf keer implementeren.

Als u de sjabloon multi-SID voor de Data Base wilt instellen, voert u in de [sjabloon multi-sid-data base][sap-templates-3-tier-multisid-db-marketplace-image]waarden in voor de volgende para meters:

- **SAP-systeem-id**. Voer de SAP-systeem-ID in van het SAP-systeem dat u wilt installeren. De ID wordt gebruikt als voor voegsel voor de resources die worden geïmplementeerd.
- **Type besturings systeem**. Selecteer het besturings systeem van de virtuele machines.
- **DBTYPE**. Selecteer het type Data Base dat u op het cluster wilt installeren. Selecteer **SQL** als u Microsoft SQL Server wilt installeren. Selecteer **Hana** als u SAP Hana wilt installeren op de virtuele machines. Zorg ervoor dat u het juiste type besturings systeem selecteert: Selecteer **Windows** voor SQL en selecteer een Linux-distributie voor Hana. De Azure Load Balancer die is verbonden met de virtuele machines, wordt geconfigureerd voor de ondersteuning van het geselecteerde database type:
  * **SQL**. De load balancer-balans poort 1433 wordt geladen. Zorg ervoor dat u deze poort gebruikt voor de installatie van SQL Server altijd.
  * **Hana**. De load balancer worden taakverdelings poorten 35015 en 35017. Zorg ervoor dat u SAP HANA installeert met instantie nummer **50**.
  In het load balancer wordt test poort 62550 gebruikt.
- **SAP-systeem grootte**. Stel het aantal SAP'S in dat door het nieuwe systeem moet worden geboden. Als u niet zeker weet hoeveel SAP'S het systeem nodig heeft, vraagt u uw SAP-technologie partner of systeem integrator.
- **Systeem beschikbaarheid**. Selecteer **ha**.
- **Gebruikers naam en beheerders wachtwoord** voor de beheerder. Maak een nieuwe gebruiker die kan worden gebruikt om u aan te melden bij de computer.
- **Subnet-id**. Voer de ID in van het subnet dat u hebt gebruikt tijdens de implementatie van de ASCS/SCS-sjabloon of de ID van het subnet dat is gemaakt als onderdeel van de implementatie van de ASCS/SCS-sjabloon.

#### <a name="application-servers-template"></a><a name="application-servers-template"></a> Sjabloon toepassings servers

Met de sjabloon toepassings servers worden twee of meer virtuele machines geïmplementeerd die kunnen worden gebruikt als SAP-toepassings server instanties voor één SAP-systeem. Als u bijvoorbeeld een ASCS/SCS-sjabloon voor vijf SAP-systemen implementeert, moet u deze sjabloon vijf keer implementeren.

Als u de sjabloon toepassings servers multi-SID wilt instellen, voert u in de [sjabloon toepassings servers multi-sid][sap-templates-3-tier-multisid-apps-marketplace-image]waarden in voor de volgende para meters:

  -  **SAP-systeem-id**. Voer de SAP-systeem-ID in van het SAP-systeem dat u wilt installeren. De ID wordt gebruikt als voor voegsel voor de resources die worden geïmplementeerd.
  -  **Type besturings systeem**. Selecteer het besturings systeem van de virtuele machines.
  -  **SAP-systeem grootte**. Het aantal SAP'S dat het nieuwe systeem zal bieden. Als u niet zeker weet hoeveel SAP'S het systeem nodig heeft, vraagt u uw SAP-technologie partner of systeem integrator.
  -  **Systeem beschikbaarheid**. Selecteer **ha**.
  -  **Gebruikers naam en beheerders wachtwoord** voor de beheerder. Maak een nieuwe gebruiker die kan worden gebruikt om u aan te melden bij de computer.
  -  **Subnet-id**. Voer de ID in van het subnet dat u hebt gebruikt tijdens de implementatie van de ASCS/SCS-sjabloon of de ID van het subnet dat is gemaakt als onderdeel van de implementatie van de ASCS/SCS-sjabloon.


### <a name="azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Virtueel Azure-netwerk
In ons voor beeld is de adres ruimte van het virtuele netwerk van Azure 10.0.0.0/16. Er bevindt zich één subnet met de naam **subnet**, met het adres bereik 10.0.0.0/24. Alle virtuele machines en interne load balancers worden geïmplementeerd in dit virtuele netwerk.

> [!IMPORTANT]
> Breng geen wijzigingen aan in de netwerk instellingen binnen het gast besturingssysteem. Dit geldt ook voor IP-adressen, DNS-servers en subnet. Configureer al uw netwerk instellingen in Azure. De Dynamic Host Configuration Protocol (DHCP)-service geeft uw instellingen door.
>
>

### <a name="dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> IP-adressen van DNS

Voer de volgende stappen uit om de vereiste DNS-IP-adressen in te stellen.

1. Controleer in de Azure Portal op de Blade **DNS-servers** of de optie **DNS-servers** voor het virtuele netwerk is ingesteld op **aangepaste DNS**.
2. Selecteer uw instellingen op basis van het type netwerk dat u hebt. Zie de volgende resources voor meer informatie:
   * Voeg de IP-adressen van de on-premises DNS-servers toe.  
   U kunt on-premises DNS-servers uitbreiden naar de virtuele machines die worden uitgevoerd in Azure. In dat scenario kunt u de IP-adressen toevoegen van de virtuele machines van Azure waarop u de DNS-service uitvoert.
   * Voor implementaties die zijn geïsoleerd in Azure: Implementeer een extra virtuele machine in hetzelfde Virtual Network exemplaar dat fungeert als een DNS-server. Voeg de IP-adressen van de virtuele Azure-machines die u hebt ingesteld voor het uitvoeren van de DNS-service toe.

   ![Afbeelding 12: DNS-servers configureren voor Azure Virtual Network][sap-ha-guide-figure-3001]

   _**Afbeelding 12:** DNS-servers configureren voor Azure Virtual Network_

   > [!NOTE]
   > Als u de IP-adressen van de DNS-servers wijzigt, moet u de virtuele machines van Azure opnieuw opstarten om de wijziging toe te passen en de nieuwe DNS-servers door te geven.
   >
   >

In ons voor beeld is de DNS-service geïnstalleerd en geconfigureerd op deze virtuele Windows-machines:

| Virtuele-machine functie | Hostnaam van virtuele machine | Naam van netwerk kaart | Statisch IP-adres |
| --- | --- | --- | --- |
| Eerste DNS-server |domcontr-0 |PR1-NIC-domcontr-0 |10.0.0.10 |
| Tweede DNS-server |domcontr-1 |PR1-NIC-domcontr-1 |10.0.0.11 |

### <a name="host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Hostnamen en statische IP-adressen voor het geclusterde exemplaar van SAP ASCS/SCS geclusterde instantie en DBMS

Voor on-premises implementatie hebt u deze gereserveerde hostnamen en IP-adressen nodig:

| Rol van virtuele hostnaam | Naam van virtuele host | Virtueel statisch IP-adres |
| --- | --- | --- |
| SAP ASCS/SCS First virtual host name (voor cluster beheer) |PR1-ascs-vir |10.0.0.42 |
| Naam van de virtuele host van het SAP ASCS/SCS-exemplaar |PR1-ascs-SAP |10.0.0.43 |
| SAP DBMS tweede cluster naam virtuele host (Cluster beheer) |PR1-DBMS-vir |10.0.0.32 |

Wanneer u het cluster maakt, maakt u de namen van de virtuele hosts **PR1-ascs-vir** en **PR1-DBMS-vir** en de bijbehorende IP-adressen die het cluster zelf beheren. Zie [cluster knooppunten verzamelen in een cluster configuratie][sap-ha-guide-8.12.1]voor meer informatie over hoe u dit doet.

U kunt hand matig de andere twee namen van virtuele hosts maken, **PR1-ascs-SAP** en **PR1-DBMS-SAP**, en de bijbehorende IP-adressen op de DNS-server. Het geclusterde SAP ASCS/SCS-exemplaar en het geclusterde DBMS-exemplaar gebruiken deze bronnen. Zie [een virtuele-hostnaam maken voor een geclusterde SAP ASCS/SCS-instantie][sap-ha-guide-9.1.1]voor meer informatie over hoe u dit doet.

### <a name="set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Statische IP-adressen voor de virtuele machines van SAP instellen
Nadat u de virtuele machines hebt geïmplementeerd voor gebruik in uw cluster, moet u vaste IP-adressen instellen voor alle virtuele machines. Doe dit in de Azure Virtual Network-configuratie en niet in het gast besturingssysteem.

1. Selecteer in het Azure Portal   >    >    >  **IP-adres** van de resource groep netwerk kaart instellingen.
2. Selecteer op de Blade **IP-adressen** onder **toewijzing** de optie **statisch**. Geef in het vak **IP-adres** het IP-adres op dat u wilt gebruiken.

   > [!NOTE]
   > Als u het IP-adres van de netwerk kaart wijzigt, moet u de virtuele machines van Azure opnieuw opstarten om de wijziging toe te passen.  
   >
   >

   ![Afbeelding 13: Stel statische IP-adressen in voor de netwerk kaart van elke virtuele machine][sap-ha-guide-figure-3002]

   _**Afbeelding 13:** Statische IP-adressen voor de netwerk kaart van elke virtuele machine instellen_

   Herhaal deze stap voor alle netwerk interfaces, dat wil zeggen, voor alle virtuele machines, met inbegrip van virtuele machines die u wilt gebruiken voor uw Active Directory/DNS-service.

In ons voor beeld hebben we deze virtuele machines en vaste IP-adressen:

| Virtuele-machine functie | Hostnaam van virtuele machine | Naam van netwerk kaart | Statisch IP-adres |
| --- | --- | --- | --- |
| Eerste SAP-toepassings Server exemplaar |PR1-di-0 |PR1-NIC-di-0 |10.0.0.50 |
| Tweede SAP-toepassings Server exemplaar |PR1-di-1 |PR1-NIC-di-1 |10.0.0.51 |
| ... |... |... |... |
| Laatste SAP-toepassings Server exemplaar |PR1-di-5 |PR1-NIC-di-5 |10.0.0.55 |
| Eerste cluster knooppunt voor ASCS/SCS-instantie |PR1-ascs-0 |PR1-NIC-ascs-0 |10.0.0.40 |
| Tweede cluster knooppunt voor ASCS/SCS-instantie |PR1-ascs-1 |PR1-NIC-ascs-1 |10.0.0.41 |
| Eerste cluster knooppunt voor het DBMS-exemplaar |PR1-DB-0 |PR1-NIC-DB-0 |10.0.0.30 |
| Tweede cluster knooppunt voor DBMS-exemplaar |PR1-db-1 |PR1-NIC-db-1 |10.0.0.31 |

### <a name="set-a-static-ip-address-for-the-azure-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Stel een statisch IP-adres in voor de interne load balancer van Azure

Met de SAP-Azure Resource Manager sjabloon maakt u een interne Azure-load balancer die wordt gebruikt voor het SAP-ASCS/SCS-exemplaar cluster en het DBMS-cluster.

> [!IMPORTANT]
> Het IP-adres van de virtuele hostnaam van de SAP ASCS/SCS is hetzelfde als het IP-adres van de SAP ASCS/SCS Internal load balancer: **PR1-lb-ASCS**.
> Het IP-adres van de virtuele naam van het DBMS is hetzelfde als het IP-adres van de interne DBMS-load balancer: **PR1-lb-DBMS**.
>
>

Een statisch IP-adres voor de interne Azure-load balancer instellen:

1. De eerste implementatie stelt het interne load balancer IP-adres in op **dynamisch**. Selecteer in de Azure Portal op de Blade **IP-adressen** onder **toewijzing** de optie **statisch**.
2. Stel het IP-adres van de interne load balancer **PR1-lb-ascs** in op het IP-adres van de naam van de virtuele host van het SAP ASCS/SCS-exemplaar.
3. Stel het IP-adres van de interne load balancer **PR1-lb-DBMS** in op het IP-adres van de naam van de virtuele host van het DBMS-exemplaar.

   ![Afbeelding 14: vaste IP-adressen voor de interne load balancer instellen voor het SAP ASCS/SCS-exemplaar][sap-ha-guide-figure-3003]

   _**Afbeelding 14:** Statische IP-adressen voor de interne load balancer instellen voor het SAP ASCS/SCS-exemplaar_

In ons voor beeld hebben we twee interne load balancers van Azure met deze statische IP-adressen:

| Rol van interne Azure-load balancer | Naam van de interne load balancer van Azure | Statisch IP-adres |
| --- | --- | --- |
| Load balancer van SAP ASCS/SCS-instantie intern |PR1-lb-ascs |10.0.0.43 |
| SAP DBMS interne load balancer |PR1-lb-DBMS |10.0.0.33 |


### <a name="default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Standaard regels voor ASCS/SCS-taak verdeling voor de interne load balancer van Azure

De SAP-Azure Resource Manager sjabloon maakt de poorten die u nodig hebt:
* Een ABAP ASCS-exemplaar met het standaard instantie nummer **00**
* Een Java SCS-exemplaar met het standaard instantie nummer **01**

Wanneer u uw SAP ASCS/SCS-exemplaar installeert, moet u het standaard instantie nummer **00** voor uw ABAP ASCS-exemplaar en het standaard instantie nummer **01** voor uw Java SCS-exemplaar gebruiken.

Maak vervolgens de vereiste interne taakverdelings eindpunten voor de SAP NetWeaver-poorten.

Als u vereiste interne taakverdelings eindpunten wilt maken, moet u eerst deze taakverdelings eindpunten maken voor de SAP NetWeaver ABAP ASCS-poorten:

| Service-en taakverdelings regel naam | Standaard poort nummers | Concrete poorten voor (ASCS-exemplaar met exemplaar nummer 00) (ERS met 10) |
| --- | --- | --- |
| Server- *lbrule3200* in wachtrij plaatsen |32<*InstanceNumber*> |3200 |
| ABAP-bericht server/ *lbrule3600* |36<*InstanceNumber*> |3600 |
| Intern ABAP-bericht/ *lbrule3900* |39<*InstanceNumber*> |3900 |
| HTTP- *Lbrule8100* van bericht server |81<*InstanceNumber*> |8100 |
| ASCS HTTP/ *Lbrule50013* voor SAP-start service |5<*InstanceNumber*>13 |50013 |
| ASCS HTTPS/ *Lbrule50014* voor SAP-start service |5<*InstanceNumber*>14 |50014 |
| Replicatie- *Lbrule50016* in wachtrij plaatsen |5<*InstanceNumber*>16 |50016 |
| ERS HTTP- *Lbrule51013* voor SAP-start service |5<*InstanceNumber*>13 |51013 |
| ERS HTTP- *Lbrule51014* voor SAP-start service |5<*InstanceNumber*>14 |51014 |
| Win RM- *Lbrule5985* | |5985 |
| Bestands share *Lbrule445* | |445 |

_**Tabel 1:** Poort nummers van de SAP NetWeaver ABAP ASCS-instanties_

Vervolgens maakt u deze eind punten voor taak verdeling voor de SAP NetWeaver Java SCS-poorten:

| Service-en taakverdelings regel naam | Standaard poort nummers | Concrete poorten voor (SCS-exemplaar met exemplaar nummer 01) (ERS met 11) |
| --- | --- | --- |
| Server- *lbrule3201* in wachtrij plaatsen |32<*InstanceNumber*> |3201 |
| Gateway server- *lbrule3301* |33<*InstanceNumber*> |3301 |
| Java-bericht server- *lbrule3900* |39<*InstanceNumber*> |3901 |
| HTTP- *Lbrule8101* van bericht server |81<*InstanceNumber*> |8101 |
| SCS HTTP/ *Lbrule50113* voor SAP-start service |5<*InstanceNumber*>13 |50113 |
| SCS HTTPS/ *Lbrule50114* voor SAP-start service |5<*InstanceNumber*>14 |50114 |
| Replicatie- *Lbrule50116* in wachtrij plaatsen |5<*InstanceNumber*>16 |50116 |
| ERS HTTP- *Lbrule51113* voor SAP-start service |5<*InstanceNumber*>13 |51113 |
| ERS HTTP- *Lbrule51114* voor SAP-start service |5<*InstanceNumber*>14 |51114 |
| Win RM- *Lbrule5985* | |5985 |
| Bestands share *Lbrule445* | |445 |

_**Tabel 2:** Poort nummers van de SAP NetWeaver Java SCS-instanties_

![Afbeelding 15: standaard regels voor ASCS/SCS-taak verdeling voor de interne load balancer van Azure][sap-ha-guide-figure-3004]

_**Afbeelding 15:** Standaard regels voor ASCS/SCS-taak verdeling voor de interne load balancer van Azure_

Stel het IP-adres van de load balancer **PR1-lb-DBMS** in op het IP-adres van de naam van de virtuele host van het DBMS-exemplaar.

### <a name="change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Wijzig de ASCS/SCS-standaard regels voor taak verdeling voor de interne Azure-load balancer

Als u andere nummers wilt gebruiken voor de SAP-instanties ASCS of SCS, moet u de namen en waarden van de poorten van de standaard waarden wijzigen.

1. Selecteer in de Azure Portal **< *sid* ->-lb-ascs Load Balancer**  >  **taakverdelings regels**.
2. Wijzig deze waarden voor alle taakverdelings regels die horen bij het SAP ASCS-of SCS-exemplaar:

   * Name
   * Poort
   * Poort van back-end

   Als u bijvoorbeeld het standaard ASCS-instantie nummer wilt wijzigen van 00 in 31, moet u de wijzigingen aanbrengen voor alle poorten die worden vermeld in tabel 1.

   Hier volgt een voor beeld van een update voor poort *lbrule3200*.

   ![Afbeelding 16: Wijzig de ASCS/SCS-standaard regels voor taak verdeling voor de interne Azure-load balancer][sap-ha-guide-figure-3005]

   _**Afbeelding 16:** Wijzig de ASCS/SCS-standaard regels voor taak verdeling voor de interne Azure-load balancer_

### <a name="add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Virtuele Windows-machines toevoegen aan het domein

Nadat u een statisch IP-adres aan de virtuele machines hebt toegewezen, voegt u de virtuele machines toe aan het domein.

![Afbeelding 17: een virtuele machine toevoegen aan een domein][sap-ha-guide-figure-3006]

_**Afbeelding 17:** Een virtuele machine toevoegen aan een domein_

### <a name="add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Voeg register vermeldingen toe aan cluster knooppunten van het SAP ASCS/SCS-exemplaar

Azure Load Balancer heeft een interne load balancer die verbindingen sluit wanneer de verbindingen gedurende een bepaalde periode inactief zijn (een time-out voor inactiviteit). SAP-werk processen in dialoogvenster exemplaren openen verbindingen met het SAP-bewerkings proces zodra de eerste aanvraag voor het plaatsen/verwijderen van de wachtrij moet worden verzonden. Deze verbindingen blijven doorgaans tot stand worden gebracht tot het werk proces of het proces voor het in de wachtrij plaatsen opnieuw wordt gestart. Als de verbinding echter gedurende een bepaalde periode inactief is, worden de verbindingen met de interne Azure-load balancer gesloten. Dit is geen probleem omdat het SAP werk proces de verbinding met het bewerkings proces moet herstellen als het niet meer bestaat. Deze activiteiten zijn gedocumenteerd in de ontwikkel aars van SAP-processen, maar ze maken een grote hoeveelheid extra inhoud in deze traceringen. Het is een goed idee om het TCP/IP-adres `KeepAliveTime` en de `KeepAliveInterval` cluster knooppunten te wijzigen. Combi neer deze wijzigingen in de TCP/IP-para meters met SAP-profiel parameters, verderop in het artikel beschreven.

Als u Register vermeldingen wilt toevoegen aan cluster knooppunten van het SAP ASCS/SCS-exemplaar, voegt u eerst deze Windows-register vermeldingen toe op beide Windows-cluster knooppunten voor SAP ASCS/SCS:

| Pad | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Naam van de variabele |`KeepAliveTime` |
| Type variabele |REG_DWORD (decimaal) |
| Waarde |120000 |
| Koppeling naar documentatie |[https://technet.microsoft.com/library/cc957549.aspx](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)) |

_**Tabel 3:** De eerste TCP/IP-para meter wijzigen_

Voeg deze Windows-register vermeldingen vervolgens toe aan de Windows-cluster knooppunten voor SAP ASCS/SCS:

| Pad | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Naam van de variabele |`KeepAliveInterval` |
| Type variabele |REG_DWORD (decimaal) |
| Waarde |120000 |
| Koppeling naar documentatie |[https://technet.microsoft.com/library/cc957548.aspx](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) |

_**Tabel 4:** De tweede TCP/IP-para meter wijzigen_

**Start beide cluster knooppunten opnieuw op om de wijzigingen toe te passen**.

### <a name="set-up-a-windows-server-failover-clustering-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Een Windows Server failover clustering-cluster instellen voor een SAP ASCS/SCS-exemplaar

Het instellen van een Windows Server failover clustering-cluster voor een SAP ASCS/SCS-instantie omvat de volgende taken:

- De cluster knooppunten in een cluster configuratie verzamelen
- Een cluster bestands share-Witness configureren

#### <a name="collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> De cluster knooppunten in een cluster configuratie verzamelen

1. Voeg in de wizard functies en onderdelen toevoegen Failover Clustering toe aan beide cluster knooppunten.
2. Stel het failovercluster in met behulp van Failoverclusterbeheer. Selecteer in Failoverclusterbeheer de optie **cluster maken** en voeg alleen de naam van het eerste cluster, knoop punt A, toe. Voeg het tweede knoop punt nog niet toe. u voegt het tweede knoop punt in een latere stap toe.

   ![Afbeelding 18: de naam van de server of de virtuele machine van het eerste cluster knooppunt toevoegen][sap-ha-guide-figure-3007]

   _**Afbeelding 18:** De naam van de server of de virtuele machine van het eerste cluster knooppunt toevoegen_

3. Voer de netwerk naam (naam van de virtuele host) van het cluster in.

   ![Afbeelding 19: Voer de naam van het cluster in][sap-ha-guide-figure-3008]

   _**Afbeelding 19:** Voer de naam van het cluster in_

4. Nadat u het cluster hebt gemaakt, voert u een cluster validatie test uit.

   ![Afbeelding 20: de cluster validatie controle uitvoeren][sap-ha-guide-figure-3009]

   _**Afbeelding 20:** De cluster validatie controle uitvoeren_

   U kunt op dit moment in het proces waarschuwingen over schijven negeren. U voegt later een bestands share-Witness en de SIOS gedeelde schijven toe. In deze fase hoeft u zich geen zorgen te maken over een quorum.

   ![Afbeelding 21: er is geen quorum schijf gevonden][sap-ha-guide-figure-3010]

   _**Afbeelding 21:** Er is geen quorum schijf gevonden_

   ![Afbeelding 22: voor de kern cluster bron is een nieuw IP-adres vereist][sap-ha-guide-figure-3011]

   _**Afbeelding 22:** Voor de kern cluster bron is een nieuw IP-adres vereist_

5. Wijzig het IP-adres van de kern Cluster service. Het cluster kan pas worden gestart als u het IP-adres van de kern Cluster service wijzigt, omdat het IP-adres van de server verwijst naar een van de knoop punten van de virtuele machine. Doe dit op de pagina **Eigenschappen** van de IP-bron van de kern Cluster service.

   We moeten bijvoorbeeld een IP-adres (in ons voor beeld **10.0.0.42**) toewijzen voor de virtuele cluster naam **PR1-ascs-vir**.

   ![Afbeelding 23: Wijzig in het dialoog venster Eigenschappen het IP-adres][sap-ha-guide-figure-3012]

   _**Afbeelding 23:** Wijzig in het dialoog venster **Eigenschappen** het IP-adres_

   ![Afbeelding 24: wijs het IP-adres toe dat is gereserveerd voor het cluster][sap-ha-guide-figure-3013]

   _**Afbeelding 24:** Wijs het IP-adres toe dat is gereserveerd voor het cluster_

6. Haal de naam van de virtuele cluster-host online.

   ![Afbeelding 25: de cluster kern service is actief en met het juiste IP-adres][sap-ha-guide-figure-3014]

   _**Afbeelding 25:** De cluster kern service is actief en met het juiste IP-adres_

7. Voeg het tweede cluster knooppunt toe.

   Nu de kern Cluster service actief is, kunt u het tweede cluster knooppunt toevoegen.

   ![Afbeelding 26: het tweede cluster knooppunt toevoegen][sap-ha-guide-figure-3015]

   _**Afbeelding 26:** Het tweede cluster knooppunt toevoegen_

8. Voer een naam in voor de tweede host van het cluster knooppunt.

   ![Afbeelding 27: Voer de tweede hostnaam van het cluster knooppunt in][sap-ha-guide-figure-3016]

   _**Afbeelding 27:** Voer de tweede hostnaam van het cluster knooppunt in_

   > [!IMPORTANT]
   > Zorg ervoor dat het selectie vakje **Voeg alle in aanmerking komende opslag aan het cluster toe** is **niet** is ingeschakeld.  
   >
   >

   ![Afbeelding 28: Schakel het selectie vakje niet in][sap-ha-guide-figure-3017]

   _**Afbeelding 28:** Schakel het selectie vakje **niet** in_

   U kunt waarschuwingen over quorum en schijven negeren. U moet het quorum instellen en de schijf later delen, zoals wordt beschreven in de [installatie van Sios data keeper cluster Edition voor SAP ASCS/SCS cluster share Disk][sap-ha-guide-8.12.3].

   ![Afbeelding 29: waarschuwingen over het schijf quorum negeren][sap-ha-guide-figure-3018]

   _**Afbeelding 29:** Waarschuwingen over het schijf quorum negeren_


#### <a name="configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Een cluster bestands share-Witness configureren

Het configureren van een cluster bestands share-Witness omvat de volgende taken:

- Een bestands share maken
- Het quorum van de bestands share-Witness instellen in Failoverclusterbeheer

##### <a name="create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Een bestands share maken

1. Selecteer een bestands share-Witness in plaats van een quorum schijf. SIOS data keeper ondersteunt deze optie.

   In de voor beelden in dit artikel bevindt de bestandssharewitness zich op de Active Directory/DNS-server die wordt uitgevoerd in Azure. De bestandssharewitness van de bestands share heet **domcontr-0**. Omdat u een VPN-verbinding met Azure hebt geconfigureerd (via site-naar-site VPN of Azure ExpressRoute), is uw Active Directory/DNS-service on-premises en is niet geschikt voor het uitvoeren van een bestandssharewitness.

   > [!NOTE]
   > Als uw Active Directory/DNS-service alleen on-premises wordt uitgevoerd, configureert u de bestandssharewitness niet op het Windows-besturings systeem Active Directory/DNS dat on-premises wordt uitgevoerd. Netwerk latentie tussen cluster knooppunten die worden uitgevoerd in Azure en on-premises Active Directory/DNS zijn mogelijk te groot en veroorzaken verbindings problemen. Zorg ervoor dat u de bestands share-Witness hebt geconfigureerd op een virtuele machine van Azure die dicht bij het cluster knooppunt wordt uitgevoerd.  
   >
   >

   Het quorum station heeft ten minste 1.024 MB beschik bare ruimte nodig. U kunt 2.048 MB beschik bare ruimte voor het quorum station aanraden.

2. Voeg het cluster naam object toe.

   ![Afbeelding 30: de machtigingen voor de share voor het cluster naam object toewijzen][sap-ha-guide-figure-3019]

   _**Afbeelding 30:** Wijs de machtigingen voor de share voor het cluster naam object toe_

   Zorg ervoor dat de machtigingen de bevoegdheid voor het wijzigen van gegevens in de share voor het cluster naam object bevatten (in het voor beeld **PR1-ascs-vir $**).

3. Selecteer **toevoegen** om het cluster naam object toe te voegen aan de lijst. Wijzig het filter om op computer objecten te controleren, naast de weer gegeven in afbeelding 31.

   ![Afbeelding 31: de object typen wijzigen zodat computers worden inge sloten][sap-ha-guide-figure-3020]

   _**Afbeelding 31:** De object typen voor het toevoegen van computers wijzigen_

   ![Afbeelding 32: Schakel het selectie vakje computers in][sap-ha-guide-figure-3021]

   _**Afbeelding 32:** Selectie vakje **computers** selecteren_

4. Voer het object cluster naam in zoals weer gegeven in afbeelding 31. Omdat de record al is gemaakt, kunt u de machtigingen wijzigen, zoals wordt weer gegeven in afbeelding 30.

5. Selecteer het tabblad **beveiliging** van de share en stel vervolgens gedetailleerde machtigingen in voor het cluster naam object.

   ![Afbeelding 33: Stel de beveiligings kenmerken in voor het cluster naam object op het bestands share quorum][sap-ha-guide-figure-3022]

   _**Afbeelding 33:** De beveiligings kenmerken voor het cluster naam object op het bestands share quorum instellen_

##### <a name="set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Het bestands share-Witness-quorum instellen in Failoverclusterbeheer

1. Open de wizard quorum instelling configureren.

   ![Afbeelding 34: de wizard cluster quorum instelling configureren starten][sap-ha-guide-figure-3023]

   _**Afbeelding 34:** De wizard cluster quorum instelling configureren starten_

2. Selecteer op de pagina **quorum configuratie selecteren** **de optie de quorumwitness**.

   ![Afbeelding 35: quorum configuraties waaruit u kunt kiezen][sap-ha-guide-figure-3024]

   _**Afbeelding 35:** Quorum configuraties waaruit u kunt kiezen_

3. Selecteer op de pagina **Quorumwitness selecteren** de optie **een bestandssharewitness configureren**.

   ![Afbeelding 36: de bestands share-Witness selecteren][sap-ha-guide-figure-3025]

   _**Afbeelding 36:** De bestandssharewitness selecteren_

4. Voer het UNC-pad naar de bestands share in (in het voor beeld \\ domcontr-0\FSW). Selecteer **volgende** om een lijst weer te geven met de wijzigingen die u kunt aanbrengen.

   ![Afbeelding 37: de locatie van de bestands share voor de witness-share definiëren][sap-ha-guide-figure-3026]

   _**Afbeelding 37:** Definieer de locatie van de bestands share voor de witness-share_

5. Selecteer de gewenste wijzigingen en selecteer vervolgens **volgende**. U moet de cluster configuratie opnieuw configureren, zoals wordt weer gegeven in afbeelding 38.  

   ![Afbeelding 38: bevestiging dat u het cluster opnieuw hebt geconfigureerd][sap-ha-guide-figure-3027]

   _**Afbeelding 38:** Bevestiging dat u het cluster opnieuw hebt geconfigureerd_

Nadat het Windows-failovercluster is geïnstalleerd, moeten de wijzigingen worden aangebracht in een aantal drempel waarden om de detectie van failover aan de voor waarden in azure aan te passen. De para meters die moeten worden gewijzigd, worden beschreven in deze blog: [https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) . Ervan uitgaande dat uw twee virtuele machines die de configuratie van het Windows-cluster voor ASCS/SCS maken, zich in hetzelfde SubNet bevinden, moeten de volgende para meters worden gewijzigd in deze waarden:  
- SameSubNetDelay = 2000  
- SameSubNetThreshold = 15  
- RoutingHistoryLength = 30  

Deze instellingen zijn getest met klanten en hebben een goede inbreuk op de ene kant, waardoor ze nog flexibel genoeg zijn. Op de andere kant waren deze instellingen snel genoeg failover in echte fout situaties op SAP-software of node/VM-fout. 

### <a name="install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> De data keeper-cluster versie van SIOS installeren voor de SAP ASCS/SCS-cluster share schijf

U hebt nu een werkende Windows Server failover clustering-configuratie in Azure. Maar als u een SAP ASCS/SCS-exemplaar wilt installeren, hebt u een gedeelde schijf resource nodig. U kunt de gedeelde schijf bronnen die u nodig hebt, niet maken in Azure. SIOS data keeper cluster Edition is een oplossing van derden die u kunt gebruiken om gedeelde schijf bronnen te maken.

U moet de volgende taken uitvoeren om de data keeper-cluster versie van SIOS te installeren voor de SAP ASCS/SCS-cluster share disk:

- De .NET Framework 3,5 toevoegen
- Data keeper SIOS installeren
- SIOS data keeper instellen

#### <a name="add-the-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> De .NET Framework 3,5 toevoegen
Het Microsoft .NET Framework 3,5 wordt niet automatisch geactiveerd of geïnstalleerd op Windows Server 2012 R2. Omdat voor SIOS data keeper de .NET Framework nodig is voor alle knoop punten waarop u data keeper installeert, moet u de .NET Framework 3,5 installeren op het gast besturingssysteem van alle virtuele machines in het cluster.

Er zijn twee manieren om de .NET Framework 3,5 toe te voegen:

- Gebruik de wizard functies en onderdelen toevoegen in Windows, zoals weer gegeven in afbeelding 39.

  ![Afbeelding 39: de .NET Framework 3,5 installeren met behulp van de wizard functies en onderdelen toevoegen][sap-ha-guide-figure-3028]

  _**Afbeelding 39:** De .NET Framework 3,5 installeren met behulp van de wizard functies en onderdelen toevoegen_

  ![Afbeelding 40: voortgangs balk voor de installatie wanneer u de .NET Framework 3,5 installeert met behulp van de wizard functies en onderdelen toevoegen][sap-ha-guide-figure-3029]

  _**Afbeelding 40:** Voortgangs balk voor installatie wanneer u de .NET Framework 3,5 installeert met behulp van de wizard functies en onderdelen toevoegen_

- Gebruik het opdracht regel programma dism.exe. Voor dit type installatie moet u toegang hebben tot de map SxS op het Windows-installatie medium. Typ het volgende bij een opdracht prompt met verhoogde bevoegdheid:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a> SIOS data keeper installeren

Installeer de data keeper-cluster versie van SIOS op elk knoop punt in het cluster. Als u virtuele gedeelde opslag met SIOS data keeper wilt maken, maakt u een gesynchroniseerde mirror en simuleert u vervolgens gedeelde cluster opslag.

Voordat u de SIOS-software installeert, maakt u de domein gebruiker **DataKeeperSvc**.

> [!NOTE]
> Voeg de **DataKeeperSvc** -gebruiker toe aan de **lokale groep Administrators** op beide cluster knooppunten.
>
>

U kunt SIOS data keeper als volgt installeren:

1. Installeer de SIOS-software op beide cluster knooppunten.

   ![SIOS-installatie programma][sap-ha-guide-figure-3030]

   ![Afbeelding 41: eerste pagina van de data keeper-installatie van SIOS][sap-ha-guide-figure-3031]

   _**Afbeelding 41:** Eerste pagina van de data keeper-installatie van SIOS_

2. Selecteer **Ja** in het dialoog venster dat wordt weer gegeven in afbeelding 42.

   ![Afbeelding 42: data keeper informeert dat een service wordt uitgeschakeld][sap-ha-guide-figure-3032]

   _**Afbeelding 42:** Data keeper informeert u dat een service wordt uitgeschakeld_

3. In het dialoog venster dat wordt weer gegeven in afbeelding 43, raden we u aan om een **domein-of Server account te** selecteren.

   ![Afbeelding 43: selectie van de gebruiker voor SIOS data keeper][sap-ha-guide-figure-3033]

   _**Afbeelding 43:** Gebruikers selectie voor SIOS data keeper_

4. Voer de gebruikers naam en het wacht woord voor het domein account in die u hebt gemaakt voor SIOS data keeper.

   ![Afbeelding 44: Voer de gebruikers naam en het wacht woord van het domein in voor de data keeper-installatie van SIOS][sap-ha-guide-figure-3034]

   _**Afbeelding 44:** Voer de gebruikers naam en het wacht woord van het domein in voor de data keeper-installatie van SIOS_

5. Installeer de licentie sleutel voor uw data keeper-exemplaar van SIOS, zoals wordt weer gegeven in afbeelding 45.

   ![Afbeelding 45: Voer uw data keeper-licentie sleutel voor SIOS in][sap-ha-guide-figure-3035]

   _**Afbeelding 45:** Voer uw data keeper-licentie sleutel voor SIOS in_

6. Start de virtuele machine opnieuw op wanneer dit wordt gevraagd.

#### <a name="set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> SIOS data keeper instellen

Nadat u SIOS data keeper op beide knoop punten hebt geïnstalleerd, moet u de configuratie starten. Het doel van de configuratie is het synchroon repliceren van gegevens tussen de extra Vhd's die zijn gekoppeld aan elke virtuele machine.

1. Start het hulp programma data keeper beheer en configuratie en selecteer vervolgens **verbinding maken met server**. (In afbeelding 46 wordt deze optie omcirkeld in het rood.)

   ![Afbeelding 46: data keeper-beheer en configuratie hulpprogramma voor SIOS][sap-ha-guide-figure-3036]

   _**Afbeelding 46:** Data keeper-beheer en configuratie hulpprogramma voor SIOS_

2. Voer de naam of het TCP/IP-adres in van het eerste knoop punt waarvan het beheer-en configuratie hulpprogramma verbinding moet maken en, in een tweede stap, het tweede knoop punt.

   ![Afbeelding 47: Voer de naam of het TCP/IP-adres in van het eerste knoop punt waarvan het beheer-en configuratie hulpprogramma verbinding moet maken, en in een tweede stap is het tweede knoop punt][sap-ha-guide-figure-3037]

   _**Afbeelding 47:** Voeg de naam of het TCP/IP-adres in van het eerste knoop punt waarmee het beheer-en configuratie hulpprogramma verbinding moet maken, en in een tweede stap het tweede knoop punt_

3. Maak de replicatie taak tussen de twee knoop punten.

   ![Afbeelding 48: een replicatie taak maken][sap-ha-guide-figure-3038]

   _**Afbeelding 48:** Een replicatie taak maken_

   Een wizard leidt u door het proces van het maken van een replicatie taak.
4. Definieer de naam, het TCP/IP-adres en het schijf volume van het bron knooppunt.

   ![Afbeelding 49: de naam van de replicatie taak definiëren][sap-ha-guide-figure-3039]

   _**Afbeelding 49:** De naam van de replicatie taak definiëren_

   ![Afbeelding 50: de basis gegevens definiëren voor het knoop punt, dat het huidige bron knooppunt moet zijn][sap-ha-guide-figure-3040]

   _**Afbeelding 50:** Definieer de basis gegevens voor het knoop punt. dit moet het huidige bron knooppunt zijn_

5. Definieer de naam, het TCP/IP-adres en het schijf volume van het doel knooppunt.

   ![Afbeelding 51: de basis gegevens voor het knoop punt definiëren. dit moet het huidige doel knooppunt zijn][sap-ha-guide-figure-3041]

   _**Afbeelding 51:** Definieer de basis gegevens voor het knoop punt. dit moet het huidige doel knooppunt zijn_

6. Definieer de compressie algoritmen. In ons voor beeld raden we u aan de replicatie stroom te comprimeren. Met name bij hersynchronisaties situaties vermindert de compressie van de replicatie stroom aanzienlijk minder hersynchronisaties tijd. Houd er rekening mee dat compressie gebruikmaakt van de CPU-en RAM-bronnen van een virtuele machine. Naarmate de compressie frequentie toeneemt, wordt het volume CPU-bronnen gebruikt. U kunt deze instelling ook later aanpassen.

7. Een andere instelling die u moet controleren, is of de replicatie asynchroon of synchroon plaatsvindt. *Wanneer u SAP ASCS/SCS-configuraties beveiligt, moet u synchrone replicatie gebruiken*.  

   ![Afbeelding 52: replicatie Details definiëren][sap-ha-guide-figure-3042]

   _**Afbeelding 52:** Replicatie Details definiëren_

8. Definiëren of het volume dat door de replicatie taak wordt gerepliceerd, moet worden weer gegeven als een cluster configuratie voor Windows Server failover clustering als een gedeelde schijf. Voor de SAP-ASCS/SCS-configuratie selecteert u **Ja** zodat het gerepliceerde volume door het Windows-cluster wordt gezien als een gedeelde schijf die als een cluster volume kan worden gebruikt.

   ![Afbeelding 53: Selecteer Ja om het gerepliceerde volume als cluster volume in te stellen][sap-ha-guide-figure-3043]

   _**Afbeelding 53:** Selecteer **Ja** om het gerepliceerde volume als cluster volume in te stellen_

   Nadat het volume is gemaakt, wordt in het data keeper-beheer en configuratie programma weer gegeven dat de replicatie taak actief is.

   ![Afbeelding 54: data keeper synchrone mirroring voor de SAP ASCS/SCS-share schijf is actief][sap-ha-guide-figure-3044]

   _**Afbeelding 54:** Data keeper synchrone mirroring voor de SAP ASCS/SCS-share schijf is actief_

   Failoverclusterbeheer wordt nu de schijf weer gegeven als een Data keeper-schijf, zoals wordt weer gegeven in afbeelding 55.

   ![Afbeelding 55: Failoverclusterbeheer toont de schijf die data keeper is gerepliceerd][sap-ha-guide-figure-3045]

   _**Afbeelding 55:** Failoverclusterbeheer toont de schijf die data keeper gerepliceerd_

## <a name="install-the-sap-netweaver-system"></a><a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> Het SAP NetWeaver-systeem installeren

De DBMS-instellingen worden niet beschreven, omdat de instellingen variëren afhankelijk van het DBMS-systeem dat u gebruikt. We gaan er echter van uit dat hoge Beschik baarheid met betrekking tot het DBMS is gericht op de functionaliteit van de verschillende DBMS-leveranciers ondersteuning voor Azure. Bijvoorbeeld altijd ingeschakeld of database spiegeling voor SQL Server, en Oracle Data Guard voor Oracle-data bases. In het scenario dat we in dit artikel gebruiken, hebben we niet meer beveiliging aan het DBMS toegevoegd.

Er zijn geen speciale overwegingen wanneer verschillende DBMS-services communiceren met dit soort geclusterde SAP ASCS/SCS-configuratie in Azure.

> [!NOTE]
> De installatie procedures van SAP NetWeaver ABAP-systemen, Java-systemen en ABAP en Java-systemen zijn bijna identiek. Het belangrijkste verschil is dat een SAP ABAP-systeem één ASCS-exemplaar heeft. Het SAP Java-systeem heeft één SCS-exemplaar. Het SAP ABAP + Java-systeem heeft een ASCS-exemplaar en een SCS-exemplaar dat wordt uitgevoerd in dezelfde micro soft failover cluster-groep. Eventuele verschillen in de installatie van elke SAP NetWeaver-installatie stack worden expliciet genoemd. U kunt ervan uitgaan dat alle andere delen hetzelfde zijn.  
>
>

### <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> SAP installeren met een ASCS/SCS-exemplaar met hoge Beschik baarheid

> [!IMPORTANT]
> Zorg ervoor dat u het wissel bestand niet op Data keeper mirrored volumes plaatst. Data keeper biedt geen ondersteuning voor gespiegelde volumes. U kunt uw wissel bestand op het tijdelijke station D van een virtuele machine van Azure laten staan. Dit is de standaard instelling. Als dit nog niet het geval is, verplaatst u het Windows-pagina bestand naar station D van uw virtuele Azure-machine.
>
>

Het installeren van SAP met een ASCS/SCS-exemplaar met hoge Beschik baarheid omvat de volgende taken:

- De naam van een virtuele host voor het geclusterde SAP ASCS/SCS-exemplaar maken
- Het SAP eerste cluster knooppunt installeren
- Het SAP-Profiel van het ASCS/SCS-exemplaar wijzigen
- Een test poort toevoegen
- De Windows Firewall-Test poort openen

#### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Een virtuele-hostnaam maken voor het geclusterde SAP ASCS/SCS-exemplaar

1. Maak een DNS-vermelding voor de naam van de virtuele host van het ASCS/SCS-exemplaar in Windows DNS-beheer.

   > [!IMPORTANT]
   > Het IP-adres dat u toewijst aan de naam van de virtuele host van het ASCS/SCS-exemplaar moet hetzelfde zijn als het IP-adres dat u hebt toegewezen aan Azure Load Balancer (**< *sid*>-lb-ASCS**).  
   >
   >

   Het IP-adres van de virtuele SAP-ASCS/SCS-hostnaam (**PR1-ASCS-SAP**) is hetzelfde als het IP-adres van Azure Load Balancer (**PR1-lb-ASCS**).

   ![Afbeelding 56: Definieer de DNS-vermelding voor de virtuele naam van het SAP ASCS/SCS-cluster en het TCP/IP-adres][sap-ha-guide-figure-3046]

   _**Afbeelding 56:** Definieer de DNS-vermelding voor de virtuele naam en het TCP/IP-adres van het SAP ASCS/SCS-cluster_

2. Selecteer **DNS-beheer** domein om het IP-adres te definiëren dat aan de naam van de virtuele host is toegewezen  >  .

   ![Afbeelding 57: nieuwe virtuele naam en TCP/IP-adres voor SAP ASCS/SCS-cluster configuratie][sap-ha-guide-figure-3047]

   _**Afbeelding 57:** Nieuwe virtuele naam en TCP/IP-adres voor de SAP-ASCS/SCS-cluster configuratie_

#### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Het SAP eerste cluster knooppunt installeren

1. Voer de eerste cluster knooppunt optie uit op cluster knooppunt A. Bijvoorbeeld op de host **PR1-ascs-0** .
2. Als u de standaard poorten voor de interne Azure-load balancer wilt gebruiken, selecteert u:

   * **ABAP systeem**: **ASCS** -exemplaar nummer **00**
   * **Java-systeem**: **SCS** -exemplaar nummer **01**
   * **ABAP + Java-systeem**: **ASCS** -exemplaar nummer **00** en **SCS** -exemplaar nummer **01**

   Als u andere exemplaar nummers dan 00 wilt gebruiken voor het ABAP ASCS-exemplaar en 01 voor het Java SCS-exemplaar, moet u eerst de standaard regels voor de taakverdelings regel van Azure load balancer wijzigen, zoals beschreven in [de regels voor de standaard taakverdelings regel ASCS/SCS voor de interne Azure-Load Balancer wijzigen][sap-ha-guide-8.9].

De volgende taken worden niet beschreven in de standaard-SAP-installatie documentatie.

> [!NOTE]
> In de SAP-installatie documentatie wordt beschreven hoe u het eerste ASCS/SCS-cluster knooppunt installeert.
>
>

#### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Het SAP-Profiel van het ASCS/SCS-exemplaar wijzigen

U moet een nieuwe profiel parameter toevoegen. De profiel parameter voor komt dat verbindingen tussen SAP-werk processen en de bewerkings server worden afgesloten wanneer ze te lang niet actief zijn. We hebben het probleem scenario vermeld in [Register vermeldingen toevoegen op cluster knooppunten van het SAP ASCS/SCS-exemplaar][sap-ha-guide-8.11]. In deze sectie hebben we ook twee wijzigingen in de para meters van een eenvoudige TCP/IP-verbinding geïntroduceerd. In een tweede stap moet u de server voor het plaatsen van een `keep_alive` signaal zo instellen dat de verbindingen de drempel waarde voor de inactieve periode van Azure internal Load Balancer niet bereiken.

Het SAP-Profiel van het ASCS/SCS-exemplaar wijzigen:

1. Voeg deze profiel parameter toe aan het SAP-exemplaar profiel voor ASCS/SCS als u gebruikmaakt van ENSA1:

   ```
   enque/encni/set_so_keepalive = true
   ```
   In ons voor beeld is het pad:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Bijvoorbeeld naar het SAP SCS-exemplaar profiel en het bijbehorende pad:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

   Voor zowel ENSA1 als ENSA2, moet u ervoor zorgen dat de `keepalive` OS-para meters zijn ingesteld zoals beschreven in SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736).  

2. Start de SAP ASCS/SCS-instantie opnieuw op om de wijzigingen toe te passen.

#### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Een test poort toevoegen

Gebruik de test functionaliteit van de interne load balancer om ervoor te zorgen dat de volledige cluster configuratie werkt met Azure Load Balancer. De interne load balancer van Azure distribueert doorgaans de inkomende werk belasting gelijkmatig tussen de deelnemende virtuele machines. Dit werkt echter niet in sommige cluster configuraties, omdat er slechts één exemplaar actief is. De andere instantie is passief en kan geen enkele werk belasting accepteren. Een test functionaliteit helpt wanneer de interne load balancer van Azure alleen werk toewijst aan een actief exemplaar. Met de test functionaliteit kan de interne load balancer detecteren welke exemplaren actief zijn en vervolgens alleen het exemplaar met de werk belasting richten.

Een test poort toevoegen:

1. Controleer de huidige **ProbePort** -instelling door de volgende Power shell-opdracht uit te voeren. Voer deze uit vanuit een van de virtuele machines in de cluster configuratie.

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

2. Definieer een test poort. Het standaard poort nummer voor de test is **0**. In ons voor beeld gebruiken we test poort **62000**.

   ![Afbeelding 58: de test poort van de cluster configuratie is standaard 0][sap-ha-guide-figure-3048]

   _**Afbeelding 58:** De standaard poort voor de cluster configuratie test is 0_

   Het poort nummer wordt gedefinieerd in SAP Azure Resource Manager-sjablonen. U kunt het poort nummer toewijzen in Power shell.

   Voer het volgende Power shell-script uit om een nieuwe ProbePort-waarde in te stellen voor de **SAP-<*sid*> IP-** cluster bron. Werk de Power shell-variabelen voor uw omgeving bij. Nadat het script is uitgevoerd, wordt u gevraagd om de SAP-cluster groep opnieuw op te starten om de wijzigingen te activeren.

   ```PowerShell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

   Clear-Host
   $SAPClusterRoleName = "SAP $SAPSID"
   $SAPIPresourceName = "SAP $SAPSID IP"
   $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
   $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
   $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
   $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
   $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
   $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
   $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

   $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

   Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

   Write-Host
   Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
   Write-Host
   Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
   Write-Host

   $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

   Write-Host

   $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

   if($ActivateChanges -eq "yes"){
   Write-Host
   Write-Host "Activating changes..." -ForegroundColor Cyan

   Write-Host
   write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
   Stop-ClusterResource -Name $SAPIPresourceName
   sleep 5

   Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
   Start-ClusterGroup -Name $SAPClusterRoleName

   Write-Host "New ProbePort parameter is active." -ForegroundColor Green
   Write-Host

   Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
   Write-Host
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
   }else
   {
   Write-Host "Changes are not activated."
   }
   ```

   Controleer of **ProbePort** is ingesteld op de nieuwe waarde nadat u de rol van **SAP- *sid* >** -cluster <online hebt gebracht.

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```

   ![Afbeelding 59: de cluster poort testen nadat u de nieuwe waarde hebt ingesteld][sap-ha-guide-figure-3049]

   _**Afbeelding 59:** De cluster poort testen nadat u de nieuwe waarde hebt ingesteld_

#### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Open de Windows Firewall-Test poort

U moet een Windows Firewall-Test poort openen op beide cluster knooppunten. Gebruik het volgende script om een Windows Firewall-Test poort te openen. Werk de Power shell-variabelen voor uw omgeving bij.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

De **ProbePort** is ingesteld op **62000**. U hebt nu toegang tot de bestands share **\\ \ascsha-clsap\sapmnt** vanaf andere hosts, zoals van **ascsha-dba's**.

### <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Het data base-exemplaar installeren

Volg de procedure die wordt beschreven in de SAP-installatie documentatie om het data base-exemplaar te installeren.

### <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Het tweede cluster knooppunt installeren

Volg de stappen in de SAP-installatie handleiding om het tweede cluster te installeren.

### <a name="change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Het start type van het SAP ERS Windows-service-exemplaar wijzigen

Wijzig het start type van de SAP ERS Windows-service in **automatisch (vertraagd starten)** op beide cluster knooppunten.

![Afbeelding 60: Wijzig het Service type voor het SAP ERS-exemplaar in delayed Automatic][sap-ha-guide-figure-3050]

_**Afbeelding 60:** Wijzig het Service type voor het SAP ERS-exemplaar in delayed Automatic_

### <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> De SAP-primaire toepassings server installeren

Installeer het exemplaar van de primaire toepassings server (PAS) <*SID*>-di-0 op de virtuele machine die u hebt ingesteld voor het hosten van de pas. Er zijn geen afhankelijkheden voor Azure-of Data keeper-instellingen.

### <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> De SAP-aanvullende toepassings server installeren

Installeer een extra SAP-toepassings server (AAS) op alle virtuele machines die u hebt aangewezen voor het hosten van een SAP-toepassings Server exemplaar. Bijvoorbeeld op <*sid*>-di-1 om *sid* te <>-di- &lt; n &gt; .

> [!NOTE]
> Hiermee voltooit u de installatie van een SAP NetWeaver-systeem met een hoge Beschik baarheid. Ga vervolgens verder met het testen van de failover.
>


## <a name="test-the-sap-ascsscs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> De SAP-ASCS/SCS-instantie-failover en SIOS-replicatie testen
Het is eenvoudig om een SAP-ASCS/SCS-exemplaar-failover en SIOS-schijf replicatie te testen en te bewaken met behulp van Failoverclusterbeheer en het hulp programma SIOS data keeper Management and Configuration.

### <a name="sap-ascsscs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> Het SAP ASCS/SCS-exemplaar wordt uitgevoerd op het cluster knooppunt A

De **SAP PR1** -cluster groep wordt uitgevoerd op cluster knooppunt A. Bijvoorbeeld op **PR1-ascs-0**. Wijs de gedeelde schijf stations, die deel uitmaakt van de **SAP PR1** -cluster groep en die het ASCS/SCS-exemplaar gebruikt, toe aan cluster knooppunt A.

![Afbeelding 61: Failoverclusterbeheer: de SAP <SID> cluster groep wordt uitgevoerd op het cluster knooppunt A][sap-ha-guide-figure-5000]

_**Afbeelding 61:** Failoverclusterbeheer: de SAP <- *SID*> cluster groep wordt uitgevoerd op het cluster knooppunt A_

In het hulp programma z-data keeper beheer en configuratie kunt u zien dat de gegevens van de gedeelde schijf synchroon worden gerepliceerd van de bron volume stations op cluster knooppunt A naar het doel volume station S op cluster knooppunt B. Het wordt bijvoorbeeld gerepliceerd van **PR1-ascs-0 [10.0.0.40]** naar **PR1-ascs-1 [10.0.0.41]**.

![Afbeelding 62: het lokale volume van cluster knooppunt A naar cluster knooppunt B repliceren in data keeper SIOS][sap-ha-guide-figure-5001]

_**Afbeelding 62:** Repliceer in SIOS data keeper het lokale volume van cluster knooppunt A naar cluster knooppunt B_

### <a name="failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Failover van knoop punt A naar knoop punt B

1. Kies een van deze opties om een failover van de SAP <- *sid* te initiëren> cluster groep van cluster knooppunt a naar cluster knooppunt B:
   - Failoverclusterbeheer gebruiken  
   - Failover cluster Power shell gebruiken

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Cluster knooppunt A opnieuw starten in het Windows-gast besturingssysteem (dit initieert een automatische failover van de SAP <*SID*> cluster groep van knoop punt A naar knoop punt B).  
3. Start het cluster knooppunt A opnieuw vanaf de Azure Portal (dit initieert een automatische failover van de SAP <*SID*> cluster groep van knoop punt A naar knoop punt B).  
4. Start cluster knooppunt A opnieuw met behulp van Azure PowerShell (Hiermee initieert u een automatische failover van de SAP <*SID*> cluster groep van knoop punt A naar knoop punt B).

   Na de failover wordt de SAP <- *SID*> cluster groep uitgevoerd op cluster knooppunt B. Het wordt bijvoorbeeld uitgevoerd op **PR1-ascs-1**.

   ![Afbeelding 63: in Failoverclusterbeheer wordt de SAP-<SID> cluster groep uitgevoerd op cluster knooppunt B][sap-ha-guide-figure-5002]

   _**Afbeelding 63**: in Failoverclusterbeheer wordt de SAP-<*sid*> cluster groep uitgevoerd op cluster knooppunt B_

   De gedeelde schijf is nu gekoppeld op cluster knooppunt B. SIOS data keeper is bezig met het repliceren van gegevens van het bron volume station S op cluster knooppunt B naar het doel volume station S op cluster knooppunt A. Het is bijvoorbeeld repliceren van **PR1-ascs-1 [10.0.0.41]** naar **PR1-ascs-0 [10.0.0.40]**.

   ![Afbeelding 64: SIOS data keeper repliceert het lokale volume van cluster knooppunt B naar cluster knooppunt A][sap-ha-guide-figure-5003]

   _**Afbeelding 64:** SIOS data keeper kopieert het lokale volume van cluster knooppunt B naar cluster knooppunt A_
