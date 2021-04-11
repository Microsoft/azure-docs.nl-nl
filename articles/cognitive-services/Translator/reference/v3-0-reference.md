---
title: Naslag informatie over Translator V 3.0
titleSuffix: Azure Cognitive Services
description: Referentie documentatie voor de Translator V 3.0. Versie 3 van het conversie programma biedt een moderne op JSON gebaseerde web-API.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 8/11/2020
ms.author: lajanuar
ms.openlocfilehash: 2b391c5a435c2dd2f19a3f170bf7c84edd7143f2
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063029"
---
# <a name="translator-v30"></a>Translator v 3.0

## <a name="whats-new"></a>Wat is er nieuw?

Versie 3 van het conversie programma biedt een moderne op JSON gebaseerde web-API. Het verbetert de bruikbaarheid en prestaties door bestaande functies te consolideren in minder bewerkingen en biedt nieuwe functies.

 * Vele voor het converteren van tekst in één taal van het ene script naar een ander script.
 * Vertaling naar meerdere talen in één aanvraag.
 * Taal detectie, vertaling en vele in één aanvraag.
 * Woorden boek om alternatieve vertalingen van een termijn op te zoeken, om back-vertalingen en voor beelden te vinden met de termen die in de context worden gebruikt.
 * Meer informatieve resultaten voor de detectie van talen.

## <a name="base-urls"></a>Basis-Url's

Micro soft Translator wordt uit meerdere datacenter locaties verzonden. Ze bevinden zich momenteel in de volgende tien [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions):

* **Amerikaans continent:** VS-Oost, Zuid-Centraal VS, West-Centraal VS en VS-West 2 
* **Azië en Stille Oceaan:** Korea-zuid, Japan-Oost, Zuidoost-Azië en Australië-oost
* **Europa:** Europa-noord, Europa-west

Aanvragen voor de micro soft Translator zijn in de meeste gevallen verwerkt door het Data Center dat zich het dichtst bij de oorspronkelijke bestemming bevindt. In het geval van een storing in een Data Center kan de aanvraag buiten de regio van Azure worden gerouteerd.

Als u wilt afdwingen dat de aanvraag wordt verwerkt door een specifieke Azure-geografie, wijzigt u het globale eind punt in de API-aanvraag naar het gewenste geografische eind punt:

|Description|Azure-Geografie|Basis-URL (geografisch eind punt)|
|:--|:--|:--|
|Azure|Algemeen (niet-regionaal)|   api.cognitive.microsofttranslator.com|
|Azure|Verenigde Staten|   api-nam.cognitive.microsofttranslator.com|
|Azure|Europa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Azië en Stille Oceaan|    api-apc.cognitive.microsofttranslator.com|

<sup>1</sup> klanten met een resource in Zwitserland-noord of Zwitserland-West kunnen ervoor zorgen dat de tekst-API-aanvragen worden aangeboden in Zwitser land. Om ervoor te zorgen dat aanvragen worden verwerkt in Zwitser land, maakt u de Vertaal resource in de resource regio ' ' Zwitserland-noord ' of ' Zwitserland-west ' en gebruikt u vervolgens het aangepaste eind punt van de resource in uw API-aanvragen. Bijvoorbeeld: als u een resource Translator in Azure Portal met ' resource Region ' als ' Zwitserland-noord ' maakt en uw resource naam ' My-CH-n ' is, is het aangepaste eind punt ' https://my-ch-n.cognitiveservices.azure.com '. En een voorbeeld aanvraag om te zetten is:
```curl
// Pass secret key and region using headers to a custom endpoint
curl -X POST " my-ch-n.cognitiveservices.azure.com/translator/text/v3.0/translate?to=fr" \
-H "Ocp-Apim-Subscription-Key: xxx" \
-H "Ocp-Apim-Subscription-Region: switzerlandnorth" \
-H "Content-Type: application/json" \
-d "[{'Text':'Hello'}]" -v
```
<sup>2</sup> aangepaste vertalers zijn momenteel niet beschikbaar in Zwitser land.

## <a name="authentication"></a>Verificatie

Abonneer u op Translator of [Cognitive Services meerdere services](https://azure.microsoft.com/pricing/details/cognitive-services/) in azure Cognitive Services en gebruik uw abonnements sleutel (beschikbaar in de Azure Portal) om u te verifiëren. 

Er zijn drie kopteksten die u kunt gebruiken om uw abonnement te verifiëren. In deze tabel wordt beschreven hoe elke wordt gebruikt:

|Kopteksten|Beschrijving|
|:----|:----|
|Ocp-Apim-Subscription-Key|*Gebruik with Cognitive Services-abonnement als u uw geheime sleutel door geven*.<br/>De waarde is de geheime Azure-sleutel voor uw abonnement op Translator.|
|Autorisatie|*Gebruik with Cognitive Services-abonnement als u een verificatie token doorgeeft.*<br/>De waarde is de Bearer-token: `Bearer <token>` .|
|OCP-APIM-abonnement-regio|*Gebruiken met Cognitive Services multi-service en regionale Translator-resource.*<br/>De waarde is de regio van de resource voor meerdere services of regionaal Translator. Deze waarde is optioneel bij het gebruik van een Global Translator-resource.|

###  <a name="secret-key"></a>Geheime sleutel
De eerste optie is om te verifiëren met behulp van de `Ocp-Apim-Subscription-Key` header. Voeg de `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` koptekst aan uw aanvraag toe.

#### <a name="authenticating-with-a-global-resource"></a>Verifiëren met een globale resource

Wanneer u een [Global Translator-resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)gebruikt, moet u één header opnemen om de vertaler aan te roepen.

|Kopteksten|Beschrijving|
|:-----|:----|
|Ocp-Apim-Subscription-Key| De waarde is de geheime Azure-sleutel voor uw abonnement op Translator.|

Hier volgt een voorbeeld aanvraag voor het aanroepen van het conversie programma met de Global Translator-resource

```curl
// Pass secret key using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-regional-resource"></a>Verifiëren met een regionale resource

Wanneer u een [regionale Translator-resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)gebruikt.
Er zijn twee kopteksten die u nodig hebt om het conversie programma aan te roepen.

|Kopteksten|Beschrijving|
|:-----|:----|
|Ocp-Apim-Subscription-Key| De waarde is de geheime Azure-sleutel voor uw abonnement op Translator.|
|OCP-APIM-abonnement-regio| De waarde is de regio van de Translator-resource. |

Hier volgt een voorbeeld aanvraag voor het aanroepen van het conversie programma met de regionale Translator-resource

```curl
// Pass secret key and region using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-multi-service-resource"></a>Verificatie met een resource van meerdere services

Wanneer u de resource met meerdere services van een cognitieve service gebruikt. Zo kunt u een enkele geheime sleutel gebruiken om aanvragen voor meerdere services te verifiëren. 

Wanneer u een geheime sleutel van meerdere services gebruikt, moet u twee verificatie headers met uw aanvraag toevoegen. Er zijn twee kopteksten die u nodig hebt om het conversie programma aan te roepen.

|Kopteksten|Beschrijving|
|:-----|:----|
|Ocp-Apim-Subscription-Key| De waarde is de geheime Azure-sleutel voor uw resource met meerdere services.|
|OCP-APIM-abonnement-regio| De waarde is de regio van de resource met meerdere services. |

De regio is vereist voor het multi-service-tekst-API-abonnement. De regio die u selecteert, is de enige regio die u voor tekst omzetting kunt gebruiken wanneer u de sleutel voor meerdere services gebruikt en moet dezelfde regio zijn die u hebt geselecteerd toen u zich registreerde voor uw abonnement op meerdere services via de Azure Portal.

Beschik bare regio's zijn `australiaeast` , `brazilsouth` ,, `canadacentral` `centralindia` , `centralus` , `centraluseuap` ,, `eastasia` , `eastus` `eastus2` `francecentral` `japaneast` `japanwest` `koreacentral` `northcentralus` `northeurope` `southcentralus` `southeastasia` `uksouth` `westcentralus` `westeurope` `westus` `westus2` `southafricanorth` ,,,,,,,,,,,,, en.

Als u de geheime sleutel in de query teken reeks doorgeeft met de para meter `Subscription-Key` , moet u de regio met de query parameter opgeven `Subscription-Region` .

### <a name="authenticating-with-an-access-token"></a>Verifiëren met een toegangs token
U kunt ook uw geheime sleutel voor een toegangs token uitwisselen. Dit token is opgenomen in elke aanvraag als de `Authorization` header. Als u een autorisatie token wilt verkrijgen, moet u een `POST` aanvraag indienen bij de volgende URL:

| Resourcetype     | URL van verificatie service                                |
|-----------------|-----------------------------------------------------------|
| Globaal          | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |
| Regionaal of meerdere services | `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Hier vindt u voor beelden van aanvragen voor het verkrijgen van een token aan de hand van een geheime sleutel:

```curl
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Een succes volle aanvraag retourneert het gecodeerde toegangs token als tekst zonder opmaak in de hoofd tekst van het antwoord. Het geldige token wordt door gegeven aan de Translator-service als Bearer-token in de autorisatie.

```http
Authorization: Bearer <Base64-access_token>
```

Een verificatie token is 10 minuten geldig. Het token moet opnieuw worden gebruikt bij het maken van meerdere aanroepen naar het conversie programma. Als uw programma echter gedurende lange tijd aanvragen voor het Translator maakt, moet uw programma regel matig een nieuw toegangs token aanvragen (bijvoorbeeld om de 8 minuten).

## <a name="virtual-network-support"></a>Ondersteuning voor virtuele netwerken

De Translator-service is nu beschikbaar met de mogelijkheden van Virtual Network (VNET) in alle regio's van de open bare Azure-Cloud. Als u Virtual Network wilt inschakelen, raadpleegt u [Azure Cognitive Services virtuele netwerken configureren](../../cognitive-services-virtual-networks.md?tabs=portal). 

Wanneer u deze functie inschakelt, moet u het aangepaste eind punt gebruiken om het conversie programma aan te roepen. U kunt het globale Translator-eind punt ("api.cognitive.microsofttranslator.com") niet gebruiken en u kunt zich niet verifiëren met een toegangs token.

U kunt het aangepaste eind punt vinden nadat u een [Translator-resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) hebt gemaakt en toegang hebt toegestaan vanuit geselecteerde netwerken en privé-eind punten.

|Kopteksten|Beschrijving|
|:-----|:----|
|Ocp-Apim-Subscription-Key| De waarde is de geheime Azure-sleutel voor uw abonnement op Translator.|
|OCP-APIM-abonnement-regio| De waarde is de regio van de Translator-resource. Deze waarde is optioneel als de resource is `global`|

Hier volgt een voorbeeld aanvraag voor het aanroepen van het conversie programma met behulp van het aangepaste eind punt

```curl
// Pass secret key and region using headers
curl -X POST "https://<your-custom-domain>.cognitiveservices.azure.com/translator/text/v3.0/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

## <a name="errors"></a>Fouten

Een standaardfout bericht is een JSON-object met een naam/waardepaar met de naam `error` . De waarde is ook een JSON-object met eigenschappen:

  * `code`: Een fout code die door de server is gedefinieerd.
  * `message`: Een teken reeks die een door de mens lees bare weer gave van de fout geeft.

Zo kan een klant met een gratis proef abonnement de volgende fout melding ontvangen wanneer het gratis quotum is uitgeput:

```json
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
De fout code is een getal van 6 cijfers, waarbij de HTTP-status code van 3 cijfers wordt gevolgd door een getal van drie cijfers om de fout verder te categoriseren. Veelvoorkomende fout codes zijn:

| Code | Description |
|:----|:-----|
| 400000| Een van de aanvraag invoer is ongeldig.|
| 400001| De para meter "scope" is ongeldig.|
| 400002| De para meter "Category" is ongeldig.|
| 400003| De taal aanduiding ontbreekt of is ongeldig.|
| 400004| Er ontbreekt een doel script aanduiding (' naar script ') of is ongeldig.|
| 400005| Er ontbreekt een invoer tekst of deze is ongeldig.|
| 400006| De combi natie van taal en script is niet geldig.|
| 400018| Er ontbreekt een bron script aanduiding ("van script") of is ongeldig.|
| 400019| Een van de opgegeven talen wordt niet ondersteund.|
| 400020| Een van de elementen in de matrix met invoer tekst is ongeldig.|
| 400021| De para meter van de API-versie ontbreekt of is ongeldig.|
| 400023| Een van de opgegeven taal paren is niet geldig.|
| 400035| Het bron taal veld (' van ') is niet geldig.|
| 400036| Het doel taal veld ("aan") ontbreekt of is ongeldig.|
| 400042| Een van de opgegeven opties (het veld ' opties ') is niet geldig.|
| 400043| De tracerings-ID van de client (ClientTraceId-veld of X-ClientTranceId-header) ontbreekt of is ongeldig.|
| 400050| De invoer tekst is te lang. [Aanvraag limieten](../request-limits.md)weer geven.|
| 400064| De para meter "Translation" ontbreekt of is ongeldig.|
| 400070| Het aantal doel scripts (ToScript para meter) komt niet overeen met het aantal doel talen (op para meter).|
| 400071| De waarde is niet geldig voor TextType.|
| 400072| De matrix met invoer tekst heeft te veel elementen.|
| 400073| De script parameter is niet geldig.|
| 400074| De hoofd tekst van de aanvraag is geen geldige JSON.|
| 400075| Het taal paar en de combi natie van categorie zijn niet geldig.|
| 400077| De maximale aanvraag grootte is overschreden. [Aanvraag limieten](../request-limits.md)weer geven.|
| 400079| Het aangepaste systeem dat is aangevraagd voor vertaling tussen van en naar taal bestaat niet.|
| 400080| Vele wordt niet ondersteund voor de taal of het script.|
| 401000| De aanvraag is niet geautoriseerd omdat de referenties ontbreken of ongeldig zijn.|
| 401015| "De gegeven referenties gelden voor de spraak-API. Voor deze aanvraag zijn referenties vereist voor de tekst-API. Een abonnement op Translator gebruiken. "|
| 403000| De bewerking is niet toegestaan.|
| 403001| De bewerking is niet toegestaan omdat het abonnement het gratis quotum heeft overschreden.|
| 405000| De aanvraag methode wordt niet ondersteund voor de aangevraagde resource.|
| 408001| Het aangevraagde Vertaal systeem wordt voor bereid. Probeer het over enkele minuten opnieuw.|
| 408002| Time-out bij het wachten op de inkomende stroom. De client heeft geen aanvraag gegenereerd binnen het tijdstip dat de server is voor bereid om te wachten. De client kan de aanvraag op een later tijdstip zonder wijzigingen herhalen.|
| 415000| De content-type-header ontbreekt of is ongeldig.|
| 429000, 429001, 429002| De server heeft de aanvraag geweigerd omdat de aanvraag limieten voor de client is overschreden.|
| 500000| Er is een onverwachte fout opgetreden. Als de fout zich blijft voordoen, meldt u deze met de datum/tijd van de fout, verzoekt u de aanvraag-id van de antwoord header X-id-aanvraag en de client-id van de aanvraag header X-ClientTraceId.|
| 503000| De service is tijdelijk niet beschikbaar. Probeer het opnieuw. Als de fout zich blijft voordoen, meldt u deze met de datum/tijd van de fout, verzoekt u de aanvraag-id van de antwoord header X-id-aanvraag en de client-id van de aanvraag header X-ClientTraceId.|

## <a name="metrics"></a>Metrische gegevens 
Met metrische gegevens kunt u het gebruik van de Vertaler en beschik baarheid in Azure Portal weer geven, onder de sectie metrische gegevens, zoals wordt weer gegeven in de onderstaande scherm afbeelding. Zie [gegevens en platform metrieken](../../../azure-monitor/essentials/data-platform-metrics.md)voor meer informatie.

![Metrische gegevens van vertaler](../media/translatormetrics.png)

Deze tabel bevat de beschik bare metrische gegevens met een beschrijving van hoe ze worden gebruikt voor het bewaken van API-aanroepen voor vertalingen.

| Metrische gegevens | Description |
|:----|:-----|
| TotalCalls| Totaal aantal API-aanroepen.|
| TotalTokenCalls| Totaal aantal API-aanroepen via token service met verificatie token.|
| SuccessfulCalls| Aantal geslaagde aanroepen.|
| TotalErrors| Het aantal aanroepen met een fout reactie.|
| BlockedCalls| Aantal aanroepen dat de frequentie of quotum limiet heeft overschreden.|
| ServerErrors| Aantal aanroepen met server interne fout (5XX).|
| ClientErrors| Aantal aanroepen met een fout aan de client zijde (4XX).|
| Latentie| De duur voor het volt ooien van de aanvraag in milliseconden.|
| CharactersTranslated| Totaal aantal tekens in binnenkomende-tekst aanvraag.|
