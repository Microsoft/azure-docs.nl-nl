---
title: Een sleutel kluis maken en configureren voor Azure Disk Encryption op een Windows-VM
description: In dit artikel worden de stappen beschreven voor het maken en configureren van een sleutel kluis voor gebruik met Azure Disk Encryption op een Windows-VM.
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: af48bd74bbc38b1cd9b4d3b0f127e7bdf5d3e037
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555445"
---
# <a name="create-and-configure-a-key-vault-for-azure-disk-encryption-on-a-windows-vm"></a>Een sleutel kluis maken en configureren voor Azure Disk Encryption op een Windows-VM

Azure Disk Encryption gebruikt Azure Key Vault om sleutels en geheimen voor schijfversleuteling te beheren.  Zie [Aan de slag met Azure Key Vault](../../key-vault/general/overview.md) en [Uw sleutelkluis beveiligen](../../key-vault/general/secure-your-key-vault.md) voor meer informatie over sleutelkluizen. 

> [!WARNING]
> - Als u eerder Azure Disk Encryption met Azure AD hebt gebruikt om een virtuele machine te versleutelen, moet u deze optie blijven gebruiken om uw virtuele machine te versleutelen. Zie [een sleutel kluis maken en configureren voor Azure Disk Encryption met Azure AD (vorige versie)](disk-encryption-key-vault-aad.md) voor meer informatie.

Een sleutelkluis maken en configureren voor gebruik met Azure Disk Encryption bestaat uit drie stappen:

> [!Note]
> U moet de optie selecteren in de beleids instellingen voor Azure Key Vault toegang om toegang tot Azure Disk Encryption voor volume versleuteling in te scha kelen. Als u de firewall hebt ingeschakeld op de sleutel kluis, gaat u naar het tabblad netwerken op de sleutel kluis en schakelt u toegang tot micro soft-vertrouwde services in. 

1. Een resourcegroep maken, indien nodig.
2. Een sleutelkluis maken. 
3. Geavanceerd toegangsbeleid voor sleutelkluis instellen.

Deze stappen worden geïllustreerd in de volgende quickstarts:

- [Een virtuele Windows-machine maken en versleutelen met behulp van Azure CLI](disk-encryption-cli-quickstart.md)
- [Een virtuele Windows-machine maken en versleutelen met behulp van Azure PowerShell](disk-encryption-powershell-quickstart.md)

U kunt eventueel ook een sleutelversleutelingssleutel genereren of importeren (KEK).

> [!Note]
> De stappen in dit artikel zijn geautomatiseerd in het [Azure Disk Encryption vereisten CLI-script](https://github.com/ejarvi/ade-cli-getting-started) en [Azure Disk Encryption vereisten Power shell-script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Hulpprogramma's installeren en verbinding maken met Azure

U kunt de stappen in dit artikel voltooien met de [Azure CLI](/cli/azure/), de [Azure PowerShell AZ-module](/powershell/azure/) of de [Azure-portal](https://portal.azure.com).

Terwijl de portal toegankelijk is via uw browser, hebben Azure CLI en Azure PowerShell lokale installatie nodig. Zie [Azure Disk Encryption voor Windows: hulpprogram Ma's installeren](disk-encryption-windows.md#install-tools-and-connect-to-azure) voor meer informatie.

### <a name="connect-to-your-azure-account"></a>Verbinding maken met uw Azure-account

Voordat u de Azure CLI of Azure PowerShell gebruikt, moet u eerst verbinding maken met uw Azure-abonnement. U doet dit door u aan te [melden met Azure cli](/cli/azure/authenticate-azure-cli), u aan te [melden met Azure Power shell](/powershell/azure/authenticate-azureps)of uw referenties aan de Azure portal toe te voegen wanneer u hierom wordt gevraagd.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Volgende stappen

- [SysteemAzure Disk Encryption vereisten CLI-script](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption van vereisten Power shell-script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Azure Disk Encryption scenario's voor Windows-vm's](disk-encryption-windows.md) leren
- Meer informatie over het [oplossen van Azure Disk Encryption](disk-encryption-troubleshooting.md)
- De [voorbeeld scripts van Azure Disk Encryption](disk-encryption-sample-scripts.md) lezen
