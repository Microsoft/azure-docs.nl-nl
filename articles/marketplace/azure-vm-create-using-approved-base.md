---
title: Een Azure virtual machine-aanbieding (VM) maken van een goedgekeurde basis, Azure Marketplace
description: Meer informatie over het maken van een virtuele machine (VM)-aanbieding vanuit een goedgekeurde basis.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 94d21cb82290e59ebb003969a566c1bfc877713e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200451"
---
# <a name="how-to-create-a-virtual-machine-using-an-approved-base"></a>Een virtuele machine maken met behulp van een goedgekeurde basis

In dit artikel wordt beschreven hoe u Azure gebruikt om een virtuele machine (VM) te maken die een vooraf geconfigureerd, goedgekeurd besturings systeem bevat. Als dit niet compatibel is met uw oplossing, is het mogelijk [een on-premises VM te maken en te configureren](azure-vm-create-using-own-image.md) met behulp van een goedgekeurd besturings systeem.

> [!NOTE]
> Voordat u met deze procedure begint, controleert u de [technische vereisten](marketplace-virtual-machines.md#technical-requirements) voor Azure VM-aanbiedingen, inclusief vereisten voor virtuele harde schijven (VHD).

## <a name="select-an-approved-base-image"></a>Een goedgekeurde basis installatie kopie selecteren

Selecteer een van de volgende Windows-of Linux-installatie kopieën als basis.

### <a name="windows"></a>Windows

- [Windows Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)
- SQL Server [2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [2014](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2014sp3-ws2012r2?tab=Overview), [2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2012sp4-ws2012r2?tab=Overview)

### <a name="linux"></a>Linux

Azure biedt een scala aan goedgekeurde Linux-distributies. Zie voor een actuele lijst [Linux op distributies die zijn goedgekeurd door Azure](../virtual-machines/linux/endorsed-distros.md).

## <a name="create-vm-on-the-azure-portal"></a>Een VM maken op het Azure Portal

1. Meld u aan bij [Azure Portal](https://ms.portal.azure.com/).
2. Selecteer **Virtuele machines**.
3. Selecteer **+ toevoegen** om het scherm **een virtuele machine maken** te openen.
4. Selecteer de installatie kopie in de vervolg keuzelijst of selecteer **Bladeren alle open bare en persoonlijke installatie kopieën** om te zoeken of door alle beschik bare installatie kopieën van virtuele machines te bladeren.
5. Als u een virtuele machine van **generatie 2** wilt maken, gaat u naar het tabblad **Geavanceerd** en selecteert u de optie **generatie 2** .

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Selecteer gen 1 of gen 2.":::

6. Selecteer de grootte van de virtuele machine die u wilt implementeren.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Selecteer een aanbevolen VM-grootte voor de geselecteerde installatie kopie.":::

7. Geef de overige vereiste gegevens op om de virtuele machine te maken.
8. Selecteer **beoordeling + maken** om uw keuzes te controleren. Wanneer het bericht **door gegeven validatie** wordt weer gegeven, selecteert u  **maken**.

Azure begint met het inrichten van de virtuele machine die u hebt opgegeven. Volg de voortgang van de taak door het tabblad **virtual machines** te selecteren in het menu links. Nadat deze is gemaakt, wordt de status van de virtuele machine gewijzigd in **actief**.

## <a name="configure-the-vm"></a>De virtuele machine configureren

In deze sectie wordt beschreven hoe u een Azure-VM kunt verg Roten of verkleinen, bijwerken en generaliseren. Deze stappen zijn nodig om uw VM voor te bereiden voor implementatie op Azure Marketplace.

### <a name="connect-to-your-vm"></a>Verbinding maken met uw VM

Raadpleeg de volgende documentatie om verbinding te maken met uw virtuele [Windows](../virtual-machines/windows/connect-logon.md) -of [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) -machine.

### <a name="install-the-most-current-updates"></a>De meest recente updates installeren

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>Aanvullende beveiligings controles uitvoeren

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Aangepaste configuratie en geplande taken uitvoeren

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Volgende stappen

- Aanbevolen volgende stap: [Test uw VM-installatie kopie](azure-vm-image-test.md) om te controleren of deze voldoet aan de publicatie vereisten voor Azure Marketplace. Dit is optioneel.
- Als u uw VM-installatie kopie niet wilt testen, meldt u zich aan bij [Partner Center](https://partner.microsoft.com/) om uw installatie kopie te publiceren.
- Zie [Veelgestelde vragen over vm's voor Azure Marketplace](azure-vm-create-faq.md)als u problemen ondervindt bij het maken van uw nieuwe VHD op basis van Azure.
