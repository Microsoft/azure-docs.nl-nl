---
title: Uw account Azure Automation naar een ander abonnement verplaatsen
description: In dit artikel wordt beschreven hoe u uw Automation-account naar een ander abonnement kunt verplaatsen.
services: automation
ms.subservice: process-automation
ms.date: 01/07/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1fd9350baf1805c0e6278b6210ad9818fa18c8d8
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831488"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Uw account Azure Automation naar een ander abonnement verplaatsen

Azure Automation kunt u sommige resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement. U kunt resources verplaatsen via de Azure Portal, PowerShell, de Azure CLI of de REST API. Zie Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement voor meer informatie [over het proces.](../../azure-resource-manager/management/move-resource-group-and-subscription.md)

Het Automation-account is een van de resources die u kunt verplaatsen. In dit artikel leert u hoe u Automation-accounts verplaatst naar een andere resource of een ander abonnement. De stappen op hoog niveau voor het verplaatsen van uw Automation-account zijn:

1. Schakel uw functies uit.
2. Ontkoppel uw werkruimte.
3. Verplaats het Automation-account.
4. Verwijder de Uitvoeren als-accounts en maak ze opnieuw.
5. Schakel uw functies opnieuw in.

## <a name="remove-features"></a>Functies verwijderen

Als u uw werkruimte wilt loskoppelen van uw Automation-account, moet u de functiebronnen in uw werkruimte verwijderen:

- Wijzigingen bijhouden en Inventaris
- Updatebeheer
- VM's starten/stoppen buiten kantooruren

1. Zoek de resourcegroep in de Azure-portal.
2. Zoek elke functie en selecteer **Verwijderen op** de **pagina Resources** verwijderen.

    ![Schermopname van het verwijderen van functiebronnen uit de Azure Portal](../media/move-account/delete-solutions.png)

Als u wilt, kunt u de resources verwijderen met behulp van de cmdlet [Remove-AzResource:](/powershell/module/Az.Resources/Remove-AzResource)

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="remove-alert-rules-for-startstop-vms-during-off-hours"></a>Waarschuwingsregels voor VM's buiten bedrijfsuren starten/stoppen

Voor VM's buiten bedrijfsuren starten/stoppen moet u ook de waarschuwingsregels verwijderen die door de functie zijn gemaakt.

1. Ga in Azure Portal naar uw resourcegroep en selecteer   >  **Bewakingswaarschuwingen**  >  **Waarschuwingsregels beheren.**

   ![Schermopname van de pagina Waarschuwingen met de selectie van Waarschuwingsregels beheren](../media/move-account/alert-rules.png)

2. Op de pagina Regels ziet u een lijst met de waarschuwingen die in die resourcegroep zijn geconfigureerd. Met de functie worden de volgende regels gemaakt:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Selecteer de regels één voor één en selecteer **Verwijderen om** ze te verwijderen.

    ![Schermopname van de pagina Regels, met het verzoek om bevestiging van verwijdering voor geselecteerde regels](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Als u geen waarschuwingsregels ziet op de pagina Regels, wijzigt u het **veld Status** in **Uitgeschakeld om** uitgeschakelde waarschuwingen weer te geven. 

4. Wanneer u de waarschuwingsregels verwijdert, moet u de actiegroep verwijderen die is gemaakt voor VM's buiten bedrijfsuren starten/stoppen meldingen. Selecteer in Azure Portal **monitorwaarschuwingen**  >    >  **Actiegroepen beheren.**

5. Selecteer **StartStop_VM_Notification**. 

6. Selecteer verwijderen op de pagina van de **actiegroep.**

    ![Schermopname van de pagina Actiegroep](../media/move-account/delete-action-group.png)

Als u wilt, kunt u uw actiegroep verwijderen met behulp van de cmdlet [Remove-AzActionGroup:](/powershell/module/az.monitor/remove-azactiongroup)

```azurepowershell-interactive
Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Uw werkruimte ontkoppelen

U kunt nu uw werkruimte ontkoppelen:

1. Selecteer in Azure Portal **Automation-account**  >  **Gerelateerde resources**  >  **Gekoppelde werkruimte.** 

2. Selecteer **Werkruimte ontkoppelen** om de werkruimte te ontkoppelen van uw Automation-account.

    ![Schermopname van het ontkoppelen van een werkruimte vanuit een Automation-account](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Uw Automation-account verplaatsen

U kunt nu uw Automation-account en de runbooks ervan verplaatsen. 

1. Blader in Azure Portal naar de resourcegroep van uw Automation-account. Selecteer **Verplaatsen**  >  **naar een ander abonnement.**

    ![Schermopname van de pagina Resourcegroep, verplaatsen naar een ander abonnement](../media/move-account/move-resources.png)

2. Selecteer de resources in de resourcegroep die u wilt verplaatsen. Zorg ervoor dat u uw Automation-account, runbooks en Log Analytics-werkruimte-resources op bevat.

## <a name="re-create-run-as-accounts"></a>Run As-accounts opnieuw maken

[Uitvoeren als-accounts](../automation-security-overview.md#run-as-accounts) maken een service-principal in Azure Active Directory om te verifiëren met Azure-resources. Wanneer u van abonnement wisselt, maakt het Automation-account niet langer gebruik van het bestaande Uitvoeren als-account. De Uitvoeren als-accounts opnieuw maken:

1. Ga naar uw Automation-account in het nieuwe abonnement en selecteer **Uitvoeren als-accounts** onder **Accountinstellingen.** U ziet dat de Uitvoeren als-accounts nu als onvolledig worden beschouwd.

    ![Schermopname van Uitvoeren als-accounts, onvolledig weergegeven](../media/move-account/run-as-accounts.png)

2. Verwijder de Uitvoeren als-accounts één voor één door **Verwijderen te selecteren** op de **pagina** Eigenschappen. 

    > [!NOTE]
    > Als u geen machtigingen hebt om Uitvoeren als-accounts te maken of weer te geven, ziet u het volgende bericht: Zie Machtigingen die zijn vereist voor het configureren van Uitvoeren als-accounts voor `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` [meer informatie.](../automation-security-overview.md#permissions)

3. Nadat u de Uitvoeren als-accounts hebt verwijderd, selecteert u **Maken onder** Azure **Uitvoeren als-account.** 

4. Selecteer op de pagina Uitvoeren als-account voor Azure toevoegen de optie **Maken om** het Uitvoeren als-account en de service-principal te maken. 

5. Herhaal de bovenstaande stappen met het klassieke Uitvoeren als-account van Azure.

## <a name="enable-features"></a>Functies inschakelen

Nadat u de Uitvoeren als-accounts opnieuw hebt aanmaken, moet u de functies die u voor de overstap hebt verwijderd, opnieuw inschakelen:

1. Als u deze wilt Wijzigingen bijhouden en inventaris, selecteert **Wijzigingen bijhouden en inventaris** in uw Automation-account. Kies de Log Analytics-werkruimte die u hebt verplaatst en selecteer **Inschakelen.**

2. Herhaal stap 1 voor Updatebeheer.

    ![Schermopname van het opnieuw inschakelen van functies in uw verplaatste Automation-account](../media/move-account/reenable-solutions.png)

3. Machines die zijn ingeschakeld met uw functies zijn zichtbaar wanneer u de bestaande Log Analytics-werkruimte hebt verbonden. Als u de VM's buiten bedrijfsuren starten/stoppen wilt inschakelen, moet u deze opnieuw inschakelen. Selecteer **onder Gerelateerde resources** de optie **VM's starten/stoppen** Meer informatie over en schakel de oplossing  >    >  **Maken in** om de implementatie te starten.

4. Kies op de pagina Oplossing toevoegen uw Log Analytics-werkruimte en Automation-account.

    ![Schermopname van het menu Oplossing toevoegen](../media/move-account/add-solution-vm.png)

5. Configureer de functie zoals beschreven in [VM's buiten bedrijfsuren starten/stoppen overzicht](../automation-solution-vm-management.md).

## <a name="verify-the-move"></a>De overstap controleren

Wanneer de overstap is voltooid, controleert u of de hieronder vermelde mogelijkheden zijn ingeschakeld. 

|Mogelijkheid|Testen|Problemen oplossen|
|---|---|---|
|Runbooks|Een runbook kan worden uitgevoerd en verbinding maken met Azure-resources.|[Problemen met runbooks oplossen](../troubleshoot/runbooks.md)
|Broncodebeheer|U kunt een handmatige synchronisatie uitvoeren op uw opslagplaats voor broncodebeheer.|[Integratie van broncodebeheer](../source-control-integration.md)|
|Wijzigingen bijhouden en inventaris|Controleer of u de huidige inventarisgegevens van uw machines ziet.|[Problemen met het bijhouden van wijzigen oplossen](../troubleshoot/change-tracking.md)|
|Updatebeheer|Controleer of u uw machines ziet en of ze in orde zijn.</br>Voer een testimplementatie voor software-updates uit.|[Problemen met updatebeheer oplossen](../troubleshoot/update-management.md)|
|Gedeelde resources|Controleer of u al uw gedeelde resources ziet, zoals [referenties](../shared-resources/credentials.md) en [variabelen.](../shared-resources/variables.md)|

## <a name="next-steps"></a>Volgende stappen

Zie Resources verplaatsen in Azure voor meer informatie over het verplaatsen [van resources in Azure.](../../azure-resource-manager/management/move-support-resources.md)
