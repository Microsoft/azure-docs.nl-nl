---
title: 'Zelfstudie: Slepen en neerzetten om het voorspellende model te maken (deel 1 van 2)'
titleSuffix: Azure Machine Learning
description: Leer hoe u een voorspellend machine learning-model kunt bouwen en implementeren met de ontwerpfunctie, zodat u het kunt gebruiken om resultaten te voorspellen in Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: f0e1ffe60069a2379f8eddab1aae74db2b4eac50
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370597"
---
# <a name="tutorial--power-bi-integration---drag-and-drop-to-create-the-predictive-model-part-1-of-2"></a>Zelfstudie:  Power BI-integratie: Slepen en neerzetten om het voorspellende model te maken (deel 1 van 2)

In het eerste deel van deze zelfstudie traint en implementeert u een voorspellend machine learning-model met behulp van de ontwerpfunctie van Azure Machine Learning, een slepen en neerzetten-gebruikersinterface met weinig code. In deel 2 gebruikt u het model om resultaten te voorspellen in Microsoft Power BI.

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Een Azure Machine Learning-rekenproces maken
> * Een Azure Machine Learning-rekencluster maken
> * Een gegevensset maken
> * Een regressiemodel trainen
> * Het model implementeren in een realtime-eindpunt voor scoren


Er zijn drie verschillende manieren om het model dat u gaat gebruiken in Power BI, te maken en te implementeren.  Dit artikel is van toepassing op Optie B: Modellen trainen en implementeren met behulp van de ontwerpfunctie.  De optie toont een ontwerpervaring met weinig code, met behulp van de ontwerpfunctie (een slepen en neerzetten-gebruikersinterface).  

Gebruik in plaats hiervan:

* [Optie A: Modellen trainen en implementeren met behulp van Notebooks](tutorial-power-bi-custom-model.md): een op code gerichte ontwerpervaring met behulp van Jupyter-notebooks die worden gehost in Azure Machine Learning Studio.
* [Optie C: Modellen trainen en implementeren met behulp van ML](tutorial-power-bi-automated-model.md): een ontwerpervaring zonder code waarmee de gegevensvoorbereiding en modeltraining volledig is geautomatiseerd.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement ([een gratis proefversie beschikbaar](https://aka.ms/AMLFree)). 
- Een Azure Machine Learning-werkruimte. Als u nog geen werkruimte hebt, volgt u [Een Azure Machine Learning-werkruimte maken](./how-to-manage-workspace.md#create-a-workspace).
- Introductiekennis van machine learning-werkstromen.


## <a name="create-compute-for-training-and-scoring"></a>Rekenproces maken voor training en score

In deze sectie maakt u een *rekenproces* dat wordt gebruikt voor machine learning-trainingsmodellen. U maakt ook een *afleidingscluster* dat wordt gebruikt om het geïmplementeerde model voor scoren in realtime te hosten.

Meld u aan bij [Azure Machine Learning Studio](https://ml.azure.com) en selecteer **Berekening** in het menu links, gevolgd door **Nieuw**:

:::image type="content" source="media/tutorial-power-bi/create-new-compute.png" alt-text="Schermopname van het maken van een rekenproces":::

Selecteer op het resulterende scherm **Rekenproces maken** een VM-grootte (selecteer voor deze zelfstudie een `Standard_D11_v2`), gevolgd door **Nieuw**. Geef op de pagina Instellingen een geldige naam op voor het rekenproces. Selecteer vervolgens **Maken**. 

>[!TIP]
> Het rekenproces kan ook worden gebruikt om notebooks te maken en uit te voeren.

U ziet nu dat de **Status** van het rekenproces **Wordt gemaakt** is. Het duurt ongeveer 4 minuten om de machine in te richten. Terwijl u wacht, selecteert u het tabblad **Afleidingscluster** op de pagina Berekening, gevolgd door **Nieuw**:

:::image type="content" source="media/tutorial-power-bi/create-cluster.png" alt-text="Schermopname van het maken van een afleidingscluster":::

Selecteer op de resulterende pagina **Afleidingscluster maken** een regio gevolgd door een VM-grootte (selecteer voor deze zelfstudie een `Standard_D11_v2`). Selecteer vervolgens **Volgende**. Doe het volgende op de pagina **Instellingen configureren**:

1. Geef een geldige naam op voor de berekening
1. Selecteer **Dev-test** als doel van het cluster (er wordt een enkel knooppunt gemaakt om het geïmplementeerde model te hosten)
1. Selecteer **Maken**

U ziet nu dat de **Status** van het afleidingscluster **Wordt gemaakt** is. Het duurt ongeveer 4 minuten om het knooppuntcluster te implementeren.

## <a name="create-a-dataset"></a>Een gegevensset maken

In deze zelfstudie gebruikt u de [Gegevensset Diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html), die beschikbaar is in [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/).

Om de gegevensset te maken selecteert u in het linkermenu **Gegevenssets** en vervolgens **Gegevensset maken**. U ziet nu de volgende opties:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Schermopname van het maken van een nieuwe gegevens":::

Selecteer **Vanuit Open Datasets**. Doe vervolgens in het scherm **Gegevensset maken vanuit Open Datasets** het volgende:

1. Zoek *diabetes* met behulp van de zoekbalk
1. Selecteer **Voorbeeld: Diabetes**
1. Selecteer **Volgende**
1. Geef een naam op voor de gegevensset: *diabetes*
1. Selecteer **Maken**

U kunt de gegevens verkennen door de gegevensset te selecteren, gevolgd door **Verkennen**:

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Schermopname van het verkennen van een gegevensset":::

De gegevens hebben 10 basislijnvariabelen voor invoer (zoals leeftijd, geslacht, BMI (body mass index), gemiddelde bloeddruk, en zes metingen voor bloedserum), en één doelvariabele genaamd **J** (een kwantitatieve meting van de progressie van diabetes één jaar na de basislijn).

## <a name="create-a-machine-learning-model-using-designer"></a>Een Machine Learning-model maken met behulp van de ontwerpfunctie

Zodra u de berekening en gegevenssets hebt gemaakt, kunt u beginnen met het maken van het machine learning-model met behulp van de ontwerpfunctie. Selecteer in Azure Machine Learning Studio de optie **Ontwerpfunctie**, gevolgd door **Nieuwe pijplijn**:

:::image type="content" source="media/tutorial-power-bi/create-designer.png" alt-text="Schermopname van het maken van een nieuwe pijplijn":::

U ziet een leeg *canvas* met een **menu Instellingen**:

:::image type="content" source="media/tutorial-power-bi/select-compute.png" alt-text="Schermopname van het selecteren van een rekendoel":::

Kies in het **menu Instellingen** de optie **Rekendoel selecteren**. Selecteer vervolgens het rekenproces dat u eerder hebt gemaakt, gevolgd door **Opslaan**. Wijzig de **Conceptnaam** in een naam die u beter kunt onthouden (bijvoorbeeld *diabetesmodel*) en voer een beschrijving in.

Vouw vervolgens in de vermelde activa **Gegevensset** uit en ga naar de gegevensset **diabetes**. Breng de module via slepen en neerzetten naar het canvas:

:::image type="content" source="media/tutorial-power-bi/drag-component.png" alt-text="Schermopname van het slepen van een onderdeel naar het canvas":::

Breng hierna de volgende onderdelen via slepen en neerzetten naar het canvas:

1. Lineaire regressie (bevindt zich in **Machine Learning-algoritmen**)
1. Model trainen (bevindt zich in **Modeltraining**)

Het canvas ziet er ongeveer als volgt uit (u ziet kleine rondjes (poorten genoemd) aan de boven- en onderkant van de onderdelen, hieronder in rood gemarkeerd):

:::image type="content" source="media/tutorial-power-bi/connections.png" alt-text="Schermopname van niet-verbonden onderdelen":::
 
Vervolgens moet u deze onderdelen met elkaar *verbinden*. Selecteer de poort aan de onderkant van de gegevensset **diabetes**, en sleep deze naar de rechterpoort bovenaan het onderdeel **Model trainen**. Selecteer de poort aan de onderkant van het onderdeel **Lineaire regressie**, en sleep deze naar de linkerpoort bovenaan het onderdeel **Model trainen**.

Kies de kolom in de gegevensset die moet worden gebruikt als de labelvariabele (doel) voor voorspellen. Selecteer het onderdeel **Model trainen**, gevolgd door **Kolom bewerken**. Selecteer in het dialoogvenster **Kolomnaam invoeren**, gevolgd door **J** in de vervolgkeuzelijst:

:::image type="content" source="media/tutorial-power-bi/label-columns.png" alt-text="Schermopname van het selecteren van de labelkolom":::

Selecteer **Opslaan**. De machine learning-*werkstroom* moet er als volgt uitzien:

:::image type="content" source="media/tutorial-power-bi/connected-diagram.png" alt-text="Schermopname van verbonden onderdelen":::

Selecteer **Verzenden** en vervolgens **Nieuwe maken** onder experiment. Geef een naam op voor het experiment, gevolgd door **Verzenden**.

>[!NOTE]
> Na de eerste uitvoering duurt het ongeveer 5 minuten om het experiment te voltooien. Volgende uitvoeringen zijn veel sneller, omdat met de ontwerpfunctie onderdelen die al zijn uitgevoerd, worden opgeslagen in de cache om latentie te verminderen.

Als het experiment is voltooid, ziet u:

:::image type="content" source="media/tutorial-power-bi/completed-run.png" alt-text="Schermopname van een voltooide uitvoering":::

U kunt de logboeken van het experiment bekijken, door **Model trainen** te selecteren, gevolgd door **Uitvoer en logboeken**.

## <a name="deploy-the-model"></a>Het model implementeren

Als u het model wilt implementeren, selecteert u **Deductiepijplijn maken** (bovenaan het canvas), gevolgd door **Realtime-deductiepijplijn**:

:::image type="content" source="media/tutorial-power-bi/pipeline.png" alt-text="Schermopname van een realtime-deductiepijplijn":::
 
De pijplijn wordt teruggebracht tot alleen de onderdelen die nodig zijn om het model te scoren. Als u de gegevens scoort, kent u de waarden van de doelvariabele niet, daarom kunnen we **J** verwijderen uit de gegevensset. Voeg, om dit te doen, een onderdeel **Kolommen in gegevensset selecteren** toe aan het canvas. Verbind het onderdeel zo dat de gegevensset de invoer is en de resultaten de uitvoer zijn in het onderdeel **Model scoren**:

:::image type="content" source="media/tutorial-power-bi/remove-column.png" alt-text="Schermopname van het verwijderen van een kolom":::

Selecteer het onderdeel **Kolommen in gegevensset selecteren** op het canvas, gevolgd door **Kolommen bewerken**. Selecteer in het dialoogvenster Kolommen selecteren de optie **Op naam**. Zorg er vervolgens voor dat alle invoervariabelen zijn geselecteerd, maar het doel **niet**:

:::image type="content" source="media/tutorial-power-bi/removal-settings.png" alt-text="Schermopname van het verwijderen van kolominstellingen":::

Selecteer **Opslaan**. Selecteer ten slotte het onderdeel **Model scoren**, en zorg ervoor dat het selectievakje **Scorekolommen toevoegen aan uitvoer** is uitgeschakeld (alleen de voorspellingen worden teruggestuurd, in plaats van de invoer *en* de voorspellingen, wat de latentie vermindert):

:::image type="content" source="media/tutorial-power-bi/score-settings.png" alt-text="Schermopname van de instellingen voor het onderdeel Model scoren":::

Selecteer **Verzenden** bovenaan het canvas.

Als u de deductiepijplijn juist het uitgevoerd, kunt u vervolgens het model implementeren in het afleidingscluster. Selecteer **Implementeren**. Het dialoogvenster **Realtime-eindpunt instellen** wordt nu weergegeven. Selecteer **Nieuw realtime-eindpunt implementeren** en geef het eindpunt de naam **mijn-diabetesmodel**. Selecteer het afleidingscluster dat u eerder hebt gemaakt, en selecteer **Implementeren**:

:::image type="content" source="media/tutorial-power-bi/endpoint-settings.png" alt-text="Schermopname van de instellingen voor het realtime-eindpunt":::
## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u gezien hoe u een ontwerpmodel traint en implementeert. In het volgende deel leert u hoe u dit model gebruikt (scoort) vanuit Power BI.

> [!div class="nextstepaction"]
> [Zelfstudie: Model gebruiken in Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
