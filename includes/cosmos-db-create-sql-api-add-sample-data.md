---
title: bestand opnemen
description: bestand opnemen
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 08/07/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: f80efbac256871af073354f23317c447d6a85f1e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "70020177"
---
U kunt nu gegevens aan uw nieuwe container toevoegen met behulp van Data Explorer.

1. Vouw in **Data Explorer** de database **Taken** uit en vouw de container **Items** uit. Selecteer **Items** en vervolgens **Nieuw item**.

   ![Nieuwe documenten maken in Data Explorer in de Azure Portal](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-new-document.png)
  
2. Voeg nu een document met de volgende structuur aan de container toe.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. Zodra u de JSON hebt toegevoegd aan het tabblad **Documenten**, selecteert u **Opslaan**.

    ![JSON-gegevens kopiëren en Opslaan selecteren in Data Explorer in de Azure-portal](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-save-document.png)

4.  Maak nog één document en sla dit op. In het document voegt u een unieke waarde toe voor de eigenschap `id`. Wijzig de andere eigenschappen naar eigen inzicht. De nieuwe documenten kunnen elke gewenste structuur hebben, omdat in Azure Cosmos DB uw gegevens geen schema krijgen opgelegd.