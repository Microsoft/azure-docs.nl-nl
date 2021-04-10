---
title: vCPU-quota
description: Meer informatie over vCPU-quota's voor Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: quota
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 1b9c0d50754d582ca7ada5d0b46c6f998b59d3ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549563"
---
# <a name="check-vcpu-quotas-using-the-azure-cli"></a>VCPU-quota's controleren met behulp van de Azure CLI

De vCPU-quota's voor virtuele machines en virtuele-machine schaal sets worden gerangschikt in twee lagen voor elk abonnement, in elke regio. De eerste laag is het totale regionale Vcpu's en de tweede laag is de verschillende kernen van de VM-grootte, zoals de Vcpu's van de D-serie. Telkens wanneer een nieuwe virtuele machine wordt geïmplementeerd, mag de Vcpu's voor de virtuele machine niet groter zijn dan het vCPU quotum voor de VM-grootte familie of het totale regionale vCPU-quotum. Als een van deze quota wordt overschreden, is de implementatie van de VM niet toegestaan. Er is ook een quotum voor het totale aantal virtuele machines in de regio. De details van elk van deze quota's kunnen worden weer gegeven in de sectie **gebruik en quota's** van de pagina **abonnement** in de [Azure Portal](https://portal.azure.com), of u kunt een query uitvoeren voor de waarden met behulp van de Azure cli.

> [!NOTE]
> Het quotum wordt berekend op basis van het totale aantal kernen dat in gebruik is toegewezen en de toewijzing ongedaan gemaakt. Als u aanvullende kernen nodig hebt, moet u [een quotum verhoging aanvragen](../../azure-portal/supportability/resource-manager-core-quotas-request.md) of vm's verwijderen die niet meer nodig zijn. 


## <a name="check-usage"></a>Gebruik controleren

U kunt het quotum gebruik controleren met behulp van [AZ VM List-Usage](/cli/azure/vm).

```azurecli-interactive
az vm list-usage --location "East US" -o table
```

De uitvoer zal er ongeveer als volgt uitzien:


```
Name                                CurrentValue    Limit
--------------------------------  --------------  -------
Availability Sets                              0     2000
Total Regional vCPUs                          29      100
Virtual Machines                               7    10000
Virtual Machine Scale Sets                     0     2000
Standard DSv3 Family vCPUs                     8      100
Standard DSv2 Family vCPUs                     3      100
Standard Dv3 Family vCPUs                      2      100
Standard D Family vCPUs                        8      100
Standard Dv2 Family vCPUs                      8      100
Basic A Family vCPUs                           0      100
Standard A0-A7 Family vCPUs                    0      100
Standard A8-A11 Family vCPUs                   0      100
Standard DS Family vCPUs                       0      100
Standard G Family vCPUs                        0      100
Standard GS Family vCPUs                       0      100
Standard F Family vCPUs                        0      100
Standard FS Family vCPUs                       0      100
Standard Storage Managed Disks                 5    10000
Premium Storage Managed Disks                  5    10000
```

## <a name="reserved-vm-instances"></a>Gereserveerde VM-instanties
Gereserveerde VM-instanties, die zijn afgestemd op één abonnement zonder flexibiliteit voor VM-grootte, zullen een nieuw aspect toevoegen aan de vCPU quota's. Deze waarden beschrijven het aantal exemplaren van de opgegeven grootte dat in het abonnement moet worden geïmplementeerd. Ze werken als tijdelijke aanduiding in het quota systeem om ervoor te zorgen dat het quotum wordt gereserveerd zodat Azure-reserve ringen in het abonnement kunnen worden geïmplementeerd. Als een specifiek abonnement bijvoorbeeld 10 Standard_D1 reserve ringen heeft, is de limiet voor het gebruik van Standard_D1 voor reserve ringen 10. Dit zorgt ervoor dat Azure ten minste 10 Vcpu's beschikbaar is in het totale regionale Vcpu's-quotum dat moet worden gebruikt voor Standard_D1-instanties en dat er ten minste 10 Vcpu's beschikbaar zijn in het vCPU quotum van de standaard D-serie om te worden gebruikt voor Standard_D1 exemplaren.

Als een quotum toename vereist is om één enkel abonnement RI te kopen, kunt u [een quotum verhoging aanvragen](../../azure-portal/supportability/resource-manager-core-quotas-request.md) voor uw abonnement.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure-abonnement en service limieten, quota's en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=/azure/billing/TOC.json)voor meer informatie over facturering en quota's.
