---
title: Voldoen aan de vereisten voor gegevens locatie in Azure Cosmos DB
description: meer informatie over het voldoen aan de vereisten voor gegevens locatie in Azure Cosmos DB zodat uw gegevens en back-ups in één regio blijven.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: c5f8a4361774368e3934d1e2b16c8311876f5caa
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491862"
---
# <a name="how-to-meet-data-residency-requirements-in-azure-cosmos-db"></a>Voldoen aan de vereisten voor gegevens locatie in Azure Cosmos DB

In Azure Cosmos DB kunt u uw gegevens en back-ups zodanig configureren dat ze in één regio blijven om te voldoen aan de[ locatie-vereisten.](https://azure.microsoft.com/en-us/global-infrastructure/data-residency/)

## <a name="residency-requirements-for-data"></a>Locatie-vereisten voor gegevens

In Azure Cosmos DB moet u de gegevens replicatie tussen regio's expliciet configureren. Meer informatie over het configureren van geo-replicatie met behulp van [Azure Portal](how-to-manage-database-account.md#addremove-regions-from-your-database-account) [Azure cli](scripts/cli/common/regions.md). Om aan de vereisten voor gegevens locatie te voldoen, kunt u een Azure-beleid maken waarmee bepaalde regio's gegevens replicatie naar ongewenste regio's kunnen voor komen.

## <a name="residency-requirements-for-backups"></a>Locatie-vereisten voor back-ups

**Back-ups in permanente modus**: deze back-ups zijn standaard Resident, omdat ze worden opgeslagen in lokaal redundante of zone redundante opslag. Zie het artikel [continu back-up](continuous-backup-restore-portal.md) voor meer informatie.

**Back-ups** van de periodieke modus: standaard worden back-ups van de periodieke modus opgeslagen in geografisch redundante opslag. Voor periodieke back-upmoduss kunt u gegevens redundantie configureren op het niveau van de account. Er zijn drie redundantie opties voor de back-upopslag. Deze zijn lokale redundantie, zone redundantie of geo-redundantie. Zie [back-upredundantie configureren](configure-periodic-backup-restore.md#configure-backup-interval-retention) met behulp van de portal voor meer informatie.

## <a name="use-azure-policy-to-enforce-the-residency-requirements"></a>Azure Policy gebruiken om de locatie-vereisten af te dwingen

Als u gegevens locatie vereisten hebt waarbij u al uw gegevens in één Azure-regio moet houden, kunt u zone-redundante of lokaal redundante back-ups voor uw account afdwingen met behulp van een Azure Policy.  U kunt ook een beleid afdwingen dat de Azure Cosmos DB accounts niet Geo-gerepliceerd zijn naar andere regio's.

Azure Policy is een service die u kunt gebruiken om beleid te maken, toe te wijzen en te beheren waarmee regels worden toegepast op Azure-resources. Azure Policy helpt u bij het houden van deze resources die voldoen aan uw bedrijfs normen en service overeenkomsten. Zie [Azure Policy](policy.md) gebruiken om governance en controles voor Azure Cosmos DB resources te implementeren voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Periodieke back-ups configureren en beheren met [Azure Portal](configure-periodic-backup-restore.md)
* Configureer en beheer doorlopende back-ups met behulp van [Azure Portal](continuous-backup-restore-portal.md), [Power shell](continuous-backup-restore-powershell.md), [cli](continuous-backup-restore-command-line.md)of [Azure Resource Manager](continuous-backup-restore-template.md).
