---
title: Power shell gebruiken voor het beheren van Azure Service Bus resources | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u met behulp van Azure PowerShell-module Service Bus entiteiten kunt maken en beheren (naam ruimten, wacht rijen, onderwerpen, abonnementen).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: b6439deb2b86c2ea5b50fe3bdbad89a0875b2acc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88065740"
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>Power shell gebruiken voor het beheren van Service Bus resources

Microsoft Azure PowerShell is een script omgeving die u kunt gebruiken om de implementatie en het beheer van Azure-Services te beheren en te automatiseren. In dit artikel wordt beschreven hoe u de [Service Bus Resource Manager Power shell-module](/powershell/module/az.servicebus) gebruikt voor het inrichten en beheren van service bus entiteiten (naam ruimten, wacht rijen, onderwerpen en abonnementen) met behulp van een lokale Azure PowerShell-console of een script.

U kunt ook Service Bus entiteiten beheren met Azure Resource Manager sjablonen. Zie voor meer informatie het artikel [Service Bus resources maken met behulp van Azure Resource Manager sjablonen](service-bus-resource-manager-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u aan de volgende vereisten voldoen:

* Een Azure-abonnement. Zie voor meer informatie over het verkrijgen van een abonnement, [aankoop opties][purchase options], [leden aanbiedingen][member offers]of [gratis account][free account].
* Een computer met Azure PowerShell. Zie [aan de slag met Azure PowerShell-cmdlets](/powershell/azure/get-started-azureps)voor instructies.
* Een algemene uitleg van Power shell-scripts, NuGet-pakketten en de .NET Framework.

## <a name="get-started"></a>Aan de slag

De eerste stap is het gebruik van Power shell om u aan te melden bij uw Azure-account en Azure-abonnement. Volg de instructies in aan de [slag met Azure PowerShell-cmdlets](/powershell/azure/get-started-azureps) om u aan te melden bij uw Azure-account en om de resources in uw Azure-abonnement op te halen en te openen.

## <a name="provision-a-service-bus-namespace"></a>Een Service Bus naam ruimte inrichten

Bij het werken met Service Bus naam ruimten kunt u de cmdlets [Get-AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace), [New-AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace), [Remove-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace)en [set-AzServiceBusNamespace](/powershell/module/az.servicebus/set-azservicebusnamespace) gebruiken.

In dit voor beeld worden een aantal lokale variabelen in het script gemaakt. `$Namespace` en `$Location` .

* `$Namespace` is de naam van de naam ruimte van Service Bus waarmee we willen werken.
* `$Location` Hiermee wordt het Data Center geïdentificeerd waarin de naam ruimte wordt ingericht.
* `$CurrentNamespace` slaat de referentie naam ruimte op die we ophalen (of maken).

In een werkelijk script `$Namespace` en `$Location` kunnen worden door gegeven als para meters.

Dit deel van het script doet het volgende:

1. Probeert een Service Bus naam ruimte op te halen met de opgegeven naam.
2. Als de naam ruimte wordt gevonden, wordt hiermee gerapporteerd wat er is gevonden.
3. Als de naam ruimte niet wordt gevonden, wordt de naam ruimte gemaakt en wordt de zojuist gemaakte naam ruimte opgehaald.
   
    ``` powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
                
    }
    ```

### <a name="create-a-namespace-authorization-rule"></a>Een autorisatie regel voor een naam ruimte maken

In het volgende voor beeld ziet u hoe u naam ruimte autorisatie regels beheert met behulp van de cmdlets [New-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule), [Get-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule), [set-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule)en [Remove-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule) .

```powershell
# Query to see if rule exists
$CurrentRule = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

# Check if the rule already exists or needs to be created
if ($CurrentRule)
{
    Write-Host "The $AuthRule rule already exists for the namespace $Namespace."
}
else
{
    Write-Host "The $AuthRule rule does not exist."
    Write-Host "Creating the $AuthRule rule for the $Namespace namespace..."
    New-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule -Rights @("Listen","Send")
    $CurrentRule = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
    Write-Host "The $AuthRule rule for the $Namespace namespace has been successfully created."

    Write-Host "Setting rights on the namespace"
    $authRuleObj = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

    Write-Host "Remove Send rights"
    $authRuleObj.Rights.Remove("Send")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Add Send and Manage rights to the namespace"
    $authRuleObj.Rights.Add("Send")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj
    $authRuleObj.Rights.Add("Manage")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Show value of primary key"
    $CurrentKey = Get-AzServiceBusKey -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
        
    Write-Host "Remove this authorization rule"
    Remove-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
}
```

## <a name="create-a-queue"></a>Een wachtrij maken

Als u een wachtrij of onderwerp wilt maken, voert u een controle van de naam ruimte uit met behulp van het script in de vorige sectie. Maak vervolgens de wachtrij:

```powershell
# Check if queue already exists
$CurrentQ = Get-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName

if($CurrentQ)
{
    Write-Host "The queue $QueueName already exists in the $Location region:"
}
else
{
    Write-Host "The $QueueName queue does not exist."
    Write-Host "Creating the $QueueName queue in the $Location region..."
    New-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -EnablePartitioning $True
    $CurrentQ = Get-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName
    Write-Host "The $QueueName queue in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="modify-queue-properties"></a>Wachtrij-eigenschappen wijzigen

Nadat u het script in de voor gaande sectie hebt uitgevoerd, kunt u de cmdlet [set-AzServiceBusQueue](/powershell/module/az.servicebus/set-azservicebusqueue) gebruiken om de eigenschappen van een wachtrij bij te werken, zoals in het volgende voor beeld:

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>Andere Service Bus-entiteiten inrichten

U kunt de [Service Bus Power shell-module](/powershell/module/az.servicebus) gebruiken om andere entiteiten, zoals onderwerpen en abonnementen, in te richten. Deze cmdlets zijn qua syntaxis vergelijkbaar met de cmdlets voor het maken van de wachtrij die in de vorige sectie worden getoond.

## <a name="next-steps"></a>Volgende stappen

- Bekijk [hier](/powershell/module/az.servicebus)de volledige documentatie over service bus Resource Manager Power shell-module. Op deze pagina vindt u alle beschik bare cmdlets.
- Zie het artikel [Service Bus resources maken met behulp van Azure Resource Manager sjablonen](service-bus-resource-manager-overview.md)voor meer informatie over het gebruik van Azure Resource Manager sjablonen.
- Informatie over [Service Bus .net-beheer bibliotheken](service-bus-management-libraries.md).

Er zijn een aantal alternatieve manieren om Service Bus entiteiten te beheren, zoals beschreven in deze blog berichten:

* [Service Bus-wacht rijen,-onderwerpen en-abonnementen maken met een Power shell-script](/archive/blogs/paolos/how-to-create-service-bus-queues-topics-and-subscriptions-using-a-powershell-script)
* [Een Service Bus naam ruimte en een event hub maken met een Power shell-script](/archive/blogs/paolos/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script)
* [Service Bus Power shell-scripts](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: https://azure.microsoft.com/pricing/purchase-options/
[member offers]: https://azure.microsoft.com/pricing/member-offers/
[free account]: https://azure.microsoft.com/pricing/free-trial/