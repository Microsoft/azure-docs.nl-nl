---
title: Prestatie lagen voor Azure Managed disks
description: Meer informatie over prestatie lagen voor beheerde schijven.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 11/19/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 28980756ac9e41c9477d687ea9df608b512759e3
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94986777"
---
# <a name="performance-tiers-for-managed-disks"></a>Prestatie lagen voor beheerde schijven

De prestaties van uw Azure Managed disk worden ingesteld wanneer u de schijf maakt, in de vorm van de prestatie-laag. De laag prestaties bepaalt de IOPS en door Voer van uw beheerde schijf. Wanneer u de ingerichte grootte van uw schijf instelt, wordt automatisch een laag voor prestaties geselecteerd. De laag prestaties kan worden gewijzigd tijdens de implementatie of daarna, zonder de grootte van de schijf te wijzigen.

Door de prestatie-laag te wijzigen, kunt u een hogere vraag voorbereiden en voldoen zonder de bursting-mogelijkheid van uw schijf te gebruiken. Het kan voordeliger zijn om uw prestatie niveau te wijzigen in plaats van te vertrouwen op bursting, afhankelijk van hoe lang de extra prestaties nodig zijn. Dit is ideaal voor gebeurtenissen die tijdelijk een consistent hoger prestatie niveau vereisen, zoals het kopen van vakantie, het testen van prestaties of het uitvoeren van een trainings omgeving. Als u deze gebeurtenissen wilt verwerken, kunt u een hogere prestatie laag gebruiken, zolang u deze nodig hebt. U kunt vervolgens terugkeren naar de oorspronkelijke laag wanneer u de extra prestaties niet meer nodig hebt.

## <a name="how-it-works"></a>Uitleg

Wanneer u een schijf voor het eerst implementeert of inricht, wordt de basislijn prestatie laag voor die schijf ingesteld op basis van de ingerichte schijf grootte. U kunt een prestatie niveau hoger dan de oorspronkelijke basis lijn gebruiken om te voldoen aan de hogere vraag. Wanneer u dat prestatie niveau niet meer nodig hebt, kunt u teruggaan naar de initiële prestatie laag van de basis lijn.

Uw facturering wordt gewijzigd wanneer de laag met de prestaties wordt gewijzigd. Als u bijvoorbeeld een P10-schijf inricht (128 GiB), wordt de laag voor basislijn prestaties ingesteld op P10 (500 IOPS en 100 MBps). U wordt gefactureerd tegen het P10ings bedrag. U kunt de laag bijwerken zodat deze overeenkomt met de prestaties van P50 (7.500 IOPS en 250 MBps) zonder de schijf grootte te verg Roten. Tijdens de upgrade wordt u gefactureerd tegen het P50ings bedrag. Wanneer u de hogere prestaties niet meer nodig hebt, kunt u teruggaan naar de P10-laag. De schijf wordt opnieuw gefactureerd tegen het P10ings bedrag.

| Schijfgrootte | Prestatie niveau basis lijn | Kan worden bijgewerkt naar |
|----------------|-----|-------------------------------------|
| 4 GiB | P1 | P2, P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 8 GiB | P2 | P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 16 GiB | P3 | P4, P6, P10, P15, P20, P30, P40, P50 | 
| 32 GiB | P4 | P6, P10, P15, P20, P30, P40, P50 |
| 64 GiB | P6 | P10, P15, P20, P30, P40, P50 |
| 128 GiB | P10 | P15, P20, P30, P40, P50 |
| 256 GiB | P15 | P20, P30, P40, P50 |
| 512 GiB | P20 | P30, P40, P50 |
| 1 TiB | P30 | P40, P50 |
| 2 TiB | P40 | P50 |
| 4 TiB | P50 | Geen |
| 8 TiB | P60 |  P70, P80 |
| 16 TiB | P70 | P80 |
| 32 TiB | P80 | Geen |

Zie [prijzen voor beheerde schijven](https://azure.microsoft.com/pricing/details/managed-disks/)voor informatie over facturering.

## <a name="restrictions"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="next-steps"></a>Volgende stappen

Zie [Portal](disks-performance-tiers-portal.md) [-of Power shell/cli-](disks-performance-tiers.md) artikelen voor meer informatie over het wijzigen van de prestatie categorie.

