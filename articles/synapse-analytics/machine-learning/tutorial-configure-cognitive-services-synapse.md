---
title: 'Zelf studie: vereisten voor Cognitive Services in azure Synapse Analytics'
description: Meer informatie over het configureren van de vereisten voor het gebruik van Cognitive Services in azure Synapse.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 3ab861caca0ef6f58c2c1bc722412774deb725ce
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936677"
---
# <a name="tutorial-prerequisites-for-using-cognitive-services-in-azure-synapse-analytics"></a>Zelf studie: vereisten voor het gebruik van Cognitive Services in azure Synapse Analytics

In deze zelf studie leert u hoe u de vereisten instelt voor veilig gebruik van Azure Cognitive Services in azure Synapse Analytics.

In deze zelfstudie komt het volgende aan bod:
> [!div class="checklist"]
> - Maak een Cognitive Services resource, zoals Text Analytics of afwijkings detectie.
> - Sla een verificatie sleutel op om bronnen te Cognitive Services als geheimen in Azure Key Vault en configureer de toegang voor een Azure Synapse Analytics-werk ruimte.
> - Maak een Azure Key Vault gekoppelde service in uw Azure Synapse Analytics-werk ruimte.

Als u geen Azure-abonnement hebt, [maakt u een gratis account voordat u begint](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Vereisten

- [Azure Synapse Analytics-werk ruimte](../get-started-create-workspace.md) met een Azure data Lake Storage Gen2 opslag account geconfigureerd als de standaard opslag. U moet de Inzender voor *gegevens* van de opslag-blob van het Azure data Lake Storage Gen2 bestands systeem waarmee u samenwerkt.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="create-a-cognitive-services-resource"></a>Een Cognitive Services-resource maken

[Azure Cognitive Services](../../cognitive-services/index.yml) bevat veel soorten services. Text Analytics en anomalie detectie zijn twee voor beelden in de zelf studies voor Azure Synapse.

U kunt in de Azure Portal een [Text Analytics](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) resource maken:

![Scherm opname van Text Analytics in de portal, met de knop maken.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

U kunt een [anomalie detector](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) bron maken in de Azure portal:

![Scherm opname van de anomalie detectie in de portal, met de knop maken.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

## <a name="create-a-key-vault-and-configure-secrets-and-access"></a>Een sleutel kluis maken en geheimen en toegang configureren

1. Maak een [sleutel kluis](https://ms.portal.azure.com/#create/Microsoft.KeyVault) in de Azure Portal.
2. Ga naar **Key Vault**  >  **toegangs beleid** en verleen de MSI-machtigingen voor de [Azure Synapse-werk ruimte](../security/synapse-workspace-managed-identity.md) om geheimen van Azure Key Vault te lezen.

   > [!NOTE]
   > Zorg ervoor dat de beleidswijzigingen worden opgeslagen. Deze stap is eenvoudig te missen.

   ![Scherm opname van selecties voor het toevoegen van een toegangs beleid.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. Ga naar uw Cognitive Services-resource. Ga bijvoorbeeld naar **anomalie detectie**  >  **sleutels en eind punt**. Kopieer vervolgens een van de twee sleutels naar het klem bord.

4. Ga naar **Key Vault**  >  **Secret** om een nieuw geheim te maken. Geef de naam van het geheim op en plak vervolgens de sleutel uit de vorige Step Into het veld **waarde** . Selecteer tot slot **maken**.

   ![Scherm opname van selecties voor het maken van een geheim.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

   > [!IMPORTANT]
   > Zorg ervoor dat u deze geheime naam vergeet of noteert. U gebruikt dit later wanneer u verbinding maakt met Cognitive Services vanuit Azure Synapse Studio.

## <a name="create-an-azure-key-vault-linked-service-in-azure-synapse"></a>Een Azure Key Vault gekoppelde service maken in azure Synapse

1. Open uw werkruimte in Azure Synapse Studio. 
2. Ga naar   >  **gekoppelde services** beheren. Maak een **Azure Key Vault** gekoppelde service door te verwijzen naar de sleutel kluis die u zojuist hebt gemaakt. 
3. Controleer de verbinding door de knop **verbinding testen** te selecteren. Als de verbinding groen is, selecteert u **maken** en selecteert u **Alles publiceren** om de wijziging op te slaan.

![Scherm opname van Azure Key Vault als een nieuwe gekoppelde service.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)

U kunt nu door gaan met een van de zelf studies voor het gebruik van de Azure Cognitive Services-ervaring in azure Synapse Studio.

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Sentimentanalyse met Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Zelfstudie: Anomaliedetectie met Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Zelfstudie: Scoren van het Machine learning-model in toegewezen SQL-pools van Azure Synapse](tutorial-sql-pool-model-scoring-wizard.md).
- [Mogelijkheden voor machine learning in Azure Synapse Analytics](what-is-machine-learning.md)