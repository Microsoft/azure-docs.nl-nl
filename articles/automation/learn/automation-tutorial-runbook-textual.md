---
title: Een PowerShell Workflow-runbook maken in Azure Automation
description: In dit artikel leert u een eenvoudige PowerShell Workflow-runbook maken, testen en publiceren.
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: e822e17f154b72854c5150e071301d7bbd53072e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98890812"
---
# <a name="tutorial-create-a-powershell-workflow-runbook"></a>Zelfstudie: Een PowerShell Workflow-runbook maken

In deze zelfstudie wordt stap voor stap het maken van een [PowerShell Workflow-runbook](../automation-runbook-types.md#powershell-workflow-runbooks) in Azure Automation beschreven. PowerShell Workflow-runbooks zijn tekstrunbooks op basis van Windows PowerShell Workflow. U kunt de code van het runbook maken en bewerken met de teksteditor in de Azure-portal. 

> [!div class="checklist"]
> * Een eenvoudige PowerShell Workflow-runbook maken
> * Het runbook testen en publiceren
> * De status van de runbooktaak uitvoeren en bijhouden
> * Het runbook bijwerken om een virtuele Azure-machine te starten met runbookparameters

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](../index.yml) om het runbook te bevatten en te verifiëren voor Azure-resources. Dit account moet machtigingen hebben om de virtuele machine te starten en stoppen.
* Een virtuele machine van Azure. Aangezien u deze machine stopt en start, mag dit geen productie-VM zijn.

## <a name="step-1---create-new-runbook"></a>Stap 1: nieuw runbook maken

Begin door een eenvoudig runbook te maken waarmee de tekst `Hello World` als uitvoer wordt gegeven.

1. Open uw Automation-account in Azure Portal.

   Op de Automation-accountpagina vindt u een beknopte weergave van de resources in dit account. U hebt als het goed is al enkele assets. De meeste van deze assets zijn de modules die automatisch zijn opgenomen in een nieuw Automation-account. U zou ook de referentieasset voor uw abonnement moeten hebben.
 
2. Selecteer **Runbooks** onder **Procesautomatisering** om de lijst van runbooks te openen.

3. Maak een nieuw runbook door **Een nieuw runbook maken** te selecteren.

4. Geef het runbook de naam **MyFirstRunbook-Workflow**.

5. In dit geval gaat u een [PowerShell Workflow-runbook](../automation-runbook-types.md#powershell-workflow-runbooks) maken. Selecteer **PowerShell Workflow** als **Runbooktype**.

6. Klik op **Maken** om het runbook te maken en de teksteditor te openen.

## <a name="step-2---add-code-to-the-runbook"></a>Stap 2: code toevoegen aan het runbook

U kunt de code rechtstreeks in het runbook typen of u kunt cmdlets, runbooks en assets selecteren in het besturingselement Bibliotheek en deze toevoegen aan het runbook met eventuele gerelateerde parameters. In deze zelfstudie typt u de code rechtstreeks in het runbook.

1. Uw runbook is momenteel leeg met alleen het vereiste `Workflow`-trefwoord, de naam van het runbook en de accolades die de volledige werkstroom omsluiten.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

2. Typ `Write-Output "Hello World"` tussen de accolades.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

3. Klik op **Opslaan** om het runbook op te slaan.

## <a name="step-3---test-the-runbook"></a>Stap 3: het runbook testen

Voordat u het runbook publiceert om het beschikbaar te maken in productie, moet u het testen om er zeker van te zijn dat het goed werkt. Wanneer u een runbook test, wordt de conceptversie uitgevoerd en kunt u de uitvoer interactief bekijken.

1. Selecteer **Testvenster** om het testvenster te openen.

2. Klik op **Starten** om de test te beginnen en de enige beschikbare optie te testen.

3. Merk op dat een [runbooktaak](../automation-runbook-execution.md) wordt gemaakt en de status ervan in het venster wordt weergegeven.

   In eerste instantie is de taakstatus 'In de wachtrij geplaatst'. Hiermee wordt aangegeven dat de taak wacht op tot er een runbook worker in de cloud beschikbaar is. De status verandert naar 'Wordt gestart' wanneer de taak wordt geclaimd door een worker. Ten slotte verandert de status naar 'Wordt uitgevoerd' wanneer de runbook daadwerkelijk wordt uitgevoerd.

4. Wanneer de runbooktaak is voltooid, wordt de uitvoer weergegeven in het testvenster. In dit geval ziet u `Hello World`.

   ![Hallo wereld](../media/automation-tutorial-runbook-textual/test-output-hello-world.png)

5. Sluit het testvenster om terug te gaan naar het papier.

## <a name="step-4---publish-and-start-the-runbook"></a>Stap 4: het runbook publiceren en starten

De runbook die u hebt gemaakt, bevindt zich nog steeds in de modus Concept. U moet het publiceren voordat u het in productie kunt uitvoeren. Wanneer u een runbook publiceert, overschrijft u de bestaande gepubliceerde versie met de conceptversie. In dit geval hebt u nog geen gepubliceerde versie omdat het runbook zojuist is gemaakt.

1. Klik op **Publiceren** om het runbook te publiceren en klik vervolgens op **Ja** wanneer hierom wordt gevraagd.

2. Schuif naar links om het runbook weer te geven op de pagina **Runbooks** en let erop dat het veld **Ontwerpstatus** op **Gepubliceerd** staat.

3. Schuif terug naar rechts om de pagina **MyFirstRunbook-Workflow** te bekijken.

   Met de opties bovenaan kunt u nu het runbook starten, een starttijd in de toekomst plannen of een [webhook](../automation-webhooks.md) maken zodat het runbook gestart kan worden via een HTTP-aanroep.

4. Selecteer **Starten** en vervolgens **Ja** wanneer gevraagd wordt om het runbook te starten.

   ![Runbook starten](../media/automation-tutorial-runbook-textual/automation-runbook-controls-start.png)

5. Er wordt een taakdeelvenster geopend voor de runbooktaak die werd aangemaakt. Laat in dit geval het deelvenster openstaan zodat u de voortgang van de taak kunt volgen.

6. Merk op dat de taakstatus wordt weergegeven onder **Taaksamenvatting**. Deze status komt overeen met de statussen bij het testen van het runbook.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-jobsummary.png" alt-text="Schermopname van het deelvenster Taakrunbook, waarbij de sectie Taaksamenvatting is uitgelicht.":::

7. Zodra voor het runbook de status Voltooid wordt weergegeven, klikt u op **Uitvoer**. De pagina Uitvoer wordt geopend waar u uw `Hello World`-bericht kunt zien.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-outputtile.png" alt-text="Schermopname van het deelvenster Taakrunbook, waarbij de knop Uitvoer is uitgelicht.":::

8. Sluit de pagina Uitvoer.

9. Klik op **Alle logboeken** om het deelvenster Streams voor de runbooktaak te openen. U ziet enkel `Hello World` in de uitvoerstroom. Merk op dat er andere stromen voor een runbooktaak kunnen worden weergegeven in het deelvenster Streams, zoals uitgebreide en foutstromen als het runbook hiernaar schrijft.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-alllogstile.png" alt-text="Schermopname van het deelvenster Taakrunbook, waarbij de knop Alle logboeken is uitgelicht.":::

10. Sluit het deelvenster Streams en het deelvenster Taak om terug te gaan naar het deelvenster MyFirstRunbook.

11. Klik op **Taken** onder **Resources** om het deelvenster Taken voor dit runbook te openen. Op deze pagina worden alle taken weergegeven die door uw runbook zijn gemaakt. U zou slechts één taak moeten zien, aangezien u de taak slechts eenmaal hebt uitgevoerd.

   ![Taken](../media/automation-tutorial-runbook-textual/runbook-control-job-tile.png)

12. Klik op de taaknaam om hetzelfde taakvenster te openen dat u hebt bekeken toen u het runbook startte. Gebruik dit deelvenster om de details van een taak die is aangemaakt voor het runbook te bekijken.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Stap 5: verificatie toevoegen voor het beheren van Azure-resources

U hebt het runbook getest en gepubliceerd, maar tot nu toe doet het nog niets nuttigs. U wilt dat er Azure-resources mee worden beheerd. Dat gaat niet zonder verificatie met de referenties voor het abonnement. Verificatie maakt gebruik van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

>[!NOTE]
>Voor PowerShell-runbooks zijn `Add-AzAccount` en `Add-AzureRMAccount` aliassen voor `Connect-AzAccount`. U kunt deze cmdlets gebruiken of u kunt [uw modules bijwerken](../automation-update-azure-modules.md) naar de nieuwste versie in uw Automation-account. Zelfs wanneer u juist een nieuw Automation-account heeft aangemaakt, moet u mogelijk uw modules bijwerken.

1. Ga naar de pagina MyFirstRunbook-Workflow en open de teksteditor door te klikken op **Bewerken**.

2. Verwijder de regel `Write-Output`.

3. Plaats de cursor op een lege regel tussen de accolades.

4. Typ of kopieer en plak de volgende code waarmee de verificatie wordt uitgevoerd met uw Automation uitvoeren als-account.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

5. Klik op **Testvenster** zodat u het runbook kunt testen.

6. Klik op **Start** om de test te starten. Zodra deze is voltooid, ziet u uitvoer zoals hieronder afgebeeld, met basisinformatie van uw account. Deze actie bevestigt dat de referentie geldig is.

   ![Verifiëren](../media/automation-tutorial-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Stap 6: code toevoegen om een virtuele machine te starten

Nu uw runbook verifieert voor het Azure-abonnement, kunt u resources beheren. Laten we een opdracht voor het starten van een virtuele machine toevoegen. U kunt elke VM in uw Azure-abonnement selecteren. Voorlopig hardcodeert u de naam hiervan in het runbook. Als u resources beheert voor verschillende abonnementen, moet u de parameter `AzContext` gebruiken met de cmdlet [Get-AzContext](/powershell/module/az.accounts/get-azcontext).

1. Geef de naam en de resourcegroepnaam van de op te starten VM op door een aanroep naar de cmdlet [Start-AzVN](/powershell/module/Az.Compute/Start-AzVM) in te voeren. 

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures that you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Get-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

2. Sla het runbook op en klik vervolgens op **Testvenster** zodat u het runbook kunt testen.

3. Klik op **Start** om de test te starten. Nadat deze is voltooid, controleert u of de VM is gestart.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Stap 7: een invoerparameter toevoegen aan het runbook

Uw runbook start nu de VM die u heeft vastgelegd in het runbook. Het is handiger om de VM op te geven wanneer het runbook wordt gestart. Laten we dus invoerparameters toevoegen aan het runbook om deze functionaliteit te bieden.

1. Voeg variabelen voor de parameters `VMName` en `ResourceGroupName` toe aan het runbook en gebruik de variabelen met de cmdlet `Start-AzVM` zoals hieronder wordt weergegeven.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Sla het runbook op en open het testvenster. U kunt nu waarden opgeven voor de twee invoervariabelen in de test.

3. Sluit het testvenster.

4. Klik op **Publiceren** om de nieuwe versie van het runbook te publiceren.

5. Stop de VM die u hebt gestart.

6. Klik op **Starten** om het runbook te starten. 

7. Typ waarden in voor **VMNAME** en **RESOURCEGROUPNAME** voor de VM die u gaat starten.

   ![Runbook starten](../media/automation-tutorial-runbook-textual/automation-pass-params.png)

8. Controleer of de VM is gestart wanneer het runbook is voltooid.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [PowerShell Docs](/powershell/scripting/overview) voor meer informatie over PowerShell, inclusief taalreferentie- en leermodules.
* Zie [Az.Automation](/powershell/module/az.automation) voor een naslagdocumentatie voor een PowerShell-cmdlet.
* Zie [Een grafisch runbook maken](automation-tutorial-runbook-graphical.md) om aan de slag te gaan met grafische runbooks.
* Zie [Een PowerShell-runbook maken](automation-tutorial-runbook-textual-powershell.md) om aan de slag te gaan met PowerShell-runbooks.
* Zie [Azure Automation-runbooktypen](../automation-runbook-types.md) voor meer informatie over runbooktypen en hun voordelen en beperkingen.
* Zie [Systeemeigen PowerShell-scriptondersteuning in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/) voor meer informatie over de functies voor PowerShelll-scriptondersteuning.
