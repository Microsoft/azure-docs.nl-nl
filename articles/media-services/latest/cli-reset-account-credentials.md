---
title: Uw account referenties opnieuw instellen-CLI
description: Gebruik het Azure CLI-script om de referenties van uw account opnieuw in te stellen en de app.config-instellingen terug te krijgen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: cc605a08147da1d076b302e515a4ebe8d411a782
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101091839"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Voorbeeld van Azure CLI: De referenties voor het account opnieuw instellen

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In het Azure CLI-script in dit artikel ziet u hoe u de referenties van uw account opnieuw kunt instellen en de app.config-instellingen kunt terugkrijgen.

## <a name="prerequisites"></a>Vereisten

[Een Azure Media Services-account maken](./create-account-howto.md).

## <a name="example-script"></a>Voorbeeldscript

```azurecli-interactive
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup
 ```

## <a name="next-steps"></a>Volgende stappen

* [az ams](/cli/azure/ams)
* [Referenties opnieuw instellen](/cli/azure/ams/account/sp#az-ams-account-sp-reset-credentials)
