---
title: 'Zelfstudie: Anomaliedetectie met Cognitive Services'
description: Meer informatie over het gebruik van Cognitive Services voor anomalie detectie in azure Synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: c54300bf37f6f4526c525b1502d902e5f4336ed7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943508"
---
# <a name="tutorial-anomaly-detection-with-cognitive-services-preview"></a>Zelf studie: afwijkings detectie met Cognitive Services (preview-versie)

In deze zelf studie leert u hoe u uw gegevens in azure Synapse Analytics eenvoudig kunt verrijken met [azure Cognitive Services](../../cognitive-services/index.yml). U gebruikt [anomalie detectie](../../cognitive-services/anomaly-detector/index.yml) om afwijkingen te vinden. Een gebruiker in Azure Synapse kan eenvoudigweg een tabel selecteren om te verrijken met detectie van afwijkingen.

In deze zelfstudie komt het volgende aan bod:

> [!div class="checklist"]
> - Stappen voor het ophalen van een Spark-tabel gegevensset die tijdreeks gegevens bevat.
> - Gebruik van een wizard-ervaring in azure Synapse om gegevens te verrijken met behulp van anomalie detectie in Cognitive Services.

Als u geen Azure-abonnement hebt, [maakt u een gratis account voordat u begint](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Vereisten

- [Azure Synapse Analytics-werk ruimte](../get-started-create-workspace.md) met een Azure data Lake Storage Gen2 opslag account geconfigureerd als de standaard opslag. U moet de Inzender voor *gegevens* van de opslag-blob van het data Lake Storage Gen2 bestands systeem waarmee u samenwerkt.
- Spark-pool in uw Azure Synapse Analytics-werkruimte. Zie [Een Spark-pool maken in Azure Synapse](../quickstart-create-sql-pool-studio.md) voor meer informatie.
- Voltooiing van de stappen voorafgaand aan configuratie in de zelf studie [configure Cognitive Services in azure Synapse](tutorial-configure-cognitive-services-synapse.md) .

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="create-a-spark-table"></a>Een Spark-tabel maken

U hebt een Spark-tabel nodig voor deze zelf studie.

1. Down load het volgende notitieblok bestand dat code bevat voor het genereren van een Spark-tabel: [prepare_anomaly_detector_data. ipynb](https://go.microsoft.com/fwlink/?linkid=2149577).

1. Upload het bestand naar uw Azure Synapse-werkruimte.

   ![Scherm opname van de selecties voor het uploaden van een notitie blok.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00a.png)

1. Open het notebook-bestand en selecteer **alles uitvoeren** om alle cellen uit te voeren.

   ![Scherm opname van selecties voor het maken van een Spark-tabel.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00b.png)

1. Een Apache Spark-tabel met de naam **anomaly_detector_testing_data** moet nu worden weergegeven in de standaard Apache Spark-database.

## <a name="open-the-cognitive-services-wizard"></a>De wizard Cognitive Services openen

1. Klik met de rechter muisknop op de Spark-tabel die u in de vorige stap hebt gemaakt. Selecteer **machine learning**  >  **verrijken met bestaand model** om de wizard te openen.

   ![Scherm opname van de selecties voor het openen van de Score wizard.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00g.png)

2. Er wordt een configuratie scherm weer gegeven en u wordt gevraagd om een Cognitive Services model te selecteren. Selecteer **anomalie detectie**.

   ![Scherm opname van de weer gave van anomalie detectie als een model.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00c.png)

## <a name="provide-authentication-details"></a>Verificatiegegevens opgeven

Als u zich wilt verifiëren bij Cognitive Services, moet u verwijzen naar het geheim in uw sleutel kluis. De volgende invoer is afhankelijk van de [vereiste stappen](tutorial-configure-cognitive-services-synapse.md) die u vóór dit punt moet volt ooien.

- **Azure-abonnement**: Selecteer het Azure-abonnement waarvan uw sleutel kluis deel uitmaakt.
- **Cognitive Services account**: geef de Text Analytics resource op waarmee u verbinding maakt.
- **Azure Key Vault gekoppelde service**: u hebt een gekoppelde service voor uw Text Analytics resource gemaakt als onderdeel van de vereiste stappen. Selecteer deze hier.
- **Geheime naam**: Voer de naam in van het geheim in de sleutel kluis die de sleutel bevat die moet worden geverifieerd bij uw Cognitive Services-resource.

![Scherm opname van de verificatie gegevens voor een sleutel kluis.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00d.png)

## <a name="configure-anomaly-detector"></a>Anomalie detectie configureren

Geef de volgende details op om anomalie detectie te configureren:

- **Granulatie**: de snelheid waarmee uw gegevens worden gesampled. Kies **maandelijks**. 

- **Time Stamp-kolom**: de kolom die de tijd van de reeks vertegenwoordigt. Kies **tijds tempel (teken reeks)**.

- **Tijds Erie waarde kolom**: de kolom die de waarde van de reeks vertegenwoordigt op het tijdstip dat is opgegeven door de time stamp-kolom. Kies een **waarde (dubbel)**.

- **Groepeer kolom**: de kolom waarin de reeks wordt gegroepeerd. Dat wil zeggen dat alle rijen met dezelfde waarde in deze kolom één tijdreeks moeten vormen. Kies **groep (teken reeks)**.

Wanneer u klaar bent, selecteert u **notitie blok openen**. Hiermee wordt een notitie blok voor u gegenereerd met PySpark-code die gebruikmaakt van Azure Cognitive Services om afwijkingen te detecteren.

![Scherm afbeelding met Configuratie Details voor anomalie detectie.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00e.png)

## <a name="run-the-notebook"></a>Het notitieblok uitvoeren

Het notitie blok dat u zojuist hebt geopend, maakt gebruik van de [mmlspark-bibliotheek](https://github.com/Azure/mmlspark) om verbinding te maken met Cognitive Services. Met de Azure Key Vault gegevens die u hebt ingevoerd, kunt u veilig vanuit deze ervaring naar uw geheimen verwijzen zonder dat u deze hoeft weer te geven.

U kunt nu alle cellen uitvoeren om anomalie detectie uit te voeren. Selecteer **alles uitvoeren**. Meer [informatie over anomalie detectie in cognitive Services](../../cognitive-services/anomaly-detector/index.yml).

![Scherm opname van de afwijkings detectie.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00f.png)

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Sentimentanalyse met Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Zelf studie: score model voor machine learning-modellen in azure Synapse dedicated SQL-groepen](tutorial-sql-pool-model-scoring-wizard.md)
- [Mogelijkheden voor machine learning in Azure Synapse Analytics](what-is-machine-learning.md)
