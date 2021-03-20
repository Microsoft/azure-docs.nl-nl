---
title: Fout opsporing door de gebruiker gedefinieerde functies in Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u door de gebruiker gedefinieerde functies in Azure Stream Analytics oplost.
author: jenssuessmeyer
ms.author: jenss
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/24/2020
ms.custom: devx-track-js
ms.openlocfilehash: 7f5a24e4d351d7c133251900a80c70094b84cc4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019428"
---
# <a name="debug-user-defined-functions-in-azure-stream-analytics"></a>Fout opsporing door de gebruiker gedefinieerde functies in Azure Stream Analytics 

Wanneer door de gebruiker gedefinieerde functies (UDF) niet werken zoals verwacht, moet u fouten opsporen om het probleem op te sporen. U kunt fouten opsporen in Udf's voor uw Stream Analytics taken wanneer u uw taken lokaal uitvoert met [Visual Studio code](visual-studio-code-local-run-live-input.md) of [Visual Studio](stream-analytics-vs-tools-local-run.md).

Wanneer u uw taak lokaal uitvoert op een live invoer stroom, imiteert deze alleen de uitvoering van de Cloud Azure Stream Analytics engine op één knoop punt. Lokale tests van live data kunnen de prestaties en schaal baarheid die u in de Cloud uitvoert niet vervangen, maar u bespaart tijd tijdens functionele tests, omdat u niet telkens uw taak hoeft te verzenden naar de Cloud wanneer u wilt testen. Daarnaast worden tijd beleidsregels uitgeschakeld voor lokale uitvoeringen met lokale of voorbeeld gegevens, maar wordt een tijd beleid ondersteund voor het testen van live data.

## <a name="pick-your-language"></a>Uw taal kiezen

U kunt UDFs voor Azure Stream Analytics schrijven met behulp van .NET (C#) of Java script. 

### <a name="functions-in-c"></a>Functies in C # 

Wanneer u [.net UDFs met Visual Studio schrijft](stream-analytics-edge-csharp-udf-methods.md), krijgt u hetzelfde ondersteunings niveau als voor elk .net-klasse-project. Deze ondersteuning omvat:

* Ondersteuning voor compilatie, zoals syntaxis controle en ondersteuning voor compileren.

* De mogelijkheid om een C#-project en artefacten in uw Stream Analytics-oplossing toe te voegen, te bouwen en ernaar te verwijzen. 

* Eenvoudig hergebruik van code die is ingekapseld in een deelbaar project. 

* Fout opsporing rechtstreeks in Visual Studio. Stel het Stream Analytics project in als start project en stel onderbrekings punten in C#-code in. Druk vervolgens op **F5** om de c#-code te debuggen zoals u dat zou doen voor elk ander C#-project. 

### <a name="functions-in-javascript"></a>Functies in Java script

Java script is een andere optie voor het maken van functies in Stream Analytics. De Java script-code wordt rechtstreeks in het functie gebied van het Stream Analytics project geplaatst, waardoor het delen van projecten lastiger wordt.

De compilatie treedt op wanneer het Stream Analytics project wordt gecompileerd of uitgevoerd. De kans op het vinden van een probleem tijdens runtime is hoger. Er is geen ondersteuning voor fout opsporing voor Java script-functies in Stream Analytics rechtstreeks.

## <a name="debug-options-for-javascript"></a>Opties voor fout opsporing voor Java script

Omdat er geen ondersteuning voor fout opsporing is voor Java script-functies in Stream Analytics, kunt u fouten opsporen door de functie in een HTML-site te integreren en de uitvoer van daaruit op te halen.

In het volgende voor beeld ziet u hoe u fouten in Java script-Udf's oplost met enkele beperkingen in een geïntegreerde runtime-omgeving in [Visual Studio code](quick-create-visual-studio-code.md).

### <a name="prerequisites"></a>Vereisten

Voordat u begint, controleert u of uw Azure Stream Analytics-project de volgende items bevat:

* Een invoer 
* Een uitvoer 
* Een Stream Analytics query (. asaql) 
* Een Stream Analytics taak configuratie (JobConfig.jsaan)
* Een Java script-UDF

### <a name="prepare-files"></a>Bestanden voorbereiden

In de volgende afbeelding bevat het *asaql* -query bestand alleen de AANROEP van UDF, *fxcharCount*. Deze wijziging zorgt ervoor dat u het project nog steeds kunt compileren nadat er wijzigingen zijn aangebracht.

> [!div class="mx-imgBorder"]
> ![Query bestand Stream Analytics testen](./media/debug-user-defined-functions/asaql-file.png)

Maak een extra map in **tests** voor het hosten van het test bestand, dat wordt aangeroepen om de test uit te voeren met de Java script-functie. In dit voor beeld is de naam van de map *fxcharCount* en is de naam van de test *Test_UDF.js*. 

In de onderstaande afbeelding ziet u de code in het test bestand, waarmee het functie bestand wordt geladen en de functie wordt uitgevoerd. Dit voor beeld is eenvoudig, maar u kunt extra test gegevensbestanden laden en door loop aanvullende tests om de uitvoer op te halen. De notatie van de functie aanroep wijkt weinig af van de gemeen schappelijke aanroepen omdat naar het bestand wordt verwezen en niet is geladen in de runtime, waardoor fouten kunnen worden opgespoord. 

> [!div class="mx-imgBorder"]
> ![Test bestand Stream Analytics](./media/debug-user-defined-functions/test-file.png)

Voeg in de functie de volgende regels code toe aan het bestand om de methoden zichtbaar te maken. Ze hebben geen invloed op de mogelijkheid om de code te compileren in Visual Studio code.

```javascript
var methods = {};
methods.fxchartCount = fxchartCount;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![Stream Analytics java script UDF](./media/debug-user-defined-functions/udf-file.png)
  
## <a name="install-debug-support"></a>Ondersteuning voor fout opsporing installeren

Als u fouten wilt opsporen, moet u **node.js** [downloaden](https://nodejs.org/en/download/) en installeren. Installeer de juiste versie op basis van het platform dat u gebruikt. Nadat u de node.js runtime hebt geïnstalleerd, start u Visual Studio code opnieuw om de wijzigingen te implementeren. 

Selecteer **uitvoeren en fout opsporing** of druk op **CTRL + SHIFT + D** om de fout opsporing te starten. Een keuze lijst met invoervak wordt weer gegeven waarin u **node.js** als runtime kunt selecteren. Als u alleen node.js hebt geïnstalleerd, wordt dit standaard gebruikt. U moet eerst de code en naar het satelliet bestand door lopen, indien nodig met F11. 

> [!div class="mx-imgBorder"]
> ![Stream Analytics uitvoeren en fouten opsporen in UDF](./media/debug-user-defined-functions/run-debug-udf.png)

### <a name="debug-user-defined-aggregates"></a>Fout opsporing door door de gebruiker gedefinieerde aggregaties 

U kunt de methode debug voor Java script-Udf's gebruiken om fouten op te sporen in door de gebruiker gedefinieerde statistische functies (UDA). In dit voor beeld wordt een UDA toegevoegd aan het *. asaql* -query bestand en het test bestand.

Net als bij de UDF neemt u een aanroep van de UDA op om ervoor te zorgen dat het project wordt gecompileerd nadat er wijzigingen zijn aangebracht. 

> [!div class="mx-imgBorder"]
> ![Uda toevoegen aan asaql](./media/debug-user-defined-functions/asaql-uda.png)

In het *Test_UDA.js* bestand verwijst u naar het UDA-bestand zoals u dat hebt gedaan met de UDF. Daarnaast belt u, `main()` `init()` en `accumulate()` . De `accumulate()` methode wordt aangeroepen in een lus om de waarden in de status stack te plaatsen. De `computeresult()` methode wordt aangeroepen om de uiteindelijke query samen te stellen. 

> [!div class="mx-imgBorder"]
> ![UDA-test bestand](./media/debug-user-defined-functions/uda-test.png)

Net als in het UDF-voor beeld moet er een code aan de UDA worden toegevoegd om de relevante methoden zichtbaar te maken.

```javascript
var methods = {};
methods.main = main;
methods.init = main.init;
methods.accumulate = main.accumulate;
methods.computeResult = main.computeResult;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![Code toegevoegd aan UDA](./media/debug-user-defined-functions/uda-expose-methods.png)

Selecteer **uitvoeren en fout opsporing** of druk op **CTRL + SHIFT + D** om de fout opsporing te starten. Een keuze lijst met invoervak wordt weer gegeven waarin u **node.js** als runtime kunt selecteren. Als u alleen node.js hebt geïnstalleerd, wordt dit standaard gebruikt. U moet eerst de code en naar het satelliet bestand door lopen, indien nodig met F11.

> [!div class="mx-imgBorder"]
> ![Stream Analytics run and debug UDA](./media/debug-user-defined-functions/run-debug-uda.png)


## <a name="next-steps"></a>Volgende stappen

* [Azure Stream Analytics taken lokaal ontwikkelen en fouten opsporen](develop-locally.md)
* [Fouten opsporen Azure Stream Analytics query's lokaal met behulp van taak diagram in Visual Studio](debug-locally-using-job-diagram.md)
* [Door de gebruiker gedefinieerde functies in Azure Stream Analytics](functions-overview.md)
 
