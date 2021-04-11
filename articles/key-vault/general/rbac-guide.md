---
title: Machtigingen verlenen aan toepassingen om toegang te krijgen tot een Azure-sleutel kluis met behulp van Azure RBAC | Microsoft Docs
description: Meer informatie over het bieden van toegang tot sleutels, geheimen en certificaten met behulp van Azure op rollen gebaseerd toegangs beheer.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 216df0d128e0557345db8f82f6010e1ef681593c
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968778"
---
# <a name="provide-access-to-key-vault-keys-certificates-and-secrets-with-an-azure-role-based-access-control"></a>Toegang bieden tot Key Vault sleutels, certificaten en geheimen met een op rollen gebaseerd toegangs beheer van Azure

> [!NOTE]
> Key Vault resource provider ondersteunt twee resource typen: **kluizen** en **beheerde hsm's**. Toegangs beheer dat in dit artikel wordt beschreven, is alleen van toepassing op **kluizen**. Zie [Managed HSM Access Control](../managed-hsm/access-control.md)(Engelstalig) voor meer informatie over toegangs beheer voor beheerde HSM.

Toegangs beheer op basis van rollen (Azure RBAC) van Azure is een autorisatie systeem dat is gebaseerd op [Azure Resource Manager](../../azure-resource-manager/management/overview.md) dat een nauw keurig toegangs beheer van Azure-resources biedt.

Met Azure RBAC kunnen gebruikers sleutel-, geheimen-en certificaat machtigingen beheren. Het biedt één plek om alle machtigingen voor alle sleutel kluizen te beheren. 

Het Azure RBAC-model biedt de mogelijkheid om machtigingen in te stellen op verschillende Scope niveaus: beheer groep, abonnement, resource groep of afzonderlijke resources.  Azure RBAC voor sleutel kluis biedt ook de mogelijkheid om afzonderlijke machtigingen te hebben voor afzonderlijke sleutels, geheimen en certificaten

Zie voor meer informatie [Azure Role-based Access Control (Azure RBAC)](../../role-based-access-control/overview.md).

## <a name="best-practices-for-individual-keys-secrets-and-certificates"></a>Aanbevolen procedures voor afzonderlijke sleutels, geheimen en certificaten

Onze aanbeveling is een kluis per toepassing per omgeving te gebruiken (ontwikkeling, voor bereiding en productie).

Afzonderlijke sleutels, geheimen en machtigingen voor certificaten moeten alleen worden gebruikt voor specifieke scenario's:

-   Toepassingen met meerdere lagen die het toegangs beheer tussen lagen moeten scheiden

-   Afzonderlijk geheim delen tussen meerdere toepassingen

Meer informatie over de richt lijnen voor Azure Key Vault beheer vindt u in:

- [Overzicht van Azure Key Vault-beveiliging](security-overview.md)
- [Azure Key Vault-service limieten](service-limits.md)

## <a name="azure-built-in-roles-for-key-vault-data-plane-operations"></a>Ingebouwde rollen van Azure voor Key Vault gegevensvlak bewerkingen
> [!NOTE]
> `Key Vault Contributor` rol is voor beheer vlak bewerkingen voor het beheren van sleutel kluizen. Er is geen toegang tot sleutels, geheimen en certificaten toegestaan.

| Ingebouwde rol | Beschrijving | Id |
| --- | --- | --- |
| Key Vault beheerder| Alle gegevenslaag bewerkingen uitvoeren op een sleutel kluis en alle objecten hierin, met inbegrip van certificaten, sleutels en geheimen. Kan geen sleutel kluis resources beheren of roltoewijzingen beheren. Werkt alleen voor sleutel kluizen die gebruikmaken van het machtigings model ' Azure op rollen gebaseerd toegangs beheer '. | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
| Key Vault-certificerings medewerker | Acties uitvoeren op de certificaten van een sleutel kluis, met uitzonde ring van machtigingen beheren. Werkt alleen voor sleutel kluizen die gebruikmaken van het machtigings model ' Azure op rollen gebaseerd toegangs beheer '. | a4417e6f-fecd-4de8-b567-7b0420556985 |
| Crypto medewerker Key Vault | Acties uitvoeren op de sleutels van een sleutel kluis, met uitzonde ring van machtigingen beheren. Werkt alleen voor sleutel kluizen die gebruikmaken van het machtigings model ' Azure op rollen gebaseerd toegangs beheer '. | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
| Versleutelings gebruiker van crypto grafie-service Key Vault | Lees de meta gegevens van sleutels en voer terugloop/uitlopende bewerkingen uit. Werkt alleen voor sleutel kluizen die gebruikmaken van het machtigings model ' Azure op rollen gebaseerd toegangs beheer '. | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
| Gebruiker van crypto grafie Key Vault  | Voer cryptografische bewerkingen uit met behulp van sleutels. Werkt alleen voor sleutel kluizen die gebruikmaken van het machtigings model ' Azure op rollen gebaseerd toegangs beheer '. | 12338af0-0e69-4776-bea7-57ae8d297424 |
| Key Vault lezer | Lees de meta gegevens van sleutel kluizen en de bijbehorende certificaten, sleutels en geheimen. Kan geen gevoelige waarden lezen, zoals geheime inhoud of sleutel materiaal. Werkt alleen voor sleutel kluizen die gebruikmaken van het machtigings model ' Azure op rollen gebaseerd toegangs beheer '. | 21090545-7ca7-4776-b22c-e363652d74d2 |
| Functionaris voor Key Vault geheimen| Acties uitvoeren op de geheimen van een sleutel kluis, met uitzonde ring van machtigingen beheren. Werkt alleen voor sleutel kluizen die gebruikmaken van het machtigings model ' Azure op rollen gebaseerd toegangs beheer '. | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
| Key Vault geheimen gebruiker | Geheime inhoud lezen. Werkt alleen voor sleutel kluizen die gebruikmaken van het machtigings model ' Azure op rollen gebaseerd toegangs beheer '. | 4633458b-17de-408a-b874-0445c86b69e6 |

Zie [ingebouwde rollen van Azure](../../role-based-access-control/built-in-roles.md)voor meer informatie over de definities van ingebouwde rollen van Azure.

## <a name="using-azure-rbac-secret-key-and-certificate-permissions-with-key-vault"></a>Azure RBAC-geheim, sleutel en certificaat machtigingen gebruiken met Key Vault

Het nieuwe Azure RBAC-machtigings model voor sleutel kluis biedt een alternatief voor het model voor het toegangs beleid van de kluis. 

### <a name="prerequisites"></a>Vereisten

Als u roltoewijzingen wilt toevoegen, hebt u het volgende nodig:

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
- Machtigingen voor `Microsoft.Authorization/roleAssignments/write` en `Microsoft.Authorization/roleAssignments/delete`, zoals [Beheerder van gebruikerstoegang](../../role-based-access-control/built-in-roles.md#user-access-administrator) of [Eigenaar](../../role-based-access-control/built-in-roles.md#owner)

### <a name="enable-azure-rbac-permissions-on-key-vault"></a>Azure RBAC-machtigingen inschakelen voor Key Vault

> [!NOTE]
> Voor het wijzigen van het machtigings model is de machtiging ' micro soft. Authorization/roleAssignments/write ' vereist, die deel uitmaakt van beheerders rollen van de [eigenaar](../../role-based-access-control/built-in-roles.md#owner) en de [gebruikers toegang](../../role-based-access-control/built-in-roles.md#user-access-administrator) . Klassieke abonnements beheerders rollen als ' service beheerder ' en ' mede beheerder ' worden niet ondersteund.

1.  Azure RBAC-machtigingen voor nieuwe sleutel kluis inschakelen:

    ![Azure RBAC-machtigingen inschakelen: nieuwe kluis](../media/rbac/image-1.png)

2.  Azure RBAC-machtigingen voor de bestaande sleutel kluis inschakelen:

    ![Azure RBAC-machtigingen inschakelen-bestaande kluis](../media/rbac/image-2.png)

> [!IMPORTANT]
> Als Azure RBAC-machtigings model wordt ingesteld, worden alle machtigingen voor toegangs beleid ongeldig gemaakt. Dit kan leiden tot storingen wanneer gelijkwaardige Azure-rollen niet zijn toegewezen.

### <a name="assign-role"></a>Rol toewijzen

> [!Note]
> Het is raadzaam om de unieke rol-ID in plaats van de rolnaam in scripts te gebruiken. Als de naam van een rol wordt gewijzigd, blijven de scripts daarom gewoon werken. In deze document rolnaam wordt alleen gebruikt voor de Lees baarheid.

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

In de Azure Portal is het scherm voor het toewijzen van Azure-functies beschikbaar voor alle resources op het tabblad toegangs beheer (IAM).

![Het tabblad roltoewijzing-(IAM)](../media/rbac/image-3.png)

### <a name="resource-group-scope-role-assignment"></a>Roltoewijzing van resource groeps bereik

1.  Ga naar de resource groep voor de sleutel kluis.
    ![Roltoewijzing-resource groep](../media/rbac/image-4.png)

2.  Klik op toegangs beheer (IAM) \> add-rol toewijzing \> toevoegen

3.  De rol ' Key Vault lezer ' van Key Vault lezer maken voor de huidige gebruiker

    ![Rol toevoegen-resource groep](../media/rbac/image-5.png)

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

Boven de roltoewijzing biedt u de mogelijkheid om sleutel kluis objecten in sleutel kluis weer te geven.

### <a name="key-vault-scope-role-assignment"></a>Toewijzing van Key Vault bereik

1. Ga naar \> het tabblad Key Vault toegangs beheer (IAM)

2. Klik op add-rol toewijzing \> toevoegen

3. Sleutel geheimen van de rol ' Key Vault geheimen ' maken voor de huidige gebruiker.

    ![Roltoewijzing-sleutel kluis](../media/rbac/image-6.png)

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

Nadat u de functie toewijzing hebt gemaakt, kunt u geheimen maken/bijwerken/verwijderen.

4. Maak een nieuw geheim (geheimen \> + genereren/importeren) voor het testen van de roltoewijzing op geheim niveau.

    ![Functie sleutel kluis toevoegen](../media/rbac/image-7.png)

### <a name="secret-scope-role-assignment"></a>Toewijzing van de rol van een geheim bereik

1. Open een van eerder gemaakte geheimen, Bekijk overzicht en toegangs beheer (IAM) 

2. Klik op het tabblad toegangs beheer (IAM)

    ![Roltoewijzing-geheim](../media/rbac/image-8.png)

3. Maak de rol van belang rijke geheimen (Key Vault geheimen) voor de huidige gebruiker, op dezelfde manier als voor de Key Vault.

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
> Browsers gebruiken caching en pagina vernieuwen is vereist nadat roltoewijzingen zijn verwijderd.<br>
> Enkele minuten toestaan dat roltoewijzingen worden vernieuwd

1. Valideer het toevoegen van nieuw geheim zonder de rol ' Key Vault geheimen ambtenaar ' op het niveau van de sleutel kluis.

Ga naar het tabblad IAM (Key kluis Access Control) en verwijder de functie toewijzing Key Vault geheimen voor deze resource.

![Toewijzings sleutel kluis verwijderen](../media/rbac/image-9.png)

Navigeer naar het eerder gemaakte geheim. U kunt alle geheime eigenschappen weer geven.

![Geheime weer gave met toegang](../media/rbac/image-10.png)

Nieuw geheim maken (geheimen \> + genereren/importeren) moet de volgende fout weer geven:

   ![Nieuw geheim maken](../media/rbac/image-11.png)

2.  Valideer het geheim bewerken zonder de rol ' Key Vaulte geheime ambtenaar ' op geheim niveau.

-   Ga naar het tabblad eerder gemaakte geheime Access Control (IAM) en verwijder de roltoewijzing van Key Vault geheimen voor deze resource.

-   Navigeer naar het eerder gemaakte geheim. U kunt geheime eigenschappen weer geven.

   ![Geheime weer gave zonder toegang](../media/rbac/image-12.png)

3. Valideer geheimen die zijn gelezen zonder de rol van lezer op het niveau van de sleutel kluis.

-   Ga naar het tabblad toegangs beheer van de sleutel kluis resource groep en verwijder de roltoewijzing Key Vault lezer.

-   Als u naar het tabblad geheimen van de sleutel kluis navigeert, wordt de volgende fout weer gegeven:

   ![Tabblad geheim-fout](../media/rbac/image-13.png)

### <a name="creating-custom-roles"></a>Aangepaste rollen maken 

[opdracht AZ Role definition Create](/cli/azure/role/definition#az-role-definition-create)

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

-   Latentie van roltoewijzingen: bij huidige verwachte prestaties duurt het Maxi maal tien minuten (600 seconden) nadat roltoewijzingen zijn gewijzigd voor het Toep assen van de rol

## <a name="learn-more"></a>Lees meer

- [Overzicht van Azure RBAC](../../role-based-access-control/overview.md)
- [Zelf studie voor aangepaste rollen](../../role-based-access-control/tutorial-custom-role-cli.md)