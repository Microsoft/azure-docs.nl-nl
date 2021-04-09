---
title: De Azure VMware Solution-resourceprovider registreren
description: Stappen voor het registreren van de Azure VMware Solution-resourceprovider.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: d2363ca2672f7f668d8f9b3816447f316d8b7347
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103555898"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Als u Azure VMware Solution wilt gebruiken, moet u de resourceprovider eerst registreren bij uw abonnement. Zie [Azure resource providers en-typen](../../azure-resource-manager/management/resource-providers-and-types.md)voor meer informatie over resource providers.


### <a name="portal"></a>[Portal](#tab/azure-portal)
 
1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **Alle services** in het menu van Azure Portal.

1. Voer **abonnement** in het vak **Alle services** in, en selecteer vervolgens **Abonnementen**.

1. Selecteer het abonnement in de lijst met abonnementen om dit weer te geven.

1. Selecteer **Resourceproviders** en voer **Microsoft.AVS** in de zoekopdracht in. 
 
1. Als de resourceprovider niet is geregistreerd, selecteert u **Registreren**.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u Azure CLI wilt gaan gebruiken:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Meld u aan bij het Azure-abonnement dat u gebruikt voor de implementatie van de Azure VMware-oplossing via de Azure CLI. Registreer de `Microsoft.AVS` resource provider bij de opdracht [AZ provider REGI ster](/cli/azure/provider#az_provider_register) :

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

U kunt de opdracht [AZ provider List](/cli/azure/provider#az_provider_list) gebruiken om alle beschik bare providers weer te geven.

---


 
