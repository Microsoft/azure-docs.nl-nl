---
title: Problemen met het implementeren van problemen met virtuele Windows-machines in azure oplossen | Microsoft Docs
description: Problemen met het implementeren van problemen met virtuele Windows-machines in het Azure Resource Manager-implementatie model oplossen.
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
ms.openlocfilehash: 98a16e0a60ddf149e8f0e1a092051f3e98ea8225
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627059"
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Problemen bij het implementeren van virtuele Windows-machines in Azure oplossen

Voor het oplossen van problemen met de implementatie van virtuele machines (VM) in azure, raadpleegt u de [meest voorkomende problemen](#top-issues) met veelvoorkomende fouten en oplossingen.

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.

## <a name="top-issues"></a>Belangrijkste problemen
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

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

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Hoe kan ik een Windows-client installatie kopie gebruiken en implementeren in azure?

U kunt Windows 7, Windows 8 of Windows 10 gebruiken in azure voor ontwikkel-en test scenario's als u een geschikt MSDN-abonnement (Visual Studio) hebt. In dit [artikel](../windows/client-images.md) vindt u een overzicht van de vereisten voor het uitvoeren van Windows-client in Azure en het gebruik van de installatie kopieën van Azure galerie.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Hoe kan ik een virtuele machine implementeren met behulp van het hybride gebruiks voordeel (HUB)?

Er zijn een aantal verschillende manieren om virtuele Windows-machines te implementeren met de Azure Hybrid Use Benefit.

Voor een Enterprise Overeenkomst-abonnement:

• Implementeer Vm's vanaf specifieke Marketplace-installatie kopieën die vooraf zijn geconfigureerd met Azure Hybrid Use Benefit.

Voor Enter prise Agreement:

• Upload een aangepaste VM en implementeer deze met behulp van een resource manager-sjabloon of Azure PowerShell.

Zie de volgende resources voor meer informatie:

 - [Overzicht van Azure Hybrid Use Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [Veelgestelde vragen over downloaden](https://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Azure Hybrid use Benefit voor Windows Server en Windows-client](../windows/hybrid-use-benefit-licensing.md).

 - [Hoe kan ik het voor deel voor hybride gebruik in azure gebruiken?](/archive/blogs/azureedu/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Hoe kan ik mijn maandelijkse tegoed voor Visual Studio Enter prise (BizSpark) activeren

Als u uw maandelijkse tegoed wilt activeren, raadpleegt u dit [artikel](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Enterprise Dev/Test toevoegen aan mijn Enterprise Overeenkomst (EA) om toegang te krijgen tot Window-client installatie kopieën?

De mogelijkheid om abonnementen te maken op basis van de Enterprise Dev/Test aanbieding is beperkt tot account eigenaren die toestemming hebben gekregen door een ondernemings beheerder. De eigenaar van het account maakt abonnementen via de Azure-account Portal en moet vervolgens actieve Visual Studio-abonnees als co-beheerder toevoegen. Zodat ze de benodigde bronnen voor ontwikkelen en testen kunnen beheren en gebruiken. Zie [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) voor meer informatie.

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>Mijn stuur Programma's ontbreken voor mijn VM van de Windows N-serie

Instructies voor het installeren van Stuur Programma's voor op Windows gebaseerde Vm's vindt u [hier](../sizes-gpu.md#supported-operating-systems-and-drivers).

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Ik kan geen GPU-exemplaar vinden in mijn N-serie-VM

Als u gebruik wilt maken van de GPU-mogelijkheden van Vm's uit de Azure N-serie, moet u na de implementatie grafische Stuur Programma's installeren op elke VM. Informatie over stuur programma-installatie is [hier](../sizes-gpu.md#supported-operating-systems-and-drivers)beschikbaar.

## <a name="are-n-series-vms-available-in-my-region"></a>Zijn er Vm's uit de N-serie beschikbaar in mijn regio?

U kunt de beschik baarheid controleren vanuit de [tabel met beschik bare producten per regio](https://azure.microsoft.com/regions/services)en [de prijzen.](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series)

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>Welke client installatie kopieën kan ik gebruiken en implementeren in azure, en hoe krijg ik deze?

U kunt Windows 7, Windows 8 of Windows 10 gebruiken in azure voor ontwikkel-en test scenario's waarin u hebt gebruikgemaakt van een geschikt MSDN-abonnement (Visual Studio). 

- Windows 10-installatie kopieën zijn beschikbaar in de Azure Gallery binnen [in aanmerking komende ontwikkel-en test aanbiedingen](../windows/client-images.md). 
- Visual Studio-abonnees binnen elk type aanbieding kunnen ook adequaat een 64-bits Windows 7-, Windows 8-of Windows 10-installatie kopie [voorbereiden en maken](../windows/prepare-for-upload-vhd-image.md) en vervolgens [uploaden naar Azure](../windows/upload-generalized-managed.md). Het gebruik blijft beperkt tot dev/test door actieve Visual Studio-abonnees.

In dit [artikel](../windows/client-images.md) vindt u een overzicht van de vereisten voor het uitvoeren van Windows-client in Azure en het gebruik van de installatie kopieën van Azure galerie.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Ik kan de serie van de VM-grootte niet zien die ik wil bij het wijzigen van de grootte van mijn VM.

Wanneer een virtuele machine wordt uitgevoerd, wordt deze geïmplementeerd op een fysieke server. De fysieke servers in azure-regio's worden gegroepeerd in clusters van algemene fysieke hardware. Het wijzigen van de grootte van een virtuele machine die vereist dat de VM naar verschillende hardwareconfiguraties wordt verplaatst, is afhankelijk van het implementatie model dat is gebruikt voor het implementeren van de virtuele machine.

- Vm's die zijn geïmplementeerd in het klassieke implementatie model, moet de Cloud service-implementatie worden verwijderd en opnieuw worden geïmplementeerd om de Vm's te wijzigen in een grootte in een andere grootte familie.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

- Vm's die zijn geïmplementeerd in het Resource Manager-implementatie model, moet u alle virtuele machines in de beschikbaarheidsset stoppen voordat u de grootte van een virtuele machine in de beschikbaarheidsset wijzigt.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>De vermelde VM-grootte wordt niet ondersteund tijdens de implementatie in de Beschikbaarheidsset.

Kies een grootte die wordt ondersteund op het cluster van de beschikbaarheidsset. Het is raadzaam om een beschikbaarheidsset te maken om de grootste VM-grootte te kiezen die u nodig hebt en die uw eerste implementatie voor de Beschikbaarheidsset moet zijn.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Kan ik een bestaande klassieke virtuele machine toevoegen aan een beschikbaarheidsset?

Ja. U kunt een bestaande klassieke virtuele machine toevoegen aan een nieuwe of bestaande Beschikbaarheidsset. Zie [een bestaande virtuele machine toevoegen aan een beschikbaarheidsset](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine)voor meer informatie.


## <a name="next-steps"></a>Volgende stappen
Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/).

U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.
