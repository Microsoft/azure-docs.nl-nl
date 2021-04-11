---
title: Facturen-formulier herkenning
titleSuffix: Azure Cognitive Services
description: Leer concepten met betrekking tot factuur analyse met de formulier Recognizer API-gebruik en limieten.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 46cf34bd40832488985008a645f1da25eb87b9d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467383"
---
# <a name="form-recognizer-prebuilt-invoice-model"></a>Vooraf gebouwd factuur model voor formulier herkenning

De Azure-formulier herkenner kan gegevens uit verkoop facturen analyseren en extra heren met behulp van de vooraf gemaakte factuur modellen. Met de factuur-API kunnen klanten facturen in verschillende indelingen nemen en gestructureerde gegevens retour neren om de factuur verwerking te automatiseren. Het combineert onze krachtige functies voor [optische teken herkenning (OCR)](../computer-vision/concept-recognizing-text.md) met factuur uitgebreide leer modellen voor het extra heren van belang rijke informatie uit facturen in het Engels. Hiermee worden de tekst, tabellen en gegevens, zoals klant, leverancier, factuur-ID, verval datum van factuur, totaal, factuur bedrag, belasting bedrag, verzen ding, factuur en regel items geëxtraheerd. De vooraf gemaakte factuur-API is openbaar beschikbaar in de preview-versie van de formulier Recognizer v 2.1.

## <a name="what-does-the-invoice-service-do"></a>Wat doet de factuur service?

De factuur-API extraheert sleutel velden en regel items van facturen en retourneert deze in een geordend Structured JSON-antwoord. Facturen kunnen uit verschillende indelingen en kwaliteit bestaan, inclusief door de telefoon vastgelegde installatie kopieën, gescande documenten en digitale Pdf's. De factuur-API haalt de gestructureerde uitvoer op uit al deze facturen. 

![Voor beeld van Contoso-factuur](./media/invoice-example-new.jpg)

## <a name="try-it-out"></a>Probeer het eens

Als u de factuur service voor formulier herkenning wilt uitproberen, gaat u naar het hulp programma online voor beeld-UI:

> [!div class="nextstepaction"]
> [Vooraf gebouwde modellen uitproberen](https://fott-preview.azurewebsites.net/)

U hebt een Azure-abonnement nodig ([Maak er een gratis](https://azure.microsoft.com/free/cognitive-services)) en een resource-eind punt en-sleutel van een [formulier herkenning](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) om de formulier Recognizer-factuur service uit te proberen. 

:::image type="content" source="media/analyze-invoice-new.png" alt-text="Voor beeld van analyseerde facturen" lightbox="media/analyze-invoice-new.png":::

### <a name="input-requirements"></a>Vereisten voor invoer

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-invoice-operation"></a>De bewerking factuur analyseren

Met de bewerking voor het [analyseren van facturen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291) wordt een afbeelding of PDF van een factuur als invoer gebruikt en worden de waarden van de rente opgehaald. De aanroep retourneert een veld met een antwoord header met de naam `Operation-Location` . De `Operation-Location` waarde is een URL die de resultaat-id bevat die in de volgende stap moet worden gebruikt.

|Reactie header| Resultaten-URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-invoice-result-operation"></a>De resultaat bewerking analyse van factuur ophalen

De tweede stap bestaat uit het aanroepen van de bewerking [analyse van factuur resultaat ophalen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9acb78c40a2533aee83) . Met deze bewerking wordt de resultaat-ID gebruikt die is gemaakt door de bewerking voor het analyseren van een factuur. Er wordt een JSON-antwoord geretourneerd dat een **status** veld met de volgende mogelijke waarden bevat. U roept deze bewerking iteratief aan tot deze met de **geslaagde** waarde wordt geretourneerd. Gebruik een interval van 3 tot 5 seconden om te voor komen dat het aantal aanvragen per seconde (RPS) wordt overschreden.

|Veld| Type | Mogelijke waarden |
|:-----|:----:|:----|
|status | tekenreeks | notStarted: de analyse bewerking is niet gestart.<br /><br />uitvoeren: de analyse bewerking wordt uitgevoerd.<br /><br />mislukt: de analyse bewerking is mislukt.<br /><br />geslaagd: de analyse bewerking is voltooid.|

Wanneer het veld **status** de waarde **geslaagd** heeft, omvat het JSON-antwoord de factuur resultaten, tabellen geëxtraheerde en optionele tekst herkennings resultaten, indien aangevraagd. Het resultaat van de factuur overeenkomst is ingedeeld als een woorden lijst met benoemde veld waarden, waarbij elke waarde de geëxtraheerde tekst, genormaliseerde waarde, begrenzingsvak, betrouw baarheid en bijbehorende woord elementen bevat. Het bevat ook de regel items die worden geëxtraheerd, waarbij elk regel item de hoeveelheid, de beschrijving, de prijs per eenheid, het aantal, enzovoort bevat. Het resultaat van de tekst herkenning is ingedeeld als een hiërarchie van lijnen en woorden, met tekst, selectie kader en betrouwbaarheids informatie.

### <a name="sample-json-output"></a>Voor beeld van JSON-uitvoer

Het antwoord op de bewerking analyse van factuur resultaat ophalen is de gestructureerde weer gave van de factuur met alle gegevens die worden geëxtraheerd. Hier ziet u een voor beeld van een [factuur bestand](media/sample-invoice.jpg) en de bijbehorende gestructureerde uitvoer [voorbeeld factuur uitvoer](media/invoice-example-new.jpg).

De JSON-uitvoer heeft drie delen: 
* `"readResults"` het knoop punt bevat alle herkende tekst-en selectie markeringen. De tekst wordt geordend op pagina, vervolgens per regel en vervolgens op afzonderlijke woorden. 
* `"pageResults"` het knoop punt bevat de tabellen en cellen die zijn geëxtraheerd met hun begrenzingsvak, betrouw baarheid en een verwijzing naar de regels en woorden in ' readResults '.
* `"documentResults"` het knoop punt bevat de specifieke factuur waarden en regel items die het model heeft gedetecteerd. Hier vindt u alle velden van de factuur, zoals factuur-ID, verzen ding, factuur naar, klant, totaal, regel items en nog veel meer.

## <a name="example-output"></a>Voorbeelduitvoer

Met de factuur service worden de velden tekst, tabellen en 26 factuur geëxtraheerd. Hieronder vindt u de velden die zijn geëxtraheerd uit een factuur in het JSON-uitvoer antwoord (de onderstaande uitvoer gebruikt deze [voorbeeld factuur](media/sample-invoice.jpg)).

|Naam| Type | Description | Tekst | Waarde (gestandaardiseerde uitvoer) |
|:-----|:----|:----|:----| :----|
| CustomerName | tekenreeks | Klant wordt gefactureerd | Micro soft Corp |  |
| CustomerId | tekenreeks | Referentie-ID voor de klant | CID-12345 |  |
| PurchaseOrder | tekenreeks | Een referentie nummer van een inkoop order | IO-3333 | | 
| InvoiceId | tekenreeks | ID voor deze specifieke factuur (vaak factuur nummer) | INV-100 | | 
| InvoiceDate | date | Datum waarop de factuur is verzonden | 11/15/2019 | 2019-11-15 | 
| DueDate | date | De verval datum van de betaling voor deze factuur | 12/15/2019 | 2019-12-15 |
| Leveranciers naam | tekenreeks | Leverancier die deze factuur heeft gemaakt | CONTOSO LTD. | |
| VendorAddress | tekenreeks | E-mail adres voor de leverancier | 123 456th St New York, NY, 10001 | |
| VendorAddressRecipient | tekenreeks | De naam die is gekoppeld aan de VendorAddress | Contoso Headquarters | |
| CustomerAddress | tekenreeks | E-mail adres voor de klant | 123 overige st, Redmond WA, 98052 | |
| CustomerAddressRecipient | tekenreeks | De naam die is gekoppeld aan de CustomerAddress | Micro soft Corp | |
| BillingAddress | tekenreeks | Expliciet factuur adres voor de klant | 123 factuur KT, Redmond WA, 98052 | |
| BillingAddressRecipient | tekenreeks | De naam die is gekoppeld aan de BillingAddress | Micro soft-Services | |
| ShippingAddress | tekenreeks | Expliciete verzend adres voor de klant | 123 verzen ding St, Redmond WA, 98052 | |
| ShippingAddressRecipient | tekenreeks | De naam die is gekoppeld aan de ShippingAddress | Micro soft Delivery | |
| Subtotaal | getal | Subtotaal veld dat is geïdentificeerd op deze factuur | $100,00 | 100 | 
| TotalTax | getal | Het totale BTW-veld dat op deze factuur is vermeld | $10,00 | 10 |
| InvoiceTotal | getal | Totaal aantal nieuwe kosten gekoppeld aan deze factuur | $110,00 | 110 |
| AmountDue |  getal | Totaal bedrag als gevolg van de leverancier | $610,00 | 610 |
| ServiceAddress | tekenreeks | Expliciet service adres of eigenschaps adres voor de klant | 123 service St, Redmond WA, 98052 | |
| ServiceAddressRecipient | tekenreeks | De naam die is gekoppeld aan de ServiceAddress | Micro soft-Services | |
| RemittanceAddress | tekenreeks | Expliciete remise of betalings adres voor de klant | 123 remitte St New York, NY, 10001 |  |
| RemittanceAddressRecipient | tekenreeks | De naam die is gekoppeld aan de RemittanceAddress | Contoso-facturering |  |
| ServiceStartDate | date | Eerste datum voor de service periode (bijvoorbeeld een service periode van het hulp programma) | 14-10-2019 | 2019-10-14 |
| ServiceEndDate | date | De eind datum voor de service periode (bijvoorbeeld een service periode van het hulp programma) | 11/14/2019 | 2019-11-14 |
| PreviousUnpaidBalance | getal | Expliciet eerder onbetaald saldo | $500,00 | 500 |

Hieronder ziet u de regel items die worden geëxtraheerd uit een factuur in het JSON-uitvoer antwoord (de onderstaande uitvoer gebruikt deze [voorbeeld factuur](./media/sample-invoice.jpg))  

|Naam| Type | Description | Tekst (#1 van regel items) | Waarde (gestandaardiseerde uitvoer) |
|:-----|:----|:----|:----| :----|
| Items | tekenreeks | Volledige tekst regel van het regel item | 3/4/2021 A123 Consulting Services 2 uur $30,00 10% $60,00 | |
| Bedrag | getal | De hoeveelheid van het regel item | $60,00 | 100 |
| Description | tekenreeks | De tekst beschrijving voor het factuur regel item | Consulting Service | Consulting Service |
| Aantal | getal | De hoeveelheid voor dit factuur regel item | 2 | 2 |
| UnitPrice | getal | Het nettobedrag of de bruto prijs (afhankelijk van de instelling van de brutobedrag van de factuur) van één eenheid van dit item | $30,00 | 30 |
| Code | tekenreeks| Product code, product nummer of SKU die is gekoppeld aan het specifieke regel item | A123 | |
| Eenheid | tekenreeks| De eenheid van het regel item, bijvoorbeeld kg, LB enz. | uur | |
| Datum | datum| De datum die bij elk regel item hoort. Vaak is dit de datum waarop het regel item is verzonden | 3/4/2021| 2021-03-04 |
| Btw | getal | De BTW die is gekoppeld aan elk regel item. Mogelijke waarden zijn belasting bedrag, belasting percentage en belasting Y/N | 10% | |


## <a name="next-steps"></a>Volgende stappen

- Probeer uw eigen facturen en voor beelden in de voor [beeld-UI voor formulier herkenning](https://fott-preview.azurewebsites.net/).
- Voltooi de [Snelstartgids van een formulier herkenning](quickstarts/client-library.md) om aan de slag te gaan met het schrijven van een app voor het verwerken van facturen met de formulier herkenner in de ontwikkel taal van uw keuze.

## <a name="see-also"></a>Zie ook

* [Wat is Form Recognizer?](./overview.md)
* [REST API referentie documenten](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291)
