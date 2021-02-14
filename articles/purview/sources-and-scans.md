---
title: Ondersteunde gegevensbronnen en bestandstypen
description: Dit artikel bevat conceptuele informatie over ondersteunde gegevens bronnen en bestands typen in controle sfeer liggen.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: references_regions
ms.openlocfilehash: 9a73f9b734d5404d07e05dd37d5ad8571c1aab2e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383888"
---
# <a name="supported-data-sources-and-file-types-in-azure-purview"></a>Ondersteunde gegevens bronnen en bestands typen in azure controle sfeer liggen

In dit artikel worden de ondersteunde gegevens bronnen, bestands typen en het scannen van concepten in controle sfeer liggen beschreven.

## <a name="supported-data-sources"></a>Ondersteunde gegevensbronnen

Azure controle sfeer liggen ondersteunt de volgende bronnen:

| Opslag type | Ondersteund verificatie type | Scans instellen via UX/Power shell |
| ---------- | ------------------- | ------------------------------ |
| On-premises SQL Server                   | SQL-verificatie                        | UX                                |
| Azure Synapse Analytics (voorheen SQL DW)            | SQL-verificatie, Service-Principal, MSI               | UX                             |
| Azure SQL Database (DB)                  | SQL-verificatie, Service-Principal, MSI               | UX |
| Beheerd exemplaar van Azure SQL Database      | SQL-verificatie, Service-Principal, MSI               | UX    |
| Azure Blob Storage                       | Account sleutel, Service-Principal, MSI | UX            |
| Azure Data Explorer                      | Service-principal                              | UX            |
| Azure Data Lake Storage Gen1 (ADLS Gen1) | Service-Principal, MSI                              | UX            |
| Azure Data Lake Storage Gen2 (ADLS Gen2) | Account sleutel, Service-Principal, MSI            | UX            |
| Azure Cosmos DB                          | Account sleutel                                    | UX            |


> [!Note]
> Azure Data Lake Storage Gen2 is nu overal algemeen beschikbaar. We raden aan vandaag nog hierop over te stappen. Zie de [productpagina](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/) voor meer informatie.

## <a name="file-types-supported-for-scanning"></a>Ondersteunde bestands typen voor scannen

De volgende bestands typen worden ondersteund voor het scannen, voor schema-extractie en-classificatie waar van toepassing:

- Gestructureerde bestands indelingen die worden ondersteund door uitbrei ding: AVRO, ORC, PARQUET, CSV, JSON, PSV, SSV, TSV, TXT, XML
- Bestands indelingen van documenten die worden ondersteund door extensie: DOC, DOCM, DOCX, DOT, ODP, ODS, ODT, PDF, POT, PPS, PPSX, PPT, PPTM, PPTX, XLC, XLS, XLSB, XLSM, XLSX, XLT
- Controle sfeer liggen biedt ook ondersteuning voor aangepaste bestands extensies en aangepaste parsers.

## <a name="sampling-within-a-file"></a>Bemonstering in een bestand

In controle sfeer liggen terminologie,
- L1-scan: haalt basis informatie en meta gegevens op zoals bestands naam, grootte en volledig gekwalificeerde naam
- L2-scan: Hiermee wordt het schema uitgepakt voor gestructureerde bestands typen en database tabellen
- L3-scan: haalt het schema op waar dit van toepassing is en heeft betrekking op het voorbeeld bestand op systeem-en aangepaste classificatie regels

Voor alle gestructureerde bestands indelingen controle sfeer liggen scanner-voorbeeld bestanden op de volgende manier:

- Voor gestructureerde bestands typen worden in elke kolom of 1 MB, afhankelijk van wat kleiner is, de rijen 128.
- Voor de bestands indelingen van documenten wordt voor elk bestand 20 MB van alle bestanden voor genomen.
    - Als een document bestand groter is dan 20 MB, is het niet onderhevig aan een grondige scan (onderhevig aan classificatie). In dat geval legt controle sfeer liggen alleen basis-meta gegevens vast zoals de bestands naam en een volledig gekwalificeerde naam.

## <a name="resource-set-file-sampling"></a>Voorbeeld bestand van resource set

Een map of groep partitie bestanden wordt gedetecteerd als een resource die is *ingesteld* in controle sfeer liggen, als deze overeenkomt met een systeem bronset of een door de klant gedefinieerd beleid voor het instellen van een resource. Als er een resourceset wordt gedetecteerd, wordt in controle sfeer liggen een voor beeld van elke map die de set bevat. Meer informatie over resource sets [vindt u hier](concept-resource-sets.md).

Bestands sampling voor resource sets op bestands typen:

- **Bestanden met scheidings tekens (CSV, PSV, SSV, tsv)** -1 in 100 bestanden worden bemonsterd (L3-scan) in een map of groep partitie bestanden die worden beschouwd als een ' resource set '
- **Data Lake bestands typen (Parquet, AVRO, Orc)** -1 in 18446744073709551615 (lange maximum) bestanden worden bemonsterd (L3-scan) in een map of groep partitie bestanden die worden beschouwd als een *resourceset*
- **Andere gestructureerde bestands typen (JSON, XML, txt)** -1 in 100 bestanden worden bemonsterd (L3-scan) in een map of groep partitie bestanden die worden beschouwd als een ' resource set '
- **SQL-objecten en CosmosDB-entiteiten** : elk bestand is N3-scans.
- **Document bestands typen** : elk bestand wordt gecontroleerd op N3. Patronen voor het instellen van een resource zijn niet van toepassing op deze bestands typen.

## <a name="scan-regions"></a>Scan regio's
Hier volgt een lijst met alle Azure-gegevens bron regio's (Data Centers) waarop de controle sfeer liggen-scanner wordt uitgevoerd. Als uw Azure-gegevens bron deel uitmaakt van een regio buiten deze lijst, wordt de scanner uitgevoerd in de regio van uw controle sfeer liggen-exemplaar.
 
### <a name="purview-scanner-regions"></a>Controle sfeer liggen-scanner regio's

- EastUs
- EastUs2 
- SouthCentralUS
- WestUs
- WestUs2
- SoutheastAsia
- West-Europa
- NorthEurope
- UkSouth
- AustraliaEast
- CanadaCentral
- BrazilSouth
- CentralIndia
- JapanEast
- SouthAfricaNorth
- FranceCentral

## <a name="classification"></a>Classificatie

Alle 105 systeem classificatie regels zijn van toepassing op gestructureerde bestands indelingen. Alleen de MCE-classificatie regels zijn van toepassing op document bestands typen (niet op de systeem eigen regex-patronen van gegevens scan, op basis van op filter gebaseerde detectie). Zie [ondersteunde classificaties in azure controle sfeer liggen](supported-classifications.md)voor meer informatie over ondersteunde classificaties.

## <a name="next-steps"></a>Volgende stappen

- [Zelf studie: het Start pakket uitvoeren en gegevens scannen](tutorial-scan-data.md)
- [Gegevens bronnen beheren in azure controle sfeer liggen (preview)](manage-data-sources.md)