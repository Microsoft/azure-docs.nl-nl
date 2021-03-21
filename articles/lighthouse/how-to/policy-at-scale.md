---
title: Azure Policy implementeren voor gedelegeerde abonnementen op de juiste schaal
description: Meer informatie over hoe u met Azure Lighthouse een beleids definitie en beleids toewijzing in meerdere tenants kunt implementeren.
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 48354c3cca7574b1d5acf71865218564591bc23e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102049777"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>Azure Policy implementeren voor gedelegeerde abonnementen op de juiste schaal

Als service provider hebt u mogelijk meerdere tenants voor klanten in [Azure Lighthouse](../overview.md). Met Azure Lighthouse kunnen service providers bewerkingen op verschillende tijdstippen in meerdere tenants tegelijk uitvoeren, waardoor beheer taken efficiënter zijn.

In dit onderwerp wordt beschreven hoe u [Azure Policy](../../governance/policy/index.yml) kunt gebruiken om een beleids definitie en beleids toewijzing te implementeren voor meerdere tenants met behulp van Power shell-opdrachten. In dit voor beeld zorgt de beleids definitie ervoor dat opslag accounts worden beveiligd door alleen HTTPS-verkeer toe te staan.

> [!TIP]
> Hoewel we in dit onderwerp naar service providers en klanten verwijzen, kunnen [bedrijven die meerdere tenants beheren](../concepts/enterprise.md) , dezelfde processen gebruiken.

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>Azure resource Graph gebruiken voor het uitvoeren van een query op de tenants van de klant

U kunt [Azure resource Graph](../../governance/resource-graph/index.yml) gebruiken om een query uit te zoeken voor alle abonnementen in de tenants van de klant die u beheert. In dit voor beeld identificeren we opslag accounts in deze abonnementen die momenteel geen HTTPS-verkeer vereisen.  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>Een beleid implementeren over meerdere tenants van de klant

In het onderstaande voor beeld ziet u hoe u een [Azure Resource Manager sjabloon](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json) gebruikt om een beleids definitie en beleids toewijzing te implementeren voor gedelegeerde abonnementen in meerdere tenants van klanten. Deze beleids definitie vereist dat alle opslag accounts gebruikmaken van HTTPS-verkeer, waardoor er geen nieuwe opslag accounts kunnen worden gemaakt die niet voldoen aan bestaande opslag accounts en zonder de instelling worden gemarkeerd als niet-compatibel.

```powershell
Write-Output "In total, there are $($ManagedSubscriptions.Count) delegated customer subscriptions to be managed"

foreach ($ManagedSub in $ManagedSubscriptions)
{
    Select-AzSubscription -SubscriptionId $ManagedSub.subscriptionId

    New-AzSubscriptionDeployment -Name mgmt `
                     -Location eastus `
                     -TemplateUri "https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json" `
                     -AsJob
}
```

> [!NOTE]
> Hoewel u beleid kunt implementeren in meerdere tenants, kunt u op dit moment geen [compatibiliteits details weer geven](../../governance/policy/how-to/determine-non-compliance.md#compliance-details) voor niet-compatibele resources in deze tenants.

## <a name="validate-the-policy-deployment"></a>De beleids implementatie valideren

Nadat u de Azure Resource Manager sjabloon hebt geïmplementeerd, kunt u controleren of de beleids definitie is toegepast door te proberen een opslag account te maken waarvoor **EnableHttpsTrafficOnly** is ingesteld op **False** in een van uw gedelegeerde abonnementen. U kunt dit opslag account niet maken vanwege de beleids toewijzing.  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent, verwijdert u de beleids definitie en toewijzing die door de implementatie is gemaakt.

```powershell
foreach ($ManagedSub in $ManagedSubscriptions)
{
    select-azsubscription -subscriptionId $ManagedSub.subscriptionId

    Remove-AzSubscriptionDeployment -Name mgmt -AsJob

    $Assignment = Get-AzPolicyAssignment | where-object {$_.Name -like "enforce-https-storage-assignment"}

    if ([string]::IsNullOrEmpty($Assignment))
    {
        Write-Output "Nothing to clean up - we're done"
    }
    else
    {

    Remove-AzPolicyAssignment -Name 'enforce-https-storage-assignment' -Scope "/subscriptions/$($ManagedSub.subscriptionId)" -Verbose

    Write-Output "Deployment has been deleted - we're done"
    }
}
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure Policy](../../governance/policy/index.yml).
- Meer informatie over [beheerervaring in meerdere tenants](../concepts/cross-tenant-management-experience.md).
- Meer informatie over het [implementeren van een beleid dat kan worden hersteld](deploy-policy-remediation.md) binnen een gedelegeerd abonnement.
