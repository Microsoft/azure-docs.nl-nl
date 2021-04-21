---
title: Toepassingen toegang verlenen tot een Azure-sleutelkluis met behulp van Azure RBAC-| Microsoft Docs
description: Meer informatie over het bieden van toegang tot sleutels, geheimen en certificaten met behulp van op rollen gebaseerd toegangsbeheer van Azure.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/15/2021
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 966f704bd47b4b238ed72579a6103bd2e4348849
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772213"
---
# <a name="provide-access-to-key-vault-keys-certificates-and-secrets-with-an-azure-role-based-access-control"></a>Toegang bieden tot Key Vault, certificaten en geheimen met op rollen gebaseerd toegangsbeheer van Azure

> [!NOTE]
> Key Vault resourceprovider ondersteunt twee resourcetypen: **kluizen en** **beheerde HMS's.** Toegangsbeheer dat in dit artikel wordt beschreven, is alleen van toepassing **op kluizen**. Zie Toegangsbeheer voor beheerde HSM voor meer informatie over [toegangsbeheer voor beheerde HSM.](../managed-hsm/access-control.md)

Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) is een autorisatiesysteem dat is gebouwd op [Azure Resource Manager](../../azure-resource-manager/management/overview.md) dat een fijnkeurig toegangsbeheer van Azure-resources biedt.

Met Azure RBAC kunnen gebruikers machtigingen voor sleutels, geheimen en certificaten beheren. Het biedt één plek voor het beheren van alle machtigingen voor alle sleutelkluizen. 

Het Azure RBAC-model biedt de mogelijkheid om machtigingen in te stellen op verschillende bereikniveaus: beheergroep, abonnement, resourcegroep of afzonderlijke resources.  Azure RBAC voor key vault biedt ook de mogelijkheid om afzonderlijke machtigingen te hebben voor afzonderlijke sleutels, geheimen en certificaten

Zie Op rollen gebaseerd toegangsbeheer [van Azure (Azure RBAC)](../../role-based-access-control/overview.md)voor meer informatie.

## <a name="best-practices-for-individual-keys-secrets-and-certificates"></a>Best practices voor afzonderlijke sleutels, geheimen en certificaten

Het wordt aanbevolen een kluis per toepassing per omgeving te gebruiken (ontwikkeling, preproductie en productie).

Machtigingen voor afzonderlijke sleutels, geheimen en certificaten mogen alleen worden gebruikt voor specifieke scenario's:

-   Toepassingen met meerdere lagen die toegangsbeheer tussen lagen moeten scheiden

-   Afzonderlijk geheim delen tussen meerdere toepassingen

Meer informatie Azure Key Vault richtlijnen voor beheer, zie:

- [Overzicht van Azure Key Vault-beveiliging](security-overview.md)
- [Azure Key Vault servicelimieten](service-limits.md)

## <a name="azure-built-in-roles-for-key-vault-data-plane-operations"></a>Ingebouwde Azure-rollen voor Key Vault gegevensvlakbewerkingen
> [!NOTE]
> `Key Vault Contributor` de rol is voor beheervlakbewerkingen voor het beheren van sleutelkluizen. Er wordt geen toegang tot sleutels, geheimen en certificaten toegestaan.

| Ingebouwde rol | Beschrijving | Id |
| --- | --- | --- |
| Key Vault Administrator| Alle gegevensvlakbewerkingen uitvoeren op een sleutelkluis en alle objecten daarin, inclusief certificaten, sleutels en geheimen. Kan geen key vault-resources beheren of roltoewijzingen beheren. Werkt alleen voor sleutelkluizen die gebruikmaken van het machtigingsmodel Op rollen gebaseerd toegangsbeheer van Azure. | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
| Key Vault Certificates Officer | Voer een actie uit op de certificaten van een sleutelkluis, behalve machtigingen beheren. Werkt alleen voor sleutelkluizen die gebruikmaken van het machtigingsmodel Op rollen gebaseerd toegangsbeheer van Azure. | a4417e6f-fecd-4de8-b567-7b0420556985 |
| Key Vault Crypto Officer | Voer een actie uit op de sleutels van een sleutelkluis, behalve machtigingen beheren. Werkt alleen voor sleutelkluizen die gebruikmaken van het machtigingsmodel Op rollen gebaseerd toegangsbeheer van Azure. | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
| Key Vault cryptoserviceversleutelingsgebruiker | Metagegevens van sleutels lezen en wrap-/unwrap-bewerkingen uitvoeren. Werkt alleen voor sleutelkluizen die gebruikmaken van het machtigingsmodel Op rollen gebaseerd toegangsbeheer van Azure. | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
| Key Vault cryptogebruiker  | Cryptografische bewerkingen uitvoeren met behulp van sleutels. Werkt alleen voor sleutelkluizen die gebruikmaken van het machtigingsmodel Op rollen gebaseerd toegangsbeheer van Azure. | 12338af0-0e69-4776-bea7-57ae8d297424 |
| Key Vault Lezer | Metagegevens van sleutelkluizen en de certificaten, sleutels en geheimen lezen. Kan geen gevoelige waarden lezen, zoals geheime inhoud of sleutelmateriaal. Werkt alleen voor sleutelkluizen die gebruikmaken van het machtigingsmodel Op rollen gebaseerd toegangsbeheer van Azure. | 21090545-7ca7-4776-b22c-e363652d74d2 |
| Key Vault Secrets Officer| Voer een actie uit op de geheimen van een sleutelkluis, behalve machtigingen beheren. Werkt alleen voor sleutelkluizen die gebruikmaken van het machtigingsmodel Op rollen gebaseerd toegangsbeheer van Azure. | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
| Key Vault Geheimen-gebruiker | Geheime inhoud lezen. Werkt alleen voor sleutelkluizen die gebruikmaken van het machtigingsmodel Op rollen gebaseerd toegangsbeheer van Azure. | 4633458b-17de-408a-b874-0445c86b69e6 |

Zie Ingebouwde Azure-rollen voor meer informatie over ingebouwde [Azure-roldefinities.](../../role-based-access-control/built-in-roles.md)

## <a name="using-azure-rbac-secret-key-and-certificate-permissions-with-key-vault"></a>Azure RBAC-machtigingen voor geheimen, sleutels en certificaten gebruiken met Key Vault

Het nieuwe Azure RBAC-machtigingsmodel voor key vault biedt een alternatief voor het model voor toegangsbeleid voor de kluis. 

### <a name="prerequisites"></a>Vereisten

Als u roltoewijzingen wilt toevoegen, hebt u het volgende nodig:

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
- Machtigingen voor `Microsoft.Authorization/roleAssignments/write` en `Microsoft.Authorization/roleAssignments/delete`, zoals [Beheerder van gebruikerstoegang](../../role-based-access-control/built-in-roles.md#user-access-administrator) of [Eigenaar](../../role-based-access-control/built-in-roles.md#owner)

### <a name="enable-azure-rbac-permissions-on-key-vault"></a>Azure RBAC-machtigingen inschakelen op Key Vault

> [!NOTE]
> Voor het wijzigen van het machtigingsmodel is de machtiging Microsoft.Authorization/roleAssignments/write vereist, die deel uitmaakt van de rollen Eigenaar en [Gebruikerstoegangbeheerder.](../../role-based-access-control/built-in-roles.md#user-access-administrator) [](../../role-based-access-control/built-in-roles.md#owner) Klassieke abonnementsbeheerdersrollen zoals 'Servicebeheerder' en 'Medebeheerder' worden niet ondersteund.

1.  Azure RBAC-machtigingen inschakelen voor nieuwe sleutelkluis:

    ![Azure RBAC-machtigingen inschakelen - nieuwe kluis](../media/rbac/image-1.png)

2.  Azure RBAC-machtigingen inschakelen voor bestaande sleutelkluis:

    ![Azure RBAC-machtigingen inschakelen - bestaande kluis](../media/rbac/image-2.png)

> [!IMPORTANT]
> Als u het Azure RBAC-machtigingsmodel instelt, worden alle machtigingen voor toegangsbeleid ongeldig. Dit kan uitval veroorzaken wanneer er geen equivalente Azure-rollen worden toegewezen.

### <a name="assign-role"></a>Rol toewijzen

> [!Note]
> Het is raadzaam om de unieke rol-id te gebruiken in plaats van de rolnaam in scripts. Als de naam van een rol wordt gewijzigd, blijven uw scripts daarom werken. In dit document wordt de naam van de rol alleen gebruikt voor leesbaarheid.

Voer de volgende opdracht uit om een roltoewijzing te maken:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope <scope>
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName <role_name> -SignInName <assignee_upn> -Scope <scope>

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName Reader -ApplicationId <applicationId> -Scope <scope>
```
---

In de Azure Portal is het scherm Azure-roltoewijzingen beschikbaar voor alle resources op het tabblad Toegangsbeheer (IAM).

![Roltoewijzing - tabblad (IAM)](../media/rbac/image-3.png)

### <a name="resource-group-scope-role-assignment"></a>Roltoewijzing voor het bereik van resourcegroep

1.  Ga naar de sleutelkluisresourcegroep.
    ![Roltoewijzing - resourcegroep](../media/rbac/image-4.png)

2.  Klik op Toegangsbeheer (IAM) \> Roltoewijzing toevoegen \>

3.  De Key Vault lezerrol 'Key Vault Lezer' maken voor de huidige gebruiker

    ![Rol toevoegen - resourcegroep](../media/rbac/image-5.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Reader" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Reader' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Reader' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```
---

Bovenstaande roltoewijzing biedt de mogelijkheid om sleutelkluisobjecten in de sleutelkluis weer te geven.

### <a name="key-vault-scope-role-assignment"></a>Key Vault bereik van roltoewijzing

1. Ga naar Key Vault \> tabblad Toegangsbeheer (IAM)

2. Klik op Roltoewijzing toevoegen \>

3. De rol Key Secrets Officer 'Key Vault Secrets Officer' maken voor de huidige gebruiker.

    ![Roltoewijzing - sleutelkluis](../media/rbac/image-6.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Secrets Officer" --assignee {i.e jalichwa@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```
---

Nadat u de bovenstaande roltoewijzing hebt aanmaken, kunt u geheimen maken/bijwerken/verwijderen.

4. Maak een nieuw geheim (Geheimen \> +Genereren/importeren) om roltoewijzing op geheim niveau te testen.

    ![Rol toevoegen - sleutelkluis](../media/rbac/image-7.png)

### <a name="secret-scope-role-assignment"></a>Roltoewijzing geheim bereik

1. Open een van de eerder gemaakte geheimen. U ziet overzicht en toegangsbeheer (IAM) 

2. Klik op het tabblad Toegangsbeheer (IAM)

    ![Roltoewijzing - geheim](../media/rbac/image-8.png)

3. Maak de rol Key Secrets Officer 'Key Vault Secrets Officer' voor de huidige gebruiker, net zoals hierboven is gedaan voor de Key Vault.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Secrets Officer" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```
---

### <a name="test-and-verify"></a>Testen en verifiëren

> [!NOTE]
> Browsers gebruiken caching en paginavernieuwing is vereist na het verwijderen van roltoewijzingen.<br>
> Enkele minuten toestaan dat roltoewijzingen worden vernieuwd

1. Valideer het toevoegen van een nieuw geheim Key Vault de rol Secrets Officer op key vault-niveau.

Ga naar het tabblad Toegangsbeheer (IAM) van de sleutelkluis en verwijder de roltoewijzing 'Key Vault Secrets Officer' voor deze resource.

![Toewijzing verwijderen - sleutelkluis](../media/rbac/image-9.png)

Navigeer naar het eerder gemaakte geheim. U kunt alle geheime eigenschappen zien.

![Geheime weergave met toegang](../media/rbac/image-10.png)

Voor het maken van een nieuw geheim \> (Geheimen +Genereren/importeren) wordt de onderstaande fout weergegeven:

   ![Nieuw geheim maken](../media/rbac/image-11.png)

2.  Valideer het bewerken van geheimen Key Vault de rol Secret Officer op geheim niveau.

-   Ga naar het tabblad eerder gemaakte Access Control (IAM) en verwijder de roltoewijzing 'Key Vault Secrets Officer' voor deze resource.

-   Navigeer naar het eerder gemaakte geheim. U kunt geheime eigenschappen zien.

   ![Geheime weergave zonder toegang](../media/rbac/image-12.png)

3. Valideer geheimen die zijn gelezen zonder de rol lezer op sleutelkluisniveau.

-   Ga naar het tabblad Toegangsbeheer (IAM) van de sleutelkluisresourcegroep en verwijder de roltoewijzing Key Vault Lezer.

-   Als u naar het tabblad Geheimen van de sleutelkluis navigeert, wordt de onderstaande fout weergegeven:

   ![Tabblad Geheim - fout](../media/rbac/image-13.png)

### <a name="creating-custom-roles"></a>Aangepaste rollen maken 

[az role definition create command](/cli/azure/role/definition#az_role_definition_create)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
```azurecli
az role definition create --role-definition '{ \
   "Name": "Backup Keys Operator", \
   "Description": "Perform key backup/restore operations", \
    "Actions": [ 
    ], \
    "DataActions": [ \
        "Microsoft.KeyVault/vaults/keys/read ", \
        "Microsoft.KeyVault/vaults/keys/backup/action", \
         "Microsoft.KeyVault/vaults/keys/restore/action" \
    ], \
    "NotDataActions": [ 
   ], \
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] \
}'
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$roleDefinition = @"
{ 
   "Name": "Backup Keys Operator", 
   "Description": "Perform key backup/restore operations", 
    "Actions": [ 
    ], 
    "DataActions": [ 
        "Microsoft.KeyVault/vaults/keys/read ", 
        "Microsoft.KeyVault/vaults/keys/backup/action", 
         "Microsoft.KeyVault/vaults/keys/restore/action" 
    ], 
    "NotDataActions": [ 
   ], 
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] 
}
"@

$roleDefinition | Out-File role.json

New-AzRoleDefinition -InputFile role.json
```
---

Zie voor meer informatie over het maken van aangepaste rollen:

[Aangepaste Azure-rollen](../../role-based-access-control/custom-roles.md)

## <a name="known-limits-and-performance"></a>Bekende limieten en prestaties

-   2000 Azure-roltoewijzingen per abonnement

-   Latentie van roltoewijzingen: bij de huidige verwachte prestaties duurt het maximaal 10 minuten (600 seconden) nadat roltoewijzingen zijn gewijzigd voordat de rol wordt toegepast

## <a name="learn-more"></a>Lees meer

- [Overzicht van Azure RBAC](../../role-based-access-control/overview.md)
- [Zelfstudie aangepaste rollen](../../role-based-access-control/tutorial-custom-role-cli.md)