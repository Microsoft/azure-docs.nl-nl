---
title: Document indelingen en naam conventies-aangepaste Translator
titleSuffix: Azure Cognitive Services
description: Dit is een hand leiding voor document indelingen en naam conventies in Custom Translator. Dit concept helpt bij het beter beheren van documenten namen en het voor komen van naam conflicten.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 9598d3c1d339d1c37b02892cc83164acae447434
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895846"
---
# <a name="document-formats-and-naming-convention-guidance"></a>Richt lijnen voor document indelingen en naamgevings regels

Elk bestand dat wordt gebruikt voor aangepaste vertaling moet ten minste **vier** tekens lang zijn.

Deze tabel bevat alle ondersteunde bestands indelingen die u kunt gebruiken om uw Vertaal systeem te bouwen:

| Indeling            | Uitbreidingen   | Description                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| XLIFF             | . XLF, . XLIFF | Een parallelle document indeling, export van Vertaal geheugen systemen. De gebruikte talen worden in het bestand gedefinieerd.                                                                                                                                                              |
| TMX               | . TMX         | Een parallelle document indeling, export van Vertaal geheugen systemen. De gebruikte talen worden in het bestand gedefinieerd.                                                                                                                                                              |
| TELEFOON               | . TELEFOON         | ZIP is een archief bestands indeling.                                                                                                                                                                                                        |
| Locstudio         | . LCL         | Een micro soft-indeling voor parallelle documenten                                                                                                                                                                                                                                      |
| Microsoft Word    | . DOCX        | Micro soft Word-document                                                                                                                                                                                                                                                        |
| Adobe Acrobat     | . EXPORTEERT         | Adobe Acrobat Portable Document                                                                                                                                                                                                                                                |
| HTML              | . HTML,. HTM  | HTML-document                                                                                                                                                                                                                                                                  |
| Tekstbestand         | . TXT         | Tekst bestanden met UTF-16-of UTF-8-code ring. De bestands naam mag geen Japanse tekens bevatten.                                                                                                                                                                                        |
| Uitgelijnd tekst bestand | . ALIGN       | De uitbrei ding `.ALIGN` is een speciale extensie die u kunt gebruiken als u weet dat de zinnen in het document paar perfect zijn uitgelijnd. Als u een `.ALIGN` bestand opgeeft, worden de zinnen door aangepaste vertalers niet voor u uitgelijnd. |
| Excel-bestand        | . XLSX        | Excel-bestand (2013 of hoger). De eerste regel of rij van het werk blad moet taal code zijn.                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>Woordenboek indelingen

Voor woorden lijsten ondersteunt aangepaste vertaler alle bestands indelingen die worden ondersteund voor trainings sets. Als u een Excel-woorden lijst gebruikt, moet de eerste regel of rij van het werk blad taal codes zijn.

## <a name="zip-file-formats"></a>Zip-bestands indelingen

Documenten kunnen worden gegroepeerd in één ZIP-bestand en worden geüpload. De aangepaste vertaler ondersteunt zip-bestands indelingen (ZIP, GZ en TGZ).

Elk document in het zip-bestand met de extensie TXT, HTML, HTM, PDF, DOCX, ALIGN moet voldoen aan deze naamgevings Conventie:

{document naam} \_ {taal code} waarbij {document naam} de naam van uw document is, {taal code} de ISO LanguageID (twee tekens) is, waarmee wordt aangegeven dat het document zinnen bevat in die taal. Er moet een onderstrepings teken (_) voor de taal code zijn.

Als u bijvoorbeeld twee parallelle documenten in een zip wilt uploaden naar een Engels-naar-Spaans systeem, moeten de bestanden ' data_en ' en ' data_es ' worden genoemd.

Vertaal geheugen bestanden (TMX, XLF, XLIFF, LCL, XLSX) hoeven niet te voldoen aan de specifieke taal naam Conventie.  

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [project](workspace-and-project.md#what-is-a-custom-translator-project) om ze te maken en te beheren.
