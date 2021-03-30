---
title: IntelliSense in Azure Stream Analytics-hulpprogram ma's voor Visual Studio code
description: In dit artikel wordt beschreven hoe u IntelliSense-functies gebruikt in Azure Stream Analytics-hulpprogram ma's voor Visual Studio code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: how-to
ms.openlocfilehash: 988b9d364f09a4c88bcaab1fe81ae80c8de5d4d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96491567"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>IntelliSense in Azure Stream Analytics-hulpprogram ma's voor Visual Studio code

IntelliSense is beschikbaar voor [Stream Analytics-query taal](/stream-analytics-query/stream-analytics-query-language-reference?bc=https%253a%2f%2fdocs.microsoft.com%2fazure%2fbread%2ftoc.json&toc=https%253a%2f%2fdocs.microsoft.com%2fazure%2fstream-analytics%2ftoc.json) in [Azure stream Analytics-HULPPROGRAM Ma's voor VS code](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview). IntelliSense is een hulp programma voor het volt ooien van de code die een aantal functies omvat: lijst leden, parameter info, snelle informatie en volledig woord. IntelliSense-functies worden soms ook wel aangeroepen door andere namen, zoals ' code completion ', ' content Assist ' en ' code hints '.

![IntelliSense-demo](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>IntelliSense-functies

De IntelliSense-functies in Stream Analytics-hulpprogram ma's voor VS code worden aangedreven door een taal service. Een taal service analyseert uw bron code en biedt intelligente code voltooiingen op basis van taal semantiek. Als een taal service mogelijke afrondingen kent, kunnen IntelliSense-suggesties worden gemaakt terwijl u typt. Als u doorgaat met typen, wordt een lijst met leden, zoals variabelen en methoden, gefilterd zodat alleen leden worden opgenomen die de door u getypte tekens bevatten. Wanneer u op de `Tab` of `Enter` toetsen drukt, voegt IntelliSense het door u geselecteerde lid in.

U kunt IntelliSense in elk editor venster activeren door een trigger teken, zoals het punt teken, te typen `.` .

![automatisch aanvullen IntelliSense](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> De widget suggesties biedt ondersteuning voor CamelCase-filtering. U kunt de letters met een hoofd letter in een methode naam typen om de suggesties te beperken. Zo wordt ' CRA ' snel ' createApplication ' weer geven.

### <a name="types-of-completions"></a>Typen voltooiingen

Stream Analytics-hulpprogram ma's voor VS code IntelliSense bieden verschillende typen aanvullingen, waaronder suggesties voor taal servers, fragmenten en eenvoudige woord afrondingen op basis van woorden.

|Voltooiing     |  Type       |
| ----- | ------- |
| Trefwoorden | `keyword`
| Functions | `build-in function`, `user defined function`  |
| Naam van gegevensset| `input`, `output`, `intermediate result set`|
| Kolom naam voor gegevensset|`input`, `intermediate result set`|

#### <a name="name-completion"></a>Naam voltooiing

Stream Analytics-hulpprogram ma's voor VS-code leest de lijst met invoer-en uitvoer namen van taken en de namen van de kolommen in uw gegevens bronnen wanneer ze worden geconfigureerd. De uitbrei ding onthoudt deze informatie om de mogelijkheden voor het volt ooien van namen te bieden die nuttig zijn voor het invoeren van instructies met een paar toetsaanslagen:

Tijdens het coderen hoeft u de editor niet te verlaten om Zoek opdrachten uit te voeren op namen van taken, uitvoer naam en kolom namen. U kunt uw context, de informatie die u nodig hebt, zoeken, elementen rechtstreeks in uw code invoegen en IntelliSense uw getypte tekst laten volt ooien.

Houd er rekening mee dat u lokale invoer of Live-invoer moet configureren en sla het configuratie bestand op om het gebruik van de naam te kunnen volt ooien.

![naam voltooiing](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>Parameter informatie

Met de optie informatie over IntelliSense- **para meter** wordt een lijst met para meters geopend met informatie over het aantal, de namen en de typen van de para meters die vereist zijn voor een functie. De para meter in vet geeft de volgende para meter aan die vereist is tijdens het typen van een functie.

De parameter lijst wordt ook weer gegeven voor geneste functies. Als u een functie als een para meter voor een andere functie typt, worden de para meters voor de binnenste functie weer gegeven in de lijst met para meters. Wanneer de lijst met para meters van de interne functie is voltooid, wordt de parameter lijst opnieuw ingesteld om de buitenste functie parameters weer te geven.

![parameter informatie](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>Snelle informatie

Zoals wordt bepaald door de taal service, kunt u voor elke id in uw code **snelle gegevens** bekijken. Enkele voor beelden van id's zijn invoer, uitvoer, een tussenliggende resultatenset of functie. Wanneer u de muis aanwijzer over een id beweegt, wordt de declaratie ervan weer gegeven in een pop-upvenster. De eigenschappen en gegevens schema's voor invoer, indien geconfigureerd en tussenliggende gegevens sets worden weer gegeven.

![snelle informatie](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>Problemen met IntelliSense oplossen

Dit probleem wordt veroorzaakt door een ontbrekende invoer configuratie die gegevens bevat. U kunt controleren of een [lokale invoer](visual-studio-code-local-run.md#define-a-local-input) of [Live invoer](visual-studio-code-local-run-live-input.md#define-a-live-stream-input) correct is geconfigureerd.

## <a name="next-steps"></a>Volgende stappen

* [Snelstartgids: een Azure Stream Analytics-taak maken in Visual Studio code](quick-create-visual-studio-code.md)
* [Stream Analytics-query's lokaal testen met voorbeeldgegevens testen met behulp van Visual Studio Code](visual-studio-code-local-run.md)
* [Stream Analytics query's lokaal testen op invoer van live streams met behulp van Visual Studio code](visual-studio-code-local-run-live-input.md)