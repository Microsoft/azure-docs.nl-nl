---
title: Een sleutelkluis voor Azure Disk Encryption maken en configureren
description: Dit artikel bevat stappen voor het maken en configureren van een sleutelkluis voor gebruik Azure Disk Encryption linux-VM.
ms.service: virtual-machines
ms.collection: linux
ms.subservice: disks
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 313f7030d56a8a199c6d2d04fa0d979429d0bce1
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750802"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Een sleutelkluis voor Azure Disk Encryption maken en configureren

Azure Disk Encryption gebruikt Azure Key Vault om sleutels en geheimen voor schijfversleuteling te beheren.  Zie [Aan de slag met Azure Key Vault](../../key-vault/general/overview.md) en [Uw sleutelkluis beveiligen](../../key-vault/general/security-overview.md) voor meer informatie over sleutelkluizen. 

> [!WARNING]
> - Als u eerder een Azure Disk Encryption met Azure AD hebt gebruikt om een VM te versleutelen, moet u deze optie blijven gebruiken om uw VM te versleutelen. Zie [Een sleutelkluis maken en configureren voor Azure Disk Encryption met Azure AD (vorige versie)](disk-encryption-key-vault-aad.md) voor meer informatie.

Een sleutelkluis maken en configureren voor gebruik met Azure Disk Encryption bestaat uit drie stappen:

1. Een resourcegroep maken, indien nodig.
2. Een sleutelkluis maken. 
3. Geavanceerd toegangsbeleid voor sleutelkluis instellen.

Deze stappen worden geïllustreerd in de volgende quickstarts:

- [Een Linux-VM maken en versleutelen met behulp van Azure CLI](disk-encryption-cli-quickstart.md)
- [Een Linux-VM maken en versleutelen met behulp van Azure PowerShell](disk-encryption-powershell-quickstart.md)

U kunt eventueel ook een sleutelversleutelingssleutel genereren of importeren (KEK).

> [!Note]
> De stappen in dit artikel zijn geautomatiseerd in het [CLI Azure Disk Encryption script](https://github.com/ejarvi/ade-cli-getting-started) met vereisten en Azure Disk Encryption [PowerShell-script.](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)

## <a name="install-tools-and-connect-to-azure"></a>Hulpprogramma's installeren en verbinding maken met Azure

U kunt de stappen in dit artikel voltooien met de [Azure CLI](/cli/azure/), de [Azure PowerShell AZ-module](/powershell/azure/) of de [Azure-portal](https://portal.azure.com). 

Hoewel de portal toegankelijk is via uw browser, is voor Azure CLI en Azure PowerShell lokale installatie vereist; zie [Azure Disk Encryption voor Linux: Hulpprogramma's installeren](disk-encryption-linux.md#install-tools-and-connect-to-azure) voor meer informatie.

### <a name="connect-to-your-azure-account"></a>Verbinding maken met uw Azure-account

Voordat u de Azure CLI of Azure PowerShell gebruikt, moet u eerst verbinding maken met uw Azure-abonnement. U doet dit door u aan te melden met [Azure CLI](/cli/azure/authenticate-azure-cli), u aan te melden met [Azure Powershell](/powershell/azure/authenticate-azureps)of uw referenties op te geven aan de Azure Portal wanneer u hierom wordt gevraagd.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
 
## <a name="next-steps"></a>Volgende stappen

- [Azure Disk Encryption CLI-script met vereisten](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption PowerShell-script met vereisten](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Meer [informatie Azure Disk Encryption scenario's op Linux-VM's](disk-encryption-linux.md)
- Meer informatie over het [oplossen van Azure Disk Encryption](disk-encryption-troubleshooting.md)
- De [voorbeeldscripts Azure Disk Encryption lezen](disk-encryption-sample-scripts.md)
