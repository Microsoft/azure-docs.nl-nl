---
title: Gegevens bewaken en verzamelen van Machine Learning webservice-eindpunten
titleSuffix: Azure Machine Learning
description: Meer informatie over het verzamelen van gegevens van modellen die zijn geïmplementeerd op webservice-eindpunten in Azure Kubernetes Service (AKS) of Azure Container Instances (ACI).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: blackmist
ms.date: 09/15/2020
ms.topic: how-to
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: f3853ecc5a3741b485f779581da387b133065ed5
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107889354"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Gegevens van ML-webservice-eindpunten bewaken en verzamelen


In dit artikel leert u hoe u gegevens verzamelt van modellen die zijn geïmplementeerd op webservice-eindpunten in Azure Kubernetes Service (AKS) of Azure Container Instances (ACI). Gebruik [Azure-toepassing Insights om](../azure-monitor/app/app-insights-overview.md) de volgende gegevens van een eindpunt te verzamelen:
* Uitvoergegevens
* Antwoorden
* Aanvraagsnelheden, reactietijden en foutpercentages
* Afhankelijkheidssnelheden, reactietijden en foutpercentages
* Uitzonderingen

In het notebook [enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) worden concepten in dit artikel gedemonstreerd.
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]
 
## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement: probeer de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).

* Een Azure Machine Learning werkruimte, een lokale map met uw scripts en de Azure Machine Learning SDK voor Python geïnstalleerd. Zie Een ontwikkelomgeving configureren [voor meer informatie.](how-to-configure-environment.md)

* Een getraind machine learning model. Zie de zelfstudie [Afbeeldingsclassificatiemodel trainen voor meer](tutorial-train-models-with-aml.md) informatie.

<a name="python"></a>

## <a name="configure-logging-with-the-python-sdk"></a>Logboekregistratie configureren met de Python-SDK

In deze sectie leert u hoe u Application Insight-logboekregistratie kunt inschakelen met behulp van de Python SDK. 

### <a name="update-a-deployed-service"></a>Een geïmplementeerde service bijwerken

Gebruik de volgende stappen om een bestaande webservice bij te werken:

1. Identificeer de service in uw werkruimte. De waarde voor `ws` is de naam van uw werkruimte

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Uw service bijwerken en Azure-toepassing Insights inschakelen

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Aangepaste traceringen in uw service aanmelden

> [!IMPORTANT]
> Azure-toepassing Insights registreert alleen nettoladingen van maximaal 64 kB. Als deze limiet is bereikt, ziet u mogelijk fouten zoals geheugenverlies of wordt er geen informatie geregistreerd. Als de gegevens die u wilt logboeken groter zijn dan 64 kB, moet u deze opslaan in blob-opslag met behulp van de informatie in Gegevens verzamelen voor modellen [in productie.](how-to-enable-data-collection.md)
>
> Voor complexere situaties, zoals het bijhouden van modellen binnen een AKS-implementatie, raden we u aan een bibliotheek van derden te gebruiken, zoals [OpenCensus.](https://opencensus.io)

Als u aangepaste traceringen wilt logboeken, volgt u het standaardimplementatieproces voor AKS of ACI in het document [Implementeren en waar.](how-to-deploy-and-where.md) Gebruik vervolgens de volgende stappen:

1. Werk het scoring-bestand bij door afdrukverklaringen toe te voegen om gegevens naar Application Insights tijdens de deferentie te verzenden. Gebruik een JSON-structuur voor complexere informatie, zoals de aanvraaggegevens en het antwoord. 

    Het volgende voorbeeldbestand registreert wanneer het model is geinitialiseerd, invoer en uitvoer tijdens de deferentie en het tijdstip `score.py` waarop er fouten optreden.

    
    ```python
    import pickle
    import json
    import numpy 
    from sklearn.externals import joblib
    from sklearn.linear_model import Ridge
    from azureml.core.model import Model
    import time

    def init():
        global model
        #Print statement for appinsights custom traces:
        print ("model initialized" + time.strftime("%H:%M:%S"))
        
        # note here "sklearn_regression_model.pkl" is the name of the model registered under the workspace
        # this call should return the path to the model.pkl file on the local disk.
        model_path = Model.get_model_path(model_name = 'sklearn_regression_model.pkl')
        
        # deserialize the model file back into a sklearn model
        model = joblib.load(model_path)
    

    # note you can pass in multiple rows for scoring
    def run(raw_data):
        try:
            data = json.loads(raw_data)['data']
            data = numpy.array(data)
            result = model.predict(data)
            # Log the input and output data to appinsights:
            info = {
                "input": raw_data,
                "output": result.tolist()
                }
            print(json.dumps(info))
            # you can return any datatype as long as it is JSON-serializable
            return result.tolist()
        except Exception as e:
            error = str(e)
            print (error + time.strftime("%H:%M:%S"))
            return error
    ```

2. Werk de serviceconfiguratie bij en zorg ervoor dat u deze Application Insights.
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Bouw een afbeelding en implementeer deze op AKS of ACI. Zie Implementeren en waar voor [meer informatie.](how-to-deploy-and-where.md)


### <a name="disable-tracking-in-python"></a>Tracering uitschakelen in Python

Als u Azure-toepassing Insights wilt uitschakelen, gebruikt u de volgende code:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="configure-logging-with-azure-machine-learning-studio"></a>Logboekregistratie configureren met Azure Machine Learning-studio

U kunt inzichten ook Azure-toepassing inschakelen vanuit Azure Machine Learning-studio. Wanneer u klaar bent om uw model als een webservice te implementeren, gebruikt u de volgende stappen om de volgende stappen Application Insights:

1. Meld u aan bij de studio op https://ml.azure.com .
1. Ga naar **Modellen** en selecteer het model dat u wilt implementeren.
1. Selecteer **+Implementeren.**
1. Vul het formulier **Model** implementeren in.
1. Vouw het menu **Geavanceerd** uit.

    ![Formulier implementeren](./media/how-to-enable-app-insights/deploy-form.png)
1. Selecteer **Enable Application Insights diagnostics and data collection**.

    ![App Insights inschakelen](./media/how-to-enable-app-insights/enable-app-insights.png)

## <a name="view-metrics-and-logs"></a>Metrische gegevens en logboeken weergeven

### <a name="query-logs-for-deployed-models"></a>Query's uitvoeren op logboeken voor geïmplementeerde modellen

Logboeken van realtime-eindpunten zijn klantgegevens. U kunt de functie gebruiken `get_logs()` om logboeken op te halen uit een eerder geïmplementeerde webservice. De logboeken kunnen gedetailleerde informatie bevatten over fouten die zijn opgetreden tijdens de implementatie.

```python
from azureml.core import Workspace
from azureml.core.webservice import Webservice

ws = Workspace.from_config()

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Als u meerdere tenants hebt, moet u mogelijk de volgende verificatiecode toevoegen voordat u `ws = Workspace.from_config()`

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="the tenant_id in which your workspace resides")
```

### <a name="view-logs-in-the-studio"></a>Logboeken weergeven in de studio

Azure-toepassing Insights slaat uw servicelogboeken op in dezelfde resourcegroep als de Azure Machine Learning werkruimte. Gebruik de volgende stappen om uw gegevens weer te geven met behulp van de studio:

1. Ga naar Azure Machine Learning werkruimte in [de studio](https://ml.azure.com/).
1. Selecteer **Eindpunten.**
1. Selecteer de geïmplementeerde service.
1. Selecteer de **Application Insights URL.**

    [![Zoek Application Insights URL](./media/how-to-enable-app-insights/appinsightsloc.png)](././media/how-to-enable-app-insights/appinsightsloc.png#lightbox)

1. Selecteer Application Insights op het **tabblad Overzicht** of de sectie __Bewaking__ de optie __Logboeken.__

    [![Tabblad Overzicht van bewaking](./media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. Als u gegevens wilt weergeven die zijn vastgelegd vanuit score.py bestand, bekijkt u de __traceringentabel.__ Met de volgende query wordt gezocht naar logboeken waarin de __invoerwaarde__ is vastgelegd:

    ```kusto
    traces
    | where customDimensions contains "input"
    | limit 10
    ```

   [![traceergegevens](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)

Zie Wat [is Azure-toepassing?](../azure-monitor/app/app-insights-overview.md)voor meer informatie over Application Insights insights.

## <a name="web-service-metadata-and-response-data"></a>Metagegevens en antwoordgegevens van webservice

> [!IMPORTANT]
> Azure-toepassing Insights registreert alleen nettoladingen van maximaal 64 kB. Als deze limiet is bereikt, ziet u mogelijk fouten zoals geheugenverlies of wordt er geen informatie geregistreerd.

Als u informatie over de webserviceaanvraag wilt logboeken, voegt u `print` instructies toe aan score.py bestand. Elke `print` instructie resulteert in één vermelding in de Application Insights traceertabel onder het bericht `STDOUT` . Application Insights slaat de `print` uitvoer van de instructie op in  `customDimensions` en in de `Contents` traceertabel. Het afdrukken van JSON-tekenreeksen produceert een hiërarchische gegevensstructuur in de traceeruitvoer onder `Contents` .

## <a name="export-data-for-retention-and-processing"></a>Gegevens exporteren voor retentie en verwerking

>[!Important]
> Azure-toepassing Insights ondersteunt alleen exports naar blob-opslag. Zie [Telemetrie](../azure-monitor/app/export-telemetry.md#continuous-export-advanced-storage-configuration)exporteren vanuit App Insights voor meer informatie over de limieten van deze implementatie.

Gebruik Application Insights export [om gegevens](../azure-monitor/app/export-telemetry.md) te exporteren naar een blob-opslagaccount waar u retentie-instellingen kunt definiëren. Application Insights exporteert de gegevens in JSON-indeling. 

:::image type="content" source="media/how-to-enable-app-insights/continuous-export-setup.png" alt-text="Continue export":::

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u logboekregistratie kunt inschakelen en logboeken voor webservice-eindpunten kunt weergeven. Probeer deze artikelen voor de volgende stappen:


* [Een model implementeren in een AKS-cluster](./how-to-deploy-azure-kubernetes-service.md)

* [Een model implementeren in Azure Container Instances](./how-to-deploy-azure-container-instance.md)

* [MLOps: modellen beheren, implementeren](./concept-model-management-and-deployment.md) en bewaken met Azure Machine Learning meer informatie over het gebruik van gegevens die zijn verzameld van modellen in productie. Met dergelijke gegevens kunt u uw proces van machine learning verbeteren.