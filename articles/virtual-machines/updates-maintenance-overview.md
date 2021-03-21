---
title: Overzicht van updates en onderhoud
description: Meer informatie over de beschik bare updates en onderhouds opties voor virtuele machines in azure
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: overview
ms.date: 03/08/2021
ms.reviewer: cynthn
ms.openlocfilehash: 81c6fb2e7f25abc9a236c80d1412b84fd6761872
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103022264"
---
# <a name="updates-and-maintenance-overview"></a>Overzicht van updates en onderhoud
Dit artikel bevat een overzicht van de verschillende update-en onderhouds opties voor Azure virtual machines (Vm's).

## <a name="automatic-os-image-upgrade"></a>Upgrade van automatische installatie kopie van besturings systeem

Door [automatische installatie kopieën van besturings systemen](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context) in te scha kelen in uw schaalset kunt u het update beheer vereenvoudigen door de besturingssysteem schijf veilig en automatisch bij te werken voor alle exemplaren in de schaalset.

[Automatische upgrade van het besturings systeem](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context) heeft de volgende kenmerken:

- Na de configuratie wordt de meest recente installatie kopie van het besturings systeem, gepubliceerd door installatie kopieën van uitgevers, automatisch toegepast op de schaalset zonder tussen komst van de gebruiker.
- Voert een upgrade uit van batches van instanties wanneer een nieuwe installatie kopie wordt gepubliceerd door de uitgever.
- Kan worden geïntegreerd met de status tests van toepassingen en de [uitbrei ding van de toepassings status](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md?context=/azure/virtual-machines/context/context).
- Werkt voor alle VM-grootten en voor zowel Windows-als Linux-installatie kopieën.
- U kunt op elk gewenst moment automatische upgrades afmelden (OS-upgrades kunnen ook hand matig worden gestart).
- De besturingssysteem schijf van een virtuele machine wordt vervangen door de nieuwe besturingssysteem schijf die is gemaakt met de nieuwste versie van de installatie kopie. Geconfigureerde extensies en aangepaste gegevens scripts worden uitgevoerd, terwijl persistente gegevens schijven behouden blijven.
- [Extensie volgorde bepaling](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md?context=/azure/virtual-machines/context/context) wordt ondersteund.
- Automatische upgrade van installatie kopie van besturings systeem kan worden ingeschakeld op een schaalset van elke grootte.


## <a name="automatic-vm-guest-patching-preview"></a>Automatische VM-gast patches (preview-versie)

Het inschakelen van [automatische VM-gast patches](automatic-vm-guest-patching.md) voor uw virtuele Azure-machines helpt het update beheer te vereenvoudigen door het veilig en automatisch bijwerken van virtuele machines voor het onderhouden van beveiligings naleving.

[Automatische VM-gast patching](automatic-vm-guest-patching.md) heeft de volgende kenmerken:
- Patches die zijn geclassificeerd als *kritiek* of *beveiliging* , worden automatisch gedownload en toegepast op de virtuele machine.
- Patches worden toegepast tijdens rustige uren in de tijd zone van de virtuele machine.
- Patch indeling wordt beheerd door Azure en patches worden toegepast na de [eerste principes van Beschik baarheid](automatic-vm-guest-patching.md#availability-first-patching).
- De status van de virtuele machine, zoals wordt bepaald door platform status signalen, wordt bewaakt om patch fouten te detecteren.
- Werkt voor alle VM-grootten.


## <a name="automatic-extension-upgrade-preview"></a>Upgrade van automatische extensie (preview-versie)

[Automatische extensie-upgrades](automatic-extension-upgrade.md) zijn beschikbaar als preview-versie van Azure Vm's en Azure virtual machine Scale sets. Als automatische extensie-upgrade is ingeschakeld op een VM of schaalset, wordt de extensie automatisch bijgewerkt wanneer de uitbrei ding Publisher een nieuwe versie voor die uitbrei ding uitgeeft.

 Automatische extensie-upgrade heeft de volgende functies:
- Ondersteund voor Azure-Vm's en Azure-Virtual Machine Scale Sets. Service Fabric Virtual Machine Scale Sets worden momenteel niet ondersteund.
- Upgrades worden toegepast in een Beschik baarheid-eerste implementatie model.
- Voor een Schaalset voor een virtuele machine wordt niet meer dan 20% van de virtuele machines van de schaalset in één batch bijgewerkt. De minimale Batch grootte is één virtuele machine.
- Werkt voor alle VM-grootten en voor Windows-en Linux-uitbrei dingen.
- U kunt op elk gewenst moment automatische upgrades afmelden.
- Automatische extensie-upgrades kunnen worden ingeschakeld voor een Virtual Machine Scale Sets van elke grootte.
- Elke ondersteunde uitbrei ding wordt afzonderlijk geregistreerd en u kunt kiezen welke extensies automatisch moeten worden bijgewerkt.
- Ondersteund in alle open bare Cloud regio's.

## <a name="hotpatch"></a>Hotpatch 

[Hotpatching](../automanage/automanage-hotpatch.md?context=/azure/virtual-machines/context/context) is een nieuwe manier om updates te installeren op nieuwe virtuele machines met Windows Server Azure Edition (vm's) die na de installatie niet opnieuw moeten worden opgestart. Hotpatch voor Windows Server Azure Edition Vm's biedt de volgende voor delen:

- Minder gevolgen voor de werk belasting met weinig opnieuw opstarten
- Snellere implementatie van updates omdat de pakketten kleiner zijn, sneller worden geïnstalleerd en de patch indeling met Azure update manager is vereenvoudigd
- Betere beveiliging, omdat de hotpatch-update pakketten zijn afgestemd op Windows-beveiligings updates die sneller worden geïnstalleerd zonder opnieuw op te starten


## <a name="azure-update-management"></a>Beheer van Azure update

U kunt [updatebeheer in azure Automation](../automation/update-management/overview.md?context=/azure/virtual-machines/context/context) gebruiken om updates van besturings systemen te beheren voor uw virtuele Windows-en Linux-machines in azure, in on-premises omgevingen en in andere Cloud omgevingen. U kunt snel de status van beschik bare updates op alle agent computers beoordelen en het proces voor het installeren van vereiste updates voor servers beheren.

## <a name="maintenance-control"></a>Onderhoudsbeheer

Platform updates beheren, die niet opnieuw moeten worden opgestart, met behulp van [onderhouds beheer](maintenance-control.md). Azure werkt de infra structuur regel matig bij om de betrouw baarheid, prestaties en beveiliging te verbeteren of nieuwe functies te starten. De meeste updates zijn transparant voor gebruikers. Sommige gevoelige werk belastingen, zoals games, mediastreaming en financiële trans acties, kunnen niet worden toegestaan tot zelfs enkele seconden van een VM-bevriezing of de verbinding met het beheer voor onderhoud. Onderhouds beheer biedt u de mogelijkheid te wachten op platform updates en deze toe te passen binnen een 35-daagse venster. 

Met de onderhouds controle kunt u bepalen wanneer u updates wilt Toep assen op uw geïsoleerde Vm's en voor Azure toegewezen hosts.

Met [onderhouds controle](maintenance-control.md)kunt u het volgende doen:
- Batch updates in één update pakket.
- Wacht tot 35 dagen om updates toe te passen. 
- Automatiseer platform updates door een onderhouds planning te configureren of door [Azure functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler)te gebruiken.
- Onderhouds configuraties werken in abonnementen en resource groepen. 


## <a name="scheduled-events"></a>Geplande gebeurtenissen

Scheduled Events is een Azure-Metadata Service die uw toepassings tijd biedt voor het voorbereiden van onderhoud van virtuele machines (VM). Het bevat informatie over aanstaande onderhouds gebeurtenissen (bijvoorbeeld opnieuw opstarten), zodat uw toepassing deze kan voorbereiden en de onderbreking kan beperken. Het is beschikbaar voor alle Azure-Virtual Machines typen, waaronder PaaS en IaaS in Windows en Linux. 

Zie [Scheduled Events voor Windows-vm's](./windows/scheduled-events.md) en [Scheduled Events voor Linux](./linux/scheduled-events.md) voor meer informatie over Scheduled Events

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de documentatie over [Beschik baarheid en schaal](availability.md) voor meer manieren om de uptime van uw toepassingen en services te verbeteren. 