---
title: Overzicht Azure Automation VM's buiten bedrijfsuren starten/stoppen verwijderen
description: In dit artikel wordt beschreven hoe u de VM's buiten bedrijfsuren starten/stoppen verwijdert en een Automation-account ontkoppelt uit de Log Analytics-werkruimte.
services: automation
ms.subservice: process-automation
ms.date: 04/15/2021
ms.topic: conceptual
ms.openlocfilehash: 9ec76197bfde6bb679f70c44ab01712f9f52bfd2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533948"
---
# <a name="remove-startstop-vms-during-off-hours-from-automation-account"></a>Gegevens VM's buiten bedrijfsuren starten/stoppen Automation-account verwijderen

Nadat u de functie VM's buiten bedrijfsuren starten/stoppen om de status van uw Azure-VM's te beheren, kunt u besluiten om te stoppen met het gebruik ervan. U kunt deze functie verwijderen met behulp van een van de volgende methoden op basis van de ondersteunde implementatiemodellen:

* Verwijder de resourcegroep met het Automation-account en de gekoppelde Azure Monitor Log Analytics-werkruimte, die elk zijn toegewezen ter ondersteuning van deze functie.
* Ontkoppel de Log Analytics-werkruimte van het Automation-account en verwijder het Automation-account dat is toegewezen voor deze functie.
* Verwijder de functie uit een Automation-account en een gekoppelde werkruimte die ondersteuning bieden voor andere beheer- en bewakingsdoelstellingen.

Als u deze functie verwijdert, worden alleen de gekoppelde runbooks verwijderd. De planningen of variabelen die zijn gemaakt tijdens de implementatie of eventuele aangepaste gedefinieerde die daarna zijn gemaakt, worden niet verwijderd.

> [!NOTE]
> Voordat u doorgaat, controleert [](../azure-resource-manager/management/lock-resources.md) u of er geen Resource Manager zijn toegepast op het abonnement, de resourcegroep of resource, waardoor het per ongeluk verwijderen of wijzigen van kritieke resources wordt voorkomen. Wanneer u de VM's buiten bedrijfsuren starten/stoppen-oplossing implementeert, stelt deze het vergrendelingsniveau in op **CanNotDelete** voor verschillende afhankelijke resources in het Automation-account (met name de runbooks en variabelen). Eventuele vergrendelingen moeten worden verwijderd voordat u het Automation-account kunt verwijderen.

## <a name="delete-the-dedicated-resource-group"></a>De toegewezen resourcegroep verwijderen

Als u de resourcegroep wilt verwijderen, volgt u de stappen die worden beschreven in Azure Resource Manager [resourcegroep en het](../azure-resource-manager/management/delete-resource-group.md) verwijderen van resources.

## <a name="delete-the-automation-account"></a>Het Automation-account verwijderen

Voer de volgende stappen uit om uw Automation-account VM's buiten bedrijfsuren starten/stoppen te verwijderen.

1. Meld u aan bij Azure op [https://portal.azure.com](https://portal.azure.com) .

2. Navigeer naar uw Automation-account en selecteer **Gekoppelde werkruimte** onder **Gerelateerde resources.**

3. Selecteer **Naar werkruimte gaan.**

4. Klik **op Oplossingen** onder **Algemeen.**

5. Selecteer op de pagina Oplossingen **de optie Start-Stop-VM[Werkruimte]**.

6. Selecteer verwijderen in het menu op de  pagina **VMManagementSolution[Workspace].**

7. Terwijl de informatie is geverifieerd en de functie is verwijderd, kunt u de voortgang volgen onder **Meldingen,** gekozen in het menu. U keert terug naar de pagina Oplossingen na het verwijderingsproces.

### <a name="unlink-workspace-from-automation-account"></a>De werkruimte ontkoppelen van het Automation-account

Er zijn twee opties voor het loskoppelen van de Log Analytics-werkruimte van uw Automation-account. U kunt dit proces uitvoeren vanuit het Automation-account of vanuit de gekoppelde werkruimte.

Voer de volgende stappen uit om de verbinding met uw Automation-account te ontkoppelen.

1. Selecteer in Azure Portal **Automation-accounts.**

2. Open uw Automation-account en selecteer **Gekoppelde werkruimte** onder **Gerelateerde resources** aan de linkerkant.

3. Selecteer op **de pagina Werkruimte ontkoppelen** de optie **Werkruimte ontkoppelen** en reageren op prompts.

   ![Werkruimtepagina loskoppelen](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Terwijl wordt geprobeerd de Log Analytics-werkruimte los te koppelen, kunt u de voortgang volgen onder **Meldingen** in het menu.

Voer de volgende stappen uit om de verbinding met de werkruimte op te maken.

1. Selecteer in Azure Portal de optie **Log Analytics-werkruimten.**

2. Selecteer in de werkruimte **Automation-account onder** **Gerelateerde resources.**

3. Selecteer op de pagina Automation-account de optie **Account ontkoppelen** en reageren op prompts.

Terwijl wordt geprobeerd het Automation-account te ontkoppelen, kunt u de voortgang volgen onder **Meldingen** in het menu.

### <a name="delete-automation-account"></a>Automation-account verwijderen

1. Selecteer in Azure Portal **Automation-accounts.**

2. Open uw Automation-account en selecteer **Verwijderen** in het menu.

Terwijl de informatie is geverifieerd en het account wordt verwijderd, kunt u de voortgang volgen onder **Meldingen,** gekozen in het menu.

## <a name="delete-the-feature"></a>De functie verwijderen

Voer de VM's buiten bedrijfsuren starten/stoppen uit uw Automation-account om deze te verwijderen. Het Automation-account en de Log Analytics-werkruimte worden niet verwijderd als onderdeel van dit proces. Als u de Log Analytics-werkruimte niet wilt behouden, moet u deze handmatig verwijderen. Zie Azure Log Analytics-werkruimte verwijderen en herstellen voor meer informatie over het verwijderen [van uw werkruimte.](../azure-monitor/logs/delete-workspace.md)

1. Navigeer naar uw Automation-account en selecteer **Gekoppelde werkruimte** onder **Gerelateerde resources.**

2. Selecteer **Naar werkruimte gaan.**

3. Klik **op Oplossingen** onder **Algemeen.**

4. Selecteer op de pagina Oplossingen **de optie Start-Stop-VM[Werkruimte]**.

5. Selecteer verwijderen in het menu op de  pagina **VMManagementSolution[Workspace].**

    ![VM-beheerfunctie verwijderen](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. Bevestig in het venster Oplossing verwijderen dat u de functie wilt verwijderen.

7. Terwijl de informatie is geverifieerd en de functie is verwijderd, kunt u de voortgang volgen onder **Meldingen,** gekozen in het menu. U keert terug naar de pagina Oplossingen na het verwijderingsproces.

8. Als u de [resources](automation-solution-vm-management.md#components) die zijn gemaakt door de functie of later (zoals variabelen, planningen, enzovoort) niet wilt behouden, moet u ze handmatig verwijderen uit het account.

## <a name="next-steps"></a>Volgende stappen

Zie Starten/stoppen inschakelen buiten werkuren om deze functie [opnieuw in te schakelen.](automation-solution-vm-management-enable.md)