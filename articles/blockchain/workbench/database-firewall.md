---
title: Azure Block Chain Workbench-database firewall configureren
description: Meer informatie over het configureren van de preview-versie van de Azure Block Chain Workbench-database firewall zodat externe clients en toepassingen verbinding kunnen maken.
ms.date: 09/09/2019
ms.topic: how-to
ms.reviewer: mmercuri
ms.openlocfilehash: dc7a1dc7e4a083ef8f5f7650f29ed1430b32e88b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009446"
---
# <a name="configure-the-azure-blockchain-workbench-database-firewall"></a>Firewall voor database van Azure Blockchain Workbench configureren

In dit artikel wordt uitgelegd hoe u een firewallregel configureert met Azure Portal. Met behulp van firewallregels kunnen externe clients of toepassingen verbinding maken met uw Azure Blockchain Workbench-database.

## <a name="connect-to-the-blockchain-workbench-database"></a>Verbinding maken met een database van Blockchain Workbench

Verbinding maken met de database waar u een regel wilt configureren:

1. Meld u aan bij de Azure Portal met een account met **eigenaars**     machtigingen voor de Azure Block Chain Workbench-Resources.
2. Kies **Resourcegroepen** in het linkernavigatievenster.
3. Kies de naam van de resourcegroep voor uw implementatie van Blockchain Workbench.
4. Selecteer **Type** om de lijst met resources te sorteren en kies vervolgens uw **SQL-server**.
5. In het voorbeeld met een resource-overzicht in de volgende schermafbeelding ziet u twee databases: *master* en *lsgn sdk*. U configureert de firewallregel in *lsgn sdk*.

![Resources van Azure Blockchain Workbench opvragen](./media/database-firewall/list-database-resources.png)

## <a name="create-a-database-firewall-rule"></a>Een firewallregel voor een database maken

Een firewallregel maken:

1. Kies de link naar de database 'lsgn sdk'.
2. Selecteer **Serverfirewall instellen** op de menubalk.

   ![Serverfirewall instellen](./media/database-firewall/configure-server-firewall.png)

3. Een regel maken voor uw organisatie:

   * Geef een waarde op voor **REGELNAAM**
   * Geef een IP-adres voor **Eerste IP-adres** van het adresbereik
   * Geef een IP-adres voor **Laatste IP-adres** van het adresbereik

   ![Firewallregel maken](./media/database-firewall/create-firewall-rule.png)

    > [!NOTE]
    > Als u alleen het IP-adres van uw computer wilt toevoegen, kiest u **+ IP-adres van client toevoegen**.
        
1. Selecteer **Opslaan** om de firewallconfiguratie op te slaan.
2. Test het IP-adresbereik dat u hebt geconfigureerd voor de database door verbinding te maken vanuit een toepassing of hulpprogramma, zoals SQL Server Management Studio.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Databaseweergaven in Azure Blockchain Workbench](database-views.md)