---
title: Gedeelde VM-installatie kopieën gebruiken om een schaalset te maken in azure CLI
description: Meer informatie over hoe u de Azure CLI gebruikt om gedeelde VM-installatie kopieën te maken die u kunt gebruiken voor het implementeren van schaal sets voor virtuele machines in Azure.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: b5e9d5995e8173950db483c5c26a11830a62862e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444000"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Gedeelde installatie kopieën maken en gebruiken voor schaal sets voor virtuele machines met Azure CLI 2,0

Wanneer u een schaalset maakt, geeft u een installatiekopie op die moet worden gebruikt wanneer de VM-exemplaren zijn geïmplementeerd. Met een [Shared Image Gallery](../virtual-machines/shared-image-galleries.md) kunt u het delen van aangepaste installatiekopieën in uw organisatie vereenvoudigen. Aangepaste installatiekopieën zijn soortgelijk aan Marketplace-installatiekopieën, maar u kunt deze zelf maken. Aangepaste installatiekopieën kunnen worden gebruikt voor het opstarten van configuraties, zoals het vooraf laden van toepassingen, toepassingsconfiguraties en andere besturingssysteemconfiguraties. 

Met de galerie gedeelde afbeeldingen kunt u uw installatie kopieën delen met anderen. U kunt kiezen welke installatiekopieën u wilt delen, in welke regio’s u ze beschikbaar wilt maken en met wie u ze wilt delen. 


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>Volgende stappen

Een installatie kopie versie maken op basis van een [virtuele machine](../virtual-machines/image-version-vm-cli.md)of een [beheerde installatie kopie](../virtual-machines/image-version-managed-image-cli.md).

Zie het [overzicht](../virtual-machines/shared-image-galleries.md)voor meer informatie over gedeelde afbeeldings galerieën. Als u problemen ondervindt, raadpleegt u [problemen met de galerie met gedeelde afbeeldingen oplossen](../virtual-machines/troubleshooting-shared-images.md).
