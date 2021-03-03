---
title: Azure Virtual Machines-implementatie voor SAP NetWeaver | Microsoft Docs
description: Meer informatie over het implementeren van SAP-software op virtuele Linux-machines in Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/16/2020
ms.author: sedusch
ms.openlocfilehash: fe98ef297c6bed5ef3d982ed09db361244f75216
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675693"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>Azure Virtual Machines-implementatie voor SAP NetWeaver

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
[2367194]:https://launchpad.support.sap.com/#/notes/2367194

[azure-cli]:../../../cli-install-nodejs.md
[azure-cli-2]:https://docs.microsoft.com/cli/azure/install-azure-cli
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Azure Virtual Machines DBMS-implementatie voor SAP)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Caching voor Vm's en Vhd's)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Software-RAID)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure Storage)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Structuur van een RDBMS-implementatie)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Hoge Beschik baarheid en herstel na nood geval met virtuele Azure-machines)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 en hoger)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 en eerdere versies)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Een SQL Server-installatie kopie van Azure Marketplace gebruiken)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Algemene SQL Server voor SAP on Azure samen vatting)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Details SQL Server RDBMS)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Opslag configuratie)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Back-ups maken en herstellen)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Prestatie overwegingen voor back-up en herstel)
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Daarenteg)
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

[deployment-guide]:deployment-guide.md (Azure Virtual Machines-implementatie voor SAP)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP-bronnen)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Een virtuele machine implementeren met behulp van een aangepaste installatie kopie)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Scenario 1: een virtuele machine implementeren vanuit Azure Marketplace voor SAP)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Scenario 2: een virtuele machine implementeren met een aangepaste installatie kopie voor SAP)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Scenario 3: een virtuele machine verplaatsen van on-premises met een niet-gegeneraliseerde Azure VHD met SAP)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Implementatie scenario's van Vm's voor SAP op Microsoft Azure)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Azure PowerShell-cmdlets implementeren)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (SAP-relevante Power shell-cmdlets downloaden en importeren)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Een VM toevoegen aan een on-premises domein-alleen Windows)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Spreek)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (De Azure VM-agent downloaden, installeren en inschakelen)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (De Azure-extensie voor SAP configureren)
[deployment-guide-configure-new-extension-ps]:deployment-guide.md#2ad55a0d-9937-4943-9dd2-69bc2b5d3de0 (De nieuwe Azure-extensie voor SAP configureren met Azure PowerShell)
[deployment-guide-configure-new-extension-cli]:deployment-guide.md#c8749c24-fada-42ad-b114-f9aae2dc37da (De nieuwe Azure-extensie voor SAP configureren met Azure CLI)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Gereedheids controle voor Azure-extensie voor SAP)
[deployment-guide-5.1-new]:deployment-guide.md#7bf24f59-7347-4c7a-b094-4693e4687ee5 (Gereedheids controle voor nieuwe Azure-extensie voor SAP)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Status controle voor de Azure-extensie voor SAP-configuratie)
[deployment-guide-5.2-new]:deployment-guide.md#464ac96d-7d3c-435d-a5ae-3faf3bfef4b3 (Status controle voor de nieuwe Azure-extensie voor SAP-configuratie)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Problemen met Azure-extensie voor SAP oplossen)
[deployment-guide-5.3-new]:deployment-guide.md#b7afb8ef-a64c-495d-bb37-2af96688c530 (Problemen met de nieuwe Azure-extensie voor SAP oplossen)
[deployment-guide-contact-support]:deployment-guide.md#3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2 (Problemen met Azure-extensie voor SAP oplossen-contact opnemen met ondersteuning)
[deployment-guide-run-the-script]:deployment-guide.md#0d2847ad-865d-4a4c-a405-f9b7baaa00c7 (Problemen met Azure-extensie voor SAP oplossen-het installatie script uitvoeren)
[deployment-guide-redeploy-after-sysprep]:deployment-guide.md#2cd61f22-187d-42ed-bb8c-def0c983d756 (Problemen met Azure-extensie voor SAP oplossen na Sysprep)
[deployment-guide-fix-internet-connection]:deployment-guide.md#e92bc57d-80d9-4a2b-a2f4-16713a22ad89 ( Problemen met de Azure-extensie voor SAP oplossen Internet verbinding)


[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (VM-extensie configureren)
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (De proxy configureren)
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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Controles en probleem oplossing)

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

[msdn-set-Azvmaemextension]:https://docs.microsoft.com/powershell/module/az.compute/set-azvmaemextension

[planning-guide]:planning-guide.md (Azure Virtual Machines planning en implementatie voor SAP)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Resources)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Hoge Beschik baarheid en herstel na nood gevallen voor SAP NetWeaver die op Azure wordt uitgevoerd Virtual Machines)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Hoge Beschik baarheid voor SAP-toepassings servers)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Automatisch starten voor SAP-instanties gebruiken)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Cloud-only-implementaties van virtuele machines in azure zonder afhankelijkheden van het on-premises klanten netwerk)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Cross-premises-implementatie van één of meerdere SAP-Vm's in azure volledig geïntegreerd met het on-premises netwerk)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure-regio's)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Fout domeinen)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Upgrade domeinen)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Azure-beschikbaarheids sets)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Concept van Microsoft Azure virtuele machines)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Een virtuele machine verplaatsen van on-premises naar Azure met een niet-gegeneraliseerde schijf)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Een VM implementeren met een klantspecifieke installatie kopie)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Voor bereiding voor het verplaatsen van een virtuele machine van on-premises naar Azure met een niet-gegeneraliseerde schijf)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Voor bereiding voor het implementeren van een VM met een klantspecifieke installatie kopie voor SAP)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Vm's voorbereiden met SAP voor Azure)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Verschil tussen een Azure-schijf en een Azure-installatie kopie)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Een VHD uploaden van on-premises naar Azure)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Schijven kopiëren tussen Azure Storage accounts)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (VM-en VHD-structuur voor SAP-implementaties)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Automatisch koppelen voor gekoppelde schijven instellen)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure-bewakings oplossing voor SAP)
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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure netwerken)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Opslag: Microsoft Azure Storage en gegevens schijven)

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]:https://support.sap.com/pam (SAP-product beschikbaarheids matrix)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-os-disk-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk-md%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-2-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image-md%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image-md%2Fazuredeploy.json
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
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Virtuele machines implementeren en beheren met behulp van Azure Resource Manager sjablonen en de Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Virtuele machines beheren met behulp van Azure Resource Manager en Power shell)
[virtual-machines-windows-agent-user-guide]:../../extensions/agent-windows.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-linux.md#command-line-options
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
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../windows/quick-create-powershell.md
[virtual-machines-sizes]:../../linux/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
[virtual-machines-upload-image-windows-resource-manager]:../../windows/upload-image.md
[virtual-machines-windows-tutorial]:../../windows/quick-create-portal.md
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
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md
[qs-configure-powershell-windows-vm]:../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md
[qs-configure-cli-windows-vm]:../../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md
[howto-assign-access-powershell]:../../../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md
[howto-assign-access-cli]:../../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Azure Virtual Machines is de oplossing voor organisaties die behoefte hebben aan reken-en opslag resources, op minimale tijd en zonder langdurige aankoop cycli. U kunt Azure Virtual Machines gebruiken om klassieke toepassingen te implementeren, zoals toepassingen op basis van SAP netweave, in Azure. Breid de betrouw baarheid en beschik baarheid van een toepassing uit zonder extra on-premises resources. Azure Virtual Machines ondersteunt cross-premises-connectiviteit, zodat u Azure Virtual Machines kunt integreren in de on-premises domeinen, persoonlijke Clouds en het SAP-systeem van uw organisatie.

In dit artikel behandelen we de stappen voor het implementeren van SAP-toepassingen op virtuele machines (Vm's) in azure, inclusief alternatieve implementatie opties en probleem oplossing. Dit artikel is gebaseerd op de informatie in [Azure virtual machines planning en implementatie voor SAP net-Weaver][planning-guide]. Het is ook een aanvulling op de SAP-installatie documentatie en SAP-notities, die de primaire bronnen zijn voor het installeren en implementeren van SAP-software.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Voor het instellen van een virtuele Azure-machine voor de SAP-software moet u meerdere stappen en resources uitvoeren. Voordat u begint, moet u ervoor zorgen dat u voldoet aan de vereisten voor het installeren van SAP-software op virtuele machines in Azure.

### <a name="local-computer"></a>Lokale computer

Als u virtuele Windows-of Linux-machines wilt beheren, kunt u een Power shell-script en de Azure Portal gebruiken. Voor beide hulpprogram ma's hebt u een lokale computer met Windows 7 of een nieuwere versie van Windows nodig. Als u alleen virtuele Linux-machines wilt beheren en u een Linux-computer voor deze taak wilt gebruiken, kunt u Azure CLI gebruiken.

### <a name="internet-connection"></a>Internet verbinding

Als u de hulpprogram ma's en scripts wilt downloaden en uitvoeren die vereist zijn voor de implementatie van SAP-software, moet u verbinding hebben met internet. De Azure-VM met de Azure-extensie voor SAP moet ook toegang hebben tot internet. Als de Azure-VM deel uitmaakt van een virtueel Azure-netwerk of een on-premises domein, moet u ervoor zorgen dat de relevante proxy-instellingen zijn ingesteld, zoals beschreven in [de proxy configureren][deployment-guide-configure-proxy].

### <a name="microsoft-azure-subscription"></a>Microsoft Azure-abonnement

U hebt een actief Azure-account nodig.

### <a name="topology-and-networking"></a>Topologie en netwerken

U moet de topologie en architectuur van de SAP-implementatie in azure definiëren:

* Te gebruiken Azure-opslag accounts
* Het virtuele netwerk waarop u het SAP-systeem wilt implementeren
* De resource groep waarvoor u het SAP-systeem wilt implementeren
* Azure-regio waar u het SAP-systeem wilt implementeren
* SAP-configuratie (twee lagen of drie lagen)
* VM-grootten en het aantal extra gegevens schijven dat moet worden gekoppeld aan de virtuele machines
* SAP-correctie en transport systeem configuratie (CTS)

Maak en configureer Azure Storage-accounts (indien nodig) of virtuele Azure-netwerken voordat u begint met het implementatie proces van SAP-software. Voor informatie over het maken en configureren van deze resources raadpleegt u [Azure virtual machines planning en implementatie voor SAP NetWeaver][planning-guide].

### <a name="sap-sizing"></a>SAP-grootte

Ken de volgende informatie toe voor SAP-grootte:

* Geprojecteerde SAP-werk belasting, bijvoorbeeld door gebruik te maken van het hulp programma voor snelle grootte van SAP, en het SAP-nummer voor de Application Performance Standard (SAP'S)
* Vereist CPU-resource-en geheugen verbruik van het SAP-systeem
* Vereiste invoer/uitvoer-bewerkingen (I/O) per seconde
* Vereiste netwerk bandbreedte voor de uiteindelijke communicatie tussen Vm's in azure
* De vereiste netwerk bandbreedte tussen on-premises assets en het door Azure geïmplementeerde SAP-systeem

### <a name="resource-groups"></a>Resourcegroepen

In Azure Resource Manager kunt u resource groepen gebruiken om alle toepassings resources in uw Azure-abonnement te beheren. Zie [Overzicht van Azure Resource Manager][resource-group-overview] voor meer informatie.

## <a name="resources"></a>Resources

### <a name="sap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>SAP-bronnen

Wanneer u uw SAP-software-implementatie instelt, hebt u de volgende SAP-resources nodig:

* SAP-opmerking [1928533], die:
  * Lijst met Azure-VM-grootten die worden ondersteund voor de implementatie van SAP-software
  * Informatie over belang rijke capaciteit voor Azure VM-grootten
  * Ondersteunde SAP-software en besturings systemen (OS) en database combinaties
  * Vereiste versie van de SAP-kernel voor Windows en Linux op Microsoft Azure

* SAP-opmerking [2015553] bevat vereisten voor SAP-ondersteuning voor SAP-software-implementaties in Azure.
* SAP Note [2178632] bevat gedetailleerde informatie over alle bewakings gegevens die zijn gerapporteerd voor SAP in Azure.
* SAP Note [1409604] heeft de vereiste SAP host agent-versie voor Windows in Azure.
* SAP Note [2191498] heeft de vereiste SAP host agent-versie voor Linux in Azure.
* SAP Note [2243692] bevat informatie over SAP-licentie verlening op Linux in Azure.
* SAP Note [1984787] bevat algemene informatie over SuSE Linux Enterprise Server 12.
* SAP Note [2002167] bevat algemene informatie over Red Hat Enterprise Linux 7. x.
* SAP Note [2069760] bevat algemene informatie over Oracle Linux 7. x.
* SAP Note [1999351] bevat extra informatie over probleem oplossing voor de Azure-extensie voor SAP.
* SAP Note [1597355] bevat algemene informatie over wissel ruimte voor Linux.
* [SAP on Azure SCN-pagina](https://wiki.scn.sap.com/wiki/x/Pia7Gg) heeft nieuws en een verzameling nuttige resources.
* Op de [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) zijn alle vereiste SAP-notities voor Linux geïnstalleerd.
* SAP-specifieke Power shell-cmdlets die deel uitmaken van [Azure PowerShell][azure-ps].
* SAP-specifieke Azure CLI-opdrachten die deel uitmaken van [Azure cli][azure-cli].

### <a name="windows-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Windows-bronnen

Deze micro soft-artikelen behandelen SAP-implementaties in Azure:

* [Azure Virtual Machines planning en implementatie voor SAP net-Weaver][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP NetWeaver (dit artikel)][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP net-Weaver][dbms-guide]

## <a name="deployment-scenarios-for-sap-software-on-azure-vms"></a><a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Implementatie scenario's voor SAP-software op virtuele Azure-machines

U hebt meerdere opties voor het implementeren van Vm's en gekoppelde schijven in Azure. Het is belang rijk om de verschillen tussen implementatie opties te begrijpen, omdat u mogelijk verschillende stappen kunt ondernemen om uw virtuele machines voor te bereiden voor implementatie op basis van het implementatie type dat u kiest.

### <a name="scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap"></a><a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Scenario 1: een virtuele machine implementeren vanuit Azure Marketplace voor SAP

U kunt een installatie kopie van micro soft of door derden op Azure Marketplace gebruiken om uw virtuele machine te implementeren. Marketplace biedt enkele standaard installatie kopieën van besturings systemen van Windows Server en verschillende Linux-distributies. U kunt ook een installatie kopie implementeren die Database Management System (DBMS) Sku's bevat, bijvoorbeeld Microsoft SQL Server. Voor meer informatie over het gebruik van installatie kopieën met DBMS-Sku's raadpleegt u [Azure virtual machines DBMS-implementatie voor SAP NetWeaver][dbms-guide].

Het volgende stroom diagram toont de SAP-specifieke volg orde van de stappen voor het implementeren van een VM vanuit Azure Marketplace:

![Stroom diagram van VM-implementatie voor SAP-systemen met behulp van een VM-installatie kopie van Azure Marketplace][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Een virtuele machine maken met behulp van de Azure Portal

De eenvoudigste manier om een nieuwe virtuele machine te maken met een installatie kopie van Azure Marketplace is met behulp van de Azure Portal.

1.  Ga naar <https://portal.azure.com/#create/hub>.  U kunt ook **+ Nieuw** in het menu Azure Portal selecteren.
1.  Selecteer **Compute** en selecteer vervolgens het type besturings systeem dat u wilt implementeren. Bijvoorbeeld Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7,2 (RHEL 7,2) of Oracle Linux 7,2. In de standaard lijst weergave worden niet alle ondersteunde besturings systemen weer gegeven. Selecteer **alles weer geven** voor een volledige lijst. Voor meer informatie over ondersteunde besturings systemen voor de implementatie van SAP-software raadpleegt u SAP Note [1928533].
1.  Bekijk de voor waarden op de volgende pagina.
1.  Selecteer **Resource Manager** in het vak **een implementatie model selecteren** .
1.  Selecteer **Maken**.

De wizard begeleidt u bij het instellen van de vereiste para meters voor het maken van de virtuele machine, naast alle vereiste resources, zoals netwerk interfaces en opslag accounts. Enkele van deze para meters zijn:

1. **Basisprincipes**:
   * **Naam**: de naam van de resource (de naam van de virtuele machine).
   * **VM-schijf type**: Selecteer het schijf type van de besturingssysteem schijf. Als u Premium Storage voor uw gegevens schijven wilt gebruiken, raden we u aan om ook Premium Storage te gebruiken voor de besturingssysteem schijf.
   * **Gebruikers naam en wacht woord** of **open bare SSH-sleutel**: Voer de gebruikers naam en het wacht woord in van de gebruiker die tijdens het inrichten is gemaakt. Voor een virtuele Linux-machine kunt u de open bare Secure shell-sleutel (SSH) invoeren die u gebruikt om u aan te melden bij de computer.
   * **Abonnement**: Selecteer het abonnement dat u wilt gebruiken om de nieuwe virtuele machine in te richten.
   * **Resource groep**: de naam van de resource groep voor de virtuele machine. U kunt de naam van een nieuwe resource groep of de naam van een resource groep opgeven die al bestaat.
   * **Locatie**: waar de nieuwe virtuele machine moet worden geïmplementeerd. Als u de virtuele machine wilt verbinden met uw on-premises netwerk, moet u ervoor zorgen dat u de locatie van het virtuele netwerk selecteert waarmee Azure verbinding maakt met uw on-premises netwerk. Zie voor meer informatie [Microsoft Azure netwerken][planning-guide-microsoft-azure-networking] in [Azure virtual machines planning en implementatie van SAP net-Weaver][planning-guide].
1. **Grootte**:

     Zie SAP Note [1928533]voor een lijst met ondersteunde VM-typen. Zorg ervoor dat u het juiste VM-type selecteert als u Azure Premium Storage wilt gebruiken. Niet alle VM-typen ondersteunen Premium Storage. Zie voor meer informatie [opslag: Microsoft Azure Storage en gegevens schijven][planning-guide-storage-microsoft-azure-storage-and-data-disks] en [Azure Storage voor SAP-workloads](./planning-guide-storage.md) in [Azure virtual machines planning en implementatie van SAP NetWeaver][planning-guide].

1. **Instellingen**:
   * **Storage**
     * **Schijf type**: Selecteer het schijf type van de besturingssysteem schijf. Als u Premium Storage voor uw gegevens schijven wilt gebruiken, raden we u aan om ook Premium Storage te gebruiken voor de besturingssysteem schijf.
     * **Beheerde schijven gebruiken**: als u Managed disks wilt gebruiken, selecteert u Ja. Zie hoofd stuk [Managed disks](./planning-guide-storage.md#microsoft-azure-storage-resiliency) in de plannings handleiding voor meer informatie over Managed disks.
     * **Opslag account**: Selecteer een bestaand opslag account of maak een nieuwe. Niet alle opslag typen werken voor het uitvoeren van SAP-toepassingen. Zie [opslag structuur van een virtuele machine voor RDBMS-implementaties](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)voor meer informatie over opslag typen.
   * **Netwerk**
     * **Virtueel netwerk** en **subnet**: als u de virtuele machine wilt integreren met uw intranet, selecteert u het virtuele netwerk dat is verbonden met uw on-premises netwerk.
     * **Openbaar IP-adres**: Selecteer het open bare IP-adres dat u wilt gebruiken of voer para meters in om een nieuw openbaar IP-adres te maken. U kunt een openbaar IP-adres gebruiken om toegang te krijgen tot uw virtuele machine via internet. Zorg ervoor dat u ook een netwerk beveiligings groep maakt om de toegang tot uw virtuele machine te beveiligen.
     * **Netwerk beveiligings groep**: Zie [netwerk verkeer beheren met netwerk beveiligings groepen][virtual-networks-nsg]voor meer informatie.
   * **Extensies**: u kunt virtuele-machine uitbreidingen installeren door ze toe te voegen aan de implementatie. U hoeft geen uitbrei dingen toe te voegen in deze stap. De uitbrei dingen die zijn vereist voor SAP-ondersteuning, worden later geïnstalleerd. Zie hoofd stuk [de Azure-extensie voor SAP configureren][deployment-guide-4.5] in deze hand leiding.
   * **Hoge Beschik baarheid**: Selecteer een beschikbaarheidsset of voer de para meters in om een nieuwe beschikbaarheidsset te maken. Zie [Azure-beschikbaarheids sets][planning-guide-3.2.3]voor meer informatie.
   * **Controle**
     * **Diagnostische gegevens over opstarten**: u kunt **uitschakelen** selecteren voor diagnostische gegevens over opstarten.
     * **Diagnostische gegevens van het gast besturingssysteem**: u kunt **uitschakelen** selecteren voor diagnostische gegevens over bewaking.

1. **Samen vatting**:

   Controleer uw selecties en selecteer vervolgens **OK**.

Uw virtuele machine wordt geïmplementeerd in de resource groep die u hebt geselecteerd.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Een virtuele machine maken met behulp van een sjabloon

U kunt een virtuele machine maken met behulp van een van de SAP-sjablonen die zijn gepubliceerd in de [github-opslag plaats Azure-Quick Start-sjablonen][azure-quickstart-templates-github]. U kunt ook hand matig een virtuele machine maken met behulp van de [Azure Portal][virtual-machines-windows-tutorial], [Power shell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]of [Azure cli][virtual-machines-linux-tutorial].

* [**Configuratie met twee lagen (slechts één virtuele-machine) sjabloon** (SAP-2-tier-Marketplace-image)][sap-templates-2-tier-marketplace-image]

  Gebruik deze sjabloon om een systeem met twee lagen te maken met slechts één virtuele machine.
* [**Configuratie met twee lagen (slechts één virtuele-machine) sjabloon-Managed disks** (SAP-2-tier-Marketplace-image-MD)][sap-templates-2-tier-marketplace-image-md]

  Gebruik deze sjabloon om een systeem met twee lagen te maken met slechts één virtuele machine en Managed Disks.
* [**Configuratie met drie lagen (meerdere virtuele machines)** (SAP-3-laag-Marketplace-afbeelding)][sap-templates-3-tier-marketplace-image]

  Gebruik deze sjabloon om een systeem met drie lagen te maken met behulp van meerdere virtuele machines.
* [**Configuratie met drie lagen (meerdere virtuele machines)-Managed disks** (SAP-3-tier-Marketplace-afbeelding-MD)][sap-templates-3-tier-marketplace-image-md]

  Gebruik deze sjabloon om een systeem met drie lagen te maken met meerdere virtuele machines en Managed Disks.

Voer in het Azure Portal de volgende para meters voor de sjabloon in:

1. **Basisprincipes**:
   * **Abonnement**: het abonnement dat moet worden gebruikt voor het implementeren van de sjabloon.
   * **Resource groep**: de resource groep die moet worden gebruikt voor het implementeren van de sjabloon. U kunt een nieuwe resource groep maken, maar u kunt ook een bestaande resource groep in het abonnement selecteren.
   * **Locatie**: waar de sjabloon wordt geïmplementeerd. Als u een bestaande resource groep hebt geselecteerd, wordt de locatie van die resource groep gebruikt.

1. **Instellingen**:
   * **SAP-systeem-id**: de SAP-systeem-id (sid).
   * **Bs-type**: het besturings systeem dat u wilt implementeren, bijvoorbeeld Windows Server 2012 R2, SuSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7,2 (RHEL 7,2) of Oracle Linux 7,2.

     In de lijst weergave worden niet alle ondersteunde besturings systemen weer gegeven. Voor meer informatie over ondersteunde besturings systemen voor de implementatie van SAP-software raadpleegt u SAP Note [1928533].
   * **SAP-systeem grootte**: de grootte van het SAP-systeem.

     Het aantal SAP'S dat het nieuwe systeem biedt. Als u niet zeker weet hoeveel SAP'S het systeem nodig heeft, vraagt u uw SAP-technologie partner of systeem integrator.
   * **Systeem beschikbaarheid** (alleen sjabloon met drie lagen): de beschik baarheid van het systeem.

     Selecteer **ha** voor een configuratie die geschikt is voor een installatie met een hoge Beschik baarheid. Er worden twee database servers en twee servers voor ABAP SAP Central Services (ASCS) gemaakt.
   * **Opslag type** (alleen sjabloon met twee lagen): het type opslag dat moet worden gebruikt.

     Voor grotere systemen raden we u aan Azure Premium Storage te gebruiken. Zie de volgende bronnen voor meer informatie over opslag typen:
      * [Gebruik van Azure Premium-SSD Storage voor SAP DBMS-exemplaar][2367194]
      * [Opslag structuur van een virtuele machine voor RDBMS-implementaties](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: opslag met hoge prestaties voor werk belastingen van virtuele Azure-machines][storage-premium-storage-preview-portal]
      * [Inleiding tot Microsoft Azure Storage][storage-introduction]
   * **Gebruikers naam** en **beheerders wachtwoord** voor de beheerder: een gebruikers naam en wacht woord.
     Er wordt een nieuwe gebruiker gemaakt om u aan te melden bij de virtuele machine.
   * **Nieuw of bestaand subnet**: Hiermee wordt bepaald of er een nieuw virtueel netwerk en subnet wordt gemaakt of een bestaand subnet wordt gebruikt. Als u al een virtueel netwerk hebt dat is verbonden met uw on-premises netwerk, selecteert u **bestaande**.
   * **Subnet-id**: als u de virtuele machine wilt implementeren in een bestaand VNet waarvoor u een subnet hebt gedefinieerd, moet de virtuele machine worden toegewezen aan, de id van het specifieke subnet benoemen. De ID ziet er ongeveer als volgt uit:/Subscriptions/ &lt; -abonnements-id>/resourceGroups/naam van de &lt; resource groep>/providers/Microsoft.Network/virtualNetworks/naam van het &lt; virtuele netwerk>/subnets/- &lt; subnet naam>

1. Voor **waarden**:  
    Bekijk en accepteer de juridische voor waarden.

1. Selecteer **Aankoop**.

De Azure VM-agent wordt standaard geïmplementeerd wanneer u een installatie kopie gebruikt vanuit Azure Marketplace.

#### <a name="configure-proxy-settings"></a>Proxyinstellingen configureren

Afhankelijk van de configuratie van uw on-premises netwerk moet u mogelijk de proxy op uw virtuele machine instellen. Als uw virtuele machine is verbonden met uw on-premises netwerk via VPN of ExpressRoute, is de virtuele machine mogelijk niet in staat om toegang te krijgen tot internet en kunt u de vereiste VM-extensies niet downloaden of Azure-infrastructuur gegevens voor de SAP host agent verzamelen via de SAP-extensie voor Azure. Zie [Configure the proxy][deployment-guide-configure-proxy](Engelstalig) voor meer informatie.

#### <a name="join-a-domain-windows-only"></a>Lid worden van een domein (alleen Windows)

Als uw Azure-implementatie is verbonden met een on-premises Active Directory of een DNS-exemplaar via een Azure site-naar-site-VPN-verbinding of ExpressRoute (dit wordt *Cross-premises* genoemd in [Azure virtual machines planning en implementatie voor SAP NetWeaver][planning-guide]), wordt verwacht dat de virtuele machine wordt toegevoegd aan een on-premises domein. Zie [een virtuele machine toevoegen aan een on-premises domein (alleen Windows)][deployment-guide-4.3]voor meer informatie over overwegingen voor deze taak.

#### <a name="configure-vm-extension"></a><a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>VM-extensie configureren

Om ervoor te zorgen dat SAP uw omgeving ondersteunt, stelt u de Azure-extensie voor SAP in zoals beschreven in [de Azure-extensie voor SAP configureren][deployment-guide-4.5]. Controleer de vereisten voor SAP en de vereiste minimum versie van de SAP-kernel en SAP Hosta Gent, in de resources die worden vermeld in [SAP-resources][deployment-guide-2.2].

#### <a name="vm-extension-for-sap-check"></a>VM-extensie voor SAP-controle

Controleer of de VM-extensie voor SAP werkt, zoals wordt beschreven in [controles en probleem oplossing][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Stappen na implementatie

Nadat u de virtuele machine hebt gemaakt en de virtuele machine is geïmplementeerd, moet u de vereiste software onderdelen installeren in de virtuele machine. Vanwege de implementatie-en software-installatie volgorde in dit type VM-implementatie moet de software die moet worden geïnstalleerd, al beschikbaar zijn in azure, op een andere virtuele machine of als een schijf die kan worden bijgevoegd. U kunt ook een cross-premises scenario gebruiken, waarin de verbinding met de lokale assets (installatie shares) wordt gegeven.

Nadat u uw virtuele machine in azure hebt geïmplementeerd, volgt u dezelfde richt lijnen en hulpprogram ma's om de SAP-software op uw virtuele machine te installeren, net zoals u zou doen in een on-premises omgeving. Als u SAP-software wilt installeren op een virtuele Azure-machine, wordt u aangeraden SAP en micro soft te uploaden en op te slaan in azure Vhd's of Managed Disks, of door een Azure-VM te maken die werkt als een bestands server met alle vereiste SAP-installatie media.

### <a name="scenario-2-deploying-a-vm-with-a-custom-image-for-sap"></a><a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Scenario 2: een virtuele machine implementeren met een aangepaste installatie kopie voor SAP

Omdat verschillende versies van een besturings systeem of DBMS andere patch vereisten hebben, zijn de installatie kopieën die u in de Azure Marketplace vindt mogelijk niet aan uw behoeften voldoet. U kunt in plaats daarvan een virtuele machine maken met behulp van uw eigen installatie kopie van het besturings systeem/DBMS-VM. deze moet u later opnieuw implementeren.
U kunt verschillende stappen gebruiken om een persoonlijke installatie kopie voor Linux te maken dan om er een te maken voor Windows.

---
> ![Windows-logo.][Logo_Windows] Windows
>
> Om een Windows-installatie kopie voor te bereiden die u kunt gebruiken om meerdere virtuele machines te implementeren, moeten de Windows-instellingen (zoals Windows SID en hostname) worden abstracted of gegeneraliseerd op de on-premises VM. U kunt [Sysprep](/previous-versions/windows/it-pro/windows-8.1-and-8/hh825084(v=win.10)) gebruiken om dit te doen.
>
> ![Linux-logo.][Logo_Linux] Linux
>
> Om een Linux-installatie kopie voor te bereiden die u kunt gebruiken om meerdere virtuele machines te implementeren, moeten bepaalde Linux-instellingen worden abstracted of gegeneraliseerd op de on-premises VM. U kunt `waagent -deprovision`  dit gebruiken om dit te doen. Zie [een virtuele Linux-machine vastleggen die wordt uitgevoerd op Azure][virtual-machines-linux-capture-image] en de [Gebruikers handleiding voor de Azure Linux-agent][virtual-machines-linux-agent-user-guide-command-line-options]voor meer informatie.
>
>

---
U kunt een aangepaste installatie kopie voorbereiden en maken en deze vervolgens gebruiken om meerdere nieuwe Vm's te maken. Dit wordt beschreven in [Azure virtual machines planning en implementatie voor SAP net-Weaver][planning-guide]. Stel uw database inhoud in met behulp van SAP software Provisioning Manager voor het installeren van een nieuw SAP-systeem (herstelt een database back-up van een schijf die is gekoppeld aan de virtuele machine) of door een back-up van een Data Base rechtstreeks te herstellen vanuit Azure Storage, als uw DBMS deze ondersteunt. Zie [Azure virtual machines DBMS-implementatie voor SAP net-Weaver][dbms-guide]voor meer informatie. Als u al een SAP-systeem op uw on-premises VM hebt geïnstalleerd (met name voor systemen met twee lagen), kunt u de SAP-systeem instellingen aanpassen na de implementatie van de virtuele Azure-machine met behulp van de procedure voor het wijzigen van het systeem dat wordt ondersteund door SAP software Provisioning Manager (SAP-notitie [1619720]). Als dat niet het geval is, kunt u de SAP-software installeren nadat u de virtuele Azure-machine hebt geïmplementeerd.

Het volgende stroom diagram toont de SAP-specifieke volg orde van de stappen voor het implementeren van een virtuele machine vanuit een aangepaste installatie kopie:

![Stroom diagram van VM-implementatie voor SAP-systemen met behulp van een VM-installatie kopie in particuliere Marketplace][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Een virtuele machine maken met behulp van de Azure Portal

De eenvoudigste manier om een nieuwe virtuele machine te maken op basis van een installatie kopie van een beheerde schijf is met behulp van de Azure Portal. Lees [een beheerde installatie kopie van een gegeneraliseerde vm in azure vastleggen](../../windows/capture-image-resource.md) voor meer informatie over het maken van een installatie kopie van een schijf.

1.  Ga naar <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages>. Of Selecteer in het menu Azure Portal de optie **installatie kopieën**.
1.  Selecteer de installatie kopie van de beheerde schijf die u wilt implementeren en klik op **VM maken**

De wizard begeleidt u bij het instellen van de vereiste para meters voor het maken van de virtuele machine, naast alle vereiste resources, zoals netwerk interfaces en opslag accounts. Enkele van deze para meters zijn:

1. **Basisprincipes**:
   * **Naam**: de naam van de resource (de naam van de virtuele machine).
   * **VM-schijf type**: Selecteer het schijf type van de besturingssysteem schijf. Als u Premium Storage voor uw gegevens schijven wilt gebruiken, raden we u aan om ook Premium Storage te gebruiken voor de besturingssysteem schijf.
   * **Gebruikers naam en wacht woord** of **open bare SSH-sleutel**: Voer de gebruikers naam en het wacht woord in van de gebruiker die tijdens het inrichten is gemaakt. Voor een virtuele Linux-machine kunt u de open bare Secure shell-sleutel (SSH) invoeren die u gebruikt om u aan te melden bij de computer.
   * **Abonnement**: Selecteer het abonnement dat u wilt gebruiken om de nieuwe virtuele machine in te richten.
   * **Resource groep**: de naam van de resource groep voor de virtuele machine. U kunt de naam van een nieuwe resource groep of de naam van een resource groep opgeven die al bestaat.
   * **Locatie**: waar de nieuwe virtuele machine moet worden geïmplementeerd. Als u de virtuele machine wilt verbinden met uw on-premises netwerk, moet u ervoor zorgen dat u de locatie van het virtuele netwerk selecteert waarmee Azure verbinding maakt met uw on-premises netwerk. Zie voor meer informatie [Microsoft Azure netwerken][planning-guide-microsoft-azure-networking] in [Azure virtual machines planning en implementatie van SAP net-Weaver][planning-guide].
1. **Grootte**:

     Zie SAP Note [1928533]voor een lijst met ondersteunde VM-typen. Zorg ervoor dat u het juiste VM-type selecteert als u Azure Premium Storage wilt gebruiken. Niet alle VM-typen ondersteunen Premium Storage. Zie voor meer informatie [opslag: Microsoft Azure Storage en gegevens schijven][planning-guide-storage-microsoft-azure-storage-and-data-disks] en [Azure Storage voor SAP-workloads](./planning-guide-storage.md) in [Azure virtual machines planning en implementatie van SAP NetWeaver][planning-guide].

1. **Instellingen**:
   * **Storage**
     * **Schijf type**: Selecteer het schijf type van de besturingssysteem schijf. Als u Premium Storage voor uw gegevens schijven wilt gebruiken, raden we u aan om ook Premium Storage te gebruiken voor de besturingssysteem schijf.
     * **Beheerde schijven gebruiken**: als u Managed disks wilt gebruiken, selecteert u Ja. Zie hoofd stuk [Managed disks](./planning-guide-storage.md#microsoft-azure-storage-resiliency) in de plannings handleiding voor meer informatie over Managed disks.
   * **Netwerk**
     * **Virtueel netwerk** en **subnet**: als u de virtuele machine wilt integreren met uw intranet, selecteert u het virtuele netwerk dat is verbonden met uw on-premises netwerk.
     * **Openbaar IP-adres**: Selecteer het open bare IP-adres dat u wilt gebruiken of voer para meters in om een nieuw openbaar IP-adres te maken. U kunt een openbaar IP-adres gebruiken om toegang te krijgen tot uw virtuele machine via internet. Zorg ervoor dat u ook een netwerk beveiligings groep maakt om de toegang tot uw virtuele machine te beveiligen.
     * **Netwerk beveiligings groep**: Zie [netwerk verkeer beheren met netwerk beveiligings groepen][virtual-networks-nsg]voor meer informatie.
   * **Extensies**: u kunt virtuele-machine uitbreidingen installeren door ze toe te voegen aan de implementatie. U hoeft in deze stap geen extensie toe te voegen. De uitbrei dingen die zijn vereist voor SAP-ondersteuning, worden later geïnstalleerd. Zie hoofd stuk [de Azure-extensie voor SAP configureren][deployment-guide-4.5] in deze hand leiding.
   * **Hoge Beschik baarheid**: Selecteer een beschikbaarheidsset of voer de para meters in om een nieuwe beschikbaarheidsset te maken. Zie [Azure-beschikbaarheids sets][planning-guide-3.2.3]voor meer informatie.
   * **Controle**
     * **Diagnostische gegevens over opstarten**: u kunt **uitschakelen** selecteren voor diagnostische gegevens over opstarten.
     * **Diagnostische gegevens van het gast besturingssysteem**: u kunt **uitschakelen** selecteren voor diagnostische gegevens over bewaking.

1. **Samen vatting**:

   Controleer uw selecties en selecteer vervolgens **OK**.

Uw virtuele machine wordt geïmplementeerd in de resource groep die u hebt geselecteerd.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Een virtuele machine maken met behulp van een sjabloon

Als u een implementatie wilt maken met behulp van een installatie kopie van een persoonlijk besturings systeem uit de Azure Portal, gebruikt u een van de volgende SAP-sjablonen. Deze sjablonen worden gepubliceerd in de [github-opslag plaats Azure-Quick Start-sjablonen][azure-quickstart-templates-github]. U kunt ook hand matig een virtuele machine maken met behulp van [Power shell][virtual-machines-upload-image-windows-resource-manager].

* [**Configuratie met twee lagen (slechts één virtuele-machine) sjabloon** (SAP-2-tier-User-image)][sap-templates-2-tier-user-image]

  Gebruik deze sjabloon om een systeem met twee lagen te maken met slechts één virtuele machine.
* [**Configuratie met twee lagen (slechts één virtuele-machine) sjabloon-beheerde schijf installatie kopie** (SAP-2-tier-User-image-MD)][sap-templates-2-tier-user-image-md]

  Gebruik deze sjabloon om een systeem met twee lagen te maken met slechts één virtuele machine en een beheerde schijf installatie kopie.
* [**Configuratie met drie lagen (meerdere virtuele machines)** (SAP-3-tier-User-image)][sap-templates-3-tier-user-image]

  Gebruik deze sjabloon om een systeem met drie lagen te maken met meerdere virtuele machines of uw eigen installatie kopie van het besturings systeem.
* [**Configuratie met drie lagen (meerdere virtuele machines) sjabloon-beheerde schijf installatie kopie** (SAP-3-tier-User-image-MD)][sap-templates-3-tier-user-image-md]

  Gebruik deze sjabloon om een systeem met drie lagen te maken met meerdere virtuele machines of uw eigen installatie kopie van het besturings systeem en een installatie kopie van een beheerde schijf.

Voer in het Azure Portal de volgende para meters voor de sjabloon in:

1. **Basisprincipes**:
   * **Abonnement**: het abonnement dat moet worden gebruikt voor het implementeren van de sjabloon.
   * **Resource groep**: de resource groep die moet worden gebruikt voor het implementeren van de sjabloon. U kunt een nieuwe resource groep maken of een bestaande resource groep selecteren in het abonnement.
   * **Locatie**: waar de sjabloon wordt geïmplementeerd. Als u een bestaande resource groep hebt geselecteerd, wordt de locatie van die resource groep gebruikt.
1. **Instellingen**:
   * **SAP-systeem-id**: de SAP-systeem-id.
   * **Bs-type**: het type besturings systeem dat u wilt implementeren (Windows of Linux).
   * **SAP-systeem grootte**: de grootte van het SAP-systeem.

     Het aantal SAP'S dat het nieuwe systeem biedt. Als u niet zeker weet hoeveel SAP'S het systeem nodig heeft, vraagt u uw SAP-technologie partner of systeem integrator.
   * **Systeem beschikbaarheid** (alleen sjabloon met drie lagen): de beschik baarheid van het systeem.

     Selecteer **ha** voor een configuratie die geschikt is voor een installatie met een hoge Beschik baarheid. Er worden twee database servers en twee servers voor ASCS gemaakt.
   * **Opslag type** (alleen sjabloon met twee lagen): het type opslag dat moet worden gebruikt.

     Voor grotere systemen raden we u aan Azure Premium Storage te gebruiken. Raadpleeg de volgende bronnen voor meer informatie over opslag typen:
      * [Gebruik van Azure Premium-SSD Storage voor SAP DBMS-exemplaar][2367194]
      * [Opslag structuur van een virtuele machine voor RDBMS-implementaties](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: opslag met hoge prestaties voor werk belastingen van virtuele Azure-machines][storage-premium-storage-preview-portal]
      * [Inleiding tot Microsoft Azure Storage][storage-introduction]
   * **VHD-URI van gebruikers installatie kopie** (alleen sjabloon voor onbeheerde schijf installatie kopie): de URI van de VHD van de persoonlijke installatie kopie, bijvoorbeeld https:// &lt; accountname>. blob.core.Windows.net/VHDs/userimage.VHD.
   * **Opslag account voor gebruikers installatie kopie** (alleen sjabloon voor onbeheerde schijf installatie kopieën): de naam van het opslag account waarin de installatie kopie van het privé-besturings systeem is opgeslagen, bijvoorbeeld &lt; accountname> in https:// &lt; account naam>. blob.core.Windows.net/VHDs/userimage.VHD.
   * **userImageId** (alleen sjabloon voor beheerde schijf kopieën): id van de beheerde schijf kopie die u wilt gebruiken
   * **Gebruikers naam** en **wacht woord** beheerder: de gebruikers naam en het wacht woord.

     Er wordt een nieuwe gebruiker gemaakt om u aan te melden bij de virtuele machine.
   * **Nieuw of bestaand subnet**: Hiermee wordt bepaald of er een nieuw virtueel netwerk en subnet wordt gemaakt of een bestaand subnet wordt gebruikt. Als u al een virtueel netwerk hebt dat is verbonden met uw on-premises netwerk, selecteert u **bestaande**.
   * **Subnet-id**: als u de virtuele machine wilt implementeren in een bestaand VNet waarvoor u een subnet hebt gedefinieerd, moet de virtuele machine worden toegewezen aan, de id van het specifieke subnet benoemen. De ID ziet er ongeveer als volgt uit:/Subscriptions/ &lt; -abonnements-id>/resourceGroups/naam van de &lt; resource groep>/providers/Microsoft.Network/virtualNetworks/naam van het &lt; virtuele netwerk>/subnets/- &lt; subnet naam>

1. Voor **waarden**:  
    Bekijk en accepteer de juridische voor waarden.

1. Selecteer **Aankoop**.

#### <a name="install-the-vm-agent-linux-only"></a>De VM-agent installeren (alleen Linux)

Als u de sjablonen wilt gebruiken die in de voor gaande sectie worden beschreven, moet de Linux-agent al zijn geïnstalleerd in de installatie kopie van de gebruiker, anders mislukt de implementatie. Down load en installeer de VM-agent in de installatie kopie van de gebruiker, zoals beschreven in [de Azure VM-agent downloaden, installeren en inschakelen][deployment-guide-4.4]. Als u de sjablonen niet gebruikt, kunt u de VM-agent ook later installeren.

#### <a name="join-a-domain-windows-only"></a>Lid worden van een domein (alleen Windows)

Als uw Azure-implementatie is verbonden met een on-premises Active Directory of een DNS-exemplaar via een Azure site-naar-site-VPN-verbinding of Azure ExpressRoute (dit wordt *Cross-premises* genoemd in [Azure virtual machines planning en implementatie voor SAP NetWeaver][planning-guide]), wordt verwacht dat de virtuele machine wordt toegevoegd aan een on-premises domein. Zie [een virtuele machine toevoegen aan een on-premises domein (alleen Windows)][deployment-guide-4.3]voor meer informatie over overwegingen voor deze stap.

#### <a name="configure-proxy-settings"></a>Proxyinstellingen configureren

Afhankelijk van de configuratie van uw on-premises netwerk moet u mogelijk de proxy op uw virtuele machine instellen. Als uw virtuele machine is verbonden met uw on-premises netwerk via VPN of ExpressRoute, is de virtuele machine mogelijk niet in staat om toegang te krijgen tot internet en kunt u de vereiste VM-uitbrei dingen niet downloaden of Azure-infrastructuur gegevens voor de SAP host agent verzamelen via de SAP-extensie voor Azure, raadpleegt [u de proxy configureren][deployment-guide-configure-proxy].

#### <a name="configure-azure-vm-extension-for-sap"></a>Azure VM-extensie voor SAP configureren

Om ervoor te zorgen dat SAP uw omgeving ondersteunt, stelt u de Azure-extensie voor SAP in zoals beschreven in [de Azure-extensie voor SAP configureren][deployment-guide-4.5]. Controleer de vereisten voor SAP en de vereiste minimum versie van de SAP-kernel en SAP Hosta Gent, in de resources die worden vermeld in [SAP-resources][deployment-guide-2.2].

#### <a name="sap-vm-extension-check"></a>Controle van SAP-VM-extensie

Controleer of de VM-extensie voor SAP werkt, zoals wordt beschreven in [controles en probleem oplossing][deployment-guide-troubleshooting-chapter].


### <a name="scenario-3-moving-an-on-premises-vm-by-using-a-non-generalized-azure-vhd-with-sap"></a><a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Scenario 3: een on-premises VM verplaatsen met behulp van een niet-gegeneraliseerde virtuele harde schijf met SAP

In dit scenario bent u van plan een specifiek SAP-systeem te verplaatsen van een on-premises omgeving naar Azure. U kunt dit doen door de VHD met het besturings systeem, de SAP binaire bestanden en uiteindelijk de binaire bestanden van de DBMS te uploaden, plus de Vhd's met de gegevens-en logboek bestanden van het DBMS, naar Azure. In tegens telling tot het scenario dat wordt beschreven in [scenario 2: een virtuele machine implementeren met een aangepaste installatie kopie voor SAP][deployment-guide-3.3], in dit geval moet u de HOSTNAAM, SAP sid en SAP-gebruikers accounts in de Azure VM blijven, omdat deze zijn geconfigureerd in de on-premises omgeving. U hoeft het besturings systeem niet te generaliseren. Dit scenario is het meest van toepassing op cross-premises scenario's waarbij een deel van het SAP-landschap on-premises en een deel ervan wordt uitgevoerd op Azure.

In dit scenario wordt de VM-agent **niet** automatisch geïnstalleerd tijdens de implementatie. Omdat de VM-agent en de Azure-extensie voor SAP vereist zijn voor het uitvoeren van SAP NetWeaver op Azure, moet u beide onderdelen hand matig downloaden, installeren en inschakelen nadat u de virtuele machine hebt gemaakt.

Zie de volgende bronnen voor meer informatie over de Azure VM-agent.

---
> ![Windows-logo.][Logo_Windows] Windows
>
> [Overzicht van de agent voor virtuele Azure-machines][virtual-machines-windows-agent-user-guide]
>
> ![Linux-logo.][Logo_Linux] Linux
>
> [Gebruikershandleiding voor Azure Linux Agent][virtual-machines-linux-agent-user-guide]
>
>

---

Het volgende stroom diagram toont de volg orde van de stappen voor het verplaatsen van een on-premises VM met behulp van een niet-gegeneraliseerde Azure VHD:

![Stroom diagram van VM-implementatie voor SAP-systemen met behulp van een VM-schijf][deployment-guide-figure-400]

Als de schijf al is geüpload en gedefinieerd in azure (Zie [azure virtual machines planning en implementatie voor SAP NetWeaver][planning-guide]), voert u de taken uit die in de volgende secties worden beschreven.

#### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Als u een implementatie wilt maken met behulp van een persoonlijke besturingssysteem schijf via de Azure Portal, gebruikt u de SAP-sjabloon die is gepubliceerd in de [github-opslag plaats Azure-Quick Start-sjablonen][azure-quickstart-templates-github]. U kunt ook hand matig een virtuele machine maken met behulp van Power shell.

* [**Configuratie met twee lagen (slechts één virtuele-machine) sjabloon** (SAP-2-tier-User-disk)][sap-templates-2-tier-os-disk]

  Gebruik deze sjabloon om een systeem met twee lagen te maken met slechts één virtuele machine.
* [**Configuratie met twee lagen (slechts één virtuele machine) sjabloon beheerde schijf** (SAP-2-tier-User-Disk-MD)][sap-templates-2-tier-os-disk-md]

  Gebruik deze sjabloon om een systeem met twee lagen te maken met slechts één virtuele machine en een beheerde schijf.

Voer in het Azure Portal de volgende para meters voor de sjabloon in:

1. **Basisprincipes**:
   * **Abonnement**: het abonnement dat moet worden gebruikt voor het implementeren van de sjabloon.
   * **Resource groep**: de resource groep die moet worden gebruikt voor het implementeren van de sjabloon. U kunt een nieuwe resource groep maken of een bestaande resource groep selecteren in het abonnement.
   * **Locatie**: waar de sjabloon wordt geïmplementeerd. Als u een bestaande resource groep hebt geselecteerd, wordt de locatie van die resource groep gebruikt.
1. **Instellingen**:
   * **SAP-systeem-id**: de SAP-systeem-id.
   * **Bs-type**: het type besturings systeem dat u wilt implementeren (Windows of Linux).
   * **SAP-systeem grootte**: de grootte van het SAP-systeem.

     Het aantal SAP'S dat het nieuwe systeem biedt. Als u niet zeker weet hoeveel SAP'S het systeem nodig heeft, vraagt u uw SAP-technologie partner of systeem integrator.
   * **Opslag type** (alleen sjabloon met twee lagen): het type opslag dat moet worden gebruikt.

     Voor grotere systemen raden we u aan Azure Premium Storage te gebruiken. Raadpleeg de volgende bronnen voor meer informatie over opslag typen:
      * [Gebruik van Azure Premium-SSD Storage voor SAP DBMS-exemplaar][2367194]
      * [Opslag structuur van een virtuele machine voor RDBMS-implementaties](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: opslag met hoge prestaties voor werk belastingen van virtuele Azure-machines][storage-premium-storage-preview-portal]
      * [Inleiding tot Microsoft Azure Storage][storage-introduction]
   * **VHD-URI van besturingssysteem schijf** (alleen onbeheerde schijf sjabloon): de URI van de persoonlijke besturingssysteem schijf, bijvoorbeeld https:// &lt; accountnaam>. blob.core.Windows.net/VHDs/osdisk.VHD.
   * **Beheerde schijf-id van de besturingssysteem schijf** (alleen beheerde schijf sjabloon): de id van de beheerde schijf van het besturings systeem,/Subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/Group/providers/Microsoft.Compute/disks/Win
   * **Nieuw of bestaand subnet**: Hiermee wordt bepaald of er een nieuw virtueel netwerk en subnet wordt gemaakt, of dat er een bestaand subnet wordt gebruikt. Als u al een virtueel netwerk hebt dat is verbonden met uw on-premises netwerk, selecteert u **bestaande**.
   * **Subnet-id**: als u de virtuele machine wilt implementeren in een bestaand VNet waarvoor u een subnet hebt gedefinieerd, moet de virtuele machine worden toegewezen aan, de id van het specifieke subnet benoemen. De ID ziet er ongeveer als volgt uit:/Subscriptions/ &lt; -abonnements-id>/resourceGroups/naam van de &lt; resource groep>/providers/Microsoft.Network/virtualNetworks/naam van het &lt; virtuele netwerk>/subnets/- &lt; subnet naam>

1. Voor **waarden**:  
    Bekijk en accepteer de juridische voor waarden.

1. Selecteer **Aankoop**.

#### <a name="install-the-vm-agent"></a>De VM-agent installeren

Als u de sjablonen wilt gebruiken die in de voor gaande sectie worden beschreven, moet de VM-agent op de besturingssysteem schijf zijn geïnstalleerd, anders mislukt de implementatie. Down load en installeer de VM-agent in de virtuele machine, zoals beschreven in [de Azure VM-agent downloaden, installeren en inschakelen][deployment-guide-4.4].

Als u geen gebruik maakt van de sjablonen die in de voor gaande sectie worden beschreven, kunt u de VM-agent ook later installeren.

#### <a name="join-a-domain-windows-only"></a>Lid worden van een domein (alleen Windows)

Als uw Azure-implementatie is verbonden met een on-premises Active Directory of een DNS-exemplaar via een Azure site-naar-site-VPN-verbinding of ExpressRoute (dit wordt *Cross-premises* genoemd in [Azure virtual machines planning en implementatie voor SAP NetWeaver][planning-guide]), wordt verwacht dat de virtuele machine wordt toegevoegd aan een on-premises domein. Zie [een virtuele machine toevoegen aan een on-premises domein (alleen Windows)][deployment-guide-4.3]voor meer informatie over overwegingen voor deze taak.

#### <a name="configure-proxy-settings"></a>Proxyinstellingen configureren

Afhankelijk van de configuratie van uw on-premises netwerk moet u mogelijk de proxy op uw virtuele machine instellen. Als uw virtuele machine is verbonden met uw on-premises netwerk via VPN of ExpressRoute, is de virtuele machine mogelijk niet in staat om toegang te krijgen tot internet en kunt u de vereiste VM-uitbrei dingen niet downloaden of Azure-infrastructuur gegevens voor de SAP host agent verzamelen via de SAP-extensie voor Azure, raadpleegt [u de proxy configureren][deployment-guide-configure-proxy].

#### <a name="configure-azure-vm-extension-for-sap"></a>Azure VM-extensie voor SAP configureren

Om ervoor te zorgen dat SAP uw omgeving ondersteunt, stelt u de Azure-extensie voor SAP in zoals beschreven in [de Azure-extensie voor SAP configureren][deployment-guide-4.5]. Controleer de vereisten voor SAP en de vereiste minimum versie van de SAP-kernel en SAP Hosta Gent, in de resources die worden vermeld in [SAP-resources][deployment-guide-2.2].

#### <a name="sap-vm-check"></a>Controle van SAP-VM

Controleer of de VM-extensie voor SAP werkt, zoals wordt beschreven in [controles en probleem oplossing][deployment-guide-troubleshooting-chapter].

## <a name="update-the-configuration-of-azure-extension-for-sap"></a>De configuratie van de Azure-extensie voor SAP bijwerken

Werk de configuratie van de Azure-extensie voor SAP bij in een van de volgende scenario's:
* Het Joint micro soft/SAP-team breidt de mogelijkheden van de VM-extensie uit en vraagt meer of minder tellers.
* Micro soft introduceert een nieuwe versie van de onderliggende Azure-infra structuur die de gegevens levert en de Azure-extensie voor SAP moet worden aangepast aan die wijzigingen.
* U kunt extra gegevens schijven koppelen aan uw Azure VM of een gegevens schijf verwijderen. In dit scenario werkt u het verzamelen van gegevens met betrekking tot opslag bij. Het wijzigen van uw configuratie door eind punten toe te voegen of te verwijderen of door IP-adressen toe te wijzen aan een virtuele machine heeft geen invloed op de configuratie van de extensie.
* U kunt de grootte van uw virtuele Azure-machine wijzigen, bijvoorbeeld van grootte A5 naar een andere VM-grootte.
* U voegt nieuwe netwerk interfaces toe aan uw Azure-VM.

Als u de instellingen wilt bijwerken, moet u de configuratie van Azure-extensie voor SAP bijwerken door de stappen in [de Azure-extensie voor SAP configureren][deployment-guide-4.5]te volgen.

## <a name="detailed-tasks-for-sap-software-deployment"></a>Gedetailleerde taken voor SAP-software-implementatie

Deze sectie bevat gedetailleerde stappen voor het uitvoeren van specifieke taken in het configuratie-en implementatie proces.

### <a name="deploy-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Azure PowerShell-cmdlets implementeren

Volg de stappen die worden beschreven in het artikel [de Azure PowerShell-module installeren](/powershell/azure/install-az-ps)

Controleer regel matig op updates voor de Power shell-cmdlets, die meestal maandelijks worden bijgewerkt. Volg de stappen die in [Dit](/powershell/azure/install-az-ps#update-the-azure-powershell-module) artikel worden beschreven. Tenzij anders vermeld in SAP Note [1928533] of sap opmerking [2015553], raden we u aan om te werken met de nieuwste versie van Azure PowerShell-cmdlets.

Als u de versie van de Azure PowerShell-cmdlets wilt controleren die op uw computer zijn geïnstalleerd, voert u de volgende Power shell-opdracht uit:

```powershell
(Get-Module Az.Compute).Version
```

### <a name="deploy-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Azure CLI implementeren

Volg de stappen die worden beschreven in het artikel [de Azure cli installeren](/cli/azure/install-azure-cli)

Controleer regel matig op updates voor Azure CLI, die meestal maandelijks worden bijgewerkt.

Als u de versie van de Azure CLI wilt controleren die op uw computer is geïnstalleerd, voert u deze opdracht uit:

```console
az --version
```

### <a name="join-a-vm-to-an-on-premises-domain-windows-only"></a><a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Een VM toevoegen aan een on-premises domein (alleen Windows)

Als u SAP-Vm's implementeert in een cross-premises scenario, waarbij on-premises Active Directory en DNS worden uitgebreid in azure, wordt ervan uitgegaan dat de virtuele machines worden toegevoegd aan een on-premises domein. De stappen die u moet nemen om een virtuele machine te koppelen aan een on-premises domein en de aanvullende software die moet worden toegevoegd aan een lid van een on-premises domein, varieert per klant. Als u een virtuele machine wilt toevoegen aan een on-premises domein, moet u meestal extra software, zoals antimalware-software, en back-up-of bewakings software installeren.

In dit scenario moet u er ook voor zorgen dat als de Internet proxy-instellingen worden afgedwongen wanneer een virtuele machine aan een domein in uw omgeving wordt toegevoegd, het lokale systeem account (S-1-5-18) in de gast-VM dezelfde proxy instellingen heeft. De eenvoudigste optie is om de proxy af te dwingen met behulp van een domein groepsbeleid, dat van toepassing is op systemen in het domein.

### <a name="download-install-and-enable-the-azure-vm-agent"></a><a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>De Azure VM-agent downloaden, installeren en inschakelen

Voor virtuele machines die worden geïmplementeerd vanuit een installatie kopie van een besturings systeem die niet gegeneraliseerd is (bijvoorbeeld een installatie kopie die niet afkomstig is van de Windows-systeem voorbereiding of Sysprep, hulp programma), moet u de Azure VM-agent hand matig downloaden, installeren en inschakelen.

Als u een virtuele machine implementeert vanuit Azure Marketplace, is deze stap niet vereist. Installatie kopieën van Azure Marketplace hebben al de Azure VM-agent.

#### <a name="windows"></a><a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

1. De Azure VM-agent downloaden:
   1.  Down load het [installatie pakket van de Azure VM-agent](https://go.microsoft.com/fwlink/?LinkId=394789).
   1.  Sla het MSI-pakket van de VM-agent lokaal op een personal computer of server op.
1. Installeer de Azure VM-agent:
   1.  Maak verbinding met de geïmplementeerde Azure-VM met behulp van Remote Desktop Protocol (RDP).
   1.  Open een Windows Verkenner-venster op de virtuele machine en selecteer de doelmap voor het MSI-bestand van de VM-agent.
   1.  Sleep het MSI-bestand van het installatie programma van de Azure VM-agent van uw lokale computer/server naar de doel directory van de VM-agent op de VM.
   1.  Dubbel klik op het MSI-bestand op de virtuele machine.
1. Voor virtuele machines die lid zijn van on-premises domeinen, moet u ervoor zorgen dat de uiteindelijke Internet proxy instellingen ook van toepassing zijn op het Windows lokale systeem account (S-1-5-18) in de VM, zoals beschreven in [de proxy configureren][deployment-guide-configure-proxy]. De VM-agent wordt uitgevoerd in deze context en moet verbinding kunnen maken met Azure.

Er is geen interactie van de gebruiker vereist om de Azure VM-agent bij te werken. De VM-agent wordt automatisch bijgewerkt en het opnieuw opstarten van de VM is niet vereist.

#### <a name="linux"></a><a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux

Gebruik de volgende opdrachten om de VM-agent voor Linux te installeren:

* **SUSE Linux Enterprise Server (SLES)**

  ```console
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) of Oracle Linux**

  ```console
  sudo yum install WALinuxAgent
  ```

Als de agent al is geïnstalleerd, voert u de stappen uit die worden beschreven in [de Azure Linux-agent op een VM bijwerken naar de meest recente versie van github][virtual-machines-linux-update-agent]om de Azure Linux-agent bij te werken.

### <a name="configure-the-proxy"></a><a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>De proxy configureren

De stappen die u moet nemen voor het configureren van de proxy in Windows verschillen van de manier waarop u de proxy in Linux configureert.

#### <a name="windows"></a>Windows

Proxy-instellingen moeten correct worden ingesteld voor het lokale systeem account om toegang te krijgen tot internet. Als uw proxy instellingen niet zijn ingesteld door groepsbeleid, kunt u de instellingen voor het lokale systeem account configureren.

1. Ga naar **Start**, Voer **gpedit. msc** in en selecteer vervolgens **Enter**.
1. Selecteer **computer configuratie**  >  **Beheersjablonen**  >  **Windows-onderdelen**  >  **Internet Explorer**. Zorg ervoor dat de instelling **proxy-instellingen per computer (in plaats van per gebruiker)** is uitgeschakeld of niet is geconfigureerd.
1. Ga in **het configuratie scherm** naar de Internet opties van **netwerk centrum en delen**  >  .
1. Op het tabblad **verbindingen** selecteert u de knop **LAN-instellingen** .
1. Schakel het selectievakje **Instellingen automatisch detecteren** uit.
1. Schakel het selectie vakje **een proxy server gebruiken voor uw LAN** in en voer vervolgens het proxy adres en de poort in.
1. Selecteer de knop **Geavanceerd** .
1. Voer in het vak **uitzonde ringen** het IP-adres **168.63.129.16** in. Selecteer **OK**.

#### <a name="linux"></a>Linux

Configureer de juiste proxy in het configuratie bestand van de Microsoft Azure Guest-agent, die zich op \\ etc \\ waagent. conf bevindt.

Stel de volgende para meters in:

1. **Host voor http-proxy**. Stel deze bijvoorbeeld in op **proxy. Corp. local**.

   ```console
   HttpProxy.Host=<proxy host>

   ```

1. **Http-proxy poort**. Stel deze bijvoorbeeld in op **80**.

   ```console
   HttpProxy.Port=<port of the proxy host>

   ```

1. Start de agent opnieuw.

   ```console
   sudo service waagent restart
   ```

De proxy-instellingen in \\ etc \\ waagent. conf zijn ook van toepassing op de vereiste VM-extensies. Als u de Azure-opslag plaatsen wilt gebruiken, moet u ervoor zorgen dat het verkeer naar deze opslag plaatsen niet via uw on-premises intranet gaat. Als u door de gebruiker gedefinieerde routes hebt gemaakt om geforceerde Tunneling in te scha kelen, moet u ervoor zorgen dat u een route toevoegt die verkeer naar de opslag plaatsen rechtstreeks naar het internet stuurt, en niet via uw site-naar-site-VPN-verbinding.

* **SLES**

  U moet ook routes toevoegen voor de IP-adressen die worden vermeld in \\ etc \\ regionserverclnt. cfg. In de volgende afbeelding ziet u een voor beeld:

  ![Geforceerde tunneling][deployment-guide-figure-50]


* **RHEL**

  U moet ook routes toevoegen voor de IP-adressen van de hosts die worden vermeld in \\ etc \\ yum. opslag plaatsen. d \\ rhui-load balancers. Zie de voor gaande afbeelding voor een voor beeld.

* **Oracle Linux**

  Er zijn geen opslag plaatsen voor Oracle Linux op Azure. U moet uw eigen opslag plaatsen voor Oracle Linux configureren of gebruikmaken van de open bare opslag plaatsen.

Zie door de gebruiker [gedefinieerde routes en door sturen via IP][virtual-networks-udr-overview]voor meer informatie over door de gebruiker gedefinieerde routes.

### <a name="configure-the-azure-extension-for-sap"></a><a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>De Azure-extensie voor SAP configureren

> [!NOTE]
> Algemene ondersteunings verklaring:  
> Ondersteuning voor de Azure-extensie voor SAP wordt geboden via SAP-ondersteunings kanalen. Als u hulp nodig hebt met de Azure-extensie voor SAP, opent u een ondersteunings aanvraag met [SAP-ondersteuning](https://support.sap.com/). 

Wanneer u de virtuele machine hebt voor bereid zoals beschreven in [implementatie scenario's van vm's voor SAP on Azure][deployment-guide-3], wordt de Azure VM-agent geïnstalleerd op de virtuele machine. De volgende stap is het implementeren van de Azure-extensie voor SAP, die beschikbaar is in de Azure extension-opslag plaats in de wereld wijde Azure-data centers. Zie [Azure virtual machines planning en implementatie voor SAP net-Weaver][planning-guide-9.1]voor meer informatie.

We zijn bezig met het vrijgeven van een nieuwe versie van de Azure-extensie voor SAP. De nieuwe extensie maakt gebruik van de door het systeem toegewezen identiteit van de virtuele machine om informatie over de gekoppelde schijven, netwerk interfaces en de virtuele machine zelf op te halen. Om toegang te kunnen krijgen tot deze bronnen, heeft de systeem identiteit van de virtuele machine lezers machtigingen nodig voor de virtuele machine, de besturingssysteem schijf, gegevens schijven en netwerk interfaces. U wordt momenteel aangeraden de nieuwe extensie alleen in de volgende scenario's te installeren:

1. U wilt de uitbrei ding installeren met terraform, Azure Resource Manager sjablonen of met een andere methode dan Azure CLI of Azure PowerShell
1. U wilt de uitbrei ding installeren op SUSE SLES 15 of hoger.
1. Micro soft-of SAP-ondersteuning vraagt u de nieuwe extensie te installeren
1. U wilt Azure Ultra disk of Standard Managed Disks gebruiken

Voor deze scenario's volgt u de stappen in hoofd stuk de [nieuwe Azure-extensie voor SAP met Azure PowerShell][deployment-guide-configure-new-extension-ps] voor Azure PowerShell configureren of [de nieuwe Azure-extensie voor SAP configureren met Azure CLI][deployment-guide-configure-new-extension-cli] voor Azure cli.

Volg [Azure PowerShell][deployment-guide-4.5.1] of [Azure cli][deployment-guide-4.5.2] om de standaard versie van de Azure-extensie voor SAP te installeren en te configureren.

#### <a name="azure-powershell-for-linux-and-windows-vms"></a><a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Azure PowerShell voor Linux-en Windows-Vm's

De Azure-extensie voor SAP installeren met behulp van Power shell:

1. Zorg ervoor dat u de nieuwste versie van de cmdlet Azure PowerShell hebt geïnstalleerd. Zie [deploying Azure PowerShell cmdlets][deployment-guide-4.1](Engelstalig) voor meer informatie.  
1. Voer de volgende PowerShell-cmdlet uit.
    Voer cmdlet uit voor een lijst met beschik bare omgevingen `Get-AzEnvironment` . Als u wereld wijd Azure wilt gebruiken, is uw omgeving **Cloud**. Selecteer **AzureChinaCloud** voor Azure China 21vianet.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

Nadat u uw account gegevens hebt ingevoerd, implementeert het script de vereiste uitbrei dingen en schakelt de vereiste functies in. Dit kan enkele minuten duren.
`Set-AzVMAEMExtension`Zie [set-AzVMAEMExtension][msdn-set-Azvmaemextension]voor meer informatie over.

![De SAP-specifieke Azure-cmdlet Set-AzVMAEMExtension is uitgevoerd][deployment-guide-figure-900]

De `Set-AzVMAEMExtension` configuratie bevat alle stappen voor het configureren van het verzamelen van hostgegevens voor SAP.

De script uitvoer bevat de volgende informatie:

* Bevestiging dat het verzamelen van gegevens voor de besturingssysteem schijf en alle extra gegevens schijven is geconfigureerd.
* De volgende twee berichten bevestigen de configuratie van metrische opslag gegevens voor een specifiek opslag account.
* Eén regel van uitvoer geeft de status van de werkelijke update van de VM-extensie voor SAP-configuratie.
* Een andere regel van uitvoer bevestigt dat de configuratie is geïmplementeerd of bijgewerkt.
* De laatste regel van de uitvoer is informatief. Hier ziet u de opties voor het testen van de VM-extensie voor SAP-configuratie.
* Als u wilt controleren of alle stappen van de Azure VM-extensie voor SAP-configuratie met succes zijn uitgevoerd en dat de Azure-infra structuur de benodigde gegevens bevat, gaat u verder met de gereedheids controle voor de Azure-extensie voor SAP, zoals beschreven in [gereedheids controle voor Azure extension for SAP][deployment-guide-5.1].
* Wacht 15-30 minuten tot Azure Diagnostics de relevante gegevens wilt verzamelen.

#### <a name="azure-cli-for-linux-vms"></a><a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Azure CLI voor Linux-Vm's

De Azure-extensie voor SAP installeren met behulp van Azure CLI:

   1. Installeer de klassieke Azure-CLI, zoals beschreven in [Installeer de klassieke Azure-cli][azure-cli].
   1. Meld u aan met uw Azure-account:

      ```console
      azure login
      ```

   1. Overschakelen naar Azure Resource Manager modus:

      ```console
      azure config mode arm
      ```

   1. Azure-extensie voor SAP inschakelen:

      ```console
      azure vm enable-aem <resource-group-name> <vm-name>
      ```

1. Installeren met behulp van Azure CLI 2,0

   1. Installeer Azure CLI 2,0, zoals beschreven in [Azure cli 2,0 installeren][azure-cli-2].
   1. Meld u aan met uw Azure-account:

      ```azurecli
      az login
      ```

   1. Azure CLI AEM-extensie installeren
  
      ```azurecli
      az extension add --name aem
      ```
  
   1. Installeer de extensie met
  
      ```azurecli
      az vm aem set -g <resource-group-name> -n <vm name>
      ```

1. Controleer of de Azure-extensie voor SAP actief is op de virtuele Azure Linux-machine. Controleer of het bestand \\ var \\ lib \\ AzureEnhancedMonitor \\ PerfCounters bestaat. Als deze bestaat, voert u bij een opdracht prompt de volgende opdracht uit om de gegevens weer te geven die zijn verzameld door de Azure-extensie voor SAP:

   ```console
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   De uitvoer ziet er als volgt uit:

   ```output
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

#### <a name="configure-the-new-azure-extension-for-sap-with-azure-powershell"></a><a name="2ad55a0d-9937-4943-9dd2-69bc2b5d3de0"></a>De nieuwe Azure-extensie voor SAP configureren met Azure PowerShell

De nieuwe VM-extensie voor SAP maakt gebruik van een beheerde identiteit die is toegewezen aan de virtuele machine om toegang te krijgen tot de bewakings-en configuratie gegevens van de virtuele machine. Als u de nieuwe Azure-extensie voor SAP wilt installeren met behulp van Power shell, moet u eerst een dergelijke identiteit toewijzen aan de virtuele machine en die identiteit toegang verlenen tot alle resources die worden gebruikt door die VM, bijvoorbeeld schijven en netwerk interfaces.

> [!NOTE]
> Voor de volgende stappen zijn eigenaars rechten vereist voor de resource groep of afzonderlijke resources (virtuele machine, gegevens schijven, enzovoort).

1. Zorg ervoor dat u SAP host agent 7,21 PL 47 of hoger gebruikt.
1. Zorg ervoor dat u de huidige versie van de VM-extensie voor SAP verwijdert. Het is niet mogelijk om beide versies van de VM-extensie voor SAP op dezelfde virtuele machine te installeren.
1. Zorg ervoor dat u de nieuwste versie van de Azure PowerShell cmdlet (ten minste 4.3.0) hebt geïnstalleerd. Zie [deploying Azure PowerShell cmdlets][deployment-guide-4.1](Engelstalig) voor meer informatie.
1. Voer de volgende PowerShell-cmdlet uit.
    Voer cmdlet uit voor een lijst met beschik bare omgevingen `Get-AzEnvironment` . Als u wereld wijd Azure wilt gebruiken, is uw omgeving **Cloud**. Selecteer **AzureChinaCloud** voor Azure China 21vianet.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name> -InstallNewExtension
    ```

#### <a name="configure-the-new-azure-extension-for-sap-with-azure-cli"></a><a name="c8749c24-fada-42ad-b114-f9aae2dc37da"></a>De nieuwe Azure-extensie voor SAP configureren met Azure CLI

De nieuwe VM-extensie voor SAP maakt gebruik van een beheerde identiteit die is toegewezen aan de virtuele machine om toegang te krijgen tot de bewakings-en configuratie gegevens van de virtuele machine. Als u de nieuwe Azure-extensie voor SAP wilt installeren met behulp van Azure CLI, moet u eerst een dergelijke identiteit toewijzen aan de virtuele machine en die identiteit toegang verlenen tot alle resources die worden gebruikt door die VM, bijvoorbeeld schijven en netwerk interfaces.

> [!NOTE]
> Voor de volgende stappen zijn eigenaars rechten vereist voor de resource groep of afzonderlijke resources (virtuele machine, gegevens schijven, enzovoort).

1. Zorg ervoor dat u SAP host agent 7,21 PL 47 of hoger gebruikt.
1. Zorg ervoor dat u de huidige versie van de VM-extensie voor SAP verwijdert. Het is niet mogelijk om beide versies van de VM-extensie voor SAP op dezelfde virtuele machine te installeren.
1. Installeer Azure CLI 2,0, zoals beschreven in [Azure cli 2,0 installeren][azure-cli-2].

1. Meld u aan met uw Azure-account:

   ```azurecli
   az login
   ```

1. Volg de stappen in het artikel [beheerde identiteiten voor Azure-resources configureren op een Azure VM met behulp van Azure cli][qs-configure-cli-windows-vm] om een door System-Assigned beheerde identiteit in te scha kelen voor de virtuele machine. User-Assigned beheerde identiteiten worden niet ondersteund door de VM-extensie voor SAP. U kunt echter zowel een door het systeem toegewezen als een door de gebruiker toegewezen identiteit inschakelen.

   Voorbeeld:
   ```azurecli
   az vm identity assign -g <resource-group-name> -n <vm name>
   ```

1. Wijs de beheerde identiteits toegang toe aan de resource groep van de virtuele machine of aan alle netwerk interfaces, Managed disks en de virtuele machine zelf, zoals beschreven in [een beheerde identiteits toegang toewijzen aan een resource met behulp van Azure cli][howto-assign-access-cli]

    Voorbeeld:

    ```azurecli
    # Azure CLI on Linux
    spID=$(az resource show -g <resource-group-name> -n <vm name> --query identity.principalId --out tsv --resource-type Microsoft.Compute/virtualMachines)
    rgId=$(az group show -g <resource-group-name> --query id --out tsv)
    az role assignment create --assignee $spID --role 'Reader' --scope $rgId

    # Azure CLI on Windows/PowerShell
    $spID=az resource show -g <resource-group-name> -n <vm name> --query identity.principalId --out tsv --resource-type Microsoft.Compute/virtualMachines
    $rgId=az group show -g <resource-group-name> --query id --out tsv
    az role assignment create --assignee $spID --role 'Reader' --scope $rgId
    ```

1. Voer de volgende Azure CLI-opdracht uit om de Azure-extensie voor SAP te installeren.
    De uitbrei ding wordt momenteel alleen ondersteund in Cloud. Azure China 21Vianet, Azure Government of een van de andere speciale omgevingen worden nog niet ondersteund.

    ```azurecli
    # Azure CLI on Linux
    ## For Linux machines
    az vm extension set --publisher Microsoft.AzureCAT.AzureEnhancedMonitoring --name MonitorX64Linux --version 1.0 -g <resource-group-name> --vm-name <vm name> --settings '{"system":"SAP"}'

    ## For Windows machines
    az vm extension set --publisher Microsoft.AzureCAT.AzureEnhancedMonitoring --name MonitorX64Windows --version 1.0 -g <resource-group-name> --vm-name <vm name> --settings '{"system":"SAP"}'

    # Azure CLI on Windows/PowerShell
    ## For Linux machines
    az vm extension set --publisher Microsoft.AzureCAT.AzureEnhancedMonitoring --name MonitorX64Linux --version 1.0 -g <resource-group-name> --vm-name <vm name> --settings '{\"system\":\"SAP\"}'

    ## For Windows machines
    az vm extension set --publisher Microsoft.AzureCAT.AzureEnhancedMonitoring --name MonitorX64Windows --version 1.0 -g <resource-group-name> --vm-name <vm name> --settings '{\"system\":\"SAP\"}'
    ```

## <a name="checks-and-troubleshooting"></a><a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Controles en probleem oplossing

Nadat u uw virtuele Azure-machine hebt geïmplementeerd en de relevante Azure-extensie voor SAP hebt ingesteld, controleert u of alle onderdelen van de extensie werken zoals verwacht.

Voer de gereedheids controle uit voor de Azure-extensie voor SAP, zoals beschreven in [de gereedheids controle voor de Azure-extensie voor SAP][deployment-guide-5.1]. Als alle resultaten van de gereedheids controle positief zijn en alle relevante prestatie meter items goed worden weer gegeven, is Azure-extensie voor SAP ingesteld. U kunt door gaan met de installatie van de SAP host-agent zoals beschreven in de SAP-notities in [SAP-bronnen][deployment-guide-2.2]. Als de gereedheids controle aangeeft dat er prestatie meter items ontbreken, voert u de status controle voor de Azure-extensie voor SAP uit, zoals wordt beschreven in [status controle voor Azure-extensie voor SAP-configuratie][deployment-guide-5.2]. Zie [problemen met Azure-extensie voor SAP oplossen][deployment-guide-5.3]voor meer opties voor probleem oplossing.

### <a name="readiness-check-for-the-azure-extension-for-sap"></a><a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Gereedheids controle voor de Azure-extensie voor SAP

> [!NOTE]
> Er zijn twee versies van de VM-extensie. In dit hoofd stuk wordt de standaard-VM-extensie besproken. Als u de nieuwe VM-extensie hebt geïnstalleerd, raadpleegt u de [gereedheids controle voor de nieuwe Azure-extensie voor SAP][deployment-guide-5.1-new]

Met deze controle wordt ervoor gezorgd dat alle prestatie gegevens die in uw SAP-toepassing worden weer gegeven, worden verzorgd door de onderliggende Azure-extensie voor SAP.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>De gereedheids controle uitvoeren op een Windows-VM

1. Meld u aan bij de virtuele machine van Azure (met een beheerders account is niet nodig).
1. Open een opdrachtpromptvenster.
1. Ga naar de opdracht prompt en wijzig de map in de installatiemap van de Azure-extensie voor SAP: C: \\ pakketten \\ invoeg toepassingen \\ micro soft. AzureCAT. AzureEnhancedMonitoring. AzureCATExtensionHandler \\ &lt; versie>\\ Drop

   De *versie* in het pad naar de extensie kan variëren. Als er mappen worden weer gegeven voor meerdere versies van de uitbrei ding in de installatiemap, controleert u de configuratie van de AzureEnhancedMonitoring Windows-service en schakelt u over naar de map die wordt aangegeven als *pad naar uitvoerbaar bestand*.

   ![Eigenschappen van de service die de Azure-extensie voor SAP uitvoert][deployment-guide-figure-1000]

1. Voer bij de opdracht prompt **azperflib.exe** zonder para meters uit.

   > [!NOTE]
   > Azperflib.exe wordt in een lus uitgevoerd en de verzamelde tellers worden elke 60 seconden bijgewerkt. Sluit het opdracht prompt venster om de lus te beëindigen.
   >
   >

Als de Azure-extensie voor SAP niet is geïnstalleerd of als de AzureEnhancedMonitoring-service niet wordt uitgevoerd, is de extensie niet op de juiste wijze geconfigureerd. Zie [problemen met de Azure-extensie voor SAP oplossen][deployment-guide-5.3]voor meer informatie over het implementeren van de uitbrei ding.

> [!NOTE]
> De Azperflib.exe is een onderdeel dat niet kan worden gebruikt voor eigen doel einden. Het is een onderdeel dat Azure-infrastructuur gegevens levert die betrekking hebben op de virtuele machine voor de SAP host-agent.
> 

##### <a name="check-the-output-of-azperflibexe"></a>Controleer de uitvoer van azperflib.exe

Azperflib.exe uitvoer toont alle gevulde Azure-prestatie meter items voor SAP. Onder aan de lijst met verzamelde items wordt de status van de Azure-extensie voor SAP weer gegeven in een samen vatting en status indicator.

![Uitvoer van de status controle door azperflib.exe uit te voeren, wat aangeeft dat er geen problemen zijn][deployment-guide-figure-1100]
<a name="figure-11"></a>

Controleer het resultaat dat wordt geretourneerd voor de **tellers totale** uitvoer. dit wordt gerapporteerd als leeg **en voor de status die** in de voor gaande afbeelding wordt weer gegeven.

Interpreteer de resulterende waarden als volgt:

| Azperflib.exe resultaat waarden | Azure-uitbrei ding voor SAP-status |
| --- | --- |
| **API-aanroepen-niet beschikbaar** | Tellers die niet beschikbaar zijn, zijn mogelijk niet van toepassing op de configuratie van de virtuele machine of fouten. Zie de **integriteits status**. |
| **Totaal aantal tellers-leeg** |De volgende twee Azure-opslag-items kunnen leeg zijn: <ul><li>Lezen van de opslag op latentie server msec</li><li>Opslag gelezen op latentie E2E msec</li></ul>Alle andere tellers moeten waarden bevatten. |
| **Integriteits status** |Alleen OK als de retour status **OK** is. |
| **Diagnostics** |Gedetailleerde informatie over de status. |

Als de **status** waarde niet **OK** is, volgt u de instructies in [status controleren voor Azure extension for SAP-configuratie][deployment-guide-5.2].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>De gereedheids controle uitvoeren op een virtuele Linux-machine

1. Maak verbinding met de virtuele Azure-machine met behulp van SSH.

1. Controleer de uitvoer van de Azure-extensie voor SAP.

   a.  `more /var/lib/AzureEnhancedMonitor/PerfCounters` uitvoeren

   **Verwacht resultaat**: retourneert een lijst met prestatie meter items. Het bestand mag niet leeg zijn.

   b. `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error` uitvoeren

   **Verwacht resultaat**: retourneert één regel waarbij de fout **geen** is, bijvoorbeeld **3; config; Fout;; 0; 0; geen; 0; 1456416792; TST-servercs;**

   c. `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord` uitvoeren

   **Verwacht resultaat**: retourneert als leeg of bestaat niet.

Als de voor gaande controle niet is geslaagd, voert u de volgende extra controles uit:

1. Zorg ervoor dat de waagent is geïnstalleerd en ingeschakeld.

   a.  `sudo ls -al /var/lib/waagent/` uitvoeren

     **Verwacht resultaat**: hier wordt de inhoud van de waagent-map weer gegeven.

   b.  `ps -ax | grep waagent` uitvoeren

   **Verwacht resultaat**: er wordt een item weer gegeven dat er ongeveer als volgt uitziet: `python /usr/sbin/waagent -daemon`

1. Zorg ervoor dat de Azure-extensie voor SAP is geïnstalleerd en wordt uitgevoerd.

   a.  `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'` uitvoeren

   **Verwacht resultaat**: hier wordt de inhoud van de Azure-extensie voor SAP-Directory weer gegeven.

   b. `ps -ax | grep AzureEnhanced` uitvoeren

   **Verwacht resultaat**: er wordt een item weer gegeven dat er ongeveer als volgt uitziet: `python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

1. Installeer de SAP host agent zoals beschreven in SAP Note [1031096]en controleer de uitvoer van `saposcol` .

   a.  `/usr/sap/hostctrl/exe/saposcol -d` uitvoeren

   b.  `dump ccm` uitvoeren

   c.  Controleer of de **Virtualization_Configuration** meet waarde voor \Enhanced-controle **waar** is.

Als u al een SAP NetWeaver ABAP-toepassings server hebt geïnstalleerd, opent u de trans actie ST06 en controleert u of bewaking is ingeschakeld.

Als een van deze controles mislukt en voor gedetailleerde informatie over het opnieuw implementeren van de uitbrei ding, raadpleegt u [problemen met de Azure-extensie voor SAP oplossen][deployment-guide-5.3].

### <a name="readiness-check-for-the-new-azure-extension-for-sap"></a><a name="7bf24f59-7347-4c7a-b094-4693e4687ee5"></a>Gereedheids controle voor de nieuwe Azure-extensie voor SAP

> [!NOTE]
> Er zijn twee versies van de VM-extensie. In dit hoofd stuk wordt de nieuwe VM-extensie besproken. Als u de standaard-VM-extensie hebt geïnstalleerd, raadpleegt u de [gereedheids controle voor Azure extension for SAP][deployment-guide-5.1].

Met deze controle wordt ervoor gezorgd dat alle prestatie gegevens die in uw SAP-toepassing worden weer gegeven, worden verzorgd door de onderliggende Azure-extensie voor SAP.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>De gereedheids controle uitvoeren op een Windows-VM

1. Meld u aan bij de virtuele machine van Azure (met een beheerders account is niet nodig).
1. Open een webbrowser en ga naar http://127.0.0.1:11812/azure4sap/metrics
1. De browser moet een XML-bestand met de bewakings gegevens van uw virtuele machine weer geven of downloaden. Als dat niet het geval is, moet u ervoor zorgen dat de Azure-extensie voor SAP is geïnstalleerd.

##### <a name="check-the-content-of-the-xml-file"></a>De inhoud van het XML-bestand controleren

Het XML-bestand dat u kunt openen, http://127.0.0.1:11812/azure4sap/metrics bevat alle Azure-prestatie meter items voor SAP. Het bevat ook een samen vatting en status indicator van de status Azure-extensie voor SAP.

Controleer de waarde van het element **provider status Description** . Als de waarde niet **OK** is, volgt u de instructies in [status controleren voor nieuwe Azure-extensie voor SAP-configuratie][deployment-guide-5.2-new].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>De gereedheids controle uitvoeren op een virtuele Linux-machine

1. Maak verbinding met de virtuele Azure-machine met behulp van SSH.

1. Controleer de uitvoer van de volgende opdracht

    ```console
    curl http://127.0.0.1:11812/azure4sap/metrics
    ```
    
   **Verwacht resultaat**: retourneert een XML-document dat de bewakings gegevens bevat van de virtuele machine, de schijven en netwerk interfaces.

Als de voor gaande controle niet is geslaagd, voert u de volgende extra controles uit:

1. Zorg ervoor dat de waagent is geïnstalleerd en ingeschakeld.

   a.  `sudo ls -al /var/lib/waagent/` uitvoeren

     **Verwacht resultaat**: hier wordt de inhoud van de waagent-map weer gegeven.

   b.  `ps -ax | grep waagent` uitvoeren

   **Verwacht resultaat**: er wordt een item weer gegeven dat er ongeveer als volgt uitziet: `python /usr/sbin/waagent -daemon`

1. Zorg ervoor dat de Azure-extensie voor SAP is geïnstalleerd en wordt uitgevoerd.

   a.  `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-*/'` uitvoeren

   **Verwacht resultaat**: hier wordt de inhoud van de Azure-extensie voor SAP-Directory weer gegeven.

   b. `ps -ax | grep AzureEnhanced` uitvoeren

   **Verwacht resultaat**: er wordt een item weer gegeven dat er ongeveer als volgt uitziet: `/var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-1.0.0.82/AzureEnhancedMonitoring -monitor`

1. Installeer de SAP host agent zoals beschreven in SAP Note [1031096]en controleer de uitvoer van `saposcol` .

   a.  `/usr/sap/hostctrl/exe/saposcol -d` uitvoeren

   b.  `dump ccm` uitvoeren

   c.  Controleer of de **Virtualization_Configuration** meet waarde voor \Enhanced-controle **waar** is.

Als u al een SAP NetWeaver ABAP-toepassings server hebt geïnstalleerd, opent u de trans actie ST06 en controleert u of bewaking is ingeschakeld.

Als een van deze controles mislukt en voor gedetailleerde informatie over het opnieuw implementeren van de uitbrei ding, raadpleegt u [problemen met de nieuwe Azure-extensie voor SAP oplossen][deployment-guide-5.3-new].

### <a name="health-check-for-the-azure-extension-for-sap-configuration"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Status controle voor de Azure-extensie voor SAP-configuratie

> [!NOTE]
> Er zijn twee versies van de VM-extensie. In dit hoofd stuk wordt de standaard-VM-extensie besproken. Als u de nieuwe VM-extensie hebt geïnstalleerd, raadpleegt u de hoofdstuk [status controle voor de nieuwe Azure-extensie voor SAP-configuratie][deployment-guide-5.2-new].

Als sommige infrastructuur gegevens niet correct worden afgeleverd, zoals wordt aangegeven door de test die wordt beschreven in de [gereedheids controle voor Azure extension for SAP][deployment-guide-5.1], voert `Test-AzVMAEMExtension` u de cmdlet uit om te controleren of de Azure-infra structuur en de Azure-extensie voor SAP correct zijn geconfigureerd.

1. Zorg ervoor dat u de nieuwste versie van de cmdlet Azure PowerShell hebt geïnstalleerd, zoals beschreven in [Azure PowerShell-cmdlets implementeren][deployment-guide-4.1].
1. Voer de volgende PowerShell-cmdlet uit. Voer de cmdlet uit voor een lijst met beschik bare omgevingen `Get-AzEnvironment` . Als u wereld wijd Azure wilt gebruiken, selecteert u de **Cloud** -omgeving. Selecteer **AzureChinaCloud** voor Azure China 21vianet.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. Het script test de configuratie van de virtuele machine die u selecteert.

   ![Uitvoer van een geslaagde test van de Azure-extensie voor SAP][deployment-guide-figure-1300]

Zorg ervoor dat elk resultaat van de status controle **OK** is. Als sommige controles niet **OK** worden weer gegeven, voert u de update-cmdlet uit zoals beschreven in [de Azure-extensie voor SAP configureren][deployment-guide-4.5]. Wacht 15 minuten en herhaal de controles die zijn beschreven in [gereedheids controle voor Azure Extension voor SAP][deployment-guide-5.1] en [status controle voor Azure-extensie voor SAP-configuratie][deployment-guide-5.2]. Als de controles nog steeds een probleem met enkele of alle items aangeven, raadpleegt u [problemen met de Azure-extensie voor SAP oplossen][deployment-guide-5.3].

> [!Note]
> U kunt waarschuwingen krijgen in gevallen waarin u beheerde standaard-Azure-schijven gebruikt. Er worden waarschuwingen weer gegeven in plaats van de tests die ' OK ' retour neren. Dit is normaal en bedoeld voor dat schijf type. Zie ook [problemen met de Azure-extensie voor SAP oplossen][deployment-guide-5.3]
> 

### <a name="health-check-for-the-new-azure-extension-for-sap-configuration"></a><a name="464ac96d-7d3c-435d-a5ae-3faf3bfef4b3"></a>Status controle voor de nieuwe Azure-extensie voor SAP-configuratie

> [!NOTE]
> Er zijn twee versies van de VM-extensie. In dit hoofd stuk wordt de nieuwe VM-extensie besproken. Als u de standaard-VM-extensie hebt geïnstalleerd, raadpleegt u de hoofdstuk [status controle voor de Azure-extensie voor SAP-configuratie][deployment-guide-5.2].

Als sommige infrastructuur gegevens niet correct worden afgeleverd, zoals wordt aangegeven door de test die wordt beschreven in de [gereedheids controle voor Azure extension for SAP][deployment-guide-5.1-new], voert `Get-AzVMExtension` u de cmdlet uit om te controleren of de Azure-extensie voor SAP is geïnstalleerd. De `Test-AzVMAEMExtension` biedt nog geen ondersteuning voor de nieuwe extensie. Zodra de cmdlet de nieuwe uitbrei ding ondersteunt, wordt dit artikel bijgewerkt.

1. Zorg ervoor dat u de nieuwste versie van de cmdlet Azure PowerShell hebt geïnstalleerd, zoals beschreven in [Azure PowerShell-cmdlets implementeren][deployment-guide-4.1].
1. Voer de volgende PowerShell-cmdlet uit. Voer de cmdlet uit voor een lijst met beschik bare omgevingen `Get-AzEnvironment` . Als u wereld wijd Azure wilt gebruiken, selecteert u de **Cloud** -omgeving. Selecteer **AzureChinaCloud** voor Azure China 21vianet.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. De cmdlet test de configuratie van de VM-extensie voor SAP op de virtuele machine die u selecteert.

### <a name="troubleshooting-azure-extension-for-sap"></a><a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Problemen met Azure-extensie voor SAP oplossen

> [!NOTE]
> Er zijn twee versies van de VM-extensie. In dit hoofd stuk wordt de standaard-VM-extensie besproken. Als u de nieuwe VM-extensie hebt geïnstalleerd, raadpleegt u het hoofd stuk [problemen met de nieuwe Azure-extensie voor SAP oplossen][deployment-guide-5.3-new].

#### <a name="windows-logologo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows-logo.][Logo_Windows] Prestatie meter items van Azure worden helemaal niet weer gegeven

De AzureEnhancedMonitoring Windows-service verzamelt prestatie gegevens in Azure. Als de service niet juist is geïnstalleerd of niet op uw virtuele machine wordt uitgevoerd, kunnen er geen metrische gegevens voor prestaties worden verzameld.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>De installatiemap van de Azure-extensie voor SAP is leeg

###### <a name="issue"></a>Probleem

De installatie directory C: \\ pakketten \\ invoeg toepassingen \\ micro soft. AzureCAT. AzureEnhancedMonitoring. AzureCATExtensionHandler \\ &lt; versie>\\ Drop is leeg.

###### <a name="solution"></a>Oplossing

De extensie is niet geïnstalleerd. Bepaal of dit een proxy probleem is (zoals eerder beschreven). Mogelijk moet u de computer opnieuw opstarten of het `Set-AzVMAEMExtension` configuratie script opnieuw uitvoeren.

##### <a name="service-for-azure-extension-for-sap-does-not-exist"></a>De service voor Azure-extensie voor SAP bestaat niet

###### <a name="issue"></a>Probleem

De AzureEnhancedMonitoring Windows-service bestaat niet.

Azperflib.exe uitvoer genereert een fout:

![Uitvoering van azperflib.exe geeft aan dat de service van de Azure-extensie voor SAP niet wordt uitgevoerd][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Oplossing

Als de service niet bestaat, is de Azure-extensie voor SAP niet op de juiste wijze geïnstalleerd. Implementeer de uitbrei ding opnieuw met behulp van de stappen die worden beschreven in het implementatie scenario in [implementatie scenario's van vm's voor SAP in azure][deployment-guide-3].

Nadat u de uitbrei ding hebt geïmplementeerd, controleert u na één uur of de prestatie meter items van Azure zijn opgenomen in de Azure VM.

##### <a name="service-for-azure-extension-for-sap-exists-but-fails-to-start"></a>Er bestaat een service voor Azure-extensie voor SAP, maar deze kan niet worden gestart

###### <a name="issue"></a>Probleem

De AzureEnhancedMonitoring Windows-service bestaat en is ingeschakeld, maar kan niet worden gestart. Raadpleeg het gebeurtenis logboek van de toepassing voor meer informatie.

###### <a name="solution"></a>Oplossing

De configuratie is onjuist. Start de Azure-extensie voor SAP in de VM opnieuw, zoals beschreven in [de Azure-extensie voor SAP configureren][deployment-guide-4.5].

#### <a name="windows-logologo_windows-some-azure-performance-counters-are-missing"></a>![Windows-logo.][Logo_Windows] Sommige prestatie meter items van Azure ontbreken

De AzureEnhancedMonitoring Windows-service verzamelt prestatie gegevens in Azure. De service haalt gegevens op uit verschillende bronnen. Sommige configuratie gegevens worden lokaal verzameld en sommige prestatie gegevens worden gelezen uit Azure Diagnostics. Opslag tellers worden gebruikt vanuit uw logboek registratie op het niveau van het opslag abonnement.

Als u het probleem niet kunt oplossen met SAP Note [1999351] , voert u het `Set-AzVMAEMExtension` configuratie script opnieuw uit. Mogelijk moet u een uur wachten, omdat er mogelijk geen opslag analyse-of diagnostische tellers worden gemaakt direct nadat deze zijn ingeschakeld. Als het probleem zich blijft voordoen, opent u een SAP-klant bericht voor ondersteuning op de component BC-OP-NT-AZR voor Windows of BC-OP-LNX-AZR voor een virtuele Linux-machine.

#### <a name="linux-logologo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux-logo.][Logo_Linux] Prestatie meter items van Azure worden helemaal niet weer gegeven

Metrische gegevens over prestaties in Azure worden verzameld door een daemon. Als de daemon niet wordt uitgevoerd, kunnen er geen prestatie gegevens worden verzameld.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>De installatiemap van de Azure-extensie voor SAP is leeg

###### <a name="issue"></a>Probleem

De map \\ var \\ lib \\ waagent heeft \\ geen submap voor de Azure-extensie voor SAP.

###### <a name="solution"></a>Oplossing

De extensie is niet geïnstalleerd. Bepaal of dit een proxy probleem is (zoals eerder beschreven). Mogelijk moet u de computer opnieuw opstarten en/of het `Set-AzVMAEMExtension` configuratie script opnieuw uitvoeren.

##### <a name="the-execution-of-set-azvmaemextension-and-test-azvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>De uitvoering van Set-AzVMAEMExtension en Test-AzVMAEMExtension waarschuwings berichten weer geven met de mede deling dat standaard Managed Disks niet worden ondersteund

###### <a name="issue"></a>Probleem

Bij het uitvoeren van Set-AzVMAEMExtension of Test-AzVMAEMExtension berichten zoals deze worden weer gegeven:

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

Als u azperfli.exe uitvoert zoals eerder beschreven, kunt u een resultaat krijgen dat een niet-goede status aangeeft. 

###### <a name="solution"></a>Oplossing

De berichten worden veroorzaakt door het feit dat standaard Managed Disks geen Api's leveren die worden gebruikt door de SAP-extensie voor SAP om te controleren op statistische gegevens van de standaard Azure Storage accounts. Dit is niet van belang. Reden voor het introduceren van het verzamelen van gegevens voor Standard-Disk Storage accounts is het beperken van de invoer en uitvoer die regel matig zijn opgetreden. Managed disks voor komt een dergelijke beperking door het aantal schijven in een opslag account te beperken. Daarom is het niet zo dat dat type gegevens niet kritiek zijn.


#### <a name="linux-logologo_linux-some-azure-performance-counters-are-missing"></a>![Linux-logo.][Logo_Linux] Sommige prestatie meter items van Azure ontbreken

De metrische gegevens over prestaties in Azure worden verzameld door een daemon. deze haalt informatie uit verschillende bronnen op. Sommige configuratie gegevens worden lokaal verzameld en sommige prestatie gegevens worden gelezen uit Azure Diagnostics. Opslag items zijn afkomstig uit de logboeken in uw opslag abonnement.

Voor een volledige en bijgewerkte lijst met bekende problemen raadpleegt u SAP Note [1999351], die extra informatie over probleem oplossing voor Azure extension for SAP bevat.

Als u het probleem niet kunt oplossen met SAP Note [1999351] , voert u het `Set-AzVMAEMExtension` configuratie script opnieuw uit zoals beschreven in [de Azure-extensie voor SAP configureren][deployment-guide-4.5]. Mogelijk moet u een uur wachten, omdat er mogelijk geen opslag analyse-of diagnostische tellers worden gemaakt direct nadat deze zijn ingeschakeld. Als het probleem zich blijft voordoen, opent u een SAP-klant bericht voor ondersteuning op de component BC-OP-NT-AZR voor Windows of BC-OP-LNX-AZR voor een virtuele Linux-machine.

### <a name="troubleshooting-the-new-azure-extension-for-sap"></a><a name="b7afb8ef-a64c-495d-bb37-2af96688c530"></a>Problemen met de nieuwe Azure-extensie voor SAP oplossen

> [!NOTE]
> Er zijn twee versies van de VM-extensie. In dit hoofd stuk wordt de nieuwe VM-extensie besproken. Als u de standaard-VM-extensie hebt geïnstalleerd, raadpleegt u hoofd stuk [problemen met de Azure-extensie voor SAP oplossen][deployment-guide-5.3].

#### <a name="windows-logologo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows-logo.][Logo_Windows] Prestatie meter items van Azure worden helemaal niet weer gegeven

Het AzureEnhancedMonitoring-proces verzamelt prestatie gegevens in Azure. Als het proces niet wordt uitgevoerd in uw virtuele machine, kunnen er geen metrische gegevens over prestaties worden verzameld.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>De installatiemap van de Azure-extensie voor SAP is leeg

###### <a name="issue"></a>Probleem

De installatie directory C: \\ pakketten \\ invoeg toepassingen \\ micro soft. AzureCAT. AzureEnhancedMonitoring. MonitorX64Windows \\ &lt; versie> is leeg.

###### <a name="solution"></a>Oplossing

De extensie is niet geïnstalleerd. Bepaal of dit een proxy probleem is (zoals eerder beschreven). Mogelijk moet u de computer opnieuw opstarten of de VM-extensie opnieuw installeren.

#### <a name="windows-logologo_windows-some-azure-performance-counters-are-missing"></a>![Windows-logo.][Logo_Windows] Sommige prestatie meter items van Azure ontbreken

Het AzureEnhancedMonitoring Windows-proces verzamelt prestatie gegevens in Azure. Het proces haalt gegevens uit verschillende bronnen op. Sommige configuratie gegevens worden lokaal verzameld en sommige prestatie gegevens worden gelezen uit Azure Monitor.

Als u het probleem niet kunt oplossen met SAP Note [1999351] , opent u een sap-klant ondersteunings bericht in component BC-op-NT-AZR voor Windows of BC-op-LNX-AZR voor een virtuele Linux-machine. Koppel het logboek bestand C: \\ pakketten \\ invoeg toepassingen \\ micro soft. AzureCAT. AzureEnhancedMonitoring. MonitorX64Windows \\ &lt;>\\logapp.txt aan het incident.

#### <a name="linux-logologo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux-logo.][Logo_Linux] Prestatie meter items van Azure worden helemaal niet weer gegeven

Metrische gegevens over prestaties in Azure worden verzameld door een daemon. Als de daemon niet wordt uitgevoerd, kunnen er geen prestatie gegevens worden verzameld.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>De installatiemap van de Azure-extensie voor SAP is leeg

###### <a name="issue"></a>Probleem

De map \\ var \\ lib \\ waagent heeft \\ geen submap voor de Azure-extensie voor SAP.

###### <a name="solution"></a>Oplossing

De extensie is niet geïnstalleerd. Bepaal of dit een proxy probleem is (zoals eerder beschreven). Mogelijk moet u de computer opnieuw opstarten en/of de VM-extensie opnieuw installeren.

#### <a name="linux-logologo_linux-some-azure-performance-counters-are-missing"></a>![Linux-logo.][Logo_Linux] Sommige prestatie meter items van Azure ontbreken

De metrische gegevens over prestaties in Azure worden verzameld door een daemon. deze haalt informatie uit verschillende bronnen op. Sommige configuratie gegevens worden lokaal verzameld en sommige prestatie gegevens worden gelezen uit Azure Monitor.

Voor een volledige en bijgewerkte lijst met bekende problemen raadpleegt u SAP Note [1999351], die extra informatie over probleem oplossing voor Azure extension for SAP bevat.

Als u het probleem niet kunt oplossen met SAP Note [1999351] , installeert u de extensie opnieuw zoals beschreven in [de Azure-extensie voor SAP configureren][deployment-guide-4.5]. Als het probleem zich blijft voordoen, opent u een SAP-klant bericht voor ondersteuning op de component BC-OP-NT-AZR voor Windows of BC-OP-LNX-AZR voor een virtuele Linux-machine. Sluit het logboek bestand/var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux- &lt; versie>/logapp.txt aan het incident.

## <a name="azure-extension-error-codes"></a>Fout codes voor Azure-extensies

| Fout-ID | Foutbeschrijving | Oplossing |
|---|---|---|
| <a name="cfg_018"></a>cfg/018 | App-configuratie ontbreekt. | [installatie script uitvoeren][deployment-guide-run-the-script] |
| <a name="cfg_019"></a>cfg/019 | Geen implementatie-ID in app-configuratie. | [contact opnemen met ondersteuning][deployment-guide-contact-support] |
| <a name="cfg_020"></a>cfg/020 | Geen RoleInstanceId in de app-configuratie. | [contact opnemen met ondersteuning][deployment-guide-contact-support] |
| <a name="cfg_022"></a>cfg/022 | Geen RoleInstanceId in de app-configuratie. | [contact opnemen met ondersteuning][deployment-guide-contact-support] |
| <a name="cfg_031"></a>cfg/031 | Kan de Azure-configuratie niet lezen. | [contact opnemen met ondersteuning][deployment-guide-contact-support] |
| <a name="cfg_021"></a>cfg/021 | Het configuratie bestand van de app ontbreekt. | [installatie script uitvoeren][deployment-guide-run-the-script] |
| <a name="cfg_015"></a>cfg/015 | Er is geen VM-grootte in de app-configuratie. | [installatie script uitvoeren][deployment-guide-run-the-script] |
| <a name="cfg_016"></a>cfg/016 | De teller GlobalMemoryStatusEx is mislukt. | [contact opnemen met ondersteuning][deployment-guide-contact-support] |
| <a name="cfg_023"></a>cfg/023 | De teller MaxHwFrequency is mislukt. | [contact opnemen met ondersteuning][deployment-guide-contact-support] |
| <a name="cfg_024"></a>cfg/024 | De NIC-items zijn mislukt. | [contact opnemen met ondersteuning][deployment-guide-contact-support] |
| <a name="cfg_025"></a>cfg/025 | Schijf toewijzings teller is mislukt. | [contact opnemen met ondersteuning][deployment-guide-contact-support] |
| <a name="cfg_026"></a>cfg/026 | De teller van de processor naam is mislukt. | [contact opnemen met ondersteuning][deployment-guide-contact-support] |
| <a name="cfg_027"></a>cfg/027 | Schijf toewijzings teller is mislukt. | [contact opnemen met ondersteuning][deployment-guide-contact-support] |
| <a name="cfg_038"></a>cfg/038 | Het metrische schijf type ontbreekt in het configuratie bestand van de extensie config.xml. Schijf type en enkele andere tellers zijn geïntroduceerd in v 2.2.0.68 12/16/2015. Als u de extensie hebt geïmplementeerd vóór 12/16/2015, wordt het oude configuratie bestand gebruikt. De uitbrei ding wordt door Azure extension Framework automatisch bijgewerkt naar een nieuwere versie, maar de config.xml blijft ongewijzigd. Down load en voer het meest recente Power shell-installatie script uit om de configuratie bij te werken. | [installatie script uitvoeren][deployment-guide-run-the-script] |
| <a name="cfg_039"></a>cfg/039 | Geen schijf cache. | [installatie script uitvoeren][deployment-guide-run-the-script] |
| <a name="cfg_036"></a>cfg/036 | Geen SLA-door Voer voor schijf. | [installatie script uitvoeren][deployment-guide-run-the-script] |
| <a name="cfg_037"></a>cfg/037 | Geen SLA voor schijf ruimte. | [installatie script uitvoeren][deployment-guide-run-the-script] |
| <a name="cfg_028"></a>cfg/028 | Schijf toewijzings teller is mislukt. | [contact opnemen met ondersteuning][deployment-guide-contact-support] |
| <a name="cfg_029"></a>cfg/029 | Laatste wijzigings teller voor hardware is mislukt. | [contact opnemen met ondersteuning][deployment-guide-contact-support] |
| <a name="cfg_030"></a>cfg/030 | NIC-items mislukt | [contact opnemen met ondersteuning][deployment-guide-contact-support] |
| <a name="cfg_017"></a>cfg/017 | Vanwege het Sysprep-bestand van de VM is uw Windows-SID gewijzigd. | [opnieuw implementeren na Sysprep][deployment-guide-redeploy-after-sysprep] |
| <a name="str_007"></a>Str/007 | Toegang tot de opslag analyse is mislukt. <br /><br />Als populatie van opslag analyse gegevens op een nieuw gemaakte virtuele machine mogelijk tot een half uur nodig is, kan de fout na enige tijd verdwijnen. Als de fout nog steeds wordt weer gegeven, voert u het installatie script opnieuw uit. | [installatie script uitvoeren][deployment-guide-run-the-script] |
| <a name="str_010"></a>Str/010 | Geen Opslaganalyse meter items. | [installatie script uitvoeren][deployment-guide-run-the-script] |
| <a name="str_009"></a>Str/009 | Opslaganalyse is mislukt. | [installatie script uitvoeren][deployment-guide-run-the-script] |
| <a name="wad_004"></a>wad/004 | Ongeldige WAD-configuratie. | [installatie script uitvoeren][deployment-guide-run-the-script] |
| <a name="wad_002"></a>wad/002 | Onverwachte WAD-indeling. | [contact opnemen met ondersteuning][deployment-guide-contact-support] |
| <a name="wad_001"></a>wad/001 | Geen WAD-items gevonden. | [installatie script uitvoeren][deployment-guide-run-the-script] |
| <a name="wad_040"></a>wad/040 | Er zijn verouderde WAD-items gevonden. | [contact opnemen met ondersteuning][deployment-guide-contact-support] |
| <a name="wad_003"></a>wad/003 | Kan de WAD-tabel niet lezen. Er is geen verbinding met de WAD-tabel. Dit kan verschillende oorzaken hebben:<br /><br /> 1) verouderde configuratie <br />2) geen netwerk verbinding met Azure <br />3) problemen met WAD-instellingen | [installatie script uitvoeren][deployment-guide-run-the-script]<br />[Internet verbinding herstellen][deployment-guide-fix-internet-connection]<br />[contact opnemen met ondersteuning][deployment-guide-contact-support] |
| <a name="prf_011"></a>PRF/011 | Metrieken voor perfmon-NIC mislukt. | [contact opnemen met ondersteuning][deployment-guide-contact-support] |
| <a name="prf_012"></a>PRF/012 | Prestatie meters voor perfmon-schijf zijn mislukt. | [contact opnemen met ondersteuning][deployment-guide-contact-support] |
| <a name="prf_013"></a>PRF/013 | Enkele metrische prefmon-gegevens zijn mislukt. | [contact opnemen met ondersteuning][deployment-guide-contact-support] |
| <a name="prf_014"></a>PRF/014 | Perfmon kan geen teller maken. | [contact opnemen met ondersteuning][deployment-guide-contact-support] |
| <a name="cfg_035"></a>cfg/035 | Er zijn geen metrische providers geconfigureerd. | [contact opnemen met ondersteuning][deployment-guide-contact-support] |
| <a name="str_006"></a>Str/006 | Ongeldige Opslaganalyse configuratie. | [installatie script uitvoeren][deployment-guide-run-the-script] |
| <a name="str_032"></a>Str/032 | Opslaganalyse metrieken is mislukt. | [installatie script uitvoeren][deployment-guide-run-the-script] |
| <a name="cfg_033"></a>cfg/033 | Een van de metrische providers is mislukt. | [installatie script uitvoeren][deployment-guide-run-the-script] |
| <a name="str_034"></a>Str/034 | Provider thread is mislukt. | [contact opnemen met ondersteuning][deployment-guide-contact-support] |

### <a name="detailed-guidelines-on-solutions-provided"></a>Gedetailleerde richt lijnen voor oplossingen die

#### <a name="run-the-setup-script"></a><a name="0d2847ad-865d-4a4c-a405-f9b7baaa00c7"></a>Het installatie script uitvoeren

Volg de stappen in hoofd stuk [de Azure-extensie voor SAP configureren][deployment-guide-4.5] in deze hand leiding om de uitbrei ding opnieuw te installeren. Houd er rekening mee dat sommige tellers tot wel 30 minuten moeten zijn voor het inrichten.

Als de fouten niet verdwijnen, [neemt u contact op met de ondersteuning][deployment-guide-contact-support].

#### <a name="contact-support"></a><a name="3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2"></a>Contact opnemen met ondersteuning

Er is een onverwachte fout opgetreden of er is geen bekende oplossing. Verzamel het bestand AzureEnhancedMonitoring_service. log dat zich in de map C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ \<version\> \Drop (Windows) of/var/log/Azure/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux (Linux) bevindt en neem contact op met SAP-ondersteuning voor verdere ondersteuning.

#### <a name="redeploy-after-sysprep"></a><a name="2cd61f22-187d-42ed-bb8c-def0c983d756"></a>Opnieuw implementeren na Sysprep

Als u van plan bent om een gegeneraliseerde installatie kopie van het Sysprep-besturings systeem (waaronder SAP-software) te maken, is het raadzaam dat deze installatie kopie de Azure-extensie voor SAP niet bevat. U moet de Azure-extensie voor SAP installeren nadat het nieuwe exemplaar van de gegeneraliseerde installatie kopie van het besturings systeem is geïmplementeerd.

Als uw gegeneraliseerde en Sysprep OS-installatie kopie al de Azure-extensie voor SAP bevat, kunt u de volgende tijdelijke oplossing Toep assen om de uitbrei ding opnieuw te configureren op het nieuw geïmplementeerde VM-exemplaar:

* Op de zojuist geïmplementeerde VM-instantie verwijdert u de inhoud van de volgende mappen:  
  C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ \<version\> \RuntimeSettings C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ \<version\> \Status

* Volg de stappen in hoofd stuk [de Azure-extensie voor SAP configureren][deployment-guide-4.5] in deze hand leiding om de uitbrei ding opnieuw te installeren.

#### <a name="fix-internet-connection"></a><a name="e92bc57d-80d9-4a2b-a2f4-16713a22ad89"></a>Internet verbinding herstellen

Op de Microsoft Azure virtuele machine met de Azure-extensie voor SAP is toegang tot internet vereist. Als deze Azure-VM deel uitmaakt van een Azure-Virtual Network of een on-premises domein, moet u ervoor zorgen dat de relevante proxy-instellingen zijn ingesteld. Deze instellingen moeten ook geldig zijn voor de LocalSystem-account om toegang te krijgen tot internet. Volg hoofd stuk [de proxy configureren][deployment-guide-configure-proxy] in deze hand leiding.

Als u bovendien een statisch IP-adres voor uw virtuele Azure-machine moet instellen, moet u deze niet hand matig instellen in de Azure-VM, maar instellen met behulp van [Azure PowerShell](../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md), [Azure cli](../../../virtual-network/virtual-networks-static-private-ip-arm-cli.md) [Azure Portal](../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md). Het statische IP-adres wordt door gegeven via de Azure DHCP-service.

Het hand matig instellen van een statisch IP-adres in de virtuele Azure-machine wordt niet ondersteund en kan leiden tot problemen met de Azure-extensie voor SAP.
