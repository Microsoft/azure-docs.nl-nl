---
title: 'Zelfstudie: Een model trainen met behulp van geautomatiseerde ML'
description: Zelfstudie over hoe u een machine learning-model traint zonder code in Azure Synapse met Apache Spark en geautomatiseerde ML.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 4967d5305b4b438f3baa6fca078d7b3169612590
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093397"
---
# <a name="tutorial-train-a-machine-learning-model-code-free-in-azure-synapse-with-apache-spark-and-automated-ml"></a>Zelfstudie: Een machine learning-model trainen zonder code in Azure Synapse met Apache Spark en geautomatiseerde ML

Leer hoe u eenvoudig uw gegevens in Spark-tabellen kunt verrijken met nieuwe machine learning-modellen die u traint met behulp van [geautomatiseerde ML in Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml).  Een gebruiker kan in Synapse gewoon een Spark-tabel selecteren in de Azure Synapse-werkruimte, en deze gebruiken als trainingsgegevensset om zonder code machine learning-modellen te bouwen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> - Machine learning-modellen trainen zonder code te gebruiken, in Azure Synapse Studio dat gebruikmaakt van geautomatiseerde ML in Azure Machine Learning. Het type model dat u traint, is afhankelijk van het probleem dat u wilt oplossen.

Als u geen Azure-abonnement hebt, [maakt u een gratis account voordat u begint](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Vereisten

- [Synapse Analytics-werkruimte](../get-started-create-workspace.md) met een ADLS Gen2-opslagaccount dat is geconfigureerd als de standaard opslag. U moet de **Inzender van de Storage Blob-gegevens** zijn van het ADLS Gen2-bestandssysteem waar u mee werkt.
- Spark-pool in uw Azure Synapse Analytics-werkruimte. Zie [Een Spark-pool maken in Azure Synapse](../quickstart-create-sql-pool-studio.md) voor meer informatie.
- Met Azure Machine Learning gekoppelde service in uw Azure Synapse Analytics-werkruimte. Raadpleeg [Een met Azure Machine Learning gekoppelde service maken in Azure Synapse](quickstart-integrate-azure-machine-learning.md) voor meer informatie.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/)

## <a name="create-a-spark-table-for-training-dataset"></a>Een Spark-tabel maken voor een trainingsgegevensset

Voor deze zelfstudie hebt u een Spark-tabel nodig. Met het volgende notebook wordt een Spark-tabel gemaakt.

1. Download het notebook [Create-Spark-Table-NYCTaxi- Data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149229)

1. Importeer het notebook in Azure Synapse Studio.
![Notebook importeren](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. Selecteer de Spark-pool die u wilt gebruiken, en klik op `Run all`. Als u dit notebook uitvoert, worden taxigegevens uit New York opgehaald uit de open gegevensset en opgeslagen in uw standaarddatabase in Spark.
![Alles uitvoeren](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. Nadat de uitvoering van het notebook is voltooid, wordt een nieuwe Spark-tabel gemaakt in de standaarddatabase in Spark. Ga naar de Data Hub en zoek de tabel met de naam met `nyc_taxi`.
![Spark-tabel](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="launch-automated-ml-wizard-to-train-a-model"></a>Wizard Geautomatiseerde ML starten om een model te trainen

Klik met de rechtermuisknop op de Spark-tabel die u in de vorige stap hebt gemaakt. Selecteer Machine Learning -> Verrijken met nieuw model om de wizard te openen.
![Wizard Geautomatiseerd ML starten](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

Er wordt een configuratiescherm weergegeven en u wordt gevraagd om de configuratiedetails op te geven om een experiment met geautomatiseerde ML te maken in Azure Machine Learning. Met deze uitvoering worden meerdere modellen getraind. Het beste model van een geslaagde uitvoering wordt geregistreerd in het Azure Machine Learning-modelregister:

![Uitvoering configureren - stap 1](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

- **Azure Machine Learning-werkruimte**: Er is een Azure Machine Learning-werkruimte vereist om de uitvoering van het geautomatiseerde ML-experiment te maken. U moet ook uw Azure Synapse-werkruimte koppelen aan de Azure Machine Learning-werkruimte met behulp van een [gekoppelde service](quickstart-integrate-azure-machine-learning.md). Zodra u beschikt over alle vereisten, kunt u de Azure Machine Learning-werkruimte opgeven die u wilt gebruiken voor deze geautomatiseerde ML-uitvoering.

- **Naam van experiment**: Geef de naam van het experiment op. Wanneer u een geautomatiseerde ML-uitvoering verzendt, geeft u een naam op voor het experiment. Informatie over de uitvoering wordt opgeslagen bij dit experiment in de Azure Machine Learning-werkruimte. Er wordt standaard een nieuw experiment gemaakt, en er wordt een suggestie voor een naam gegenereerd. U kunt echter ook de naam van een bestaand experiment opgeven.

- **Beste model**: Geef de naam op van het beste model uit de geautomatiseerde ML-uitvoering. Het beste model krijgt deze naam, en wordt na deze uitvoering automatisch opgeslagen in het Azure Machine Learning-modelregister. Bij een geautomatiseerde ML-uitvoering worden veel machine learning-modellen gemaakt. Deze modellen kunnen worden vergeleken en het beste model kan worden geselecteerd op basis van de primaire metrische gegevens die u in een latere stap selecteert.

- **Doelkolom**: Het model is getraind om dit te voorspellen. Kies de kolom die u wilt voorspellen.

- **Spark-pool**: De Spark-pool die u wilt gebruiken voor de uitvoering van het geautomatiseerde ML-experiment. De berekeningen worden uitgevoerd voor de pool die u opgeeft.

- **Spark-configuratiedetails**: Naast de Spark-pool hebt u ook de mogelijkheid om configuratiedetails voor de sessie op te geven.

In deze zelfstudie selecteren we de numerieke kolom `fareAmount` als doelkolom.

Klik op Doorgaan.

## <a name="choose-task-type"></a>Kies het taaktype

Selecteer het type machine learning-model voor het experiment, op basis van de vraag die u wilt beantwoorden. Omdat we `fareAmount` hebben geselecteerd als doelkolom, en dit een numerieke waarde is, selecteren we *Regressie*.

Klik op Doorgaan om aanvullende instellingen te configureren.

![Selectie van taaktype](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>Aanvullende configuraties

Als u het type *Classificatie* of *Regressie* selecteert, zijn de aanvullende configuraties:

- **Primaire metrische gegevens**: De metrische gegevens die worden gebruikt om te meten hoe goed het model is. Dit zijn de metrische gegevens die worden gebruikt om verschillende modellen te vergelijken die zijn gemaakt bij de geautomatiseerde ML-uitvoering. Deze geven aan welk model het beste heeft gepresteerd.

- **Duur van trainingstaak (in uren)** : De maximale tijdsduur, in uren, om een experiment uit te voeren en modellen te trainen. Opmerking: u kunt ook waarden opgeven die kleiner zijn dan 1. Bijvoorbeeld `0.5`.

- **Maximum aantal gelijktijdige herhalingen**: Vertegenwoordigt het maximum aantal herhalingen die parallel worden uitgevoerd.

- **Compatibiliteit met ONNX-modellen**: Als dit is ingeschakeld, worden de modellen die zijn getraind met geautomatiseerde ML, geconverteerd naar de ONNX-indeling. Dit is vooral relevant als u het model wilt gebruiken in SQL-pools van Azure Synapse.

Deze instellingen hebben allemaal een standaardwaarde die u kunt aanpassen.
![aanvullende configuraties](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

> Opmerking: als u Prognose voor tijdreeksen selecteert, zijn er meer configuraties vereist. Het maken van een prognose biedt ook geen ondersteuning voor compatibiliteit met ONNX-modellen.

Zodra alle vereiste configuraties zijn uitgevoerd, kunt u geautomatiseerde ML uitvoeren.

Er zijn twee manieren om een geautomatiseerde ML-uitvoering te starten in Azure Synapse. Als u geen code wilt gebruiken, kunt u **Direct uitvoering maken** kiezen. Als u liever werkt met code, kunt u **Openen in notebook** selecteren. U ziet dan de code waarmee de uitvoering wordt gemaakt en kunt het notebook uitvoeren.

### <a name="create-run-directly"></a>Direct uitvoering maken

Klik op Uitvoering starten om geautomatiseerde ML direct uit te voeren. U ziet een melding waarin wordt aangegeven dat de geautomatiseerde ML-uitvoering wordt gestart.

Nadat de geautomatiseerde ML-uitvoering is gestart, ziet u nog een melding over de geslaagde uitvoering. U kunt ook op de knop Melding klikken om de status van de verzending van de uitvoering te controleren.
Azure Machine Learning door te klikken op de koppeling in de melding over de geslaagde uitvoering.
![Melding over geslaagde uitvoering](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-run-with-notebook"></a>Uitvoering maken met notebook

Selecteer *Openen in notebook* om een notebook te genereren. Klik op *Alles uitvoeren* om het notebook uit te voeren.
Dit biedt u ook de mogelijkheid om extra instellingen toe te voegen aan de geautomatiseerde ML-uitvoering.

![Notebook openen](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

Nadat de uitvoering van het notebook is verzonden, verschijnt er een koppeling naar de uitvoering van het experiment in de Azure Machine Learning-werkruimte in de uitvoer van het notebook. U kunt op de koppeling klikken om de geautomatiseerde ML-uitvoering te bewaken in Azure Machine Learning.
![Notebook - alles uitvoeren](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png))

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Scoren van het Machine learning-model in toegewezen SQL-pools van Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md).
- [Snelstart: Een nieuwe gekoppelde Azure Machine Learning-service maken in Azure Synapse](quickstart-integrate-azure-machine-learning.md)
- [Mogelijkheden voor machine learning in Azure Synapse Analytics](what-is-machine-learning.md)
