---
title: Een IoT Hub maken met behulp van Azure CLI | Microsoft Docs
description: Leer hoe u de Azure CLI-opdrachten gebruikt om een resourcegroep te maken en vervolgens een IoT-hub in de resourcegroep te maken. Meer informatie over het verwijderen van de hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: eff0085a4a739e0831b25b1bd28cd234fdbcde3d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766439"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Een IoT-hub maken met behulp van de Azure CLI

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

In dit artikel wordt beschreven hoe u een IoT-hub maakt met behulp van Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

Gebruik de Azure CLI om een resourcegroep te maken en vervolgens een IoT-hub toe te voegen.

1. Wanneer u een IoT-hub maakt, moet u deze in een resourcegroep maken. Gebruik een bestaande resourcegroep of voer de volgende [opdracht voor het maken van een resourcegroep](/cli/azure/resource) uit:
    
   ```azurecli-interactive
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > In het voorbeeld wordt de resourcegroep gemaakt in de locatie VS - west. U kunt een lijst met beschikbare locaties weergeven door deze opdracht uit te voeren: 
   >
   > ```azurecli-interactive
   > az account list-locations -o table
   > ```
   >

2. Voer de volgende [opdracht uit om een IoT-hub te maken](/cli/azure/iot/hub#az_iot_hub_create) in uw resourcegroep met behulp van een wereldwijd unieke naam voor uw IoT-hub:
    
   ```azurecli-interactive
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Met de vorige opdracht maakt u een IoT-hub in de prijscategorie S1 waarvoor u wordt gefactureerd. Zie [Prijzen voor Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/) voor meer informatie.

## <a name="remove-an-iot-hub"></a>Een IoT Hub

U kunt Azure CLI gebruiken om een afzonderlijke [resource](/cli/azure/resource)te verwijderen, zoals een IoT-hub, of een resourcegroep en alle resources ervan verwijderen, inclusief alle IoT-hubs.

Voer [de volgende opdracht uit om een IoT-hub](/cli/azure/iot/hub#az_iot_hub_delete)te verwijderen:

```azurecli-interactive
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Voer de volgende opdracht uit om een [resourcegroep](/cli/azure/group#az_group_delete) en alle resources ervan te verwijderen:

```azurecli-interactive
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over het gebruik van een IoT-hub:

* [IoT Hub ontwikkelaarshandleiding](iot-hub-devguide.md)
* [De Azure Portal gebruiken voor het beheren van IoT Hub](iot-hub-create-through-portal.md)