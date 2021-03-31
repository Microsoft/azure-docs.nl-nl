---
title: Algemene Azure CLI-opdrachten
description: Meer informatie over de algemene Azure CLI-opdrachten waarmee u aan de slag kunt met het beheer van uw Vm's in Azure Resource Manager modus
author: RicksterCDN
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 05/12/2017
ms.author: rclaus
ms.openlocfilehash: 2084d79ecbbc53ef9e3c75bae0664eae7de0eccb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559627"
---
# <a name="common-azure-cli-commands-for-managing-azure-resources"></a>Algemene Azure CLI-opdrachten voor het beheren van Azure-resources

Met de Azure CLI kunt u uw Azure-resources maken en beheren op macOS, Linux en Windows. In dit artikel vindt u meer informatie over de meest voorkomende opdrachten voor het maken en beheren van virtuele machines (Vm's).

Voor dit artikel is de Azure CLI-versie 2.0.4 of hoger vereist. Voer `az --version` uit om de versie te bekijken. Als u een upgrade wilt uitvoeren, raadpleegt u [Azure cli installeren](/cli/azure/install-azure-cli). U kunt [Cloud shell](../../cloud-shell/quickstart.md) ook gebruiken vanuit uw browser.

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Basisopdrachten van Azure Resource Manager in Azure CLI
Voor gedetailleerde hulp bij specifieke opdracht regel opties en-opties kunt u de online opdracht Help en-opties gebruiken door te typen `az <command> <subcommand> --help` .

### <a name="create-vms"></a>Virtuele machines maken
| Taak | Azure CLI-opdrachten |
| --- | --- |
| Een resourcegroep maken | `az group create --name myResourceGroup --location eastus` |
| Een virtuele Linux-machine maken | `az vm create --resource-group myResourceGroup --name myVM --image ubuntults` |
| Een Windows-VM maken | `az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter` |

### <a name="manage-vm-state"></a>Status van de virtuele machine beheren
| Taak | Azure CLI-opdrachten |
| --- | --- |
| Een VM starten | `az vm start --resource-group myResourceGroup --name myVM` |
| Een VM stoppen | `az vm stop --resource-group myResourceGroup --name myVM` |
| De toewijzing van een VM ongedaan maken | `az vm deallocate --resource-group myResourceGroup --name myVM` |
| Een VM opnieuw opstarten | `az vm restart --resource-group myResourceGroup --name myVM` |
| Een virtuele machine opnieuw implementeren | `az vm redeploy --resource-group myResourceGroup --name myVM` |
| Een VM verwijderen | `az vm delete --resource-group myResourceGroup --name myVM` |

### <a name="get-vm-info"></a>VM-gegevens ophalen
| Taak | Azure CLI-opdrachten |
| --- | --- |
| Lijst met VM's opvragen | `az vm list` |
| Informatie over een VM ophalen | `az vm show --resource-group myResourceGroup --name myVM` |
| Verbruik van VM-resources opvragen | `az vm list-usage --location eastus` |
| Alle beschikbare VM-grootten opvragen | `az vm list-sizes --location eastus` |

## <a name="disks-and-images"></a>Schijven en installatie kopieën
| Taak | Azure CLI-opdrachten |
| --- | --- |
| Een gegevensschijf toevoegen aan een VM | `az vm disk attach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk --size-gb 128 --new` |
| Een gegevensschijf verwijderen van een VM | `az vm disk detach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk` |
| De grootte van een schijf wijzigen | `az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 256` |
| Een momentopname maken van een schijf | `az snapshot create --resource-group myResourceGroup --name mySnapshot --source myDataDisk` |
| Een installatie kopie van een virtuele machine maken | `az image create --resource-group myResourceGroup --source myVM --name myImage` |
| Een VM maken op basis van een installatie kopie | `az vm create --resource-group myResourceGroup --name myNewVM --image myImage` |


## <a name="next-steps"></a>Volgende stappen
Zie [Linux Vm's maken en beheren met de Azure cli](tutorial-manage-vm.md) -zelf studie voor meer voor beelden van de CLI-opdrachten.
