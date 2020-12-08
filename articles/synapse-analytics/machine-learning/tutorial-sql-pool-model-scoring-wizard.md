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
ms.openlocfilehash: 605a5f2f74ca6fb46d851c41f60001c48a95be95
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450884"
---
# <a name="tutorial-machine-learning-model-scoring-wizard-preview-for-dedicated-sql-pools"></a>Zelfstudie: Wizard voor scoren van het Machine learning-model voor toegewezen SQL-pools

Meer informatie over hoe u uw gegevens in toegewezen SQL-pools eenvoudig kunt verrijken met voorspellende Machine Learning-modellen.  De modellen die uw gegevensanalisten maken, zijn nu eenvoudig toegankelijk voor gegevensprofessionals voor predictive analytics. Een gegevensprofessional kan in Synapse eenvoudigweg een model selecteren uit het register voor Azure Machine Learning-modellen voor implementatie in Synapse SQL-pools en voorspellingen starten om de gegevens te verrijken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> - Een voorspellend Machine Learning-model trainen en het model registreren in het register met Azure Machine Learning-modellen
> - De SQL wizard voor scoren gebruiken om voorspellingen te starten in toegewezen SQL-pool

Als u geen Azure-abonnement hebt, [maakt u een gratis account voordat u begint](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Vereisten

- [Synapse Analytics-werkruimte](../get-started-create-workspace.md) met een ADLS Gen2-opslagaccount dat is geconfigureerd als de standaard opslag. U moet de **Inzender van de Storage Blob-gegevens** zijn van het ADLS Gen2-bestandssysteem waar u mee werkt.
- Toegewezen SQL-pool in uw Azure Synapse Analytics-werkruimte. Zie [Een toegewezen SQL-pool maken](../quickstart-create-sql-pool-studio.md) voor meer informatie.
- Met Azure Machine Learning gekoppelde service in uw Azure Synapse Analytics-werkruimte. Zie [Een Azure Machine Learning-gekoppelde service maken in Synapse](quickstart-integrate-azure-machine-learning.md) voor meer informatie.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/)

## <a name="train-a-model-in-azure-machine-learning"></a>Een model trainen in Azure Machine Learning

Controleer voordat u begint of uw versie van **sklearn** 0.20.3 is.

Voordat u alle cellen in het notebook uitvoert, controleert u of het rekenproces wordt uitgevoerd.

![AML-berekening verifiëren](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00b.png)

1. Ga naar uw Azure Machine Learning-werkruimte.

1. Download [Voorspellen NYC Taxi Tips.ipynb](https://go.microsoft.com/fwlink/?linkid=2144301).

1. Start de Azure Machine Learning-werkruimte in de [Azure Machine Learning-studio](https://ml.azure.com).

1. Ga naar **Notebooks** en klik op **Bestanden uploaden**, selecteer 'Voorspellen NYC Taxi Tips.ipynb' die u hebt gedownload en upload het bestand.
   ![Bestand uploaden](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00a.png)

1. Nadat het notebook is geüpload en geopend, klikt u op **Alle cellen uitvoeren**.

   Een van de cellen kan mislukken en u vragen om te verifiëren bij Azure. Let hierop in de uitvoer van de cellen en verifieer in uw browser door de link te volgen en de code in te voeren. Voer het notebook daarna opnieuw uit.

1. Het notebook traint een ONNX-model en registreert dit met MLFlow. Ga naar **Modellen** om te controleren of het nieuwe model juist is geregistreerd.
   ![Model in register](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00c.png)

1. Als u het notebook uitvoert, worden de testgegevens geëxporteerd naar een CSV-bestand. Download het CSV-bestand naar uw lokale systeem. Later gaat u het CSV-bestand importeren in een toegewezen SQL-pool en de gegevens gebruiken om het model te testen.

   Het CSV-bestand wordt gemaakt in dezelfde map als het notebook-bestand. Klik op 'Vernieuwen' in de bestandenverkenner als u dit niet meteen ziet.

   ![CSV-bestand](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00d.png)

## <a name="launch-predictions-with-sql-scoring-wizard"></a>Voorspellingen starten met SQL wizard voor scoren

1. Open de Synapse-werkruimte met Synapse Studio.

1. Navigeer naar **Gegevens** -> **gekoppelde** -> **opslagaccounts**. Upload `test_data.csv` naar de standaard opslagaccount.

   ![Gegevens uploaden](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00a.png)

1. Ga naar **Ontwikkel** -> **SQL-scripts**. Maak een nieuw SQL-script om `test_data.csv` in uw toegewezen SQL-pool te laden.

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

1. Ga naar **Gegevens** -> **Werkruimte**. Open de SQL wizard voor scoren door met de rechtermuisknop te klikken op de tabel van de toegewezen SQL-pool. Selecteer **Machine Learning** -> **Verrijken met bestaand model**.

   > [!NOTE]
   > De optie machine learning wordt alleen weergegeven als u een gekoppelde service hebt gemaakt voor Azure Machine Learning (zie **Vereisten** aan het begin van deze zelfstudie).

   ![Machine Learning-optie](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00c.png)

1. Selecteer een gekoppelde Azure Machine Learning-werkruimte in de vervolgkeuzelijst. Hiermee wordt een lijst met Machine Learning-modellen geladen vanuit het modelregister van de gekozen Azure Machine Learning-werkruimte. Op dit moment worden alleen ONNX-modellen ondersteund en dus worden alleen ONNX-modellen weergegeven.

1. Selecteer het model dat u zojuist hebt getraind en klik op **Doorgaan**.

   ![Selecteer een Azure Machine Learning-model](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00d.png)

1. Wijs vervolgens de tabelkolommen toe aan de modelinvoer en geef de modeluitvoer op. Als het model is opgeslagen in de MLFlow-indeling en de modelhandtekening is gevuld, wordt de toewijzing automatisch voor u uitgevoerd met behulp van een logica op basis van de gelijkenis van namen. De interface ondersteunt ook handmatige toewijzing.

   Klik op **Doorgaan**.

   ![Tabel naar modeltoewijzing](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00e.png)

1. De gegenereerde T-SQL-code wordt verpakt in een opgeslagen procedure. Daarom moet u een opgeslagen procedure een naam geven. Het model binair inclusief metagegevens (versie, beschrijving, enzovoort) wordt fysiek gekopieerd van Azure Machine Learning naar een tabel van een toegewezen SQL-pool. U moet dus opgeven in welke tabel u het model wilt opslaan. U kunt kiezen voor 'Bestaande tabel gebruiken' of 'Nieuwe tabel maken'. Als u klaar bent, klikt u op **Model implementeren + editor openen** om het model te implementeren en een T-SQL-voorspellingsscript te genereren.

   ![Procedure maken](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00f.png)

1. Zodra het script is gegenereerd, klikt u op 'Uitvoeren' om het scoren uit te voeren en voorspellingen te verkrijgen.

   ![Voorspellingen uitvoeren](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00g.png)

## <a name="next-steps"></a>Volgende stappen

- [Snelstart: Een nieuwe gekoppelde Azure Machine Learning-service maken in Synapse](quickstart-integrate-azure-machine-learning.md)
- [Mogelijkheden voor machine learning in Azure Synapse Analytics](what-is-machine-learning.md)
