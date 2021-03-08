---
title: '& rapport archiveren met Azure Monitor-Azure AD-rechts beheer'
description: Meer informatie over het archiveren van Logboeken en het maken van rapporten met Azure Monitor in Azure Active Directory rechten beheer.
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
ms.openlocfilehash: b635a83c5ea1378a525ff607e5c6f2725337ad1a
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102453033"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Logboeken en rapporten archiveren op het beheer van rechten van Azure AD in Azure Monitor

Azure AD slaat controle gebeurtenissen gedurende Maxi maal 30 dagen op in het audit logboek. U kunt de controle gegevens langer bewaren dan de standaard retentie periode, die wordt beschreven in [hoe lang Azure AD Store-rapport gegevens worden gerapporteerd?](../reports-monitoring/reference-reports-data-retention.md)door deze te routeren naar een Azure Storage account of Azure monitor te gebruiken. U kunt vervolgens werkmappen en aangepaste query's en rapporten op deze gegevens gebruiken.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Azure AD configureren voor het gebruik van Azure Monitor
Voordat u de Azure Monitor werkmappen kunt gebruiken, moet u Azure AD configureren voor het verzenden van een kopie van de audit logboeken naar Azure Monitor.

Voor het archiveren van Azure AD-controle logboeken moet u Azure Monitor in een Azure-abonnement hebben. Meer informatie over de vereisten en geschatte kosten van het gebruik van Azure Monitor in [Azure AD-activiteiten Logboeken vindt u in azure monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md).

**Vereiste rol**: Hoofdbeheerder

1. Meld u aan bij de Azure Portal als een gebruiker die een globale beheerder is. Zorg ervoor dat u toegang hebt tot de resource groep met de Azure Monitor-werk ruimte.
 
1. Selecteer **Azure Active Directory** Klik op **Diagnostische instellingen** onder bewaking in het navigatie menu links. Controleer of er al een instelling is voor het verzenden van de audit logboeken naar die werk ruimte.

1. Als er nog geen instelling is, klikt u op **Diagnostische instelling toevoegen**. Volg de instructies in het artikel [Azure AD-logboeken integreren met Azure monitor-logboeken](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) om het Azure AD-controle logboek te verzenden naar de Azure monitor-werk ruimte.

    ![Het deelvenster Diagnostische instellingen](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. Nadat het logboek naar Azure Monitor is verzonden, selecteert u **log Analytics werk ruimten** en selecteert u de werk ruimte die de Azure AD-controle Logboeken bevat.

1. Selecteer **gebruik en geschatte kosten** en klik op **gegevens retentie**. Wijzig de schuif regelaar in het aantal dagen dat u de gegevens wilt houden om te voldoen aan uw controle vereisten.

    ![Deel venster Log Analytics-werk ruimten](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

1. Als u later het datum bereik in uw werk ruimte wilt zien, kunt u de werkmap voor het *Archief datums bereik* gebruiken:  
    
    1. Selecteer **Azure Active Directory** klik vervolgens op **werkmappen**. 
    
    1. Vouw de sectie **Azure Active Directory probleem oplossing** uit en klik op **datum bereik gearchiveerd logboek**. 


## <a name="view-events-for-an-access-package"></a>Gebeurtenissen voor een toegangs pakket weer geven  

Als u gebeurtenissen voor een toegangs pakket wilt weer geven, moet u toegang hebben tot de onderliggende Azure monitor-werk ruimte (Zie [toegang beheren voor logboek gegevens en werk ruimten in azure monitor](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions) voor informatie) en in een van de volgende rollen: 

- Globale beheerder  
- Beveiligingsbeheerder  
- Beveiligingslezer  
- Rapport lezer  
- Toepassings beheerder  

Gebruik de volgende procedure om gebeurtenissen weer te geven: 

1. Selecteer in de Azure Portal **Azure Active Directory** klik vervolgens op **werkmappen**. Als u slechts één abonnement hebt, gaat u verder met stap 3. 

1. Als u meerdere abonnementen hebt, selecteert u het abonnement met de werk ruimte.  

1. Selecteer de werkmap met de naam *Access-pakket activiteit*. 

1. Selecteer in die werkmap een tijds bereik (wijzigen in **alle** als dit niet zeker is) en selecteer een toegangs pakket-id in de vervolg keuzelijst van alle toegangs pakketten met activiteit tijdens dat tijds bereik. De gebeurtenissen met betrekking tot het toegangs pakket dat is opgetreden tijdens het geselecteerde tijds bereik, worden weer gegeven.  

    ![Access-pakket gebeurtenissen weer geven](./media/entitlement-management-logs-and-reporting/view-events-access-package.png) 

    Elke rij bevat de tijd, toegangs pakket-id, de naam van de bewerking, de object-id, UPN en de weergave naam van de gebruiker die de bewerking heeft gestart.  Meer informatie vindt u in JSON.   


## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Aangepaste Azure Monitor query's maken met behulp van de Azure Portal
U kunt uw eigen query's maken voor Azure AD-controle gebeurtenissen, inclusief rechten beheer gebeurtenissen.  

1. Klik in Azure Active Directory van de Azure Portal op **Logboeken** onder het gedeelte bewaking in het navigatie menu aan de linkerkant om een nieuwe query pagina te maken.

1. Uw werk ruimte wordt weer gegeven in de linkerbovenhoek van de query-pagina. Als u meerdere Azure Monitor-werk ruimten hebt en de werk ruimte die u gebruikt voor het opslaan van Azure AD-controle gebeurtenissen niet wordt weer gegeven, klikt u op **bereik selecteren**. Selecteer vervolgens het juiste abonnement en de gewenste werk ruimte.

1. Verwijder vervolgens in het tekst gebied query de teken reeks "Search *" en vervang deze door de volgende query:

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. Klik vervolgens op **uitvoeren**. 

    ![Klik op uitvoeren om de query te starten](./media/entitlement-management-logs-and-reporting/run-query.png)

In de tabel worden de gebeurtenissen voor het controle logboek van het laatste uur standaard weer gegeven voor het beheer van rechten. U kunt de instelling ' tijds bereik ' wijzigen om oudere gebeurtenissen weer te geven. Als u deze instelling wijzigt, worden echter alleen gebeurtenissen weer gegeven die zijn opgetreden nadat Azure AD is geconfigureerd voor het verzenden van gebeurtenissen naar Azure Monitor.

Als u de oudste en meest recente controle gebeurtenissen in Azure Monitor wilt weten, gebruikt u de volgende query:

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Zie [het schema voor Azure AD-controle logboeken interpreteren in azure monitor](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)voor meer informatie over de kolommen die zijn opgeslagen voor controle gebeurtenissen in azure monitor.

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Aangepaste Azure Monitor query's maken met behulp van Azure PowerShell

U kunt Logboeken openen via Power shell nadat u Azure AD hebt geconfigureerd voor het verzenden van logboeken naar Azure Monitor. Vervolgens kunt u query's verzenden vanuit scripts of de Power shell-opdracht regel, zonder dat u een globale beheerder hoeft te zijn in de Tenant. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>Zorg ervoor dat de gebruiker of Service-Principal de juiste roltoewijzing heeft

Zorg ervoor dat u, de gebruiker of service-principal die wordt geverifieerd bij Azure AD, de juiste Azure-rol in de werk ruimte Log Analytics heeft. De functie opties zijn Log Analytics lezer of de Log Analytics Inzender. Als u al een van deze rollen hebt, gaat u verder met het [ophalen van log Analytics-id met één Azure-abonnement](#retrieve-log-analytics-id-with-one-azure-subscription).

Voer de volgende stappen uit om de roltoewijzing in te stellen en een query te maken:

1. Zoek in de Azure Portal de [log Analytics-werk ruimte](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
).

1. Selecteer **Access Control (IAM)**.

1. Klik vervolgens op **toevoegen** om een roltoewijzing toe te voegen.

    ![Een roltoewijzing toevoegen](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Azure PowerShell module installeren

Zodra u de juiste roltoewijzing hebt, start u Power shell en [installeert u de module Azure PowerShell](/powershell/azure/install-az-ps?view=azps-3.3.0) (als u dat nog niet hebt gedaan) door het volgende te typen:

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
Nu bent u klaar om te verifiëren bij Azure AD en haalt u de ID op van de Log Analytics werk ruimte waarin u een query uitvoert.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>Log Analytics-ID ophalen met één Azure-abonnement
Als u slechts één Azure-abonnement hebt en een enkele Log Analytics-werk ruimte, typt u het volgende om te verifiëren bij Azure AD, maakt u verbinding met dat abonnement en haalt u die werk ruimte op:
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>Log Analytics-ID met meerdere Azure-abonnementen ophalen

 [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) werkt in één abonnement tegelijk. Als u meerdere Azure-abonnementen hebt, moet u er dus voor zorgen dat u verbinding maakt met het abonnement dat de Log Analytics-werk ruimte heeft met de Azure AD-Logboeken. 
 
 Met de volgende cmdlets wordt een lijst met abonnementen weer gegeven en wordt de ID van het abonnement gevonden dat de Log Analytics-werk ruimte heeft:
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
U kunt uw Power shell-sessie opnieuw verifiëren en koppelen aan dit abonnement met behulp van een opdracht zoals `Connect-AzAccount –Subscription $subs[0].id` . Zie [Aanmelden met Azure PowerShell](/powershell/azure/authenticate-azureps)voor meer informatie over het verifiëren van Azure vanuit Power shell, inclusief niet-interactief.

Als u meerdere Log Analytics-werk ruimten in dat abonnement hebt, wordt met de cmdlet [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) de lijst met werk ruimten geretourneerd. Vervolgens kunt u de logboeken met de Azure AD-logbestanden vinden. Het `CustomerId` veld dat door deze cmdlet wordt geretourneerd, is hetzelfde als de waarde van de werk ruimte-id die wordt weer gegeven in de Azure Portal in het log Analytics overzicht van de werk ruimte.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>De query naar de Log Analytics-werk ruimte verzenden
Ten slotte, wanneer u een werk ruimte hebt geïdentificeerd, kunt u [invoke-AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery?view=azps-3.3.0
) gebruiken om een Kusto-query naar die werk ruimte te verzenden. Deze query's worden geschreven in de [Kusto-query taal](/azure/kusto/query/).
 
U kunt bijvoorbeeld het datum bereik van de controle gebeurtenis records ophalen uit de werk ruimte Log Analytics, met Power shell-cmdlets voor het verzenden van een query zoals:
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

U kunt ook rechten beheer gebeurtenissen ophalen met behulp van een query als:

```azurepowershell
$bQuery = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>Volgende stappen:
- [Interactieve rapporten maken met Azure Monitor werkmappen](../../azure-monitor/visualize/workbooks-overview.md)