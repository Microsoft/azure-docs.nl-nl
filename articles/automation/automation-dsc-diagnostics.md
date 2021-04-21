---
title: Integreren met Azure Monitor logboeken
description: In dit artikel wordt beschreven hoe u Desired State Configuration rapportagegegevens verzendt van Azure Automation State Configuration naar Azure Monitor logboeken.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
manager: carmonm
ms.openlocfilehash: 8952ea87cfd9317225ecb9e313174f8d1fe8e519
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834800"
---
# <a name="integrate-with-azure-monitor-logs"></a>Integreren met Azure Monitor logboeken

Azure Automation State Configuration knooppuntstatusgegevens worden 30 dagen bewaard. U kunt knooppuntstatusgegevens verzenden naar uw Log Analytics-werkruimte als u deze gegevens liever voor een langere periode wilt bewaren. De nalevingsstatus is zichtbaar in de Azure Portal of met PowerShell, voor knooppunten en voor afzonderlijke DSC-resources in knooppuntconfiguraties. 

Azure Monitor logboeken bieden meer operationele zichtbaarheid voor uw Automation-State Configuration en kunnen helpen incidenten sneller op te lossen. Met Azure Monitor kunt u het volgende doen:

- Haal nalevingsinformatie op voor beheerde knooppunten en afzonderlijke resources.
- Activeer een e-mail of waarschuwing op basis van de nalevingsstatus.
- Schrijf geavanceerde query's over uw beheerde knooppunten.
- Nalevingsstatus correleren tussen Automation-accounts.
- Gebruik aangepaste weergaven en zoekquery's om de resultaten van uw runbook, de taakstatus van het runbook en andere gerelateerde sleutelindicatoren of metrische gegevens te visualiseren.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Vereisten

Als u uw Automation-State Configuration wilt verzenden naar Azure Monitor logboeken, hebt u het volgende nodig:

- De release van november 2016 of later [van Azure PowerShell](/powershell/azure/) (v2.3.0).
- Een Azure Automation-account. Zie An introduction to Azure Automation (Inleiding tot Azure Automation) [voor meer informatie.](automation-intro.md)
- Een Log Analytics-werkruimte met een Automation & Control-serviceaanbieding. Zie Aan de slag met Log Analytics in Azure Monitor voor [meer Azure Monitor.](../azure-monitor/logs/log-analytics-tutorial.md)
- Ten minste één Azure Automation State Configuration knooppunt. Zie [Onboarding machines for management by Azure Automation State Configuration (Machines onboarden voor beheer door Azure Automation State Configuration).](automation-dsc-onboarding.md)
- De [xDscDiagnostics-module,](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) versie 2.7.0.0 of hoger. Zie Problemen met Azure Automation Desired State Configuration voor [installatiestappen.](./troubleshoot/desired-state-configuration.md)

## <a name="set-up-integration-with-azure-monitor-logs"></a>Integratie met logboeken Azure Monitor instellen

Als u gegevens wilt importeren uit Azure Automation State Configuration logboeken Azure Monitor, moet u de volgende stappen voltooien:

1. Meld u aan bij uw Azure-account in PowerShell. Zie [Aanmelden met Azure PowerShell](/powershell/azure/authenticate-azureps).
1. Haal de resource-id van uw Automation-account op door de volgende PowerShell-cmdlet uit te uitvoeren. Als u meer dan één Automation-account hebt, kiest u de resource-id voor het account dat u wilt configureren.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Haal de resource-id van uw Log Analytics-werkruimte op door de volgende PowerShell-cmdlet uit te uitvoeren. Als u meer dan één werkruimte hebt, kiest u de resource-id voor de werkruimte die u wilt configureren.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Voer de volgende PowerShell-cmdlet uit, vervang en door de waarden `<AutomationResourceId>` uit elk van de vorige `<WorkspaceResourceId>` `ResourceId` stappen.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Als u wilt stoppen met het importeren van gegevens Azure Automation State Configuration in Azure Monitor logboeken, moet u de volgende PowerShell-cmdlet uitvoeren.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>De logboeken State Configuration weergeven

Nadat u integratie met Azure Monitor-logboeken voor uw Automation State Configuration-gegevens hebt ingesteld,  kunt u deze weergeven door Logboeken **te** selecteren in de sectie Bewaking in het linkerdeelvenster van de pagina Statusconfiguratie (DSC).

![Logboeken](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

Het deelvenster Zoeken in logboeken wordt geopend met een queryregio die is beperkt tot uw Automation-accountresource. U kunt in de State Configuration zoeken naar DSC-bewerkingen door te zoeken in Azure Monitor logboeken. De records voor DSC-bewerkingen worden opgeslagen in de `AzureDiagnostics` tabel. Als u bijvoorbeeld knooppunten wilt vinden die niet compatibel zijn, typt u de volgende query.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

Filterdetails:

* Filter op `DscNodeStatusData` om bewerkingen te retourneren voor elk State Configuration knooppunt.
* Filter op `DscResourceStatusData` om bewerkingen te retourneren voor elke DSC-resource die wordt aangeroepen in de knooppuntconfiguratie die op die resource is toegepast. 
* Filter op `DscResourceStatusData` om foutinformatie te retourneren voor DSC-resources die mislukken.

Zie Overzicht van logboekquery's in Azure Monitor voor meer informatie over het maken van [logboekquery'Azure Monitor.](../azure-monitor/logs/log-query-overview.md)

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Een e-mail verzenden wanneer een State Configuration nalevingscontrole mislukt

Een van onze belangrijkste klantaanvragen is de mogelijkheid om een e-mailbericht of tekst te verzenden wanneer er iets misgaat met een DSC-configuratie.

Als u een waarschuwingsregel wilt maken, begint u met het maken van een logboekzoekactie voor de State Configuration rapportrecords die de waarschuwing moeten aanroepen. Klik op **de knop Nieuwe waarschuwingsregel** om de waarschuwingsregel te maken en te configureren.

1. Klik op de pagina Overzicht van de Log Analytics-werkruimte op **Logboeken.**
1. Maak een zoekquery voor uw waarschuwing door de volgende zoekopdracht in het queryveld te typen:  `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Als u logboeken van meer dan één Automation-account of -abonnement voor uw werkruimte hebt ingesteld, kunt u uw waarschuwingen groepeert op abonnement en Automation-account. De Naam van het Automation-account afleiden uit `Resource` het veld in het zoeken naar de `DscNodeStatusData` records.
1. Als u het **scherm Regel maken** wilt openen, klikt u op **Nieuwe** waarschuwingsregel boven aan de pagina. 

Zie Een waarschuwingsregel maken voor meer informatie over de opties voor het [configureren van de waarschuwing.](../azure-monitor/alerts/alerts-metric.md)

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Mislukte DSC-resources zoeken op alle knooppunten

Een voordeel van het gebruik Azure Monitor logboeken is dat u kunt zoeken naar mislukte controles op knooppunten. Alle exemplaren van DSC-resources zoeken die zijn mislukt:

1. Klik op de pagina Overzicht van de Log Analytics-werkruimte op **Logboeken.**
1. Maak een zoekquery voor uw waarschuwing door de volgende zoekopdracht in het queryveld te typen:  `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Historische DSC-knooppuntstatus weergeven

Als u de statusgeschiedenis van uw DSC-knooppunt in de tijd wilt visualiseren, kunt u deze query gebruiken:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Deze query geeft een grafiek weer van de knooppuntstatus gedurende een periode.

## <a name="azure-monitor-logs-records"></a>Azure Monitor registreert records

Azure Automation diagnostische gegevens maakt u twee categorieën records in Azure Monitor logboeken:

* Statusgegevens van knooppunt ( `DscNodeStatusData` )
* Resourcestatusgegevens ( `DscResourceStatusData` )

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Eigenschap | Beschrijving |
| --- | --- |
| TimeGenerated |De datum en tijd waarop de nalevingscontrole is geweest. |
| OperationName |`DscNodeStatusData`. |
| ResultType |Waarde die aangeeft of het knooppunt compatibel is. |
| NodeName_s |De naam van het beheerde knooppunt. |
| NodeComplianceStatus_s |Statuswaarde die aangeeft of het knooppunt compatibel is. |
| DscReportStatus |Statuswaarde die aangeeft of de nalevingscontrole is uitgevoerd. |
| ConfigurationMode | De modus die wordt gebruikt om de configuratie toe te passen op het knooppunt. Mogelijke waarden zijn: <ul><li>`ApplyOnly`: DSC past de configuratie toe en doet niets meer, tenzij een nieuwe configuratie naar het doel-knooppunt wordt pushen of wanneer een nieuwe configuratie van een server wordt gehaald. Na de eerste toepassing van een nieuwe configuratie controleert DSC niet op afwijking van een eerder geconfigureerde status. DSC probeert de configuratie toe te passen totdat deze is geslaagd voordat `ApplyOnly` de waarde van kracht wordt. </li><li>`ApplyAndMonitor`: Dit is de standaardwaarde. De LCM past alle nieuwe configuraties toe. Als het doel-knooppunt na de eerste toepassing van een nieuwe configuratie van de gewenste status afdrijft, rapporteert DSC de discrepantie in logboeken. DSC probeert de configuratie toe te passen totdat deze is geslaagd voordat de `ApplyAndMonitor` waarde van kracht wordt.</li><li>`ApplyAndAutoCorrect`: DSC past nieuwe configuraties toe. Als het doel-knooppunt na de eerste toepassing van een nieuwe configuratie van de gewenste status afdrijft, rapporteert DSC de discrepantie in logboeken en wordt vervolgens de huidige configuratie opnieuw van toepassing.</li></ul> |
| HostName_s | De naam van het beheerde knooppunt. |
| IPAddress | Het IPv4-adres van het beheerde knooppunt. |
| Categorie | `DscNodeStatus`. |
| Resource | De naam van het Azure Automation account. |
| Tenant_g | GUID die de tenant voor de aanroeper identificeert. |
| NodeId_g | GUID die het beheerde knooppunt identificeert. |
| DscReportId_g | GUID die het rapport identificeert. |
| LastSeenTime_t | De datum en tijd waarop het rapport voor het laatst is bekeken. |
| ReportStartTime_t | De datum en tijd waarop het rapport is gestart. |
| ReportEndTime_t | De datum en tijd waarop het rapport is voltooid. |
| NumberOfResources_d | Het aantal DSC-resources dat wordt aangeroepen in de configuratie die is toegepast op het knooppunt. |
| SourceSystem | Het bronsysteem dat identificeert hoe Azure Monitor logboeken de gegevens heeft verzameld. Altijd `Azure` voor Diagnostische gegevens van Azure. |
| ResourceId |De resource-id van het Azure Automation account. |
| ResultDescription | De resourcebeschrijving voor deze bewerking. |
| SubscriptionId | De Azure-abonnements-id (GUID) voor het Automation-account. |
| ResourceGroup | De naam van de resourcegroep voor het Automation-account. |
| ResourceProvider | Microsoft. Automatisering. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId | Een GUID die de correlatie-id van het nalevingsrapport is. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Eigenschap | Beschrijving |
| --- | --- |
| TimeGenerated |De datum en tijd waarop de nalevingscontrole is geweest. |
| OperationName |`DscResourceStatusData`.|
| ResultType |Of de resource compatibel is. |
| NodeName_s |De naam van het beheerde knooppunt. |
| Categorie | DscNodeStatus. |
| Resource | De naam van het Azure Automation account. |
| Tenant_g | GUID die de tenant voor de aanroeper identificeert. |
| NodeId_g |GUID die het beheerde knooppunt identificeert. |
| DscReportId_g |GUID die het rapport identificeert. |
| DscResourceId_s |De naam van het DSC-resource-exemplaar. |
| DscResourceName_s |De naam van de DSC-resource. |
| DscResourceStatus_s |Of de DSC-resource in overeenstemming is. |
| DscModuleName_s |De naam van de PowerShell-module die de DSC-resource bevat. |
| DscModuleVersion_s |De versie van de PowerShell-module die de DSC-resource bevat. |
| DscConfigurationName_s |De naam van de configuratie die op het knooppunt is toegepast. |
| ErrorCode_s | De foutcode als de resource is mislukt. |
| ErrorMessage_s |Het foutbericht als de resource is mislukt. |
| DscResourceDuration_d |De tijd in seconden die de DSC-resource heeft gehad. |
| SourceSystem | Hoe Azure Monitor logboeken de gegevens verzameld. Altijd `Azure` voor Diagnostische gegevens van Azure. |
| ResourceId |De id van het Azure Automation account. |
| ResultDescription | De beschrijving voor deze bewerking. |
| SubscriptionId | De Azure-abonnements-id (GUID) voor het Automation-account. |
| ResourceGroup | De naam van de resourcegroep voor het Automation-account. |
| ResourceProvider | Microsoft. Automatisering. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |GUID die de correlatie-id van het nalevingsrapport is. |

## <a name="next-steps"></a>Volgende stappen

- Zie overzicht Azure Automation State Configuration [voor een overzicht.](automation-dsc-overview.md)
- Zie Aan de slag met Azure Automation State Configuration om aan de [slag te gaan.](automation-dsc-getting-started.md)
- Zie Compile DSC configurations in Azure Automation State Configuration voor meer informatie over het compileren van [DSC-configuraties, zodat u ze](automation-dsc-compile.md)kunt toewijzen aan doelknooppunten.
- Zie [Az.Automation](/powershell/module/az.automation) voor een naslagdocumentatie voor een PowerShell-cmdlet.
- Zie prijzen voor Azure Automation State Configuration [informatie.](https://azure.microsoft.com/pricing/details/automation/)
- Zie Continue implementatie instellen met Chocolatey Azure Automation State Configuration een voorbeeld van het gebruik van Azure Automation State Configuration in [een pijplijn voor continue implementatie.](automation-dsc-cd-chocolatey.md)
- Zie Zoekopdrachten Azure Monitor in logboeken in logboeken voor meer informatie over het maken van verschillende zoekquery's en het controleren van de Automation State Configuration-logboeken met [Azure Monitor-logboeken.](../azure-monitor/logs/log-query-overview.md)
- Zie Het overzicht van Azure-Azure Monitor-opslaggegevens verzamelen in Azure Monitor logboeken voor meer informatie over het verzamelen [Azure Monitor logboeken en gegevensverzamelingsbronnen.](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)
