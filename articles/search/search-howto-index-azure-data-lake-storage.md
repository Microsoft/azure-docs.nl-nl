---
title: Zoeken in Azure Data Lake Storage Gen2 (preview-versie)
titleSuffix: Azure Cognitive Search
description: Meer informatie over het indexeren van inhoud en meta gegevens in Azure Data Lake Storage Gen2. Deze functie is momenteel beschikbaar als open bare preview
manager: nitinme
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: 51c32501015a16819ba9316272ed0d522e1083d7
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545561"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Documenten indexeren in Azure Data Lake Storage Gen2

> [!IMPORTANT] 
> Azure Data Lake Storage Gen2-ondersteuning is momenteel beschikbaar als open bare preview. Deze previewfunctie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. U kunt toegang tot de voor beelden aanvragen door [dit formulier](https://aka.ms/azure-cognitive-search/indexer-preview)in te vullen. De [rest API versie 2020-06-30-preview](search-api-preview.md) en Portal bieden deze functie. Er is momenteel geen .NET SDK-ondersteuning.


Wanneer u een Azure Storage-account instelt, hebt u de mogelijkheid om een [hiërarchische naam ruimte](../storage/blobs/data-lake-storage-namespace.md)in te scha kelen. Hierdoor kan het verzamelen van inhoud in een account worden georganiseerd in een hiërarchie van directory's en geneste submappen. Door hiërarchische naam ruimte in te scha kelen, schakelt u [Azure data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)in.

In dit artikel wordt beschreven hoe u aan de slag gaat met het indexeren van documenten in Azure Data Lake Storage Gen2.

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>Azure Data Lake Storage Gen2 Indexeer functie instellen

Er zijn een paar stappen die u moet volt ooien om inhoud te indexeren van Data Lake Storage Gen2.

### <a name="step-1-sign-up-for-the-preview"></a>Stap 1: Meld u aan voor de preview-versie

Meld u aan voor de Data Lake Storage Gen2 Preview voor indexering door [dit formulier](https://aka.ms/azure-cognitive-search/indexer-preview)in te vullen. U ontvangt een bevestigings-e-mail zodra u deze hebt geaccepteerd in de preview-versie.

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>Stap 2: de installatie stappen voor het indexeren van Azure Blob Storage volgen

Zodra u een bevestiging hebt ontvangen dat uw Preview-Registratie is geslaagd, bent u klaar om de Indexing-pijp lijn te maken.

U kunt inhoud en meta gegevens indexeren van Data Lake Storage Gen2 met behulp van de [rest API versie 2020-06-30-preview](search-api-preview.md) of de portal. Er is op dit moment geen .NET SDK-ondersteuning.

Het indexeren van inhoud in Data Lake Storage Gen2 is identiek aan het indexeren van inhoud in Azure Blob-opslag. Als u wilt weten hoe u de Data Lake Storage Gen2 gegevens bron, index en Indexeer functie kunt instellen, raadpleegt u [documenten in azure Blob Storage indexeren met azure Cognitive Search](search-howto-indexing-azure-blob-storage.md). Het artikel Blob Storage bevat ook informatie over welke document indelingen worden ondersteund, welke eigenschappen van BLOB-meta gegevens worden geëxtraheerd, incrementeel indexeren en nog veel meer. Deze informatie is hetzelfde voor Data Lake Storage Gen2.

## <a name="access-control"></a>Toegangsbeheer

Azure Data Lake Storage Gen2 implementeert een [model voor toegangs beheer](../storage/blobs/data-lake-storage-access-control.md) dat zowel op rollen gebaseerd toegangs beheer (Azure RBAC) als POSIX-achtige acl's (toegangs beheer lijsten) ondersteunt. Wanneer u inhoud indexeert vanaf Data Lake Storage Gen2, haalt Azure Cognitive Search de Azure RBAC-en ACL-gegevens niet uit de inhoud. Als gevolg hiervan worden deze gegevens niet opgenomen in uw Azure Cognitive Search-index.

Als het beheren van toegangs beheer voor elk document in de index belang rijk is, is het aan de ontwikkelaar van de toepassing om [beveiligings beperking](./search-security-trimming-for-azure-search.md)te implementeren.

## <a name="change-detection"></a>Wijzigings detectie

De Data Lake Storage Gen2 indexer ondersteunt de detectie van wijzigingen. Dit betekent dat wanneer de Indexeer functie alleen wordt uitgevoerd, de gewijzigde blobs opnieuw worden geïndexeerd zoals bepaald door de tijds tempel van de BLOB `LastModified` .

> [!NOTE] 
> Data Lake Storage Gen2 kunnen mappen worden hernoemd. Wanneer de naam van een map wordt gewijzigd, worden de tijds tempels voor de blobs in die map niet bijgewerkt. Als gevolg hiervan worden deze blobs niet opnieuw geïndexeerd door de Indexeer functie. Als u wilt dat de blobs in een map opnieuw worden geïndexeerd nadat de naam van een map is gewijzigd, moet u de `LastModified` tijds tempel voor alle blobs in de map bijwerken zodat de Indexeer functie deze opnieuw kan indexeren tijdens een toekomstige uitvoering.