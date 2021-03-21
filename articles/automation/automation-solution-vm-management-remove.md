---
title: Overzicht van Azure Automation VM's buiten bedrijfsuren starten/stoppen verwijderen
description: In dit artikel wordt beschreven hoe u de VM's buiten bedrijfsuren starten/stoppen-functie verwijdert en een Automation-account loskoppelt vanuit de werk ruimte Log Analytics.
services: automation
ms.subservice: process-automation
ms.date: 03/04/2021
ms.topic: conceptual
ms.openlocfilehash: 0bab5d8e82ce432e9b3834fe4c003316545eb338
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122082"
---
# <a name="remove-startstop-vms-during-off-hours-from-automation-account"></a>VM's buiten bedrijfsuren starten/stoppen verwijderen uit Automation-account

Nadat u de functie VM's buiten bedrijfsuren starten/stoppen hebt ingeschakeld om de uitvoerings status van uw Azure-Vm's te beheren, kunt u ervoor kiezen om deze niet meer te gebruiken. Het verwijderen van deze functie kan worden uitgevoerd met behulp van een van de volgende methoden op basis van de ondersteunde implementatie modellen:

* Verwijder de resource groep met het Automation-account en de gekoppelde Azure Monitor Log Analytics werk ruimte, elk speciaal voor de ondersteuning van deze functie.
* Koppel de Log Analytics-werk ruimte uit het Automation-account en verwijder het Automation-account dat u voor deze functie hebt toegewezen.
* De functie verwijderen uit een Automation-account en een gekoppelde werk ruimte die andere beheer-en bewakings doelstellingen ondersteunt.

Als u deze functie verwijdert, worden alleen de gekoppelde runbooks verwijderd, worden de planningen of variabelen die zijn gemaakt tijdens de implementatie of door aangepaste gedefinieerde waarden die na zijn gemaakt, niet verwijderd.

## <a name="delete-the-dedicated-resource-group"></a>De speciale resource groep verwijderen

Als u de resource groep wilt verwijderen, voert u de stappen uit die worden beschreven in het artikel [Azure Resource Manager resource groep en resource verwijderen](../azure-resource-manager/management/delete-resource-group.md) .

## <a name="delete-the-automation-account"></a>Het Automation-account verwijderen

Voer de volgende stappen uit om uw Automation-account te verwijderen dat aan VM's buiten bedrijfsuren starten/stoppen is toegewezen.

1. Meld u aan bij Azure op [https://portal.azure.com](https://portal.azure.com) .

2. Navigeer naar uw Automation-account en selecteer **gekoppelde werk ruimte** onder **gerelateerde resources**.

3. Selecteer **Ga naar werk ruimte**.

4. Klik op **oplossingen** onder **Algemeen**.

5. Selecteer op de pagina oplossingen **Start-Stop-VM [werk ruimte]**.

6. Selecteer op de pagina **VMManagementSolution [workspace]** **verwijderen** in het menu.

7. Terwijl de informatie wordt gecontroleerd en de functie wordt verwijderd, kunt u de voortgang bijhouden onder **meldingen**, geselecteerd in het menu. Nadat het proces is verwijderd, keert u terug naar de pagina oplossingen.

### <a name="unlink-workspace-from-automation-account"></a>De werkruimte ontkoppelen van het Automation-account

Er zijn twee opties voor het ontkoppelen van de Log Analytics-werk ruimte van uw Automation-account. U kunt dit proces uitvoeren vanuit het Automation-account of vanuit de gekoppelde werk ruimte.

Voer de volgende stappen uit om de koppeling te verbreken van uw Automation-account.

1. Selecteer in de Azure Portal **Automation-accounts**.

2. Open uw Automation-account en selecteer **gekoppelde werk ruimte** onder **gerelateerde resources** aan de linkerkant.

3. Op de pagina **werk ruimte ontkoppelen** selecteert u **werk ruimte ontkoppelen** en reageert u op prompts.

   ![Pagina werk ruimte ontkoppelen](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Tijdens de poging om de Log Analytics-werk ruimte te ontkoppelen, kunt u de voortgang bijhouden onder **meldingen** in het menu.

Voer de volgende stappen uit om de koppeling met de werk ruimte te verbreken.

1. Selecteer **log Analytics werk ruimten** In het Azure Portal.

2. Selecteer in de werk ruimte **Automation-account** onder **gerelateerde resources**.

3. Selecteer op de pagina Automation-account de optie **account ontkoppelen** en reageer op prompts.

Tijdens de poging om het Automation-account te ontkoppelen, kunt u de voortgang bijhouden onder **meldingen** in het menu.

### <a name="delete-automation-account"></a>Automation-account verwijderen

1. Selecteer in de Azure Portal **Automation-accounts**.

2. Open uw Automation-account en selecteer **verwijderen** in het menu.

Terwijl de gegevens worden geverifieerd en het account wordt verwijderd, kunt u de voortgang bijhouden onder **meldingen**, geselecteerd in het menu.

## <a name="delete-the-feature"></a>De functie verwijderen

Voer de volgende stappen uit om VM's buiten bedrijfsuren starten/stoppen van uw Automation-account te verwijderen. Het Automation-account en de Log Analytics-werk ruimte worden niet verwijderd als onderdeel van dit proces. Als u de Log Analytics-werk ruimte niet wilt gebruiken, moet u deze hand matig verwijderen. Zie [Azure log Analytics-werk ruimte verwijderen en herstellen](../azure-monitor/logs/delete-workspace.md)voor meer informatie over het verwijderen van uw werk ruimte.

1. Navigeer naar uw Automation-account en selecteer **gekoppelde werk ruimte** onder **gerelateerde resources**.

2. Selecteer **Ga naar werk ruimte**.

3. Klik op **oplossingen** onder **Algemeen**.

4. Selecteer op de pagina oplossingen **Start-Stop-VM [werk ruimte]**.

5. Selecteer op de pagina **VMManagementSolution [workspace]** **verwijderen** in het menu.

    ![VM-beheer functie verwijderen](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. Bevestig in het venster oplossing verwijderen dat u de functie wilt verwijderen.

7. Terwijl de informatie wordt gecontroleerd en de functie wordt verwijderd, kunt u de voortgang bijhouden onder **meldingen**, geselecteerd in het menu. Nadat het proces is verwijderd, keert u terug naar de pagina oplossingen.

8. Als u de [resources](automation-solution-vm-management.md#components) die door de functie zijn gemaakt, niet wilt blijven gebruiken of als u later (zoals variabelen, schema's enz.) wilt, moet u deze hand matig verwijderen uit het account.

## <a name="next-steps"></a>Volgende stappen

Als u deze functie opnieuw wilt inschakelen, raadpleegt u [Start/Stop inschakelen tijdens buiten kantoor uren](automation-solution-vm-management-enable.md).