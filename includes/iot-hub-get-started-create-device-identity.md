---
title: bestand opnemen
description: bestand opnemen
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: b1a863498603006e37ab98b838ffd426b962d788
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92755320"
---
In deze sectie gebruikt u de Azure CLI om een apparaat-id voor dit artikel te maken. Apparaat-id's zijn hoofdlettergevoelig.

1. Open [Azure Cloud Shell](https://shell.azure.com/).

1. Voer in Azure Cloud Shell de volgende opdracht uit om de Microsoft Azure IoT-extensie voor Azure CLI te installeren:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. Maak een nieuwe apparaat-id `myDeviceId` met de naam en haal het apparaat Connection String op met de volgende opdrachten:

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Noteer het apparaat connection string van het resultaat. Dit apparaat connection string wordt gebruikt door de apparaat-app om verbinding te maken met uw IoT Hub als een apparaat.

<!-- images and links -->
