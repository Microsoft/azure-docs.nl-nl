---
title: IoT Central beheren vanuit Azure CLI | Microsoft Docs
description: In dit artikel wordt beschreven hoe u uw IoT Central-toepassing maakt en beheert met behulp van CLI. U kunt de toepassing weer geven, wijzigen en verwijderen met behulp van CLI.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: philmea
ms.openlocfilehash: d414b86ff81a33f9e818a0a28031e73d88cabec2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202260"
---
# <a name="manage-iot-central-from-azure-cli"></a>IoT Central beheren vanuit Azure CLI

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

In plaats van IoT Central-toepassingen te maken en te beheren op de website van [azure IOT Central Application Manager](https://aka.ms/iotcentral) , kunt u [Azure cli](/cli/azure/) gebruiken voor het beheren van uw toepassingen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Als u uw CLI-opdrachten moet uitvoeren in een ander Azure-abonnement, raadpleegt u [het actieve abonnement wijzigen](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription).

## <a name="create-an-application"></a>Een app maken

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Gebruik de opdracht [AZ IOT Central app Create](/cli/azure/iot/central/app#az-iot-central-app-create) om een IOT Central-toepassing te maken in uw Azure-abonnement. Bijvoorbeeld:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iot central app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview" \
  --display-name "My Custom Display Name"
```

Met deze opdrachten maakt u eerst een resource groep in de regio VS-Oost voor de toepassing. In de volgende tabel worden de para meters beschreven die worden gebruikt bij de opdracht **AZ IOT Central app Create** :

| Parameter         | Beschrijving |
| ----------------- | ----------- |
| resource-group    | De resource groep die de toepassing bevat. Deze resource groep moet al bestaan in uw abonnement. |
| location          | Deze opdracht maakt standaard gebruik van de locatie uit de resource groep. Op dit moment kunt u een IoT Central-toepassing maken in de geografs **Australia**, **Azië en Stille Oceaan**, **Europa**, **Verenigde Staten**, het **Verenigd Konink rijk** en **Japan** . |
| naam              | De naam van de toepassing in de Azure Portal. |
| subdomein         | Het subdomein in de URL van de toepassing. In het voor beeld is de URL van de toepassing `https://mysubdomain.azureiotcentral.com` . |
| sku               | Op dit moment kunt u **ST1** of **ST2** gebruiken. Zie [prijzen voor Azure IOT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| sjabloon          | De toepassings sjabloon die moet worden gebruikt. Zie de volgende tabel voor meer informatie. |
| weergave naam      | De naam van de toepassing, zoals deze wordt weer gegeven in de gebruikers interface. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Uw toepassingen bekijken

Gebruik de opdracht [AZ IOT Central app List](/cli/azure/iot/central/app#az-iot-central-app-list) om uw IOT Central toepassingen te vermelden en meta gegevens weer te geven.

## <a name="modify-an-application"></a>Een toepassing wijzigen

Gebruik de opdracht [AZ IOT Central App Update](/cli/azure/iot/central/app#az-iot-central-app-update) om de meta gegevens van een IOT Central-toepassing bij te werken. Als u bijvoorbeeld de weergave naam van uw toepassing wilt wijzigen:

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Een toepassing verwijderen

Gebruik de opdracht [AZ IOT Central app delete](/cli/azure/iot/central/app#az-iot-central-app-delete) om een IOT Central-toepassing te verwijderen. Bijvoorbeeld:

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u Azure IoT Central-toepassingen kunt beheren vanuit Azure CLI, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Uw toepassing beheren](howto-administer.md)
