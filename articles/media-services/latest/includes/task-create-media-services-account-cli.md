---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 07b0897de3fce6a108836f1e72ce956a3a8092f1
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107893131"
---
<!--Create a media services account -->

Met de volgende Azure CLI-opdracht wordt een nieuwe Media Services-account gemaakt. U kunt de volgende waarden vervangen: (moet overeenkomen met de waarde die u hebt gegeven voor uw opslagaccount) en (moet overeenkomen met de waarde die u hebt gegeven `amsaccount` `storageaccountforams` voor de `amsResourceGroup` resourcegroep).  

```azurecli
az ams account create --name amsaccount -g amsResourceGroup --storage-account storageaccountforams -l westus2
```
