---
title: Pijplijnparameters in de ontwerpfunctie gebruiken om veelzijdige pijplijnen te bouwen
titleSuffix: Azure Machine Learning
description: Pijplijnparameters gebruiken in Azure Machine Learning designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.date: 04/09/2020
ms.topic: how-to
ms.custom: designer
ms.openlocfilehash: ba5af77022c3f230fdaf77d115a1c1a4b2151c3e
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107888148"
---
# <a name="use-pipeline-parameters-in-the-designer-to-build-versatile-pipelines"></a>Pijplijnparameters in de ontwerpfunctie gebruiken om veelzijdige pijplijnen te bouwen

Gebruik pijplijnparameters om flexibele pijplijnen te bouwen in de ontwerpfunctie. Met pijplijnparameters kunt u tijdens runtime dynamisch waarden instellen om pijplijnlogica in te kapselen en assets opnieuw te gebruiken.

Pijplijnparameters zijn vooral nuttig bij het opnieuw inzenden van een pijplijn, het [opnieuw trainen](how-to-retrain-designer.md)van modellen of het uitvoeren [van batchvoorspellingen.](how-to-run-batch-predictions-designer.md)

In dit artikel leert u het volgende te doen:

> [!div class="checklist"]
> * Pijplijnparameters maken
> * Pijplijnparameters verwijderen en beheren
> * Pijplijn-runs activeren tijdens het aanpassen van pijplijnparameters

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte. Raadpleeg [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md).

* Voltooi de zelfstudie voor de ontwerpfunctie voor een begeleide [inleiding tot de ontwerpfunctie.](tutorial-designer-automobile-price-train-score.md) 

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-pipeline-parameter"></a>Pijplijnparameter maken

Er zijn drie manieren om een pijplijnparameter te maken in de ontwerpfunctie:
- Maak een pijplijnparameter in het instellingenvenster en bind deze aan een module.
- Promover een moduleparameter naar een pijplijnparameter.
- Een gegevensset promoveren naar een pijplijnparameter

> [!NOTE]
> Pijplijnparameters ondersteunen alleen basisgegevenstypen `int` `float` zoals , en `string` .

### <a name="option-1-create-a-pipeline-parameter-in-the-settings-panel"></a>Optie 1: een pijplijnparameter maken in het instellingenvenster

In deze sectie maakt u een pijplijnparameter in het instellingenvenster.

In dit voorbeeld maakt u een pijplijnparameter die definieert hoe een pijplijn ontbrekende gegevens opvult met behulp van de module **Ontbrekende gegevens** ops schonen.

1. Selecteer naast de naam van uw pijplijnontwerp het tandwielpictogram **om** het deelvenster **Instellingen te** openen.

1. Selecteer het **pictogram in** de sectie Pijplijnparameters. **+**

1.  Voer een naam in voor de parameter en een standaardwaarde. 

    Voer bijvoorbeeld in `replace-missing-value` als parameternaam en `0` als standaardwaarde.

   ![Schermopname die laat zien hoe u een pijplijnparameter maakt](media/how-to-use-pipeline-parameter/create-pipeline-parameter.png)


Nadat u een pijplijnparameter hebt gemaakt, moet u deze koppelen [aan de moduleparameter](#attach-module-parameter-to-pipeline-parameter) die u dynamisch wilt instellen.

### <a name="option-2-promote-a-module-parameter"></a>Optie 2: Een moduleparameter promoveren

De eenvoudigste manier om een pijplijnparameter voor een modulewaarde te maken, is door het promoveren van een moduleparameter. Gebruik de volgende stappen om een moduleparameter te promoveren naar een pijplijnparameter:

1. Selecteer de module waar u een pijplijnparameter aan wilt koppelen.
1. Ga in het detailvenster van de module met de muis over de parameter die u wilt opgeven.
1. Selecteer het beletselletsel **(...**) dat wordt weergegeven.
1. Selecteer **Toevoegen aan pijplijnparameter**.

    ![Schermopname die laat zien hoe u de moduleparameter kunt promoveren naar pijplijnparameter1](media/how-to-use-pipeline-parameter/promote-module-para-to-pipeline-para.png)

1. Voer een parameternaam en standaardwaarde in.
1. Selecteer **Opslaan**

U kunt nu nieuwe waarden voor deze parameter opgeven wanneer u deze pijplijn indient.

### <a name="option-3-promote-a-dataset-to-a-pipeline-parameter"></a>Optie 3: Een gegevensset promoveren naar een pijplijnparameter

Als u uw pijplijn wilt verzenden met variabele gegevenssets, moet u uw gegevensset promoveren naar een pijplijnparameter:

1. Selecteer de gegevensset die u wilt veranderen in een pijplijnparameter.

1. In het detailvenster van de gegevensset controleert u **Instellen als pijplijnparameter.**

   ![Schermopname die laat zien hoe u de gegevensset in kunt stellen als pijplijnparameter](media/how-to-use-pipeline-parameter/set-dataset-as-pipeline-parameter.png)

U kunt nu een andere gegevensset opgeven met behulp van de pijplijnparameter de volgende keer dat u de pijplijn gaat uitvoeren.

## <a name="attach-and-detach-module-parameter-to-pipeline-parameter"></a>Moduleparameter koppelen en loskoppelen van pijplijnparameter 

In deze sectie leert u hoe u de moduleparameter koppelt aan de pijplijnparameter.

### <a name="attach-module-parameter-to-pipeline-parameter"></a>Moduleparameter koppelen aan pijplijnparameter

U kunt dezelfde moduleparameters van gedupliceerde modules koppelen aan dezelfde pijplijnparameter als u de waarde in één keer wilt wijzigen bij het activeren van de pijplijn-run.

In het volgende voorbeeld is de module **Clean Missing Data gedupliceerd.** Voeg voor elke module **Clean Missing Data** de waarde Replacement **toe** aan de **pijplijnparameter replace-missing-value:**

1. Selecteer de module **Clean Missing Data**.

1. Stel in het detailvenster van de module rechts van het canvas de opschoonmodus in **op** Aangepaste vervangingswaarde.

1. Muisaanwijzer over **het veld Vervangingswaarde.**

1. Selecteer het beletselletselletsel **(...**) dat wordt weergegeven.

1. Selecteer de pijplijnparameter `replace-missing-value` .

   ![Schermopname die laat zien hoe u een pijplijnparameter koppelt](media/how-to-use-pipeline-parameter/attach-replace-value-to-pipeline-parameter.png)

U hebt het veld Vervangingswaarde **gekoppeld** aan uw pijplijnparameter. 


### <a name="detach-module-parameter-to-pipeline-parameter"></a>Moduleparameter loskoppelen van pijplijnparameter

Nadat u **vervangingswaarde aan de** pijplijnparameter hebt gekoppeld, kan er geen actie worden ondernomen.

U kunt de moduleparameter loskoppelen van de pijplijnparameter door te klikken op het beletsellijn **(...**) naast de moduleparameter en Loskoppelen van **pijplijnparameter te selecteren.**

 ![Schermopname die laat zien dat er geen actie kan worden ondernomen na het koppelen aan de pijplijnparameter](media/how-to-use-pipeline-parameter/non-actionable-module-parameter.png)

## <a name="update-and-delete-pipeline-parameters"></a>Pijplijnparameters bijwerken en verwijderen

In deze sectie leert u hoe u pijplijnparameters kunt bijwerken en verwijderen.

### <a name="update-pipeline-parameters"></a>Pijplijnparameters bijwerken

Gebruik de volgende stappen om een modulepijplijnparameter bij te werken:

1. Selecteer bovenaan het canvas het tandwielpictogram.
1. In de **sectie Pijplijnparameters** kunt u de naam en standaardwaarde voor al uw pijplijnparameters weergeven en bijwerken.

### <a name="delete-a-dataset-pipeline-parameter"></a>Een pijplijnparameter voor een gegevensset verwijderen

Gebruik de volgende stappen om een pijplijnparameter voor een gegevensset te verwijderen:

1. Selecteer de gegevenssetmodule.
1. Vink de optie **Instellen als pijplijnparameter aan.**


### <a name="delete-module-pipeline-parameters"></a>Modulepijplijnparameters verwijderen

Gebruik de volgende stappen om een modulepijplijnparameter te verwijderen:

1. Selecteer bovenaan het canvas het tandwielpictogram.

1. Selecteer het beletselletsel **(...**) naast de pijplijnparameter.

    In deze weergave ziet u aan welke modules de pijplijnparameter is gekoppeld.

    ![Schermopname van de huidige pijplijnparameter die is toegepast op een module](media/how-to-use-pipeline-parameter/delete-pipeline-parameter2.png)

1. Selecteer **Parameter Verwijderen om** de pijplijnparameter te verwijderen.

    > [!NOTE]
    > Het verwijderen van een pijplijnparameter zorgt ervoor dat alle gekoppelde moduleparameters worden losgekoppeld en dat de waarde van losgekoppelde moduleparameters de huidige parameterwaarde van de pijplijn bewaarde.     

## <a name="trigger-a-pipeline-run-with-pipeline-parameters"></a>Een pijplijnrun activeren met pijplijnparameters 

In deze sectie leert u hoe u een pijplijn-run kunt verzenden tijdens het instellen van pijplijnparameters.

### <a name="resubmit-a-pipeline-run"></a>Een pijplijn opnieuw uitvoeren

Nadat u een pijplijn met pijplijnparameters hebt verzenden, kunt u een pijplijn met verschillende parameters opnieuw verzenden:

1. Ga naar de detailpagina van de pijplijn. In het **overzichtsvenster Pijplijnuit** voeren kunt u de huidige pijplijnparameters en -waarden controleren.

1. Selecteer **Opnieuw inzenden.**
1. Geef in **De pijplijn uitvoeren instellen** uw nieuwe pijplijnparameters op. 

![Schermopname van het opnieuw inzenden van een pijplijn met pijplijnparameters](media/how-to-use-pipeline-parameter/resubmit-pipeline-run.png)

### <a name="use-published-pipelines"></a>Gepubliceerde pijplijnen gebruiken

U kunt ook een pijplijn publiceren om de pijplijnparameters ervan te gebruiken. Een **gepubliceerde pijplijn** is een pijplijn die is geïmplementeerd in een rekenresource, die clienttoepassingen kunnen aanroepen via een REST-eindpunt.

Gepubliceerde eindpunten zijn vooral nuttig voor scenario's voor opnieuw trainen en batchvoorspelling. Zie Modellen opnieuw trainen [in](how-to-retrain-designer.md) de ontwerpfunctie of [Batchvoorspellingen uitvoeren in de ontwerpfunctie voor meer informatie.](how-to-run-batch-predictions-designer.md)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u pijplijnparameters maakt in de ontwerpfunctie. Bekijk vervolgens hoe u pijplijnparameters kunt gebruiken om modellen [opnieuw te trainen](how-to-retrain-designer.md) of [batchvoorspellingen uit te voeren.](how-to-run-batch-predictions-designer.md)

U kunt ook leren hoe u [pijplijnen programmatisch gebruikt met de SDK](how-to-deploy-pipelines.md).
