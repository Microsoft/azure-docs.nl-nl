---
title: 'Zelfstudie: Een Azure Database for PostgreSQL-single server ontwerpen - Azure PowerShell'
description: Deze zelfstudie laat zien hoe u uw eerste Azure Database for PostgreSQL-single server kunt aanmaken en configureren en er query's op kunt toepassen met behulp van Azure PowerShell.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: cf84a8abcc0f1c890a51bf07056ac31d37107e53
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96749110"
---
# <a name="tutorial-design-an-azure-database-for-postgresql---single-server-using-powershell"></a>Zelfstudie: Een Azure Database for PostgreSQL-single server ontwerpen met behulp van PowerShell

Azure Database for PostgreSQL is een relationele databaseservice in de Microsoft Cloud die is gebaseerd op de PostgreSQL Community Edition database engine. In deze zelfstudie gebruikt u PowerShell en andere hulpprogramma's om het volgende te leren:

> [!div class="checklist"]
> - Een Azure Database voor PostgreSQL-server maken
> - De serverfirewall configureren
> - Het hulpprogramma [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) gebruiken om een database te maken
> - Voorbeeldgegevens laden
> - Querygegevens
> - Gegevens bijwerken
> - Gegevens terugzetten

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

Als u PowerShell lokaal wilt gebruiken, moet u voor dit artikel de Az-module van PowerShell installeren en verbinding maken met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Zie [Azure PowerShell installeren](/powershell/azure/install-az-ps) voor meer informatie over het installeren van de Az-module van PowerShell.

> [!IMPORTANT]
> Hoewel de PowerShell-module Az.PostgreSql in preview is, moet u deze afzonderlijk van de PowerShell-module Az installeren met behulp van de volgende opdracht: `Install-Module -Name Az.PostgreSql -AllowPrerelease`.
> Zodra de PowerShell-module Az.PostgreSql algemeen beschikbaar is, wordt deze onderdeel van toekomstige releases van Az PowerShell en is de module systeemeigen beschikbaar vanuit Azure Cloud Shell.

Als dit de eerste keer is dat u de Azure Database for PostgreSQL-service gebruikt, moet u de resourceprovider van **Microsoft.DBforPostgreSQL** registreren.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforPostgreSQL
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Als u meerdere Azure-abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. Selecteer een specifieke abonnementen-id met behulp van de cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een [Azure-resourcegroep](../azure-resource-manager/management/overview.md) met de cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en groepsgewijs worden beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam **myresourcegroup** gemaakt in de regio **US – West**.

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Een Azure-database voor PostgreSQL-server maken

Maak een Azure Database for PostgreSQL-server met de cmdlet `New-AzPostgreSqlServer`. Een server kan meerdere databases beheren. Een aparte database wordt doorgaans gebruikt voor elk project of voor elke gebruiker.

In het volgende voorbeeld wordt een PostgreSQL-server gemaakt in de regio **US - west**. De server heeft de naam **mydemoserver** en bevindt zich in de resourcegroep **myresourcegroup**. De serverbeheerder kan zich aanmelden met **myadmin**. De server is een Gen 5-server in de prijscategorie Algemeen en beschikt over twee vCores. Daarnaast is geografisch redundante back-ups ingeschakeld. Noteer het wachtwoord dat in de eerste regel van het voorbeeld wordt gebruikt, aangezien dit het wachtwoord voor het beheerdersaccount van de PostgreSQL-server is.

> [!TIP]
> Een servernaam wordt toegewezen aan een DNS-naam en moet wereldwijd uniek zijn in Azure.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

De parameterwaarde voor de **SKU** volgt de conventie **prijscategorie\_compute-generatie\_vCores** zoals is te zien in de volgende voorbeelden.

- `-Sku B_Gen5_1` komt overeen met Basic, Gen 5 en 1 vCore. Deze optie is de kleinst beschikbare SKU.
- `-Sku GP_Gen5_32` komt overeen met Algemeen gebruik, Gen 5 en 32 vCores.
- `-Sku MO_Gen5_2` komt overeen met Geoptimaliseerd voor geheugen, Gen 5 en 2 vCores.

Voor meer informatie over geldige **SKU**-waarden per regio en voor categorieën gaat u naar [Prijscategorieën voor Azure Database for PostgreSQL](./concepts-pricing-tiers.md).

Overweeg het gebruik van de prijscategorie Basic als lichte reken- en I/O-capaciteit voldoende is voor uw workload.

> [!IMPORTANT]
> Servers die zijn gemaakt in de prijscategorie Basic, kunnen later niet meer worden geschaald voor algemeen gebruik of worden geoptimaliseerd voor het geheugen. Daarnaast is het niet mogelijk om geo-replicatie te gebruiken.

## <a name="configure-a-firewall-rule"></a>Een firewallregel configureren

Maak een firewallregel op Azure Database for PostgreSQL-serverniveau met de cmdlet `New-AzPostgreSqlFirewallRule`. Een firewallregel op serverniveau stelt een externe toepassing, zoals het opdrachtregelprogramma `psql` of PostgreSQL Workbench, in staat om via de Azure Database for PostgreSQL-servicefirewall verbinding te maken met uw server.

In het volgende voorbeeld wordt een firewallregel met de naam **AllowMyIP** gemaakt waarmee verbindingen van een specifiek IP-adres, 192.168.0.1, worden toegestaan. Vervang dit door een IP-adres of een bereik van IP-adressen dat overeenkomt met de locatie van waaruit u verbinding maakt.

```azurepowershell-interactive
New-AzPostgreSqlFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Verbindingen met Azure Database for PostgreSQL lopen via poort 5432. Als u verbinding probeert te maken vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 5432 mogelijk niet toegestaan. In dit scenario kunt u alleen verbinding maken met uw server als uw IT-afdeling poort 5432 opent.

## <a name="get-the-connection-information"></a>De verbindingsgegevens ophalen

Als u verbinding met uw server wilt maken, moet u hostgegevens en toegangsreferenties opgeven. Gebruik het volgende voorbeeld om de verbindingsgegevens te bepalen. Noteer de waarden voor **FullyQualifiedDomainName** en de **AdministratorLogin**.

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.postgresql.database.azure.com       myadmin
```

## <a name="connect-to-postgresql-database-using-psql"></a>Verbinding maken met een PostgreSQL-database met behulp van psql

Als op uw clientcomputer PostgreSQL is geïnstalleerd, kunt u een lokale instantie van [psql](https://www.postgresql.org/docs/current/static/app-psql.html) gebruiken om verbinding te maken met een Azure PostgreSQL-server. U kunt ook een vooraf geïnstalleerde versie openen van het opdrachtregelhulpprogramma `psql` in Azure Cloud Shell door in een codevoorbeeld in dit artikel de knop **Proberen** te selecteren. Andere manieren om toegang te krijgen tot Azure Cloud Shell zijn de selectie van de knop **> _** in de werkbalk rechtsboven in de Azure Portal of een bezoek aan [shell.azure.com](https://shell.azure.com/).

1. Maak verbinding met uw Azure PostgreSQL-server met behulp van het opdrachtregelprogramma `psql`.

   ```azurepowershell-interactive
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Met de volgende opdracht maakt u bijvoorbeeld verbinding met de standaarddatabase **postgres** op uw PostgreSQL-server `mydemoserver.postgres.database.azure.com` met behulp van toegangsreferenties. Voer het `<server_admin_password>` in dat u koos toen u werd gevraagd om een wachtwoord.

   ```azurepowershell-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Als u liever een URL-pad gebruikt om verbinding te maken met Postgres, URL-codeert u het teken @ in de gebruikersnaam met `%40`. De verbindingsreeks voor psql zou bijvoorbeeld worden: `psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres`

1. Wanneer u met de server bent verbonden, maakt u bij de prompt een lege database.

   ```sql
   CREATE DATABASE mypgsqldb;
   ```

1. Voer na de prompt de volgende opdracht uit om verbinding te maken met de zojuist gemaakte database **mypgsqldb**:

   ```sql
   \c mypgsqldb
   ```

## <a name="create-tables-in-the-database"></a>Tabellen maken in de database

Nu u weet hoe u verbinding kunt maken met de Azure Database for PostgreSQL-database, is het tijd om enkele eenvoudige taken uit te voeren.

We gaan eerst een tabel maken en hierin enkele gegevens laden. Laten we een tabel maken waarin voorraadgegevens worden opgeslagen.

```sql
CREATE TABLE inventory (
  id serial PRIMARY KEY,
  name VARCHAR(50),
  quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Gegevens laden in de tabellen

De volgende stap bestaat uit het toevoegen van gegevens aan de tabel. Voer bij de opdrachtprompt de volgende query uit om enkele rijen met gegevens in te voegen.

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150);
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

We hebben nu twee rijen met voorbeeldgegevens in de tabel die u eerder hebt gemaakt.

## <a name="query-and-update-the-data-in-the-tables"></a>De gegevens in de tabellen opvragen en bijwerken

Voer de volgende query uit om gegevens op te halen uit de databasetabel.

```sql
SELECT * FROM inventory;
```

U kunt ook de gegevens in de tabellen bijwerken.

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

De rij wordt dan meteen bijgewerkt wanneer u gegevens ophaalt.

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Een database herstellen naar een eerder tijdstip

U kunt de server naar een eerder tijdstip herstellen. De herstelde gegevens worden gekopieerd naar een nieuwe server. De bestaande server blijft ongewijzigd. Als een tabel bijvoorbeeld per ongeluk wordt verwijderd, kunt u de server herstellen naar een tijdstip net voordat de tabel werd verwijderd. Vervolgens kunt u de ontbrekende tabel en gegevens ophalen van de herstelde kopie van de server.

Als u de server wilt herstellen, gebruikt u de PowerShell-cmdlet `Restore-AzPostgreSqlServer`.

### <a name="run-the-restore-command"></a>De herstelopdracht uitvoeren

Voer het volgende voorbeeld uit in PowerShell uit om de server te herstellen.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzPostgreSqlServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

Wanneer u een server naar een eerder tijdstip herstelt, wordt er een nieuwe server gemaakt. De oorspronkelijke server en de bijbehorende databases van het opgegeven tijdstip worden naar de nieuwe server gekopieerd.

De locatie en prijscategorie van de herstelde server zijn hetzelfde als die van de oorspronkelijke server.

Nadat het herstelproces is voltooid, zoekt u de nieuwe server en controleert u of de gegevens correct zijn hersteld. De aanmeldingsnaam en het wachtwoord voor de nieuwe server zijn hetzelfde als voor de bestaande server op het moment dat de herstelbewerking werd gestart. Het wachtwoord kan worden gewijzigd op de pagina **Overzicht** van de nieuwe server.

De nieuwe server die is gemaakt tijdens een herstelbewerking, bevat niet de VNet-service-eindpunten die bestonden op de oorspronkelijke server. Deze regels moeten afzonderlijk worden ingesteld voor de nieuwe server. Firewallregels van de oorspronkelijke server worden hersteld.

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u Azure-resources in een resourcegroep gemaakt. Als u deze resources in de toekomst niet nodig denkt te hebben, kunt u de servergroep verwijderen. Druk op de knop *Verwijderen* op de pagina *Overzicht* voor uw servergroep. Wanneer u daarom op een pop-uppagina wordt gevraagd, bevestigt u de naam van de servergroep en klikt u op de laatste knop *Verwijderen*.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een back-up van Azure Database for PostgreSQL-server maken en deze terugzetten met behulp van PowerShell](howto-restore-server-powershell.md)
