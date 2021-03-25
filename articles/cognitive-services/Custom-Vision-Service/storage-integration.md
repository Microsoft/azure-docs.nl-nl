---
title: Azure Storage integreren voor meldingen en model back-ups
titleSuffix: Azure Cognitive Services
description: Meer informatie over het integreren van Azure-opslag voor het ontvangen van push meldingen wanneer u Custom Vision modellen traint of exporteert. U kunt ook een back-up van geëxporteerde modellen opslaan.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: fd4ad1443f466a78abfc569d5f52f6bdeff2d5be
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048881"
---
# <a name="integrate-azure-storage-for-notifications-and-backup"></a>Azure Storage integreren voor meldingen en back-ups

U kunt uw Custom Vision-project integreren met een Azure Blob-opslagwachtrij om pushmeldingen te ontvangen over projecttrainings-/exportactiviteiten en back-ups van gepubliceerde modellen. Deze functie is handig om te voorkomen dat de service voortdurend wordt gecontroleerd op resultaten wanneer lange bewerkingen worden uitgevoerd. In plaats daarvan kunt u de meldingen van de opslagwachtrij in uw werkstroom integreren.

Deze hand leiding laat zien hoe u deze REST-Api's met krul kunt gebruiken. U kunt ook een HTTP-aanvraag service zoals postman gebruiken om de aanvragen uit te geven.

> [!NOTE]
> Push meldingen zijn afhankelijk van de optionele _notificationQueueUri_ -para meter in de **CreateProject** -API. voor model back-ups moet u ook de optionele para meter _exportModelContainerUri_ gebruiken. In deze hand leiding worden beide voor de volledige set met functies gebruikt.

## <a name="prerequisites"></a>Vereisten

- Een Custom Vision resource in Azure. Als u er nog geen hebt, gaat u naar de Azure Portal en [maakt u een nieuwe Custom Vision resource](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true). Deze functie biedt momenteel geen ondersteuning voor de cognitieve service resource (allemaal in één sleutel).
- Een Azure Storage-account met een BLOB-container. Volg [Oefening 1 van de Azure Storage Lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) als u hulp nodig hebt bij deze stap.
* [Power shell versie 6.0 +](/powershell/scripting/install/installing-powershell-core-on-windows), of een vergelijk bare opdracht regel toepassing.

## <a name="set-up-azure-storage-integration"></a>Azure Storage-integratie instellen

Ga naar uw Custom Vision training-resource op de Azure Portal, selecteer de pagina **identiteit** en schakel door systeem toegewezen beheerde identiteiten in.

Ga vervolgens naar uw opslag resource in de Azure Portal. Ga naar de pagina **toegangs beheer (IAM)** en voeg een roltoewijzing toe voor elke integratie functie:
* Selecteer uw Custom Vision trainings resource en wijs de rol van **BLOB-gegevens voor opslag** toe als u van plan bent de functie voor model back-ups te gebruiken. 
* Selecteer vervolgens uw Custom Vision trainings resource en wijs de Inzender voor de gegevens van de **opslag wachtrij** toe als u van plan bent de functie voor meldingen wachtrij te gebruiken.

> [!div class="mx-imgBorder"]
> ![Toewijzings pagina voor het toevoegen van een opslag account](./media/storage-integration/storage-access.png)

### <a name="get-integration-urls"></a>Url's voor integratie ophalen

Vervolgens krijgt u de Url's die uw Custom Vision-resource in staat stellen om toegang te krijgen tot deze eind punten.

Ga naar de pagina **wacht rijen** van uw opslag account, voeg een nieuwe wachtrij toe en sla de URL op een tijdelijke locatie op voor de integratie-URL van de meldingen wachtrij.

> [!div class="mx-imgBorder"]
> ![Pagina Azure Storage-wachtrij](./media/storage-integration/queue-url.png) 

Ga voor de URL van de model back-upintegratie naar de pagina **containers** van uw opslag account en maak een nieuwe container. Vervolgens selecteert u het item en gaat u naar de pagina **Eigenschappen** . Kopieer de URL naar een tijdelijke locatie.
 
> [!div class="mx-imgBorder"]
> ![Eigenschappen pagina Azure-opslag container](./media/storage-integration/container-url.png) 


## <a name="integrate-custom-vision-project"></a>Custom Vision project integreren

Nu u de integratie-Url's hebt, kunt u een nieuw Custom Vision project maken dat de Azure Storage-functies integreert. U kunt ook een bestaand project bijwerken om de functies toe te voegen.

### <a name="create-new-project"></a>Nieuw project maken

Wanneer u de [CreateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae) -API aanroept, voegt u de optionele para meters _exportModelContainerUri_ en _notificationQueueUri_ toe. Wijs de URL-waarden toe die u in de vorige sectie hebt ontvangen. 

```curl
curl -v -X POST "{endpoint}/customvision/v3.3/Training/projects?exportModelContainerUri={inputUri}&notificationQueueUri={inputUri}&name={inputName}"
-H "Training-key: {subscription key}"
```

Als u een `200/OK` antwoord ontvangt, betekent dit dat de url's zijn ingesteld. U ziet nu ook uw URL-waarden in het JSON-antwoord:

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
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

### <a name="update-existing-project"></a>Bestaand project bijwerken

Als u een bestaand project wilt bijwerken met de functie integratie van Azure Storage, roept u de [UpdateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb1) -API aan met de id van het project dat u wilt bijwerken. 

```curl
curl -v -X PATCH "{endpoint}/customvision/v3.3/Training/projects/{projectId}"
-H "Content-Type: application/json"
-H "Training-key: {subscription key}"

--data-ascii "{body}" 
```

Stel de hoofd tekst van de aanvraag ( `body` ) in op de volgende JSON-indeling en vul de juiste waarden in voor _ExportModelContainerUri_ en _notificationQueueUri_:

```json
{
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}

  },
  "status": "Succeeded"
}
```

Als u een `200/OK` antwoord ontvangt, betekent dit dat de url's zijn ingesteld.

## <a name="verify-the-connection"></a>De verbinding controleren 

De API-aanroep in de vorige sectie moet al nieuwe informatie hebben geactiveerd in uw Azure-opslag account. 

In de aangewezen container moet er een test-Blob in een **CustomVision-TestPermission-** map staan. Deze blob is alleen tijdelijk aanwezig.

In uw meldings wachtrij ziet u een test melding met de volgende indeling:
 
```json
{
"version": "1.0" ,
"type": "ConnectionTest",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="get-event-notifications"></a>Gebeurtenis meldingen ophalen

Wanneer u klaar bent, kunt u de [TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1) -API voor uw project aanroepen om een normale trainings bewerking uit te voeren.

In uw wachtrij voor opslag meldingen ontvangt u een melding wanneer de training is voltooid:

```json
{
"version": "1.0" ,
"type": "Training",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "trainingStatus": "TrainingCompleted"
    }
}
```

Het `"trainingStatus"` veld kan ofwel `"TrainingCompleted"` of zijn `"TrainingFailed"` . Het `"iterationId"` veld is de id van het getrainde model.

## <a name="get-model-export-backups"></a>Back-ups voor model exporteren ophalen

Wanneer u klaar bent, roept u de [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddece) -API aan om een getraind model te exporteren naar een opgegeven platform.

In de aangewezen opslag container wordt een back-upkopie van het geëxporteerde model weer gegeven. De naam van de BLOB heeft de volgende indeling:

```
{projectId} - {iterationId}.{platformType}
```

U ontvangt ook een melding in uw wachtrij wanneer de export is voltooid. 

```json
{
"version": "1.0" ,
"type": "Export",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "exportStatus": "ExportCompleted",
    "modelUri": {url}
    }
}
```

Het `"exportStatus"` veld kan ofwel `"ExportCompleted"` of zijn `"ExportFailed"` . Het `"modelUri"` veld bevat de URL van het back-upmodel dat is opgeslagen in uw container. Hierbij wordt ervan uitgegaan dat u de wachtrij meldingen in het begin hebt geïntegreerd. Als dat niet het geval `"modelUri"` is, wordt in het veld de SAS-URL weer gegeven voor uw Custom Vision model-blob.

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding hebt u geleerd hoe u een project tussen Custom Vision resources kopieert en verplaatst. Bekijk vervolgens de naslag documenten voor de API om te zien wat u nog meer kunt doen met Custom Vision.
* [Referentie documentatie voor REST API (training)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)
* [Referentie documentatie voor REST API (voor spelling)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.1/operations/5eb37d24548b571998fde5f3)