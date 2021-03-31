---
title: Het sjabloon implementatie script van Visual Studio bijwerken om AZ Power shell te gebruiken
description: Het Visual Studio-sjabloon implementatie script bijwerken vanuit AzureRM naar AZ Power shell
author: cweining
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: cweining
ms.openlocfilehash: 357e0289f3237ed32b0801280316225ba5530282
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "76963866"
---
# <a name="update-visual-studio-template-deployment-script-to-use-az-powershell-module"></a>Script voor de implementatie van Visual Studio-sjablonen bijwerken om te gebruiken AZ Power shell module

Visual Studio 16,4 ondersteunt het gebruik van de AZ Power shell-module in het sjabloon implementatie script. Visual Studio installeert die module echter niet automatisch. Als u de AZ-module wilt gebruiken, moet u vier stappen uitvoeren:

1. [AzureRM-module verwijderen](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module)
1. [Installeren AZ module](/powershell/azure/install-az-ps)
1. Update Visual Studio op 16,4
1. Werk het implementatie script bij in uw project.

## <a name="update-visual-studio-to-164"></a>Update Visual Studio op 16,4

Werk uw installatie van Visual Studio bij naar versie 16,4 of hoger. Zorg er tijdens de upgrade voor dat het onderdeel Azure PowerShell niet is ingeschakeld. Sinds u de AZ-module via de galerie hebt geïnstalleerd, wilt u de AzureRM-module niet opnieuw installeren.

Als u al een upgrade hebt uitgevoerd naar 16,4 en het Azure PowerShell onderdeel is gecontroleerd, kunt u het verwijderen door het installatie programma van Visual Studio uit te voeren. Selecteer niet het onderdeel Azure PowerShell in de Azure-werk belasting of op de pagina afzonderlijke onderdelen.

## <a name="update-the-deployment-script-in-your-project"></a>Het implementatie script in uw project bijwerken

Vervang alle instanties van de teken reeks ' AzureRm ' door AZ ' in uw implementatie script. U kunt [in deze informatie over](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) revisies verwijzen om de wijzigingen te bekijken. Zie [Azure PowerShell migreren van AzureRM naar AZ](/powershell/azure/migrate-from-azurerm-to-az)voor meer informatie over het bijwerken van scripts naar de AZ-module.

## <a name="next-steps"></a>Volgende stappen

Zie [projecten van Azure-resource groepen maken en implementeren via Visual Studio](create-visual-studio-deployment-project.md)voor meer informatie over het gebruik van het Visual Studio-project.
