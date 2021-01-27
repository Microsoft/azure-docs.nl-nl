---
title: Uw Azure Automation-account verplaatsen naar een ander abonnement
description: In dit artikel leest u hoe u uw Automation-account kunt verplaatsen naar een ander abonnement.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8454c5a5bb5b44d2a60ae0095a9b82a19ed27c8d
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896643"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Uw Azure Automation-account verplaatsen naar een ander abonnement

Met Azure Automation kunt u enkele resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement. U kunt resources verplaatsen via de Azure Portal, Power shell, de Azure CLI of de REST API. Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md)voor meer informatie over het proces.

Het Automation-account is een van de resources die u kunt verplaatsen. In dit artikel leert u hoe u Automation-accounts kunt verplaatsen naar een andere resource of een ander abonnement. De stappen op hoog niveau voor het verplaatsen van uw Automation-account zijn:

1. Schakel uw functies uit.
2. De werk ruimte ontkoppelen.
3. Verplaats het Automation-account.
4. Verwijder de run as-accounts en maak deze opnieuw.
5. Schakel uw functies opnieuw in.

## <a name="remove-features"></a>Onderdelen verwijderen

Als u uw werk ruimte wilt ontkoppelen van uw Automation-account, moet u de functie resources in uw werk ruimte verwijderen:

- Wijzigingen bijhouden en Inventaris
- Updatebeheer
- VM's starten/stoppen buiten kantooruren

1. Zoek de resourcegroep in de Azure-portal.
2. Zoek elke functie en selecteer **verwijderen** op de pagina **resources verwijderen** .

    ![Scherm opname van het verwijderen van functie resources uit het Azure Portal](../media/move-account/delete-solutions.png)

Als u wilt, kunt u de resources verwijderen met de cmdlet [Remove-AzResource](/powershell/module/Az.Resources/Remove-AzResource) :

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="remove-alert-rules-for-startstop-vms-during-off-hours"></a>Waarschuwings regels voor VM's buiten bedrijfsuren starten/stoppen verwijderen

Voor VM's buiten bedrijfsuren starten/stoppen moet u ook de waarschuwings regels verwijderen die door de functie zijn gemaakt.

1. Ga in het Azure Portal naar de resource groep en selecteer **bewakings**  >  **waarschuwingen**  >  **beheren waarschuwings regels**.

   ![Scherm afbeelding van de pagina waarschuwingen, waarin de selectie van waarschuwings regels beheren wordt weer gegeven](../media/move-account/alert-rules.png)

2. Op de pagina regels ziet u een lijst met de waarschuwingen die zijn geconfigureerd in die resource groep. Deze regels worden door de functie gemaakt:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Selecteer de regels een voor een tegelijk en selecteer **verwijderen** om ze te verwijderen.

    ![Scherm opname van de pagina regels en de bevestiging van het verwijderen van geselecteerde regels aanvragen](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Als u geen waarschuwings regels ziet op de pagina regels, wijzigt u het veld **status** in **uitgeschakeld** om uitgeschakelde waarschuwingen weer te geven. 

4. Wanneer u de waarschuwings regels verwijdert, moet u de actie groep verwijderen die is gemaakt voor VM's buiten bedrijfsuren starten/stoppen meldingen. Selecteer in het Azure Portal **bewaken** van  >  **waarschuwingen** controleren  >  **actie groepen beheren**.

5. Selecteer **StartStop_VM_Notification**. 

6. Selecteer op de pagina actie groep de optie **verwijderen**.

    ![Scherm afbeelding van de pagina actie groep](../media/move-account/delete-action-group.png)

Als u wilt, kunt u uw actie groep verwijderen met de cmdlet [Remove-AzActionGroup](/powershell/module/az.monitor/remove-azactiongroup) :

```azurepowershell-interactive
Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Uw werk ruimte ontkoppelen

U kunt nu uw werk ruimte ontkoppelen:

1. Selecteer in de Azure portal aan **Automation-account**  >  **gerelateerde resources**  >  **gekoppelde werk ruimte**. 

2. Selecteer **werk ruimte ontkoppelen** om de werk ruimte te ontkoppelen van uw Automation-account.

    ![Scherm afbeelding van het ontkoppelen van een werk ruimte van een Automation-account](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Uw Automation-account verplaatsen

U kunt nu uw Automation-account en de bijbehorende runbooks verplaatsen. 

1. Blader in het Azure Portal naar de resource groep van uw Automation-account. Selecteer **verplaatsen**  >  **naar ander abonnement** verplaatsen.

    ![Scherm afbeelding van de pagina resource groep, verplaatsen naar een ander abonnement](../media/move-account/move-resources.png)

2. Selecteer de resources in de resource groep die u wilt verplaatsen. Zorg ervoor dat u uw Automation-account, runbooks en Log Analytics werkruimte resources opneemt.

## <a name="re-create-run-as-accounts"></a>Run as-accounts opnieuw maken

[Run as-accounts](../manage-runas-account.md) maken een service-principal in azure Active Directory om te verifiëren met Azure-resources. Wanneer u abonnementen wijzigt, wordt het bestaande uitvoeren als-account niet meer gebruikt voor het Automation-account. De run as-accounts opnieuw maken:

1. Ga naar uw Automation-account in het nieuwe abonnement en selecteer **uitvoeren als-accounts** onder **account instellingen**. U ziet dat de uitvoeren als-accounts nu onvolledig worden weer gegeven.

    ![Scherm opname van run as-accounts, met onvolledige](../media/move-account/run-as-accounts.png)

2. Verwijder de run as-accounts, één per keer, door **verwijderen** te selecteren op de pagina **Eigenschappen** . 

    > [!NOTE]
    > Als u geen machtigingen hebt om de run as-accounts te maken of weer te geven, ziet u het volgende bericht: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` Zie [machtigingen vereist voor het configureren van run as-accounts](../manage-runas-account.md#permissions)voor meer informatie.

3. Nadat u de run as-accounts hebt verwijderd, selecteert u **maken** onder **Azure uitvoeren als-account**. 

4. Selecteer op de pagina uitvoeren als-account voor Azure toevoegen de optie **maken** om het uitvoeren als-account en de service-principal te maken. 

5. Herhaal de bovenstaande stappen met het klassieke uitvoeren als-account van Azure.

## <a name="enable-features"></a>Functies inschakelen

Nadat u de run as-accounts opnieuw hebt gemaakt, moet u de functies die u hebt verwijderd vóór de verplaatsing opnieuw inschakelen:

1. Als u Wijzigingen bijhouden en inventaris wilt inschakelen, selecteert u **Wijzigingen bijhouden en inventaris** in uw Automation-account. Kies de Log Analytics-werk ruimte die u hebt verplaatst en selecteer **inschakelen**.

2. Herhaal stap 1 voor Updatebeheer.

    ![Scherm opname van het opnieuw inschakelen van functies in het verplaatste Automation-account](../media/move-account/reenable-solutions.png)

3. Computers waarop de functies zijn ingeschakeld, zijn zichtbaar wanneer u de bestaande Log Analytics-werk ruimte hebt verbonden. Als u de functie VM's buiten bedrijfsuren starten/stoppen wilt inschakelen, moet u deze opnieuw inschakelen. Onder **gerelateerde resources** selecteert u **Start-en stop vm's**  >  **meer informatie over en schakelt u de oplossing**  >  **maken** in om de implementatie te starten.

4. Kies op de pagina oplossing toevoegen de Log Analytics-werk ruimte en het Automation-account.

    ![Scherm opname van het menu oplossing toevoegen](../media/move-account/add-solution-vm.png)

5. Configureer de functie zoals beschreven in [VM's buiten bedrijfsuren starten/stoppen overzicht](../automation-solution-vm-management.md).

## <a name="verify-the-move"></a>De verplaatsing controleren

Wanneer de verplaatsing is voltooid, controleert u of de hieronder vermelde mogelijkheden zijn ingeschakeld. 

|Mogelijkheid|Testen|Problemen oplossen|
|---|---|---|
|Runbooks|Een runbook kan worden uitgevoerd en er verbinding mee worden gemaakt met Azure-resources.|[Problemen met runbooks oplossen](../troubleshoot/runbooks.md)
|Broncodebeheer|U kunt een hand matige synchronisatie uitvoeren in uw opslag plaats voor broncode beheer.|[Integratie van broncode beheer](../source-control-integration.md)|
|Wijzigingen bijhouden en inventaris|Controleer of er actuele inventaris gegevens van uw computers worden weer gegeven.|[Problemen met het bijhouden van wijzigingen oplossen](../troubleshoot/change-tracking.md)|
|Updatebeheer|Controleer of uw computers worden weer geven en of ze in orde zijn.</br>Voer een test implementatie van de software-update uit.|[Problemen met update beheer oplossen](../troubleshoot/update-management.md)|
|Gedeelde resources|Controleer of alle gedeelde bronnen, zoals [referenties](../shared-resources/credentials.md) en [variabelen](../shared-resources/variables.md), worden weer geven.|

## <a name="next-steps"></a>Volgende stappen

Zie [resources verplaatsen in azure](../../azure-resource-manager/management/move-support-resources.md)voor meer informatie over het verplaatsen van resources in Azure.
