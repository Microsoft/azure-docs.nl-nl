---
title: Een grafisch runbook maken in Azure Automation
description: In dit artikel leert u hoe u een eenvoudig grafisch runbook kunt maken, testen en publiceren in Azure Automation.
keywords: runbook, runbook-sjabloon, runbook-automatisering, azure-runbook
services: automation
ms.subservice: process-automation
ms.date: 09/15/2020
ms.topic: tutorial
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 668f661d2c8f349f4dac3f850800d83890d2f4d9
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831434"
---
# <a name="tutorial-create-a-graphical-runbook"></a>Zelfstudie: Een grafisch runbook maken

In deze zelfstudie wordt stap voor stap het maken van een [grafisch runbook](../automation-runbook-types.md#graphical-runbooks) in Azure Automation beschreven. U kunt grafische PowerShell Werkstroom-runbooks maken en bewerken met behulp van de grafische editor in Azure Portal. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een eenvoudig grafisch runbook maken
> * Het runbook testen en publiceren
> * De status van de runbooktaak uitvoeren en bijhouden
> * Het runbook bijwerken om een virtuele Azure-machine te starten met runbookparameters en voorwaardelijke links

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free).
* [Automation-account](../index.yml) om het runbook te bevatten en te verifiëren voor Azure-resources. Dit account moet machtigingen hebben om de virtuele machine te starten en stoppen.
* Een virtuele machine van Azure. Aangezien u deze machine stopt en start, mag dit geen productie-VM zijn.
* [Imorteer Azure-modules](../shared-resources/modules.md) indien nodig of [update de modules](../automation-update-azure-modules.md) op basis van de cmdlets die u gebruikt.

## <a name="step-1---create-runbook"></a>Stap 1: runbook maken

Begin door een eenvoudig runbook te maken waarmee de tekst `Hello World` als uitvoer wordt gegeven.

1. Open uw Automation-account in Azure Portal.

    Op de Automation-accountpagina vindt u een beknopte weergave van de resources in dit account. U hebt als het goed is al enkele assets. De meeste van deze assets zijn de modules die automatisch zijn opgenomen in een nieuw Automation-account. U zou ook de referentieasset voor uw abonnement moeten hebben.

2. Selecteer **Runbooks** onder **Procesautomatisering** om de lijst van runbooks te openen.

3. Maak een nieuw runbook door **Een nieuw runbook maken** te selecteren.

4. Geef het runbook de naam **MyFirstRunbook-Graphical**.

5. In dit geval gaat u een [grafisch runbook](../automation-graphical-authoring-intro.md) maken. Selecteer **Grafisch** voor **Runbook-type**.

    ![Nieuw runbook](../media/automation-tutorial-runbook-graphical/create-new-runbook.png)

6. Klik op **Maken** om het runbook te maken en de grafische editor te openen.

## <a name="step-2---add-activities"></a>Stap 2: activiteiten toevoegen

Met het besturingselement Bibliotheek aan de linkerkant van de editor kunt u activiteiten selecteren die u aan uw runbook wilt toevoegen. U gaat een `Write-Output`-cmdlet toevoegen om tekst als uitvoer van het runbook te krijgen.

1. Klik in het besturingselement bibliotheek op het zoekveld en typ `write-output`. Zoekresultaten worden weergegeven op de volgende afbeelding.

    ![Microsoft.PowerShell.Utility](../media/automation-tutorial-runbook-graphical/search-powershell-cmdlet-writeoutput.png)

2. Schuif omlaag naar de onderkant van de lijst. Klik met de rechtermuisknop op **Write-output** en selecteer **Toevoegen aan canvas**. U kunt ook op het beletselteken (...) naast de naam van de cmdlet klikken en vervolgens **Toevoegen aan canvas** selecteren.

3. Klik op de activiteit **Write-Output** op het papier. Met deze actie opent u de besturingselementpagina Configuratie waarmee u de activiteit kunt configureren.

4. Het veld **Label** krijgt standaard de naam van de cmdlet, maar u kunt dit wijzigen in een duidelijkere beschrijving. Wijzig deze in `Write Hello World to output`.

5. Klik op **Parameters** om waarden op te geven voor de parameters van de cmdlet.

   Sommige cmdlets hebben meerdere parametersets. U moet dan selecteren welke u wilt gebruiken. In dit geval heeft `Write-Output` slechts één parameter ingesteld.

6. Selecteer de parameter `InputObject`. Dit is de parameter die u gebruikt om de tekst op te geven die naar de uitvoerstroom moet worden verzonden.

7. In de vervolgkeuzelijst **Gegevensbron** staan bronnen die u kunt gebruiken om een parameterwaarde te vullen. Selecteer in dit menu de optie **Power shell-expressie**.

   U kunt uitvoer van deze bronnen gebruiken, zoals een andere activiteit, een Automation-asset of een PowerShell-expressie. In dit geval is de uitvoer gewoon `Hello World`. U kunt een PowerShell-expressie gebruiken en een tekenreeks opgeven.

8. Typ in het veld **Expressie** de tekst `"Hello World"` en klik vervolgens tweemaal op **OK** om terug te gaan naar het canvas.

9. Klik op **Opslaan** om het runbook op te slaan.

## <a name="step-3---test-the-runbook"></a>Stap 3: het runbook testen

Voordat u het runbook publiceert om het beschikbaar te maken in productie, moet u het testen om er zeker van te zijn dat het goed werkt. Wanneer u een runbook test, wordt de conceptversie uitgevoerd en kunt u de uitvoer interactief bekijken.

1. Selecteer **Testvenster** om het testvenster te openen.

2. Klik op **Start** om de test te starten. Dit moet de enige ingeschakelde optie zijn.

3. Merk op dat een [runbooktaak](../automation-runbook-execution.md) wordt gemaakt en de status ervan in het venster wordt weergegeven.

   In eerste instantie is de taakstatus `Queued`. Hiermee wordt aangegeven dat de taak wacht op tot er een runbook worker in de cloud beschikbaar is. De status verandert naar `Starting` wanneer de taak wordt geclaimd door een worker. Ten slotte verandert de status naar `Running` wanneer het runbook effectief wordt uitgevoerd.

4. Wanneer de runbooktaak is voltooid, wordt de uitvoer weergegeven in het testvenster. In dit geval ziet u `Hello World`.

    ![Uitvoer van runbook Hallo wereld](../media/automation-tutorial-runbook-graphical/runbook-test-results.png)

5. Sluit het testvenster om terug te gaan naar het papier.

## <a name="step-4---publish-and-start-the-runbook"></a>Stap 4: het runbook publiceren en starten

Het runbook dat u hebt gemaakt, bevindt zich nog steeds in de modus Concept. Het moet worden gepubliceerd voordat u het in productie kan uitvoeren. Wanneer u een runbook publiceert, overschrijft u de bestaande gepubliceerde versie met de conceptversie. In dit geval hebt u nog geen gepubliceerde versie omdat het runbook zojuist is gemaakt.

1. Selecteer **Publiceren** om het runbook te publiceren en vervolgens **Ja** wanneer hierom wordt gevraagd.

2. Schuif naar links om het runbook weer te geven op de pagina Runbooks en let erop dat het veld **Ontwerpstatus** op **Gepubliceerd** staat.

3. Schuif terug naar rechts om de pagina **MyFirstRunbook-Graphical** te bekijken.

   Met de opties bovenaan kunt u nu het runbook starten, een starttijd in de toekomst plannen of een [webhook](../automation-webhooks.md) maken zodat het runbook kan worden gestart via een HTTP-aanroep.

4. Selecteer **Starten** en vervolgens **Ja** wanneer gevraagd wordt om het runbook te starten.

5. Er wordt een taakdeelvenster geopend voor de runbooktaak die is gemaakt. Controleer of het veld **Taakstatus** **Voltooid** aangeeft.

6. Klik op **Uitvoer** om de uitvoerpagina te openen, waar u `Hello World` weergegeven kunt zien.

7. Sluit de pagina Uitvoer.

8. Klik op **Alle logboeken** om het deelvenster Streams voor de runbooktaak te openen. U ziet enkel `Hello World` in de uitvoerstroom.

    Merk op dat er andere stromen voor een runbooktaak kunnen worden weergegeven in het deelvenster Streams, zoals uitgebreide en foutstromen als het runbook hiernaar schrijft.

9. Sluit het deelvenster Streams en het deelvenster Taak om terug te gaan naar het deelvenster MyFirstRunbook-Graphical.

10. Als u alle taken voor het runbook wilt weergeven, selecteert u **Taken** onder **Resources**. Op de pagina Taken worden alle taken weergegeven die door uw runbook zijn gemaakt. U zou slechts één taak moeten zien, aangezien u de taak slechts eenmaal hebt uitgevoerd.

11. Klik op de taaknaam om hetzelfde taakvenster te openen dat u hebt bekeken toen u het runbook startte. Gebruik dit deelvenster om de details te bekijken van een taak die is gemaakt voor het runbook.

## <a name="step-5---create-variable-assets"></a>Stap 5: variabele assets maken

U hebt het runbook getest en gepubliceerd, maar tot nu toe doet het nog niets nuttigs wat betreft het beheren van Azure-resources. Voordat u het runbook configureert voor verificatie, moet u een variabele maken om de abonnements-id op te slaan, een activiteit instellen voor verificatie en vervolgens naar de variabele verwijzen. Door een verwijzing naar de abonnementscontext op te nemen, kunt u eenvoudig werken tussen verschillende abonnementen.

1. Kopieer uw abonnements-id uit de optie **Abonnementen** in het deelvenster Navigatie.

2. Selecteer op de pagina Automation Accounts **Variabelen** onder **Gedeelde resources**.

3. Selecteer **Een variabele toevoegen**.

4. Stel op de pagina Nieuwe variabele de volgende instellingen in de velden in.

    * **Naam**: voer `AzureSubscriptionId` in.
    * **Waarde**: voer de id van uw Azure-abonnement in.
    * **Type**: houd de tekenreeks geselecteerd.
    * **Versleuteling**: gebruik de standaardwaarde.

5. Klik op **Maken** om de variabele te maken.

## <a name="step-6---add-authentication"></a>Stap 6 - Verificatie toevoegen

Nu u een variabele hebt die onze abonnements-id kan bevatten, kunt u uw runbook configureren voor verificatie met de Uitvoeren als referenties voor uw abonnement. Dit doet u door de Azure Uitvoeren als-verbinding als een asset toe te voegen. U moet ook de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) en de cmdlet [set-AzContext](/powershell/module/az.accounts/Set-AzContext) toevoegen aan het canvas.

>[!NOTE]
>Voor PowerShell-runbooks zijn `Add-AzAccount` en `Add-AzureRMAccount` aliassen voor `Connect-AzAccount`. Houd er rekening mee dat deze aliassen niet beschikbaar zijn voor uw grafische runbooks. Een grafisch runbook kan alleen `Connect-AzAccount`zichzelf gebruiken.

1. Ga naar uw runbook en selecteer **Bewerken** op de pagina MyFirstRunbook-Graphical.

2. U hebt de `Write Hello World to output`-vermelding niet meer nodig. Klik op het beletselteken en selecteer **Verwijderen**.

3. Vouw in het besturingselement Bibliotheek het item **ASSETS** uit en vervolgens **Verbindingen**. Voeg `AzureRunAsConnection` toe aan het canvas door **Toevoegen aan canvas** te selecteren.

4. Naam `AzureRunAsConnection` wijzigen in `Get Run As Connection`.

5. In het besturingselement Bibliotheek typt u `Connect-AzAccount` in het zoekveld.

6. Voeg `Connect-AzAccount` toe aan het canvas.

7. Beweeg de muisaanwijzer over `Get Run As Connection` totdat een cirkel wordt weergegeven aan de onderkant van de vorm. Klik op de cirkel en sleep de pijl naar `Connect-AzAccount` om een koppeling te vormen. Het runbook wordt gestart met `Get Run As Connection` en voert vervolgens `Connect-AzAccount` uit.

    ![Koppeling tussen activiteiten maken](../media/automation-tutorial-runbook-graphical/runbook-link-auth-activities.png)

8. Selecteer `Connect-AzAccount` op het canvas. Typ in het deelvenster Configuratie **Aanmelden bij Azure** in het veld **Label**.

9. Klik op **Parameters**, waarna de pagina Parameterconfiguratie van activiteit verschijnt.

10. De cmdlet `Connect-AzAccount` heeft meerdere parametersets. U moet er een selecteren voordat u parameterwaarden opgeeft. Klik op **Parameterset** en selecteer vervolgens **ServicePrincipalCertificatewithSubscriptionId**.

11. De parameters voor deze parameterset worden weergegeven op de pagina Parameterconfiguratie van activiteit. Klik op **APPLICATIONID**.

    ![Parameters voor Azure-account toevoegen](../media/automation-tutorial-runbook-graphical/Add-AzureRmAccount-params.png)

12. Voer op de pagina Parameterwaarde de volgende instellingen in en klik vervolgens op **OK**.

   * **Gegevensbron**: selecteer **Activiteitsuitvoer**.
   * Lijst met gegevensbronnen: selecteer **Automation-verbinding ophalen**.
   * **Pad naar veld**: typ `ApplicationId`. U geeft de naam van de eigenschap voor het pad naar het veld op omdat de uitvoer van de activiteit een object met meerdere eigenschappen bevat.

13. Klik op **CERTIFICATETHUMBPRINT** en stel op de pagina Parameterwaarde de volgende instellingen in en klik vervolgens op **OK**.

    * **Gegevensbron**: selecteer **Activiteitsuitvoer**.
    * Lijst met gegevensbronnen: selecteer **Automation-verbinding ophalen**.
    * **Pad naar veld**: typ `CertificateThumbprint`.

14. Klik op **SERVICEPRINCIPAL** en selecteer op de pagina Parameterwaarde **ConstantValue** voor het veld **Gegevensbron**. Klik op de optie **Waar** en klik vervolgens op **OK**.

15. Klik op **TENANTID** en stel de volgende instellingen in op de pagina Parameterwaarde. Als u klaar bent, klikt u tweemaal op **OK**.

    * **Gegevensbron**: selecteer **Activiteitsuitvoer**.
    * Lijst met gegevensbronnen: selecteer **Automation-verbinding ophalen**.
    * **Pad naar veld**: typ `TenantId`.

16. In het besturingselement Bibliotheek typt u `Set-AzContext` in het zoekveld.

17. Voeg `Set-AzContext` toe aan het canvas.

18. Selecteer `Set-AzContext` op het canvas. Voer in het deelvenster Configuratie `Specify Subscription Id` in in het veld **Label**.

19. Klik op **Parameters**, waarna de pagina Parameterconfiguratie van activiteit verschijnt.

20. De cmdlet `Set-AzContext` heeft meerdere parametersets. U moet er een selecteren voordat u parameterwaarden opgeeft. Klik op **Parameterset** en selecteer vervolgens **SubscriptionId**.

21. De parameters voor deze parameterset worden weergegeven op de pagina Parameterconfiguratie van activiteit. Klik op **SubscriptionID**.

22. Selecteer op de pagina Parameterwaarde de optie **Variabele asset** voor het veld **Gegevensbron** en selecteer **AzureSubscriptionId** in de bronnenlijst. Als u klaar bent, klikt u tweemaal op **OK**.

23. Beweeg de muisaanwijzer over `Login to Azure` totdat een cirkel wordt weergegeven aan de onderkant van de vorm. Klik op de cirkel en sleep de pijl naar `Specify Subscription Id`. Uw runbook zou er op dit punt als volgt moeten uitzien.

    :::image type="content" source="../media/automation-tutorial-runbook-graphical/runbook-auth-config.png" alt-text="Schermopname van het runbook na het verslepen van de pijl naar 'Specify Subscription Id'.":::

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Stap 7: activiteit toevoegen om een virtuele machine te starten

Nu moet u een `Start-AzVM`-activiteit toevoegen om een virtuele machine te starten. U kunt een willekeurige VM in uw Azure-abonnement kiezen. Voor nu gebruikt u de naam via hardcoding in de cmdlet [start-AzVM](/powershell/module/az.compute/start-azvm).

1. In het besturingselement Bibliotheek typt u `Start-Az` in het zoekveld.

2. Voeg `Start-AzVM` toe aan het canvas en sleep deze onder `Specify Subscription Id`.

3. Beweeg de muisaanwijzer over `Specify Subscription Id` totdat een cirkel wordt weergegeven aan de onderkant van de vorm. Klik op de cirkel en sleep de pijl naar `Start-AzVM`.

4. Selecteer `Start-AzVM`. Klik op **Parameters** en vervolgens op **Parameterset** om de sets voor de activiteit weer te geven.

5. Selecteer **ResourceGroupNameParameterSetName** voor de parameterset. De velden **ResourceGroupName** en **Naam** hebben een uitroepteken ernaast om aan te geven dat het vereiste parameters zijn. Houd er rekening mee dat in beide velden tekenreekswaarden worden verwacht.

6. Selecteer **Name**. Kies **PowerShell-expressie** voor het veld **Gegevensbron**. Voor de virtuele machine die u gebruikt om dit runbook te starten, typt u de naam van de computer tussen dubbele aanhalingstekens. Klik op **OK**.

7. Selecteer **ResourceGroupName**. Gebruik de waarde **PowerShell-expressie** voor de **Gegevensbron** en typ de naam van de resourcegroep tussen dubbele aanhalingstekens. Klik op **OK**.

8. Klik op **Testvenster** zodat u het runbook kunt testen.

9. Klik op **Start** om de test te starten. Nadat deze is voltooid, controleert u of de VM is gestart. Uw runbook zou er op dit punt als volgt moeten uitzien.

    ![Uitvoer van runbook Start-AzVM](../media/automation-tutorial-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Stap 8: aanvullende invoerparameters toevoegen

Met uw runbook wordt momenteel de virtuele machine gestart in de resourcegroep die u hebt opgegeven voor de cmdlet `Start-AzVM`. Het runbook is handiger als u zowel naam als resourcegroep opgeeft wanneer het runbook wordt gestart. Laten we dus invoerparameters toevoegen aan het runbook om deze functionaliteit te bieden.

1. Open de grafische editor door te klikken op **Bewerken** op de pagina MyFirstRunbook-Graphical.

2. Selecteer **Invoer en uitvoer** en vervolgens **Invoer toevoegen** om het deelvenster Invoerparameter van runbook te openen.

3. Stel de volgende instellingen in de beschikbare velden in en klik vervolgens op **OK**.
   * **Naam**: geef `VMName` op.
   * **Type**: behoud de tekenreeksinstelling.
   * **Verplicht**: wijzig de waarde in **Ja**.

4. Maak een tweede verplichte invoerparameter met de naam `ResourceGroupName` en klik vervolgens op **OK** om het venster Invoer en uitvoer te sluiten.

    ![Invoerparameters van runbook](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-outputs.png)

5. Selecteer de activiteit `Start-AzVM` en klik vervolgens op **Parameters**.

6. Wijzig het veld **Gegevensbron** voor **Naam** in **Runbookinvoer**. Selecteer vervolgens **VMName**.

7. Wijzig het veld **gegevensbron** voor **ResourceGroupName** in **Runbookinvoer** en selecteer vervolgens **ResourceGroupName**.

    ![Start-AzVM-parameters](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-runbookinput.png)

8. Sla het runbook op en open het testvenster. U kunt nu waarden opgeven voor de twee invoervariabelen die in de test worden gebruikt.

9. Sluit het testvenster.

10. Klik op **Publiceren** om de nieuwe versie van het runbook te publiceren.

11. Stop de VM die u eerder hebt gestart.

12. Klik op **Starten** om het runbook te starten. Typ waarden voor `VMName` en `ResourceGroupName` voor de VM die u gaat starten.

13. Controleer of de VM is gestart wanneer het runbook is voltooid.

## <a name="step-9---create-a-conditional-link"></a>Stap 9: een voorwaardelijke koppeling maken

U kunt het runbook nu wijzigen zodat alleen wordt geprobeerd de VM te starten als deze nog niet is gestart. Dit doet u door een [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM)-cmdlet toe te voegen waarmee de status op instantieniveau van de virtuele machine wordt opgehaald. We voegen vervolgens een PowerShell Workflow-codemodule genaamd `Get Status` toe met een PowerShell-codefragment om te bepalen of de status van de VM actief of gestopt is. Met een voorwaardelijke koppeling van de module `Get Status` wordt `Start-AzVM` alleen uitgevoerd als de huidige uitvoerstatus is gestopt. Aan het einde van deze procedure gebruikt uw runbook de cmdlet `Write-Output` om een bericht uit te voeren om u te laten weten dat de VM is gestart.

1. Open **MyFirstRunbook-Graphical** in de grafische editor.

2. Verwijder de koppeling tussen `Specify Subscription Id` en `Start-AzVM` door erop te klikken en vervolgens op **Verwijderen** te drukken.

3. In het besturingselement Bibliotheek typt u `Get-Az` in het zoekveld.

4. Voeg `Get-AzVM` toe aan het canvas.

5. Selecteer `Get-AzVM` en klik vervolgens op **Parameterset** om de sets voor de cmdlet weer te geven.

6. Selecteer de parameterset **GetVirtualMachineInResourceGroupNameParamSet**. De velden **ResourceGroupName** en **Naam** hebben een uitroepteken ernaast, waarmee wordt aangegeven dat hiermee verplichte parameters worden opgegeven. Houd er rekening mee dat in beide velden tekenreekswaarden worden verwacht.

7. Selecteer onder **Gegevensbron** voor **Naam** de optie **Runbookinvoer** en vervolgens **VMName**. Klik op **OK**.

8. Selecteer onder **Gegevensbron** voor **ResourceGroupName** de optie **Runbookinvoer** en vervolgens **ResourceGroupName**. Klik op **OK**.

9. Selecteer onder **Gegevensbron** voor **Status** de optie **Constante waarde** en vervolgens **Waar**. Klik op **OK**.

10. Maak een koppeling van `Specify Subscription Id` naar `Get-AzVM`.

11. Vouw in het besturingselement Bibliotheek **Runbookbesturing** uit en voeg **Code** toe aan het papier.  

12. Maak een koppeling van `Get-AzVM` naar `Code`.  

13. Klik op `Code` en wijzig in het deelvenster Configuratie het label in **Status ophalen**.

14. Selecteer `Code` en de pagina Code-editor wordt weergegeven.  

15. Plak het volgende codefragment in de editorpagina.

    ```powershell
    $Statuses = $ActivityOutput['Get-AzVM'].Statuses
    $StatusOut = ""
    foreach ($Status in $Statuses) {
      if($Status.Code -eq "Powerstate/running")
        {$StatusOut = "running"}
      elseif ($Status.Code -eq "Powerstate/deallocated")
        {$StatusOut = "stopped"}
    }
    $StatusOut
    ```

16. Maak een koppeling van `Get Status` naar `Start-AzVM`.

    ![Runbook met codemodule](../media/automation-tutorial-runbook-graphical/runbook-startvm-get-status.png)  

17. Selecteer de koppeling en wijzig in het deelvenster Configuratie **Voorwaarde toepassen** in **Ja**. De koppeling wordt een gestreepte lijn waarmee wordt aangegeven dat de doelactiviteit alleen wordt uitgevoerd als de voorwaarde 'waar' is.  

18. Voor een **Voorwaarde-expressie** typt u `$ActivityOutput['Get Status'] -eq "Stopped"`. `Start-AzVM` wordt nu alleen uitgevoerd als de VM is gestopt.

19. Vouw in het besturingselement Bibliotheek **Cmdlets** uit en vervolgens **Microsoft.PowerShell.Utility**.

20. Voeg `Write-Output` tweemaal toe aan het canvas.

21. In het eerste besturingselement `Write-Output` klikt u op **Parameters** en wijzigt u de waarde voor **Label** in **VM gestart melden**.

22. Voor **InputObject** wijzigt u **Gegevensbron** in **PowerShell-expressie** en typt u de expressie `$VMName successfully started.`.

23. In het tweede besturingselement `Write-Output` klikt u op **Parameters** en wijzigt u de waarde voor **Label** in **Starten VM mislukt melden**.

24. Voor **InputObject** wijzigt u **Gegevensbron** in **PowerShell-expressie** en typt u de expressie `$VMName could not start`.

25. Koppelingen van `Start-AzVM` naar `Notify VM Started` en `Notify VM Start Failed` maken.

26. Selecteer de koppeling naar `Notify VM Started` en wijzig **Voorwaarde toepassen** in waar.

27. Voor de **Voorwaarde-expressie** typt u `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`. Dit `Write-Output` besturingselement wordt nu alleen uitgevoerd als de VM is gestart.

28. Selecteer de koppeling naar `Notify VM Start Failed` en wijzig **Voorwaarde toepassen** in waar.

29. Voor het veld **Voorwaarde-expressie** typt u `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`. Dit `Write-Output` besturingselement wordt nu alleen uitgevoerd als de VM niet is gestart. Uw runbook zou er als de volgende afbeelding uit moeten zien.

    ![Runbook met Write-Output](../media/automation-tutorial-runbook-graphical/runbook-startazurermvm-complete.png)

30. Sla het runbook op en open het testvenster.

31. Start het runbook terwijl de VM gestopt is. De machine zou nu moeten starten.

## <a name="next-steps"></a>Volgende stappen

* Zie [Grafisch runbook ontwerpen in Azure Automation](../automation-graphical-authoring-intro.md) voor meer informatie over grafisch ontwerpen.
* Zie [Een PowerShell-runbook maken](automation-tutorial-runbook-textual-powershell.md) om aan de slag te gaan met PowerShell-runbooks.
* Zie [Een PowerShell Workflow-runbook maken](automation-tutorial-runbook-textual.md) om aan de slag te gaan met PowerShell Workflow-runbooks.
* Zie [Az.Automation](/powershell/module/az.automation) voor een naslagdocumentatie voor een PowerShell-cmdlet.
