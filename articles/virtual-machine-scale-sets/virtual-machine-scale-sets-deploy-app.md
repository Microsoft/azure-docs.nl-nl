---
title: Een toepassing implementeren in een virtuele-machineschaalset van Azure
description: Meer informatie over het implementeren van toepassingen in linux- en Windows-exemplaren van virtuele machines in een schaalset
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 078c78f9fe9e52ee2a71784d5c5ae5c2a478fbe4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484251"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Uw toepassing implementeren op virtuele-machineschaalsets

Als u toepassingen wilt uitvoeren op de exemplaren van een virtuele machine (VM) in een schaalset, moet u eerst de toepassingsonderdelen en de vereiste bestanden installeren. In dit artikel worden manieren beschreven voor het bouwen van een aangepaste VM-installatie afbeelding voor exemplaren in een schaalset of het automatisch uitvoeren van installatiescripts op bestaande VM-exemplaren. U leert ook hoe u toepassings- of besturingssysteemupdates beheert in een schaalset.


## <a name="build-a-custom-vm-image"></a>Een aangepaste VM-afbeelding bouwen
Wanneer u een van de installatieprogramma's van het Azure-platform gebruikt om de exemplaren in uw schaalset te maken, wordt er geen extra software geïnstalleerd of geconfigureerd. U kunt de installatie van deze onderdelen automatiseren, maar dat zorgt voor extra tijd voor het inrichten van VM-exemplaren voor uw schaalsets. Als u veel configuratiewijzigingen op de VM-exemplaren wilt toepassen, is er beheeroverhead met deze configuratiescripts en -taken.

Als u het configuratiebeheer en de tijd voor het inrichten van een VM wilt verminderen, kunt u een aangepaste VM-installatiebestand maken dat klaar is om uw toepassing uit te voeren zodra er een exemplaar is ingericht in de schaalset. Zie de volgende zelfstudies voor meer informatie over het maken en gebruiken van een aangepaste VM-afbeelding met een schaalset:

- [Azure-CLI](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="install-an-app-with-the-custom-script-extension"></a><a name="already-provisioned"></a>Een app installeren met de aangepaste scriptextensie
Met de aangepaste scriptextensie kunnen scripts worden gedownload en uitgevoerd op virtuele machines in Azure. Deze uitbreiding is handig voor post-implementatieconfiguraties, software-installaties of andere configuratie-/beheertaken. Scripts kunnen worden gedownload uit Azure Storage of GitHub, of worden geleverd in Azure Portal tijdens de uitvoering van extensies. Zie de volgende zelfstudies voor meer informatie over het installeren van een app met een aangepaste scriptextensie:

- [Azure-CLI](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Azure Resource Manager-sjabloon](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Een app installeren op een Windows-VM met PowerShell DSC
[PowerShell Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) is een beheerplatform voor het definiëren van de configuratie van doelmachines. DSC-configuraties definiëren wat er op een computer moet worden geïnstalleerd en hoe de host moet worden geconfigureerd. Een LCM Configuration Manager-engine (Local Configuration Manager) wordt uitgevoerd op elk doel-knooppunt dat aangevraagde acties verwerkt op basis van pushconfiguraties.

Met de PowerShell DSC-extensie kunt u VM-exemplaren in een schaalset aanpassen met PowerShell. Het volgende voorbeeld:

- Geeft de VM-exemplaren de opdracht om een DSC-pakket te downloaden van GitHub - *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Hiermee stelt u de extensie voor het uitvoeren van een installatiescript - `configure-http.ps1`
- Haalt informatie op over een schaalset [met Get-AzVmss](/powershell/module/az.compute/get-azvmss)
- De extensie wordt toegepast op de VM-exemplaren met [Update-AzVmss](/powershell/module/az.compute/update-azvmss)

De DSC-extensie wordt toegepast op de VM-exemplaren *myScaleSet* in de resourcegroep met de *naam myResourceGroup.* Voer als volgt uw eigen namen in:

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

Als het upgradebeleid voor uw schaalset *handmatig* is, werkt u uw VM-exemplaren bij [met Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance). Met deze cmdlet wordt de bijgewerkte schaalsetconfiguratie toegepast op de VM-exemplaren en wordt uw toepassing geïnstalleerd.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Een app installeren op een Linux-VM met cloud-init
[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) is een veelgebruikte benadering voor het aanpassen van een Linux-VM als deze voor de eerste keer wordt opgestart. U kunt cloud-init gebruiken voor het installeren van pakketten en schrijven van bestanden, of om gebruikers en beveiliging te configureren. Als de initialisatie van de cloud-init wordt uitgevoerd tijdens het opstartproces, zijn er geen extra stappen of agents vereist om uw configuratie toe te passen.

Cloud-init werkt ook in distributies. U gebruikt bijvoorbeeld niet **apt-get install** of **yum install** om een pakket te installeren. In plaats daarvan kunt u een lijst definiëren met te installeren pakketten. Cloud-init maakt automatisch gebruik van het hulpprogramma voor systeemeigen pakketbeheer voor de distro die u selecteert.

Zie Cloud-init  gebruiken om Virtuele Azure-cloud-init.txtaan te passen voor meer informatie, waaronder een voorbeeld van [eencloud-init.txtbestand.](../virtual-machines/linux/using-cloud-init.md)

Als u een schaalset wilt maken en een cloud-init-bestand wilt gebruiken, voegt u de parameter toe aan de opdracht az vmss create en geeft u de naam van een `--custom-data` cloud-init-bestand op. [](/cli/azure/vmss) In het volgende voorbeeld wordt een schaalset met de naam *myScaleSet* gemaakt in *myResourceGroup* en worden VM-exemplaren geconfigureerd met een bestand met *de naamcloud-init.txt*. Voer als volgt uw eigen namen in:

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


### <a name="install-applications-with-os-updates"></a>Toepassingen installeren met besturingssysteemupdates
Wanneer er nieuwe versies van het besturingssysteem beschikbaar zijn, kunt u een nieuwe aangepaste afbeelding gebruiken of bouwen en upgrades van het besturingssysteem [implementeren](virtual-machine-scale-sets-upgrade-scale-set.md) in een schaalset. Elk VM-exemplaar wordt bijgewerkt naar de meest recente afbeelding die u opgeeft. U kunt een aangepaste installatie afbeelding gebruiken met de toepassing vooraf geïnstalleerd, de aangepaste scriptextensie of PowerShell DSC om uw toepassing automatisch beschikbaar te maken wanneer u de upgrade gaat uitvoeren. Mogelijk moet u het toepassingsonderhoud plannen terwijl u dit proces gaat uitvoeren om ervoor te zorgen dat er geen problemen zijn met de versiecompatibiliteit.

Als u een aangepaste VM-installatielijn gebruikt met de toepassing die vooraf is geïnstalleerd, kunt u de toepassingsupdates integreren met een implementatiepijplijn om de nieuwe installatie afbeeldingen te bouwen en upgrades van het besturingssysteem in de schaalset te implementeren. Met deze aanpak kan de pijplijn de meest recente toepassings builds ophalen, een VM-afbeelding maken en valideren en vervolgens de VM-exemplaren in de schaalset upgraden. Als u een implementatiepijplijn wilt uitvoeren waarmee toepassingsupdates worden gebouwd en geïmplementeerd in aangepaste VM-installatie afbeeldingen, kunt u een Packer-installatielijn maken en implementeren met [Azure DevOps Services](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)of een ander platform gebruiken, zoals [Spinnaker](https://www.spinnaker.io/) of [Jenkins.](https://jenkins.io/)


## <a name="next-steps"></a>Volgende stappen
Wanneer u toepassingen bouwt en implementeert in uw schaalsets, kunt u het Overzicht [van het ontwerp van schaalsets bekijken.](virtual-machine-scale-sets-design-overview.md) Zie PowerShell gebruiken om uw schaalset te beheren voor meer informatie over het beheren [van uw schaalset.](./virtual-machine-scale-sets-manage-powershell.md)
