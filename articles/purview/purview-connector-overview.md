---
title: Overzicht van controle sfeer liggen-connector
description: In dit artikel vindt u een overzicht van de verschillende gegevens archieven en-functies die worden ondersteund in controle sfeer liggen
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 88fb9c823df6ae5df345911ccce1c579009fba02
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2021
ms.locfileid: "96780209"
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
||[Beheerd exemplaar van Azure SQL Database](register-scan-azure-sql-database-managed-instance.md)|Ja| Ja| Nee| Ja| Ja| Ja|
||[Azure Synapse Analytics (voorheen SQL DW)](register-scan-azure-synapse-analytics.md)|Ja| Ja| Nee| Ja| Ja| Ja|
|Database|[SQL Server](register-scan-on-premises-sql-server.md)|Ja| Ja| Nee| Ja| Ja| Ja|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|Ja| Ja| Nee| Nee| Nee| Ja|

## <a name="next-steps"></a>Volgende stappen

- [Azure Blob-opslag bron registreren en scannen](register-scan-azure-blob-storage-source.md)