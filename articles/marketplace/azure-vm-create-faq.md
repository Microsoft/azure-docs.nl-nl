---
title: Veelgestelde vragen over VM in azure Marketplace
description: Er zijn algemene vragen aangetroffen bij het maken van een virtuele machine in azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: d045af3b170d585b4bf1f8c57b7ba924c6b30695
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489783"
---
# <a name="common-questions-about-vm-in-azure-marketplace"></a>Veelgestelde vragen over VM in azure Marketplace

Deze veelgestelde vragen zijn van belang voor veelvoorkomende problemen die kunnen optreden bij het maken van een virtuele machine (VM)-aanbieding in azure Marketplace.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Hoe kan ik een virtueel particulier netwerk (VPN) configureren om met mijn Vm's te werken?

Als u het Azure Resource Manager-implementatie model gebruikt, hebt u drie opties:

- [Een op een route gebaseerde VPN-gateway maken met behulp van de Azure Portal](../vpn-gateway/tutorial-create-gateway-portal.md)
- [Een op een route gebaseerde VPN-gateway maken met behulp van Azure PowerShell](../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
- [Een op een route gebaseerde VPN-gateway maken met behulp van CLI](../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Wat is micro soft-ondersteunings beleid voor het uitvoeren van micro soft-server software op Vm's op basis van Azure?

Meer informatie vindt u op de [micro soft-server software ondersteuning voor Microsoft Azure virtuele machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Hoe kan ik in een virtuele machine de aangepaste script extensie in de opstart taak beheren?

Zie [aangepaste script extensie voor Windows](../virtual-machines/extensions/custom-script-windows.md)voor meer informatie over het gebruik van de aangepaste script extensie met behulp van de module Azure PowerShell, Azure Resource Manager sjablonen en stappen voor probleem oplossing op Windows-systemen.

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Worden 32-bits toepassingen of services ondersteund in azure Marketplace?

Nee. De ondersteunde besturings systemen en standaard services voor virtuele Azure-machines zijn allemaal 64 bits. Hoewel de meeste 64-bits besturings systemen 32-bits versies van toepassingen ondersteunen voor achterwaartse compatibiliteit, is het gebruik van 32-bits toepassingen als onderdeel van uw VM-oplossing niet-ondersteund en sterk afgeraden. Maak uw toepassing opnieuw als een 64-bits project.

Raadpleeg deze artikelen voor meer informatie:

- [Het uitvoeren van 32-bits toepassingen](/windows/desktop/WinProg64/running-32-bit-applications)
- [Ondersteuning voor 32-bits besturingssystemen in virtuele Azure-machines](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Ondersteuning van Microsoft-serversoftware voor virtuele Microsoft Azure-machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Fout: VHD is al geregistreerd bij de opslag plaats van de installatie kopie als de resource

Telkens wanneer ik een installatie kopie van mijn Vhd's probeer te maken, krijg ik de fout ' VHD is al geregistreerd bij de opslag plaats van de installatie kopie als de resource ' in Azure PowerShell. Ik heb geen installatie kopie gemaakt vóór en ik heb een installatie kopie met deze naam in azure gevonden. Hoe los ik dit op?

Dit probleem wordt doorgaans weer gegeven als u een virtuele machine hebt gemaakt op basis van een virtuele harde schijf met een vergren deling. Controleer of er geen VM is toegewezen van deze VHD en voer de bewerking opnieuw uit. Als dit probleem zich blijft voordoen, opent u een ondersteunings ticket. Zie [ondersteuning voor Partner Center](support.md).

## <a name="how-do-i-test-a-hidden-preview-image"></a>Hoe kan ik een verborgen voorbeeld afbeelding testen?

U kunt verborgen preview-installatie kopieën implementeren met Quick Start-sjablonen.
Als u een Linux-voorbeeld installatie kopie wilt implementeren, 
1. Ga naar deze [sjabloon voor snel starten](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)en selecteer implementeren naar Azure. U moet Azure Portal
2. Selecteer in Azure Portal de optie sjabloon bewerken.
3. In de JSON-sjabloon zoekt u naar imageReference en werkt u de publisherid, offerid, skuId en versie van de installatie kopie bij. Als u de voorbeeld afbeelding wilt testen, voegt u '-PREVIEW ' toe aan de offerid.
 ![afbeelding](https://user-images.githubusercontent.com/79274470/110191995-71c7d500-7de0-11eb-9f3c-6a42f55d8f03.png)
4. Klik op Opslaan
5. Vul de rest van de details in. Controleren en maken



## <a name="next-steps"></a>Volgende stappen

- [Problemen oplossen met VM-certificering](azure-vm-create-certification-faq.md)
