---
title: Voer in de Azure Portal (preview) een query voor de SQL Database uit met de Query-editor
description: Meer informatie over het gebruik van de Query-editor om Transact-SQL-query's (T-SQL) uit te voeren op een database in Azure SQL Database.
titleSuffix: Azure SQL Database
keywords: verbinding maken met sql database, azure portal, portal, query-editor
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1, contperf-fy21q3-portal
ms.devlang: ''
ms.topic: quickstart
author: Ninarn
ms.author: ninarn
ms.reviewer: sstein
ms.date: 03/01/2021
ms.openlocfilehash: a6f13e27a5aa2684a16565c616d781e3d5c3a750
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594186"
---
# <a name="quickstart-use-the-azure-portals-query-editor-preview-to-query-an-azure-sql-database"></a>Quickstart: De Query-editor (preview) van de Azure Portal gebruiken om een query uit te voeren op Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

De Query-editor is een hulpprogramma in de Azure-portal voor het uitvoeren van SQL-query's op uw database in Azure SQL Database of uw datawarehouse in Azure Synapse Analytics.

In deze snelstart gebruikt u de Query-editor om Transact-SQL-query's (T-SQL) uit te voeren op een database.

## <a name="prerequisites"></a>Vereisten

### <a name="create-a-database-with-sample-data"></a>Een database maken met voorbeeldgegevens

Voor het voltooien van deze snelstart is de voorbeelddatabase AdventureWorksLT vereist. Als u geen werkende kopie van de AdventureWorksLT-voorbeeld database in SQL Database hebt, kunt u in de volgende Snelstartgids snel een exemplaar maken:

[Snelstart: Maak een afzonderlijke Azure SQL Database met de Azure Portal, PowerShell of Azure CLI](single-database-create-quickstart.md)

### <a name="set-an-azure-active-directory-admin-for-the-server-optional"></a>Een Azure Active Directory beheerder voor de server instellen (optioneel)

Als u een Azure Active Directory-beheerder (Azure AD) instelt, kunt u gebruikmaken van één identiteit om u aan te melden bij de Azure Portal en uw database. Als u Azure AD wilt gebruiken om verbinding te maken met query-editor, volgt u de onderstaande stappen.

Dit proces is optioneel. u kunt in plaats daarvan SQL-verificatie gebruiken om verbinding te maken met de query-editor.

> [!NOTE]
> * E-mailaccounts (bijvoorbeeld outlook.com, gmail.com, yahoo.com, enzovoort) worden nog niet ondersteund als Azure AD-beheerders. Kies een gebruiker die ofwel systeemeigen is gemaakt in de Azure AD ofwel federatief in de Azure AD.
> * Aanmelding van Azure AD-beheerder werkt met accounts waarvoor twee ledige verificatie is ingeschakeld, maar de query-editor biedt geen ondersteuning voor twee ledige authenticatie.

1. Ga in het Azure Portal naar de SQL database-server.

2. Selecteer in het menu **SQL-server** de optie **Active Directory-beheerder**.

3. Selecteer op de werk balk SQL Server **Active Directory-beheer** pagina de optie **beheerder instellen**.

    ![active directory selecteren](./media/connect-query-portal/select-active-directory.png)

4. Voer op de pagina **Beheerder toevoegen** in het zoekvak een gebruiker of groep in die u wilt zoeken, selecteer deze als beheerder en klik vervolgens op de knop **Selecteren**.

5. Selecteer op de paginawerkbalk SQL Server **Active Directory-beheerder** **Opslaan**.

## <a name="using-sql-query-editor"></a>SQL-query editor gebruiken

1. Meld u aan bij de [Azure portal](https://portal.azure.com/) en selecteer de database waarvoor u een query wilt uitvoeren.

2. Selecteer in het menu **SQL database** **Query-editor (preview)** .

    ![queryeditor zoeken](./media/connect-query-portal/find-query-editor.PNG)

### <a name="establish-a-connection-to-the-database"></a>Verbinding met de database tot stand brengen

Zelfs al bent u aangemeld bij de portal, moet u nog steeds referenties opgeven voor toegang tot de database. U kunt verbinding maken met behulp van SQL-verificatie of Azure Active Directory om verbinding te maken met uw database.

#### <a name="connect-using-sql-authentication"></a>Verbinding maken met behulp van SQL-verificatie

1. Voer op de pagina **Aanmeldings** onder **SQL Server-verificatie** een **Aanmelding** en **Wachtwoord** in voor een gebruiker die toegang heeft tot de database. Als u niet zeker bent, gebruikt u de aanmelding en het wachtwoord voor de Serverbeheerder van de server van de database.

    ![aanmelden](./media/connect-query-portal/login-menu.png)

2. Selecteer **OK**.

#### <a name="connect-using-azure-active-directory"></a>Verbinding maken met Azure Active Directory

Ga in de **query-editor (preview)** naar de **aanmeldings** pagina in het gedeelte **Active Directory-verificatie** . De verificatie wordt automatisch uitgevoerd. Als u een Azure AD-beheerder bent voor de data base, ziet u een bericht met de melding dat u bent aangemeld. Selecteer vervolgens de knop **door gaan als** *\<your user or group ID>* . Als de pagina aangeeft dat u niet bent aangemeld, moet u de pagina mogelijk vernieuwen.

### <a name="query-a-database-in-sql-database"></a>Een query uitvoeren op een database in SQL Database

De volgende voorbeeld query's moeten worden uitgevoerd op basis van de voorbeelddatabase AdventureWorksLT.

#### <a name="run-a-select-query"></a>Een query SELECT uitvoeren

1. Plak de volgende query in de Query-editor:

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Selecteer **Uitvoeren** en bekijk de uitvoer in het deelvenster **Resultaten**.

   ![resultaten queryeditor](./media/connect-query-portal/query-editor-results.png)

3. U kunt de query ook opslaan als een .sql-bestand of de geretourneerde gegevens exporteren als een .json-, csv- of xml-bestand.

#### <a name="run-an-insert-query"></a>Een query INSERT uitvoeren

Voer de volgende T-SQL-instructie [INSERT](/sql/t-sql/statements/insert-transact-sql/) uit om een nieuw product toe in de `SalesLT.Product`tabel toe te voegen.

1. Vervang de vorige query door deze.

    ```sql
    INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
    VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```


2. Selecteer **Uitvoeren** om een nieuwe rij in te voegen in de tabel `Product`. Het deelvenster **Berichten** toont **Query voltooid: Betroffen rijen: 1**.


#### <a name="run-an-update-query"></a>Een query UPDATE uitvoeren

Voer de volgende T-SQL-instructie [UPDATE](/sql/t-sql/queries/update-transact-sql/) uit om uw nieuwe product te wijzigen.

1. Vervang de vorige query door deze.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Selecteer **Uitvoeren** om de opgegeven rij in de tabel `Product` bij te werken. Het deelvenster **Berichten** toont **Query voltooid: Betroffen rijen: 1**.

#### <a name="run-a-delete-query"></a>Een query DELETE uitvoeren

Gebruik de volgende T-SQL-instructie [DELETE](/sql/t-sql/statements/delete-transact-sql/) uit om uw nieuwe product te verwijderen.

1. Vervang de vorige query door deze:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Selecteer **Uitvoeren** om de opgegeven rij in de tabel `Product` te verwijderen. Het deelvenster **Berichten** toont **Query voltooid: Betroffen rijen: 1**.


## <a name="troubleshooting-and-considerations"></a>Probleemoplossing en aandachtspunten

U moet enkele dingen weten voordat u met de queryeditor gaat werken.

### <a name="configure-local-network-settings"></a>Instellingen voor het lokale netwerk configureren

Als u een van de volgende fouten in de query-editor krijgt:
 - *De instellingen van uw lokale netwerk verhinderen mogelijk dat de query-editor query's kan uitgeven. Klik hier voor instructies over het configureren van uw netwerk instellingen*
 - *Er kan geen verbinding met de server tot stand worden gebracht. Dit kan duiden op een probleem met de configuratie van uw lokale firewall of uw netwerk proxy-instellingen*

Dit komt doordat de query-editor poort 443 en 1443 gebruikt om te communiceren. U moet ervoor zorgen dat u het uitgaande HTTPS-verkeer op deze poorten hebt ingeschakeld. In de onderstaande instructies vindt u informatie over hoe u dit kunt doen, afhankelijk van uw besturings systeem. Mogelijk moet u samen werken met uw bedrijf om toestemming te verlenen om deze verbinding te openen op uw lokale netwerk.

#### <a name="steps-for-windows"></a>Stappen voor Windows

1. **Windows Defender firewall** openen
2. Selecteer in het menu aan de linkerkant **Geavanceerde instellingen**
3. Selecteer in **Windows Defender Firewall met geavanceerde beveiliging** de optie **Uitgaande regels** in het menu aan de linkerkant.
4. Selecteer **nieuwe regel...** in het menu aan de rechter kant

Voer in de **wizard Nieuwe regel voor uitgaande verbindingen** de volgende stappen uit:

1. Selecteer **poort** als het type regel dat u wilt maken. Selecteer **Volgende**
2. **TCP** selecteren
3. Selecteer **specifieke externe poorten** en voer "443, 1443" in. Selecteer **volgende**
4. Selecteer de verbinding toestaan als deze veilig is
5. Selecteer **volgende** en vervolgens opnieuw **volgende** selecteren
5. ' Domein ', ' persoonlijk ' en ' openbaar ' alles blijven geselecteerd
6. Geef een naam op voor de regel, bijvoorbeeld ' toegang tot de Azure SQL-query-editor ' en optioneel een beschrijving. Selecteer vervolgens **volt ooien**

#### <a name="steps-for-mac"></a>Stappen voor Mac
1. Open **systeem voorkeuren** (Apple-menu > systeem voorkeuren).
2. Klik op **beveiliging & privacy**.
3. Klik op **firewall**.
4. Als de firewall is uitgeschakeld, selecteert **u op de vergren deling om de wijzigingen aan te brengen** en selecteert u **firewall inschakelen**
4. Klik op **firewall opties**.
5. Selecteer in het venster **beveiliging & privacy** deze optie: "automatisch toestaan dat ondertekende software binnenkomende verbindingen ontvangt."

#### <a name="steps-for-linux"></a>Stappen voor Linux
Voer deze opdrachten uit om iptables bij te werken
  ```
  sudo iptables -A OUTPUT -p tcp --dport 443 -j ACCEPT
  sudo iptables -A OUTPUT -p tcp --dport 1443 -j ACCEPT
  ```

### <a name="connection-considerations"></a>Verbindings overwegingen

* Voor open bare verbindingen met de query-editor moet u [uw uitgaande IP-adres toevoegen aan de toegestane firewall regels van de server](firewall-create-server-level-portal-quickstart.md) om toegang te krijgen tot uw data bases en data warehouses.

* Als er een verbinding voor een privé koppeling is ingesteld op de server en u verbinding maakt met query-editor vanuit een IP-adres in de persoonlijke Virtual Network, werkt de query-editor zonder dat u het client-IP-adressen hoeft toe te voegen aan de firewall regels van de SQL database-server.

* De meest eenvoudige RBAC-machtigingen die nodig zijn voor het gebruik van de query-editor zijn lees toegang tot de server en de data base. Iedereen met dit toegangs niveau krijgt toegang tot de functie Query-Editor. Als u de toegang tot bepaalde gebruikers wilt beperken, moet u voor komen dat ze zich bij de query-editor kunnen aanmelden met de referenties voor Azure Active Directory of SQL-verificatie. Als ze niet kunnen worden toegewezen als de AAD-beheerder voor de server of als u een SQL-beheerders account wilt openen/toevoegen, moeten ze de query-editor niet kunnen gebruiken.

* De query-editor biedt geen ondersteuning voor het maken van verbinding met de `master`-database.

* Query-Editor kan geen verbinding maken met een replica database met `ApplicationIntent=ReadOnly`

* Als u dit fout bericht krijgt: ' de X-CSRF-handtekening header kan niet worden gevalideerd ', moet u de volgende actie ondernemen om het probleem op te lossen.

    * Zorg ervoor dat de klok van uw computer is ingesteld op de juiste tijd en tijd zone. U kunt ook proberen de tijd zone van uw computer te vergelijken met Azure door te zoeken naar de tijd zone voor de locatie van uw exemplaar, zoals VS-Oost, Pacific, enzovoort.
    * Als u een proxy netwerk hebt, moet u ervoor zorgen dat de aanvraag header "X-CSRF-Signature" niet wordt gewijzigd of verwijderd.

### <a name="other-considerations"></a>Andere overwegingen

* Wanneer u op **F5** drukt, wordt de pagina van de Query-editor vernieuwd en gaan query's waaraan wordt gewerkt, verloren.

* Er is een time-out van vijf minuten voor uitvoering van de query.

* De queryeditor ondersteunt alleen cilindrische projectie voor geografiegegevenstypen.

* Er is geen ondersteuning voor IntelliSense voor databasetabellen en -weergaven, maar de editor ondersteunt wel automatisch aanvullen op namen die al zijn getypt.

## <a name="next-steps"></a>Volgende stappen

Zie [Transact-SQL-verschillen oplossen tijdens migratie naar SQL Database](transact-sql-tsql-differences-sql-server.md) voor informatie over de Transact-SQL (T-SQL) die in Azure SQL-database wordt ondersteund.
