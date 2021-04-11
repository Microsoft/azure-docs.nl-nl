---
title: Pijplijn parameters in de ontwerp functie gebruiken om veelzijdige pijp lijnen te bouwen
titleSuffix: Azure Machine Learning
description: Pijplijn parameters gebruiken in Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.date: 03/19/2021
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: 09eabffb0e01ee6c5ea6b541378773a7d60397a3
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080270"
---
# <a name="use-pipeline-parameters-in-the-designer-to-build-versatile-pipelines"></a>Pijplijn parameters in de ontwerp functie gebruiken om veelzijdige pijp lijnen te bouwen

Gebruik pijplijn parameters om flexibele pijp lijnen te bouwen in de ontwerp functie. Met pijplijn parameters kunt u tijdens runtime dynamische waarden instellen om pijplijn logica te integreren en activa opnieuw te gebruiken.

Pijplijn parameters zijn vooral nuttig bij het opnieuw indienen van een pijplijn uitvoering, het opnieuw [trainen van modellen](how-to-retrain-designer.md)of het [uitvoeren van batch voorspellingen](how-to-run-batch-predictions-designer.md).

In dit artikel leert u hoe u het volgende kunt doen:

> [!div class="checklist"]
> * Pijplijn parameters maken
> * Pijplijn parameters verwijderen en beheren
> * De pijp lijn wordt geactiveerd tijdens het aanpassen van pijplijn parameters

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte. Raadpleeg [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md).

* Voltooi de ontwerp [zelf studie](tutorial-designer-automobile-price-train-score.md)voor een begeleide Inleiding tot de ontwerp functie. 

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-pipeline-parameter"></a>Pijplijn parameter maken

Er zijn drie manieren om een pijplijn parameter te maken in de ontwerp functie:
- Maak een pijplijn parameter in het deel venster instellingen en koppel deze aan een module.
- Niveau een module parameter verhogen naar een pijplijn parameter.
- Een gegevensset promo veren naar een pijplijn parameter

> [!NOTE]
> Pijplijn parameters bieden alleen ondersteuning voor basis gegevens typen als `int` , `float` en `string` .

### <a name="option-1-create-a-pipeline-parameter-in-the-settings-panel"></a>Optie 1: een pijplijn parameter maken in het deel venster instellingen

In deze sectie maakt u een pijplijn parameter in het deel venster instellingen.

In dit voor beeld maakt u een pijplijn parameter die definieert hoe een pijp lijn de ontbrekende gegevens opvult met de module **clean Missing Data** .

1. Selecteer naast de naam van uw pijp lijn concept het **tandwiel pictogram** om het deel venster **instellingen** te openen.

1. Selecteer het pictogram in de sectie **pijplijn parameters** **+** .

1.  Voer een naam in voor de para meter en een standaard waarde. 

    Voer bijvoorbeeld `replace-missing-value` in als parameter naam en `0` als standaard waarde.

   ![Scherm afbeelding die laat zien hoe u een pijplijn parameter maakt](media/how-to-use-pipeline-parameter/create-pipeline-parameter.png)


Nadat u een pijplijn parameter hebt gemaakt, moet u [deze koppelen aan de para meter van de module](#attach-module-parameter-to-pipeline-parameter) die u dynamisch wilt instellen.

### <a name="option-2-promote-a-module-parameter"></a>Optie 2: een module parameter verhogen

De eenvoudigste manier om een pijplijn parameter voor een module waarde te maken, is door een module parameter te promo veren. Gebruik de volgende stappen om een module parameter te promo veren naar een pijplijn parameter:

1. Selecteer de module waaraan u een pijplijn parameter wilt koppelen.
1. Mouseover in het deel venster module Details de para meter die u wilt opgeven.
1. Selecteer de weglatings tekens (**...**) die worden weer gegeven.
1. Selecteer **toevoegen aan pijplijn parameter**.

    ![Scherm afbeelding die laat zien hoe u de module parameter kunt promo veren naar pipeline parameter1](media/how-to-use-pipeline-parameter/promote-module-para-to-pipeline-para.png)

1. Voer een parameter naam en standaard waarde in.
1. Selecteer **Opslaan**

U kunt nu nieuwe waarden voor deze para meter opgeven wanneer u deze pijp lijn verzendt.

### <a name="option-3-promote-a-dataset-to-a-pipeline-parameter"></a>Optie 3: een gegevensset promo veren naar een pijplijn parameter

Als u de pijp lijn met variabele gegevens sets wilt verzenden, moet u uw gegevensset promo veren naar een pijplijn parameter:

1. Selecteer de gegevensset die u wilt omzetten in een pijplijn parameter.

1. In het deel venster Details van gegevensset, controleert u de **para meter instellen als pijplijn**.

   ![Scherm afbeelding die laat zien hoe gegevensset instellen als pijplijn parameter](media/how-to-use-pipeline-parameter/set-dataset-as-pipeline-parameter.png)

U kunt nu een andere gegevensset opgeven met behulp van de pijplijn parameter de volgende keer dat u de pijp lijn uitvoert.

## <a name="attach-module-parameter-to-pipeline-parameter"></a>Module parameter aan pijplijn parameter koppelen 

In deze sectie leert u hoe u module parameter kunt koppelen aan de pijplijn parameter.

U kunt dezelfde module parameters van dubbele modules aan dezelfde pijplijn parameter koppelen als u de waarde in één keer wilt wijzigen wanneer de uitvoering van de pijp lijn wordt geactiveerd.

In het volgende voor beeld is gedupliceerde **gegevens module clean ontbreekt** . Voor elke **schone ontbrekende gegevens** module koppelt u een **vervangings waarde** aan de pijplijn parameter **replace-ontbreekt-waarde**:

1. Selecteer de module **Clean Missing Data**.

1. Stel in het deel venster module detail rechts van het canvas de **Opschonings modus** in op ' aangepaste vervangings waarde '.

1. Mouseover het veld **vervangings waarde** in.

1. Selecteer de weglatings tekens (**...**) die worden weer gegeven.

1. Selecteer de pijplijn parameter `replace-missing-value` .

   ![Scherm afbeelding die laat zien hoe u een pijplijn parameter kunt koppelen](media/how-to-use-pipeline-parameter/attach-replace-value-to-pipeline-parameter.png)

U hebt het veld **vervangings waarde** gekoppeld aan de pijplijn parameter. De **vervangings waarde** in de modules kan niet worden uitgevoerd.

 ![Scherm afbeelding met een niet-actie die niet kan worden uitgevoerd na het koppelen aan de pijplijn parameter](media/how-to-use-pipeline-parameter/non-actionable-module-parameter.png)


## <a name="update-and-delete-pipeline-parameters"></a>Pijplijn parameters bijwerken en verwijderen

In deze sectie leert u hoe u pijplijn parameters kunt bijwerken en verwijderen.

### <a name="update-pipeline-parameters"></a>Pijplijn parameters bijwerken

Gebruik de volgende stappen om een module pijplijn parameter bij te werken:

1. Selecteer boven aan het canvas het tandwiel pictogram.
1. In de sectie **pijplijn parameters** kunt u de naam en de standaard waarde voor alle pijplijn parameters weer geven en bijwerken.

### <a name="delete-a-dataset-pipeline-parameter"></a>Een gegevensset pijplijn parameter verwijderen

Voer de volgende stappen uit om een DataSet pijplijn-para meter los te koppelen:

1. Selecteer de module gegevensset.
1. Schakel de optie **set als pijplijn parameter** uit.


### <a name="delete-module-pipeline-parameters"></a>Module pijplijn parameters verwijderen

Gebruik de volgende stappen om een module pijplijn parameter te verwijderen:

1. Selecteer boven aan het canvas het tandwiel pictogram.

1. Selecteer de weglatings tekens (**...**) naast de pijplijn parameter.

    In deze weer gave ziet u aan welke modules de pijplijn parameter is gekoppeld. Als u een pijplijn parameter wilt verwijderen, moet u deze eerst loskoppelen van de module parameters.

    ![Scherm opname van de huidige pijplijn parameter die wordt toegepast op een module](media/how-to-use-pipeline-parameter/current-pipeline-parameter.png)

1. Selecteer op het canvas een module waaraan de pijplijn parameter nog is gekoppeld.
1. Zoek in het deel venster module-eigenschappen aan de rechter kant het veld waaraan de pijplijn parameter is gekoppeld.
1. Mouseover het gekoppelde veld. Selecteer vervolgens de weglatings tekens (**...**) die worden weer gegeven.
1. **Loskoppelen van pijplijn parameter** selecteren

    ![Scherm opname van het loskoppelen van pijplijn parameters](media/how-to-use-pipeline-parameter/detach-from-pipeline-parameter.png)

1. Herhaal de vorige stappen totdat u de para meter pijplijn hebt losgekoppeld van alle velden.
1. Selecteer de weglatings tekens (**...**) naast de pijplijn parameter.
1. Selecteer **para meter verwijderen** om de pijplijn parameter te verwijderen.

    ![Scherm opname van het verwijderen van pijplijn parameters](media/how-to-use-pipeline-parameter/delete-pipeline-parameter.png)

## <a name="trigger-a-pipeline-run-with-pipeline-parameters"></a>Een pijplijn uitvoering activeren met pijplijn parameters 

In deze sectie leert u hoe u een pijplijn run kunt verzenden tijdens het instellen van pijplijn parameters.

### <a name="resubmit-a-pipeline-run"></a>Een pijplijn uitvoering opnieuw verzenden

Nadat u een pijp lijn met pijplijn parameters hebt ingediend, kunt u een pijplijn uitvoering opnieuw verzenden met verschillende para meters:

1. Ga naar de detail pagina van de pijp lijn. In het venster **pijplijn uitvoering overzicht** kunt u de huidige pijplijn parameters en-waarden controleren.

1. Selecteer **opnieuw verzenden**.
1. Geef in de **pijplijn installatie** de nieuwe pijplijn parameters op. 

![Scherm opname van de weer gave van de pijp lijn opnieuw verzenden met pijplijn parameters](media/how-to-use-pipeline-parameter/resubmit-pipeline-run.png)

### <a name="use-published-pipelines"></a>Gepubliceerde pijp lijnen gebruiken

U kunt ook een pijp lijn publiceren om de pijplijn parameters te gebruiken. Een **gepubliceerde pijp lijn** is een pijp lijn die is geïmplementeerd op een reken resource en die client toepassingen kunnen aanroepen via een rest-eind punt.

Gepubliceerde eind punten zijn vooral handig voor het opnieuw trainen en batch Voorspellings scenario's. Zie [modellen in de ontwerp functie opnieuw trainen](how-to-retrain-designer.md) of [batch voorspellingen uitvoeren in de ontwerp functie](how-to-run-batch-predictions-designer.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u pijplijn parameters maakt in de ontwerp functie. Bekijk vervolgens hoe u pijplijn parameters kunt gebruiken voor het opnieuw [trainen van modellen](how-to-retrain-designer.md) of het uitvoeren van [batch voorspellingen](how-to-run-batch-predictions-designer.md).

U kunt ook meer informatie over [het gebruik van pijp lijnen met de SDK](how-to-deploy-pipelines.md).
