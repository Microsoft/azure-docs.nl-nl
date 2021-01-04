---
title: Gegevens van Machine Learning-webservice-eind punten bewaken en verzamelen
titleSuffix: Azure Machine Learning
description: Meer informatie over het verzamelen van gegevens van modellen die zijn geïmplementeerd op web service-eind punten in azure Kubernetes service (AKS) of Azure Container Instances (ACI).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
ms.author: larryfr
author: blackmist
ms.date: 09/15/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 13b99fe129191b89b5bb2d7f5473e910fa619ce7
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97739838"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Gegevens van ML-webservice-eindpunten bewaken en verzamelen


In dit artikel leert u hoe u gegevens kunt verzamelen van modellen die zijn geïmplementeerd op web service-eind punten in azure Kubernetes service (AKS) of Azure Container Instances (ACI). Gebruik [Azure-toepassing Insights](../azure-monitor/app/app-insights-overview.md) voor het verzamelen van de volgende gegevens uit een eind punt:
* Uitvoergegevens
* Antwoorden
* Aanvraag tarieven, reactie tijden en fout tarieven
* Afhankelijkheids tarieven, reactie tijden en fout percentages
* Uitzonderingen

De [Enable-app-Insights-in-production-service. ipynb-](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) notebook demonstreert de concepten in dit artikel.
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]
 
## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement: Probeer de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).

* Een Azure Machine Learning-werk ruimte, een lokale map die uw scripts bevat en de Azure Machine Learning SDK voor python is geïnstalleerd. Zie [How to configure a Development Environment (een ontwikkel omgeving configureren](how-to-configure-environment.md)) voor meer informatie.

* Een getraind machine learning model. Zie voor meer informatie de zelf studie over het [classificeren van het trainen van installatie kopieën](tutorial-train-models-with-aml.md) .

<a name="python"></a>

## <a name="configure-logging-with-the-python-sdk"></a>Logboek registratie configureren met de python-SDK

In deze sectie leert u hoe u logboek registratie van toepassingen kunt inschakelen met behulp van de python-SDK. 

### <a name="update-a-deployed-service"></a>Een geïmplementeerde service bijwerken

Gebruik de volgende stappen om een bestaande webservice bij te werken:

1. Identificeer de service in uw werk ruimte. De waarde voor `ws` is de naam van uw werk ruimte

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Uw service bijwerken en Azure-toepassing Insights inschakelen

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Aangepaste traceringen vastleggen in uw service

> [!IMPORTANT]
> Met Azure-toepassing Insights worden alleen nettoladingen geregistreerd van Maxi maal 64 kB. Als deze limiet is bereikt, worden er mogelijk fouten weer geven, zoals onvoldoende geheugen of wordt er geen informatie vastgelegd. Als de gegevens die u wilt vastleggen groter zijn dan 64 KB, moet u deze opslaan in Blob Storage met behulp van de gegevens in [gegevens verzamelen voor modellen in de productie omgeving](how-to-enable-data-collection.md).
>
> Voor complexere situaties, zoals het bijhouden van modellen binnen een AKS-implementatie, raden we u aan een bibliotheek van derden te gebruiken zoals [Opentelling](https://opencensus.io).

Als u aangepaste traceringen wilt registreren, volgt u het standaard implementatie proces voor AKS of ACI in het document [implementeren en where](how-to-deploy-and-where.md) . Gebruik vervolgens de volgende stappen:

1. Werk het Score bestand bij door afdruk instructies toe te voegen voor het verzenden van gegevens naar Application Insights tijdens de interferentie. Gebruik een JSON-structuur voor complexere informatie, zoals de gegevens van de aanvraag en het antwoord. 

    In het volgende voor beeld wordt een `score.py` logboek bestand gemaakt wanneer het model is geïnitialiseerd, ingevoerd en uitgevoerd tijdens de afleiding en wanneer er fouten optreden.

    
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

2. Werk de service configuratie bij en zorg ervoor dat Application Insights is ingeschakeld.
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Bouw een installatie kopie en implementeer deze op AKS of ACI. Zie [How to deploy](how-to-deploy-and-where.md)(Engelstalig) (Engelstalig) voor meer informatie.


### <a name="disable-tracking-in-python"></a>Tracering in python uitschakelen

Als u Azure-toepassing Insights wilt uitschakelen, gebruikt u de volgende code:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="configure-logging-with-azure-machine-learning-studio"></a>Logboek registratie configureren met Azure Machine Learning Studio

U kunt ook Azure-toepassing Insights inschakelen vanuit Azure Machine Learning Studio. Wanneer u klaar bent om uw model als een webservice te implementeren, gebruikt u de volgende stappen om Application Insights in te scha kelen:

1. Meld u aan bij de studio op https://ml.azure.com .
1. Ga naar **modellen** en selecteer het model dat u wilt implementeren.
1. Selecteer  **+ implementeren**.
1. Vul het formulier **model implementeren** in.
1. Vouw het menu **Geavanceerd** uit.

    ![Formulier implementeren](./media/how-to-enable-app-insights/deploy-form.png)
1. Selecteer **Application Insights diagnose en gegevens verzameling inschakelen**.

    ![App Insights inschakelen](./media/how-to-enable-app-insights/enable-app-insights.png)

## <a name="view-metrics-and-logs"></a>Metrische gegevens en logboeken weer geven

### <a name="query-logs-for-deployed-models"></a>Query logboeken voor geïmplementeerde modellen

Logboeken van real-time eind punten zijn klant gegevens. U kunt de `get_logs()` functie gebruiken om logboeken op te halen van een eerder geïmplementeerde webservice. De logboeken bevatten mogelijk gedetailleerde informatie over eventuele fouten die zijn opgetreden tijdens de implementatie.

```python
from azureml.core import Workspace
from azureml.core.webservice import Webservice

ws = Workspace.from_config()

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Als u meerdere tenants hebt, moet u mogelijk de volgende verificatie code toevoegen voordat `ws = Workspace.from_config()`

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="the tenant_id in which your workspace resides")
```

### <a name="view-logs-in-the-studio"></a>Logboeken weer geven in de Studio

Azure-toepassing Insights slaat uw service logboeken op in dezelfde resource groep als de Azure Machine Learning-werk ruimte. Gebruik de volgende stappen om uw gegevens weer te geven met behulp van Studio:

1. Ga naar uw Azure Machine Learning-werk ruimte in de [Studio](https://ml.azure.com/).
1. Selecteer **eind punten**.
1. Selecteer de geïmplementeerde service.
1. Selecteer de **URL** -koppeling Application Insights.

    [![Application Insights URL zoeken](./media/how-to-enable-app-insights/appinsightsloc.png)](././media/how-to-enable-app-insights/appinsightsloc.png#lightbox)

1. In Application Insights klikt u op het tabblad **overzicht** of in het gedeelte __bewaking__ op __Logboeken__.

    [![Tabblad Overzicht van bewaking](./media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. Als u informatie wilt weer geven die is vastgelegd in het score.py-bestand, bekijkt u de tabel __traces__ . Met de volgende query zoekt u naar Logboeken waarin de __invoer__ waarde is vastgelegd:

    ```kusto
    traces
    | where customDimensions contains "input"
    | limit 10
    ```

   [![gegevens traceren](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)

Zie [Wat is Application Insights?](../azure-monitor/app/app-insights-overview.md)voor meer informatie over het gebruik van Azure-toepassing Insights.

## <a name="web-service-metadata-and-response-data"></a>Meta gegevens en antwoord van de webservice

> [!IMPORTANT]
> Met Azure-toepassing Insights worden alleen nettoladingen geregistreerd van Maxi maal 64 kB. Als deze limiet wordt bereikt, worden er mogelijk fouten weer geven, zoals onvoldoende geheugen of wordt er geen informatie vastgelegd.

Als u gegevens van de webservice-aanvraag wilt registreren, voegt `print` u instructies toe aan uw score.py-bestand. Elke `print` instructie resulteert in één vermelding in de traceer tabel Application Insights onder het bericht `STDOUT` . Application Insights slaat de `print` uitvoer van de instructie in  `customDimensions` en in de `Contents` tracerings tabel. Het afdrukken van JSON-teken reeksen produceert een hiërarchische gegevens structuur in de tracerings uitvoer onder `Contents` .

## <a name="export-data-for-retention-and-processing"></a>Gegevens exporteren voor retentie en verwerking

>[!Important]
> Azure-toepassing Insights ondersteunt alleen export naar Blob Storage. Zie [telemetrie exporteren vanuit app Insights](../azure-monitor/app/export-telemetry.md#continuous-export-advanced-storage-configuration)voor meer informatie over de limieten van deze implementatie.

Gebruik Application Insights [doorlopend exporteren](../azure-monitor/app/export-telemetry.md) om gegevens te exporteren naar een Blob Storage-account waar u Bewaar instellingen kunt definiëren. Application Insights exporteert de gegevens in JSON-indeling. 

:::image type="content" source="media/how-to-enable-app-insights/continuous-export-setup.png" alt-text="Continue export":::

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u logboek registratie inschakelt en Logboeken kunt weer geven voor webservice-eind punten. Voer deze artikelen uit voor de volgende stappen:


* [Een model implementeren in een AKS-cluster](./how-to-deploy-azure-kubernetes-service.md)

* [Een model implementeren in Azure Container Instances](./how-to-deploy-azure-container-instance.md)

* [MLOps: modellen met Azure machine learning beheren, implementeren en bewaken](./concept-model-management-and-deployment.md) voor meer informatie over het benutten van gegevens die zijn verzameld van modellen in de productie omgeving. Dergelijke gegevens kunnen helpen uw machine learning proces voortdurend te verbeteren.