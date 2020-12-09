---
title: Ondersteunde gegevensarchieven in Azure Data Share
description: Meer informatie over de gegevens archieven die worden ondersteund voor het gebruik van Azure data share.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 12/7/2020
ms.openlocfilehash: 566fd2c9c31933420769f7200a0434cc53f8c2f3
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853136"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Ondersteunde gegevensarchieven in Azure Data Share

Azure data share biedt open en flexibel delen van gegevens, met inbegrip van de mogelijkheid om te delen vanuit en naar verschillende gegevens archieven. Gegevens providers kunnen gegevens delen van het ene type gegevens archief en hun gegevens gebruikers kunnen kiezen met welke gegevens opslag gegevens moeten worden ontvangen. 

In dit artikel vindt u meer informatie over de uitgebreide set met Azure-gegevens archieven die worden ondersteund in azure data share. U kunt ook informatie vinden over de combi Naties van gegevens archieven die kunnen worden gebruikt door gegevens providers en gegevens gebruikers. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Welke gegevens archieven worden ondersteund in azure data share? 

De onderstaande tabel bevat een overzicht van de ondersteunde gegevens bronnen voor Azure data share. 

| Gegevensarchief | Op moment opnamen gebaseerd delen (volledige moment opname) | Op moment opnamen gebaseerd delen (incrementele moment opname) | In-place delen 
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ |✓ | |
| Azure Data Lake Storage Gen1 |✓ |✓ | |
| Azure Data Lake Storage Gen2 |✓ |✓ ||
| Azure SQL Database |✓ | | |
| Azure Synapse Analytics (voorheen Azure SQL DW) |✓ | | |
| Exclusieve SQL-groep voor Azure Synapse Analytics (werk ruimte) |✓ | | |
| Azure Data Explorer | | |✓ |

## <a name="data-store-support-matrix"></a>Ondersteunings matrix voor gegevens opslag

Azure-gegevens share biedt gegevens gebruikers flexibiliteit bij het bepalen van een gegevens archief om gegevens te accepteren in. Gegevens die vanuit Azure SQL Database worden gedeeld, kunnen bijvoorbeeld worden ontvangen in Azure Data Lake Store Gen2, Azure SQL Database of Azure Synapse Analytics. Klanten kunnen kiezen in welke indeling gegevens moeten worden ontvangen bij het configureren van een ontvangen gegevens share. 

De onderstaande tabel bevat informatie over verschillende combi Naties en keuzen die gebruikers van gegevens hebben wanneer ze hun gegevens delen accepteren en configureren. Zie [toewijzing van gegevensset configureren](how-to-configure-mapping.md)voor meer informatie over het configureren van gegevensset-toewijzingen.

| Gegevensarchief | Azure Blob Storage | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | Azure SQL Database | Azure Synapse Analytics (voorheen Azure SQL DW) | Exclusieve SQL-groep voor Azure Synapse Analytics (werk ruimte) | Azure Data Explorer
|:--- |:--- |:--- |:--- |:--- |:--- |:--- | :--- |
| Azure Blob Storage | ✓ || ✓ |||
| Azure Data Lake Storage Gen1 | ✓ | | ✓ |||
| Azure Data Lake Storage Gen2 | ✓ | | ✓ |||
| Azure SQL Database | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Azure Synapse Analytics (voorheen Azure SQL DW) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Exclusieve SQL-groep voor Azure Synapse Analytics (werk ruimte) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Azure Data Explorer ||||||| ✓ |

## <a name="share-from-a-storage-account"></a>Delen vanuit een opslagaccount
De Azure-gegevens share ondersteunt het delen van bestanden, mappen en bestands systemen van Azure Data Lake gen1 en Azure Data Lake Gen2. Het biedt ook ondersteuning voor het delen van blobs, mappen en containers vanuit Azure Blob Storage. Momenteel wordt alleen blok-BLOB ondersteund. Wanneer bestands systemen, containers of mappen worden gedeeld in delen op basis van moment opnamen, kan de gegevens consument ervoor kiezen om een volledige kopie van de share gegevens te maken of om alleen nieuwe of bijgewerkte bestanden te kopiëren. Incrementele moment opname is gebaseerd op de tijd van de laatste wijziging van de bestanden. Bestaande bestanden met dezelfde naam worden overschreven.

Raadpleeg [en ontvang gegevens van Azure Blob Storage en Azure data Lake Storage](how-to-share-from-storage.md) voor meer informatie.

## <a name="share-from-a-sql-based-source"></a>Delen vanuit een bron op basis van SQL
De Azure-gegevens share ondersteunt het delen van tabellen en weer gaven van Azure SQL Database en Azure Synapse Analytics (voorheen Azure SQL DW) en het delen van tabellen vanuit een Azure Synapse Analytics (werk ruimte) toegewezen SQL-groep. Delen vanuit een Azure Synapse Analytics (werk ruimte) SQL-groep zonder server wordt momenteel niet ondersteund. Gegevens gebruikers kunnen ervoor kiezen om de gegevens in Azure Data Lake Storage Gen2 of Azure Blob Storage te accepteren als CSV-of Parquet-bestand, evenals in Azure SQL Database en Azure Synapse Analytics als tabellen.

Wanneer gegevens worden geaccepteerd in Azure Data Lake Store Gen2 of Azure Blob Storage, overschrijven volledige moment opnamen de inhoud van het doel bestand als dit al bestaat.
Wanneer gegevens in de tabel worden ontvangen en als de doel tabel nog niet bestaat, maakt Azure-gegevens share de SQL-tabel met het bron schema. Als er al een doel tabel met dezelfde naam bestaat, wordt deze verwijderd en overschreven met de laatste volledige moment opname. Incrementele moment opnamen worden momenteel niet ondersteund.

Raadpleeg [en ontvang gegevens van Azure SQL database en Azure Synapse Analytics](how-to-share-from-sql.md) voor meer informatie.

## <a name="share-from-azure-data-explorer"></a>Delen vanuit Azure Data Explorer
Azure data share ondersteunt de mogelijkheid om data bases in-place te delen vanuit Azure Data Explorer-clusters. Gegevens provider kan delen op Data Base of cluster niveau. Wanneer gegevens worden gedeeld op database niveau, hebben gebruikers alleen toegang tot de specifieke data base (s) die worden gedeeld door de gegevens provider. Wanneer gegevens worden gedeeld op cluster niveau, heeft de gebruiker toegang tot alle data bases van het cluster van de provider, met inbegrip van toekomstige data bases die zijn gemaakt door de gegevens provider.

Voor toegang tot gedeelde data bases moet de gegevens consument zijn eigen Azure Data Explorer-cluster hebben. Azure Data Explorer-cluster van de gegevens verbruiker moet in hetzelfde Azure-Data Center vinden als het Azure Data Explorer-cluster van de gegevens provider. Wanneer het delen van een relatie tot stand is gebracht, maakt Azure data share een symbolische koppeling tussen de Azure Data Explorer-clusters van de provider en de consumer. Gegevens die worden opgenomen in de batch-modus in de bron-Azure-Data Explorer cluster worden binnen een paar seconden tot een paar minuten weer gegeven op het doel cluster.

Raadpleeg uw [gegevens delen en ontvangen van Azure Data Explorer](/azure/data-explorer/data-share) voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen

Ga door naar de zelf studie [uw gegevens delen](share-your-data.md) voor meer informatie over het delen van gegevens.
