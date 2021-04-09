---
title: De technische activa van uw Azure-container voorbereiden
description: Technische bronnen en richt lijnen om u te helpen bij het configureren van een container aanbod op Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 11/30/2020
ms.openlocfilehash: 014bcd6fc519c267cdf17e9e98b850425c25ead6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96459336"
---
# <a name="prepare-your-azure-container-technical-assets"></a>De technische activa van uw Azure-container voorbereiden

Dit artikel bevat technische bronnen en aanbevelingen om u te helpen bij het maken van een container aanbod op Azure Marketplace.

## <a name="before-you-begin"></a>Voordat u begint

Raadpleeg de [documentatie van Azure container instances](../container-instances/index.yml)voor Quick starts, zelf studies en voor beelden.

## <a name="fundamental-technical-knowledge"></a>Fundamentele technische kennis

Het ontwerpen, bouwen en testen van deze assets vergt tijd en vereist technische kennis van het Azure-platform en de technologieën die worden gebruikt om de aanbieding te bouwen.

Naast uw oplossings domein moet uw technische team kennis hebben van de volgende micro soft-technologieën:

- Basis informatie over [Azure-Services](https://azure.microsoft.com/services/)
- Azure- [toepassingen ontwerpen en ontwikkelen](https://azure.microsoft.com/solutions/architecture/)
- Praktische kennis van [Azure-virtual machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage)en [Azure-netwerken](https://azure.microsoft.com/services/?filter=networking)
- Werk ervaring van [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Werk kennis van [JSON](https://www.json.org/).

## <a name="suggested-tools"></a>Aanbevolen hulpprogram ma's

Kies een of beide van de volgende script omgevingen om uw container installatie kopie te beheren:

- [Azure PowerShell](/powershell/azure/)
- [Azure CLI](/cli/azure/).

U wordt aangeraden deze hulpprogram ma's toe te voegen aan uw ontwikkel omgeving:

- [Azure-opslagverkenner](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Extensie: [Azure Resource Manager-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Extensie: [beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Extensie: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Bekijk de beschik bare hulpprogram ma's op de pagina [Azure Ontwikkelhulpprogramma's](https://azure.microsoft.com/) . Als u Visual Studio gebruikt, raadpleegt u de hulpprogram ma's die beschikbaar zijn in de [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>De container installatie kopie maken

U kunt geen installatie kopie implementeren naar Azure Container Instances vanuit een on-premises REGI ster.

- Als u al een werkende container in uw lokale REGI ster hebt, maakt u een Azure-REGI ster en uploadt u de container installatie kopie naar de Azure Container Registry. Zie voor meer informatie [zelf studie: container installatie kopieën bouwen en implementeren in de Cloud met Azure container Registry taken](../container-registry/container-registry-tutorial-quick-task.md).

- Als u nog geen container installatie kopie hebt en u de bestaande toepassing wilt container plaatsen of een nieuwe toepassing op basis van een container wilt maken, moet u de bron code van de toepassing klonen vanuit GitHub, een container installatie kopie maken uit de toepassings bron en de installatie kopie testen in een lokale docker-omgeving. Zie [zelf studie: een container installatie kopie maken voor implementatie naar Azure container instances voor](../container-instances/container-instances-tutorial-prepare-app.md)meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Uw container aanbieding maken](create-azure-container-offer.md)