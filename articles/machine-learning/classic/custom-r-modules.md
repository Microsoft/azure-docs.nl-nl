---
title: 'ML Studio (klassiek): Maak & aangepaste R-modules implementeren-Azure'
description: Meer informatie over het ontwerpen en implementeren van een aangepaste R-module in ML Studio (klassiek).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: d44f2cfa72bd53b01da073fca31ca698eb42720d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100520473"
---
# <a name="define-custom-r-modules-for-machine-learning-studio-classic"></a>Aangepaste R-modules voor Machine Learning Studio definiëren (klassiek)

**VAN TOEPASSING OP:**  ![Van toepassing op.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek) ![Niet van toepassing op.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

In dit onderwerp wordt beschreven hoe u een aangepaste R Studio (klassiek) ontwerpt en implementeert. Hierin wordt uitgelegd wat aangepaste R-modules zijn en welke bestanden worden gebruikt om ze te definiëren. Hierin ziet u hoe u de bestanden kunt samen stellen die een module definiëren en hoe u de module kunt registreren voor implementatie in een Machine Learning-werk ruimte. De elementen en kenmerken die worden gebruikt in de definitie van de aangepaste module, worden vervolgens uitgebreid beschreven. Het gebruik van de hulp functionaliteit en bestanden en meerdere uitvoer wordt ook besproken. 

Een **aangepaste module** is een door de gebruiker gedefinieerde module die naar uw werk ruimte kan worden geüpload en die als onderdeel van Azure machine learning Studio (klassiek) experiment kan worden uitgevoerd. Een **aangepaste r-module** is een aangepaste module waarmee een door de gebruiker gedefinieerde R-functie wordt uitgevoerd. **R** is een programmeer taal voor statistische computing en grafische afbeeldingen die veel worden gebruikt door statistici en gegevens wetenschappers voor het implementeren van algoritmen. Op dit moment is R de enige taal die wordt ondersteund in aangepaste modules, maar ondersteuning voor extra talen wordt gepland voor toekomstige releases.

Aangepaste modules hebben de status van de **eerste klasse** in azure machine learning Studio (klassiek) in de zin dat ze kunnen worden gebruikt, net als bij andere modules. Ze kunnen worden uitgevoerd met andere modules, opgenomen in gepubliceerde experimenten of in visualisaties. U hebt controle over het algoritme dat door de module wordt geïmplementeerd, de invoer-en uitvoer poorten die moeten worden gebruikt, de model parameters en andere runtime-gedragingen. Een experiment dat aangepaste modules bevat, kan ook worden gepubliceerd in de Azure AI Gallery om eenvoudig te kunnen delen.

## <a name="files-in-a-custom-r-module"></a>Bestanden in een aangepaste R-module
Een aangepaste R-module wordt gedefinieerd door een zip-bestand met ten minste twee bestanden:

* Een **bron bestand** dat de R-functie implementeert die beschikbaar is gesteld door de module
* Een **XML-definitie bestand** met een beschrijving van de interface van de aangepaste module

Aanvullende hulp bestanden kunnen ook worden opgenomen in het zip-bestand dat de functionaliteit biedt die toegankelijk is vanuit de aangepaste module. Deze optie wordt beschreven in het gedeelte **argumenten** van de sectie met referentie **-elementen in het XML-definitie bestand** volgens het Snelstartgids-voor beeld.

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>Voor beeld van Snelstartgids: een aangepaste R-module definiëren, inpakken en registreren
In dit voor beeld ziet u hoe u de bestanden bouwt die vereist zijn voor een aangepaste R-module, ze inpakken in een zip-bestand en de module vervolgens registreren in uw Machine Learning-werk ruimte. Het voorbeeld bestand zip-pakket en voorbeeld bestanden kunnen worden gedownload van een [gedownload CustomAddRows.zip](https://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

## <a name="the-source-file"></a>Het bron bestand
Bekijk het voor beeld van een aangepaste module voor het **toevoegen van rijen** waarmee de standaard implementatie van de module **rijen toevoegen** wordt gebruikt voor het samen voegen van rijen (waarnemingen) van twee gegevens sets (Data frames). In de Standard-module **Rows toevoegen** worden de rijen van de tweede invoer-gegevensset toegevoegd aan het einde van de eerste invoer gegevensset met behulp van de `rbind` algoritme. De aangepaste `CustomAddRows` functie accepteert op dezelfde manier twee gegevens sets, maar accepteert ook een para meter voor een Boole-swap als extra invoer. Als de para meter swap is ingesteld op **False**, wordt dezelfde gegevensset geretourneerd als de standaard implementatie. Maar als de para meter swap **True** is, voegt de functie rijen van de eerste invoer-gegevensset toe aan het einde van de tweede gegevensset in plaats daarvan. Het bestand CustomAddRows. R dat de implementatie bevat van de R-functie die wordt `CustomAddRows` weer gegeven door de module **aangepaste add rows** , heeft de volgende R-code.

```r
CustomAddRows <- function(dataset1, dataset2, swap=FALSE) 
{
    if (swap)
    {
        return (rbind(dataset2, dataset1));
    }
    else
    {
        return (rbind(dataset1, dataset2));
    } 
} 
```

### <a name="the-xml-definition-file"></a>Het XML-definitie bestand
Als u deze functie beschikbaar wilt `CustomAddRows` maken als de module Azure machine learning Studio (klassiek), moet er een XML-definitie bestand worden gemaakt om op te geven hoe de module **aangepaste rijen toevoegen** eruit moet zien en zich gedraagt. 

```xml
<!-- Defined a module using an R Script -->
<Module name="Custom Add Rows">
    <Owner>Microsoft Corporation</Owner>
    <Description>Appends one dataset to another. Dataset 2 is concatenated to Dataset 1 when Swap is FALSE, and vice versa when Swap is TRUE.</Description>

<!-- Specify the base language, script file and R function to use for this module. -->        
    <Language name="R" 
        sourceFile="CustomAddRows.R" 
        entryPoint="CustomAddRows" />  

<!-- Define module input and output ports -->
<!-- Note: The values of the id attributes in the Input and Arg elements must match the parameter names in the R Function CustomAddRows defined in CustomAddRows.R. -->
    <Ports>
        <Input id="dataset1" name="Dataset 1" type="DataTable">
            <Description>First input dataset</Description>
        </Input>
        <Input id="dataset2" name="Dataset 2" type="DataTable">
            <Description>Second input dataset</Description>
        </Input>
        <Output id="dataset" name="Dataset" type="DataTable">
            <Description>The combined dataset</Description>
        </Output>
    </Ports>

<!-- Define module parameters -->
    <Arguments>
        <Arg id="swap" name="Swap" type="bool" >
            <Description>Swap input datasets.</Description>
        </Arg>
    </Arguments>
</Module>
```

Het is belang rijk om te weten dat de waarde van de **id-** kenmerken van de **invoer** -en **ARG** -elementen in het XML-bestand exact moet overeenkomen met de namen van de functie parameters van de R-code in het bestand CustomAddRows. R: (*dataset1*, *dataset2* en *swap* in het voor beeld). Op dezelfde manier moet de waarde van het kenmerk **Entry Point** van het **taal** element exact overeenkomen met de naam van de functie in het R-script: (*CustomAddRows* in het voor beeld). 

Het kenmerk **id** voor het element **output** komt daarentegen niet overeen met een van de variabelen in het R-script. Als er meer dan één uitvoer vereist is, retourneert eenvoudigweg een lijst van de R-functie met *de resultaten die in dezelfde volg orde* zijn geplaatst als **uitvoer** elementen die in het XML-bestand worden gedefinieerd.

### <a name="package-and-register-the-module"></a>De module inpakken en registreren
Sla deze twee bestanden op als *CustomAddRows. R* en *CustomAddRows.xml* en voer de twee bestanden vervolgens samen in een *CustomAddRows.zip* bestand.

Als u deze wilt registreren in uw Machine Learning-werk ruimte, gaat u naar uw werk ruimte in Azure Machine Learning Studio (klassiek), klikt u op de knop **+ Nieuw** aan de onderkant en kiest u **module-> van zip-pakket** om de nieuwe aangepaste module voor het **toevoegen van rijen** te uploaden.

![Zip uploaden](./media/custom-r-modules/upload-from-zip-package.png)

De module **aangepaste rijen toevoegen** is nu gereed om toegang te krijgen tot uw machine learning experimenten.

## <a name="elements-in-the-xml-definition-file"></a>Elementen in het XML-definitie bestand
### <a name="module-elements"></a>Module-elementen
Het **module** -element wordt gebruikt voor het definiëren van een aangepaste module in het XML-bestand. Meerdere modules kunnen in één XML-bestand worden gedefinieerd met behulp van meerdere **module** -elementen. Elke module in uw werk ruimte moet een unieke naam hebben. Een aangepaste module met dezelfde naam als een bestaande aangepaste module registreren en de bestaande module vervangen door de nieuwe. Aangepaste modules kunnen echter worden geregistreerd met dezelfde naam als een bestaande Azure Machine Learning Studio (klassieke) module. Als dat het geval is, worden ze weer gegeven in de categorie **aangepast** van het module palet.

```xml
<Module name="Custom Add Rows" isDeterministic="false"> 
    <Owner>Microsoft Corporation</Owner>
    <Description>Appends one dataset to another...</Description>/> 
```

Binnen het **module** -element kunt u twee extra optionele elementen opgeven:

* een **eigenaars** element dat is inge sloten in de module  
* een **beschrijvings** element dat tekst bevat die wordt weer gegeven in snelle Help voor de module en wanneer u de muis aanwijzer over de module in de machine learning gebruikers interface beweegt.

Regels voor de limieten van tekens in de module-elementen:

* De waarde van het **naam** kenmerk in het **module** -element mag niet langer zijn dan 64 tekens. 
* De inhoud van het **beschrijvings** element mag niet langer zijn dan 128 tekens.
* De inhoud van het **eigenaars** element mag niet langer zijn dan 32 tekens.

De resultaten van een module kunnen deterministisch of niet-deterministisch zijn. * * standaard worden alle modules als deterministisch beschouwd. Op basis van een niet-veranderende set invoer parameters en gegevens, moet de module dezelfde resultaten eacRAND of een functie tijd retour neren die wordt uitgevoerd. Op basis van dit gedrag Azure Machine Learning Studio (klassiek) alleen modules opnieuw uit te voeren die zijn gemarkeerd als deterministisch als een para meter of de invoer gegevens zijn gewijzigd. Het retour neren van de resultaten in de cache levert ook veel snellere uitvoering van experimenten.

Er zijn functies die niet-deterministisch zijn, zoals ASELECT of een functie die de huidige datum of tijd retourneert. Als uw module een niet-deterministische functie gebruikt, kunt u opgeven dat de module niet-deterministisch is door het optionele kenmerk **isDeterministic** in te stellen op **False**. Dit zorgt ervoor dat de module opnieuw wordt gestart wanneer het experiment wordt uitgevoerd, zelfs als de module-invoer en de para meters niet zijn gewijzigd. 

### <a name="language-definition"></a>Taal definitie
Het **taal** element in het XML-definitie bestand wordt gebruikt om de taal van de aangepaste module op te geven. Momenteel is R de enige ondersteunde taal. De waarde van het kenmerk **sourceFile** moet de naam zijn van het R-bestand dat de functie bevat die moet worden aangeroepen wanneer de module wordt uitgevoerd. Dit bestand moet deel uitmaken van het zip-pakket. De waarde van het **Entry Point** -kenmerk is de naam van de functie die wordt aangeroepen en moet overeenkomen met een geldige functie die is gedefinieerd in het bron bestand.

```xml
<Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />
```

### <a name="ports"></a>Poorten
De invoer-en uitvoer poorten voor een aangepaste module zijn opgegeven in onderliggende elementen van de sectie **poorten** van het XML-definitie bestand. De volg orde van deze elementen bepaalt de ervaring van de lay-out (UX) door gebruikers. De eerste onderliggende **invoer** of **uitvoer** die wordt vermeld in het element **ports** van het XML-bestand wordt de meest linkse invoer poort in de machine learning UX.
Elke invoer-en uitvoer poort heeft mogelijk een onderliggend onderliggend **Beschrijving** -element met de tekst die wordt weer gegeven wanneer u de muis aanwijzer boven de poort in de machine learning gebruikers interface houdt.

**Poort regels**:

* Het maximum aantal **invoer-en uitvoer poorten** is 8 voor elke poort.

### <a name="input-elements"></a>Invoer elementen
Met invoer poorten kunt u gegevens door geven aan uw R-functie en-werk ruimte. De volgende **gegevens typen** worden ondersteund voor invoer poorten: 

**DataTable:** Dit type wordt door gegeven aan uw R-functie als data. frame. Alle typen (zoals CSV-bestanden of ARFF-bestanden) die worden ondersteund door Machine Learning en die compatibel zijn met **DataTable** , worden in feite geconverteerd naar een gegevens. frame. 

```xml
<Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
    <Description>Input Dataset 1</Description>
</Input>
```

Het kenmerk id dat is gekoppeld aan elke **DataTable** **-** invoer poort moet een unieke waarde hebben en deze waarde moet overeenkomen met de bijbehorende benoemde para meter in uw R-functie.
Optionele **DataTable** -poorten die niet als invoer worden door gegeven in een experiment, hebben de waarde **Null** die is door gegeven aan de R-functie en optionele zip-poorten worden genegeerd als de invoer niet is verbonden. Het kenmerk **isOptional** is optioneel voor zowel de **DataTable** -als de **zip** -typen en is standaard ingesteld op *Onwaar* .

**Zip:** Aangepaste modules kunnen een zip-bestand accepteren als invoer. Deze invoer wordt uitgepakt in de werkmap R van uw functie

```xml
<Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
    <Description>Zip files to be extracted to the R working directory.</Description>
</Input>
```

Voor aangepaste R-modules moet de ID voor een zip-poort niet overeenkomen met de para meters van de R-functie. De reden hiervoor is dat het zip-bestand automatisch wordt uitgepakt naar de werkmap R.

**Invoer regels:**

* De waarde van het kenmerk **id** van het element **input** moet een geldige R-variabelenaam zijn.
* De waarde van het kenmerk **id** van het **invoer** element mag niet langer zijn dan 64 tekens.
* De waarde van het kenmerk **name** van het element **input** mag niet langer zijn dan 64 tekens.
* De inhoud van het **beschrijvings** element mag niet langer zijn dan 128 tekens
* De waarde van het kenmerk **type** van het element **input** moet *zip* of *DataTable* zijn.
* De waarde van het kenmerk **isOptional** van het **input** -element is niet vereist (en is standaard *Onwaar* wanneer niet opgegeven); maar als deze waarde is opgegeven, moet deze *True* of *False* zijn.

### <a name="output-elements"></a>Uitvoer elementen
**Standaard uitvoer poorten:** Uitvoer poorten worden toegewezen aan de retour waarden van uw R-functie, die vervolgens door volgende modules kan worden gebruikt. *DataTable* is het enige standaard uitvoer poort type dat momenteel wordt ondersteund. (Ondersteuning voor *leerers* en *trans formaties* wordt aangekondigd.) Een *DataTable* -uitvoer wordt als volgt gedefinieerd:

```xml
<Output id="dataset" name="Dataset" type="DataTable">
    <Description>Combined dataset</Description>
</Output>
```

Voor uitvoer in aangepaste R-modules hoeft de waarde van het kenmerk id niet overeen te komen met iets in het R **-** script, maar dit moet uniek zijn. Voor een enkele module-uitvoer moet de retour waarde van de functie R een *Data. frame* zijn. Als u meer dan één object van een ondersteund gegevens type wilt uitvoeren, moeten de juiste uitvoer poorten worden opgegeven in het XML-definitie bestand en moeten de objecten als een lijst worden geretourneerd. De uitvoer objecten worden toegewezen aan uitvoer poorten van links naar rechts, waarbij de volg orde wordt weer gegeven waarin de objecten in de geretourneerde lijst worden geplaatst.

Als u bijvoorbeeld de module **aangepaste rijen toevoegen** wilt wijzigen in uitvoer van de oorspronkelijke twee gegevens sets, *dataset1* en *dataset2*, naast de nieuwe gekoppelde gegevensset, *gegevensset*(in een volg orde, van links naar rechts, zoals: *DataSet*, *dataset1*, *dataset2*), definieert u de uitvoer poorten in het CustomAddRows.xml-bestand als volgt:

```xml
<Ports> 
    <Output id="dataset" name="Dataset Out" type="DataTable"> 
        <Description>New Dataset</Description> 
    </Output> 
    <Output id="dataset1_out" name="Dataset 1 Out" type="DataTable"> 
        <Description>First Dataset</Description> 
    </Output> 
    <Output id="dataset2_out" name="Dataset 2 Out" type="DataTable"> 
        <Description>Second Dataset</Description> 
    </Output> 
    <Input id="dataset1" name="Dataset 1" type="DataTable"> 
        <Description>First Input Table</Description>
    </Input> 
    <Input id="dataset2" name="Dataset 2" type="DataTable"> 
        <Description>Second Input Table</Description> 
    </Input> 
</Ports> 
```

En retour neren de lijst met objecten in een lijst in de juiste volg orde in ' CustomAddRows. R ':

```r
CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
    if (swap) { dataset <- rbind(dataset2, dataset1)) } 
    else { dataset <- rbind(dataset1, dataset2)) 
    } 
    return (list(dataset, dataset1, dataset2)) 
} 
```

**Visualisatie-uitvoer:** U kunt ook een uitvoer poort van het type *visualisatie* opgeven, waarin de uitvoer van het R graphics-apparaat en de console-uitvoer wordt weer gegeven. Deze poort maakt geen deel uit van de uitvoer van de R-functie en heeft geen invloed op de volg orde van de andere typen uitvoer poorten. Als u een visualisatie poort wilt toevoegen aan de aangepaste modules, voegt u een **uitvoer** element met de waarde *visualisatie* voor het **type** kenmerk toe:

```xml
<Output id="deviceOutput" name="View Port" type="Visualization">
    <Description>View the R console graphics device output.</Description>
</Output>
```

**Uitvoer regels:**

* De waarde van het kenmerk **id** van het element **output** moet een geldige R-variabelenaam zijn.
* De waarde van het kenmerk **id** van het element **output** mag niet langer zijn dan 32 tekens.
* De waarde van het kenmerk **name** van het element **output** mag niet langer zijn dan 64 tekens.
* De waarde van het kenmerk **type** van het element **output** moet *visualisatie* zijn.

### <a name="arguments"></a>Argumenten
Aanvullende gegevens kunnen worden door gegeven aan de functie R via module parameters die zijn gedefinieerd in het element **Arguments** . Deze para meters worden weer gegeven in het deel venster meest rechtse eigenschappen van de Machine Learning gebruikers interface wanneer de module is geselecteerd. Argumenten kunnen een van de ondersteunde typen zijn of u kunt een aangepaste opsomming maken wanneer dat nodig is. Net als bij **poort** elementen kunnen **argumenten** elementen een optioneel **beschrijvings** element hebben dat de tekst bevat die wordt weer gegeven wanneer u de muis aanwijzer boven de parameter naam houdt.
Optionele eigenschappen voor een module, zoals defaultValue, minValue en maxValue, kunnen worden toegevoegd aan een argument als kenmerken voor een **Eigenschappen** -element. Geldige eigenschappen voor het element **Properties** zijn afhankelijk van het argument type en worden beschreven met de ondersteunde argument typen in de volgende sectie. Voor argumenten waarvoor de eigenschap **isOptional** is ingesteld op **' True '** , hoeft de gebruiker geen waarde in te voeren. Als er geen waarde is opgegeven voor het argument, wordt het argument niet door gegeven aan de functie voor het toegangs punt. Argumenten van de ingangs punt functie die optioneel moeten zijn, moeten expliciet worden afgehandeld door de functie, bijvoorbeeld een standaard waarde van NULL toegewezen in de functie definitie van het toegangs punt. Bij een optioneel argument worden alleen de andere argument beperkingen, d.w.z. min of Max, afgedwongen als een waarde door de gebruiker wordt verschaft.
Net als bij invoer en uitvoer is het essentieel dat aan elk van de para meters unieke ID-waarden zijn gekoppeld. In het Snelstartgids-voor beeld is de bijbehorende id/para meter *gewisseld*.

### <a name="arg-element"></a>ARG-element
Een module parameter wordt gedefinieerd met behulp van het onderliggende element **ARG** van de sectie **Arguments** van het XML-definitie bestand. Net als bij de onderliggende elementen in de sectie **poorten** , definieert de volg orde van de para meters in de sectie **argumenten** de lay-out die in de UX is aangetroffen. De para meters worden in de gebruikers interface weer gegeven in dezelfde volg orde waarin ze zijn gedefinieerd in het XML-bestand. De typen die door Machine Learning worden ondersteund voor para meters, worden hier weer gegeven. 

**int** : een geheel getal (32-bits) type parameter.

```xml
<Arg id="intValue1" name="Int Param" type="int">
    <Properties min="0" max="100" default="0" />
    <Description>Integer Parameter</Description>
</Arg>
```

* *Optionele eigenschappen*: **min**, **Max**, **standaard** en **isOptional**

**dubbele** -een dubbele-type para meter.

```xml
<Arg id="doubleValue1" name="Double Param" type="double">
    <Properties min="0.000" max="0.999" default="0.3" />
    <Description>Double Parameter</Description>
</Arg>
```

* *Optionele eigenschappen*: **min**, **Max**, **standaard** en **isOptional**

**BOOL** : een Boole-para meter die wordt vertegenwoordigd door een selectie vakje in UX.

```xml
<Arg id="boolValue1" name="Boolean Param" type="bool">
    <Properties default="true" />
    <Description>Boolean Parameter</Description>
</Arg>
```

* *Optionele eigenschappen*: **standaard** -False als niet ingesteld

**teken reeks**: een standaard teken reeks

```xml
<Arg id="stringValue1" name="My string Param" type="string">
    <Properties isOptional="true" />
    <Description>String Parameter 1</Description>
</Arg>    
```

* *Optionele eigenschappen*: **standaard** en **isOptional**

**ColumnPicker**: een kolom selectie parameter. Dit type wordt weer gegeven in de UX als een kolom kiezer. Het element **Property** wordt hier gebruikt om de id op te geven van de poort van waaruit kolommen worden geselecteerd, waarbij het doel poort type *DataTable* moet zijn. Het resultaat van de kolom selectie wordt door gegeven aan de R-functie als een lijst met teken reeksen met de geselecteerde kolom namen. 

```xml
<Arg id="colset" name="Column set" type="ColumnPicker">      
    <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
    <Description>Column set</Description>
</Arg>
```

* *Vereiste eigenschappen*: **portId** : komt overeen met de id van een INPUT-element met type *DataTable*.
* *Optionele eigenschappen*:
  
  * **allowedTypes** : Hiermee filtert u de kolom typen waaruit u kunt kiezen. Geldige waarden zijn: 
    
    * Numeriek
    * Boolean
    * Categorische gegevens
    * Tekenreeks
    * Label
    * Functie
    * Score
    * Alles
  * **standaard** -geldige standaard selecties voor de kolom kiezer zijn onder andere: 
    
    * Geen
    * NumericFeature
    * NumericLabel
    * NumericScore
    * Numeriek
    * BooleanFeature
    * BooleanLabel
    * BooleanScore
    * BooleanAll
    * CategoricalFeature
    * CategoricalLabel
    * CategoricalScore
    * CategoricalAll
    * StringFeature
    * StringLabel
    * StringScore
    * StringAll
    * AllLabel
    * AllFeature
    * AllScore
    * Alles

**Dropdown**: een door de gebruiker opgegeven lijst met opsommings (vervolg keuzelijst). De vervolg keuzelijst items worden opgegeven in het **Eigenschappen** element met behulp van een **item** -element. De **id** voor elk **item** moet uniek zijn en een geldige R-variabele zijn. De waarde van de **naam** van een **item** fungeert als de tekst die u ziet en de waarde die wordt door gegeven aan de functie R.

```xml
<Arg id="color" name="Color" type="DropDown">
    <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
    </Properties>
    <Description>Select a color.</Description>
</Arg>    
```

* *Optionele eigenschappen*:
  * **default** -de waarde voor de eigenschap Default moet overeenkomen met een id-waarde van een van de **item** elementen.

### <a name="auxiliary-files"></a>Hulp bestanden
Alle bestanden die in uw aangepaste module-ZIP-bestand worden geplaatst, kunnen tijdens de uitvoerings tijd worden gebruikt. Alle aanwezige mapstructuren blijven behouden. Dit betekent dat file sourcing hetzelfde lokaal en in de uitvoering van Azure Machine Learning Studio (klassiek) werkt. 

> [!NOTE]
> U ziet dat alle bestanden worden uitgepakt naar de map src, zodat alle paden het voor voegsel src/hebben.
> 
> 

Stel dat u rijen met NAs wilt verwijderen uit de gegevensset en ook dubbele rijen wilt verwijderen voordat u de gegevens uitCustomAddRowst en dat u al een R-functie hebt geschreven die in een bestand RemoveDupNARows. R:

```r
RemoveDupNARows <- function(dataFrame) {
    #Remove Duplicate Rows:
    dataFrame <- unique(dataFrame)
    #Remove Rows with NAs:
    finalDataFrame <- dataFrame[complete.cases(dataFrame),]
    return(finalDataFrame)
}
```

U kunt het hulp bestand RemoveDupNARows. R in de CustomAddRows-functie als bron gebruiken:

```r
CustomAddRows <- function(dataset1, dataset2, swap=FALSE) {
    source("src/RemoveDupNARows.R")
        if (swap) { 
            dataset <- rbind(dataset2, dataset1))
        } else { 
            dataset <- rbind(dataset1, dataset2)) 
        } 
    dataset <- removeDupNARows(dataset)
    return (dataset)
}
```

Upload vervolgens een zip-bestand met ' CustomAddRows. R ', ' CustomAddRows.xml ' en ' RemoveDupNARows. R ' als een aangepaste R-module.

## <a name="execution-environment"></a>Uitvoerings omgeving
De uitvoerings omgeving voor het R-script maakt gebruik van dezelfde versie van R als de **script module Execute r** en kan dezelfde standaard pakketten gebruiken. U kunt ook extra R-pakketten toevoegen aan uw aangepaste module door deze op te nemen in het aangepaste module zip-pakket. Laad ze in uw R-script net zoals u dat in uw eigen R-omgeving zou doen. 

**De beperkingen van de uitvoerings omgeving** zijn onder andere:

* Niet-persistent bestands systeem: bestanden die worden geschreven wanneer de aangepaste module wordt uitgevoerd, worden niet opgeslagen in meerdere uitvoeringen van dezelfde module.
* Geen netwerk toegang