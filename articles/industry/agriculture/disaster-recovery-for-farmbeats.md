---
title: Herstel na nood geval voor FarmBeats
description: In dit artikel wordt beschreven hoe gegevens herstel bescherming biedt tegen het verlies van uw gegevens.
author: uhabiba04
ms.topic: article
ms.date: 04/13/2020
ms.author: v-ummehabiba
ms.openlocfilehash: 9ece624546cab1b8b6fab8c19f4401bd050f6267
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179881"
---
# <a name="disaster-recovery-for-farmbeats"></a>Herstel na nood geval voor FarmBeats

Met gegevens herstel kunt u uw gegevens verliezen in een gebeurtenis zoals het samen vouwen van de Azure-regio. In een dergelijk geval kunt u een failover starten en de gegevens herstellen die zijn opgeslagen in uw FarmBeats-implementatie.

Gegevens herstel is geen standaard functie in azure FarmBeats. U kunt deze functie hand matig configureren door de vereiste Azure-resources te configureren die worden gebruikt door FarmBeats om gegevens op te slaan in een Azure-gekoppelde regio. Gebruik actief – passieve benadering om herstel in te scha kelen.

De volgende secties bevatten informatie over het configureren van gegevens herstel in azure FarmBeats:

- [Gegevens redundantie inschakelen](#enable-data-redundancy)
- [Service herstellen vanuit online back-up](#restore-service-from-online-backup)


## <a name="enable-data-redundancy"></a>Gegevens redundantie inschakelen

FarmBeats slaat gegevens op in drie Azure-Services voor de eerste partij, die **Azure Storage** zijn, **Cosmos DB** en **Time Series Insights**. Gebruik de volgende stappen om gegevens redundantie voor deze services in te scha kelen voor een gekoppelde Azure-regio:

1.  **Azure Storage** : Volg deze richt lijnen om gegevens redundantie in te scha kelen voor elk opslag account in uw FarmBeats-implementatie.
2.  **Azure Cosmos DB** : Volg deze richt lijnen om gegevens redundantie in te scha kelen voor Cosmos DB account van uw FarmBeats-implementatie.
3.  **Azure time series Insights (TSI)** -TSI biedt momenteel geen gegevens redundantie. Als u Time Series Insights gegevens wilt herstellen, gaat u naar uw sensor/weer partner en pusht u de gegevens naar FarmBeats-implementatie.

## <a name="restore-service-from-online-backup"></a>Service herstellen vanuit online back-up

U kunt een failover initiëren en de opgeslagen gegevens herstellen waarvoor elk van de hierboven genoemde gegevens archieven voor uw FarmBeats-implementatie. Wanneer u de gegevens voor Azure Storage en Cosmos DB hebt hersteld, maakt u een andere FarmBeats-implementatie in de Azure-gekoppelde regio en configureert u vervolgens de nieuwe implementatie voor het gebruik van gegevens uit de herstelde gegevens archieven (Azure Storage en Cosmos DB) met behulp van de onderstaande stappen:

1. [Cosmos DB configureren](#configure-cosmos-db)
2. [Opslag account configureren](#configure-storage-account)


### <a name="configure-cosmos-db"></a>Cosmos DB configureren

Kopieer de toegangs sleutel van de herstelde Cosmos DB en werk de nieuwe FarmBeats Datahub-Key Vault bij.


  ![Scherm afbeelding waarin wordt uitgelegd waar u de kopie van de toegangs toets wilt ophalen.](./media/disaster-recovery-for-farmbeats/key-vault-secrets.png)

> [!NOTE]
> Kopieer de URL van de herstelde Cosmos DB en werk deze bij in de nieuwe FarmBeats-Datahub App Service-configuratie. U kunt nu Cosmos DB-account verwijderen in de nieuwe FarmBeats-implementatie.

  ![Scherm afbeelding die laat zien waar u de URL van de herstelde Cosmos DB kopieert.](./media/disaster-recovery-for-farmbeats/configuration.png)

### <a name="configure-storage-account"></a>Opslag account configureren

Kopieer de toegangs sleutel van het herstelde opslag account en werk het bij in de nieuwe FarmBeats Datahub Key Vault.

![Scherm opname van de locatie waar de toegangs sleutel van het herstelde opslag account moet worden gekopieerd.](./media/disaster-recovery-for-farmbeats/key-vault-7-secrets.png)

>[!NOTE]
> Zorg ervoor dat u de naam van het opslag account bijwerkt in het nieuwe FarmBeats batch-configuratie bestand voor de VM.

![Herstel na noodgevallen](./media/disaster-recovery-for-farmbeats/batch-prep-files.png)

Als u gegevens herstel voor uw Accelerator Storage-account hebt ingeschakeld, volgt u de stap 2 voor het bijwerken van de toegangs sleutel en naam van het Accelerator Storage-account in het nieuwe FarmBeats-exemplaar.
