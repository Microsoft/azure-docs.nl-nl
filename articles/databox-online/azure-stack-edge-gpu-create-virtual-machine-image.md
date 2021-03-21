---
title: VM-installatiekopiën maken voor uw GPU-apparaat in Azure Stack Edge Pro
description: Hierin wordt beschreven hoe u Linux- of Windows-VM-installatiekopieën maakt voor gebruik met uw GPU-apparaat in Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: ee0587063c0fac67068869c58471ada58354fab7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102437669"
---
# <a name="create-custom-vm-images-for-your-azure-stack-edge-pro-device"></a>Aangepaste VM-installatiekopiën maken voor uw Azure Stack Edge Pro-apparaat

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Om VM's te implementeren op uw Azure Stack Edge Pro-apparaat, moet u aangepaste VM-installatiekopieën kunnen maken om VM's mee te maken. In dit artikel worden de stappen beschreven die nodig zijn voor het maken van aangepaste Linux-of Windows VM-installatie kopieën die u kunt gebruiken om Vm's te implementeren op uw Azure Stack Edge Pro-apparaat.

## <a name="vm-image-workflow"></a>Werkstroom voor VM-installatiekopie

Voor deze werkstroom moet u een virtuele machine maken in Azure, de VM aanpassen, generaliseren en vervolgens de VHD downloaden die hoort bij de virtuele machine. Deze gegeneraliseerde VHD wordt geüpload naar Azure Stack Edge Pro. Er wordt een beheerde schijf gemaakt op basis van die VHD. Er wordt een installatie kopie gemaakt op basis van de beheerde schijf. En ten slotte worden virtuele machines gemaakt op basis van die installatie kopie.

Ga voor meer informatie naar [Een VM op uw Azure Stack Edge Pro-apparaat implementeren met behulp van Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).


## <a name="create-a-windows-custom-vm-image"></a>Een aangepaste VM-installatiekopie voor Windows maken

Voer de volgende stappen uit om een installatiekopie voor een Windows-VM te maken.

1. Maak een virtuele Windows-machine. Voor meer informatie gaat u naar [Zelfstudie: Windows-VM's maken en beheren met Azure PowerShell](../virtual-machines/windows/tutorial-manage-vm.md)

2. Download een bestaande besturingssysteemschijf.

    - Volg de stappen in [Een VHD downloaden](../virtual-machines/windows/download-vhd.md).

    - Gebruik de volgende `sysprep`-opdracht in plaats van wat wordt beschreven in de voorgaande procedure.
    
        `c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm`
   
       U kunt ook verwijzen naar [Overzicht van Sysprep (systeemvoorbereiding)](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Gebruik deze VHD om nu een virtuele machine te maken en te implementeren op uw Azure Stack Edge Pro-apparaat.

## <a name="create-a-linux-custom-vm-image"></a>Een aangepaste VM-installatiekopie voor Linux maken

Voer de volgende stappen uit om een installatiekopie voor een Linux-VM te maken.

1. Maak een virtuele Linux-machine. Voor meer informatie gaat u naar [Zelfstudie: Virtuele Linux-machines maken en beheren met de Azure CLI](../virtual-machines/linux/tutorial-manage-vm.md).

1. Maak de inrichting van de virtuele machine ongedaan. Gebruik de Azure VM-agent om computerspecifieke bestanden en gegevens te verwijderen. Gebruik de `waagent`-opdracht met de parameter `-deprovision+user` op uw virtuele Linux-machine. Zie [Informatie en gebruik Azure Linux-agent](../virtual-machines/extensions/agent-linux.md) voor meer informatie.

    1. Maak verbinding met uw virtuele Linux-machine met een SSH-client.
    2. Voer in het SSH-venster de volgende opdracht in:
       
        ```bash
        sudo waagent -deprovision+user
        ```
       > [!NOTE]
       > Voer deze opdracht alleen uit op een virtuele machine die u vastlegt als een installatiekopie. Met deze opdracht wordt niet gegarandeerd dat de installatiekopie van alle gevoelige informatie wordt gewist of geschikt is voor herdistributie. Met de parameter `+user` wordt ook het laatste ingerichte gebruikersaccount verwijderd. Gebruik alleen `-deprovision` om de referenties van het gebruikersaccount in de virtuele machine te blijven gebruiken.
     
    3. Voer **y** in om door te gaan. U kunt de parameter `-force` toevoegen om deze bevestigings stap te voorkomen.
    4. Nadat de opdracht is voltooid, voert u **afsluiten** in om de SSH-client te sluiten.  De virtuele machine wordt nog steeds uitgevoerd.


1. [Download een bestaande besturingssysteemschijf.](../virtual-machines/linux/download-vhd.md)

Gebruik deze VHD om nu een virtuele machine te maken en te implementeren op uw Azure Stack Edge Pro-apparaat. U kunt de volgende twee Azure Marketplace-installatiekopieën gebruiken om aangepaste Linux-installatiekopieën te maken:

|Itemnaam  |Description  |Publisher  |
|---------|---------|---------|
|[Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.ubuntuserver) |Ubuntu Server is de meest populaire Linux-distributie voor cloudomgevingen ter wereld.|Canonical|
|[Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.debian) |Debian GNU/Linux is een van de populairste Linux-distributies.     |credativ|

Ga voor een volledige lijst met installatiekopiën op de Azure Marketplace die kunnen werken (nog niet getest) naar [Azure Marketplace-items die beschikbaar zijn voor Azure Stack Hub](/azure-stack/operator/azure-stack-marketplace-azure-items?view=azs-1910&preserve-view=true).


## <a name="next-steps"></a>Volgende stappen

[Implementeer VM's op uw Azure Stack Edge-apparaat](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).