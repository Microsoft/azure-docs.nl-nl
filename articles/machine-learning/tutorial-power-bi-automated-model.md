---
title: 'Zelfstudie: Het voorspellende model maken met behulp van geautomatiseerde ML (deel 1 van 2)'
titleSuffix: Azure Machine Learning
description: Leer hoe u geautomatiseerde ML-modellen bouwt en implementeert, zodat u het beste model kunt gebruiken om de resultaten te voorspellen in Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 897f493edf6ccdebb25c201e8e4f9babfb0754c5
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370759"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-using-automated-machine-learning-part-1-of-2"></a>Zelfstudie: Power BI-integratie: het voorspellende model maken met behulp van geautomatiseerde machine learning (deel 1 van 2)

In het eerste deel van deze zelfstudie traint en implementeert u een voorspellend machine learning-model met behulp van geautomatiseerde machine learning in Azure Machine Learning Studio.  In deel 2 gebruikt u het best presterende model om resultaten te voorspellen in Microsoft Power BI.

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Een Azure Machine Learning-rekencluster maken
> * Een gegevensset maken
> * Een geautomatiseerde ML-uitvoering maken
> * Het beste model implementeren in een realtime-eindpunt voor scoren


Er zijn drie verschillende manieren om het model dat u gaat gebruiken in Power BI, te maken en te implementeren.  Dit artikel is van toepassing op Optie C: Train en implementeer modellen met behulp van geautomatiseerde ML in Studio.  Deze optie toont een ontwerpervaring zonder code waarmee de gegevensvoorbereiding en modeltraining volledig is geautomatiseerd. 

Gebruik in plaats hiervan:

* [Optie A: Modellen trainen en implementeren met behulp van Notebooks](tutorial-power-bi-custom-model.md): een op code gerichte ontwerpervaring met behulp van Jupyter-notebooks die worden gehost in Azure Machine Learning Studio.
* [Optie B: Modellen trainen en implementeren met behulp van de ontwerpfunctie](tutorial-power-bi-designer-model.md): een ontwerpervaring met weinig code, met behulp van de ontwerpfunctie (een slepen en neerzetten-gebruikersinterface).

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement ([een gratis proefversie beschikbaar](https://aka.ms/AMLFree)). 
- Een Azure Machine Learning-werkruimte. Als u nog geen werkruimte hebt, volgt u [Een Azure Machine Learning-werkruimte maken](./how-to-manage-workspace.md#create-a-workspace).

## <a name="create-compute-cluster"></a>Een rekencluster maken

Met geautomatiseerde ML worden veel verschillende machine learning-modellen getraind, om zo het beste algoritme en de beste parameters te vinden. Met Azure Machine Learning wordt de modeltraining parallel uitgevoerd in een rekencluster.

Selecteer in [Azure Machine Learning Studio](https://ml.azure.com) de optie **Berekening** in het linkermenu, gevolgd door tabblad **Rekenclusters**. Selecteer **Nieuw**:

:::image type="content" source="media/tutorial-power-bi/create-compute-cluster.png" alt-text="Schermopname van het maken van een rekencluster":::

Selecteer in het scherm **Rekencluster maken**:

1. Selecteer een VM-grootte (voor deze zelfstudie is een `Standard_D11_v2`-machine voldoende).
1. Selecteer **Volgende**
1. Geef een geldige naam op voor de berekening
1. Houd **Minimum aantal knooppunten** op 0
1. Wijzig **Maximum aantal knooppunten** in 4
1. Selecteer **Maken**

U ziet dat de status van het cluster is gewijzigd in **Wordt gemaakt**.

>[!NOTE]
> Wanneer het cluster is gemaakt, heeft het een 0 knooppunten. Dit betekent dat er geen berekeningskosten worden gemaakt. Er worden alleen kosten in rekening gebracht wanneer de geautomatiseerde ML-taak actief is. Het cluster wordt na een niet-actieve periode van 120 seconden terug geschaald naar 0.


## <a name="create-dataset"></a>Gegevensset maken

In deze zelfstudie gebruikt u de [Gegevensset Diabetes](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html), die beschikbaar is in [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/).

Om de gegevensset te maken selecteert u in het linkermenu **Gegevenssets**, gevolgd door **Gegevensset maken**. U ziet nu de volgende opties:

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

## <a name="create-automated-ml-run"></a>Geautomatiseerde ML-uitvoering maken

Selecteer in [Azure Machine Learning Studio](https://ml.azure.com) in het linkermenu de optie **Geautomatiseerde ML**, gevolgd door **Nieuwe geautomatiseerde ML-uitvoering**:

:::image type="content" source="media/tutorial-power-bi/create-new-run.png" alt-text="Schermopname van het maken van een nieuwe geautomatiseerde ML-uitvoering":::

Selecteer vervolgens de gegevensset **diabetes** die u eerder hebt gemaakt, en selecteer **Volgende**:

:::image type="content" source="media/tutorial-power-bi/select-dataset.png" alt-text="Schermopname van het selecteren van een gegevensset":::
 
Selecteer in het scherm **Uitvoering configureren**,

1. onder **Naam van experiment**, de optie **Nieuwe maken**
1. Geef het experiment een naam
1. Selecteer in het veld Doelkolom de optie **J**
1. Selecteer in het veld **Rekencluster selecteren** het rekencluster dat u eerder hebt gemaakt. 

Het ingevulde formulier ziet er ongeveer als volgt uit:

:::image type="content" source="media/tutorial-power-bi/configure-automated.png" alt-text="Schermopname van het configureren van geautomatiseerde ML":::

Ten slotte selecteert u de machine learning-taak die moet worden uitgevoerd. Dit is **Regressie**:

:::image type="content" source="media/tutorial-power-bi/configure-task.png" alt-text="Schermopname van het configureren van een taak":::

Selecteer **Finish**.

> [!IMPORTANT]
> Het duurt ongeveer 30 minuten voordat alle 100 verschillende modellen zijn getraind met geautomatiseerde ML.

## <a name="deploy-the-best-model"></a>Het beste model implementeren

Zodra de geautomatiseerde ML-uitvoering is voltooid, ziet u een lijst met alle verschillende machine learning-modellen die zijn geprobeerd, door het tabblad **Modellen** te selecteren. De modellen zijn gerangschikt op prestaties. Het best presterende model wordt als eerste weergegeven. Als u het beste model selecteert, wordt de knop **Implementeren** ingeschakeld:

:::image type="content" source="media/tutorial-power-bi/list-models.png" alt-text="Schermopname van de lijst met modellen":::

Als u **Implementeren** selecteert, verschijnt het scherm **Een model implementeren**:

1. Geef de modelservice een naam: gebruik **diabetesmodel**
1. Selecteer **Azure Container Service**
1. Selecteer **Implementeren**

U ziet nu een bericht waarin staat dat de implementatie van het model is geslaagd.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u gezien hoe u een machine learning-model traint en implementeert met behulp van geautomatiseerde ML. In de volgende zelfstudie leert u hoe u dit model kunt gebruiken (scoren) vanuit Power BI.

> [!div class="nextstepaction"]
> [Zelfstudie: Model gebruiken in Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
