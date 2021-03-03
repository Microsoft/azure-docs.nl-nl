---
title: Overzicht van controle sfeer liggen-connector
description: In dit artikel vindt u een overzicht van de verschillende gegevens archieven en-functies die worden ondersteund in controle sfeer liggen
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 08b22af8743082bab1d547205e51917cb9d92a11
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695767"
---
# <a name="supported-data-stores"></a>Ondersteunde gegevensarchieven

Controle sfeer liggen ondersteunt de volgende gegevens archieven. Klik op elk gegevens Archief voor meer informatie over de ondersteunde mogelijkheden en de bijbehorende configuraties.

## <a name="purview-data-sources"></a>Controle sfeer liggen-gegevens bronnen

|**Categorie**|  **Gegevens archief**  |**Extractie van meta gegevens**|**Volledige scan**|**Incrementele scan**|**Scoped scan**|**Classificatie**|**Herkomst**|
|---|---|---|---|---|---|---|---|
| Azure | [Azure Blob Storage](register-scan-azure-blob-storage-source.md)| Ja| Ja| Ja| Ja| Ja| Ja|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)|Ja| Ja| Ja| Ja| Ja| Ja|
||[Azure Data Explorer](register-scan-azure-data-explorer.md)|Ja| Ja| Ja| Ja| Ja| Ja|
||[Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)|Ja| Ja| Ja| Ja| Ja| Ja|
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)|Ja| Ja| Ja| Ja| Ja| Ja|
||[Azure SQL Database](register-scan-azure-sql-database.md)|Ja| Ja| Nee| Ja| Ja| Ja|
||[Beheerde instantie Azure SQL Database](register-scan-azure-sql-database-managed-instance.md)|Ja| Ja| Nee| Ja| Ja| Ja|
||[Azure Synapse Analytics (voorheen SQL DW)](register-scan-azure-synapse-analytics.md)|Ja| Ja| Nee| Ja| Ja| Ja|
|Database|[Oracle DB](register-scan-oracle-source.md)|Ja| Ja| Nee| Nee| Nee| Ja|
||[SQL Server](register-scan-on-premises-sql-server.md)|Ja| Ja| Nee| Ja| Ja| Ja|
||[Teradata](register-scan-teradata-source.md)|Ja| Ja| Nee| Nee| Nee| Ja|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|Ja| Ja| Nee| Nee| Nee| Ja|
|Services en apps|[SAP ECC](register-scan-sapecc-source.md)|Ja| Ja| Nee| Ja| Ja| Ja|
||[SAP-S4HANA](register-scan-saps4hana-source.md)|Ja| Ja| Nee| Ja| Ja| Ja|

## <a name="next-steps"></a>Volgende stappen

- [Azure Blob-opslag bron registreren en scannen](register-scan-azure-blob-storage-source.md)