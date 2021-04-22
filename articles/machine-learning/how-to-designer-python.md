---
title: Python-script uitvoeren in de ontwerpfunctie
titleSuffix: Azure Machine Learning
description: Leer hoe u het Python-scriptmodel uitvoeren in Azure Machine Learning designer kunt gebruiken om aangepaste bewerkingen uit te voeren die zijn geschreven in Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: likebupt
ms.author: keli19
ms.date: 09/09/2020
ms.topic: how-to
ms.custom: designer, devx-track-python
ms.openlocfilehash: ffb46fcc4576e4b4daaf3be02625e9f9cf00a461
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107885232"
---
# <a name="run-python-code-in-azure-machine-learning-designer"></a>Python-code uitvoeren in Azure Machine Learning designer

In dit artikel leert u hoe u de module [Python-script](algorithm-module-reference/execute-python-script.md) uitvoeren kunt gebruiken om aangepaste logica toe te voegen aan Azure Machine Learning designer. In de volgende stappen gebruikt u de Pandas-bibliotheek om eenvoudige feature engineering.

U kunt de ingebouwde code-editor gebruiken om snel eenvoudige Python-logica toe te voegen. Als u complexere code wilt toevoegen of aanvullende Python-bibliotheken wilt uploaden, moet u de zip-bestandsmethode gebruiken.

De standaarduitvoeringsomgeving maakt gebruik van de Anacondas-distributie van Python. Zie de referentiepagina [Python-scriptmodule](algorithm-module-reference/execute-python-script.md) uitvoeren voor een volledige lijst met vooraf geïnstalleerde pakketten.

![Python-invoerkaart uitvoeren](media/how-to-designer-python/execute-python-map.png)

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="execute-python-written-in-the-designer"></a>Python uitvoeren die is geschreven in de ontwerpfunctie

### <a name="add-the-execute-python-script-module"></a>De module Python-script uitvoeren toevoegen

1. Zoek de **module Python-script uitvoeren** in het ontwerppalet. U vindt deze in de **sectie Python-taal.**

1. Sleep de module en zet deze neer op het pijplijn-canvas.

### <a name="connect-input-datasets"></a>Verbinding maken met invoersets

In dit artikel wordt gebruikgemaakt van de voorbeeldgegevensset **Automobile price data (Raw).** 

1. Sleep uw gegevensset en zet deze neer op het pijplijn-canvas.

1. Verbind de uitvoerpoort van de gegevensset met de invoerpoort linksboven van de module **Python-script** uitvoeren. De ontwerpfunctie toont de invoer als een parameter voor het invoerpuntscript.
    
    De juiste invoerpoort is gereserveerd voor ingepakte Python-bibliotheken.

    ![Gegevenssets verbinden](media/how-to-designer-python/connect-dataset.png)
        

1. Noteer welke invoerpoort u gebruikt. De ontwerpfunctie wijst de linkerinvoerpoort toe aan de variabele `dataset1` en de middelste invoerpoort aan `dataset2` . 

Invoermodules zijn optioneel omdat u gegevens rechtstreeks in de module **Python-script uitvoeren kunt genereren of** importeren.

### <a name="write-your-python-code"></a>Uw Python-code schrijven

De ontwerpfunctie biedt een eerste invoerpuntscript dat u kunt bewerken en uw eigen Python-code kunt invoeren. 

In dit voorbeeld gebruikt u Pandas om twee kolommen te combineren in de gegevensset automobile, **Price** en **Horsepower,** om een nieuwe kolom te maken, **Dollars per paardenkracht.** Deze kolom geeft aan hoeveel u voor elk pk betaalt. Dit kan een handige functie zijn om te bepalen of een auto een goede deal voor het geld is. 

1. Selecteer de module **Python-script** uitvoeren.

1. Selecteer in het deelvenster rechts van het canvas het tekstvak **Python-script.**

1. Kopieer en plak de volgende code in het tekstvak.

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    Uw pijplijn moet er als volgt uitzien:
    
    ![Python-pijplijn uitvoeren](media/how-to-designer-python/execute-python-pipeline.png)

    Het invoerpuntscript moet de functie `azureml_main` bevatten. Er zijn twee functieparameters die zijn toe te schrijven aan de twee invoerpoorten voor de module **Python-script** uitvoeren.

    De retourwaarde moet een Pandas-dataframe zijn. U kunt maximaal twee gegevensframes retourneren als module-uitvoer.
    
1. Verzend de pijplijn.

U hebt nu een gegevensset met de nieuwe functie **Dollars/HP,** die nuttig kan zijn bij het trainen van een auto-aanbeveling. Dit is een voorbeeld van functieextractie en dimensionaliteitsvermindering. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [importeren van uw eigen gegevens](how-to-designer-import-data.md) in Azure Machine Learning designer.
