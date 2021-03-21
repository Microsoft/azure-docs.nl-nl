---
title: Gewijzigde en verwijderde blobs
titleSuffix: Azure Cognitive Search
description: Na het maken van een eerste zoek index die uit Azure Blob-opslag wordt geïmporteerd, kunt u met de volgende Indexeer bewerking alleen de blobs ophalen die zijn gewijzigd of verwijderd. In dit artikel worden de details uitgelegd.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 79d5583f8c9e562a0d21a91c210aa6259472661d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100383531"
---
# <a name="change-and-deletion-detection-in-blob-indexing-azure-cognitive-search"></a>Detectie van wijzigingen en verwijdering in BLOB-indexering (Azure Cognitive Search)

Nadat u een eerste zoek index hebt gemaakt, wilt u mogelijk dat er nieuwe en gewijzigde documenten worden opgehaald met de Indexeer functie. Voor zoek inhoud die afkomstig is uit Azure Blob-opslag, wordt de detectie van wijzigingen automatisch uitgevoerd wanneer u een schema gebruikt voor het activeren van indexeren. De service indexeert standaard alleen de gewijzigde blobs, zoals wordt bepaald door de tijds tempel van de BLOB `LastModified` . In tegens telling tot andere gegevens bronnen die worden ondersteund door zoek indexen, hebben blobs altijd een tijds tempel, waardoor het niet meer nodig is om een wijzigings detectie beleid hand matig in te stellen.

Hoewel de wijzigings detectie een gegeven is, is de detectie van de verwijdering niet. Als u verwijderde documenten wilt detecteren, moet u ervoor zorgen dat u de methode ' zacht verwijderen ' gebruikt. Als u de blobs naar rechts verwijdert, worden de bijbehorende documenten niet verwijderd uit de zoek index.

Er zijn twee manieren om de methode van zacht verwijderen te implementeren:

+ Systeem eigen BLOB zacht verwijderen (preview), volgende beschreven
+ [Zacht verwijderen met aangepaste meta gegevens](#soft-delete-using-custom-metadata)

## <a name="native-blob-soft-delete-preview"></a>Voorlopig verwijderen van systeemeigen blob (preview)

Voor deze benadering van het verwijderen van de detectie is Cognitive Search afhankelijk van de functie voor het [voorlopig verwijderen van blobs](../storage/blobs/soft-delete-blob-overview.md) in Azure Blob-opslag om te bepalen of blobs zijn overgezet naar een voorlopig verwijderde status. Wanneer blobs in deze status worden gedetecteerd, gebruikt een zoek Indexeer functie deze informatie om het bijbehorende document uit de index te verwijderen.

> [!IMPORTANT]
> Ondersteuning voor het voorlopig verwijderen van een blob is in preview. Deze previewfunctie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. De [rest API versie 2020-06-30-preview](./search-api-preview.md) biedt deze functie. Er is momenteel geen portal-of .NET SDK-ondersteuning.

### <a name="prerequisites"></a>Vereisten

+ [Schakel voorlopig verwijderen in voor blobs](../storage/blobs/soft-delete-blob-enable.md).
+ Blobs moeten zich in een Azure Blob-opslag container bevinden. Het beleid voor voorlopig verwijderen van de Cognitive Search systeem eigen BLOB wordt niet ondersteund voor blobs van Azure Data Lake Storage Gen2.
+ Document sleutels voor de documenten in uw index moeten worden toegewezen aan een BLOB-eigenschap of BLOB-meta gegevens.
+ U moet de preview-REST API ( `api-version=2020-06-30-Preview` ) gebruiken om ondersteuning voor het voorlopig verwijderen te configureren.

### <a name="how-to-configure-deletion-detection-using-native-soft-delete"></a>Verwijderings detectie configureren met systeem eigen voors en verwijderen

1. In Blob Storage, bij het inschakelen van zacht verwijderen, stelt u het Bewaar beleid in op een waarde die veel hoger is dan het interval schema van uw indexer. Als er een probleem is met het uitvoeren van de Indexeer functie of als u een groot aantal documenten hebt om te indexeren, is er genoeg tijd voor de Indexeer functie om de zachte verwijderde blobs uiteindelijk te verwerken. Met Azure Cognitive Search Indexeer functies wordt alleen een document uit de index verwijderd als het de BLOB verwerkt terwijl deze de status zacht verwijderd heeft.

1. Stel in Cognitive Search een systeem eigen detectie beleid voor voorlopig verwijderen van blobs in voor de gegevens bron. Hieronder kunt u een voorbeeld bekijken. Omdat deze functie in preview is, moet u de preview-REST API gebruiken.

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

1. [Voer de Indexeer functie uit](/rest/api/searchservice/run-indexer) of stel de Indexeer functie [in op een schema](search-howto-schedule-indexers.md). Wanneer de Indexeer functie wordt uitgevoerd en een BLOB verwerkt die de status voorlopig verwijderen heeft, wordt het bijbehorende Zoek document verwijderd uit de index.

### <a name="reindexing-undeleted-blobs-using-native-soft-delete-policies"></a>Niet-verouderde blobs opnieuw indexeren (met systeem eigen beleid voor voorlopig verwijderen)

Als u een voorlopig verwijderde Blob in Blob Storage herstelt, wordt deze niet altijd opnieuw geïndexeerd met de Indexeer functie. Dit komt doordat de Indexeer functie de tijds tempel van de BLOB gebruikt `LastModified` om te bepalen of indexeren nodig is. Wanneer een zachte verwijderde BLOB wordt verwijderd, wordt de `LastModified` tijds tempel ervan niet bijgewerkt, dus als de Indexeer functie al verwerkte blobs met meer recente `LastModified` tijds tempels heeft verwerkt, wordt de niet-verwijderde BLOB niet opnieuw geïndexeerd. 

Om ervoor te zorgen dat een niet-verouderde BLOB opnieuw wordt geïndexeerd, moet u de tijds tempel van de BLOB bijwerken `LastModified` . Een manier om dit te doen, is door de meta gegevens van die BLOB opnieuw op te slaan. U hoeft de meta gegevens niet te wijzigen. Als u de meta gegevens opnieuw opslaat, wordt de tijds tempel van de BLOB bijgewerkt `LastModified` zodat de Indexeer functie deze kan ophalen.

## <a name="soft-delete-using-custom-metadata"></a>Zacht verwijderen met aangepaste meta gegevens

Deze methode maakt gebruik van de meta gegevens van een blob om te bepalen of een zoek document moet worden verwijderd uit de index. Deze methode vereist twee afzonderlijke acties, waarbij u het zoek document uit de index verwijdert, gevolgd door het verwijderen van blobs in Azure Storage.

Er zijn stappen die u moet volgen in zowel de Blob-opslag als de Cognitive Search, maar er zijn geen andere onderdeel afhankelijkheden. Deze mogelijkheid wordt ondersteund in algemeen beschik bare Api's.

1. Voeg een aangepaste sleutel-waardepaar voor meta gegevens toe aan de blob om aan te geven dat deze logisch is verwijderd door Azure Cognitive Search.

1. Configureer een beleid voor het detecteren van een tijdelijke verwijderings kolom voor de gegevens bron. Het volgende beleid beschouwt bijvoorbeeld een blob die moet worden verwijderd als deze een meta gegevens eigenschap `IsDeleted` met de waarde heeft `true` :

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
    ```

1. Zodra de Indexeer functie de BLOB heeft verwerkt en het document uit de index heeft verwijderd, kunt u de BLOB verwijderen in Azure Blob-opslag.

### <a name="reindexing-undeleted-blobs-using-custom-metadata"></a>Niet-verouderde blobs opnieuw indexeren (met behulp van aangepaste meta gegevens)

Wanneer een Indexeer functie een verwijderde BLOB verwerkt en het bijbehorende Zoek document uit de index verwijdert, wordt die BLOB niet opnieuw bezocht als u deze later herstelt als de tijds tempel van de BLOB `LastModified` ouder is dan de laatste uitgevoerde indexer.

Als u het document opnieuw wilt indexeren, wijzigt u de `"softDeleteMarkerValue" : "false"` voor die Blob en voert u de Indexeer functie opnieuw uit.

## <a name="next-steps"></a>Volgende stappen

+ [Indexeerfuncties in Azure Cognitive Search](search-indexer-overview.md)
+ [Een BLOB-Indexeer functie configureren](search-howto-indexing-azure-blob-storage.md)
+ [Overzicht van BLOB-indexering](search-blob-storage-integration.md)