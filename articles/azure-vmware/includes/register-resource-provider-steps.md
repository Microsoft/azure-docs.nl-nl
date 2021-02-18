---
title: De Azure VMware Solution-resourceprovider registreren
description: Stappen voor het registreren van de Azure VMware Solution-resourceprovider.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: 80010a232f80865b20c2e3d953dc1d9d22ece1c6
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653161"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Als u Azure VMware Solution wilt gebruiken, moet u de resourceprovider eerst registreren bij uw abonnement. Zie [Azure resource providers en-typen](/azure/azure-resource-manager/management/resource-providers-and-types)voor meer informatie over resource providers.

### <a name="azure-cli"></a>Azure CLI 

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

### <a name="azure-portal"></a>Azure Portal
 
1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **Alle services** in het menu van Azure Portal.

1. Voer **abonnement** in het vak **Alle services** in, en selecteer vervolgens **Abonnementen**.

1. Selecteer het abonnement in de lijst met abonnementen om dit weer te geven.

1. Selecteer **Resourceproviders** en voer **Microsoft.AVS** in de zoekopdracht in. 
 
1. Als de resourceprovider niet is geregistreerd, selecteert u **Registreren**.
