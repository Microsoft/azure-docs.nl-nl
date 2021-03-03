---
title: Transcripties-richt lijnen met Human labels-Speech Service
titleSuffix: Azure Cognitive Services
description: Als u de nauw keurigheid van de spraak herkenning wilt verbeteren, bijvoorbeeld wanneer woorden worden verwijderd of onjuist zijn vervangen, kunt u transcripties met menselijke labels samen met uw audio gegevens gebruiken. Transcripties met menselijke labels zijn woord voor woord, Verbatim transcripties van een audio bestand.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: erhopf
ms.openlocfilehash: af6ced49071b7fbae983508e68964aa064ef38e1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700028"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Transcripties met menselijke labels maken

Als u de nauw keurigheid van de herkenning wilt verbeteren, met name problemen die worden veroorzaakt wanneer woorden worden verwijderd of onjuist zijn vervangen, kunt u transcripties met menselijke labels samen met uw audio gegevens gebruiken. Wat zijn transcripties van menselijke labels? Dat is eenvoudig, ze zijn woord-voor-Word, Verbatim transcripties van een audio bestand.

Er is een grote steek proef van transcriptie-gegevens vereist om de herkenning te verbeteren. we raden u aan om te voorzien in een waarde van 1 tot 20 uur aan transcriptie-gegevens. De spraak service maakt gebruik van Maxi maal 20 uur aan audio voor training. Op deze pagina bekijken we richt lijnen die zijn ontworpen om u te helpen bij het maken van transcripties met hoge kwaliteit. Deze hand leiding is opgedeeld op land instellingen, met secties voor Engels (Verenigde Staten), Mandarijn Chinees en Duits.

> [!NOTE]
> Niet alle basis modellen ondersteunen aanpassing met audio bestanden. Als een basis model dit niet ondersteunt, wordt de tekst van de transcripties op dezelfde manier gebruikt als gerelateerde tekst. Zie [taal ondersteuning](language-support.md#speech-to-text) voor een lijst met basis modellen die ondersteuning bieden voor training met audio gegevens.

> [!NOTE]
> Als u het basis model dat wordt gebruikt voor de training wijzigt en u audio hebt in de trainings-gegevensset, moet u *altijd* controleren of het nieuwe basis model [training voor audio gegevens ondersteunt](language-support.md#speech-to-text). Als het eerder gebruikte basis model geen training met audio gegevens ondersteunt, en de training-gegevensset bevat audio, wordt de trainings tijd met het nieuwe basis model **drastisch** verhoogd en kan het enkele uren tot enkele dagen en langer duren. Dit geldt vooral als uw abonnement op de spraak service zich **niet** in een regio bevindt [met de speciale hardware](custom-speech-overview.md#set-up-your-azure-account) voor training.
>
> Als u het probleem voor komt dat in de bovenstaande alinea wordt beschreven, kunt u de trainings tijd snel verlagen door de hoeveelheid audio in de gegevensset te verminderen of deze volledig te verwijderen en alleen de tekst te verlaten. De laatste optie wordt sterk aanbevolen als uw abonnement op de spraak service zich **niet** in een regio bevindt [met de speciale hardware](custom-speech-overview.md#set-up-your-azure-account) voor training.

## <a name="us-english-en-us"></a>Amerikaans Engels (nl-nl)

Transcripties voor het Engels van de mens moet worden verschaft als tekst zonder opmaak, alleen met behulp van ASCII-tekens. Vermijd het gebruik van Latijnse-1-of Unicode-Lees tekens. Deze tekens worden vaak per ongeluk toegevoegd bij het kopiëren van tekst uit een tekstverwerkings toepassing of het knipselen van gegevens van webpagina's. Als deze tekens aanwezig zijn, moet u deze bijwerken met de juiste ASCII-vervanging.

Enkele voorbeelden:

| Te vermijden tekens | Substitution | Notities |
| ------------------- | ------------ | ----- |
| "Hallo wereld" | "Hello world" | De aanhalings tekens openen en sluiten worden vervangen door de juiste ASCII-tekens. |
| Dag van John | Dag van John | De apostrof is vervangen door het juiste ASCII-teken. |
| het was goed, Nee, het was geweldig! | het was goed--Nee, het was geweldig. | Het em-streepje is vervangen door twee afbreek streepjes. |

### <a name="text-normalization-for-us-english"></a>Tekst normalisatie voor Engels (Verenigde Staten)

Tekst normalisatie is de trans formatie van woorden in een consistente indeling die wordt gebruikt bij het trainen van een model. Sommige regels voor normalisatie worden automatisch toegepast op tekst. we raden u echter aan om deze richt lijnen te gebruiken bij het voorbereiden van uw transcriptie-gegevens met Human Labels:

- Afkortingen in woorden afschrijven.
- Schrijf niet-standaard numerieke teken reeksen op in woorden (zoals boekhoud voorwaarden).
- Niet-alfabetische tekens of gecombineerde alfanumerieke tekens moeten worden getranscribeerd als uitgesp roken.
- Afkortingen die worden uitgesp roken als woorden, mogen niet worden bewerkt (zoals ' radar ', ' Laser ', ' RAM ' of ' NAVO ').
- Schrijf afkortingen die worden uitgesp roken als afzonderlijke letters, waarbij elke letter wordt gescheiden door een spatie.
- Als u audio gebruikt, transcribeert u getallen als woorden die overeenkomen met de audio (bijvoorbeeld ' 101 ' kunnen worden uitgesp roken als ' 1 0 1 ' of ' 101 ').
- Vermijd het gebruik van herhaalde tekens, woorden of groepen woorden meer dan drie keer, zoals ' ja ja ja ja '. Regels met dergelijke herhalingen kunnen door de spraak service worden verwijderd.

Hier volgen enkele voor beelden van normalisatie die u moet uitvoeren op de transcriptie:

| Oorspronkelijke tekst               | Tekst na normalisatie              |
| --------------------------- | ------------------------------------- |
| Banner Dr. Bruce            | Dokter Bruce Banner                   |
| James obligatie, 007             | Jeroens obligatie, dubbele Oh-zeven           |
| Ke $ ha                       | Kesha                                 |
| Hoe lang is het 2x4         | Hoe lang is de twee door vier           |
| De vergadering loopt van 1-3pm | De vergadering loopt van een tot drie uur |
| Mijn bloed type is O +         | Mijn bloed type is O positief           |
| Water is H20                | Water is H 2 O                        |
| OU812 Play by van halen     | O U 8 1 2 afspelen met van halen           |
| UTF-8 met stuklijst              | U T F 8 met stuk lijst                      |

De volgende regels voor normalisatie worden automatisch toegepast op transcripties:

- Gebruik kleine letters.
- Alle Lees tekens behalve apostrofs in woorden verwijderen.
- Breid getallen uit in woorden/gesp roken formulieren, zoals dollar bedragen.

Hier volgen enkele voor beelden van normalisatie automatisch uitgevoerd op de transcriptie:

| Oorspronkelijke tekst                          | Tekst na normalisatie          |
| -------------------------------------- | --------------------------------- |
| "Heilige koeien!" voornoemd Batman.               | heilige koeien zei Batman              |
| ' Wat? ' Batman side kick, Robin. | wat gezegd Batman side kick Robin |
| Ga naar Get-Em!                            | Ga naar em                         |
| Ik ben dubbel gepaard                     | Ik ben dubbel gepaard                |
| 104 Elm straat                         | 1 0 4 Elm straat            |
| Afstemmen op 102,7                          | afstemmen op 1 0 2 punt 7    |
| Pi is ongeveer 3,14                       | Pi is ongeveer drie punt 1 4  |
| IT-kosten \$ 3,14                        | IT-kosten 3 14           |

## <a name="mandarin-chinese-zh-cn"></a>Mandarijn Chinees (zh-CN)

Transcripties voor Mandarijn-Chinees met menselijke labels moeten een UTF-8-code ring met een byte-volgorde markering zijn. Vermijd het gebruik van interpunctie tekens met halve breedte. Deze tekens kunnen per ongeluk worden opgenomen wanneer u de gegevens voorbereidt in een tekstverwerkings programma of gegevens van webpagina's samenvoegt. Als deze tekens aanwezig zijn, moet u deze bijwerken met de juiste vervanging van de volledige breedte.

Enkele voorbeelden:

| Te vermijden tekens | Substitution   | Notities |
| ------------------- | -------------- | ----- |
| "你好" | "你好" | De aanhalings tekens openen en sluiten worden vervangen door de juiste tekens. |
| 需要什么帮助? | 需要什么帮助？| Het vraag teken is vervangen door het juiste teken. |

### <a name="text-normalization-for-mandarin-chinese"></a>Tekst normalisatie voor Mandarijn Chinees

Tekst normalisatie is de trans formatie van woorden in een consistente indeling die wordt gebruikt bij het trainen van een model. Sommige regels voor normalisatie worden automatisch toegepast op tekst. we raden u echter aan om deze richt lijnen te gebruiken bij het voorbereiden van uw transcriptie-gegevens met Human Labels:

- Afkortingen in woorden afschrijven.
- Numerieke teken reeksen schrijven in gesp roken vorm.

Hier volgen enkele voor beelden van normalisatie die u moet uitvoeren op de transcriptie:

| Oorspronkelijke tekst | Tekst na normalisatie |
| ------------- | ------------------------ |
| 我今年 21 | 我今年二十一 |
| 3 号楼 504 | 三号 楼 五 零 四 |

De volgende regels voor normalisatie worden automatisch toegepast op transcripties:

- Alle Lees tekens verwijderen
- Getallen uitvouwen naar een gesp roken formulier
- Letters met een volle breedte converteren naar letters met halve breedte
- Hoofd letters gebruiken voor alle Engelse woorden

Hier volgen enkele voor beelden van normalisatie automatisch uitgevoerd op de transcriptie:

| Oorspronkelijke tekst | Tekst na normalisatie |
| ------------- | ------------------------ |
| 3,1415 | 三 点 一 四 一 五 |
| ¥3,5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992 年 8 月 8 日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午 5:00 的航班 | 下午 五点 的 航班 |
| 我今年 21 岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>Duits (de-DE) en andere talen

Transcripties voor Duitse audio (en andere niet-Engelse of Mandarijn Chinese talen) moet UTF-8 zijn gecodeerd met een byte volgorde markering. Voor elk audio bestand moet één transcript met menselijke labels worden gegeven.

### <a name="text-normalization-for-german"></a>Tekst normalisatie voor Duits

Tekst normalisatie is de trans formatie van woorden in een consistente indeling die wordt gebruikt bij het trainen van een model. Sommige regels voor normalisatie worden automatisch toegepast op tekst. we raden u echter aan om deze richt lijnen te gebruiken bij het voorbereiden van uw transcriptie-gegevens met Human Labels:

- Schrijf decimale punten als ', ' en niet '. '.
- Schrijf tijd scheiders als ': ' en niet '. ' (bijvoorbeeld: 12:00 Uhr).
- Afkortingen zoals ' ca. ' worden niet vervangen. U wordt aangeraden de volledige gesp roken vorm te gebruiken.
- De vier belangrijkste reken kundige Opera tors (+,-, \* en/) worden verwijderd. We raden u aan ze te vervangen door de geschreven vorm: "plus teken," "min," "geteilt".
- Vergelijkings operatoren worden verwijderd (=, < en >). U kunt ze het beste vervangen door ' gleich ', ' kleinere als ' en ' grösser als '.
- Schrijf breuken, zoals 3/4, in geschreven vorm (bijvoorbeeld: "Drei Viertel" in plaats van 3/4).
- Het symbool "€" vervangen door de geschreven vorm "euro".

Hier volgen enkele voor beelden van normalisatie die u moet uitvoeren op de transcriptie:

| Oorspronkelijke tekst    | Tekst na gebruikers normalisatie | Tekst na systeem normalisatie       |
| ---------------- | ----------------------------- | ------------------------------------- |
| Es ist 12,23 Uhr | Es ist 12:23 Uhr              | es ist zwölf Uhr Drei und Zwanzig Uhr |
| {12,45}          | {12,45}                       | zwölf komma vier fünf                 |
| 2 + 3-4        | 2 plus 3 min 4              | Zwei plus Drei min vier             |

De volgende regels voor normalisatie worden automatisch toegepast op transcripties:

- Gebruik kleine letters voor alle tekst.
- Alle Lees tekens verwijderen, met inbegrip van de verschillende typen aanhalings tekens (testen, testen, testen en «test» zijn OK).
- Rijen negeren met speciale tekens uit deze set: ¢ ¤ ¥ ¦ § © ª ¬® ° ² ¬ x ÿ Ø, ¬.
- Breid cijfers uit naar gesp roken tekst, inclusief dollar of euro bedragen.
- Accepteer alleen umlauten voor a, o en u. Andere worden vervangen door de th of worden verwijderd.

Hier volgen enkele voor beelden van normalisatie automatisch uitgevoerd op de transcriptie:

| Oorspronkelijke tekst    | Tekst na normalisatie |
| ---------------- | ------------------------ |
| Frankfurter Ring | Frankfurter Ring         |
| ¡ Eine.     | Eine-fragmenten               |
| wir, haben       | wir haben                |

### <a name="text-normalization-for-japanese"></a>Tekst normalisatie voor Japans

In het Japans (ja-JP) is er een maximale lengte van 90 tekens voor elke zin. Regels met meer zinnen worden verwijderd. Als u meer tekst wilt toevoegen, voegt u een punt in tussen.

## <a name="next-steps"></a>Volgende stappen

- [Uw gegevens voorbereiden en testen](./how-to-custom-speech-test-and-train.md)
- [Uw gegevens controleren](how-to-custom-speech-inspect-data.md)
- [Uw gegevens evalueren](how-to-custom-speech-evaluate-data.md)
- [Uw model trainen](how-to-custom-speech-train-model.md)
- [Uw model implementeren](./how-to-custom-speech-train-model.md)