---
author: dominicbetts
ms.author: dominicbetts
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: 647226091c3e15a19bf8262c23e62d95018488b3
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831197"
---
Als u van plan bent om door te gaan met aanvullende IoT Plug en Play-artikelen, kunt u de resources die u in dit artikel hebt gebruikt, behouden en opnieuw gebruiken. Als dat niet het geval is, kunt u de resources die u in dit artikel hebt gemaakt, verwijderen om extra kosten te vermijden.

U kunt zowel de hub als het geregistreerde apparaat tegelijk verwijderen door met de volgende Azure CLI-opdracht de hele resourcegroep te verwijderen. Gebruik deze opdracht niet als deze resources een resourcegroep delen met andere resources die u wilt houden.

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```

Als u alleen de IoT-hub wilt verwijderen, voert u de volgende opdracht uit met behulp van Azure CLI:

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

Als u alleen de apparaat-id wilt verwijderen die u hebt geregistreerd bij de IoT-hub, voert u de volgende opdracht uit met behulp van Azure CLI:

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Mogelijk wilt u ook de gekloonde voorbeeldbestanden verwijderen van uw ontwikkelcomputer.
