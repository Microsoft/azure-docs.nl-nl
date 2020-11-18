---
title: Een galerie met gedeelde afbeeldingen maken met Azure PowerShell
description: Meer informatie over het gebruik van Azure PowerShell voor het maken van een galerie met gedeelde afbeeldingen in azure
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 1d135c6fe4e867a9b270e2a5c1b986e1957e7012
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843839"
---
# <a name="create-a-shared-image-gallery-with-azure-powershell"></a>Een galerie met gedeelde afbeeldingen maken met Azure PowerShell 

Met een [Shared Image Gallery](./windows/shared-image-galleries.md) kunt u het delen van aangepaste installatiekopieën in uw organisatie vereenvoudigen. Aangepaste installatiekopieën zijn soortgelijk aan Marketplace-installatiekopieën, maar u kunt deze zelf maken. Aangepaste installatie kopieën kunnen worden gebruikt om implementatie taken te Boots trapn, zoals het vooraf laden van toepassingen, toepassings configuraties en andere besturingssysteem configuraties. 

Met de galerie voor gedeelde afbeeldingen kunt u uw aangepaste VM-installatie kopieën delen met anderen in uw organisatie, binnen of tussen verschillende regio's binnen een AAD-Tenant. U kunt kiezen welke installatiekopieën u wilt delen, in welke regio’s u ze beschikbaar wilt maken en met wie u ze wilt delen. U kunt meerdere galerieën maken zodat u gedeelde installatie kopieën logisch kunt groeperen. 

De galerie is een resource op het hoogste niveau die volledige toegangs beheer op basis van rollen (Azure RBAC) biedt. Installatie kopieën kunnen versie nummer hebben en u kunt ervoor kiezen om elke installatie kopie versie te repliceren naar een andere set Azure-regio's. De galerie werkt alleen met beheerde installatie kopieën.

De functie gedeelde installatie kopie galerie heeft meerdere bron typen. 

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../includes/virtual-machines-shared-image-gallery-resources.md)]


[!INCLUDE [virtual-machines-common-shared-images-powershell](../../includes/virtual-machines-common-shared-images-powershell.md)]


## <a name="next-steps"></a>Volgende stappen

Een installatie kopie maken van een [virtuele machine](image-version-vm-powershell.md), een [beheerde installatie kopie](image-version-managed-image-powershell.md)of een [installatie kopie in een andere galerie](image-version-another-gallery-powershell.md).

Met [Azure Image Builder (preview)](./windows/image-builder-overview.md) kunt u het maken van de installatie kopie versie automatiseren, maar u kunt deze zelfs gebruiken om [een nieuwe installatie kopie versie te maken op basis van een bestaande versie van de installatie kopie](./windows/image-builder-gallery-update-image-version.md). 

U kunt ook een resource voor de galerie met gedeelde afbeeldingen maken met behulp van sjablonen. Er zijn verschillende Azure-quickstart-sjablonen beschikbaar: 

- [Een gedeelde installatiekopiegalerie maken](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Een installatiekopiedefinitie maken in een gedeelde installatiekopiegalerie](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Een installatiekopieversie maken in een gedeelde installatiekopiegalerie](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Een VM maken van een installatiekopieversie](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


