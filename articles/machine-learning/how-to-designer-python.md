---
title: Python-script uitvoeren in de ontwerp functie
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van het script model uitvoeren python in Azure Machine Learning Designer om aangepaste bewerkingen uit te voeren die zijn geschreven in python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: likebupt
ms.author: keli19
ms.date: 09/09/2020
ms.topic: conceptual
ms.custom: how-to, designer, devx-track-python
ms.openlocfilehash: dcc28d98efbc82079586de8cfbecd35effc93d6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94556230"
---
# <a name="run-python-code-in-azure-machine-learning-designer"></a>Python-code uitvoeren in Azure Machine Learning Designer

In dit artikel leert u hoe u de script module voor het [uitvoeren van python](algorithm-module-reference/execute-python-script.md) kunt gebruiken om aangepaste logica aan Azure machine learning Designer toe te voegen. In de volgende procedures gebruikt u de bibliotheek van Pandas om eenvoudige functie techniek uit te voeren.

U kunt de ingebouwde code-editor gebruiken om snel een eenvoudige python-logica toe te voegen. Als u complexere code wilt toevoegen of extra python-bibliotheken wilt uploaden, moet u de methode zip-bestand gebruiken.

De standaard uitvoerings omgeving maakt gebruik van de Anacondas-distributie van python. Voor een volledige lijst met vooraf geïnstalleerde pakketten gaat u naar de pagina overzicht van [python-script module uitvoeren](algorithm-module-reference/execute-python-script.md) .

![Python-invoer toewijzing uitvoeren](media/how-to-designer-python/execute-python-map.png)

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="execute-python-written-in-the-designer"></a>Python die is geschreven in de ontwerp functie uitvoeren

### <a name="add-the-execute-python-script-module"></a>De script module voor het uitvoeren van python toevoegen

1. Zoek de **script module python uitvoeren** in het palet ontwerp. U kunt dit vinden in de sectie **python-taal** .

1. Sleep de module en zet deze neer op het canvas op de pijp lijn.

### <a name="connect-input-datasets"></a>Invoer gegevens sets verbinden

In dit artikel wordt gebruikgemaakt van de voor beeld-gegevensset, **Auto Mobile price data (RAW)**. 

1. Sleep uw gegevensset en zet deze neer op het pijp lijn papier.

1. Verbind de uitvoer poort van de gegevensset met de linksboven invoer poort van de script module voor het **uitvoeren van python** . De ontwerp functie biedt de invoer als een para meter voor het toegangs punt script.
    
    De juiste invoer poort is gereserveerd voor gezipte python-bibliotheken.

    ![Gegevens sets verbinden](media/how-to-designer-python/connect-dataset.png)
        

1. Noteer de invoer poort die u gebruikt. De Designer wijst de linker invoer poort toe aan de variabele `dataset1` en de middelste invoer poort `dataset2` . 

Invoer modules zijn optioneel, omdat u gegevens rechtstreeks kunt genereren of importeren in de **script module python uitvoeren** .

### <a name="write-your-python-code"></a>Uw Python-code schrijven

De Designer biedt een eerste toegangs punt script waarmee u uw eigen python-code kunt bewerken en invoeren. 

In dit voor beeld gebruikt u Pandas om twee kolommen te combi neren die worden gevonden in de automobiele gegevensset, de **prijs** en de **paarden kracht**, om een nieuwe kolom te maken, **dollars per paarden kracht**. Deze kolom geeft aan hoeveel u betaalt voor elk paarden kracht, wat nuttig kan zijn om te bepalen of een auto een goede deal is voor het geld. 

1. Selecteer de **script** module voor het uitvoeren van python.

1. Selecteer in het deel venster dat rechts van het canvas wordt weer gegeven het **python script** -tekstvak.

1. Kopieer en plak de volgende code in het tekstvak.

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    De pijp lijn moet de volgende afbeelding bekijken:
    
    ![Python-pijp lijn uitvoeren](media/how-to-designer-python/execute-python-pipeline.png)

    Het ingangs punt script moet de functie bevatten `azureml_main` . Er zijn twee functie parameters die worden toegewezen aan de twee invoer poorten voor de script module voor het **uitvoeren van python** .

    De geretourneerde waarde moet een Panda data frame zijn. U kunt Maxi maal twee dataframes retour neren als module-uitvoer.
    
1. Verzend de pijp lijn.

Nu hebt u een gegevensset met de nieuwe functie **dollars/HP**, wat nuttig kan zijn bij het trainen van een auto aanbeveling. Dit is een voor beeld van het uitpakken en dalen van de functie. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [importeren van uw eigen gegevens](how-to-designer-import-data.md) in azure machine learning Designer.
