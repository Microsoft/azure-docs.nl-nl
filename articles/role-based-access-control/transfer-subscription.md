---
title: Een Azure-abonnement overdragen naar een andere Azure AD-directory
description: Meer informatie over het overdragen van een Azure-abonnement en bekende gerelateerde resources naar een Azure Active Directory directory (Azure AD).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.workload: identity
ms.date: 04/06/2021
ms.author: rolyon
ms.openlocfilehash: 72dc92ae211034e2a49bc77f60880f17ab15dec7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868173"
---
# <a name="transfer-an-azure-subscription-to-a-different-azure-ad-directory"></a>Een Azure-abonnement overdragen naar een andere Azure AD-directory

Organisaties kunnen verschillende Azure-abonnementen hebben. Elk abonnement is gekoppeld aan een Azure Active Directory (Azure AD)-directory. Om het beheer eenvoudiger te maken, kunt u een abonnement overdragen naar een andere Azure AD-directory. Wanneer u een abonnement overdraagt naar een andere Azure AD-directory, worden sommige resources niet overgedragen naar de doeldirectory. Alle roltoewijzingen en aangepaste rollen in op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) worden bijvoorbeeld permanent verwijderd uit **de** bronmap en worden niet overgedragen naar de doeldirectory.

In dit artikel worden de basisstappen beschreven die u kunt volgen om een abonnement over te dragen naar een andere Azure AD-directory en een aantal resources na de overdracht opnieuw te maken.

> [!NOTE]
> Voor Azure CSP-abonnementen (Cloud Solution Providers) wordt het wijzigen van de Azure AD-directory voor het abonnement niet ondersteund.

## <a name="overview"></a>Overzicht

Het overdragen van een Azure-abonnement naar een andere Azure AD-directory is een complex proces dat zorgvuldig moet worden gepland en uitgevoerd. Voor veel Azure-services zijn beveiligingsprincipalen (identiteiten) vereist om normaal te werken of zelfs andere Azure-resources te beheren. In dit artikel wordt geprobeerd de meeste Azure-services te behandelen die sterk afhankelijk zijn van beveiligingsprincipeiten, maar die niet uitgebreid zijn.

> [!IMPORTANT]
> In sommige scenario's kan het overdragen van een abonnement downtime vereisen om het proces te voltooien. Er is een zorgvuldige planning vereist om te beoordelen of downtime vereist is voor uw overdracht.

In het volgende diagram ziet u de basisstappen die u moet volgen wanneer u een abonnement overdraagt naar een andere directory.

1. Voorbereiden op de overdracht

1. Het Azure-abonnement overdragen naar een andere directory

1. Resources opnieuw maken in de doelmap, zoals roltoewijzingen, aangepaste rollen en beheerde identiteiten

    ![Abonnementsdiagram overdragen](./media/transfer-subscription/transfer-subscription.png)

### <a name="deciding-whether-to-transfer-a-subscription-to-a-different-directory"></a>Beslissen of u een abonnement wilt overdragen naar een andere directory

Hier volgen enkele redenen waarom u een abonnement wilt overdragen:

- Vanwege een fusie of overname van een bedrijf wilt u een gekocht abonnement beheren in uw primaire Azure AD-directory.
- Iemand in uw organisatie heeft een abonnement gemaakt en u wilt het beheer consolideren in een bepaalde Azure AD-directory.
- U hebt toepassingen die afhankelijk zijn van een bepaalde abonnements-id of URL en het is niet eenvoudig om de configuratie of code van de toepassing te wijzigen.
- Een deel van uw bedrijf is opgesplitst in een afzonderlijk bedrijf en u moet een aantal van uw resources verplaatsen naar een andere Azure AD-directory.
- U wilt een aantal van uw resources in een andere Azure AD-directory beheren voor beveiligingsisolatiedoeleinden.

### <a name="alternate-approaches"></a>Andere manieren

Voor het overdragen van een abonnement is downtime vereist om het proces te voltooien. Afhankelijk van uw scenario kunt u de volgende alternatieve benaderingen overwegen:

- Maak de resources opnieuw en kopieer gegevens naar de doelmap en het abonnement.
- Gebruik een architectuur met meerdere mappen en laat het abonnement in de bronmap staan. Gebruik Azure Lighthouse om resources te delegeren zodat gebruikers in de doelmap toegang hebben tot het abonnement in de bronmap. Zie voor meer informatie Azure Lighthouse [in bedrijfsscenario's.](../lighthouse/concepts/enterprise.md)

### <a name="understand-the-impact-of-transferring-a-subscription"></a>Inzicht in de impact van het overdragen van een abonnement

Verschillende Azure-resources zijn afhankelijk van een abonnement of map. Afhankelijk van uw situatie bevat de volgende tabel de bekende impact van de overdracht van een abonnement. Door de stappen in dit artikel uit te voeren, kunt u enkele resources die bestonden vóór de abonnementsoverdracht opnieuw maken.

> [!IMPORTANT]
> In deze sectie vindt u de bekende Azure-services of -resources die afhankelijk zijn van uw abonnement. Omdat resourcetypen in Azure voortdurend in ontwikkeling zijn, worden hier mogelijk aanvullende afhankelijkheden niet vermeld die een belangrijke wijziging in uw omgeving kunnen veroorzaken. 

| Service of resource | Beïnvloed | Herstelbare | Is dit van invloed op u? | Wat u kunt doen |
| --------- | --------- | --------- | --------- | --------- |
| Roltoewijzingen | Ja | Ja | [Lijst met roltoewijzingen weergeven](#save-all-role-assignments) | Alle roltoewijzingen worden permanent verwijderd. U moet gebruikers, groepen en service-principals aan de bijbehorende objecten in de doelmap toevoegen. U moet de roltoewijzingen opnieuw maken. |
| Aangepaste rollen | Ja | Ja | [Aangepaste rollen opvragen](#save-custom-roles) | Alle aangepaste rollen worden permanent verwijderd. U moet de aangepaste rollen en eventuele roltoewijzingen opnieuw maken. |
| Door het systeem toegewezen beheerde identiteiten | Ja | Ja | [Beheerde identiteiten op een lijst zetten](#list-role-assignments-for-managed-identities) | U moet de beheerde identiteiten uitschakelen en opnieuw inschakelen. U moet de roltoewijzingen opnieuw maken. |
| Door de gebruiker toegewezen beheerde identiteiten | Ja | Ja | [Beheerde identiteiten op een lijst zetten](#list-role-assignments-for-managed-identities) | U moet de beheerde identiteiten verwijderen, opnieuw maken en koppelen aan de juiste resource. U moet de roltoewijzingen opnieuw maken. |
| Azure Key Vault | Ja | Ja | [Toegangsbeleid Key Vault lijst](#list-key-vaults) | U moet de tenant-id bijwerken die is gekoppeld aan de sleutelkluizen. U moet nieuw toegangsbeleid verwijderen en toevoegen. |
| Azure SQL databases met Azure AD-verificatieintegratie ingeschakeld | Ja | Nee | [Databases Azure SQL met Azure AD-verificatie](#list-azure-sql-databases-with-azure-ad-authentication) | U kunt een Azure SQL database met Azure AD-verificatie ingeschakeld niet overdragen naar een andere directory. Zie Use [Azure Active Directory authentication (Verificatie Azure Active Directory gebruiken) voor meer informatie.](../azure-sql/database/authentication-aad-overview.md) | 
| Azure Storage en Azure Data Lake Storage Gen2 | Ja | Ja |  | U moet alle ACL's opnieuw maken. |
| Azure Data Lake Storage Gen1 | Ja | Ja |  | U moet alle ACL's opnieuw maken. |
| Azure Files | Ja | Ja |  | U moet alle ACL's opnieuw maken. |
| Azure File Sync | Ja | Ja |  | De opslagsynchronisatieservice en/of het opslagaccount kunnen worden verplaatst naar een andere map. Zie Veelgestelde vragen [(FAQ)](../storage/files/storage-files-faq.md#azure-file-sync) over de Azure Files |
| Azure Managed Disks | Ja | Ja |  |  Als u schijfversleutelingssets gebruikt om Managed Disks met door de klant beheerde sleutels te versleutelen, moet u de door het systeem toegewezen identiteiten die zijn gekoppeld aan schijfversleutelingssets uitschakelen en opnieuw inschakelen. En u moet de roltoewijzingen opnieuw maken, dat wil zeggen nogmaals de vereiste machtigingen verlenen aan schijfversleutelingssets in de sleutelkluizen. |
| Azure Kubernetes Service | Ja | Nee |  | U kunt uw AKS-cluster en de bijbehorende resources niet overdragen naar een andere map. Zie Veelgestelde vragen [over AKS (Azure Kubernetes Service) voor meer informatie](../aks/faq.md) |
| Azure Policy | Ja | Nee | Alle Azure Policy, inclusief aangepaste definities, toewijzingen, uitzonderingen en nalevingsgegevens. | U moet [definities](../governance/policy/how-to/export-resources.md)exporteren, importeren en opnieuw toewijzen. Maak vervolgens nieuwe beleidstoewijzingen en eventuele benodigde [beleidsvrijstellingen.](../governance/policy/concepts/exemption-structure.md) |
| Azure Active Directory Domain Services | Ja | Nee |  | U kunt een beheerd Azure AD Domain Services niet overdragen naar een andere directory. Zie Veelgestelde vragen over Azure Active Directory [(AD) Domain Services](../active-directory-domain-services/faqs.md) voor meer informatie |
| App-registraties | Ja | Ja |  |  |

> [!WARNING]
> Als u versleuteling-at-rest gebruikt voor een resource, zoals een opslagaccount of SQL database, die afhankelijk **is** van een sleutelkluis die zich niet in hetzelfde abonnement dat wordt overgedragen, kan dit leiden tot een onherkenbaar scenario. Als u deze situatie hebt, moet u stappen ondernemen om een andere sleutelkluis te gebruiken of door de klant beheerde sleutels tijdelijk uit te schakelen om dit onherkenbare scenario te voorkomen.

## <a name="prerequisites"></a>Vereisten

Als u deze stappen wilt voltooien, hebt u het volgende nodig:

- [Bash in Azure Cloud Shell](../cloud-shell/overview.md) of [Azure CLI](/cli/azure)
- Accountbeheerder van het abonnement dat u wilt overdragen in de bronmap
- [De](built-in-roles.md#owner) rol van eigenaar in de doelmap

## <a name="step-1-prepare-for-the-transfer"></a>Stap 1: voorbereiden op de overdracht

### <a name="sign-in-to-source-directory"></a>Aanmelden bij de bronmap

1. Meld u als beheerder aan bij Azure.

1. Haal een lijst met uw abonnementen op met de [opdracht az account list.](/cli/azure/account#az_account_list)

    ```azurecli
    az account list --output table
    ```

1. Gebruik [az account set om](/cli/azure/account#az_account_set) het actieve abonnement in te stellen dat u wilt overdragen.

    ```azurecli
    az account set --subscription "Marketing"
    ```

### <a name="install-the-azure-resource-graph-extension"></a>De extensie Azure Resource Graph installeren

 Met de Azure CLI-extensie [Azure Resource Graph](../governance/resource-graph/index.yml), *resource-graph*, kunt u de [opdracht az graph](/cli/azure/graph) gebruiken om query's uit te voeren op resources die worden beheerd door Azure Resource Manager. U gebruikt deze opdracht in latere stappen.

1. Gebruik [az extension list om](/cli/azure/extension#az_extension_list) te zien of de extensie *resource-graph* is geïnstalleerd.

    ```azurecli
    az extension list
    ```

1. Als dat niet het beste is, *installeert u de extensie resource-graph.*

    ```azurecli
    az extension add --name resource-graph
    ```

### <a name="save-all-role-assignments"></a>Alle roltoewijzingen opslaan

1. Gebruik [az role assignment list om](/cli/azure/role/assignment#az_role_assignment_list) alle roltoewijzingen weer te geven (inclusief overgenomen roltoewijzingen).

    Om het gemakkelijker te maken om de lijst te bekijken, kunt u de uitvoer exporteren als JSON, TSV of een tabel. Zie Lijst met [roltoewijzingen maken met Azure RBAC en Azure CLI voor meer informatie.](role-assignments-list-cli.md)

    ```azurecli
    az role assignment list --all --include-inherited --output json > roleassignments.json
    az role assignment list --all --include-inherited --output tsv > roleassignments.tsv
    az role assignment list --all --include-inherited --output table > roleassignments.txt
    ```

1. Sla de lijst met roltoewijzingen op.

    Wanneer u een abonnement over dragen,  worden alle roltoewijzingen permanent verwijderd. Het is dus belangrijk om een kopie op te slaan.

1. Bekijk de lijst met roltoewijzingen. Mogelijk zijn er roltoewijzingen die u niet nodig hebt in de doelmap.

### <a name="save-custom-roles"></a>Aangepaste rollen opslaan

1. Gebruik de [az role definition list om](/cli/azure/role/definition#az_role_definition_list) uw aangepaste rollen weer te geven. Zie Aangepaste Azure-rollen maken [of bijwerken met behulp van Azure CLI voor meer informatie.](custom-roles-cli.md)

    ```azurecli
    az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
    ```

1. Sla elke aangepaste rol die u nodig hebt in de doelmap op als een afzonderlijk JSON-bestand.

    ```azurecli
    az role definition list --name <custom_role_name> > customrolename.json
    ```

1. Maak kopieën van de aangepaste rolbestanden.

1. Wijzig elke kopie in de volgende indeling.

    U gebruikt deze bestanden later om de aangepaste rollen opnieuw te maken in de doelmap.

    ```json
    {
      "Name": "",
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": []
    }
    ```

### <a name="determine-user-group-and-service-principal-mappings"></a>Toewijzingen van gebruikers, groepen en service-principals bepalen

1. Bepaal op basis van uw lijst met roltoewijzingen de gebruikers, groepen en service-principals aan wie u in de doelmap wilt toevoegen.

    U kunt het type principal identificeren door te kijken naar de `principalType` eigenschap in elke roltoewijzing.

1. Maak indien nodig in de doelmap alle gebruikers, groepen of service-principals die u nodig hebt.

### <a name="list-role-assignments-for-managed-identities"></a>Lijst met roltoewijzingen voor beheerde identiteiten

Beheerde identiteiten worden niet bijgewerkt wanneer een abonnement wordt overgedragen naar een andere directory. Als gevolg hiervan worden alle bestaande door het systeem toegewezen of door de gebruiker toegewezen beheerde identiteiten verbroken. Na de overdracht kunt u alle door het systeem toegewezen beheerde identiteiten opnieuw inschakelen. Voor door de gebruiker toegewezen beheerde identiteiten moet u deze opnieuw maken en koppelen in de doelmap.

1. Bekijk de [lijst met Azure-services die beheerde identiteiten ondersteunen om](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) te zien waar u mogelijk beheerde identiteiten gebruikt.

1. Gebruik [az ad sp list om](/cli/azure/ad/sp#az_ad_sp_list) uw door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteiten weer te bieden.

    ```azurecli
    az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
    ```

1. Bepaal in de lijst met beheerde identiteiten welke door het systeem zijn toegewezen en welke door de gebruiker zijn toegewezen. U kunt de volgende criteria gebruiken om het type te bepalen.

    | Criteria | Type beheerde identiteit |
    | --- | --- |
    | `alternativeNames` eigenschap omvat `isExplicit=False` | Door het systeem toegewezen |
    | `alternativeNames` de eigenschap bevat geen `isExplicit` | Door het systeem toegewezen |
    | `alternativeNames` eigenschap omvat `isExplicit=True` | Door de gebruiker toegewezen |

    U kunt az [identity list ook gebruiken om alleen](/cli/azure/identity#az_identity_list) door de gebruiker toegewezen beheerde identiteiten weer te geven. Zie [Create, list, or delete a user-assigned managed identity using the Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)(Een door de gebruiker toegewezen beheerde identiteit maken, in een lijst of verwijderen) voor meer informatie.

    ```azurecli
    az identity list
    ```

1. Haal een lijst op met `objectId` de waarden voor uw beheerde identiteiten.

1. Zoek in uw lijst met roltoewijzingen om te zien of er roltoewijzingen zijn voor uw beheerde identiteiten.

### <a name="list-key-vaults"></a>Sleutelkluizen vermelden

Wanneer u een sleutelkluis maakt, wordt deze automatisch gekoppeld aan de Azure Active Directory tenant-id voor het abonnement waarin deze is gemaakt. Alle vermeldingen van het toegangsbeleid zijn ook gekoppeld aan deze tenant-ID. Zie Moving [an Azure Key Vault to another subscription (Een Azure Key Vault verplaatsen naar een ander abonnement) voor meer informatie.](../key-vault/general/move-subscription.md)

> [!WARNING]
> Als u versleuteling-at-rest gebruikt voor een resource, zoals een opslagaccount of SQL database, die afhankelijk **is** van een sleutelkluis die zich niet in hetzelfde abonnement dat wordt overgedragen, kan dit leiden tot een onherkenbaar scenario. Als u deze situatie hebt, moet u stappen ondernemen om een andere sleutelkluis te gebruiken of door de klant beheerde sleutels tijdelijk uit te schakelen om dit onherkenbare scenario te voorkomen.

- Als u een sleutelkluis hebt, gebruikt [u az keyvault show om](/cli/azure/keyvault#az_keyvault_show) het toegangsbeleid weer te geven. Zie Assign [a Key Vault access policy (Een toegangsbeleid Key Vault toewijzen) voor meer informatie.](../key-vault/general/assign-access-policy-cli.md)

    ```azurecli
    az keyvault show --name MyKeyVault
    ```

### <a name="list-azure-sql-databases-with-azure-ad-authentication"></a>Een Azure SQL databases met Azure AD-verificatie

- Gebruik [az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) en de az [graph](/cli/azure/graph) extension om te zien of u Azure SQL databases gebruikt met Azure AD-verificatie-integratie ingeschakeld. Zie Configure and manage Azure Active Directory authentication with SQL (Verificatie met [SQL configureren Azure Active Directory beheren) voor meer informatie.](../azure-sql/database/authentication-aad-configure.md)

    ```azurecli
    az sql server ad-admin list --ids $(az graph query -q 'resources | where type == "microsoft.sql/servers" | project id' -o tsv | cut -f1)
    ```

### <a name="list-acls"></a>ACL's opslijst

1. Als u een Azure Data Lake Storage Gen1, vermeldt u de ACL's die worden toegepast op een bestand met behulp van de Azure Portal of PowerShell.

1. Als u een Azure Data Lake Storage Gen2, vermeldt u de ACL's die worden toegepast op een bestand met behulp van de Azure Portal of PowerShell.

1. Als u een Azure Files, vermeldt u de ACL's die worden toegepast op een bestand.

### <a name="list-other-known-resources"></a>Lijst met andere bekende resources

1. Gebruik [az account show om](/cli/azure/account#az_account_show) uw abonnements-id op te halen.

    ```azurecli
    subscriptionId=$(az account show --query id | sed -e 's/^"//' -e 's/"$//')
    ```

1. Gebruik de [az graph extension](/cli/azure/graph) om andere Azure-resources met bekende Azure AD-directoryafhankelijkheden weer te geven.

    ```azurecli
    az graph query -q \
    'resources | where type != "microsoft.azureactivedirectory/b2cdirectories" | where  identity <> "" or properties.tenantId <> "" or properties.encryptionSettingsCollection.enabled == true | project name, type, kind, identity, tenantId, properties.tenantId' \
    --subscriptions $subscriptionId --output table
    ```

## <a name="step-2-transfer-the-subscription"></a>Stap 2: Het abonnement overdragen

In deze stap zet u het abonnement over van de bronmap naar de doelmap. De stappen verschillen, afhankelijk van of u ook het eigendom van de facturering wilt overdragen.

> [!WARNING]
> Wanneer u het abonnement over dragen, worden alle roltoewijzingen in **de** bronmap permanent verwijderd en kunnen ze niet worden hersteld. U kunt niet teruggaan nadat u het abonnement hebt over dragen. Zorg ervoor dat u de vorige stappen hebt voltooid voordat u deze stap gaat uitvoeren.

1. Bepaal of u het eigendom van de facturering ook wilt overdragen naar een ander account.

1. Het abonnement overdragen naar een andere directory.

    - Als u het huidige eigendom van facturering wilt behouden, volgt u de stappen in Een Azure-abonnement koppelen of toevoegen aan [uw Azure Active Directory tenant](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    - Als u het eigendom van de facturering ook wilt overdragen, volgt u de stappen in Eigendom van facturering van een Azure-abonnement overdragen [aan een ander account.](../cost-management-billing/manage/billing-subscription-transfer.md) Als u het abonnement wilt overdragen naar een andere directory, moet u het selectievakje **Abonnement Azure AD-tenant** in.

1. Wanneer u klaar bent met de overdracht van het abonnement, gaat u terug naar dit artikel om de resources opnieuw te maken in de doelmap.

## <a name="step-3-re-create-resources"></a>Stap 3: Resources opnieuw maken

### <a name="sign-in-to-target-directory"></a>Aanmelden bij doelmap

1. Meld u in de doelmap aan als de gebruiker die de overdrachtsaanvraag heeft geaccepteerd.

    Alleen de gebruiker in het nieuwe account die de overdrachtsaanvraag heeft geaccepteerd, heeft toegang om de resources te beheren.

1. Haal een lijst met uw abonnementen op met de [opdracht az account list.](/cli/azure/account#az_account_list)

    ```azurecli
    az account list --output table
    ```

1. Gebruik [az account set om](/cli/azure/account#az_account_set) het actieve abonnement in te stellen dat u wilt gebruiken.

    ```azurecli
    az account set --subscription "Contoso"
    ```

### <a name="create-custom-roles"></a>Aangepast rollen maken
        
- Gebruik [az role definition create om](/cli/azure/role/definition#az_role_definition_create) elke aangepaste rol te maken van de bestanden die u eerder hebt gemaakt. Zie Aangepaste Azure-rollen maken of bijwerken met behulp van [Azure CLI voor meer informatie.](custom-roles-cli.md)

    ```azurecli
    az role definition create --role-definition <role_definition>
    ```

### <a name="assign-roles"></a>Rollen toewijzen

- Gebruik [az role assignment create om](/cli/azure/role/assignment#az_role_assignment_create) rollen toe te wijzen aan gebruikers, groepen en service-principals. Zie Azure-rollen toewijzen met behulp van [Azure CLI voor meer informatie.](role-assignments-cli.md)

    ```azurecli
    az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
    ```

### <a name="update-system-assigned-managed-identities"></a>Door het systeem toegewezen beheerde identiteiten bijwerken

1. Door het systeem toegewezen beheerde identiteiten uitschakelen en opnieuw inschakelen.

    | Azure-service | Meer informatie | 
    | --- | --- |
    | Virtuele machines | [Beheerde identiteiten voor Azure-resources op een virtuele Azure-machine configureren met behulp van Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#system-assigned-managed-identity) |
    | Virtuele-machineschaalsets | [Beheerde identiteiten voor Azure-resources configureren op een virtuele-machineschaalset met Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#system-assigned-managed-identity) |
    | Overige services | [Services die beheerde identiteiten voor Azure-resources ondersteunen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) |

1. Gebruik [az role assignment create om](/cli/azure/role/assignment#az_role_assignment_create) rollen toe te wijzen aan door het systeem toegewezen beheerde identiteiten. Zie Toegang tot een beheerde identiteit toewijzen aan een resource met behulp van Azure CLI voor [meer informatie.](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-user-assigned-managed-identities"></a>Door de gebruiker toegewezen beheerde identiteiten bijwerken

1. Door de gebruiker toegewezen beheerde identiteiten verwijderen, opnieuw maken en koppelen.

    | Azure-service | Meer informatie | 
    | --- | --- |
    | Virtuele machines | [Beheerde identiteiten voor Azure-resources op een virtuele Azure-machine configureren met behulp van Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) |
    | Virtuele-machineschaalsets | [Beheerde identiteiten voor Azure-resources configureren op een virtuele-machineschaalset met Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#user-assigned-managed-identity) |
    | Overige services | [Services die beheerde identiteiten voor Azure-resources ondersteunen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)<br/>[Een door de gebruiker toegewezen beheerde identiteit maken, op een lijst zetten of verwijderen met behulp van de Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) |

1. Gebruik [az role assignment create om](/cli/azure/role/assignment#az_role_assignment_create) rollen toe te wijzen aan door de gebruiker toegewezen beheerde identiteiten. Zie Toegang tot een beheerde identiteit toewijzen aan een resource met behulp [van Azure CLI voor meer informatie.](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-key-vaults"></a>Sleutelkluizen bijwerken

In deze sectie worden de basisstappen beschreven voor het bijwerken van uw sleutelkluizen. Zie Moving [an Azure Key Vault to another subscription (Een Azure Key Vault verplaatsen naar een ander abonnement) voor meer informatie.](../key-vault/general/move-subscription.md)

1. Werk de tenant-id bij die is gekoppeld aan alle bestaande sleutelkluizen in het abonnement naar de doelmap.

1. Alle bestaande vermeldingen van het toegangsbeleid te verwijderen.

1. Voeg nieuwe vermeldingen voor toegangsbeleid toe die zijn gekoppeld aan de doelmap.

### <a name="update-acls"></a>ACL's bijwerken

1. Als u een Azure Data Lake Storage Gen1, wijst u de juiste ACL's toe. Zie Gegevens beveiligen die zijn opgeslagen in Azure Data Lake Storage Gen1 [voor meer Azure Data Lake Storage Gen1.](../data-lake-store/data-lake-store-secure-data.md)

1. Als u een Azure Data Lake Storage Gen2, wijst u de juiste ACL's toe. Zie [Toegangsbeheer in Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md) voor meer informatie.

1. Als u een Azure Files, wijst u de juiste ACL's toe.

### <a name="review-other-security-methods"></a>Andere beveiligingsmethoden controleren

Hoewel roltoewijzingen tijdens de overdracht worden verwijderd, hebben gebruikers in het account van de oorspronkelijke eigenaar mogelijk nog steeds toegang tot het abonnement via andere beveiligingsmethoden, waaronder:

- Toegangssleutels voor services zoals Storage.
- [Beheercertificaten die de](../cloud-services/cloud-services-certs-create.md) gebruiker beheerderstoegang verlenen tot abonnementsbronnen.
- Referenties voor externe toegang voor services zoals Azure Virtual Machines.

Als u de toegang van gebruikers in de bronmap wilt verwijderen zodat ze geen toegang hebben tot de doelmap, kunt u overwegen om referenties te roteren. Totdat de referenties zijn bijgewerkt, blijven gebruikers na de overdracht toegang houden.

1. Toegangssleutels voor opslagaccounts roteren. Zie [Toegangssleutels voor een opslagaccount beheren](../storage/common/storage-account-keys-manage.md) voor meer informatie.

1. Als u toegangssleutels gebruikt voor andere services, zoals Azure SQL Database of Azure Service Bus Messaging, moet u de toegangssleutels roteren.

1. Voor resources die gebruikmaken van geheimen, opent u de instellingen voor de resource en werk u het geheim bij.

1. Werk het certificaat bij voor resources die gebruikmaken van certificaten.

## <a name="next-steps"></a>Volgende stappen

- [Eigendom van de facturering van een Azure-abonnement overdragen aan een ander account](../cost-management-billing/manage/billing-subscription-transfer.md)
- [Overdracht van Azure-abonnementen tussen abonnees en CSP's](../cost-management-billing/manage/transfer-subscriptions-subscribers-csp.md)
- [Een Azure-abonnement aan uw Azure Active Directory-tenant toevoegen of koppelen](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
- [Azure Lighthouse in zakelijke scenario's](../lighthouse/concepts/enterprise.md)
