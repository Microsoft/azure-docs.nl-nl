---
title: Transparent Data Encryption
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Een overzicht van transparante gegevensversleuteling voor Azure SQL Database, Azure SQL Managed Instance en Azure Synapse Analytics. Het document bevat de voordelen en configuratieopties, waaronder door de service beheerde transparante gegevensversleuteling en Bring Your Own Key.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 10/12/2020
ms.openlocfilehash: 160066f9599388256c7c821732a1e06fec49bdf5
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749038"
---
# <a name="transparent-data-encryption-for-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Transparante gegevensversleuteling voor SQL Database, SQL Managed Instance en Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Met Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) kunt u Azure SQL Database, Azure SQL Managed Instance en Azure Synapse Analytics beschermen tegen de bedreiging van schadelijke offlineactiviteiten door data-at-rest te versleutelen. Het voert in realtime versleuteling en ontsleuteling van de database, bijbehorende back-ups en transactielogboekbestanden 'at-rest' uit, zonder dat er wijzigingen in de toepassing moeten worden aangebracht. TDE is standaard ingeschakeld voor alle nieuw geïmplementeerde SQL Databases en moet handmatig worden ingeschakeld voor oudere databases van Azure SQL Database, Azure SQL Managed Instance. TDE moet handmatig worden ingeschakeld voor Azure Synapse Analytics.

TDE voert realtime I/O-versleuteling en ontsleuteling van de gegevens op paginaniveau uit. Elke pagina wordt ontsleuteld wanneer deze wordt ingelezen in het geheugen en vervolgens versleuteld voordat deze naar een schijf wordt geschreven. TDE versleutelt de opslag van een hele database met behulp van een symmetrische sleutel die de Database Encryption Key (DEK) wordt genoemd. Bij het opstarten van de database wordt de versleutelde DEK ontsleuteld en vervolgens gebruikt voor het ontsleutelen en opnieuw versleutelen van de databasebestanden in het SQL Server database-engineproces. DEK wordt beveiligd door de TDE-beveiliging. TDE-beveiliging is een door de service beheerd certificaat (door de service beheerde transparante gegevensversleuteling) of een asymmetrische sleutel die is opgeslagen in [Azure Key Vault](../../key-vault/general/security-overview.md) (door de klant beheerde transparante gegevensversleuteling).

Voor Azure SQL Database en Azure Synapse wordt de TDE-beveiliging ingesteld op [serverniveau](logical-servers.md) en overgenomen door alle databases die aan die server zijn gekoppeld. Voor Azure SQL Managed Instance wordt de TDE-beveiliging ingesteld op exemplaarniveau en wordt deze overgenomen door alle versleutelde databases op dat exemplaar. De term *server* verwijst in dit document zowel naar de server als het exemplaar, tenzij anders vermeld.

> [!IMPORTANT]
> Alle nieuw gemaakte databases in SQL Database worden standaard versleuteld met behulp van door de service beheerde transparante gegevensversleuteling. Bestaande SQL-databases die zijn gemaakt vóór mei 2017 en SQL-databases die zijn gemaakt via herstel, geo-replicatie en databasekopieën, worden niet standaard versleuteld. Bestaande SQL Managed Instance databases die vóór februari 2019 zijn gemaakt, worden niet standaard versleuteld. SQL Managed Instance databases die zijn gemaakt via herstellen, nemen de versleutelingsstatus over van de bron.

> [!NOTE]
> TDE kan niet worden gebruikt voor het versleutelen van systeemdatabases, zoals de **hoofddatabase,** in Azure SQL Database en Azure SQL Managed Instance. De **hoofddatabase** bevat objecten die nodig zijn om de TDE-bewerkingen uit te voeren op de gebruikersdatabases. Het wordt aanbevolen geen gevoelige gegevens op te slaan in de systeemdatabases. [Infrastructuurversleuteling](transparent-data-encryption-byok-overview.md#doubleencryption) wordt nu uitgerold, waardoor de systeemdatabases worden versleuteld, inclusief de hoofddatabase. 

## <a name="service-managed-transparent-data-encryption"></a>Door de service beheerde transparante gegevensversleuteling

In Azure is de standaardinstelling voor TDE dat de DEK wordt beveiligd door een ingebouwd servercertificaat. Het ingebouwde servercertificaat is uniek voor elke server en het gebruikte versleutelingsalgoritme is AES 256. Als een database een geo-replicatierelatie heeft, worden zowel de primaire als de geo-secundaire database beveiligd door de bovenliggende serversleutel van de primaire database. Als twee databases zijn verbonden met dezelfde server, delen ze ook hetzelfde ingebouwde certificaat. Microsoft roteert deze certificaten automatisch in overeenstemming met het interne beveiligingsbeleid en de hoofdsleutel wordt beveiligd door een intern geheim opslag van Microsoft. Klanten kunnen controleren SQL Database en SQL Managed Instance naleving van het interne beveiligingsbeleid in onafhankelijke controlerapporten van derden die beschikbaar zijn in [het Microsoft Trust Center.](https://servicetrust.microsoft.com/)

Microsoft verplaatst en beheert de sleutels ook naadloos naar behoefte voor geo-replicatie en herstel.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Door de klant beheerde transparante gegevensversleuteling - Bring Your Own Key

Door de klant beheerde TDE wordt ook wel Bring Your Own Key (BYOK)-ondersteuning voor TDE genoemd. In dit scenario is de TDE-beveiliging die de DEK versleutelt een door de klant beheerde asymmetrische sleutel die wordt opgeslagen in een door de klant beheerde en beheerde Azure Key Vault (het externe sleutelbeheersysteem in de cloud van Azure) en nooit de sleutelkluis verlaat. De TDE-beveiliging kan worden [gegenereerd door](../../key-vault/keys/hsm-protected-keys.md) de sleutelkluis of worden overgedragen naar de sleutelkluis vanaf een on-premises HSM-apparaat (Hardware Security Module). SQL Database, SQL Managed Instance en Azure Synapse moeten machtigingen worden verleend aan de sleutelkluis van de klant om de DEK te ontsleutelen en te versleutelen. Als de machtigingen van de server voor de sleutelkluis zijn ingetrokken, is een database niet toegankelijk en worden alle gegevens versleuteld

Met TDE met Azure Key Vault-integratie kunnen gebruikers sleutelbeheertaken beheren, waaronder sleutelrotaties, sleutelkluismachtigingen, sleutelback-ups, en controle/rapportage inschakelen voor alle TDE-beveiligingen met behulp van Azure Key Vault-functionaliteit. Key Vault biedt centraal sleutelbeheer, maakt gebruik van nauw bewaakte HMS's en maakt scheiding van taken tussen het beheer van sleutels en gegevens mogelijk om te voldoen aan de naleving van het beveiligingsbeleid.
Zie Transparent data encryption with Azure Key Vault integration (Transparante gegevensversleuteling met [Azure Key Vault-integratie)](transparent-data-encryption-byok-overview.md)voor Azure SQL Database en Azure Synapse informatie over BYOK.

Als u TDE wilt gaan gebruiken met Azure Key Vault-integratie, bekijkt u de handleiding Transparante gegevensversleuteling in-/uit-Key Vault. [](transparent-data-encryption-byok-configure.md)

## <a name="move-a-transparent-data-encryption-protected-database"></a>Een met transparante gegevensversleuteling beveiligde database verplaatsen

U hoeft geen databases te ontsleutelen voor bewerkingen in Azure. De TDE-instellingen voor de brondatabase of primaire database worden transparant overgenomen op het doel. Bewerkingen die zijn opgenomen, hebben betrekking op:

- Geo-herstel
- Selfservice voor herstel naar een bepaald tijdstip
- Herstel van een verwijderde database
- Actieve geo-replicatie
- Een databasekopie maken
- Back-upbestand herstellen naar Azure SQL Managed Instance

> [!IMPORTANT]
> Handmatige COPY ONLY-back-up maken van een database die is versleuteld door een door de service beheerde TDE wordt niet ondersteund in Azure SQL Managed Instance, omdat het certificaat dat wordt gebruikt voor versleuteling niet toegankelijk is. Gebruik de functie voor herstel naar een bepaald tijdstip om dit type database te verplaatsen naar een SQL Managed Instance of om over te schakelen naar een door de klant beheerde sleutel.

Wanneer u een met TDE beveiligde database exporteert, wordt de geëxporteerde inhoud van de database niet versleuteld. Deze geëxporteerde inhoud wordt opgeslagen in niet-versleutelde BACPAC-bestanden. Zorg ervoor dat u de BACPAC-bestanden op de juiste manier bebeveiligen en TDE inschakelen nadat het importeren van de nieuwe database is voltooid.

Als het BACPAC-bestand bijvoorbeeld wordt geëxporteerd uit een SQL Server-exemplaar, wordt de geïmporteerde inhoud van de nieuwe database niet automatisch versleuteld. En als het BACPAC-bestand wordt geïmporteerd in een SQL Server-exemplaar, wordt de nieuwe database ook niet automatisch versleuteld.

De enige uitzondering hierop is wanneer u een database exporteert van en naar SQL Database. TDE is ingeschakeld voor de nieuwe database, maar het BACPAC-bestand zelf is nog steeds niet versleuteld.

## <a name="manage-transparent-data-encryption"></a>Transparante gegevensversleuteling beheren

# <a name="the-azure-portal"></a>[Azure Portal](#tab/azure-portal)

Beheer TDE in de Azure Portal.

Als u TDE wilt configureren via Azure Portal, moet u zijn verbonden als de Azure-eigenaar, inzender of SQL Security Manager.

Schakel TDE in en uit op databaseniveau. Gebruik Azure SQL Managed Instance Transact-SQL (T-SQL) om TDE in en uit te schakelen voor een database. Voor Azure SQL Database en Azure Synapse kunt u TDE voor de database in de [Azure Portal](https://portal.azure.com) beheren nadat u zich hebt aangemeld met het Account van de Azure-beheerder of inzender. Zoek de TDE-instellingen onder uw gebruikersdatabase. Standaard wordt door de service beheerde transparante gegevensversleuteling gebruikt. Er wordt automatisch een TDE-certificaat gegenereerd voor de server die de database bevat.

![Door de service beheerde transparante gegevensversleuteling](./media/transparent-data-encryption-tde-overview/service-managed-transparent-data-encryption.png)  

U stelt de TDE-hoofdsleutel, ook wel de TDE-beveiliging genoemd, in op server- of exemplaarniveau. Als u TDE wilt gebruiken met BYOK-ondersteuning en uw databases wilt beveiligen met een sleutel van Key Vault, opent u de TDE-instellingen onder uw server.

![Transparante gegevensversleuteling met Bring Your Own Key ondersteuning](./media/transparent-data-encryption-tde-overview/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

TDE beheren met behulp van PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund, maar alle toekomstige ontwikkeling is voor de Az.Sql-module. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/) voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek.

Als u TDE wilt configureren via PowerShell, moet u zijn verbonden als de Azure-eigenaar, inzender of SQL Security Manager.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Cmdlets voor Azure SQL Database en Azure Synapse

Gebruik de volgende cmdlets voor Azure SQL Database en Azure Synapse:

| Cmdlet | Beschrijving |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Hiermee schakelt u transparante gegevensversleuteling voor een database in of uit.|
| [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Haalt de transparante gegevensversleutelingstoestand voor een database op. |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Controleert de voortgang van de versleuteling voor een database. |
| [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Voegt een Key Vault sleutel toe aan een server. |
| [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Haalt de Key Vault voor een server op  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Hiermee stelt u de transparent data encryption-beveiliging voor een server. |
| [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Haalt de transparante gegevensversleutelingsbeveiliging op |
| [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Hiermee verwijdert u Key Vault sleutel van een server. |
|  | |

> [!IMPORTANT]
> Gebruik Azure SQL Managed Instance T-SQL [ALTER DATABASE-opdracht](/sql/t-sql/statements/alter-database-azure-sql-database) om TDE in en uit te schakelen op databaseniveau, en controleer het [PowerShell-voorbeeldscript](transparent-data-encryption-byok-configure.md) om TDE op exemplaarniveau te beheren.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)

TDE beheren met behulp van Transact-SQL.

Maak verbinding met de database met behulp van een aanmelding die een beheerder of lid is van de **rol dbmanager** in de hoofddatabase.

| Opdracht | Beschrijving |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) | MET SET ENCRYPTION ON/OFF wordt een database versleuteld of ontsleuteld |
| [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Retourneert informatie over de versleutelingstoestand van een database en de bijbehorende databaseversleutelingssleutels |
| [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Retourneert informatie over de versleutelingstoestand van elk Azure Synapse knooppunt en de bijbehorende databaseversleutelingssleutels |
|  | |

U kunt de TDE-beveiliging niet overschakelen naar een sleutel van Key Vault met behulp van Transact-SQL. Gebruik PowerShell of de Azure Portal.

# <a name="rest-api"></a>[REST API](#tab/azure-RESTAPI)

Beheer TDE met behulp van de REST API.

Als u TDE wilt configureren via REST API, moet u zijn verbonden als de Azure-eigenaar, inzender of SQL Security Manager.
Gebruik de volgende set opdrachten voor Azure SQL Database en Azure Synapse:

| Opdracht | Beschrijving |
| --- | --- |
|[Server maken of bijwerken](/rest/api/sql/servers/createorupdate)|Voegt een Azure Active Directory-identiteit toe aan een server. (wordt gebruikt om toegang te verlenen tot Key Vault)|
|[Serversleutel maken of bijwerken](/rest/api/sql/serverkeys/createorupdate)|Voegt een Key Vault sleutel toe aan een server.|
|[Serversleutel verwijderen](/rest/api/sql/serverkeys/delete)|Hiermee verwijdert u Key Vault sleutel van een server. |
|[Serversleutels ophalen](/rest/api/sql/serverkeys/get)|Haalt een specifieke Key Vault sleutel op van een server.|
|[Serversleutels per server opneren](/rest/api/sql/serverkeys/listbyserver)|Haalt de Key Vault voor een server op. |
|[Versleutelingsbeveiliging maken of bijwerken](/rest/api/sql/encryptionprotectors/createorupdate)|Hiermee stelt u de TDE-beveiliging voor een server.|
|[Versleutelingsbeveiliging krijgen](/rest/api/sql/encryptionprotectors/get)|Haalt de TDE-beveiliging voor een server.|
|[Lijst met versleutelingsbeveiligingen per server](/rest/api/sql/encryptionprotectors/listbyserver)|Haalt de TDE-beveiligingen voor een server op. |
|[Configuratie van Transparent Data Encryption maken Transparent Data Encryption bijwerken](/rest/api/sql/transparentdataencryptions/createorupdate)|Hiermee wordt TDE voor een database in- of uitgeschakeld.|
|[Een Transparent Data Encryption configureren](/rest/api/sql/transparentdataencryptions/get)|Haalt de TDE-configuratie voor een database op.|
|[Lijst Transparent Data Encryption configuratieresultaten](/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Haalt het versleutelingsresultaat voor een database op.|

## <a name="see-also"></a>Zie ook

- SQL Server die wordt uitgevoerd op een virtuele Azure-machine, kan ook een asymmetrische sleutel van Key Vault. De configuratiestappen verschillen van het gebruik van een asymmetrische sleutel in SQL Database en SQL Managed Instance. Zie [Extensible key management by using Azure Key Vault (SQL Server) (Extensible key management](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server)met behulp van Azure Key Vault (SQL Server) voor meer informatie.
- Zie Transparante gegevensversleuteling voor een algemene beschrijving [van](/sql/relational-databases/security/encryption/transparent-data-encryption)TDE.
- Zie Transparante gegevensversleuteling met ondersteuning voor Azure SQL Database, Azure SQL Managed Instance en Azure Synapse voor meer informatie over TDE met [BYOK Bring Your Own Key ondersteuning.](transparent-data-encryption-byok-overview.md)
- Als u TDE wilt gaan gebruiken met Bring Your Own Key-ondersteuning, bekijkt u de handleiding Transparante gegevensversleuteling in [Key Vault.](transparent-data-encryption-byok-configure.md)
- Zie Beveiligde toegang tot Key Vault sleutelkluis voor meer informatie over de beveiliging van [een sleutelkluis.](../../key-vault/general/security-overview.md)