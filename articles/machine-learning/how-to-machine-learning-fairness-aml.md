---
title: De redelijkheid van ML-modellen beoordelen in Python (preview)
titleSuffix: Azure Machine Learning
description: Meer informatie over het beoordelen en beperken van de redelijkheid van uw machine learning modellen met behulp van Fairlearn en Azure Machine Learning Python SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mesameki
author: mesameki
ms.reviewer: luquinta
ms.date: 11/16/2020
ms.topic: how-to
ms.custom: devx-track-python, responsible-ml
ms.openlocfilehash: 3b71347f9375ebb24befe665c031af9cd7ad7cc3
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107884836"
---
# <a name="use-azure-machine-learning-with-the-fairlearn-open-source-package-to-assess-the-fairness-of-ml-models-preview"></a>Gebruik Azure Machine Learning het opensource-pakket Fairlearn om de redelijkheid van ML-modellen (preview) te beoordelen

In deze handleiding leert u hoe u het open-source [Python-pakket Fairlearn](https://fairlearn.github.io/) kunt gebruiken met Azure Machine Learning volgende taken uit te voeren:

* Evalueer de redelijkheid van uw modelvoorspellingen. Zie voor meer informatie over redelijkheid in machine learning artikel machine learning [redelijkheid.](concept-fairness-ml.md)
* Upload, vermeld en download inzichten in de beoordeling van redelijkheid naar/van Azure Machine Learning-studio.
* Bekijk een dashboard voor een redelijkheidsevaluatie in Azure Machine Learning-studio om te communiceren met de redelijkheidsinzichten van uw model(en).

>[!NOTE]
> Een redelijkheidsevaluatie is geen uitsluitend technische oefening. **Met dit pakket kunt u de redelijkheid van een machine learning model beoordelen, maar alleen u kunt configureren en beslissingen nemen over hoe het model presteert.**  Hoewel dit pakket helpt om kwantitatieve metrische gegevens te identificeren om de redelijkheid te evalueren, moeten ontwikkelaars van machine learning-modellen ook een kwalitatieve analyse uitvoeren om de redelijkheid van hun eigen modellen te evalueren.

## <a name="azure-machine-learning-fairness-sdk"></a>Azure Machine Learning Fairness SDK 

De Azure Machine Learning Fairness SDK, , integreert het `azureml-contrib-fairness` opensource Python-pakket [Fairlearn](http://fairlearn.github.io)in Azure Machine Learning. Bekijk deze voorbeeldnotenotes voor meer informatie over de integratie van Fairlearn in [Azure Machine Learning.](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness) Zie de voorbeeldhandleiding en [](https://fairlearn.org/v0.6.0/auto_examples/) voorbeeldnotenotes voor meer informatie [over](https://github.com/fairlearn/fairlearn/tree/master/notebooks)Fairlearn. 

Gebruik de volgende opdrachten om de pakketten `azureml-contrib-fairness` en `fairlearn` te installeren:
```bash
pip install azureml-contrib-fairness
pip install fairlearn==0.4.6
```
Latere versies van Fairlearn moeten ook werken in de volgende voorbeeldcode.



## <a name="upload-fairness-insights-for-a-single-model"></a>Redelijkheidsinzichten uploaden voor één model

In het volgende voorbeeld ziet u hoe u het pakket voor redelijkheid gebruikt. We uploaden modelinzichten voor redelijkheid naar Azure Machine Learning en zien het dashboard voor de beoordeling van de redelijkheid in Azure Machine Learning-studio.

1. Een voorbeeldmodel trainen in Jupyter Notebook. 

    Voor de gegevensset gebruiken we de bekende volkstellingsgegevensset voor volwassenen, die we ophalen van OpenML. We doen alsof we een probleem hebben met de beslissing over een lening met het label dat aangeeft of een persoon een eerdere lening heeft afgelost. We trainen een model om te voorspellen of eerder niet-geziene personen een lening zullen afsluiten. Een dergelijk model kan worden gebruikt bij het nemen van beslissingen over leningen.

    ```python
    import copy
    import numpy as np
    import pandas as pd

    from sklearn.compose import ColumnTransformer
    from sklearn.datasets import fetch_openml
    from sklearn.impute import SimpleImputer
    from sklearn.linear_model import LogisticRegression
    from sklearn.model_selection import train_test_split
    from sklearn.preprocessing import StandardScaler, OneHotEncoder
    from sklearn.compose import make_column_selector as selector
    from sklearn.pipeline import Pipeline
    
    from fairlearn.widget import FairlearnDashboard

    # Load the census dataset
    data = fetch_openml(data_id=1590, as_frame=True)
    X_raw = data.data
    y = (data.target == ">50K") * 1
    
    # (Optional) Separate the "sex" and "race" sensitive features out and drop them from the main data prior to training your model
    X_raw = data.data
    y = (data.target == ">50K") * 1
    A = X_raw[["race", "sex"]]
    X = X_raw.drop(labels=['sex', 'race'],axis = 1)
    
    # Split the data in "train" and "test" sets
    (X_train, X_test, y_train, y_test, A_train, A_test) = train_test_split(
        X_raw, y, A, test_size=0.3, random_state=12345, stratify=y
    )

    # Ensure indices are aligned between X, y and A,
    # after all the slicing and splitting of DataFrames
    # and Series
    X_train = X_train.reset_index(drop=True)
    X_test = X_test.reset_index(drop=True)
    y_train = y_train.reset_index(drop=True)
    y_test = y_test.reset_index(drop=True)
    A_train = A_train.reset_index(drop=True)
    A_test = A_test.reset_index(drop=True)

    # Define a processing pipeline. This happens after the split to avoid data leakage
    numeric_transformer = Pipeline(
        steps=[
            ("impute", SimpleImputer()),
            ("scaler", StandardScaler()),
        ]
    )
    categorical_transformer = Pipeline(
        [
            ("impute", SimpleImputer(strategy="most_frequent")),
            ("ohe", OneHotEncoder(handle_unknown="ignore")),
        ]
    )
    preprocessor = ColumnTransformer(
        transformers=[
            ("num", numeric_transformer, selector(dtype_exclude="category")),
            ("cat", categorical_transformer, selector(dtype_include="category")),
        ]
    )

    # Put an estimator onto the end of the pipeline
    lr_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                LogisticRegression(solver="liblinear", fit_intercept=True),
            ),
        ]
    )

    # Train the model on the test data
    lr_predictor.fit(X_train, y_train)

    # (Optional) View this model in Fairlearn's fairness dashboard, and see the disparities which appear:
    from fairlearn.widget import FairlearnDashboard
    FairlearnDashboard(sensitive_features=A_test, 
                       sensitive_feature_names=['Race', 'Sex'],
                       y_true=y_test,
                       y_pred={"lr_model": lr_predictor.predict(X_test)})
    ```

2. Meld u aan Azure Machine Learning en registreer uw model.
   
    Het redelijkheidsdashboard kan worden geïntegreerd met geregistreerde of niet-geregistreerde modellen. Registreer uw model in Azure Machine Learning met de volgende stappen:
    ```python
    from azureml.core import Workspace, Experiment, Model
    import joblib
    import os
    
    ws = Workspace.from_config()
    ws.get_details()

    os.makedirs('models', exist_ok=True)
    
    # Function to register models into Azure Machine Learning
    def register_model(name, model):
        print("Registering ", name)
        model_path = "models/{0}.pkl".format(name)
        joblib.dump(value=model, filename=model_path)
        registered_model = Model.register(model_path=model_path,
                                        model_name=name,
                                        workspace=ws)
        print("Registered ", registered_model.id)
        return registered_model.id

    # Call the register_model function 
    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    ```

3. Metrische gegevens voor redelijkheid vooraf berekenen.

    Maak een dashboard-woordenlijst met behulp van het pakket van `metrics` Fairlearn. De methode heeft argumenten die vergelijkbaar zijn met de Dashboard-constructor, behalve dat de gevoelige functies worden doorgegeven als een woordenlijst (om ervoor te zorgen `_create_group_metric_set` dat namen beschikbaar zijn). We moeten ook het type voorspelling opgeven (binaire classificatie in dit geval) bij het aanroepen van deze methode.

    ```python
    #  Create a dictionary of model(s) you want to assess for fairness 
    sf = { 'Race': A_test.race, 'Sex': A_test.sex}
    ys_pred = { lr_reg_id:lr_predictor.predict(X_test) }
    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Upload de vooraf becomputte metrische gegevens voor redelijkheid.
    
    Importeer nu `azureml.contrib.fairness` het pakket om het uploaden uit te voeren:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Maak een experiment, vervolgens een Uitvoeren en upload het dashboard naar het experiment:
    ```python
    exp = Experiment(ws, "Test_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness insights of Logistic Regression Classifier"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```
5. Controleer het redelijkheidsdashboard Azure Machine Learning-studio

    Als u de vorige stappen hebt voltooid (door gegenereerde redelijkheidsinzichten te uploaden naar Azure Machine Learning), kunt u het redelijkheidsdashboard bekijken in [Azure Machine Learning-studio](https://ml.azure.com). Dit dashboard is hetzelfde visualisatiedashboard als in Fairlearn, zodat u de verschillen tussen de subgroepen van uw gevoelige functie (bijvoorbeeld mannen versus vrouwen) kunt analyseren.
    Volg een van deze paden om toegang te krijgen tot het visualisatiedashboard in Azure Machine Learning-studio:

    * **Deelvenster Experimenten (preview)**
    1. Selecteer **Experimenten** in het linkerdeelvenster voor een lijst met experimenten die u hebt uitgevoerd op Azure Machine Learning.
    1. Selecteer een bepaald experiment om alle runs in dat experiment te bekijken.
    1. Selecteer een run en vervolgens het tabblad **Redelijkheid** naar het visualisatiedashboard van de uitleg.
    1. Zodra u op het **tabblad Redelijkheid** komt, klikt u op een **redelijkheids-id** in het menu aan de rechterkant.
    1. Configureer uw dashboard door uw gevoelige kenmerk, metrische prestatie- en redelijkheidsmetrische gegevens te selecteren om op de pagina voor de redelijkheidsevaluatie te komen.
    1. Schakel het grafiektype van het  ene naar het andere om de toewijzings- en kwaliteit van **service-schade te** bekijken.



    [![Verdelingsdashboardtoewijzing](./media/how-to-machine-learning-fairness-aml/dashboard-1.png)](./media/how-to-machine-learning-fairness-aml/dashboard-1.png#lightbox)
    
    [![Fairness Dashboard Quality of Service](./media/how-to-machine-learning-fairness-aml/dashboard-2.png)](./media/how-to-machine-learning-fairness-aml/dashboard-2.png#lightbox)
    * **Deelvenster Modellen**
    1. Als u het oorspronkelijke model hebt geregistreerd door de vorige stappen te volgen, kunt u **Modellen** selecteren in het linkerdeelvenster om het weer te geven.
    1. Selecteer een model en vervolgens het tabblad **Redelijkheid** om het visualisatiedashboard met uitleg weer te geven.

    Raadpleeg de gebruikershandleiding van Fairlearn voor meer informatie over het [visualisatiedashboard](https://fairlearn.org/v0.6.0/user_guide/assessment.html#fairlearn-dashboard)en wat het bevat.

## <a name="upload-fairness-insights-for-multiple-models"></a>Redelijkheidsinzichten uploaden voor meerdere modellen

Als u meerdere modellen wilt vergelijken en wilt zien hoe de redelijkheidsevaluaties verschillen, kunt u meer dan één model doorgeven aan het visualisatiedashboard en hun balans tussen prestaties en redelijkheid vergelijken.

1. Uw modellen trainen:
    
    We maken nu een tweede classificatie, op basis van een Support Vector Machine-estimator, en uploaden een woordenlijst voor het redelijkheidsdashboard met behulp van het pakket van `metrics` Fairlearn. We gaan ervan uit dat het eerder getrainde model nog steeds beschikbaar is.


    ```python
    # Put an SVM predictor onto the preprocessing pipeline
    from sklearn import svm
    svm_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                svm.SVC(),
            ),
        ]
    )

    # Train your second classification model
    svm_predictor.fit(X_train, y_train)
    ```

2. Uw modellen registreren

    Registreer vervolgens beide modellen binnen Azure Machine Learning. Voor het gemak kunt u de resultaten opslaan in een woordenlijst, die de van het geregistreerde model (een tekenreeks in indeling) toekent `id` `name:version` aan de voorspeller zelf:

    ```python
    model_dict = {}

    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    model_dict[lr_reg_id] = lr_predictor

    svm_reg_id = register_model("fairness_svm", svm_predictor)
    model_dict[svm_reg_id] = svm_predictor
    ```

3. Het Fairlearn-dashboard lokaal laden

    Voordat u de redelijkheidsinzichten uploadt naar Azure Machine Learning, kunt u deze voorspellingen onderzoeken in een lokaal aangeroepen Fairlearn-dashboard. 



    ```python
    #  Generate models' predictions and load the fairness dashboard locally 
    ys_pred = {}
    for n, p in model_dict.items():
        ys_pred[n] = p.predict(X_test)

    from fairlearn.widget import FairlearnDashboard

    FairlearnDashboard(sensitive_features=A_test, 
                    sensitive_feature_names=['Race', 'Sex'],
                    y_true=y_test.tolist(),
                    y_pred=ys_pred)
    ```

3. Metrische gegevens voor redelijkheid vooraf berekenen.

    Maak een dashboard-woordenlijst met behulp van het pakket van `metrics` Fairlearn.

    ```python
    sf = { 'Race': A_test.race, 'Sex': A_test.sex }

    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Upload de vooraf becomputte metrische gegevens voor redelijkheid.
    
    Importeer nu `azureml.contrib.fairness` het pakket om het uploaden uit te voeren:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Maak een experiment, vervolgens een Uitvoeren en upload het dashboard naar het:
    ```python
    exp = Experiment(ws, "Compare_Two_Models_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness Assessment of Logistic Regression and SVM Classifiers"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```


    Net als in de vorige sectie kunt u een van de hierboven beschreven paden (via **experimenten** of modellen **)** in Azure Machine Learning-studio volgen om toegang te krijgen tot het visualisatiedashboard en de twee modellen te vergelijken op het gebied van redelijkheid en prestaties.


## <a name="upload-unmitigated-and-mitigated-fairness-insights"></a>Niet-gemitigeerde en beperkt inzicht in redelijkheid uploaden

U kunt de risicobeperkingsalgoritmen van Fairlearn gebruiken, de gegenereerde beperkt model(s) vergelijken met het oorspronkelijke niet-gemitigeerde model en navigeren tussen de prestatie-/redelijkheids-afwegingen tussen de vergeleken modellen. [](https://fairlearn.org/v0.6.0/user_guide/mitigation.html)

Bekijk dit voorbeeldnotenoteboek voor een voorbeeld van het gebruik van het risicobeperkingsalgoritme van [Grid Search](https://fairlearn.org/v0.6.0/user_guide/mitigation.html#grid-search) (waarmee een verzameling van verkleinde modellen wordt gemaakt met verschillende afwegingen op het gebied van redelijkheid en [prestaties).](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/fairness/fairlearn-azureml-mitigation.ipynb) 

Door de redelijkheidsinzichten van meerdere modellen in één uitvoering te uploaden, kunt u modellen vergelijken met betrekking tot redelijkheid en prestaties. U kunt op een van de modellen klikken die worden weergegeven in de modelvergelijkingsgrafiek om de gedetailleerde redelijkheidsinzichten van het specifieke model te bekijken.


[![Fairlearn-dashboard voor modelvergelijking](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png)](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png#lightbox)
    

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over model redelijkheid](concept-fairness-ml.md)

[Bekijk de Azure Machine Learning voor redelijkheidsvoorbeelden](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)
