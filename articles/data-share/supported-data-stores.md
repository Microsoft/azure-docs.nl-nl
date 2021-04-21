---
title: Ondersteunde gegevensarchieven in Azure Data Share
description: Meer informatie over de gegevensopslag die worden ondersteund voor gebruik in Azure Data Share.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: def73d137f3cc2c79ae8417995ec6bdf6c519b7d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812620"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Ondersteunde gegevensarchieven in Azure Data Share

Azure Data Share biedt open en flexibel delen van gegevens, waaronder de mogelijkheid om gegevens te delen van en naar verschillende gegevensopslag. Gegevensproviders kunnen gegevens uit één type gegevensopslag delen en gegevensverbruikers kunnen een gegevensopslag kiezen om de gegevens te ontvangen. 

In dit artikel vindt u informatie over de uitgebreide set Azure-gegevensopslag die Azure Data Share ondersteunt. U leert ook hoe gegevensproviders en gegevensverbruikers verschillende gegevensopslag kunnen combineren. 

## <a name="supported-data-stores"></a>Ondersteunde gegevensarchieven 

In de volgende tabel worden de gegevensopslagen uitgelegd die Azure Data Share ondersteunen. 

| Gegevensarchief | Delen op basis van volledige momentopnamen | Delen op basis van incrementele momentopnamen | Delen is mogelijk 
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ |✓ | |
| Azure Data Lake Storage Gen1 |✓ |✓ | |
| Azure Data Lake Storage Gen2 |✓ |✓ ||
| Azure SQL Database |✓ | | |
| Azure Synapse Analytics (voorheen Azure SQL Data Warehouse) |✓ | | |
| Azure Synapse Analytics toegewezen SQL-pool (werkruimte) |✓ | | |
| Azure Data Explorer | | |✓ |

## <a name="data-store-support-matrix"></a>Ondersteuningsmatrix voor gegevensopslag

Azure Data Share kunnen gegevensverbruikers een gegevensopslag kiezen om gegevens te accepteren. Gegevens die worden gedeeld vanuit een Azure SQL Database kunnen bijvoorbeeld worden ontvangen in Azure Data Lake Storage Gen2, Azure SQL Database of Azure Synapse Analytics. Wanneer klanten een ontvangende gegevens share instellen, kunnen ze de indeling kiezen om de gegevens te ontvangen. 

In de volgende tabel worden de combinaties en opties uitgelegd die gegevensverbruikers kunnen kiezen wanneer ze een gegevens share accepteren en configureren. Zie Configure [a dataset mapping (Een gegevenssettoewijzing configureren) voor meer informatie.](how-to-configure-mapping.md)

| Gegevensarchief | Blob Storage | Data Lake Storage Gen1 | Data Lake Storage Gen2 | SQL Database | Synapse Analytics (voorheen SQL Data Warehouse) | Synapse Analytics toegewezen SQL-pool (werkruimte) | Data Explorer
|:--- |:--- |:--- |:--- |:--- |:--- |:--- | :--- |
| Blob Storage | ✓ || ✓ |||
| Data Lake Storage Gen1 | ✓ | | ✓ |||
| Data Lake Storage Gen2 | ✓ | | ✓ |||
| SQL Database | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics (voorheen SQL Data Warehouse) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics toegewezen SQL-pool (werkruimte) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Data Explorer ||||||| ✓ |

## <a name="share-from-a-storage-account"></a>Delen vanuit een opslagaccount
Azure Data Share ondersteunt het delen van bestanden, mappen en bestandssystemen vanuit Azure Data Lake Storage Gen1 en Azure Data Lake Storage Gen2. Het biedt ook ondersteuning voor het delen van blobs, mappen en containers van Azure Blob Storage. U kunt blok-, toevoegen- of pagina-blobs delen en deze worden ontvangen als blok-blobs.

Wanneer bestandssystemen, containers of mappen worden gedeeld in delen op basis van momentopnamen, kunnen gegevensverbruikers ervoor kiezen om een volledige kopie van de gedeelde gegevens te maken. Ze kunnen ook de mogelijkheid voor incrementele momentopnamen gebruiken om alleen nieuwe bestanden of bijgewerkte bestanden te kopiëren. 

Een incrementele momentopname is gebaseerd op het tijdstip waarop de bestanden het laatst zijn gewijzigd. Bestaande bestanden met dezelfde naam als bestanden in de ontvangen gegevens worden overschreven in een momentopname. Bestanden die uit de bron worden verwijderd, worden niet verwijderd op het doel. 

Zie Gegevens delen en ontvangen van Azure Blob Storage en [Azure Data Lake Storage voor meer Azure Data Lake Storage.](how-to-share-from-storage.md)

## <a name="share-from-a-sql-based-source"></a>Delen vanuit een bron op basis van SQL
Azure Data Share ondersteunt het delen van tabellen en weergaven van Azure SQL Database en Azure Synapse Analytics (voorheen Azure SQL Data Warehouse). Het ondersteunt het delen van tabellen uit Azure Synapse Analytics toegewezen SQL-pool (werkruimte). Delen vanuit Azure Synapse Analytics serverloze SQL-pool (werkruimte) wordt momenteel niet ondersteund. 

Gegevensverbruikers kunnen ervoor kiezen om de gegevens te accepteren in Azure Data Lake Storage Gen2 of Azure Blob Storage CSV-bestand of Parquet-bestand. Ze kunnen ook gegevens als tabellen accepteren in Azure SQL Database en Azure Synapse Analytics.

Wanneer consumenten gegevens accepteren in Azure Data Lake Storage Gen2 of Azure Blob Storage, overschrijven volledige momentopnamen de inhoud van het doelbestand als het bestand al bestaat. Wanneer gegevens worden ontvangen in een tabel en de doeltabel nog niet bestaat, maakt Azure Data Share een SQL-tabel met behulp van het bronschema. Als er al een doeltabel bestaat en deze dezelfde naam heeft, wordt deze weggeschreven en overschreven met de meest recente volledige momentopname. Incrementele momentopnamen worden momenteel niet ondersteund.

Zie Gegevens delen en ontvangen van Azure SQL Database en Azure Synapse Analytics voor [meer Azure Synapse Analytics.](how-to-share-from-sql.md)

## <a name="share-from-data-explorer"></a>Delen vanuit Data Explorer
Azure Data Share biedt ondersteuning voor de mogelijkheid om databases in-place te delen vanuit Azure Data Explorer clusters. Een gegevensprovider kan delen op het niveau van de database of het cluster. 

Wanneer gegevens op databaseniveau worden gedeeld, hebben gegevensverbruikers alleen toegang tot de databases die de gegevensprovider heeft gedeeld. Wanneer een provider gegevens deelt op clusterniveau, hebben gegevensverbruikers toegang tot alle databases vanuit het cluster van de provider, met inbegrip van toekomstige databases die de gegevensprovider maakt.

Voor toegang tot gedeelde databases hebben gegevensverbruikers hun eigen cluster Azure Data Explorer nodig. Het cluster moet zich in hetzelfde Azure-datacenter bevinden als het Azure Data Explorer van de gegevensprovider. 

Wanneer er een deelrelatie tot stand is gebracht, Azure Data Share een symbolische koppeling tussen het cluster van de provider en het cluster van de consument. Gegevens die in het broncluster worden opgenomen met behulp van de batchmodus, worden binnen enkele minuten op het doelcluster weergegeven.

Zie Share [and receive data from Azure Data Explorer (Gegevens delen en ontvangen van Azure Data Explorer) voor meer Azure Data Explorer.](/azure/data-explorer/data-share) 

## <a name="next-steps"></a>Volgende stappen

Als u wilt weten hoe u gegevens kunt delen, gaat u verder met de [zelfstudie Uw gegevens](share-your-data.md) delen.
