---
title: Azure Automation Wijzigingen bijhouden en inventaris inschakelen vanuit een runbook
description: In dit artikel leest u hoe u Wijzigingen bijhouden en inventaris kunt inschakelen vanuit een runbook.
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: e5b42d6102737b778ea5d19cd7da3c2f64881b1b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585926"
---
# <a name="enable-change-tracking-and-inventory-from-a-runbook"></a>Wijzigingen bijhouden en inventaris inschakelen vanuit een runbook

In dit artikel wordt beschreven hoe u een runbook kunt gebruiken om [Wijzigingen bijhouden en inventaris](overview.md) voor vm's in uw omgeving in te scha kelen. Als u Azure-Vm's op schaal wilt inschakelen, moet u een bestaande VM inschakelen met behulp van Wijzigingen bijhouden en inventarisatie.

> [!NOTE]
> Bij het inschakelen van Wijzigingen bijhouden en inventaris worden alleen bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werk ruimte en een Automation-account. Zie [Regio's toewijzen voor Automation-account en Log Analytics-werkruimte](../how-to/region-mappings.md) voor een lijst van alle ondersteunde toewijzingsparen.

Deze methode gebruikt twee runbooks:

* **Enable-MultipleSolution** : het primaire runbook dat vraagt om configuratie-informatie, query's uitvoeren op de opgegeven virtuele machine en andere validatie controles uitvoert, en vervolgens het runbook **Enable-AutomationSolution** aanroept voor het configureren van wijzigingen bijhouden en inventaris voor elke virtuele machine in de opgegeven resource groep.
* **Enable-AutomationSolution** : Hiermee schakelt u wijzigingen bijhouden en inventarisatie in voor een of meer virtuele machines die zijn opgegeven in de doel resource groep. Er wordt gecontroleerd of aan de vereisten wordt voldaan, controleert of de Log Analytics VM-extensie is geïnstalleerd en wordt geïnstalleerd als deze niet wordt gevonden, en voegt de Vm's toe aan de scope configuratie in de opgegeven Log Analytics werk ruimte die is gekoppeld aan het Automation-account.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](../automation-security-overview.md) voor het beheren van computers.
* [Log Analytics werk ruimte](../../azure-monitor/logs/design-logs-deployment.md)
* Een [virtuele machine](../../virtual-machines/windows/quick-create-portal.md).
* Twee Automation-assets, die worden gebruikt door het **Enable-AutomationSolution** runbook. Dit runbook, als het nog niet bestaat in uw Automation-account, wordt tijdens de eerste uitvoering automatisch geïmporteerd door het runbook **Enable-MultipleSolution** .
    * *LASolutionSubscriptionId*: abonnement-id van waar de log Analytics-werk ruimte zich bevindt.
    * *LASolutionWorkspaceId*: werk ruimte-id van de log Analytics-werk ruimte die is gekoppeld aan uw Automation-account.

    Deze variabelen worden gebruikt voor het configureren van de werk ruimte van de onboarded VM. Als deze niet zijn opgegeven, zoekt het script eerst naar een VM die in het abonnement van Wijzigingen bijhouden en inventarisatie wordt uitgevoerd, gevolgd door het abonnement waarin het Automation-account zich bevindt, gevolgd door alle andere abonnementen waarvan uw gebruikers account toegang heeft. Als de configuratie niet juist is geconfigureerd, kan dit ertoe leiden dat uw apparaten op een wille keurige Log Analytics-werk ruimte worden uitgevoerd.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="enable-change-tracking-and-inventory"></a>Wijzigingen bijhouden en Inventaris inschakelen

1. Navigeer in het Azure Portal naar **Automation-accounts**. Selecteer uw account in de lijst op de pagina **Automation-accounts** .

1. Selecteer in uw Automation-account **inventarisatie** of **Wijzigingen bijhouden** onder **configuratie beheer**.

1. Selecteer de Log Analytics-werkruimte en klik op **Inschakelen**. Terwijl de inventarisatie of Wijzigingen bijhouden wordt ingeschakeld, wordt er een banner weer gegeven.

    ![Wijzigingen bijhouden en Inventaris inschakelen](media/enable-from-automation-account/enable-feature.png)

## <a name="install-and-update-modules"></a>Modules installeren en bijwerken

Het is vereist om bij te werken naar de meest recente Azure-modules en de module [AZ. OperationalInsights](/powershell/module/az.operationalinsights) te importeren om updatebeheer voor uw virtuele machines in te scha kelen met het runbook.

1. Selecteer **Modules** onder **Gedeelde resources** in uw Automation-account.

2. Selecteer **Azure-modules bijwerken** om de Azure-modules bij te werken naar de nieuwste versie.

3. Klik op **Ja** om alle bestaande Azure-modules bij te werken naar de nieuwste versie.

    ![Modules bijwerken](media/enable-from-runbook/update-modules.png)

4. Ga terug naar **Modules** onder **Gedeelde resources**.

5. Selecteer **Bladeren in galerie** om de modulegalerie te openen.

6. Zoek naar `Az.OperationalInsights` en importeer deze module in uw Automation-account.

    ![De module OperationalInsights importeren](media/enable-from-runbook/import-operational-insights-module.png)

## <a name="select-azure-vm-to-manage"></a>Te beheren Azure-VM selecteren

Als Wijzigingen bijhouden en inventarisatie is ingeschakeld, kunt u een Azure VM toevoegen voor beheer door de functie.

1. Selecteer in uw Automation-account **Wijzigingen bijhouden** of **inventaris** onder **configuratie beheer**.

2. Klik op **virtuele machines van Azure toevoegen** om uw virtuele machine toe te voegen.

3. Kies uw virtuele machine in de lijst en klik op **inschakelen**. Met deze actie worden Wijzigingen bijhouden en inventarisatie voor de virtuele machine ingeschakeld.

   ![Wijzigingen bijhouden en inventaris voor de virtuele machine inschakelen](media/enable-from-runbook/enable-azure-vm.png)

    > [!NOTE]
    > Als u probeert een andere functie in te scha kelen voordat de installatie van Wijzigingen bijhouden en de inventarisatie is voltooid, ontvangt u dit bericht: `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="import-a-runbook-to-enable-change-tracking-and-inventory"></a>Een runbook importeren om Wijzigingen bijhouden en inventarisatie in te scha kelen

1. Selecteer **Runbooks** onder **Procesautomatisering** in uw Automation-account.

2. Selecteer **Bladeren in galerie**.

3. Zoeken naar **updates en het bijhouden van wijzigingen**.

4. Selecteer het runbook en klik op **importeren** op de pagina **bron weer geven** .

5. Klik op **OK** om het runbook in het Automation-account te importeren.

   ![Runbook importeren voor installatie](media/enable-from-runbook/import-from-gallery.png)

6. Selecteer op de pagina **runbook** het runbook **Enable-MultipleSolution** en klik vervolgens op **bewerken**. Selecteer in de tekst editor  **publiceren**.

7. Wanneer u wordt gevraagd om te bevestigen, klikt u op **Ja** om het runbook te publiceren.

## <a name="start-the-runbook"></a>Het runbook starten

U moet Wijzigingen bijhouden en inventaris inschakelen voor een virtuele Azure-machine om dit runbook te starten. Hiervoor is een bestaande virtuele machine en resource groep vereist waarvoor de functie is ingeschakeld om een of meer virtuele machines in de doel resource groep te configureren.

1. Open het runbook **Enable-MultipleSolution**.

   ![Runbook voor meerdere oplossingen](media/enable-from-runbook/runbook-overview.png)

2. Klik op de startknop en voer in de volgende velden parameterwaarden in:

   * **VMNAME** : de naam van een bestaande virtuele machine die aan wijzigingen bijhouden en inventaris moet worden toegevoegd. Laat dit veld leeg als uy alle VM's in de resourcegroep wilt toevoegen.
   * **VMRESOURCEGROUP**: de naam van de resourcegroep voor de in te schakelen VM's.
   * **SUBSCRIPTIONID**: de abonnement-id van de nieuwe in te schakelen VM. Laat dit veld leeg om het abonnement van de werkruimte te gebruiken. Wanneer u een andere abonnement-id gebruikt, voegt u het Uitvoeren als-account voor uw Automation-account toe als inzender voor het abonnement.
   * **ALREADYONBOARDEDVM**: de naam van de VM waarop updates al handmatig zijn ingeschakeld.
   * **ALREADYONBOARDEDVMRESOURCEGROUP**: de naam van de resourcegroep waartoe de VM behoort.
   * **Solution type** : Voer **change tracking** in.

   ![Parameters voor het runbook Enable-MultipleSolution](media/enable-from-runbook/runbook-parameters.png)

3. Selecteer **OK** om de runbooktaak te starten.

4. De voortgang van de runbook-taak en eventuele fouten van de pagina **taken** bewaken.

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg [Planningen beheren in Azure Automation](../shared-resources/schedules.md) om een runbook te plannen.

* Zie [Wijzigingen bijhouden beheren](manage-change-tracking.md) en [inventaris beheren](manage-inventory-vms.md)voor meer informatie over het werken met de functie.

* Zie [problemen met wijzigingen bijhouden en voorraad problemen oplossen](../troubleshoot/change-tracking.md)voor informatie over het oplossen van algemene problemen met de functie.


