---
title: Azure Automation Updatebeheer inschakelen vanuit runbook
description: Dit artikel beschrijft hoe u Updatebeheer kunt inschakelen vanuit een runbook.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: mvc
ms.openlocfilehash: 5377a3ff7ef7033b57f8785baa615a717ef7fa0f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575867"
---
# <a name="enable-update-management-from-a-runbook"></a>Updatebeheer inschakelen vanuit een runbook

Dit artikel beschrijft hoe u een runbook kunt gebruiken om de [Updatebeheer](overview.md)-functie voor VM's in uw omgeving in te schakelen. Als u Azure-Vm's op schaal wilt inschakelen, moet u een bestaande virtuele machine met Updatebeheer inschakelen.

> [!NOTE]
> Bij het inschakelen van Updatebeheer worden slechts bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werkruimte aan een Automation-account. Zie [Regio's toewijzen voor Automation-account en Log Analytics-werkruimte](../how-to/region-mappings.md) voor een lijst van alle ondersteunde toewijzingsparen.

Deze methode gebruikt twee runbooks:

* **Enable-MultipleSolution** : het primaire runbook dat vraagt om configuratie-informatie, query's uitvoeren op de opgegeven virtuele machine en andere validatie controles uitvoert, en vervolgens het runbook **Enable-AutomationSolution** aanroept om updatebeheer voor elke virtuele machine in de opgegeven resource groep te configureren.
* **Enable-AutomationSolution** : Hiermee schakelt u updatebeheer in voor een of meer virtuele machines die zijn opgegeven in de doel resource groep. Er wordt gecontroleerd of aan de vereisten wordt voldaan, controleert of de Log Analytics VM-extensie is geïnstalleerd en wordt geïnstalleerd als deze niet wordt gevonden, en voegt de Vm's toe aan de scope configuratie in de opgegeven Log Analytics werk ruimte die is gekoppeld aan het Automation-account.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](../automation-security-overview.md) voor het beheren van computers.
* [Log Analytics werk ruimte](../../azure-monitor/logs/design-logs-deployment.md)
* Een [virtuele machine](../../virtual-machines/windows/quick-create-portal.md).
* Twee Automation-assets, die worden gebruikt door het **Enable-AutomationSolution** runbook. Dit runbook, als het nog niet bestaat in uw Automation-account, wordt tijdens de eerste uitvoering automatisch geïmporteerd door het runbook **Enable-MultipleSolution** .
    * *LASolutionSubscriptionId*: abonnement-id van waar de log Analytics-werk ruimte zich bevindt.
    * *LASolutionWorkspaceId*: werk ruimte-id van de log Analytics-werk ruimte die is gekoppeld aan uw Automation-account.

    Deze variabelen worden gebruikt voor het configureren van de werk ruimte van de onboarded VM en u moet deze hand matig maken. Als deze niet zijn opgegeven, zoekt het script eerst naar een virtuele machine die is onboarded naar Updatebeheer in het abonnement, gevolgd door het abonnement waarin het Automation-account zich bevindt, gevolgd door alle andere abonnementen waartoe uw gebruikers account toegang heeft. Als de configuratie niet juist is geconfigureerd, kan dit ertoe leiden dat uw apparaten op een wille keurige Log Analytics-werk ruimte worden uitgevoerd.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="enable-update-management"></a>Updatebeheer inschakelen

1. Navigeer in het Azure Portal naar **Automation-accounts**. Selecteer uw account in de lijst op de pagina **Automation-accounts** .

2. Selecteer **Updatebeheer** onder **Updatebeheer** in uw Automation-account.

3. Selecteer de Log Analytics-werkruimte en klik op **Inschakelen**. Wanneer Updatebeheer wordt ingeschakeld, wordt er een banner weer gegeven.

    ![Updatebeheer inschakelen](media/enable-from-runbook/enable-update-management.png)

## <a name="install-and-update-modules"></a>Modules installeren en bijwerken

Het is vereist om bij te werken naar de meest recente Azure-modules en de [AzureRM. OperationalInsights](/powershell/module/azurerm.operationalinsights) -module te importeren om updatebeheer voor uw virtuele machines in te scha kelen met het runbook.

1. Selecteer **Modules** onder **Gedeelde resources** in uw Automation-account.

2. Selecteer **Azure-modules bijwerken** om de Azure-modules bij te werken naar de nieuwste versie.

3. Klik op **Ja** om alle bestaande Azure-modules bij te werken naar de nieuwste versie.

    ![Modules bijwerken](media/enable-from-runbook/update-modules.png)

4. Ga terug naar **Modules** onder **Gedeelde resources**.

5. Selecteer **Bladeren in galerie** om de modulegalerie te openen.

6. Zoek naar `AzureRM.OperationalInsights` en importeer deze module in uw Automation-account.

    ![De module OperationalInsights importeren](media/enable-from-runbook/import-operational-insights-module-azurerm.png)

## <a name="select-azure-vm-to-manage"></a>Te beheren Azure-VM selecteren

Als Updatebeheer is ingeschakeld, kunt u een Azure-VM toevoegen om updates te ontvangen.

1. Selecteer in uw Automation-account **Update beheer** onder het gedeelte **Update beheer**.

2. Selecteer **Azure-VM's toevoegen** om uw VM toe te voegen.

3. Kies de virtuele machine in de lijst en klik op **inschakelen** om de virtuele machine te configureren voor beheer.

   ![Updatebeheer inschakelen voor VM](media/enable-from-runbook/enable-update-management-vm.png)

    > [!NOTE]
    > Als u probeert een andere functie in te schakelen voordat de installatie van Updatebeheer is voltooid, ontvangt u dit bericht: `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="import-a-runbook-to-enable-update-management"></a>Een runbook importeren om Updatebeheer in te schakelen

1. Selecteer **Runbooks** onder **Procesautomatisering** in uw Automation-account.

2. Selecteer **Bladeren in galerie**.

3. Zoeken naar **updates en het bijhouden van wijzigingen**.

4. Selecteer het runbook en klik op **importeren** op de pagina **bron weer geven** .

5. Klik op **OK** om het runbook in het Automation-account te importeren.

   ![Runbook importeren voor installatie](media/enable-from-runbook/import-from-gallery.png)

6. Selecteer op de pagina **runbook** het runbook **Enable-MultipleSolution** en klik vervolgens op **bewerken**. Selecteer in de tekst editor  **publiceren**.

7. Wanneer u wordt gevraagd om te bevestigen, klikt u op **Ja** om het runbook te publiceren.

## <a name="start-the-runbook"></a>Het runbook starten

U moet Updatebeheer op een Azure-VM hebben ingeschakeld om dit runbook te starten. Hiervoor is een bestaande virtuele machine en resource groep vereist waarvoor de functie is ingeschakeld om een of meer virtuele machines in de doel resource groep te configureren.

1. Open het runbook **Enable-MultipleSolution**.

   ![Runbook voor meerdere oplossingen](media/enable-from-runbook/runbook-overview.png)

2. Klik op de startknop en voer in de volgende velden parameterwaarden in:

   * **VMNAME**: de naam van een bestaande VM om aan Updatebeheer toe te voegen. Laat dit veld leeg als uy alle VM's in de resourcegroep wilt toevoegen.
   * **VMRESOURCEGROUP**: de naam van de resourcegroep voor de in te schakelen VM's.
   * **SUBSCRIPTIONID**: de abonnement-id van de nieuwe in te schakelen VM. Laat dit veld leeg om het abonnement van de werkruimte te gebruiken. Wanneer u een andere abonnement-id gebruikt, voegt u het Uitvoeren als-account voor uw Automation-account toe als inzender voor het abonnement.
   * **ALREADYONBOARDEDVM**: de naam van de VM waarop updates al handmatig zijn ingeschakeld.
   * **ALREADYONBOARDEDVMRESOURCEGROUP**: de naam van de resourcegroep waartoe de VM behoort.
   * **SOLUTIONTYPE**: voer **Updates** in.

   ![Parameters voor het runbook Enable-MultipleSolution](media/enable-from-runbook/runbook-parameters.png)

3. Selecteer **OK** om de runbooktaak te starten.

4. De voortgang van de runbook-taak en eventuele fouten van de pagina **taken** bewaken.

## <a name="next-steps"></a>Volgende stappen

* Zie [updates en patches voor uw virtuele machines beheren](manage-updates-for-vm.md)voor meer informatie over het gebruik van updatebeheer voor vm's.

* Zie [Problemen met Updatebeheer oplossen](../troubleshoot/update-management.md) voor meer informatie over het oplossen van algemene Updatebeheer-fouten.
