---
title: Uw IoT Central beheren vanuit Azure CLI | Microsoft Docs
description: In dit artikel wordt beschreven hoe u uw IoT Central maken en beheren met behulp van CLI. U kunt de toepassing weergeven, wijzigen en verwijderen met cli.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: philmea
ms.openlocfilehash: c30781cb83436e15a217a1d43c0e39facae9f52d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770402"
---
# <a name="manage-iot-central-from-azure-cli"></a>Uw IoT Central beheren vanuit Azure CLI

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

In plaats van toepassingen te IoT Central maken en beheren op de [Azure IoT Central application manager-website,](https://aka.ms/iotcentral) kunt u Azure [CLI](/cli/azure/) gebruiken om uw toepassingen te beheren.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Zie Het actieve abonnement wijzigen als u uw CLI-opdrachten wilt uitvoeren in een ander [Azure-abonnement.](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription)

## <a name="create-an-application"></a>Een app maken

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Gebruik de [opdracht az iot central app create](/cli/azure/iot/central/app#az_iot_central_app_create) om een toepassing IoT Central maken in uw Azure-abonnement. Bijvoorbeeld:

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

Met deze opdrachten maakt u eerst een resourcegroep in de regio VS - oost voor de toepassing. In de volgende tabel worden de parameters beschreven die worden gebruikt met **de opdracht az iot central app create:**

| Parameter         | Beschrijving |
| ----------------- | ----------- |
| resource-group    | De resourcegroep die de toepassing bevat. Deze resourcegroep moet al bestaan in uw abonnement. |
| location          | Deze opdracht maakt standaard gebruik van de locatie van de resourcegroep. Op dit moment kunt u een IoT Central toepassing maken in de regio's **Australië,** **Azië en Stille Oceaan,** **Europa,** **Verenigde Staten,** **Verenigd** Koninkrijk en **Japan.** |
| naam              | De naam van de toepassing in de Azure Portal. |
| Subdomein         | Het subdomein in de URL van de toepassing. In het voorbeeld is de url van de toepassing `https://mysubdomain.azureiotcentral.com` . |
| sku               | Op dit moment kunt u **ST1** of **ST2 gebruiken.** Zie [Azure IoT Central prijzen.](https://azure.microsoft.com/pricing/details/iot-central/) |
| sjabloon          | De toepassingssjabloon die moet worden gebruikt. Zie de volgende tabel voor meer informatie. |
| weergavenaam      | De naam van de toepassing zoals weergegeven in de gebruikersinterface. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Uw toepassingen bekijken

Gebruik de [opdracht az iot central app list om](/cli/azure/iot/central/app#az_iot_central_app_list) uw toepassingen IoT Central en metagegevens weer te geven.

## <a name="modify-an-application"></a>Een toepassing wijzigen

Gebruik de [opdracht az iot central app update](/cli/azure/iot/central/app#az_iot_central_app_update) om de metagegevens van een IoT Central bijwerken. Als u bijvoorbeeld de weergavenaam van uw toepassing wilt wijzigen:

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Een toepassing verwijderen

Gebruik de [opdracht az iot central app delete](/cli/azure/iot/central/app#az_iot_central_app_delete) om een IoT Central verwijderen. Bijvoorbeeld:

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u uw toepassingen Azure IoT Central Azure CLI kunt beheren, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Uw toepassing beheren](howto-administer.md)
