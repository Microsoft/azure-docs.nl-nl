---
title: Azure Attestation configureren voor uw logische Azure SQL-Server
description: Azure Attestation configureren voor Always Encrypted met beveiligde enclaves in Azure SQL Database.
keywords: versleutelen van gegevens, SQL-versleuteling, database versleuteling, gevoelige gegevens, Always Encrypted, beveiligde enclaves, SGX, Attestation
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: 51431bf0da9145e1b61da708942b675e4c3eea78
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733812"
---
# <a name="configure-azure-attestation-for-your-azure-sql-logical-server"></a>Azure Attestation configureren voor uw logische Azure SQL-Server

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted met beveiligde enclaves voor Azure SQL Database is momenteel beschikbaar als **open bare preview**.

[Microsoft Azure Attestation](../../attestation/overview.md) is een oplossing voor het TEEs (Trusted Execution Environment), met inbegrip van Intel-software Guard Extensions (Intel SGX) enclaves. 

Als u Azure-Attestation wilt gebruiken voor de verificatie van Intel SGX-enclaves die wordt gebruikt voor [Always encrypted met Secure enclaves](/sql/relational-databases/security/encryption/always-encrypted-enclaves) in Azure SQL database, moet u het volgende doen:

1. Maak een [Attestation-provider](../../attestation/basic-concepts.md#attestation-provider) en configureer deze met het aanbevolen Attestation-beleid.

2. Verleen uw logische Azure SQL-Server toegang tot uw Attestation-provider.

> [!NOTE]
> Het configureren van Attestation is de verantwoordelijkheid van de Attestation-beheerder. Zie [rollen en verantwoordelijkheden bij het configureren van SGX-enclaves en-Attestation](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation).

## <a name="requirements"></a>Vereisten

De logische Azure SQL-Server en de Attestation-provider moeten deel uitmaken van dezelfde Azure Active Directory Tenant. Cross-Tenant interacties worden niet ondersteund. 

Er moet een Azure AD-identiteit worden toegewezen aan de logische Azure SQL-Server. Als Attestation-beheerder moet u de Azure AD-identiteit van de server verkrijgen van de Azure SQL Database beheerder voor die server. U gaat de identiteit gebruiken om de server toegang te verlenen tot de Attestation-provider. 

Zie [een Azure AD-identiteit toewijzen aan uw server](transparent-data-encryption-byok-configure.md#assign-an-azure-active-directory-azure-ad-identity-to-your-server)voor instructies over het maken van een server met een identiteit of het toewijzen van een identiteit aan een bestaande server met behulp van Power shell en Azure cli.

## <a name="create-and-configure-an-attestation-provider"></a>Een Attestation-provider maken en configureren

Een [Attestation-provider](../../attestation/basic-concepts.md#attestation-provider) is een bron in azure Attestation waarbij [Attestation-aanvragen](../../attestation/basic-concepts.md#attestation-request) worden geëvalueerd tegen [attest beleid](../../attestation/basic-concepts.md#attestation-request) en [Attestation-tokens](../../attestation/basic-concepts.md#attestation-token). 

Attestation-beleid wordt opgegeven met behulp van de grammatica van de [claim regel](../../attestation/claim-rule-grammar.md).

Micro soft raadt het volgende beleid aan voor de attesting van Intel SGX enclaves die wordt gebruikt voor Always Encrypted in Azure SQL Database:

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

Met het bovenstaande beleid wordt gecontroleerd:

- De enclave in Azure SQL Database biedt geen ondersteuning voor fout opsporing (waardoor het beveiligings niveau van de enclave wordt verminderd).
- De product-ID van de bibliotheek in de enclave is de product-ID die is toegewezen aan Always Encrypted met Secure enclaves (4639).
- De versie-ID (SVN) van de bibliotheek is groter dan 0.
- De bibliotheek in het enclave is ondertekend met behulp van de micro soft-handtekening sleutel (de waarde van de x-MS-SGX-mrsigner-claim is de hash van de ondertekeningssleutel).

> [!IMPORTANT]
> Er wordt een Attestation-provider gemaakt met het standaard beleid voor Intel SGX enclaves, waarmee de code die niet binnen de enclave wordt uitgevoerd, niet wordt gevalideerd. Micro soft adviseert u ten zeerste het aanbevolen beleid in te stellen en het standaard beleid niet te gebruiken voor Always Encrypted met beveiligde enclaves.

Voor instructies voor het maken van een Attestation-provider en het configureren van een Attestation-beleid met behulp van:

- [Snelstartgids: Azure Attestation met Azure Portal instellen](../../attestation/quickstart-portal.md)
    > [!IMPORTANT]
    > Wanneer u uw Attestation-beleid configureert met Azure Portal, stelt u Attestation-type in op `SGX-IntelSDK` .
- [Quickstart: Azure Attestation instellen met behulp van Microsoft Azure PowerShell](../../attestation/quickstart-powershell.md)
    > [!IMPORTANT]
    > Wanneer u uw Attestation-beleid configureert met Azure PowerShell, stelt u de `Tee` para meter in op `SgxEnclave` .
- [Quickstart: Azure Attestation instellen met Azure CLI](../../attestation/quickstart-azure-cli.md)
    > [!IMPORTANT]
    > Wanneer u uw Attestation-beleid configureert met Azure CLI, stelt u de `attestation-type` para meter in op `SGX-IntelSDK` .

## <a name="determine-the-attestation-url-for-your-attestation-policy"></a>De Attestation-URL voor het Attestation-beleid bepalen

Nadat u een Attestation-beleid hebt geconfigureerd, moet u de URL van de Attestation delen, die verwijst naar het beleid, Administrators van toepassingen die gebruikmaken van Always Encrypted met beveiligde enclaves in Azure SQL Database. Toepassings beheerders of/en toepassings gebruikers moeten hun apps configureren met de Attestation-URL, zodat ze instructies kunnen uitvoeren die gebruikmaken van beveiligde enclaves.

### <a name="use-powershell-to-determine-the-attestation-url"></a>Power shell gebruiken om de Attestation-URL te bepalen

Gebruik het volgende script om uw Attestation-URL te bepalen:

```powershell
$attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName 
$attestationUrl = $attestationProvider.AttestUri + “/attest/SgxEnclave”
Write-Host "Your attestation URL is: " $attestationUrl 
```

### <a name="use-azure-portal-to-determine-the-attestation-url"></a>Azure Portal gebruiken om de Attestation-URL te bepalen

1. Kopieer in het deel venster Overzicht voor uw Attestation-provider de waarde van de eigenschap Attestation URI naar het klem bord. Een Attestation-URI moet er als volgt uitzien: `https://MyAttestationProvider.us.attest.azure.net` .

2. Voeg het volgende toe aan de Attestation-URI: `/attest/SgxEnclave` . 

De resulterende Attestation-URL moet er als volgt uitzien: `https://MyAttestationProvider.us.attest.azure.net/attest/SgxEnclave`

## <a name="grant-your-azure-sql-logical-server-access-to-your-attestation-provider"></a>De logische Azure SQL-Server toegang verlenen tot uw Attestation-provider

Tijdens de Attestation-werk stroom roept de logische Azure SQL-Server met uw data base de Attestation-provider aan om een Attestation-aanvraag in te dienen. Voor de logische Azure SQL-Server om Attestation-aanvragen te kunnen indienen, moet de server een machtiging hebben voor de `Microsoft.Attestation/attestationProviders/attestation/read` actie op de Attestation-provider. De aanbevolen manier om de machtiging te verlenen is de beheerder van de Attestation-provider om de Azure AD-identiteit van de server toe te wijzen aan de rol van de Attestation-provider of de bijbehorende resource groep.

### <a name="use-azure-portal-to-assign-permission"></a>Azure Portal gebruiken om machtigingen toe te wijzen

Als u de identiteit van een Azure SQL-Server wilt toewijzen aan de rol van de Attestation-lezer voor een Attestation-provider, volgt u de algemene instructies in [Azure-roltoewijzingen toevoegen of verwijderen met behulp van de Azure Portal](../../role-based-access-control/role-assignments-portal.md). Als u zich in het deel venster **toewijzing van rol toevoegen** bevindt:

1. Selecteer in de vervolg keuzelijst **rol** de rol **Attestation-lezer** .
1. Voer in het veld **selecteren** de naam van uw Azure SQL-Server in om ernaar te zoeken.

Zie de onderstaande scherm afbeelding voor een voor beeld.

![roltoewijzing van Attestation-lezer](./media/always-encrypted-enclaves/attestation-provider-role-assigment.png)

> [!NOTE]
> Een server kan alleen worden weer gegeven in het deel venster **toewijzing van rol toevoegen** als aan de server een Azure AD-identiteit is toegewezen. Zie [vereisten](#requirements).

### <a name="use-powershell-to-assign-permission"></a>Power shell gebruiken om machtigingen toe te wijzen

1. Zoek uw logische Azure SQL-Server.

```powershell
$serverResourceGroupName = "<server resource group name>"
$serverName = "<server name>" 
$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName
```
 
2. Wijs de server toe aan de rol van de Attestation-lezer voor de resource groep met uw Attestation-provider.

```powershell
$attestationResourceGroupName = "<attestation provider resource group name>"
New-AzRoleAssignment -ObjectId $server.Identity.PrincipalId -RoleDefinitionName "Attestation Reader" -ResourceGroupName $attestationResourceGroupName
```

Zie [Azure-roltoewijzingen toevoegen of verwijderen met Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md#add-role-assignment-examples)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Manage keys for Always Encrypted with secure enclaves](/sql/relational-databases/security/encryption/always-encrypted-enclaves-manage-keys) (Sleutels beheren voor Always Encrypted met beveiligde enclaves)

## <a name="see-also"></a>Zie ook

- [Zelf studie: aan de slag met Always Encrypted met beveiligde enclaves in Azure SQL Database](always-encrypted-enclaves-getting-started.md)
