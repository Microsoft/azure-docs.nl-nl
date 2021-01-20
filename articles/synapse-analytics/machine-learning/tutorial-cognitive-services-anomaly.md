---
title: 'Zelfstudie: Anomaliedetectie met Cognitive Services'
description: Zelfstudie voor het gebruik van Cognitive Services voor anomaliedetectie in Synapse
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 5e7b914d459d2452704f93987ce1bf91bfba988c
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222204"
---
# <a name="tutorial-anomaly-detection-with-cognitive-services-preview"></a>Zelfstudie: Anomaliedetectie met Cognitive Services (preview)

In deze zelfstudie leert u hoe u uw gegevens in Azure Synapse eenvoudig kunt verrijken met [Cognitive Services](../../cognitive-services/index.yml). We gebruiken de [Anomaly Detector](../../cognitive-services/anomaly-detector/index.yml) voor het uitvoeren van anomaliedetectie. Een gebruiker in Azure Synapse kan eenvoudigweg een tabel selecteren om te verrijken met detectie van afwijkingen.

In deze zelfstudie komt het volgende aan bod:

> [!div class="checklist"]
> - Stappen voor het ophalen van een gegevensset van een Apache Spark-tabel die tijdreeksgegevens bevat.
> - Gebruik de wizard in Azure Synapse om gegevens te verrijken met behulp van Anomaly Detector Cognitive Services.

Als u geen Azure-abonnement hebt, [maakt u een gratis account voordat u begint](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Vereisten

- [Azure Synapse Analytics-werkruimte](../get-started-create-workspace.md) met een ADLS Gen2-opslagaccount dat is geconfigureerd als de standaardopslag. U moet de **Inzender van de Storage Blob-gegevens** zijn van het ADLS Gen2-bestandssysteem waar u mee werkt.
- Spark-pool in uw Azure Synapse Analytics-werkruimte. Zie [Een Spark-pool maken in Azure Synapse](../quickstart-create-sql-pool-studio.md) voor meer informatie.
- Voordat u deze zelfstudie kunt gebruiken, moet u ook de stappen voorafgaand aan de configuratie voltooien die in deze zelfstudie worden beschreven. [Cognitive Services in Azure Synapse configureren](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/)

## <a name="create-a-spark-table"></a>Een Spark-tabel maken

Voor deze zelfstudie hebt u een Spark-tabel nodig.

1. Download het volgende notebookbestand met code voor het genereren van een Apache Spark-tabel: [prepare_anomaly_detector_data.ipynb](https://go.microsoft.com/fwlink/?linkid=2149577)

1. Upload het bestand naar uw Azure Synapse-werkruimte.
![Notebook uploaden](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00a.png)

1. Open het notebookbestand en kies voor **Alles uitvoeren** voor de cellen.
![Spark-tabel maken](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00b.png)

1. Een Apache Spark-tabel met de naam **anomaly_detector_testing_data** moet nu worden weergegeven in de standaard Apache Spark-database.

## <a name="launch-cognitive-services-wizard"></a>De wizard van Cognitive Services starten

1. Klik met de rechtermuisknop op de Spark-tabel die u in de vorige stap hebt gemaakt. Selecteer 'Machine Learning -> Verrijken met bestaand model' om de wizard te openen.
![Wizard voor scoren starten](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00g.png)

2. Er wordt een configuratievenster weergegeven waarin u wordt gevraagd om een Cognitive Services-model te selecteren. Anomaly Detector selecteren.

![Wizard voor scoren starten - stap 1](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00c.png)

## <a name="provide-authentication-details"></a>Verificatiegegevens opgeven

Als u zich wilt verifiëren bij Cognitive Services, moet u verwijzen naar het geheim dat moet worden gebruikt in uw Key Vault. De onderstaande invoer is afhankelijk van [vereiste stappen](tutorial-configure-cognitive-services-synapse.md) die u vóór deze stap moet hebben voltooid.

- **Azure-abonnement**: selecteer het Azure-abonnement waarvan uw sleutelkluis deel uitmaakt.
- **Account voor Cognitive Services**: dit is de Text Analytics-resource waarmee u verbinding gaat maken.
- **Gekoppelde Azure Key Vault-service**: als onderdeel van de vereiste stappen hebt u een gekoppelde service voor uw Text Analytics-resource gemaakt. Selecteer deze hier.
- **Naam van geheim**: dit is de naam van het geheim in de sleutelkluis met de sleutel die moet worden geverifieerd bij uw Cognitive Services-resource.

![Azure Key Vault-gegevens opgeven](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00d.png)

## <a name="configure-anomaly-detection"></a>Anomaliedetectie configureren

Vervolgens moet u de anomaliedetectie configureren. Geef de volgende gegevens op:

- Granulariteit: De snelheid waarmee steekproeven van uw gegevens worden genomen. Als uw gegevens bijvoorbeeld voor elke minuut een waarde hebben, is uw granulariteit per minuut. **Maandelijks** kiezen 

- Tijdstempel: De kolom die de tijd van de reeks weergeeft. Kies de kolom **timestamp**

- Waarde van tijdreeks: De kolom die de waarde van de reeks aangeeft op het tijdstip dat is vermeld in de kolom Timestamp. Kies de kolom **waarde**

- Groeperen: kolom waarmee de reeks wordt gegroepeerd. Dat wil zeggen dat alle rijen met dezelfde waarde in deze kolom één tijdreeks moeten vormen. Kies de kolom **groep**

Selecteer als u klaar bent **Notebook openen**. Hiermee wordt een notebook voor u gegenereerd met PySpark-code die de anomaliedetectie uitvoert met behulp van Azure Cognitive Services.

![Anomaliedetectie configureren](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00e.png)

## <a name="open-notebook-and-run"></a>Notebook openen en uitvoeren

De notebook die u zojuist hebt geopend, maakt gebruik van de [mmlspark-bibliotheek](https://github.com/Azure/mmlspark) om verbinding te maken met Cognitive Services.

Met de Azure Key Vault-gegevens die u hebt ingevoerd, kunt u vanaf hier veilig naar uw geheimen verwijzen zonder dat u deze hoeft weer te geven.

U kunt nu **Alles uitvoeren** selecteren voor de cellen om anomaliedetectie uit te voeren. Meer informatie over [Cognitive Services - Anomaly Detector](../../cognitive-services/anomaly-detector/index.yml).

![Anomaliedetectie uitvoeren](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00f.png)

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Sentimentanalyse met Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Zelfstudie: Scoren van het Machine learning-model in toegewezen SQL-pools van Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md)
- [Mogelijkheden voor machine learning in Azure Synapse Analytics](what-is-machine-learning.md)