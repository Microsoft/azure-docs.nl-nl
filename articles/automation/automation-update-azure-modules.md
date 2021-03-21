---
title: Azure PowerShell-modules in Azure Automation bijwerken
description: In dit artikel wordt beschreven hoe u algemene Azure PowerShell-modules die standaard worden meegeleverd in Azure Automation bijwerken.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: f05c3247ecdd77fe4cf02db70c7ba4b2c534f959
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99055345"
---
# <a name="update-azure-powershell-modules"></a>Azure PowerShell-modules bijwerken

De meest voorkomende Power shell-modules zijn standaard opgenomen in elk Automation-account. Zie [standaard modules](shared-resources/modules.md#default-modules). Naarmate het Azure-team de Azure-modules regel matig bijwerkt, kunnen er wijzigingen optreden met de meegeleverde cmdlets. Deze wijzigingen, zoals het wijzigen van de naam van een para meter of het volledig terugvallen van een cmdlet, kunnen een negatieve invloed hebben op uw runbooks. 

> [!NOTE]
> U kunt geen globale modules verwijderen. Dit zijn modules die automatisch door Automation worden verstrekt.

## <a name="set-up-an-automation-account"></a>Een Automation-account instellen

Om te voor komen dat uw runbooks en de processen worden geautomatiseerd, moet u testen en valideren tijdens het maken van updates. Als u geen speciaal Automation-account hebt dat is bedoeld voor dit doel, kunt u overwegen om een groot aantal verschillende scenario's te testen tijdens de ontwikkeling van uw runbooks. Deze test moet terugkerende wijzigingen bevatten, zoals het bijwerken van de Power shell-modules.

Zorg ervoor dat er een [Azure run as-account](automation-security-overview.md#run-as-accounts) is gemaakt voor uw Automation-account.

Als u uw scripts lokaal ontwikkelt, is het raadzaam om dezelfde module versies lokaal te hebben als tijdens het testen, zodat u zeker weet dat u dezelfde resultaten krijgt. Nadat de resultaten zijn gevalideerd en u alle vereiste wijzigingen hebt toegepast, kunt u de wijzigingen naar de productie verplaatsen.

> [!NOTE]
> Een nieuw Automation-account bevat mogelijk niet de meest recente modules.

## <a name="obtain-a-runbook-to-use-for-updates"></a>Een runbook verkrijgen dat moet worden gebruikt voor updates

Als u de Azure-modules in uw Automation-account wilt bijwerken, moet u het runbook [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) gebruiken. Dit is beschikbaar als open source. Als u dit runbook wilt gaan gebruiken om uw Azure-modules bij te werken, kunt u het downloaden van de GitHub-opslag plaats. U kunt deze vervolgens importeren in uw Automation-account of als een script uitvoeren. Zie [een Runbook importeren](manage-runbooks.md#import-a-runbook)voor meer informatie over het importeren van een runbook in uw Automation-account.

Het **Update-AutomationAzureModulesForAccount-** runbook ondersteunt standaard het bijwerken van de Azure-, AzureRM-en AZ-modules. Raadpleeg het [Leesmij-bestand voor Azure-modules bijwerken](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) voor meer informatie over het bijwerken van AZ. Automation-modules met dit runbook. Er zijn extra belang rijke factoren die u moet uitvoeren wanneer u de AZ-modules in uw Automation-account gebruikt. Zie [modules beheren in azure Automation](shared-resources/modules.md)voor meer informatie.

## <a name="use-update-runbook-code-as-a-regular-powershell-script"></a>Runbook-code bijwerken als een normaal Power shell-script gebruiken

U kunt de runbook-code als een gewoon Power shell-script gebruiken in plaats van een runbook. Als u dit wilt doen, meldt u zich eerst aan bij Azure met de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) en geeft `-Login $false` u vervolgens het script door.

## <a name="use-the-update-runbook-on-sovereign-clouds"></a>Het bijwerken van het runbook op soevereine Clouds gebruiken

Als u dit runbook wilt gebruiken op soevereine Clouds, gebruikt u de `AzEnvironment` para meter om de juiste omgeving door te geven aan het runbook. Acceptabele waarden zijn Cloud (open bare Azure-Cloud), AzureChinaCloud, AzureGermanCloud en AzureUSGovernment. Deze waarden kunnen worden opgehaald met `Get-AzEnvironment | select Name` . Als u geen waarde aan deze cmdlet doorgeeft, wordt het runbook standaard ingesteld op Cloud.

## <a name="use-the-update-runbook-to-update-a-specific-module-version"></a>Het update-runbook gebruiken om een specifieke module versie bij te werken

Als u een specifieke Azure PowerShell module versie wilt gebruiken in plaats van de meest recente module die beschikbaar is op de PowerShell Gallery, geeft u deze versies door aan de optionele `ModuleVersionOverrides` para meter van het Runbook **Update-AutomationAzureModulesForAccount** . Zie het  [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1) runbook voor voor beelden. Azure PowerShell-modules die niet in de `ModuleVersionOverrides` para meter worden vermeld, worden bijgewerkt met de nieuwste module versies op de PowerShell Gallery. Als u niets doorgeeft aan de `ModuleVersionOverrides` para meter, worden alle modules bijgewerkt met de nieuwste module versies op het PowerShell Gallery. Dit gedrag is hetzelfde voor de knop **Azure-modules bijwerken** in de Azure Portal.

## <a name="next-steps"></a>Volgende stappen

* Zie [modules beheren in azure Automation](shared-resources/modules.md)voor meer informatie over het gebruik van modules.
* Zie het [Runbook Azure-modules bijwerken](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)voor meer informatie over het update runbook.
