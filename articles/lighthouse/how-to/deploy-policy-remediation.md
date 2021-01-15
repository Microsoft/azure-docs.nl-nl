---
title: Beleid implementeren dat kan worden hersteld
description: Als u beleid wilt implementeren dat gebruikmaakt van een herstel taak via Azure Lighthouse, moet u een beheerde identiteit maken in de Tenant van de klant.
ms.date: 01/14/2021
ms.topic: how-to
ms.openlocfilehash: 01070133241117596bdf2b8e1e7c3fa101fc656c
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98233879"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>Een beleid implementeren dat kan worden hersteld binnen een gedelegeerd abonnement

Met [Azure Lighthouse](../overview.md) kunnen service providers beleids definities maken en bewerken binnen een gedelegeerd abonnement. Als u echter beleid wilt implementeren dat gebruikmaakt van een [herstel taak](../../governance/policy/how-to/remediate-resources.md) (dat wil zeggen beleid met het [deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) of [wijzigings](../../governance/policy/concepts/effects.md#modify) effect), moet u een [beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md) maken in de Tenant van de klant. Deze beheerde identiteit kan worden gebruikt door Azure Policy om de sjabloon in het beleid te implementeren. Er zijn stappen vereist om dit scenario in te scha kelen, zowel wanneer u de klant voorbereidt voor Azure gedelegeerd resource beheer als u het beleid zelf implementeert.

> [!TIP]
> Hoewel we in dit onderwerp naar service providers en klanten verwijzen, kunnen [bedrijven die meerdere tenants beheren](../concepts/enterprise.md) , dezelfde processen gebruiken.

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>Een gebruiker maken die rollen kan toewijzen aan een beheerde identiteit in de Tenant van de klant

Wanneer u een klant onboardt naar Azure Lighthouse, gebruikt u een [Azure Resource Manager sjabloon](onboard-customer.md#create-an-azure-resource-manager-template) samen met een parameter bestand om autorisaties te definiëren die toegang verlenen tot gedelegeerde resources in de Tenant van de klant. Elke autorisatie geeft een **principalId** op die overeenkomt met een Azure AD-gebruiker,-groep of Service-Principal in de beheer Tenant en een **roledefinitionid hebben** die overeenkomt met de [ingebouwde Azure-rol](../../role-based-access-control/built-in-roles.md) die wordt toegekend.

Als u een **principalId** wilt toestaan een beheerde identiteit in de Tenant van de klant te maken, moet u de **roledefinitionid hebben** ervan instellen op de beheerder van de **gebruikers toegang**. Hoewel deze rol niet algemeen wordt ondersteund, kan deze worden gebruikt in dit specifieke scenario, zodat gebruikers accounts met deze machtiging een of meer specifieke ingebouwde rollen aan beheerde identiteiten kunnen toewijzen. Deze rollen worden gedefinieerd in de eigenschap **delegatedRoleDefinitionIds** en kunnen alle [Ondersteunde ingebouwde rollen van Azure](../concepts/tenants-users-roles.md#role-support-for-azure-lighthouse) bevatten, met uitzonde ring van de beheerder of eigenaar van de gebruikers toegang.

Nadat de klant onboarding is uitgevoerd, kan de **principalId** die in deze autorisatie is gemaakt, deze ingebouwde rollen toewijzen aan beheerde identiteiten in de Tenant van de klant. Ze hebben echter geen andere machtigingen die normaal gesp roken zijn gekoppeld aan de rol beheerder van gebruikers toegang.

In het onderstaande voor beeld ziet u een **principalId** die de rol beheerder voor gebruikers toegang heeft. Deze gebruiker kan twee ingebouwde rollen toewijzen aan beheerde identiteiten in de Tenant van de klant: Inzender en Log Analytics Inzender.

```json
{
    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
    "principalIdDisplayName": "Policy Automation Account",
    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "delegatedRoleDefinitionIds": [
         "b24988ac-6180-42a0-ab88-20f7382dd24c",
         "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
    ]
}
```

## <a name="deploy-policies-that-can-be-remediated"></a>Beleid implementeren dat kan worden hersteld

Zodra u de gebruiker met de vereiste machtigingen hebt gemaakt, zoals hierboven wordt beschreven, kan die gebruiker beleids regels implementeren die gebruikmaken van herstel taken binnen gedelegeerde klant abonnementen.

Stel bijvoorbeeld dat u Diagnostische gegevens wilt inschakelen op Azure Key Vault resources in de Tenant van de klant, zoals wordt geïllustreerd in dit voor [beeld](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring). Een gebruiker in de Tenant beheren met de juiste machtigingen (zoals hierboven beschreven) implementeert een [Azure Resource Manager sjabloon](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json) om dit scenario in te scha kelen.

Houd er rekening mee dat het maken van de beleids toewijzing voor gebruik met een gedelegeerd abonnement op dit moment moet worden uitgevoerd via Api's, niet in de Azure Portal. Wanneer u dit doet, moet de **apiVersion** worden ingesteld op **2019-04-01-preview**, inclusief de nieuwe eigenschap **delegatedManagedIdentityResourceId** . Met deze eigenschap kunt u een beheerde identiteit toevoegen die zich bevindt in de Tenant van de klant (in een abonnement of resource groep die is geboardd naar Azure Lighthouse).

In het volgende voor beeld ziet u een roltoewijzing met een **delegatedManagedIdentityResourceId**.

```json
"type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2019-04-01-preview",
            "name": "[parameters('rbacGuid')]",
            "dependsOn": [
                "[variables('policyAssignment')]"
            ],
            "properties": {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', variables('rbacContributor'))]",
                "principalType": "ServicePrincipal",
                "delegatedManagedIdentityResourceId": "[concat(subscription().id, '/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment'))]",
                "principalId": "[toLower(reference(concat('/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment')), '2018-05-01', 'Full' ).identity.principalId)]"
            }
```

> [!TIP]
> Een [vergelijkbaar voor beeld](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) is beschikbaar om te laten zien hoe u een beleid kunt implementeren waarmee een tag (met behulp van het wijzigings effect) wordt toegevoegd aan een gedelegeerd abonnement.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure Policy](../../governance/policy/index.yml).
- Meer informatie over [beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md).
