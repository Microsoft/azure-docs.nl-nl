---
title: Configuratie na de implementatie met behulp van extensies
description: Meer informatie over het gebruik van Azure Resource Manager sjabloon-uitbrei dingen (ARM-sjabloon) voor het leveren van configuraties na de implementatie.
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: 23d5a9aaa546542218a6f839ab415184ff309928
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97934319"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Configuraties na de implementatie opgeven met behulp van extensies

Azure Resource Manager sjabloon (ARM-sjabloon) uitbrei dingen zijn kleine toepassingen die configuratie van na de implementatie en automatiserings taken op Azure-resources bieden. De populairste versie is de extensie van de virtuele machine. Zie [extensies en functies van virtuele machines voor Windows](../../virtual-machines/extensions/features-windows.md)en [virtuele-machine uitbreidingen en-functies voor Linux](../../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Uitbreidingen

De bestaande uitbrei dingen zijn:

- [Micro soft. Compute/informatie/Extensions](/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Micro soft. Compute virtualMachineScaleSets/Extensions](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Micro soft. HDInsight-clusters/-extensies](/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters)
- [Micro soft. SQL-servers/data bases/uitbrei dingen](/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions)
- [Micro soft. web/sites/siteextensions](/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Als u de beschik bare uitbrei dingen wilt weten, bladert u naar de [sjabloon verwijzing](/azure/templates/). Voer in **filteren op titel** de **extensie** in.

Zie voor meer informatie over het gebruik van deze uitbrei dingen:

- [Zelf studie: virtuele-machine uitbreidingen implementeren met arm-sjablonen](template-tutorial-deploy-vm-extensions.md).
- [Zelfstudie: SQL BACPAC-bestanden met ARM-sjablonen importeren](template-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Extensies voor virtuele machines implementeren met ARM-sjablonen](template-tutorial-deploy-vm-extensions.md)
