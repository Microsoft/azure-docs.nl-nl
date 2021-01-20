---
title: 'Zelfstudie: Vereisten voor Cognitive Services in Azure Synapse'
description: Zelfstudie voor het configureren van de vereisten voor het gebruik van Cognitive Services in Azure Synapse
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: eef65db05ab94b5b8de5ff82c2c51dba0730f170
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222170"
---
# <a name="tutorial-pre-requisites-for-using-cognitive-services-in-azure-synapse"></a>Zelfstudie: Vereisten voor het gebruik van Cognitive Services in Azure Synapse

In deze zelfstudie leert u hoe u de vereisten instelt voor het veilig gebruiken van Cognitive Services in Azure Synapse.

In deze zelfstudie komt het volgende aan bod:
> [!div class="checklist"]
> - Een Cognitive Services-resource maken. Bijvoorbeeld Text Analytics of Anomaly Detector.
> - De verificatiesleutel voor Cognitive Services-resources als geheim opslaan in Azure Key Vault en de toegang voor de Azure Synapse-werkruimte configureren.
> - Een aan Azure Key Vault gekoppelde service in uw Synapse Analytics-werkruimte maken.

Als u geen Azure-abonnement hebt, [maakt u een gratis account voordat u begint](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Vereisten

- [Azure Synapse Analytics-werkruimte](../get-started-create-workspace.md) met een ADLS Gen2-opslagaccount dat is geconfigureerd als de standaardopslag. U moet de **Inzender van de Storage Blob-gegevens** zijn van het ADLS Gen2-bestandssysteem waar u mee werkt.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/)

## <a name="create-a-cognitive-services-resource"></a>Een Cognitive Services-resource maken

[Azure Cognitive Services](../../cognitive-services/index.yml) bevatten een groot aantal verschillende typen services. Hieronder ziet u enkele voorbeelden die worden gebruikt in de Synapse-zelfstudies.

### <a name="create-an-anomaly-detector-resource"></a>Een Anomaly Detector-resource maken
Maak een [Anomaly Detector](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) in Azure Portal.

![Anomaly Detector maken](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

### <a name="create-a-text-analytics-resource"></a>Een Text Analytics-resource maken
Maak een [Text Analytics](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)-resource maken in Azure Portal.

![Text Analytics maken](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

## <a name="create-key-vault-and-configure-secrets-and-access"></a>Sleutelkluis maken en geheimen en toegang configureren

1. Maak een [sleutelkluis](https://ms.portal.azure.com/#create/Microsoft.KeyVault) in Azure Portal.
2. Ga naar **Sleutelkluis > Toegangsbeleid** en verleen de [MSI van de Azure Synapse-werkruimte](../security/synapse-workspace-managed-identity.md) machtigingen om geheimen uit Azure Key Vault te lezen.

>Zorg ervoor dat de beleidswijzigingen worden opgeslagen. Deze stap is eenvoudig te missen.

![Toegangsbeleid toevoegen](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. Ga naar uw Cognitive Service-resource, bijvoorbeeld **Anomaly Detector -> Sleutels en eindpunt**, en kopieer een van de twee sleutels naar het klembord.

4. Ga naar **Sleutelkluis > Geheim** om een nieuw geheim te maken. Geef de naam van het geheim op en plak vervolgens de sleutel uit de vorige stap in het veld 'Waarde'. Klik tot slot op **Maken**.

![Geheim maken](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

> Zorg ervoor dat u de naam van het geheim naam niet vergeet of ergens noteert. U hebt deze later nodig wanneer u verbinding maakt met Cognitive Services vanuit Azure Synapse Studio.

## <a name="create-azure-keyvault-linked-service-in-azure-synapse"></a>Een aan Azure Key Vault gekoppelde service maken in Azure Synapse

1. Open uw werkruimte in Azure Synapse Studio. Ga naar **Beheren -> Gekoppelde services**. Maak een aan Azure Key Vault gekoppelde service die verwijst naar de sleutelkluis die we zojuist hebben gemaakt. Controleer vervolgens de verbinding door te klikken op de knop 'Verbinding testen' en te controleren of deze groen is. Als alles naar behoren werkt, klikt u eerst op 'Maken' en vervolgens op 'Alles publiceren' om de wijziging op te slaan.
![Gekoppelde service](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)

U kunt nu doorgaan met een van de zelfstudies over het gebruik van de Azure Cognitive Services-ervaring in Azure Synapse Studio.

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Sentimentanalyse met Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Zelfstudie: Anomaliedetectie met Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Zelfstudie: Scoren van het Machine learning-model in toegewezen SQL-pools van Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md).
- [Mogelijkheden voor machine learning in Azure Synapse Analytics](what-is-machine-learning.md)