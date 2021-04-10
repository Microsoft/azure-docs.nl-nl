---
title: Een door Azure Cosmos DB geactiveerde functie maken
description: Gebruik Azure Functions om een functie zonder server te maken die wordt aangeroepen wanneer gegevens worden toegevoegd aan een database in Azure Cosmos DB.
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.topic: how-to
ms.date: 04/28/2020
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 621773a84db99dbacfaa163f77189974ba102163
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98034812"
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Een door Azure Cosmos DB geactiveerde functie maken

Leer hoe u een functie maakt die wordt geactiveerd wanneer gegevens worden toegevoegd of gewijzigd in Azure Cosmos DB. Zie [Azure Cosmos DB: Serverless database computing using Azure Functions](../cosmos-db/serverless-computing-database.md) (Azure Cosmos DB: database berekenen zonder server met Azure Functions) voor meer informatie over Azure Cosmos DB.

:::image type="content" source="./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png" alt-text="Azure Cosmos DB code":::

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

+ Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

> [!NOTE]
> [!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u met uw Azure-account aan bij [Azure Portal](https://portal.azure.com/).

## <a name="create-an-azure-cosmos-db-account"></a>Maak een Azure Cosmos DB-account

U moet een Azure Cosmos DB-account hebben dat gebruikmaakt van de SQL-API voordat u de trigger maakt.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="create-a-function-app-in-azure"></a>Een functie-app maken in Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Vervolgens maakt u een functie in de nieuwe functie-app.

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>Een Azure Cosmos DB-trigger maken

1. Selecteer in de functie-app **functies** in het menu links en selecteer vervolgens **toevoegen** in het bovenste menu. 

1. Voer op de pagina **nieuwe functie** `cosmos` in het zoek veld in en kies vervolgens de sjabloon **Azure Cosmos DB trigger** .

   :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/function-choose-cosmos.png" alt-text="De pagina functies in het Azure Portal":::


1. Configureer de nieuwe trigger met de instellingen zoals opgegeven in de volgende tabel:

    | Instelling      | Voorgestelde waarde  | Beschrijving                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Nieuwe functie** | Accepteer de standaardnaam | De naam van de functie. |
    | **Cosmos DB-accountverbinding** | De standaard nieuwe naam accepteren | Selecteer **Nieuw**, het **Database account** dat u eerder hebt gemaakt en klik vervolgens op **OK**. Met deze actie wordt een toepassings instelling voor uw account verbinding gemaakt. Deze instelling wordt gebruikt door de binding om verbinding te maken met de database. |
    | **Databasenaam** | Taken | De naam van de data base die de verzameling bevat die moet worden bewaakt. |
    | **Naam van verzameling** | Items | De naam van de verzameling die moet worden bewaakt. |
    | **Naam van de verzameling voor leases** | leases | De naam van de verzameling waarin de leases moeten worden opgeslagen. |
    | **Een lease verzameling maken als deze nog niet bestaat** | Yes | Controleert op de aanwezigheid van de lease verzameling en maakt deze automatisch. |

    :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png" alt-text="Maak de door Azure Cosmos DB geactiveerde functie":::

1. Selecteer **Functie maken**. 

    Azure maakt de functie trigger Cosmos DB.

1. Als u de functie code op basis van een sjabloon wilt weer geven, selecteert u **code + testen**.

    :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png" alt-text="Cosmos DB-functiesjabloon in C#":::

    Met deze functiesjabloon worden het aantal documenten en de eerste document-id naar de logboeken geschreven.

Vervolgens gaat u verbinding maken met uw Azure Cosmos DB-account en maakt u de `Items` container in de- `Tasks` Data Base.

## <a name="create-the-items-container"></a>De container items maken

1. Open een tweede exemplaar van [Azure Portal](https://portal.azure.com) op een nieuw tabblad in de browser.

1. Vouw aan de linkerkant van de portal de pictogrammenbalk uit, typ `cosmos` in het zoekveld en selecteer **Azure Cosmos DB**.

    ![Zoek naar de Azure Cosmos DB-service](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

1. Kies uw Azure Cosmos DB-account en selecteer vervolgens **Data Explorer**. 

1. Kies onder **SQL API** de optie **taken** data base en selecteer **nieuwe container**.

    ![Een container maken](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container.png)

1. Gebruik in **container toevoegen** de instellingen die in de tabel onder de afbeelding worden weer gegeven. 

    ![De taken container definiëren](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container2.png)

    | Instelling|Voorgestelde waarde|Beschrijving |
    | ---|---|--- |
    | **Database-id** | Taken |De naam voor de nieuwe database. Dit moet overeenkomen met de naam die is gedefinieerd in de functiebinding. |
    | **Container-id** | Items | De naam voor de nieuwe container. Dit moet overeenkomen met de naam die is gedefinieerd in de functiebinding.  |
    | **[Partitiesleutel](../cosmos-db/partitioning-overview.md)** | /category|Een partitiesleutel waarmee gegevens gelijkmatig worden gedistribueerd naar elke partitie. Het is belang rijk dat u de juiste partitie sleutel selecteert bij het maken van een uitvoerende container. | 
    | **Doorvoer** |400 RU| Gebruik de standaardwaarde. U kunt de doorvoer later opschalen als u de latentie wilt beperken. |    

1. Klik op **OK** om de container items te maken. Het kan even duren voordat de container is gemaakt.

Nadat de container die in de functie binding is opgegeven, bestaat, kunt u de functie testen door items toe te voegen aan deze nieuwe container.

## <a name="test-the-function"></a>De functie testen

1. Vouw de container nieuwe **items** in Data Explorer uit, kies **items** en selecteer vervolgens **Nieuw item**.

    :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/create-item-in-container.png" alt-text="Een item in de items container maken":::

1. Vervang de inhoud van het nieuwe item door de volgende inhoud en kies vervolgens **Opslaan**.

    ```yaml
    {
        "id": "task1",
        "category": "general",
        "description": "some task"
    }
    ```

1. Schakel over naar het eerste browsertabblad dat de functie in de portal bevat. Vouw de functielogboeken uit en controleer of de functie is geactiveerd met behulp van het nieuwe document. U ziet nu dat de waarde voor de `task1`-document-id naar de logboeken is geschreven. 

    ![Bekijk het bericht in de logboeken.](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-view-logs.png)

1. (Optioneel) Ga terug naar het document, breng een wijziging aan en klik op **Bijwerken**. Ga vervolgens terug naar de functielogboeken en controleer of met de update ook de functie is geactiveerd.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt een functie gemaakt die wordt uitgevoerd wanneer een document wordt toegevoegd of gewijzigd in uw Azure Cosmos DB-account. Zie [Azure Cosmos DB bindings for Azure Functions](functions-bindings-cosmosdb.md) (Azure Cosmos DB-bindingen voor Azure Functions) voor meer informatie over Azure Cosmos DB-triggers.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
