---
title: Een sleutelkluis maken en configureren voor Azure Disk Encryption op een Windows-VM
description: Dit artikel bevat stappen voor het maken en configureren van een sleutelkluis voor gebruik met Azure Disk Encryption een Windows-VM.
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: b9e61a72af1e2d07fc45a9265de6008fa4e509ef
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813772"
---
# <a name="create-and-configure-a-key-vault-for-azure-disk-encryption-on-a-windows-vm"></a>Een sleutelkluis maken en configureren Azure Disk Encryption een Windows-VM

Azure Disk Encryption gebruikt Azure Key Vault om sleutels en geheimen voor schijfversleuteling te beheren.  Zie [Aan de slag met Azure Key Vault](../../key-vault/general/overview.md) en [Uw sleutelkluis beveiligen](../../key-vault/general/security-features.md) voor meer informatie over sleutelkluizen. 

> [!WARNING]
> - Als u eerder een Azure Disk Encryption Azure AD hebt gebruikt om een VM te versleutelen, moet u deze optie blijven gebruiken om uw VM te versleutelen. Zie [Een sleutelkluis maken en configureren voor Azure Disk Encryption met Azure AD (vorige versie)](disk-encryption-key-vault-aad.md) voor meer informatie.

Een sleutelkluis maken en configureren voor gebruik met Azure Disk Encryption bestaat uit drie stappen:

> [!Note]
> U moet de optie selecteren in de instellingen Azure Key Vault toegangsbeleid om toegang tot Azure Disk Encryption voor volumeversleuteling in te stellen. Als u de firewall in de sleutelkluis hebt ingeschakeld, moet u naar het tabblad Netwerken in de sleutelkluis gaan en toegang tot vertrouwde Microsoft-services inschakelen. 

1. Een resourcegroep maken, indien nodig.
2. Een sleutelkluis maken. 
3. Geavanceerd toegangsbeleid voor sleutelkluis instellen.

Deze stappen worden geïllustreerd in de volgende quickstarts:

- [Een virtuele Windows-machine maken en versleutelen met behulp van Azure CLI](disk-encryption-cli-quickstart.md)
- [Een virtuele Windows-machine maken en versleutelen met behulp van Azure PowerShell](disk-encryption-powershell-quickstart.md)

U kunt eventueel ook een sleutelversleutelingssleutel genereren of importeren (KEK).

> [!Note]
> De stappen in dit artikel worden geautomatiseerd in het [CLI Azure Disk Encryption script](https://github.com/ejarvi/ade-cli-getting-started) met vereisten en Azure Disk Encryption [PowerShell-script.](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)

## <a name="install-tools-and-connect-to-azure"></a>Hulpprogramma's installeren en verbinding maken met Azure

U kunt de stappen in dit artikel voltooien met de [Azure CLI](/cli/azure/), de [Azure PowerShell AZ-module](/powershell/azure/) of de [Azure-portal](https://portal.azure.com).

Hoewel de portal toegankelijk is via uw browser, is voor Azure CLI en Azure PowerShell lokale installatie vereist; zie [Azure Disk Encryption voor Windows: Hulpprogramma's installeren](disk-encryption-windows.md#install-tools-and-connect-to-azure) voor meer informatie.

### <a name="connect-to-your-azure-account"></a>Verbinding maken met uw Azure-account

Voordat u de Azure CLI of Azure PowerShell gebruikt, moet u eerst verbinding maken met uw Azure-abonnement. U doet dit door u aan te melden met [Azure CLI,](/cli/azure/authenticate-azure-cli)u aan te melden met [Azure Powershell](/powershell/azure/authenticate-azureps)of door uw referenties op te geven aan de Azure Portal wanneer u hierom wordt gevraagd.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Volgende stappen

- [Azure Disk Encryption CLI-script voor vereisten](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption PowerShell-script voor vereisten](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Meer [informatie Azure Disk Encryption scenario's op Windows-VM's](disk-encryption-windows.md)
- Meer informatie over het [oplossen van problemen Azure Disk Encryption](disk-encryption-troubleshooting.md)
- De [voorbeeldscripts Azure Disk Encryption lezen](disk-encryption-sample-scripts.md)
