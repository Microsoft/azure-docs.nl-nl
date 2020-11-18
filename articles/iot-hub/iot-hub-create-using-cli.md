---
title: Een IoT Hub maken met behulp van Azure CLI | Microsoft Docs
description: Meer informatie over het gebruik van de Azure CLI-opdrachten voor het maken van een resource groep en het maken van een IoT-hub in de resource groep. Meer informatie over het verwijderen van de hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: e9757b94330f889869080a0e961ab2728cee86f6
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659910"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Een IoT-hub maken met behulp van de Azure CLI

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

In dit artikel wordt beschreven hoe u een IoT-hub maakt met behulp van Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-iot-hub"></a>Een IoT Hub maken

Gebruik de Azure CLI om een resource groep te maken en vervolgens een IoT-hub toe te voegen.

1. Wanneer u een IoT-hub maakt, moet u deze maken in een resource groep. Gebruik een bestaande resourcegroep of voer de volgende [opdracht voor het maken van een resourcegroep](/cli/azure/resource) uit:
    
   ```azurecli-interactive
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > In het voorbeeld wordt de resourcegroep gemaakt in de locatie VS - west. U kunt een lijst met beschik bare locaties weer geven door de volgende opdracht uit te voeren: 
   >
   > ```azurecli-interactive
   > az account list-locations -o table
   > ```
   >

2. Voer de volgende [opdracht uit om een IOT-hub](/cli/azure/iot/hub#az-iot-hub-create) in uw resource groep te maken met behulp van een wereld wijd unieke naam voor uw IOT-hub:
    
   ```azurecli-interactive
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Met de vorige opdracht maakt u een IoT-hub in de prijs categorie S1 waarvoor u een factuur wilt maken. Zie [Prijzen voor Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/) voor meer informatie.

## <a name="remove-an-iot-hub"></a>Een IoT Hub verwijderen

U kunt Azure CLI gebruiken voor [het verwijderen van een afzonderlijke resource](/cli/azure/resource), zoals een IOT-hub, of het verwijderen van een resource groep en alle bijbehorende resources, inclusief IOT-hubs.

Als u [een IOT-hub wilt verwijderen](/cli/azure/iot/hub#az-iot-hub-delete), voert u de volgende opdracht uit:

```azurecli-interactive
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Als u [een resource groep](/cli/azure/group#az-group-delete) en alle bijbehorende resources wilt verwijderen, voert u de volgende opdracht uit:

```azurecli-interactive
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over het gebruik van een IoT hub:

* [Ontwikkelaars handleiding IoT Hub](iot-hub-devguide.md)
* [De Azure Portal gebruiken om IoT Hub te beheren](iot-hub-create-through-portal.md)