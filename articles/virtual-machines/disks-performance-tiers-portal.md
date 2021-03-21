---
title: De prestaties van Azure Managed disks wijzigen met behulp van de Azure Portal
description: Meer informatie over het wijzigen van de prestatie lagen voor nieuwe en bestaande beheerde schijven met behulp van de Azure Portal.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 01/05/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 625fb1e3dd0b433da6b60f995aa6b380c23ec9ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97901016"
---
# <a name="change-your-performance-tier-using-the-azure-portal"></a>Uw prestatie niveau wijzigen met behulp van de Azure Portal

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="getting-started"></a>Aan de slag

### <a name="new-disks"></a>Nieuwe schijven

De volgende stappen laten zien hoe u de prestatie laag van de schijf wijzigt wanneer u de schijf voor het eerst maakt:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Ga naar de virtuele machine waarvoor u een nieuwe schijf wilt maken.
1. Wanneer u de nieuwe schijf selecteert, kiest u eerst de grootte van de schijf die u nodig hebt.
1. Wanneer u een grootte hebt geselecteerd, selecteert u een andere prestatie categorie om de prestaties te wijzigen.
1. Selecteer **OK** om de schijf te maken.

:::image type="content" source="media/disks-performance-tiers-portal/new-disk-change-performance-tier.png" alt-text="Scherm opname van de Blade schijf maken, een schijf is gemarkeerd en de vervolg keuzelijst voor de prestatie laag is gemarkeerd." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::


## <a name="existing-disks"></a>Bestaande schijven

In de volgende stappen wordt uitgelegd hoe u de prestatie tier van een bestaande schijf wijzigt:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Ga naar de virtuele machine met de schijf die u wilt wijzigen.
1. Maak de toewijzing van de virtuele machine ongedaan of ontkoppel de schijf.
1. Uw schijf selecteren
1. Selecteer **formaat en prestaties**.
1. Selecteer in de vervolg keuzelijst **prestatie laag** een andere laag dan de huidige prestatie laag van de schijf.
1. Selecteer **Formaat wijzigen**.

:::image type="content" source="media/disks-performance-tiers-portal/change-tier-existing-disk.png" alt-text="Scherm afbeelding van de Blade formaat en prestaties, prestatie niveau is gemarkeerd." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::

## <a name="next-steps"></a>Volgende stappen

Als u de grootte van een schijf moet wijzigen om te profiteren van de hogere prestatie lagen, raadpleegt u de volgende artikelen:

- [Virtuele harde schijven op een Linux VM uitbreiden met de Azure CLI](linux/expand-disks.md)
- [Een beheerde schijf uitbreiden die is gekoppeld aan een virtuele Windows-machine](windows/expand-os-disk.md)
