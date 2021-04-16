---
title: Uw Azure Automation verwijderen
description: In dit artikel wordt beschreven hoe u uw Automation-account verwijdert in de verschillende configuratiescenario's.
services: automation
ms.service: automation
ms.subservice: process-automation
ms.date: 04/15/2021
ms.topic: conceptual
ms.openlocfilehash: d088f3adc391068de5e337c10ab52dc3d3a2dd07
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535542"
---
# <a name="how-to-delete-your-azure-automation-account"></a>Uw Azure Automation verwijderen

Nadat u een Azure Automation-account hebt ingeschakeld om IT of bedrijfsproces te automatiseren, of nadat u de andere functies hebt ingeschakeld voor het ondersteunen van operationeel beheer van uw Azure- en niet-Azure-machines, zoals Updatebeheer, kunt u besluiten om het Automation-account niet meer te gebruiken. Als u functies hebt ingeschakeld die afhankelijk zijn van integratie met een Azure Monitor Log Analytics-werkruimte, zijn er meer stappen nodig om deze actie te voltooien.

U kunt uw Automation-account verwijderen met behulp van een van de volgende methoden op basis van de ondersteunde implementatiemodellen:

* Verwijder de resourcegroep met het Automation-account.
* Verwijder de resourcegroep met het Automation-account en de gekoppelde Azure Monitor Log Analytics-werkruimte, als:

    * Het account en de werkruimte zijn toegewezen aan Updatebeheer, Wijzigingen bijhouden en inventaris en/of VM's buiten bedrijfsuren starten/stoppen.
    * Het account is toegewezen aan procesautomatisering en geïntegreerd met een werkruimte voor het verzenden van runbook-taakstatussen en taakstromen.

* Ontkoppel de Log Analytics-werkruimte van het Automation-account en verwijder het Automation-account.
* Verwijder de functie uit uw gekoppelde werkruimte, koppel het account los van de werkruimte en verwijder vervolgens het Automation-account.

In dit artikel wordt beschreven hoe u uw Automation-account volledig kunt verwijderen via de Azure Portal, met behulp van Azure PowerShell, de Azure CLI of de REST API.

> [!NOTE]
> Voordat u doorgaat, controleert [](../azure-resource-manager/management/lock-resources.md) u of er geen Resource Manager zijn toegepast op het abonnement, de resourcegroep of resource, waardoor het per ongeluk verwijderen of wijzigen van kritieke resources wordt voorkomen. Als u de VM's buiten bedrijfsuren starten/stoppen-oplossing hebt geïmplementeerd, stelt deze het vergrendelingsniveau in op **CanNotDelete** voor verschillende afhankelijke resources in het Automation-account (met name de runbooks en variabelen). Eventuele vergrendelingen moeten worden verwijderd voordat u het Automation-account kunt verwijderen.

## <a name="delete-the-dedicated-resource-group"></a>De toegewezen resourcegroep verwijderen

Als u uw Automation-account en ook de Log Analytics-werkruimte wilt verwijderen als deze is gekoppeld aan het account, gemaakt in dezelfde resourcegroep die is toegewezen aan het account, volgt u de stappen die worden beschreven in het artikel [Azure Resource Manager resourcegroep](../azure-resource-manager/management/delete-resource-group.md) en resource verwijderen.

## <a name="delete-a-standalone-automation-account"></a>Een zelfstandig Automation-account verwijderen

Als uw Automation-account niet is gekoppeld aan een Log Analytics-werkruimte, voert u de volgende stappen uit om deze te verwijderen.

# <a name="azure-portal"></a>[Azure-portal](#tab/azure-portal)

1. Meld u aan bij Azure op [https://portal.azure.com](https://portal.azure.com) .

2. Navigeer in Azure Portal naar **Automation-accounts.**

3. Open uw Automation-account en selecteer **Verwijderen** in het menu.

Terwijl de informatie is geverifieerd en het account is verwijderd, kunt u de voortgang volgen onder **Meldingen,** gekozen in het menu.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Met deze opdracht wordt het Automation-account verwijderd zonder dat u om validatie wordt gevraagd.

```powershell
Remove-AzAutomationAccount -Name "automationAccountName" -Force -ResourceGroupName "resourceGroupName"
```

---

## <a name="delete-a-standalone-automation-account-linked-to-workspace"></a>Een zelfstandig Automation-account verwijderen dat is gekoppeld aan een werkruimte

Als uw Automation-account is gekoppeld aan een Log Analytics-werkruimte om taakstromen en taaklogboeken te verzamelen, voert u de volgende stappen uit om het account te verwijderen.

Er zijn twee opties voor het loskoppelen van de Log Analytics-werkruimte van uw Automation-account. U kunt dit proces uitvoeren vanuit het Automation-account of vanuit de gekoppelde werkruimte.

Voer de volgende stappen uit om de verbinding met uw Automation-account te ontkoppelen.

1. Navigeer in Azure Portal naar **Automation-accounts.**

2. Open uw Automation-account en selecteer **Gekoppelde werkruimte** onder **Gerelateerde resources** aan de linkerkant.

3. Selecteer op **de pagina Werkruimte ontkoppelen** de optie **Werkruimte ontkoppelen** en reageer op prompts.

   ![Werkruimtepagina loskoppelen](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Terwijl wordt geprobeerd de Log Analytics-werkruimte los te koppelen, kunt u de voortgang volgen onder **Meldingen** in het menu.

Voer de volgende stappen uit om de werkruimte los te koppelen.

1. Navigeer in Azure Portal naar **Log Analytics-werkruimten.**

2. Selecteer in de werkruimte **Automation-account onder** **Gerelateerde resources.**

3. Selecteer op de pagina Automation-account de optie **Account ontkoppelen** en reageer op prompts.

Terwijl wordt geprobeerd het Automation-account los te koppelen, kunt u de voortgang volgen onder **Meldingen** in het menu.

Nadat het Automation-account is losgekoppeld van de werkruimte, voert u de stappen uit in de sectie zelfstandig [Automation-account](#delete-a-standalone-automation-account) om het account te verwijderen.

## <a name="delete-a-shared-capability-automation-account"></a>Een Automation-account voor gedeelde mogelijkheden verwijderen

Als u uw Automation-account wilt verwijderen dat is gekoppeld aan een Log Analytics-werkruimte ter ondersteuning van Updatebeheer, Wijzigingen bijhouden en inventaris en/of VM's buiten bedrijfsuren starten/stoppen, voert u de volgende stappen uit.

### <a name="step-1-delete-the-solution-from-the-linked-workspace"></a>Stap 1. De oplossing verwijderen uit de gekoppelde werkruimte

# <a name="azure-portal"></a>[Azure-portal](#tab/azure-portal)

1. Meld u aan bij Azure op [https://portal.azure.com](https://portal.azure.com) .

2. Navigeer naar uw Automation-account en selecteer **Gekoppelde werkruimte** onder **Gerelateerde resources.**

3. Selecteer **Naar werkruimte gaan.**

4. Klik **op Oplossingen** onder **Algemeen.**

5. Selecteer op de pagina Oplossingen een van de volgende opties op basis van de functies die in het account zijn geïmplementeerd:

    * Voor VM's buiten bedrijfsuren starten/stoppen selecteert **u Start-Stop-VM[werkruimtenaam]**.
    * Selecteer Updatebeheer updates **(werkruimtenaam).**
    * Selecteer Wijzigingen bijhouden en inventaris **ChangeTracking(werkruimtenaam)** voor meer.

6. Selecteer verwijderen **in** het menu **op** de pagina Oplossing. Als meer dan een van de bovenstaande functies zijn geïmplementeerd in het Automation-account en de gekoppelde werkruimte, moet u deze selecteren en verwijderen voordat u doorgaat.

7. Terwijl de informatie is geverifieerd en de functie is verwijderd, kunt u de voortgang volgen onder **Meldingen,** gekozen in het menu. U keert terug naar de pagina Oplossingen na het verwijderingsproces.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u een geïnstalleerde oplossing wilt verwijderen Azure PowerShell, gebruikt u de cmdlet [Remove-AzMonitorLogAnalyticsSolution.](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution)

```powershell
Remove-AzMonitorLogAnalyticsSolution -ResourceGroupName "resourceGroupName" -Name "solutionName"
```

---

### <a name="step-2-unlink-workspace-from-automation-account"></a>Stap 2. De werkruimte ontkoppelen van het Automation-account

Er zijn twee opties voor het loskoppelen van de Log Analytics-werkruimte van uw Automation-account. U kunt dit proces uitvoeren vanuit het Automation-account of vanuit de gekoppelde werkruimte.

Voer de volgende stappen uit om de verbinding met uw Automation-account te ontkoppelen.

1. Navigeer in Azure Portal naar **Automation-accounts.**

2. Open uw Automation-account en selecteer **Gekoppelde werkruimte** onder **Gerelateerde resources** aan de linkerkant.

3. Selecteer op **de pagina Werkruimte ontkoppelen** de optie **Werkruimte ontkoppelen** en reageer op prompts.

   ![Werkruimtepagina loskoppelen](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Terwijl wordt geprobeerd de Log Analytics-werkruimte los te koppelen, kunt u de voortgang volgen onder **Meldingen** in het menu.

Voer de volgende stappen uit om de verbinding met de werkruimte op te maken.

1. Navigeer in Azure Portal naar **Log Analytics-werkruimten.**

2. Selecteer in de werkruimte **Automation-account onder** **Gerelateerde resources.**

3. Selecteer op de pagina Automation-account de optie **Account ontkoppelen** en reageer op prompts.

Terwijl wordt geprobeerd het Automation-account te ontkoppelen, kunt u de voortgang volgen onder **Meldingen** in het menu.

### <a name="step-3-delete-automation-account"></a>Stap 3. Automation-account verwijderen

Nadat het Automation-account is losgekoppeld van de werkruimte, voert u de stappen uit in de sectie zelfstandig [Automation-account](#delete-a-standalone-automation-account) om het account te verwijderen.

## <a name="next-steps"></a>Volgende stappen

Als u een Automation-account wilt maken op Azure Portal, zie [Een zelfstandig Azure Automation maken.](automation-create-standalone-account.md) Zie Een Automation-account maken met behulp van een Azure Resource Manager sjabloon als u liever een account [maakt met behulp Azure Resource Manager sjabloon.](quickstart-create-automation-account-template.md)