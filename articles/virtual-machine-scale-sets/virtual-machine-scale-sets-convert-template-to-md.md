---
title: Een sjabloon voor schaalsets converteren voor gebruik van beheerde schijven
description: Een Azure Resource Manager sjabloon voor een schaalset voor virtuele machines converteren naar een sjabloon met een beheerde schijf schaalset.
keywords: virtuele-machineschaalsets
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 6/25/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 03cbe4eb56f3b3b99f87048b699f76b30b7937c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85373961"
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Een sjabloon voor schaal sets converteren naar een sjabloon voor een schaalset voor beheerde schijven

Klanten met een resource manager-sjabloon voor het maken van een schaalset die geen gebruik maakt van een beheerde schijf, kan deze wijzigen voor het gebruik van beheerde schijven. In dit artikel wordt uitgelegd hoe u Managed disks gebruikt, als een voor beeld van een pull-aanvraag van de [Azure Quick](https://github.com/Azure/azure-quickstart-templates)start-sjablonen, een door de Community gestuurde opslag plaats voor voorbeeld sjablonen van Resource Manager. De volledige pull-aanvraag kan hier worden weer gegeven: [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998) en de relevante delen van het verschil zijn hieronder, samen met uitleg:

## <a name="making-the-os-disks-managed"></a>De beheerde schijven van het besturings systeem maken

In het volgende verschil worden verschillende variabelen met betrekking tot het opslag account en de schijf eigenschappen verwijderd. Het type opslag account is niet meer nodig (Standard_LRS is de standaard instelling), maar u kunt dit desgewenst opgeven. Alleen Standard_LRS en Premium_LRS worden ondersteund met beheerde schijven. Er zijn nieuwe achtervoegsels voor het opslag account, een unieke teken reeks matrix en het aantal sa's in de oude sjabloon gebruikt voor het genereren van namen van opslag accounts. Deze variabelen zijn niet meer nodig in de nieuwe sjabloon omdat de beheerde schijf automatisch opslag accounts maakt namens de klant. De naam van de VHD-container en de besturingssysteem schijf zijn niet meer nodig omdat de beheerde schijf automatisch de namen van de onderliggende containers en schijven van de BLOB voor opslag overschrijdt.

```diff
   "variables": {
-    "storageAccountType": "Standard_LRS",
     "namingInfix": "[toLower(substring(concat(parameters('vmssName'), uniqueString(resourceGroup().id)), 0, 9))]",
     "longNamingInfix": "[toLower(parameters('vmssName'))]",
-    "newStorageAccountSuffix": "[concat(variables('namingInfix'), 'sa')]",
-    "uniqueStringArray": [
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '0')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '1')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '2')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '3')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '4')))]"
-    ],
-    "saCount": "[length(variables('uniqueStringArray'))]",
-    "vhdContainerName": "[concat(variables('namingInfix'), 'vhd')]",
-    "osDiskName": "[concat(variables('namingInfix'), 'osdisk')]",
     "addressPrefix": "10.0.0.0/16",
     "subnetPrefix": "10.0.0.0/24",
     "virtualNetworkName": "[concat(variables('namingInfix'), 'vnet')]",
```


In het volgende voor beeld wordt de compute-API bijgewerkt naar versie 2016-04-30-preview. Dit is de oudste vereiste versie voor ondersteuning van beheerde schijven met schaal sets. U kunt niet-beheerde schijven in de nieuwe API-versie gebruiken met de oude syntaxis, indien gewenst. Als u de compute API-versie alleen bijwerkt en iets anders niet wijzigt, moet de sjabloon blijven werken zoals voorheen.

```diff
@@ -86,7 +74,7 @@
       "version": "latest"
     },
     "imageReference": "[variables('osType')]",
-    "computeApiVersion": "2016-03-30",
+    "computeApiVersion": "2016-04-30-preview",
     "networkApiVersion": "2016-03-30",
     "storageApiVersion": "2015-06-15"
   },
```

In het volgende verschil wordt de bron van het opslag account volledig uit de resource matrix verwijderd. De resource is niet meer nodig om de beheerde schijf automatisch te maken.

```diff
@@ -113,19 +101,6 @@
       }
     },
-    {
-      "type": "Microsoft.Storage/storageAccounts",
-      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "[variables('storageApiVersion')]",
-      "copy": {
-        "name": "storageLoop",
-        "count": "[variables('saCount')]"
-      },
-      "properties": {
-        "accountType": "[variables('storageAccountType')]"
-      }
-    },
     {
       "type": "Microsoft.Network/publicIPAddresses",
       "name": "[variables('publicIPAddressName')]",
       "location": "[resourceGroup().location]",
```

In het volgende voor komt, kunnen we zien dat de component afhankelijk is van het verwijderen van de-schaalset naar de lus die opslag accounts heeft gemaakt. In de oude sjabloon was dit ervoor te zorgen dat de opslag accounts zijn gemaakt voordat de schaalset wordt gemaakt, maar deze component niet langer nodig is met beheerde schijf. De eigenschap VHD containers wordt ook verwijderd, samen met de naam eigenschap van de besturingssysteem schijf, omdat deze eigenschappen automatisch worden afgehandeld op de schermen van de beheerde schijf. U kunt `"managedDisk": { "storageAccountType": "Premium_LRS" }` de configuratie ' osDisk ' toevoegen als u wilt dat er Premium-besturingssysteem schijven zijn. Alleen Vm's met een hoofd letters of kleine letters in de VM-SKU kunnen Premium-schijven gebruiken.

```diff
@@ -183,7 +158,6 @@
       "location": "[resourceGroup().location]",
       "apiVersion": "[variables('computeApiVersion')]",
       "dependsOn": [
-        "storageLoop",
         "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
         "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
       ],
@@ -200,16 +174,8 @@
         "virtualMachineProfile": {
           "storageProfile": {
             "osDisk": {
-              "vhdContainers": [
-                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"
-              ],
-              "name": "[variables('osDiskName')]",
             },
             "imageReference": "[variables('imageReference')]"
           },

```

Er is geen expliciete eigenschap in de configuratie van de schaalset voor of een beheerde of onbeheerde schijf moet worden gebruikt. De schaalset weet dat u moet gebruiken op basis van de eigenschappen die in het opslag profiel aanwezig zijn. Het is dus belang rijk dat u de sjabloon wijzigt om ervoor te zorgen dat de juiste eigenschappen zich in het opslag profiel van de schaalset bevinden.


## <a name="data-disks"></a>Gegevensschijven

Met de bovenstaande wijzigingen maakt de schaalset gebruik van beheerde schijven voor de besturingssysteem schijf, maar over gegevens schijven? Als u gegevens schijven wilt toevoegen, voegt u de eigenschap "data disks" onder "storageProfile" toe op hetzelfde niveau als "osDisk". De waarde van de eigenschap is een JSON-lijst met objecten waarvan elk eigenschappen ' LUN ' (dat uniek moet zijn per gegevens schijf op een VM), ' createOption ' (' empty ' is momenteel de enige ondersteunde optie) en ' diskSizeGB ' (de grootte van de schijf in gigabytes; moet groter zijn dan 0 en kleiner zijn dan 1024), zoals in het volgende voor beeld:

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Als u `n` schijven in deze matrix opgeeft, haalt elke virtuele machine in de schaalset `n` gegevens schijven op. Houd er echter rekening mee dat deze gegevens schijven onbewerkte apparaten zijn. Ze zijn niet ingedeeld. Het is de klant om de schijven te koppelen, partitioneren en Format teren voordat ze worden gebruikt. U kunt eventueel ook opgeven `"managedDisk": { "storageAccountType": "Premium_LRS" }` in elk gegevens schijf object om aan te geven dat het een Premium-gegevens schijf moet zijn. Alleen Vm's met een hoofd letters of kleine letters in de VM-SKU kunnen Premium-schijven gebruiken.

Zie [dit artikel](./virtual-machine-scale-sets-attached-disks.md)voor meer informatie over het gebruik van gegevens schijven met schaal sets.


## <a name="next-steps"></a>Volgende stappen
Voor beelden van Resource Manager-sjablonen met schaal sets zoekt u naar ' vmss ' in de [Azure Quick Start-sjablonen github opslag plaats](https://github.com/Azure/azure-quickstart-templates).

Bekijk de [belangrijkste landings pagina voor schaal sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/)voor algemene informatie.

