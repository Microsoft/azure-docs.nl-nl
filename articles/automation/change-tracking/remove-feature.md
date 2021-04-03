---
title: Azure Automation Wijzigingen bijhouden-en inventaris functie verwijderen
description: In dit artikel leest u hoe u Wijzigingen bijhouden en inventarisatie niet meer kunt gebruiken en hoe u een Automation-account loskoppelt van de werk ruimte Log Analytics.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 2e3e5abdfbb2bf2e9d7a12a677422adc67336775
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92209817"
---
# <a name="remove-change-tracking-and-inventory-from-automation-account"></a>Wijzigingen bijhouden en inventaris van het Automation-account verwijderen

Nadat u het beheer van uw virtuele machines met behulp van Azure Automation Wijzigingen bijhouden en inventaris hebt ingeschakeld, kunt u besluiten om te stoppen met het gebruik ervan en de configuratie verwijderen uit het account en de gekoppelde Log Analytics-werk ruimte. In dit artikel leest u hoe u Wijzigingen bijhouden en inventarisatie volledig kunt verwijderen uit de beheerde Vm's, uw Automation-account en Log Analytics werk ruimte.

## <a name="sign-into-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="remove-management-of-vms"></a>Beheer van Vm's verwijderen

Voordat u Wijzigingen bijhouden en inventaris verwijdert, moet u eerst stoppen met het beheren van uw Vm's. Zie [vm's uit wijzigingen bijhouden verwijderen](remove-vms-from-change-tracking.md) als u de registratie van de functie ongedaan wilt maken.

## <a name="remove-changetracking-solution"></a>Change tracking-oplossing verwijderen

Voordat u het Automation-account kunt ontkoppelen van de werk ruimte, moet u deze stappen volgen om Wijzigingen bijhouden en inventarisatie volledig te verwijderen. U verwijdert de **change tracking** -oplossing uit de werk ruimte.

1. Selecteer in de Azure-portal de optie **Alle services**. Typ in de lijst met resources **Log Analytics**. Wanneer u begint te typen, worden in de lijst suggesties weer geven op basis van uw invoer. Selecteer **Log Analytics**.

2. Selecteer in de lijst met Log Analytics-werk ruimten de werk ruimte die u hebt gekozen bij het inschakelen van Wijzigingen bijhouden en de inventarisatie.

3. Selecteer aan de linkerkant **oplossingen**.  

4. Selecteer **change tracking (werkruimte naam)** in de lijst met oplossingen. Selecteer op de pagina **overzicht** voor de oplossing **verwijderen**. Selecteer **Ja** als u wordt gevraagd om te bevestigen.

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

## <a name="next-steps"></a>Volgende stappen

Als u deze functie opnieuw wilt inschakelen, raadpleegt u [Wijzigingen bijhouden en inventaris inschakelen vanuit een Automation-account](enable-from-automation-account.md), [Wijzigingen bijhouden en inventaris inschakelen door te bladeren door de Azure Portal](enable-from-portal.md), [Wijzigingen bijhouden en inventaris van een runbook](enable-from-runbook.md)in te scha kelen of [Wijzigingen bijhouden en inventaris van een Azure-VM](enable-from-vm.md)in te scha kelen.
