---
title: 'Zelf studie: aan de slag met Always Encrypted met beveiligde enclaves in Azure SQL Database'
description: In deze zelf studie leert u hoe u een eenvoudige omgeving kunt maken voor Always Encrypted met beveiligde enclaves in Azure SQL Database en hoe u gegevens in-place versleutelt, en hoe u op versleutelde kolommen uitgebreide, vertrouwelijke query's kunt uitvoeren met behulp van SQL Server Management Studio (SSMS).
keywords: versleutelen van gegevens, SQL-versleuteling, database versleuteling, gevoelige gegevens, Always Encrypted, beveiligde enclaves, SGX, Attestation
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: tutorial
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: 809ac72977b670faff984ad39effb1c70767e141
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102120943"
---
# <a name="tutorial-getting-started-with-always-encrypted-with-secure-enclaves-in-azure-sql-database"></a>Zelf studie: aan de slag met Always Encrypted met beveiligde enclaves in Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted met beveiligde enclaves voor Azure SQL Database is momenteel beschikbaar als **open bare preview**.

In deze zelf studie leert u hoe u aan de slag kunt gaan met [Always encrypted met beveiligde enclaves](/sql/relational-databases/security/encryption/always-encrypted-enclaves) in Azure SQL database. Hiermee wordt het volgende weer gegeven:

> [!div class="checklist"]
> - Een omgeving maken voor het testen en evalueren van Always Encrypted met beveiligde enclaves.
> - Informatie over het versleutelen van gegevens in-place en het geven van uitgebreide vertrouwelijke query's op versleutelde kolommen met behulp van SQL Server Management Studio (SSMS).

## <a name="prerequisites"></a>Vereisten

Voor deze zelf studie is Azure PowerShell en [SSMS](/sql/ssms/download-sql-server-management-studio-ssms)vereist.

### <a name="powershell-requirements"></a>Power shell-vereisten

Zie [Overzicht van Azure PowerShell](/powershell/azure) voor meer informatie over het installeren en uitvoeren van Azure PowerShell. 

Minimale versie van AZ-modules die vereist zijn om Attestation-bewerkingen te ondersteunen:

- Az 4.5.0
- Az.Accounts 1.9.2
- Az.Attestation 0.1.8

Voer de onderstaande opdracht uit om de geïnstalleerde versie van alle AZ-modules te controleren:

```powershell
Get-InstalledModule
```

Als de versies niet overeenkomen met de minimum vereiste, voert u de `Update-Module` opdracht uit.

De PowerShell Gallery heeft verouderde Transport Layer Security (TLS) versies 1,0 en 1,1. TLS 1,2 of een latere versie wordt aanbevolen. Als u een TLS-versie lager dan 1,2 gebruikt, worden de volgende fouten weer gegeven:

- `WARNING: Unable to resolve package source 'https://www.powershellgallery.com/api/v2'`
- `PackageManagement\Install-Package: No match was found for the specified search criteria and module name.`

Als u wilt blijven werken met de PowerShell Gallery, voert u de volgende opdracht uit vóór de installatie-module-opdrachten

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
```

### <a name="ssms-requirements"></a>SSMS-vereisten

Zie [down load SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) voor informatie over het downloaden van SSMS.

De vereiste minimale versie van SSMS is 18,8.


## <a name="step-1-create-and-configure-a-server-and-a-dc-series-database"></a>Stap 1: een server en een DC-Series-Data Base maken en configureren

In deze stap maakt u een nieuwe Azure SQL Database logische server en een nieuwe Data Base met behulp van de generatie van de fabrikant van de DC-serie, die vereist is voor Always Encrypted met beveiligde enclaves. Zie [DC-Series](service-tiers-vcore.md#dc-series)voor meer informatie.

1. Open een Power shell-console en importeer de vereiste versie AZ.

  ```PowerShell
  Import-Module "Az" -MinimumVersion "4.5.0"
  ```
  
2. Meld u aan bij Azure. Als dat nodig is, [schakelt u over naar het abonnement](/powershell/azure/manage-subscriptions-azureps) dat u voor deze zelf studie gebruikt.

  ```PowerShell
  Connect-AzAccount
  $subscriptionId = "<your subscription ID>"
  Set-AzContext -Subscription $subscriptionId
  ```

3. Een nieuwe resourcegroep maken. 

  > [!IMPORTANT]
  > U moet de resource groep maken in een regio (locatie) die zowel de generatie van hardware voor de DC-serie als de Microsoft Azure attest ondersteunt. Zie [Beschik baarheid DC-Series](service-tiers-vcore.md#dc-series-1)voor een lijst met REGIO'S die DC-serie ondersteunen. [Dit](https://azure.microsoft.com/global-infrastructure/services/?products=azure-attestation) is de regionale Beschik baarheid van Microsoft Azure Attestation.

  ```powershell
  $resourceGroupName = "<your new resource group name>"
  $location = "<Azure region supporting DC-series and Microsoft Azure Attestation>"
  New-AzResourceGroup -Name $resourceGroupName -Location $location
  ```

4. Maak een logische Azure SQL-Server. Wanneer u hierom wordt gevraagd, voert u de naam van de server beheerder en een wacht woord in. Zorg ervoor dat u de naam en het wacht woord van de beheerder herinnert. u hebt deze later nodig om verbinding te maken met de server.

  ```powershell
  $serverName = "<your server name>" 
  New-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName -Location $location 
  ```

5. Maak een server firewall regel waarmee toegang vanuit het opgegeven IP-bereik is toegestaan.
  
  ```powershell
  $startIp = "<start of IP range>"
  $endIp = "<end of IP range>"
  $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
  ```

6. Wijs een beheerde systeem identiteit toe aan uw server. 

  ```PowerShell
  $server = Set-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName -AssignIdentity
  $serverObjectId = $server.Identity.PrincipalId
  ```

7. Maak een Data Base van de DC-serie.

  ```powershell
  $databaseName = "ContosoHR"
  $edition = "GeneralPurpose"
  $vCore = 2
  $generation = "DC"
  New-AzSqlDatabase -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -DatabaseName $databaseName `
    -Edition $edition `
    -Vcore $vCore `
    -ComputeGeneration $generation
  ```

8. De informatie over uw server en de Data Base op te halen en op te slaan. U hebt deze informatie nodig, evenals de naam van de beheerder en het wacht woord uit stap 4 in deze sectie, in latere secties.

  ```powershell
  Write-Host 
  Write-Host "Fully qualified server name: $($server.FullyQualifiedDomainName)" 
  Write-Host "Server Object Id: $serverObjectId"
  Write-Host "Database name: $databaseName"
  ```
  
## <a name="step-2-configure-an-attestation-provider"></a>Stap 2: een Attestation-provider configureren 

In deze stap maakt en configureert u een Attestation-provider in Microsoft Azure Attestation. Dit is nodig om de beveiligde enclave te bevestigen die uw data base gebruikt.

1. Kopieer het onderstaande Attestation-beleid en sla het beleid op in een tekst bestand (txt). Zie [een Attestation-provider maken en configureren](always-encrypted-enclaves-configure-attestation.md#create-and-configure-an-attestation-provider)voor meer informatie over het onderstaande beleid.

  ```output
  version= 1.0;
  authorizationrules 
  {
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==4639 ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
    => permit();
  };
  ```

2. Importeer de vereiste versie van `Az.Attestation` .  

  ```powershell
  Import-Module "Az.Attestation" -MinimumVersion "0.1.8"
  ```
  
3. Maak een Attestation-provider. 

  ```powershell
  $attestationProviderName = "<your attestation provider name>" 
  New-AzAttestation -Name $attestationProviderName -ResourceGroupName $resourceGroupName -Location $location
  ```

4. Uw Attestation-beleid configureren.
  
  ```powershell
  $policyFile = "<the pathname of the file from step 1 in this section>"
  $teeType = "SgxEnclave"
  $policyFormat = "Text"
  $policy=Get-Content -path $policyFile -Raw
  Set-AzAttestationPolicy -Name $attestationProviderName `
    -ResourceGroupName $resourceGroupName `
    -Tee $teeType `
    -Policy $policy `
    -PolicyFormat  $policyFormat
  ```

5. Verleen uw logische Azure SQL-Server toegang tot uw Attestation-provider. In deze stap gebruikt u de object-ID van de beheerde service-identiteit die u eerder aan uw server hebt toegewezen.

  ```powershell
  New-AzRoleAssignment -ObjectId $serverObjectId `
    -RoleDefinitionName "Attestation Reader" `
    -ResourceName $attestationProviderName `
    -ResourceType "Microsoft.Attestation/attestationProviders" `
    -ResourceGroupName $resourceGroupName  
  ```

6. Haal de Attestation-URL op die verwijst naar een Attestation-beleid dat u hebt geconfigureerd voor de SGX-enclave. Sla de URL op, omdat u deze later nodig hebt.

  ```powershell
  $attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $resourceGroupName 
  $attestationUrl = $attestationProvider.AttestUri + “/attest/SgxEnclave”
  Write-Host
  Write-Host "Your attestation URL is: $attestationUrl"
  ```
  
  De URL van de Attestation moet er als volgt uitzien: `https://contososqlattestation.uks.attest.azure.net/attest/SgxEnclave`

## <a name="step-3-populate-your-database"></a>Stap 3: uw data base vullen

In deze stap maakt u een tabel en vult u deze met enkele gegevens die u later versleutelt en query's uitvoert.

1. Open SSMS en maak verbinding met de **ContosoHR** -data base in de logische Azure SQL-Server die u hebt gemaakt **zonder** always encrypted ingeschakeld in de database verbinding.
    1. Geef in het dialoog venster **verbinding maken met server** de volledig gekwalificeerde naam op van uw server (bijvoorbeeld *myserver123.database.Windows.net*) en voer de gebruikers naam van de beheerder en het wacht woord in die u hebt opgegeven tijdens het maken van de server.
    2. Klik op **opties >>** en selecteer het tabblad **verbindings eigenschappen** . Zorg ervoor dat u de **ContosoHR** -data base selecteert (niet de standaard-data base). 
    3. Selecteer het tabblad **Always encrypted** .
    4. Zorg ervoor dat het selectie vakje **Always encrypted inschakelen (kolom versleuteling)** **niet** is ingeschakeld.

        ![Verbinding maken zonder Always Encrypted](media/always-encrypted-enclaves/connect-without-always-encrypted-ssms.png)

    5. Klik op **Verbinden**.

2. Maak een nieuwe tabel met de naam **werk nemers**.

    ```sql
    CREATE SCHEMA [HR];
    GO

    CREATE TABLE [HR].[Employees]
    (
        [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
        [SSN] [char](11) NOT NULL,
        [FirstName] [nvarchar](50) NOT NULL,
        [LastName] [nvarchar](50) NOT NULL,
        [Salary] [money] NOT NULL
    ) ON [PRIMARY];
    GO
    ```

3. Voeg een paar werknemers records toe aan de tabel **Employees** .

    ```sql
    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('795-73-9838'
            , N'Catherine'
            , N'Abel'
            , $31692);

    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('990-00-6818'
            , N'Kim'
            , N'Abercrombie'
            , $55415);
    ```


## <a name="step-4-provision-enclave-enabled-keys"></a>Stap 4: enclave-sleutels inrichten

In deze stap maakt u een kolom hoofd sleutel en een kolom versleutelings sleutel waarmee enclave berekeningen worden toegestaan.

1. Gebruik het SSMS-exemplaar uit de vorige stap in **objectverkenner**, vouw de data base uit en navigeer naar **beveiligings**  >  **Always encrypted sleutels**.
1. Een nieuwe enclave-hoofd sleutel voor kolommen inrichten:
    1. Klik met de rechter muisknop op **Always encrypted Keys** en selecteer **nieuwe kolom hoofd sleutel...**.
    2. Selecteer de naam van de hoofd sleutel van de kolom: **CMK1**.
    3. Zorg ervoor dat u **Windows-certificaat archief (huidige gebruiker of lokale computer)** of **Azure Key Vault** selecteert.
    4. Selecteer **enclave-berekeningen toestaan**.
    5. Als u Azure Key Vault hebt geselecteerd, meldt u zich aan bij Azure en selecteert u de sleutel kluis. Zie [uw sleutel kluizen beheren via Azure Portal](/archive/blogs/kv/manage-your-key-vaults-from-new-azure-portal)voor meer informatie over het maken van een sleutel kluis voor always encrypted.
    6. Selecteer uw certificaat of sleutel waarde Azure key als dit al bestaat, of klik op de knop **certificaat genereren** om een nieuwe te maken.
    7. Selecteer **OK**.

        ![Enclave-berekeningen toestaan](media/always-encrypted-enclaves/allow-enclave-computations.png)

1. Maak een nieuwe enclave-kolom versleutelings sleutel:

    1. Klik met de rechter muisknop op **Always encrypted Keys** en selecteer **nieuwe kolom versleutelings sleutel**.
    2. Voer een naam in voor de nieuwe kolom versleutelings sleutel: **CEK1**.
    3. Selecteer in de vervolg keuzelijst **kolom hoofd sleutel** de kolom hoofd sleutel die u in de vorige stappen hebt gemaakt.
    4. Selecteer **OK**.

## <a name="step-5-encrypt-some-columns-in-place"></a>Stap 5: enkele kolommen op locatie versleutelen

In deze stap versleutelt u de gegevens die zijn opgeslagen in de kolommen **SSN** en **Salary** in het enclave aan de server zijde en test u vervolgens een selectie query op de gegevens.

1. Open een nieuw exemplaar van SSMS en maak verbinding met uw data base **met** always encrypted ingeschakeld voor de database verbinding.
    1. Start een nieuw exemplaar van SSMS.
    2. Geef in het dialoog venster **verbinding maken met server** de volledig gekwalificeerde naam op van uw server (bijvoorbeeld *myserver123.database.Windows.net*) en voer de gebruikers naam van de beheerder en het wacht woord in die u hebt opgegeven tijdens het maken van de server.
    3. Klik op **opties >>** en selecteer het tabblad **verbindings eigenschappen** . Zorg ervoor dat u de **ContosoHR** -data base selecteert (niet de standaard-data base). 
    4. Selecteer het tabblad **Always encrypted** .
    5. Zorg ervoor dat het selectie vakje **Always encrypted inschakelen (kolom versleuteling)** is geselecteerd.
    6. Geef uw enclave Attestation-URL op die u hebt verkregen door de stappen in [stap 2: een Attestation-provider configureren](#step-2-configure-an-attestation-provider)te volgen. Zie de onderstaande scherm afbeelding.

        ![Verbinding maken met Attestation](media/always-encrypted-enclaves/connect-to-server-configure-attestation.png)

    7. Selecteer **Verbinding maken**.
    8. Als u wordt gevraagd om parameterisering in te scha kelen voor Always Encrypted query's, selecteert u **inschakelen**.



1. Open met behulp van hetzelfde SSMS-exemplaar (met Always Encrypted ingeschakeld) een nieuw query venster en versleutel de kolommen **SSN** en **salaris** door de onderstaande instructies uit te voeren.

    ```sql
    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [SSN] [char] (11) COLLATE Latin1_General_BIN2
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [Salary] [money]
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE;
    ```

    > [!NOTE]
    > Let op de PROCEDURE_CACHE instructie ALTER data base SCOPEed CONFIGURATION (leeg) om de cache van het query plan voor de data base in het bovenstaande script te wissen. Nadat u de tabel hebt gewijzigd, moet u de plannen voor alle batches en opgeslagen procedures wissen die toegang hebben tot de tabel om de versleutelings gegevens van de para meters te vernieuwen. 

1. Als u wilt controleren of de kolommen **SSN** en **Salary** nu zijn versleuteld, opent u een nieuw query venster in het SSMS-exemplaar **zonder** always encrypted ingeschakeld voor de database verbinding en voert u de onderstaande instructie uit. In het query venster moeten versleutelde waarden in de kolommen **SSN** en **Salary** worden geretourneerd. Als u dezelfde query uitvoert met behulp van het exemplaar SSMS met Always Encrypted ingeschakeld, ziet u dat de gegevens moeten worden ontsleuteld.

    ```sql
    SELECT * FROM [HR].[Employees];
    ```

## <a name="step-6-run-rich-queries-against-encrypted-columns"></a>Stap 6: uitgebreide query's uitvoeren op versleutelde kolommen

U kunt uitgebreide query's uitvoeren op de versleutelde kolommen. Er wordt een verwerking van query's uitgevoerd binnen uw enclave aan de server zijde. 

1. Zorg ervoor dat in het SSMS-exemplaar **met** always encrypted ingeschakeld dat parameterisering ook voor always encrypted is ingeschakeld.
    1. Selecteer **extra** in het hoofd menu van SSMS.
    2. **Opties selecteren...**.
    3. Navigeer naar **uitvoering van query's**  >  **SQL Server**  >  **Geavanceerd**.
    4. Zorg ervoor dat **parameterisering inschakelen voor always encrypted** is ingeschakeld.
    5. Selecteer **OK**.
2. Open een nieuw query venster, plak de onderstaande query en voer deze uit. De query moet waarden voor ongecodeerde tekst en rijen retour neren die voldoen aan de opgegeven zoek criteria.

    ```sql
    DECLARE @SSNPattern [char](11) = '%6818';
    DECLARE @MinSalary [money] = $1000;
    SELECT * FROM [HR].[Employees]
    WHERE SSN LIKE @SSNPattern AND [Salary] >= @MinSalary;
    ```

3. Probeer dezelfde query opnieuw in het SSMS-exemplaar waarvoor geen Always Encrypted is ingeschakeld. Er treedt een fout op.
 
## <a name="next-steps"></a>Volgende stappen

Nadat u deze zelf studie hebt voltooid, kunt u naar een van de volgende zelf studies gaan:
- [Zelf studie: een .NET-toepassing ontwikkelen met behulp van Always Encrypted met beveiligde enclaves](/sql/connect/ado-net/sql/tutorial-always-encrypted-enclaves-develop-net-apps)
- [Zelf studie: een .NET Framework-toepassing ontwikkelen met behulp van Always Encrypted met beveiligde enclaves](/sql/relational-databases/security/tutorial-always-encrypted-enclaves-develop-net-framework-apps)
- [Zelf studie: indexen maken en gebruiken voor met enclave ingeschakelde kolommen met wille keurige versleuteling](/sql/relational-databases/security/tutorial-creating-using-indexes-on-enclave-enabled-columns-using-randomized-encryption)

## <a name="see-also"></a>Zie ook

- [Always Encrypted configureren en gebruiken met beveiligde enclaves](/sql/relational-databases/security/encryption/configure-always-encrypted-enclaves)
