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
ms.date: 03/10/2021
ms.author: heikora
ms.openlocfilehash: b8e02071eca139cde02a8bad1b0e0e443db6ab86
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103555331"
---
# <a name="model-and-endpoint-lifecycle"></a>Levens cyclus van modellen en eind punten

Custom Speech maakt gebruik van *basis modellen* en *aangepaste modellen*. Elke taal heeft een of meer basis modellen. Wanneer een nieuw spraak model wordt vrijgegeven aan de normale spraak service, wordt dit doorgaans ook als een nieuw basis model geïmporteerd in de Custom Speech-Service. Ze worden elke 6 tot 12 maanden bijgewerkt. Oudere modellen worden doorgaans minder nuttig in de loop van de tijd, omdat het meest recente model meestal een hogere nauw keurigheid heeft.

Aangepaste modellen worden daarentegen gemaakt door het aanpassen van een gekozen basis model met gegevens uit uw specifieke klant scenario. U kunt een bepaald aangepast model gedurende lange tijd blijven gebruiken nadat u er een hebt dat aan uw behoeften voldoet. We raden u echter aan regel matig bij te werken naar het nieuwste basis model en het opnieuw te trainen in een periode met aanvullende gegevens. 

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

Hier volgt een voor beeld van de verval gegevens van de API-aanroep van GetModel. De ' DEPRECATIONDATES ' tonen het volgende: 
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
        "ADAPTATIONDATETIME": "2022-01-15T00:00:00Z",     // last date this model can be used for adaptation
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

## <a name="next-steps"></a>Volgende stappen

* [Een model trainen en implementeren](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Aanvullende bronnen

* [Uw gegevens voorbereiden en testen](./how-to-custom-speech-test-and-train.md)
* [Uw gegevens controleren](how-to-custom-speech-inspect-data.md)