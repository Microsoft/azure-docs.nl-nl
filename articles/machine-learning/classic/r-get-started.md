---
title: R gebruiken met Machine Learning Studio (klassiek)-Azure
description: Gebruik deze zelf studie voor R-Program ma's om aan de slag te gaan met Azure Machine Learning Studio (klassiek) in R om een prognose oplossing te maken.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: c619b51f9323477bda4f1ec99aeeb1bfa01028fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100517736"
---
# <a name="get-started-with-azure-machine-learning-studio-classic-in-r"></a>Aan de slag met Azure Machine Learning Studio (klassiek) in R

**VAN TOEPASSING OP:**  ![Dit is een vinkje, wat betekent dat dit artikel van toepassing is op Machine Learning Studio (klassiek).](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek)   ![Dit is een X, wat betekent dat dit artikel van toepassing is op Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

<!-- Stephen F Elston, Ph.D. -->
In deze zelf studie leert u hoe u Azure Machine Learning Studio (klassiek) kunt gebruiken om R-code te maken, te testen en uit te voeren. Uiteindelijk hebt u een volledige oplossing voor prognoses.

> [!div class="checklist"]
> * Maak code voor het opschonen van gegevens en de trans formatie.
> * Analyseer de correlaties tussen verschillende variabelen in onze gegevensset.
> * Maak een prognose model voor seizoensgebonden tijd reeksen voor de melk productie.


Machine Learning Studio (klassiek) bevat veel krachtige modules voor machine learning en gegevens manipulatie. Met de programmeer taal R biedt deze combi natie de schaal baarheid en eenvoudigere implementatie van Machine Learning Studio (klassiek) met de flexibiliteit en diepe analyse van R.

Het maken van prognoses is een veelgebruikte en handige analyse methode. Veelvoorkomende toepassingen variëren van het voors pellen van de verkoop van seizoensgebonden artikelen en het bepalen van optimale voorraad niveaus voor het voors pellen van macro-variabelen. Prognoses worden doorgaans uitgevoerd met Time Series-modellen. Time Series-gegevens zijn gegevens waarin de waarden een tijd index hebben. De tijd index kan regel matig, bijvoorbeeld elke maand of per minuut, zijn. De tijd index kan ook onregelmatig zijn. Een tijdreeks model is gebaseerd op Time Series-gegevens. De programmeer taal R bevat een flexibel Framework en uitgebreide analyses voor tijdreeks gegevens.

## <a name="get-the-data"></a>De gegevens ophalen

In deze zelf studie gebruikt u de productie-en prijs gegevens van Californië zuivel, waaronder maandelijkse gegevens over de productie van verschillende zuivel producten en de prijs van melkvet, een bench Mark-basis.

De gegevens die in dit artikel worden gebruikt, samen met R-scripts, kunnen worden gedownload van [MachineLearningSamples-notebooks/Studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples). De gegevens in het bestand `cadairydata.csv` werden oorspronkelijk gesynthesizerd op basis van de informatie die beschikbaar is op de site van de University of Wisconsin- [markten](https://dairymarkets.com).

## <a name="interact-with-r-language-in-machine-learning-studio-classic"></a><a id="mlstudio"></a>Communiceren met R-taal in Machine Learning Studio (klassiek)

In deze sectie maakt u kennis met enkele basis principes van interactie met de programmeer taal R in de omgeving Machine Learning Studio (klassiek). De R-taal biedt een krachtig hulp programma voor het maken van aangepaste modules voor analyse en gegevens manipulatie in de omgeving Machine Learning Studio (klassiek).

We gebruiken RStudio om R-code op een kleine schaal te ontwikkelen, te testen en op te sporen. Deze code wordt vervolgens geknipt en geplakt in een module voor het [uitvoeren van R-scripts][execute-r-script] die kan worden uitgevoerd in machine learning Studio (klassiek).

### <a name="the-execute-r-script-module"></a>De module R-script uitvoeren

In Machine Learning Studio (klassiek) worden R-scripts uitgevoerd in de module voor het uitvoeren van een [r-script][execute-r-script] . Hier wordt een voor beeld gegeven van de module voor het [uitvoeren van R-scripts][execute-r-script] in machine learning Studio (klassiek).

 ![Scherm opname van R-programmeer taal: de module voor het uitvoeren van R-scripts geselecteerd in Machine Learning Studio (klassiek).](./media/r-quickstart/fig1.png)

In de vorige afbeelding ziet u enkele van de belangrijkste onderdelen van de Machine Learning Studio-omgeving (klassiek) voor het werken met de module voor het [uitvoeren van R-scripts][execute-r-script] :

* De modules in het experiment worden weer gegeven in het middelste deel venster.
* Het bovenste deel venster bevat een venster dat u kunt gebruiken om uw R-scripts weer te geven en te bewerken.
* Het onderste gedeelte van het rechter deel venster bevat enkele eigenschappen van het [R-script uitvoeren][execute-r-script]. U kunt de fout-en uitvoer logboeken weer geven door de juiste gebieden in dit deel venster te selecteren.

In de rest van dit artikel bespreken we het [Execute R-script][execute-r-script] in meer detail.

Wanneer u werkt met complexe R-functies, raden we u aan om te bewerken, testen en fouten op te sporen in RStudio. Net als bij elke ontwikkeling van software breidt u de code stapsgewijs uit en test u deze op kleine, eenvoudige test cases. Knip en plak vervolgens uw functies in het R-Script venster van de module voor het uitvoeren van een [r-script][execute-r-script] . Met deze aanpak kunt u de RStudio-Integrated Development Environment (IDE) en de kracht van Machine Learning Studio (klassiek) benutten.

#### <a name="execute-r-code"></a>R-code uitvoeren

Alle R-code in de [script module Execute r][execute-r-script] wordt uitgevoerd wanneer u het experiment uitvoert door de knop **uitvoeren** te selecteren. Wanneer de uitvoering is voltooid, wordt er een vinkje weer gegeven op het pictogram [R-script uitvoeren][execute-r-script] .

#### <a name="defensive-r-coding-for-machine-learning-studio-classic"></a>Verdediging R-code ring voor Machine Learning Studio (klassiek)

Als u R-code ontwikkelt voor, bijvoorbeeld een webservice met behulp van Machine Learning Studio (klassiek), moet u zeker plannen hoe uw code zal omgaan met een onverwachte gegevens invoer en uitzonde ringen. Om duidelijkheid te houden, hebben we in de meeste code voorbeelden niet veel in de manier van controle of uitzonde ringen opgenomen. Tijdens het uitvoeren van deze procedure bieden we verschillende voor beelden van functies met behulp van de functie voor het afhandelen van uitzonde ringen.

Als u een meer volledige behandeling van de verwerking van R-uitzonde ringen nodig hebt, leest u de toepasselijke secties van het boek door Wickham vermeld in [verdere Lees bewerkingen](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>Fouten opsporen en testen R in Machine Learning Studio (klassiek)

Test en fout opsporing van uw R-code op een kleine schaal in RStudio. Er zijn ook gevallen waarin u R-code problemen voor het uitvoeren van een [r-script][execute-r-script] zelf moet volgen. Daarnaast is het raadzaam om de resultaten in Machine Learning Studio (klassiek) te controleren.

Uitvoer van de uitvoering van uw R-code en op het Machine Learning Studio (klassiek)-platform wordt voornamelijk in output. log gevonden. Er is een fout opgetreden bij het maken van een aantal aanvullende gegevens.

Als er een fout optreedt in Machine Learning Studio (klassiek) tijdens het uitvoeren van uw R-code, moet u eerst de volgende stappen volgen om te kijken naar de fout. log. Dit bestand kan nuttige fout berichten bevatten om u te helpen uw fout te begrijpen en te verhelpen. Als u fout. log wilt weer geven, selecteert u **fout logboek weer geven** in het deel venster Eigenschappen voor het [script uitvoeren][execute-r-script] dat de fout bevat.

We hebben bijvoorbeeld de volgende R-code, met een niet-gedefinieerde variabele y, in een [Execute r-script][execute-r-script] module uitgevoerd.

```r
x <- 1.0
z <- x + y
```

Deze code kan niet worden uitgevoerd, wat resulteert in een fout. Als u het **fouten logboek weer geven** in het deel venster Eigenschappen selecteert, wordt de volgende weer gave gegenereerd.

  ![Scherm afbeelding met een pop-upvenster met fout berichten.](./media/r-quickstart/fig2.png)

Het lijkt erop dat er in output. log moet worden gezocht om het R-fout bericht te zien. Selecteer de module [R-script uitvoeren][execute-r-script] en selecteer vervolgens het item **uitvoer weer geven. logboek** in het deel venster Eigenschappen aan de rechter kant. Er wordt een nieuw browser venster geopend en het volgende fout bericht wordt weer gegeven.

```output
[Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
---------- Start of error message from R ----------
object 'y' not found


object 'y' not found
----------- End of error message from R -----------
```

Dit fout bericht bevat geen verrassingen en identificeert het probleem duidelijk.

Als u de waarde van een object in R wilt controleren, kunt u deze waarden afdrukken naar het bestand output. log. De regels voor het onderzoeken van object waarden zijn in wezen hetzelfde als in een interactieve R-sessie. Als u bijvoorbeeld de naam van een variabele op een regel opgeeft, wordt de waarde van het object afgedrukt naar het bestand output. log.

#### <a name="packages-in-machine-learning-studio-classic"></a>Pakketten in Machine Learning Studio (klassiek)

Machine Learning Studio (klassiek) wordt geleverd met meer dan 350 vooraf geïnstalleerde R-taal pakketten. U kunt de volgende code in de module voor het [uitvoeren van R-scripts][execute-r-script] gebruiken om een lijst met vooraf geïnstalleerde pakketten op te halen.

```r
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Lees de informatie over als u de laatste regel van deze code op dit moment niet begrijpt. In de rest van dit artikel wordt uitgebreide informatie over het gebruik van R in de omgeving Machine Learning Studio (Classic) besproken.

### <a name="introduction-to-rstudio"></a>Inleiding tot RStudio

RStudio is een veel gebruikte IDE voor R. We gebruiken RStudio om een deel van de R-code die in deze hand leiding wordt gebruikt, te bewerken, te testen en op te lossen. Nadat de R-code is getest en klaar is, kunt u uit de RStudio-editor knippen en plakken in een Machine Learning Studio (klassiek) [R-script module uitvoeren][execute-r-script] .

Als u de R-programmeer taal niet op uw computer hebt geïnstalleerd, doet u dat nu. Gratis down loads van de open source R-taal zijn beschikbaar op het [uitgebreide r-archief netwerk (kranen)](https://www.r-project.org/). Down loads zijn beschikbaar voor Windows, macOS en Linux/UNIX. Kies een nabijgelegen mirror en volg de download instructies. Daarnaast bevat KRANen veel nuttige analyse-en gegevens manipulatie pakketten.

Als u niet bekend bent met RStudio, moet u de desktop versie downloaden en installeren. U kunt de RStudio-down loads voor Windows, macOS en Linux/UNIX vinden op [RStudio](http://www.rstudio.com/products/RStudio/). Volg de instructies voor het installeren van RStudio op uw desktop computer.

Een zelf studie Inleiding tot RStudio is beschikbaar op [het gebruik van de RSTUDIO IDE](https://support.rstudio.com/hc/sections/200107586-Using-RStudio).

Zie de [hand leiding voor RStudio-documentatie](#appendixa)voor meer informatie over het gebruik van RStudio.

## <a name="get-data-in-and-out-of-the-execute-r-script-module"></a><a id="scriptmodule"></a>Gegevens ophalen uit en uit de module voor het uitvoeren van R-scripts

In deze sectie wordt uitgelegd hoe u gegevens in en uit de module voor het [uitvoeren van R-script][execute-r-script] kunt ophalen. Ook wordt uitgelegd hoe u verschillende gegevens typen kunt verwerken die in en uit de module voor het [uitvoeren van R-script][execute-r-script] worden gelezen.

De volledige code voor deze sectie bevindt zich in [MachineLearningSamples-notebooks/Studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="load-and-check-data"></a>Gegevens laden en controleren

#### <a name="load-the-dataset"></a><a id="loading"></a>De gegevensset laden

U begint met het laden van het **csdairydata.csv** bestand in machine learning Studio (klassiek).

1. Start uw Machine Learning Studio-omgeving (klassiek).
1. Selecteer **+ Nieuw** in de linkerbenedenhoek van het scherm en selecteer **gegevensset**.
1. Selecteer een **lokaal bestand** en selecteer vervolgens **Bladeren** om het bestand te selecteren.
1. Zorg ervoor dat u **Algemeen CSV-bestand met header (. CSV)** hebt geselecteerd als het type voor de gegevensset.
1. Schakel het selectie vakje in.
1. Nadat de gegevensset is geüpload, ziet u de nieuwe gegevensset wanneer u het tabblad **gegevens sets** selecteert.

#### <a name="create-an-experiment"></a>Een experiment maken

Nu we een aantal gegevens hebben in Machine Learning Studio (klassiek), moeten we een experiment maken om de analyse uit te voeren.  

1. Selecteer **+ Nieuw** in de linkerbenedenhoek van het scherm **en selecteer een**  >  **leeg experiment** experimenteren.
1. Noem uw experiment door het experiment dat boven aan de pagina is **gemaakt** , te selecteren en te wijzigen. U kunt dit bijvoorbeeld wijzigen in **CA-zuivel analyse**.
1. Selecteer aan de linkerkant van de pagina Experimenteer **opgeslagen gegevens sets** met  >  **mijn gegevens sets**. U ziet het **cadairydata.csv** bestand dat u eerder hebt geüpload.
1. Sleep **csdairydata.csv gegevensset** naar het experiment.
1. Voer in het vak **Zoek proef items** bovenaan in het linkerdeel venster de opdracht [R-script uitvoeren][execute-r-script]in. De module wordt weer gegeven in de lijst zoeken.
1. Sleep de module [R-script uitvoeren][execute-r-script] naar uw pallet.
1. Verbind de uitvoer van **csdairydata.csv gegevensset** met de meest linkse invoer (**Dataset1**) van het [R-script uitvoeren][execute-r-script].
1. Selecteer **Opslaan**.

Op dit moment moet uw experiment er ongeveer als volgt uitzien.

![Diagram waarin het analyse experiment van de CA-zuivel wordt weer gegeven met de script module gegevensset en execute R.](./media/r-quickstart/fig3.png)


#### <a name="check-on-the-data"></a>De gegevens controleren

Laten we eens kijken naar de gegevens die we in ons experiment hebben geladen. Selecteer in het experiment de uitvoer van de **cadairydata.csv gegevensset** en selecteer **visualiseren**. U ziet iets als deze samen vatting.

![Scherm afbeelding met een samen vatting van de cadairydata.csv gegevensset.](./media/r-quickstart/fig4.png)

In deze weer gave ziet u veel nuttige informatie. We kunnen de eerste verschillende rijen van de gegevensset zien. Als we een kolom selecteren, ziet u in de sectie **Statistieken** meer informatie over de kolom. De rij **functie type** laat bijvoorbeeld zien welke gegevens typen machine learning Studio (klassiek) aan de kolom zijn toegewezen. Controleer deze weer gave voordat u begint met het uitvoeren van een serieus werk.

### <a name="first-r-script"></a>Eerste R-script

Laten we een eenvoudig eerste R-script maken om te experimenteren binnen Machine Learning Studio (klassiek). Het volgende script is gemaakt en getest in RStudio.

```r
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
str(cadairydata)
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

U moet dit script nu overdragen aan Machine Learning Studio (klassiek). U kunt knippen en plakken, maar in dit geval moet u het R-script overdragen via een zip-bestand.

### <a name="data-input-to-the-execute-r-script-module"></a>Gegevens invoer van de module voor het uitvoeren van R-scripts

Laten we eens kijken naar de invoer voor de module [R-script uitvoeren][execute-r-script] . In dit voor beeld lezen we de gegevens van Californië zuivel in de module voor het [uitvoeren van R-scripts][execute-r-script] .

Er zijn drie mogelijke invoer voor de module voor het uitvoeren van een [R-script][execute-r-script] . U kunt elk van deze invoer gebruiken, afhankelijk van uw toepassing. U kunt ook een R-script gebruiken dat helemaal geen invoer in beslag neemt.

We gaan elk van deze invoer eens bekijken, van links naar rechts. U kunt de namen van elk van de invoer zien door de cursor over de invoer te plaatsen en de knop info te lezen.

#### <a name="script-bundle"></a>Script bundel

Met de invoer van de script bundel kunt u de inhoud van een zip-bestand door geven aan de [script module Execute R][execute-r-script] . U kunt een van de volgende opdrachten gebruiken om de inhoud van het zip-bestand te lezen in de R-code.

```r
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> Machine Learning Studio (klassiek) behandelt bestanden in het zip-bestand alsof ze zich in de map src/directory bevinden, dus u moet de bestands namen met deze mapnaam voor voegsel. Als het zip-bestand bijvoorbeeld de bestanden `yourfile.R` en `yourData.rdata` in de hoofdmap van het zip-bestand bevat, kunt u deze bestanden adresseren als `src/yourfile.R` en `src/yourData.rdata` Wanneer u `source` en gebruikt `load` .

[De gegevensset voor het](#loading)laden van gegevens sets is al besproken. Nadat u het R-script in de vorige sectie hebt gemaakt en getest, voert u de volgende stappen uit.

1. Sla het R-script op in een. R-bestand. Het script bestand simpleplot wordt aangeroepen **. R**. Dit is what's in het bestand:

   ```r
   ## Only one of the following two lines should be used
   ## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
   ## If in RStudio, use the second line with read.csv()
   cadairydata <- maml.mapInputPort(1)
   # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
   str(cadairydata)
   pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
   ## The following line should be executed only when running in
   ## Azure Machine Learning Studio (classic)
   maml.mapOutputPort('cadairydata')
   ```

1. Maak een zip-bestand en kopieer uw script naar dit zip-bestand. In Windows kunt u met de rechter muisknop op het bestand klikken en **verzenden naar**  >  **gecomprimeerde map** selecteren. Met deze actie maakt u een nieuw zip-bestand dat de **simpleplot bevat. R** -bestand.

1. Voeg uw bestand toe aan de **gegevens sets** in machine learning Studio (klassiek) en geef het type op als **zip**. U ziet nu het zip-bestand in de gegevens sets.

1. Sleep het zip-bestand van **gegevens sets** naar het **ml Studio (klassiek)-canvas**.

1. Koppel de uitvoer van het pictogram **zip-gegevens** aan de invoer van de **script bundel** van de module voor het uitvoeren van een [R-script][execute-r-script] .

1. Voer de `source()` functie in met de naam van het zip-bestand in het code venster voor de module voor het uitvoeren van een [R-script][execute-r-script] . In dit geval zijn we ingevoerd `source("src/simpleplot.R")` .

1. Selecteer **Opslaan**.

Nadat deze stappen zijn voltooid, voert de [script module Execute r][execute-r-script] het R-script uit in het zip-bestand wanneer het experiment wordt uitgevoerd. Op dit moment moet uw experiment er ongeveer als volgt uitzien.

![Diagram waarin een experiment wordt weer gegeven met behulp van een gezipt R-script.](./media/r-quickstart/fig6.png)

#### <a name="dataset1"></a>Dataset1

U kunt een rechthoekige tabel met gegevens door geven aan uw R-code met behulp van de Dataset1-invoer. In ons eenvoudige script leest de `maml.mapInputPort(1)` functie de gegevens van poort 1. Deze gegevens worden vervolgens toegewezen aan de naam van een data frame-variabele in uw code. In ons eenvoudige script voert de eerste regel code de toewijzing uit.

```r
cadairydata <- maml.mapInputPort(1)
```

Voer uw experiment uit door de knop **uitvoeren** te selecteren. Wanneer de uitvoering is voltooid, selecteert u de module [R-script uitvoeren][execute-r-script] en selecteert u vervolgens **uitvoer logboek weer geven** in het deel venster Eigenschappen. Er wordt een nieuwe pagina weer gegeven in de browser die de inhoud van het bestand output. log weergeeft. Wanneer u omlaag schuift, ziet u iets zoals in de volgende uitvoer.

```output
[ModuleOutput] InputDataStructure
[ModuleOutput]
[ModuleOutput] {
[ModuleOutput]  "InputName":Dataset1
[ModuleOutput]  "Rows":228
[ModuleOutput]  "Cols":9
[ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
[ModuleOutput] }
```

Verder is de pagina meer gedetailleerde informatie over de kolommen. dit ziet er ongeveer uit zoals in de volgende uitvoer.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput]
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput]
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput]
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput]
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput]
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput]
[ModuleOutput]  $ Month            : chr  "Jan" "Feb" "Mar" "Apr" ...
[ModuleOutput]
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput]
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput]
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput]
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
```

Deze resultaten zijn voornamelijk zoals verwacht, met 228 waarnemingen en 9 kolommen in de data frame. De kolom namen, het gegevens Type R en een voor beeld van elke kolom worden weer gegeven.

> [!NOTE]
> Dezelfde gedrukte uitvoer is eenvoudig beschikbaar vanuit de R-uitvoer van de module voor het uitvoeren van een [r-script][execute-r-script] . We bespreken de uitvoer van de module voor het [uitvoeren van R-scripts][execute-r-script] in de volgende sectie.  

#### <a name="dataset2"></a>Dataset2

Het gedrag van de Dataset2-invoer is gelijk aan die van Dataset1. Met deze invoer kunt u een tweede rechthoekige tabel met gegevens door geven aan uw R-code. De functie `maml.mapInputPort(2)` , met het argument 2, wordt gebruikt om deze gegevens door te geven.  

### <a name="execute-r-script-outputs"></a>Uitvoer van R-script uitvoeren

#### <a name="output-a-dataframe"></a>Een data frame uitvoeren

U kunt de inhoud van een R-data frame uitvoeren als een rechthoekige tabel via de Dataset1-poort van het resultaat met behulp van de `maml.mapOutputPort()` functie. In het eenvoudige R-script wordt deze actie uitgevoerd door de volgende regel.

```r
maml.mapOutputPort('cadairydata')
```

Nadat u het experiment hebt uitgevoerd, selecteert u het resultaat Dataset1 uitvoer poort en selecteert u vervolgens **visualiseren**. U ziet iets als in dit voor beeld.

![Scherm opname van de weer gave van de uitvoer van de California zuivel-gegevens.](./media/r-quickstart/fig7.png)

Deze uitvoer ziet er precies zo uit als verwacht.

### <a name="r-device-output"></a>R-apparaat-uitvoer

De uitvoer van het apparaat van de [script module Execute R][execute-r-script] bevat berichten en grafische uitvoer. Standaard uitvoer en standaard fout berichten van R worden verzonden naar de uitvoer poort van het R-apparaat.

Als u de uitvoer van het R-apparaat wilt weer geven, selecteert u de poort en selecteert u vervolgens **visualiseren**. De standaard uitvoer en de standaard fout van het R-script worden hier weer gegeven.

![Scherm afbeelding met de standaard uitvoer en de standaard fout van de R-poort van het apparaat.](./media/r-quickstart/fig8.png)

Als u naar beneden schuift, zien we de grafische uitvoer van het R-script.

![Scherm afbeelding met de grafische uitvoer van de R-poort van het apparaat.](./media/r-quickstart/fig9.png)

## <a name="data-filtering-and-transformation"></a><a id="filtering"></a>Gegevens filteren en trans formatie

In deze sectie worden enkele basis bewerkingen voor gegevens filtering en-trans formatie uitgevoerd op de gegevens van Californië zuivel. Aan het einde van deze sectie hebben we gegevens in een indeling die geschikt is voor het bouwen van een analytisch model.

In deze sectie worden verschillende algemene taken voor gegevens reiniging en-trans formatie uitgevoerd: type trans formatie, filteren op dataframes, toevoegen van nieuwe berekende kolommen en waarde-trans formaties. Deze achtergrond zou u kunnen helpen bij het oplossen van de vele variaties die in praktijk problemen optreden.

De volledige R-code voor deze sectie is beschikbaar in [MachineLearningSamples-notebooks/Studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="type-transformations"></a>Type trans formaties

Nu we de California zuivel-gegevens kunnen lezen in de R-code in de script module voor het [uitvoeren van r][execute-r-script] , moeten we ervoor zorgen dat de gegevens in de kolommen het beoogde type en de juiste indeling hebben.

R is een dynamisch getypeerde taal, wat betekent dat de gegevens typen zo nodig naar een andere worden gedwongen. De atomische gegevens typen in R bevatten numeriek, logisch en teken. Het type factor wordt gebruikt om categorische-gegevens op te slaan. Zie voor meer informatie over gegevens typen de verwijzingen in [verdere Lees bewerkingen](#appendixb).

Wanneer tabellaire gegevens in R van een externe bron worden gelezen, is het altijd een goed idee om de resulterende typen in de kolommen te controleren. Mogelijk wilt u een kolom van het type teken, maar in veel gevallen wordt de kolom weer gegeven als factor of andersom. In andere gevallen moet een kolom die u beschouwt numeriek worden weer gegeven door teken gegevens, bijvoorbeeld "1,23" in plaats van 1,23 als een getal met drijvende komma.

Gelukkig kunt u eenvoudig een type naar een andere omzetten, zolang de toewijzing mogelijk is. U kunt bijvoorbeeld ' Nevada ' niet converteren naar een numerieke waarde, maar u kan deze converteren naar een factor (categorische-variabele). Een ander voor beeld: u kunt een numerieke waarde 1 omzetten in een teken ' 1 ' of een factor.

De syntaxis voor een van deze conversies is eenvoudig: `as.datatype()` . Deze functies voor type conversie zijn onder andere de volgende functies:

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Op de gegevens typen van de kolommen die we in de vorige sectie hebben ingevoerd, zijn alle kolommen van het type Numeriek. De uitzonde ring is de kolom met het label month, een type teken. Laten we dit type converteren naar een factor en de resultaten testen.

De regel die de spreidings plot-matrix heeft gemaakt, is verwijderd en er is een lijn toegevoegd om de kolom month om te zetten in een factor. In dit experiment knipt en plakt u de R-code in het code venster van de module voor het uitvoeren van een [R-script][execute-r-script] . U kunt het zip-bestand ook bijwerken en uploaden naar Machine Learning Studio (klassiek), maar deze optie heeft verschillende stappen.

```r
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(cadairydata$Month)
str(cadairydata) # Check the result
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

We gaan deze code uitvoeren en kijken naar het uitvoer logboek voor het R-script. De relevante gegevens uit het logboek worden hier weer gegeven.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 14 levels "Apr","April",..: 6 5 9 1 11 8 7 3 14 13 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

Voor het type voor maand moet nu de **factor w/14 niveaus** worden gedicteerd. Dit type is een probleem omdat er slechts 12 maanden in het jaar zijn. U kunt ook controleren of het type in **visualiseren** van de **categorische** van het resultaat van de gegevensset wordt weer geven.

Het probleem is dat de maand kolom niet systematisch is gecodeerd. In sommige gevallen wordt een maand april genoemd, en in andere situaties wordt deze afgekort tot apr. We kunnen dit probleem oplossen door de teken reeks te verkleinen tot drie tekens. De regel code ziet er nu uit als in het volgende voor beeld.

```r
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Voer het experiment opnieuw uit en Bekijk het uitvoer logboek. De verwachte resultaten worden hier weer gegeven.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```


Onze factor variabele heeft nu de gewenste 12 niveaus.

### <a name="basic-dataframe-filtering"></a>Eenvoudige data frame-filtering

R dataframes ondersteunen krachtige filter mogelijkheden. Gegevens sets kunnen worden subsetted met behulp van logische filters voor rijen en kolommen. In veel gevallen is het complexe filter criterium vereist. Zie de verwijzingen in [verdere Lees bewerkingen](#appendixb)voor uitgebreide voor beelden van het filteren van dataframes.

Er zijn twee filters die in onze gegevensset moeten worden uitgevoerd. Als u de kolommen in de cadairydata-data frame bekijkt, ziet u twee overbodige kolommen. De eerste kolom bevat een rijnummer. Dit is niet erg nuttig. De tweede kolom, jaar. maand, bevat redundante informatie. We kunnen deze kolommen eenvoudig uitsluiten met behulp van de volgende R-code.

> [!NOTE]
> Vanaf nu in deze sectie wordt de extra code weer gegeven die we toevoegen in de module voor het [uitvoeren][execute-r-script] van een R-script. We voegen elke nieuwe regel toe *voor* de `str()` functie. Deze functie wordt gebruikt om de resultaten in Machine Learning Studio (klassiek) te controleren.

We voegen de volgende regel toe aan de R-code in de module voor het uitvoeren van een [r-script][execute-r-script] .

```r
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Voer deze code uit in uw experiment en controleer het resultaat van het uitvoer logboek. Deze resultaten worden hier weer gegeven.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  7 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

Nu worden de verwachte resultaten weer geven.

### <a name="add-a-new-column"></a>Een nieuwe kolom toevoegen

Als u Time Series-modellen wilt maken, is het handig om een kolom te hebben die de maanden sinds het begin van de tijd reeks bevat. We maken de nieuwe kolom maand. Count.

Voor het indelen van de code maken we onze eerste eenvoudige functie, `num.month()` . Vervolgens worden deze functies toegepast om een nieuwe kolom in de data frame te maken. De nieuwe code is als volgt:

```r
## Create a new column with the month count
## Function to find the number of months from the first
## month of the time series
num.month <- function(Year, Month) {
  ## Find the starting year
  min.year  <- min(Year)

  ## Compute the number of months from the start of the time series
  12 * (Year - min.year) + Month - 1
}

## Compute the new column for the dataframe
cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)
```

Voer nu het bijgewerkte experiment uit en gebruik het uitvoer logboek om de resultaten weer te geven. Deze resultaten worden hier weer gegeven.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```


Het lijkt alsof alles werkt. We hebben de nieuwe kolom met de verwachte waarden in onze data frame.

### <a name="value-transformations"></a>Waarde-trans formaties

In deze sectie worden enkele eenvoudige trans formaties uitgevoerd op de waarden in sommige kolommen van de data frame. De R-taal ondersteunt bijna wille keurige waarden trans formaties. Zie de verwijzingen in [verdere Lees bewerkingen](#appendixb)voor meer voor beelden.

Als u de waarden in de samen vattingen van de data frame bekijkt, ziet u hier iets oneven. Is er meer ijs dan in Californië geproduceerde melk? Nee, uiteraard niet. Het probleem is dat de eenheden verschillend zijn. De prijs is in eenheden van Amerikaanse ponden, melk bevindt zich in eenheden van 1.000.000 Euro's, ijs in eenheden van 1.000 Amerikaanse gallons en huis kaas in eenheden van 1.000 Euro's. Aangenomen dat ijs ongeveer 6,5 pond per gallon weegt, kunnen we de vermenigvuldiging eenvoudig uitvoeren om deze waarden om te zetten, zodat ze allemaal gelijk zijn aan alle eenheden van 1.000 Euro's.

Voor ons prognose model gebruiken we een multiplicative-model voor trend en seizoen aanpassing van deze gegevens. Met een logboek transformatie kan ons een lineair model gebruiken, waarmee dit proces wordt vergemakkelijkt. We kunnen de logboek transformatie Toep assen in dezelfde functie als de vermenigvuldiger wordt toegepast.

In de volgende code definieert u een nieuwe functie `log.transform()` en past u deze toe op de rijen die de numerieke waarden bevatten. De `Map()` functie R wordt gebruikt om de `log.transform()` functie toe te passen op de geselecteerde kolommen van de data frame. De `Map()` functie is vergelijkbaar met `apply()` , maar biedt meer dan een lijst met argumenten voor de functie. Houd er rekening mee dat een lijst met vermenigvuldigers het tweede argument voor de `log.transform()` functie levert. De `na.omit()` functie wordt gebruikt als een beetje opschoning om ervoor te zorgen dat er geen ontbrekende of niet-gedefinieerde waarden in de data frame zijn.

```r
log.transform <- function(invec, multiplier = 1) {
  ## Function for the transformation, which is the log
  ## of the input value times a multiplier

  warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
                       "ERROR: Arguments to function log.transform must be greate than zero",
                       "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
                       "ERROR: Invalid time seies value encountered in function log.transform"
                       )

  ## Check the input arguments
  if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
  if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
  if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

  ## Wrap the multiplication in tryCatch
  ## If there is an exception, print the warning message to
  ## standard error and return NA
  tryCatch(log(multiplier * invec),
           error = function(e){warning(warningmessages[4]); NA})
}


## Apply the transformation function to the 4 columns
## of the dataframe with production data
multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

## Get rid of any rows with NA values
cadairydata <- na.omit(cadairydata)  
```

Er is een beetje wat er gebeurt in de `log.transform()` functie. Met de meeste van deze code wordt gecontroleerd op mogelijke problemen met de argumenten of het verwerken van uitzonde ringen. deze kunnen zich nog steeds voordoen tijdens de berekeningen. Er zijn slechts een paar regels van deze code de berekeningen.

Het doel van het Program meren van defensie is om te voor komen dat er een storing optreedt in een enkele functie waardoor de verwerking niet kan worden voortgezet. Een plotselinge uitval van een langlopende analyse kan frustrerend zijn voor gebruikers. Om deze situatie te voor komen, moeten er standaard retour waarden worden gekozen, waardoor de schade wordt beperkt tot downstream-verwerking. Er wordt ook een bericht gegenereerd om gebruikers te waarschuwen dat er iets verkeerd is gegaan.

Als u niet wordt gebruikt voor verdedigings programmering in R, is al deze code mogelijk overweldigend. Laten we de belangrijkste stappen door lopen:

1. Er wordt een vector van vier berichten gedefinieerd. Deze berichten worden gebruikt voor het communiceren van informatie over een aantal van de mogelijke fouten en uitzonde ringen die kunnen optreden met deze code.
1. We retour neren de waarde N.V.T. voor elke aanvraag. Er zijn veel andere mogelijkheden die mogelijk minder neven effecten hebben. We kunnen bijvoorbeeld een vector van nullen of de oorspronkelijke invoer vector retour neren.
1. Controles worden uitgevoerd op de argumenten voor de functie. Als er een fout wordt gedetecteerd, wordt er in elk geval een standaard waarde geretourneerd en wordt er een bericht gegenereerd door de `warning()` functie. We gebruiken dit `warning()` in plaats van `stop()` omdat de laatste keer dat de uitvoering wordt beëindigd, wat we proberen te voor komen. Deze code wordt geschreven in een procedure stijl, omdat in dit geval een functionele benadering gecompliceerd en onzichtbaar is.
1. De logboek berekeningen worden ingepakt `tryCatch()` , zodat uitzonde ringen geen plotselinge onderbreking van de verwerking veroorzaken. Zonder `tryCatch()` , worden de meeste fouten die zijn opgetreden door R-functies, veroorzaakt door een stop signaal. Dit betekent alleen dat.

Voer deze R-code uit in uw experiment en Bekijk de gedrukte uitvoer in het bestand output. log. U ziet nu de getransformeerde waarden van de vier kolommen in het logboek, zoals hier wordt weer gegeven.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

We zien dat de waarden zijn getransformeerd. Zuivel productie overschrijdt nu de productie van zuivel producten, waardoor we nu een logaritmische schaal bekijken.

De gegevens worden op dit moment opgeschoond en we zijn klaar voor een model lering. Als u de visualisatie samenvatting bekijkt voor de uitvoer van de resultaten gegevensset van de [script module Execute R][execute-r-script] , ziet u dat de kolom maand categorische is met 12 unieke waarden, die net als we gewenst zijn.

## <a name="time-series-objects-and-correlation-analysis"></a><a id="timeseries"></a>Tijd reeks objecten en correlatie analyse

In deze sectie gaan we een paar eenvoudige R Time Series-objecten verkennen en de correlaties tussen enkele variabelen analyseren. Ons doel is het uitvoeren van een data frame die de informatie over de Pairwise correlatie bevat op verschillende lags.

De volledige R-code voor deze sectie bevindt zich in [MachineLearningSamples-notebooks/Studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="time-series-objects-in-r"></a>Time Series-objecten in R

Zoals al vermeld, is de tijd reeks een reeks gegevens waarden die op tijd worden geïndexeerd. R-tijd reeks objecten worden gebruikt voor het maken en beheren van de time-index. Er zijn verschillende voor delen voor het gebruik van tijd reeks-objecten. Met tijdreeks objecten kunt u veel meer informatie over het beheren van de time series-index waarden die zijn ingekapseld in het object. Daarnaast kunt u met Time Series-objecten gebruikmaken van de vele tijdreeks methoden voor het uitzetten, afdrukken, model leren, enzovoort.

De POSIXct-klasse van de tijd reeks wordt meestal gebruikt en is relatief eenvoudig. Deze tijd reeks klasse meet tijd vanaf het begin van de epoche, 1 januari 1970. We gebruiken POSIXct Time Series-objecten in dit voor beeld. Andere veelgebruikte R-Time Series object klassen zijn Zoo and XTS (Extensible time series).

### <a name="time-series-object-example"></a>Voor beeld van een time Series-object

Laten we aan de slag met ons voor beeld. Sleep een nieuwe module voor het uitvoeren van een [R-script][execute-r-script] naar uw experiment. Verbind het resultaat Dataset1 uitvoer poort van de bestaande [Execute r-script][execute-r-script] module naar de Dataset1-invoer poort van de nieuwe module voor het uitvoeren van een [r-script][execute-r-script] .

Zoals we voor de eerste voor beelden hebben gedaan, gaan we verder met het voor beeld. Op sommige punten worden alleen de incrementele extra regels van de R-code weer gegeven bij elke stap.

#### <a name="read-the-dataframe"></a>De data frame lezen

Als eerste stap lezen we in een data frame en zorgen we ervoor dat de verwachte resultaten worden opgehaald. Voer de volgende code uit om de taak uit te voeren.

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Voer nu het experiment uit. Het logboek van de nieuwe shape voor het uitvoeren R-script moet er als volgt uitzien.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
```

Deze gegevens zijn van de verwachte typen en notatie. Nu is de kolom maand van het type factor en het verwachte aantal niveaus.

#### <a name="create-a-time-series-object"></a>Een time Series-object maken

We moeten een tijd reeks object toevoegen aan onze data frame. Vervang de huidige code door de volgende code, waarmee een nieuwe kolom van klasse POSIXct wordt toegevoegd.

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

Controleer nu het logboek. Dit moet eruitzien als in dit voor beeld.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...
```

In de samen vatting van de nieuwe kolom is de klasse POSIXct te zien.

### <a name="explore-and-transform-the-data"></a>De gegevens verkennen en transformeren

Laten we een aantal variabelen in deze gegevensset verkennen. Een spreidings plot-matrix is een goede manier om een snel uiterlijk te maken. De `str()` functie in de vorige R-code wordt vervangen door de volgende regel.

```r
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Voer deze code uit en Bekijk wat er gebeurt. Het waarnemings punt dat is geproduceerd op de poort R-apparaat moet er als volgt uitzien.

![Scherm afbeelding met de spreidings tekening matrix van geselecteerde variabelen.](./media/r-quickstart/fig17.png)

Er is een enige oneven structuur in de relaties tussen deze variabelen. Mogelijk komt deze structuur voort uit trends in de gegevens en uit het feit dat we de variabelen niet hebben gestandaardiseerd.

### <a name="correlation-analysis"></a>Correlatieanalyse

Om correlatie-analyses uit te voeren, moeten we zowel de-trend als de variabelen standaardiseren. We kunnen eenvoudigweg de R `scale()` -functie gebruiken, waarmee variabelen worden gecentreerd en geschaald. Deze functie kan sneller worden uitgevoerd. Maar laten we eens kijken naar een voor beeld van een verdedigings programmering in R.

De `ts.detrend()` volgende functie voert beide bewerkingen uit. De volgende twee regels code detrenden de gegevens en vervolgens worden de waarden genormaliseerd.

```r
ts.detrend <- function(ts, Time, min.length = 3){
  ## Function to de-trend and standardize a time series

  ## Define some messages if they are NULL  
  messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
                'ERROR: ts.detrend requires argument ts to be of type numeric',
                paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
                'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
                'ERROR: Detrend regression has failed in ts.detrend',
                'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
  )
  # Create a vector of zeros to return as a default in some cases
  zerovec  <- rep(length(ts), 0.0)

  # The input arguments are not of the same length, return ts and quit
  if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

  # If the ts is not numeric, just return a zero vector and quit
  if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

  # If the ts is too short, just return it and quit
  if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

  ## Check that the Time variable is of class POSIXct
  if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

  ## De-trend the time series by using a linear model
  ts.frame  <- data.frame(ts = ts, Time = Time)
  tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
           error = function(e){warning(messages[5]); zerovec})

  tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
             ts <- ts/stdev},
            error = function(e){warning(messages[6]); zerovec})

  ts
}  
## Apply the detrend.ts function to the variables of interest
df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

## Plot the results to look at the relationships
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")
```

Er is een beetje wat er gebeurt in de `ts.detrend()` functie. Met de meeste van deze code wordt gecontroleerd op mogelijke problemen met de argumenten of het verwerken van uitzonde ringen. deze kunnen zich nog steeds voordoen tijdens de berekeningen. Slechts een paar regels van deze code voeren feitelijk de berekeningen uit.

We hebben al een voor beeld van een verdedigings programma besproken in waarde-trans formaties. Beide reken blokken worden ingepakt in `tryCatch()` . Voor sommige fouten is het zinvol om de oorspronkelijke invoer vector te retour neren. In andere gevallen retour neren we een vector van nullen.

Houd er rekening mee dat de lineaire regressie die wordt gebruikt voor de trending een tijd reeks regressie is. De Voorspellings variabele is een time Series-object.

Nadat `ts.detrend()` is gedefinieerd, passen we het toe op de variabelen van belang in onze data frame. We moeten de resulterende lijst die is gemaakt met `lapply()` gegevens in de data frame, afdwingen met behulp van `as.data.frame()` . Vanwege de verdedigings aspecten van kan `ts.detrend()` het verwerken van een van de variabelen niet de juiste verwerking van de andere waarden verhinderen.

Met de laatste regel code wordt een Pairwise-spreidings tekening gemaakt. Na het uitvoeren van de R-code worden de resultaten van het spreidings plot hier weer gegeven.

![Scherm opname van het Pairwise-spreidings diagram van de getrendde en gestandaardiseerde tijd reeks.](./media/r-quickstart/fig18.png)

U kunt deze resultaten vergelijken met die in het vorige voor beeld. Als de trend is verwijderd en de variabelen zijn genormaliseerd, zien we een veel minder structuur in de relaties tussen deze variabelen.

De code voor het berekenen van de correlaties als R CCF-objecten is als volgt.

```r
## A function to compute pairwise correlations from a
## list of time series value vectors
pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
  ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
}

## A list of the pairwise indices
corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

## Compute the list of ccf objects
cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

cadairycorrelations
```

Als deze code wordt uitgevoerd, wordt hier het logboek gegenereerd.

```output
[ModuleOutput] Loading objects:
[ModuleOutput]   port1
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] [[1]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]    -1     0     1 
[ModuleOutput] 0.148 0.358 0.317 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[2]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.395 -0.186 -0.238 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[3]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.059 -0.089 -0.127 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[4]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]    -1     0     1 
[ModuleOutput] 0.140 0.294 0.293 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[5]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.002 -0.074 -0.124 
```

Er is een correlatie waarde voor elke vertraging. Geen van deze correlatie waarden is groot genoeg om significant te zijn. We kunnen concluderen dat elke variabele onafhankelijk kan worden model leren.

### <a name="output-a-dataframe"></a>Een data frame uitvoeren
We hebben de Pairwise-correlaties berekend als een lijst met R CCF-objecten. Dit geeft een deel van een probleem als resultaat van de uitvoer poort van de resulterende gegevensset is een data frame vereist. Verder is het CCF-object zelf een lijst en we willen alleen de waarden in het eerste element van deze lijst, de correlaties op de verschillende lags.

Met de volgende code worden de vertragings waarden geëxtraheerd uit de lijst met CCF-objecten, die zelf worden weer gegeven.

```r
df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
r.names  <- c("Corr Cot Cheese - Ice Cream",
              "Corr Cot Cheese - Milk Prod",
              "Corr Cot Cheese - Fat Price",
              "Corr Ice Cream - Mik Prod",
              "Corr Ice Cream - Fat Price",
              "Corr Milk Prod - Fat Price")

## Build a dataframe with the row names column and the
## correlation dataframe and assign the column names
outframe <- cbind(r.names, df.correlations)
colnames(outframe) <- c.names
outframe


## WARNING!
## The following line works only in Azure Machine Learning Studio (classic)
## When running in RStudio, this code will result in an error
#maml.mapOutputPort('outframe')
```

De eerste regel code is een beetje truc en enkele uitleg kan u helpen bij het begrijpen. U kunt aan de slag met het volgende:

1. De **[[** operator met het argument **1** selecteert de vector of correlaties in de lags van het eerste element van de lijst met CCF-objecten.
1. De `do.call()` functie past de `rbind()` functie toe op de elementen van de lijst die worden geretourneerd door `lapply()` .
1. `data.frame()`Met de functie wordt het resultaat dat is geproduceerd door `do.call()` naar een data frame, afgedwongen.

Houd er rekening mee dat de namen van de rijen in een kolom van de data frame staan. Hiermee behoudt u de namen van de rijen wanneer deze worden uitgevoerd vanuit het [script Execute R][execute-r-script].

Als de code wordt uitgevoerd, wordt de uitvoer die hier wordt weer gegeven, gegenereerd als u **visualiseren** selecteert om de uitvoer in de poort van het resultaat gegevensset weer te geven. De namen van de rijen bevinden zich in de eerste kolom, zoals bedoeld.

![Scherm opname van de resultaten uitvoer van de correlatie analyse.](./media/r-quickstart/fig20.png)

## <a name="time-series-example-seasonal-forecasting"></a><a id="seasonalforecasting"></a>Voor beeld van een tijd reeks: seizoen prognose

Onze gegevens bevinden zich nu in een formulier dat geschikt is voor analyse en we hebben vastgesteld dat er geen aanzienlijke correlaties tussen de variabelen zijn. We gaan nu een tijd reeks prognose model maken. Met dit model wordt de productie van Californië voor de 12 maanden van 2013.

Het prognose model bevat twee onderdelen, een trend component en een seizoen component. De volledige prognose is het product van deze twee onderdelen. Dit type model wordt een multiplicative-model genoemd. Het alternatief is een additief model. Er is al een logboek transformatie toegepast op de variabelen van interest, waardoor deze analyse kan worden ondertrekt.

De volledige R-code voor deze sectie bevindt zich in [MachineLearningSamples-notebooks/Studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="create-the-dataframe-for-analysis"></a>De data frame maken voor analyse

Voeg eerst een nieuwe module voor het [uitvoeren van R-scripts][execute-r-script] toe aan uw experiment. Koppel de uitvoer gegevensset van de bestaande [Execute R-script][execute-r-script] module aan de **Dataset1** -invoer van de nieuwe module. Het resultaat ziet er ongeveer uit als in dit voor beeld.

![Diagram waarin het experiment met de nieuwe methode voor het uitvoeren van de R-script module wordt weer gegeven.](./media/r-quickstart/fig21.png)

Net als bij de correlatie analyse die we zojuist hebben voltooid, moeten we een kolom met een POSIXct Time Series-object toevoegen. Met de volgende code wordt de kolom toegevoegd.

```r
# If running in Machine Learning Studio (classic), uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Voer deze code uit en Bekijk het logboek. Het resultaat moet eruitzien als in dit voor beeld.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...
```

Met dit resultaat gaan we onze analyse nu starten.

### <a name="create-a-training-dataset"></a>Een trainings gegevensset maken

Als de data frame is gebouwd, moeten we een trainings gegevensset maken. Deze gegevens omvatten alle waarnemingen, met uitzonde ring van de laatste 12 van het jaar 2013, wat onze test gegevensset is. Met de volgende code worden de data frame en de productie-en prijs variabelen van zuivel gemaakt. Vervolgens maken we een grafiek van de vier productie-en prijs variabelen. Een anonieme functie wordt gebruikt voor het definiëren van een aantal uitbrei ding voor het waarnemings punt en herhaalt vervolgens de lijst met andere twee argumenten met `Map()` . Als u denkt dat u hier een for-lus zou hebben gewerkt, bent u er goed aan. Maar omdat R een functionele taal is, kijken we naar een functionele benadering.

```r
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

Als de code wordt uitgevoerd, wordt de reeks time series-grafieken gegenereerd op basis van de uitvoer van het R-apparaat dat hier wordt weer gegeven. De tijdsas bevindt zich in eenheden van datums, wat een goed voor deel is van de teken methode van de tijd reeks.

![Eerste tijds reeks voor de serie van Californië zuivel productie-en prijs gegevens.](./media/r-quickstart/unnamed-chunk-161.png)

![Tweede tijd reeks diagram van Californië zuivel productie-en prijs gegevens.](./media/r-quickstart/unnamed-chunk-162.png)

![Derde tijds reeks tekening van Californië zuivel productie-en prijs gegevens.](./media/r-quickstart/unnamed-chunk-163.png).

![Vierde fase van de serie Californië zuivel productie-en prijs gegevens](./media/r-quickstart/unnamed-chunk-164.png)

### <a name="a-trend-model"></a>Een trend model

Nu u een tijd reeks object hebt gemaakt en de gegevens hebt bekeken, kunt u beginnen met het maken van een trend model voor de productie gegevens van de California-melk. We kunnen een regressie voor tijd reeksen gebruiken. Het is duidelijk uit het waarnemings punt dat we meer dan een helling en snij punt nodig hebben om de waargenomen trend in de trainings gegevens nauw keurig te model leren.

Gezien de kleine schaal van de gegevens, maken we het model voor trend in RStudio en knip en plak je het resulterende model in Machine Learning Studio (klassiek). RStudio biedt een interactieve omgeving voor dit type interactieve analyse.

Bij de eerste poging proberen we een polynoom met een polynomiale regressie te maken met een macht van Maxi maal drie. Er is een echt risico dat dit soort modellen wordt overschreden. Het is raadzaam om voor waarden met hoge volg orde te voor komen. De `I()` functie belemmert de interpretatie van de inhoud (interpreteert de inhoud als is) en maakt het mogelijk een geïnterpreteerde functie te schrijven in een regressie vergelijking.

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

Deze functie genereert de volgende uitvoer.

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3),
##     data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.12667 -0.02730  0.00236  0.02943  0.10586
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 ***
## Time              1.63e-09   1.72e-10    9.47   <2e-16 ***
## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 *  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0418 on 212 degrees of freedom
## Multiple R-squared:  0.941,    Adjusted R-squared:  0.94
## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16
```

Van P-waarden ( `Pr(>|t|)` ) in deze uitvoer ziet u dat de vier Kante term mogelijk niet significant is. We gebruiken de `update()` functie om dit model te wijzigen door de vier Kante term neer te zetten.

```r
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

Deze functie genereert de volgende uitvoer.

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.12597 -0.02659  0.00185  0.02963  0.10696
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## (Intercept)       6.38e+00   4.07e-02   156.6   <2e-16 ***
## Time              1.57e-09   4.32e-11    36.3   <2e-16 ***
## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0417 on 213 degrees of freedom
## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16
```

Deze uitvoer ziet er beter uit. Alle voor waarden zijn belang rijk. De 2e-16-waarde is een standaard waarde die niet te ernstig moet worden genomen.  

Als Sanity-test kunt u een tijd reeks tekenen van de productie gegevens van Californië zuivel met de trend lijn die wordt weer gegeven. We hebben de volgende code toegevoegd in het Machine Learning Studio (klassiek) het [R-script][execute-r-script] model (niet RStudio) om het model te maken en een waarnemings punt uit te voeren. Het resultaat wordt weer gegeven in het volgende voor beeld.

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![Productie gegevens voor Californië melk met trend model weer gegeven.](./media/r-quickstart/unnamed-chunk-18.png)

Het lijkt erop dat het trend model op de juiste wijze in de gegevens past. Verder lijkt het niet alsof er sprake is van meerwaarde, zoals oneven Wiggles in de model curve.

### <a name="seasonal-model"></a>Seizoen model

Met een trend model in handen moeten we de seizoensgebonden effecten pushen en toevoegen. We gebruiken de maand van het jaar als een dummy variabele in het lineaire model om het effect van de maand per maand vast te leggen. Wanneer u factor variabelen in een model introduceert, mag het snij punt niet worden berekend. Als u dit niet doet, wordt de formule boven opgegeven en wordt een van de gewenste factoren door R verwijderd, maar wordt de onderhouds periode bewaard.

Omdat we een voldoende trend model hebben, kunnen we de `update()` functie gebruiken om de nieuwe voor waarden aan het bestaande model toe te voegen. De-1 in de update formule daalt de interceptie termijn. Gedurende het moment van RStudio voortzetten:

```r
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

Deze functie genereert de volgende uitvoer.

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^3) + Month - 1,
##     data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.06879 -0.01693  0.00346  0.01543  0.08726
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## Time              1.57e-09   2.72e-11    57.7   <2e-16 ***
## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 ***
## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 ***
## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 ***
## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 ***
## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 ***
## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 ***
## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 ***
## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 ***
## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 ***
## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 ***
## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 ***
## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 ***
## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0263 on 202 degrees of freedom
## Multiple R-squared:     1,    Adjusted R-squared:     1
## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16
```

We zien dat het model geen interceptie periode meer heeft en 12 significante maand factoren heeft. Dit resultaat is precies wat we wilden zien.

Laten we nog een tijd reeks tekenen van de California zuivel productie gegevens om te zien hoe goed het seizoen model werkt. We hebben de volgende code toegevoegd in de Machine Learning Studio (klassiek) [R-script uitvoeren][execute-r-script] om het model te maken en een plot te creëren.

```r
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

Als u deze code uitvoert in Machine Learning Studio (klassiek), wordt het hier weer gegeven.

![Productie van Californië met model, met inbegrip van seizoen effecten.](./media/r-quickstart/unnamed-chunk-20.png)

De aanpassing aan de gegevens die in dit voor beeld wordt weer gegeven, is in plaats daarvan te stimuleren. Zowel de trend als het seizoen effect (maandelijkse variatie) lijkt redelijk.

Als u een andere controle op ons model hebt, gaan we kijken naar de verschillen. Met de volgende code worden de voorspelde waarden van onze twee modellen berekend, worden de verschillen voor het seizoen model berekend en worden deze verschillen voor de opleidings gegevens weer gegeven.

```r
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

Het residuele waarnemings punt wordt hier weer gegeven.

![Resten van het seizoen model voor de trainings gegevens.](./media/r-quickstart/unnamed-chunk-21.png)

Deze verschillen zien er redelijk uit. Er is geen specifieke structuur, met uitzonde ring van het effect van de 2008-2009-recessie, waarbij ons model niet bijzonder goed werkt.

Het waarnemings punt dat in dit voor beeld wordt weer gegeven, is handig voor het detecteren van tijd afhankelijke patronen in de verschillen. De expliciete aanpak van het berekenen en uitzetten van de verschillen die we hebben gebruikt, plaatst de resten in de tijd volgorde op het waarnemings punt. Als we hebben geplotd `milk.lm$residuals` , zou het waarnemings punt niet in de juiste volg orde zijn.

U kunt ook gebruiken `plot.lm()` om een reeks diagnostische grafieken te maken.

```r
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Deze code produceert een reeks diagnostische waarnemings punten die in de volgende voor beelden worden weer gegeven.

![Eerste diagnose tekening voor het seizoen model.](./media/r-quickstart/unnamed-chunk-221.png)

![Tweede diagnose tekening voor het seizoen model.](./media/r-quickstart/unnamed-chunk-222.png)

![Derde diagnose tekening voor het seizoen model.](./media/r-quickstart/unnamed-chunk-223.png)

![Vierde diagnose tekening voor het seizoen model.](./media/r-quickstart/unnamed-chunk-224.png)

Er zijn een aantal zeer influentiale punten die in deze grafieken worden geïdentificeerd, maar er is niets om grote bezorgdheid te veroorzaken. Verder kunnen we zien uit de normale Q-Q-tekening dat de resten dicht bij normaal gedistribueerd zijn, wat een belang rijke veronderstelling voor lineaire modellen is.

### <a name="forecasting-and-model-evaluation"></a>Prognoses en model evaluatie

Er is nog maar één ding voor het volt ooien van ons voor beeld. We moeten prognoses berekenen en de fout op de werkelijke gegevens meten. Onze prognose geldt voor de 12 maanden van 2013. We kunnen een fout meting voor deze prognose berekenen op basis van de werkelijke gegevens die geen deel uitmaken van onze trainings gegevensset. Daarnaast kunnen we de prestaties van de 18 jaar van de trainings gegevens vergelijken met de 12 maanden test gegevens.

Een aantal metrische gegevens wordt gebruikt om de prestaties van Time Series-modellen te meten. In ons geval gebruiken we de fout melding root Square (RMS). Met de volgende functie wordt de RMS-fout tussen twee reeksen berekend.

```r
RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
  ## Function to compute the RMS error or difference between two
  ## series or vectors

  messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
                "ERROR: Input vector to function RMS.error is too short",
                "ERROR: Input vectors to function RMS.error must be of same length",
                "WARNING: Funtion rms.error has received invald input time series.")

  ## Check the arguments
  if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
    warning(messages[1])
    return(NA)}

  if(length(series1) < min.length) {
    warning(messages[2])
    return(NA)}

  if((length(series1) != length(series2))) {
       warning(messages[3])
    return(NA)}

  ## If is.log is TRUE exponentiate the values, else just copy
  if(is.log) {
    tryCatch( {
      temp1 <- exp(series1)
      temp2 <- exp(series2) },
      error = function(e){warning(messages[4]); NA}
    )
  } else {
    temp1 <- series1
    temp2 <- series2
  }

 ## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute the RMS error in a dataframe
  tryCatch( {
    sqrt(sum((temp1 - temp2)^2) / length(temp1))},
    error = function(e){warning(messages[4]); NA})
}
```

Net als bij de `log.transform()` functie die we in de sectie ' trans formaties ' hebben besproken, is er een groot aantal fout controles en code voor het herstellen van uitzonde ringen in deze functie. De gebruikte principes zijn hetzelfde. Het werk wordt uitgevoerd op twee plaatsen in `tryCatch()` . Ten eerste is de tijd reeks exponentiated, omdat we werken met de logboeken van de waarden. Ten tweede wordt de werkelijke RMS-fout berekend.

Met een functie die de RMS-fout meet, gaan we een data frame maken en uitvoeren die de RMS-fouten bevat. We nemen voor waarden voor het trend model alleen en het volledige model met seizoen factoren. De volgende code voert de taak uit met behulp van de twee lineaire modellen die we hebben gebouwd.

```r
## Compute the RMS error in a dataframe
## Include the row names in the first column so they will
## appear in the output of the Execute R Script
RMS.df  <-  data.frame(
rowNames = c("Trend Model", "Seasonal Model"),
  Traing = c(
  RMS.error(predict1[1:216], cadairydata$Milk.Prod[1:216]),
  RMS.error(predict2[1:216], cadairydata$Milk.Prod[1:216])),
  Forecast = c(
    RMS.error(predict1[217:228], cadairydata$Milk.Prod[217:228]),
    RMS.error(predict2[217:228], cadairydata$Milk.Prod[217:228]))
)
RMS.df

## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('RMS.df')
```

Als deze code wordt uitgevoerd, produceert de uitvoer die hier wordt weer gegeven in de uitvoer poort van de resultaten gegevensset.

![Scherm opname van de vergelijking van RMS-fouten voor de modellen.](./media/r-quickstart/fig26.png)

Vanuit deze resultaten ziet u dat het toevoegen van de seizoensgebonden factoren aan het model de RMS-fout aanzienlijk reduceert. Het is niet te verrassend, de RMS-fout voor de trainings gegevens is een bit kleiner dan voor de prognose.

## <a name="guide-to-rstudio-documentation"></a><a id="appendixa"></a>Gids voor RStudio-documentatie

RStudio is goed gedocumenteerd. Hier vindt u enkele koppelingen naar de belangrijkste secties van de RStudio-documentatie om aan de slag te gaan.

* **Projecten maken**: u kunt uw R-code in projecten indelen en beheren met behulp van RStudio. Zie [using projects](https://support.rstudio.com/hc/articles/200526207-Using-Projects)(Engelstalig) voor meer informatie. Volg deze instructies en maak een project voor de R-code voorbeelden in dit artikel.
* **R-code bewerken en uitvoeren**: RStudio biedt een geïntegreerde omgeving voor het bewerken en uitvoeren van r-code. Zie [code bewerken en uitvoeren](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code)voor meer informatie.
* **Debug**: RStudio bevat krachtige functies voor fout opsporing. Zie [fout opsporing met RStudio](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio)voor meer informatie over deze functies. Zie [probleem oplossing](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting)voor meer informatie over functies voor het oplossen van onderbrekings punten.

## <a name="further-reading"></a><a id="appendixb"></a>Meer lezen

Deze zelf studie R-programmeer behandelt de basis beginselen van wat u nodig hebt om de R-taal met Machine Learning Studio (klassiek) te gebruiken. Als u niet bekend bent met R, zijn er twee inleidingen beschikbaar op KRANen:

* [R voor beginners](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf) door Emmanuel Paradis is een goede plaats om te starten.
* [Een inleiding tot R](https://cran.r-project.org/doc/manuals/R-intro.html) door W. N. Venables et al. gaat meer dieper in.

Er zijn veel boeken op R die u kunnen helpen om aan de slag te gaan:

* **Art of R-programmering: een rond leiding door het statistische software ontwerp** van Norman Matloff is een uitstekende inleiding tot het Program meren in R.
* **R Cookbook** van Paul Teetor biedt een oplossing voor het oplossen van problemen en oplossingen voor het gebruik van R.
* De **R in actie** van Robert Kabacoff is een ander handig inleidende boek. De [snelle R-website](https://www.statmethods.net/) van de Companion is een nuttige resource.
* **De R-Inferno** door Patrick branding is een verrassend humoristische-boek dat een aantal trucige en moeilijke onderwerpen behandelt die kunnen worden gevonden wanneer u programmeert in R. Het boek is gratis beschikbaar op [het R-Inferno](https://www.burns-stat.com/documents/books/the-r-inferno/).
* **Geavanceerde R** door Hadley Wickham biedt een grondige kennis van geavanceerde onderwerpen in R. De online versie van dit boek is gratis beschikbaar op [geavanceerde R](http://adv-r.had.co.nz/).
* **Inleidende tijd reeks met r** door Paul Cowpertwait en Andrew Metcalfe biedt een inleiding tot het gebruik van R voor time series-analyses. Veel meer theoretische teksten bieden R-voor beelden.

Hier volgen enkele fantastische Internet bronnen:

* [Taak weergave voor kranen: Time Series-analyse](https://cran.r-project.org/web/views/TimeSeries.html) bevat een catalogus met R time series-pakketten. Zie de documentatie voor het desbetreffende pakket voor meer informatie over specifieke Series-object pakketten.
* [Inleiding tot R](https://www.datacamp.com/courses/introduction-to-r) is een gratis interactieve cursus van DataCamp die in het comfort van uw browser met behulp van video lessen en coderings oefeningen met r. Er zijn interactieve zelf studies voor de nieuwste R-technieken en-pakketten.
* [Meer informatie over R-Program ma's, de definitieve hand leiding](https://www.datamentor.io/r-programming/) van DataMentor.
* [R coder](https://r-coder.com/) bevat gedetailleerde r-zelf studies en een gratis R-cursus voor beginners.
* [R-zelf studie](https://www.cyclismo.org/tutorial/R/) voor Kelly Black van Clarkson University is een snelle zelf studie.
* [De belangrijkste R-taal bronnen voor het verbeteren van uw gegevens vaardigheden](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html) lijsten meer dan 60 R-resources.

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
