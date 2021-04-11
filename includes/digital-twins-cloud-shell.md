---
author: baanders
description: bestand opnemen voor Azure Digital Twins - Cloud Shell en de IoT-extensie instellen
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: d4d9efd99a60c93dbfef2d6f45971781d71e83fb
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105053"
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

Vervolgens voegt u de [**Microsoft Azure IoT-extensie voor Azure CLI**](/cli/azure/service-page/azure%20iot) aan uw Cloud Shell toe om opdrachten voor interactie met Azure Digital Twins en andere IoT-Services in te schakelen. Voer deze opdracht uit om te controleren of u de meest recente versie van de uitbrei ding hebt:

```azurecli-interactive
az extension add --upgrade -n azure-iot
```

U bent nu klaar om aan de slag te gaan met Azure Digital Twins in de Cloud Shell.

U kunt dit controleren door `az dt -h` op elk gewenst moment uit te voeren om een lijst weer te geven van de Azure Digital Twins-opdrachten op het hoogste niveau die beschikbaar zijn.