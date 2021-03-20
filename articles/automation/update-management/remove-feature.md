---
title: Azure Automation Updatebeheer functie verwijderen
description: In dit artikel wordt beschreven hoe u Updatebeheer stopt en een Automation-account loskoppelt vanuit de werk ruimte Log Analytics.
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 1d83f859fce33b9499d01c4b58e69f56fdbbb293
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92222240"
---
# <a name="remove-update-management-from-automation-account"></a>Updatebeheer verwijderen uit Automation-account

Nadat u het beheer van updates op uw virtuele machines hebt ingeschakeld met behulp van Azure Automation Updatebeheer, kunt u ervoor kiezen om het niet meer te gebruiken en de configuratie te verwijderen uit het account en de gekoppelde Log Analytics-werk ruimte.  In dit artikel leest u hoe u Updatebeheer volledig kunt verwijderen uit de beheerde Vm's, uw Automation-account en Log Analytics werk ruimte.

## <a name="sign-into-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="remove-management-of-vms"></a>Beheer van Vm's verwijderen

Voordat u Updatebeheer verwijdert, moet u eerst stoppen met het beheren van uw Vm's. Zie [vm's uit updatebeheer verwijderen](remove-vms.md) als u de registratie van de functie ongedaan wilt maken.

## <a name="remove-updatemanagement-solution"></a>UpdateManagement-oplossing verwijderen

Voordat u het Automation-account kunt ontkoppelen van de werk ruimte, moet u deze stappen volgen om Updatebeheer volledig te verwijderen. U verwijdert de **Update** -oplossing uit de werk ruimte.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Selecteer in de Azure-portal de optie **Alle services**. Typ in de lijst met resources **Log Analytics**. Wanneer u begint te typen, worden in de lijst suggesties weer geven op basis van uw invoer. Selecteer **Log Analytics**.

3. Selecteer in de lijst met Log Analytics-werk ruimten de werk ruimte die u hebt gekozen bij het inschakelen van Updatebeheer.

4. Selecteer aan de linkerkant **oplossingen**.  

5. Selecteer in de lijst met oplossingen **updates (werkruimte naam)**. Selecteer op de pagina **overzicht** voor de oplossing **verwijderen**. Selecteer **Ja** als u wordt gevraagd om te bevestigen.

## <a name="unlink-workspace-from-automation-account"></a>De werkruimte ontkoppelen van het Automation-account

1. Selecteer in de Azure Portal **Automation-accounts**.

2. Open uw Automation-account en selecteer **gekoppelde werk ruimte** onder **gerelateerde resources** aan de linkerkant.

3. Op de pagina **werk ruimte ontkoppelen** selecteert u **werk ruimte ontkoppelen** en reageert u op prompts.

   ![Pagina werk ruimte ontkoppelen](media/remove-feature/automation-unlink-workspace-blade.png)

Tijdens de poging om de Log Analytics-werk ruimte te ontkoppelen, kunt u de voortgang bijhouden onder **meldingen** in het menu.

U kunt de Log Analytics-werk ruimte ook ontkoppelen van uw Automation-account vanuit de werk ruimte:

1. Selecteer in Azure Portal de optie **Log Analytics**.

2. Selecteer in de werk ruimte **Automation-account** onder **gerelateerde resources**.

3. Selecteer op de pagina Automation-account de optie **account loskoppelen**.

Tijdens de poging om het Automation-account te ontkoppelen, kunt u de voortgang bijhouden onder **meldingen** in het menu.

## <a name="cleanup-automation-account"></a>Automation-account opruimen

Als Updatebeheer is geconfigureerd ter ondersteuning van eerdere versies van Azure SQL-bewaking, heeft de installatie van de functie mogelijk Automation-assets gemaakt die u moet verwijderen. Voor Updatebeheer wilt u mogelijk de volgende items verwijderen die niet meer nodig zijn:

   * Update schema's: elk is een naam die overeenkomt met de update-implementatie die u hebt gemaakt.
   * Hybrid worker-groepen die zijn gemaakt voor Updatebeheer-elk heeft de naam op dezelfde manier als *machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8)*.

## <a name="next-steps"></a>Volgende stappen

Als u deze functie opnieuw wilt inschakelen, raadpleegt u [updatebeheer van een Automation-account inschakelen](enable-from-automation-account.md), [schakelt u updatebeheer in door de Azure portal te bladeren](enable-from-portal.md), [updatebeheer in te scha kelen in een Runbook](enable-from-runbook.md)of [updatebeheer van een Azure-VM](enable-from-vm.md)in te scha kelen.
