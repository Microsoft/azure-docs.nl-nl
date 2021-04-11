---
title: 'Quickstart: Verbinding maken - MySQL Workbench - Azure Database for MySQL - Flexible Server'
description: Deze quickstart bevat de stappen voor het gebruik van MySQL Workbench om verbinding te maken met en gegevens op te vragen uit Azure Database for MySQL - Flexible Server.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: 95ecb64165075a7effe0c6eaf568ee172ad67b9b
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226291"
---
# <a name="quickstart-use-mysql-workbench-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server-preview"></a>Quickstart: MySQL Workbench gebruiken om verbinding te maken en gegevens op te vragen in Azure Database for MySQL - Flexible Server (preview-versie)


> [!IMPORTANT] 
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

In deze quickstart ziet u hoe u met behulp van een MySQL Workbench-toepassing verbinding maakt met een Azure Database for MySQL Flexible Server.

## <a name="prerequisites"></a>Vereisten

In deze snelstartgids worden de resources die in een van deze handleidingen zijn gemaakt, als uitgangspunt gebruikt:

- [Een Azure Database for MySQL Flexible Server maken met behulp van de Azure-portal](./quickstart-create-server-portal.md)
- [Een Azure Database for MySQL Flexible Server maken met behulp van Azure CLI](./quickstart-create-server-cli.md)

## <a name="preparing-your-client-workstation"></a>Uw clientwerkstation voorbereiden
- Als u een flexibele server hebt gemaakt met *Private Access (VNet-integratie)* , moet u verbinding maken met uw server vanuit een resource binnen hetzelfde VNet als uw server. U kunt een virtuele machine maken en toevoegen aan het VNet dat is gemaakt met de flexibele server. Zie [Virtueel netwerk met Azure Database for MySQL Flexible Server maken met behulp van Azure CLI](./how-to-manage-virtual-network-cli.md).
- Als u uw flexibele server met *Openbare toegang (toegestane IP-adressen)* hebt gemaakt, kunt u uw lokale IP-adres toevoegen aan de lijst met firewallregels op uw server. Zie [Firewallregels voor Azure Database for MySQL Flexible Server maken en beheren met behulp van de Azure CLI](./how-to-manage-firewall-cli.md).

- MySQL Workbench downloaden en installeren op de computer vanaf de [MySQL-website](https://dev.mysql.com/downloads/workbench/).

## <a name="get-connection-information"></a>Verbindingsgegevens ophalen

Haal de verbindingsgegevens op die nodig zijn om verbinding te maken met de flexibele server. U hebt de volledig gekwalificeerde servernaam en aanmeldingsreferenties nodig.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer in het menu aan de linkerkant in de Azure-portal **Alle resources** en zoek naar de server die u hebt gemaakt (bijvoorbeeld **mydemoserver**).
3. Selecteer de servernaam.
4. Ga naar het venster **Overzicht** van de server en noteer de **Servernaam** en de **Aanmeldingsnaam van de serverbeheerder**. Als u uw wachtwoord vergeet, kunt u het wachtwoord in dit venster opnieuw instellen.
<!--- :::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::--->

## <a name="connect-to-the-server-using-mysql-workbench"></a>Verbinding maken met de server met MySQL Workbench

Ga als volgt te werk om verbinding te maken met Azure Database for MySQL Flexible Server met behulp van MySQL Workbench:

1. Start de toepassing MySQL Workbench op de computer.

2. Voer in het dialoogvenster **Nieuwe verbinding instellen** de volgende gegevens in op het tabblad **Parameters**:

    :::image type="content" source="./media/connect-workbench/2-setup-new-connection.png" alt-text="nieuwe verbinding instellen":::

    | **Parameters** | **Voorgestelde waarde** | **Beschrijving van veld** |
    |---|---|---|
    |    Verbindingsnaam | Demo-verbinding | Geef een label op voor deze verbinding. |
    | Verbindingsmethode | Standard (TCP/IP) | Standard (TCP/IP) is voldoende. |
    | Hostnaam | *servernaam* | Geef de waarde van de servernaam op die u hebt gebruikt toen u de Azure Database voor MySQL-server eerder hebt gemaakt. De server in ons voorbeeld is mydemoserver.mysql.database.azure.com. Gebruik de FQDN (Fully Qualified Domain Name) (\*.mysql.database.azure.com) zoals weergegeven in het voorbeeld. Volg de stappen in de vorige sectie om de verbindingsgegevens op te halen als u de servernaam bent vergeten.  |
    | Poort | 3306 | Gebruik altijd poort 3306 bij het verbinden met Azure Database voor MySQL. |
    | Gebruikersnaam |  *aanmeldnaam van serverbeheerder* | Typ de gebruikersnaam van de serverbeheerder die u hebt opgegeven toen u de Azure Database voor MySQL-server eerder hebt gemaakt. De gebruikersnaam in ons voorbeeld is myadmin. Volg de stappen in de vorige sectie om de verbindingsgegevens op te halen als u de gebruikersnaam bent vergeten.
    | Wachtwoord | Uw wachtwoord | Klik op de knop **Opslaan in de kluis...** om het wachtwoord op te slaan. |

3. Klik op **Verbinding testen** om te controleren of alle parameters juist zijn geconfigureerd.

4. Klik op **OK** om de verbinding op te slaan.

5. Klik in de lijst met **MySQL Connections** op de tegel die voor uw server is bedoeld en wacht tot de verbinding tot stand is gebracht.

    Er wordt een nieuw SQL-tabblad geopend met een lege editor waar u uw query's kunt typen.

> [!NOTE]
> Versleutelde verbinding met TLS 1.2 is vereist en wordt afgedwongen op uw Azure Database for MySQL Flexible Server. Hoewel er gewoonlijk geen aanvullende configuratie met TLS/SSL-certificaten vereist is om MySQL Workbench verbinding met de server te laten maken, verdient het aanbeveling de TLS/SSL CA-certificering te binden aan MySQL Workbench. Zie [Verbinding maken met behulp van TLS/SSL](./how-to-connect-tls-ssl.md) voor meer informatie

## <a name="create-a-table-insert-data-read-data-update-data-delete-data"></a>Tabel maken, gegevens invoegen, gegevens lezen, gegevens bijwerken, gegevens verwijderen

1. Kopieer de SQL-voorbeeldcode en plak deze in een leeg SQL-tabblad om enkele voorbeeldgegevens te illustreren.

    Met deze code wordt een lege database gemaakt met de naam quickstartdb. Vervolgens wordt er een voorbeeldtabel gemaakt met de naam inventory (voorraad). Er worden een paar rijen ingevoegd, die vervolgens gelezen worden. De gegevens worden gewijzigd met een update-instructie, waarna de rijen opnieuw worden gelezen. Ten slotte wordt er een rij verwijderd, en worden de rijen opnieuw gelezen.

    ```sql
    -- Create a database
    -- DROP DATABASE IF EXISTS quickstartdb;
    CREATE DATABASE quickstartdb;
    USE quickstartdb;

    -- Create a table and insert rows
    DROP TABLE IF EXISTS inventory;
    CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
    INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
    INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
    INSERT INTO inventory (name, quantity) VALUES ('apple', 100);

    -- Read
    SELECT * FROM inventory;

    -- Update
    UPDATE inventory SET quantity = 200 WHERE id = 1;
    SELECT * FROM inventory;

    -- Delete
    DELETE FROM inventory WHERE id = 2;
    SELECT * FROM inventory;
    ```

    In de schermopname wordt een voorbeeld getoond van SQL-code in SQL Workbench, alsmede de uitvoer nadat de code is uitgevoerd.

    :::image type="content" source="./media/connect-workbench/3-workbench-sql-tab.png" alt-text="Tabblad MySQL Workbench voor het uitvoeren van SQL-code":::

2. Als u de SQL-code wilt uitvoeren, klikt u op het bliksemstraalpictogram in de werkbalk van het tabblad **SQL File**.
3. Let op de drie tabbladen met resultaten in de sectie **Result Grid** in het midden van de pagina.
4. Let ook op de lijst **Output** onderaan de pagina. De status van elke opdracht wordt weergegeven.

U hebt nu verbinding gemaakt met Azure Database for MySQL Flexible Server met behulp van MyQL Workbench en u hebt query's uitgevoerd op gegevens met behulp van de taal SQL.

## <a name="next-steps"></a>Volgende stappen
- [Versleutelde verbinding met behulp van Transport Layer Security (TLS 1.2) in Azure Database for MySQL - Flexible Server](./how-to-connect-tls-ssl.md).
- Meer informatie over [Netwerken in Azure Database for MySQL - Flexible Server](./concepts-networking.md).
- [Firewallregels voor Azure Database for MySQL Flexible Server maken met behulp van de Azure-portal](./how-to-manage-firewall-portal.md).
- [Virtueel netwerk met Azure Database for MySQL Flexible Server maken met behulp van de Azure-portal](./how-to-manage-virtual-network-portal.md).
