---
title: Een schaalset voor virtuele machines maken en versleutelen met Azure Resource Manager-sjablonen
description: In deze quickstart leert u hoe u Azure Resource Manager-sjablonen kunt gebruiken om een schaalset voor virtuele machines te maken en te versleutelen
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 4284e94f8d8d0effd160c5048f54fcbede417e38
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86129752"
---
# <a name="encrypt-virtual-machine-scale-sets-with-azure-resource-manager"></a>Schaalsets voor virtuele machines versleutelen met Azure Resource Manager

U kunt schaalsets voor virtuele Linux-machines versleutelen of ontsleutelen met Azure Resource Manager-sjablonen.

## <a name="deploying-templates"></a>Sjablonen implementeren

Selecteer eerst de sjabloon die past bij uw scenario.

- [Schijfversleuteling inschakelen op een actieve Linux-schaalset voor virtuele machines](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Schijfversleuteling inschakelen op een actieve Windows-schaalset voor virtuele machines](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [Een virtuele-machineschaalset met Linux-VM’s implementeren met een jumpbox en versleuteling inschakelen voor virtuele-machineschaalsets van Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [Een virtuele-machineschaalset van Windows-VM's implementeren met een jumpbox en versleuteling inschakelen voor virtuele-machineschaalsets van Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Schijfversleuteling uitschakelen op een actieve Linux-schaalset voor virtuele machines](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Schijfversleuteling uitschakelen op een actieve Windows-schaalset voor virtuele machines](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

Volg dan deze stappen:

1. Klik op **Implementeren in Azure**.
2. Vul de vereiste velden in en ga akkoord met de voor waarden.
3. Klik op **kopen** om de sjabloon te implementeren.

## <a name="next-steps"></a>Volgende stappen

- [Azure Disk Encryption voor virtuele-machineschaalsets](disk-encryption-overview.md)
- [ Een virtuele-machineschaalset versleutelen met de Azure CLI](disk-encryption-cli.md)
- [Een virtuele-machineschaalset versleutelen met de Azure PowerShell](disk-encryption-powershell.md)
- [Een sleutelkluis voor Azure Disk Encryption maken en configureren](disk-encryption-key-vault.md)
- [Azure Disk Encryption gebruiken met extensiereeksen voor virtuele-machineschaalsets](disk-encryption-extension-sequencing.md)
