---
title: Azure functions-activiteit in Azure Data Factory
description: Meer informatie over het gebruik van de Azure function-activiteit voor het uitvoeren van een Azure-functie in een Data Factory-pijp lijn
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: 13f00907737a99bc5dcd8c21d660ce83aa681908
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783792"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure functions-activiteit in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Met de functie activiteit van Azure kunt u [Azure functions](../azure-functions/functions-overview.md) uitvoeren in een Data Factory-pijp lijn. Als u een Azure-functie wilt uitvoeren, moet u een gekoppelde service verbinding maken en een activiteit opgeven die de Azure-functie specificeert die u wilt uitvoeren.

Bekijk de volgende video voor een inleiding en demonstratie van acht minuten voor deze functie:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Gekoppelde Azure-functie Service

Het retour type van de Azure-functie moet geldig zijn `JObject` . (Houd er wel bij [](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) dat JArray *geen* is `JObject` .) Een ander retour type dan `JObject` mislukt en de inhoud van het fout bericht van de gebruiker wordt gegenereerd, *is geen geldige JObject*.

| **Eigenschap** | **Beschrijving** | **Vereist** |
| --- | --- | --- |
| type   | De eigenschap type moet worden ingesteld op: **AzureFunction** | ja |
| URL van de functie-app | De URL voor de Azure-functie-app. Indeling is `https://<accountname>.azurewebsites.net` . Deze URL is de waarde onder **URL** -gedeelte bij het weer geven van uw functie-app in de Azure Portal  | ja |
| functie toets | Toegangs sleutel voor de Azure-functie. Klik op de sectie **beheren** voor de betreffende functie en kopieer de **functie sleutel** of de host- **sleutel**. Meer informatie vindt u hier: [Azure functions HTTP-triggers en-bindingen](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | ja |
|   |   |   |

## <a name="azure-function-activity"></a>Azure function-activiteit

| **Eigenschap**  | **Beschrijving** | **Toegestane waarden** | **Vereist** |
| --- | --- | --- | --- |
| naam  | Naam van de activiteit in de pijp lijn  | Tekenreeks | ja |
| type  | Type activiteit is ' AzureFunctionActivity ' | Tekenreeks | ja |
| gekoppelde service | De gekoppelde Azure-functie service voor de bijbehorende Azure-functie-app  | Verwijzing naar gekoppelde service | ja |
| functie naam  | De naam van de functie in de Azure-functie-app die deze activiteit aanroept | Tekenreeks | ja |
| method  | REST API methode voor de functie aanroep | Ondersteunde typen teken reeksen: ' ophalen ', ' POST ', ' PUT '   | ja |
| koptekst  | Kopteksten die naar de aanvraag worden verzonden. Als u bijvoorbeeld de taal en het type van een aanvraag wilt instellen: ' headers ': {' Accept-Language ': ' nl-nl ', ' content-type ': ' application/json '} | Teken reeks (of expressie met het resultType van de teken reeks) | No |
| body  | hoofd tekst die samen met de aanvraag wordt verzonden naar de functie-API-methode  | Teken reeks (of expressie met het resultType van de teken reeks) of het object.   | Vereist voor PUT/POST-methoden |
|   |   |   | |

Zie het schema van de sectie aanvraag lading in schema voor de lading van de [aanvraag](control-flow-web-activity.md#request-payload-schema) .

## <a name="routing-and-queries"></a>Route ring en query's

De Azure functions-activiteit ondersteunt **route ring**. Als uw Azure-functie bijvoorbeeld het eind punt heeft  `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>` , is de functie `functionName` voor het gebruik in de Azure functions-activiteit `<functionName>/<value>` . U kunt deze functie para meters om de gewenste runtime te leveren `functionName` .

De Azure function-activiteit biedt ook ondersteuning voor **query's**. Een query moet worden opgenomen als onderdeel van de `functionName` . Als de naam van de functie bijvoorbeeld is `HttpTriggerCSharp` en de query die u wilt toevoegen `name=hello` , kunt u de `functionName` in de Azure function-activiteit als opgeven `HttpTriggerCSharp?name=hello` . Deze functie kan worden ingesteld op de para meter, zodat de waarde tijdens runtime kan worden bepaald.

## <a name="timeout-and-long-running-functions"></a>Time-out en langlopende functies

Azure Functions een time-out na 230 seconden, ongeacht de `functionTimeout` instelling die u in de instellingen hebt geconfigureerd. Raadpleeg [dit artikel](../azure-functions/functions-versions.md#timeout) voor meer informatie. U kunt dit probleem omzeilen door een async-patroon te volgen of door Durable Functions te gebruiken. Het voor deel van Durable Functions is dat ze hun eigen status tracerings mechanisme bieden, zodat u uw eigen functie niet hoeft te implementeren.

Meer informatie over Durable Functions in [dit artikel](../azure-functions/durable/durable-functions-overview.md). U kunt een Azure function-activiteit instellen om de duurzame functie aan te roepen, waardoor een antwoord wordt geretourneerd met een andere URI, zoals [dit voor beeld](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery). Omdat `statusQueryGetUri` de HTTP-Status 202 wordt geretourneerd terwijl de functie wordt uitgevoerd, kunt u de status van de functie navragen met behulp van een webactiviteit. Stel een webactiviteit in waarbij het `url` veld is ingesteld op `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri` . Wanneer de duurzame functie is voltooid, wordt de uitvoer van de functie de uitvoer van de webactiviteit.


## <a name="sample"></a>Voorbeeld

U vindt een voor beeld van een Data Factory waarin een Azure-functie wordt gebruikt om de inhoud van een tar [-bestand te](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction)extra heren.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over activiteiten in Data Factory in [pijp lijnen en activiteiten in azure Data Factory](concepts-pipelines-activities.md).
