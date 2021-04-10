---
title: Gegevens verzamelen in uw productie modellen
titleSuffix: Azure Machine Learning
description: Meer informatie over het verzamelen van gegevens uit een Azure Machine Learning model dat is geïmplementeerd op een AKS-cluster (Azure Kubernetes service).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 07/14/2020
ms.topic: conceptual
ms.custom: how-to, data4ml
ms.openlocfilehash: f42a2f9e606eaa0475f2e35c479e99545ecf8193
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102521897"
---
# <a name="collect-data-from-models-in-production"></a>Gegevens verzamelen van modellen in productie

In dit artikel wordt uitgelegd hoe u gegevens kunt verzamelen van een Azure Machine Learning model dat is geïmplementeerd op een AKS-cluster (Azure Kubernetes service). De verzamelde gegevens worden vervolgens opgeslagen in Azure Blob-opslag.

Zodra de verzameling is ingeschakeld, kunt u met de gegevens die u verzamelt, het volgende doen:

* [Bewaak gegevens drift](how-to-monitor-datasets.md) over de productie gegevens die u verzamelt.

* Analyseer verzamelde gegevens met behulp van [Power bi](#powerbi) of [Azure Databricks](#databricks)

* Neem betere beslissingen over wanneer u uw model opnieuw traint of optimaliseert.

* Train uw model opnieuw met de verzamelde gegevens.

## <a name="what-is-collected-and-where-it-goes"></a>Wat wordt verzameld en waar het gaat

De volgende gegevens kunnen worden verzameld:

* Model invoer gegevens van webservices die zijn geïmplementeerd in een AKS-cluster. Voice audio, afbeeldingen en video worden *niet* verzameld.
  
* Model voorspellingen met behulp van invoer gegevens voor productie.

>[!NOTE]
> Preaggregatie en voor berekeningen op deze gegevens maken momenteel geen deel uit van de verzamelings service.

De uitvoer wordt opgeslagen in Blob Storage. Omdat de gegevens worden toegevoegd aan de Blob-opslag, kunt u uw favoriete hulp programma kiezen om de analyse uit te voeren.

Het pad naar de uitvoer gegevens in de BLOB volgt de volgende syntaxis:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> In versies van de Azure Machine Learning SDK voor python ouder dan versie 0.1.0 A16 wordt het `designation` argument genoemd `identifier` . Als u uw code hebt ontwikkeld met een eerdere versie, moet u deze dienovereenkomstig bijwerken.

## <a name="prerequisites"></a>Vereisten

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://aka.ms/AMLFree) aan voordat u begint.

- Er moet een Azure Machine Learning-werk ruimte, een lokale map met uw scripts en de Azure Machine Learning SDK voor python zijn geïnstalleerd. Zie [How to configure a Development Environment (een ontwikkel omgeving configureren](how-to-configure-environment.md)) voor meer informatie over het installeren van deze.

- U hebt een getraind machine learning-model nodig om te worden geïmplementeerd op AKS. Als u geen model hebt, raadpleegt u de zelf studie over het [classificatie Model Train image](tutorial-train-models-with-aml.md) .

- U hebt een AKS-cluster nodig. Voor informatie over hoe u een maakt en implementeert, raadpleegt [u hoe u implementeert en waar](how-to-deploy-and-where.md).

- [Stel uw omgeving](how-to-configure-environment.md) in en installeer de [Azure machine learning monitoring-SDK](/python/api/overview/azure/ml/install).

## <a name="enable-data-collection"></a>Gegevensverzameling inschakelen

U kunt [gegevens verzameling](/python/api/azureml-monitoring/azureml.monitoring.modeldatacollector.modeldatacollector) inschakelen, ongeacht het model dat u via Azure machine learning of andere hulpprogram ma's implementeert.

Als u gegevens verzameling wilt inschakelen, moet u het volgende doen:

1. Open het Score bestand.

1. Voeg de volgende code toe boven aan het bestand:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. Declareer de variabelen voor het verzamelen van gegevens in uw `init` functie:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3", "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* is een optionele para meter. U hoeft deze niet te gebruiken als uw model dit niet nodig heeft. Het gebruik van *CorrelationId* helpt u gemakkelijker te koppelen met andere gegevens, zoals *LoanNumber* of *CustomerId*.
    
    De *id* -para meter wordt later gebruikt voor het maken van de mapstructuur in uw blob. U kunt deze gebruiken om onbewerkte gegevens te onderscheiden van verwerkte gegevens.

1. Voeg de volgende regels code toe aan de `run(input_df)` functie:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. Het verzamelen van gegevens wordt *niet* automatisch ingesteld op **True** wanneer u een service in AKS implementeert. Werk uw configuratie bestand bij, zoals in het volgende voor beeld:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    U kunt ook Application Insights voor service bewaking inschakelen door deze configuratie te wijzigen:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. Zie [implementeren en waar](how-to-deploy-and-where.md)als u een nieuwe installatie kopie wilt maken en het machine learning model wilt implementeren.

1. Voeg het PIP-pakket ' Azure-monitoring ' toe aan de Conda-afhankelijkheden van de web-service omgeving:
  ```Python
    env = Environment('webserviceenv')
    env.python.conda_dependencies = CondaDependencies.create(conda_packages=['numpy'],pip_packages=['azureml-defaults','azureml-monitoring','inference-schema[numpy-support]'])
  ```


## <a name="disable-data-collection"></a>Gegevens verzamelen uitschakelen

U kunt het verzamelen van gegevens op elk gewenst moment stoppen. Gebruik python-code om het verzamelen van gegevens uit te scha kelen.

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>Valideer en analyseer uw gegevens

U kunt een hulp programma kiezen van uw voor keur voor het analyseren van de gegevens die in uw Blob-opslag zijn verzameld.

### <a name="quickly-access-your-blob-data"></a>Snel toegang krijgen tot uw BLOB-gegevens

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Open uw werk ruimte.

1. Selecteer **Opslag**.

    [![Selecteer de optie voor opslag](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Volg het pad naar de uitvoer gegevens van de blob met de volgende syntaxis:

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a><a id="powerbi"></a> Model gegevens analyseren met behulp van Power BI

1. Down load en open [Power bi Desktop](https://www.powerbi.com).

1. Selecteer **gegevens ophalen** en selecteer [**Azure Blob Storage**](/power-bi/desktop-data-sources).

    [![Setup van Power BI BLOB](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. Voeg de naam van uw opslag account toe en voer uw opslag sleutel in. U kunt deze informatie vinden door **instellingen**  >  **toegangs sleutels** in uw BLOB te selecteren.

1. Selecteer de container **model gegevens** en selecteer **bewerken**.

    [![Power BI Navigator](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. Klik in de query-editor op onder de kolom **naam** en voeg uw opslag account toe.

1. Voer het pad naar het model in het filter in. Als u alleen wilt zoeken naar bestanden van een bepaald jaar of per maand, vouwt u het pad naar het filter uit. Als u bijvoorbeeld alleen in maart gegevens wilt bekijken, gebruikt u dit pad naar het filter:

   /modeldata/ \<subscriptionid> / \<resourcegroupname> / \<workspacename> / \<webservicename> / \<modelname> / \<modelversion> / \<designation> / \<year> /3

1. Filter de gegevens die relevant zijn voor u op basis van **naam** waarden. Als u voor spellingen en invoer hebt opgeslagen, moet u een query maken voor elke.

1. Selecteer de dubbele pijl-omlaag **naast de kolomkop om de bestanden** te combi neren.

    [![Power BI inhoud](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Selecteer **OK**. De gegevens worden geladen.

    [![Power BI bestanden combi neren](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Selecteer **sluiten en Toep assen**.

1. Als u invoer en voor spellingen hebt toegevoegd, worden uw tabellen automatisch gerangschikt op basis van **aanvraag** -waarden.

1. Begin met het bouwen van uw aangepaste rapporten in uw model gegevens.

### <a name="analyze-model-data-using-azure-databricks"></a><a id="databricks"></a> Model gegevens analyseren met behulp van Azure Databricks

1. Maak een [Azure Databricks-werk ruimte](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal).

1. Ga naar uw Databricks-werk ruimte.

1. Selecteer **gegevens uploaden** in uw Databricks-werk ruimte.

    [![De optie Databricks-gegevens uploaden selecteren](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Selecteer **nieuwe tabel maken** en selecteer **andere gegevens bronnen**  >  **Azure Blob Storage**  >  **tabel maken in notitie blok**.

    [![Databricks-tabel maken](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Werk de locatie van uw gegevens bij. Hier volgt een voorbeeld:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [![Databricks-installatie](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Volg de stappen in de sjabloon om uw gegevens weer te geven en te analyseren.

## <a name="next-steps"></a>Volgende stappen

[Gegevens drift detecteren](how-to-monitor-datasets.md) op gegevens die u hebt verzameld.