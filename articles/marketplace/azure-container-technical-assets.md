---
title: De technische assets van uw Azure-container voorbereiden
description: Technische resources en richtlijnen om u te helpen bij het configureren van een containeraanbieding op Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/30/2021
ms.openlocfilehash: 0bbb1cb1cac4f531b090fc49bd7848b2bb4a2b8f
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107892672"
---
# <a name="prepare-azure-container-technical-assets"></a>Technische assets voor Azure-container voorbereiden

In dit artikel vindt u technische resources en aanbevelingen voor het maken van een containeraanbieding op Azure Marketplace.

## <a name="before-you-begin"></a>Voordat u begint

Zie de documentatie voor quickstarts, zelfstudies en [voorbeelden Azure Container Instances documentatie.](../container-instances/index.yml)

## <a name="fundamental-technical-knowledge"></a>Fundamentele technische kennis

Het ontwerpen, bouwen en testen van deze assets kost tijd en vereist technische kennis van zowel het Azure-platform als de technologieën die worden gebruikt om de aanbieding te bouwen.

Naast uw oplossingsdomein moet uw engineeringteam kennis hebben van de volgende Microsoft-technologieën:

- Basiskennis van [Azure-services](https://azure.microsoft.com/services/)
- [Azure-toepassingen ontwerpen en ontwerpen](https://azure.microsoft.com/solutions/architecture/)
- Werkende kennis van [Azure Virtual Machines,](https://azure.microsoft.com/services/virtual-machines/) [Azure Storage](https://azure.microsoft.com/services/?filter=storage)en [Azure-netwerken](https://azure.microsoft.com/services/?filter=networking)
- Werkende kennis van [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Working Knowledge of [JSON](https://www.json.org/).

## <a name="suggested-tools"></a>Voorgestelde hulpprogramma's

Kies een of beide van de volgende scriptomgevingen om uw Container-afbeelding te beheren:

- [Azure PowerShell](/powershell/azure/)
- [Azure-CLI](/cli/azure/)

We raden u aan deze hulpprogramma's toe te voegen aan uw ontwikkelomgeving:

- [Azure-opslagverkenner](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Extensie: [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Extensie: [Signtify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Extensie: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Bekijk de beschikbare hulpprogramma's op de pagina Ontwikkelhulpprogramma's Azure-hulpprogramma's. [](https://azure.microsoft.com/) Als u gebruik maakt van Visual Studio, bekijkt u de hulpprogramma's die beschikbaar zijn in [Visual Studio Marketplace.](https://marketplace.visualstudio.com/)

## <a name="create-the-container-image"></a>De containerafbeelding maken

U kunt een afbeelding niet implementeren Azure Container Instances vanuit een on-premises register.

- Als u al een werkende container in uw lokale register hebt, maakt u een Azure-register en uploadt u de containerafbeelding naar de Azure Container Registry. Zie Zelfstudie: Containerafbeeldingen bouwen en implementeren [in de cloud](../container-registry/container-registry-tutorial-quick-task.md)met Azure Container Registry-taken.

- Als u nog geen containerafbeelding hebt en uw bestaande toepassing in een container moet zetten of een nieuwe op containers gebaseerde toepassing moet maken, kloont u de broncode van de toepassing vanuit GitHub, maakt u een containerafbeelding van de toepassingsbron en test u de afbeelding in een lokale Docker-omgeving. Zie Zelfstudie: Een [containerafbeelding](../container-instances/container-instances-tutorial-prepare-app.md)maken voor implementatie naar Azure Container Instances.

## <a name="next-steps"></a>Volgende stappen

- [Uw containeraanbieding maken](azure-container-offer-setup.md)