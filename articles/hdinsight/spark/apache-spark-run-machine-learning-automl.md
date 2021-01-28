---
title: Azure Machine Learning werk belastingen uitvoeren op Apache Spark in HDInsight
description: Meer informatie over het uitvoeren van Azure Machine Learning workloads met geautomatiseerde machine learning (AutoML) op Apache Spark in azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/13/2019
ms.openlocfilehash: 4087341a9a96ae56c00972f886ce3cc8891750a5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929737"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-on-apache-spark-in-hdinsight"></a>Azure Machine Learning werk belastingen uitvoeren met geautomatiseerde machine learning op Apache Spark in HDInsight

Azure Machine Learning vereenvoudigt en versnelt het bouwen, trainen en implementeren van machine learning modellen. In automatische machine learning (AutoML) begint u met trainings gegevens met een gedefinieerde doel functie en herhaalt u vervolgens combi Naties van algoritmen en functie selecties om automatisch het beste model voor uw gegevens te selecteren op basis van de trainings scores. Met HDInsight kunnen klanten clusters met honderden knoop punten inrichten. Met AutoML die op Spark in een HDInsight-cluster wordt uitgevoerd, kunnen gebruikers reken capaciteit op deze knoop punten gebruiken om trainings taken op een schaal bare manier uit te voeren en om meerdere trainings taken parallel uit te voeren. Hiermee kunnen gebruikers AutoML experimenten uitvoeren tijdens het delen van de reken kracht met hun andere big data workloads.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Azure Machine Learning installeren op een HDInsight-cluster

Voor algemene zelf studies van automatische machine learning raadpleegt u [zelf studie: geautomatiseerd machine learning gebruiken om uw regressie model te maken](../../machine-learning/tutorial-auto-train-models.md).
Alle nieuwe HDInsight-Spark-clusters worden vooraf geïnstalleerd met AzureML-AutoML SDK.

> [!Note]
> Azure Machine Learning pakketten worden geïnstalleerd in de Python3 Conda-omgeving. De geïnstalleerde Jupyter Notebook moet worden uitgevoerd met behulp van de PySpark3-kernel.

U kunt ook Zeppelin-notebooks gebruiken voor het gebruik van AutoML.

> [!Note]
> Zeppelin heeft een [bekend probleem](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) waarbij PySpark3 niet de juiste versie van python selecteert. Gebruik de gedocumenteerde omzeilde werkzaamheden.

## <a name="authentication-for-workspace"></a>Verificatie voor werk ruimte

Voor het maken en experimenteren van de werk ruimte is een verificatie token vereist. Dit token kan worden gegenereerd met een [Azure AD-toepassing](../../active-directory/develop/app-objects-and-service-principals.md). Een [Azure AD-gebruiker](/azure/python/python-sdk-azure-authenticate) kan ook worden gebruikt voor het genereren van het vereiste verificatie token als multi-factor Authentication niet is ingeschakeld voor het account.  

Met het volgende code fragment wordt een verificatie token gemaakt met behulp van een **Azure AD-toepassing**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
    tenant_id='<Azure Tenant ID>',
    service_principal_id='<Azure AD Application ID>',
    service_principal_password='<Azure AD Application Key>'
)
```

Met het volgende code fragment wordt een verificatie token gemaakt met behulp van een **Azure AD-gebruiker**.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com', 'my_smart_password')
```

## <a name="loading-dataset"></a>Gegevensset laden

Automatische machine learning op Spark maakt gebruik van **gegevens stromen**, die vertraagd geëvalueerd, onveranderbare bewerkingen op gegevens.  Een gegevensstroom kan een gegevensset laden vanuit een blob met open bare Lees toegang of vanuit een BLOB-URL met een SAS-token.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(
    path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

U kunt de gegevens opslag ook registreren bij de werk ruimte met behulp van een eenmalige registratie.

## <a name="experiment-submission"></a>Inzending experimenteren

In de [configuratie van automatische machine learning](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)moet de eigenschap `spark_context` worden ingesteld voor het uitvoeren van het pakket op de gedistribueerde modus. De eigenschap `concurrent_iterations` , waarmee het maximum aantal iteraties wordt uitgevoerd, moet worden ingesteld op een waarde die kleiner is dan de uitvoer kernen voor de Spark-app.

## <a name="next-steps"></a>Volgende stappen

* Voor meer informatie over de motivatie achter automatische machine learning, Zie [release modellen in tempo met behulp van automatische machine learning van micro soft.](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* Zie voor meer informatie over het gebruik van Azure ML-functies voor geautomatiseerd ML [nieuwe functies voor automatische machine learning in azure machine learning](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [AutoML-project van micro soft Research](https://www.microsoft.com/research/project/automl/)
