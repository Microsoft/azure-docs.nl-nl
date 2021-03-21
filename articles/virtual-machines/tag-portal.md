---
title: Een virtuele machine labelen met behulp van de Azure Portal
description: Meer informatie over het coderen van een virtuele machine met behulp van de Azure Portal.
ms.topic: how-to
ms.workload: infrastructure-services
author: cynthn
ms.service: virtual-machines
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 9c220814ae1f714e9eac0c0c11a50d9cf68a621d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897405"
---
# <a name="tagging-a-vm-using-the-portal"></a>Een virtuele machine coderen met behulp van de portal

In dit artikel wordt beschreven hoe u tags aan een virtuele machine toevoegt met behulp van de portal. Tags zijn door de gebruiker gedefinieerde sleutel/waarde-paren die rechtstreeks kunnen worden geplaatst op een resource of resource groep. Azure ondersteunt momenteel Maxi maal 50 Tags per resource en resource groep. Labels kunnen worden geplaatst op een resource op het moment dat ze worden gemaakt of worden toegevoegd aan een bestaande resource.


1. Navigeer naar uw virtuele machine in de portal.
1. Selecteer in **Essentials** **hier om tags toe te voegen**.

    :::image type="content" source="media/tag/azure-portal-tag.png" alt-text="Scherm afbeelding van de sectie Essentials van de VM-pagina.":::

1. Voeg een waarde toe voor **naam** en **waarde**, en selecteer vervolgens **Opslaan**.

    :::image type="content" source="media/tag/key-value.png" alt-text="Scherm afbeelding van de pagina voor het toevoegen van een sleutel waarde-paar als een tag.":::

### <a name="next-steps"></a>Volgende stappen

- Zie [Azure Resource Manager Overview](../azure-resource-manager/management/overview.md) en [Tags gebruiken om uw Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)voor meer informatie over het coderen van uw Azure-resources.
- Zie [informatie over uw Azure-factuur](../cost-management-billing/understand/review-individual-bill.md)als u wilt weten hoe Tags u kunnen helpen bij het beheren van uw gebruik van Azure-resources.
