---
title: Problemen Azure Automation VM's buiten bedrijfsuren starten/stoppen oplossen
description: In dit artikel wordt beschreven hoe u problemen kunt oplossen die ontstaan tijdens het gebruik van VM's buiten bedrijfsuren starten/stoppen functie.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b03481b41e879acac4c3b193d72594454f650525
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831182"
---
# <a name="troubleshoot-startstop-vms-during-off-hours-issues"></a>Problemen VM's buiten bedrijfsuren starten/stoppen oplossen

Dit artikel bevat informatie over het oplossen en oplossen van problemen die zich voordoen wanneer u de functie Azure Automation VM's buiten bedrijfsuren starten/stoppen uw VM's implementeert. 

## <a name="scenario-startstop-vms-during-off-hours-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Scenario: VM's buiten bedrijfsuren starten/stoppen kan niet goed worden geïmplementeerd

### <a name="issue"></a>Probleem

Wanneer u een [VM's buiten bedrijfsuren starten/stoppen,](../automation-solution-vm-management.md)ontvangt u een van de volgende fouten:

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

Implementaties kunnen mislukken om een van de volgende redenen:

- Er is al een Automation-account met dezelfde naam in de geselecteerde regio.
- Een beleid staat de implementatie van de VM's buiten bedrijfsuren starten/stoppen.
- Het `Microsoft.OperationsManagement` `Microsoft.Insights` resourcetype , of `Microsoft.Automation` is niet geregistreerd.
- Uw Log Analytics-werkruimte is vergrendeld.
- U hebt een verouderde versie van de AzureRM-modules of de VM's buiten bedrijfsuren starten/stoppen functie.

### <a name="resolution"></a>Oplossing

Bekijk de volgende oplossingen voor mogelijke oplossingen:

* Automation-accounts moeten uniek zijn binnen een Azure-regio, zelfs als ze zich in verschillende resourcegroepen hebben. Controleer uw bestaande Automation-accounts in de doelregio.
* Een bestaand beleid voorkomt dat een resource wordt geïmplementeerd die VM's buiten bedrijfsuren starten/stoppen is vereist. Ga naar uw beleidstoewijzingen in Azure Portal en controleer of u een beleidstoewijzing hebt die de implementatie van deze resource niet toewijs. Zie [RequestDisallowedByPolicy error (RequestDisallowedByPolicy-fout) voor meer informatie.](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md)
* Als u VM's buiten bedrijfsuren starten/stoppen implementeren, moet uw abonnement zijn geregistreerd bij de volgende Azure-resourcenaamruimten:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Zie Resolve errors for resource provider registration (Fouten voor registratie van [resourceproviders](../../azure-resource-manager/templates/error-register-resource-provider.md)oplossen) voor meer informatie over fouten bij het registreren van providers.
* Als uw Log Analytics-werkruimte is vergrendeld, gaat u naar uw werkruimte in de Azure Portal en verwijdert u eventuele vergrendelingen voor de resource.
* Als deze oplossingen uw probleem niet oplossen, volgt u de instructies onder De functie [bijwerken](../automation-solution-vm-management.md#update-the-feature) om de functie opnieuw te VM's buiten bedrijfsuren starten/stoppen.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Scenario: alle VM's starten of stoppen niet

### <a name="issue"></a>Probleem

U hebt de VM's buiten bedrijfsuren starten/stoppen geconfigureerd, maar hiermee worden niet alle VM's starten of stoppen.

### <a name="cause"></a>Oorzaak

Deze fout kan een van de volgende oorzaken hebben:

- Een planning is niet juist geconfigureerd.
- Het Uitvoeren als-account is mogelijk niet juist geconfigureerd.
- Een runbook kan fouten hebben.
- De VM's zijn mogelijk uitgesloten.

### <a name="resolution"></a>Oplossing

Bekijk de volgende lijst voor mogelijke oplossingen:

* Controleer of u een planning voor een VM's buiten bedrijfsuren starten/stoppen. Zie Planningen voor meer informatie over het configureren [van een planning.](../shared-resources/schedules.md)

* Controleer de [taakstromen om](../automation-runbook-execution.md#job-statuses) te zoeken naar fouten. Zoek naar taken uit een van de volgende runbooks:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Controleer of uw [Uitvoeren als-account](../automation-security-overview.md#run-as-accounts) de juiste machtigingen heeft voor de VM's die u wilt starten of stoppen. Zie Quickstart: Rollen weergeven die zijn toegewezen aan een gebruiker met behulp van de Azure Portal voor meer informatie over het controleren [van de machtigingen voor Azure Portal.](../../role-based-access-control/check-access.md) U moet de toepassings-id voor de service-principal die wordt gebruikt door het Uitvoeren als-account, verstrekken. U kunt deze waarde ophalen door naar uw Automation-account in de Azure Portal. Selecteer **Uitvoeren als-accounts** **onder Accountinstellingen** en selecteer het juiste Uitvoeren als-account.

* VM's worden mogelijk niet gestart of gestopt als ze expliciet worden uitgesloten. Uitgesloten VM's worden ingesteld in `External_ExcludeVMNames` de variabele in het Automation-account waarop de functie is geïmplementeerd. In het volgende voorbeeld ziet u hoe u een query kunt uitvoeren op die waarde met PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Scenario: Sommige van mijn VM's starten of stoppen niet

### <a name="issue"></a>Probleem

U hebt een VM's buiten bedrijfsuren starten/stoppen geconfigureerd, maar sommige geconfigureerde VM's worden niet start of gestopt.

### <a name="cause"></a>Oorzaak

Deze fout kan een van de volgende oorzaken hebben:

- In het sequentiescenario ontbreekt of is een tag onjuist.
- De VM kan worden uitgesloten.
- Het Uitvoeren als-account heeft mogelijk niet voldoende machtigingen op de VM.
- De VM kan een probleem hebben dat het starten of stoppen ervan heeft gestopt.

### <a name="resolution"></a>Oplossing

Bekijk de volgende lijst voor mogelijke oplossingen:

* Wanneer u het [volgordescenario van](../automation-solution-vm-management.md) VM's buiten bedrijfsuren starten/stoppen gebruikt, moet u ervoor zorgen dat elke VM die u wilt starten of stoppen de juiste tag heeft. Zorg ervoor dat de VM's die u wilt starten de tag hebben en de VM's die `sequencestart` u wilt stoppen de tag `sequencestop` hebben. Voor beide tags is een positief geheel getal vereist. U kunt een query gebruiken die vergelijkbaar is met het volgende voorbeeld om te zoeken naar alle VM's met de tags en hun waarden.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* VM's worden mogelijk niet gestart of gestopt als ze expliciet worden uitgesloten. Uitgesloten VM's worden ingesteld in de `External_ExcludeVMNames` variabele in het Automation-account waarop de functie is geïmplementeerd. In het volgende voorbeeld ziet u hoe u een query kunt uitvoeren op die waarde met PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Als u VM's wilt starten en stoppen, moet het Uitvoeren als-account voor het Automation-account de juiste machtigingen hebben voor de VM. Zie Quickstart: Rollen weergeven die zijn toegewezen aan een gebruiker met behulp van de Azure Portal voor meer informatie over het controleren [van de machtigingen voor Azure Portal.](../../role-based-access-control/check-access.md) U moet de toepassings-id voor de service-principal die wordt gebruikt door het Uitvoeren als-account, verstrekken. U kunt deze waarde ophalen door naar uw Automation-account in de Azure Portal. Selecteer **Uitvoeren als-accounts** onder **Accountinstellingen** en selecteer het juiste Uitvoeren als-account.
* Als er een probleem is met het starten of de toewijzing van de VM, is er mogelijk een probleem met de VM zelf. Voorbeelden zijn een update die wordt toegepast wanneer de VM probeert af te sluiten, een service die vast komt te zitten en meer. Ga naar uw VM-resource en controleer **activiteitenlogboeken** om te zien of er fouten zijn in de logboeken. U kunt ook proberen u aan te melden bij de VM om te zien of er fouten zijn in de gebeurtenislogboeken. Zie Problemen met virtuele [Azure-machines](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)oplossen voor meer informatie over het oplossen van problemen met uw virtuele machine.
* Controleer de [taakstromen om](../automation-runbook-execution.md#job-statuses) te zoeken naar fouten. Ga in de portal naar uw Automation-account en selecteer **Taken onder** **Procesautomatisering.**

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Scenario: Mijn aangepaste runbook kan mijn VM's niet starten of stoppen

### <a name="issue"></a>Probleem

U hebt een aangepast runbook gemaakt of een van de PowerShell Gallery gedownload en het werkt niet goed.

### <a name="cause"></a>Oorzaak

Er kunnen veel oorzaken voor de fout zijn. Ga naar uw Automation-account in Azure Portal en selecteer **Taken** onder **Procesautomatisering.** Zoek op **de** pagina Taken naar taken uit uw runbook om eventuele mislukte taken weer te geven.

### <a name="resolution"></a>Oplossing

U wordt aangeraden dat u:

* Gebruik [VM's buiten bedrijfsuren starten/stoppen](../automation-solution-vm-management.md) om VM's te starten en te stoppen in Azure Automation. 
* Microsoft biedt geen ondersteuning voor aangepaste runbooks. Mogelijk vindt u een oplossing voor uw aangepaste runbook in [Problemen met runbook oplossen.](runbooks.md) Controleer de [taakstromen om](../automation-runbook-execution.md#job-statuses) te zoeken naar fouten. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Scenario: VM's starten of stoppen niet in de juiste volgorde

### <a name="issue"></a>Probleem

De VM's die u voor de functie hebt ingeschakeld, starten of stoppen niet in de juiste volgorde.

### <a name="cause"></a>Oorzaak

Dit probleem wordt veroorzaakt door onjuiste tagging op de VM's.

### <a name="resolution"></a>Oplossing

Volg deze stappen om ervoor te zorgen dat de functie correct is ingeschakeld:

1. Zorg ervoor dat alle VM's die moeten worden gestart of gestopt, een `sequencestart` `sequencestop` tag of hebben, afhankelijk van uw situatie. Deze tags hebben een positief geheel getal als waarde nodig. VM's worden in oplopende volgorde verwerkt op basis van deze waarde.
1. Zorg ervoor dat de resourcegroepen voor de te starten of gestopte VM's zich in de variabelen of , afhankelijk `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` van uw situatie, zijn.
1. Test uw wijzigingen door het runbook **SequencedStartStop_Parent** uitvoeren met de parameter ingesteld op Waar om een `WHATIF` voorbeeld van uw wijzigingen weer te geven.

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Scenario: VM's buiten bedrijfsuren starten/stoppen taak mislukt met fout 403 verboden

### <a name="issue"></a>Probleem

U vindt taken die zijn mislukt met een `403 forbidden` fout voor VM's buiten bedrijfsuren starten/stoppen runbooks.

### <a name="cause"></a>Oorzaak

Dit probleem kan worden veroorzaakt door een onjuist geconfigureerd of verlopen Uitvoeren als-account. Dit kan ook worden veroorzaakt door onvoldoende machtigingen voor de VM-resources door het Uitvoeren als-account.

### <a name="resolution"></a>Oplossing

Als u wilt controleren of uw Uitvoeren als-account correct is geconfigureerd, gaat u naar uw Automation-account in de Azure Portal selecteert u **Uitvoeren als-accounts** onder **Accountinstellingen.** Als een Uitvoeren als-account onjuist is geconfigureerd of is verlopen, wordt de voorwaarde in de status weergeven.

Als uw Uitvoeren als-account onjuist is geconfigureerd, verwijdert u het Uitvoeren als-account en maakt u het opnieuw. Zie Uitvoeren als-accounts Azure Automation [meer informatie.](../automation-security-overview.md#run-as-accounts)

Als het certificaat is verlopen voor uw Uitvoeren als-account, volgt u de stappen in [Zelf-ondertekend](../manage-runas-account.md#cert-renewal) certificaat vernieuwen om het certificaat te vernieuwen.

Als er machtigingen ontbreken, gaat u naar [Quickstart: Rollen weergeven](../../role-based-access-control/check-access.md)die zijn toegewezen aan een gebruiker met behulp van de Azure Portal. U moet de toepassings-id voor de service-principal die wordt gebruikt door het Uitvoeren als-account, verstrekken. U kunt deze waarde ophalen door naar uw Automation-account in de Azure Portal. Selecteer **Uitvoeren als-accounts** **onder Accountinstellingen** en selecteer het juiste Uitvoeren als-account.

## <a name="scenario-my-problem-isnt-listed-here"></a><a name="other"></a>Scenario: Mijn probleem wordt hier niet vermeld

### <a name="issue"></a>Probleem

U ervaart een probleem of onverwacht resultaat wanneer u VM's buiten bedrijfsuren starten/stoppen die niet op deze pagina wordt vermeld.

### <a name="cause"></a>Oorzaak

Vaak kunnen fouten worden veroorzaakt door het gebruik van een oude en verouderde versie van de functie.

> [!NOTE]
> De VM's buiten bedrijfsuren starten/stoppen is getest met de Azure-modules die in uw Automation-account worden geïmporteerd wanneer u de functie op VM's implementeert. De functie werkt momenteel niet met nieuwere versies van de Azure-module. Deze beperking is alleen van invloed op het Automation-account dat u gebruikt om de VM's buiten bedrijfsuren starten/stoppen. U kunt nog steeds nieuwere versies van de Azure-module gebruiken in uw andere Automation-accounts, zoals beschreven in [Update Azure PowerShell modules](../automation-update-azure-modules.md).

### <a name="resolution"></a>Oplossing

Als u veel fouten wilt oplossen, verwijdert en [updatet u VM's buiten bedrijfsuren starten/stoppen](../automation-solution-vm-management.md#update-the-feature). U kunt ook de [taakstromen controleren om](../automation-runbook-execution.md#job-statuses) te zoeken naar fouten. 

## <a name="next-steps"></a>Volgende stappen

Als u het probleem hier niet ziet of als u het probleem niet kunt oplossen, probeert u een van de volgende kanalen voor aanvullende ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure-forums.](https://azure.microsoft.com/support/forums/)
* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) , het officiële Microsoft Azure account voor het verbeteren van de klantervaring. Ondersteuning voor Azure maakt de Azure-community verbinding met antwoorden, ondersteuning en experts.
* Een incident ondersteuning voor Azure indienen. Ga naar de [ondersteuning voor Azure site](https://azure.microsoft.com/support/options/)en selecteer **Ondersteuning krijgen.**
