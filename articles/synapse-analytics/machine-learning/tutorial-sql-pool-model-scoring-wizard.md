---
title: 'Zelfstudie: Wizard voor scoren van het Machine learning-model voor toegewezen SQL-pools'
description: Zelfstudie voor het gebruik van de wizard voor scoren van het Machine Learning-model voor het verrijken van gegevens in toegewezen SQL-pools.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: d8db9257ad6eed98b39cd2c9a52351f013453365
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98935229"
---
# <a name="tutorial-machine-learning-model-scoring-wizard-preview-for-dedicated-sql-pools"></a>Zelfstudie: Wizard voor scoren van het Machine learning-model voor toegewezen SQL-pools

Meer informatie over hoe u uw gegevens in toegewezen SQL-pools eenvoudig kunt verrijken met voorspellende Machine Learning-modellen. De modellen die uw gegevensanalisten maken, zijn nu eenvoudig toegankelijk voor gegevensprofessionals voor predictive analytics. Een Data Professional in azure Synapse Analytics kan eenvoudigweg een model selecteren uit het Azure Machine Learning model register voor implementatie in azure Synapse SQL-groepen en voor spellingen starten om de gegevens te verrijken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> - Train een voorspellend machine learning model en registreer het model in het REGI ster Azure Machine Learning model.
> - Gebruik de wizard SQL score voor het starten van voor spellingen in een toegewezen SQL-groep.

Als u geen Azure-abonnement hebt, [maakt u een gratis account voordat u begint](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Vereisten

- [Azure Synapse Analytics-werk ruimte](../get-started-create-workspace.md) met een Azure data Lake Storage Gen2 opslag account geconfigureerd als de standaard opslag. U moet de Inzender voor *gegevens* van de opslag-blob van het data Lake Storage Gen2 bestands systeem waarmee u samenwerkt.
- Toegewezen SQL-pool in uw Azure Synapse Analytics-werkruimte. Zie [Een toegewezen SQL-pool maken](../quickstart-create-sql-pool-studio.md) voor meer informatie.
- Met Azure Machine Learning gekoppelde service in uw Azure Synapse Analytics-werkruimte. Raadpleeg [Een met Azure Machine Learning gekoppelde service maken in Azure Synapse](quickstart-integrate-azure-machine-learning.md) voor meer informatie.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="train-a-model-in-azure-machine-learning"></a>Een model trainen in Azure Machine Learning

Controleer voordat u begint of uw versie van sklearn 0.20.3 is.

Controleer voordat u alle cellen in het notitie blok uitvoert of het reken exemplaar wordt uitgevoerd.

![Scherm afbeelding die de verificatie van Azure Machine Learning Compute weergeeft.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00b.png)

1. Ga naar uw Azure Machine Learning-werk ruimte.

1. Download [Voorspellen NYC Taxi Tips.ipynb](https://go.microsoft.com/fwlink/?linkid=2144301).

1. Open de werk ruimte Azure Machine Learning in [Azure machine learning Studio](https://ml.azure.com).

1. Ga naar **notitie blokken**  >  **bestanden uploaden**. Selecteer vervolgens het bestand **NYCe taxi tips. ipynb** dat u hebt gedownload en geüpload.
   ![Scherm afbeelding van de knop voor het uploaden van een bestand.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00a.png)

1. Nadat het notitie blok is geüpload en geopend, selecteert u **alle cellen uitvoeren**.

   Een van de cellen kan mislukken en vraagt u om te verifiëren bij Azure. Bekijk deze in de uitvoer van de cel en verifieer in uw browser door de koppeling te volgen en de code in te voeren. Voer vervolgens het notitie blok opnieuw uit.

1. Het notitie blok traint een ONNX-model en registreert dit met MLflow. Ga naar **modellen** om te controleren of het nieuwe model juist is geregistreerd.
   ![Scherm opname van het model in het REGI ster.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00c.png)

1. Als u het notebook uitvoert, worden de testgegevens geëxporteerd naar een CSV-bestand. Download het CSV-bestand naar uw lokale systeem. Later importeert u het CSV-bestand in een toegewezen SQL-groep en gebruikt u de gegevens om het model te testen.

   Het CSV-bestand wordt gemaakt in dezelfde map als het notebook-bestand. Selecteer **vernieuwen** in Verkenner als u dit niet meteen ziet.

   ![Scherm opname van het C S V-bestand.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00d.png)

## <a name="launch-predictions-with-the-sql-scoring-wizard"></a>Voor spellingen starten met de wizard SQL Score

1. Open de Azure Synapse-werk ruimte met Synapse Studio.

1. Ga naar aan **gegevens**  >  **gekoppelde**  >  **opslag accounts**. Upload `test_data.csv` naar de standaard opslagaccount.

   ![Scherm opname van de selecties voor het uploaden van gegevens.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00a.png)

1. Ga naar **Ontwikkel** > **SQL-scripts**. Maak een nieuw SQL-script om `test_data.csv` in uw toegewezen SQL-pool te laden.

   > [!NOTE]
   > Werk de bestands-URL in dit script bij voordat u deze uitvoert.

   ```SQL
   IF NOT EXISTS (SELECT * FROM sys.objects WHERE NAME = 'nyc_taxi' AND TYPE = 'U')
   CREATE TABLE dbo.nyc_taxi
   (
       tipped int,
       fareAmount float,
       paymentType int,
       passengerCount int,
       tripDistance float,
       tripTimeSecs bigint,
       pickupTimeBin nvarchar(30)
   )
   WITH
   (
       DISTRIBUTION = ROUND_ROBIN,
       CLUSTERED COLUMNSTORE INDEX
   )
   GO
   
   COPY INTO dbo.nyc_taxi
   (tipped 1, fareAmount 2, paymentType 3, passengerCount 4, tripDistance 5, tripTimeSecs 6, pickupTimeBin 7)
   FROM '<URL to linked storage account>/test_data.csv'
   WITH
   (
       FILE_TYPE = 'CSV',
       ROWTERMINATOR='0x0A',
       FIELDQUOTE = '"',
       FIELDTERMINATOR = ',',
       FIRSTROW = 2
   )
   GO
   
   SELECT TOP 100 * FROM nyc_taxi
   GO
   ```

   ![Gegevens opladen naar een toegewezen SQL-pool](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00b.png)

1. Ga naar **Gegevens** > **Werkruimte**. Open de SQL wizard voor scoren door met de rechtermuisknop te klikken op de tabel van de toegewezen SQL-pool. Selecteer **Machine Learning** > **Verrijken met bestaand model**.

   > [!NOTE]
   > De optie machine learning wordt alleen weer gegeven als u een gekoppelde service hebt gemaakt voor Azure Machine Learning. (Zie de [vereisten](#prerequisites) aan het begin van deze zelf studie.)

   ![Scherm afbeelding waarin de optie machine learning wordt weer gegeven.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00c.png)

1. Selecteer een gekoppelde Azure Machine Learning-werkruimte in de vervolgkeuzelijst. Met deze stap wordt een lijst met machine learning modellen geladen uit het model register van de gekozen Azure Machine Learning werk ruimte. Op dit moment worden alleen ONNX-modellen ondersteund. in deze stap worden alleen ONNX modellen weer gegeven.

1. Selecteer het model dat u zojuist hebt getraind en selecteer vervolgens **door gaan**.

   ![Scherm opname van het selecteren van het Azure Machine Learning model.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00d.png)

1. De tabel kolommen toewijzen aan de model invoer en de model uitvoer opgeven. Als het model is opgeslagen in de MLflow-indeling en de model handtekening is gevuld, wordt de toewijzing automatisch voor u uitgevoerd met behulp van een logica op basis van de gelijkenis van namen. De interface ondersteunt ook handmatige toewijzing.

   Selecteer **Doorgaan**.

   ![Scherm opname van tabel-naar-model-toewijzing.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00e.png)

1. De gegenereerde T-SQL-code wordt verpakt in een opgeslagen procedure. Daarom moet u de naam van een opgeslagen procedure opgeven. Het model binary, inclusief meta gegevens (versie, beschrijving en andere informatie), wordt fysiek gekopieerd van Azure Machine Learning naar een toegewezen SQL-groeps tabel. U moet dus opgeven in welke tabel u het model wilt opslaan. 

   U kunt een **bestaande tabel** kiezen of een **nieuwe maken**. Wanneer u klaar bent, selecteert u **model + script openen** om het model te implementeren en een T-SQL-Voorspellings script te genereren.

   ![Scherm opname van de selecties voor het maken van een opgeslagen procedure.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00f.png)

1. Nadat het script is gegenereerd, selecteert u uitvoeren om de score **uit** te voeren en voor spellingen te verkrijgen.

   ![Scherm opname van scores en voor spellingen.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00g.png)

## <a name="next-steps"></a>Volgende stappen

- [Snelstart: Een nieuwe gekoppelde Azure Machine Learning-service maken in Azure Synapse](quickstart-integrate-azure-machine-learning.md)
- [Mogelijkheden voor machine learning in Azure Synapse Analytics](what-is-machine-learning.md)
