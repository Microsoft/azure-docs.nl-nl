---
title: Foutdomeinen beheren in virtuele-machineschaalsets van Azure
description: Meer informatie over het kiezen van het juiste aantal FD's tijdens het maken van een virtuele-machineschaalset.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 12/18/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 10d45662f84a354ee4b261c2e7255a57aa81ad0f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774481"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>Het juiste aantal foutdomeinen kiezen voor een virtuele-machineschaalset
Virtuele-machineschaalsets worden standaard gemaakt met vijf foutdomeinen in Azure-regio's zonder zones. Voor de regio's die zonelijke implementatie van virtuele-machineschaalsets ondersteunen en deze optie is geselecteerd, is de standaardwaarde van het aantal foutdomeinen 1 voor elk van de zones. FD =1 impliceert in dit geval dat de VM-exemplaren die tot de schaalset behoren, op basis van de beste inspanning over veel rekken worden verdeeld.

U kunt ook overwegen om het aantal foutdomeinen in de schaalset af te stemmen op het aantal Managed Disks foutdomeinen. Deze uitlijning kan voorkomen dat quorumverlies wordt voorkomen als een Managed Disks foutdomein uitvallen. Het aantal FD's kan worden ingesteld op minder dan of gelijk aan het aantal Managed Disks foutdomeinen dat beschikbaar is in elk van de regio's. Raadpleeg dit [document voor](../virtual-machines/availability.md) meer informatie over het aantal Managed Disks foutdomeinen per regio.

## <a name="rest-api"></a>REST-API
U kunt de eigenschap `properties.platformFaultDomainCount` instellen op 1, 2 of 3 (standaard 3 als deze niet is opgegeven). Raadpleeg de documentatie voor REST API [hier.](/rest/api/compute/virtualmachinescalesets/createorupdate)

## <a name="azure-cli"></a>Azure CLI
U kunt de parameter `--platform-fault-domain-count` instellen op 1, 2 of 3 (standaard 3 indien niet opgegeven). Raadpleeg hier de documentatie voor Azure [CLI.](/cli/azure/vmss#az_vmss_create)

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --platform-fault-domain-count 3\
  --generate-ssh-keys
```

Het duurt enkele minuten om alle schaalsetresources en VM's te maken en te configureren.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [beschikbaarheids- en redundantiefuncties](../virtual-machines/availability.md) voor Azure-omgevingen.
