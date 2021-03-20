---
title: 'Zelfstudie: Sentimentanalyse met Cognitive Services'
description: Meer informatie over het gebruik van Cognitive Services voor sentiment-analyse in azure Synapse Analytics
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 08d5e53facce172c2287c2e341895f0ee38571f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943707"
---
# <a name="tutorial-sentiment-analysis-with-cognitive-services-preview"></a>Zelf studie: sentiment analyse met Cognitive Services (preview-versie)

In deze zelf studie leert u hoe u uw gegevens in azure Synapse Analytics eenvoudig kunt verrijken met [azure Cognitive Services](../../cognitive-services/index.yml). U gebruikt de [Text Analytics](../../cognitive-services/text-analytics/index.yml) mogelijkheden om analyse van sentiment uit te voeren. 

Een gebruiker in azure Synapse kan eenvoudigweg een tabel selecteren die een tekst kolom bevat die moet worden verrijkt met gevoel. Deze gevoel kunnen positieve, negatieve, gemengde of neutrale zijn. Er wordt ook een kans geretourneerd.

In deze zelfstudie komt het volgende aan bod:

> [!div class="checklist"]
> - Stappen voor het ophalen van een Spark-tabel gegevensset die een tekst kolom bevat voor sentiment analyse.
> - Met behulp van een wizard-ervaring in azure Synapse naar verrijkt u gegevens met behulp van Text Analytics in Cognitive Services.

Als u geen Azure-abonnement hebt, [maakt u een gratis account voordat u begint](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Vereisten

- [Azure Synapse Analytics-werk ruimte](../get-started-create-workspace.md) met een Azure data Lake Storage Gen2 opslag account geconfigureerd als de standaard opslag. U moet de Inzender voor *gegevens* van de opslag-blob van het data Lake Storage Gen2 bestands systeem waarmee u samenwerkt.
- Spark-pool in uw Azure Synapse Analytics-werkruimte. Zie [Een Spark-pool maken in Azure Synapse](../quickstart-create-sql-pool-studio.md) voor meer informatie.
- De stappen voorafgaand aan de configuratie die in de zelf studie worden beschreven, [configureren Cognitive Services in azure Synapse](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="create-a-spark-table"></a>Een Spark-tabel maken

U hebt een Spark-tabel nodig voor deze zelf studie.

1. Down load het [FabrikamComments.csv](https://github.com/Kaiqb/KaiqbRepo0731190208/blob/master/CognitiveServices/TextAnalytics/FabrikamComments.csv) -bestand, dat een gegevensset bevat voor tekst analyse. 

1. Upload het bestand naar uw Azure Synapse-opslag account in Data Lake Storage Gen2.
  
   ![Scherm opname van de selecties voor het uploaden van gegevens.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00a.png)

1. Maak een Spark-tabel vanuit het CSV-bestand door met de rechter muisknop op het bestand te klikken en **Nieuw notitie blok**  >  **maken Spark-tabel** te selecteren.

   ![Scherm opname van selecties voor het maken van een Spark-tabel.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00b.png)

1. Geef de tabel een naam in de codecel en voer de notebook uit in een Spark-pool. Vergeet niet in te stellen `header=True` .

   ![Scherm afbeelding waarin een notitie blok wordt weer gegeven.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00c.png)

   ```python
   %%pyspark
   df = spark.read.load('abfss://default@azuresynapsesa.dfs.core.windows.net/data/FabrikamComments.csv', format='csv'
   ## If a header exists, uncomment the line below
   , header=True
   )
   df.write.mode("overwrite").saveAsTable("default.YourTableName")
   ```

## <a name="open-the-cognitive-services-wizard"></a>De wizard Cognitive Services openen

1. Klik met de rechter muisknop op de Spark-tabel die u in de vorige procedure hebt gemaakt. Selecteer **machine learning**  >  **verrijken met bestaand model** om de wizard te openen.

   ![Scherm opname van de selecties voor het openen van de Score wizard.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00d.png)

2. Er wordt een configuratie scherm weer gegeven en u wordt gevraagd om een Cognitive Services model te selecteren. Selecteer **tekst analyse-sentimentanalyse**.

   ![Scherm opname van de selectie van een Cognitive Services model.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00e.png)

## <a name="provide-authentication-details"></a>Verificatiegegevens opgeven

Als u zich wilt verifiëren bij Cognitive Services, moet u verwijzen naar het geheim voor uw sleutel kluis. De volgende invoer is afhankelijk van de [vereiste stappen](tutorial-configure-cognitive-services-synapse.md) die u vóór dit punt moet volt ooien.

- **Azure-abonnement**: Selecteer het Azure-abonnement waarvan uw sleutel kluis deel uitmaakt.
- **Cognitive Services account**: geef de Text Analytics resource op waarmee u verbinding maakt.
- **Azure Key Vault gekoppelde service**: u hebt een gekoppelde service voor uw Text Analytics resource gemaakt als onderdeel van de vereiste stappen. Selecteer deze hier.
- **Geheime naam**: Voer de naam in van het geheim in de sleutel kluis die de sleutel bevat die moet worden geverifieerd bij uw Cognitive Services-resource.

![Scherm opname van de verificatie gegevens voor een sleutel kluis.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00f.png)

## <a name="configure-sentiment-analysis"></a>Sentiment-analyse configureren

Configureer vervolgens de sentiment-analyse. Selecteer de volgende details:
- **Taal**: Selecteer **Engels** als taal van de tekst waarvoor u sentiment analyse wilt uitvoeren.
- **Tekst kolom**: Selecteer **Opmerking (teken reeks)** als de tekst kolom in uw gegevensset die u wilt analyseren om de sentiment te bepalen.

Wanneer u klaar bent, selecteert u **notitie blok openen**. Hiermee wordt een notitie blok voor u gegenereerd met PySpark-code waarmee de sentiment-analyse wordt uitgevoerd met Azure Cognitive Services.

![Scherm opname van de selecties voor het configureren van sentiment-analyse.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00g.png)

## <a name="run-the-notebook"></a>Het notitieblok uitvoeren

Het notitie blok dat u zojuist hebt geopend, maakt gebruik van de [mmlspark-bibliotheek](https://github.com/Azure/mmlspark) om verbinding te maken met Cognitive Services. Met de Azure Key Vault gegevens die u hebt ingevoerd, kunt u veilig vanuit deze ervaring naar uw geheimen verwijzen zonder dat u deze hoeft weer te geven.

U kunt nu alle cellen uitvoeren om uw gegevens te verrijken met gevoel. Selecteer **alles uitvoeren**. 

De gevoel worden geretourneerd als **positief**, **negatief**, **neutraal** of **gemengd**. U krijgt ook waarschijnlijkheid per sentiment. Meer [informatie over sentiment-analyse in cognitive Services](../../cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md).

![Scherm opname van de sentiment analyse.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00h.png)

## <a name="next-steps"></a>Volgende stappen
- [Zelfstudie: Anomaliedetectie met Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Zelf studie: score model voor machine learning-modellen in azure Synapse dedicated SQL-groepen](tutorial-sql-pool-model-scoring-wizard.md)
- [Mogelijkheden voor machine learning in Azure Synapse Analytics](what-is-machine-learning.md)