---
title: Verbinding maken met SQL Server, Azure SQL Database of Azure SQL Managed instance
description: Automatiseer taken voor SQL-data bases on-premises of in de Cloud met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 01/07/2021
tags: connectors
ms.openlocfilehash: 75c657236b6e06a7e0f6c717d746bcc8c034d423
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98013444"
---
# <a name="automate-workflows-for-a-sql-database-by-using-azure-logic-apps"></a>Werk stromen automatiseren voor een SQL database met behulp van Azure Logic Apps

In dit artikel wordt beschreven hoe u toegang kunt krijgen tot gegevens in uw SQL database vanuit een logische app met de SQL Server-connector. Op die manier kunt u taken, processen of werk stromen automatiseren die uw SQL-gegevens en-resources beheren door logische apps te maken. De SQL Server-connector werkt zowel voor [SQL Server](/sql/sql-server/sql-server-technical-documentation) als [Azure SQL database](../azure-sql/database/sql-database-paas-overview.md) en [Azure SQL Managed instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).

U kunt logische apps maken die worden uitgevoerd wanneer deze worden geactiveerd door gebeurtenissen in uw SQL database of in andere systemen, zoals Dynamics CRM Online. Uw Logic apps kunnen ook gegevens ophalen, invoegen en verwijderen, samen met het uitvoeren van SQL-query's en opgeslagen procedures. U kunt bijvoorbeeld een logische app maken die automatisch controleert op nieuwe records in Dynamics CRM Online, items toevoegt aan uw SQL database voor nieuwe records en vervolgens e-mail waarschuwingen over de toegevoegde items verzendt.

Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps](../logic-apps/logic-apps-overview.md) en [Quick Start: uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md). Zie de pagina met Naslag informatie over [SQL Server-connector](/connectors/sql/)voor connector-specifieke technische gegevens, beperkingen en bekende problemen.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een [SQL Server-Data Base](/sql/relational-databases/databases/create-a-database), [Azure SQL database](../azure-sql/database/single-database-create-quickstart.md)of [Azure SQL Managed instance](../azure-sql/managed-instance/instance-create-quickstart.md).

  Uw tabellen moeten gegevens bevatten zodat uw logische app resultaten kan retour neren wanneer bewerkingen worden aangeroepen. Als u Azure SQL Database gebruikt, kunt u voorbeeld databases gebruiken die zijn opgenomen.

* De naam van uw SQL-Server, de database naam, uw gebruikers naam en uw wacht woord. U hebt deze referenties nodig zodat u uw logica kunt machtigen om toegang te krijgen tot uw SQL Server.

  * Voor on-premises SQL Server kunt u deze details vinden in de connection string:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Voor Azure SQL Database kunt u deze informatie vinden in de connection string.
  
    Als u deze teken reeks bijvoorbeeld wilt zoeken in de Azure Portal, opent u de data base. Selecteer in het menu Data Base ofwel **verbindings reeksen** of **Eigenschappen**:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

<a name="multi-tenant-or-ise"></a>

* Op basis van het feit of uw Logic apps worden uitgevoerd in Global, multi tenant Azure of een [Integration service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), zijn hier andere vereisten voor het maken van verbinding met on-premises SQL Server:

  * Voor Logic apps in Global, multi tenant Azure dat verbinding maakt met on-premises SQL Server, moet u de [on-premises gegevens gateway](../logic-apps/logic-apps-gateway-install.md) installeren op een lokale computer en een [gegevens gateway resource die al in Azure is gemaakt](../logic-apps/logic-apps-gateway-connection.md).

  * Voor Logic apps in een ISE die verbinding maken met on-premises SQL Server en Windows-authenticatie gebruiken, ondersteunt de ISE-connector van het SQL Server geen ondersteuning voor Windows-verificatie. Daarom moet u nog steeds de gegevens gateway en de niet-ISE SQL Server-connector gebruiken. Voor andere verificatie typen hoeft u de gegevens gateway niet te gebruiken en kunt u de ISE-connector gebruiken.

* De logische app waar u toegang tot uw SQL database nodig hebt. Als u uw logische app wilt starten met een SQL-trigger, hebt u een [lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)nodig.

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Verbinding maken met uw database

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Ga nu verder met de volgende stappen:

* [Verbinding maken met een Cloud Azure SQL Database of een beheerd exemplaar](#connect-azure-sql-db)
* [Verbinding maken met on-premises SQL Server](#connect-sql-server)

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database-or-managed-instance"></a>Verbinding maken met Azure SQL Database of een beheerd exemplaar

Voor toegang tot een Azure SQL Managed instance zonder gebruik te maken van de on-premises gegevens gateway of integratie service omgeving moet u [het open bare eind punt instellen op het door Azure SQL beheerde exemplaar](../azure-sql/managed-instance/public-endpoint-configure.md). Het open bare eind punt gebruikt poort 3342, dus zorg ervoor dat u dit poort nummer opgeeft wanneer u de verbinding maakt vanuit uw logische app.


De eerste keer dat u een SQL- [trigger](#add-sql-trigger) of [SQL-actie](#add-sql-action)toevoegt en u nog geen verbinding hebt gemaakt met uw data base, wordt u gevraagd om de volgende stappen uit te voeren:

1. Selecteer bij **verificatie type** de verificatie die vereist is en is ingeschakeld in uw data base in Azure SQL database of Azure SQL Managed instance:

   | Verificatie | Beschrijving |
   |----------------|-------------|
   | [**Geïntegreerd met Azure AD**](../azure-sql/database/authentication-aad-overview.md) | -Ondersteunt zowel de niet-ISE als de ISE SQL Server-connector. <p><p>-Vereist een geldige identiteit in Azure Active Directory (Azure AD) die toegang heeft tot uw data base. <p>Raadpleeg de volgende onderwerpen voor meer informatie: <p>- [Overzicht van Azure SQL-beveiliging-verificatie](../azure-sql/database/security-overview.md#authentication) <br>- [Database toegang verlenen aan Azure SQL-verificatie en autorisatie](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) <br>- [Azure SQL-geïntegreerde Azure AD-verificatie](../azure-sql/database/authentication-aad-overview.md) |
   | [**SQL Server-verificatie**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | -Ondersteunt zowel de niet-ISE als de ISE SQL Server-connector. <p><p>-Vereist een geldige gebruikers naam en een sterk wacht woord die zijn gemaakt en opgeslagen in uw data base. <p>Raadpleeg de volgende onderwerpen voor meer informatie: <p>- [Overzicht van Azure SQL-beveiliging-verificatie](../azure-sql/database/security-overview.md#authentication) <br>- [Database toegang verlenen aan Azure SQL-verificatie en autorisatie](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) |
   |||

   Dit voor beeld gaat verder met **Azure AD Integrated**:

   ![Scherm afbeelding met het verbindings venster ' SQL Server ' met de geopende lijst ' verificatie type ' en ' Azure AD Integrated ' geselecteerd.](./media/connectors-create-api-sqlazure/select-azure-ad-authentication.png)

1. Nadat u **Azure AD Integrated** hebt geselecteerd, selecteert u **Aanmelden**. Selecteer uw gebruikers referenties voor verificatie op basis van het feit of u Azure SQL Database of Azure SQL Managed instance gebruikt.

1. Selecteer deze waarden voor uw Data Base:

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Servernaam** | Ja | Het adres voor uw SQL Server, bijvoorbeeld `Fabrikam-Azure-SQL.database.windows.net` |
   | **Databasenaam** | Ja | De naam voor uw SQL database, bijvoorbeeld `Fabrikam-Azure-SQL-DB` |
   | **Tabel naam** | Ja | De tabel die u wilt gebruiken, bijvoorbeeld `SalesLT.Customer` |
   ||||

   > [!TIP]
   > U hebt de volgende opties om uw data base en tabel gegevens op te geven:
   > 
   > * Deze informatie vindt u in de connection string van uw data base. Zoek en open bijvoorbeeld uw data base in de Azure Portal. Selecteer in het menu Data Base ofwel **verbindings reeksen** of **Eigenschappen**, waar u deze teken reeks kunt vinden:
   >
   >   `Server=tcp:{your-server-address}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`
   >
   > * Tabellen in systeem databases worden standaard gefilterd, zodat ze mogelijk niet automatisch worden weer gegeven wanneer u een systeem database selecteert. Als alternatief kunt u de tabel naam hand matig invoeren nadat u **aangepaste waarde invoeren** hebt geselecteerd in de lijst met data bases.
   >

   In dit voor beeld ziet u hoe deze waarden eruit kunnen zien:

   ![Verbinding maken met SQL database](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Ga nu verder met de stappen die u nog niet hebt voltooid in [een SQL-trigger toevoegen](#add-sql-trigger) of [een SQL-actie toevoegen](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-on-premises-sql-server"></a>Verbinding maken met on-premises SQL Server

De eerste keer dat u een SQL- [trigger](#add-sql-trigger) of [SQL-actie](#add-sql-action)toevoegt en u nog geen verbinding hebt gemaakt met uw data base, wordt u gevraagd om de volgende stappen uit te voeren:

1. Voor verbindingen met uw on-premises SQL Server die de on-premises gegevens gateway nodig hebben, moet u ervoor zorgen dat u [deze vereisten hebt voltooid](#multi-tenant-or-ise).

   Anders wordt uw gegevens gateway bron niet weer gegeven in de lijst met **verbindings gateways** wanneer u de verbinding maakt.

1. Selecteer bij **verificatie type** de verificatie die vereist is en is ingeschakeld op uw SQL Server:

   | Verificatie | Beschrijving |
   |----------------|-------------|
   | [**Windows-verificatie**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) | -Ondersteunt alleen de niet-ISE SQL Server-connector, waarvoor een gegevens gateway resource is vereist die eerder in azure voor uw verbinding is gemaakt, ongeacht of u gebruikmaakt van multi tenant Azure of een ISE. <p><p>-Vereist een geldige Windows-gebruikers naam en-wacht woord om uw identiteit te bevestigen via uw Windows-account. <p>Zie [Windows-verificatie](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) voor meer informatie. |
   | [**SQL Server-verificatie**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | -Ondersteunt zowel de niet-ISE als de ISE SQL Server-connector. <p><p>-Vereist een geldige gebruikers naam en een sterk wacht woord die zijn gemaakt en opgeslagen in uw SQL Server. <p>Zie [SQL Server-verificatie](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication)voor meer informatie. |
   |||

   Dit voor beeld gaat door met **Windows-verificatie**:

   ![Selecteer het verificatie type dat u wilt gebruiken](./media/connectors-create-api-sqlazure/select-windows-authentication.png)

1. Selecteer of geef de volgende waarden op voor uw SQL database:

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **SQL Server-naam** | Ja | Het adres voor uw SQL Server, bijvoorbeeld `Fabrikam-Azure-SQL.database.windows.net` |
   | **SQL-databasenaam** | Ja | De naam voor uw SQL Server-Data Base, bijvoorbeeld `Fabrikam-Azure-SQL-DB` |
   | **Gebruikersnaam** | Ja | Uw gebruikers naam voor de SQL-Server en-data base |
   | **Wachtwoord** | Ja | Uw wacht woord voor de SQL-Server en-data base |
   | **Abonnement** |  Ja, voor Windows-verificatie | Het Azure-abonnement voor de gegevens gateway resource die u eerder hebt gemaakt in azure |
   | **Verbindings gateway** | Ja, voor Windows-verificatie | De naam voor de gegevens gateway resource die u eerder hebt gemaakt in azure <p><p>**Tip**: als uw gateway niet in de lijst wordt weer gegeven, controleert u of u [uw gateway](../logic-apps/logic-apps-gateway-connection.md)correct hebt ingesteld. |
   |||

   > [!TIP]
   > U kunt deze informatie vinden in de connection string van uw Data Base:
   > 
   > * `Server={your-server-address}`
   > * `Database={your-database-name}`
   > * `User ID={your-user-name}`
   > * `Password={your-password}`

   In dit voor beeld ziet u hoe deze waarden eruit kunnen zien:

   ![SQL Server verbinding maken is voltooid](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Wanneer u klaar bent, selecteert u **maken**.

1. Ga nu verder met de stappen die u nog niet hebt voltooid in [een SQL-trigger toevoegen](#add-sql-trigger) of [een SQL-actie toevoegen](#add-sql-action).

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Een SQL-trigger toevoegen

1. Maak in de [Azure Portal](https://portal.azure.com) of in Visual Studio een lege logische app, waarmee de ontwerp functie voor logische apps wordt geopend. In dit voor beeld wordt de Azure Portal voortgezet.

1. Voer op de ontwerp functie in het zoekvak in `sql server` . Selecteer in de lijst triggers de gewenste SQL-trigger. In dit voor beeld wordt de trigger **Wanneer een item is gemaakt** gebruikt.

   ![De trigger ' wanneer een item wordt gemaakt ' selecteren](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Als u voor de eerste keer verbinding maakt met uw SQL database, wordt u gevraagd om [uw SQL database verbinding nu te maken](#create-connection). Nadat u deze verbinding hebt gemaakt, kunt u door gaan met de volgende stap.

1. Geef in de trigger het interval en de frequentie op voor hoe vaak de trigger de tabel controleert.

1. Als u andere beschik bare eigenschappen voor deze trigger wilt toevoegen, opent u de lijst **nieuwe para meter toevoegen** .

   Deze trigger retourneert slechts één rij uit de geselecteerde tabel en niets anders. Als u andere taken wilt uitvoeren, kunt u door gaan door een [SQL-connector actie](#add-sql-action) toe te voegen of door een [andere actie](../connectors/apis-list.md) uit te voeren waarmee de volgende taak wordt uitgevoerd in de werk stroom van de logische app.

   Als u de gegevens in deze rij bijvoorbeeld wilt weer geven, kunt u andere acties toevoegen waarmee een bestand wordt gemaakt dat de velden uit de geretourneerde rij bevat, en vervolgens e-mail waarschuwingen verzenden. Zie de [referentie pagina van de connector](/connectors/sql/)voor meer informatie over andere beschik bare acties voor deze connector.

1. Selecteer **Opslaan** op de werkbalk van de ontwerper.

   Hoewel met deze stap automatisch uw logische app in azure wordt ingeschakeld en gepubliceerd, is de enige actie die momenteel door uw logische app wordt uitgevoerd, het controleren van uw Data Base op basis van het opgegeven interval en de frequentie.

<a name="trigger-recurrence-shift-drift"></a>

### <a name="trigger-recurrence-shift-and-drift"></a>Verschuiving van terugkeer patroon en drift activeren

Op verbindingen gebaseerde triggers waarbij u eerst een verbinding moet maken, zoals de SQL-trigger, verschillen van ingebouwde triggers die in Azure Logic Apps systeem eigen worden uitgevoerd, zoals de [terugkeer patroon trigger](../connectors/connectors-native-recurrence.md). In terugkerende activeringen op basis van een verbinding is de terugkeer planning niet het enige stuur programma dat de uitvoering beheert, en de tijd zone bepaalt alleen de eerste begin tijd. De volgende uitvoeringen zijn afhankelijk van het terugkeer schema, de laatste uitvoering van triggers *en* andere factoren die kunnen leiden tot een onverwacht gedrag of het produceren van onverwachte werking, zoals het niet behouden van het opgegeven schema wanneer zomer tijd (DST) begint en eindigt. Om ervoor te zorgen dat de tijd van het terugkeer patroon niet wordt verschoven wanneer het ZOMERtijd duurt, moet u het terugkeer patroon hand matig aanpassen zodat uw logische app op het verwachte tijdstip blijft worden uitgevoerd. Anders wordt de begin tijd één uur vooruit geschoven wanneer zomer tijd wordt gestart en één uur terug wanneer de DST eindigt. Zie [terugkeer patroon voor triggers op basis](../connectors/apis-list.md#recurrence-connection-based)van een verbinding voor meer informatie.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Een SQL-actie toevoegen

In dit voor beeld wordt de logische app gestart met de [terugkeer patroon](../connectors/connectors-native-recurrence.md)en wordt een actie aangeroepen waarmee een rij uit een SQL database wordt opgehaald.

1. Open in de [Azure Portal](https://portal.azure.com) of in Visual Studio uw logische app in de ontwerp functie voor logische apps. In dit voor beeld wordt de Azure Portal voortgezet.

1. Selecteer **nieuwe stap** onder de trigger of actie waar u de SQL-actie wilt toevoegen.

   ![Een actie toevoegen aan uw logische app](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Als u een actie wilt toevoegen tussen de bestaande stappen, plaatst u de muis aanwijzer op de verbindings pijl. Selecteer het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Voer in het zoekvak onder **Kies een actie** `sql server` in. Selecteer in de lijst acties de gewenste SQL-actie. In dit voor beeld wordt de actie **rij ophalen** gebruikt, waarmee één record wordt opgehaald.

   ![Selecteer de actie SQL-rij ophalen](./media/connectors-create-api-sqlazure/select-sql-get-row-action.png)

1. Als u voor de eerste keer verbinding maakt met uw SQL database, wordt u gevraagd om [uw SQL database verbinding nu te maken](#create-connection). Nadat u deze verbinding hebt gemaakt, kunt u door gaan met de volgende stap.

1. Selecteer de **tabel naam** `SalesLT.Customer` in dit voor beeld. Voer de **rij-id** in voor de record die u wilt.

   ![Tabel naam selecteren en rij-ID opgeven](./media/connectors-create-api-sqlazure/specify-table-row-id.png)

   Met deze actie wordt slechts één rij uit de geselecteerde tabel geretourneerd, niets anders. Om de gegevens in deze rij weer te geven, kunt u dus ook andere acties toevoegen die een bestand maken dat de velden uit de geretourneerde rij bevat, en dat bestand opslaan in een opslag account in de Cloud. Zie de [referentie pagina van de connector](/connectors/sql/)voor meer informatie over andere beschik bare acties voor deze connector.

1. Selecteer **Opslaan** op de werkbalk van de ontwerper wanneer u klaar bent.

   Met deze stap wordt uw logische app Live in azure automatisch ingeschakeld en gepubliceerd.

<a name="handle-bulk-data"></a>

## <a name="handle-bulk-data"></a>Bulk gegevens verwerken

Soms moet u met de resultaten sets zo groot zijn dat de connector niet alle resultaten tegelijk retourneert, of u wilt de controle over de grootte en de structuur voor uw resultaten sets beter te bepalen. Hier volgt een aantal manieren waarop u dergelijke grote resultaten sets kunt verwerken:

* Schakel *paginering* in om de resultaten te beheren als kleinere sets. Zie voor meer informatie [bulk gegevens, records en items ophalen met behulp van paginering](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md). Zie [SQL-paginering voor bulk gegevens overdracht met Logic apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)voor meer informatie.

* Maak een [*opgeslagen procedure*](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) waarmee de resultaten op de gewenste manier worden georganiseerd. De SQL-connector biedt veel back-end-functies die u kunt gebruiken met Azure Logic Apps, zodat u gemakkelijker zakelijke taken kunt automatiseren die samen werken met SQL database tabellen.

  Wanneer u meerdere rijen haalt of invoegt, kan de logische app deze rijen door lopen met behulp van een [*until-lus*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) binnen deze [grenzen](../logic-apps/logic-apps-limits-and-config.md). Als uw logische app echter moet werken met record sets zo groot is, bijvoorbeeld duizenden of miljoenen rijen, die u de kosten wilt beperken die voortvloeien uit aanroepen naar de data base.

  Als u de resultaten op de gewenste manier wilt indelen, kunt u een opgeslagen procedure maken die wordt uitgevoerd in uw SQL-exemplaar en gebruikmaakt van de **Select-order by-** instructie. Met deze oplossing hebt u meer controle over de grootte en de structuur van uw resultaten. Uw logische app roept de opgeslagen procedure aan met behulp van de actie voor het uitvoeren van een **opgeslagen procedure** van de SQL Server-connector. Zie [SELECT-Order By-Component](/sql/t-sql/queries/select-order-by-clause-transact-sql)voor meer informatie.

  > [!NOTE]
  > De SQL-connector heeft een time-outlimiet van een opgeslagen procedure die [korter is dan 2 minuten](/connectors/sql/#known-issues-and-limitations). Een aantal opgeslagen procedures kan langer duren dan deze limiet is bereikt, waardoor `504 Timeout` er een fout optreedt. U kunt dit probleem omzeilen door gebruik te maken van een SQL-voltooiings trigger, een native SQL Pass Through-query, een status tabel en taken aan de server zijde.
  > 
  > Voor deze taak kunt u de [Azure elastische taak agent](../azure-sql/database/elastic-jobs-overview.md) gebruiken voor [Azure SQL database](../azure-sql/database/sql-database-paas-overview.md). Voor [SQL Server on-premises](/sql/sql-server/sql-server-technical-documentation) en [Azure SQL Managed instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)kunt u de [SQL Server Agent](/sql/ssms/agent/sql-server-agent)gebruiken. Zie voor meer informatie [verwerken van langlopende opgeslagen procedure time-outs in de SQL-connector voor Azure Logic apps](../logic-apps/handle-long-running-stored-procedures-sql-connector.md).

### <a name="handle-dynamic-bulk-data"></a>Dynamische bulk gegevens verwerken

Wanneer u een opgeslagen procedure aanroept met behulp van de SQL Server-connector, is de geretourneerde uitvoer soms dynamisch. In dit scenario voert u de volgende stappen uit:

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps.

1. De uitvoer indeling weer geven door een test uitvoering uit te voeren. Kopieer uw voorbeeld uitvoer en sla deze op.

1. Selecteer in de ontwerp functie, onder de actie waar u de opgeslagen procedure aanroept, de optie **nieuwe stap**.

1. Zoek onder **Kies een actie** de actie [**JSON parseren**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) en selecteer deze.

1. Selecteer in de actie **JSON parseren** **voorbeeld lading gebruiken om schema te genereren**.

1. Plak in het vak **een voor beeld van een JSON-nettolading invoeren of plakken** uw voorbeeld uitvoer en selecteer **gereed**.

   > [!NOTE]
   > Als er een fout bericht wordt weer gegeven dat Logic Apps geen schema kan genereren, controleert u of de syntaxis van de voorbeeld uitvoer correct is ingedeeld. Als u het schema nog niet kunt genereren, voert u in het vak **schema** het schema hand matig in.

1. Selecteer **Opslaan** op de werkbalk van de ontwerper.

1. Als u wilt verwijzen naar de eigenschappen van de JSON-inhoud, klikt u in de bewerkings vakken waar u naar deze eigenschappen wilt verwijzen, zodat de lijst met dynamische inhoud wordt weer gegeven. Selecteer in de lijst onder de kop [**JSON parseren**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) de gegevens tokens voor de gewenste eigenschappen van de JSON-inhoud.

## <a name="troubleshoot-problems"></a>Problemen oplossen

<a name="connection-problems"></a>

### <a name="connection-problems"></a>Verbindingsproblemen

Problemen met de verbinding kunnen zich doorgaans voordoen, zodat u het probleem kunt oplossen door [verbindings fouten te SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server). Hier volgen enkele voorbeelden:

* `A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.`

* `(provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server) (Microsoft SQL Server, Error: 53)`

* `(provider: TCP Provider, error: 0 - No such host is known.) (Microsoft SQL Server, Error: 11001)`

## <a name="connector-specific-details"></a>Connector-specifieke Details

Zie de [referentie pagina van de connector](/connectors/sql/)die wordt gegenereerd op basis van de Swagger-beschrijving voor technische informatie over de triggers, acties en limieten van deze connector.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [connectors voor Azure Logic apps](../connectors/apis-list.md)
