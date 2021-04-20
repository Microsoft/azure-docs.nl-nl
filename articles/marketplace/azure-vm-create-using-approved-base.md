---
title: Een azure VM-aanbieding (VM) maken op basis van een goedgekeurde basis, Azure Marketplace
description: Meer informatie over het maken van een aanbieding voor virtuele machines (VM's) op basis van een goedgekeurde basis.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 04/16/2021
ms.openlocfilehash: 19ae4b929964aaeb971bef75a2a620e40e4667f5
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727164"
---
# <a name="how-to-create-a-virtual-machine-using-an-approved-base"></a>Een virtuele machine maken met een goedgekeurde basis

In dit artikel wordt beschreven hoe u Azure gebruikt om een virtuele machine (VM) te maken die een vooraf geconfigureerd, goedgekeurd besturingssysteem bevat. Als dit niet compatibel is met uw oplossing, is het mogelijk om een [on-premises VM](azure-vm-create-using-own-image.md) te maken en te configureren met behulp van een goedgekeurd besturingssysteem.

> [!NOTE]
> Voordat u met deze procedure begint, bekijkt u de technische [vereisten](marketplace-virtual-machines.md#technical-requirements) voor Azure VM-aanbiedingen, waaronder vereisten voor virtuele harde schijven (VHD).

## <a name="select-an-approved-base-image"></a>Een goedgekeurde basisafbeelding selecteren

Selecteer een van de volgende Windows- of Linux-afbeeldingen als basis.

### <a name="windows"></a>Windows

- [Windows Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)
- SQL Server [2019,](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview) [2014,](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2014sp3-ws2012r2?tab=Overview) [2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2012sp4-ws2012r2?tab=Overview)

### <a name="linux"></a>Linux

Azure biedt een reeks goedgekeurde Linux-distributies. Zie Linux op [distributies](../virtual-machines/linux/endorsed-distros.md)die zijn goedgekeurd door Azure voor een actuele lijst.

## <a name="create-vm-on-the-azure-portal"></a>VM maken op de Azure Portal

1. Meld u aan bij [Azure Portal](https://ms.portal.azure.com/).
2. Selecteer **Virtuele machines**.
3. Selecteer **+ Toevoegen om** het scherm Een virtuele machine maken **te** openen.
4. Selecteer de afbeelding in de vervolgkeuzelijst of selecteer **Door** alle openbare en persoonlijke afbeeldingen bladeren om alle beschikbare virtuele-machine-afbeeldingen te zoeken of te doorzoeken.
5. Als u een **Gen 2-VM** wilt maken, gaat u naar **het tabblad Geavanceerd** en selecteert u de optie Gen **2.**

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Selecteer Gen 1 of Gen 2.":::

6. Selecteer de grootte van de VM die u wilt implementeren.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Selecteer een aanbevolen VM-grootte voor de geselecteerde afbeelding.":::

7. Geef de overige vereiste gegevens op om de VM te maken.
8. Selecteer **Beoordelen en maken om** uw keuzes te controleren. Wanneer het **bericht Validatie** geslaagd wordt weergegeven, selecteert u **Maken.**

Azure begint met het inrichten van de virtuele machine die u hebt opgegeven. Houd de voortgang bij door het tabblad **Virtual Machines** selecteren in het menu links. Nadat de virtuele machine is gemaakt, verandert de status van Virtuele machine in **Wordt uitgevoerd.**

## <a name="configure-the-vm"></a>De virtuele machine configureren

In deze sectie wordt beschreven hoe u een Azure-VM kunt formattiseren, bijwerken en generaliseren. Deze stappen zijn nodig om uw VM voor te bereiden voor de Azure Marketplace.

### <a name="connect-to-your-vm"></a>Verbinding maken met uw VM

Raadpleeg de volgende documentatie om verbinding te maken met uw [Windows-](../virtual-machines/windows/connect-logon.md) of [Linux-VM.](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm)

### <a name="install-the-most-current-updates"></a>De meest recente updates installeren

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>Aanvullende beveiligingscontroles uitvoeren

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Aangepaste configuratie- en geplande taken uitvoeren

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Volgende stappen

- Aanbevolen volgende stap: [Test uw VM-afbeelding om](azure-vm-image-test.md) te controleren of deze voldoet Azure Marketplace publicatievereisten. Dit is optioneel.
- Als u uw VM-afbeelding niet wilt testen, meld u zich dan aan [bij](https://partner.microsoft.com/) Partner Center om uw afbeelding te publiceren.
- Als u problemen ondervindt bij het maken van uw nieuwe VHD op basis van Azure, bekijkt u veelgestelde vragen [over VM'Azure Marketplace](azure-vm-create-faq.md).
