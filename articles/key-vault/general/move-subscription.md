---
title: Azure Key Vault kluis verplaatsen naar een ander | Microsoft Docs
description: Richtlijnen voor het verplaatsen van een sleutelkluis naar een ander abonnement.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 65dc9da03a6b763d419c51e53bf756550e8b56a4
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751846"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Een Azure Key Vault verplaatsen naar een ander abonnement

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Overzicht

> [!IMPORTANT]
> **Het verplaatsen van een sleutelkluis naar een ander abonnement veroorzaakt een belangrijke wijziging in uw omgeving.**
> Zorg ervoor dat u de impact van deze wijziging begrijpt en volg de richtlijnen in dit artikel zorgvuldig voordat u besluit om de sleutelkluis te verplaatsen naar een nieuw abonnement.
> Als u Managed Service Identities (MSI) gebruikt, leest u de instructies na het verplaatsen aan het einde van het document. 

[Azure Key Vault](overview.md) is automatisch gekoppeld aan de [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) tenant-id voor het abonnement waarin het is gemaakt. U vindt de tenant-id die is gekoppeld aan uw abonnement door deze handleiding [te volgen.](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md) Alle vermeldingen van toegangsbeleid en roltoewijzingen zijn ook gekoppeld aan deze tenant-id.  Als u uw Azure-abonnement verplaatst van tenant A naar tenant B, zijn uw bestaande sleutelkluizen niet toegankelijk voor de service-principals (gebruikers en toepassingen) in tenant B. U kunt dit probleem oplossen door het volgende te doen:

* De tenant-ID die is gekoppeld aan alle bestaande sleutelkluizen in het abonnement te wijzigen in tenant B.
* Alle bestaande vermeldingen van het toegangsbeleid te verwijderen.
* Nieuwe aan tenant B gekoppelde vermeldingen van het toegangsbeleid toe te voegen.

Zie voor meer informatie Azure Key Vault en Azure Active Directory
- [Over Azure Key Vault](overview.md)
- [Wat is Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)
- [Een tenant-id vinden](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)

## <a name="limitations"></a>Beperkingen

> [!IMPORTANT]
> **Sleutelkluizen die worden gebruikt voor schijfversleuteling, kunnen niet worden verplaatst** Als u een sleutelkluis gebruikt met schijfversleuteling voor een VM, kan de sleutelkluis niet worden verplaatst naar een andere resourcegroep of een abonnement terwijl schijfversleuteling is ingeschakeld. U moet schijfversleuteling uitschakelen voordat u de sleutelkluis naar een nieuwe resourcegroep of een nieuw abonnement verplaatst. 

Sommige service-principals (gebruikers en toepassingen) zijn gebonden aan een specifieke tenant. Als u uw sleutelkluis verplaatst naar een abonnement in een andere tenant, bestaat de kans dat u de toegang tot een specifieke service-principal niet kunt herstellen. Controleer of alle essentiële service-principals aanwezig zijn in de tenant waar u uw sleutelkluis verplaatst.

## <a name="prerequisites"></a>Vereisten

* [Toegang](../../role-based-access-control/built-in-roles.md#contributor) op inzenderniveau of hoger tot het huidige abonnement waar uw sleutelkluis zich bevindt. U kunt een rol toewijzen [met behulp Azure Portal,](../../role-based-access-control/role-assignments-portal.md) [Azure CLI](../../role-based-access-control/role-assignments-cli.md)of [PowerShell.](../../role-based-access-control/role-assignments-powershell.md)
* [Toegang](../../role-based-access-control/built-in-roles.md#contributor) op inzenderniveau of hoger tot het abonnement waar u uw sleutelkluis wilt verplaatsen. U kunt een rol toewijzen [met behulp Azure Portal,](../../role-based-access-control/role-assignments-portal.md) [Azure CLI](../../role-based-access-control/role-assignments-cli.md)of [PowerShell.](../../role-based-access-control/role-assignments-powershell.md)
* Een resourcegroep in het nieuwe abonnement. U kunt er een maken [met Azure Portal,](../../azure-resource-manager/management/manage-resource-groups-portal.md) [PowerShell](../../azure-resource-manager/management/manage-resource-groups-powershell.md)of [Azure CLI.](../../azure-resource-manager/management/manage-resource-groups-cli.md)

U kunt bestaande rollen controleren met [behulp Azure Portal,](../../role-based-access-control/role-assignments-list-portal.md) [PowerShell,](../../role-based-access-control/role-assignments-list-powershell.md) [Azure CLI](../../role-based-access-control/role-assignments-list-cli.md)of [REST API.](../../role-based-access-control/role-assignments-list-rest.md)


## <a name="moving-a-key-vault-to-a-new-subscription"></a>Een sleutelkluis verplaatsen naar een nieuw abonnement

1. Meld u aan bij Azure Portal op https://portal.azure.com.
2. Navigeer naar [uw sleutelkluis](overview.md)
3. Klik op het tabblad Overzicht
4. Selecteer de knop Verplaatsen
5. Selecteer 'Verplaatsen naar een ander abonnement' in de vervolgkeuzeopties
6. Selecteer de resourcegroep waarin u de sleutelkluis wilt verplaatsen
7. De waarschuwing met betrekking tot het verplaatsen van resources bevestigen
8. Selecteer OK

## <a name="additional-steps-when-subscription-is-in-a-new-tenant"></a>Aanvullende stappen wanneer het abonnement zich in een nieuwe tenant

Als u uw sleutelkluis hebt verplaatst naar een abonnement in een nieuwe tenant, moet u de tenant-id handmatig bijwerken en oude toegangsbeleidsregels en roltoewijzingen verwijderen. Hier volgen zelfstudies voor deze stappen in PowerShell en Azure CLI. Als u PowerShell gebruikt, moet u mogelijk de onderstaande Clear-AzContext uitvoeren, zodat u resources kunt zien die buiten het huidige geselecteerde bereik vallen. 

### <a name="update-tenant-id-in-a-key-vault"></a>Tenant-id in een sleutelkluis bijwerken

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource -ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.

Clear-AzContext                                                            #Clear the context from PowerShell
Connect-AzAccount                                                          #Log in again to confirm you have the correct tenant id
````

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```
### <a name="update-access-policies-and-role-assignments"></a>Toegangsbeleid en roltoewijzingen bijwerken

> [!NOTE]
> Als Key Vault azure [RBAC-machtigingsmodel](../../role-based-access-control/overview.md) gebruikt. U moet ook roltoewijzingen voor de sleutelkluis verwijderen. U kunt roltoewijzingen verwijderen met [behulp van Azure Portal,](../../role-based-access-control/role-assignments-portal.md) [Azure CLI](../../role-based-access-control/role-assignments-cli.md)of [PowerShell.](../../role-based-access-control/role-assignments-powershell.md) 

Nu uw kluis is gekoppeld aan de juiste tenant-id en oude vermeldingen van het toegangsbeleid of roltoewijzingen zijn verwijderd, stelt u nieuwe vermeldingen of roltoewijzingen in voor het toegangsbeleid.

Zie voor het toewijzen van beleidsregels:
- [Een toegangsbeleid toewijzen met behulp van de portal](assign-access-policy-portal.md)
- [Een toegangsbeleid toewijzen met behulp van Azure CLI](assign-access-policy-cli.md)
- [Een toegangsbeleid toewijzen met behulp van PowerShell](assign-access-policy-powershell.md)

Zie voor het toevoegen van roltoewijzingen:
- [Azure-rollen toewijzen met behulp van de Azure Portal](../../role-based-access-control/role-assignments-portal.md)
- [Azure-rollen toewijzen met behulp van Azure CLI](../../role-based-access-control/role-assignments-cli.md)
- [Azure-rollen toewijzen met behulp van PowerShell](../../role-based-access-control/role-assignments-powershell.md)


### <a name="update-managed-identities"></a>Beheerde identiteiten bijwerken

Als u een volledig abonnement overzetten en een beheerde identiteit gebruikt voor Azure-resources, moet u dit ook bijwerken naar de nieuwe Azure Active Directory tenant. Voor meer informatie over beheerde identiteiten, [overzicht van beheerde identiteiten.](../../active-directory/managed-identities-azure-resources/overview.md)

Als u een beheerde identiteit gebruikt, moet u ook de identiteit bijwerken omdat de oude identiteit niet meer in de juiste Azure Active Directory tenant. Zie de volgende documenten om dit probleem op te lossen. 

* [MSI bijwerken](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)
* [Abonnement overdragen naar nieuwe map](../../role-based-access-control/transfer-subscription.md)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [sleutels, geheimen en certificaten](about-keys-secrets-certificates.md)
- Voor conceptuele informatie, waaronder het interpreteren van Key Vault logboeken, zie [Key Vault logboekregistratie](logging.md)
- [Gids voor Key Vault-ontwikkelaars](../general/developers-guide.md)
- [Uw Key Vault beveiligen](security-overview.md)
- [Azure Key Vault-firewalls en virtuele netwerken configureren](network-security.md)