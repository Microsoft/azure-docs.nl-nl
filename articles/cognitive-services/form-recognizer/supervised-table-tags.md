---
title: Tabel Tags gebruiken voor het trainen van uw aangepaste formulier model-formulier herkenner
titleSuffix: Azure Cognitive Services
description: Meer informatie over het effectief gebruiken van labeling voor de code van een super visie.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 5422520c6a863876091d7820a5c07fa2413346c7
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467816"
---
# <a name="use-table-tags-to-train-your-custom-form-model"></a>Tabel Tags gebruiken om uw aangepaste formulier model te trainen

In dit artikel leert u hoe u uw aangepaste formulier model kunt trainen met tabel Tags (labels). Voor sommige scenario's is complexere labels vereist dan het gewoon uitlijnen van sleutel-waardeparen. Dergelijke scenario's zijn onder andere het extra heren van informatie uit formulieren met complexe hiërarchische structuren of items die niet automatisch worden gedetecteerd en geëxtraheerd door de service. In dergelijke gevallen kunt u tabel Tags gebruiken om uw aangepaste formulier model te trainen.

## <a name="when-should-i-use-table-tags"></a>Wanneer moet ik tabel Tags gebruiken?

Hier volgen enkele voor beelden van het gebruik van tabel Tags:

- De gegevens die u wilt extra heren, worden weer gegeven als tabellen in uw formulieren en de structuur van de tabellen is zinvol. Elke rij van de tabel vertegenwoordigt bijvoorbeeld één item en elke kolom van de rij vertegenwoordigt een specifieke functie van het item. In dit geval kunt u een tabel code gebruiken waarbij een kolom bestaat uit functies en een rij bevat informatie over elke functie.
- Er zijn gegevens die u wilt extra heren die niet in specifieke formulier velden worden weer gegeven, maar de gegevens kunnen in een tweedimensionale raster passen. Het formulier bevat bijvoorbeeld een lijst met personen en bevat een voor naam, een achternaam en een e-mail adres. U wilt deze informatie ophalen. In dit geval kunt u een tabel code met de voor naam, achternaam en e-mail adres gebruiken als kolommen en wordt elke rij gevuld met informatie over een persoon uit uw lijst.

> [!NOTE]
> Met formulier herkenning worden automatisch alle tabellen in uw documenten gevonden en opgehaald, ongeacht of de tabellen zijn gelabeld of niet. Daarom hoeft u niet elke tabel uit het formulier te labelen met een tabel code en hoeft u de tabel Tags niet de structuur van de tabel te repliceren die in uw formulier is gevonden. Tabellen die automatisch worden geëxtraheerd door de formulier herkenner, worden opgenomen in de sectie pageResults van de JSON-uitvoer.

## <a name="create-a-table-tag-with-form-ocr-test-tool-fott"></a>Een tabel code maken met het formulier OCR testen (FOTT)
<!-- markdownlint-disable MD004 -->
* Bepaal of u een tabel label met een **dynamische** of **vaste grootte** wilt. Een dynamische tabel code gebruiken als het aantal rijen van het document tot het document verschilt. Als het aantal rijen consistent is in uw documenten, gebruikt u een tabel label met een vaste grootte.
* Als uw tabel code dynamisch is, definieert u de kolom namen en het gegevens type en de indeling voor elke kolom.
* Als uw tabel een vaste grootte heeft, definieert u de kolom naam, de rijnaam, het gegevens type en de indeling voor elk label.
:::image type="content" source="./media/table-tag-configure.png" alt-text="Een tabel code configureren":::

## <a name="label-your-table-tag-data"></a>Label gegevens van uw tabel labelen

* Als uw project een tabel label heeft, kunt u het deel venster labelen openen en het label vullen zoals u zou labelen.
:::image type="content" source="media/table-labeling.png" alt-text="Label met tabel Tags":::

## <a name="next-steps"></a>Volgende stappen

Volg onze Snelstartgids om uw aangepaste formulier Recognizer-model te trainen en gebruiken:

> [!div class="nextstepaction"]
> [Trainen met labels met behulp van het voor beeld-label programma](quickstarts/label-tool.md)

## <a name="see-also"></a>Zie ook

* [Wat is Form Recognizer?](overview.md)
>
