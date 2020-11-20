---
title: Extensie en functies van virtuele Azure-machines
description: Meer informatie over Azure VM-extensies
services: virtual-machines
author: amjads1
ms.service: virtual-machines
ms.subservice: extensions
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/03/2020
ms.author: amjads
ms.openlocfilehash: b1dd26fce2e0a761ceed211933cb79ce518905e2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965881"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Extensie en functies van virtuele Azure-machines
Uitbrei dingen zijn kleine toepassingen die configuratie en automatisering na de implementatie bieden op virtuele machines van Azure. Het Azure-platform fungeert als host voor veel uitbrei dingen die de VM-configuratie, bewakings-, beveiligings-en hulpprogramma toepassingen beslaan Uitgevers maken een toepassing, zetten deze in een uitbrei ding en vereenvoudigen de installatie. U hoeft alleen maar de vereiste para meters op te geven. 

## <a name="how-can-i-find-what-extensions-are-available"></a>Hoe kan ik vinden welke extensies beschikbaar zijn?
U kunt beschik bare uitbrei dingen weer geven door een virtuele machine te selecteren en de optie **extensies** selecteren in het menu links. Zie [VM-extensies detecteren voor Linux](features-linux.md) en het [detecteren van VM-extensies voor Windows](features-windows.md)voor meer informatie over het ophalen van een volledige lijst met uitbrei dingen.

## <a name="how-can-i-install-an-extension"></a>Hoe kan ik een uitbrei ding installeren?
Azure VM-extensies kunnen worden beheerd met behulp van de Azure CLI, Power shell, Resource Manager-sjablonen en de Azure Portal. Als u een uitbrei ding wilt proberen, gaat u naar de Azure Portal, selecteert u de aangepaste script extensie en voert u een opdracht of script uit om de extensie uit te voeren.

Zie voor meer informatie [Windows Custom Script extension](custom-script-windows.md) en [Linux Custom Script extension](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>De levens cyclus van de extensie toepassing Hoe kan ik beheren?
U hoeft niet rechtstreeks verbinding te maken met een virtuele machine om een uitbrei ding te installeren of verwijderen. De levens cyclus van Azure-uitbrei dingen wordt buiten de virtuele machine beheerd en geïntegreerd in het Azure-platform.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Iets anders zou ik denken te weten over uitbrei dingen?
Sommige individuele VM-extensie toepassingen hebben mogelijk hun eigen omgevings vereisten, zoals toegang tot een eind punt. Elke uitbrei ding heeft een artikel met uitleg over eventuele vereisten, waaronder de besturings systemen die worden ondersteund.

## <a name="troubleshoot-extensions"></a>Problemen met extensies oplossen

Informatie voor probleem oplossing voor elke extensie vindt u in de sectie **probleem oplossing en ondersteuning** in het overzicht voor de uitbrei ding. Hier volgt een lijst met de beschik bare informatie over het oplossen van problemen:

| Naamruimte | Problemen oplossen |
|-----------|-----------------|
| micro soft. Azure. monitoring. dependencyagent. dependencyagentlinux | [Azure Monitor afhankelijkheid voor Linux](agent-dependency-linux.md#troubleshoot-and-support) |
| micro soft. Azure. monitoring. dependencyagent. dependencyagentwindows | [Azure Monitor afhankelijkheid voor Windows](agent-dependency-windows.md#troubleshoot-and-support) |
| micro soft. Azure. Security. azurediskencryptionforlinux | [Azure Disk Encryption voor Linux](azure-disk-enc-linux.md#troubleshoot-and-support) |
| micro soft. Azure. Security. azurediskencryption | [Azure Disk Encryption voor Windows](azure-disk-enc-windows.md#troubleshoot-and-support) |
| micro soft. compute. customscriptextension | [Aangepast script voor Windows](custom-script-windows.md#troubleshoot-and-support) |
| micro soft. ostcextensions. customscriptforlinux | [Configuratie van de gewenste status voor Linux](dsc-linux.md#troubleshoot-and-support) |
| micro soft. Power shell. DSC | [Configuratie van desired state voor Windows](dsc-windows.md#troubleshoot-and-support) |
| micro soft. hpccompute. nvidiagpudriverlinux | [Uitbrei ding van NVIDIA GPU-stuur programma voor Linux](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| micro soft. hpccompute. nvidiagpudriverwindows | [Uitbrei ding voor NVIDIA GPU-stuur programma voor Windows](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| micro soft. Azure. Security. iaasantimalware | [Uitbrei ding van antimalware voor Windows](iaas-antimalware-windows.md#troubleshoot-and-support) |
| micro soft. enterprisecloud. monitoring. omsagentforlinux | [Azure Monitor voor Linux](oms-linux.md#troubleshoot-and-support)
| micro soft. enterprisecloud. monitoring. microsoftmonitoringagent | [Azure Monitor voor Windows](oms-windows.md#troubleshoot-and-support) |
| stackify. linuxagent. extension. stackifylinuxagentextension | [Stackify opnieuw traceren voor Linux](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux. micro soft. ostcextensions | [Wacht woord opnieuw instellen voor Linux](vmaccess.md#troubleshoot-and-support) |
| micro soft. Recovery Services. vmsnapshot | [Moment opname voor Linux](vmsnapshot-linux.md#troubleshoot-and-support) |
| micro soft. Recovery Services. vmsnapshot | [Moment opname voor Windows](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Volgende stappen
* Zie [Azure VM-extensies en-functies voor Linux](features-linux.md)voor meer informatie over de werking van de Linux-agent en-extensies.
* Zie [Azure VM-extensies en-functies voor Windows](features-windows.md)voor meer informatie over de werking van de Windows-gast agent en-extensies.  
* Zie [overzicht van Azure Windows virtual machine agent voor informatie](agent-windows.md)over het installeren van de Windows-gast agent.  
* Als u de Linux-agent wilt installeren, raadpleegt u [overzicht van Azure Linux Virtual Machine agent](agent-linux.md).  

