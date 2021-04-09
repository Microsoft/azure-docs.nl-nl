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
ms.openlocfilehash: aaf0aab2ef600b269b9b47182aeb096ca13c7a87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943517"
---
# <a name="tutorial-train-a-machine-learning-model-without-code"></a>Zelfstudie: een machine learning-model trainen zonder code

U kunt uw gegevens in Spark-tabellen uitbreiden met nieuwe machine learning-modellen die u traint met behulp van [geautomatiseerde machine learning](../../machine-learning/concept-automated-ml.md). In Azure Synapse Analytics kunt u een Spark-tabel selecteren in de werkruimte en deze gebruiken als trainingsgegevensset om zonder code machine learning-modellen te bouwen.

In deze zelf studie leert u hoe u machine learning modellen traint met behulp van een code-Free experience in Synapse Studio. Synapse Studio is een functie van Azure Synapse Analytics. 

U gebruikt automatische machine learning in Azure Machine Learning, in plaats van de ervaring hand matig te coderen. Het type model dat u traint, is afhankelijk van het probleem dat u probeert op te lossen.

Als u geen Azure-abonnement hebt, [maakt u een gratis account voordat u begint](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Vereisten

- Een [Azure Synapse Analytics-werkruimte](../get-started-create-workspace.md). Zorg ervoor dat er een Azure Data Lake Storage Gen2 Storage-account is geconfigureerd als de standaard opslag. Voor het Data Lake Storage Gen2-bestandssysteem waarmee u werkt, moet u ervoor zorgen dat u de *inzender voor Storage Blob-gegevens bent*.
- Een Apache Spark-pool in uw Azure Synapse Analytics-werkruimte. Zie [Quick Start: een speciale SQL-groep maken met behulp van Synapse Studio](../quickstart-create-sql-pool-studio.md)voor meer informatie.
- Een gekoppelde Azure Machine Learning-service in uw Azure Synapse Analytics-werkruimte. Voor meer informatie raadpleegt u [Quickstart: een nieuwe gekoppelde Azure Machine Learning-service maken in Azure Synapse Analytics](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="create-a-spark-table-for-the-training-dataset"></a>Een Spark-tabel maken voor de trainings gegevensset

Voor deze zelfstudie hebt u een Spark-tabel nodig. Het volgende notitie blok maakt er een:

1. Download het notebook [Create-Spark-Table-NYCTaxi- Data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149229).

1. Importeer het notitie blok in Synapse Studio.
![Scherm opname van Azure Synapse Analytics, met de optie importeren gemarkeerd.](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. Selecteer de Spark-groep die u wilt gebruiken en selecteer vervolgens **alles uitvoeren**. Met deze stap worden de nieuwe taxi's-gegevens opgehaald uit de open gegevensset en worden de gegevens opgeslagen in uw standaard Spark-data base.
![Schermopname van Azure Synapse Analytics, met Alle uitvoeren en de Spark-database gemarkeerd.](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. Nadat de uitvoering van het notebook is voltooid, ziet u een nieuwe Spark-tabel in de standaarddatabase in Spark. Zoek vanuit **Gegevens** de tabel met de naam **nyc_taxi**.
![Scherm afbeelding van het tabblad Azure Synapse Analytics-gegevens, waarbij de nieuwe tabel is gemarkeerd.](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="open-the-automated-machine-learning-wizard"></a>Open de wizard geautomatiseerde machine learning

De wizard openen:

1. Klik met de rechtermuisknop op de Spark-tabel die u in de vorige stap hebt gemaakt. Selecteer vervolgens **machine learning**  >  **verrijken met nieuw model**.
![Schermopname van de Spark-tabel, met Machine Learning en Verrijken met nieuw model gemarkeerd.](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

1. Geef configuratie gegevens op voor het maken van een automatisch machine learning experiment dat wordt uitgevoerd in Azure Machine Learning. Deze uitvoering treinen meerdere modellen. Het beste model van een geslaagde uitvoering wordt geregistreerd in het REGI ster van het Azure Machine Learning model.

   ![Scherm opname van configuratie specificaties voor het trainen van een machine learning model.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

    - **Azure Machine Learning-werkruimte**: Er is een Azure Machine Learning-werkruimte is vereist om de uitvoering van het geautomatiseerde ML-experiment te maken. U moet ook uw Azure Synapse Analytics-werkruimte koppelen aan de Azure Machine Learning-werkruimte met behulp van een [gekoppelde service](quickstart-integrate-azure-machine-learning.md). Nadat u aan alle vereisten hebt voldaan, kunt u de Azure Machine Learning werk ruimte opgeven die u wilt gebruiken voor deze geautomatiseerde uitvoering.

    - **Naam van experiment**: Geef de naam van het experiment op. Wanneer u een geautomatiseerde ML-uitvoering verzendt, geeft u een naam op voor het experiment. Informatie over de uitvoering wordt opgeslagen bij dit experiment in de Azure Machine Learning-werkruimte. Deze ervaring maakt standaard een nieuw experiment en genereert een voorgestelde naam, maar u kunt ook de naam van een bestaand experiment opgeven.

    - **Beste model naam**: Geef de naam op van het beste model van de geautomatiseerde uitvoering. Het beste model krijgt deze naam. Deze wordt na deze uitvoering automatisch opgeslagen in het Azure Machine Learning-modelregister. In een geautomatiseerde ML-uitvoering worden veel ML-modellen gemaakt. Deze modellen kunnen worden vergeleken en het beste model kan worden geselecteerd op basis van de primaire metrische gegevens die u in een latere stap selecteert.

    - **Doelkolom**: Het model is getraind om dit te voorspellen. Kies de kolom die u wilt voorspellen. (In deze zelfstudie wordt de numerieke kolom `fareAmount` als doelkolom geselecteerd.)

    - **Spark-groep**: Geef de Spark-groep op die u wilt gebruiken voor de automatische uitvoering van het experiment. De berekeningen worden uitgevoerd op de pool die u opgeeft.

    - **Spark-configuratie details**: naast de Spark-groep hebt u de mogelijkheid om sessie configuratie details op te geven.

1. Selecteer **Doorgaan**.

## <a name="choose-a-task-type"></a>Kies een taak type

Selecteer het type machine learning-model voor het experiment op basis van de vraag die u wilt beantwoorden. Omdat `fareAmount` de doel kolom is en een numerieke waarde is, raden we u aan om hier **regressie** te selecteren. Selecteer vervolgens **Doorgaan**.

![Schermopname van Verrijken met nieuw model, met Regressie gemarkeerd.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>Aanvullende configuraties

Als u in de vorige sectie **regressie** of **classificatie** hebt geselecteerd als uw model type, zijn de volgende configuraties beschikbaar:

- **Primaire metriek**: Voer de metrische gegevens in waarmee wordt gemeten hoe goed het model is. U gebruikt deze metrische gegevens om verschillende modellen te vergelijken die in de geautomatiseerde uitvoering zijn gemaakt en om te bepalen welk model het beste wordt uitgevoerd.

- **Tijd van trainings taak (uren)**: Geef de maximale tijds duur op, in uren, voor een experiment voor het uitvoeren van en het trainen van modellen. U kunt ook waarden opgeven die kleiner zijn dan 1 (bijvoorbeeld **0,5**).

- **Max. aantal gelijktijdige herhalingen**: Kies het maximum van de herhalingen die parallel worden uitgevoerd.

- **Compatibiliteit met ONNX-modellen**: Als u deze optie inschakelt, worden de modellen die met automatische machine learning zijn getraind, geconverteerd naar de ONNX-indeling. Dit is met name relevant als u het model wilt gebruiken in SQL-pools van Azure Synapse Analytics.

Deze instellingen hebben allemaal een standaardwaarde die u kunt aanpassen.
![Scherm opname van aanvullende configuraties voor het configureren van een regressie model.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

Zodra alle vereiste configuraties zijn uitgevoerd, kunt u de geautomatiseerde uitvoering starten. U kunt **Create run** kiezen, waarmee de uitvoering rechtstreeks, zonder code, wordt gestart. Als u liever code gebruikt, kunt u **Openen in notebook** selecteren. Met deze optie kunt u de code die de uitvoering maakt, bekijken en vervolgens het notitie blok uitvoeren.

>[!NOTE]
>Als u de **Time Series-prognose** hebt geselecteerd als uw model type in de vorige sectie, moet u extra configuraties maken. Het maken van een prognose biedt ook geen ondersteuning voor compatibiliteit met ONNX-modellen.

### <a name="create-a-run-directly"></a>Een rechtstreekse uitvoering maken

Als u uw geautomatiseerde machine learning direct wilt uitvoeren, selecteert u **Uitvoering starten**. U ziet een melding waarin wordt aangegeven dat de uitvoering wordt gestart. Vervolgens ziet u een andere melding die het succes aangeeft. U kunt ook de status in Azure Machine Learning controleren door de koppeling in de melding te selecteren.
![Schermopname van geslaagde melding.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-a-run-with-a-notebook"></a>Een run met een notitie blok maken

Selecteer **Openen in notebook** om een notebook te genereren. Selecteer vervolgens **Alle uitvoeren**. Dit biedt u ook de mogelijkheid om instellingen toe te voegen aan uw geautomatiseerde machine learning uitvoering.

![Scherm afbeelding van een notitie blok, met alles gemarkeerde uitvoeren.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

Nadat u de uitvoering hebt verzonden, ziet u een koppeling naar het experiment dat wordt uitgevoerd in de werk ruimte Azure Machine Learning van de notitieblok uitvoer. Selecteer de koppeling om de geautomatiseerde uitvoering te bewaken in Azure Machine Learning.
![Scherm opname van Azure Synapse Analytics met een koppeling gemarkeerd. ](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png) )

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Wizard voor scoren van het Machine learning-model voor toegewezen SQL-pools](tutorial-sql-pool-model-scoring-wizard.md)
- [Snelstart: een nieuwe gekoppelde Azure Machine Learning-service maken in Azure Synapse Analytics](quickstart-integrate-azure-machine-learning.md)
- [Mogelijkheden voor machine learning in Azure Synapse Analytics](what-is-machine-learning.md)