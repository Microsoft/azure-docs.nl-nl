---
title: Door TDE certificaat beheerde instantie migreren
description: Een certificaat voor het beveiligen van de database versleutelings sleutel van een Data Base met Transparent Data Encryption naar Azure SQL Managed instance migreren
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein, jovanpop
ms.date: 07/21/2020
ms.openlocfilehash: c465da3d5d812ea7e811cbe59318122700c6e786
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97824666"
---
# <a name="migrate-a-certificate-of-a-tde-protected-database-to-azure-sql-managed-instance"></a>Een certificaat van een met TDE beveiligde data base migreren naar een met Azure SQL beheerd exemplaar
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Wanneer u een Data Base die wordt beveiligd door [transparent Data Encryption (TDE),](/sql/relational-databases/security/encryption/transparent-data-encryption) migreert naar een door Azure SQL beheerd exemplaar met behulp van de systeem eigen herstel optie, moet het bijbehorende certificaat uit het SQL Server exemplaar worden gemigreerd voordat de data base wordt hersteld. Dit artikel begeleidt u bij het hand matig migreren van het certificaat naar Azure SQL Managed instance:

> [!div class="checklist"]
>
> * Het certificaat exporteren naar een pfx-bestand (Personal Information Exchange)
> * Het certificaat uit een bestand uitpakken naar een base-64-teken reeks
> * Uploaden met een Power shell-cmdlet

Zie [How to migrate your on-premises data base to Azure SQL Managed instance](../../dms/tutorial-sql-server-to-managed-instance.md)(Engelstalig) met Azure database Migration service voor een alternatieve optie waarbij een volledig beheerde service wordt gebruikt voor naadloze migratie van zowel een met TDe beveiligde Data Base als een bijbehorend certificaat.

> [!IMPORTANT]
> Een gemigreerde certificaat wordt alleen gebruikt voor het herstellen van de TDE-beveiligde data base. Zodra het herstellen is voltooid, wordt het gemigreerde certificaat vervangen door een andere Protector, een door een service beheerd certificaat of een asymmetrische sleutel van de sleutel kluis, afhankelijk van het type TDE dat u op het exemplaar hebt ingesteld.

## <a name="prerequisites"></a>Vereisten

U moet de volgende vereiste zaken hebben om de stappen in dit artikel uit te voeren:

* Het [Pvk2Pfx](/windows-hardware/drivers/devtest/pvk2pfx)-opdrachtregelprogramma is geïnstalleerd op de on-premises server of een andere computer met toegang tot het certificaat dat als een bestand wordt geëxporteerd. Het Pvk2Pfx-hulp programma maakt deel uit van de [Enter prise Windows Driver Kit](/windows-hardware/drivers/download-the-wdk), een zelf opgenomen opdracht regel omgeving.
* [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell) versie 5.0 of hoger is geïnstalleerd.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Zorg ervoor dat u over de volgende zaken beschikt:

* De module Azure PowerShell [geïnstalleerd en bijgewerkt](/powershell/azure/install-az-ps).
* [AZ. SQL-module](https://www.powershellgallery.com/packages/Az.Sql).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door Azure SQL Managed instance, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/) voor deze cmdlets. De argumenten voor de opdrachten in de module AZ en in de AzureRM-modules zijn aanzienlijk identiek.

Voer de volgende opdrachten in Power shell uit om de module te installeren of bij te werken:

```azurepowershell
Install-Module -Name Az.Sql
Update-Module -Name Az.Sql
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

* * *

## <a name="export-the-tde-certificate-to-a-pfx-file"></a>Het TDE-certificaat exporteren naar een pfx-bestand

Het certificaat kan rechtstreeks vanuit de bron SQL Server-instantie of uit het certificaat archief worden geëxporteerd als het daar wordt bewaard.

### <a name="export-the-certificate-from-the-source-sql-server-instance"></a>Het certificaat van het bron SQL Server exemplaar exporteren

Gebruik de volgende stappen om het certificaat te exporteren met SQL Server Management Studio en converteer het naar. pfx-indeling. De algemene namen *TDE_Cert* en *full_path* worden gebruikt voor certificaat-en bestands namen en paden door de stappen uit te voeren. Deze moeten worden vervangen door de werkelijke namen.

1. Open een nieuw query venster in SSMS en maak verbinding met het bron SQL Server exemplaar.

1. Gebruik het volgende script om TDE beveiligde data bases weer te geven en de naam op te halen van het certificaat dat de versleuteling van de te migreren data base beveiligt:

   ```sql
   USE master
   GO
   SELECT db.name as [database_name], cer.name as [certificate_name]
   FROM sys.dm_database_encryption_keys dek
   LEFT JOIN sys.certificates cer
   ON dek.encryptor_thumbprint = cer.thumbprint
   INNER JOIN sys.databases db
   ON dek.database_id = db.database_id
   WHERE dek.encryption_state = 3
   ```

   ![Lijst met TDE-certificaten](./media/tde-certificate-migrate/onprem-certificate-list.png)

1. Voer het volgende script uit om het certificaat te exporteren naar een bestandspaar (.cer en .pvk), waarbij de gegevens van de openbare en persoonlijke sleutel behouden blijven:

   ```sql
   USE master
   GO
   BACKUP CERTIFICATE TDE_Cert
   TO FILE = 'c:\full_path\TDE_Cert.cer'
   WITH PRIVATE KEY (
     FILE = 'c:\full_path\TDE_Cert.pvk',
     ENCRYPTION BY PASSWORD = '<SomeStrongPassword>'
   )
   ```

   ![Back-TDE certificaat](./media/tde-certificate-migrate/backup-onprem-certificate.png)

1. Gebruik de Power shell-console om certificaat gegevens van een paar nieuwe bestanden naar een pfx-bestand te kopiëren met het Pvk2Pfx-hulp programma:

   ```cmd
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-the-certificate-from-a-certificate-store"></a>Het certificaat uit een certificaat archief exporteren

Als het certificaat in het certificaat archief van de SQL Server lokale machine wordt bewaard, kan dit worden geëxporteerd met de volgende stappen:

1. Open de Power shell-console en voer de volgende opdracht uit om de module Certificaten van micro soft Management console te openen:

   ```cmd
   certlm
   ```

2. Vouw in de MMC-module Certificaten het pad persoonlijk > certificaten uit om de lijst met certificaten weer te geven.

3. Klik met de rechter muisknop op het certificaat en klik op **exporteren**.

4. Volg de wizard om het certificaat en de persoonlijke sleutel naar een. pfx-indeling te exporteren.

## <a name="upload-the-certificate-to-azure-sql-managed-instance-using-an-azure-powershell-cmdlet"></a>Upload het certificaat naar een Azure SQL Managed instance met een Azure PowerShell-cmdlet

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Begin met de voorbereidingsstappen in PowerShell:

   ```azurepowershell
   # import the module into the PowerShell session
   Import-Module Az
   # connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   Get-AzSubscription
   # set subscription for the session
   Select-AzSubscription <subscriptionId>
   ```

2. Als alle voorbereidende stappen zijn uitgevoerd, voert u de volgende opdrachten uit om een base-64-gecodeerd certificaat te uploaden naar het beheerde exemplaar van de doel groep:

   ```azurepowershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -AsByteStream
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "<password>"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<resourceGroupName>" `
       -ManagedInstanceName "<managedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U moet eerst [een Azure-sleutel kluis instellen](../../key-vault/general/manage-with-cli2.md) met uw *PFX* -bestand.

1. Begin met de voorbereidingsstappen in PowerShell:

   ```azurecli
   # connect to Azure with an interactive dialog for sign-in
   az login

   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   az account list

   # set subscription for the session
   az account set --subscription <subscriptionId>
   ```

1. Als alle voorbereidende stappen zijn uitgevoerd, voert u de volgende opdrachten uit om het door base-64 gecodeerde certificaat te uploaden naar het beheerde exemplaar van de bestemming:

   ```azurecli
   az sql mi tde-key set --server-key-type AzureKeyVault --kid "<keyVaultId>" `
       --managed-instance "<managedInstanceName>" --resource-group "<resourceGroupName>"
   ```

* * *

Het certificaat is nu beschikbaar voor het opgegeven beheerde exemplaar en de back-up van de bijbehorende met TDE beveiligde data base kan worden hersteld.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een certificaat kunt migreren van de versleutelings sleutel van een Data Base met Transparent Data Encryption, van het on-premises of het IaaS SQL Server-exemplaar naar Azure SQL Managed instance.

Zie [een back-up van een Data Base herstellen naar een beheerd exemplaar van Azure SQL](restore-sample-database-quickstart.md) voor meer informatie over het herstellen van een database back-up naar Azure SQL Managed instance.