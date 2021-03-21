---
title: Een PowerShell-runbook maken in Azure Automation
description: In dit artikel leert u een eenvoudige PowerShell-runbook maken, testen en publiceren.
keywords: azure powershell, zelfstudie powershell-script, powershell-automatisering
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: 896b4db433164471f41aa09791ede5d677028bfb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896610"
---
# <a name="tutorial-create-a-powershell-runbook"></a>Zelfstudie: Een PowerShell-runbook maken

In deze zelfstudie wordt stap voor stap het maken van een [PowerShell-runbook](../automation-runbook-types.md#powershell-runbooks) in Azure Automation beschreven. PowerShell-runbooks zijn gebaseerd op Windows PowerShell. U kunt de code van het runbook rechtstreeks bewerken met de teksteditor in de Azure-portal.

> [!div class="checklist"]
> * Een eenvoudige PowerShell-runbook maken
> * Het runbook testen en publiceren
> * De status van de runbooktaak uitvoeren en bijhouden
> * Het runbook bijwerken om een virtuele Azure-machine te starten met runbookparameters

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](../automation-quickstart-create-account.md) om het runbook te bevatten en te verifiëren voor Azure-resources. Dit account moet machtigingen hebben om de virtuele machine te starten en stoppen.
* Een virtuele machine van Azure. Aangezien u deze machine stopt en start, mag dit geen productie-VM zijn.
* [Imorteer Azure-modules](../shared-resources/modules.md) indien nodig of [update de modules](../automation-update-azure-modules.md) op basis van de cmdlets die u gebruikt.

## <a name="differences-from-powershell-workflow-runbooks"></a>Verschillen met PowerShell Workflow-runbooks

PowerShell-runbooks hebben dezelfde levenscyclus, mogelijkheden en beheer als PowerShell Workflow-runbooks. Er zijn echter wel enkele verschillen en beperkingen.

| Kenmerk  | PowerShell Runbooks | PowerShell Workflow-runbooks |
| ------ | ----- | ----- |
| Snelheid | Worden snel uitgevoerd, omdat ze geen compilatiestap gebruiken. | Worden minder snel uitgevoerd. |
| Controlepunten | Controlepunten worden niet ondersteund. Een PowerShell-runbook kan alleen de bewerking hervatten vanaf het begin. | Gebruikt controlepunten, waardoor een workbook vanaf elk punt de bewerking kan vervolgen. |
| Opdrachtuitvoering | Ondersteunt alleen seriële uitvoering. | Ondersteunt zowel seriële als parallelle uitvoering.|
| Runspace | Een enkele runspace voert alles uit in een script. | Er kan een aparte runspace worden gebruikt voor een activiteit, een opdracht of een scriptblok. |

Naast deze verschillen hebben PowerShell-runbooks enkele [syntactische verschillen](/previous-versions/technet-magazine/dn151046(v=msdn.10)) met PowerShell Workflow-runbooks.

## <a name="step-1---create-runbook"></a>Stap 1: runbook maken

Begin door een eenvoudig runbook te maken waarmee de tekst `Hello World` als uitvoer wordt gegeven.

1. Open uw Automation-account in Azure Portal.

2. Selecteer **Runbooks** onder **Procesautomatisering** om de lijst van runbooks te openen.

3. Maak een nieuw runbook door **Een nieuw runbook maken** te selecteren.

4. Geef het runbook de naam **MyFirstRunbook-PowerShell**.

5. In dit geval gaat u een [PowerShell-runbook](../automation-runbook-types.md#powershell-runbooks) maken. Selecteer **PowerShell** als **Runbooktype**.

6. Klik op **Maken** om het runbook te maken en de teksteditor te openen.

## <a name="step-2---add-code-to-the-runbook"></a>Stap 2: code toevoegen aan het runbook

U kunt de code rechtstreeks in het runbook typen of u kunt cmdlets, runbooks en assets selecteren in het besturingselement Bibliotheek en deze laten toevoegen aan het runbook met eventuele gerelateerde parameters. In deze zelfstudie typt u de code rechtstreeks in het runbook.

1. Uw runbook is momenteel leeg. Typ `Write-Output "Hello World"` in de hoofdtekst van het script.

   ![Hallo wereld](../media/automation-tutorial-runbook-textual-powershell/automation-helloworld.png)

2. Klik op **Opslaan** om het runbook op te slaan.

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"> </a> Stap 3: het runbook testen

Voordat u het runbook publiceert om het beschikbaar te maken in productie, moet u het testen om er zeker van te zijn dat het goed werkt. Wanneer u een runbook test, wordt de conceptversie uitgevoerd en kunt u de uitvoer interactief bekijken.

1. Klik op **Testvenster** om het testvenster te openen.

2. Klik op **Start** om de test te starten. Dit moet de enige ingeschakelde optie zijn.

3. Merk op dat een [runbooktaak](../automation-runbook-execution.md) wordt gemaakt en de status ervan in het venster wordt weergegeven.

   In eerste instantie is de taakstatus 'In de wachtrij geplaatst'. Hiermee wordt aangegeven dat de taak wacht op tot er een runbook worker in de cloud beschikbaar is. De status verandert naar 'Wordt gestart' wanneer de taak wordt geclaimd door een worker. Ten slotte verandert de status naar 'Wordt uitgevoerd' wanneer de runbook daadwerkelijk wordt uitgevoerd.

4. Wanneer de runbooktaak is voltooid, wordt de uitvoer weergegeven in het testvenster. In dit geval ziet u `Hello World`.

   ![Uitvoer testvenster](../media/automation-tutorial-runbook-textual-powershell/automation-testpane-output.png)

5. Sluit het testvenster om terug te gaan naar het papier.

## <a name="step-4---publish-and-start-the-runbook"></a>Stap 4: het runbook publiceren en starten

Het runbook dat u hebt gemaakt, bevindt zich nog steeds in de modus Concept. Het moet worden gepubliceerd voordat u het in productie kan uitvoeren. Wanneer u een runbook publiceert, overschrijft u de bestaande gepubliceerde versie met de conceptversie. In dit geval hebt u nog geen gepubliceerde versie omdat het runbook zojuist is gemaakt.

1. Klik op **Publiceren** om het runbook te publiceren en klik vervolgens op **Ja** wanneer hierom wordt gevraagd.

2. Schuif naar links om het runbook weer te geven op de pagina Runbooks en let erop dat het veld **Ontwerpstatus** op **Gepubliceerd** staat.

3. Schuif terug naar rechts om de pagina voor **MyFirstRunbook-PowerShell** weer te geven.
   
   Met de opties bovenaan kunt u nu het runbook starten, een starttijd in de toekomst plannen of een [webhook](../automation-webhooks.md) maken zodat het runbook gestart kan worden via een HTTP-aanroep.

4. Selecteer **Starten** en vervolgens **Ja** wanneer gevraagd wordt om het runbook te starten. 

5. Er wordt een taakdeelvenster geopend voor de runbooktaak die werd aangemaakt. U zou dit deelvenster kunnen sluiten, maar laat het open, zodat u de voortgang van de taak kunt bekijken. De taakstatus word weergegeven in **Taakoverzicht** en mogelijke statussen zijn zoals beschreven voor testen van het runbook.

   ![Taakoverzicht](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

6. Zodra de runbookstatus 'Voltooid' aangeeft, klikt u op **Uitvoer** om de pagina Uitvoer te openen waar u `Hello World` kunt zien.

   ![Taakuitvoer](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

7. Sluit de pagina Uitvoer.

8. Klik op **Alle logboeken** om het deelvenster Streams voor de runbooktaak te openen. U ziet enkel `Hello World` in de uitvoerstroom.

    Merk op dat er andere stromen voor een runbooktaak kunnen worden weergegeven in het deelvenster Streams, zoals uitgebreide en foutstromen als het runbook hiernaar schrijft.

   ![Alle logboeken](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

9. Sluit het deelvenster Streams en het deelvenster Taak om terug te gaan naar de pagina MyFirstRunbook-PowerShell.

10. Klik op **Taken** onder **Details** om de pagina Taken voor dit runbook te openen. Op deze pagina worden alle taken weergegeven die door uw runbook zijn gemaakt. U zou slechts één taak moeten zien, aangezien u de taak slechts eenmaal hebt uitgevoerd.

   ![Takenlijst](../media/automation-tutorial-runbook-textual-powershell/runbook-control-job-tile.png)

11. Klik op de taaknaam om hetzelfde taakvenster te openen dat u hebt bekeken toen u het runbook startte. Gebruik dit deelvenster om de details van een taak die is aangemaakt voor het runbook te bekijken.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Stap 5: verificatie toevoegen voor het beheren van Azure-resources

U hebt het runbook getest en gepubliceerd, maar tot nu toe doet het nog niets nuttigs. U wilt dat er Azure-resources mee worden beheerd. Hiervoor moet het runbook de Uitvoeren als-account kunnen verifiëren die automatisch is gemaakt toen u uw Automation-account hebt gemaakt.

Zoals u in onderstaand voorbeeld kunt zien, wordt de Uitvoeren als-verbinding gemaakt met de [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)-cmdlet. Als u resources beheert voor verschillende abonnementen, moet u de parameter `AzContext` gebruiken met [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext).

> [!NOTE]
> Voor PowerShell-runbooks zijn `Add-AzAccount` en `Add-AzureRMAccount` aliassen voor `Connect-AzAccount`. U kunt deze cmdlets gebruiken of u kunt [uw modules bijwerken](../automation-update-azure-modules.md) naar de nieuwste versie in uw Automation-account. Zelfs wanneer u zojuist een nieuw Automation-account hebt aangemaakt, moet u mogelijk uw modules bijwerken.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $AzureContext
   ```

1. Open de teksteditor door te klikken op **Bewerken** in de pagina MyFirstRunbook-PowerShell.

2. U hebt de `Write-Output`-regel niet meer nodig. Verwijder deze.

3. Typ of kopieer en plak de volgende code waarmee de verificatie wordt uitgevoerd met uw Automation uitvoeren als-account.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

4. Klik op **Testvenster** zodat u het runbook kunt testen.

5. Klik op **Start** om de test te starten. Zodra deze is voltooid, ziet u uitvoer zoals hieronder afgebeeld, met basisinformatie van uw account. Deze uitvoer bevestigt dat de Uitvoeren als-account geldig is.

   ![Verifiëren](../media/automation-tutorial-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Stap 6: code toevoegen om een virtuele machine te starten

Nu uw runbook verifieert voor uw Azure-abonnement, kunt u resources beheren. Laten we een opdracht voor het starten van een virtuele machine toevoegen. U kunt elke virtuele machine in uw Azure-abonnement selecteren en de naam hiervan voorlopig hardcoderen in het runbook.

1. Voeg aan uw runbookscript de cmdlet [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) toe om de virtuele machine te starten. Zoals u hieronder kunt zien start de cmdlet een virtuele machine met de naam `VMName` en een resourcegroep met de naam `ResourceGroupName`.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

2. Sla het runbook op en klik vervolgens op **Testvenster** zodat u het runbook kunt testen.

3. Klik op **Start** om de test te starten. Nadat deze is voltooid, controleert u of de virtuele machine is gestart.

## <a name="step-7---add-an-input-parameter"></a>Stap 7: een invoerparameter toevoegen

Uw runbook start nu de virtuele machine die u hebt vastgelegd in het runbook. Het runbook is praktischer als u de virtuele machine specificeert wanneer het runbook wordt gestart. Laten we dus invoerparameters toevoegen aan het runbook om deze functionaliteit te bieden.

1. Wijzig in de teksteditor de `Start-AzVM`-cmdlet om variabelen te gebruiken voor de parameters `VMName` en `ResourceGroupName`. 

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

2. Sla het runbook op en open het testvenster. U kunt nu waarden opgeven voor de twee invoervariabelen die in de test worden gebruikt.

3. Sluit het testvenster.

4. Klik op **Publiceren** om de nieuwe versie van het runbook te publiceren.

5. Stop de virtuele machine die u eerder hebt gestart.

6. Klik op **Starten** om het runbook te starten. 

7. Typ waarden voor **VMNAME** en **RESOURCEGROUPNAME** voor de virtuele machine die u wilt starten en klik dan op **OK**.

    ![Parameter doorgeven](../media/automation-tutorial-runbook-textual-powershell/automation-pass-params.png)

8. Controleer of de virtuele machine is gestart wanneer het runbook is voltooid.

## <a name="next-steps"></a>Volgende stappen

* Zie de [PowerShell Docs](/powershell/scripting/overview) voor meer informatie over PowerShell, inclusief taalreferentie- en leermodules.
* Zie [Az.Automation](/powershell/module/az.automation) voor een naslagdocumentatie voor een PowerShell-cmdlet.
* Zie [Een grafisch runbook maken](automation-tutorial-runbook-graphical.md) om aan de slag te gaan met grafische runbooks.
* Zie [Een PowerShell Workflow-runbook maken](automation-tutorial-runbook-textual.md) om aan de slag te gaan met PowerShell Workflow-runbooks.
* Zie [Azure Automation-runbooktypen](../automation-runbook-types.md) voor meer informatie over runbooktypen en hun voordelen en beperkingen.
* Zie [Systeemeigen PowerShell-scriptondersteuning in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) voor meer informatie over de functie voor de PowerShelll-scriptondersteuning.
