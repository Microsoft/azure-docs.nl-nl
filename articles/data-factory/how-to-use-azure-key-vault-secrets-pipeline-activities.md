---
title: Azure Key Vault-geheimen gebruiken in pijplijnactiviteiten
description: Meer informatie over het ophalen van opgeslagen referenties van Azure sleutel kluis en het gebruik ervan tijdens data factory pijplijn uitvoeringen.
services: data-factory
author: ChrisLound
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
ms.openlocfilehash: 1766705e73afab5d15cdb5aa2c5bb1487ad3d7c5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013884"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>Azure Key Vault-geheimen gebruiken in pijplijnactiviteiten

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

U kunt referenties of geheime waarden opslaan in een Azure Key Vault en deze gebruiken tijdens de uitvoering van de pijp lijn om uw activiteiten door te geven.

## <a name="prerequisites"></a>Vereisten

Deze functie is afhankelijk van de data factory beheerde identiteit.  Meer informatie over hoe het werkt met [beheerde identiteit voor Data Factory](./data-factory-service-identity.md) en om ervoor te zorgen dat uw Data Factory een koppeling heeft.

## <a name="steps"></a>Stappen

1. Open de eigenschappen van uw data factory en kopieer de waarde voor de toepassings-ID van de beheerde identiteit.

    ![Waarde beheerde identiteit](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. Open het toegangs beleid voor de sleutel kluis en voeg de beheerde identiteits machtigingen toe om geheimen op te halen en weer te geven.

    ![Scherm opname van de pagina ' toegangs beleid ' met de actie ' toegangs beleid toevoegen ' gemarkeerd.](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Toegangs beleid Key Vault](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    Klik op **toevoegen** en vervolgens op **Opslaan**.

3. Navigeer naar uw Key Vault Secret en kopieer de geheime id.

    ![Geheime id](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    Noteer de geheime URI die u tijdens de uitvoering van de data factory pijplijn wilt ontvangen.

4. Voeg in uw Data Factory-pijp lijn een nieuwe webactiviteit toe en configureer deze als volgt.  

    |Eigenschap  |Waarde  |
    |---------|---------|
    |Beveiligde uitvoer     |True         |
    |URL     |[Uw geheime URI-waarde]? API-Version = 7.0         |
    |Methode     |GET         |
    |Verificatie     |MSI         |
    |Resource        |https://vault.azure.net       |

    ![Webactiviteit](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > U moet **? API-Version = 7.0** toevoegen aan het einde van de geheime URI.  

    > [!CAUTION]
    > Stel de optie voor beveiligde uitvoer in op True om te voor komen dat de geheime waarde wordt geregistreerd als tekst zonder opmaak.  Voor verdere activiteiten waarvoor deze waarde wordt gebruikt, moet de optie voor beveiligde invoer zijn ingesteld op waar.

5. Als u de waarde in een andere activiteit wilt gebruiken, gebruikt u de volgende code-expressie **@activity (' web1 '). output. Value**.

    ![Code-expressie](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>Volgende stappen

Zie [referenties opslaan in azure Key Vault](./store-credentials-in-key-vault.md) voor meer informatie over het gebruik van Azure Key Vault voor het opslaan van referenties voor gegevens archieven en-berekeningen.