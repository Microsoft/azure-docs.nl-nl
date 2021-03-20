---
title: Ondersteuning voor het verplaatsen van Azure SQL-resources tussen regio's met Azure resource-overschakeling.
description: Bekijk de ondersteuning voor het verplaatsen van Azure SQL-resources tussen regio's met Azure resource-overschakeling.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 22a7738c2d4d3cc02c03c233e0821f07b459dd94
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96452088"
---
# <a name="support-for-moving-azure-sql-resources-between-azure-regions"></a>Ondersteuning voor het verplaatsen van Azure SQL-resources tussen Azure-regio's

In dit artikel vindt u een overzicht van de ondersteuning en vereisten voor het verplaatsen van Azure SQL-resources tussen Azure-regio's met Azure resource-overschakeling.

## <a name="requirements"></a>Vereisten

In de volgende tabel vindt u een overzicht van de vereisten.

**Functie** | **Ondersteund/niet ondersteund** | **Details**
--- | --- | ---
**Azure SQL Database grootschalige** | Niet ondersteund | Kan geen data bases verplaatsen in de Azure SQL grootschalige-servicelaag met resource-overschakeling.
**Zoneredundantie** | Ondersteund |  Ondersteunde opties voor verplaatsen:<br/><br/> -Tussen regio's die ondersteuning bieden voor zone redundantie.<br/><br/> -Tussen regio's die geen zone redundantie ondersteunen.<br/><br/> -Tussen een regio die zone redundantie ondersteunt naar een regio die geen zone redundantie ondersteunt.<br/><br/> -Tussen een regio die geen ondersteuning biedt voor zone redundantie voor een regio die zone redundantie ondersteunt. 
**Gegevens synchronisatie** | Hub/synchronisatie database: niet ondersteund<br/><br/> Lid synchroniseren: ondersteund. | Als een Sync-lid wordt verplaatst, moet u de gegevens synchronisatie instellen voor de nieuwe doel database.
**Bestaande geo-replicatie** | Ondersteund | Bestaande geo-replica's worden opnieuw toegewezen aan de nieuwe primaire in de doel regio.<br/><br/> Seeding moet worden geïnitialiseerd na de verplaatsing. [Meer informatie](../azure-sql/database/active-geo-replication-configure-portal.md)
**Transparent Data Encryption (TDE) met Bring Your Own Key (BYOK)** | Ondersteund | Meer [informatie](../key-vault/general/move-region.md) over het verplaatsen van sleutel kluizen in verschillende regio's.
**TDE met door de service beheerde sleutel** | Ondersteund. |  Meer [informatie](../key-vault/general/move-region.md) over het verplaatsen van sleutel kluizen in verschillende regio's.
**Regels voor dynamische gegevens maskering** | Ondersteund. | Regels worden automatisch naar de doel regio gekopieerd als onderdeel van de verplaatsing. [Meer informatie](../azure-sql/database/dynamic-data-masking-configure-portal.md).
**Advanced data security** | Wordt niet ondersteund. | Tijdelijke oplossing: Stel in het doel gebied het SQL Server niveau in. [Meer informatie](../azure-sql/database/azure-defender-for-sql.md).
**Firewall-regels** | Wordt niet ondersteund. | Tijdelijke oplossing: Stel firewall regels in voor SQL Server in de doel regio. Firewall regels op database niveau worden van de bron server naar de doel server gekopieerd. [Meer informatie](../azure-sql/database/firewall-create-server-level-portal-quickstart.md).
**Controle beleid** | Wordt niet ondersteund. | Het beleid wordt na de verplaatsing opnieuw ingesteld op de standaard waarde. [Meer informatie](../azure-sql/database/auditing-overview.md) over hoe u opnieuw kunt instellen.
**Back-upretentie** | Ondersteund. | Het bewaarbeleid voor back-ups van de brondatabase wordt meegenomen naar de doeldatabase. [Meer informatie](../azure-sql/database/long-term-backup-retention-configure.md) over het wijzigen van instellingen na de verplaatsing.
**Automatisch afstemmen** | Wordt niet ondersteund. | Tijdelijke oplossing: Stel de instellingen voor automatisch afstemmen in na de verplaatsing. [Meer informatie](../azure-sql/database/automatic-tuning-enable.md).
**Database waarschuwingen** | Wordt niet ondersteund. | Tijdelijke oplossing: Stel waarschuwingen in na de verplaatsing. [Meer informatie](../azure-sql/database/alerts-insights-configure-portal.md).
**Azure SQL Server stretch data base** | Niet ondersteund | Kan SQL Server stretch-data bases niet verplaatsen met resource-overdrijfing.
**Azure Synapse Analytics** | Niet ondersteund | Kan Azure Synapse Analytics niet verplaatsen met resource-overdrijfing.
## <a name="next-steps"></a>Volgende stappen

Probeer [Azure SQL-resources](tutorial-move-region-sql.md) naar een andere regio met resource-overdrijfing.