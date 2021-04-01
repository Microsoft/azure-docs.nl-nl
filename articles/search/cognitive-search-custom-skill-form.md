---
title: Aangepaste vaardigheid van de formulier herkenning (C#)
titleSuffix: Azure Cognitive Search
description: Meer informatie over het maken van aangepaste vaardig heden voor een formulier herkenning met C# en Visual Studio.
manager: nitinme
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: a3b073cdb90e0c427bfbca15c1440b9122672610
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98880132"
---
# <a name="example-create-a-form-recognizer-custom-skill"></a>Voor beeld: een aangepaste vaardigheid voor het herkennen van een formulier maken

In dit voor beeld van Azure Cognitive Search vaardig heden leert u hoe u een aangepaste vaardigheid voor een formulier herkenning kunt maken met C# en Visual Studio. Met de formulier herkenning worden documenten geanalyseerd en worden sleutel-waardeparen en tabel gegevens geëxtraheerd. Door de formulier herkenning in te stellen op de [aangepaste vaardigheids interface](cognitive-search-custom-skill-interface.md), kunt u deze mogelijkheid toevoegen als een stap in een end-to-end-verrijkings pijplijn. De pijp lijn kan de documenten vervolgens laden en andere trans formaties uitvoeren.

## <a name="prerequisites"></a>Vereisten

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (alle edities).
- Ten minste vijf vormen van hetzelfde type. U kunt voorbeeld gegevens gebruiken die in deze hand leiding zijn opgenomen.

## <a name="create-a-form-recognizer-resource"></a>Een Form Recognizer-resource maken

[!INCLUDE [create resource](../cognitive-services/form-recognizer/includes/create-resource.md)]

## <a name="train-your-model"></a>Uw model trainen

U moet een model voor formulier herkenning trainen met uw invoer formulieren voordat u deze vaardigheid gebruikt. Volg de [krul Snelstartgids](../cognitive-services/form-recognizer/quickstarts/client-library.md?pivots=programming-language-rest-api) voor meer informatie over het trainen van een model. U kunt gebruikmaken van de voorbeeld formulieren in die Snelstartgids of u kunt uw eigen gegevens gebruiken. Wanneer het model is getraind, kopieert u de ID-waarde naar een veilige locatie.

## <a name="set-up-the-custom-skill"></a>De aangepaste vaardigheid instellen

Deze zelf studie maakt gebruik van het [AnalyzeForm](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Vision/AnalyzeForm) -project in de GitHub-opslag plaats [Azure Search Power skills](https://github.com/Azure-Samples/azure-search-power-skills) . Kloon deze opslag plaats naar uw lokale machine en navigeer naar **Vision/AnalyzeForm/** om het project te openen. Open vervolgens _AnalyzeForm. csproj_ in Visual Studio. Dit project maakt een Azure-functie resource die voldoet aan de [aangepaste vaardigheids interface](cognitive-search-custom-skill-interface.md) en kan worden gebruikt voor de verrijking van azure-Cognitive Search. Er worden formulieren gevormd als invoer en de sleutel/waarde-paren die u opgeeft, worden uitgevoerd (als tekst).

Voeg eerst omgevings variabelen op project niveau toe. Zoek het project **AnalyzeForm** in het linkerdeel venster, klik er met de rechter muisknop op en selecteer **Eigenschappen**. Klik in het venster **Eigenschappen** op het tabblad **fout opsporing** en zoek het veld **omgevings variabelen** . Klik op **toevoegen** om de volgende variabelen toe te voegen:
* `FORMS_RECOGNIZER_ENDPOINT_URL` met de waarde die is ingesteld op uw eind punt-URL.
* `FORMS_RECOGNIZER_API_KEY` met de waarde die is ingesteld op uw abonnements sleutel.
* `FORMS_RECOGNIZER_MODEL_ID` met de waarde die is ingesteld op de ID van het model dat u hebt getraind.
* `FORMS_RECOGNIZER_RETRY_DELAY` waarvan de waarde is ingesteld op 1000. Deze waarde is de tijd in milliseconden die het programma wacht voordat de query opnieuw wordt uitgevoerd.
* `FORMS_RECOGNIZER_MAX_ATTEMPTS` waarvan de waarde is ingesteld op 100. Deze waarde is het aantal keren dat het programma de service zal opvragen tijdens het ophalen van een geslaagd antwoord.

Open vervolgens _AnalyzeForm. cs_ en zoek de `fieldMappings` variabele, die verwijst naar de *field-mappings.jsin* het bestand. Dit bestand (en de variabele waarmee ernaar wordt verwezen) definieert de lijst met sleutels die u wilt extra heren uit uw formulieren en een aangepast label voor elke sleutel. Zo betekent een waarde `{ "Address:", "address" }, { "Invoice For:", "recipient" }` dat het script alleen de waarden voor de gedetecteerde `Address:` en `Invoice For:` velden opslaat, en worden deze waarden labelen met `"address"` `"recipient"` respectievelijk.

Let ten slotte op de `contentType` variabele. Met dit script wordt het opgegeven formulier Recognizer-model uitgevoerd op externe documenten waarnaar wordt verwezen door de URL, zodat het inhouds type `application/json` . Als u lokale bestanden wilt analyseren door de byte-streams in de HTTP-aanvragen op te nemen, moet u het `contentType` juiste [MIME-type](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types) voor het bestand wijzigen.

## <a name="test-the-function-from-visual-studio"></a>De functie testen vanuit Visual Studio

Nadat u het project hebt bewerkt, slaat u het op en stelt u het **AnalyzeForm** -project in als het opstart project in Visual Studio (als dit nog niet is ingesteld). Druk vervolgens op **F5** om de functie uit te voeren in uw lokale omgeving. Gebruik een REST-service zoals [postman](https://www.postman.com/) om de functie aan te roepen.

### <a name="http-request"></a>HTTP-aanvraag

U maakt de volgende aanvraag om de functie aan te roepen.

```HTTP
POST https://localhost:7071/api/analyze-form
```

### <a name="request-body"></a>Aanvraagbody

Begin met de onderstaande sjabloon voor aanvraag tekst.

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": { 
                "formUrl": "<your-form-url>",
                "formSasToken": "<your-sas-token>"
            }
        }
    ]
}
```

Hier moet u de URL opgeven van een formulier dat hetzelfde type heeft als de formulieren waarmee u hebt getraind. Voor test doeleinden kunt u een van uw trainings formulieren gebruiken. Als u de krul Snelstartgids hebt gevolgd, worden uw formulieren in een Azure Blob-opslag account geplaatst. Open Azure Storage Explorer, zoek een formulier bestand, klik er met de rechter muisknop op en selecteer **Shared Access Signature ophalen**. Het volgende dialoog venster bevat een URL en SAS-token. Voer deze teken reeksen in `"formUrl"` de `"formSasToken"` velden en van de hoofd tekst van de aanvraag in.

> [!div class="mx-imgBorder"]
> ![Azure Storage Explorer; Er is een PDF-document geselecteerd](media/cognitive-search-skill-form/form-sas.png)

Als u een extern document wilt analyseren dat zich niet in Azure Blob-opslag bevindt, plakt u de URL in het `"formUrl"` veld en laat u het `"formSasToken"` veld leeg.

> [!NOTE]
> Wanneer de vaardigheid is geïntegreerd in een vaardig heden, worden de URL en het token verschaft door Cognitive Search.

### <a name="response"></a>Antwoord

Er wordt een antwoord weer gegeven dat vergelijkbaar is met het volgende voor beeld:

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": {
                "address": "1111 8th st. Bellevue, WA 99501 ",
                "recipient": "Southridge Video 1060 Main St. Atlanta, GA 65024 "
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>De functie publiceren naar Azure

Wanneer u tevreden bent met het functie gedrag, kunt u het publiceren.

1. Klik in de **Solution Explorer** in Visual Studio met de rechter muisknop op het project en selecteer **publiceren**. Kies **nieuwe**  >  **publicatie** maken.
1. Als u Visual Studio nog niet hebt verbonden met uw Azure-account, selecteert u **een account toevoegen....**
1. Volg de aanwijzingen op het scherm. Geef een unieke naam op voor uw app service, het Azure-abonnement, de resource groep, het hosting plan en het opslag account dat u wilt gebruiken. U kunt een nieuwe resource groep, een nieuw hosting plan en een nieuw opslag account maken als u deze nog niet hebt. Wanneer u klaar bent, selecteert u **Maken**.
1. Nadat de implementatie is voltooid, ziet u de URL van de site. Deze URL is het adres van uw functie-app in Azure. Sla het bestand op een tijdelijke locatie op.
1. Navigeer in het [Azure Portal](https://portal.azure.com)naar de resource groep en zoek naar de `AnalyzeForm` functie die u hebt gepubliceerd. Onder de sectie **beheren** ziet u de host-sleutels. Kopieer de *standaardhosts* sleutel en sla deze op een tijdelijke locatie op.

## <a name="connect-to-your-pipeline"></a>Verbinding maken met uw pijp lijn

Als u deze vaardigheid wilt gebruiken in een Cognitive Search pijp lijn, moet u een vaardigheids definitie toevoegen aan uw vaardig heden. Het volgende JSON-blok is een voor beeld van een vaardigheids definitie (u moet de invoer en uitvoer bijwerken zodat deze overeenkomen met uw specifieke scenario en uw vaardig heden-omgeving). Vervang door `AzureFunctionEndpointUrl` de URL van uw functie en vervang door `AzureFunctionDefaultHostKey` de sleutel van uw host.

```json
{ 
  "description":"Skillset that invokes the Form Recognizer custom skill",
  "skills":[ 
    "[... your existing skills go here]",
    { 
      "@odata.type":"#Microsoft.Skills.Custom.WebApiSkill",
      "name":"formrecognizer",
      "description":"Extracts fields from a form using a pre-trained form recognition model",
      "uri":"[AzureFunctionEndpointUrl]/api/analyze-form?code=[AzureFunctionDefaultHostKey]",
      "httpMethod":"POST",
      "timeout":"PT30S",
      "context":"/document",
      "batchSize":1,
      "inputs":[ 
        { 
          "name":"formUrl",
          "source":"/document/metadata_storage_path"
        },
        { 
          "name":"formSasToken",
          "source":"/document/metadata_storage_sas_token"
        }
      ],
      "outputs":[ 
        { 
          "name":"address",
          "targetName":"address"
        },
        { 
          "name":"recipient",
          "targetName":"recipient"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding hebt u een aangepaste vaardigheid gemaakt op basis van de Azure Form Recognizer-service. Zie de volgende bronnen voor meer informatie over aangepaste vaardig heden. 

* [Azure Search Power vaardig heden: een opslag plaats met aangepaste vaardig heden](https://github.com/Azure-Samples/azure-search-power-skills)
* [Een aangepaste vaardigheid toevoegen aan een AI-verrijkings pijplijn](cognitive-search-custom-skill-interface.md)
* [Een set vaardigheden definiëren](cognitive-search-defining-skillset.md)
* [Een vaardig heden maken (REST)](/rest/api/searchservice/create-skillset)
* [Verrijkte velden toewijzen](cognitive-search-output-field-mapping.md)