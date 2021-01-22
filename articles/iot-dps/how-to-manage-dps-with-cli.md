---
title: IoT Hub Device Provisioning Service beheren met Azure CLI & IoT-extensie
description: Meer informatie over het gebruik van Azure CLI en de IoT-extensie voor het beheren van de IoT Hub Device Provisioning Service (DPS)
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
ms.custom: devx-track-azurecli
services: iot-dps
ms.openlocfilehash: dd0564fbb23a0695d849852fd464308cd1b5fac9
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678940"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Azure CLI en de IoT-extensie gebruiken voor het beheren van de IoT Hub Device Provisioning Service

[Azure cli](/cli/azure) is een open source-opdracht regel programma voor meerdere platformen voor het beheer van Azure-resources, zoals IOT Edge. Azure CLI is beschikbaar in Windows, Linux en macOS. Met Azure CLI kunt u Azure IoT Hub-resources, Device Provisioning Service-instanties en gekoppelde-hubs uit het vak beheren.

De IoT-extensie verrijkt Azure CLI met functies als Apparaatbeheer en volledige IoT Edge mogelijkheid.

In deze zelf studie voltooit u eerst de stappen voor het instellen van Azure CLI en de IoT-extensie. Vervolgens leert u hoe u CLI-opdrachten kunt uitvoeren om elementaire service bewerkingen voor Device Provisioning uit te voeren. 

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prerequisites"></a>Vereisten

- [Python 2.7x of Python 3.x](https://www.python.org/downloads/) is vereist.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Voor dit artikel is versie 2.0.70 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="basic-device-provisioning-service-operations"></a>Elementaire service bewerkingen voor Device Provisioning

In het voor beeld ziet u hoe u zich aanmeldt bij uw Azure-account, een Azure-resource groep maakt (een container met gerelateerde resources voor een Azure-oplossing), een IoT Hub maakt, een Device Provisioning-Service maakt, de bestaande apparaten van de inrichting van het apparaat vermeld en een gekoppelde IoT-hub maakt met CLI-opdrachten. 

Voer de hierboven beschreven installatiestappen uit voordat u begint. Als u geen Azure-account hebt, kunt u nu [een gratis account maken](https://azure.microsoft.com/free/?v=17.39a). 


### <a name="1-log-in-to-the-azure-account"></a>1. Meld u aan bij het Azure-account
  
```azurecli
az login
```

![Scherm afbeelding toont een opdracht prompt venster met de opdracht AZ login.](./media/how-to-manage-dps-with-cli/login.jpg)

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Maak een resource groep IoTHubBlogDemo in de oostelijke sector

```azurecli
az group create -l eastus -n IoTHubBlogDemo
```

![Een resourcegroep maken](./media/how-to-manage-dps-with-cli/create-resource-group.jpg)


### <a name="3-create-two-device-provisioning-services"></a>3. twee Device Provisioning Services maken

```azurecli
az iot dps create --resource-group IoTHubBlogDemo --name demodps
```

![Device Provisioning Service maken](./media/how-to-manage-dps-with-cli/create-dps.jpg)

```azurecli
az iot dps create --resource-group IoTHubBlogDemo --name demodps2
```

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. alle bestaande Device Provisioning Services onder deze resource groep weer geven

```azurecli
az iot dps list --resource-group IoTHubBlogDemo
```

![Services voor het inrichten van apparaten weer geven](./media/how-to-manage-dps-with-cli/list-dps.jpg)


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Maak een IoT Hub blogDemoHub onder de zojuist gemaakte resource groep

```azurecli
az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo
```

![IoT Hub maken](./media/how-to-manage-dps-with-cli/create-hub.jpg)

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. een bestaande IoT Hub koppelen aan een Device Provisioning Service

```azurecli
az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus
```

![Hub koppelen](./media/how-to-manage-dps-with-cli/create-hub.jpg)

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Het apparaat inschrijven
> * Het apparaat starten
> * Controleren of het apparaat is geregistreerd

Ga naar de volgende zelfstudie voor informatie over het inrichten van meerdere apparaten via hubs met gelijke taakverdeling. 

> [!div class="nextstepaction"]
> [Apparaten inrichten in meerdere IoT-hubs met gelijke taakverdeling](./tutorial-provision-multiple-hubs.md)