---
title: Een sleutelkluis voor Azure Disk Encryption maken en configureren
description: In dit artikel worden de stappen beschreven voor het maken en configureren van een sleutelkluis voor gebruik met Azure Disk Encryption
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: b8697e64eac69af8b8552f01fb6a03da0488a7cc
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102210088"
---
# <a name="create-and-configure-a-key-vault-for-azure-disk-encryption"></a>Een sleutelkluis voor Azure Disk Encryption maken en configureren

Azure Disk Encryption gebruikt Azure Key Vault om sleutels en geheimen voor schijfversleuteling te beheren.  Zie [Aan de slag met Azure Key Vault](../key-vault/general/overview.md) en [Uw sleutelkluis beveiligen](../key-vault/general/secure-your-key-vault.md) voor meer informatie over sleutelkluizen.

Een sleutelkluis maken en configureren voor gebruik met Azure Disk Encryption bestaat uit drie stappen:

1. Een resourcegroep maken, indien nodig.
2. Een sleutelkluis maken. 
3. Geavanceerd toegangsbeleid voor sleutelkluis instellen.

Deze stappen worden geïllustreerd in de volgende quickstarts:

U kunt eventueel ook een sleutelversleutelingssleutel genereren of importeren (KEK).

## <a name="install-tools-and-connect-to-azure"></a>Hulpprogramma's installeren en verbinding maken met Azure

U kunt de stappen in dit artikel voltooien met de [Azure CLI](/cli/azure/), de [Azure PowerShell AZ-module](/powershell/azure/) of de [Azure-portal](https://portal.azure.com).

### <a name="connect-to-your-azure-account"></a>Verbinding maken met uw Azure-account

Voordat u de Azure CLI of Azure PowerShell gebruikt, moet u eerst verbinding maken met uw Azure-abonnement. U doet dit door [u aan te melden met Azure CLI](/cli/azure/authenticate-azure-cli), [u aan te melden met Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)of uw referenties aan de Azure-portal op te geven wanneer u hierom wordt gevraagd.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)
- [ Een virtuele-machineschaalset versleutelen met de Azure CLI](disk-encryption-cli.md)
- [Een virtuele-machineschaalset versleutelen met de Azure PowerShell](disk-encryption-powershell.md)
