---
title: Een Log Analytics werkruimte verplaatsen in Azure Monitor | Microsoft Docs
description: Meer informatie over het verplaatsen van uw Log Analytics-werk ruimte naar een ander abonnement of een andere resource groep.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/12/2020
ms.openlocfilehash: 8f48ed1aa7422d6925c3a7b0ad30b59a479e4614
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034945"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Een Log Analytics-werk ruimte verplaatsen naar een ander abonnement of een andere resource groep

In dit artikel leert u de stappen voor het verplaatsen van Log Analytics werk ruimte naar een andere resource groep of een ander abonnement in dezelfde regio. U vindt meer informatie over het verplaatsen van Azure-resources via de Azure Portal, Power shell, de Azure CLI of de REST API. bij [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md). 

> [!IMPORTANT]
> U kunt een werk ruimte niet verplaatsen naar een andere regio.

## <a name="verify-active-directory-tenant"></a>Active Directory Tenant verifiëren
De bron-en doel abonnementen van de werk ruimte moeten zich in dezelfde Azure Active Directory Tenant bevinden. Gebruik Azure PowerShell om te controleren of beide abonnementen dezelfde Tenant-ID hebben.

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="workspace-move-considerations"></a>Overwegingen bij het verplaatsen van de werk ruimte
- Beheerde oplossingen die in de werk ruimte zijn geïnstalleerd, worden verplaatst met de bewerking voor het verplaatsen van Log Analytics werk ruimte. 
- Werkruimte sleutels (primair en secundair) worden opnieuw gegenereerd met de bewerking voor het verplaatsen van de werk ruimte. Als u een kopie van uw werkruimte sleutels in de sleutel kluis bijwerkt, moet u deze bijwerken met de nieuwe sleutels die zijn gegenereerd nadat de werk ruimte is verplaatst. 
- Verbonden agents blijven verbonden en blijven gegevens verzenden naar de werk ruimte na de verplaatsing. 
- Omdat voor de verplaatsings bewerking vereist is dat er geen gekoppelde services zijn uit de werk ruimte, moeten oplossingen die afhankelijk zijn van die koppeling worden verwijderd zodat de werk ruimte kan worden verplaatst. Oplossingen die moeten worden verwijderd voordat u uw Automation-account kunt ontkoppelen:
  - Updatebeheer
  - Wijzigingen bijhouden
  - VM's starten/stoppen buiten kantooruren
  - Azure Security Center

>[!IMPORTANT]
> **Azure Sentinel-klanten**
> - Wanneer Azure Sentinel op een werk ruimte wordt geïmplementeerd, wordt de werk ruimte momenteel niet ondersteund door een andere resource groep of een ander abonnement. 
> - Als u de werkruimte al hebt verplaatst, moet u alle actieve regels onder **Analyses** uitschakelen en na vijf minuten opnieuw inschakelen. Dit moet in de meeste gevallen een efficiënte oplossing zijn, hoewel het niet wordt ondersteund en niet wordt uitgevoerd op uw eigen risico.
> 
> **Waarschuwingen opnieuw maken**
> - Alle waarschuwingen moeten na een verplaatsing opnieuw worden gemaakt, omdat de machtigingen zijn gebaseerd op de Azure-Resource-ID van de werk ruimte, die verandert tijdens het verplaatsen van een werk ruimte.
>
> **Resource paden bijwerken**
> - Nadat een werk ruimte is verplaatst, moeten alle Azure-of externe resources die naar de werk ruimte verwijzen, worden gecontroleerd en bijgewerkt om naar het nieuwe bronpad te verwijzen.
> 
>   *Voorbeelden:*
>   - [Azure Monitor waarschuwings regels](../alerts/alerts-resource-move.md)
>   - Externe toepassingen
>   - Aangepast uitvoeren van scripts
>

### <a name="delete-solutions-in-azure-portal"></a>Oplossingen in Azure Portal verwijderen
Gebruik de volgende procedure om de oplossingen te verwijderen met behulp van de Azure Portal:

1. Open het menu voor de resource groep waarin alle oplossingen zijn geïnstalleerd.
2. Selecteer de oplossingen die u wilt verwijderen.
3. Klik op **resources verwijderen** en bevestig de resources die moeten worden verwijderd door te klikken op **verwijderen**.

![Oplossingen verwijderen](media/move-workspace/delete-solutions.png)

### <a name="delete-using-powershell"></a>Verwijderen met Power shell

Als u de oplossingen wilt verwijderen met behulp van Power shell, gebruikt u de cmdlet [Remove-AzResource](/powershell/module/az.resources/remove-azresource) , zoals weer gegeven in het volgende voor beeld:

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

### <a name="remove-alert-rules-for-startstop-vms-solution"></a>Waarschuwings regels verwijderen voor de oplossing Vm's starten/stoppen
Als u de oplossing voor het **starten/stoppen van vm's** wilt verwijderen, moet u ook de waarschuwings regels verwijderen die zijn gemaakt door de oplossing. Gebruik de volgende procedure in de Azure Portal om deze regels te verwijderen.

1. Open het menu **monitor** en selecteer vervolgens **waarschuwingen**.
2. Klik op **waarschuwings regels beheren**.
3. Selecteer de volgende drie waarschuwings regels en klik vervolgens op **verwijderen**.

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![Regels verwijderen](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Automation-account ontkoppelen
Gebruik de volgende procedure om het Automation-account te ontkoppelen van de werk ruimte met behulp van de Azure Portal:

1. Open het menu **Automation-accounts** en selecteer vervolgens het account dat u wilt verwijderen.
2. Selecteer in de sectie **Verwante resources** van het menu de optie **gekoppelde werk ruimte**. 
3. Klik op **werk ruimte ontkoppelen** om de werk ruimte te ontkoppelen van uw Automation-account.

    ![Werkruimte ontkoppelen](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>Uw werk ruimte verplaatsen

### <a name="azure-portal"></a>Azure Portal
Gebruik de volgende procedure om uw werk ruimte te verplaatsen met behulp van de Azure Portal:

1. Open het menu **log Analytics werk ruimten** en selecteer vervolgens uw werk ruimte.
2. Klik op de pagina **overzicht** op **wijzigen** naast de **resource groep** of het **abonnement**.
3. Er wordt een nieuwe pagina geopend met een lijst met resources die betrekking hebben op de werk ruimte. Selecteer de resources die u wilt verplaatsen naar hetzelfde doel abonnement en dezelfde resource groep als de werk ruimte. 
4. Selecteer een doel **abonnement** en **resource groep**. Als u de werk ruimte naar een andere resource groep in hetzelfde abonnement verplaatst, wordt de optie **abonnement** niet weer geven.
5. Klik op **OK** om de werk ruimte en de geselecteerde resources te verplaatsen.

    ![Scherm afbeelding toont het deel venster Overzicht in de werk ruimte Log Analytics met opties voor het wijzigen van de resource groep en de naam van het abonnement.](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
Als u uw werk ruimte wilt verplaatsen met behulp van Power shell, gebruikt u de [Move-AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource) zoals in het volgende voor beeld:

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```

> [!IMPORTANT]
> Nadat de bewerking is verplaatst, moeten verwijderde oplossingen en de koppeling naar het Automation-account opnieuw worden geconfigureerd om de vorige status van de werk ruimte te herstellen.


## <a name="next-steps"></a>Volgende stappen
- Zie [ondersteuning voor het verplaatsen van resources voor bronnen](../../azure-resource-manager/management/move-support-resources.md)voor een lijst met resources die kunnen worden verplaatst.
