---
title: Problemen oplossen met het implementeren van problemen met virtuele Linux-machines in azure | Microsoft Docs
description: Problemen met het implementeren van problemen met virtuele Linux-machines in het Azure Resource Manager-implementatie model oplossen.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: cf8fb383625ec2752264d6e5a70d8625f06689fc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022804"
---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Problemen bij het implementeren van virtuele Linux-machines in Azure oplossen

Voor het oplossen van problemen met de implementatie van virtuele machines (VM) in azure, raadpleegt u de [meest voorkomende problemen](#top-issues) met veelvoorkomende fouten en oplossingen.

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.

## <a name="top-issues"></a>Belangrijkste problemen
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>Het cluster kan de aangevraagde VM-grootte niet ondersteunen
\<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Voer de aanvraag opnieuw uit met een kleinere VM-grootte.
- Als de grootte van de aangevraagde virtuele machine niet kan worden gewijzigd:
    - Stop alle virtuele machines in de beschikbaarheidsset. Klik op **resource groepen** > de resource groep > **resources** > uw beschikbaarheidsset > **virtual machines** > uw virtuele machine > **gestopt**.
    - Nadat alle virtuele machines zijn gestopt, maakt u de virtuele machine in de gewenste grootte.
    - Start eerst de nieuwe VM en selecteer vervolgens elk van de gestopte Vm's en klik op Start.


## <a name="the-cluster-does-not-have-free-resources"></a>Het cluster heeft geen vrije resources
\<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Voer de aanvraag later opnieuw uit.
- Als de nieuwe VM deel kan uitmaken van een andere beschikbaarheidsset
    - Maak een virtuele machine in een andere beschikbaarheidsset (in dezelfde regio).
    - Voeg de nieuwe virtuele machine toe aan hetzelfde virtuele netwerk.

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Hoe kan ik mijn maandelijkse tegoed voor Visual Studio Enter prise (BizSpark) activeren

Als u uw maandelijkse tegoed wilt activeren, raadpleegt u dit [artikel](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>Waarom kan ik het GPU-stuur programma voor een Ubuntu NV-VM niet installeren?

Linux GPU-ondersteuning is momenteel alleen beschikbaar op Azure NC-Vm's waarop Ubuntu Server 16,04 LTS wordt uitgevoerd. Zie [GPU-Stuur Programma's instellen voor vm's met de N-serie waarop Linux wordt uitgevoerd](../linux/n-series-driver-setup.md)voor meer informatie.

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>De Stuur Programma's ontbreken voor de VM van mijn Linux N-serie

Instructies voor het installeren van Stuur Programma's voor Vm's op basis van Linux vindt u [hier](../sizes-gpu.md#supported-operating-systems-and-drivers).

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Ik kan geen GPU-exemplaar vinden in mijn N-serie-VM

Als u gebruik wilt maken van de GPU-mogelijkheden van Vm's uit de Azure N-serie, moet u na de implementatie grafische Stuur Programma's installeren op elke VM. Informatie over stuur programma-installatie is [hier](../sizes-gpu.md#supported-operating-systems-and-drivers)beschikbaar.

## <a name="are-n-series-vms-available-in-my-region"></a>Zijn er Vm's uit de N-serie beschikbaar in mijn regio?

U kunt de beschik baarheid controleren vanuit de [tabel met beschik bare producten per regio](https://azure.microsoft.com/regions/services)en [de prijzen.](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series)

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Ik kan de serie van de VM-grootte niet zien die ik wil bij het wijzigen van de grootte van mijn VM.

Wanneer een virtuele machine wordt uitgevoerd, wordt deze geïmplementeerd op een fysieke server. De fysieke servers in azure-regio's worden gegroepeerd in clusters van algemene fysieke hardware. Het wijzigen van de grootte van een virtuele machine die vereist dat de VM naar verschillende hardwareconfiguraties wordt verplaatst, is afhankelijk van het implementatie model dat is gebruikt voor het implementeren van de virtuele machine.

- Vm's die zijn geïmplementeerd in het klassieke implementatie model, moet de Cloud service-implementatie worden verwijderd en opnieuw worden geïmplementeerd om de Vm's te wijzigen in een grootte in een andere grootte familie.

- Vm's die zijn geïmplementeerd in het Resource Manager-implementatie model, moet u alle virtuele machines in de beschikbaarheidsset stoppen voordat u de grootte van een virtuele machine in de beschikbaarheidsset wijzigt.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>De vermelde VM-grootte wordt niet ondersteund tijdens de implementatie in de Beschikbaarheidsset.

Kies een grootte die wordt ondersteund op het cluster van de beschikbaarheidsset. Het is raadzaam om een beschikbaarheidsset te maken om de grootste VM-grootte te kiezen die u nodig hebt en die uw eerste implementatie voor de Beschikbaarheidsset moet zijn.

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>Welke Linux-distributies/-versies worden ondersteund in azure?

U kunt de lijst op Linux vinden in door [Azure goedgekeurde distributies](../linux/endorsed-distros.md).

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Kan ik een bestaande klassieke virtuele machine toevoegen aan een beschikbaarheidsset?

Ja. U kunt een bestaande klassieke virtuele machine toevoegen aan een nieuwe of bestaande Beschikbaarheidsset. Zie [een bestaande virtuele machine toevoegen aan een beschikbaarheidsset](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine)voor meer informatie.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>Volgende stappen
Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/).

U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.
