---
title: Onderhoudsmeldingen ontvangen met behulp van de CLI
description: Bekijk onderhoudsmeldingen voor virtuele machines die worden uitgevoerd in Azure en start selfserviceonderhoud met behulp van de Azure CLI.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: d8a9b7ec6425a3cd32b597c3f14f8227fde67064
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777865"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Meldingen voor gepland onderhoud verwerken met behulp van de Azure CLI

**Dit artikel is van toepassing op virtuele machines met linux en Windows.**

U kunt de CLI gebruiken om te zien wanneer VM's zijn gepland voor [onderhoud.](maintenance-notifications.md) Informatie over gepland onderhoud is beschikbaar [in az vm get-instance-view.](/cli/azure/vm#az_vm_get_instance_view)
 
Onderhoudsgegevens worden alleen geretourneerd als er onderhoud is gepland. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

Uitvoer
```
      "maintenanceRedeployStatus": {
      "additionalProperties": {},
      "isCustomerInitiatedMaintenanceAllowed": true,
      "lastOperationMessage": null,
      "lastOperationResultCode": "None",
      "maintenanceWindowEndTime": "2018-06-04T16:30:00+00:00",
      "maintenanceWindowStartTime": "2018-05-21T16:30:00+00:00",
      "preMaintenanceWindowEndTime": "2018-05-19T12:30:00+00:00",
      "preMaintenanceWindowStartTime": "2018-05-14T12:30:00+00:00"
```

## <a name="start-maintenance"></a>Onderhoud starten

Met de volgende aanroep wordt onderhoud op een VM starten als `IsCustomerInitiatedMaintenanceAllowed` is ingesteld op true.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Klassieke implementaties

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Als u nog steeds verouderde VM's hebt die zijn geïmplementeerd met het klassieke implementatiemodel, kunt u de klassieke Azure CLI gebruiken om een query uit te voeren voor VM's en onderhoud te starten.

Zorg ervoor dat u in de juiste modus werkt met klassieke VM door het volgende te typen:

```
azure config mode asm
```

Om de onderhoudsstatus van een VM met de naam *myVM* op te halen, typt u:

```
azure vm show myVM 
``` 

Als u wilt beginnen met het onderhoud op uw klassieke VM met de naam *myVM* in de *myService-service* en *myDeployment-implementatie,* typt u:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>Volgende stappen

U kunt gepland onderhoud ook afhandelen met behulp van [Azure PowerShell](maintenance-notifications-powershell.md) of [portal](maintenance-notifications-portal.md).
