---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 12/09/2020
ms.topic: include
ms.openlocfilehash: 60a52c04ad9e1b9d5207c0bd4f3d218495be39d0
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102184264"
---
Aan de slag met de REST API van Custom Vision. Volg deze stappen om de API aan te roepen en een afbeeldingsclassificatiemodel te bouwen. U maakt een project, voegt tags toe, traint het project en gebruikt de voorspellingseindpunt-URL van het project om het programmatisch te testen. Gebruik dit voorbeeld als een sjabloon om uw eigen beeldherkennings-app te maken.

> [!NOTE]
> Custom Vision is het eenvoudigst te gebruiken via een SDK van de clientbibliotheek of via de [aanwijzingen in de browser](../../get-started-build-detector.md).

Gebruik de Custom Vision-clientbibliotheek voor .NET voor het volgende:

* Een nieuw project maken in de Custom Vision-service
* Label aan het project toevoegen
* Afbeeldingen uploaden en labelen
* Het project trainen
* De huidige herhaling publiceren
* Voorspellingseindpunt testen

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* Zodra u uw Azure-abonnement hebt, <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title="een Custom Vision-resource maken"  target="_blank"> maakt u een Custom Vision-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in de Azure-portal om een trainings- en voorspellingsresource te maken en uw sleutels en eindpunt op te halen. Wacht tot deze is geïmplementeerd en klik op de knop **Naar de resource gaan**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met Custom Vision. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.
* [Power shell versie 6.0 +](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-7.1), of een vergelijk bare opdracht regel toepassing.


## <a name="create-a-new-custom-vision-project"></a>Een nieuw project maken in de Custom Vision-service

U gebruikt een opdracht als de volgende voor het maken van een afbeeldingsclassificatieproject. Het project wordt weergegeven op de [Custom Vision-website](https://customvision.ai/).


:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createproject":::

Kopieer de opdracht naar een teksteditor en maak de volgende wijzigingen:

* Vervang `{subscription key}` door een geldige Face-abonnementssleutel.
* Vervang `{endpoint}` door het eindpunt dat overeenkomt met uw abonnementssleutel.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
* Vervang `{name}` door de naam van uw project.
* Stel eventueel andere URL-parameters in om te configureren welk type model voor uw project moet worden gebruikt. Zie de [CreatProject API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae) (Engelstalig) voor opties.

U ontvangt een JSON-antwoord dat lijkt op het volgende. Sla de `"id"`-waarde van het project op een tijdelijke locatie op.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="add-tags-to-the-project"></a>Label aan het project toevoegen

Gebruik de volgende opdracht om de tags te definiëren waarmee u het model wilt trainen.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createtag":::

* Voeg nogmaals uw eigen sleutel en eindpunt-URL in.
* Vervang `{projectId}` door uw eigen project-id.
* Vervang `{name}` door de naam van de tag die u wilt gebruiken.

Herhaal dit proces voor alle tags die u in uw project wilt gebruiken. Als u de voorbeeldafbeeldingen gebruikt, voegt u de tags `"Hemlock"` en `"Japanese Cherry"` toe.

U ontvangt een JSON-antwoord dat lijkt op het volgende. Sla de `"id"`-waarde van elke tag op een tijdelijke locatie op.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "type": "Regular",
  "imageCount": 0
}
```

## <a name="upload-and-tag-images"></a>Afbeeldingen uploaden en labelen

Download vervolgens de voorbeeldafbeeldingen voor dit project. Sla de inhoud van de [map Voorbeeldafbeeldingen](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) op uw lokale apparaat op.

> [!NOTE]
> Hebt u een grotere set installatie kopieën nodig om uw training te volt ooien? Met Trove, een Microsoft Garage-project, kunt u sets afbeeldingen verzamelen en aanschaffen voor trainingsdoeleinden. Wanneer u uw afbeeldingen hebt verzameld kunt u ze downloaden en importeren naar uw Custom Vision-project. Ga naar de pagina [Trove](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) voor meer informatie.

Gebruik de volgende opdracht om de afbeeldingen te uploaden en tags toe te passen; eenmaal voor de Hemlock-afbeeldingen en afzonderlijk voor de Japanese Cherry-afbeeldingen. Zie de API [Create Images From Data](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb5) (Afbeeldingen van gegevens maken) voor meer opties.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="uploadimages":::

* Voeg nogmaals uw eigen sleutel en eindpunt-URL in.
* Vervang `{projectId}` door uw eigen project-id.
* Vervang `{tagArray}` door de id van een tag.
* Vul vervolgens de hoofdtekst van de aanvraag in met de binaire gegevens van de afbeeldingen die u wilt taggen.

## <a name="train-the-project"></a>Het project trainen

Met deze methode wordt het model getraind met de getagde afbeeldingen die u hebt geüpload en wordt er een id voor de huidige projectiteratie geretourneerd.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="trainproject":::

* Voeg nogmaals uw eigen sleutel en eindpunt-URL in.
* Vervang `{projectId}` door uw eigen project-id.
* Vervang `{tagArray}` door de id van een tag.
* Vul vervolgens de hoofdtekst van de aanvraag in met de binaire gegevens van de afbeeldingen die u wilt taggen.
* Gebruik eventueel andere URL-parameters. Zie API [Train Project](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1) (Project trainen) voor options.

> [!TIP]
> Trainen met geselecteerde tags
>
> Indien gewenst kun u alleen trainen op een subset van de toegepaste tags. U kunt dit doen als u bepaalde tags nog niet vaak genoeg hebt toegepast, maar u wel voldoende andere codes hebt toegepast. Voeg de optionele JSON-inhoud toe aan de hoofdtekst van uw aanvraag. Vul de `"selectedTags"`-matrix met de id's van de tags die u wilt gebruiken.
> ```json
> {
>   "selectedTags": [
>     "00000000-0000-0000-0000-000000000000"
>   ]
> }
> ```

Het JSON-antwoord bevat informatie over uw getrainde project, waaronder de iteratie-id (`"id"`). Sla deze waarde op voor de volgende stap.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "status": "string",
  "created": "string",
  "lastModified": "string",
  "trainedAt": "string",
  "projectId": "00000000-0000-0000-0000-000000000000",
  "exportable": true,
  "exportableTo": [
    "CoreML"
  ],
  "domainId": "00000000-0000-0000-0000-000000000000",
  "classificationType": "Multiclass",
  "trainingType": "Regular",
  "reservedBudgetInHours": 0,
  "trainingTimeInMinutes": 0,
  "publishName": "string",
  "originalPublishResourceId": "string"
}
```

## <a name="publish-the-current-iteration"></a>De huidige herhaling publiceren

Met deze methode wordt de huidige iteratie van het model beschikbaar voor het uitvoeren van query's. U gebruikt de naam van het model als referentie voor het verzenden van voorspellingsaanvragen. 

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* Voeg nogmaals uw eigen sleutel en eindpunt-URL in.
* Vervang `{projectId}` door uw eigen project-id.
* Vervang `{iterationId}` door de id die in de vorige stap is geretourneerd.
* Vervang `{publishedName}` door de naam die u wilt toewijzen aan uw voorspellingsmodel.
* Vervang `{predictionId}` door de id van uw eigen voorspellingsresource. U kunt deze vinden op het tabblad **Overzicht** van de voorspellingsresource in Azure Portal, vermeld als **Abonnements-id**.
* Gebruik eventueel andere URL-parameters. Zie de API [Publish Iteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fdded5) (Iteratie publiceren).

## <a name="test-the-prediction-endpoint"></a>Voorspellingseindpunt testen

Gebruik deze opdracht ten slotte om uw getrainde model te testen door een nieuwe afbeelding te uploaden zodat deze met tags kan worden geclassificeerd. U kunt de afbeelding gebruiken in de map Test met de voorbeeldbestanden die u eerder hebt gedownload.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* Voeg nogmaals uw eigen sleutel en eindpunt-URL in.
* Vervang `{projectId}` door uw eigen project-id.
* Vervang `{publishedName}` door de naam die u in de vorige stap hebt gebruikt.
* Voeg de binaire gegevens van uw lokale afbeelding opnieuw toe aan de hoofdtekst van de aanvraag.
* Gebruik eventueel andere URL-parameters. Zie de API [Classify image](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.1/operations/5eb37d24548b571998fde5f3) (Afbeelding classificeren).

Het geretourneerde JSON-antwoord vermeld alle tags die in door model op uw afbeelding zijn toegepast, samen met de waarschijnlijkheidsscores voor elke tag. 

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "project": "00000000-0000-0000-0000-000000000000",
  "iteration": "00000000-0000-0000-0000-000000000000",
  "created": "string",
  "predictions": [
    {
      "probability": 0.0,
      "tagId": "00000000-0000-0000-0000-000000000000",
      "tagName": "string",
      "boundingBox": {
        "left": 0.0,
        "top": 0.0,
        "width": 0.0,
        "height": 0.0
      },
      "tagType": "Regular"
    }
  ]
}
```

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt nu elke stap van het proces voor afbeeldingsclassificatie uitgevoerd met behulp van de REST API. Met dit voorbeeld wordt één trainingsiteratie uitgevoerd, maar vaak zult u uw model meerdere keren willen trainen en testen om het nauwkeuriger te maken.

> [!div class="nextstepaction"]
> [Een model testen en opnieuw trainen](../../test-your-model.md)

* Wat is Custom Vision?
* [Naslagdocumentatie over API's (training)](/dotnet/api/overview/azure/cognitiveservices/client/customvision)
* [Naslagdocumentatie over API's (voorspelling)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae)
