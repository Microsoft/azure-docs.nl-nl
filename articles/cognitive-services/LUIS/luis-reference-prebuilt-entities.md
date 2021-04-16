---
title: Alle vooraf gebouwde entiteiten - LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat lijsten met de vooraf gebouwde entiteiten die zijn opgenomen in Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/13/2021
ms.openlocfilehash: 7155a829655645e13e0485ed7d51305ec50e5b0a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502756"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Entiteiten per cultuur in uw LUIS-model

Language Understanding (LUIS) biedt vooraf gebouwde entiteiten.

## <a name="entity-resolution"></a>Entiteitsoplossing
Wanneer een vooraf gebouwde entiteit is opgenomen in uw toepassing, bevat LUIS de bijbehorende entiteitsoplossing in het eindpuntreactie. Alle voorbeeld-utterances worden ook gelabeld met de entiteit .

Het gedrag van vooraf gebouwde entiteiten kan niet worden gewijzigd, maar u kunt de oplossing verbeteren door de vooraf gebouwde entiteit toe te voegen als een functie aan een [machine learning-entiteit of subentiteit](luis-concept-entity-types.md#prebuilt-entity).

## <a name="availability"></a>Beschikbaarheid
Tenzij anders vermeld, zijn vooraf gebouwde entiteiten beschikbaar in alle luis-toepassingsinstellingen (culturen). In de volgende tabel ziet u de vooraf gebouwde entiteiten die voor elke cultuur worden ondersteund.

|Cultuur|Subculturen|Notities|
|--|--|--|
|Chinees|[zh-CN](#chinese-entity-support)||
|Nederlands|[nl-NL](#dutch-entity-support)||
|Engels|[en-US (Amerikaans)](#english-american-entity-support)||
|Frans|[fr-CA (Canada),](#french-canadian-entity-support) [fr-FR (Frankrijk)](#french-france-entity-support), ||
|Duits|[de-DE](#german-entity-support)||
|Italiaans|[it-IT](#italian-entity-support)||
|Japans|[ja-JP](#japanese-entity-support)||
|Koreaans|[ko-KR](#korean-entity-support)||
|Portugees|[pt-BR (Brazilië)](#portuguese-brazil-entity-support)||
|Spaans|[es-ES (Spanje)](#spanish-spain-entity-support), [es-MX (Mexico)](#spanish-mexico-entity-support)||
|Turks|[Turks](#turkish-entity-support)||

## <a name="prediction-endpoint-runtime"></a>Voorspellings-eindpuntruntime

De beschikbaarheid van een vooraf gebouwde entiteit in een specifieke taal wordt bepaald door de runtimeversie van het voorspellings-eindpunt.

## <a name="chinese-entity-support"></a>Ondersteuning voor Chinese entiteiten

De volgende entiteiten worden ondersteund:

| Vooraf gebouwde entiteit | zh-CN |
| --------------- | :---: |
[Leeftijd:](luis-reference-prebuilt-age.md)<br>jaar<br>maand<br>Week<br>day   |    V2, V3   |
[Valuta (geld)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractionele eenheid (bijvoorbeeld: fractionele eenheid)  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>daterange<br>tijd<br>timerange   |    V2, V3   |
[Dimensie:](luis-reference-prebuilt-dimension.md)<br>volume<br>gebied<br>gewicht<br>informatie (bijvoorbeeld: bit/byte)<br>lengte (bijvoorbeeld: meter)<br>snelheid (bijvoorbeeld: mijl per uur)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Rangtelwoord](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Telefoonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="dutch-entity-support"></a>Ondersteuning voor Nederlandse entiteiten

De volgende entiteiten worden ondersteund:

| Vooraf gebouwde entiteit | nl-NL |
| --------------- | :---: |
[Leeftijd:](luis-reference-prebuilt-age.md)<br>jaar<br>maand<br>Week<br>day   |    V2, V3   |
[Valuta (geld)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractionele eenheid (bijvoorbeeld: fractionele eenheid)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimensie:](luis-reference-prebuilt-dimension.md)<br>volume<br>gebied<br>gewicht<br>informatie (bijvoorbeeld: bit/byte)<br>lengte (bijvoorbeeld: meter)<br>snelheid (bijvoorbeeld mijl per uur)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Rangtelwoord](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefoonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="english-american-entity-support"></a>Ondersteuning voor Engelse (Amerikaanse) entiteiten

De volgende entiteiten worden ondersteund:

| Vooraf gebouwde entiteit | en-US |
| --------------- | :---: |
[Leeftijd:](luis-reference-prebuilt-age.md)<br>jaar<br>maand<br>Week<br>day   |    V2, V3   |
[Valuta (geld)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractionele eenheid (bijvoorbeeld: fractionele eenheid)  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>daterange<br>tijd<br>timerange   |    V2, V3   |
[Dimensie:](luis-reference-prebuilt-dimension.md)<br>volume<br>gebied<br>gewicht<br>informatie (bijvoorbeeld: bit/byte)<br>lengte (bijvoorbeeld: meter)<br>snelheid (bijvoorbeeld: mijl per uur)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    V2, V3   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Rangtelwoord](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    V2, V3   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Telefoonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-france-entity-support"></a>Ondersteuning voor Franse (Frankrijk) entiteiten

De volgende entiteiten worden ondersteund:

| Vooraf gebouwde entiteit | fr-FR |
| --------------- | :---: |
[Leeftijd:](luis-reference-prebuilt-age.md)<br>jaar<br>maand<br>Week<br>day   |    V2, V3   |
[Valuta (geld)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractionele eenheid (bijvoorbeeld: fractionele eenheid)  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>daterange<br>tijd<br>timerange   |    V2, V3   |
[Dimensie:](luis-reference-prebuilt-dimension.md)<br>volume<br>gebied<br>gewicht<br>informatie (bijvoorbeeld: bit/byte)<br>lengte (bijvoorbeeld: meter)<br>snelheid (bijvoorbeeld: mijl per uur)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Rangtelwoord](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |   -   |
[Telefoonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-canadian-entity-support"></a>Ondersteuning voor Franse (Canada) entiteiten

De volgende entiteiten worden ondersteund:

| Vooraf gebouwde entiteit | fr-CA |
| --------------- | :---: |
[Leeftijd:](luis-reference-prebuilt-age.md)<br>jaar<br>maand<br>Week<br>day   |    V2, V3   |
[Valuta (geld)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractionele eenheid (bijvoorbeeld:  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>daterange<br>tijd<br>timerange   |    V2, V3   |
[Dimensie:](luis-reference-prebuilt-dimension.md)<br>volume<br>gebied<br>gewicht<br>informatie (bijvoorbeeld: bit/byte)<br>lengte (bijvoorbeeld: meter)<br>snelheid (bijvoorbeeld: mijl per uur)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Rangtelwoord](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefoonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatuur:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="german-entity-support"></a>Ondersteuning voor Duitse entiteiten

De volgende entiteiten worden ondersteund:

|Vooraf gebouwde entiteit | de-DE |
| -------------- | :---: |
[Leeftijd:](luis-reference-prebuilt-age.md)<br>jaar<br>maand<br>Week<br>day   |    V2, V3   |
[Valuta (geld)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractionele eenheid (bijvoorbeeld:  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>daterange<br>tijd<br>timerange   |    V2, V3   |
[Dimensie:](luis-reference-prebuilt-dimension.md)<br>volume<br>gebied<br>gewicht<br>informatie (bijvoorbeeld: bit/byte)<br>lengte (bijvoorbeeld: meter)<br>snelheid (bijvoorbeeld mijl per uur)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Rangtelwoord](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefoonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatuur:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="italian-entity-support"></a>Ondersteuning voor Italiaans entiteit

De italiaans vooraf gebouwde leeftijd, valuta, dimensie, getal, _resolutiespercentage_ is gewijzigd van V2 en V3 preview.

De volgende entiteiten worden ondersteund:

| Vooraf gebouwde entiteit | it-IT |
| --------------- | :---: |
[Leeftijd:](luis-reference-prebuilt-age.md)<br>jaar<br>maand<br>Week<br>day   |    V2, V3   |
[Valuta (geld)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractionele eenheid (bijvoorbeeld:  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>gewicht<br>informatie (bijvoorbeeld: bit/byte)<br>lengte (bijvoorbeeld: meter)<br>snelheid (bijvoorbeeld: mijl per uur)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Rangtelwoord](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefoonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatuur:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="japanese-entity-support"></a>Ondersteuning voor Japanse entiteiten

De volgende entiteiten worden ondersteund:

|Vooraf gebouwde entiteit | ja-JP |
| -------------- | :---: |
[Leeftijd:](luis-reference-prebuilt-age.md)<br>jaar<br>maand<br>Week<br>day   |    V2, -   |
[Valuta (geld)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractionele eenheid (bijvoorbeeld:  |    V2, -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>gewicht<br>informatie (bijvoorbeeld: bit/byte)<br>lengte (bijvoorbeeld: meter)<br>snelheid (bijvoorbeeld: mijl per uur)  |    V2, -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2, -   |
[Rangtelwoord](luis-reference-prebuilt-ordinal.md)   |    V2, -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefoonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatuur:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, -   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="korean-entity-support"></a>Ondersteuning voor Koreaans entiteit

De volgende entiteiten worden ondersteund:

| Vooraf gebouwde entiteit | ko-KR |
| --------------- | :---: |
[Leeftijd:](luis-reference-prebuilt-age.md)<br>jaar<br>maand<br>Week<br>day   |    -   |
[Valuta (geld)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractionele eenheid (bijvoorbeeld: fractionele eenheid)  |    -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>gewicht<br>informatie (bijvoorbeeld: bit/byte)<br>lengte (bijvoorbeeld: meter)<br>snelheid (bijvoorbeeld: mijl per uur)  |    -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Number](luis-reference-prebuilt-number.md)   |    -   |
[Rangtelwoord](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefoonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="portuguese-brazil-entity-support"></a>Ondersteuning voor Portugees (Brazilië) voor entiteiten

De volgende entiteiten worden ondersteund:

| Vooraf gebouwde entiteit | pt-BR |
| --------------- | :---: |
[Leeftijd:](luis-reference-prebuilt-age.md)<br>jaar<br>maand<br>Week<br>day   |    V2, V3   |
[Valuta (geld)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractionele eenheid (bijvoorbeeld: fractionele eenheid)  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>daterange<br>tijd<br>timerange   |    V2, V3   |
[Dimensie:](luis-reference-prebuilt-dimension.md)<br>volume<br>gebied<br>gewicht<br>informatie (bijvoorbeeld: bit/byte)<br>lengte (bijvoorbeeld: meter)<br>snelheid (bijvoorbeeld mijl per uur)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Rangtelwoord](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefoonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

KeyPhrase is niet beschikbaar in alle subsectoren van het Portugees (Brazilië) - ```pt-BR``` .

## <a name="spanish-spain-entity-support"></a>Ondersteuning voor Spaans (Spanje) voor entiteiten

De volgende entiteiten worden ondersteund:

| Vooraf gebouwde entiteit | es-ES |
| --------------- | :---: |
[Leeftijd:](luis-reference-prebuilt-age.md)<br>jaar<br>maand<br>Week<br>day   |    V2, V3   |
[Valuta (geld)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractionele eenheid (bijvoorbeeld: fractionele eenheid)  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>daterange<br>tijd<br>timerange   |    V2, V3   |
[Dimensie:](luis-reference-prebuilt-dimension.md)<br>volume<br>gebied<br>gewicht<br>informatie (bijvoorbeeld: bit/byte)<br>lengte (bijvoorbeeld: meter)<br>snelheid (bijvoorbeeld mijl per uur)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Rangtelwoord](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefoonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-mexico-entity-support"></a>Ondersteuning voor Spaans (Mexico) voor entiteiten

De volgende entiteiten worden ondersteund:

| Vooraf gebouwde entiteit | es-MX |
| --------------- | :---: |
[Leeftijd:](luis-reference-prebuilt-age.md)<br>jaar<br>maand<br>Week<br>day   |    -   |
[Valuta (geld)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractionele eenheid (bijvoorbeeld: fractionele eenheid)  |    -   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>daterange<br>tijd<br>timerange   |    -   |
[Dimensie:](luis-reference-prebuilt-dimension.md)<br>volume<br>gebied<br>gewicht<br>informatie (bijvoorbeeld: bit/byte)<br>lengte (bijvoorbeeld: meter)<br>snelheid (bijvoorbeeld mijl per uur)  |    -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Rangtelwoord](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefoonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatuur:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

Zie de opmerkingen [over afgeschafte vooraf gebouwde entiteiten](luis-reference-prebuilt-deprecated.md)

## <a name="turkish-entity-support"></a>Ondersteuning voor Turks-entiteiten

| Vooraf gebouwde entiteit | tr-tr |
| --------------- | :---: |
[Leeftijd:](luis-reference-prebuilt-age.md)<br>jaar<br>maand<br>Week<br>day   |    -   |
[Valuta (geld)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractionele eenheid (bijvoorbeeld:  |    -   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>daterange<br>tijd<br>timerange   |    -   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>gewicht<br>informatie (bijvoorbeeld: bit/byte)<br>lengte (bijvoorbeeld: meter)<br>snelheid (bijvoorbeeld: mijl per uur)  |    -   |
[E-mail](luis-reference-prebuilt-email.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Number](luis-reference-prebuilt-number.md)   |    -   |
[Rangtelwoord](luis-reference-prebuilt-ordinal.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   |
[Telefoonnummer](luis-reference-prebuilt-phonenumber.md)   |    -   |
[Temperatuur:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    -   |

<!---
See notes on [Deprecated prebuilt entities](luis-reference-prebuilt-deprecated.md)
KeyPhrase is not available.
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>Bijdragen aan vooraf gebouwde entiteitscultuur
De vooraf gebouwde entiteiten zijn ontwikkeld in Recognizers-Text opensource-project. [Bijdragen](https://github.com/Microsoft/Recognizers-Text) aan het project. Dit project bevat voorbeelden van valuta's per cultuur.

GeographyV2 en PersonName zijn niet opgenomen in het Recognizers-Text project. Open een ondersteuningsaanvraag voor problemen met deze vooraf gebouwde [entiteiten.](../../azure-portal/supportability/how-to-create-azure-support-request.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het getal,](luis-reference-prebuilt-number.md) [datetimeV2](luis-reference-prebuilt-datetimev2.md)en [valuta-entiteiten.](luis-reference-prebuilt-currency.md)
