---
title: Problemen met Azure Automation gedeelde resource oplossen
description: In dit artikel wordt beschreven hoe u problemen met gedeelde Azure Automation oplossen.
services: automation
ms.subservice: ''
ms.date: 01/27/2021
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b497b0a8f34b4310e3f11beed982c4453fc79159
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830822"
---
# <a name="troubleshoot-shared-resource-issues"></a>Problemen met gedeelde resources oplossen

In dit artikel worden problemen besproken die zich kunnen voordoen wanneer u gedeelde [resources](../automation-intro.md#shared-resources) gebruikt in Azure Automation.

## <a name="modules"></a>Modules

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Scenario: Een module is vastgelopen tijdens het importeren

#### <a name="issue"></a>Probleem

Een module is vastgelopen in *de status Importeren* wanneer u uw modules importeert of bij Azure Automation werkt.

#### <a name="cause"></a>Oorzaak

Omdat het importeren van PowerShell-modules een complex proces met meerdere stappen is, kan een module mogelijk niet correct worden geïmporteerd en kan deze vast komen te zitten in een tijdelijke status. Zie [Importing a PowerShell module (Een PowerShell-module importeren) voor meer informatie over het importproces.](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)

#### <a name="resolution"></a>Oplossing

Als u dit probleem wilt oplossen, moet u de module verwijderen die is vastgelopen met behulp van de cmdlet [Remove-AzAutomationModule.](/powershell/module/Az.Automation/Remove-AzAutomationModule) Vervolgens kunt u proberen de module opnieuw te importeren.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>Scenario: AzureRM-modules zijn vastgelopen tijdens het importeren na een updatepoging

#### <a name="issue"></a>Probleem

Een banner met het volgende bericht blijft in uw account nadat u uw AzureRM-modules hebt bijgewerkt:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Oorzaak

Er is een bekend probleem met het bijwerken van de AzureRM-modules in een Automation-account. Het probleem treedt met name op als de modules zich in een resourcegroep met een numerieke naam die begint met 0.

#### <a name="resolution"></a>Oplossing

Als u uw AzureRM-modules in uw Automation-account wilt bijwerken, moet het account zich in een resourcegroep met een alfanumerieke naam. Resourcegroepen met numerieke namen vanaf 0 kunnen Momenteel geen AzureRM-modules bijwerken.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Scenario: Module kan niet worden geïmporteerd of cmdlets kunnen niet worden uitgevoerd na het importeren

#### <a name="issue"></a>Probleem

Een module kan niet worden geïmporteerd of geïmporteerd, maar er worden geen cmdlets geëxtraheerd.

#### <a name="cause"></a>Oorzaak

Enkele veelvoorkomende redenen waarom een module mogelijk niet kan worden geïmporteerd Azure Automation zijn:

* De structuur komt niet overeen met de structuur die Automation nodig heeft.
* De module is afhankelijk van een andere module die niet is geïmplementeerd in uw Automation-account.
* De afhankelijkheden van de module ontbreken in de map .
* De cmdlet [New-AzAutomationModule](/powershell/module/Az.Automation/New-AzAutomationModule) wordt gebruikt om de module te uploaden en u hebt het volledige opslagpad niet opgegeven of u hebt de module niet geladen met behulp van een openbaar toegankelijke URL.

#### <a name="resolution"></a>Oplossing

Gebruik een van deze oplossingen om het probleem op te lossen:

* Zorg ervoor dat de module de volgende indeling heeft: ModuleName.zip -> ModuleName of Version Number -> (ModuleName.psm1, ModuleName.psd1).
* Open het **.psd1-bestand** en kijk of de module afhankelijkheden heeft. Als dat het gebeurt, uploadt u deze modules naar het Automation-account.
* Zorg ervoor dat alle **DLL-bestanden** waarnaar wordt verwezen, aanwezig zijn in de modulemap.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Scenario: Update-AzureModule.ps1 tijdens het bijwerken van modules

#### <a name="issue"></a>Probleem

Wanneer u het runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) om uw Azure-modules bij te werken, wordt het module-updateproces tijdelijk opgeschort.

#### <a name="cause"></a>Oorzaak

Voor dit runbook is de standaardinstelling om te bepalen hoeveel modules tegelijk worden bijgewerkt 10. Het updateproces is gevoelig voor fouten wanneer er te veel modules tegelijk worden bijgewerkt.

#### <a name="resolution"></a>Oplossing

Het is niet gebruikelijk dat alle AzureRM- of Az-modules zijn vereist in hetzelfde Automation-account. Importeer alleen de specifieke modules die u nodig hebt.

> [!NOTE]
> Importeer niet de hele `Az.Automation` module of `AzureRM.Automation` module, die alle ingesloten modules importeert.

Als het updateproces wordt opgeschort, voegt u de parameter toe aan hetUpdate-AzureModules.ps1script en geeft u een lagere waarde op dan de `SimultaneousModuleImportJobCount` standaardwaarde van  10. Als u deze logica implementeert, probeert u te beginnen met een waarde van 3 of 5. `SimultaneousModuleImportJobCount` is een parameter van het **systeemrunbook Update-AutomationAzureModulesForAccount** dat wordt gebruikt om Azure-modules bij te werken. Als u deze aanpassing maakt, wordt het updateproces langer uitgevoerd, maar hebt u een betere kans om deze te voltooien. In het volgende voorbeeld ziet u de parameter en waar u deze in het runbook kunt plaatsen:

 ```powershell
         $Body = @"
            {
               "properties":{
               "runbook":{
                   "name":"Update-AutomationAzureModulesForAccount"
               },
               "parameters":{
                    ...
                    "SimultaneousModuleImportJobCount":"3",
                    ... 
               }
              }
           }
"@
```

## <a name="run-as-accounts"></a>Uitvoeren als-accounts

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>Scenario: u kunt geen Uitvoeren als-account maken of bijwerken

#### <a name="issue"></a>Probleem

Wanneer u een Uitvoeren als-account probeert te maken of bij te werken, ontvangt u een fout die er ongeveer als volgt uit ziet:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Oorzaak

U hebt niet de machtigingen die u nodig hebt om het Uitvoeren als-account te maken of bij te werken, of de resource is vergrendeld op het niveau van een resourcegroep.

#### <a name="resolution"></a>Oplossing

Als u een Uitvoeren als-account wilt [](../automation-security-overview.md#permissions) maken of bijwerken, moet u over de juiste machtigingen beschikken voor de verschillende resources die worden gebruikt door het Uitvoeren als-account.

Als het probleem wordt veroorzaakt door een vergrendeling, controleert u of de vergrendeling kan worden verwijderd. Ga vervolgens naar de resource die is vergrendeld in Azure Portal, klik met de rechtermuisknop op de vergrendeling en selecteer **Verwijderen.**

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Scenario: U ontvangt de fout 'Kan een toegangspunt met de naam 'GetPerAdapterInfo' in DLL 'iplpapi.dll' niet vinden bij het uitvoeren van een runbook

#### <a name="issue"></a>Probleem

Wanneer u een runbook uitvoert, ontvangt u de volgende uitzondering:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Oorzaak

Deze fout wordt waarschijnlijk veroorzaakt door een onjuist geconfigureerd [Uitvoeren als-account.](../automation-security-overview.md)

#### <a name="resolution"></a>Oplossing

Zorg ervoor dat uw Uitvoeren als-account juist is geconfigureerd. Controleer vervolgens of u de juiste code in uw runbook hebt om te verifiëren met Azure. In het volgende voorbeeld ziet u een codefragment voor verificatie bij Azure in een runbook met behulp van een Uitvoeren als-account.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Volgende stappen

Als dit artikel uw probleem niet verhelpt, probeert u een van de volgende kanalen voor aanvullende ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure-forums.](https://azure.microsoft.com/support/forums/)
* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) . Dit is de officiële Microsoft Azure voor het verbinden van de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.
* Een incident ondersteuning voor Azure indienen. Ga naar ondersteuning voor Azure [site en](https://azure.microsoft.com/support/options/)selecteer **Ondersteuning krijgen.**
