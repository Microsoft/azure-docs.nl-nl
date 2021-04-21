---
title: Update Azure PowerShell modules in Azure Automation
description: In dit artikel wordt beschreven hoe u Azure PowerShell modules kunt bijwerken die standaard in de Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c1632da35864fc6822b385adac06d7f124aea061
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830408"
---
# <a name="update-azure-powershell-modules"></a>Azure PowerShell-modules bijwerken

De meest voorkomende PowerShell-modules worden standaard in elk Automation-account geleverd. Zie [Standaardmodules.](shared-resources/modules.md#default-modules) Omdat het Azure-team de Azure-modules regelmatig bij werkt, kunnen er wijzigingen optreden met de meegeleverde cmdlets. Deze wijzigingen, bijvoorbeeld het wijzigen van de naam van een parameter of het volledig afvangen van een cmdlet, kunnen een negatieve invloed hebben op uw runbooks. 

> [!NOTE]
> U kunt geen algemene modules verwijderen. Dit zijn modules die door Automation worden aangeboden.

## <a name="set-up-an-automation-account"></a>Een Automation-account instellen

Om te voorkomen dat dit van invloed is op uw runbooks en de processen die ze automatiseren, moet u testen en valideren wanneer u updates aan het maken bent. Als u geen toegewezen Automation-account hebt dat voor dit doel is bedoeld, kunt u overwegen om er een te maken, zodat u veel verschillende scenario's kunt testen tijdens de ontwikkeling van uw runbooks. Deze test moet iteratieve wijzigingen bevatten, zoals het bijwerken van de PowerShell-modules.

Zorg ervoor dat er voor uw Automation-account een [Uitvoeren als-account voor Azure is](automation-security-overview.md#run-as-accounts) gemaakt.

Als u uw scripts lokaal ontwikkelt, is het raadzaam om dezelfde moduleversies lokaal in uw Automation-account te hebben wanneer u test om ervoor te zorgen dat u dezelfde resultaten ontvangt. Nadat de resultaten zijn gevalideerd en u de vereiste wijzigingen hebt toegepast, kunt u de wijzigingen naar productie verplaatsen.

> [!NOTE]
> Een nieuw Automation-account bevat mogelijk niet de nieuwste modules.

## <a name="obtain-a-runbook-to-use-for-updates"></a>Een runbook verkrijgen om te gebruiken voor updates

Als u de Azure-modules in uw Automation-account wilt bijwerken, moet u het runbook [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) gebruiken, dat beschikbaar is als open source. Als u dit runbook wilt gebruiken om uw Azure-modules bij te werken, downloadt u het uit de GitHub-opslagplaats. U kunt het vervolgens importeren in uw Automation-account of uitvoeren als een script. Zie Een runbook importeren voor meer informatie over het importeren van een runbook in [uw Automation-account.](manage-runbooks.md#import-a-runbook)

Het **runbook Update-AutomationAzureModulesForAccount ondersteunt** standaard het bijwerken van de modules Azure, AzureRM en Az. Lees [readme voor het runbook Azure-modules bijwerken](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) voor meer informatie over het bijwerken van Az.Automation-modules met dit runbook. Er zijn nog meer belangrijke factoren waar u rekening mee moet houden bij het gebruik van de Az-modules in uw Automation-account. Zie Modules beheren in Azure Automation voor [meer Azure Automation.](shared-resources/modules.md)

## <a name="use-update-runbook-code-as-a-regular-powershell-script"></a>Runbookcode bijwerken gebruiken als een gewoon PowerShell-script

U kunt de runbookcode gebruiken als een gewoon PowerShell-script in plaats van een runbook. Om dit te doen, moet u zich eerst aanmelden bij Azure met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) en vervolgens doorgeven `-Login $false` aan het script.

## <a name="use-the-update-runbook-on-sovereign-clouds"></a>Het updaterunbook gebruiken in onafhankelijke clouds

Als u dit runbook wilt gebruiken in onafhankelijke clouds, gebruikt u de parameter om de juiste omgeving door te `AzEnvironment` geven aan het runbook. Acceptabele waarden zijn AzureCloud (openbare Cloud van Azure), AzureChinaCloud, AzureGermanCloud en AzureUSGovernment. Deze waarden kunnen worden opgehaald met behulp van `Get-AzEnvironment | select Name` . Als u geen waarde doorlaat aan deze cmdlet, wordt het runbook standaard ingesteld op AzureCloud.

## <a name="use-the-update-runbook-to-update-a-specific-module-version"></a>Het updaterunbook gebruiken om een specifieke moduleversie bij te werken

Als u een specifieke versie van de Azure PowerShell-module wilt gebruiken in plaats van de nieuwste module die beschikbaar is op de PowerShell Gallery, geeft u deze versies door aan de optionele parameter van het `ModuleVersionOverrides` runbook **Update-AutomationAzureModulesForAccount.** Zie het runbook  [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1) voorbeelden. Azure PowerShell modules die niet worden vermeld in de parameter worden bijgewerkt met de nieuwste `ModuleVersionOverrides` moduleversies op de PowerShell Gallery. Als u niets doorgeeft aan de parameter, worden alle modules bijgewerkt met de nieuwste `ModuleVersionOverrides` moduleversies op de PowerShell Gallery. Dit gedrag is hetzelfde voor de **knop Azure-modules** bijwerken in de Azure Portal.

## <a name="next-steps"></a>Volgende stappen

* Zie Modules beheren in Azure Automation voor meer [informatie over het gebruik Azure Automation.](shared-resources/modules.md)
* Zie Runbook voor Azure-modules bijwerken voor meer informatie over [het updaterunbook.](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)
