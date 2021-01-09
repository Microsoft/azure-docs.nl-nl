---
title: De Azure VMware Solution-resourceprovider registreren
description: Stappen voor het registreren van de Azure VMware Solution-resourceprovider.
ms.topic: include
ms.date: 12/24/2020
ms.openlocfilehash: 7d24ce86f24c941c7d48d3b73576dcdfda120f51
ms.sourcegitcommit: 489ce69c0ff3f5188889ecfef5ffa76f7121e0d3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/24/2020
ms.locfileid: "97770814"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

Als u Azure VMware Solution wilt gebruiken, moet u de resourceprovider eerst registreren bij uw abonnement.  

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