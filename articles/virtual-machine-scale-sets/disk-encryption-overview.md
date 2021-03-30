---
title: Azure Disk Encryption voor Virtual Machine Scale Sets inschakelen
description: In dit artikel vindt u instructies voor het inschakelen van Microsoft Azure schijf versleuteling voor Virtual Machine Scale Sets
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 6b9805d66149a18216a200bc89a79b3e06106c9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "83195116"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Azure Disk Encryption voor Virtual Machine Scale Sets

Azure Disk Encryption biedt volume versleuteling voor het besturings systeem en de gegevens schijven van uw virtuele machines, waardoor uw gegevens worden beschermd en beveiligd om te voldoen aan de verplichtingen van de organisatie beveiliging en naleving. Zie [Azure Disk Encryption: virtuele Linux-machines](../virtual-machines/linux/disk-encryption-overview.md) en- [Azure Disk Encryption: Windows-vm's](../virtual-machines/windows/disk-encryption-overview.md) voor meer informatie.  

Azure Disk Encryption kan ook worden toegepast op schaal sets voor virtuele Windows-en Linux-machines, in deze gevallen:
- Schaal sets die zijn gemaakt met Managed disks. Azure Disk Encryption wordt niet ondersteund voor een systeem eigen (of niet-beheerde) schijf schaal sets.
- Besturings systeem-en gegevens volumes in Windows-schaal sets.
- Gegevens volumes in Linux-schaal sets. Versleuteling van de besturingssysteem schijf wordt op dit moment niet ondersteund voor Linux-schaal sets.

In slechts enkele minuten kunt u de basis principes van Azure Disk Encryption voor virtuele-machine schaal sets [versleutelen met behulp van de Azure cli](disk-encryption-cli.md) of de [schaal sets van virtuele machines versleutelen met behulp van de Azure PowerShell](disk-encryption-powershell.md) zelf studies.

## <a name="next-steps"></a>Volgende stappen

- [Een schaal sets voor virtuele machines versleutelen met behulp van de Azure Resource Manager](disk-encryption-azure-resource-manager.md)
- [Een sleutelkluis voor Azure Disk Encryption maken en configureren](disk-encryption-key-vault.md)
- [Azure Disk Encryption gebruiken met extensiereeksen voor virtuele-machineschaalsets](disk-encryption-extension-sequencing.md)
