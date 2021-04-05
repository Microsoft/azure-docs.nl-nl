---
title: Gegevens analyseren met Azure Machine Learning
description: Gebruik Azure Machine Learning om een voorspellend machine learning model te maken op basis van gegevens die zijn opgeslagen in azure Synapse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 76a154d3a137017f374247308a3980d598698246
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678656"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Gegevens analyseren met Azure Machine Learning

In deze zelf studie wordt [Azure machine learning Designer](../../machine-learning/concept-designer.md) gebruikt voor het bouwen van een voorspellend machine learning model. Het model is gebaseerd op de gegevens die zijn opgeslagen in azure Synapse. Het scenario voor de zelf studie is om te voors pellen of een klant waarschijnlijk een fiets koopt of niet, dat wil zeggen dat de fiets winkel een gerichte marketing campagne kan bouwen.

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* een SQL-pool vooraf geladen met AdventureWorksDW-voorbeeld gegevens. Zie [een SQL-groep maken](create-data-warehouse-portal.md) en kiezen om de voorbeeld gegevens te laden om deze SQL-groep in te richten. Als u al een Data Warehouse hebt, maar geen voorbeeld gegevens hebt, kunt u [hand matig voorbeeld gegevens laden](./load-data-from-azure-blob-storage-using-copy.md).
* een Azure machine learning-werk ruimte. Volg [deze zelf studie](../../machine-learning/how-to-manage-workspace.md) om een nieuwe te maken.

## <a name="get-the-data"></a>De gegevens ophalen

De gebruikte gegevens bevindt zich in de weer gave dbo. vTargetMail in AdventureWorksDW. Als u gegevens opslag in deze zelf studie wilt gebruiken, worden deze eerst geëxporteerd naar Azure Data Lake Storage account, omdat Azure Synapse momenteel geen gegevens sets ondersteunt. Azure Data Factory kunnen worden gebruikt om gegevens uit het Data Warehouse te exporteren naar Azure Data Lake Storage met behulp van de [Kopieer activiteit](../../data-factory/copy-activity-overview.md). Gebruik de volgende query voor het importeren:

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Wanneer de gegevens beschikbaar zijn in Azure Data Lake Storage, worden data stores in Azure Machine Learning gebruikt om [verbinding te maken met Azure Storage-services](../../machine-learning/how-to-access-data.md). Volg de onderstaande stappen om een gegevens opslag en een bijbehorende gegevensset te maken:

1. Start Azure Machine Learning Studio vanuit Azure Portal of Meld u aan bij [Azure machine learning Studio](https://ml.azure.com/).

1. Klik op **gegevens opslag** in het linkerdeel venster van de sectie **beheren** en klik vervolgens op **nieuwe gegevens opslag**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/datastores-tab.png" alt-text="Scherm opname van het linkerdeel venster van Azure Machine Learning interface":::

1. Geef een naam op voor de gegevens opslag, selecteer het type ' Azure Blob Storage ', geef locatie en referenties op. Klik vervolgens op **maken**.

1. Klik vervolgens op **gegevens sets** in het linkerdeel venster van de sectie **assets** . Selecteer **gegevensset maken** met de optie **uit gegevens opslag**.

1. Geef de naam van de gegevensset op en selecteer het type dat in de **tabel** moet worden getypt. Klik vervolgens op **volgende** om door te gaan.

1. Selecteer in de **sectie een gegevens archief selecteren of maken** de optie **eerder het gegevens archief is gemaakt**. Selecteer de gegevens opslag die u eerder hebt gemaakt. Klik op volgende en geef het pad en de bestands instellingen op. Zorg ervoor dat u de kolomkop opgeeft als de bestanden er een bevatten.

1. Klik tot slot op **maken** om de gegevensset te maken.

## <a name="configure-designer-experiment"></a>Ontwerp van Designer configureren

Volg vervolgens de stappen hieronder voor de ontwerp configuratie:

1. Klik op het tabblad **Designer** in het linkerdeel venster van de sectie **Auteur** .

1. Selecteer **eenvoudig te gebruiken vooraf gedefinieerde modules** om een nieuwe pijp lijn te maken.

1. Geef in het deel venster instellingen aan de rechter kant de naam van de pijp lijn op.

1. Selecteer ook een doel berekenings cluster voor het hele experiment in instellingen knop naar een eerder ingericht cluster. Sluit het deel venster instellingen.

## <a name="import-the-data"></a>Gegevens importeren

1. Selecteer de subtabblad **gegevens sets** in het linkerdeel venster onder het zoekvak.

1. Sleep de gegevensset die u eerder hebt gemaakt, naar het canvas.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/import-dataset.png" alt-text="Scherm opname van module gegevensset op het canvas.":::

## <a name="clean-the-data"></a>De gegevens opschonen

Als u de gegevens wilt opschonen, verwijdert u kolommen die niet relevant zijn voor het model. Volg de onderstaande stappen:

1. Selecteer de **modules** subtabblad in het linkerdeel venster.

1. Sleep de module **select columns in dataset** onder **Data Transformation < manipulatie** in het canvas. Deze module verbinden met de module **gegevensset** .

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-in.png" alt-text="Scherm afbeelding van de module voor kolom selectie op het canvas." lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-out.png":::

1. Klik op de module om het eigenschappen venster te openen. Klik op kolom bewerken om op te geven welke kolommen u wilt neerzetten.

1. Sluit twee kolommen uit: CustomerAlternateKey en GeographyKey. Klik op **Opslaan**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/drop-columns.png" alt-text="Scherm opname waarin kolommen worden weer gegeven die worden verwijderd.":::

## <a name="build-the-model"></a>Het model bouwen

De gegevens worden gesplitst 80-20:80% om een machine learning model te trainen en 20% om het model te testen. ' Twee klasse '-algoritmen worden gebruikt in dit probleem met de binaire classificatie.

1. Sleep de module **Split data** naar het canvas.

1. Voer in het deel venster Eigenschappen 0,8 **in voor het gedeelte van rijen in de eerste uitvoer gegevensset**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/split-data.png" alt-text="Scherm afbeelding met de Splits ratio van 0,8.":::

1. Sleep de module **Two-Class Boosted Decision Tree** (Beslissingsstructuur op basis van twee klassen) naar het canvas.

1. Sleep de module **Train model** naar het canvas. Geef invoer op door deze te verbinden met de **twee klasse versterkte beslissings structuur** (ml-algoritme) en **gesplitste gegevens** (gegevens voor het trainen van de algoritmen op) modules.

1. Selecteer in het deel venster Eigenschappen voor Train model model de optie kolom bewerken bij **Label kolom** . Selecteer de kolom **BikeBuyer** als de kolom die u wilt voors pellen en selecteer **Opslaan**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/label-column.png" alt-text="Scherm opname met een label kolom, BikeBuyer, geselecteerd.":::

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/train-model.png" alt-text="Scherm afbeelding met Train model module die is verbonden met Two-Class versterkte beslissings structuur en gesplitste gegevens modules.":::

## <a name="score-the-model"></a>Het model beoordelen

Nu kunt u testen hoe het model presteert op test gegevens. Er worden twee verschillende algoritmen vergeleken om te zien welke een betere resultaten heeft. Volg de onderstaande stappen:

1. Sleep de module **score model** naar het canvas en verbind deze met een **Train model** en **Splits gegevens** modules.

1. Sleep de **gemiddelde Perceptron van de twee klassen Bayes** naar het canvas van het experiment. U gaat vergelijken hoe dit algoritme presteert in vergelijking met de Two-Class versterkte beslissings structuur.

1. Kopieer en plak de modules **Train model** en **score model** op het canvas.

1. Sleep het model **Evaluate Model** (Model evalueren) naar het canvas om de twee algoritmen te vergelijken.

1. Klik op **verzenden** om de pijplijn uitvoering in te stellen.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-in.png" alt-text="Scherm afbeelding van alle resterende modules op het canvas." lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-out.png":::

1. Zodra de uitvoering is voltooid, klikt u met de rechter muisknop op de module **Evaluate model** en klikt u op **evaluatie resultaten visualiseren**.

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/result-visualize-zoomed-out.png" alt-text="Scherm opname van de resultaten.":::

De gegeven metrische gegevens zijn de ROC-curve, het diagram met precisie terughalen en de kromme voor liften. Bekijk deze metrische gegevens om te zien dat het eerste model beter is uitgevoerd dan het tweede. Klik met de rechter muisknop op de module score model om te zien wat het eerste model is voorspeld en op gescoorde gegevensset visualiseren om de voorspelde resultaten te zien.

U ziet twee meer kolommen die worden toegevoegd aan de gegevensset voor de test.

* Scored Probabilities (Berekende kansen): de waarschijnlijkheid dat een klant een fiets koopt.
* Scored Labels (Berekende Labels): de classificatie die door het model is uitgevoerd: fietskoper (1) of niet (0). Deze drempelwaarde voor waarschijnlijkheid voor labeling is ingesteld op 50% en kan worden aangepast.

Vergelijk de kolom BikeBuyer (werkelijk) met de gescoorde labels (voor spelling) om te zien hoe goed het model is uitgevoerd. Vervolgens kunt u dit model gebruiken om voor spellingen te doen voor nieuwe klanten. U kunt [dit model publiceren als een webservice](../../machine-learning/tutorial-designer-automobile-price-deploy.md) of resultaten terugschrijven naar Azure Synapse.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg [Introduction to machine learning on Azure](../../machine-learning/overview-what-is-azure-ml.md)voor meer informatie over Azure machine learning.

Meer informatie over de ingebouwde Score in het data [Warehouse.](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true)