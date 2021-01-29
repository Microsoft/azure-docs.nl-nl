---
title: Problemen met Azure Automation gedeelde bronnen oplossen
description: In dit artikel leest u hoe u problemen met Azure Automation gedeelde bronnen kunt oplossen en oplossen.
services: automation
ms.subservice: ''
ms.date: 01/27/2021
ms.topic: troubleshooting
ms.openlocfilehash: 1a822166ae4c2bf793e0fa50e93018f499fcc27a
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053616"
---
# <a name="troubleshoot-shared-resource-issues"></a>Problemen met gedeelde bronnen oplossen

In dit artikel worden problemen beschreven die zich kunnen voordoen wanneer u [gedeelde bronnen](../automation-intro.md#shared-resources) gebruikt in azure Automation.

## <a name="modules"></a>Modules

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Scenario: een module is vastgelopen tijdens het importeren

#### <a name="issue"></a>Probleem

Wanneer u uw Azure Automation-modules importeert of bijwerkt, is er een module vastgelopen in de *import* status.

#### <a name="cause"></a>Oorzaak

Omdat het importeren van Power shell-modules een complexe werk proces is, kan een module mogelijk niet correct worden geïmporteerd en vastlopen in een tijdelijke status. Zie [een Power shell-module importeren](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)voor meer informatie over het import proces.

#### <a name="resolution"></a>Oplossing

Om dit probleem op te lossen, moet u de module verwijderen die vastzit met de cmdlet [Remove-AzAutomationModule](/powershell/module/Az.Automation/Remove-AzAutomationModule) . U kunt de module vervolgens opnieuw importeren.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>Scenario: AzureRM-modules blijven hangen tijdens het importeren na een update poging

#### <a name="issue"></a>Probleem

Een banner met het volgende bericht blijft in uw account na het bijwerken van uw AzureRM-modules:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Oorzaak

Er is een bekend probleem met het bijwerken van de AzureRM-modules in een Automation-account. Het probleem treedt met name op als de modules zich in een resource groep bevinden met een numerieke naam die begint met 0.

#### <a name="resolution"></a>Oplossing

Als u uw AzureRM-modules in uw Automation-account wilt bijwerken, moet het account zich in een resource groep met een alfanumerieke naam be. Resource groepen met een numerieke naam die begint met 0, kunnen op dit moment geen AzureRM-modules bijwerken.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Scenario: de module kan niet worden geïmporteerd of cmdlets kunnen niet worden uitgevoerd na het importeren

#### <a name="issue"></a>Probleem

Een module kan niet worden geïmporteerd of worden geïmporteerd, maar er worden geen cmdlets geëxtraheerd.

#### <a name="cause"></a>Oorzaak

Enkele veelvoorkomende redenen waarom een module mogelijk niet met succes kan worden geïmporteerd naar Azure Automation zijn:

* De structuur komt niet overeen met de structuur die nodig is voor automatisering.
* De module is afhankelijk van een andere module die niet is geïmplementeerd in uw Automation-account.
* De afhankelijkheden van de module ontbreken in de map.
* De cmdlet [New-AzAutomationModule](/powershell/module/Az.Automation/New-AzAutomationModule) wordt gebruikt om de module te uploaden, en u hebt niet het volledige opslagpad gegeven of u hebt de module niet geladen met behulp van een openbaar toegankelijke URL.

#### <a name="resolution"></a>Oplossing

Gebruik een van de volgende oplossingen om het probleem op te lossen:

* Zorg ervoor dat de module de volgende indeling heeft: ModuleName.zip-> module of versie nummer-> (module naam. psm1, ModuleName.psd1).
* Open het **. psd1** -bestand en controleer of de module afhankelijkheden heeft. Als dit het geval is, uploadt u deze modules naar het Automation-account.
* Zorg ervoor dat alle **. dll** -bestanden waarnaar wordt verwezen, aanwezig zijn in de module map.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Scenario: Update-AzureModule.ps1 onderbroken bij het bijwerken van modules

#### <a name="issue"></a>Probleem

Wanneer u het [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbook gebruikt om uw Azure-modules bij te werken, wordt het update proces van de module onderbroken.

#### <a name="cause"></a>Oorzaak

Voor dit runbook is de standaard instelling om te bepalen hoeveel modules tegelijkertijd worden bijgewerkt, is 10. Het update proces is gevoelig voor fouten wanneer te veel modules tegelijk worden bijgewerkt.

#### <a name="resolution"></a>Oplossing

Het is niet gebruikelijk dat alle AzureRM-of AZ-modules zijn vereist in hetzelfde Automation-account. Importeer alleen de specifieke modules die u nodig hebt.

> [!NOTE]
> Vermijd het importeren van het hele `Az.Automation` of `AzureRM.Automation` -module, waardoor alle opgenomen modules worden geïmporteerd.

Als het update proces wordt onderbroken, voegt `SimultaneousModuleImportJobCount` u de para meter toe aan het **Update-AzureModules.ps1** script en geeft u een lagere waarde dan de standaard instelling van 10. Als u deze logica implementeert, kunt u beginnen met een waarde van 3 of 5. `SimultaneousModuleImportJobCount` is een para meter van het runbook **Update-AutomationAzureModulesForAccount** System dat wordt gebruikt om Azure-modules bij te werken. Als u deze aanpassing aanbrengt, wordt het update proces langer uitgevoerd, maar heeft het een grotere kans om te volt ooien. In het volgende voor beeld ziet u de para meter en waar u deze in het runbook plaatst:

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

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>Scenario: u kunt een uitvoeren als-account niet maken of bijwerken

#### <a name="issue"></a>Probleem

Wanneer u een uitvoeren als-account probeert te maken of bij te werken, wordt een fout bericht van de volgende strekking weer gegeven:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Oorzaak

U hebt niet de machtigingen die u nodig hebt om het uitvoeren als-account te maken of bij te werken, of de resource is vergrendeld op het niveau van een resource groep.

#### <a name="resolution"></a>Oplossing

Als u een uitvoeren als-account wilt maken of bijwerken, moet u de juiste [machtigingen](../automation-security-overview.md#permissions) hebben voor de verschillende resources die worden gebruikt door het run as-account.

Als het probleem wordt veroorzaakt door een vergren deling, controleert u of de vergren deling kan worden verwijderd. Ga vervolgens naar de resource die is vergrendeld in Azure Portal, klik met de rechter muisknop op de vergren deling en selecteer **verwijderen**.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Scenario: u ontvangt de fout ' kan geen ingangs punt vinden met de naam ' GetPerAdapterInfo ' in DLL-iplpapi.dll ' ' tijdens het uitvoeren van een runbook

#### <a name="issue"></a>Probleem

Wanneer u een runbook uitvoert, wordt de volgende uitzonde ring weer gegeven:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Oorzaak

Deze fout wordt waarschijnlijk veroorzaakt door een onjuist geconfigureerd [uitvoeren als-account](../automation-security-overview.md).

#### <a name="resolution"></a>Oplossing

Zorg ervoor dat het uitvoeren als-account correct is geconfigureerd. Controleer vervolgens of u de juiste code in uw runbook hebt om te verifiëren met Azure. In het volgende voor beeld ziet u een code fragment voor het verifiëren van Azure in een runbook met behulp van een uitvoeren als-account.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet kunt oplossen met dit artikel, kunt u een van de volgende kanalen proberen voor aanvullende ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure-forums](https://azure.microsoft.com/support/forums/).
* Verbinding maken met [@AzureSupport](https://twitter.com/azuresupport) . Dit is het officiële Microsoft Azure account voor het verbinden van de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.
* Een ondersteunings incident voor Azure. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/)en selecteer **ondersteuning verkrijgen**.