---
title: Firewall regels beheren-Azure Portal-Azure Database for MySQL
description: Azure Database for MySQL firewall regels maken en beheren met behulp van de Azure Portal
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 032db324f9dd2f0d6a5dce5c4fd5c64342fed59f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94540481"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-portal"></a>Firewallregels voor Azure Database for MySQL maken via Azure Portal
Firewall regels op server niveau kunnen worden gebruikt voor het beheren van toegang tot een Azure Database for MySQL server vanaf een opgegeven IP-adres of een bereik van IP-adressen. 

Regels voor Virtual Network (VNet) kunnen ook worden gebruikt voor het beveiligen van de toegang tot uw server. Meer informatie over [het maken en beheren van Virtual Network Service-eind punten en-regels met behulp van de Azure Portal](howto-manage-vnet-using-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Een serverfirewallregel maken in Azure Portal

1. Klik op de pagina MySQL-server, onder instellingen kop, op **verbindings beveiliging** om de pagina verbindings beveiliging te openen voor de Azure database for MySQL.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/1-connection-security.png" alt-text="Azure Portal-Klik op verbindings beveiliging":::

2. Klik op **Mijn IP toevoegen** op de werk balk. Hiermee wordt automatisch een firewall regel gemaakt met het open bare IP-adres van uw computer, zoals wordt waargenomen door het Azure-systeem.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/2-add-my-ip.png" alt-text="Azure Portal-Klik op mijn IP toevoegen":::

3. Controleer uw IP-adres voordat u de configuratie opslaat. In sommige gevallen wijkt het IP-adres van Azure Portal af van het IP-adres dat wordt gebruikt voor toegang tot het internet en Azure-servers. Daarom moet u het eerste IP-adres en het laatste IP-adres wijzigen om de regel te laten werken zoals verwacht.

   Gebruik een zoek machine of een ander online hulp programma om uw eigen IP-adres te controleren. Zoek bijvoorbeeld ' wat is mijn IP-adres '.

4. Voeg extra adresbereiken toe. In de firewall regels voor de Azure Database for MySQL, kunt u één IP-adres of een bereik van adressen opgeven. Als u de regel wilt beperken tot één IP-adres, typt u hetzelfde adres in de velden Begin-IP en eind-IP. Als u de firewall opent, kunnen beheerders, gebruikers en toepassingen toegang krijgen tot alle data bases op de MySQL-server waarvoor ze geldige referenties hebben.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/4-specify-addresses.png" alt-text="Azure Portal-firewall regels":::

5. Klik op **Opslaan** op de werk balk om deze firewall regel op server niveau op te slaan. Wacht tot de bevestiging van de update van de firewall regels is geslaagd.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png" alt-text="Azure Portal-Klik op opslaan":::

## <a name="connecting-from-azure"></a>Verbinding maken vanuit Azure
Azure-verbindingen moeten zijn ingeschakeld om toepassingen van Azure toe te staan om verbinding te maken met uw Azure Database for MySQL server. Als u bijvoorbeeld een Azure Web Apps-toepassing wilt hosten, of een toepassing die wordt uitgevoerd in een Azure-VM of als u verbinding wilt maken vanuit een Azure Data Factory Data Management Gateway. De resources hoeven zich niet in dezelfde Virtual Network (VNet) of resource groep voor de firewall regel te bevinden om deze verbindingen in te scha kelen. Wanneer een toepassing vanuit Azure probeert verbinding te maken met uw databaseserver, verifieert de firewall of Azure-verbindingen zijn toegestaan. Er zijn een aantal methoden om deze typen verbindingen in te scha kelen. Een firewallinstelling waarvan het begin- en eindadres gelijk zijn aan 0.0.0.0 geeft aan dat deze verbindingen zijn toegestaan. U kunt ook de optie toegang tot **Azure-Services toestaan** in op in de Portal instellen in het deel **venster verbindings beveiliging** en **op** **Opslaan** klikken. Als de verbindings poging niet is toegestaan, wordt de Azure Database for MySQL server niet bereikt door de aanvraag.

> [!IMPORTANT]
> Met deze optie configureert u de firewall zo dat alle verbindingen vanuit Azure zijn toegestaan, inclusief verbindingen vanuit de abonnementen van andere klanten. Wanneer u deze optie selecteert, zorgt u er dan voor dat uw aanmeldings- en gebruikersmachtigingen de toegang beperken tot alleen geautoriseerde gebruikers.
> 

## <a name="manage-existing-server-level-firewall-rules-by-using-the-azure-portal"></a>Bestaande firewall regels op server niveau beheren met behulp van de Azure Portal
Herhaal de stappen voor het beheren van de firewall regels.
* Als u de huidige computer wilt toevoegen, klikt u op **+ Mijn IP toevoegen**. Klik op **Opslaan** om de wijzigingen op te slaan.
* Als u extra IP-adressen wilt toevoegen, typt u de naam van de **regel**, het **eerste IP-** adres en het **laatste IP-** adres. Klik op **Opslaan** om de wijzigingen op te slaan.
* Als u een bestaande regel wilt wijzigen, klikt u op een van de velden in de regel en wijzigt u vervolgens. Klik op **Opslaan** om de wijzigingen op te slaan.
* Als u een bestaande regel wilt verwijderen, klikt u op het weglatings teken [...] en klikt u vervolgens op **verwijderen**. Klik op **Opslaan** om de wijzigingen op te slaan.


## <a name="next-steps"></a>Volgende stappen
- Op dezelfde manier kunt u scripts [maken en beheren Azure database for MySQL firewall regels met behulp van Azure cli](howto-manage-firewall-using-cli.md).
- U kunt de toegang tot uw server verder beveiligen door [Virtual Network Service-eind punten en-regels te maken en beheren met behulp van de Azure Portal](howto-manage-vnet-using-portal.md).
- Zie voor hulp bij het maken van verbinding met een Azure Database for MySQL-server [verbindings bibliotheken voor Azure database for MySQL](./concepts-connection-libraries.md).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 
