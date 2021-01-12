---
title: 'Zelfstudie: Een model trainen met geautomatiseerde machine learning'
description: Zelfstudie over hoe u een machine learning-model zonder code traint in Azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: e219531a88787f19197a2e8c2a80040497c6dc1e
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901416"
---
# <a name="tutorial-train-a-machine-learning-model-without-code"></a>Zelfstudie: een machine learning-model trainen zonder code

U kunt uw gegevens in Spark-tabellen uitbreiden met nieuwe machine learning-modellen die u traint met behulp van [geautomatiseerde machine learning](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml). In Azure Synapse Analytics kunt u een Spark-tabel selecteren in de werkruimte en deze gebruiken als trainingsgegevensset om zonder code machine learning-modellen te bouwen.

In deze zelfstudie leert u hoe u zonder code machine learning-modellen traint in de Azure Synapse Analytics-studio. U gebruikt geautomatiseerde machine learning in Azure Machine Learning in plaats van handmatig te coderen. Het type model dat u traint, is afhankelijk van het probleem dat u wilt oplossen.

Als u geen Azure-abonnement hebt, [maakt u een gratis account voordat u begint](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Vereisten

- Een [Azure Synapse Analytics-werkruimte](../get-started-create-workspace.md). Zorg ervoor dat deze het volgende opslagaccount heeft, geconfigureerd als standaardopslag: Azure Data Lake Storage Gen2. Voor het Data Lake Storage Gen2-bestandssysteem waarmee u werkt, moet u ervoor zorgen dat u de **inzender voor Storage Blob-gegevens bent**.
- Een Apache Spark-pool in uw Azure Synapse Analytics-werkruimte. Voor meer informatie raadpleegt u [Quickstart: Een toegewezen SQL-pool maken met behulp van de Azure Synapse Analytics-studio](../quickstart-create-sql-pool-studio.md).
- Een gekoppelde Azure Machine Learning-service in uw Azure Synapse Analytics-werkruimte. Voor meer informatie raadpleegt u [Quickstart: een nieuwe gekoppelde Azure Machine Learning-service maken in Azure Synapse Analytics](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="create-a-spark-table-for-training-dataset"></a>Een Spark-tabel maken voor een trainingsgegevensset

Voor deze zelfstudie hebt u een Spark-tabel nodig. Met het volgende notebook wordt er een gemaakt.

1. Download het notebook [Create-Spark-Table-NYCTaxi- Data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149229).

1. Importeer het notebook in de Azure Synapse Analytics-studio.
![Schermopname van Azure Synapse Analytics, met de optie importeren gemarkeerd.](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. Selecteer de Spark-pool die u wilt gebruiken en selecteer **Alle uitvoeren**. Hiermee worden taxigegevens uit New York opgehaald uit de open gegevensset en opgeslagen in uw standaarddatabase in Spark.
![Schermopname van Azure Synapse Analytics, met Alle uitvoeren en de Spark-database gemarkeerd.](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. Nadat de uitvoering van het notebook is voltooid, ziet u een nieuwe Spark-tabel in de standaarddatabase in Spark. Zoek vanuit **Gegevens** de tabel met de naam **nyc_taxi**.
![Schermopname van het tabblad Gegevens in Azure Synapse Analytics, met de nieuwe tabel gemarkeerd.](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="launch-automated-machine-learning-wizard"></a>Start de wizard voor geautomatiseerde machine learning

U doet dit als volgt:

1. Klik met de rechtermuisknop op de Spark-tabel die u in de vorige stap hebt gemaakt. Selecteer **Machine Learning** > **Verrijken met nieuw model** om de wizard te openen.
![Schermopname van de Spark-tabel, met Machine Learning en Verrijken met nieuw model gemarkeerd.](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

1. U kunt vervolgens uw configuratiegegevens opgeven om een geautomatiseerd ML-experiment te maken dat in Azure Machine Learning wordt uitgevoerd. Met deze uitvoering worden meerdere modellen getraind. Het beste model van een geslaagde uitvoering wordt geregistreerd in het Azure Machine Learning-modelregister.

   ![Schermopname van de configuratiespecificaties voor Verrijken met nieuw model.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

    - **Azure Machine Learning-werkruimte**: Er is een Azure Machine Learning-werkruimte is vereist om de uitvoering van het geautomatiseerde ML-experiment te maken. U moet ook uw Azure Synapse Analytics-werkruimte koppelen aan de Azure Machine Learning-werkruimte met behulp van een [gekoppelde service](quickstart-integrate-azure-machine-learning.md). Zodra u aan alle vereisten voldoet, kunt u de Azure Machine Learning-werkruimte opgeven die u wilt gebruiken voor deze geautomatiseerde uitvoering.

    - **Naam van experiment**: Geef de naam van het experiment op. Wanneer u een geautomatiseerde ML-uitvoering verzendt, geeft u een naam op voor het experiment. Informatie over de uitvoering wordt opgeslagen bij dit experiment in de Azure Machine Learning-werkruimte. Er wordt standaard een nieuw experiment gemaakt en er wordt een suggestie voor een naam gegenereerd. U kunt echter ook de naam van een bestaand experiment opgeven.

    - **Beste model**: Geef de naam op van het beste model uit de geautomatiseerde uitvoering. Het beste model krijgt deze naam. Deze wordt na deze uitvoering automatisch opgeslagen in het Azure Machine Learning-modelregister. In een geautomatiseerde ML-uitvoering worden veel ML-modellen gemaakt. Deze modellen kunnen worden vergeleken en het beste model kan worden geselecteerd op basis van de primaire metrische gegevens die u in een latere stap selecteert.

    - **Doelkolom**: Het model is getraind om dit te voorspellen. Kies de kolom die u wilt voorspellen. (In deze zelfstudie wordt de numerieke kolom `fareAmount` als doelkolom geselecteerd.)

    - **Spark-pool**: De Spark-pool die u wilt gebruiken voor de uitvoering van het geautomatiseerde experiment. De berekeningen worden uitgevoerd voor de pool die u opgeeft.

    - **Spark-configuratiedetails**: Naast de Spark-pool hebt u ook de mogelijkheid om configuratiedetails voor de sessie op te geven.

1. Selecteer **Doorgaan**.

## <a name="choose-task-type"></a>Kies het taaktype

Selecteer het type machine learning-model voor het experiment op basis van de vraag die u wilt beantwoorden. Aangezien `fareAmount` de doelkolom is en dit een numerieke waarde is, selecteert u hier **Regressie**. Selecteer vervolgens **Doorgaan**.

![Schermopname van Verrijken met nieuw model, met Regressie gemarkeerd.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>Aanvullende configuraties

Als u **Regressie** of **Classificatie** als uw modeltype in de vorige sectie selecteert, zijn de volgende configuraties beschikbaar:

- **Primaire metrische gegevens**: De metrische gegevens die worden gebruikt om te meten hoe goed het model is. Dit zijn de metrische gegevens die worden gebruikt om verschillende modellen te vergelijken die zijn gemaakt bij de geautomatiseerde uitvoering. Deze geven aan welk model het beste heeft gepresteerd.

- **Duur van trainingstaak (in uren)** : De maximale tijdsduur, in uren, om een experiment uit te voeren en modellen te trainen. U kunt ook waarden opgeven die kleiner zijn dan 1 (bijvoorbeeld `0.5`).

- **Maximum aantal gelijktijdige herhalingen**: Vertegenwoordigt het maximum aantal herhalingen die parallel worden uitgevoerd.

- **Compatibiliteit met ONNX-modellen**: Als u deze optie inschakelt, worden de modellen die met automatische machine learning zijn getraind, geconverteerd naar de ONNX-indeling. Dit is met name relevant als u het model wilt gebruiken in SQL-pools van Azure Synapse Analytics.

Deze instellingen hebben allemaal een standaardwaarde die u kunt aanpassen.
![Schermopname van aanvullende configuraties voor Verrijken met nieuw model.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

Zodra alle vereiste configuraties zijn uitgevoerd, kunt u de geautomatiseerde uitvoering starten. U kunt **Create run** kiezen, waarmee de uitvoering rechtstreeks, zonder code, wordt gestart. Als u liever code gebruikt, kunt u **Openen in notebook** selecteren. Met deze optie kunt u de code zien waarmee de uitvoering wordt gemaakt en die het notebook uitvoert.

>[!NOTE]
>Als u **Prognose voor tijdreeksen** als modeltype in de vorige sectie selecteert, moet u aanvullende configuraties maken. Het maken van een prognose biedt ook geen ondersteuning voor compatibiliteit met ONNX-modellen.

### <a name="create-run-directly"></a>Uitvoering rechtstreeks maken

Als u uw geautomatiseerde machine learning direct wilt uitvoeren, selecteert u **Uitvoering starten**. U ziet een melding waarin wordt aangegeven dat de uitvoering wordt gestart. Vervolgens ziet u een andere melding die aangeeft dat de uitvoering is geslaagd. U kunt ook de status in Azure Machine Learning controleren door de koppeling in de melding te selecteren.
![Schermopname van geslaagde melding.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-run-with-notebook"></a>Uitvoering maken met notebook

Selecteer **Openen in notebook** om een notebook te genereren. Selecteer vervolgens **Alle uitvoeren**. Dit biedt u ook de mogelijkheid om extra instellingen toe te voegen aan de geautomatiseerde ML-uitvoering.

![Schermopname van notebook, met Alle uitvoeren gemarkeerd.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

Nadat de uitvoering is verzonden, verschijnt er in de uitvoer van het notebook een koppeling naar de uitvoering van het experiment in de Azure Machine Learning-werkruimte. Selecteer de koppeling om de geautomatiseerde uitvoering te bewaken in Azure Machine Learning.
![Schermopname van Azure Synapse Analytics, met de koppeling gemarkeerd.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png)

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Wizard voor scoren van het Machine learning-model voor toegewezen SQL-pools](tutorial-sql-pool-model-scoring-wizard.md)
- [Snelstart: een nieuwe gekoppelde Azure Machine Learning-service maken in Azure Synapse Analytics](quickstart-integrate-azure-machine-learning.md)
- [Mogelijkheden voor machine learning in Azure Synapse Analytics](what-is-machine-learning.md)
