---
title: Informatie over het bijwerken van apparaten voor Azure IoT Hub-resources | Microsoft Docs
description: Informatie over het bijwerken van apparaten voor Azure IoT Hub-resources
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ba43889b885252f68bb3b4b158b5626411aac3d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101662666"
---
# <a name="device-update-resources"></a>Resources voor het bijwerken van apparaten

Als u het bijwerken van het apparaat voor IoT Hub wilt gebruiken, moet u een update account voor het apparaat en de bron voor het bijwerken maken. 

## <a name="device-update-account"></a>Account voor update van apparaat

Een update van het apparaat is een resource die wordt gemaakt in uw Azure-abonnement. Op het niveau van de update op het apparaat kunt u de regio selecteren waar uw update account voor het apparaat wordt gemaakt. U kunt ook machtigingen instellen om gebruikers te autoriseren die toegang hebben tot het bijwerken van het apparaat.


## <a name="device-update-instance"></a>Update-exemplaar van apparaat
Nadat u een account hebt gemaakt, moet u een update-exemplaar voor het apparaat maken. Een exemplaar is een logische container die updates en implementaties bevat die zijn gekoppeld aan een specifieke IoT-hub. Bij het bijwerken van het apparaat wordt IoT hub gebruikt als een Apparaatlijst en een communicatie kanaal met apparaten. 

Tijdens de open bare Preview kunnen twee update accounts voor apparaten worden gemaakt per abonnement. Daarnaast kan er een update-exemplaar van twee apparaten worden gemaakt per account.

## <a name="configuring-device-update-linked-iot-hub"></a>Gekoppelde IoT Hub voor het bijwerken van apparaten configureren 

Als u wilt dat de apparaat-update meldingen over wijzigingen ontvangt van IoT Hub, integreert de update van het apparaat met de ' ingebouwde ' event hub. Als u op de knop IoT Hub configureren in uw exemplaar klikt, worden de vereiste bericht routes en het toegangs beleid geconfigureerd om te communiceren met IoT-apparaten. 

De volgende bericht routes zijn geconfigureerd voor het bijwerken van apparaten:

|   Route naam    | Routerings query  | Description  |
| :--------- | :---- |:---- |
|  DeviceUpdate.DigitalTwinChanges | true |Luistert naar gebeurtenissen met een digitale dubbele wijziging  |
|  DeviceUpdate.DeviceLifeCycle | opType = ' deleteDeviceIdentity '  | Luistert naar apparaten die zijn verwijderd |
|  DeviceUpdate.TelemetryModelInformation | iothub-interface-id = "urn: azureiot: ModelDiscovery: ModelInformation: 1 | Luistert naar nieuwe typen apparaten |
|  DeviceUpdate.DeviceTwinEvents| (opType = ' updateTwin' of opType = ' replaceTwin') EN IS_DEFINED ($body. Tags. ADUGroup) | Luistert naar nieuwe update groepen voor apparaten |

## <a name="next-steps"></a>Volgende stappen

[Resources voor het bijwerken van apparaten maken](./create-device-update-account.md)
