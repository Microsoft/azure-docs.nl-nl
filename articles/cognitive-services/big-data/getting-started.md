---
title: Aan de slag met Cognitive Services voor Big Data
description: Stel uw MMLSpark-pijplijn in met Cognitive Services in Azure Databricks en voer een voorbeeld uit.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: 095f2c3ed17042bb616fb091d1af52a64c913709
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460493"
---
# <a name="getting-started"></a>Aan de slag

Uw omgeving instellen is de eerste stap om een pijplijn te ontwerpen voor uw gegevens. Wanneer uw omgeving klaar is, kunt u gemakkelijk en snel een voorbeeld uitvoeren.

In dit artikel voeren we de volgende stappen uit om u op weg te helpen:

1. [Een Cognitive Services-resource maken](#create-a-cognitive-services-resource)
1. [Een Apache Spark-cluster maken](#create-an-apache-spark-cluster)
1. [Een voorbeeld uitproberen](#try-a-sample)

## <a name="create-a-cognitive-services-resource"></a>Een Cognitive Services-resource maken

Als u de Big Data Cognitive Services wilt gebruiken, moet u eerst een Cognitive Service maken voor onze werkstroom. Er zijn twee voorname types Cognitive Services: cloudservices die worden gehost in Azure en in een container geplaatste services die worden beheerd door gebruikers. We raden u aan om te beginnen met het eenvoudigere, cloudgebaseerde Cloud Cognitive Services.

### <a name="cloud-services"></a>Cloud services

Cloudgebaseerde Cognitive Services zijn intelligente algoritmen die worden gehost in Azure. Deze services zijn klaar voor gebruik zonder training. U hebt hiervoor alleen een internetverbinding nodig. U kunt [een Cognitive Service maken in het Azure-portal](../cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows) of met de [Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows).

### <a name="containerized-services-optional"></a>In een container geplaatste services (optioneel)

Als uw toepassing of workload grote gegevenssets gebruikt, een particulier netwerk nodig heeft of geen contact kan maken met de cloud, kan communicatie met cloudservices wel eens niet mogelijk zijn. In deze situatie bieden in een container geplaatste Cognitive Services de volgende voordelen:

* **Lage connectiviteit**: U kunt in een container geplaatste Cognitive Services implementeren in elke rekenomgeving, binnen als buiten de cloud. Als uw toepassing geen contact kan maken met de cloud, overweeg dan om in een container geplaatste Cloud Services te implementeren in uw toepassing.

* **Lage latentie**: Omdat voor in een container geplaatste services geen retourcommunicatie naar/van de cloud vereist is, ligt de latentie voor antwoorden veel lager.

* **Privacy en gegevensbeveiliging**: U kunt in een container geplaatste services implementeren in privénetwerken, zodat gevoelige gegevens het netwerk niet moeten verlaten.

* **Hoge schaalbaarheid**: In een container geplaatste services hebben geen 'frequentielimieten' en worden uitgevoerd op computers die worden beheerd door de gebruiker. U kunt dus Cognitive Services schalen zonder veel grotere workloads te verwerken.

Volg [deze handleiding](../cognitive-services-container-support.md?tabs=luis) voor het maken van een in een container geplaatste Cognitive Service.

## <a name="create-an-apache-spark-cluster"></a>Een Apache Spark-cluster maken

[Apache Spark&trade;](http://spark.apache.org/) is een gedistribueerd rekennetwerk dat is ontworpen voor de verwerking van big data. Gebruikers kunnen werken met Apache Spark in Azure met services zoals Azure Databricks, Azure Synapse Analytics, HDInsight en Azure Kubernetes Services. Als u de Big Data Cognitive Services wilt gebruiken, moet u eerst een cluster maken. Als u al een Spark-cluster hebt, dan kunt u een voorbeeld uitproberen.

### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks is een op Apache Spark gebaseerd analyseplatform dat met één klik geconfigureerd kan worden, gestroomlijnde werkstromen en een interactieve werkruimte heeft. Het wordt vaak gebruikt om samen te werken tussen gegevenswetenschappers, technici en bedrijfsanalisten. Als u de Big Data Cognitive Services in Azure Databricks wilt gebruiken, volgt u deze stappen:

1. [Een Azure Databricks-werkruimte maken](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)
1. [Een Spark-cluster maken in Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal#create-a-spark-cluster-in-databricks)
1. De Big Data Cognitive Services installeren
    * Maak een nieuwe bibliotheek in uw databricks-werkruimte  
       <img src="media/create-library.png" alt="Create library" width="50%"/>
    * Voer de volgende maven-coördinaten in:  `com.microsoft.ml.spark:mmlspark_2.11:1.0.0-rc3`Opslagplaats: `https://mmlspark.azureedge.net/maven`  
      <img src="media/library-coordinates.png" alt="Library Coordinates" width="50%"/>
    * Installeer de bibliotheek op een cluster  
      <img src="media/install-library.png" alt="Install Library on Cluster" width="50%"/>

### <a name="azure-synapse-analytics-optional"></a>Azure Synapse Analytics (optioneel)

U kunt Synapse Analytics ook gebruiken om een Spark-cluster te maken. Azure Synapse Analytics brengt datawarehousing voor ondernemingen en big data-analyses samen. Deze geeft u de vrijheid om op schaal gegevens op te vragen over uw voorwaarden, met behulp van serverloze on-demand of ingerichte resources. Voer de volgende stappen uit om aan de slag te gaan met Azure Synapse Analytics:

1. [Maak een Synapse-werkruimte (preview)](../../synapse-analytics/quickstart-create-workspace.md).
1. [Maak een nieuwe serverloze Apache Spark-pool (preview) met behulp van Azure Portal](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md).

Big data voor Cognitive Services is standaard geïnstalleerd in Azure Synapse Analytics.

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Als u in een container geplaatste Cognitive Services gebruikt, is de Azure Kubernetes Service een populaire optie om Spark samen met containers te implementeren.

Om aan de slag te gaan met Azure Kubernetes Service, volgt u deze stappen:

1. [Een AKS-cluster (Azure Kubernetes Service) implementeren met behulp van de Azure-portal](../../aks/kubernetes-walkthrough-portal.md)
1. [Installeer de Helm-grafiek van Apache Spark 2.4.0](https://hub.helm.sh/charts/microsoft/spark)
1. [Installeer een Cognitive Service-container met behulp van Helm](../computer-vision/deploy-computer-vision-on-premises.md)

## <a name="try-a-sample"></a>Een voorbeeld uitproberen

Nadat u de Spark-cluster en -omgeving hebt ingesteld, kunnen we een kort voorbeeld uitvoeren. In deze sectie wordt gedemonstreerd hoe u de Big Data voor Cognitive Services gebruikt in Azure Databricks.

Eerst kunnen we een notitieblok maken in Azure Databricks. Gebruik voor andere Spark-clusterproviders hun notebooks of Spark Submit.

1. Maak een nieuw Databricks-notebook door **Nieuwe notebook** te kiezen in het menu **Azure Databricks**.

    <img src="media/new-notebook.png" alt="Create a new notebook" width="50%"/>

1. Voer in het dialoogvenster **Notitieblok maken** een naam in, selecteer **Python** als taal en selecteer het Spark-cluster dat u eerder hebt gemaakt.

    <img src="media/databricks-notebook-details.jpg" alt="New notebook details" width="50%"/>

    Selecteer **Maken**.

1. Plak dit codefragment in uw nieuwe notebook.

```python
from mmlspark.cognitive import *
from pyspark.sql.functions import col

# Add your subscription key from Text Analytics (or a general Cognitive Service key)
service_key = "ADD-SUBSCRIPTION-KEY-HERE"

df = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US"),
], ["text", "language"])

sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

results = sentiment.transform(df)

# Show the results in a table
display(results.select("text", col("sentiment")[0].getItem("score").alias("sentiment")))

```

1. Haal uw abonnementssleutel op uit het menu **Sleutels en eindpunt** van uw Text Analytics-dashboard in het Azure-portal.
1. Vervang de tijdelijke aanduiding voor de abonnementssleutel in de code in uw Databricks-notebook door uw abonnementssleutel.
1. Selecteer in de rechterbovenhoek van uw notebook-cel het symbool voor afspelen, of de driehoek, om het voorbeeld uit te voeren. U kunt ook **Alles uitvoeren** selecteren bovenaan uw notebook om alle cellen uit te voeren. De antwoorden worden weergegeven onder de cel in een tabel.

### <a name="expected-results"></a>Verwachte resultaten

| tekst                                      |   gevoel |
|:------------------------------------------|------------:|
| Ik ben zo blij vandaag, de zon schijnt!           |   0,978959  |
| Ik ben geïrriteerd vanwege het verkeer in de spits |   0,0237956 |
| De cognitieve services in Spark zijn niet slecht  |   0,888896  |

## <a name="next-steps"></a>Volgende stappen

- [Korte Python-voorbeelden](samples-python.md)
- [Korte Scala-voorbeelden](samples-scala.md)
- [Recept: Voorspellend onderhoud](recipes/anomaly-detection.md)
- [Recept: Verkenning van intelligente kunst](recipes/art-explorer.md)