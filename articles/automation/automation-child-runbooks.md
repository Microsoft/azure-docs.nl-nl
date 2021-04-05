---
title: Modulaire runbooks maken in Azure Automation
description: In dit artikel leest u hoe u een runbook maakt dat wordt aangeroepen door een ander runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: f0dd5cf5209924972080af6d22429252338754de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99491245"
---
# <a name="create-modular-runbooks"></a>Modulaire runbooks maken

Het is een aanbevolen procedure in Azure Automation om herbruikbare, modulaire runbooks te schrijven met een discrete functie die wordt aangeroepen door andere runbooks. Een bovenliggend runbook roept vaak een of meer onderliggende runbooks aan om de vereiste functionaliteit uit te voeren. 

Er zijn twee manieren om een onderliggend runbook aan te roepen en er zijn verschillende verschillen die u moet begrijpen om te bepalen welke het meest geschikt is voor uw scenario's. De volgende tabel bevat een overzicht van de verschillen tussen de twee manieren om een runbook van een andere te bellen.

|  | Inline | Cmdlet |
|:--- |:--- |:--- |
| **Taak** |Onderliggende runbooks worden uitgevoerd in dezelfde taak als het bovenliggende runbook. |Er wordt een afzonderlijke taak gemaakt voor het onderliggende runbook. |
| **Uitvoering** |Bovenliggend runbook wacht tot het onderliggende runbook is voltooid voordat het verdergaat. |Het bovenliggende runbook blijft onmiddellijk nadat het onderliggende runbook is gestart *of* het bovenliggende runbook wacht totdat de onderliggende taak is voltooid. |
| **Uitvoer** |Bovenliggend runbook kan uitvoer rechtstreeks uit onderliggend runbook verkrijgen. |Bovenliggend runbook moet uitvoer ophalen van onderliggend runbook-taak *of* bovenliggend runbook kan rechtstreeks uitvoer ophalen van onderliggend runbook. |
| **Parameters** |Waarden voor de parameters van onderliggend runbook worden afzonderlijk opgegeven en kunnen elk gegevenstype hebben. |Waarden voor de para meters van het onderliggende runbook moeten worden gecombineerd in één hashtabel. Deze hashtabel kan alleen eenvoudige, matrix-en object gegevens typen bevatten die gebruikmaken van JSON-serialisatie. |
| **Automation-account** |Bovenliggend runbook kan alleen een onderliggend runbook gebruiken in hetzelfde Automation-account. |Bovenliggende runbooks kunnen een onderliggend runbook gebruiken uit elk Automation-account, van hetzelfde Azure-abonnement en zelfs van een ander abonnement waarmee u verbinding hebt. |
| **Publiceren** |Onderliggend runbook moet zijn gepubliceerd voordat bovenliggend runbook wordt gepubliceerd. |Onderliggend runbook wordt gepubliceerd telkens voordat het bovenliggende runbook wordt gestart. |

## <a name="invoke-a-child-runbook-using-inline-execution"></a>Een onderliggend runbook aanroepen met inline-uitvoering

Als u een runbook inline vanuit een ander runbook wilt aanroepen, gebruikt u de naam van het runbook en geeft u waarden op voor de para meters, net zoals u een activiteit of cmdlet zou gebruiken. Alle runbooks in hetzelfde Automation-account zijn beschikbaar voor alle anderen die op deze manier moeten worden gebruikt. Het bovenliggende runbook wacht tot het onderliggende runbook is voltooid voordat het naar de volgende regel wordt verplaatst. alle uitvoer wordt rechtstreeks naar de bovenliggende opdracht geretourneerd.

Als u een runbook inline aanroept, wordt dit uitgevoerd in dezelfde taak als het bovenliggende runbook. Er is geen indicatie van de taak geschiedenis van het onderliggende runbook. Eventuele uitzonde ringen en stroom uitvoer van het onderliggende runbook zijn gekoppeld aan de bovenliggende. Dit gedrag resulteert in minder taken en maakt het eenvoudiger om te volgen en problemen op te lossen.

Wanneer een runbook wordt gepubliceerd, moeten alle onderliggende runbooks die worden aangeroepen, al worden gepubliceerd. De reden hiervoor is dat Azure Automation een koppeling bouwt met een wille keurige onderliggende runbooks wanneer het een runbook compileert. Als de onderliggende runbooks nog niet zijn gepubliceerd, lijkt het bovenliggende runbook goed te publiceren, maar genereert een uitzonde ring wanneer het wordt gestart. Als dit het geval is, kunt u het bovenliggende runbook opnieuw publiceren om naar behoren naar de onderliggende runbooks te verwijzen. U hoeft het bovenliggende runbook niet opnieuw te publiceren als een onderliggend runbook is gewijzigd, omdat de koppeling al is gemaakt.

De para meters van een onderliggend runbook met de naam inline kunnen van elk gegevens type zijn, inclusief complexe objecten. Er is geen [JSON-serialisatie](start-runbooks.md#work-with-runbook-parameters), omdat u het runbook start met behulp van de Azure portal of met de cmdlet [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) .

### <a name="runbook-types"></a>Runbooktypen

Welke typen runbook kunnen elkaar aanroepen?

* Een [Power shell-runbook](automation-runbook-types.md#powershell-runbooks) en een [grafisch runbook](automation-runbook-types.md#graphical-runbooks) kunnen elk ander inline aanroepen, zoals op Power shell is gebaseerd.
* Een [Power shell workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks) en een grafisch Power shell werk stroom-runbook kunnen elk ander inline aanroepen, zowel op Power shell-werk stroom gebaseerd.
* De Power shell-typen en de Power shell-werk stroom typen kunnen niet elk ander inline aanroepen en moeten worden gebruikt `Start-AzAutomationRunbook` .

Wanneer is de volg orde van publicatie belang rijk?

De publicatie volgorde van runbooks is alleen van belang voor Power shell-werk stromen en grafische runbooks in Power shell-werk stromen.

Als uw runbook een grafisch of een onderliggend runbook van een Power shell-werk stroom aanroept met inline-uitvoering, wordt de naam van het runbook gebruikt. De naam moet beginnen met `.\\` om op te geven dat het script zich in de lokale map bevindt.

### <a name="example"></a>Voorbeeld

In het volgende voor beeld wordt een onderliggend runbook voor een test gestart dat een complex object, een geheel getal en een Booleaanse waarde accepteert. De uitvoer van het onderliggende runbook is toegewezen aan een variabele. In dit geval is het onderliggende runbook een Power shell workflow-runbook.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Hier volgt een voor beeld van het gebruik van een Power shell-runbook als onderliggend object.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="start-a-child-runbook-using-a-cmdlet"></a>Een onderliggend runbook starten met een cmdlet

> [!IMPORTANT]
> Als uw runbook een onderliggend runbook aanroept met de `Start-AzAutomationRunbook` cmdlet met de `Wait` para meter en het onderliggende runbook een object resultaat oplevert, kan er een fout optreden in de bewerking. Als u de fout wilt omzeilen, raadpleegt u [onderliggende runbooks met object uitvoer](troubleshoot/runbooks.md#child-runbook-object) voor meer informatie over het implementeren van de logica voor het controleren van de resultaten met behulp van de cmdlet [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) .

U kunt gebruiken `Start-AzAutomationRunbook` om een runbook te starten zoals beschreven in [om een runbook te starten met Windows Power shell](start-runbooks.md#start-a-runbook-with-powershell). Er zijn twee modi voor het gebruik van deze cmdlet. In de ene modus retourneert de cmdlet de taak-ID wanneer de taak voor het onderliggende runbook is gemaakt. In de andere modus, die door het script wordt ingeschakeld door het opgeven van de *wait* -para meter, wacht de cmdlet totdat de onderliggende taak is voltooid en de uitvoer van het onderliggende runbook wordt geretourneerd.

De taak uit een onderliggend runbook dat is gestart met een cmdlet, wordt afzonderlijk van de bovenliggende runbook-taak uitgevoerd. Dit gedrag resulteert in meer taken dan bij het starten van het runbook inline en maakt het lastig om de taken bij te houden. De bovenliggende site kan meer dan een onderliggend runbook asynchroon starten zonder te wachten tot de bewerking is voltooid. Voor deze parallelle uitvoering die de onderliggende runbooks inline aanroept, moet het bovenliggende runbook het [sleutel woord parallel](automation-powershell-workflow.md#use-parallel-processing)gebruiken.

De onderliggende runbook-uitvoer wordt niet betrouwbaar teruggestuurd naar het bovenliggende runbook vanwege een time-out. Daarnaast kunnen variabelen zoals `$VerbosePreference` , `$WarningPreference` en andere niet worden door gegeven aan de onderliggende runbooks. Als u deze problemen wilt voor komen, kunt u de onderliggende runbooks starten als afzonderlijke automatiserings taken met behulp `Start-AzAutomationRunbook` van de `Wait` para meter. Deze techniek blokkeert het bovenliggende runbook tot het onderliggende runbook is voltooid.

Als u niet wilt dat het bovenliggende runbook wordt geblokkeerd tijdens het wachten, kunt u het onderliggende runbook starten met `Start-AzAutomationRunbook` zonder de `Wait` para meter. In dit geval moet uw runbook [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) gebruiken om te wachten tot de taak is voltooid. Het moet ook [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) en [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) gebruiken om de resultaten op te halen.

Para meters voor een onderliggend runbook dat is gestart met een cmdlet, worden opgegeven als een hash-tabel, zoals beschreven in [runbook-para meters](start-runbooks.md#work-with-runbook-parameters). Alleen eenvoudige gegevens typen kunnen worden gebruikt. Als het runbook een parameter met een complex gegevenstype heeft, moet dit inline worden aangeroepen.

De context van het abonnement kan verloren gaan bij het starten van onderliggende runbooks als afzonderlijke taken. Voor het onderliggende runbook om AZ module-cmdlets uit te voeren op basis van een specifiek Azure-abonnement, moet het onderliggend element worden geverifieerd bij dit abonnement onafhankelijk van het bovenliggende runbook.

Als taken binnen hetzelfde Automation-account werken met meer dan één abonnement, kan het selecteren van een abonnement in de ene taak de momenteel geselecteerde abonnements context voor andere taken wijzigen. Gebruik `Disable-AzContextAutosave –Scope Process` aan het begin van elk runbook om deze situatie te voor komen. Met deze actie wordt de context alleen opgeslagen bij de uitvoering van het runbook.

### <a name="example"></a>Voorbeeld

In het volgende voor beeld wordt een onderliggend runbook met para meters gestart en wordt gewacht tot het is voltooid met de `Start-AzAutomationRunbook` cmdlet met de `Wait` para meter. Zodra dit is voltooid, wordt de uitvoer van de cmdlet verzameld van het onderliggende runbook. Als u wilt gebruiken `Start-AzAutomationRunbook` , moet het script worden geverifieerd bij uw Azure-abonnement.

```azurepowershell-interactive
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Set-AzContext -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –Wait
```

## <a name="next-steps"></a>Volgende stappen

* Als u het runbook wilt uitvoeren, raadpleegt u [een Runbook starten in azure Automation](start-runbooks.md).
* Zie [runbook-uitvoer en berichten in azure Automation](automation-runbook-output-and-messages.md)voor het controleren van de runbook-bewerking.
