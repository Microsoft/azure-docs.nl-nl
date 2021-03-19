---
title: Visual Studio Code gebruiken om verbinding te maken en query's uit te voeren
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Ontdek hoe u verbinding maakt met Azure SQL Database of SQL Managed Instance in Azure met behulp van Visual Studio Code. Voer daarna Transact-SQL-instructies (T-SQL) uit om query's uit te voeren voor gegevens en om gegevens te bewerken.
keywords: verbinding maken met sql-database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: f823b6d04a217328fe2e825e64906460cd9cbae9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92672480"
---
# <a name="quickstart-use-visual-studio-code-to-connect-and-query"></a>Quickstart: Visual Studio Code gebruiken om verbinding te maken en query's uit te voeren 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[Visual Studio Code](https://code.visualstudio.com/docs) is een grafische code-editor voor Linux, macOS en Windows. Het ondersteunt extensies, waaronder de [MSSQL-extensie](https://aka.ms/mssql-marketplace) voor het uitvoeren van een query op een SQL Server-exemplaar, Azure SQL Database, een door Azure SQL beheerd exemplaar en een database in Azure Synapse Analytics. In deze snelstart gaat u Visual Studio Code gebruiken om verbinding te maken met Azure SQL Database of Azure SQL Managed Instance en vervolgens Transact-SQL-instructies uitvoeren om query's op gegevens in de database uit te voeren, gegevens in te voegen, bij te werken en te verwijderen.

## <a name="prerequisites"></a>Vereisten

- Een database in Azure SQL Database of Azure SQL Managed Instance. U kunt een van deze quickstarts gebruiken om een database te maken en vervolgens te configureren in Azure SQL Database:

  | Bewerking | Azure SQL Database | Azure SQL Managed Instance |
  |:--- |:--- |:---|
  | Maken| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) |
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) |
  | Configureren | [IP-firewallregel op serverniveau](firewall-create-server-level-portal-quickstart.md)| [Connectiviteit van een virtuele machine (VM)](../managed-instance/connect-vm-instance-configure.md)|
  |||[Connectiviteit vanaf on-premises](../managed-instance/point-to-site-p2s-configure.md)
  |Gegevens laden|Adventure Works geladen volgens de quickstart|[Wide World Importers herstellen](../managed-instance/restore-sample-database-quickstart.md)
  |||Adventure Works herstellen of importeren uit een [BACPAC](database-import.md)-bestand vanuit [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > De scripts in dit artikel zijn geschreven voor gebruik met de Adventure Works-database. Met een met SQL beheerd exemplaar moet u de Adventure Works-database in een exemplaardatabase importeren of de scripts in dit artikel wijzigen zodat deze de Wide World Importers-database gebruiken.

## <a name="install-visual-studio-code"></a>Visual Studio Code installeren

Zorg ervoor dat u de nieuwste versie van [Visual Studio Code](https://code.visualstudio.com/Download) hebt geïnstalleerd en de [mssql-extensie](https://aka.ms/mssql-marketplace) hebt geladen. Zie [Visual Studio Code installeren](/sql/linux/sql-server-linux-develop-use-vscode#install-and-start-visual-studio-code) en [mssql voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql) voor hulp bij het installeren van de mssql-extensie.

## <a name="configure-visual-studio-code"></a>Visual Studio Code configureren

### <a name="macos"></a>**MacOS**

Voor macOS moet u OpenSSL installeren. Dit is een vereiste voor .NET Core waarvan de mssql-extensie gebruikmaakt. Open de terminal en voer de volgende opdrachten in om **brew** en **OpenSSL** te installeren.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

Er is geen speciale configuratie vereist.

### <a name="windows"></a>**Windows**

Er is geen speciale configuratie vereist.

## <a name="get-server-connection-information"></a>SQL Server-verbindingsgegevens ophalen

Haal de verbindingsgegevens op die u nodig hebt om verbinding te maken met de Azure SQL-database. U hebt de volledig gekwalificeerde servernaam of hostnaam, databasenaam en aanmeldingsgegevens nodig voor de volgende procedures.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Navigeer naar de pagina **SQL-databases** of **Met SQL beheerde exemplaren**.

3. Bekijk op de pagina **Overzicht** de volledig gekwalificeerde servernaam naast **Servernaam** voor SQL Database, of de volledig gekwalificeerde servernaam naast **Host** voor een met SQL beheerd exemplaar. Als u de servernaam of hostnaam wilt kopiëren, plaatst u de muisaanwijzer erboven en selecteert u het pictogram **Kopiëren**.

## <a name="set-language-mode-to-sql"></a>Taalmodus instellen op SQL

Stel de taalmodus in Visual Studio Code in op **SQL** om mssql-opdrachten en T-SQL IntelliSense in te schakelen.

1. Open een nieuw Visual Studio Code venster.

2. Druk op **Ctrl**+**N**. Hiermee wordt een nieuw bestand met tekst zonder opmaak geopend.

3. Selecteer **Tekst zonder opmaak** in de rechterbenedenhoek van de statusbalk.

4. Selecteer in de vervolgkeuzelijst **Taalmodus selecteren** die wordt geopend de optie **SQL**.

## <a name="connect-to-your-database"></a>Verbinding maken met uw database

Gebruik Visual Studio Code om verbinding te maken met uw server.

> [!IMPORTANT]
> Zorg ervoor dat u de server- en aanmeldingsgegevens bij de hand hebt, voordat u verdergaat. Wanneer u de verbindingsprofielgegevens gaat invoeren, moet u ervoor zorgen dat de focus op Visual Studio Code blijft, omdat u anders opnieuw moet beginnen met het maken van het verbindingsprofiel.

1. Druk in Visual Studio Code op **Ctrl+Shift+P** (of **F1**) om het opdrachtenpalet te openen.

2. Selecteer **MS SQL:Connect** en kies **Enter**.

3. Selecteer **Verbindingsprofiel maken**.

4. Volg de aanwijzingen op om de verbindingseigenschappen voor het nieuwe profiel op te geven. Wanneer u een waarde hebt opgegeven, kiest u **Enter** om door te gaan.

   | Eigenschap       | Voorgestelde waarde | Beschrijving |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Servernaam** | De volledig gekwalificeerde servernaam | Dit moet er ongeveer als volgt uitzien: **mijnnieuweserver20170313.database.windows.net**. |
   | **Databasenaam** | mySampleDatabase | De database waarmee verbinding moet worden gemaakt. |
   | **Verificatie** | SQL-aanmelding| In deze zelfstudie wordt gebruik gemaakt van SQL-verificatie. |
   | **Gebruikersnaam** | Gebruikersnaam | De gebruikersnaam van het serverbeheerdersaccount dat wordt gebruikt voor het maken van de server. |
   | **Wachtwoord (SQL-aanmelding)** | Wachtwoord | Het wachtwoord van het serverbeheerdersaccount dat wordt gebruikt voor het maken van de server. |
   | **Wachtwoord opslaan?** | Ja of nee | Selecteer **Ja** als u het wachtwoord niet elke keer wilt invoeren. |
   | **Voer een naam in voor dit profiel** | Een profielnaam, zoals **mySampleProfile** | Een opgeslagen profiel zorgt ervoor dat de verbinding sneller tot stand komt bij toekomstige aanmeldingen. |

   Wanneer uw profiel is gemaakt, wordt er een melding weergegeven dat het profiel is gemaakt en verbonden is.

## <a name="query-data"></a>Querygegevens

Voer de volgende Transact-SQL-[SELECT](/sql/t-sql/queries/select-transact-sql)-instructie uit om op categorie een query uit te voeren voor de 20 populairste producten.

1. Plak in het editorvenster de volgende SQL-query.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. Druk op **Ctrl**+**Shift**+**E** om de query uit te voeren en de resultaten uit de tabellen `Product` en `ProductCategory` weer te geven.

    ![Query voor het ophalen van gegevens uit twee tabellen](./media/connect-query-vscode/query.png)

## <a name="insert-data"></a>Gegevens invoegen

Voer de volgende Transact-SQL-[INSERT](/sql/t-sql/statements/insert-transact-sql)-instructie uit om een nieuw product toe te voegen aan de tabel `SalesLT.Product`.

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

2. Druk op **Ctrl**+**Shift**+**E** om een nieuwe rij in de tabel `Product` in te voegen.

## <a name="update-data"></a>Gegevens bijwerken

Voer de volgende Transact-SQL-[UPDATE](/sql/t-sql/queries/update-transact-sql)-instructie uit om het toegevoegde product bij te werken.

1. Vervang de vorige query door deze:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Druk op **Ctrl**+**Shift**+**E** om de opgegeven rij in de tabel `Product` bij te werken.

## <a name="delete-data"></a>Gegevens verwijderen

Voer de volgende Transact-SQL-[DELETE](/sql/t-sql/statements/delete-transact-sql)-instructie uit om het nieuwe product te verwijderen.

1. Vervang de vorige query door deze:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Druk op **Ctrl**+**Shift**+**E** om de opgegeven rij in de tabel `Product` te verwijderen.

## <a name="next-steps"></a>Volgende stappen

- Als u verbinding wilt maken en query's wilt uitvoeren met SQL Server Management Studio, gaat u naar [Snelstartgids: SQL Server Management Studio gebruiken om verbinding te maken met een database in Azure SQL Database en query's uit te voeren voor gegevens](connect-query-ssms.md).
- Als u verbinding wilt maken en query's wilt uitvoeren met Azure Portal, gaat u naar [Snelstartgids: De SQL-queryeditor in Azure Portal gebruiken om verbinding te maken en query's op gegevens uit te voeren](connect-query-portal.md).
- Zie het blogbericht [Create a database IDE with MSSQL extension](/archive/msdn-magazine/2017/june/data-points-visual-studio-code-create-a-database-ide-with-mssql-extension) voor een MSDN-artikel over het gebruik van Visual Studio Code.