---
title: Custom Vision projecten kopiëren en verplaatsen
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van de ExportProject-en ImportProject-Api's om uw Custom Vision-projecten te kopiëren en verplaatsen.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/08/2020
ms.author: pafarley
ms.openlocfilehash: 7d58a8239c728f70efe3584c2649e196dffd791f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501087"
---
# <a name="copy-and-move-your-custom-vision-projects"></a>Uw Custom Vision projecten kopiëren en verplaatsen

Nadat u een Custom Vision project hebt gemaakt en getraind, kunt u uw project naar een andere resource kopiëren. U kunt bijvoorbeeld een project verplaatsen van een ontwikkeling naar een productie omgeving of een back-up maken van een project naar een account in een andere Azure-regio voor betere gegevens beveiliging.

Met de **[ExportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** -en **[ImportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** -api's wordt dit scenario ingeschakeld, zodat u projecten kunt kopiëren van een Custom Vision account naar anderen. Deze hand leiding laat zien hoe u deze REST-Api's met krul kunt gebruiken. U kunt ook een HTTP-aanvraag service zoals postman gebruiken om de aanvragen uit te geven.

## <a name="business-scenarios"></a>Bedrijfs scenario's

Als uw app of bedrijf afhankelijk is van het gebruik van een Custom Vision project, raden we u aan uw model te kopiëren naar een ander Custom Vision-account in een andere regio. Als er een regionale storing optreedt, kunt u het project openen in de regio waar het is gekopieerd.

##  <a name="prerequisites"></a>Vereisten

- Twee Azure Custom Vision-resources. Als u deze niet hebt, gaat u naar de Azure Portal en [maakt u een nieuwe Custom Vision resource](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true).
- De trainings sleutels en eind punt-Url's van uw Custom Vision-resources. U kunt deze waarden vinden op het tabblad **overzicht** van de resource op het Azure Portal.
- Een gemaakt Custom Vision project. Zie [een classificatie bouwen](./getting-started-build-a-classifier.md) voor instructies over hoe u dit doet.
* [Power shell versie 6.0 +](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows), of een soortgelijk opdracht regel programma.

## <a name="process-overview"></a>Overzicht van het proces

Het proces voor het kopiëren van een project bestaat uit de volgende stappen:

1. Eerst krijgt u de ID van het project in uw bron account dat u wilt kopiëren.
1. Vervolgens roept u de **ExportProject** -API aan met de project-id en de trainings sleutel van uw bron account. U krijgt een tijdelijke token teken reeks.
1. Vervolgens roept u de **ImportProject** -API aan met de token teken reeks en de trainings sleutel van uw doel account. Het project wordt vervolgens vermeld onder uw doel account.

## <a name="get-the-project-id"></a>De project-ID ophalen

Roep **[GetProjects](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddead)** aan om een lijst met bestaande Custom Vision projecten en hun id's te bekijken. Gebruik de trainings sleutel en het eind punt van uw bron account.

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects"
-H "Training-key: {training key}"
```

U ontvangt een `200\OK` antwoord met een lijst met projecten en de bijbehorende meta gegevens in de hoofd tekst. De `"id"` waarde is de teken reeks die moet worden gekopieerd voor de volgende stappen.

```json
[
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
]
```

## <a name="export-the-project"></a>Het project exporteren

Roep **[ExportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** aan met de project-id en de trainings sleutel en het eind punt van uw bron.

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects/{projectId}/export"
-H "Training-key: {training key}"
```

U ontvangt een `200/OK` antwoord met meta gegevens over het geëxporteerde project en een verwijzings reeks `"token"` . Kopieer de waarde van het token.

```json
{
  "iterationCount": 0,
  "imageCount": 0,
  "tagCount": 0,
  "regionCount": 0,
  "estimatedImportTimeInMS": 0,
  "token": "string"
}
```

## <a name="import-the-project"></a>Het project importeren

Roep **[ImportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** aan met behulp van uw doel-en eind punt, samen met het referentie token. U kunt het project ook een naam geven in het nieuwe account.

```curl
curl -v -G -X POST "{endpoint}/customvision/v3.3/Training/projects/import"
--data-urlencode "token={token}" --data-urlencode "name={name}"
-H "Training-key: {training key}" -H "Content-Length: 0"
```

U ontvangt een `200/OK` antwoord met meta gegevens over uw zojuist geïmporteerde project.

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

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding hebt u geleerd hoe u een project tussen Custom Vision resources kopieert en verplaatst. Bekijk vervolgens de naslag documenten voor de API om te zien wat u nog meer kunt doen met Custom Vision.
* [REST API-referentiedocumentatie](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)