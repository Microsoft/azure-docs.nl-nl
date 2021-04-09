---
title: Ingebouwde edgeAgent direct-methoden-Azure IoT Edge
description: Een IoT Edge-implementatie bewaken en beheren met behulp van ingebouwde directe methoden in de IoT Edge agent-runtime-module
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 52cd7817594c5c2a1d4e3a4ca9c56891df594cd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201112"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>Communiceren met edgeAgent met behulp van ingebouwde directe methoden

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Bewaak en beheer IoT Edge implementaties met behulp van de directe methoden die zijn opgenomen in de module IoT Edge agent. Directe methoden worden geïmplementeerd op het apparaat en kunnen vervolgens vanuit de cloud worden aangeroepen. De IoT Edge-agent bevat directe methoden waarmee u uw IoT Edge apparaten op afstand kunt bewaken en beheren.

Zie voor meer informatie over directe methoden, hoe u deze kunt gebruiken en hoe u deze in uw eigen modules implementeert, [direct methoden begrijpen en aanroepen vanuit IOT hub](../iot-hub/iot-hub-devguide-direct-methods.md).

De namen van deze directe methoden worden niet-hoofdletter gevoelig verwerkt.

## <a name="ping"></a>Ping

De **ping** -methode is handig om te controleren of IOT Edge wordt uitgevoerd op een apparaat, of of het apparaat een open verbinding heeft met IOT hub. Gebruik deze directe methode om de IoT Edge-agent te pingen en de status ervan op te halen. Een geslaagde ping retourneert een lege nettolading en **' status ': 200**.

Bijvoorbeeld:

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

Roep in het Azure Portal de methode met de naam van de methode `ping` en een lege JSON-nettolading `{}` .

![Directe methode ping in Azure Portal aanroepen](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>Module opnieuw starten

Met de methode **RestartModule** kunt u extern beheer van modules die worden uitgevoerd op een IOT edge apparaat. Als een module een mislukte status of een ander beschadigd gedrag rapporteert, kunt u de IoT Edge-agent activeren om deze opnieuw te starten. Een geslaagde herstart-opdracht retourneert een lege Payload en **' status ': 200**.

De methode RestartModule is beschikbaar in IoT Edge versie 1.0.9 en hoger. 

U kunt de RestartModule directe methode gebruiken voor elke module die wordt uitgevoerd op een IoT Edge apparaat, met inbegrip van de edgeAgent-module zelf. Als u deze directe methode echter gebruikt om de edgeAgent af te sluiten, ontvangt u geen resultaat omdat de verbinding wordt verstoord terwijl de module opnieuw wordt opgestart.

Bijvoorbeeld:

```azurecli
az iot hub invoke-module-method --method-name 'RestartModule' -n <hub name> -d <device name> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "id": "<module name>"
    }
'
```

Roep in de Azure Portal de methode met de naam van de methode `RestartModule` en de volgende JSON-nettolading:

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![Directe methode ' RestartModule ' aanroepen in Azure Portal](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="diagnostic-direct-methods"></a>Diagnostische directe methoden

* [GetModuleLogs](how-to-retrieve-iot-edge-logs.md#retrieve-module-logs): ophalen van module Logboeken inline in het antwoord van de directe methode.
* [UploadModuleLogs](how-to-retrieve-iot-edge-logs.md#upload-module-logs): u kunt module Logboeken ophalen en deze uploaden naar Azure Blob Storage.
* [UploadSupportBundle](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics): u kunt module Logboeken ophalen met behulp van een ondersteunings bundel en een zip-bestand uploaden naar Azure Blob Storage.
* [GetTaskStatus](how-to-retrieve-iot-edge-logs.md#get-upload-request-status): Controleer de status van een upload logboek of een ondersteunings aanvraag.

Deze diagnostische directe methoden zijn beschikbaar vanaf de 1.0.10-release.

## <a name="next-steps"></a>Volgende stappen

[Eigenschappen van de IoT Edge agent en IoT Edge hub-module apparaatdubbels](module-edgeagent-edgehub.md)
