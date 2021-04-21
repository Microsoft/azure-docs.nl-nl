---
title: Een & archiveren met Azure Monitor - Azure AD-rechtenbeheer
description: Meer informatie over het archiveren van logboeken en het maken van rapporten met Azure Monitor in Azure Active Directory rechtenbeheer.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 354805b3e2b538c92ba2345df2bcd93968640068
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764383"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Logboeken en rapportage archiveren over Azure AD-rechtenbeheer in Azure Monitor

Azure AD slaat auditgebeurtenissen maximaal 30 dagen op in het auditlogboek. U kunt de controlegegevens echter langer bewaren dan de standaardretentieperiode, zoals beschreven in Hoe lang worden rapportagegegevens in [Azure AD opgeslagen?](../reports-monitoring/reference-reports-data-retention.md)door ze door te leiden naar een Azure Storage-account of door gebruik te maken van Azure Monitor. Vervolgens kunt u werkmappen en aangepaste query's en rapporten gebruiken voor deze gegevens.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Azure AD configureren voor het gebruik van Azure Monitor
Voordat u de Azure Monitor werkmappen gebruikt, moet u Azure AD configureren om een kopie van de auditlogboeken naar de Azure Monitor.

Voor het archiveren van Azure AD-auditlogboeken moet u Azure Monitor in een Azure-abonnement. Meer informatie over de vereisten en geschatte kosten voor het gebruik van Azure Monitor vindt u [in Azure AD-activiteitenlogboeken in Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md).

**Vereiste rol**: Hoofdbeheerder

1. Meld u aan bij Azure Portal gebruiker die een globale beheerder is. Zorg ervoor dat u toegang hebt tot de resourcegroep met de Azure Monitor werkruimte.
 
1. Selecteer **Azure Active Directory** klik vervolgens op **Diagnostische instellingen** onder Bewaking in het navigatiemenu links. Controleer of er al een instelling is om de auditlogboeken naar die werkruimte te verzenden.

1. Als er nog geen instelling is, klikt u op **Diagnostische instelling toevoegen.** Gebruik de instructies in het artikel [Azure AD-logboeken integreren met Azure Monitor-logboeken](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) om het Azure AD-auditlogboek naar de werkruimte Azure Monitor verzenden.

    ![Het deelvenster Diagnostische instellingen](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. Nadat het logboek is verzonden naar Azure Monitor, selecteert u **Log Analytics-werkruimten** en selecteert u de werkruimte die de Azure AD-auditlogboeken bevat.

1. Selecteer **Gebruik en geschatte kosten** en klik op **Gegevensretentie.** Wijzig de schuifregelaar in het aantal dagen dat u de gegevens wilt behouden om te voldoen aan uw controlevereisten.

    ![Deelvenster Log Analytics-werkruimten](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

1. Later kunt u de werkmap Gearchiveerde logboekdatumbereik gebruiken om het datumbereik in uw werkruimte *te* bekijken:  
    
    1. Selecteer **Azure Active Directory** klik vervolgens op **Werkmappen.** 
    
    1. Vouw de sectie **Azure Active Directory oplossen en** klik op **Gearchiveerde logboekdatumbereik.** 


## <a name="view-events-for-an-access-package"></a>Gebeurtenissen voor een toegangspakket weergeven  

Als u gebeurtenissen voor een [toegangspakket](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions) wilt bekijken, moet u toegang hebben tot de onderliggende Azure Monitor-werkruimte (zie Toegang tot logboekgegevens en werkruimten beheren in Azure Monitor voor informatie) en in een van de volgende rollen: 

- Globale beheerder  
- Beveiligingsbeheerder  
- Beveiligingslezer  
- Rapportlezer  
- Toepassingsbeheerder  

Gebruik de volgende procedure om gebeurtenissen weer te geven: 

1. Selecteer in Azure Portal de **Azure Active Directory** klik vervolgens op **Werkmappen.** Als u slechts één abonnement hebt, gaat u verder met stap 3. 

1. Als u meerdere abonnementen hebt, selecteert u het abonnement dat de werkruimte bevat.  

1. Selecteer de werkmap met de naam *Access Package Activity*. 

1. Selecteer in die werkmap een tijdsbereik (wijzig in Alle indien niet zeker) en selecteer een toegangspakket-id in de vervolgkeuzelijst met alle toegangspakketten die in dat tijdsbereik activiteit hadden.  De gebeurtenissen met betrekking tot het toegangspakket die zijn opgetreden tijdens het geselecteerde tijdsbereik, worden weergegeven.  

    ![Gebeurtenissen van toegangspakket weergeven](./media/entitlement-management-logs-and-reporting/view-events-access-package.png) 

    Elke rij bevat de tijd, de id van het toegangspakket, de naam van de bewerking, de object-id, DEN en de weergavenaam van de gebruiker die de bewerking heeft gestart.  Aanvullende details zijn opgenomen in JSON.   


## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Aangepaste query Azure Monitor maken met behulp van de Azure Portal
U kunt uw eigen query's maken voor Azure AD-controlegebeurtenissen, waaronder rechtenbeheergebeurtenissen.  

1. Klik Azure Active Directory van de Azure Portal logboeken  onder de sectie Bewaking in het navigatiemenu links om een nieuwe querypagina te maken.

1. Uw werkruimte moet linksboven op de querypagina worden weergegeven. Als u meerdere Azure Monitor hebt en de werkruimte die u gebruikt voor het opslaan van Azure AD-controlegebeurtenissen niet wordt weergegeven, klikt u op **Bereik selecteren.** Selecteer vervolgens het juiste abonnement en de juiste werkruimte.

1. Verwijder vervolgens in het tekstgebied van de query de tekenreeks 'search *' en vervang deze door de volgende query:

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. Klik vervolgens op **Uitvoeren.** 

    ![Klik op Uitvoeren om de query te starten](./media/entitlement-management-logs-and-reporting/run-query.png)

In de tabel worden standaard de auditlogboekgebeurtenissen voor rechtenbeheer van het afgelopen uur weergegeven. U kunt de instelling 'Tijdsbereik' wijzigen om oudere gebeurtenissen weer te geven. Als u deze instelling echter verandert, worden alleen gebeurtenissen die zijn opgetreden nadat Azure AD is geconfigureerd voor het verzenden van gebeurtenissen naar Azure Monitor.

Als u wilt weten wat de oudste en nieuwste controlegebeurtenissen in Azure Monitor, gebruikt u de volgende query:

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Zie Interpret the Azure AD audit logs schema in Azure Monitor [(Het Azure AD-auditlogboekenschema](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)interpreteren in Azure Monitor) voor meer informatie over de kolommen die zijn opgeslagen voor controlegebeurtenissen in Azure Monitor.

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Aangepaste Azure Monitor maken met behulp van Azure PowerShell

U kunt logboeken openen via PowerShell nadat u Azure AD hebt geconfigureerd voor het verzenden van logboeken naar Azure Monitor. Verzend vervolgens query's vanuit scripts of de PowerShell-opdrachtregel, zonder dat u een globale beheerder hoeft te zijn in de tenant. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>Zorg ervoor dat de gebruiker of service-principal de juiste roltoewijzing heeft

Zorg ervoor dat u, de gebruiker of service-principal die wordt geverifieerd bij Azure AD, de juiste Azure-rol heeft in de Log Analytics-werkruimte. De rolopties zijn Log Analytics-lezer of Inzender voor Log Analytics. Als u al een van deze rollen hebt, gaat u verder met [Log Analytics-id ophalen met één Azure-abonnement.](#retrieve-log-analytics-id-with-one-azure-subscription)

Ga als volgt te werk om de roltoewijzing in te stellen en een query te maken:

1. Zoek in Azure Portal de [Log Analytics-werkruimte](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
).

1. Selecteer **Access Control (IAM)**.

1. Klik vervolgens op **Toevoegen om** een roltoewijzing toe te voegen.

    ![Een roltoewijzing toevoegen](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Een Azure PowerShell installeren

Zodra u de juiste roltoewijzing hebt, start u PowerShell en installeert u de [Azure PowerShell-module](/powershell/azure/install-az-ps) (als u dat nog niet hebt gedaan) door het volgende te typen:

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
U bent nu klaar om u te verifiëren bij Azure AD en de id op te halen van de Log Analytics-werkruimte die u wilt opvragen.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>Log Analytics-id ophalen met één Azure-abonnement
Als u slechts één Azure-abonnement en één Log Analytics-werkruimte hebt, typt u het volgende om te verifiëren bij Azure AD, verbinding te maken met dat abonnement en die werkruimte op te halen:
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>Log Analytics-id met meerdere Azure-abonnementen ophalen

 [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) werkt in één abonnement tegelijk. Als u dus meerdere Azure-abonnementen hebt, moet u ervoor zorgen dat u verbinding maakt met de werkruimte met de Log Analytics-werkruimte met de Azure AD-logboeken. 
 
 Met de volgende cmdlets wordt een lijst met abonnementen weergegeven en wordt de id van het abonnement met de Log Analytics-werkruimte gevonden:
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
U kunt uw PowerShell-sessie opnieuw autoriëren en koppelen aan dat abonnement met behulp van een opdracht zoals `Connect-AzAccount –Subscription $subs[0].id` . Zie Aanmelden met Azure PowerShell voor meer informatie over het verifiëren bij Azure vanuit PowerShell, inclusief [niet-interactief.](/powershell/azure/authenticate-azureps)

Als u meerdere Log Analytics-werkruimten in dat abonnement hebt, retourneert de cmdlet [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) de lijst met werkruimten. Vervolgens vindt u de logboeken van Azure AD. Het veld dat door deze cmdlet wordt geretourneerd, is hetzelfde als de waarde van de 'werkruimte-id' die wordt weergegeven in de Azure Portal in het overzicht van de `CustomerId` Log Analytics-werkruimte.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>De query verzenden naar de Log Analytics-werkruimte
Als u een werkruimte hebt geïdentificeerd, kunt u [Invoke-AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) gebruiken om een Kusto-query naar die werkruimte te verzenden. Deze query's zijn geschreven in [Kusto-querytaal](/azure/kusto/query/).
 
U kunt bijvoorbeeld het datumbereik van de controlegebeurtenisrecords ophalen uit de Log Analytics-werkruimte, met PowerShell-cmdlets voor het verzenden van een query zoals:
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

U kunt ook rechtenbeheergebeurtenissen ophalen met behulp van een query zoals:

```azurepowershell
$bQuery = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>Volgende stappen:
- [Interactieve rapporten maken met Azure Monitor werkmappen](../../azure-monitor/visualize/workbooks-overview.md)
