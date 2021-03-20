---
title: U-SQL-scripts uitbreiden met R in Azure Data Lake Analytics
description: Meer informatie over het uitvoeren van R-code in U-SQL-scripts met behulp van Azure Data Lake Analytics. R-code inline of referentie van bestanden insluiten.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/20/2017
ms.openlocfilehash: ab28ba1bb6185f5f0ab90df99f0abea7895b1bf4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92217997"
---
# <a name="extend-u-sql-scripts-with-r-code-in-azure-data-lake-analytics"></a>U-SQL-scripts uitbreiden met R-code in Azure Data Lake Analytics

In het volgende voor beeld ziet u de basis stappen voor het implementeren van R-code:

* Gebruik de `REFERENCE ASSEMBLY` instructie om R-extensies in te scha kelen voor het U-SQL-script.
* Gebruik de `REDUCE` bewerking voor het partitioneren van de invoer gegevens op een sleutel.
* De R-uitbrei dingen voor U-SQL bevatten een ingebouwde versmaller ( `Extension.R.Reducer` ) waarmee R-code wordt uitgevoerd op elk hoek punt dat aan de verminderr is toegewezen.
* Het gebruik van toegewezen benoemde gegevens frames `inputFromUSQL` met de naam en `outputToUSQL` respectievelijk door geven van gegevens tussen U-SQL en R. invoer-en uitvoer data frame-id-id's zijn vast (dat wil zeggen, gebruikers kunnen deze vooraf gedefinieerde namen van invoer-en uitvoer data frame-id's niet wijzigen).

## <a name="embedding-r-code-in-the-u-sql-script"></a>R-code in het U-SQL-script insluiten

U kunt de R-code van uw U-SQL-script inline met behulp van de opdracht parameter van de `Extension.R.Reducer` . U kunt bijvoorbeeld het R-script declareren als een teken reeks variabele en dit door geven als een para meter voor de reducer.

```usql
REFERENCE ASSEMBLY [ExtR];

DECLARE @myRScript = @"
inputFromUSQL$Species = as.factor(inputFromUSQL$Species)
lm.fit=lm(unclass(Species)~.-Par, data=inputFromUSQL)
#do not return readonly columns and make sure that the column names are the same in usql and r cripts,
outputToUSQL=data.frame(summary(lm.fit)$coefficients)
colnames(outputToUSQL) <- c(""Estimate"", ""StdError"", ""tValue"", ""Pr"")
outputToUSQL
";

@RScriptOutput = REDUCE … USING new Extension.R.Reducer(command:@myRScript, ReturnType:"dataframe");
```

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>De R-code in een afzonderlijk bestand opslaan en ernaar verwijzen naar het U-SQL-script

In het volgende voor beeld wordt een complexere gebruik geïllustreerd. In dit geval wordt de R-code geïmplementeerd als een RESOURCE die het U-SQL-script is.

Sla deze R-code op als een afzonderlijk bestand.

```usql
load("my_model_LM_Iris.rda")
outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
```

Gebruik een U-SQL-script om dat R-script te implementeren met de instructie DEPLOY RESOURCE.

```usql
REFERENCE ASSEMBLY [ExtR];
DEPLOY RESOURCE @"/usqlext/samples/R/RinUSQL_PredictUsingLinearModelasDF.R";
DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
DECLARE @PartitionCount int = 10;
@InputData =
    EXTRACT
        SepalLength double,
        SepalWidth double,
        PetalLength double,
        PetalWidth double,
        Species string
    FROM @IrisData
    USING Extractors.Csv();
@ExtendedData =
    SELECT
        Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
        SepalLength,
        SepalWidth,
        PetalLength,
        PetalWidth
    FROM @InputData;
// Predict Species
@RScriptOutput = REDUCE @ExtendedData ON Par
    PRODUCE Par, fit double, lwr double, upr double
    READONLY Par
    USING new Extension.R.Reducer(scriptFile:"RinUSQL_PredictUsingLinearModelasDF.R", rReturnType:"dataframe", stringsAsFactors:false);
    OUTPUT @RScriptOutput TO @OutputFilePredictions USING Outputters.Tsv();
```

## <a name="how-r-integrates-with-u-sql"></a>Hoe R integreert met U-SQL

### <a name="datatypes"></a>Gegevens typen

* Teken reeks-en numerieke kolommen van U-SQL worden geconverteerd als-tussen R data frame en U-SQL [ondersteunde typen: `double` , `string` , `bool` , `integer` , `byte` ].
* Het `Factor` gegevens type wordt niet ondersteund in U-SQL.
* `byte[]` moet worden geserialiseerd als een base64-gecodeerd `string` .
* U-SQL-teken reeksen kunnen worden geconverteerd naar factoren in R-code, wanneer U-SQL een R-invoer data frame maakt of door de para meter reducer in te stellen `stringsAsFactors: true` .

### <a name="schemas"></a>Schema 's

* U-SQL-gegevens sets mogen geen dubbele kolom namen bevatten.
* U-SQL-gegevens sets kolom namen moeten teken reeksen zijn.
* Kolom namen moeten hetzelfde zijn in U-SQL-en R-scripts.
* Een alleen-lezen kolom kan geen deel uitmaken van de uitvoer data frame. Omdat alleen-lezen kolommen automatisch worden ingevoegd in de U-SQL-tabel als deze deel uitmaakt van het uitvoer schema van UDO.

### <a name="functional-limitations"></a>Functionele beperkingen

* De R-engine kan niet twee maal in hetzelfde proces worden geïnstantieerd.
* Op dit moment biedt U-SQL geen ondersteuning voor de combi natie Udo's voor voor spellingen met behulp van gepartitioneerde modellen die zijn gegenereerd met de verkorter Udo's. Gebruikers kunnen de gepartitioneerde modellen als resource declareren en gebruiken in hun R-script (zie voorbeeld code `ExtR_PredictUsingLMRawStringReducer.usql` )

### <a name="r-versions"></a>R-versies

Alleen R 3.2.2 wordt ondersteund.

### <a name="standard-r-modules"></a>Standard R-modules

```usql
base
boot
Class
Cluster
codetools
compiler
datasets
doParallel
doRSR
foreach
foreign
Graphics
grDevices
grid
iterators
KernSmooth
lattice
MASS
Matrix
Methods
mgcv
nlme
Nnet
Parallel
pkgXMLBuilder
RevoIOQ
revoIpe
RevoMods
RevoPemaR
RevoRpeConnector
RevoRsrConnector
RevoScaleR
RevoTreeView
RevoUtils
RevoUtilsMath
Rpart
RUnit
spatial
splines
Stats
stats4
survival
Tcltk
Tools
translations
utils
XML
```

### <a name="input-and-output-size-limitations"></a>Limieten voor invoer en uitvoer

Aan elk hoek punt is een beperkte hoeveelheid geheugen toegewezen. Omdat de invoer-en uitvoer DataFrames in het geheugen van de R-code moeten bestaan, mag de totale grootte van de invoer en uitvoer niet groter zijn dan 500 MB.

### <a name="sample-code"></a>Voorbeeldcode

Meer voorbeeld code is beschikbaar in uw Data Lake Store-account nadat u de U-SQL Advanced Analytics-extensies hebt geïnstalleerd. Het pad voor meer voorbeeld code is: `<your_account_address>/usqlext/samples/R` .

## <a name="deploying-custom-r-modules-with-u-sql"></a>Aangepaste R-modules implementeren met U-SQL

Maak eerst een aangepaste R-module en verzend deze en upload het bestand met de aangepaste module zip R naar uw ADL Store. In het voor beeld worden magittr_1.5.zip geüpload naar de hoofdmap van het standaard ADLS-account voor het ADLA-account dat we gebruiken. Wanneer u de module uploadt naar de ADL-Store, declareert u deze als RESOURCE voor DEPLOY gebruiken om deze beschikbaar te maken in uw U-SQL-script en aanroepen `install.packages` om het te installeren.

```usql
REFERENCE ASSEMBLY [ExtR];
DEPLOY RESOURCE @"/magrittr_1.5.zip";
DECLARE @IrisData string =  @"/usqlext/samples/R/iris.csv";
DECLARE @OutputFileModelSummary string = @"/R/Output/CustomPackages.txt";
// R script to run
DECLARE @myRScript = @"
# install the magrittr package,
install.packages('magrittr_1.5.zip', repos = NULL),
# load the magrittr package,
require(magrittr),
# demonstrate use of the magrittr package,
2 %>% sqrt
";
@InputData =
EXTRACT SepalLength double,
SepalWidth double,
PetalLength double,
PetalWidth double,
Species string
FROM @IrisData
USING Extractors.Csv();
@ExtendedData =
SELECT 0 AS Par,
*
FROM @InputData;
@RScriptOutput = REDUCE @ExtendedData ON Par
PRODUCE Par, RowId int, ROutput string
READONLY Par
USING new Extension.R.Reducer(command:@myRScript, rReturnType:"charactermatrix");
OUTPUT @RScriptOutput TO @OutputFileModelSummary USING Outputters.Tsv();
```

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [U-SQL-scripts ontwikkelen met Data Lake Tools voor Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [U-SQL-venster functies gebruiken voor Azure Data Lake Analytics taken](./data-lake-analytics-u-sql-get-started.md)