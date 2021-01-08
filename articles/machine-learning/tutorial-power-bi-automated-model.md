---
title: 'Zelfstudie: Het voorspellende model maken met behulp van geautomatiseerde ML (deel 1 van 2)'
titleSuffix: Azure Machine Learning
description: Ontdek hoe u geautomatiseerde machine learning-modellen bouwt en implementeert, zodat u het beste model kunt gebruiken om resultaten te voorspellen in Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 6dc99d58f15653e9d3f991622de3bb3388690459
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814802"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-by-using-automated-machine-learning-part-1-of-2"></a>Zelfstudie: Power BI-integratie: het voorspellende model maken met behulp van geautomatiseerde machine learning (deel 1 van 2)

In deel 1 van deze zelfstudie traint en implementeert u een voorspellend machine learning-model. U kunt automatische machine learning (ML) gebruiken in Azure Machine Learning Studio.  In deel 2 gebruikt u het best presterende model om resultaten te voorspellen in Microsoft Power BI.

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Maak een Azure Machine Learning-rekencluster.
> * Maak een gegevensset maken.
> * Maak een uitvoering voor geautomatiseerde machine learning.
> * Implementeer het beste model in een realtime-eindpunt voor scoren.


Er zijn drie verschillende manieren om het model, dat u gaat gebruiken in Power BI, te maken en te implementeren.  Dit artikel is van toepassing op 'Optie C: Train en implementeer modellen met geautomatiseerde machine learning in de studio."  Deze optie is een creatie-ervaring zonder code. De gegevensvoorbereiding en modeltraining worden volledig geautomatiseerd. 

Maar u kunt in plaats daarvan één van de andere opties gebruiken:

* [Optie A: Train en implementeer modellen met behulp van Jupyter Notebooks](tutorial-power-bi-custom-model.md). Deze op code gerichte creatie-ervaring gebruikt Jupyter Notebooks die worden gehost in Azure Machine Learning Studio.
* [Optie B: Train en implementeer modellen met Azure Machine Learning-designer](tutorial-power-bi-designer-model.md). Deze creatie-ervaring met weinig code gebruikt een slepen en neerzetten-gebruikersinterface.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u nog geen abonnement hebt, kunt u een [gratis proefaccount](https://aka.ms/AMLFree) gebruiken. 
- Een Azure Machine Learning-werkruimte. Als u nog geen werkruimte hebt, zie [Azure Machine Learning-werkruimten maken en beheren](./how-to-manage-workspace.md#create-a-workspace).

## <a name="create-a-compute-cluster"></a>Een rekencluster maken

Met geautomatiseerde machine learning worden veel verschillende machine learning-modellen getraind, om zo het beste algoritme en de beste parameters te vinden. Met Azure Machine Learning wordt de modeltraining parallel uitgevoerd in een rekencluster.

Om te beginnen, gaat u naar [Azure Machine Learning Studio](https://ml.azure.com). In het menu aan de linkerkant selecteert u **Berekening**. Open het tabblad **Rekenclusters**. Selecteer vervolgens **Nieuw**:

:::image type="content" source="media/tutorial-power-bi/create-compute-cluster.png" alt-text="Schermopname toont hoe een rekencluster kan worden gemaakt.":::

Selecteer op de pagina **Rekencluster maken**:

1. Selecteer een VM-grootte. Voor deze zelfstudie is een **Standard_D11_v2**-machine prima.
1. Selecteer **Next**.
1. Geef een geldige naam op voor de berekening.
1. Houd **Minimum aantal knooppunten** op `0`.
1. Wijzig **Maximum aantal knooppunten** in `4`.
1. Selecteer **Maken**.

De status van het cluster verandert in **Aan het maken**.

>[!NOTE]
> Het nieuwe cluster heeft 0 knooppunten, waardoor er geen rekenkosten in rekening worden gebracht. Er worden alleen kosten in rekening gebracht wanneer de geautomatiseerde machine learning-taak wordt uitgevoerd. Het cluster wordt na een niet-actieve periode van 120 seconden teruggeschaald naar 0.


## <a name="create-a-dataset"></a>Een gegevensset maken

In deze zelfstudie gebruikt u de [gegevensset Diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html). Deze gegevensset is beschikbaar in [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/).

Om de gegevensset te maken, selecteert u **Gegevenssets**. Selecteer vervolgens **Gegevensset maken**. U ziet de volgende opties:

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="Schermopname toont hoe een nieuwe gegevensset kan worden gemaakt.":::

Selecteer **Van Open Datasets**. Vervolgens, op de pagina **Gegevensset maken op basis van Open Datasets**:

1. Gebruik de zoekbalk om *diabetes* te vinden.
1. Selecteer **Voorbeeld: Diabetes**.
1. Selecteer **Next**.
1. Geef uw gegevensset de naam *diabetes*.
1. Selecteer **Maken**.

Om de gegevens te verkennen, selecteert u de gegevensset en selecteert u vervolgens **Verkennen**:

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="Schermopname toont hoe een gegevensset kan worden verkend.":::

De gegevens bevatten 10 basislijnvariabelen voor invoer (zoals leeftijd, geslacht, BMI (body mass index), gemiddelde bloeddruk en zes metingen voor bloedserum). Het bevat ook één doelvariabele met de naam **Y**. Deze doelvariabele is een kwantitatieve maat voor de voortgang van diabetes één jaar na de basislijn.

## <a name="create-an-automated-machine-learning-run"></a>Een uitvoering voor geautomatiseerde machine learning maken

In [Azure Machine Learning Studio](https://ml.azure.com) selecteert u, in het menu aan de linkerkant **Geautomatiseerde ML**. Selecteer vervolgens **Nieuwe geautomatiseerde ML-uitvoering**:

:::image type="content" source="media/tutorial-power-bi/create-new-run.png" alt-text="Schermopname toont hoe geautomatiseerde machine learning kan worden gemaakt.":::

Selecteer vervolgens de gegevensset **diabetes** die u eerder hebt gemaakt. Selecteer vervolgens **Volgende**:

:::image type="content" source="media/tutorial-power-bi/select-dataset.png" alt-text="Schermopname toont hoe een database kan worden geselecteerd.":::
 
In het scherm **Uitvoering configureren**:

1. Selecteer onder **Naam van het experiment** **Nieuw maken**.
1. Geef een naam aan het experiment.
1. Selecteer in het veld **Doelkolom** de optie **Y**.
1. Selecteer in het veld **Rekencluster selecteren** het rekencluster dat u eerder hebt gemaakt. 

Het ingevulde formulier zou er als volgt uit moeten zien:

:::image type="content" source="media/tutorial-power-bi/configure-automated.png" alt-text="Schermopname toont hoe machine learning kan worden geautomatiseerd.":::

Selecteer ten slotte een machine learning-taak. In dit geval is de taak **Regressie**:

:::image type="content" source="media/tutorial-power-bi/configure-task.png" alt-text="Schermopname toont hoe een taak kan worden geconfigureerd.":::

Selecteer **Finish**.

> [!IMPORTANT]
> Automatische machine learning traint de 100 modellen in ongeveer 30 minuten.

## <a name="deploy-the-best-model"></a>Het beste model implementeren

Zodra de geautomatiseerde machine learning is voltooid, ziet u in het tabblad **Modellen** alle verschillende machine learning-modellen die zijn geprobeerd. De modellen zijn gerangschikt op prestaties. Het best presterende model wordt als eerste weergegeven. Nadat u het beste model hebt geselecteerd, wordt de knop **Implementeren** ingeschakeld:

:::image type="content" source="media/tutorial-power-bi/list-models.png" alt-text="Schermopname van de lijst met modellen.":::

Selecteer **Implementeren** om een venster **Een model implementeren** te openen:

1. Geef uw modelservice de naam *diabetesmodel*.
1. Selecteer **Azure Container Service**.
1. Selecteer **Implementeren**.

U zou nu een bericht moeten zien waarin staat dat de implementatie van het model is geslaagd.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u gezien hoe u een machine learning-model traint en implementeert met behulp van geautomatiseerde machine learning. In de volgende zelfstudie ontdekt u hoe u dit model gebruikt (scoort) in Power BI.

> [!div class="nextstepaction"]
> [Zelfstudie: Een model gebruiken in Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
