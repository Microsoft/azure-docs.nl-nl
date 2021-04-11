---
title: Verwijzen naar een bestaand virtueel netwerk in een Azure Scale set-sjabloon
description: Meer informatie over het toevoegen van een virtueel netwerk aan een bestaande Azure virtual machine-schaal sets sjabloon
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: networking
ms.date: 03/30/2021
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: f15fddc54f4b7c5a03843da1bcc11d1991b70d02
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076663"
---
# <a name="reference-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Verwijzen naar een bestaand virtueel netwerk in een Azure Scale set-sjabloon

In dit artikel wordt beschreven hoe u de [basisschaalset-sjabloon](virtual-machine-scale-sets-mvss-start.md) kunt aanpassen om te implementeren in een bestaand virtueel netwerk in plaats van een nieuwe te maken.

## <a name="prerequisites"></a>Vereisten

In een vorig artikel moesten we een [basisschaalset-sjabloon](virtual-machine-scale-sets-mvss-start.md)maken. U hebt die eerdere sjabloon nodig zodat u deze kunt wijzigen om een sjabloon te maken waarmee een schaalset in een bestaand virtueel netwerk wordt geïmplementeerd.

## <a name="identify-subnet"></a>Subnet identificeren

Voeg eerst een `subnetId` para meter toe. Deze teken reeks wordt door gegeven aan de configuratie van de schaalset, waardoor de schaalset het vooraf gemaakte subnet kan identificeren voor het implementeren van virtuele machines in. Deze teken reeks moet de volgende indeling hebben:

`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`


Als u bijvoorbeeld de schaalset wilt implementeren in een bestaand virtueel netwerk met `myvnet` de naam, `mysubnet` het subnet, `myrg` de resource groep en het abonnement `00000000-0000-0000-0000-000000000000` , zou de subnetId: 

`/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

```diff
      },
      "adminPassword": {
        "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
      }
    },
```

## <a name="delete-extra-virtual-network-resource"></a>Extra bron van virtueel netwerk verwijderen

Verwijder vervolgens de bron van het virtuele netwerk uit de `resources` matrix, omdat u een bestaand virtueel netwerk gebruikt en niet hoeft een nieuwe te implementeren.

```diff
    "variables": {},
    "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2018-11-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```
## <a name="remove-dependency-clause"></a>Afhankelijkheids component verwijderen

Het virtuele netwerk bestaat al voordat de sjabloon wordt geïmplementeerd. u hoeft dus geen component op te geven `dependsOn` van de schaalset naar het virtuele netwerk. Verwijder de volgende regels:

```diff
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets",
        "name": "myScaleSet",
        "location": "[resourceGroup().location]",
        "apiVersion": "2019-03-01",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
        "sku": {
          "name": "Standard_A1",
          "capacity": 2
```

## <a name="pass-subnet-parameter"></a>Subnet-para meter door geven

Ga ten slotte door met de `subnetId` para meter die is ingesteld door de gebruiker (in plaats van `resourceId` te gebruiken om de id van een vnet op te halen in dezelfde implementatie. Dit is wat de basis sjabloon voor een levensvat bare schaalset is).

```diff
                        "name": "myIpConfig",
                        "properties": {
                          "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                          }
                        }
                      }
```


## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
