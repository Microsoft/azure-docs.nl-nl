---
title: Meta gegevens en Acl's behouden met Kopieer activiteit in Azure Data Factory
description: Meer informatie over het bewaren van meta gegevens en Acl's tijdens het kopiëren met de Kopieer activiteit in Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: jingwang
ms.openlocfilehash: 396a598d143e85687f9dfbf765b3c18736627e41
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387713"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>Meta gegevens en Acl's behouden met Kopieer activiteit in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Wanneer u Azure Data Factory Kopieer activiteit gebruikt om gegevens te kopiëren van bron naar sink, kunt u in de volgende scenario's ook de meta gegevens en Acl's behouden.

## <a name="preserve-metadata-for-lake-migration"></a><a name="preserve-metadata"></a> Meta gegevens voor Lake Migration behouden

Wanneer u gegevens migreert van de ene data Lake naar een andere, met inbegrip van [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md)en [Azure File Storage](connector-azure-file-storage.md), kunt u ervoor kiezen om de meta gegevens van het bestand te behouden samen met de gegevens.

De Kopieer activiteit biedt ondersteuning voor het behouden van de volgende kenmerken tijdens het kopiëren van de gegevens:

- **Alle door de klant opgegeven meta gegevens** 
- En de volgende **vijf ingebouwde systeem eigenschappen van het gegevens archief**: `contentType` , `contentLanguage` (behalve voor Amazon S3), `contentEncoding` , `contentDisposition` ,, `cacheControl` .

**Verschillen in meta gegevens afhandelen:** Amazon S3 en Azure Storage staan verschillende sets tekens toe aan de sleutels van de door de klant opgegeven meta gegevens. Wanneer u ervoor kiest om meta gegevens te bewaren met behulp van Kopieer activiteit, vervangt ADF automatisch de ongeldige tekens door ' _ '.

Wanneer u bestanden kopieert als-afkomstig van Amazon S3/Azure data Lake Storage Gen2/Azure Blob/Azure File Storage naar Azure data Lake Storage Gen2/Azure Blob/Azure File Storage met binaire indeling, kunt u de optie **behouden** vinden op het tabblad instellingen van de **Kopieer activiteit**  >   voor het ontwerpen van activiteiten of de pagina **instellingen** in gegevens kopiëren hulp programma.

![De meta gegevens van de Kopieer activiteit behouden](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

Hier volgt een voor beeld van de JSON-configuratie van de Kopieer activiteit (Zie `preserve` ): 

```json
"activities":[
    {
        "name": "CopyAndPreserveMetadata",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AmazonS3ReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "Attributes"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset Amazon S3/Azure Blob/ADLS Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset for Azure Blob/ADLS Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="preserve-acls-from-data-lake-storage-gen1gen2-to-gen2"></a><a name="preserve-acls"></a> Acl's van Data Lake Storage Gen1/Gen2 naar Gen2 behouden

Wanneer u een upgrade uitvoert van Azure Data Lake Storage Gen1 naar Gen2 of gegevens kopieert tussen ADLS Gen2, kunt u ervoor kiezen om de POSIX Access Control Lists (Acl's) samen met gegevens bestanden te bewaren. Zie voor meer informatie over toegangs beheer [toegangs beheer in azure data Lake Storage gen1](../data-lake-store/data-lake-store-access-control.md) en [toegangs beheer in azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

De Kopieer activiteit ondersteunt het behouden van de volgende typen Acl's tijdens het kopiëren van de gegevens. U kunt een of meer typen selecteren:

- **ACL**: de POSIX-toegangs beheer lijsten voor bestanden en mappen kopiëren en bewaren. Hiermee worden de volledige bestaande Acl's van de bron naar de Sink gekopieerd. 
- **Eigenaar**: de gebruiker die eigenaar is van bestanden en mappen kopiëren en bewaren. Super gebruikers hebben toegang tot Sink Data Lake Storage Gen2 vereist.
- **Groep**: Kopieer en bewaar de groep die eigenaar is van bestanden en mappen. Super gebruikers hebben toegang tot Sink Data Lake Storage Gen2 of de gebruiker die eigenaar is (als de gebruiker die eigenaar is ook lid is van de doel groep) is vereist.

Als u opgeeft dat u wilt kopiëren vanuit een map, Data Factory repliceert de Acl's voor die bepaalde map en de bestanden en mappen daaronder als deze `recursive` is ingesteld op True. Als u opgeeft dat u vanuit één bestand wilt kopiëren, worden de Acl's voor dat bestand gekopieerd.

>[!NOTE]
>Wanneer u ADF gebruikt om Acl's van Data Lake Storage Gen1/Gen2 naar Gen2 te bewaren, worden de bestaande Acl's op Sink-Gen2's bijbehorende map/bestanden overschreven.

>[!IMPORTANT]
>Wanneer u ervoor kiest om Acl's te behouden, moet u ervoor zorgen dat u Maxi maal voldoende machtigingen voor Data Factory verleent voor het uitvoeren van uw Sink Data Lake Storage Gen2-account. Gebruik bijvoorbeeld account sleutel verificatie of wijs de rol Storage BLOB data owner toe aan de service-principal of beheerde identiteit.

Wanneer u bron configureert als data Lake Storage gen1/Gen2 met binaire indeling of de optie voor binaire kopieën, en u wilt opvangen als data Lake Storage Gen2 met binaire indeling of de binaire kopie optie, kunt u de optie **behouden** vinden op de pagina **instellingen** van gegevens kopiëren hulp programma of op het tabblad instellingen van de **Kopieer activiteit**  >   voor het ontwerpen van een activiteit.

![Data Lake Storage Gen1-Gen2 tot Gen2-ACL behouden](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Hier volgt een voor beeld van de JSON-configuratie van de Kopieer activiteit (Zie `preserve` ): 

```json
"activities":[
    {
        "name": "CopyAndPreserveACLs",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1/Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>Volgende stappen

Zie de andere artikelen over Kopieer activiteiten:

- [Overzicht van de Kopieer activiteit](copy-activity-overview.md)
- [Prestaties van kopieeractiviteit](copy-activity-performance.md)
