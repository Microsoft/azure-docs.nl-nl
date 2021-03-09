---
title: Niet-compatibele Azure Automation status configuratie servers herstellen
description: In dit artikel wordt beschreven hoe u configuraties op aanvraag opnieuw kunt Toep assen op servers waar de configuratie status is gedrijfd.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: 98164c20af2a5d6ae7ccac43331a2876c23ccfb7
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500169"
---
# <a name="remediate-noncompliant-azure-automation-state-configuration-servers"></a>Niet-compatibele Azure Automation status configuratie servers herstellen

Als servers zijn geregistreerd met Azure Automation status configuratie, wordt de configuratie modus ingesteld op `ApplyOnly` , `ApplyandMonitor` of `ApplyAndAutoCorrect` . Als de modus niet is ingesteld op `ApplyAndAutoCorrect` , blijven servers die van een compatibele status zijn, om welke reden dan ook niet-compatibel.

Azure Compute biedt een functie met de naam run-opdracht waarmee klanten scripts in virtuele machines kunnen uitvoeren.
Dit document bevat voorbeeld scripts voor deze functie wanneer de configuratie-drift hand matig wordt gecorrigeerd.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Juiste drift van virtuele Windows-machines met behulp van Power shell

U kunt het aantal virtuele machines van Windows corrigeren met de `Run` opdracht functie. Zie [Power shell-scripts uitvoeren in uw Windows-VM met de opdracht uitvoeren](../virtual-machines/windows/run-command.md).

Gebruik de cmdlet [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration) om een configuratie knooppunt van Azure Automation status af te dwingen om de nieuwste configuratie te downloaden en toe te passen.

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Juiste drift van virtuele Linux-machines

Voor virtuele Linux-machines hebt u geen optie om de opdracht te gebruiken `Run` . U kunt de drift voor deze machines alleen corrigeren door het registratie proces te herhalen. 

Voor Azure-knoop punten kunt u de drift in het Azure Portal corrigeren of met AZ module-cmdlets. Informatie over dit proces wordt beschreven in [een VM inschakelen met behulp van Azure Portal](automation-dsc-onboarding.md#enable-a-vm-using-azure-portal).

Voor hybride knoop punten kunt u met behulp van de python-scripts de drift corrigeren. Zie [DSC-bewerkingen uitvoeren vanaf de Linux-computer](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Volgende stappen

- Zie [Az.Automation](/powershell/module/az.automation/#automation) voor een naslagdocumentatie voor een PowerShell-cmdlet.
- Zie [continue implementatie instellen met chocolade](automation-dsc-cd-chocolatey.md)voor een voor beeld van het gebruik van Azure Automation status configuratie in een pijp lijn voor continue implementatie.
