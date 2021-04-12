---
title: Problemen met Azure Automation VM's buiten bedrijfsuren starten/stoppen oplossen
description: In dit artikel leest u hoe u problemen oplost en oplost die ontstaan tijdens het gebruik van de functie VM's buiten bedrijfsuren starten/stoppen.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: troubleshooting
ms.openlocfilehash: f6d2696a68643f87de0fcaf2e723da9365d9953e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104953847"
---
# <a name="troubleshoot-startstop-vms-during-off-hours-issues"></a>Problemen met VM's buiten bedrijfsuren starten/stoppen oplossen

In dit artikel vindt u informatie over het oplossen van problemen die zich voordoen wanneer u de Azure Automation VM's buiten bedrijfsuren starten/stoppen functie op uw Vm's implementeert. 

## <a name="scenario-startstop-vms-during-off-hours-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Scenario: VM's buiten bedrijfsuren starten/stoppen niet goed worden geïmplementeerd

### <a name="issue"></a>Probleem

Wanneer u [VM's buiten bedrijfsuren starten/stoppen](../automation-solution-vm-management.md)implementeert, wordt een van de volgende fouten weer gegeven:

```error
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```error
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]".
```

```error
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```error
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```error
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

```error
A parameter cannot be found that matches parameter name 'TagName'
```

```error
Start-AzureRmVm : Run Login-AzureRmAccount to login
```

### <a name="cause"></a>Oorzaak

Implementaties kunnen om een van de volgende redenen mislukken:

- Er is al een Automation-account met dezelfde naam in de geselecteerde regio.
- Een beleid staat de implementatie van VM's buiten bedrijfsuren starten/stoppen niet toe.
- Het `Microsoft.OperationsManagement` `Microsoft.Insights` `Microsoft.Automation` resource type, of is niet geregistreerd.
- Uw Log Analytics-werk ruimte is vergrendeld.
- U hebt een verouderde versie van de AzureRM-modules of de functie VM's buiten bedrijfsuren starten/stoppen.

### <a name="resolution"></a>Oplossing

Bekijk de volgende oplossingen voor mogelijke oplossingen:

* Automation-accounts moeten uniek zijn binnen een Azure-regio, zelfs als ze zich in verschillende resource groepen bevinden. Controleer uw bestaande Automation-accounts in de doel regio.
* Een bestaand beleid voor komt dat een resource die vereist is voor VM's buiten bedrijfsuren starten/stoppen, wordt geïmplementeerd. Ga naar de beleids toewijzingen in de Azure Portal en controleer of u een beleids toewijzing hebt die de implementatie van deze resource niet toestaat. Zie [RequestDisallowedByPolicy-fout](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md)voor meer informatie.
* Als u VM's buiten bedrijfsuren starten/stoppen wilt implementeren, moet uw abonnement worden geregistreerd bij de volgende Azure-resource-naam ruimten:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Zie [fouten voor de registratie van de resource provider oplossen](../../azure-resource-manager/templates/error-register-resource-provider.md)voor meer informatie over fouten bij het registreren van providers.
* Als u een vergren deling op uw Log Analytics-werk ruimte hebt, gaat u naar uw werk ruimte in de Azure Portal en verwijdert u de vergren delingen van de resource.
* Als u met deze oplossingen het probleem niet kunt oplossen, volgt u de instructies onder [Update de functie](../automation-solution-vm-management.md#update-the-feature) om VM's buiten bedrijfsuren starten/stoppen opnieuw te implementeren.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Scenario: alle Vm's kunnen niet worden gestart of gestopt

### <a name="issue"></a>Probleem

U hebt VM's buiten bedrijfsuren starten/stoppen geconfigureerd, maar de virtuele machines worden niet gestart of gestopt.

### <a name="cause"></a>Oorzaak

Deze fout kan een van de volgende oorzaken hebben:

- Een planning is niet correct geconfigureerd.
- Het run as-account is mogelijk niet juist geconfigureerd.
- Een runbook kan worden uitgevoerd als er fouten zijn opgetreden.
- De Vm's zijn mogelijk uitgesloten.

### <a name="resolution"></a>Oplossing

Bekijk de volgende lijst voor mogelijke oplossingen:

* Controleer of u een planning voor VM's buiten bedrijfsuren starten/stoppen hebt geconfigureerd. Zie [schema's](../shared-resources/schedules.md)voor meer informatie over het configureren van een schema.

* Controleer de [taak stromen](../automation-runbook-execution.md#job-statuses) om te controleren of er fouten zijn opgetreden. Zoek naar taken uit een van de volgende runbooks:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Controleer of het [uitvoeren als-account](../automation-security-overview.md#run-as-accounts) de juiste machtigingen heeft voor de vm's die u probeert te starten of te stoppen. Zie voor meer informatie over het controleren van de machtigingen voor een resource [Quick Start: rollen weer geven die zijn toegewezen aan een gebruiker met behulp van de Azure Portal](../../role-based-access-control/check-access.md). U moet de toepassings-ID opgeven voor de service-principal die wordt gebruikt door het run as-account. U kunt deze waarde ophalen door naar uw Automation-account te gaan in de Azure Portal. Selecteer **uitvoeren als-accounts** onder **account instellingen** en selecteer het juiste uitvoeren als-account.

* Vm's kunnen niet worden gestart of gestopt als ze expliciet worden uitgesloten. Uitgesloten Vm's worden ingesteld in de `External_ExcludeVMNames` variabele in het Automation-account waarop de functie wordt geïmplementeerd. In het volgende voor beeld ziet u hoe u een query kunt uitvoeren op die waarde met Power shell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Scenario: sommige Vm's kunnen niet worden gestart of gestopt

### <a name="issue"></a>Probleem

U hebt VM's buiten bedrijfsuren starten/stoppen geconfigureerd, maar er worden geen enkele geconfigureerde Vm's gestart of gestopt.

### <a name="cause"></a>Oorzaak

Deze fout kan een van de volgende oorzaken hebben:

- In het Sequence-scenario is het mogelijk dat een tag ontbreekt of onjuist is.
- De virtuele machine is mogelijk uitgesloten.
- Het uitvoeren als-account heeft mogelijk onvoldoende machtigingen voor de virtuele machine.
- De virtuele machine kan een probleem hebben waardoor het starten of stoppen van de VM is gestopt.

### <a name="resolution"></a>Oplossing

Bekijk de volgende lijst voor mogelijke oplossingen:

* Wanneer u het [volgorde scenario](../automation-solution-vm-management.md) van VM's buiten bedrijfsuren starten/stoppen gebruikt, moet u ervoor zorgen dat elke VM die u wilt starten of stoppen de juiste tag heeft. Zorg ervoor dat de virtuele machines die u wilt starten `sequencestart` , de tag hebben en de vm's die u wilt stoppen, de `sequencestop` tag hebben. Voor beide tags is een positief geheel getal vereist. U kunt een query gebruiken die vergelijkbaar is met het volgende voor beeld om te zoeken naar alle virtuele machines met de labels en hun waarden.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Vm's kunnen niet worden gestart of gestopt als ze expliciet worden uitgesloten. Uitgesloten Vm's worden ingesteld in de `External_ExcludeVMNames` variabele in het Automation-account waarop de functie wordt geïmplementeerd. In het volgende voor beeld ziet u hoe u een query kunt uitvoeren op die waarde met Power shell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Voor het starten en stoppen van Vm's moet het uitvoeren als-account voor het Automation-account de juiste machtigingen hebben voor de virtuele machine. Zie voor meer informatie over het controleren van de machtigingen voor een resource [Quick Start: rollen weer geven die zijn toegewezen aan een gebruiker met behulp van de Azure Portal](../../role-based-access-control/check-access.md). U moet de toepassings-ID opgeven voor de service-principal die wordt gebruikt door het run as-account. U kunt deze waarde ophalen door naar uw Automation-account te gaan in de Azure Portal. Selecteer **uitvoeren als-accounts** onder **account instellingen** en selecteer het juiste uitvoeren als-account.
* Als de virtuele machine een probleem ondervindt bij het starten of ongedaan maken van de toewijzing, is er mogelijk een probleem op de VM zelf. Voor beelden zijn een update die wordt toegepast wanneer de virtuele machine wordt afgesloten, een service die vastloopt en nog veel meer. Ga naar de VM-resource en controleer de **activiteiten logboeken** om te zien of er fouten in de logboeken zijn. U kunt ook proberen u aan te melden bij de virtuele machine om te zien of er fouten zijn in de gebeurtenis Logboeken. Zie [problemen met virtuele Azure-machines oplossen](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)voor meer informatie over het oplossen van problemen met uw VM.
* Controleer de [taak stromen](../automation-runbook-execution.md#job-statuses) om te controleren of er fouten zijn opgetreden. Ga in de portal naar uw Automation-account en selecteer **taken** onder **proces automatisering**.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Scenario: mijn aangepaste runbook kan mijn Vm's niet starten of stoppen

### <a name="issue"></a>Probleem

U hebt een aangepast runbook gemaakt of een gedownloade versie van het PowerShell Gallery, en deze werkt niet goed.

### <a name="cause"></a>Oorzaak

Er kunnen veel oorzaken zijn voor de fout. Ga naar uw Automation-account in het Azure Portal en selecteer **taken** onder **proces automatisering**. Zoek op de pagina **taken** naar taken van uw runbook om eventuele taak fouten weer te geven.

### <a name="resolution"></a>Oplossing

U wordt aangeraden dat u:

* Gebruik [VM's buiten bedrijfsuren starten/stoppen](../automation-solution-vm-management.md) om vm's in azure Automation te starten en te stoppen. 
* Houd er rekening mee dat micro soft geen ondersteuning biedt voor aangepaste runbooks. Mogelijk vindt u een oplossing voor uw aangepaste runbook bij het oplossen van problemen [met het runbook](runbooks.md). Controleer de [taak stromen](../automation-runbook-execution.md#job-statuses) om te controleren of er fouten zijn opgetreden. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Scenario: Vm's worden niet in de juiste volg orde gestart of gestopt

### <a name="issue"></a>Probleem

De Vm's die u hebt ingeschakeld voor de functie, worden niet in de juiste volg orde gestart of gestopt.

### <a name="cause"></a>Oorzaak

Dit probleem wordt veroorzaakt door onjuiste tagging op de Vm's.

### <a name="resolution"></a>Oplossing

Volg deze stappen om ervoor te zorgen dat de functie correct is ingeschakeld:

1. Zorg ervoor dat alle Vm's die moeten worden gestart of gestopt `sequencestart` , een or `sequencestop` -tag hebben, afhankelijk van uw situatie. Voor deze tags is een positief geheel getal vereist als waarde. Vm's worden in oplopende volg orde verwerkt op basis van deze waarde.
1. Zorg ervoor dat de resource groepen voor de virtuele machines die moeten worden gestart of gestopt, zich in de `External_Start_ResourceGroupNames` or `External_Stop_ResourceGroupNames` -variabelen bevinden, afhankelijk van uw situatie.
1. Test uw wijzigingen door het **SequencedStartStop_Parent** runbook uit te voeren, waarbij de `WHATIF` para meter is ingesteld op True om een voor beeld van uw wijzigingen te bekijken.

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Scenario: de VM's buiten bedrijfsuren starten/stoppen-taak is mislukt met een niet-toegestane fout van 403

### <a name="issue"></a>Probleem

U vindt taken die zijn mislukt met een `403 forbidden` fout voor VM's buiten bedrijfsuren starten/stoppen runbooks.

### <a name="cause"></a>Oorzaak

Dit probleem kan worden veroorzaakt door een onjuist geconfigureerd of verlopen uitvoeren als-account. Dit kan ook worden veroorzaakt door ontoereikende machtigingen voor de VM-resources door het run as-account.

### <a name="resolution"></a>Oplossing

Als u wilt controleren of uw uitvoeren als-account correct is geconfigureerd, gaat u naar uw Automation-account in het Azure Portal en selecteert u **uitvoeren als-accounts** onder **account instellingen**. Als een uitvoeren als-account onjuist is geconfigureerd of is verlopen, wordt de voor waarde in de status weer gegeven.

Als uw uitvoeren als-account onjuist is geconfigureerd, verwijdert u het uitvoeren als-account en maakt u het opnieuw. Zie [Azure Automation uitvoeren als-accounts](../automation-security-overview.md#run-as-accounts)voor meer informatie.

Als het certificaat is verlopen voor uw uitvoeren als-account, volgt u de stappen in [zelfondertekend certificaat vernieuwen](../manage-runas-account.md#cert-renewal) om het certificaat te vernieuwen.

Als er ontbrekende machtigingen zijn, raadpleegt u [Quick Start: rollen weer geven die aan een gebruiker zijn toegewezen met behulp van de Azure Portal](../../role-based-access-control/check-access.md). U moet de toepassings-ID opgeven voor de service-principal die wordt gebruikt door het run as-account. U kunt deze waarde ophalen door naar uw Automation-account te gaan in de Azure Portal. Selecteer **uitvoeren als-accounts** onder **account instellingen** en selecteer het juiste uitvoeren als-account.

## <a name="scenario-my-problem-isnt-listed-here"></a><a name="other"></a>Scenario: mijn probleem wordt hier niet vermeld

### <a name="issue"></a>Probleem

U ondervindt een probleem of onverwacht resultaat wanneer u VM's buiten bedrijfsuren starten/stoppen gebruikt dat niet op deze pagina wordt weer gegeven.

### <a name="cause"></a>Oorzaak

Vaak kunnen fouten worden veroorzaakt door gebruik te maken van een oude en verouderde versie van de functie.

> [!NOTE]
> De functie VM's buiten bedrijfsuren starten/stoppen is getest met de Azure-modules die worden geïmporteerd in uw Automation-account wanneer u de functie op Vm's implementeert. De functie werkt momenteel niet met nieuwere versies van de Azure-module. Deze beperking is alleen van invloed op het Automation-account dat u gebruikt om VM's buiten bedrijfsuren starten/stoppen uit te voeren. U kunt nog steeds nieuwere versies van de Azure-module gebruiken in uw andere Automation-accounts, zoals beschreven in [Update Azure PowerShell-modules](../automation-update-azure-modules.md).

### <a name="resolution"></a>Oplossing

Als u veel fouten wilt oplossen, moet u VM's buiten bedrijfsuren starten/stoppen verwijderen en [bijwerken](../automation-solution-vm-management.md#update-the-feature). U kunt ook de [taak stromen](../automation-runbook-execution.md#job-statuses) controleren om fouten op te sporen. 

## <a name="next-steps"></a>Volgende stappen

Als uw probleem hier niet wordt weer gegeven of u het probleem niet kunt oplossen, kunt u een van de volgende kanalen proberen voor aanvullende ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure-forums](https://azure.microsoft.com/support/forums/).
* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) , het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Azure-ondersteuning verbindt de Azure-community met antwoorden, ondersteuning en experts.
* Een ondersteunings incident voor Azure. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/)en selecteer **ondersteuning verkrijgen**.