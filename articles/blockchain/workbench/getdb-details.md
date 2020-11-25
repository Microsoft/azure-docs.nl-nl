---
title: Details van Azure Blockchain Workbench-database opvragen
description: Meer informatie over het ophalen van de voorbeeld database en database server gegevens voor Azure Block Chain Workbench.
ms.date: 09/05/2019
ms.topic: how-to
ms.reviewer: mmercuri
ms.openlocfilehash: 898360d8e22803e17ff4da7d3b63f5595cad47ba
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015482"
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Informatie opvragen over uw Azure Blockchain Workbench-database

In dit artikel wordt uitgelegd hoe u gedetailleerde informatie krijgt over de preview-versie van uw Azure Block Chain Workbench-data base.

## <a name="overview"></a>Overzicht

Informatie over toepassingen, werkstromen de uitvoering van slimme contracten kunt u opvragen met behulp van weergaven van de SQL-database van Blockchain Workbench. Ontwikkel aars kunnen deze informatie gebruiken bij het gebruik van hulpprogram ma's zoals micro soft Excel, Power BI, Visual Studio en SQL Server Management Studio.

Ontwikkelaars hebben het volgende nodig om verbinding te kunnen maken met de database:

* Externe clienttoegang is toegestaan in de databasefirewall. In dit artikel over het configureren van een databasefirewall wordt uitgelegd hoe u toegang kunt verlenen.
* De naam van de databaseserver en van de database.

## <a name="connect-to-the-blockchain-workbench-database"></a>Verbinding maken met een database van Blockchain Workbench

Verbinding maken met de database:

1. Meld u aan bij de Azure Portal met een account met **eigenaars** machtigingen voor de Azure Block Chain Workbench-Resources.
2. Kies **Resourcegroepen** in het linkernavigatievenster.
3. Kies de naam van de resourcegroep voor uw implementatie van Blockchain Workbench.
4. Selecteer **Type** om de lijst met resources te sorteren en kies vervolgens uw **SQL-server**. In de gesorteerde lijst in de volgende scherm opname ziet u twee data bases, ' Master ', en een met ' lhgn ' als **resource voorvoegsel**.

   ![Lijst met gesorteerde resources van Azure Blockchain Workbench](./media/getdb-details/sorted-workbench-resource-list.png)

5. Als u gedetailleerde gegevens wilt weergeven voor de database van Blockchain Workbench, selecteert u de link voor de database met het **resourcevoorvoegsel** dat u hebt opgegeven voor het implementeren van Blockchain Workbench.

   ![Databasedetails](./media/getdb-details/workbench-db-details.png)

Met behulp van de naam van de databaseserver en de database kunt u vanuit uw ontwikkel- of rapportageprogramma verbinding met de database Blockchain Workbench.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Databaseweergaven in Azure Blockchain Workbench](database-views.md)