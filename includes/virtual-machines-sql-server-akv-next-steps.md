---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 6195b949cc71043dfa7a12bdece7a311dbde5e21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95559618"
---
## <a name="next-steps"></a>Volgende stappen

Nadat u Azure Key Vault-integratie hebt ingeschakeld, kunt u SQL Server versleuteling inschakelen op uw SQL-VM. Eerst moet u in uw sleutel kluis een asymmetrische sleutel maken en een symmetrische sleutel binnen SQL Server op uw VM. Daarna kunt u T-SQL-instructies uitvoeren om versleuteling in te scha kelen voor uw data bases en back-ups.

Er zijn verschillende soorten versleuteling waarmee u kunt profiteren van:

* [TDE (Transparent Data Encryption)](/sql/relational-databases/security/encryption/transparent-data-encryption)
* [Versleutelde back-ups](/sql/relational-databases/backup-restore/backup-encryption)
* [Versleuteling op kolom niveau (CLE)](/sql/t-sql/functions/cryptographic-functions-transact-sql)

De volgende Transact-SQL-scripts bieden voor beelden voor elk van deze gebieden.

### <a name="prerequisites-for-examples"></a>Vereisten voor voor beelden

Elk voor beeld is gebaseerd op de twee vereisten: een asymmetrische sleutel uit de sleutel kluis met de naam **CONTOSO_KEY** en een referentie die is gemaakt door de Azure-integratie functie met de naam **Azure_EKM_cred**. Met de volgende Transact-SQL-opdrachten worden deze vereisten ingesteld voor het uitvoeren van de voor beelden.

``` sql
USE master;
GO

--create credential
--The <<SECRET>> here requires the <Application ID> (without hyphens) and <Secret> to be passed together without a space between them.
CREATE CREDENTIAL Azure_EKM_cred
    WITH IDENTITY = 'keytestvault', --keyvault
    SECRET = '<<SECRET>>'
FOR CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov;


--Map the credential to a SQL login that has sysadmin permissions. This allows the SQL login to access the key vault when creating the asymmetric key in the next step.
ALTER LOGIN [SQL_Login]
ADD CREDENTIAL Azure_EKM_cred;


CREATE ASYMMETRIC KEY CONTOSO_KEY
FROM PROVIDER [AzureKeyVault_EKM_Prov]
WITH PROVIDER_KEY_NAME = 'KeyName_in_KeyVault',  --The key name here requires the key we created in the key vault
CREATION_DISPOSITION = OPEN_EXISTING;
```

### <a name="transparent-data-encryption-tde"></a>Transparent Data Encryption (TDE)

1. Maak een SQL Server aanmelding die moet worden gebruikt door de data base-engine voor TDE en voeg vervolgens de referentie toe.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it loads a database
   -- encrypted by TDE.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the TDE Login to add the credential for use by the
   -- Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred;
   GO
   ```

1. Maak de database versleutelings sleutel die wordt gebruikt voor TDE.

   ``` sql
   USE ContosoDatabase;
   GO

   CREATE DATABASE ENCRYPTION KEY 
   WITH ALGORITHM = AES_128 
   ENCRYPTION BY SERVER ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the database to enable transparent data encryption.
   ALTER DATABASE ContosoDatabase
   SET ENCRYPTION ON;
   GO
   ```

### <a name="encrypted-backups"></a>Versleutelde back-ups

1. Maak een SQL Server aanmelding die door de data base-engine moet worden gebruikt voor het versleutelen van back-ups en voeg de referentie toe aan de-server.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it is encrypting the backup.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the Encrypted Backup Login to add the credential for use by
   -- the Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred ;
   GO
   ```

1. Back-up maken van de data base die versleuteling opgeeft met de asymmetrische sleutel die is opgeslagen in de sleutel kluis.

   ``` sql
   USE master;
   BACKUP DATABASE [DATABASE_TO_BACKUP]
   TO DISK = N'[PATH TO BACKUP FILE]'
   WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD,
   ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
   GO
   ```

### <a name="column-level-encryption-cle"></a>Versleuteling op kolom niveau (CLE)

Met dit script maakt u een symmetrische sleutel die wordt beveiligd door de asymmetrische sleutel in de sleutel kluis. vervolgens wordt de symmetrische sleutel gebruikt voor het versleutelen van gegevens in de-data base.

``` sql
CREATE SYMMETRIC KEY DATA_ENCRYPTION_KEY
WITH ALGORITHM=AES_256
ENCRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

DECLARE @DATA VARBINARY(MAX);

--Open the symmetric key for use in this session
OPEN SYMMETRIC KEY DATA_ENCRYPTION_KEY
DECRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

--Encrypt syntax
SELECT @DATA = ENCRYPTBYKEY(KEY_GUID('DATA_ENCRYPTION_KEY'), CONVERT(VARBINARY,'Plain text data to encrypt'));

-- Decrypt syntax
SELECT CONVERT(VARCHAR, DECRYPTBYKEY(@DATA));

--Close the symmetric key
CLOSE SYMMETRIC KEY DATA_ENCRYPTION_KEY;
```

## <a name="additional-resources"></a>Aanvullende bronnen

Zie voor meer informatie over het gebruik van deze versleutelings functies [EKM gebruiken met SQL Server-versleutelings functies](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server#UsesOfEKM).

In de stappen in dit artikel wordt ervan uitgegaan dat u al SQL Server hebt uitgevoerd op een virtuele machine van Azure. Als dat niet het geval is, raadpleegt u [een SQL Server virtuele machine inrichten in azure](../articles/azure-sql/virtual-machines/windows/create-sql-vm-portal.md). Zie [SQL Server op azure virtual machines Overview](../articles/azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)voor meer informatie over het uitvoeren van SQL Server op Azure-vm's.