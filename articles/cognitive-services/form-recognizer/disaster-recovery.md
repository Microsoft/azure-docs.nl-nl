---
title: Richt lijnen voor herstel na nood gevallen voor Azure Form Recognizer
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van de copy model-API om een back-up te maken van uw formulieren Recognizer-resources.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: b5eb776a7807f48ae6c1a0e3c5879da1f6823830
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103466912"
---
# <a name="back-up-and-recover-your-form-recognizer-models"></a>Back-ups van uw formulieren Recognizer-modellen maken en herstellen

Wanneer u een resource voor een formulier herkenning maakt in de Azure Portal, geeft u een regio op. Daarna blijven uw resource en alle bijbehorende bewerkingen gekoppeld aan de desbetreffende Azure server-regio. Het is zeldzaam, maar niet onmogelijk, om een netwerk probleem op te lossen dat een hele regio aantreft. Als uw oplossing altijd beschikbaar moet zijn, moet u deze zo ontwerpen dat failover wordt uitgevoerd naar een andere regio of de werk belasting tussen twee of meer regio's kan worden gesplitst. Beide benaderingen vereisen ten minste twee formulier herkennings resources in verschillende regio's en de mogelijkheid om aangepaste modellen te synchroniseren tussen regio's.

Met de copy API wordt dit scenario ingeschakeld, zodat u aangepaste modellen kunt kopiëren van één formulier Recognizer-account of naar anderen, dat in elke ondersteunde geografische regio kan voor komen. Deze hand leiding laat zien hoe u de Kopieer REST API met krul kunt gebruiken. U kunt ook een HTTP-aanvraag service zoals postman gebruiken om de aanvragen uit te geven.

## <a name="business-scenarios"></a>Bedrijfs scenario's

Als uw app of bedrijf afhankelijk is van het gebruik van een aangepast model voor formulier herkenning, raden we u aan uw model te kopiëren naar een ander account voor formulier herkenning in een andere regio. Als er een regionale storing optreedt, kunt u vervolgens toegang krijgen tot uw model in de regio waar het is gekopieerd.

##  <a name="prerequisites"></a>Vereisten

1. Twee formulier herkenner Azure-resources in verschillende Azure-regio's. Als u deze niet hebt, gaat u naar de Azure Portal en <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title=" maakt u een nieuwe formulier Recognizer-resource " target="_blank"> een nieuwe formulier Recognizer-resource maken </a> .
1. De abonnements sleutel, eind punt-URL en abonnements-ID van de bron van de formulier herkenning. U kunt deze waarden vinden op het tabblad **overzicht** van de resource op het Azure Portal.


## <a name="copy-api-overview"></a>Overzicht van de API kopiëren

Het proces voor het kopiëren van een aangepast model bestaat uit de volgende stappen:

1. Eerst geeft u een Kopieer autorisatie aanvraag naar de doel bron &mdash; , de resource die het gekopieerde model zal ontvangen. U krijgt een back-up van de URL van het zojuist gemaakte doel model, waarin de gekopieerde gegevens worden ontvangen.
1. Vervolgens verzendt u de Kopieer aanvraag naar de bron bron van &mdash; de resource die het model bevat dat moet worden gekopieerd. U krijgt een URL terug waarmee u een query kunt uitvoeren om de voortgang van de bewerking bij te houden.
1. U gebruikt de referenties van uw bron bron voor het uitvoeren van een query op de voortgangs-URL totdat de bewerking is voltooid. U kunt ook een query uitvoeren op de nieuwe model-ID in de doel bron om de status van het nieuwe model op te halen.

## <a name="generate-copy-authorization-request"></a>Kopieer autorisatie aanvraag genereren

Met de volgende HTTP-aanvraag wordt de Kopieer autorisatie van uw doel bron opgehaald. U moet het eind punt en de sleutel van uw doel resource als kopteksten invoeren.

```
POST https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

U ontvangt een `201\Created` reactie met een `modelId` waarde in de hoofd tekst. Deze teken reeks is de ID van het zojuist gemaakte (lege) model. De `accessToken` is nodig voor de API om gegevens naar deze bron te kopiëren en de `expirationDateTimeTicks` waarde is de verval datum van het token. Sla alle drie deze waarden op een veilige locatie op.

```
HTTP/1.1 201 Created
Location: https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d
{"modelId":"<your model ID>","accessToken":"<your access token>","expirationDateTimeTicks":637233481531659440}
```

## <a name="start-copy-operation"></a>Kopieer bewerking starten

Met de volgende HTTP-aanvraag wordt de Kopieer bewerking gestart op de bron bron. U moet het eind punt en de sleutel van uw bron resource opgeven als koptekst. U ziet dat de aanvraag-URL de model-ID bevat van het bron model dat u wilt kopiëren.

```
POST https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/<your model ID>/copy HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

De hoofd tekst van uw aanvraag moet de volgende indeling hebben. U moet de resource-ID en de regio naam van de doel resource invoeren. U kunt de resource-ID vinden op het tabblad **Eigenschappen** van uw resource in het Azure Portal en u kunt de regio naam vinden op het tabblad **sleutels en eind punt** . U hebt ook de model-ID, het toegangs token en de verloop waarde nodig die u uit de vorige stap hebt gekopieerd.

```json
{
   "targetResourceId": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}",  
   "targetResourceRegion": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}",
   "copyAuthorization": {"modelId":"<your model ID>","accessToken":"<your access token>","expirationDateTimeTicks":637233481531659440}
}
```

> [!NOTE]
> De copy-API ondersteunt de functie [AEK/CMK](https://msazure.visualstudio.com/Cognitive%20Services/_wiki/wikis/Cognitive%20Services.wiki/52146/Customer-Managed-Keys) op transparante wijze. Hiervoor is geen speciale behandeling vereist, maar houd er rekening mee dat als u tussen een niet-versleutelde resource naar een versleutelde bron kopieert, de aanvraag header moet worden toegevoegd `x-ms-forms-copy-degrade: true` . Als deze header niet is opgenomen, mislukt de Kopieer bewerking en wordt er een geretourneerd `DataProtectionTransformServiceError` .

U ontvangt een `202\Accepted` antwoord met een Operation-Location-header. Deze waarde is de URL die u gebruikt om de voortgang van de bewerking bij te houden. Kopieer het naar een tijdelijke locatie voor de volgende stap.

```
HTTP/1.1 202 Accepted
Operation-Location: https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1
```

### <a name="common-errors"></a>Algemene fouten

|Fout|Oplossing|
|:--|:--|
| 400/ongeldige aanvraag met `"code:" "1002"` | Geeft een validatie fout of een onjuist gevormde Kopieer aanvraag aan. Veelvoorkomende problemen zijn onder andere: a) ongeldige of gewijzigde `copyAuthorization` nettolading. b) verlopen waarde voor `expirationDateTimeTicks` token ( `copyAuhtorization` Payload is 24 uur geldig). c) ongeldig of wordt niet ondersteund `targetResourceRegion` . d) ongeldige of verkeerd ingedeelde `targetResourceId` teken reeks.
|

## <a name="track-copy-progress"></a>Voortgang van kopiëren bijhouden

Volg de voortgang van de query op de resulterende API voor het **kopiëren van modellen ophalen** op basis van het bron-eind punt.

```
GET https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1 HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

Uw reactie is afhankelijk van de status van de bewerking. Zoek het `"status"` veld in de JSON-hoofd tekst. Als u deze API-aanroep in een script automatiseert, raden we u aan de bewerking eenmaal per seconde op te vragen.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"status":"succeeded","createdDateTime":"2020-04-23T18:18:01.0275043Z","lastUpdatedDateTime":"2020-04-23T18:18:01.0275048Z","copyResult":{}}
```

### <a name="common-errors"></a>Algemene fouten

|Fout|Oplossing|
|:--|:--|
|"fouten": [{"code": "AuthorizationError",<br>"bericht": "autorisatie fout vanwege <br>ontbrekende of ongeldige autorisatie claims. "}]   | Deze gebeurtenis treedt op wanneer de `copyAuthorization` Payload of inhoud wordt gewijzigd vanuit wat is geretourneerd door de `copyAuthorization` API. Zorg ervoor dat de payload hetzelfde is als de exacte inhoud die is geretourneerd door de eerdere `copyAuthorization` aanroep.|
|"fouten": [{"code": "AuthorizationError",<br>bericht ': ' kan autorisatie niet ophalen <br>metagegevensarchiefmethode. Als dit probleem zich blijft voordoen, gebruikt u een andere <br>doel model waarnaar moet worden gekopieerd. "}] | Geeft aan dat de `copyAuthorization` Payload opnieuw wordt gebruikt met een Kopieer aanvraag. Een Kopieer aanvraag die slaagt, staat geen verdere aanvragen toe die gebruikmaken van dezelfde `copyAuthorization` payload. Als u een afzonderlijke fout verhoogt (zoals de hieronder vermelde) en u de kopie vervolgens opnieuw probeert met dezelfde autorisatie lading, wordt deze fout gegenereerd. De oplossing is een nieuwe Payload genereren `copyAuthorization` en vervolgens de Kopieer aanvraag opnieuw uitgeven.|
|"fouten": [{"code": "DataProtectionTransformServiceError",<br>"bericht": "aanvraag voor gegevens overdracht is niet toegestaan <br>zoals het downgradet naar een minder veilig Data Protection-schema. Raadpleeg de documentatie of neem contact op met de service beheerder <br>voor meer informatie. "}]    | Deze gebeurtenis treedt op bij het kopiëren tussen een `AEK` ingeschakelde resource naar een niet- `AEK` ingeschakelde resource. Om het kopiëren van versleutelde modellen naar het doel als niet-versleutelde header te kunnen opgeven `x-ms-forms-copy-degrade: true` met de Kopieer aanvraag.|
|"fouten": [{"code": "ResourceResolverError",<br>' bericht ': ' kan geen informatie ophalen voor de cognitieve resource met de id '... '. Controleer of de resource geldig is en bestaat in de opgegeven regio ' westus2 '.. "}] | Geeft aan dat de Azure-resource die wordt aangegeven door de `targetResourceId` geen geldige cognitieve resource is of niet bestaat. Controleer de Kopieer aanvraag en geef deze opnieuw op om dit probleem op te lossen.|


### <a name="optional-track-the-target-model-id"></a>Beschrijving De doel model-ID bijhouden 

U kunt ook de API voor **aangepast model ophalen** gebruiken om de status van de bewerking bij te houden door een query uit te geven op het doel model. Roep deze API aan met de doel model-ID die u in de eerste stap hebt gekopieerd.

```
GET https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

In de hoofd tekst van het antwoord wordt informatie over het model weer geven. Controleer het `"status"` veld voor de status van het model.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"modelInfo":{"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","status":"ready","createdDateTime":"2020-02-26T16:59:28Z","lastUpdatedDateTime":"2020-02-26T16:59:34Z"},"trainResult":{"trainingDocuments":[{"documentName":"0.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"1.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"2.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"3.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"4.pdf","pages":1,"errors":[],"status":"succeeded"}],"errors":[]}}
```

## <a name="curl-sample-code"></a>Krul voorbeeld code

De volgende code fragmenten gebruiken krul om ervoor te zorgen dat de API-aanroepen in de bovenstaande stappen worden beschreven. U moet nog steeds de model-Id's en abonnements gegevens invullen die specifiek zijn voor uw eigen resources.

### <a name="generate-copy-authorization-request"></a>Kopieer autorisatie aanvraag genereren

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" 
```

### <a name="start-copy-operation"></a>Kopieer bewerking starten

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0/custom/models/copyAuthorization" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" --data-ascii "{ \"targetResourceId\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}\",   \"targetResourceRegion\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}\", \"copyAuthorization\": "{\"modelId\":\"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d\",\"accessToken\":\"1855fe23-5ffc-427b-aab2-e5196641502f\",\"expirationDateTimeTicks\":637233481531659440}"}"
```

### <a name="track-copy-progress"></a>Voortgang van kopiëren bijhouden

```bash
curl -i GET "https://<SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT>/formrecognizer/v2.0/custom/models/{SOURCE_MODELID}/copyResults/{RESULT_ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}"
```

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding hebt u geleerd hoe u de copy API gebruikt om een back-up te maken van uw aangepaste modellen naar een secundaire resource voor de herkenner van het formulier. Bekijk vervolgens de naslag documenten voor de API om te zien wat u nog meer kunt doen met de formulier herkenner.
* [REST API-referentiedocumentatie](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)
