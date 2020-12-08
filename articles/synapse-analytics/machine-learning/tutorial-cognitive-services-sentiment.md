---
title: 'Zelfstudie: Sentimentanalyse met Cognitive Services'
description: Zelfstudie voor het gebruik van Cognitive Services voor sentimentanalyse in Synapse
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 1b407cbee5218149f794ab125ac058e32b422558
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96464366"
---
# <a name="tutorial-sentiment-analysis-with-cognitive-services-preview"></a>Zelfstudie: Sentimentanalyse met Cognitive Services (preview-versie)

In deze zelfstudie leert u hoe u uw gegevens in Azure Synapse eenvoudig kunt verrijken met [Cognitive Services](https://go.microsoft.com/fwlink/?linkid=2147492). We maken gebruik van de mogelijkheden van [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) om een sentimentanalyse uit te voeren. Een gebruiker in Azure Synapse kan eenvoudigweg een tabel selecteren die een tekstkolom bevat die moet worden verrijkt met sentimenten. Deze sentimenten kunnen positief, negatief, gemengd of neutraal en een waarschijnlijkheid zijn.

In deze zelfstudie komt het volgende aan bod:

> [!div class="checklist"]
> - Stappen voor het ophalen van een Spark-tabelgegevensset met een tekstkolom voor sentimentanalyse.
> - Gebruik de wizard in Azure Synapse om gegevens te verrijken met behulp van Text Analytics Cognitive Services.

Als u geen Azure-abonnement hebt, [maakt u een gratis account voordat u begint](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Vereisten

- [Azure Synapse Analytics-werkruimte](../get-started-create-workspace.md) met een ADLS Gen2-opslagaccount dat is geconfigureerd als de standaardopslag. U moet de **Inzender van de Storage Blob-gegevens** zijn van het ADLS Gen2-bestandssysteem waar u mee werkt.
- Spark-pool in uw Azure Synapse Analytics-werkruimte. Zie [Een Spark-pool maken in Azure Synapse](../quickstart-create-sql-pool-studio.md) voor meer informatie.
- Voordat u deze zelfstudie kunt gebruiken, moet u ook de stappen voorafgaand aan de configuratie voltooien die in deze zelfstudie worden beschreven. [Cognitive Services in Azure Synapse configureren](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/)

## <a name="create-a-spark-table"></a>Een Spark-tabel maken

Voor deze zelfstudie hebt u een Spark-tabel nodig.

1. Download het volgende CSV-bestand met een gegevensset voor tekstanalyse: [FabrikamComments.csv](https://github.com/Kaiqb/KaiqbRepo0731190208/blob/master/CognitiveServices/TextAnalytics/FabrikamComments.csv)

1. Upload het bestand naar uw Azure Synapse ADLSGen2-opslagaccount.
![Gegevens uploaden](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00a.png)

1. Maak een Spark-tabel vanuit het CSV-bestand door met de rechtermuisknop op het bestand te klikken en **Nieuwe notebook -> Spark-tabel** te selecteren.
![Spark-tabel maken](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00b.png)

1. Geef de tabel een naam in de codecel en voer de notebook uit in een Spark-pool. Vergeet niet om "header = True" in te stellen.
![Notebook uitvoeren](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00c.png)

```python
%%pyspark
df = spark.read.load('abfss://default@azuresynapsesa.dfs.core.windows.net/data/FabrikamComments.csv', format='csv'
## If header exists uncomment line below
, header=True
)
df.write.mode("overwrite").saveAsTable("default.YourTableName")
```

## <a name="launch-cognitive-services-wizard"></a>De wizard van Cognitive Services starten

1. Klik met de rechtermuisknop op de Spark-tabel die u in de vorige stap hebt gemaakt. Selecteer 'Machine Learning -> Verrijken met bestaand model' om de wizard te openen.
![Wizard voor scoren starten](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00d.png)

2. Er wordt een configuratievenster weergegeven waarin u wordt gevraagd om een Cognitive Services-model te selecteren. Selecteer Text Analytics - Sentimentanalyse.

![Wizard voor scoren starten - stap 1](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00e.png)

## <a name="provide-authentication-details"></a>Verificatiegegevens opgeven

Als u zich wilt verifiëren bij Cognitive Services, moet u verwijzen naar het geheim dat moet worden gebruikt in uw Key Vault. De onderstaande invoer is afhankelijk van [vereiste stappen](tutorial-configure-cognitive-services-synapse.md) die u vóór deze stap moet hebben voltooid.

- **Azure-abonnement**: selecteer het Azure-abonnement waarvan uw sleutelkluis deel uitmaakt.
- **Account voor Cognitive Services**: dit is de Text Analytics-resource waarmee u verbinding gaat maken.
- **Gekoppelde Azure Key Vault-service**: als onderdeel van de vereiste stappen hebt u een gekoppelde service voor uw Text Analytics-resource gemaakt. Selecteer deze hier.
- **Naam van geheim**: dit is de naam van het geheim in de sleutelkluis met de sleutel die moet worden geverifieerd bij uw Cognitive Services-resource.

![Azure Key Vault-gegevens opgeven](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00f.png)

## <a name="configure-sentiment-analysis"></a>Sentimentanalyse configureren

Vervolgens moet u de sentimentanalyse configureren. Selecteer de volgende gegevens:
- **Taal**: selecteer de taal van de tekst waarvoor u sentimentanalyse wilt uitvoeren. Selecteer **EN**.
- **Tekstkolom**: dit is de tekstkolom in uw gegevensset die u wilt analyseren om het sentiment te bepalen. Selecteer de tabelkolom **Opmerking**.

Klik als u klaar bent op **Notebook openen**. Hiermee wordt een notebook voor u gegenereerd met PySpark-code die de sentimentanalyse uitvoert met Azure Cognitive Services.

![Sentimentanalyse configureren](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00g.png)

## <a name="open-notebook-and-run"></a>Notebook openen en uitvoeren

De notebook die u zojuist hebt geopend, maakt gebruik van de [mmlspark-bibliotheek](https://github.com/Azure/mmlspark) om verbinding te maken met Cognitive Services.

Met de Azure Key Vault-gegevens die u hebt ingevoerd, kunt u vanaf hier veilig naar uw geheimen verwijzen zonder dat u deze hoeft weer te geven.

U kunt nu **alle** cellen uitvoeren om uw gegevens te verrijken met sentimenten. De sentimenten worden geretourneerd als positief/negatief/neutraal/gemengd, en u krijgt ook waarschijnlijkheid per sentiment. Meer informatie over [Cognitive Services - Sentimentanalyse](https://go.microsoft.com/fwlink/?linkid=2147792).

![Sentimentanalyse uitvoeren](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00h.png)

## <a name="next-steps"></a>Volgende stappen
- [Zelfstudie: Anomaliedetectie met Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Zelfstudie: Scoren van het Machine learning-model in toegewezen SQL-pools van Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md)
- [Mogelijkheden voor machine learning in Azure Synapse Analytics](what-is-machine-learning.md)