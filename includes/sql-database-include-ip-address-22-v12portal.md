---
title: Firewallregels op serverniveau
description: Firewallregels op serverniveau
keywords: SQL-verbinding, connection string
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 8d0f9899dbb7599340b8d15ca010a0157011fb9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67176114"
---
1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

2. Selecteer in de lijst aan de linkerkant **alle services**.

3. Schuif en selecteer **SQL-servers**.

    ![Uw Azure SQL Database-Server zoeken in de portal][b21-FindServerInPortal]
5. Typ in het tekstvak Filter de naam van uw server. De rij wordt weer gegeven.

6. Selecteer de rij voor uw server. Er wordt een Blade voor uw server weer gegeven.

7. Selecteer op de Blade Server de optie **instellingen**.

8. Selecteer **firewall**.

    ![Instellingen > firewall selecteren][b31-SettingsFirewallNavig]
9. Selecteer **client-IP toevoegen**. Typ in het eerste tekstvak een naam voor de nieuwe regel.

10. Typ de waarden voor het laagste en hoogste IP-adres voor het bereik dat u wilt inschakelen.

    * Het kan handig zijn om de lage waarde end te hebben met **dpm\dpm\protectionagents\ra\3.0.** en de hoge waarde eindigt op **. 255**.

11. Selecteer **Opslaan**.

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->
