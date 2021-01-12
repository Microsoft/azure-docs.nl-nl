---
title: Interpreteren & uitleg van ML-modellen in python (preview-versie)
titleSuffix: Azure Machine Learning
description: Meer informatie over het verkrijgen van uitleg over hoe het machine learning-model bepaalt het belang van de functie en voor spellingen wanneer u de Azure Machine Learning SDK gebruikt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 07/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 74ddaaf7a2d279439c0cd27ba0840f02f297877b
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98119557"
---
# <a name="use-the-interpretability-package-to-explain-ml-models--predictions-in-python-preview"></a>Gebruik het vertolkings pakket om ML-modellen & voor spellingen in python uit te leggen (preview)



In deze hand leiding leert u hoe u het vertolkings pakket van de Azure Machine Learning python SDK kunt gebruiken om de volgende taken uit te voeren:


* Leg het volledige model gedrag of afzonderlijke voor spellingen op uw persoonlijke machine lokaal toe.

* Schakel de methoden voor het interpreteren van functies in.

* Leg het gedrag uit voor het hele model en afzonderlijke voor spellingen in Azure.

* Gebruik een visualisatie dashboard om te communiceren met de uitleg van uw model.

* Implementeer een beoordelings uitleg naast uw model om uitleg te geven tijdens het afnemen.



Zie voor meer informatie over de ondersteunde technieken voor het interpreteren en machine learning modellen de [vertolking van modellen in azure machine learning](how-to-machine-learning-interpretability.md) en [voorbeeld notitieblokken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>Urgentie waarde van functie op uw persoonlijke machine genereren 
In het volgende voor beeld ziet u hoe u het vertolkings pakket gebruikt op uw persoonlijke computer zonder dat u contact hoeft op te nemen met Azure-Services.

1. Installeer het `azureml-interpret`-pakket.
    ```bash
    pip install azureml-interpret
    ```

2. Train een voorbeeld model in een lokale Jupyter Notebook.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

3. Roep de uitleg lokaal aan.
   * Als u een uitleg object wilt initialiseren, geeft u uw model en enkele trainings gegevens door aan de constructor van de uitleger.
   * Als u uw uitleg en visualisaties meer informatiever wilt maken, kunt u ervoor kiezen om de namen van onderdelen en uitvoer klassen door te geven als u de classificatie uitvoert.

   In de volgende code blokken ziet u hoe u een object van een uitleg maakt met `TabularExplainer` , `MimicExplainer` en `PFIExplainer` lokaal.
   * `TabularExplainer` roept een van de drie SHAP-uitleg onder ( `TreeExplainer` , `DeepExplainer` , of `KernelExplainer` ) aan.
   * `TabularExplainer` selecteert automatisch het meest geschikte voor uw use-case, maar u kunt elk van de drie onderliggende uitleg rechtstreeks aanroepen.

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    of

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns.
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```

    of

    ```python
    from interpret.ext.blackbox import PFIExplainer

    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model,
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="explain-the-entire-model-behavior-global-explanation"></a>Beschrijf het volledige model gedrag (globale uitleg) 

Raadpleeg het volgende voor beeld om u te helpen bij het ophalen van belang rijke waarden voor de statistische functie (Global).

```python

# you can use the training data or the test data here, but test data would allow you to use Explanation Exploration
global_explanation = explainer.explain_global(x_test)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_train)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="explain-an-individual-prediction-local-explanation"></a>Uitleg over een afzonderlijke voor spelling (lokale uitleg)
Haal de belang rijke waarden van de afzonderlijke functie van verschillende data Points op door uitleg te geven voor een afzonderlijk exemplaar of een groep exemplaren.
> [!NOTE]
> `PFIExplainer` biedt geen ondersteuning voor lokale toelichtingen.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

### <a name="raw-feature-transformations"></a>Trans formaties onbewerkte onderdelen

U kunt ervoor kiezen om uitleg te krijgen over onbewerkte, niet-getransformeerde functies in plaats van functies die zijn ontworpen. Voor deze optie geeft u de pijp lijn voor de functie transformatie door aan de uitleger in `train_explain.py` . Anders biedt de uitleg uitleg over de functies die zijn ontworpen voor de functie.

De indeling van ondersteunde trans formaties is hetzelfde als beschreven in [sklearn-Panda](https://github.com/scikit-learn-contrib/sklearn-pandas). Over het algemeen worden trans formaties ondersteund zolang ze worden toegepast op één kolom, zodat deze duidelijk een-op-veel zijn.

Krijg een uitleg voor onbewerkte functies door gebruik te maken `sklearn.compose.ColumnTransformer` van een of een lijst met de bijpassende Tuples. In het volgende voor beeld wordt gebruikt `sklearn.compose.ColumnTransformer` .

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

Als u het voor beeld wilt uitvoeren met de lijst met ingebouwde Tuples, gebruikt u de volgende code:

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="generate-feature-importance-values-via-remote-runs"></a>Urgentie waarden van functies genereren via externe uitvoeringen

In het volgende voor beeld ziet u hoe u de-klasse kunt gebruiken `ExplanationClient` om de vertolking van modellen voor externe uitvoeringen in te scha kelen. Het is conceptueel vergelijkbaar met het lokale proces, met uitzonde ring van het volgende:

* Gebruik de `ExplanationClient` in de externe uitvoering om de interpreter-context te uploaden.
* Down load de context later in een lokale omgeving.

1. Installeer het `azureml-interpret`-pakket.
    ```bash
    pip install azureml-interpret
    ```
1. Een trainings script maken in een lokale Jupyter Notebook. Bijvoorbeeld `train_explain.py`.

    ```python
    from azureml.interpret import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=feature_names, 
                                 classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

1. Stel een Azure Machine Learning Compute in als uw reken doel en verzend uw trainings uitvoering. Zie [Azure machine learning compute-clusters maken en beheren](how-to-create-attach-compute-cluster.md) voor instructies. Mogelijk vindt u ook de [voorbeeld notitieblokken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) nuttig.

1. Down load de uitleg in uw lokale Jupyter Notebook.

    ```python
    from azureml.interpret import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```


## <a name="visualizations"></a>Visualisaties

Nadat u de uitleg in uw lokale Jupyter Notebook hebt gedownload, kunt u het visualisatie dashboard gebruiken om uw model te begrijpen en te interpreteren. Gebruik de volgende code om de widget van het visualisatie dashboard in uw Jupyter Notebook te laden:

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, datasetX=x_test)
```

De visualisatie ondersteunt uitleg over zowel ontworpen als onbewerkte functies. Onbewerkte toelichtingen zijn gebaseerd op de functies van de oorspronkelijke gegevensset en de door de engineer verstrekte uitleg zijn gebaseerd op de functies van de gegevensset waarop functie techniek is toegepast.

Wanneer u probeert een model te interpreteren ten opzichte van de oorspronkelijke gegevensset, wordt het aanbevolen om onbewerkte toelichtingen te gebruiken, omdat elk belang rijk kenmerk overeenkomt met een kolom uit de oorspronkelijke gegevensset. Een scenario waarin toelichtingen van de implementatie handig kunnen zijn, is bij het onderzoeken van de impact van afzonderlijke categorieën vanuit een categorische-functie. Als er een een-hot-code ring wordt toegepast op een categorische-functie, bevat de resulterende uitleg bij een technicus een andere urgentie waarde per categorie, één per functie met een hot Engineer. Dit kan handig zijn bij het beperken van het meest informatieve deel van de gegevensset.

> [!NOTE]
> Onderliggend en onbewerkte uitleg worden sequentieel berekend. Eerst wordt een door een technicus geleerde uitleg gemaakt op basis van de model-en parametrisatie-pijp lijn. Vervolgens wordt de onbewerkte uitleg gemaakt op basis van de uitleg die is opgetreden door het samen voegen van het belang van de functies die van dezelfde onbewerkte functie afkomstig zijn.

### <a name="create-edit-and-view-dataset-cohorts"></a>Cohortes van een gegevensset maken, bewerken en weer geven

Het bovenste lint toont de algemene statistieken voor uw model en gegevens. U kunt uw gegevens in dataset-cohortes of-subgroepen delen en analyseren om de prestaties en uitleg van uw model over deze gedefinieerde subgroepen te onderzoeken of te vergelijken. Door de statistieken van uw gegevensset en de uitleg over deze subgroepen te vergelijken, kunt u een idee krijgen van de oorzaken van mogelijke fouten in één groep versus een andere.

[![Maken, bewerken en weer geven van gegevensset-cohortes](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif#lightbox)

### <a name="understand-entire-model-behavior-global-explanation"></a>Volledige model gedrag begrijpen (globale uitleg) 

De eerste drie tabbladen van het uitleg-dash board bieden een algemene analyse van het getrainde model samen met de voor spellingen en toelichtingen.

#### <a name="model-performance"></a>Modelprestaties
Evalueer de prestaties van uw model door de verdeling van de Voorspellings waarden en de waarden van de prestatie gegevens van uw model te verkennen. U kunt uw model verder onderzoeken door te kijken naar een vergelijkende analyse van de prestaties in verschillende cohortes of subgroepen van uw gegevensset. Selecteer Filters langs y-waarde en x-waarde om te knippen over verschillende dimensies. Statistieken weer geven zoals nauw keurigheid, precisie, intrekken, fout positief (voor) en ONWAAR (FNR).

[![Het tabblad model prestaties in de uitleg van de visualisatie](./media/how-to-machine-learning-interpretability-aml/model-performance.gif)](./media/how-to-machine-learning-interpretability-aml/model-performance.gif#lightbox)

#### <a name="dataset-explorer"></a>Gegevensset Verkenner
Verken de statistieken van uw gegevensset door verschillende filters langs de X-, Y-en kleuren assen te selecteren om uw gegevens te segmenteren in verschillende dimensies. Maak bovenstaande gegevensset-cohortes om de statistieken van de gegevensset te analyseren met filters zoals voorspelde resultaten, functies van gegevensset en fout groepen. Gebruik het tandwiel pictogram in de rechter bovenhoek van de grafiek om de grafiek typen te wijzigen.

[![Het tabblad gegevensset Verkenner in de uitleg van de visualisatie](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif#lightbox)

#### <a name="aggregate-feature-importance"></a>Belang van cumulatieve functie
Bekijk de belangrijkste belang rijke functies die van invloed zijn op de voor spellingen van uw algemene model (ook wel bekend als globale uitleg). Gebruik de schuif regelaar om de aflopende urgentie waarden van de functie weer te geven. Selecteer Maxi maal drie cohort om de belangrijkste waarden van de functie naast elkaar weer te geven. Klik op een van de functie balken in de grafiek om te zien hoe waarden van de geselecteerde functie van invloed zijn op de voor spelling van het model in de onderstaande afhankelijke tekening.

[![Tabblad belang rijke functie in de beschrijving van de visualisatie](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif)](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>Meer informatie over afzonderlijke voor spellingen (lokale uitleg) 

Op het vierde tabblad van het tabblad uitleg kunt u inzoomen op een afzonderlijk data Point en de afzonderlijke functie belangen. U kunt het urgentie diagram van de afzonderlijke functie voor elk gegevens punt laden door te klikken op een van de afzonderlijke gegevens punten in het hoofd verstrooiings teken of door een specifieke data Point te selecteren in de wizard van het deel venster aan de rechter kant.

|Plotten|Beschrijving|
|----|-----------|
|Urgentie van afzonderlijke functie|Hier worden de belangrijkste belang rijke functies voor een afzonderlijke voor spelling weer gegeven. Helpt het lokale gedrag van het onderliggende model op een specifiek gegevens punt te illustreren.|
|What-If analyse|Hiermee kunt u de functie waarden van het geselecteerde werkelijke gegevens punt wijzigen en de resulterende wijzigingen in de Voorspellings waarde observeren door een hypothetisch data Point te genereren met de nieuwe functie waarden.|
|Afzonderlijke Voorwaardelijke verwachting (ijs)|Hiermee wordt de functie waarde gewijzigd van een minimum waarde naar een maximum waarde. Beschrijft hoe de voor spelling van het gegevens punt wordt gewijzigd wanneer een functie wordt gewijzigd.|

[![Het belang van afzonderlijke functies en wat als tabblad in het uitleg-dash board](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif)](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif#lightbox)

> [!NOTE]
> Dit zijn uitleg op basis van veel benaderingen en zijn niet de ' Oorzaak ' van voor spellingen. Zonder strikte wiskundige robuustheid van het oorzakelijke geval adviseren we gebruikers niet om levenscyclus beslissingen te nemen op basis van de functie perturbations van het hulp programma What-If. Dit hulp programma is voornamelijk bedoeld voor het leren van het model en het opsporen van fouten.

### <a name="visualization-in-azure-machine-learning-studio"></a>Visualisatie in Azure Machine Learning Studio

Als u de stappen voor [externe interpretaties](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs) hebt voltooid (upload gegenereerde uitleg bij Azure machine learning uitvoerings geschiedenis), kunt u het visualisatie dashboard weer geven in [Azure machine learning Studio](https://ml.azure.com). Dit dash board is een eenvoudigere versie van het visualisatie dashboard dat hierboven wordt beschreven. What-If dataas genereren en ijs worden uitgeschakeld omdat er geen actieve Compute is in Azure Machine Learning studio die de real-time berekeningen kan uitvoeren.

Als de gegevensset, globale en lokale uitleg beschikbaar zijn, worden alle tabbladen gevuld met gegevens. Als er alleen een globale uitleg beschikbaar is, wordt het tabblad urgentie van de afzonderlijke functie uitgeschakeld.

Volg een van deze paden om toegang te krijgen tot het visualisatie dashboard in Azure Machine Learning studio:

* Het deel venster **experimenten** (preview)
  1. Selecteer **experimenten** in het linkerdeel venster om een lijst met experimenten te bekijken die u op Azure machine learning hebt uitgevoerd.
  1. Selecteer een bepaald experiment om alle uitvoeringen in dat experiment weer te geven.
  1. Selecteer een run en klik vervolgens op het tabblad **uitleg** voor het visualisatie-dash board.

   [![Visualisatie dashboard met het belang van de cumulatieve functie in AzureML Studio in experimenten](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png)](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png#lightbox)

* Deel venster **modellen**
  1. Als u uw oorspronkelijke model hebt geregistreerd door de stappen in [modellen implementeren met Azure machine learning](./how-to-deploy-and-where.md)te volgen, kunt u **modellen** selecteren in het linkerdeel venster om de app weer te geven.
  1. Selecteer een model en klik vervolgens op het tabblad **uitleg** om het visualisatie dashboard te bekijken.

## <a name="interpretability-at-inference-time"></a>Interpreteer tijd

U kunt de uitleger samen met het oorspronkelijke model implementeren en gebruiken om tijd te verhelpen om de afzonderlijke functie belang rijke waarden (lokale uitleg) voor een nieuwe data Point te bieden. We bieden ook licht gewichten uitleg over scores om de prestaties van de interacties te verbeteren. dit wordt momenteel alleen ondersteund in Azure Machine Learning SDK. Het proces voor het implementeren van een lichtere Score uitleg is vergelijkbaar met het implementeren van een model en bevat de volgende stappen:

1. Maak een uitleg-object. U kunt bijvoorbeeld het volgende gebruiken `TabularExplainer` :

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Maak een beoordelings uitleg met het object uitleg.

   ```python
   from azureml.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Configureer en Registreer een installatie kopie die gebruikmaakt van het scoreer uitleg model.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Als optionele stap kunt u de uitleg van de Score van de Cloud ophalen en de uitleg testen.

   ```python
   from azureml.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Implementeer de installatie kopie op een reken doel door de volgende stappen uit te voeren:

   1. Als dat nodig is, registreert u uw oorspronkelijke Voorspellings model door de stappen te volgen in [modellen implementeren met Azure machine learning](./how-to-deploy-and-where.md).

   1. Een score bestand maken.

         ```python
         %%writefile score.py
         import json
         import numpy as np
         import pandas as pd
         import os
         import pickle
         from sklearn.externals import joblib
         from sklearn.linear_model import LogisticRegression
         from azureml.core.model import Model
          
         def init():
         
            global original_model
            global scoring_model
             
            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

         def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
         ```
   1. De implementatieconfiguratie configureren.

         Deze configuratie is afhankelijk van de vereisten van uw model. In het volgende voor beeld wordt een configuratie gedefinieerd die gebruikmaakt van één CPU-kern en één GB aan geheugen.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. Maak een bestand met omgevings afhankelijkheden.

         ```python
         from azureml.core.conda_dependencies import CondaDependencies

         # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

         azureml_pip_packages = ['azureml-defaults', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

         # specify CondaDependencies obj
         myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                          pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                          pin_sdk_version=False)


         with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

         with open("myenv.yml","r") as f:
            print(f.read())
         ```

   1. Maak een aangepaste dockerfile met g + + geïnstalleerd.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. Implementeer de gemaakte installatie kopie.
   
         Dit proces duurt ongeveer vijf minuten.

         ```python
         from azureml.core.webservice import Webservice
         from azureml.core.image import ContainerImage

         # use the custom scoring, docker, and conda files we created above
         image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                         docker_file="dockerfile",
                                                         runtime="python",
                                                         conda_file="myenv.yml")

         # use configs and models generated above
         service = Webservice.deploy_from_model(workspace=ws,
                                             name='model-scoring-service',
                                             deployment_config=aciconfig,
                                             models=[scoring_explainer_model, original_model],
                                             image_config=image_config)

         service.wait_for_deployment(show_output=True)
         ```

1. De implementatie testen.

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Opschonen.

   Als u een geïmplementeerde webservice wilt verwijderen, gebruikt u `service.delete()` .

## <a name="troubleshooting"></a>Problemen oplossen

* **Sparse gegevens niet ondersteund**: het model uitleg van het dash board wordt aanzienlijk vertraagd of langzamer met een groot aantal functies. Daarom bieden we momenteel geen ondersteuning voor sparse gegevens. Daarnaast ontstaan er algemene geheugen problemen met grote gegevens sets en een groot aantal functies. 

* **Prognose modellen worden niet ondersteund met model verklaringen**: interpretiteit, aanbevolen model uitleg is niet beschikbaar voor AutoML-prognose experimenten waarbij de volgende algoritmen worden aanbevolen als het beste model: TCNForecaster, AutoArima, Prophet, ExponentialSmoothing, Average, Naive, seizoen Average en seizoen Naive. AutoML-prognose heeft regressie modellen die ondersteuning bieden voor uitleg. In het uitleg-dash board wordt het tabblad ' belang rijk onderdeel ' echter alleen niet ondersteund voor prognoses vanwege de complexiteit van hun gegevens pijplijnen.

* **Lokale uitleg voor de gegevens index**: het uitleg-dash board biedt geen ondersteuning voor gerelateerde lokale urgentie waarden in een rij-id van de oorspronkelijke validatie gegevensset als die gegevensset groter is dan 5000 data Points als het dash board de gegevens wille keurig downsamplen. Het dash board toont echter de waarden van de onbewerkte gegevensset voor elk data Point dat wordt door gegeven aan het dash board onder het tabblad prioriteit van afzonderlijke functie. Gebruikers kunnen lokale belang rijke gegevens weer toewijzen aan de oorspronkelijke gegevensset door te voldoen aan de waarden van de onbewerkte gegevensset-onderdelen. Als de grootte van de validatie gegevensset kleiner is dan 5000 steek proeven, `index` komt de functie in AzureML Studio overeen met de index in de validatie-gegevensset.

* **Wat-als/ijs-grafieken worden niet ondersteund in Studio**: What-If en afzonderlijke Runtimes voor voorwaardelijke verwachting (ijs) worden niet ondersteund in azure machine learning studio onder het tabblad uitleg, omdat voor de geüploade uitleg een actieve Compute moet worden berekend voor het opnieuw berekenen van voor spellingen en waarschijnlijkheid van perturbed-functies. De functie wordt momenteel ondersteund in Jupyter-notebooks wanneer deze wordt uitgevoerd als een widget met de SDK.


## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het vertolken van modellen](how-to-machine-learning-interpretability.md)

[Azure Machine Learning voor beeld van interpretaties van voor beelden bekijken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
