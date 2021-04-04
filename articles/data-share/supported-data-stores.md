---
title: Ondersteunde gegevensarchieven in Azure Data Share
description: Meer informatie over de gegevens archieven die worden ondersteund voor gebruik in azure data share.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 12/16/2020
ms.openlocfilehash: 852c44f5edc5c0b0f5f655f63ab040927bd9bc7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97963676"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Ondersteunde gegevensarchieven in Azure Data Share

Azure data share biedt open en flexibel delen van gegevens, met inbegrip van de mogelijkheid om te delen vanuit en naar verschillende gegevens archieven. Gegevens providers kunnen gegevens delen van het ene type gegevens archief en gegevens gebruikers kunnen een gegevens archief kiezen voor het ontvangen van de gegevens. 

In dit artikel vindt u meer informatie over de uitgebreide set met Azure-gegevens archieven die door Azure data share worden ondersteund. U leert ook hoe gegevens providers en gegevens gebruikers verschillende gegevens archieven kunnen combi neren. 

## <a name="supported-data-stores"></a>Ondersteunde gegevensarchieven 

In de volgende tabel worden de gegevens archieven beschreven die door Azure data share worden ondersteund. 

| Gegevensarchief | Delen op basis van volledige moment opnamen | Delen op basis van incrementele moment opnamen | Delen op locatie 
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Storage |✓ |✓ | |
| Azure Data Lake Storage Gen1 |✓ |✓ | |
| Azure Data Lake Storage Gen2 |✓ |✓ ||
| Azure SQL Database |✓ | | |
| Azure Synapse Analytics (voorheen Azure SQL Data Warehouse) |✓ | | |
| Exclusieve SQL-groep voor Azure Synapse Analytics (werk ruimte) |✓ | | |
| Azure Data Explorer | | |✓ |

## <a name="data-store-support-matrix"></a>Ondersteunings matrix voor gegevens opslag

Met Azure data share kunnen gegevens gebruikers een gegevens archief kiezen om gegevens te accepteren. Gegevens die vanuit Azure SQL Database worden gedeeld, kunnen bijvoorbeeld worden ontvangen in Azure Data Lake Storage Gen2, Azure SQL Database of Azure Synapse Analytics. Wanneer klanten een ontvangen gegevens delen instellen, kunnen ze de indeling kiezen voor het ontvangen van de gegevens. 

In de volgende tabel ziet u de combi Naties en opties die gebruikers kunnen kiezen wanneer ze een gegevens share accepteren en configureren. Zie [Configure a dataset mapping](how-to-configure-mapping.md)(Engelstalig) voor meer informatie.

| Gegevensarchief | Blob Storage | Data Lake Storage Gen1 | Data Lake Storage Gen2 | SQL Database | Synapse Analytics (voorheen SQL Data Warehouse) | Toegewezen SQL-groep voor Synapse Analytics (werk ruimte) | Data Explorer
|:--- |:--- |:--- |:--- |:--- |:--- |:--- | :--- |
| Blob Storage | ✓ || ✓ |||
| Data Lake Storage Gen1 | ✓ | | ✓ |||
| Data Lake Storage Gen2 | ✓ | | ✓ |||
| SQL Database | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Synapse Analytics (voorheen SQL Data Warehouse) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Toegewezen SQL-groep voor Synapse Analytics (werk ruimte) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Data Explorer ||||||| ✓ |

## <a name="share-from-a-storage-account"></a>Delen vanuit een opslagaccount
De Azure-gegevens share ondersteunt het delen van bestanden, mappen en bestands systemen van Azure Data Lake Storage Gen1 en Azure Data Lake Storage Gen2. Het biedt ook ondersteuning voor het delen van blobs, mappen en containers vanuit Azure Blob Storage. Alleen blok-blobs worden momenteel ondersteund. 

Wanneer bestands systemen, containers of mappen worden gedeeld in delen op basis van moment opnamen, kunnen gegevens gebruikers ervoor kiezen om een volledige kopie van de gedeelde gegevens te maken. Of ze kunnen de mogelijkheid tot incrementele moment opname gebruiken om alleen nieuwe bestanden of bijgewerkte bestanden te kopiëren. 

Een incrementele moment opname is gebaseerd op de tijd van de laatste wijziging van de bestanden. Bestaande bestanden die dezelfde naam hebben als de bestanden in de ontvangen gegevens, worden overschreven in een moment opname. Bestanden die worden verwijderd uit de bron, worden niet verwijderd op het doel. 

Zie [gegevens delen en ontvangen van Azure Blob Storage en Azure data Lake Storage](how-to-share-from-storage.md)voor meer informatie.

## <a name="share-from-a-sql-based-source"></a>Delen vanuit een bron op basis van SQL
De Azure-gegevens share biedt ondersteuning voor het delen van tabellen en weer gaven van Azure SQL Database en Azure Synapse Analytics (voorheen Azure SQL Data Warehouse). Het biedt ondersteuning voor het delen van tabellen vanuit een toegewezen SQL-groep in azure Synapse Analytics (workspace). Delen vanuit een Azure Synapse Analytics (werk ruimte) SQL-groep zonder server wordt momenteel niet ondersteund. 

Gegevens gebruikers kunnen ervoor kiezen om de gegevens in Azure Data Lake Storage Gen2 of Azure Blob Storage te accepteren als een CSV-bestand of een Parquet-bestand. Ze kunnen ook gegevens als tabellen accepteren in Azure SQL Database en Azure Synapse Analytics.

Wanneer gebruikers gegevens accepteren in Azure Data Lake Storage Gen2 of Azure Blob Storage, overschrijven volledige moment opnamen de inhoud van het doel bestand als het bestand al bestaat. Wanneer gegevens worden ontvangen in een tabel en de doel tabel nog niet bestaat, maakt Azure data share een SQL-tabel met behulp van het bron schema. Als er al een doel tabel bestaat en deze dezelfde naam heeft, wordt deze verwijderd en overschreven met de laatste volledige moment opname. Incrementele moment opnamen worden momenteel niet ondersteund.

Zie [gegevens delen en ontvangen van Azure SQL database en Azure Synapse Analytics](how-to-share-from-sql.md)voor meer informatie.

## <a name="share-from-data-explorer"></a>Delen vanuit Data Explorer
Azure data share ondersteunt de mogelijkheid om data bases in-place te delen vanuit Azure Data Explorer-clusters. Een gegevens provider kan delen op het niveau van de data base of het cluster. 

Wanneer gegevens worden gedeeld op database niveau, hebben gegevens gebruikers alleen toegang tot de data bases die door de gegevens provider worden gedeeld. Wanneer een provider gegevens deelt op het cluster niveau, hebben gegevens gebruikers toegang tot alle data bases van het cluster van de provider, met inbegrip van toekomstige data bases die de gegevens provider maakt.

Voor toegang tot gedeelde data bases hebben gegevens gebruikers hun eigen Azure Data Explorer-cluster nodig. Het cluster moet zich in hetzelfde Azure-Data Center betreden als de Azure Data Explorer-cluster van de gegevens provider. 

Wanneer een delende relatie tot stand is gebracht, maakt Azure data share een symbolische koppeling tussen het cluster van de provider en het cluster van de gebruiker. Gegevens die worden opgenomen in het bron cluster met behulp van de batch modus, worden binnen een paar minuten weer gegeven op het doel cluster.

Zie [gegevens delen en ontvangen van Azure Data Explorer](/azure/data-explorer/data-share)voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen

Ga door naar de zelf studie [uw gegevens delen](share-your-data.md) voor meer informatie over het delen van gegevens.
