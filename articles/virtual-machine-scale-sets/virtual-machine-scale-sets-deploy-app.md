---
title: Een toepassing implementeren in een schaalset voor virtuele Azure-machines
description: Meer informatie over het implementeren van toepassingen voor virtuele Linux-en Windows-machines in een schaalset
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurepowershell
ms.openlocfilehash: e7a4ddaf74df6e04c1597b9c106cd458ddebac55
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89079588"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Uw toepassing implementeren op virtuele-machineschaalsets

Als u toepassingen wilt uitvoeren op de exemplaren van een virtuele machine (VM) in een schaalset, moet u eerst de toepassingsonderdelen en de vereiste bestanden installeren. In dit artikel worden manieren beschreven om een aangepaste VM-installatie kopie te maken voor instanties in een schaalset, of om automatisch installatie scripts uit te voeren op bestaande VM-exemplaren. U leert ook hoe u updates van toepassingen of besturings systemen kunt beheren in een schaalset.


## <a name="build-a-custom-vm-image"></a>Een aangepaste VM-installatie kopie maken
Wanneer u een van de installatie kopieën van het Azure-platform gebruikt om de instanties in uw schaalset te maken, wordt er geen extra software geïnstalleerd of geconfigureerd. U kunt de installatie van deze onderdelen automatiseren, maar voegt ook toe aan de tijd die nodig is om VM-exemplaren in te richten op uw schaal sets. Als u veel configuratie wijzigingen toepast op de VM-exemplaren, is er beheer overhead met die configuratie scripts en-taken.

Om het configuratie beheer en de tijd voor het inrichten van een virtuele machine te beperken, kunt u een aangepaste VM-installatie kopie maken die gereed is voor het uitvoeren van uw toepassing zodra een exemplaar is ingericht in de schaalset. Zie de volgende zelf studies voor meer informatie over het maken en gebruiken van een aangepaste VM-installatie kopie met een schaalset:

- [Azure-CLI](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="install-an-app-with-the-custom-script-extension"></a><a name="already-provisioned"></a>Een app met de aangepaste script extensie installeren
Met de aangepaste scriptextensie kunnen scripts worden gedownload en uitgevoerd op virtuele machines in Azure. Deze uitbreiding is handig voor post-implementatieconfiguraties, software-installaties of andere configuratie-/beheertaken. Scripts kunnen worden gedownload uit Azure Storage of GitHub, of worden geleverd in Azure Portal tijdens de uitvoering van extensies. Zie de volgende zelf studies voor meer informatie over het installeren van een app met een aangepaste script extensie:

- [Azure-CLI](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Azure Resource Manager-sjabloon](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Een app installeren op een Windows-VM met Power shell DSC
[Power shell desired state Configuration (DSC)](/powershell/scripting/dsc/overview/overview) is een beheer platform voor het definiëren van de configuratie van doel computers. DSC-configuraties bepalen wat er op een machine moet worden geïnstalleerd en hoe de host moet worden geconfigureerd. Een lokale Configuration Manager (LCM)-engine wordt uitgevoerd op elk doel knooppunt dat gevraagde acties verwerkt op basis van gepushte configuraties.

Met de Power shell DSC-extensie kunt u VM-exemplaren in een schaalset aanpassen met Power shell. Het volgende voor beeld:

- Hiermee wordt de VM-instantie geïnstrueerd een DSC-pakket te downloaden van GitHub- *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Hiermee stelt u de uitbrei ding voor het uitvoeren van een installatie script- `configure-http.ps1`
- Haalt informatie op over een schaalset met [Get-AzVmss](/powershell/module/az.compute/get-azvmss)
- Hiermee wordt de extensie toegepast op de VM [-exemplaren met update-AzVmss](/powershell/module/az.compute/update-azvmss)

De DSC-extensie wordt toegepast op de *myScaleSet* VM-exemplaren in de resource groep met de naam *myResourceGroup*. Voer uw eigen namen als volgt in:

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Get information about the scale set
$vmss = Get-AzVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

Als het upgrade beleid voor uw schaalset *hand matig* is, werkt u uw VM-exemplaren bij met [Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance). Met deze cmdlet wordt de bijgewerkte configuratie van de schaalset toegepast op de VM-exemplaren en wordt uw toepassing geïnstalleerd.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Een app installeren op een virtuele Linux-machine met Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) is een veelgebruikte benadering voor het aanpassen van een Linux-VM als deze voor de eerste keer wordt opgestart. U kunt cloud-init gebruiken voor het installeren van pakketten en schrijven van bestanden, of om gebruikers en beveiliging te configureren. Als de initialisatie van de cloud-init wordt uitgevoerd tijdens het opstartproces, zijn er geen extra stappen of agents vereist om uw configuratie toe te passen.

Cloud-init werkt ook in distributies. U gebruikt bijvoorbeeld niet **apt-get install** of **yum install** om een pakket te installeren. In plaats daarvan kunt u een lijst definiëren met te installeren pakketten. Cloud-init maakt automatisch gebruik van het hulpprogramma voor systeemeigen pakketbeheer voor de distro die u selecteert.

Zie [Cloud-init gebruiken voor het aanpassen van Azure-vm's](../virtual-machines/linux/using-cloud-init.md)voor meer informatie, inclusief een voor beeld van een *cloud-init.txt* bestand.

Als u een schaalset wilt maken en een Cloud-init-bestand wilt gebruiken, voegt `--custom-data` u de para meter toe aan de opdracht [AZ vmss Create](/cli/azure/vmss) en geeft u de naam van een Cloud-init-bestand op. In het volgende voor beeld wordt een schaalset gemaakt met de naam *myScaleSet* in *MYRESOURCEGROUP* en worden VM-exemplaren geconfigureerd met een bestand met de naam *cloud-init.txt*. Voer uw eigen namen als volgt in:

```azurecli
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```


### <a name="install-applications-with-os-updates"></a>Toepassingen installeren met updates van het besturings systeem
Wanneer er nieuwe versies van het besturings systeem beschikbaar zijn, kunt u een nieuwe aangepaste installatie kopie gebruiken of bouwen en upgrades voor het [besturings systeem implementeren](virtual-machine-scale-sets-upgrade-scale-set.md) naar een schaalset. Elk VM-exemplaar wordt bijgewerkt naar de meest recente installatie kopie die u opgeeft. U kunt een aangepaste installatie kopie met de vooraf geïnstalleerde toepassing, de aangepaste script extensie of Power shell DSC gebruiken om uw toepassing automatisch beschikbaar te stellen tijdens het uitvoeren van de upgrade. U moet mogelijk het onderhoud van de toepassing plannen terwijl u dit proces uitvoert om ervoor te zorgen dat er geen compatibiliteits problemen met versie zijn.

Als u een aangepaste VM-installatie kopie gebruikt waarop de toepassing vooraf is geïnstalleerd, kunt u de toepassings updates integreren met een implementatie pijplijn om de nieuwe installatie kopieën te bouwen en upgrades voor het besturings systeem te implementeren in de schaalset. Met deze aanpak kan de pijp lijn de nieuwste toepassings builds ophalen, een VM-installatie kopie maken en valideren en vervolgens de VM-exemplaren in de schaalset bijwerken. Als u een implementatie pijplijn wilt uitvoeren die toepassings updates bouwt en implementeert op aangepaste VM-installatie kopieën, kunt u [een installatie kopie van een pakket maken en implementeren met Azure DevOps Services](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset), of een ander platform gebruiken zoals [Spinnaker](https://www.spinnaker.io/) of [Jenkins](https://jenkins.io/).


## <a name="next-steps"></a>Volgende stappen
Wanneer u toepassingen op uw schaal sets bouwt en implementeert, kunt u het [ontwerp overzicht van de schaalset](virtual-machine-scale-sets-design-overview.md)bekijken. Zie [Power shell gebruiken voor het beheren van uw schaalset](./virtual-machine-scale-sets-manage-powershell.md)voor meer informatie over het beheren van uw schaalset.
