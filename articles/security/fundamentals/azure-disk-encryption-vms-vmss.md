---
title: Azure Disk Encryption voor virtuele machines en virtuele-machineschaalsets
description: Meer informatie over Azure Disk Encryption voor virtuele machines (Vm's) en VM-schaal sets. Azure Disk Encryption werkt zowel voor Linux-als Windows-Vm's.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: 21194bf2fe76a7eb0ee034d4a502c20ee3032dd9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87543670"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>Azure Disk Encryption voor virtuele machines en virtuele-machineschaalsets

Azure Disk Encryption kan worden toegepast op virtuele machines met Linux en Windows, en op virtuele-machine schaal sets. 

## <a name="linux-virtual-machines"></a>Virtuele Linux-machines

De volgende artikelen bevatten richt lijnen voor het versleutelen van virtuele Linux-machines.

### <a name="current-version-of-azure-disk-encryption"></a>Huidige versie van Azure Disk Encryption

- [Overzicht van Azure Disk Encryption voor Linux-VM's](../../virtual-machines/linux/disk-encryption-overview.md)
- [Azure Disk Encryption-scenario's voor Linux-VM's](../../virtual-machines/linux/disk-encryption-linux.md)
- [Een Linux-VM maken en versleutelen met behulp van Azure CLI](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [Een Linux-VM maken en versleutelen met behulp van Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [Een Linux-VM maken en versleutelen met behulp van Azure Portal](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [Azure Disk Encryption extensie schema voor Linux](../../virtual-machines/extensions/azure-disk-enc-linux.md)
- [Een sleutelkluis voor Azure Disk Encryption maken en configureren](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Voorbeeldscripts voor Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Problemen met Azure Disk Encryption oplossen](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Veelgestelde vragen over Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure Disk Encryption met Azure AD (vorige versie)

- [Overzicht van Azure Disk Encryption met Azure AD voor virtuele Linux-machines](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- [Azure Disk Encryption met Azure AD-scenario's op Linux-Vm's](../../virtual-machines/linux/disk-encryption-linux.md)
- [Een sleutel kluis maken en configureren voor Azure Disk Encryption met Azure AD (vorige versie)](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Virtuele Windows-machines

De volgende artikelen bevatten richt lijnen voor het versleutelen van virtuele Windows-machines.

### <a name="current-version-of-azure-disk-encryption"></a>Huidige versie van Azure Disk Encryption

- [Overzicht van Azure Disk Encryption voor Windows-VM's](../../virtual-machines/windows/disk-encryption-overview.md)
- [Azure Disk Encryption-scenario's voor Windows-VM's](../../virtual-machines/windows/disk-encryption-windows.md)
- [Een virtuele Windows-machine maken en versleutelen met behulp van Azure CLI](../../virtual-machines/windows/disk-encryption-cli-quickstart.md)
- [Een virtuele Windows-machine maken en versleutelen met behulp van Azure PowerShell](../../virtual-machines/windows/disk-encryption-powershell-quickstart.md)
- [Een virtuele Windows-machine maken en versleutelen met behulp van de Azure-portal](../../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- [Azure Disk Encryption extensie schema voor Windows](../../virtual-machines/extensions/azure-disk-enc-windows.md)
- [Een sleutelkluis voor Azure Disk Encryption maken en configureren](../../virtual-machines/windows/disk-encryption-key-vault.md)
- [Voorbeeldscripts voor Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-sample-scripts.md)
- [Problemen met Azure Disk Encryption oplossen](../../virtual-machines/windows/disk-encryption-troubleshooting.md)
- [Veelgestelde vragen over Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure Disk Encryption met Azure AD (vorige versie)

- [Overzicht van Azure Disk Encryption met Azure AD voor virtuele Windows-machines](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- [Azure Disk Encryption met Azure AD-scenario's op Windows-Vm's](../../virtual-machines/windows/disk-encryption-windows.md)
- [Een sleutel kluis maken en configureren voor Azure Disk Encryption met Azure AD (vorige versie)](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>Virtuele-machineschaalsets

De volgende artikelen bevatten richt lijnen voor het versleutelen van virtuele-machine schaal sets.

- [Overzicht van Azure Disk Encryption voor schaal sets voor virtuele machines](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [ Een virtuele-machineschaalset versleutelen met de Azure CLI](../../virtual-machine-scale-sets/disk-encryption-cli.md) 
- [Een schaalset voor virtuele machines versleutelen met behulp van Azure Power shell](../../virtual-machine-scale-sets/disk-encryption-powershell.md).
- [Een schaal sets voor virtuele machines versleutelen met behulp van de Azure Resource Manager](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [Een sleutelkluis voor Azure Disk Encryption maken en configureren](../../virtual-machine-scale-sets/disk-encryption-key-vault.md)
- [Azure Disk Encryption gebruiken met extensiereeksen voor virtuele-machineschaalsets](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md)

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure-versleuteling](encryption-overview.md)
- [Versleuteling van inactieve gegevens](encryption-atrest.md)
- [Best practices voor gegevensbeveiliging en -versleuteling](data-encryption-best-practices.md)
