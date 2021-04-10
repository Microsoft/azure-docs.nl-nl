---
title: SQL Data Sync instellen
description: Deze zelfstudie laat zien hoe u SQL Data Sync instelt voor Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/14/2019
ms.openlocfilehash: d6b5bab1c1b6c8db4821fdf84728eb66eb55b899
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98882226"
---
# <a name="tutorial-set-up-sql-data-sync-between-databases-in-azure-sql-database-and-sql-server"></a>Zelfstudie: SQL Data Sync instellen tussen databases in Azure SQL Database en SQL Server
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deze zelfstudie leert u hoe u SQL Data Sync kunt instellen door een synchronisatiegroep te maken die exemplaren van Azure SQL Database en SQL Server bevat. De synchronisatiegroep is aangepast geconfigureerd en wordt gesynchroniseerd volgens het schema dat u hebt ingesteld.

In de zelfstudie wordt ervan uitgegaan dat u ten minste enige ervaring hebt met SQL Database en SQL Server.

Zie [Gegevens synchroniseren tussen meerdere cloud- en on-premises databases met SQL Data Sync](sql-data-sync-data-sql-server-sql-database.md) voor een overzicht van SQL Data Sync.

Zie [Synchroniseren tussen databases in SQL Database](scripts/sql-data-sync-sync-data-between-sql-databases.md) of [tussen databases in Azure SQL Database en SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md) voor PowerShell-voorbeelden over het configureren van SQL Data Sync

> [!IMPORTANT]
> SQL Data Sync biedt op dit moment **geen** ondersteuning voor Azure SQL Managed Instance.

## <a name="create-sync-group"></a>Synchronisatiegroep maken

1. Ga naar [Azure Portal](https://portal.azure.com) om uw database in SQL Database te vinden. Zoek en selecteer **SQL-databases**.

    ![Zoeken naar databases, Microsoft Azure-portal](./media/sql-data-sync-sql-server-configure/search-for-sql-databases.png)

1. Selecteer de database die u wilt gebruiken als de hubdatabase voor Data Sync.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/select-sql-database.png" alt-text = "Select from the database list, Microsoft Azure portal":::

    > [!NOTE]
    > De hubdatabase is een centraal eindpunt van de synchronisatietopologie, waarin een synchronisatiegroep meerdere database-eindpunten heeft. Alle overige liddatabases met eindpunten in de synchronisatiegroep worden met de hubdatabase gesynchroniseerd.

1. In het menu **SQL-database** voor de geselecteerde database selecteert u **Synchroniseren met andere databases**.

    :::image type="content" source="./media/sql-data-sync-sql-server-configure/sync-to-other-databases.png" alt-text = "Sync to other databases, Microsoft Azure portal":::

1. Op de pagina **Synchroniseren met andere databases** selecteert u **Nieuwe synchronisatiegroep**. De pagina **Nieuwe synchronisatiegroep** wordt geopend met **Synchronisatiegroep maken (stap 1)** .

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/new-sync-group-private-link.png" alt-text = "Set up new sync group with private link":::

   Wijzig de volgende instellingen op de pagina **Gegevenssynchronisatiegroep maken**:

   | Instelling                        | Beschrijving |
   | ------------------------------ | ------------------------------------------------- |
   | **Naam van synchronisatiegroep** | Voer een naam in voor de nieuwe synchronisatiegroep. Deze naam is verschillend van de naam van de database zelf. |
   | **Database met metagegevens van synchronisatie** | Kies ervoor om een database te maken (aanbevolen) of om een bestaande database te gebruiken.<br/><br/>Als u **Nieuwe database** kiest, selecteert u **Nieuwe database maken**. Geef op de pagina **SQL Database** de nieuwe database een naam en configureer deze. Selecteer vervolgens **OK**.<br/><br/>Als u **Bestaande database gebruiken** kiest, selecteert u de database in de lijst. |
   | **Automatische synchronisatie** | Selecteer **Aan** of **Uit**.<br/><br/>Als u **Aan** kiest, voert u een getal in en selecteert u in de sectie **Synchronisatiefrequentie** **Seconden**, **Minuten**, **Uren** of **Dagen**.<br/> De eerste synchronisatie begint nadat de geselecteerde intervalperiode is verstreken vanaf het moment dat de configuratie wordt opgeslagen.|
   | **Conflictoplossing** | Selecteer **Hub heeft voorrang** of **Lid heeft voorrang**.<br/><br/>**Hub heeft voorrang** betekent dat als er conflicten optreden, conflicterende gegevens in de liddatabase overschreven worden met gegevens in de database van de hub.<br/><br/>**Lid heeft voorrang** betekent dat als er conflicten optreden, conflicterende gegevens in de database van de hub overschreven worden met gegevens in de liddatabase. |
   | **Private Link gebruiken** | Kies een door de service beheerd privé-eindpunt om een beveiligde verbinding tot stand te brengen tussen de synchronisatieservice en de hub-database. |

   > [!NOTE]
   > U wordt aangeraden een nieuwe, lege database te maken die als **Database met metagegevens van synchronisatie** wordt gebruikt. Met Data Sync worden tabellen in deze database gemaakt en een regelmatige workload uitgevoerd. Deze database wordt gedeeld als de **Database met metagegevens van synchronisatie** voor alle synchronisatiegroepen in een geselecteerde regio en een geselecteerd abonnement. U kunt de database of de naam ervan niet wijzigen zonder alle synchronisatiegroepen en synchronisatieagents in de regio te verwijderen.

   Selecteer **OK** en wacht tot de synchronisatiegroep is gemaakt en geïmplementeerd.
   
1. Als u op de pagina **Nieuwe synchronisatiegroep** **Private Link gebruiken** hebt geselecteerd, moet u de verbinding met het privé-eindpunt goedkeuren. De koppeling in het informatiebericht opent de privé-eindpuntverbindingen, waar u de verbinding kunt goedkeuren. 

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/approve-private-link.png" alt-text = "Approve private link":::

## <a name="add-sync-members"></a>Synchronisatieleden toevoegen

Nadat de nieuwe synchronisatiegroep is gemaakt en geïmplementeerd, wordt **Synchronisatieleden toevoegen (stap 2)** op de pagina **Nieuwe synchronisatiegroep** gemarkeerd.

In de sectie **Hubdatabase** voert u de bestaande referenties in voor de server waarop de hubdatabase zich bevindt. Voer in deze sectie geen *nieuwe* referenties in.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/steptwo.png" alt-text = "Enter existing credentials for the hub database server":::

### <a name="to-add-a-database-in-azure-sql-database"></a>Een database toevoegen in Azure SQL Database

In de sectie **Liddatabase** kunt u eventueel een database in Azure SQL Database aan de synchronisatiegroep toevoegen door **Azure SQL-database toevoegen** te selecteren. De pagina **Azure SQL Database configureren** wordt geopend.
  
   :::image type="content" source="./media/sql-data-sync-sql-server-configure/step-two-configure.png" alt-text = "Add a database to the sync group":::
   
  Op de pagina **Azure SQL Database configureren** wijzigt u de volgende instellingen:

  | Instelling                       | Beschrijving |
  | ----------------------------- | ------------------------------------------------- |
  | **Naam van synchronisatielid** | Geef een naam op voor het nieuwe synchronisatielid. Deze naam is verschillend van de naam van de database zelf. |
  | **Abonnement** | Selecteer het bijbehorende Azure-abonnement voor factureringsdoeleinden. |
  | **Azure SQL Server** | Selecteer de bestaande server. |
  | **Azure SQL Database** | Selecteer de bestaande database in SQL Database. |
  | **Synchronisatierichtingen** | Selecteer **Bidirectionele synchronisatie**, **Naar de hub** of **Van de hub**. |
  | **Gebruikersnaam** en **Wachtwoord** | Voer de bestaande referenties in voor de server waarop de liddatabase zich bevindt. Voer in deze sectie geen *nieuwe* referenties in. |
  | **Private Link gebruiken** | Kies een door de service beheerd privé-eindpunt om een beveiligde verbinding tot stand te brengen tussen de synchronisatieservice en de liddatabase. |

  Selecteer **OK** en wacht tot het nieuwe synchronisatielid is gemaakt en geïmplementeerd.

<a name="add-on-prem"></a>

### <a name="to-add-a-sql-server-database"></a>Een SQL Server-database toevoegen

In de sectie **Liddatabase** kunt u eventueel een SQL Server-database aan de synchronisatiegroep toevoegen door **Een on-premises database toevoegen** te selecteren. De pagina **On-premises configureren** wordt geopend. Hier kunt u het volgende doen:

1. Selecteer **De synchronisatieagentgateway kiezen**. De pagina **Synchronisatieagent selecteren** wordt geopend.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/steptwo-agent.png" alt-text = "Creating a sync agent":::

1. Kies op de pagina **De synchronisatieagent kiezen** of u een bestaande agent wilt gebruiken of een agent wilt maken.

   Als u **Bestaande agents** kiest, selecteert u de bestaande agent in de lijst.

   Als u **Een nieuwe agent maken** kiest, gaat u als volgt te werk:

   1. Download de gegevenssynchronisatieagent via de opgegeven koppeling en installeer deze op de computer waarop de SQL Server zich bevindt. U kunt de agent ook rechtstreeks downloaden van [Azure SQL Data Sync Agent](https://www.microsoft.com/download/details.aspx?id=27693).

      > [!IMPORTANT]
      > U moet uitgaande TCP-poort 1433 in de firewall openen om de clientagent met de server te laten communiceren.

   1. Voer een naam voor de agent in.

   1. Selecteer **Sleutel maken en genereren** en kopieer de agentsleutel naar het klembord.

   1. Selecteer **OK** om de pagina **Synchronisatieagent selecteren** te sluiten.

1. Op de SQL Server-computer zoekt u app Agent voor clientsynchronisatie en voert u deze uit.

   ![De app Clientagent voor gegevenssynchronisatie](./media/sql-data-sync-sql-server-configure/datasync-preview-clientagent.png)

    1. In de synchronisatieagent-app selecteert u **Agentsleutel verzenden**. Het dialoogvenster **Database met metagegevens van synchronisatie configureren** wordt geopend.

    1. In het dialoogvenster **Database met metagegevens van synchronisatie configureren** plakt u de agentsleutel die in Azure Portal is gekopieerd. Geef ook de bestaande referenties op voor de server waarop de database met metagegevens zich bevindt. (Als u een database met metagegevens hebt gemaakt, bevindt deze database zich op dezelfde server als de hubdatabase.) Selecteer **OK** en wacht tot de configuratie is voltooid.

        ![Agentsleutel en serverreferenties invoeren](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Als u een firewallfout krijgt, maakt u een firewallregel in Azure om binnenkomend verkeer vanaf de SQL Server-computer toe te staan. U kunt de regel handmatig maken in de portal of in SQL Server Management Studio (SSMS). In SSMS maakt u verbinding met de hubdatabase in Azure door de naam ervan in te voeren als <hub_database_name>.database.windows.net.

    1. Selecteer **Registreren** om een SQL Server-database bij de agent te registreren. Het dialoogvenster **SQL Server-configuratie** wordt geopend.

        ![SQL Server-database toevoegen en configureren](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-adddb.png)

    1. In het dialoogvenster **SQL Server-configuratie** kiest u ervoor verbinding te maken met behulp van SQL Server-verificatie of Geïntegreerde Windows-verificatie. Als u voor SQL Server-verificatie kiest, voert u de bestaande referenties in. Geef de naam op van de SQL-server en die van de database die u wilt synchroniseren. Selecteer **Verbinding testen** om uw instellingen te testen. Selecteer vervolgens **Opslaan**. De geregistreerde database wordt weergegeven in de lijst.

        ![SQL Server-database is nu geregistreerd](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-dbadded.png)

    1. Sluit de app Agent voor clientsynchronisatie.

1. In de portal, op de pagina **on-premises configureren**, selecteert u **De database selecteren**.

1. Op de pagina **De database selecteren**, in het veld **Naam van synchronisatielid**, geeft u een naam op voor het nieuwe synchronisatielid. Deze naam is verschillend van de naam van de database zelf. Selecteer de database in de lijst. In het veld **Synchronisatierichtingen** selecteert u **Bidirectionele synchronisatie**, **Naar de hub** of **Van de hub**.

    ![On-premises database selecteren](./media/sql-data-sync-sql-server-configure/datasync-preview-selectdb.png)

1. Selecteer **OK** om de pagina **Database selecteren** te sluiten. Selecteer vervolgens **OK** om de pagina **On-premises configureren** te sluiten en wacht tot het nieuwe synchronisatielid is gemaakt en geïmplementeerd. Selecteer ten slotte **OK** om de pagina **Synchronisatieleden selecteren** te sluiten.

> [!NOTE]
> Als u verbinding wilt maken met SQL Data Sync en de lokale agent, voegt u uw gebruikersnaam toe aan de rol *DataSync_Executor*. Data Sync maakt deze rol op het SQL Server-exemplaar.

## <a name="configure-sync-group"></a>Synchronisatiegroep configureren

Nadat de nieuwe leden van de synchronisatiegroep zijn gemaakt en geïmplementeerd, wordt **Synchronisatiegroep configureren (stap 3)** op de pagina **Nieuwe synchronisatiegroep** gemarkeerd.

![Instellingen voor stap 3](./media/sql-data-sync-sql-server-configure/stepthree.png)

1. Op de pagina **Tabellen** selecteert u een database in de lijst met leden van de synchronisatiegroep en selecteert u **Schema vernieuwen**.

1. Selecteer in de lijst de tabellen die u wilt synchroniseren. Standaard zijn alle kolommen geselecteerd, dus schakel het selectievakje uit voor de kolommen die u niet wilt synchroniseren. Zorg ervoor dat de primaire-sleutelkolom geselecteerd blijft.

1. Selecteer **Opslaan**.

1. Standaard worden databases pas gesynchroniseerd wanneer dit is gepland. Of de synchronisatie wordt handmatig uitgevoerd. Als u een handmatige synchronisatie wilt uitvoeren, gaat u in Azure Portal naar de database in SQL Database. Selecteer **Synchroniseren met andere databases** en selecteer de synchronisatiegroep. De pagina **Data Sync** wordt geopend. Selecteer **Synchroniseren**.

    ![Handmatig synchroniseren](./media/sql-data-sync-sql-server-configure/datasync-sync.png)

## <a name="faq"></a>Veelgestelde vragen

**Maakt SQL Data Sync volledige tabellen?**

Als synchronisatieschematabellen ontbreken in de doeldatabase, worden ze in SQL Data Sync gemaakt met de kolommen die u hebt geselecteerd. Dit resulteert echter omwille van de volgende redenen niet in een volledig identiek schema:

- Alleen kolommen die u selecteert, worden gemaakt in de doeltabel. Kolommen die niet zijn geselecteerd, worden genegeerd.
- Alleen geselecteerde kolomindexen worden gemaakt in de doeltabel. Voor niet geselecteerde kolommen worden deze indexen genegeerd.
- Indexen voor kolommen van het type XML worden niet gemaakt.
- CHECK-beperkingen worden niet gemaakt.
- Triggers voor de brontabellen worden niet gemaakt.
- Weergaven en opgeslagen procedures worden niet gemaakt.

Vanwege deze beperkingen raden we u het volgend aan:

- Maak voor productieomgevingen zelf het volledige identieke schema.
- Gebruik de functie voor automatisch inrichten wanneer u met de service experimenteert.

**Waarom worden tabellen weergeven die ik niet heb gemaakt?**

Data Sync maakt aanvullende tabellen in de database om wijzigingen bij te houden. Verwijder deze tabellen niet, anders werkt Data Sync niet meer.

**Zijn mijn gegevens convergent na een synchronisatie?**

Niet noodzakelijkerwijs. Stel u hebt een synchronisatiegroep met een hub en drie spokes (A, B en C), waarbij synchronisaties plaatsvinden tussen hub en A, hub en B, en hub en C. Als er een wijziging wordt aangebracht aan database A *nadat* synchronisatie tussen de hub en A heeft plaatsgevonden, wordt die wijziging pas naar database B of database C geschreven als de volgende synchronisatietaak wordt uitgevoerd.

**Hoe kom ik aan schemawijzigingen in een synchronisatiegroep?**

Maak alle schemawijzigingen handmatig en geef ze handmatig door.

1. Repliceer de schemawijzigingen handmatig naar de hub en naar alle synchronisatieleden.
1. Werk het synchronisatieschema bij.

Voor het toevoegen van nieuwe tabellen en kolommen:

Nieuwe tabellen en kolommen zijn niet van invloed op de huidige synchronisatie en ze worden genegeerd totdat ze aan het synchronisatieschema worden toegevoegd. Als u nieuwe databaseobjecten toevoegt, volgt u het volgende schema:

1. Voeg nieuwe tabellen of kolommen toe aan de hub en aan alle synchronisatieleden.
1. Voeg nieuwe tabellen of kolommen toe aan het synchronisatieschema.
1. Begin met het invoegen van waarden in de nieuwe tabellen en kolommen.

Voor het wijzigen van het gegevenstype van een kolom:

Wanneer u het gegevenstype van een bestaande kolom wijzigt, blijft Data Sync net zo lang werken totdat de nieuwe waarden overeenkomen met het oorspronkelijke gegevenstype dat in het synchronisatieschema is gedefinieerd. Als u bijvoorbeeld het gegevenstype in de brondatabase wijzigt van **int** in **bigint**, blijft Data Sync werken totdat u een waarde invoert die te groot is voor het gegevenstype **int**. Als u de wijziging wilt voltooien, repliceert u de schemawijziging handmatig naar de hub en naar alle synchronisatieleden, en werkt u vervolgens het synchronisatieschema bij.

**Hoe kan ik een database met Data Sync exporteren en importeren?**

Nadat u een database als een *BACPAC*-bestand hebt geëxporteerd en het bestand importeert om een database te maken, gaat u als volgt te werk om Data Sync in de nieuwe database te gebruiken:

1. Schoon de Data Sync-objecten en aanvullende tabellen in de nieuwe database op met behulp van [dit script](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql). Met het script worden alle vereiste Data Sync-objecten uit de database verwijderd.
1. Maak de synchronisatiegroep opnieuw met de nieuwe database. Als u de oude synchronisatiegroep niet meer nodig hebt, verwijdert u deze.

**Waar vind ik informatie over de clientagent?**

Zie [Veelgestelde vragen over agents](sql-data-sync-agent-overview.md#agent-faq) voor veelgestelde vragen over de clientagent.

**Moet de koppeling naar het privé-eindpunt handmatig worden goedgekeurd voordat ik deze kan gaan gebruiken?**

Ja, u moet het door de service beheerde privé-eindpunt handmatig goedkeuren op de pagina Verbindingen met privé-eindpunten van Azure Portal tijdens de implementatie van de synchronisatiegroep of met behulp van PowerShell.

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd. U hebt een synchronisatiegroep gemaakt die een SQL Database-exemplaar en een SQL Server-database bevat.

Zie de volgende onderwerpen voor meer informatie over SQL Data Sync:

- [Data Sync-agent voor Azure SQL Data Sync](sql-data-sync-agent-overview.md)
- [Best practices](sql-data-sync-best-practices.md) en [Problemen met Azure SQL Data Sync oplossen](sql-data-sync-troubleshoot.md)
- [SQL Data Sync bewaken met Azure Monitor-logboeken](./monitor-tune-overview.md)
- [Het synchronisatieschema bijwerken met Transact-SQL](sql-data-sync-update-sync-schema.md) of [PowerShell](scripts/update-sync-schema-in-sync-group.md)

Zie de volgende onderwerpen voor meer informatie over SQL Database:

- [Wat is de Azure SQL Database-service?](sql-database-paas-overview.md)
- [Database Lifecycle Management (DLM)](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
