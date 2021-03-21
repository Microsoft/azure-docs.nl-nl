---
title: Naslaginformatie over algoritmen en modules
description: Meer informatie over de Azure Machine Learning Designer-modules die u kunt gebruiken om uw eigen machine learning projecten te maken.
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/09/2020
ms.openlocfilehash: 89ad9aae7c0d01971bbcfc7e392cb9d455ef85cd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94376839"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer"></a>Naslag informatie voor algoritme & module voor Azure Machine Learning Designer

Deze referentie-inhoud biedt de technische achtergrond informatie over elk van de machine learning algoritmen en modules die beschikbaar zijn in Azure Machine Learning Designer.

Elke module vertegenwoordigt een set code die onafhankelijk kan worden uitgevoerd en een machine learning taak kan uitvoeren, op basis van de vereiste invoer. Een module kan een bepaald algoritme bevatten of een taak uitvoeren die belang rijk is in machine learning, zoals ontbrekende vervanging van waarden of statistische analyses.

Zie voor hulp bij het kiezen van algoritmen 
* [Algoritmen selecteren](../how-to-select-algorithms.md)
* [Cheat-werk blad Azure Machine Learning-algoritme](../algorithm-cheat-sheet.md)

> [!TIP]
> In een pijp lijn in de ontwerp functie kunt u informatie ophalen over een specifieke module. Selecteer de koppeling **meer informatie** in de module kaart bij het aanwijzen van de module in de module lijst of in het rechterdeel venster van de module.

## <a name="data-preparation-modules"></a>Modules voor gegevens voorbereiding


| Functionaliteit | Beschrijving | Module |
| --- |--- | --- |
| Gegevens invoer en-uitvoer | Verplaats gegevens van Cloud bronnen naar uw pijp lijn. Schrijf uw resultaten of tussenliggende gegevens naar Azure Storage, SQL Database of Hive, terwijl u een pijp lijn uitvoert, of gebruik Cloud opslag voor het uitwisselen van gegevens tussen pijp lijnen.  | [Gegevens handmatig invoeren](enter-data-manually.md) <br/> [Gegevens exporteren](export-data.md) <br/> [Gegevens importeren](import-data.md) |
| Gegevenstransformatie | Bewerkingen op gegevens die uniek zijn voor machine learning, zoals het normaliseren of Binningen van gegevens, het beperken van de afmetingen en het converteren van gegevens over verschillende bestands indelingen.| [Kolommen toevoegen](add-columns.md) <br/> [Rijen toevoegen](add-rows.md) <br/> [Wiskundige bewerking toepassen](apply-math-operation.md) <br/> [SQL-transformatie toepassen](apply-sql-transformation.md) <br/> [Ontbrekende gegevens opschonen](clean-missing-data.md) <br/> [Waarden inperken](clip-values.md) <br/> [Converteren naar CSV](convert-to-csv.md) <br/> [Converteren naar gegevensset](convert-to-dataset.md) <br/> [Converteren naar indicatorwaarden](convert-to-indicator-values.md) <br/> [Metagegevens bewerken](edit-metadata.md) <br/> [Gegevens in opslaglocaties groeperen](group-data-into-bins.md) <br/> [Gegevens samenvoegen](join-data.md) <br/> [Gegevens normaliseren](normalize-data.md) <br/> [Partitie en voorbeeld](partition-and-sample.md)  <br/> [Dubbele rijen verwijderen](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Kolomtransformatie selecteren](select-columns-transform.md) <br/> [Kolommen in gegevensset selecteren](select-columns-in-dataset.md) <br/> [Gegevens splitsen](split-data.md) |
| Functie selectie | Selecteer een subset van relevante, nuttige functies om te gebruiken bij het bouwen van een analytisch model. | [Functieselectie op basis van filters](filter-based-feature-selection.md) <br/> [Belang van permutatiefunctie](permutation-feature-importance.md) |
| Statistische functies | Bieden een groot aantal statistische methoden die zijn gerelateerd aan data Science. | [Gegevens samenvatten](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>Machine learning-algoritmen

| Functionaliteit | Beschrijving | Module |
| --- |--- | --- |
| Regressie | Een waarde voors pellen. | [Regressie versterkte beslissingsstructuur](boosted-decision-tree-regression.md) <br/> [Regressie beslissingsforest](decision-forest-regression.md) <br/> [Regressie snelle forestkwantiel](fast-forest-quantile-regression.md)  <br/> [Lineaire regressie](linear-regression.md)  <br/> [Regressie neuraal netwerk](neural-network-regression.md)  <br/> [Regressie Poisson](poisson-regression.md)  <br/>|
| Clustering | Groepeert gegevens tegelijk.| [K-means-clustering](k-means-clustering.md)
| Classificatie | Voors pellen van een klasse.  U kunt kiezen uit binaire (twee klasse) of meerdere klasse-algoritmen.| [Versterkte beslissingsstructuur met meerdere klassen](multiclass-boosted-decision-tree.md) <br/> [Beslissingsforest met meerdere klassen](multiclass-decision-forest.md) <br/> [Logistieke regressie met meerdere klassen](multiclass-logistic-regression.md)  <br/> [Neuraal netwerk met meerdere klassen](multiclass-neural-network.md) <br/> [One-vs- All Multiclass](one-vs-all-multiclass.md) <br/> [One-vs- One Multiclass](one-vs-one-multiclass.md) <br/>[Gemiddeld perceptron met twee klassen](two-class-averaged-perceptron.md) <br/>  [Versterkte beslissingsstructuur met twee klassen](two-class-boosted-decision-tree.md)  <br/> [Beslissingsforest met twee klassen](two-class-decision-forest.md) <br/>  [Logistieke regressie met twee klassen](two-class-logistic-regression.md) <br/> [Neuraal netwerk met twee klassen](two-class-neural-network.md) <br/> [Ondersteuning voor vectormachine met twee klassen](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>Modules voor het maken en evalueren van modellen

| Functionaliteit | Beschrijving | Module |
| --- |--- | --- |
| Model training | Voer gegevens uit via de algoritme. |  [Clustermodel trainen](train-clustering-model.md) <br/> [Trainingsmodel](train-model.md) <br/> [Pytorch-model trainen](train-pytorch-model.md) <br/> [Model Hyperparameters afstemmen](tune-model-hyperparameters.md) |
| Model leren en evalueren | Meet de nauw keurigheid van het getrainde model. | [Transformatie toepassen](apply-transformation.md) <br/> [Gegevens aan cluster toewijzen](assign-data-to-clusters.md) <br/> [Kruisvalidatie van model valideren](cross-validate-model.md) <br/> [Model evalueren](evaluate-model.md) <br/> [Afbeeldingsmodel voor score](score-image-model.md) <br/> [Score Model](score-model.md) |
| Python-taal | Schrijf code en sluit deze in een module in om python met uw pijp lijn te integreren. | [Python-model maken](create-python-model.md) <br/> [Python-script uitvoeren](execute-python-script.md) |
| R-taal | Schrijf code en sluit deze in een module in om R met uw pijp lijn te integreren. | [R-Script uitvoeren](execute-r-script.md) |
| Text Analytics | Bieden speciale reken kundige hulp middelen voor het werken met zowel gestructureerde als ongestructureerde tekst. |  [Word converteren naar vector](convert-word-to-vector.md) <br/> [N-Gram-functies uit tekst halen](extract-n-gram-features-from-text.md) <br/> [Functie-hashing](feature-hashing.md) <br/> [Tekst voorverwerken](preprocess-text.md) <br/> [Latente Dirichlet-toewijzing](latent-dirichlet-allocation.md) <br/> [Vowpal Wabbit-model scoren](score-vowpal-wabbit-model.md) <br/> [Vowpal Wabbit-model trainen](train-vowpal-wabbit-model.md)|
| Computer Vision | Voor verwerking van installatie kopie-en installatie kopieën gerelateerde modules. |  [Afbeeldingstransformatie toepassen](apply-image-transformation.md) <br/> [Converteren naar afbeeldingsmap](convert-to-image-directory.md) <br/> [Afbeeldingstransformatie initiëren](init-image-transformation.md) <br/> [Map om afbeeldingen te splitsen](split-image-directory.md) <br/> [DenseNet](densenet.md) <br/> [ResNet](resnet.md) |
| Aanbeveling | Aanbevelings modellen bouwen. | [Aanbevelingsfunctie voor evaluatie](evaluate-recommender.md) <br/> [Aanbevelingsfunctie voor SVD-score](score-svd-recommender.md) <br/> [Aanbevelingsfunctie voor wide en deep learning-scores](score-wide-and-deep-recommender.md)<br/> [Aanbevelingsfunctie van SVD-training](train-SVD-recommender.md) <br/> [Aanbevelingsfunctie voor wide en deep learning-trainingen](train-wide-and-deep-recommender.md)|
| Anomaliedetectie | Anomalie detectie modellen bouwen. | [Anomaliedetectie op basis van PCA](pca-based-anomaly-detection.md) <br/> [Anomaliedetectiemodel trainen](train-anomaly-detection-model.md) |


## <a name="web-service"></a>Webservice

Meer informatie over de [modules voor webservices](web-service-input-output.md) die nodig zijn voor realtime-interferentie in azure machine learning Designer.

## <a name="error-messages"></a>Foutberichten

Meer informatie over de [fout berichten en uitzonderings codes](designer-error-codes.md) die u kunt tegen komen met behulp van modules in azure machine learning Designer.

## <a name="next-steps"></a>Volgende stappen

* [Zelf studie: een model bouwen in Designer om automatische prijzen te voors pellen](../tutorial-designer-automobile-price-train-score.md)
