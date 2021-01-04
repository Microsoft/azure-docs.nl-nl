---
title: Azure Key Vault een kluis naar een ander abonnement verplaatsen | Microsoft Docs
description: Richt lijnen voor het verplaatsen van een sleutel kluis naar een ander abonnement.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: d881394391b7967fe602155eefc9844e013de34e
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724745"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Een Azure Key Vault verplaatsen naar een ander abonnement

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Overzicht

> [!IMPORTANT]
> **Als u een sleutel kluis verplaatst naar een ander abonnement, veroorzaakt dit een ernstige wijziging in uw omgeving.**
> Zorg ervoor dat u bekend bent met de gevolgen van deze wijziging en volg de richt lijnen in dit artikel zorgvuldig voordat u besluit sleutel kluis naar een nieuw abonnement te verplaatsen.
> Als u gebruikmaakt van Managed Service Identities (MSI), raadpleegt u de instructies na het verplaatsen aan het einde van het document. 

[Azure Key Vault](overview.md) wordt automatisch gekoppeld aan de standaard [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) Tenant-id voor het abonnement waarin deze is gemaakt. U vindt de Tenant-ID die is gekoppeld aan uw abonnement door deze [hand leiding](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-to-find-tenant)te volgen. Alle toegangs beleid-en rollen toewijzingen zijn ook gekoppeld aan deze Tenant-ID.  Als u uw Azure-abonnement verplaatst van Tenant A naar Tenant B, worden uw bestaande sleutel kluizen niet toegankelijk voor de service-principals (gebruikers en toepassingen) in Tenant B. U moet het volgende doen om dit probleem op te lossen:

* De tenant-ID die is gekoppeld aan alle bestaande sleutelkluizen in het abonnement te wijzigen in tenant B.
* Alle bestaande vermeldingen van het toegangsbeleid te verwijderen.
* Nieuwe aan tenant B gekoppelde vermeldingen van het toegangsbeleid toe te voegen.

Zie voor meer informatie over Azure Key Vault en Azure Active Directory
- [Over Azure Key Vault](overview.md)
- [Wat is Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
- [Een tenant-id vinden](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-to-find-tenant)

## <a name="limitations"></a>Beperkingen

> [!IMPORTANT]
> **Sleutel kluizen die worden gebruikt voor schijf versleuteling, kunnen niet worden verplaatst** Als u sleutel kluis met schijf versleuteling voor een virtuele machine gebruikt, kan de sleutel kluis niet worden verplaatst naar een andere resource groep of een abonnement wanneer schijf versleuteling is ingeschakeld. U moet schijf versleuteling uitschakelen voordat u de sleutel kluis verplaatst naar een nieuwe resource groep of een nieuw abonnement. 

Sommige service-principals (gebruikers en toepassingen) zijn gekoppeld aan een specifieke Tenant. Als u de sleutel kluis verplaatst naar een abonnement in een andere Tenant, is er een kans dat u de toegang tot een specifieke Service-Principal niet kunt herstellen. Controleer of alle essentiële service-principals aanwezig zijn in de Tenant waar u de sleutel kluis wilt verplaatsen.

## <a name="prerequisites"></a>Vereisten

* Toegang op [Inzender](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) niveau of hoger voor het huidige abonnement waar uw sleutel kluis bestaat. U kunt een rol toewijzen met behulp van [Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), [Azure cli](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)of [Power shell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).
* Toegang op [Inzender](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) niveau of hoger voor het abonnement waar u de sleutel kluis wilt verplaatsen. U kunt een rol toewijzen met behulp van [Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), [Azure cli](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)of [Power shell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).
* Een resource groep in het nieuwe abonnement. U kunt er een maken met behulp van [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal), [Power shell](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-powershell)of [Azure cli](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-cli).

U kunt bestaande rollen controleren met behulp van [Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal), [Power shell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-powershell), [Azure cli](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-cli)of [rest API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-rest).


## <a name="moving-a-key-vault-to-a-new-subscription"></a>Een sleutel kluis verplaatsen naar een nieuw abonnement

1. Meld u aan bij Azure Portal op https://portal.azure.com.
2. Navigeer naar uw [sleutel kluis](overview.md)
3. Klik op het tabblad Overzicht
4. Selecteer de knop "verplaatsen"
5. Selecteer ' verplaatsen naar een ander abonnement ' in de vervolg keuzelijst opties
6. Selecteer de resource groep waarnaar u de sleutel kluis wilt verplaatsen
7. De waarschuwing bevestigen over het verplaatsen van resources
8. Selecteer OK

## <a name="additional-steps-when-subscription-is-in-a-new-tenant"></a>Aanvullende stappen wanneer het abonnement zich in een nieuwe Tenant bevindt

Als u de sleutel kluis naar een abonnement in een nieuwe Tenant hebt verplaatst, moet u de Tenant-ID hand matig bijwerken en het oude toegangs beleid en de roltoewijzingen verwijderen. Hier vindt u zelf studies voor deze stappen in Power shell en Azure CLI. Als u Power shell gebruikt, moet u mogelijk de Clear-AzContext-opdracht uitvoeren die hieronder wordt beschreven, zodat u de resources buiten het huidige geselecteerde bereik kunt zien. 

### <a name="update-tenant-id-in-a-key-vault"></a>Tenant-ID in een sleutel kluis bijwerken

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
### <a name="update-access-policies-and-role-assignments"></a>Het toegangs beleid en de roltoewijzingen bijwerken

> [!NOTE]
> Als Key Vault gebruikmaakt van [Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) -machtigings model. U moet ook sleutel kluis roltoewijzingen verwijderen. U kunt roltoewijzingen verwijderen met behulp van [Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), [Azure cli](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)of [Power shell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell). 

Nu uw kluis is gekoppeld aan de juiste Tenant-ID en de oude toegangs beleidsregels of roltoewijzingen worden verwijderd, stelt u nieuwe toegangs beleidsregels of roltoewijzingen in.

Zie voor het toewijzen van beleid:
- [Een toegangs beleid toewijzen met behulp van portal](assign-access-policy-portal.md)
- [Een toegangs beleid toewijzen met behulp van Azure CLI](assign-access-policy-cli.md)
- [Een toegangs beleid toewijzen met behulp van Power shell](assign-access-policy-powershell.md)

Zie voor het toevoegen van roltoewijzingen:
- [Roltoewijzing toevoegen met behulp van portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
- [Roltoewijzing toevoegen met behulp van Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)
- [Roltoewijzing toevoegen met Power shell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)


### <a name="update-managed-identities"></a>Beheerde identiteiten bijwerken

Als u een volledig abonnement overbrengt en gebruikmaakt van een beheerde identiteit voor Azure-resources, moet u dit ook bijwerken naar de nieuwe Azure Active Directory-Tenant. Voor meer informatie over beheerde identiteiten, [overzicht van beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md).

Als u beheerde identiteit gebruikt, moet u ook de identiteit bijwerken omdat de oude identiteit niet meer in de juiste Azure Active Directory Tenant voor komt. Raadpleeg de volgende documenten voor informatie over het oplossen van dit probleem. 

* [MSI bijwerken](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)
* [Abonnement overdragen naar nieuwe map](../../role-based-access-control/transfer-subscription.md)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [sleutels, geheimen en certificaten](about-keys-secrets-certificates.md)
- Zie [Key Vault logging](logging.md) (Engelstalig) voor conceptuele informatie, inclusief het interpreteren van Key Vault logboeken
- [Gids voor Key Vault-ontwikkelaars](../general/developers-guide.md)
- [Uw Key Vault beveiligen](secure-your-key-vault.md)
- [Azure Key Vault-firewalls en virtuele netwerken configureren](network-security.md)
