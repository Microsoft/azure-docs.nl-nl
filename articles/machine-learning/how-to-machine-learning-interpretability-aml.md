---
title: ML& modellen interpreteren in Python (preview)
titleSuffix: Azure Machine Learning
description: Ontdek hoe u uitleg krijgt over hoe uw machine learning model het belang van functies bepaalt en voorspellingen doet wanneer u de Azure Machine Learning SDK gebruikt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 07/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, responsible-ml
ms.openlocfilehash: d79458cfc76adcfd35a6b8dee40c0c45786abc28
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763285"
---
# <a name="use-the-interpretability-package-to-explain-ml-models--predictions-in-python-preview"></a>Het interpreteerbaarheidspakket gebruiken om ML-modellen uit & voorspellingen in Python (preview)

In deze handleiding leert u hoe u het interpreteerbaarheidspakket van de Azure Machine Learning Python SDK kunt gebruiken om de volgende taken uit te voeren:


* Leg het hele modelgedrag of afzonderlijke voorspellingen lokaal uit op uw persoonlijke computer.

* Interpreteerbaarheidstechnieken inschakelen voor ontworpen functies.

* Het gedrag voor het hele model en de afzonderlijke voorspellingen in Azure uitleggen.

* Gebruik een visualisatiedashboard om te communiceren met de uitleg van uw model.

* Implementeer een score-uitleg naast uw model om uitleg te observeren tijdens de deferencing.


Zie Model [interpretability in](how-to-machine-learning-interpretability.md) Azure Machine Learning en sample notebooks voor meer informatie over de ondersteunde interpreteerbaarheidstechnieken en machine learning [modellen.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)

Zie [Interpretability: model explanations for automated machine learning models (preview) (Interpretability: model explanations for automated machine learning models (preview) (Interpretability: model explanations for automated machine learning models (preview) (Interpretability: model explanations for automated machine learning models (preview) (Uitleg](how-to-machine-learning-interpretability-automl.md)van het model voor geautomatiseerde machine learning modellen (preview) voor instructies over het inschakelen van interpreteerbaarheid voor modellen die zijn getraind met geautomatiseerde machine learning. 

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>Waarde voor functie-belang genereren op uw persoonlijke computer 
In het volgende voorbeeld ziet u hoe u het interpreteerbaarheidspakket op uw persoonlijke computer gebruikt zonder contact op te nemen met Azure-services.

1. Installeer het `azureml-interpret`-pakket.
    ```bash
    pip install azureml-interpret
    ```

2. Een voorbeeldmodel trainen in een lokaal Jupyter Notebook.

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
   * Als u een uitlegobject wilt initialiseren, geeft u uw model en wat trainingsgegevens door aan de constructor van de uitleg.
   * Als u uw uitleg en visualisaties meer informatief wilt maken, kunt u ervoor kiezen om functienamen en uitvoerklassenamen door te geven bij classificatie.

   De volgende codeblokken laten zien hoe u een uitlegobject maakt met `TabularExplainer` `MimicExplainer` , en `PFIExplainer` lokaal.
   * `TabularExplainer` roept een van de drie SHAP-uitlegders aan onder ( `TreeExplainer` `DeepExplainer` , of `KernelExplainer` ).
   * `TabularExplainer` selecteert automatisch de meest geschikte voor uw use-case, maar u kunt elk van de drie onderliggende uitlegs rechtstreeks aanroepen.

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

### <a name="explain-the-entire-model-behavior-global-explanation"></a>Het hele modelgedrag uitleggen (globale uitleg) 

Raadpleeg het volgende voorbeeld om u te helpen bij het verzamelen van (algemene) functie-belangwaarden.

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

### <a name="explain-an-individual-prediction-local-explanation"></a>Een afzonderlijke voorspelling uitleggen (lokale uitleg)
Haal de waarden voor het belang van afzonderlijke functies van verschillende gegevenspunten op door uitleg aan te roepen voor een afzonderlijk exemplaar of een groep exemplaren.
> [!NOTE]
> `PFIExplainer` biedt geen ondersteuning voor lokale uitleg.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

### <a name="raw-feature-transformations"></a>Onbewerkte functietransformaties

U kunt ervoor kiezen om uitleg te krijgen in termen van onbewerkte, ongetransformeerde functies in plaats van ontworpen functies. Voor deze optie geeft u de functietransformatiepijplijn door aan de uitleg in `train_explain.py` . Anders geeft de uitleg uitleg over de ontworpen functies.

De indeling van ondersteunde transformaties is hetzelfde als beschreven in [sklearn-pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). Over het algemeen worden transformaties ondersteund zolang ze op één kolom werken, zodat het duidelijk is dat ze een-op-veel zijn.

Krijg een uitleg over onbewerkte functies met behulp van een of met een lijst met passende `sklearn.compose.ColumnTransformer` transformator-tuples. In het volgende voorbeeld wordt `sklearn.compose.ColumnTransformer` gebruikt.

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

Als u het voorbeeld wilt uitvoeren met de lijst met passende transformator-tuples, gebruikt u de volgende code:

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

## <a name="generate-feature-importance-values-via-remote-runs"></a>Waarden voor functie-belang genereren via externe runs

In het volgende voorbeeld ziet u hoe u de klasse kunt `ExplanationClient` gebruiken om model interpreteerbaarheid in te stellen voor externe uitvoeringen. Het is conceptueel vergelijkbaar met het lokale proces, behalve u:

* Gebruik de `ExplanationClient` in de externe run om de interpreteerbaarheidscontext te uploaden.
* Download de context later in een lokale omgeving.

1. Installeer het `azureml-interpret`-pakket.
    ```bash
    pip install azureml-interpret
    ```
1. Maak een trainingsscript in een lokaal Jupyter Notebook. Bijvoorbeeld `train_explain.py`.

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

1. Stel een rekendoel Azure Machine Learning rekendoel in en verzend de trainingsrun. Zie Create and manage Azure Machine Learning compute clusters (Een [rekenclusters maken](how-to-create-attach-compute-cluster.md) en beheren) voor instructies. Mogelijk vindt u de [voorbeeldnotenotes ook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) nuttig.

1. Download de uitleg in uw lokale Jupyter Notebook.

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

Nadat u de uitleg in uw lokale Jupyter Notebook, kunt u de visualisaties in het uitlegdashboard gebruiken om uw model te begrijpen en te interpreteren. Gebruik de volgende code om de dashboardwidget Jupyter Notebook uitleg te laden:

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, datasetX=x_test)
```

De visualisaties ondersteunen uitleg over zowel ontworpen als onbewerkte functies. Onbewerkte uitleg is gebaseerd op de functies van de oorspronkelijke gegevensset en de gedetailleerde uitleg is gebaseerd op de functies van de gegevensset feature engineering toegepast.

Wanneer u probeert een model te interpreteren met betrekking tot de oorspronkelijke gegevensset, is het raadzaam om onbewerkte uitleg te gebruiken, omdat elk functie-belang overeenkomt met een kolom uit de oorspronkelijke gegevensset. Een scenario waarin een ontworpen uitleg nuttig kan zijn, is bij het onderzoeken van de impact van afzonderlijke categorieën van een categorische functie. Als een one-hot-codering wordt toegepast op een categorische functie, bevat de resulterende engineered uitleg een andere belangwaarde per categorie, één per one-hot-engineered functie. Dit kan handig zijn bij het beperken van welk deel van de gegevensset het meest informatief is voor het model.

> [!NOTE]
> Berekende en onbewerkte uitleg wordt opeenvolgend berekend. Eerst wordt er een gemanipuleerde uitleg gemaakt op basis van het model en de featurization-pijplijn. Vervolgens wordt de onbewerkte uitleg gemaakt op basis van die gemanipuleerde uitleg door het belang te aggregeren van ontworpen functies die afkomstig zijn van dezelfde onbewerkte functie.

### <a name="create-edit-and-view-dataset-cohorts"></a>Cohorten van gegevenssets maken, bewerken en weergeven

Op het bovenste lint ziet u de algemene statistieken voor uw model en gegevens. U kunt uw gegevens in cohorten van gegevenssets of subgroepen segmenteren en analyseren om de prestaties en uitleg van uw model in deze gedefinieerde subgroepen te onderzoeken of te vergelijken. Door uw gegevenssetstatistieken en uitleg over deze subgroepen te vergelijken, kunt u een idee krijgen van de reden waarom er in de ene groep fouten optreden ten opzichte van de andere.

[![Cohorten voor gegevenssets maken, bewerken en weergeven](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif#lightbox)

### <a name="understand-entire-model-behavior-global-explanation"></a>Volledig modelgedrag begrijpen (globale uitleg) 

De eerste drie tabbladen van het uitlegdashboard bieden een algemene analyse van het getrainde model, samen met de voorspellingen en uitleg.

#### <a name="model-performance"></a>Modelprestaties
Evalueer de prestaties van uw model door de distributie van uw voorspellingswaarden en de waarden van de metrische gegevens van uw modelprestaties te verkennen. U kunt uw model verder onderzoeken door te kijken naar een vergelijkende analyse van de prestaties voor verschillende cohorten of subgroepen van uw gegevensset. Selecteer filters samen met y-value en x-value om over verschillende dimensies te knippen. Bekijk metrische gegevens zoals nauwkeurigheid, precisie, terughalen, fout-positieve snelheid (FPR) en fout-negatieve snelheid (FNR).

[![Tabblad Modelprestaties in de visualisatie van de uitleg](./media/how-to-machine-learning-interpretability-aml/model-performance.gif)](./media/how-to-machine-learning-interpretability-aml/model-performance.gif#lightbox)

#### <a name="dataset-explorer"></a>Gegevenssetverkenner
Verken uw gegevenssetstatistieken door verschillende filters op de X-, Y- en kleurassen te selecteren om uw gegevens over verschillende dimensies te segmenteren. Maak gegevenssetcohorten hierboven om gegevenssetstatistieken te analyseren met filters zoals voorspeld resultaat, functies van gegevenssets en foutgroepen. Gebruik het tandwielpictogram in de rechterbovenhoek van de grafiek om grafiektypen te wijzigen.

[![Tabblad Gegevenssetverkenner in de visualisatie van de uitleg](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif#lightbox)

#### <a name="aggregate-feature-importance"></a>Functie-belang aggregeren
Verken de belangrijkste functies die van invloed zijn op uw algemene modelvoorspellingen (ook wel globale uitleg genoemd). Gebruik de schuifregelaar om waarden voor aflopend functie-belang weer te geven. Selecteer maximaal drie cohorten om de waarden voor functie-belang naast elkaar te bekijken. Klik op een van de functiebalken in de grafiek om te zien hoe waarden van de geselecteerde functie invloed hebben op de modelvoorspelling in de onderstaande afhankelijkheidsgrafiek.

[![Tabblad Belang van aggregatiefunctie in de uitlegvisualisatie](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif)](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>Inzicht in afzonderlijke voorspellingen (lokale uitleg) 

Op het vierde tabblad van het tabblad Uitleg kunt u inzoomen op een afzonderlijk gegevenspunt en hun afzonderlijke functie-belang. U kunt de plot voor het belang van afzonderlijke functies voor elk gegevenspunt laden door te klikken op een van de afzonderlijke gegevenspunten in het hoofdspreidingsdiagram of door een specifiek gegevenspunt te selecteren in de wizard deelvenster aan de rechterkant.

|Plotten|Description|
|----|-----------|
|Belang van afzonderlijke functies|Toont de belangrijkste top-k-functies voor een afzonderlijke voorspelling. Helpt het lokale gedrag van het onderliggende model op een specifiek gegevenspunt te illustreren.|
|What-If analyse|Hiermee kunt u wijzigingen in functiewaarden van het geselecteerde echte gegevenspunt aanbrengen en resulterende wijzigingen in de voorspellingswaarde observeren door een hypothetisch gegevenspunt met de nieuwe functiewaarden te genereren.|
|Afzonderlijke voorwaardelijke verwachting (ICE)|Hiermee kunnen functiewaardewijzigingen van een minimumwaarde in een maximumwaarde worden gewijzigd. Helpt te illustreren hoe de voorspelling van het gegevenspunt verandert wanneer een functie wordt gewijzigd.|

[![Het belang van afzonderlijke functies en het what-if-tabblad in het uitlegdashboard](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif)](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif#lightbox)

> [!NOTE]
> Dit zijn uitleg op basis van veel benaderingen en zijn niet de 'oorzaak' van voorspellingen. Zonder strikte wiskundige robuustheid van causale deferentie raden we gebruikers niet aan om beslissingen in de echte wereld te nemen op basis van de functieverzwellingen van het What-If hulpprogramma. Dit hulpprogramma is voornamelijk bedoeld om inzicht te krijgen in uw model en debuggen.

### <a name="visualization-in-azure-machine-learning-studio"></a>Visualisatie in Azure Machine Learning-studio

Als u de stappen voor externe [interpreteerbaarheid](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs) voltooit (door gegenereerde uitleg te uploaden naar Azure Machine Learning Run History), kunt u de visualisaties bekijken op het [uitlegdashboard](https://ml.azure.com)in Azure Machine Learning-studio . Dit dashboard is een eenvoudigere versie van de dashboardwidget die wordt gegenereerd in uw Jupyter-notebook. What-If het genereren van gegevenspunten en ICE-plots zijn uitgeschakeld omdat er geen actieve compute in Azure Machine Learning-studio die hun realtime berekeningen kunnen uitvoeren.

Als de gegevensset, globale en lokale uitleg beschikbaar zijn, worden alle tabbladen ingevuld. Als er alleen een globale uitleg beschikbaar is, wordt het tabblad Belang van afzonderlijke functies uitgeschakeld.

Volg een van deze paden voor toegang tot het uitlegdashboard in Azure Machine Learning-studio:

* **Deelvenster Experimenten** (preview)
  1. Selecteer **Experimenten** in het linkerdeelvenster om een lijst met experimenten weer te geven die u hebt uitgevoerd op Azure Machine Learning.
  1. Selecteer een bepaald experiment om alle runs in dat experiment te bekijken.
  1. Selecteer een run en vervolgens het tabblad **Uitleg** naar het dashboard voor uitlegvisualisatie.

   [![Visualisatiedashboard met aggregatiefunctie-belang in AzureML Studio in experimenten](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png)](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png#lightbox)

* **Deelvenster Modellen**
  1. Als u het oorspronkelijke model hebt geregistreerd door de stappen te volgen in Modellen implementeren met [Azure Machine Learning](./how-to-deploy-and-where.md), kunt u **Modellen** selecteren in het linkerdeelvenster om het weer te geven.
  1. Selecteer een model en vervolgens het tabblad **Uitleg** om het uitlegdashboard weer te geven.

## <a name="interpretability-at-inference-time"></a>Interpreteerbaarheid tijdens de deferentie

U kunt de uitleg samen met het oorspronkelijke model implementeren en dit gebruiken tijdens de deferentie om de afzonderlijke waarden voor functie-belang (lokale uitleg) op te geven voor elk nieuw gegevenspunt. We bieden ook uitleg over lichtere scores om de prestaties van de interpreteerbaarheid tijdens de deferentie te verbeteren. Deze worden momenteel alleen ondersteund in Azure Machine Learning SDK. Het proces voor het implementeren van een uitleg over een lichtere score is vergelijkbaar met het implementeren van een model en omvat de volgende stappen:

1. Maak een uitlegobject. U kunt bijvoorbeeld `TabularExplainer` gebruiken:

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Maak een scoring-uitleg met het uitlegobject.

   ```python
   from azureml.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Configureer en registreer een afbeelding die gebruikmaakt van het score-uitlegmodel.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Als optionele stap kunt u de score-uitleg ophalen uit de cloud en de uitleg testen.

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

1. Implementeer de afbeelding op een rekendoel door de volgende stappen uit te voeren:

   1. Registreer zo nodig uw oorspronkelijke voorspellingsmodel door de stappen te volgen in [Modellen implementeren met Azure Machine Learning.](./how-to-deploy-and-where.md)

   1. Maak een scoring-bestand.

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

         Deze configuratie is afhankelijk van de vereisten van uw model. Het volgende voorbeeld definieert een configuratie die gebruikmaakt van één CPU-kern en één GB geheugen.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. Maak een bestand met omgevingsafhankelijkheden.

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

   1. Maak een aangepaste dockerfile met g++ geïnstalleerd.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. Implementeer de gemaakte afbeelding.
   
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

1. Test de implementatie.

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

1. Ops schonen.

   Gebruik om een geïmplementeerde webservice te `service.delete()` verwijderen.

## <a name="troubleshooting"></a>Problemen oplossen

* **Sparse gegevens worden niet ondersteund:** het dashboard voor modeluitbreeding wordt aanzienlijk beperkt/vertraagd met een groot aantal functies. Daarom bieden we momenteel geen ondersteuning voor sparse gegevensindeling. Daarnaast ontstaan er algemene geheugenproblemen met grote gegevenssets en een groot aantal functies. 

* Prognosemodellen worden niet ondersteund met **modeluit** leggen: Interpretability, best model explanation, is not available for AutoML forecasting experiments that recommend the following algorithms as the best model: TCNForecaster, AutoArima, ExponentialSmoothing, Average, Naive, Seasonal Average, and Seasonal Naive. AutoML-prognoses hebben regressiemodellen die uitleg ondersteunen. In het uitlegdashboard wordt het tabblad Belang van afzonderlijke functies echter niet ondersteund voor prognoses vanwege de complexiteit van hun gegevenspijplijnen.

* **Lokale uitleg voor** gegevensindex: het uitlegdashboard biedt geen ondersteuning voor het in verband brengen van waarden van lokaal belang met een rij-id uit de oorspronkelijke validatiegegevensset als die gegevensset groter is dan 5000 gegevenspunten, omdat het dashboard willekeurig een downsamp van de gegevens bevat. Het dashboard toont echter onbewerkte functiewaarden voor gegevenssets voor elk gegevenspunt dat wordt doorgegeven aan het dashboard op het tabblad Belang van afzonderlijke functies. Gebruikers kunnen lokale belang terug aan de oorspronkelijke gegevensset koppelen door de onbewerkte functiewaarden van de gegevensset aan te passen. Als de grootte van de validatieset minder dan 5000 voorbeelden is, komt de functie in AzureML Studio overeen met de `index` index in de validatieset.

* **Wat-als-/ICE-plots** worden niet ondersteund in Studio: What-If- en ICE-plots (Individual Conditional Expectation) worden niet ondersteund in Azure Machine Learning-studio onder het tabblad Uitleg, omdat voor de geüploade uitleg een actieve berekening nodig is om voorspellingen en waarschijnlijkheden van verstoorde functies opnieuw te berekenen. Het wordt momenteel ondersteund in Jupyter-notebooks wanneer het wordt uitgevoerd als een widget met behulp van de SDK.


## <a name="next-steps"></a>Volgende stappen

[Meer informatie over de interpreteerbaarheid van modellen](how-to-machine-learning-interpretability.md)

[Bekijk de Azure Machine Learning notebooks voor interpreteerbaarheid](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
