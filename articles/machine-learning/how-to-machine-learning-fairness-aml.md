---
title: Beoordeling van ML-modellen verdeling in python (preview-versie)
titleSuffix: Azure Machine Learning
description: Meer informatie over het beoordelen en beperken van de verdeling van uw machine learning-modellen met behulp van Fairlearn en de Azure Machine Learning python SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mesameki
author: mesameki
ms.reviewer: luquinta
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 3fbd4990fd330960bb8dbce2e2a8d1bcb578cf2a
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701181"
---
# <a name="use-azure-machine-learning-with-the-fairlearn-open-source-package-to-assess-the-fairness-of-ml-models-preview"></a>Gebruik Azure Machine Learning met het open-source pakket Fairlearn om de verdeling van ML-modellen te beoordelen (preview-versie)

In deze hand leiding vindt u informatie over het gebruik van het [Fairlearn](https://fairlearn.github.io/) open-source python-pakket met Azure machine learning om de volgende taken uit te voeren:

* Evalueer de verdeling van de voor spellingen van uw model. Zie de [verdeling in machine learning artikel](concept-fairness-ml.md)voor meer informatie over verdeling in machine learning.
* Upload, vermeld en down load verdeling Assessment Insights naar/van Azure Machine Learning Studio.
* Bekijk een verdeling-evaluatie dashboard in Azure Machine Learning Studio om te communiceren met uw model (s) ' verdeling Insights.

>[!NOTE]
> Verdeling-evaluatie is geen louter technische oefening. **Dit pakket kan u helpen bij het beoordelen van de verdeling van een machine learning model, maar u kunt alleen besluiten configureren en beslissingen nemen over de manier waarop het model werkt.**  Hoewel dit pakket helpt kwantitatieve metrische gegevens te identificeren om verdeling te beoordelen, moeten ontwikkel aars van machine learning modellen ook een kwalitatieve analyse uitvoeren om de verdeling van hun eigen modellen te evalueren.

## <a name="azure-machine-learning-fairness-sdk"></a>Azure Machine Learning verdeling SDK 

De Azure Machine Learning verdeling SDK, `azureml-contrib-fairness` , integreert het open source python-pakket [Fairlearn](http://fairlearn.github.io), binnen Azure machine learning. Bekijk deze [voorbeeld notitieblokken](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)voor meer informatie over de integratie van Fairlearn in azure machine learning. Voor meer informatie over Fairlearn raadpleegt u de voorbeeld [handleiding](https://fairlearn.github.io/master/auto_examples/) en de [voorbeeld notitieblokken](https://github.com/fairlearn/fairlearn/tree/master/notebooks). 

Gebruik de volgende opdrachten om de- `azureml-contrib-fairness` en-pakketten te installeren `fairlearn` :
```bash
pip install azureml-contrib-fairness
pip install fairlearn==0.4.6
```



## <a name="upload-fairness-insights-for-a-single-model"></a>Verdeling Insights voor één model uploaden

In het volgende voor beeld ziet u hoe u het verdeling-pakket gebruikt om model verdeling Insights te uploaden naar Azure Machine Learning en het dash board voor de verdeling-evaluatie in Azure Machine Learning Studio te bekijken.

1. Train een voorbeeld model in een Jupyter-notebook. 

    Voor de gegevensset wordt gebruikgemaakt van de bekende verzamelings gegevens voor volwassenen die worden geladen met `shap` (voor het gemak). Voor de doel einden van dit voor beeld behandelen we deze gegevensset als een probleem met een lenings beslissing en Pretend dat het label aangeeft of elke persoon een lening in het verleden opnieuw heeft betaald. We gebruiken de gegevens om een voor spelling te trainen om te voors pellen of voorheen ongeziene personen een lening zullen terugbetalen of niet. De veronderstelling is dat de model voorspellingen worden gebruikt om te bepalen of een individu een lening moet worden aangeboden.

    ```python
    from sklearn.model_selection import train_test_split
    from fairlearn.widget import FairlearnDashboard
    from sklearn.linear_model import LogisticRegression
    from sklearn.preprocessing import LabelEncoder, StandardScaler
    import pandas as pd
    import shap

    # Load the census dataset
    X_raw, Y = shap.datasets.adult()
    X_raw["Race"].value_counts().to_dict()
    

    # (Optional) Separate the "sex" and "race" sensitive features out and drop them from the main data prior to training your model
    A = X_raw[['Sex','Race']]
    X = X_raw.drop(labels=['Sex', 'Race'],axis = 1)
    X = pd.get_dummies(X)
    
    sc = StandardScaler()
    X_scaled = sc.fit_transform(X)
    X_scaled = pd.DataFrame(X_scaled, columns=X.columns)

    # Perform some standard data preprocessing steps to convert the data into a format suitable for the ML algorithms
    le = LabelEncoder()
    Y = le.fit_transform(Y)

    # Split data into train and test
    from sklearn.model_selection import train_test_split
    from sklearn.model_selection import train_test_split
    X_train, X_test, Y_train, Y_test, A_train, A_test = train_test_split(X_scaled, 
                                                        Y, 
                                                        A,
                                                        test_size = 0.2,
                                                        random_state=0,
                                                        stratify=Y)

    # Work around indexing issue
    X_train = X_train.reset_index(drop=True)
    A_train = A_train.reset_index(drop=True)
    X_test = X_test.reset_index(drop=True)
    A_test = A_test.reset_index(drop=True)

    # Improve labels
    A_test.Sex.loc[(A_test['Sex'] == 0)] = 'female'
    A_test.Sex.loc[(A_test['Sex'] == 1)] = 'male'


    A_test.Race.loc[(A_test['Race'] == 0)] = 'Amer-Indian-Eskimo'
    A_test.Race.loc[(A_test['Race'] == 1)] = 'Asian-Pac-Islander'
    A_test.Race.loc[(A_test['Race'] == 2)] = 'Black'
    A_test.Race.loc[(A_test['Race'] == 3)] = 'Other'
    A_test.Race.loc[(A_test['Race'] == 4)] = 'White'


    # Train a classification model
    lr_predictor = LogisticRegression(solver='liblinear', fit_intercept=True)
    lr_predictor.fit(X_train, Y_train)

    # (Optional) View this model in Fairlearn's fairness dashboard, and see the disparities which appear:
    from fairlearn.widget import FairlearnDashboard
    FairlearnDashboard(sensitive_features=A_test, 
                       sensitive_feature_names=['Sex', 'Race'],
                       y_true=Y_test,
                       y_pred={"lr_model": lr_predictor.predict(X_test)})
    ```

2. Meld u aan bij Azure Machine Learning en registreer uw model.
   
    Het verdeling-dash board kan worden geïntegreerd met geregistreerde of niet-geregistreerde modellen. Registreer uw model in Azure Machine Learning met de volgende stappen:
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

3. Metrische gegevens van precompute verdeling.

    Maak een dash board-woorden lijst met behulp van het `metrics` pakket Fairlearn. De- `_create_group_metric_set` methode heeft argumenten die vergelijkbaar zijn met de dash board-constructor, behalve dat de gevoelige functies worden door gegeven als een woorden lijst (om ervoor te zorgen dat er namen beschikbaar zijn). U moet ook het type voor spelling (binaire classificatie in dit geval) opgeven bij het aanroepen van deze methode.

    ```python
    #  Create a dictionary of model(s) you want to assess for fairness 
    sf = { 'Race': A_test.Race, 'Sex': A_test.Sex}
    ys_pred = { lr_reg_id:lr_predictor.predict(X_test) }
    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Upload de vooraf berekende metrische verdeling-gegevens.
    
    Importeer nu `azureml.contrib.fairness` pakket om de upload uit te voeren:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Maak een experiment, vervolgens een uitvoering en upload het dash board ernaar:
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
5. Het verdeling-dash board controleren vanuit Azure Machine Learning Studio

    Als u de vorige stappen hebt voltooid (upload gegenereerde verdeling Insights naar Azure Machine Learning), kunt u het verdeling-dash board weer geven in [Azure machine learning Studio](https://ml.azure.com). Dit dash board is hetzelfde visualisatie dashboard dat is opgenomen in Fairlearn, zodat u de verschillen kunt analyseren tussen de subgroepen van uw gevoelige functie (bijvoorbeeld mannelijk versus vrouwelijk).
    Volg een van deze paden om toegang te krijgen tot het visualisatie dashboard in Azure Machine Learning studio:

    * **Het deel venster experimenten (preview)**
    1. Selecteer **experimenten** in het linkerdeel venster om een lijst met experimenten te bekijken die u op Azure machine learning hebt uitgevoerd.
    1. Selecteer een bepaald experiment om alle uitvoeringen in dat experiment weer te geven.
    1. Selecteer een run en vervolgens het tabblad **verdeling** naar het visualisatie Dashboard van de toelichting.


    [![Verdeling-dash board](./media/how-to-machine-learning-fairness-aml/dashboard.png)](./media/how-to-machine-learning-fairness-aml/dashboard.png#lightbox)
    
    * **Deel venster modellen**
    1. Als u het oorspronkelijke model hebt geregistreerd door de vorige stappen te volgen, kunt u **modellen** selecteren in het linkerdeel venster om de app weer te geven.
    1. Selecteer een model en klik vervolgens op het tabblad **verdeling** om het visualisatie Dashboard van de uitleg weer te geven.

    Raadpleeg de [Gebruikers handleiding](https://fairlearn.github.io/master/user_guide/assessment.html#fairlearn-dashboard)van Fairlearn voor meer informatie over het visualisatie dashboard en wat het bevat.

## <a name="upload-fairness-insights-for-multiple-models"></a>Verdeling Insights voor meerdere modellen uploaden

Als u geïnteresseerd bent in het vergelijken van meerdere modellen en u wilt zien hoe de verdeling-evaluaties verschillen, kunt u meer dan één model door geven aan het visualisatie dashboard en de prestaties van verdeling traden door lopen.

1. Uw modellen trainen:
    
    Naast het vorige logistiek regressie model maken we nu een tweede classificatie op basis van een ondersteunings vector machine Estimator en uploaden we een verdeling-dash board-woorden lijst met behulp van het pakket van Fairlearn `metrics` . Hier worden de stappen voor het laden en voorverwerken van gegevens overs Laan en direct naar de model trainings fase gereageerd.


    ```python
    # Train your first classification model
    from sklearn.linear_model import LogisticRegression
    lr_predictor = LogisticRegression(solver='liblinear', fit_intercept=True)
    lr_predictor.fit(X_train, Y_train)

    # Train your second classification model
    from sklearn import svm
    svm_predictor = svm.SVC()
    svm_predictor.fit(X_train, Y_train)
    ```

2. Uw modellen registreren

    Registreer beide modellen in Azure Machine Learning. Voor het gemak van de volgende methode worden de resultaten opgeslagen in een woorden lijst, waarmee het `id` geregistreerde model (een teken reeks in `name:version` indeling) wordt toegewezen aan de voor spelling zelf:

    ```python
    model_dict = {}

    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    model_dict[lr_reg_id] = lr_predictor

    svm_reg_id = register_model("fairness_svm", svm_predictor)
    model_dict[svm_reg_id] = svm_predictor
    ```

3. Het Fairlearn-dash board lokaal laden

    Voordat u de verdeling Insights uploadt in Azure Machine Learning, kunt u deze voor spellingen bekijken in een lokaal opgeroepen Fairlearn-dash board. 



    ```python
    #  Generate models' predictions and load the fairness dashboard locally 
    ys_pred = {}
    for n, p in model_dict.items():
        ys_pred[n] = p.predict(X_test)

    from fairlearn.widget import FairlearnDashboard

    FairlearnDashboard(sensitive_features=A_test, 
                    sensitive_feature_names=['Sex', 'Race'],
                    y_true=Y_test.tolist(),
                    y_pred=ys_pred)
    ```

3. Metrische gegevens van precompute verdeling.

    Maak een dash board-woorden lijst met behulp van het `metrics` pakket Fairlearn.

    ```python
    sf = { 'Race': A_test.Race, 'Sex': A_test.Sex }

    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Upload de vooraf berekende metrische verdeling-gegevens.
    
    Importeer nu `azureml.contrib.fairness` pakket om de upload uit te voeren:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Maak een experiment, vervolgens een uitvoering en upload het dash board ernaar:
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


    Net als bij de vorige sectie kunt u een van de hierboven beschreven paden volgen (via **experimenten** of **modellen**) in azure machine learning Studio om toegang te krijgen tot het visualisatie dashboard en de twee modellen te vergelijken met de voor waarden van verdeling en prestaties.


## <a name="upload-unmitigated-and-mitigated-fairness-insights"></a>Niet-verholpen en verkleinde verdeling-inzichten uploaden

U kunt de [beperkende algoritmen](https://fairlearn.github.io/master/user_guide/mitigation.html)van Fairlearn gebruiken, de gegenereerde gereduceerde model (s) vergelijken met het oorspronkelijke ongebruikte model en door de Verwissel bare prestaties/verdeling te navigeren tussen de vergeleken modellen.

Bekijk dit [voorbeeld notitieblok](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/fairness/fairlearn-azureml-mitigation.ipynb)om een voor beeld te zien van het gebruik van het risico voor het beperken van de [raster zoek actie](https://fairlearn.github.io/master/user_guide/mitigation.html#grid-search) (waarmee een verzameling verlaagde modellen met verschillende verdeling en prestaties wordt gemaakt). 

Bij het uploaden van meerdere modellen verdeling Insights in één keer worden modellen met betrekking tot verdeling en prestaties toegestaan. U kunt verder klikken op een van de modellen die worden weer gegeven in de model vergelijkings grafiek om de gedetailleerde verdeling inzichten van het desbetreffende model te bekijken.


[![Fairlearn-dash board voor model vergelijking](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png)](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png#lightbox)
    

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over model verdeling](concept-fairness-ml.md)

[Azure Machine Learning voorbeeld notitieblokken voor verdeling bekijken](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)
