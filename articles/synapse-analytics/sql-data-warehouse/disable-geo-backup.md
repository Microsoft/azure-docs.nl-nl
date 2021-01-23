---
title: Geo-back-ups uitschakelen
description: Instructies voor het uitschakelen van geo-back-ups voor een toegewezen SQL-groep (voorheen SQL DW) in azure Synapse Analytics
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 01/06/2021
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 395d5f0697138155b0bb0c629461aada9e9c18c6
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98739116"
---
# <a name="disable-geo-backups-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Schakel geo-back-ups uit voor een toegewezen SQL-groep (voorheen SQL DW) in azure Synapse Analytics

In dit artikel leert u hoe u geo-back-ups kunt uitschakelen voor uw toegewezen SQL-groep (voorheen SQL DW) Azure Portal.

## <a name="disable-geo-backups-through-azure-portal"></a>Geo-back-ups via Azure Portal uitschakelen

Volg deze stappen om geo-back-ups voor uw toegewezen SQL-groep uit te scha kelen (voorheen SQL DW):

> [!NOTE]
> Als u geo-back-ups uitschakelt, kunt u uw toegewezen SQL-groep (voorheen SQL DW) niet meer herstellen naar een andere Azure-regio. 
>

1. Meld u aan bij uw [Azure Portal](https://portal.azure.com/) -account.
1. Selecteer de toegewezen SQL-groep (voorheen SQL DW) waarvoor u geo-back-ups wilt uitschakelen. 
1. Selecteer onder **instellingen** in het navigatie deel venster aan de linkerkant het **geo-back-upbeleid**.

   ![Geo-back-up uitschakelen](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-1.png)

1. Selecteer **uitgeschakeld** om geo-back-ups uit te scha kelen. 

   ![Geo-back-up uitgeschakeld](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-2.png)

1. Selecteer *Opslaan* om te controleren of uw instellingen zijn opgeslagen. 

   ![Geo-back-upinstellingen opslaan](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-3.png)

## <a name="next-steps"></a>Volgende stappen

- [Een bestaande toegewezen SQL-groep herstellen (voorheen SQL DW)](sql-data-warehouse-restore-active-paused-dw.md)
- [Een verwijderde toegewezen SQL-groep herstellen (voorheen SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
