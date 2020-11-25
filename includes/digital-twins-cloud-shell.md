---
author: baanders
description: bestand opnemen voor Azure Digital Twins - Cloud Shell en de IoT-extensie instellen
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: c1d1efce656bea02385cb20ac11ab90ca5664e60
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026610"
---
Om aan de slag te gaan met Azure Digital Twins in een open [Azure Cloud Shell](https://shell.azure.com)-venster, moet u zich eerst aanmelden en de shellcontext voor deze sessie instellen op uw abonnement. Voer de volgende opdrachten uit in uw Cloud Shell:

```azurecli-interactive
az login
az account set --subscription "<your-Azure-subscription-ID>"
```
> [!TIP]
> U kunt ook de naam van uw abonnement in plaats van de ID gebruiken in de bovenstaande opdracht. 

Als dit de eerste keer is dat u dit abonnement met Azure Digital Twins gebruikt, voert u deze opdracht uit om u te registreren bij de naamruimte van Azure Digital Twins. (Als u het niet zeker weet, kunt u de opdracht opnieuw uitvoeren, zelfs als u dit in het verleden een keer hebt gedaan.)

```azurecli-interactive
az provider register --namespace 'Microsoft.DigitalTwins'
```

Vervolgens voegt u de [**Microsoft Azure IoT-extensie voor Azure CLI**](/cli/azure/ext/azure-iot/iot?preserve-view=true&view=azure-cli-latest) aan uw Cloud Shell toe om opdrachten voor interactie met Azure Digital Twins en andere IoT-Services in te schakelen. 

[!INCLUDE [digital-twins-cloud-shell-extensions.md](digital-twins-cloud-shell-extensions.md)]

U bent nu klaar om aan de slag te gaan met Azure Digital Twins in de Cloud Shell.

U kunt dit controleren door `az dt -h` op elk gewenst moment uit te voeren om een lijst weer te geven van de Azure Digital Twins-opdrachten op het hoogste niveau die beschikbaar zijn.