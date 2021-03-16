---
title: Een trainings gegevensverzameling maken voor een aangepaste herkenner van het model formulier
titleSuffix: Azure Cognitive Services
description: Meer informatie over hoe u ervoor kunt zorgen dat uw trainings gegevensverzameling is geoptimaliseerd voor het trainen van een model voor formulier herkenning.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: lajanuar
ms.openlocfilehash: b33ac3cb710a2d2a9d92efadf14dc829cb5da6e8
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467490"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Een trainings gegevensverzameling voor een aangepast model bouwen

Wanneer u het aangepaste model van de formulier Recognizer gebruikt, geeft u uw eigen trainings gegevens op voor de [aangepaste model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync) bewerking voor het trainen, zodat het model kan worden opgeleid voor uw branchespecifieke formulieren. Volg deze hand leiding voor informatie over het verzamelen en voorbereiden van gegevens om het model effectief te trainen.

U moet ten minste vijf ingevulde formulieren van hetzelfde type hebben.

Als u hand matig gelabelde trainings gegevens wilt gebruiken, moet u beginnen met ten minste vijf ingevulde formulieren van hetzelfde type. Naast de vereiste gegevensset kunt u nog steeds niet-gelabelde formulieren gebruiken.

## <a name="custom-model-input-requirements"></a>Vereisten voor aangepast model invoer

Zorg er eerst voor dat uw trainings gegevensverzameling de invoer vereisten voor de formulier herkenner volgt.

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="training-data-tips"></a>Tips voor trainings gegevens

Volg deze aanvullende tips om uw gegevensset verder te optimaliseren voor training.

* Gebruik zo mogelijk PDF-documenten op basis van tekst in plaats van op afbeeldingen gebaseerde documenten. Gescande Pdf's worden behandeld als installatie kopieën.
* Voor ingevulde formulieren gebruikt u voor beelden waarvoor alle velden zijn ingevuld.
* Gebruik in formulieren met in elk veld verschillende waarden.
* Als uw formulier afbeeldingen een lagere kwaliteit hebben, gebruikt u een grotere gegevensset (bijvoorbeeld 10-15 installatie kopieën).

## <a name="upload-your-training-data"></a>Uw trainings gegevens uploaden

Wanneer u de set formulier documenten die u voor training gebruikt, hebt samengesteld, moet u deze uploaden naar een Azure Blob Storage-container. Als u niet weet hoe u een Azure-opslag account maakt met een container, volgt u de [Azure Storage Snelstartgids voor Azure Portal](../../storage/blobs/storage-quickstart-blobs-portal.md). De Standard-prestatie-laag gebruiken.

Als u hand matig gelabelde gegevens wilt gebruiken, moet u ook de *.labels.js* uploaden en *.ocr.jsop* bestanden die overeenkomen met uw trainings documenten. U kunt het voor [beeld-label programma](./quickstarts/label-tool.md) (of uw eigen gebruikers interface) gebruiken om deze bestanden te genereren.

### <a name="organize-your-data-in-subfolders-optional"></a>Organiseer uw gegevens in submappen (optioneel)

Standaard worden alleen formulier documenten gebruikt die zich [in de hoofdmap](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync) van uw opslag container bevinden. U kunt echter met gegevens in submappen trainen als u deze in de API-aanroep opgeeft. Normaal gesp roken heeft de hoofd tekst van de [trein aangepaste model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync) aanroep de volgende indeling, waarbij `<SAS URL>` de URL van de gedeelde Access-hand tekening van de container is:

```json
{
  "source":"<SAS URL>"
}
```

Als u de volgende inhoud aan de aanvraag tekst toevoegt, wordt de API getraind met documenten die zich in submappen bevinden. Het `"prefix"` veld is optioneel en beperkt de set trainings gegevens tot bestanden waarvan het pad begint met de opgegeven teken reeks. Een waarde van bijvoorbeeld `"Test"` resulteert in de API alleen om te kijken naar de bestanden of mappen die beginnen met het woord ' test '.

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  },
  "useLabelFile": false
}
```

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een set trainings gegevens bouwt, volgt u een Snelstartgids om een aangepast model voor formulier herkenning te trainen en te beginnen met het gebruik ervan in uw formulieren.

* [Een model trainen en formulier gegevens extra heren met behulp van de client bibliotheek of de REST API](./quickstarts/client-library.md)
* [Trainen met labels met behulp van het voor beeld-label programma](./quickstarts/label-tool.md)

## <a name="see-also"></a>Zie ook

* [Wat is Form Recognizer?](./overview.md)