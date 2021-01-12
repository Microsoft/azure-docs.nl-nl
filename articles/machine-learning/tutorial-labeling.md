---
title: 'Zelfstudie: Een labelproject maken voor de classificatie van afbeeldingen'
titleSuffix: Azure Machine Learning
description: Meer informatie over het beheren van het proces voor het labelen van afbeeldingen, zodat deze kunnen worden gebruikt in afbeeldingsclassificatiemodellen in meerdere klassen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sgilley
author: sdgilley
ms.reviewer: ranku
ms.date: 04/09/2020
ms.custom: data4ml
ms.openlocfilehash: 238801171d19191bcac505f0c95395320f96d75c
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882463"
---
# <a name="tutorial-create-a-labeling-project-for-multi-class-image-classification"></a>Zelfstudie: Een labelproject maken voor een afbeeldingsclassificatie met meerdere klassen 


In deze zelfstudie leert u hoe u het proces van afbeeldingen labelen (ook wel Tags toevoegen genoemd) kunt beheren als gegevens voor het compileren van machine learning-modellen. Gegevens labelen in Azure Machine Learning bevindt zich in de openbare preview.

Als u een machine learning model wilt trainen om afbeeldingen te classificeren, hebt u honderden of zelfs duizenden afbeeldingen met een correct label nodig.  Azure Machine Learning helpt u bij het beheren van de voortgang van uw persoonlijke team van domeinexperts terwijl zij uw gegevens labelen.
 
In deze zelfstudie gebruikt u afbeeldingen van katten en honden.  Omdat elke afbeelding een kat of hond is, is dit een labelproject met *meerdere klassen*. U leert het volgende:

> [!div class="checklist"]
>
> * Maak een Azure-opslagaccount en upload afbeeldingen naar het account.
> * Een Azure Machine Learning-werkruimte maken.
> * Maak een labelproject voor een afbeelding met meerdere klassen.
> * Voorzie uw gegevens van een label.  Uzelf of uw labelers kunnen deze taak uitvoeren.
> * Voltooi het project door de gegevens te controleren en te exporteren.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://aka.ms/AMLFree) aan.

## <a name="create-a-workspace"></a>Een werkruimte maken

Een Azure Machine Learning-werkruimte is een basisblok in de cloud dat u gebruikt voor het experimenteren, trainen en implementeren van machine learning-modellen. De klasse bindt uw Azure-abonnement en resourcegroep aan een eenvoudig te verbruiken object in de service.

Er zijn verschillende manieren om een [werkruimte te maken](how-to-manage-workspace.md). In deze zelfstudie maakt u een werkruimte via Azure Portal, een webconsole om uw Azure-resources te beheren.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="start-a-labeling-project"></a>Een labelproject beginnen

Hierna beheert u een project voor het labelen van gegevens in Azure Machine Learning Studio, een geconsolideerde interface met hulpmiddelen voor machine learning waar gegevenswetenschappers, ongeacht hun vaardigheidsniveaus, scenario's kunnen uitvoeren. De Studio wordt niet ondersteund in Internet Explorer-browsers.

1. Meld u aan bij [Azure Machine Learning Studio](https://ml.azure.com).

1. Selecteer uw abonnement en de werkruimte die u hebt gecreëerd.

### <a name="create-a-datastore"></a><a name="create-datastore"></a>Een gegevensarchief maken

Azure Machine Learning gegevensarchieven worden gebruikt voor het opslaan van verbindingsgegevens, zoals uw abonnements-id en token-autorisatie. Hier gebruikt u een gegevensarchief om verbinding te maken met het opslagaccount dat de afbeeldingen bevat voor deze zelfstudie.

1. Selecteer **Gegevensarchieven** aan de linkerkant van uw werkruimte.

1. Selecteer **+ Nieuw gegevensarchief**.

1. Vul het formulier in met de volgende instellingen:

    Veld|Beschrijving 
    ---|---
    Naam van gegevensarchief | Geef een naam op voor het gegevensarchief.  Hier gebruiken we **labeling_tutorial**.
    Type gegevensarchief | Selecteer het type opslag.  Hier gebruiken we **Azure Blob Storage**, de voorkeursopslag voor afbeeldingen.
    Methode voor account selecteren | Selecteer **Handmatig invoeren**.
    URL | `https://azureopendatastorage.blob.core.windows.net/openimagescontainer`
    Verificatietype | Selecteer **SAS-token**.
    Accountsleutel | `?sv=2019-02-02&ss=bfqt&srt=sco&sp=rl&se=2025-03-25T04:51:17Z&st=2020-03-24T20:51:17Z&spr=https&sig=7D7SdkQidGT6pURQ9R4SUzWGxZ%2BHlNPCstoSRRVg8OY%3D`

1. Selecteer **Maken** om het gegevensarchief te maken.

### <a name="create-a-labeling-project"></a>Labelproject maken

Nu u toegang hebt tot de gegevens die u wilt voorzien van een label, maakt u het labelproject.

1. Selecteer **Projecten** bovenaan de pagina.

1. Selecteer **+ Project toevoegen**.

    :::image type="content" source="media/tutorial-labeling/create-project.png" alt-text="Een project maken":::

### <a name="project-details"></a>Projectgegevens

1. Gebruik de volgende invoer voor het formulier **Projectgegevens**:

    Veld|Beschrijving 
    ---|---
    Projectnaam | Geef uw project een naam.  Hier gebruiken we **tutorial-cats-n-dogs**.
    Taaktype voor labelen | Selecteer **Afbeeldingsclassificatie meerdere klassen**.  
    
    Selecteer **Volgende** om door te gaan met het maken van het project.

### <a name="select-or-create-a-dataset"></a>Een gegevensset selecteren of maken

1.   Selecteer op het formulier **Een gegevensset selecteren of maken** de tweede keuze, **Een gegevensset maken** en selecteer vervolgens de koppeling **Vanuit gegevensarchief**.

1. Gebruik de volgende invoer voor het formulier **Gegevensset maken vanuit gegevensarchief**:

    1. Voeg op het formulier **Basisgegevens** een naam toe. Hier gebruiken we **images-for-tutorial**.  Voeg desgewenst een beschrijving toe.  Selecteer vervolgens **Volgende**.
    1. Op het formulier **Gegevensarchiefselectie** selecteert u **Eerder gemaakt gegevensarchief** en klikt u op de naam van het gegevensarchief. Selecteer vervolgens **Gegevensarchief selecteren**.
    1. Controleer op de volgende pagina of het momenteel geselecteerde gegevensarchief juist is. Als dat niet het geval is, selecteert u **Eerder gemaakt gegevensarchief** en herhaalt u de vorige stap.
    1. Selecteer vervolgens op het formulier **Selectie gegevensarchief** **Bladeren** en selecteer vervolgens **MultiClass - DogsCats**.  Selecteer **Opslaan** om **/MultiClass - DogsCats** als pad te gebruiken.
    1. Selecteer **Volgende** om de details te bevestigen en vervolgens **Maken** om de gegevensset te maken.
    1. Selecteer in de lijst de cirkel naast de naam van de gegevensset, bijvoorbeeld **images-for-tutorial**.

1. Selecteer **Volgende** om door te gaan met het maken van het project.

### <a name="incremental-refresh"></a>Incrementeel vernieuwen

Als u van plan bent nieuwe installatiekopieën toe te voegen aan uw gegevensset, worden deze nieuwe installatiekopieën met incrementeel vernieuwen gevonden en toegevoegd aan uw project.  Wanneer u deze functie inschakelt, controleert het project regelmatig of er nieuwe installatiekopieën zijn.  U voegt voor deze zelfstudie geen nieuwe installatiekopieën toe aan het gegevensarchief. Zorg er daarom voor dat deze functie is uitgeschakeld.

Selecteer **Volgende** om door te gaan.

### <a name="label-classes"></a>Labelklassen

1. Typ in het formulier **Labelklassen** de naam van een label en selecteer vervolgens **+ Label toevoegen** om het volgende label te typen.  Voor dit project zijn de labels **Kat**, **Hond** en **Onzeker**.

1. Selecteer **Volgende** wanneer u alle labels hebt toegevoegd.

### <a name="labeling-instructions"></a>Instructies voor labelen

1. Op het formulier **Instructies voor het labelen** kunt u een koppeling naar een website geven die uw labelers gedetailleerde instructies biedt.  Voor deze zelfstudie laten we dit leeg.

1. U kunt ook een korte beschrijving van de taak rechtstreeks op het formulier toevoegen.  Typ **Zelfstudie over labels - katten en honden.**

1. Selecteer **Next**.

1. Schakel het selectievakje in het gedeelte **Door ML ondersteund labelen** niet in. Voor labelen met ML-ondersteuning zijn meer gegevens nodig dan in deze zelfstudie worden gebruikt.

1. Selecteer **Project maken**.

Deze pagina wordt niet automatisch vernieuwd. Vernieuw de pagina na een onderbreking handmatig om de status van het project te wijzigen in **Gemaakt**.

## <a name="start-labeling"></a>Beginnen met labelen

U hebt nu uw Azure-resources ingesteld en een project voor het labelen van gegevens geconfigureerd. Het is tijd om labels toe te voegen aan uw gegevens.

### <a name="tag-the-images"></a>De afbeeldingen taggen

In dit gedeelte van de zelfstudie schakelt u over van functie, namelijk van *Projectbeheerder* naar *Labeler*.  Iedereen met inzendertoegang tot uw werkruimte kan een labeler worden.

1. Selecteer in [Machine Learning Studio](https://ml.azure.com) **Gegevens labelen** aan de linkerkant om het project te vinden.  

1. Selecteer **Labelkoppeling** voor het project.

1. Lees de instructies en selecteer vervolgens **Taken**.

1. Selecteer een miniatuurafbeelding aan de rechterkant om het aantal afbeeldingen dat u wilt labelen in één keer weer te geven. U moet al deze afbeeldingen labelen voordat u door kunt gaan. Verander de opmaak alleen wanneer u een nieuwe pagina met niet-gelabelde gegevens hebt. Door de opmaak te veranderen, wordt het werk op de pagina dat u al hebt getagd, gewist.

1. Selecteer een of meer afbeeldingen en selecteer vervolgens een tag om toe te passen op de selectie. De tag wordt onder de afbeelding weergegeven.  Ga door met het selecteren en taggen van alle afbeeldingen op de pagina.  Als u alle weergegeven afbeeldingen tegelijk wilt selecteren, selecteert u **Alle selecteren**. Selecteer ten minste één afbeelding om te taggen.


    > [!TIP]
    > U kunt de eerste negen tags selecteren met behulp van de cijfertoetsen op uw toetsenbord.

1. Als alle afbeeldingen op de pagina zijn getagd, selecteert u **Indienen** om deze labels in te dienen.

    ![Afbeeldingen taggen](media/tutorial-labeling/catsndogs.gif)

1. Nadat u tags voor de beschikbare gegevens hebt ingediend, wordt de pagina vernieuwd met een nieuwe set afbeeldingen uit de werkwachtrij.

## <a name="complete-the-project"></a>Het project voltooien

Nu neemt u weer de rol van *projectbeheerder* aan voor het labelproject.

Als beheerder wilt u mogelijk het werk van uw labeler bekijken.  

### <a name="review-labeled-data"></a>Gelabelde gegevens beoordelen

1. Selecteer in [Machine Learning Studio](https://ml.azure.com) **Gegevens labelen** aan de linkerkant om het project te vinden.  

1. Selecteer de koppeling van de projectnaam.

1. In het dashboard ziet u de voortgang van het project.

1. Selecteer **Gegevens** bovenaan de pagina.

1. Selecteer aan de linkerkant **Gelabelde gegevens** om uw getagde afbeeldingen weer te geven.  

1. Wanneer u het niet eens bent met een label, selecteert u de afbeelding en selecteert u **Afwijzen** onderaan de pagina.  De tags worden verwijderd en de afbeelding wordt teruggeplaatst in de wachtrij met niet-gelabelde afbeeldingen.

### <a name="export-labeled-data"></a>Gelabelde gegevens exporteren

U kunt de labelgegevens voor Machine Learning-experimenten op elk gewenst moment exporteren. Gebruikers exporteren vaak meerdere keren en trainen verschillende modellen, in plaats van te wachten tot alle afbeeldingen zijn gelabeld.

Labels van afbeeldingen kunnen worden geëxporteerd in [COCO-indeling](http://cocodataset.org/#format-data) of als een Azure Machine Learning-gegevensset. Met de gegevenssetindeling kunt u het eenvoudig gebruiken voor training in Azure Machine Learning.  

1. Selecteer in [Machine Learning Studio](https://ml.azure.com) **Gegevens labelen** aan de linkerkant om het project te vinden.  

1. Selecteer de koppeling van de projectnaam.

1. Selecteer **Exporteren** en kies **Exporteren als Azure ML-gegevensset**. 

    De status van de export wordt direct onder de knop **Exporteren** weergegeven. 

1. Zodra de labels zijn geëxporteerd, selecteert u **Gegevenssets** aan de linkerkant om de resultaten weer te geven.

## <a name="clean-up-resources"></a>Resources opschonen


[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u afbeeldingen gelabeld.  Gebruik nu uw gelabelde gegevens:

> [!div class="nextstepaction"]
> [Train een machine learning-model voor het herkennen van afbeeldingen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb).
