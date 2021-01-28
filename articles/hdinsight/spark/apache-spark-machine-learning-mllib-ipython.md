---
title: Machine learning-voor beeld met Spark MLlib in HDInsight-Azure
description: Meer informatie over het gebruik van Spark MLlib voor het maken van een machine learning-app die een gegevensset analyseert met behulp van logistiek regressie.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020, devx-track-python
ms.date: 04/27/2020
ms.openlocfilehash: d2054058bb00b0801aa1c3694c73b6a2edb46c80
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930046"
---
# <a name="use-apache-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Apache Spark MLlib gebruiken om een machine learning-toepassing te bouwen en een gegevensset te analyseren

Meer informatie over het gebruik van Apache Spark MLlib voor het maken van een machine learning-toepassing. De toepassing zal voorspellende analyse uitvoeren op een open gegevensset. Uit de ingebouwde machine learning bibliotheken van Spark gebruikt dit voor beeld *classificatie* door middel van logistiek regressie.

MLlib is een belang rijke Spark-bibliotheek die veel hulpprogram ma's biedt die nuttig zijn voor machine learning taken, zoals:

* Classificatie
* Regressie
* Clustering
* Modelleren
* SVD (Singular Value Decomposition) en PCA (Principal Component Snalysis)
* Hypothesen voor het testen en berekenen van voorbeeldstatistieken

## <a name="understand-classification-and-logistic-regression"></a>Classificatie en logistieke regressie begrijpen

*Classificatie*, een populaire machine learning-taak, is het proces waarbij invoergegevens worden gesorteerd in categorieën. Het is de taak van een classificatie algoritme om te bepalen hoe labels moeten worden toegewezen aan de invoer gegevens die u opgeeft. U kunt bijvoorbeeld een machine learning-algoritme zien waarin aandelen informatie als invoer wordt geaccepteerd. Vervolgens wordt het aandeel onderverdeeld in twee categorieën: aandelen die u moet verkopen en aandelen die u moet houden.

Logistiek regressie is het algoritme dat u voor classificatie gebruikt. De logistieke regressie-API van Spark is handig voor *binaire classificatie*, of voor het classificeren van invoergegevens in één van twee groepen. Zie [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression) voor meer informatie over logistieke regressies.

In samen vatting produceert het proces van logistiek regressie een *logistiek functie*. Gebruik de functie om de kans te voors pellen dat een invoer vector deel uitmaakt van een groep of de andere.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Voor beeld van voorspellende analyse van gegevens over levensmiddelen inspecties

In dit voor beeld gebruikt u Spark voor het uitvoeren van een bepaalde voorspellende analyse op levensmiddelen inspectie gegevens (**Food_Inspections1.csv**). Gegevens die zijn verkregen via de [Data Portal van de stad van Chicago](https://data.cityofchicago.org/). Deze gegevensset bevat informatie over de inspecties van de levensmiddelen inrichting die in Chicago zijn uitgevoerd. Inclusief informatie over elke inrichting, de gevonden schendingen (indien van toepassing) en de resultaten van de controle. Het CSV-gegevens bestand is al beschikbaar in het opslag account dat is gekoppeld aan het cluster op **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

In de onderstaande stappen ontwikkelt u een model om te zien wat er nodig is om een voedsel inspectie door te geven of uit te voeren.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Een machine learning-app maken in Apache Spark MLlib

1. Maak een Jupyter-notebook met behulp van de PySpark-kernel. Zie [Een Jupyter Notebook-bestand maken](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook-file) voor de instructies.

2. Importeer de typen die zijn vereist voor deze toepassing. Kopieer en plak de volgende code in een lege cel en druk op **SHIFT + ENTER**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```

    Vanwege de PySpark-kernel hoeft u niet expliciet contexten te maken. De Spark-en Hive-contexten worden automatisch gemaakt wanneer u de eerste code-cel uitvoert.

## <a name="construct-the-input-dataframe"></a>Een invoergegevensframe maken

Gebruik de Spark-context om de onbewerkte CSV-gegevens in het geheugen te halen als ongestructureerde tekst. Gebruik vervolgens de CSV-bibliotheek van python om elke regel van de gegevens te parseren.

1. Voer de volgende regels uit om een flexibele gedistribueerde gegevensset (RDD) te maken door de invoer gegevens te importeren en te parseren.

    ```PySpark
    def csvParse(s):
        import csv
        from StringIO import StringIO
        sio = StringIO(s)
        value = csv.reader(sio).next()
        sio.close()
        return value

    inspections = sc.textFile('/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                    .map(csvParse)
    ```

2. Voer de volgende code uit om één rij uit de RDD op te halen, zodat u het gegevens schema kunt zien:

    ```PySpark
    inspections.take(1)
    ```

    Dit is de uitvoer:

    ```
    [['413707',
        'LUNA PARK INC',
        'LUNA PARK  DAY CARE',
        '2049789',
        "Children's Services Facility",
        'Risk 1 (High)',
        '3250 W FOSTER AVE ',
        'CHICAGO',
        'IL',
        '60625',
        '09/21/2010',
        'License-Task Force',
        'Fail',
        '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
        '41.97583445690982',
        '-87.7107455232781',
        '(41.97583445690982, -87.7107455232781)']]
    ```

    De uitvoer geeft u een idee van het schema van het invoer bestand. Het bevat de naam van elke vestiging en het type van de inrichting. Ook het adres, de gegevens van de controles en de locatie, onder andere.

3. Voer de volgende code uit om een data frame (*DF*) en een tijdelijke tabel (*CountResults*) te maken met een paar kolommen die nuttig zijn voor de voorspellende analyse. `sqlContext` wordt gebruikt om trans formaties uit te voeren op gestructureerde gegevens.

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])

    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    De vier kolommen met interesse in de data frame zijn **id**, **naam**, **resultaten** en **schendingen**.

4. Voer de volgende code uit om een klein voor beeld van de gegevens te krijgen:

    ```PySpark
    df.show(5)
    ```

    Dit is de uitvoer:

    ```
    +------+--------------------+-------+--------------------+
    |    id|                name|results|          violations|
    +------+--------------------+-------+--------------------+
    |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
    |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
    |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
    |413708|BENCHMARK HOSPITA...|   Pass|                    |
    |413722|           JJ BURGER|   Pass|                    |
    +------+--------------------+-------+--------------------+
    ```

## <a name="understand-the-data"></a>De gegevens begrijpen

Laten we een idee krijgen van wat de gegevensset bevat. 

1. Voer de volgende code uit om de afzonderlijke waarden in de kolom **resultaten** weer te geven:

    ```PySpark
    df.select('results').distinct().show()
    ```

    Dit is de uitvoer:

    ```
    +--------------------+
    |             results|
    +--------------------+
    |                Fail|
    |Business Not Located|
    |                Pass|
    |  Pass w/ Conditions|
    |     Out of Business|
    +--------------------+
    ```

2. Voer de volgende code uit om de distributie van deze resultaten te visualiseren:

    ```PySpark
    %%sql -o countResultsdf
    SELECT COUNT(results) AS cnt, results FROM CountResults GROUP BY results
    ```

    Het `%%sql` Magic wordt gevolgd door `-o countResultsdf` ervoor te zorgen dat de uitvoer van de query lokaal wordt opgeslagen op de Jupyter-server (doorgaans de hoofd knooppunt van het cluster). De uitvoer wordt persistent gemaakt als een [Panda](https://pandas.pydata.org/) data frame met de opgegeven naam **countResultsdf**. `%%sql`Zie [kernels die beschikbaar zijn op Jupyter-notebooks met Apache Spark HDInsight-clusters](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)voor meer informatie over het Magic en andere magics die beschikbaar zijn in de PySpark-kernel.

    Dit is de uitvoer:

    ![SQL-query-uitvoer](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "SQL-query-uitvoer")

3. U kunt ook matplotlib gebruiken, een bibliotheek die wordt gebruikt om visualisatie van gegevens te maken, om een plot te creëren. Omdat het waarnemings punt moet worden gemaakt van de lokaal blijvende persistente **countResultsdf** -data frame, moet het code fragment beginnen met het `%%local` Magic. Met deze actie zorgt u ervoor dat de code lokaal wordt uitgevoerd op de Jupyter-server.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = countResultsdf['results']
    sizes = countResultsdf['cnt']
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Voor het voors pellen van een voedsel inspectie resultaat moet u een model ontwikkelen op basis van de schendingen. Omdat logistiek regressie een binaire classificatie methode is, is het zinvol om de resultaat gegevens in twee categorieën te groeperen: **mislukt** en **door geven**:

   - Geslaagd
       - Geslaagd
       - Pass-Through-voor waarden
   - Mislukt
       - Mislukt
   - Verwijderen
       - Bedrijf niet gevonden
       - Buiten bedrijf

     Gegevens met de andere resultaten (' bedrijf niet gevonden ' of ' buiten bedrijf ') zijn niet nuttig en maken toch een klein percentage van de resultaten.

4. Voer de volgende code uit om de bestaande data frame ( `df` ) te converteren naar een nieuwe data frame waarbij elke inspectie wordt weer gegeven als een combi natie van label schendingen. In dit geval vertegenwoordigt een label van `0.0` een fout, een label dat `1.0` een geslaagde waarde vertegenwoordigt en een label dat een `-1.0` resultaat oplevert naast die twee resultaten.

    ```PySpark
    def labelForResults(s):
        if s == 'Fail':
            return 0.0
        elif s == 'Pass w/ Conditions' or s == 'Pass':
            return 1.0
        else:
            return -1.0
    label = UserDefinedFunction(labelForResults, DoubleType())
    labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')
    ```

5. Voer de volgende code uit om één rij met de gelabelde gegevens weer te geven:

    ```PySpark
    labeledData.take(1)
    ```

    Dit is de uitvoer:

    ```
    [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]
    ```

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Een logistiek regressie model maken op basis van de invoer data frame

De laatste taak is het converteren van de gelabelde gegevens. Converteer de gegevens naar een indeling die door logistieke regressie kan worden geanalyseerd. De invoer van een algoritme voor logistiek regressie moet een set van *Vector paren met label functies* hebben. Waarbij "functie Vector" een vector is van getallen die het invoer punt vertegenwoordigen. Daarom moet u de kolom schendingen converteren. deze is semi-Structured en bevat veel opmerkingen in vrije tekst. De kolom converteren naar een matrix met echte getallen die een machine eenvoudig kan begrijpen.

Een standaard machine learning methode voor de verwerking van natuurlijke taal is het toewijzen van elk afzonderlijk woord een "index". Geef vervolgens een vector door aan het algoritme machine learning. Zo dat elke index waarde de relatieve frequentie van dat woord in de teken reeks bevat.

MLlib biedt een eenvoudige manier om deze bewerking uit te voeren. First ' Tokenize ' elk overtredings teken reeks om de afzonderlijke woorden in elke teken reeks op te halen. Gebruik vervolgens een `HashingTF` om elke set tokens te converteren naar een functie Vector die vervolgens kan worden door gegeven aan het algoritme voor logistieke regressie om een model te maken. U voert al deze stappen in volg orde uit met behulp van een ' pijp lijn '.

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>Het model evalueren met een andere gegevensset

U kunt het model dat u eerder hebt gemaakt, gebruiken om te voors *pellen* wat de resultaten van nieuwe inspecties zijn. De voor spellingen zijn gebaseerd op de schendingen die zijn waargenomen. U hebt dit model getraind op de gegevensset **Food_Inspections1.csv**. U kunt een tweede gegevensset **Food_Inspections2.csv** gebruiken om de kracht van dit model op de nieuwe gegevens te *evalueren* . Deze tweede gegevensset (**Food_Inspections2.csv**) bevindt zich in de standaard opslag container die aan het cluster is gekoppeld.

1. Voer de volgende code uit om een nieuwe data frame te maken, **predictionsDf** die de voor spelling bevat die door het model is gegenereerd. Het fragment maakt ook een tijdelijke tabel met de naam voor **spellingen** op basis van de data frame.

    ```PySpark
    testData = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                .map(csvParse) \
                .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
    testDf = spark.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
    predictionsDf = model.transform(testDf)
    predictionsDf.registerTempTable('Predictions')
    predictionsDf.columns
    ```

    U ziet een uitvoer zoals de volgende tekst:

    ```
    ['id',
        'name',
        'results',
        'violations',
        'words',
        'features',
        'rawPrediction',
        'probability',
        'prediction']
    ```

1. Bekijk een van de voor spellingen. Voer dit fragment uit:

    ```PySpark
    predictionsDf.take(1)
    ```

   Er is een voor spelling voor het eerste item in de test gegevensset.

1. Met de- `model.transform()` methode wordt dezelfde trans formatie toegepast op alle nieuwe gegevens met hetzelfde schema, en arriveert een voor spelling van het classificeren van de gegevens. U kunt een aantal statistieken doen om een beeld te krijgen van de manier waarop de voor spellingen zijn:

    ```PySpark
    numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                            (prediction = 1 AND (results = 'Pass' OR
                                                                results = 'Pass w/ Conditions'))""").count()
    numInspections = predictionsDf.count()

    print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
    print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"
    ```

    De uitvoer ziet er als volgt uit:

    ```
    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    Door gebruik te maken van logistiek regressie met Spark krijgt u een model van de relatie tussen schendingen in het Engels. En of een bepaald bedrijf een voedsel inspectie zou slagen of mislukken.

## <a name="create-a-visual-representation-of-the-prediction"></a>Een visuele weergave van de voorspelling maken

U kunt nu een definitieve visualisatie maken om u te helpen de testresultaten te beoordelen.

1. U begint met het extra heren van de verschillende voor spellingen en resultaten van de tijdelijke tabel met voor **spellingen** die u eerder hebt gemaakt. De volgende query's scheiden de uitvoer als *true_positive*, *false_positive*, *true_negative* en *false_negative*. In de onderstaande query's schakelt u visualisatie uit met behulp van `-q` en slaat u ook de uitvoer (met behulp van `-o` ) op als dataframes die vervolgens met het Magic kan worden gebruikt `%%local` .

    ```PySpark
    %%sql -q -o true_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

    ```PySpark
    %%sql -q -o true_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

1. Gebruik ten slotte het volgende code fragment voor het genereren van het plot met behulp van **matplotlib**.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = ['True positive', 'False positive', 'True negative', 'False negative']
    sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    U moet de volgende uitvoer zien:

    ![Spark machine learning toepassings uitvoer-cirkel diagram percentages van mislukte voedings inspecties.](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Uitvoer resultaat van Spark machine learning")

    In dit diagram verwijst een ' positief ' resultaat naar de mislukte voedsel inspectie, terwijl een negatief resultaat naar een geslaagde inspectie verwijst.

## <a name="shut-down-the-notebook"></a>Het notitie blok afsluiten

Wanneer u klaar bent met het uitvoeren van de toepassing, moet u het notitie blok afsluiten om de resources vrij te geven. Selecteer hiervoor **Sluiten en stoppen** in het menu **Bestand** van het notebook. Met deze actie wordt het notebook afgesloten en gesloten.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)
* [Analyse van website logboeken met Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Microsoft Cognitive Toolkit diepe leer model met Azure HDInsight](apache-spark-microsoft-cognitive-toolkit.md)
