---
title: 'Python-model maken: module verwijzing'
titleSuffix: Azure Machine Learning
description: Leer hoe u de module python-model maken in Azure Machine Learning kunt gebruiken om een aangepaste module voor model lering of gegevens verwerking te maken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/18/2020
ms.openlocfilehash: 03f80ebeb7b30d8c0fe14060335541d77b7523b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898619"
---
# <a name="create-python-model-module"></a>Python-model module maken

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Meer informatie over het gebruik van de module python-model maken voor het maken van een niet-traind model van een python-script. U kunt het model baseren op alle informatie die is opgenomen in een python-pakket in de Azure Machine Learning Designer-omgeving. 

Nadat u het model hebt gemaakt, kunt u [Train model](train-model.md) gebruiken om het model op een gegevensset te trainen, zoals elke andere informatieer in azure machine learning. Het getrainde model kan worden door gegeven aan het [score model](score-model.md) om voor spellingen te maken. Vervolgens kunt u het getrainde model opslaan en de Score werk stroom publiceren als een webservice.

> [!WARNING]
> Op dit moment is het niet mogelijk om deze module te verbinden met het afstemmen van de module **model Hyper parameters** of door de gescoorde resultaten van een python-model door te geven om het [model te evalueren](evaluate-model.md). Als u de Hyper parameters wilt afstemmen of een model wilt evalueren, kunt u een aangepast python-script schrijven met behulp van de script module voor het [uitvoeren van python](execute-python-script.md) .


## <a name="configure-the-module"></a>De module configureren

Het gebruik van deze module vereist tussenliggende of deskundige kennis van python. De module ondersteunt het gebruik van een deel van de informatie die is opgenomen in de Python-pakketten die al in Azure Machine Learning zijn geïnstalleerd. Zie de lijst met vooraf geïnstalleerde Python-pakketten in [uitvoeren python-script](execute-python-script.md).

> [!NOTE]
> Zorg ervoor dat u uw script schrijft en er zeker van zijn dat er geen syntaxis fout is, zoals het gebruik van een niet-gedeclareerd object of een niet-geïmporteerde module.

> [!NOTE]
> Betaal ook extra aandacht aan de lijst met vooraf geïnstalleerde modules in [python-script uitvoeren](execute-python-script.md). Importeer vooraf geïnstalleerde modules alleen. Installeer geen extra pakketten, zoals ' PIP install xgboost ' in dit script. anders worden er fouten gegenereerd bij het lezen van modellen in modules met een lagere stroom.
  
In dit artikel wordt beschreven hoe u een **python-model maakt** met een eenvoudige pijp lijn. Hier volgt een diagram van de pijp lijn:

![Diagram van het python-model maken](./media/module/create-python-model.png)

1. Selecteer **python-model maken** en bewerk het script om uw model-of gegevens beheer proces te implementeren. U kunt het model baseren op alle informatie die is opgenomen in een python-pakket in de Azure Machine Learning omgeving.

> [!NOTE]
> Betaal extra aandacht voor de opmerkingen in voorbeeld code van het script en zorg ervoor dat uw script de vereiste moet volgen, met inbegrip van de naam van de klasse, methoden en hand tekening van de methode. Schending leidt tot uitzonde ringen. 

   De volgende voorbeeld code van de Naive Bayes-classificatie met twee klassen maakt gebruik van het populaire *sklearn* -pakket:

   ```Python

   # The script MUST define a class named AzureMLModel.
   # This class MUST at least define the following three methods:
       # __init__: in which self.model must be assigned,
       # train: which trains self.model, the two input arguments must be pandas DataFrame,
       # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
   # The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.

   # Please do not install extra packages such as "pip install xgboost" in this script,
   # otherwise errors will be raised when reading models in down-stream modules.
   
   import pandas as pd
   from sklearn.naive_bayes import GaussianNB


   class AzureMLModel:
       def __init__(self):
           self.model = GaussianNB()
           self.feature_column_names = list()

       def train(self, df_train, df_label):
           # self.feature_column_names records the column names used for training.
           # It is recommended to set this attribute before training so that the
           # feature columns used in predict and train methods have the same names.
           self.feature_column_names = df_train.columns.tolist()
           self.model.fit(df_train, df_label)

       def predict(self, df):
           # The feature columns used for prediction MUST have the same names as the ones for training.
           # The name of score column ("Scored Labels" in this case) MUST be different from any other columns in input data.
           return pd.DataFrame(
               {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
                'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
           )


   ```

2. Verbind de module **python-model maken** die u zojuist hebt gemaakt om **model** -en **score model** te trainen.

3. Als u het model wilt evalueren, voegt u een [script](execute-python-script.md) module voor het uitvoeren van een python uit en bewerkt u het python-script.

   Het volgende script is voorbeeld evaluatie code:

   ```Python


   # The script MUST contain a function named azureml_main
   # which is the entry point for this module.

   # imports up here can be used to 
   import pandas as pd

   # The entry point function MUST have two input arguments:
   #   Param<dataframe1>: a pandas.DataFrame
   #   Param<dataframe2>: a pandas.DataFrame
   def azureml_main(dataframe1 = None, dataframe2 = None):
    
       from sklearn.metrics import accuracy_score, precision_score, recall_score, roc_auc_score, roc_curve
       import pandas as pd
       import numpy as np
    
       scores = dataframe1.ix[:, ("income", "Scored Labels", "probabilities")]
       ytrue = np.array([0 if val == '<=50K' else 1 for val in scores["income"]])
       ypred = np.array([0 if val == '<=50K' else 1 for val in scores["Scored Labels"]])    
       probabilities = scores["probabilities"]
    
       accuracy, precision, recall, auc = \
       accuracy_score(ytrue, ypred),\
       precision_score(ytrue, ypred),\
       recall_score(ytrue, ypred),\
       roc_auc_score(ytrue, probabilities)
    
       metrics = pd.DataFrame();
       metrics["Metric"] = ["Accuracy", "Precision", "Recall", "AUC"];
       metrics["Value"] = [accuracy, precision, recall, auc]
    
       return metrics,

   ```

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 