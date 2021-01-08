---
title: 'Zelfstudie: Slepen en neerzetten om het voorspellende model te maken (deel 1 van 2)'
titleSuffix: Azure Machine Learning
description: Ontdek hoe u een voorspellend model voor machine learning bouwt en implementeert met behulp van de designer. Later kunt u het gebruiken om resultaten te voorspellen in Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.custom: designer
ms.openlocfilehash: 995979c7fe100637aa8e241489805fb09d6723f7
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814785"
---
# <a name="tutorial-power-bi-integration---drag-and-drop-to-create-the-predictive-model-part-1-of-2"></a>Zelfstudie: Power BI-integratie: Slepen en neerzetten om het voorspellende model te maken (deel 1 van 2)

In deel 1 van deze zelfstudie traint en implementeert u een voorspellend machine learning-model. Dit doet u met behulp van Azure Machine Learning-designer. De designer is een gebruikersinterface met weinig code waarin u elementen sleept en neerzet. In deel 2 gebruikt u het model om resultaten te voorspellen in Microsoft Power BI.

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Maak een Azure Machine Learning-rekenproces.
> * Maak een Azure Machine Learning-deductiecluster.
> * Maak een gegevensset maken.
> * Train een regressiemodel.
> * Implementeer het model in een eindpunt voor scoren in realtime.


Er zijn drie verschillende manieren om het model, dat u gaat gebruiken in Power BI, te maken en te implementeren.  Dit artikel is van toepassing op 'Optie B: Train en implementeer modellen met behulp van de designer."  Deze optie is een ontwerpervaring met weinig code die gebruikmaakt van de designer-interface.  

Maar u kunt in plaats daarvan één van de andere opties gebruiken:

* [Optie A: Train en implementeer modellen met behulp van Jupyter Notebooks](tutorial-power-bi-custom-model.md). Deze op code gerichte creatie-ervaring gebruikt Jupyter Notebooks die worden gehost in Azure Machine Learning Studio.
* [Optie C: Train en implementeer modellen met geautomatiseerde machine learning](tutorial-power-bi-automated-model.md). Deze creatie-ervaring zonder code automatiseert de gegevensvoorbereiding en modeltraining volledig.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u nog geen abonnement hebt, kunt u een [gratis proefaccount](https://aka.ms/AMLFree) gebruiken. 
- Een Azure Machine Learning-werkruimte. Als u nog geen werkruimte hebt, zie [Azure Machine Learning-werkruimten maken en beheren](./how-to-manage-workspace.md#create-a-workspace).
- Introductiekennis van machine learning-werkstromen.


## <a name="create-compute-to-train-and-score"></a>Een berekening maken om te trainen en te scoren

In deze sectie maakt u een *rekenproces*. Rekeninstanties worden gebruikt om machine learning-modellen te trainen. U maakt ook een *deductiecluster* dat het geïmplementeerde model voor scoren in realtime zal hosten.

Meld u aan bij [Azure Machine Learning Studio](https://ml.azure.com). Selecteer in het menu aan de linkerkant **Berekening**. Selecteer daarna **Nieuw**:

:::image type="content" source="media/tutorial-power-bi/create-new-compute.png" alt-text="Schermopname toont hoe een rekenproces kan worden gemaakt.":::

Selecteer op de pagina **Rekenproces maken** een VM-grootte. Voor deze zelfstudie selecteert u een **Standard_D11_v2**-VM. Selecteer vervolgens **Volgende**. 

Geef, op de pagina **Instellingen**, het rekenexemplaar een naam. Selecteer vervolgens **Maken**. 

>[!TIP]
> U kunt ook het rekenproces gebruiken om notebooks te maken en uit te voeren.

Het rekenproces **Status** is nu **Aan het maken**. Het inrichten van de computer duurt ongeveer 4 minuten. 

Terwijl u wacht, selecteert u op de pagina **Berekening** het tabblad **Deductieclusters**. Selecteer vervolgens **Nieuw**:

:::image type="content" source="media/tutorial-power-bi/create-cluster.png" alt-text="Schermopname toont hoe een deductiecluster kan worden gemaakt.":::

Selecteer op de pagina **Deductiecluster maken** een regio en een VM-grootte. Voor deze zelfstudie selecteert u een **Standard_D11_v2**-VM. Selecteer vervolgens **Volgende**. 

Doe het volgende op de pagina **Instellingen configureren**:

1. Geef een geldige naam op voor de berekening.
1. Selecteer **Dev-test** als clusterdoel. Met deze optie maakt u één knooppunt voor het hosten van het geïmplementeerde model.
1. Selecteer **Maken**.

De **Status** van uw deductiecluster is nu **Aan het maken**. De implementatie van het cluster met één knooppunt duurt ongeveer 4 minuten.

## <a name="create-a-dataset"></a>Een gegevensset maken

In deze zelfstudie gebruikt u de [gegevensset Diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html). Deze gegevensset is beschikbaar in [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/).

Om de gegevensset te maken, selecteert u **Gegevenssets**. Selecteer vervolgens **Gegevensset maken**. U ziet de volgende opties:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Schermopname toont hoe een nieuwe gegevensset kan worden gemaakt.":::

Selecteer **Van Open Datasets**. Op de pagina **Gegevensset maken op basis van Open Datasets**:

1. Gebruik de zoekbalk om *diabetes* te vinden.
1. Selecteer **Voorbeeld: Diabetes**.
1. Selecteer **Next**.
1. Geef uw gegevensset de naam *diabetes*.
1. Selecteer **Maken**.

Om de gegevens te verkennen, selecteert u de gegevensset en selecteert u vervolgens **Verkennen**:

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Schermopname toont hoe een gegevensset kan worden verkend.":::

De gegevens bevatten 10 basislijnvariabelen voor invoer (zoals leeftijd, geslacht, BMI (body mass index), gemiddelde bloeddruk en zes metingen voor bloedserum). Het bevat ook één doelvariabele met de naam **Y**. Deze doelvariabele is een kwantitatieve maat voor de voortgang van diabetes één jaar na de basislijn.

## <a name="create-a-machine-learning-model-by-using-the-designer"></a>Een machine learning-model maken met behulp van de designer

Nadat u de berekening en gegevenssets hebt gemaakt, kunt u de designer gebruiken om het machine learning-model te maken. Selecteer in Azure Machine Learning Studio de optie **Designer**, gevolgd door **Nieuwe pijplijn**:

:::image type="content" source="media/tutorial-power-bi/create-designer.png" alt-text="Schermopname toont hoe een nieuwe pijplijn kan worden gemaakt.":::

U ziet een leeg *canvas* en het menu **Instellingen**:

:::image type="content" source="media/tutorial-power-bi/select-compute.png" alt-text="Schermopname toont hoe een rekendooel kan worden geselecteerd.":::

Kies in het menu **Instellingen** **Rekendoel selecteren**. Selecteer de rekenproces dat u eerder hebt gemaakt en selecteer vervolgens **Opslaan**. Wijzig de naam van de **Conceptnaam** in iets dat eenvoudiger te onthouden is, zoals *diabetesmodel*. Voer ten slotte een beschrijving in.

Vouw **Gegevenssets** uit in lijst met assets. Zoek vervolgens de gegevensset **diabetes**. Sleep dit onderdeel naar het canvas:

:::image type="content" source="media/tutorial-power-bi/drag-component.png" alt-text="Schermopname toont hoe een onderdeel naar het canvas kan worden gesleept.":::

Sleep vervolgens de volgende onderdelen naar het canvas:

1. **Lineaire regressie** (bevindt zich in **Machine Learning-algoritmen**)
1. **Model trainen** (bevindt zich in **Modeltraining**)

Op het canvas ziet u de cirkels boven- en onderaan de onderdelen. Deze cirkels zijn poorten.

:::image type="content" source="media/tutorial-power-bi/connections.png" alt-text="Schermopname van poorten van niet-verbonden onderdelen.":::
 
*Koppel* de onderdelen nu samen. Selecteer de poort onderaan de gegevensset **diabetes**. Sleep deze naar de rechterbovenhoek van het onderdeel **Model trainen**. Selecteer de poort onderaan het onderdeel **Lineaire regressie**. Sleep het naar de rechterbovenhoek van het onderdeel **Model trainen**.

Kies de kolom in de gegevensset die moet worden gebruikt als de labelvariabele of de labeldoelvariabele om te voorspellen. Selecteer het onderdeel **Model trainen**. Selecteer vervolgens **Kolom bewerken**. 

Selecteer in het dialoogvenster **Kolomnaam invoeren** > **Y**:

:::image type="content" source="media/tutorial-power-bi/label-columns.png" alt-text="Schermopname toont hoe een labelkolom kan worden geselecteerd.":::

Selecteer **Opslaan**. Uw machine learning-*werkstroom* zou er als volgt moeten uitzien:

:::image type="content" source="media/tutorial-power-bi/connected-diagram.png" alt-text="Schermopname van verbonden onderdelen.":::

Selecteer **Indienen**. Selecteer, onder **Experiment**, de optie **Nieuw maken**. Geef het experiment een naam en selecteer vervolgens **Verzenden**.

>[!NOTE]
> De eerste uitvoering van uw experiment zou ongeveer 5 minuten moeten duren. Volgende uitvoeringen zijn veel sneller. Dit komt doordat de designer onderdelen die al zijn uitgevoerd opslaat in de cache om latentie te verminderen.

Wanneer het experiment is voltooid, ziet u deze weergave:

:::image type="content" source="media/tutorial-power-bi/completed-run.png" alt-text="Schermopname van een voltooide uitvoering.":::

Als u de experimentlogboeken wilt inspecteren, selecteert u **Model trainen** en selecteert u vervolgens **Uitvoer en logboeken**.

## <a name="deploy-the-model"></a>Het model implementeren

Als u het model wilt implementeren, selecteert u bovenaan het canvas **Deductiepijplijn maken** > **Realtime-deductiepijplijn**:

:::image type="content" source="media/tutorial-power-bi/pipeline.png" alt-text="Schermopname toont waar een realtime-deductiepijplijn kan worden geselecteerd.":::
 
De pijplijn wordt teruggebracht tot alleen de onderdelen die nodig zijn om het model te scoren. Wanneer u de gegevens scoort, weet u niet wat de waarden van de doelvariabele zijn. U kunt **Y** verwijderen uit de gegevensset. 

Om **Y** te verwijderen, voegt u een onderdeel **Kolommen in gegevensset selecteren** toe aan het canvas. Koppel het onderdeel zodat de diabetesgegevensset de invoer is. De resultaten zijn de uitvoer in het onderdeel **Model scoren**:

:::image type="content" source="media/tutorial-power-bi/remove-column.png" alt-text="Schermopname toont hoe een kolom kan worden verwijderd.":::

Selecteer het onderdeel **Kolommen in gegevensset selecteren** op het canvas, gevolgd door **Kolommen bewerken**. 

Kies in het dialoogvenster **Kolommen selecteren** de optie **Op naam**. Zorg er vervolgens voor dat alle invoervariabelen zijn geselecteerd, maar dat het doel *niet* is geselecteerd:

:::image type="content" source="media/tutorial-power-bi/removal-settings.png" alt-text="Schermopname toont hoe kolominstellingen kan worden verwijderd.":::

Selecteer **Opslaan**. 

Selecteer ten slotte het onderdeel **Model scoren** en zorg ervoor dat het selectievakje **Score-kolommen toevoegen aan uitvoer** is uitgeschakeld. Om de latentie te verminderen, worden de voorspellingen teruggestuurd zonder de invoer.

:::image type="content" source="media/tutorial-power-bi/score-settings.png" alt-text="Schermopname van instellingen voor het Score Model-onderdeel.":::

Bovenaan het canvas selecteert u **Indienen**.

Als u de deductiepijplijn succesvol heeft uitgevoerd, kunt u vervolgens het model implementeren in het deductiecluster. Selecteer **Implementeren**. 

Selecteer in het dialoogvenster **Realtime-eindpunt instellen** **Nieuw realtime-eindpunt implementeren**. Geef het eindpunt de naam *mijn-diabetesmodel*. Selecteer de deductie die u eerder hebt gemaakt en selecteer vervolgens **Implementeren**:

:::image type="content" source="media/tutorial-power-bi/endpoint-settings.png" alt-text="Schermopname van de instellingen voor het realtime-eindpunt.":::
## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u gezien hoe u een ontwerpmodel traint en implementeert. In het volgende deel leert u hoe u dit model gebruikt (scoort) in Power BI.

> [!div class="nextstepaction"]
> [Zelfstudie: Een model gebruiken in Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
