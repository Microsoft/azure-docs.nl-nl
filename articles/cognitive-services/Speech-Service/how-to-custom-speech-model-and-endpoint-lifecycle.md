---
title: De levens cyclus van het model en het eind punt van de Custom Speech-Speech-Service
titleSuffix: Azure Cognitive Services
description: Custom Speech biedt basis modellen voor aanpassing en Hiermee kunt u aangepaste modellen maken op basis van uw gegevens. In dit artikel worden de tijd lijnen beschreven voor modellen en eind punten die gebruikmaken van deze modellen.
services: cognitive-services
author: heikora
manager: dongli
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/2/2021
ms.author: heikora
ms.openlocfilehash: b82a732533c3d069b519b07c3209d4b96c472900
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385022"
---
# <a name="model-and-endpoint-lifecycle"></a>Levens cyclus van modellen en eind punten

Onze standaard spraak (niet aangepast) is gebaseerd op AI-modellen die we baseren op basis modellen. In de meeste gevallen trainen we een ander basis model voor elke gesp roken taal die we ondersteunen.  De spraak service wordt om de paar maanden bijgewerkt met nieuwe basis modellen om de nauw keurigheid en kwaliteit te verbeteren.  
Met Custom Speech worden aangepaste modellen gemaakt door het aanpassen van een gekozen basis model met gegevens uit uw specifieke klant scenario. Wanneer u een aangepast model hebt gemaakt, wordt dat model niet bijgewerkt of gewijzigd, zelfs niet als het bijbehorende basis model van waaruit het is aangepast, wordt bijgewerkt in de standaard spraak service.  
Met dit beleid kunt u een bepaald aangepast model gedurende lange tijd blijven gebruiken nadat u een aangepast model hebt dat aan uw behoeften voldoet.  We raden u echter aan uw aangepaste model regel matig opnieuw te maken, zodat u kunt aanpassen uit het meest recente basis model om te profiteren van de verbeterde nauw keurigheid en kwaliteit.

Andere belang rijke termen met betrekking tot de levens cyclus van het model zijn:

* **Aanpassing**: het maken van een basis model en het aanpassen van uw domein/scenario met behulp van tekst gegevens en/of audio gegevens.
* **Decoderen**: het gebruik van een model en het uitvoeren van spraak herkenning (het coderen van audio in tekst).
* **Eind punt**: een gebruikersspecifieke implementatie van een basis model of een aangepast model dat *alleen* toegankelijk is voor een bepaalde gebruiker.

### <a name="expiration-timeline"></a>Verloop tijd lijn

Als nieuwe modellen en nieuwe functionaliteit beschikbaar en ouder worden, worden minder nauw keurige modellen buiten gebruik gesteld. Zie de volgende tijd lijnen voor het model en de verval datum van het eind punt:

**Basis modellen** 

* Aanpassing: beschikbaar voor één jaar. Nadat het model is geïmporteerd, is het één jaar beschikbaar om aangepaste modellen te maken. Na één jaar moeten nieuwe aangepaste modellen worden gemaakt op basis van een nieuwere versie van het basis model.  
* Decodering: twee jaar na het importeren beschikbaar. U kunt dus een eind punt maken en batch transcriptie voor twee jaar gebruiken met dit model. 
* Eind punten: beschikbaar op dezelfde tijd lijn als decoderen.

**Aangepaste modellen**

* Decodering: twee jaar nadat het model is gemaakt. U kunt het aangepaste model twee jaar gebruiken (batch/realtime/testen) nadat het is gemaakt. Na twee jaar *moet u uw model opnieuw trainen* omdat het basis model meestal is afgeschaft voor aanpassing.  
* Eind punten: beschikbaar op dezelfde tijd lijn als decoderen.

Wanneer een basis model of aangepast model verloopt, wordt het altijd terugvallen op de *nieuwste versie van het basis model*. Uw implementatie wordt dus nooit onderbroken, maar het kan minder nauw keurig zijn voor *uw specifieke gegevens* als aangepaste modellen de verval datum bereiken. U kunt de verval datum van een model weer geven op de volgende plaatsen in het gedeelte Custom Speech van de speech studio:

* Overzicht van model trainingen
* Details van model training
* Implementatieoverzicht
* Implementatie Details

Hier volgt een voor beeld van het model trainings overzicht:

![Model training samen vatting ](media/custom-speech/custom-speech-model-training-with-expiry.png) en ook van de detail pagina van de model training:

![Details van model training](media/custom-speech/custom-speech-model-details-with-expiry.png)

U kunt de verval datums ook controleren via de [`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) en [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) aangepaste spraak-api's onder de `deprecationDates` eigenschap in het JSON-antwoord.

Hier volgt een voor beeld van de verval gegevens van de API-aanroep van GetModel. De **DEPRECATIONDATES** tonen wanneer het model verloopt: 
```json
{
    "SELF": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}",
    "BASEMODEL": {
    "SELF": HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/BASE/{id}
    },
    "DATASETS": [
    {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/datasets/{id}
    }
    ],
    "LINKS": {
    "MANIFEST": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}/MANIFEST",
    "COPYTO": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}/copyto
    },
    "PROJECT": {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/projects/{id}
    },
    "PROPERTIES": {
    "DEPRECATIONDATES": {
        "ADAPTATIONDATETIME": "2022-01-15T00:00:00Z",     // last date the base model can be used for adaptation
        "TRANSCRIPTIONDATETIME": "2023-03-01T21:27:29Z"   // last date this model can be used for decoding
    }
    },
    "LASTACTIONDATETIME": "2021-03-01T21:27:40Z",
    "STATUS": "SUCCEEDED",
    "CREATEDDATETIME": "2021-03-01T21:27:29Z",
    "LOCALE": "EN-US",
    "DISPLAYNAME": "EXAMPLE MODEL",
    "DESCRIPTION": "",
    "CUSTOMPROPERTIES": {
    "PORTALAPIVERSION": "3"
    }
}
```
Houd er rekening mee dat u het model op een aangepast spraak eindpunt zonder downtime kunt bijwerken door het model dat door het eind punt wordt gebruikt, te wijzigen in de sectie implementatie van de speech Studio of via de aangepaste spraak-API.

## <a name="what-happens-when-models-expire-and-how-to-update-them"></a>Wat gebeurt er wanneer modellen verlopen en hoe ze kunnen worden bijgewerkt
Wat er gebeurt wanneer een model verloopt en hoe het model moet worden bijgewerkt, is afhankelijk van hoe het wordt gebruikt.
### <a name="batch-transcription"></a>Batchtranscriptie
Als een model verloopt dat wordt gebruikt met [batch transcriptie](batch-transcription.md) transcriptie-aanvragen, mislukt de fout 4xx. Als u wilt voor komen dat deze de `model` para meter in de JSON die is verzonden in de hoofd tekst van de **transcriptie maken** , naar een meer recent basis model of een meer recent aangepast model bijwerken. U kunt ook de `model` vermelding uit de JSON verwijderen om altijd het meest recente basis model te gebruiken.
### <a name="custom-speech-endpoint"></a>Aangepast spraak eindpunt
Als een model verloopt dat wordt gebruikt door een [aangepast spraak eindpunt](how-to-custom-speech-train-model.md), wordt de service automatisch terugvallen op het gebruik van het meest recente basis model voor de taal die u gebruikt. kunt u in het menu **Custom speech** boven aan de pagina de optie **implementatie** selecteren en vervolgens op de naam van het eind punt klikken om de details ervan weer te geven. Boven aan de pagina Details ziet u een knop **Update model** waarmee u het model dat door dit eind punt wordt gebruikt zonder uitval tijd probleemloos kunt bijwerken. U kunt deze wijziging ook programmatisch aanbrengen met behulp van de rest API van het [**Update model**](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/UpdateModel) .

## <a name="next-steps"></a>Volgende stappen

* [Een model trainen en implementeren](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Aanvullende bronnen

* [Uw gegevens voorbereiden en testen](./how-to-custom-speech-test-and-train.md)
* [Uw gegevens controleren](how-to-custom-speech-inspect-data.md)