---
title: Verbinding maken met SQL Server, Azure SQL Database of Azure SQL Managed Instance
description: Taken voor SQL-databases on-premises of in de cloud automatiseren met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 03/24/2021
tags: connectors
ms.openlocfilehash: 2e06616914f1e78a71a540fbd64021c0e1bfcbab
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785969"
---
# <a name="automate-workflows-for-a-sql-database-by-using-azure-logic-apps"></a>Werkstromen voor een SQL database automatiseren met behulp van Azure Logic Apps

In dit artikel wordt beschreven hoe u toegang hebt tot gegevens in uw SQL database vanuit een logische app met de SQL Server connector. Op die manier kunt u taken, processen of werkstromen automatiseren die uw SQL-gegevens en -resources beheren door logische apps te maken. De SQL Server-connector werkt [voor SQL Server](/sql/sql-server/sql-server-technical-documentation) en Azure SQL Database [en](../azure-sql/database/sql-database-paas-overview.md) [Azure SQL Managed Instance.](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)

U kunt logische apps maken die worden uitgevoerd wanneer ze worden geactiveerd door gebeurtenissen in uw SQL database of in andere systemen, zoals Dynamics CRM Online. Uw logische apps kunnen ook gegevens opvragen, invoegen en verwijderen, samen met het uitvoeren van SQL-query's en opgeslagen procedures. U kunt bijvoorbeeld een logische app bouwen die automatisch controleert op nieuwe records in Dynamics CRM Online, items toevoegt aan uw SQL database voor nieuwe records en vervolgens e-mailwaarschuwingen verzendt over de toegevoegde items.

Als u geen tijd hebt voor logic apps, bekijkt u [Wat is Azure Logic Apps](../logic-apps/logic-apps-overview.md) en [Quickstart: Uw eerste logische app maken.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Zie de referentiepagina voor de connector voor connectorspecifieke technische informatie, beperkingen en bekende [problemen SQL Server connector.](/connectors/sql/)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een [SQL Server database](/sql/relational-databases/databases/create-a-database), [Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md)of [Azure SQL Managed Instance](../azure-sql/managed-instance/instance-create-quickstart.md).

  Uw tabellen moeten gegevens bevatten, zodat uw logische app resultaten kan retourneren bij het aanroepen van bewerkingen. Als u Azure SQL Database, kunt u voorbeelddatabases gebruiken, die zijn opgenomen.

* Uw SQL Server-naam, databasenaam, gebruikersnaam en wachtwoord. U hebt deze referenties nodig, zodat u uw logica kunt autoreren voor toegang tot uw SQL-server.

  * Voor on-premises SQL Server vindt u deze details in de connection string:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Voor Azure SQL Database vindt u deze details in de connection string.
  
    Als u deze tekenreeks bijvoorbeeld wilt vinden in de Azure Portal, opent u uw database. Selecteer in het databasemenu Verbindingsreeksen **of** **Eigenschappen:**

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

<a name="multi-tenant-or-ise"></a>

* Op basis van of uw logische apps worden uitgevoerd in azure met meerdere tenants of een [ISE (Integration Service Environment),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)zijn hier andere vereisten voor het maken van verbinding met on-premises SQL Server:

  * Voor logische apps in globale Azure met meerdere tenants die verbinding maken met on-premises SQL Server, moet u de [on-premises](../logic-apps/logic-apps-gateway-install.md) gegevensgateway hebben geïnstalleerd op een lokale computer en een gegevensgatewayresource die al [in Azure is gemaakt.](../logic-apps/logic-apps-gateway-connection.md)

  * Voor logische apps in een ISE die verbinding maken met on-premises SQL Server en Windows-verificatie gebruiken, biedt de SQL Server-connector met ISE-versie geen ondersteuning voor Windows-verificatie. U moet dus nog steeds de gegevensgateway en de niet-ISE-connector SQL Server gebruiken. Voor andere verificatietypen hoeft u de gegevensgateway niet te gebruiken en kunt u de connector met ISE-versie gebruiken.

* De logische app waar u toegang tot uw SQL database. Als u uw logische app wilt starten met een SQL-trigger, hebt u een [lege logische app nodig.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Verbinding maken met uw database

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Ga nu verder met deze stappen:

* [Verbinding maken met cloudgebaseerde Azure SQL Database of een beheerd exemplaar](#connect-azure-sql-db)
* [Verbinding maken met on-premises SQL Server](#connect-sql-server)

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database-or-managed-instance"></a>Verbinding maken met Azure SQL Database of beheerd exemplaar

Als u toegang wilt Azure SQL Managed Instance zonder gebruik te maken van de on-premises gegevensgateway of integratieserviceomgeving, moet u het openbare eindpunt instellen op [Azure SQL Managed Instance](../azure-sql/managed-instance/public-endpoint-configure.md). Het openbare eindpunt gebruikt poort 3342, dus zorg ervoor dat u dit poortnummer opgeeft wanneer u de verbinding maakt vanuit uw logische app.


De eerste keer dat u een [SQL-trigger](#add-sql-trigger) of [SQL-actie](#add-sql-action)toevoegt en u nog geen verbinding met uw database hebt gemaakt, wordt u gevraagd de volgende stappen uit te voeren:

1. Selecteer **bij Verificatietype** de verificatie die is vereist en ingeschakeld voor uw database in Azure SQL Database of Azure SQL Managed Instance:

   | Verificatie | Description |
   |----------------|-------------|
   | [**Geïntegreerde Azure AD**](../azure-sql/database/authentication-aad-overview.md) | - Ondersteunt zowel de niet-ISE- als de ISE-SQL Server connector. <p><p>- Vereist een geldige identiteit in Azure Active Directory (Azure AD) die toegang heeft tot uw database. <p>Raadpleeg de volgende onderwerpen voor meer informatie: <p>- [Azure SQL Beveiligingsoverzicht - Verificatie](../azure-sql/database/security-overview.md#authentication) <br>- [Databasetoegang tot Azure SQL autorisatie - verificatie en autorisatie](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) <br>- [Azure SQL - Geïntegreerde Azure AD-verificatie](../azure-sql/database/authentication-aad-overview.md) |
   | [**SQL Server verificatie**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | - Ondersteunt zowel de niet-ISE- als de ISE-SQL Server connector. <p><p>- Vereist een geldige gebruikersnaam en een sterk wachtwoord die worden gemaakt en opgeslagen in uw database. <p>Raadpleeg de volgende onderwerpen voor meer informatie: <p>- [Azure SQL Beveiligingsoverzicht - Verificatie](../azure-sql/database/security-overview.md#authentication) <br>- [Databasetoegang tot Azure SQL autorisatie - verificatie en autorisatie](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) |
   |||

   Dit voorbeeld wordt voortgezet met **geïntegreerde Azure AD:**

   ![Schermopname van het venster 'SQL Server' met de geopende lijst 'Verificatietype' en 'Azure AD Geïntegreerd' geselecteerd.](./media/connectors-create-api-sqlazure/select-azure-ad-authentication.png)

1. Nadat u Azure **AD Integrated hebt geselecteerd,** **selecteert u Aanmelden.** Selecteer uw gebruikersreferenties voor verificatie Azure SQL Database of Azure SQL Managed Instance.

1. Selecteer deze waarden voor uw database:

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Servernaam** | Ja | Het adres voor uw SQL-server, bijvoorbeeld `Fabrikam-Azure-SQL.database.windows.net` |
   | **Databasenaam** | Yes | De naam van uw SQL database, bijvoorbeeld `Fabrikam-Azure-SQL-DB` |
   | **Tabelnaam** | Yes | De tabel die u wilt gebruiken, bijvoorbeeld `SalesLT.Customer` |
   ||||

   > [!TIP]
   > U hebt de volgende opties om uw database- en tabelgegevens op te geven:
   > 
   > * U vindt deze informatie in de connection string. Zoek en open bijvoorbeeld in Azure Portal database. Selecteer in het databasemenu **Verbindingsreeksen** of **Eigenschappen,** waar u deze tekenreeks kunt vinden:
   >
   >   `Server=tcp:{your-server-address}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`
   >
   > * Standaard worden tabellen in systeemdatabases uitgefilterd, zodat ze mogelijk niet automatisch worden weergegeven wanneer u een systeemdatabase selecteert. Als alternatief kunt u de tabelnaam handmatig invoeren nadat u **Aangepaste waarde invoeren hebt geselecteerd** in de lijst met databases.
   >

   In dit voorbeeld ziet u hoe deze waarden eruit kunnen zien:

   ![Verbinding maken met SQL database](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Ga nu verder met de stappen die u nog niet hebt voltooid in Een [SQL-trigger toevoegen](#add-sql-trigger) of [Een SQL-actie toevoegen.](#add-sql-action)

<a name="connect-sql-server"></a>

### <a name="connect-to-on-premises-sql-server"></a>Verbinding maken met on-premises SQL Server

De eerste keer dat u een [SQL-trigger](#add-sql-trigger) of [SQL-actie](#add-sql-action)toevoegt en u nog geen verbinding met uw database hebt gemaakt, wordt u gevraagd deze stappen uit te voeren:

1. Voor verbindingen met uw on-premises SQL-server waarvoor de on-premises gegevensgateway is vereist, moet u ervoor zorgen dat u [aan deze vereisten hebt voltooid.](#multi-tenant-or-ise)

   Anders wordt uw gegevensgatewayresource  niet weergegeven in de lijst Verbindingsgateway wanneer u de verbinding maakt.

1. Selecteer **bij Verificatietype** de verificatie die is vereist en ingeschakeld op uw SQL Server:

   | Verificatie | Description |
   |----------------|-------------|
   | [**Windows-verificatie**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) | - Ondersteunt alleen de niet-ISE SQL Server-connector, waarvoor een gegevensgatewayresource is vereist die eerder in Azure is gemaakt voor uw verbinding, ongeacht of u azure met meerdere tenants of een ISE gebruikt. <p><p>- Vereist een geldige Windows-gebruikersnaam en een geldig wachtwoord om uw identiteit te bevestigen via uw Windows-account. <p>Zie Windows-verificatie voor [meer informatie](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) |
   | [**SQL Server verificatie**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | - Ondersteunt zowel de niet-ISE- als de ISE-SQL Server connector. <p><p>- Vereist een geldige gebruikersnaam en een sterk wachtwoord die worden gemaakt en opgeslagen in uw SQL Server. <p>Zie verificatie voor SQL Server [meer informatie.](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) |
   |||

   Dit voorbeeld gaat verder met **Windows-verificatie:**

   ![Selecteer het verificatietype dat u wilt gebruiken](./media/connectors-create-api-sqlazure/select-windows-authentication.png)

1. Selecteer of geef de volgende waarden op voor uw SQL database:

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Sql Server-naam** | Yes | Het adres voor uw SQL-server, bijvoorbeeld `Fabrikam-Azure-SQL.database.windows.net` |
   | **SQL-databasenaam** | Yes | De naam van SQL Server database, bijvoorbeeld `Fabrikam-Azure-SQL-DB` |
   | **Gebruikersnaam** | Yes | Uw gebruikersnaam voor de SQL-server en -database |
   | **Wachtwoord** | Yes | Uw wachtwoord voor de SQL-server en -database |
   | **Abonnement** |  Ja, voor Windows-verificatie | Het Azure-abonnement voor de gegevensgatewayresource die u eerder hebt gemaakt in Azure |
   | **Verbindingsgateway** | Ja, voor Windows-verificatie | De naam van de gegevensgatewayresource die u eerder hebt gemaakt in Azure <p><p>**Tip:** als uw gateway niet wordt weergegeven in de lijst, controleert u of u [de gateway juist hebt ingesteld.](../logic-apps/logic-apps-gateway-connection.md) |
   |||

   > [!TIP]
   > U vindt deze informatie in de connection string:
   > 
   > * `Server={your-server-address}`
   > * `Database={your-database-name}`
   > * `User ID={your-user-name}`
   > * `Password={your-password}`

   In dit voorbeeld ziet u hoe deze waarden eruit kunnen zien:

   ![Verbinding SQL Server gemaakt](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Wanneer u klaar bent, selecteert u **Maken.**

1. Ga nu verder met de stappen die u nog niet hebt voltooid in Een [SQL-trigger toevoegen](#add-sql-trigger) of [Een SQL-actie toevoegen.](#add-sql-action)

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Een SQL-trigger toevoegen

1. Maak in [Azure Portal](https://portal.azure.com) of in Visual Studio een lege logische app, waarmee de ontwerpfunctie voor logische apps wordt geopend. Dit voorbeeld gaat verder met de Azure Portal.

1. Voer in de ontwerpfunctie in het zoekvak `sql server` in. Selecteer in de lijst met triggers de sql-trigger die u wilt gebruiken. In dit voorbeeld wordt de **trigger Wanneer een item wordt gemaakt** gebruikt.

   ![Selecteer de trigger Wanneer een item is gemaakt](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Als u voor het eerst verbinding maakt met uw SQL database, wordt u gevraagd om uw SQL database [maken.](#create-connection) Nadat u deze verbinding hebt gemaakt, kunt u doorgaan met de volgende stap.

1. Geef in de trigger het interval en de frequentie op voor hoe vaak de trigger de tabel controleert.

1. Als u andere beschikbare eigenschappen voor deze trigger wilt toevoegen, opent u **de lijst Nieuwe parameter** toevoegen.

   Deze trigger retourneert slechts één rij uit de geselecteerde tabel en niets anders. Als u andere taken wilt uitvoeren, gaat [](../connectors/apis-list.md) u door met het toevoegen van een [SQL-connectoractie](#add-sql-action) of een andere actie waarmee de volgende taak wordt uitgevoerd die u wilt uitvoeren in de werkstroom van uw logische app.

   Als u bijvoorbeeld de gegevens in deze rij wilt weergeven, kunt u andere acties toevoegen die een bestand maken dat de velden uit de geretourneerde rij bevat en vervolgens e-mailwaarschuwingen verzenden. Zie de referentiepagina van de connector voor meer informatie over andere beschikbare acties [voor deze connector.](/connectors/sql/)

1. Selecteer **Opslaan** op de werkbalk van de ontwerper.

   Hoewel met deze stap uw logische app automatisch live in Azure wordt in- en gepubliceerd, is de enige actie die uw logische app momenteel onderneemt, om uw database te controleren op basis van het opgegeven interval en de frequentie.

<a name="trigger-recurrence-shift-drift"></a>

### <a name="trigger-recurrence-shift-and-drift"></a>Terugkeerpatroon en drift activeren

Triggers op basis van een verbinding waarbij u eerst een verbinding moet maken, zoals de SQL-trigger, verschillen van ingebouwde triggers die standaard worden uitgevoerd in Azure Logic Apps, zoals de trigger [Terugkeerpatroon.](../connectors/connectors-native-recurrence.md) In terugkerende triggers op basis van verbindingen is het terugkeerschema niet het enige stuurprogramma dat de uitvoering bepaalt en bepaalt de tijdzone alleen de initiële begintijd. Volgende uitvoeringen zijn afhankelijk van het  terugkeerschema, de laatste uitvoering van de trigger en andere factoren die ertoe kunnen leiden dat uitvoeringstijden veranderen of onverwacht gedrag produceren, bijvoorbeeld wanneer de opgegeven planning niet wordt onderhouden wanneer zomertijd (DST) begint en eindigt. Om ervoor te zorgen dat de terugkeertijd niet verschuift wanneer DST van kracht wordt, past u het terugkeerpatroon handmatig aan zodat uw logische app op het verwachte tijdstip blijft werken. Anders verschuift de begintijd één uur vooruit wanneer DST wordt gestart en één uur achterwaarts wanneer DST eindigt. Zie Terugkeerpatroon voor [triggers op basis van verbindingen voor meer informatie.](../connectors/apis-list.md#recurrence-for-connection-based-triggers)

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Een SQL-actie toevoegen

In dit voorbeeld begint de logische app met de [trigger Terugkeerpatroon](../connectors/connectors-native-recurrence.md)en roept een actie aan die een rij ophaalt uit een SQL database.

1. Open in [Azure Portal](https://portal.azure.com) of in Visual Studio logische app in Logic App Designer. In dit voorbeeld wordt de Azure Portal.

1. Selecteer nieuwe stap onder de trigger of actie waar u de SQL-actie **wilt toevoegen.**

   ![Een actie toevoegen aan uw logische app](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Of als u een actie tussen bestaande stappen wilt toevoegen, beweegt u de muis over de verbindingspijl. Selecteer het plusteken ( **+** ) dat wordt weergegeven en selecteer vervolgens Een actie **toevoegen.**

1. Voer in het zoekvak onder **Kies een actie** `sql server` in. Selecteer in de lijst met acties de SQL-actie die u wilt gebruiken. In dit voorbeeld wordt de **actie Rij** op halen gebruikt, waarmee één record wordt opgeslagen.

   ![Selecteer DE SQL-actie Rij op halen](./media/connectors-create-api-sqlazure/select-sql-get-row-action.png)

1. Als u voor het eerst verbinding maakt met uw SQL database, wordt u gevraagd om nu uw SQL database [maken.](#create-connection) Nadat u deze verbinding hebt gemaakt, kunt u doorgaan met de volgende stap.

1. Selecteer de **tabelnaam**, in `SalesLT.Customer` dit voorbeeld. Voer de **rij-id** in voor de record die u wilt.

   ![Tabelnaam selecteren en rij-id opgeven](./media/connectors-create-api-sqlazure/specify-table-row-id.png)

   Deze actie retourneert slechts één rij uit de geselecteerde tabel, niets anders. Als u de gegevens in deze rij wilt weergeven, kunt u dus andere acties toevoegen die een bestand maken dat de velden uit de geretourneerde rij bevat en dat bestand opslaan in een cloudopslagaccount. Zie de referentiepagina van de connector voor meer informatie over andere beschikbare acties [voor deze connector.](/connectors/sql/)

1. Selecteer **Opslaan** op de werkbalk van de ontwerper wanneer u klaar bent.

   Met deze stap wordt uw logische app automatisch live in Azure in- en gepubliceerd.

<a name="handle-bulk-data"></a>

## <a name="handle-bulk-data"></a>Bulkgegevens verwerken

Soms moet u werken met resultatensets die zo groot zijn dat de connector niet alle resultaten tegelijkertijd retournt, of u wilt meer controle over de grootte en structuur van uw resultatensets. Hier zijn enkele manieren waarop u dergelijke grote resultatensets kunt verwerken:

* Schakel *paginering* in om u te helpen bij het beheren van resultaten als kleinere sets. Zie Get [bulk data, records, and items by using pagintion (Bulkgegevens, records en items verkrijgen met behulp van paginering) voor meer informatie.](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md) Zie SQL-paginering voor [bulkgegevensoverdracht](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)met Logic Apps.

* Maak een [*opgeslagen procedure*](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) die de resultaten organiseert zoals u dat wilt. De SQL-connector biedt veel back-endfuncties die u kunt openen met behulp van Azure Logic Apps, zodat u gemakkelijker zakelijke taken kunt automatiseren die met SQL database tabellen werken.

  Bij het verkrijgen of invoegen van meerdere rijen kan uw logische app deze rijen herhalen met behulp van een [*until-lus binnen*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) deze [limieten.](../logic-apps/logic-apps-limits-and-config.md) Wanneer uw logische app echter moet werken met recordsets die zo groot zijn, bijvoorbeeld duizenden of miljoenen rijen, dat u de kosten wilt minimaliseren die het gevolg zijn van aanroepen naar de database.

  Als u de resultaten wilt ordenen op de manier die u wilt, kunt u een opgeslagen procedure maken die wordt uitgevoerd in uw SQL-exemplaar en de **instructie SELECT - ORDER BY** gebruikt. Met deze oplossing hebt u meer controle over de grootte en structuur van uw resultaten. Uw logische app roept de opgeslagen procedure aan met behulp van de SQL Server de actie Opgeslagen procedure uitvoeren **van de connector.** Zie SELECT [- ORDER BY-component voor meer informatie.](/sql/t-sql/queries/select-order-by-clause-transact-sql)

  > [!NOTE]
  > De SQL-connector heeft een time-outlimiet voor een opgeslagen procedure die kleiner is [dan 2 minuten.](/connectors/sql/#known-issues-and-limitations) Sommige opgeslagen procedures kunnen langer duren dan deze limiet, waardoor er een fout `504 Timeout` wordt veroorzaakt. U kunt dit probleem oplossen met behulp van een SQL-voltooiingstrigger, systeemeigen SQL Pass Through-query, een statustabel en taken aan serverzijde.
  > 
  > Voor deze taak kunt u de [Azure Elastic Job Agent gebruiken](../azure-sql/database/elastic-jobs-overview.md) voor [Azure SQL Database.](../azure-sql/database/sql-database-paas-overview.md) Voor [SQL Server on-premises](/sql/sql-server/sql-server-technical-documentation) en [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)kunt u de [SQL Server Agent.](/sql/ssms/agent/sql-server-agent) Zie Voor meer informatie [Handle long-running stored procedure timeouts in the SQL connector for Azure Logic Apps](../logic-apps/handle-long-running-stored-procedures-sql-connector.md).

### <a name="handle-dynamic-bulk-data"></a>Dynamische bulkgegevens verwerken

Wanneer u een opgeslagen procedure aanroept met behulp van SQL Server connector, is de geretourneerde uitvoer soms dynamisch. In dit scenario volgt u deze stappen:

1. Open in [Azure Portal](https://portal.azure.com)de logische app in de ontwerpfunctie voor logische apps.

1. Bekijk de uitvoerindeling door een testuitvoer uit te voeren. Kopieer de voorbeelduitvoer en sla deze op.

1. Selecteer in de ontwerpfunctie onder de actie waar u de opgeslagen procedure aanroept de **optie Nieuwe stap.**

1. Zoek **en selecteer onder Kies** een actie de actie [**JSON parseren.**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action)

1. Selecteer in **de actie JSON parseren** de optie **Voorbeeldnlading gebruiken om schema te genereren.**

1. Plak in **het vak Enter or paste a sample JSON payload (Een voorbeeld van een JSON-nettolading** invoeren of plakken) de voorbeelduitvoer en selecteer Done **(Klaar).**

   > [!NOTE]
   > Als er een foutbericht wordt weergegeven Logic Apps geen schema kan genereren, controleert u of de syntaxis van uw voorbeelduitvoer correct is opgemaakt. Als u het schema nog steeds niet kunt genereren, voert u in het **vak Schema** het schema handmatig in.

1. Selecteer **Opslaan** op de werkbalk van de ontwerper.

1. Als u wilt verwijzen naar de JSON-inhoudseigenschappen, klikt u in de bewerkingsvakken waar u naar deze eigenschappen wilt verwijzen, zodat de lijst met dynamische inhoud wordt weergegeven. Selecteer in de lijst onder de [**kop JSON parseren**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) de gegevenstokens voor de JSON-inhoudseigenschappen die u wilt.

## <a name="troubleshoot-problems"></a>Problemen oplossen

<a name="connection-problems"></a>

### <a name="connection-problems"></a>Verbindingsproblemen

Verbindingsproblemen kunnen vaak optreden. Als u dit soort problemen wilt oplossen, bekijkt u Connectiviteitsfouten [oplossen met SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server). Hier volgen enkele voorbeelden:

* `A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.`

* `(provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server) (Microsoft SQL Server, Error: 53)`

* `(provider: TCP Provider, error: 0 - No such host is known.) (Microsoft SQL Server, Error: 11001)`

## <a name="connector-specific-details"></a>Connectorspecifieke details

Zie de referentiepagina van de connector die wordt gegenereerd op basis van de Swagger-beschrijving voor technische informatie over de triggers, acties en limieten van deze [connector.](/connectors/sql/)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [andere connectors voor Azure Logic Apps](../connectors/apis-list.md)
