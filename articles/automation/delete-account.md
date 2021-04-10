---
title: Uw Azure Automation-account verwijderen
description: In dit artikel leest u hoe u uw Automation-account kunt verwijderen in de verschillende configuratie scenario's.
services: automation
ms.service: automation
ms.subservice: process-automation
ms.date: 03/18/2021
ms.topic: conceptual
ms.openlocfilehash: c3a514aa507fcf069671f987e175b7ae5be59d10
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735068"
---
# <a name="how-to-delete-your-azure-automation-account"></a>Uw Azure Automation-account verwijderen

Nadat u een Azure Automation-account hebt ingeschakeld om het IT-of bedrijfs proces te automatiseren, of om de andere functies in te scha kelen voor het ondersteunen van het beheer van uw Azure-en niet-Azure-machines, zoals Updatebeheer, kunt u ervoor kiezen om het Automation-account niet meer te gebruiken. Als u functies hebt ingeschakeld die afhankelijk zijn van integratie met een Azure Monitor Log Analytics werk ruimte, zijn er meer stappen vereist om deze actie te volt ooien.

Het verwijderen van uw Automation-account kan worden uitgevoerd met een van de volgende methoden op basis van de ondersteunde implementatie modellen:

* Verwijder de resource groep met het Automation-account.
* Verwijder de resource groep met het Automation-account en de gekoppelde Azure Monitor Log Analytics werk ruimte als:

    * Het account en de werk ruimte zijn specifiek voor de ondersteuning van Updatebeheer, Wijzigingen bijhouden en inventaris, en/of VM's buiten bedrijfsuren starten/stoppen.
    * Het account is speciaal bedoeld voor het verwerken van automatisering en geïntegreerd met een werk ruimte om de taak status en taak stromen voor een runbook te verzenden.

* Koppel de Log Analytics-werk ruimte uit het Automation-account en verwijder het Automation-account.
* Verwijder de functie uit de gekoppelde werk ruimte, koppel het account uit de werk ruimte en verwijder vervolgens het Automation-account.

In dit artikel leest u hoe u uw Automation-account volledig kunt verwijderen via de Azure Portal, Power shell, de Azure CLI of de REST API.

## <a name="delete-the-dedicated-resource-group"></a>De speciale resource groep verwijderen

Als u uw Automation-account wilt verwijderen en ook de werk ruimte Log Analytics als deze is gekoppeld aan het account, dat is gemaakt in dezelfde resource groep die is toegewezen aan het account, volgt u de stappen die worden beschreven in het artikel [Azure Resource Manager resource groep en resource verwijderen](../azure-resource-manager/management/delete-resource-group.md) .

## <a name="delete-a-standalone-automation-account"></a>Een zelfstandig Automation-account verwijderen

Als uw Automation-account niet is gekoppeld aan een Log Analytics-werk ruimte, voert u de volgende stappen uit om deze te verwijderen.

# <a name="azure-portal"></a>[Azure-portal](#tab/azure-portal)

1. Meld u aan bij Azure op [https://portal.azure.com](https://portal.azure.com) .

2. Navigeer in het Azure Portal naar **Automation-accounts**.

3. Open uw Automation-account en selecteer **verwijderen** in het menu.

Terwijl de gegevens worden geverifieerd en het account wordt verwijderd, kunt u de voortgang bijhouden onder **meldingen**, geselecteerd in het menu.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Met deze opdracht wordt het Automation-account verwijderd zonder dat om validatie wordt gevraagd.

```powershell
Remove-AzAutomationAccount -Name "automationAccountName" -Force -ResourceGroupName "resourceGroupName"
```

---

## <a name="delete-a-standalone-automation-account-linked-to-workspace"></a>Een zelfstandig Automation-account verwijderen dat is gekoppeld aan de werk ruimte

Als uw Automation-account is gekoppeld aan een Log Analytics werk ruimte om taak stromen en taak logboeken te verzamelen, voert u de volgende stappen uit om het account te verwijderen.

Er zijn twee opties voor het ontkoppelen van de Log Analytics-werk ruimte van uw Automation-account. U kunt dit proces uitvoeren vanuit het Automation-account of vanuit de gekoppelde werk ruimte.

Voer de volgende stappen uit om de koppeling te verbreken van uw Automation-account.

1. Navigeer in het Azure Portal naar **Automation-accounts**.

2. Open uw Automation-account en selecteer **gekoppelde werk ruimte** onder **gerelateerde resources** aan de linkerkant.

3. Op de pagina **werk ruimte ontkoppelen** selecteert u **werk ruimte ontkoppelen** en reageert u op prompts.

   ![Pagina werk ruimte ontkoppelen](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Tijdens de poging om de Log Analytics-werk ruimte te ontkoppelen, kunt u de voortgang bijhouden onder **meldingen** in het menu.

Voer de volgende stappen uit om de koppeling met de werk ruimte te verbreken.

1. Ga in het Azure Portal naar **log Analytics-werk ruimten**.

2. Selecteer in de werk ruimte **Automation-account** onder **gerelateerde resources**.

3. Selecteer op de pagina Automation-account de optie **account ontkoppelen** en reageer op prompts.

Tijdens de poging om het Automation-account te ontkoppelen, kunt u de voortgang bijhouden onder **meldingen** in het menu.

Nadat het Automation-account van de werk ruimte is ontkoppeld, voert u de stappen in de sectie [zelfstandig Automation-account](#delete-a-standalone-automation-account) uit om het account te verwijderen.

## <a name="delete-a-shared-capability-automation-account"></a>Een Automation-automatiserings account verwijderen

Voer de volgende stappen uit om uw Automation-account te verwijderen dat is gekoppeld aan een Log Analytics-werk ruimte ter ondersteuning van Updatebeheer, Wijzigingen bijhouden en inventaris en/of VM's buiten bedrijfsuren starten/stoppen.

### <a name="step-1-delete-the-solution-from-the-linked-workspace"></a>Stap 1. De oplossing uit de gekoppelde werk ruimte verwijderen

# <a name="azure-portal"></a>[Azure-portal](#tab/azure-portal)

1. Meld u aan bij Azure op [https://portal.azure.com](https://portal.azure.com) .

2. Navigeer naar uw Automation-account en selecteer **gekoppelde werk ruimte** onder **gerelateerde resources**.

3. Selecteer **Ga naar werk ruimte**.

4. Klik op **oplossingen** onder **Algemeen**.

5. Selecteer op de pagina oplossingen een van de volgende onderdelen op basis van de functie (s) die in het account zijn geïmplementeerd:

    * Selecteer voor VM's buiten bedrijfsuren starten/stoppen **Start-Stop-VM [werkruimte naam]**.
    * Selecteer voor Updatebeheer **updates (werkruimte naam)**.
    * Selecteer **change tracking (werkruimte naam)** voor wijzigingen bijhouden en inventarisatie.

6. Selecteer op de pagina **oplossing** de optie **verwijderen** in het menu. Als meer dan een van de hierboven vermelde functies zijn geïmplementeerd in het Automation-account en de gekoppelde werk ruimte, moet u elk item selecteren en verwijderen voordat u doorgaat.

7. Terwijl de informatie wordt gecontroleerd en de functie wordt verwijderd, kunt u de voortgang bijhouden onder **meldingen**, geselecteerd in het menu. Nadat het proces is verwijderd, keert u terug naar de pagina oplossingen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u een geïnstalleerde oplossing met behulp van Azure PowerShell wilt verwijderen, gebruikt u de cmdlet [Remove-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution) .

```powershell
Remove-AzMonitorLogAnalyticsSolution -ResourceGroupName "resourceGroupName" -Name "solutionName"
```

---

### <a name="step-2-unlink-workspace-from-automation-account"></a>Stap 2. De werkruimte ontkoppelen van het Automation-account

Er zijn twee opties voor het ontkoppelen van de Log Analytics-werk ruimte van uw Automation-account. U kunt dit proces uitvoeren vanuit het Automation-account of vanuit de gekoppelde werk ruimte.

Voer de volgende stappen uit om de koppeling te verbreken van uw Automation-account.

1. Navigeer in het Azure Portal naar **Automation-accounts**.

2. Open uw Automation-account en selecteer **gekoppelde werk ruimte** onder **gerelateerde resources** aan de linkerkant.

3. Op de pagina **werk ruimte ontkoppelen** selecteert u **werk ruimte ontkoppelen** en reageert u op prompts.

   ![Pagina werk ruimte ontkoppelen](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Tijdens de poging om de Log Analytics-werk ruimte te ontkoppelen, kunt u de voortgang bijhouden onder **meldingen** in het menu.

Voer de volgende stappen uit om de koppeling met de werk ruimte te verbreken.

1. Ga in het Azure Portal naar **log Analytics-werk ruimten**.

2. Selecteer in de werk ruimte **Automation-account** onder **gerelateerde resources**.

3. Selecteer op de pagina Automation-account de optie **account ontkoppelen** en reageer op prompts.

Tijdens de poging om het Automation-account te ontkoppelen, kunt u de voortgang bijhouden onder **meldingen** in het menu.

### <a name="step-3-delete-automation-account"></a>Stap 3. Automation-account verwijderen

Nadat het Automation-account van de werk ruimte is ontkoppeld, voert u de stappen in de sectie [zelfstandig Automation-account](#delete-a-standalone-automation-account) uit om het account te verwijderen.

## <a name="next-steps"></a>Volgende stappen

Zie [een zelfstandig Azure Automation account maken](automation-create-standalone-account.md)als u een Automation-account wilt maken op basis van de Azure Portal. Als u uw account liever met behulp van een sjabloon wilt maken, raadpleegt u [een Automation-account maken met behulp van een Azure Resource Manager sjabloon](quickstart-create-automation-account-template.md).