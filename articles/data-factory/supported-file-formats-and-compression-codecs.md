---
title: Ondersteunde bestands indelingen op basis van de Kopieer activiteit in Azure Data Factory
description: In dit onderwerp worden de bestands indelingen en compressie codes beschreven die worden ondersteund door de Kopieer activiteit in Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jingwang
ms.openlocfilehash: bb773d01124b99b4837f393b610e00ecbfa510fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100364610"
---
# <a name="supported-file-formats-and-compression-codecs-by-copy-activity-in-azure-data-factory"></a>Ondersteunde bestands indelingen en compressie-codecs op basis van de Kopieer activiteit in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*Dit artikel is van toepassing op de volgende connectors: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Bestands systeem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)en [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

U kunt de [Kopieer activiteit](copy-activity-overview.md) gebruiken om bestanden te kopiëren tussen twee op bestanden gebaseerde gegevens archieven. in dat geval worden de gegevens efficiënt gekopieerd zonder serialisatie of deserialisatie. 

Daarnaast kunt u ook bestanden met een bepaalde indeling parseren of genereren. U kunt bijvoorbeeld het volgende doen:

* Gegevens uit een SQL Server-Data Base kopiëren en schrijven naar Azure Data Lake Storage Gen2 in de Parquet-indeling.
* Kopieer bestanden in de tekst indeling (CSV) van een on-premises bestands systeem en schrijf naar Azure Blob-opslag in de Avro-indeling.
* Kopieer gezipte bestanden van een on-premises bestands systeem, Decomprimeer ze op de vlucht en schrijf uitgepakte bestanden naar Azure Data Lake Storage Gen2.
* Kopieer gegevens in de indeling van de tekst in het type gzip (gecomprimeerd) in de vorm van Azure Blob-opslag en schrijf deze naar Azure SQL Database.
* Veel meer activiteiten waarvoor serialisatie/deserialisatie of compressie/decompressie nodig is.

## <a name="next-steps"></a>Volgende stappen

Zie de andere artikelen over Kopieer activiteiten:

- [Overzicht van de Kopieer activiteit](copy-activity-overview.md)
- [Prestaties van kopieeractiviteit](copy-activity-performance.md)
