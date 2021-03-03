---
title: Eigenschappen van meta gegevens van inhoud
titleSuffix: Azure Cognitive Search
description: Meta gegevens eigenschappen van documenten kunnen inhoud leveren aan velden in een zoek index of informatie die tijdens runtime het indexerings gedrag informeert. Dit artikel bevat een lijst met eigenschappen van meta gegevens die worden ondersteund in azure Cognitive Search.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/22/2021
ms.openlocfilehash: cbb35f596a1d32816d1a73b462bf590d9dde0d52
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668415"
---
# <a name="content-metadata-properties-used-in-azure-cognitive-search"></a>Eigenschappen van meta gegevens van inhoud die worden gebruikt in azure Cognitive Search

Share point online en Azure Blob-opslag kunnen diverse inhoud bevatten en veel van deze inhouds typen hebben eigenschappen van meta gegevens die nuttig kunnen zijn voor indexering. Net zoals u zoek velden kunt maken voor de standaard eigenschappen van blobs **`metadata_storage_name`** , kunt u velden maken voor eigenschappen van meta gegevens die specifiek zijn voor een document indeling.

## <a name="supported-document-formats"></a>Ondersteunde documentindelingen

Cognitive Search ondersteunt het indexeren van blobs en het indexeren van share point online-documenten voor de volgende document indelingen:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="properties-by-document-format"></a>Eigenschappen op document indeling

De volgende tabel geeft een overzicht van de verwerking die wordt uitgevoerd voor elke document indeling en beschrijft de eigenschappen van meta gegevens die zijn geëxtraheerd door een BLOB-indexer en de share point online-indexer.

| Document indeling/inhouds type | Geëxtraheerde meta gegevens | Details verwerken |
| --- | --- | --- |
| HTML (tekst/HTML) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |HTML-opmaak verwijderen en tekst ophalen |
| PDF (toepassing/PDF) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Tekst extra heren, inclusief Inge sloten documenten (met uitzonde ring van afbeeldingen) |
| DOCX (Application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Tekst extra heren, inclusief Inge sloten documenten |
| DOC (toepassing/MSWord) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Tekst extra heren, inclusief Inge sloten documenten |
| DOCM (Application/vnd.ms-word.document. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Tekst extra heren, inclusief Inge sloten documenten |
| WORD XML (application/vnd. MS-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Strip XML-opmaak en extra heren tekst |
| WORD 2003 XML (application/vnd. MS-WordML) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Strip XML-opmaak en extra heren tekst |
| XLSX (application/vnd. openxmlformats-officedocument. SpreadsheetML. Sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Tekst extra heren, inclusief Inge sloten documenten |
| XLS (application/vnd. MS-Excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Tekst extra heren, inclusief Inge sloten documenten |
| XLSM (application/vnd. MS-Excel. Sheet. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Tekst extra heren, inclusief Inge sloten documenten |
| PPTX (application/vnd. openxmlformats-officedocument. presentationml. Presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Tekst extra heren, inclusief Inge sloten documenten |
| PPT (application/vnd. MS-PowerPoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Tekst extra heren, inclusief Inge sloten documenten |
| PPTM (application/vnd. MS-PowerPoint. Presentation. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Tekst extra heren, inclusief Inge sloten documenten |
| MSG (application/vnd. MS-Outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Tekst extra heren, inclusief tekst die is geëxtraheerd uit bijlagen. `metadata_message_to_email``metadata_message_cc_email`en `metadata_message_bcc_email` zijn teken reeks verzamelingen, de rest van de velden zijn teken reeksen.|
| ODT (application/vnd. Oasis. open document. text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Tekst extra heren, inclusief Inge sloten documenten |
| ODS (application/vnd. Oasis. open document. spread sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Tekst extra heren, inclusief Inge sloten documenten |
| ODP (application/vnd. Oasis. open document. Presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |Tekst extra heren, inclusief Inge sloten documenten |
| ZIP (toepassing/zip) |`metadata_content_type` |Tekst uit alle documenten in het archief extra heren |
| GZ (toepassing/gzip) |`metadata_content_type` |Tekst uit alle documenten in het archief extra heren |
| EPUB (toepassing/EPUB en zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Tekst uit alle documenten in het archief extra heren |
| XML (toepassing/XML) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |Strip XML-opmaak en extra heren tekst |
| JSON (toepassing/JSON) |`metadata_content_type`<br/>`metadata_content_encoding` |Tekst extraheren<br/>Opmerking: als u meerdere document velden van een JSON-BLOB wilt extra heren, raadpleegt u [JSON-blobs indexeren](search-howto-index-json-blobs.md) voor meer informatie |
| EML (message/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Tekst extra heren, inclusief bijlagen |
| RTF (toepassing/RTF) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Tekst extraheren|
| Tekst zonder opmaak (tekst/normaal) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Tekst extraheren|

## <a name="see-also"></a>Zie ook

* [Indexeerfuncties in Azure Cognitive Search](search-indexer-overview.md)
* [Blobs begrijpen met AI](search-blob-ai-integration.md)
* [Overzicht van BLOB-indexering](search-blob-storage-integration.md)
* [Share point online indexeren](search-howto-index-sharepoint-online.md)