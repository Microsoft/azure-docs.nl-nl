---
title: Grondige kennis-geavanceerde analyses-Azure HDInsight
description: Meer informatie over hoe geavanceerde analyse algoritmen gebruikt voor het verwerken van big data in azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 0780f66c981f0cebebc1ab327d783954753db965
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866723"
---
# <a name="deep-dive---advanced-analytics"></a>Diep gaande kennis-geavanceerde analyse

## <a name="what-is-advanced-analytics-for-hdinsight"></a>Wat is geavanceerde analyse voor HDInsight?

HDInsight biedt de mogelijkheid om waardevol inzicht te krijgen in grote hoeveel heden gestructureerde, ongestructureerde en snel bewegende gegevens. Geavanceerde analyses zijn het gebruik van uiterst schaal bare architecturen, statistische en machine learning modellen, en intelligente Dash boards om u te voorzien van duidelijke inzichten. Machine learning, of *Predictive Analytics*, gebruikt algoritmen voor het identificeren en leren van relaties in uw gegevens om voor spellingen te maken en uw beslissingen te begeleiden.

## <a name="advanced-analytics-process"></a>Geavanceerd analyse proces

:::image type="content" source="./media/apache-hadoop-deep-dive-advanced-analytics/hdinsight-analytic-process.png" alt-text="Geavanceerde analyse proces stroom" border="false":::

Nadat u het bedrijfs probleem hebt geïdentificeerd en de gegevens hebt verzameld en verwerkt, moet u een model maken dat de vraag vertegenwoordigt die u wilt voors pellen. In uw model worden een of meer machine learning algoritmen gebruikt om het type voor spelling te maken dat het beste bij uw bedrijfs behoeften past.  Het meren deel van uw gegevens moet worden gebruikt om het model te trainen, met de rest die wordt gebruikt om het te testen of te evalueren.

Nadat u uw model hebt gemaakt, geladen, getest en geëvalueerd, is de volgende stap het implementeren van uw model, zodat het begint met het leveren van antwoorden op uw vragen. De laatste stap bestaat uit het bewaken van de prestaties van uw model en het afstemmen hiervan.

## <a name="common-types-of-algorithms"></a>Algemene typen algoritmen

Geavanceerde analyse oplossingen bieden een reeks machine learning-algoritmen. Hier volgt een samen vatting van de categorie algoritmen en de bijbehorende veelvoorkomende cases voor bedrijfs gebruik.

:::image type="content" source="./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-use-cases.png" alt-text="Samen vattingen van Machine Learning categorie" border="false":::

Samen met het selecteren van de beste algoritme (s), moet u overwegen of u gegevens moet opgeven voor de training. Machine learning-algoritmen worden als volgt gecategoriseerd:

* Super visie: algoritme moet worden getraind voor een set met gelabelde gegevens voordat er resultaten kunnen worden opgegeven
* Een semi-Super visie: algoritme kan worden uitgebreid door extra doelen via interactieve query's door een trainer, die tijdens de eerste fase van de training niet beschikbaar is.
* Niet onder Super visie: algoritme vereist geen trainings gegevens
* Versterking-algoritme maakt gebruik van software-agents om het ideale gedrag binnen een specifieke context te bepalen (vaak gebruikt in Robotics)

| Categorie Algorithm| Gebruik | Type training | Algoritmen |
| --- | --- | --- | -- |
| Classificatie | Mensen of dingen classificeren in groepen | Onder supervisie | Beslissings structuren, logistiek regressie, Neural netwerken |
| Clustering | Een reeks voor beelden delen in homogene groepen | Zonder super visie | k-means clustering |
| Patroon detectie | Veelgebruikte koppelingen in de gegevens identificeren | Zonder super visie | Association Rules |
| Regressie | Numerieke resultaten voors pellen | Onder supervisie | Lineaire regressie, Neural netwerken |
| Bekrachtigingen | Optimale werking van robots bepalen | Bekrachtigingen | Monte Carlo-simulaties, DeepMind |

## <a name="machine-learning-on-hdinsight"></a>Machine learning op HDInsight

HDInsight heeft verschillende machine learning opties voor een geavanceerde analyse werk stroom:

* Machine Learning en Apache Spark
* R-en ML-Services
* Azure Machine Learning en Apache Hive
* Apache Spark en diep gaande lessen

### <a name="machine-learning-and-apache-spark"></a>Machine Learning en Apache Spark

[HDInsight Spark](../spark/apache-spark-overview.md) is een door Azure gehoste aanbieding van [Apache Spark](https://spark.apache.org/), een gecombineerd, open source, parallel data processing framework dat gebruikmaakt van in-Memory verwerking om de analyse van Big data te stimuleren. De Spark-verwerkings engine is gebouwd voor snelheid, gebruiks gemak en geavanceerde analyses. Met de mogelijkheden van de gedistribueerde reken kracht van Spark in het geheugen is het een goede keuze voor de iteratieve algoritmen die worden gebruikt in machine learning-en grafiek berekeningen.

Er zijn drie schaal bare machine learning bibliotheken die algoritmen model leren met deze gedistribueerde omgeving:

* [**MLlib**](https://spark.apache.org/docs/latest/ml-guide.html) -MLlib bevat de oorspronkelijke API die boven op Spark-rdd's is gebouwd.
* [**SparkML**](https://spark.apache.org/docs/1.2.2/ml-guide.html) -SparkML is een nieuwer pakket met een op Spark-DataFrames gebouwde API met een hoger niveau voor het bouwen van ml-pijp lijnen.
* [**MMLSpark**](https://github.com/Azure/mmlspark)  : de micro soft machine learning-bibliotheek voor Apache Spark (MMLSpark) is ontworpen om gegevens wetenschappers productiever te maken op Spark, om het aantal experimenten te verhogen en te profiteren van geavanceerde machine learning technieken, waaronder diep gaande informatie over zeer grote gegevens sets. De MMLSpark-bibliotheek vereenvoudigt veelvoorkomende model taken voor het bouwen van modellen in PySpark.

### <a name="r-and-ml-services"></a>R-en ML-Services

Als onderdeel van HDInsight kunt u een HDInsight-cluster maken met [ml Services](../r-server/r-server-overview.md) die klaar zijn voor gebruik met enorme gegevens sets en modellen. Deze nieuwe mogelijkheid biedt gegevens wetenschappers en statistici met een vertrouwde R-interface die op aanvraag kan worden geschaald via HDInsight, zonder de overhead van het instellen en onderhouden van het cluster.

### <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning en Apache Hive

[Azure machine learning Studio (klassiek)](https://studio.azureml.net/) voorziet in hulpprogram ma's voor het model leren van Predictive Analytics en een volledig beheerde service die u kunt gebruiken om uw voorspellende modellen te implementeren als kant-en-klare webservices. Azure Machine Learning voorziet in hulpprogram ma's voor het maken van volledige predictive analytics-oplossingen in de cloud om snel voorspellende modellen te maken, te testen, te operationeel maken en te beheren. Maak een keuze uit een grote algoritme bibliotheek, gebruik een webgebaseerde Studio voor het ontwikkelen van modellen en implementeer uw model eenvoudig als een webservice.

### <a name="apache-spark-and-deep-learning"></a>Apache Spark en diep gaande lessen

[Grondige kennis](https://www.microsoft.com/research/group/dltc/) is een vertakking van machine learning die gebruikmaakt van *diepe Neural Networks* (DNNs), die zijn geïnspireerd op de biologische processen van het menselijk gebruik. Veel onderzoekers zien een diep gaande leer proces als een toezeggings benadering voor kunst matige intelligentie. Enkele voor beelden van diep gaande lessen zijn gesp roken taal vertalers, systemen voor afbeeldings herkenning en de reden van de computer. Micro soft heeft de gratis, gebruiks vriendelijke, open-source [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)ontwikkeld om haar eigen werk in het uitgebreid leren te helpen. De Toolkit wordt uitgebreid gebruikt door een groot aantal verschillende micro soft-producten, door bedrijven wereld wijd, met een nood zaak om diep gaande lessen op schaal te implementeren en door studenten die geïnteresseerd zijn in de nieuwste algoritmen en technieken.

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Scenario-Score afbeeldingen om patronen in stedelijke ontwikkeling te identificeren

Laten we een voor beeld bekijken van een geavanceerde analyse machine learning pijp lijn met behulp van HDInsight.

In dit scenario ziet u hoe DNNs geproduceerd in een diep Learning Framework, micro soft-Cognitive Toolkit (CNTK), kan worden gebruikt voor het scoren van grote afbeeldings verzamelingen die zijn opgeslagen in een Azure Blob Storage-account met behulp van PySpark in een HDInsight Spark-cluster. Deze aanpak wordt toegepast op een gemeen schappelijk DNN use-case, een foto-afbeeldings classificatie en kan worden gebruikt om recente patronen in stedelijke ontwikkeling te identificeren.  U gebruikt een vooraf getraind afbeeldings classificatie model. Het model is vooraf getraind op de [CIFAR-10-gegevensset](https://www.cs.toronto.edu/~kriz/cifar.html) en is toegepast op 10.000 Inge houden installatie kopieën.

Er zijn drie belang rijke taken in dit geavanceerde analyse scenario:

1. Maak een Azure HDInsight Hadoop cluster met een Apache Spark 2.1.0-distributie.
2. Voer een aangepast script uit om Microsoft Cognitive Toolkit te installeren op alle knoop punten van een Azure HDInsight Spark cluster.
3. Upload een vooraf gebouwde Jupyter Notebook naar uw HDInsight Spark-cluster om een getraind Microsoft Cognitive Toolkit diep leer model toe te passen op bestanden in een Azure Blob Storage-account met behulp van de Spark python API (PySpark).

In dit voor beeld wordt de CIFAR-10-installatie kopieset gebruikt die is gecompileerd en gedistribueerd door Alex Krizhevsky, wijnbouw Nair en Geoffrey Hinton,. De CIFAR-10-gegevensset bevat 60.000 32 × 32 kleuren afbeeldingen die deel uitmaken van tien wederzijds exclusieve klassen:

:::image type="content" source="./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-images.png" alt-text="Voorbeeld afbeeldingen Machine Learning" border="false":::

Voor meer informatie over de gegevensset raadpleegt u het leer proces van Alex Krizhevsky [meerdere lagen van functies van kleine afbeeldingen](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf).

De gegevensset is gepartitioneerd in een Trainingsset van 50.000 installatie kopieën en een testset van 10.000 installatie kopieën. De eerste set is gebruikt om een ResNet-model (20-Layer-diepe convolutional rerest Network) te trainen met Microsoft Cognitive Toolkit door [deze zelf studie](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) te volgen vanuit de Cognitive Toolkit github-opslag plaats. De resterende 10.000 installatie kopieën zijn gebruikt voor het testen van de nauw keurigheid van het model. Dit is de plaats waar gedistribueerde computing speelt: de taak van voor bereiding en het scoren van de afbeeldingen is zeer kan worden opgestart. We hebben het opgeslagen getrainde model hand matig gebruikt:

* PySpark voor het distribueren van de afbeeldingen en het getrainde model naar de worker-knoop punten van het cluster.
* Python voor het vooraf verwerken van de installatie kopieën op elk knoop punt van het HDInsight Spark-cluster.
* Cognitive Toolkit het model te laden en de vooraf verwerkte installatie kopieën op elk knoop punt te scoren.
* Jupyter-notebooks om het PySpark-script uit te voeren, de resultaten te aggregeren en [matplotlib](https://matplotlib.org/) te gebruiken om de model prestaties te visualiseren.

De volledige voor verwerking/Score van de 10.000-installatie kopieën duurt minder dan één minuut op een cluster met 4 worker-knoop punten. Het model voor spelt nauw keurig de labels van ~ 9.100 (91%) installatie kopieën. Een Verwar ring matrix illustreert de meest voorkomende classificatie fouten. De matrix laat bijvoorbeeld zien dat het labelen van honden als katten ongedaan wordt gemaakt en omgekeerd meer dan voor andere label paren plaatsvindt.

:::image type="content" source="./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-results.png" alt-text="Grafiek met Machine Learning resultaten" border="false":::

### <a name="try-it-out"></a>Probeer het eens!

Volg [deze zelf studie](../spark/apache-spark-microsoft-cognitive-toolkit.md) om de volledige end-to-end-oplossing te implementeren: Stel een HDInsight Spark-cluster in, installeer Cognitive Toolkit en voer het Jupyter notebook uit dat 10.000 CIFAR-installatie kopieën afrondt.

## <a name="next-steps"></a>Volgende stappen

Apache Hive en Azure Machine Learning

* [Apache Hive en Azure Machine Learning end-to-end](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Een Azure HDInsight Hadoop cluster gebruiken op een gegevensset van 1 TB](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Apache Spark en MLLib

* [Machine learning met Apache Spark op HDInsight](../../machine-learning/team-data-science-process/spark-overview.md)
* [Apache Spark met Machine Learning: Apache Spark in HDInsight gebruiken voor het analyseren van de gebouw temperatuur met behulp van HVAC-gegevens](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Apache Spark in HDInsight gebruiken om voedsel inspectie resultaten te voors pellen](../spark/apache-spark-machine-learning-mllib-ipython.md)

Diep leren, Cognitive Toolkit en anderen

* [Azure virtual machine voor data technologie](../../machine-learning/data-science-virtual-machine/overview.md)
* [Introductie van H2O.ai op Azure HDInsight](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
