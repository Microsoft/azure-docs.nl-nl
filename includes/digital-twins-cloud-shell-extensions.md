---
author: baanders
description: bestand opnemen voor Azure Digital Twins - meest recente IoT-extensie instellen
ms.service: digital-twins
ms.topic: include
ms.date: 7/31/2020
ms.author: baanders
ms.openlocfilehash: 984739a728f6ac5e28eeb561e0d7b6ec0485ca13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90606244"
---
Voer eerst deze opdracht uit om een lijst weer te geven met alle extensies die u al hebt geïnstalleerd.

```azurecli-interactive
az extension list
```

De uitvoer is een matrix van alle extensies die u momenteel hebt. Zoek naar het veld `"name"` voor elk vermelding in de lijst om de namen van de extensies weer te geven.

Gebruik de uitvoer om te bepalen welke van de volgende opdrachten moet worden uitgevoerd voor de extensie-instelling (u kunt er meer dan één uitvoeren).
* Als de lijst `azure-iot` bevat: U hebt de extensie al. Voer deze opdracht uit om te controleren of u over de meest recente update beschikt en er geen updates meer beschikbaar zijn:

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

* Als de lijst `azure-iot` **niet** bevat: U moet de extensie installeren. Gebruik deze opdracht:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

* Als de lijst `azure-iot-cli-ext` bevat: Dit is de verouderde versie van de extensie. Er moet slechts één versie van de extensie tegelijkertijd zijn geïnstalleerd. U moet de oude extensie dus verwijderen. Gebruik deze opdracht:

   ```azurecli-interactive
   az extension remove --name azure-cli-iot-ext
   ```