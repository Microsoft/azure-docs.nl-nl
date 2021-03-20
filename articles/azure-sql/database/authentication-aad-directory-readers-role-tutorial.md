---
title: De rol van Directory Readers toewijzen aan een Azure AD-groep en roltoewijzingen beheren
description: Dit artikel helpt u bij het inschakelen van de rol Directory Readers met behulp van Azure AD-groepen om Azure AD-roltoewijzingen te beheren met Azure SQL Database, Azure SQL Managed Instance en Azure Synapse Analytics
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: bc809cf02b827b7498890cb7d929c44bd360ab53
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99094706"
---
# <a name="tutorial-assign-directory-readers-role-to-an-azure-ad-group-and-manage-role-assignments"></a>Zelfstudie: De rol van Directory Readers toewijzen aan een Azure AD-groep en roltoewijzingen beheren

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> De rol **Directory Readers** toewijzen aan een groep in dit artikel is in de **openbare preview**. 

Dit artikel helpt u bij het maken van een groep in Azure Active Directory (Azure AD) en het toewijzen van de rol [**Directory Readers**](../../active-directory/roles/permissions-reference.md#directory-readers) aan die groep. Met de machtiging Directory Readers kunnen de groepseigenaren extra leden toevoegen aan de groep, zoals een [beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) of [Azure SQL Database](sql-database-paas-overview.md), [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) en [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Hierdoor is geen [Globale beheerder](../../active-directory/roles/permissions-reference.md#global-administrator) of [Beheerder met bevoorrechte rol](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) nodig om de rol Directory Readers rechtstreeks toe te wijzen voor elke logische serveridentiteit van Azure SQL in de tenant.

In deze zelfstudie wordt de functie gebruikt die is geïntroduceerd in [Cloudgroepen gebruiken om roltoewijzingen te beheren in Azure Active Directory (preview)](../../active-directory/roles/groups-concept.md). 

Zie [De rol Directory Readers in Azure Active Directory voor Azure SQL](authentication-aad-directory-readers-role.md) voor meer informatie over de voordelen van het toewijzen van de rol Directory Readers aan een Azure AD-groep voor Azure SQL.

## <a name="prerequisites"></a>Vereisten

- Een Azure AD-exemplaar. Zie [Azure AD-verificatie configureren en beheren met Azure SQL](authentication-aad-configure.md) voor meer informatie.
- Een SQL Database, SQL Managed Instance of Azure Synapse.

## <a name="directory-readers-role-assignment-using-the-azure-portal"></a>Toewijzing van rol Directory Readers met behulp van de Azure-portal

### <a name="create-a-new-group-and-assign-owners-and-role"></a>Een nieuwe groep maken en eigenaren en rollen toewijzen

1. Voor deze eerste installatie is een gebruiker vereist met de machtiging [Globale beheerder](../../active-directory/roles/permissions-reference.md#global-administrator) of [Beheerder met bevoorrechte rol](../../active-directory/roles/permissions-reference.md#privileged-role-administrator).
1. Laat de gemachtigde gebruiker zich aanmelden bij de [Azure-portal](https://portal.azure.com).
1. Ga naar de **Azure Active Directory**-resource. Ga onder **Beheerd** naar **Groepen**. Selecteer **Nieuwe groep** om een nieuwe groep te maken.
1. Selecteer **Beveiliging** als groepstype en vul de rest van de velden in. Zorg ervoor dat de instelling **Azure AD-rollen kunnen worden toegewezen aan de groep (preview)** is ingesteld op **Ja**. Wijs vervolgens de Azure AD-rol **Directory Readers** toe aan de groep.
1. Wijs aan de zojuist gemaakte groep Azure AD-gebruikers toe als eigenaar/eigenaren. Een groepseigenaar kan een standaard-AD-gebruiker zijn zonder een toegewezen Azure AD-beheerdersrol. De eigenaar moet een gebruiker zijn die uw SQL Database, SQL Managed Instance of Azure Synapse beheert.

   :::image type="content" source="media/authentication-aad-directory-readers-role/new-group.png" alt-text="aad-new-group":::

1. Selecteer **Maken**

### <a name="checking-the-group-that-was-created"></a>Controleren of de groep is gemaakt

> [!NOTE]
> Zorg ervoor dat het **Groepstype**, **Beveiliging** is. *Microsoft 365*-groepen worden niet ondersteund voor Azure SQL.

Ga terug naar het deelvenster **Groepen** in de Azure-portal en zoek naar uw groepsnaam om de groep die is gemaakt te controleren en te beheren. Extra eigenaren en leden kunnen worden toegevoegd onder het menu **Eigenaren** en **Leden** van de instelling **Beheren** nadat de groep is geselecteerd. U kunt ook de **Toegewezen rollen** bekijken voor de groep.

:::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-group-created.png" alt-text="Schermafbeelding van een Groepsvenster waarop de koppelingen om het menu Instellingen te openen voor Leden, Eigenaars en Toegewezen rollen (preview) gemarkeerd zijn.":::

### <a name="add-azure-sql-managed-identity-to-the-group"></a>Azure SQL-beheerde identiteit aan de groep toevoegen

> [!NOTE]
> In dit voorbeeld wordt gebruikgemaakt van SQL Managed Instance, maar soortgelijke stappen kunnen worden toegepast voor SQL Database of Azure Synapse om dezelfde resultaten te behalen.

Voor de volgende stappen is de gebruiker met de machtiging Globale beheerder of Beheerder met bevoorrechte rol niet meer nodig.

1. Meld u aan bij de Azure-portal als de gebruiker die SQL Managed Instance beheert en een eigenaar is van de groep die eerder is gemaakt.

1. Zoek de naam van de resource van uw **SQL-beheerd exemplaar** in de Azure-portal.

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance.png" alt-text="Schermafbeelding van het scherm met de beheerde SQL-exemplaren waarop de naam van het SQL-exemplaar ssomitest en de Subnetnaam ManagedInstance gemarkeerd zijn.":::

   Tijdens het maken van uw SQL Managed Instance werd er voor uw exemplaar een Azure-identiteit gemaakt. De gemaakte identiteit heeft dezelfde naam als het voorvoegsel van de naam van uw SQL Managed Instance. U kunt de service-principal vinden voor uw SQL Managed Instance-identiteit die is gemaakt als een Azure AD-toepassing door de volgende stappen uit te voeren:

    - Ga naar de **Azure Active Directory**-resource. Selecteer **Enterprise-toepassingen** onder de instelling **Beheren**. De **Object-ID** is de identiteit van het exemplaar.
    
    :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance-service-principal.png" alt-text="Schermafbeelding van de pagina Bedrijfstoepassingen voor een Azure Active Directory-resource waarop de object-ID van het door SQL beheerde exemplaar gemarkeerd is.":::

1. Ga naar de **Azure Active Directory**-resource. Ga onder **Beheerd** naar **Groepen**. Selecteer de groep die u hebt gemaakt. Selecteer **Leden** onder de instelling **Beheerd** van uw groep. Selecteer **Leden toevoegen** en voeg uw SQL Managed Instance-service-principal toe als lid van de groep door te zoeken naar de naam die hierboven is gevonden.

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-add-managed-instance-service-principal.png" alt-text="Schermafbeelding van de pagina Leden voor een Azure Active Directory-resource waarop de opties zijn gemarkeerd om een door SQL beheerd exemplaar toe te voegen als een nieuw lid.":::

> [!NOTE]
> Het kan een paar minuten duren om de machtigingen van de service-principal door te geven door het Azure-systeem en toegang te verlenen aan Azure AD Graph API. U moet mogelijk een aantal minuten wachten voordat u een Azure AD-beheerder kunt inrichten voor SQL Managed Instance.

### <a name="remarks"></a>Opmerkingen

De serveridentiteit kan, voor SQL Database en Azure Synapse, worden gemaakt tijdens het maken van de Azure SQL-logische server of nadat de server is gemaakt. Zie [Service-principals inschakelen om Azure AD-gebruikers te maken](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users) voor meer informatie over hoe u de serveridentiteit in SQL Database of Azure Synapse maakt of instelt.

Voor SQL Managed Instance moet de rol **Directory Readers** zijn toegewezen aan de beheerd-exemplaaridentiteit, voordat u [een Azure AD-beheerder voor het beheerd exemplaar kunt instellen](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

Het is niet vereist de rol **Directory Readers** toe te wijzen aan de serveridentiteit voor SQL Database of Azure Synapse bij het instellen van een Azure AD-beheerder voor de logische server. De rol **Directory Readers** is echter wel vereist om een Azure AD-object te kunnen maken in SQL Database of Azure Synapse namens een Azure AD-toepassing. Als de rol niet is toegewezen aan de SQL-logische-serveridentiteit zal het niet lukken Azure AD-gebruikers te maken in Azure SQL. Raadpleeg [Azure Active Directory-service-principal met Azure SQL](authentication-aad-service-principal.md) voor meer informatie.

## <a name="directory-readers-role-assignment-using-powershell"></a>Toewijzing van rol Directory Readers met behulp van PowerShell

> [!IMPORTANT]
> Voor deze eerste stappen is een [Globale beheerder](../../active-directory/roles/permissions-reference.md#global-administrator) of [Beheerder met bevoorrechte rol](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) nodig. Naast PowerShell biedt Azure AD Microsoft Graph API om [Een groep te maken waaraan rollen kunnen worden toegewezen in Azure AD](../../active-directory/roles/groups-create-eligible.md#using-microsoft-graph-api).

1. Download de Azure AD Preview PowerShell-module met de volgende opdrachten. U moet PowerShell mogelijk uitvoeren als beheerder.

    ```powershell
    Install-Module azureadpreview
    Import-Module azureadpreview
    #To verify that the module is ready to use, use the following command:
    Get-Module azureadpreview
    ```

1. Maak verbinding met uw Azure-tenant.

    ```powershell
    Connect-AzureAD
    ```

1. Maak een beveiligingsgroep om de rol **Directory Readers** toe te wijzen.

    - `DirectoryReaderGroup`, `Directory Reader Group` en `DirRead` kunnen worden gewijzigd op basis van uw voorkeur.

    ```powershell
    $group = New-AzureADMSGroup -DisplayName "DirectoryReaderGroup" -Description "Directory Reader Group" -MailEnabled $False -SecurityEnabled $true -MailNickName "DirRead" -IsAssignableToRole $true
    $group
    ```

1. Wijs **Directory Readers** toe aan de groep.

    ```powershell
    # Displays the Directory Readers role information
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Directory Readers'" 
    $roleDefinition
    ```

    ```powershell
    # Assigns the Directory Readers role to the group
    $roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id
    $roleAssignment
    ```

1. Wijs eigenaren toe aan de groep.

    - Vervang `<username>` met de gebruiker die u eigenaar van deze groep wilt maken. Verschillende eigenaren kunnen worden toegevoegd door deze stappen te herhalen.

    ```powershell
    $RefObjectID = Get-AzureADUser -ObjectId "<username>"
    $RefObjectID
    ```

    ```powershell
    $GrOwner = Add-AzureADGroupOwner -ObjectId $group.ID -RefObjectId $RefObjectID.ObjectID
    ```

    Controleer eigenaren van de groep met de volgende opdracht:

    ```powershell
    Get-AzureADGroupOwner -ObjectId $group.ID
    ```

    U kunt ook de eigenaren van de groep verifiëren in de [Azure-portal](https://portal.azure.com). Volg de stappen in [Controleren of de groep is gemaakt](#checking-the-group-that-was-created).

### <a name="assigning-the-service-principal-as-a-member-of-the-group"></a>De service-principal toewijzen als lid van de groep

Voor de volgende stappen is de gebruiker met de machtiging Globale beheerder of Beheerder met bevoorrechte rol niet meer nodig.

1. Gebruik een eigenaar van de groep die ook de Azure SQL-resource beheert en voer de volgende opdracht uit om verbinding te maken met uw Azure AD.

    ```powershell
    Connect-AzureAD
    ```

1. Wijs de service-principal toe als een lid van de gemaakte groep.

    - Vervang `<ServerName>` met de naam van uw Azure SQL-logische server of de naam van uw Beheerd exemplaar. Zie het gedeelte [Azure SQL-service-identiteit toevoegen aan de groep](#add-azure-sql-managed-identity-to-the-group) voor meer informatie

    ```powershell
    # Returns the service principal of your Azure SQL resource
    $miIdentity = Get-AzureADServicePrincipal -SearchString "<ServerName>"
    $miIdentity
    ```

    ```powershell
    # Adds the service principal to the group as a member
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId 
    ```

    De volgende opdracht retourneert de Object-ID van de service-principal, waarmee wordt aangegeven dat deze is toegevoegd aan de groep:

    ```powershell
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId
    ```

## <a name="next-steps"></a>Volgende stappen

- [Rol Directory Readers in Azure Active Directory voor Azure SQL](authentication-aad-directory-readers-role.md)
- [Zelfstudie: Azure AD-gebruikers maken met behulp van Azure AD-toepassingen](authentication-aad-service-principal-tutorial.md)
- [Azure AD-verificatie configureren en beheren met Azure SQL](authentication-aad-configure.md)