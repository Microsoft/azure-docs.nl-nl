---
title: Verificatiemechanismen met de COPY-instructie
description: Geeft een overzicht van de verificatiemechanismen voor het bulksgewijs laden van gegevens
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 07/10/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 25c692ea9a2dce4723472f6812ac46d82b2b318d
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98120985"
---
# <a name="securely-load-data-using-synapse-sql"></a>Gegevens veilig laden met Synapse SQL

In dit artikel vindt u voorbeelden over de veilige verificatiemechanismen voor de [COPY-instructie](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). De instructie COPY is de meest flexibele en veilige manier om gegevens bulksgewijs te laden in Synapse SQL.
## <a name="supported-authentication-mechanisms"></a>Ondersteunde verificatiemechanismen

De volgende matrix beschrijft de ondersteunde verificatiemethoden voor elk bestandstype en opslagaccount. Dit geldt voor de bronopslaglocatie en de locatie van het foutbestand.

|                          |                CSV                |                      Parquet                       |                        ORC                         |
| :----------------------: | :-------------------------------: | :------------------------------------------------: | :------------------------------------------------: |
|  **Azure Blob Storage**  | SAS/MSI/SERVICE PRINCIPAL/KEY/AAD |                      SAS/KEY                       |                      SAS/KEY                       |
| **Azure Data Lake Gen2** | SAS/MSI/SERVICE PRINCIPAL/KEY/AAD | SAS (blob<sup>1</sup>)/MSI (dfs<sup>2</sup>)/SERVICE PRINCIPAL/KEY/AAD | SAS (blob<sup>1</sup>)/MSI (dfs<sup>2</sup>)/SERVICE PRINCIPAL/KEY/AAD |

1: Het. blob-eindpunt ( **.blob**.core.windows.net) in het pad van uw externe locatie is vereist voor deze verificatiemethode.

2: Het .dfs-eindpunt ( **.dfs**.core.windows.net) in het pad van uw externe locatie is vereist voor deze verificatiemethode.

## <a name="a-storage-account-key-with-lf-as-the-row-terminator-unix-style-new-line"></a>A. Opslagaccountsleutel met LF als het rij-eindpunt (nieuwe regel voor UNIX-stijl)


```sql
--Note when specifying the column list, input field numbers start from 1
COPY INTO target_table (Col_one default 'myStringDefault' 1, Col_two default 1 3)
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='<Your_Account_Key>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='x6RWv4It5F2msnjelv3H4DA80n0QW0daPdw43jM0nyetx4c6CpDkdj3986DX5AHFMIf/YN4y6kkCnU8lb+Wx0Pj+6MDw=='),
    ,ROWTERMINATOR='0x0A' --0x0A specifies to use the Line Feed character (Unix based systems)
)
```
> [!IMPORTANT]
>
> - Gebruik de hexadecimale waarde (0x0A) om de lijnfeed/het teken voor nieuwe regel op te geven. Houd er rekening mee dat de instructie COPY de tekenreeks '\n' interpreteert als '\r\n' (regelterugloop nieuwe regel).

## <a name="b-shared-access-signatures-sas-with-crlf-as-the-row-terminator-windows-style-new-line"></a>B. Shared Access Signatures (SAS) met CRLF als het rij-eindpunt (nieuwe regel Windows-stijl)
```sql
COPY INTO target_table
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<Your_SAS_Token>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='?sv=2018-03-28&ss=bfqt&srt=sco&sp=rl&st=2016-10-17T20%3A14%3A55Z&se=2021-10-18T20%3A19%3A00Z&sig=IEoOdmeYnE9%2FKiJDSFSYsz4AkNa%2F%2BTx61FuQ%2FfKHefqoBE%3D'),
    ,ROWTERMINATOR='\n'-- COPY command automatically prefixes the \r character when \n (newline) is specified. This results in carriage return newline (\r\n) for Windows based systems.
)
```

> [!IMPORTANT]
>
> - Geef de ROWTERMINATOR niet op als '\r\n'. Die wordt geïnterpreteerd als '\r\r\n' en kan leiden tot het parseerproblemen

## <a name="c-managed-identity"></a>C. Beheerde identiteit

Beheerde identiteitsverificatie is vereist wanneer uw opslagaccount is gekoppeld aan een VNet. 

### <a name="prerequisites"></a>Vereisten

1. Installeer Azure PowerShell met behulp van deze [gids](/powershell/azure/install-az-ps?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Als u een v1-of blob-opslagaccount voor algemeen gebruik hebt, moet u eerst een upgrade uitvoeren naar de v2 voor algemeen gebruik met behulp van deze [gids](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
3. U moet **vertrouwde Microsoft-services toegang geven tot dit opslagaccount** ingeschakeld onder het instellingenmenu van uw Azure Storage-account **Firewalls en virtuele netwerken**. Raadpleeg deze [gids](../../storage/common/storage-network-security.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#exceptions) voor meer informatie.

#### <a name="steps"></a>Stappen

1. Als u een zelfstandige toegewezen SQL-groep hebt, registreert u uw SQL-server met Azure Active Directory (AAD) met behulp van PowerShell: 

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Deze stap is niet nodig voor toegewezen SQL-groepen in een Synapse-werkruimte.

1. Als u een Synapse-werkruimte hebt, registreert u de door het systeem beheerde identiteit van uw werkruimte:

   1. Ga naar uw Synapse-werkruimte in Azure Portal
   2. Ga naar de blade Beheerde identiteiten 
   3. Zorg ervoor dat de optie 'Pijplijnen toestaan' is ingeschakeld
   
   ![Systeem-msi van werkruimte registreren](./media/quickstart-bulk-load-copy-tsql-examples/msi-register-example.png)

1. **Een opslagaccount voor algemeen gebruik v2 maken** met deze [gids](../../storage/common/storage-account-create.md).

   > [!NOTE]
   >
   > - Als u een v1-of blob-opslagaccount voor algemeen gebruik hebt, moet u **eerst een upgrade uitvoeren naar de v2** voor algemeen gebruik met behulp van deze [gids](../../storage/common/storage-account-upgrade.md).
   > - Raadpleeg deze [gids](../../storage/blobs/data-lake-storage-known-issues.md) voor bekende problemen met Azure Data Lake Storage Gen2.

1. Navigeer onder uw opslagaccount naar **Access Control (IAM)** en selecteer **Roltoewijzing toevoegen**. Wijs de Azure-rol **Inzender voor opslagblob-gegevens** toe aan de server of werkruimte die als host fungeert voor de toegewezen SQL-groep die u hebt geregistreerd bij Azure Active Directory (AAD).

   > [!NOTE]
   > Alleen leden met de bevoegdheid Eigenaar kunnen deze stap uitvoeren. Raadpleeg deze [gids](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) voor verschillende ingebouwde Azure-rollen.
   
    > [!IMPORTANT]
    > Geef de Azure-rol Eigenaar, Inzender of Lezer van **Storage** **Blob-gegevens** op. Deze rollen verschillen van de ingebouwde Azure-rollen Eigenaar, Inzender en Lezer. 

    ![Azure RBAC-machtiging verlenen om te laden](./media/quickstart-bulk-load-copy-tsql-examples/rbac-load-permissions.png)

4. U kunt nu de instructie COPY uitvoeren om een "Beheerde identiteit" op te geven:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV',
        CREDENTIAL = (IDENTITY = 'Managed Identity'),
    )
    ```

## <a name="d-azure-active-directory-authentication"></a>D. Azure Active Directory-verificatie
#### <a name="steps"></a>Stappen

1. Navigeer onder uw opslagaccount naar **Access Control (IAM)** en selecteer **Roltoewijzing toevoegen**. Wijs de Azure-rol **Eigenaar, Inzender of Lezer van Storage Blob-gegevens** toe aan uw Azure AD-gebruiker. 

    > [!IMPORTANT]
    > Geef de Azure-rol Eigenaar, Inzender of Lezer van **Storage** **Blob-gegevens** op. Deze rollen verschillen van de ingebouwde Azure-rollen Eigenaar, Inzender en Lezer.

    ![Azure RBAC-machtiging verlenen om te laden](./media/quickstart-bulk-load-copy-tsql-examples/rbac-load-permissions.png)

2. Configureer Azure AD-verificatie door middel van de volgende [documentatie](../../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell). 

3. Maak verbinding met uw SQL-groep met Active Directory waarbij u nu de instructie COPY kunt uitvoeren zonder referenties op te geven:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
    )
    ```


## <a name="e-service-principal-authentication"></a>E. Verificatie van service-principal
#### <a name="steps"></a>Stappen

1. [Een Azure Active Directory-toepassing maken](../..//active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. [Toepassings-id ophalen](../..//active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)
3. [De verificatiesleutel ophalen](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)
4. [Het token-eindpunt V1 OAuth 2.0 ophalen](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications)
5. [Lees-, schrijf- en uitvoeringsmachtigingen toewijzen aan uw Azure AD-toepassing](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder) in uw opslagaccount
6. U kunt nu de instructie COPY uitvoeren:

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder0/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
        ,CREDENTIAL=(IDENTITY= '<application_ID>@<OAuth_2.0_Token_EndPoint>' , SECRET= '<authentication_key>')
        --CREDENTIAL should look something like this:
        --,CREDENTIAL=(IDENTITY= '92761aac-12a9-4ec3-89b8-7149aef4c35b@https://login.microsoftonline.com/72f714bf-86f1-41af-91ab-2d7cd011db47/oauth2/token', SECRET='juXi12sZ6gse]woKQNgqwSywYv]7A.M')
    )
    ```

> [!IMPORTANT]
>
> - Gebruik de **v1-** versie van het OAuth 2.0-token-eindpunt

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg het [artikel over COPY-instructie](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax) voor de gedetailleerde syntaxis
- Controleer het overzichtsartikel [gegevens laden](./design-elt-data-loading.md#what-is-elt) voor het laden van aanbevolen procedures
