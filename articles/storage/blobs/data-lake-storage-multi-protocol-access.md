---
title: Toegang tot meerdere protocollen op Azure Data Lake Storage | Microsoft Docs
description: Gebruik BLOB Api's en toepassingen die gebruikmaken van BLOB-Api's met Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e3997fc215637175165402a926bffc6ac8d02771
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "77914855"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Toegang met meerdere protocollen in Azure Data Lake Storage

BLOB-Api's werken nu met accounts die een hiërarchische naam ruimte hebben. Hiermee ontgrendelt u het ecosysteem van hulpprogram ma's, toepassingen en services, evenals diverse functies voor Blob-opslag voor accounts met een hiërarchische naam ruimte.

Tot die tijd moest u mogelijk afzonderlijke opslag oplossingen voor object opslag en Analytics-opslag hebben. Dat komt omdat Azure Data Lake Storage Gen2 beperkte ecosystem-ondersteuning had. Het heeft ook beperkte toegang tot Blob service functies, zoals diagnostische logboek registratie. Een gefragmenteerde opslag oplossing is moeilijk te onderhouden omdat u gegevens tussen accounts moet verplaatsen om verschillende scenario's uit te voeren. Dit hoeft u niet meer te doen.

Met toegang tot meerdere protocollen op Data Lake Storage, kunt u met uw gegevens werken door gebruik te maken van het ecosysteem van hulpprogram ma's, toepassingen en services. Dit geldt ook voor hulpprogram ma's en toepassingen van derden. U kunt ze naar accounts met een hiërarchische naam ruimte wijzen, zonder dat u ze hoeft te wijzigen. Deze *toepassingen werken ook als ze* BLOB-api's aanroepen, omdat BLOB-api's nu kunnen worden uitgevoerd op gegevens in accounts met een hiërarchische naam ruimte.

Blob Storage-functies, zoals [Diagnostische logboek registratie](../common/storage-analytics-logging.md), [toegangs lagen](storage-blob-storage-tiers.md)en [beheer van levenscyclus opslag](storage-lifecycle-management-concepts.md) , werken nu met accounts die een hiërarchische naam ruimte hebben. Daarom kunt u hiërarchische naam ruimten inschakelen op uw Blob Storage-accounts zonder de toegang tot deze belang rijke functies te verliezen. 

> [!NOTE]
> Toegang via meerdere protocollen op Data Lake Storage is algemeen beschikbaar en is beschikbaar in alle regio's. Sommige Azure-Services of Blob Storage-functies die worden ingeschakeld door multi-protocol toegang, blijven in preview.  In deze artikelen vindt u een overzicht van de huidige ondersteuning voor Blob Storage-functies en Azure-service-integraties. 
>
> [Blob Storage functies die beschikbaar zijn in Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
>
>[Azure-Services die ondersteuning bieden voor Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Hoe toegang tot meerdere protocollen op Data Lake Storage werkt

BLOB-Api's en Data Lake Storage Gen2-Api's kunnen worden gebruikt op dezelfde gegevens in opslag accounts met een hiërarchische naam ruimte. Data Lake Storage Gen2 worden BLOB-Api's via de hiërarchische naam ruimte gerouteerd, zodat u de voor delen van de eerste klassen Directory-bewerkingen en POSIX-compatibele Acl's (Access Control List) kunt krijgen. 

![Toegang voor meerdere protocollen op Data Lake Storage concept](./media/data-lake-storage-interop/interop-concept.png) 

Bestaande hulpprogram ma's en toepassingen die gebruikmaken van de BLOB API, krijgen deze voor delen automatisch. Ontwikkel aars hoeven ze niet te wijzigen. Data Lake Storage Gen2 consistent Toep assen op Acl's op Directory-en bestands niveau, ongeacht het protocol dat hulpprogram ma's en toepassingen gebruiken om toegang te krijgen tot de gegevens. 

## <a name="see-also"></a>Zie ook

- [Blob Storage functies die beschikbaar zijn in Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Azure-Services die ondersteuning bieden voor Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Open-source platforms die ondersteuning bieden voor Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Bekende problemen met Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)




