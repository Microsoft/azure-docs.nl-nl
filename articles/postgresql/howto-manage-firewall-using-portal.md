---
title: Firewall regels beheren-Azure Portal-Azure Database for PostgreSQL-één server
description: Firewall regels maken en beheren voor Azure Database for PostgreSQL-één server met behulp van de Azure Portal
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 02bd4927216b6b60d2720e6f32c5768499e310bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96022197"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Firewall regels maken en beheren voor Azure Database for PostgreSQL-één server met behulp van de Azure Portal
Firewall regels op server niveau kunnen worden gebruikt voor het beheren van de toegang tot een Azure Database for PostgreSQL-server vanaf een opgegeven IP-adres of bereik van IP-adressen.

Regels voor Virtual Network (VNet) kunnen ook worden gebruikt voor het beveiligen van de toegang tot uw server. Meer informatie over [het maken en beheren van Virtual Network Service-eind punten en-regels met behulp van de Azure Portal](howto-manage-vnet-using-portal.md).

## <a name="prerequisites"></a>Vereisten
Als u deze hand leiding wilt door lopen, hebt u het volgende nodig:
- Een server [maken een Azure database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Een serverfirewallregel maken in Azure Portal
1. Klik op de pagina PostgreSQL-server, onder instellingen kop, op **verbindings beveiliging** om de pagina verbindings beveiliging te openen voor de Azure database for PostgreSQL.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/1-connection-security.png" alt-text="Azure Portal-Klik op verbindings beveiliging":::

2. Klik op **Mijn IP toevoegen** op de werk balk. Hiermee wordt automatisch een firewall regel gemaakt met het open bare IP-adres van uw computer, zoals wordt waargenomen door het Azure-systeem.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/2-add-my-ip.png" alt-text="Azure Portal-Klik op mijn IP toevoegen":::

3. Controleer uw IP-adres voordat u de configuratie opslaat. In sommige gevallen wijkt het IP-adres van Azure Portal af van het IP-adres dat wordt gebruikt voor toegang tot het internet en Azure-servers. Daarom moet u het eerste IP-adres en het laatste IP-adres wijzigen om de regel te laten werken zoals verwacht.
   Gebruik een zoek machine of een ander online hulp programma om uw eigen IP-adres te controleren. Zoek bijvoorbeeld naar ' wat is mijn IP '.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png" alt-text="Bing zoeken naar wat is mijn IP-adres":::

4. Voeg extra adresbereiken toe. In de firewall regels voor de Azure Database for PostgreSQL, kunt u één IP-adres of een bereik van adressen opgeven. Als u de regel wilt beperken tot één IP-adres, typt u hetzelfde adres in het veld voor eerste IP en laatste IP. Als u de firewall opent, kunnen beheerders, gebruikers en toepassingen toegang krijgen tot alle data bases op de PostgreSQL-server waarvoor ze geldige referenties hebben.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/4-specify-addresses.png" alt-text="Azure Portal-firewall regels":::

5. Klik op **Opslaan** op de werk balk om deze firewall regel op server niveau op te slaan. Wacht tot de bevestiging is geslaagd voor de update van de firewall regels.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png" alt-text="Azure Portal-Klik op opslaan":::

## <a name="connecting-from-azure"></a>Verbinding maken vanuit Azure
Azure-verbindingen moeten zijn ingeschakeld om toepassingen van Azure toe te staan om verbinding te maken met uw Azure Database for PostgreSQL server. Als u bijvoorbeeld een Azure Web Apps-toepassing wilt hosten, of een toepassing die wordt uitgevoerd in een Azure-VM of als u verbinding wilt maken vanuit een Azure Data Factory Data Management Gateway. De resources hoeven zich niet in dezelfde Virtual Network (VNet) of resource groep voor de firewall regel te bevinden om deze verbindingen in te scha kelen. Wanneer een toepassing vanuit Azure probeert verbinding te maken met uw databaseserver, verifieert de firewall of Azure-verbindingen zijn toegestaan. Er zijn een aantal methoden om deze typen verbindingen in te scha kelen. Een firewallinstelling waarvan het begin- en eindadres gelijk zijn aan 0.0.0.0 geeft aan dat deze verbindingen zijn toegestaan. U kunt ook de optie toegang tot **Azure-Services toestaan** in op in de Portal instellen in het deel **venster verbindings beveiliging** en **op** **Opslaan** klikken. Als de verbindings poging niet is toegestaan, wordt de Azure Database for PostgreSQL server niet bereikt door de aanvraag.

> [!IMPORTANT]
> Met deze optie configureert u de firewall zo dat alle verbindingen vanuit Azure zijn toegestaan, inclusief verbindingen vanuit de abonnementen van andere klanten. Wanneer u deze optie selecteert, zorgt u er dan voor dat uw aanmeldings- en gebruikersmachtigingen de toegang beperken tot alleen geautoriseerde gebruikers.
> 

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Bestaande firewallregels op serverniveau beheren via Azure Portal
Herhaal de stappen voor het beheren van de firewall regels.
* Als u de huidige computer wilt toevoegen, klikt u op de knop om het **IP-adres toe te voegen**. Klik op **Opslaan** om de wijzigingen op te slaan.
* As u extra IP-adressen wilt toevoegen, typt u de Regelnaam, het Eerste IP-adres en het Laatste IP-adres in. Klik op **Opslaan** om de wijzigingen op te slaan.
* Als u een bestaande regel wilt wijzigen, klikt u op een willekeurig veld in de regel om dit aan te passen. Klik op **Opslaan** om de wijzigingen op te slaan.
* Als u een bestaande regel wilt verwijderen, klikt u op het beletsel teken [...] en klikt u op **verwijderen** om de regel te verwijderen. Klik op **Opslaan** om de wijzigingen op te slaan.

## <a name="next-steps"></a>Volgende stappen
- Op dezelfde manier kunt u scripts [maken en beheren Azure database for PostgreSQL firewall regels met behulp van Azure cli](howto-manage-firewall-using-cli.md).
- U kunt de toegang tot uw server verder beveiligen door [Virtual Network Service-eind punten en-regels te maken en beheren met behulp van de Azure Portal](howto-manage-vnet-using-portal.md).
- Zie voor hulp bij het maken van verbinding met een Azure Database for PostgreSQL-server [verbindings bibliotheken voor Azure database for PostgreSQL](concepts-connection-libraries.md).
