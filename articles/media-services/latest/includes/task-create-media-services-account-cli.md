---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 5c0341087cdd348e973da5faaa1f90081780c9c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88602242"
---
<!--Create a media services account -->

Met de volgende Azure CLI-opdracht wordt een nieuwe Media Services-account gemaakt. U kunt de volgende waarden vervangen: `amsaccount` `storageaccountforams` (moet overeenkomen met de waarde die u hebt opgegeven voor uw opslag account) en `amsResourceGroup` (moet overeenkomen met de waarde die u hebt opgegeven voor de resource groep).  

```azurecli
az ams account create --name amsaccount -g amsResourceGroup --storage-account storageaccountforams -l westus2
```