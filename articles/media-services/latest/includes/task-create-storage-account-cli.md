---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: f0d0322f6f5f14b94a67285fe8688d72c941b3a4
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104746"
---
<!-- ### Create a storage account -->

Als u een Media Services-account gaat maken, moet u de naam van een Azure Storage-accountresource opgeven. Het opgegeven opslagaccount wordt gekoppeld aan uw Media Services-account. Zie [Storage-accounts](../storage-account-concept.md) voor meer informatie over het gebruik van Storage-accounts in Media Services.

U moet één **primair** opslag account hebben en u kunt een onbeperkt aantal **secundaire** opslag accounts aan uw Media Services-account koppelen. Media Services ondersteunt **GPv2**-accounts (General-purpose v2) of **GPv1**-accounts (General-purpose v1). Blob-accounts kunt u niet instellen als **primaire** account. Zie [Opties voor Azure Storage-account](../../../storage/common/storage-account-overview.md) voor meer informatie over opslagaccounts. 

In dit voorbeeld maakt u een Standard LRS-account voor algemeen gebruik (v2). Als u wilt experimenteren met opslagaccounts, gebruikt u `--sku Standard_LRS`. Als u echter een SKU voor productie selecteert, kunt u overwegen om `--sku Standard_RAGRS` te gebruiken. Deze biedt geografische replicatie voor bedrijfscontinuïteit. Zie [Opslagaccounts](/cli/azure/storage/account) voor meer informatie.

Met de volgende opdracht maakt u een Storage-account die wordt gekoppeld aan de Media Services-account. Vervang in het onderstaande script door `storageaccountforams` de naam van uw eigen unieke met een lengte van minder dan 24 tekens. `amsResourceGroup` in de vorige stap moet overeenkomen met de waarde die u hebt opgegeven voor de resource groep.

```azurecli
az storage account create --name storageaccountforams --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```
