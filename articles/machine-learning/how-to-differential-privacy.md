---
title: Differentiële privacy How-to-SmartNoise (preview)
titleSuffix: Azure Machine Learning
description: Meer informatie over het Toep assen van aanbevolen procedures voor privacybeleid om modellen te Azure Machine Learning met behulp van de open-source-SmartNoise-bibliotheken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, responsible-ml
ms.author: slbird
author: slbird
ms.reviewer: luquinta
ms.date: 01/21/2020
ms.openlocfilehash: 62a002569696da4ef18e7bd967f027eb8247ef65
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98681401"
---
# <a name="use-differential-privacy-in-azure-machine-learning-preview"></a>Differentiële privacy in Azure Machine Learning gebruiken (preview-versie)

Meer informatie over het Toep assen van de aanbevolen procedures voor privacybeleid voor het Azure Machine Learning modellen met behulp van de open-source-SmartNoise python-bibliotheken.

Differentiële privacy is de Gold-Standard-definitie van privacy. Systemen die voldoen aan deze definitie van privacy, bieden sterke garanties tegen een breed scala aan gegevens herstel en het hernoemen van aanvallen, met inbegrip van aanvallen door aanvallers met informatie over de hulp. Meer informatie over [hoe differentiële privacy werkt](./concept-differential-privacy.md).


## <a name="prerequisites"></a>Vereisten

- Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure Machine Learning](https://aka.ms/AMLFree).
- [Python 3](https://www.python.org/downloads/)

## <a name="install-smartnoise-python-libraries"></a>SmartNoise python-bibliotheken installeren

### <a name="standalone-installation"></a>Zelfstandige installatie

De bibliotheken zijn ontworpen om te werken vanuit gedistribueerde Spark-clusters en kunnen net als andere pakketten worden geïnstalleerd.

In de onderstaande instructies wordt ervan uitgegaan dat uw `python` en- `pip` opdrachten zijn toegewezen aan `python3` en `pip3` .

PIP gebruiken om de [SmartNoise Python-pakketten](https://pypi.org/project/opendp-smartnoise/)te installeren.

`pip install opendp-smartnoise`

Als u wilt controleren of de pakketten zijn geïnstalleerd, start u een python-prompt en typt u:

```python
import opendp.smartnoise.core
import opendp.smartnoise.sql
```

Als de imports zijn voltooid, zijn de bibliotheken geïnstalleerd en klaar voor gebruik.

### <a name="docker-image-installation"></a>Installatie van docker-installatie kopie

U kunt ook SmartNoise-pakketten gebruiken met docker.

Haal de `opendp/smartnoise` installatie kopie op voor het gebruik van de bibliotheken in een docker-container met Spark-, Jupyter-en voorbeeld code.

```sh
docker pull opendp/smartnoise:privacy
```

Zodra u de installatie kopie hebt opgehaald, start u de Jupyter-server:

```sh
docker run --rm -p 8989:8989 --name smartnoise-run opendp/smartnoise:privacy
```

Hiermee start u een Jupyter-server op `8989` de poort op uw `localhost` , met een wacht woord `pass@word99` . Als u de bovenstaande opdracht regel hebt gebruikt om de container met de naam te starten `smartnoise-privacy` , kunt u een bash-terminal openen in de Jupyter-server door uit te voeren:

```sh
docker exec -it smartnoise-run bash
```

Het docker-exemplaar wist alle statussen bij het afsluiten, zodat alle notitie blokken die u in het actieve exemplaar maakt, verloren gaan. U kunt dit oplossen door een lokale map te koppelen aan de container wanneer u deze start:

```sh
docker run --rm -p 8989:8989 --name smartnoise-run --mount type=bind,source=/Users/your_name/my-notebooks,target=/home/privacy/my-notebooks opendp/smartnoise:privacy
```

Alle notitie blokken die u onder de map *My-notebooks* maakt, worden opgeslagen in het lokale bestands systeem.

## <a name="perform-data-analysis"></a>Gegevens analyse uitvoeren

U moet een gegevens bron, een statistiek en enkele privacy-para meters kiezen om het niveau van privacy-beveiliging op te geven als u een differentiatie-versie wilt voorbereiden.  

In dit voor beeld wordt verwezen naar de open bare gegevens van Californië (PUMS) die geanonimiseerd records van burgerlijke demografische gegevens vertegenwoordigen:

```python
import os
import sys
import numpy as np
import opendp.smartnoise.core as sn

data_path = os.path.join('.', 'data', 'PUMS_california_demographics_1000', 'data.csv')
var_names = ["age", "sex", "educ", "race", "income", "married", "pid"]
```

In dit voor beeld berekenen we het gemiddelde en de variantie van de leeftijd.  We gebruiken een totaal `epsilon` van 1,0 (epsilon is onze privacy-para meter, verspreiden onze privacy-begroting over de twee hoeveel heden die we willen berekenen. Meer informatie over [metrische gegevens](concept-differential-privacy.md#differential-privacy-metrics)over de privacy.

```python
with sn.Analysis() as analysis:
    # load data
    data = sn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = sn.dp_mean(data = sn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
    # get variance of age
    age_var = sn.dp_variance(data = sn.cast(data['age'], type="FLOAT"),
                             privacy_usage = {'epsilon': .35},
                             data_lower = 0.,
                             data_upper = 100.,
                             data_n = 1000
                            )
analysis.release()

print("DP mean of age: {0}".format(age_mean.value))
print("DP variance of age: {0}".format(age_var.value))
print("Privacy usage: {0}".format(analysis.privacy_usage))
```

De resultaten zien er ongeveer als volgt uit:

```text
DP mean of age: 44.55598845931517
DP variance of age: 231.79044646429134
Privacy usage: approximate {
  epsilon: 1.0
}
```

Er zijn enkele belang rijke aandachtspunten met betrekking tot dit voor beeld.  Eerst vertegenwoordigt het `Analysis` object een grafiek voor gegevens verwerking.  In dit voor beeld worden het gemiddelde en de afwijking berekend op basis van hetzelfde bron knooppunt.  U kunt echter complexere expressies toevoegen die invoer op een wille keurige manier combi neren met uitvoer.

De analyse grafiek bevat `data_upper` en `data_lower` meta gegevens, waarbij de onderste en de bovengrens voor leeftijden worden opgegeven.  Deze waarden worden gebruikt om het geluid nauw keurig te kalibreren om te zorgen voor differentiële privacy.  Deze waarden worden ook gebruikt bij het verwerken van uitschieters of ontbrekende waarden.

Ten slotte houdt de analyse grafiek het totale aantal uitgegeven privacy-budgets bij.

U kunt de bibliotheek gebruiken voor het opstellen van complexere analyse grafieken met verschillende mechanismen, statistieken en hulpprogramma functies:

| Statistieken    | Mechanismen | Hulpprogramma's  |
| ------------- |------------|------------|
| Count         | Gaussiaans   | Omgezet       |
| Histogram     | Geometrisch  | MSS   |
| Gemiddeld          | Laplace    | Digitaliseren   |
| Kwantielen     |            | Filter     |
| Sum           |            | Toerekening |
| Variantie/covariantie |      | Transformeren  |

Raadpleeg de [notebook voor gegevens analyse](https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/basic_data_analysis.ipynb) voor meer informatie.

## <a name="approximate-utility-of-differentially-private-releases"></a>Het hulp programma benadert u met verschillen persoonlijke releases

Omdat differentiële privacy werkt door het kalibreren van lawaai, kan het hulp programma van releases variëren afhankelijk van het privacy-risico.  Over het algemeen is het geluid dat nodig is voor het beveiligen van elke persoon te verwaarlozen omdat de voorbeeld grootten groter worden.  Analisten kunnen de nauw keurige informatie voor een release controleren om te bepalen hoe nuttig de release is:

```python
with sn.Analysis() as analysis:
    # load data
    data = sn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = sn.dp_mean(data = sn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
analysis.release()

print("Age accuracy is: {0}".format(age_mean.get_accuracy(0.05)))
```

Het resultaat van die bewerking moet er ongeveer als volgt uitzien:

```text
Age accuracy is: 0.2995732273553991
```

In dit voor beeld wordt het gemiddelde berekend als hierboven en wordt de `get_accuracy` functie gebruikt om de nauw keurigheid van 0,05 op te vragen `alpha` . Een `alpha` van de 0,05 vertegenwoordigt een interval van 95%, in die uitgebrachte waarde binnen de gerapporteerde nauw keurigheid van meer dan 95% van de tijd.  In dit voor beeld is de gerapporteerde nauw keurigheid 0,3, wat betekent dat de uitgebrachte waarde binnen een interval van de breedte 0,6 ongeveer 95% van de tijd valt.  Het is niet juist om deze waarde als een fout balk te beschouwen, omdat de uitgebrachte waarde buiten het gerapporteerde nauwkeurigheids bereik valt op basis van het opgegeven aantal `alpha` , en waarden buiten het bereik mogelijk in een van beide richtingen.

Analisten kunnen een query uitvoeren `get_accuracy` op verschillende waarden van `alpha` om smaller of breder betrouwbaarheids intervallen te verkrijgen, zonder dat hiervoor extra privacy-kosten in rekening worden gebracht.

## <a name="generate-a-histogram"></a>Een histogram genereren

De ingebouwde `dp_histogram` functie maakt verschillen in persoonlijke histogrammen voor een van de volgende gegevens typen:

- Een doorlopende variabele, waarbij de reeks getallen moet worden verdeeld in de opslag locaties
- Een Booleaanse of dichotomous-variabele die alleen twee waarden kan hebben
- Een categorische-variabele, waarbij verschillende categorieën worden opgesomd als teken reeksen

Hier volgt een voor beeld van een `Analysis` opslag locatie opgeven voor een continu variabel histogram:

```python
income_edges = list(range(0, 100000, 10000))

with sn.Analysis() as analysis:
    data = sn.Dataset(path = data_path, column_names = var_names)

    income_histogram = sn.dp_histogram(
            sn.cast(data['income'], type='int', lower=0, upper=100),
            edges = income_edges,
            upper = 1000,
            null_value = 150,
            privacy_usage = {'epsilon': 0.5}
        )
```

Omdat de individuen niet-aaneengesloten worden gepartitioneerd tussen de histogram bakken, worden de privacykosten slechts eenmaal per histogram berekend, zelfs als het histogram veel opslag locaties bevat.

Zie het [notitie blok met histogrammen](https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/histograms.ipynb)voor meer informatie over histogrammen.

## <a name="generate-a-covariance-matrix"></a>Een covariantie matrix genereren

SmartNoise biedt drie verschillende functies met de `dp_covariance` functie:

- Covariantie tussen twee vectoren
- Covariantie matrix van een matrix
- Kruis variantie matrix van een paar matrices

Hier volgt een voor beeld van het berekenen van een scalaire covariantie:

```python
with sn.Analysis() as analysis:
    wn_data = sn.Dataset(path = data_path, column_names = var_names)

    age_income_cov_scalar = sn.dp_covariance(
      left = sn.cast(wn_data['age'], 
      type = "FLOAT"), 
      right = sn.cast(wn_data['income'], 
      type = "FLOAT"), 
      privacy_usage = {'epsilon': 1.0},
      left_lower = 0., 
      left_upper = 100., 
      left_n = 1000, 
      right_lower = 0., 
      right_upper = 500_000.,
      right_n = 1000)
```

Zie voor meer informatie de [covariantie notebook](
https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/covariance.ipynb)

## <a name="next-steps"></a>Volgende stappen

- Verken [SmartNoise-voorbeeld notitieblokken](https://github.com/opendifferentialprivacy/smartnoise-samples/tree/master/analysis).