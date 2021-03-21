---
title: Een upgrade uitvoeren van de VM Insights-afhankelijkheids agent
description: In dit artikel wordt beschreven hoe u de machine Insights-afhankelijkheids agent bijwerkt met behulp van de opdracht regel, de installatie wizard en andere methoden.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/16/2020
ms.openlocfilehash: acd1b6c8e5c1be76b29c93fddc57c799aef0a526
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046700"
---
# <a name="how-to-upgrade-the-vm-insights-dependency-agent"></a>Een upgrade uitvoeren van de VM Insights-afhankelijkheids agent

Na de eerste implementatie van de VM Insights-afhankelijkheids agent, worden er updates uitgebracht met oplossingen voor problemen of ondersteuning van nieuwe functies of functionaliteit.  In dit artikel wordt uitgelegd hoe u de beschik bare methoden en de upgrade hand matig of via Automation kunt begrijpen.

## <a name="upgrade-options"></a>Upgrade opties 

De afhankelijkheids agent voor Windows en Linux kan hand matig worden bijgewerkt naar de meest recente versie of automatisch afhankelijk van het implementatie scenario en de omgeving waarin de computer wordt uitgevoerd. De volgende methoden kunnen worden gebruikt om de agent bij te werken.

|Omgeving |Installatie methode |Upgrademethode |
|------------|--------------------|---------------|
|Azure VM | VM-extensie van afhankelijkheids agent voor [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) en [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) | De agent wordt standaard automatisch geüpgraded tenzij u uw Azure Resource Manager-sjabloon hebt geconfigureerd om u af te melden door de eigenschap *autoUpgradeMinorVersion* in te stellen op **False**. De upgrade voor de secundaire versie waarbij automatische upgrade is uitgeschakeld en een primaire versie-upgrade dezelfde methode volgen: Verwijder de extensie en installeer deze opnieuw. |
| Aangepaste installatie kopieën van Azure VM | Hand matige installatie van de afhankelijkheids agent voor Windows/Linux | Het bijwerken van Vm's naar de nieuwste versie van de agent moet worden uitgevoerd vanaf de opdracht regel met het Windows Installer-pakket of Linux self-extracting en installeer bare shell script bundel.|
| Niet-Azure Vm's | Hand matige installatie van de afhankelijkheids agent voor Windows/Linux | Het bijwerken van Vm's naar de nieuwste versie van de agent moet worden uitgevoerd vanaf de opdracht regel met het Windows Installer-pakket of Linux self-extracting en installeer bare shell script bundel. |

## <a name="upgrade-windows-agent"></a>Windows-agent bijwerken 

Als u de agent op een virtuele Windows-machine wilt bijwerken naar de nieuwste versie die niet is geïnstalleerd met behulp van de VM-extensie voor de afhankelijkheids agent, voert u uit vanaf de opdracht prompt, het script of een andere Automation-oplossing of met behulp van de wizard Setup van InstallDependencyAgent-Windows.exe.  

U kunt [hier](https://aka.ms/dependencyagentwindows)de meest recente versie van de Windows-agent downloaden.

### <a name="using-the-setup-wizard"></a>Met de wizard Setup

1. Meld u aan bij de computer met een account met beheerders rechten.

2. Voer **InstallDependencyAgent-Windows.exe** uit om de installatie wizard te starten.
   
3. Volg de **installatie** wizard van dependency agent om de vorige versie van de afhankelijkheids agent te verwijderen en vervolgens de nieuwste versie te installeren.


### <a name="from-the-command-line"></a>Vanaf de opdracht regel

1. Meld u aan bij de computer met een account met beheerders rechten.

2. Voer de volgende opdracht uit.

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    `/RebootMode=manual`Met de para meter voor komt u dat de upgrade van de computer automatisch opnieuw wordt opgestart als sommige processen gebruikmaken van bestanden uit de vorige versie en een vergren deling hebben. 

3. Om te bevestigen dat de upgrade is geslaagd, raadpleegt u de `install.log` voor gedetailleerde informatie over de installatie. De logboekmap is *%ProgramFiles%\Microsoft dependency Agent\logs*.

## <a name="upgrade-linux-agent"></a>Linux-agent bijwerken 

Upgrade van eerdere versies van de afhankelijkheids agent op Linux wordt ondersteund en uitgevoerd na dezelfde opdracht als een nieuwe installatie.

U kunt [hier](https://aka.ms/dependencyagentlinux)de meest recente versie van de Linux-agent downloaden.

1. Meld u aan bij de computer met een account met beheerders rechten.

2. Voer de volgende opdracht uit als basis `sh InstallDependencyAgent-Linux64.bin -s` . 

Als de afhankelijkheids agent niet kan worden gestart, raadpleegt u de logboeken voor gedetailleerde informatie over de fout. Op Linux-agents is de logboekmap */var/opt/Microsoft/dependency-agent/log*. 

## <a name="next-steps"></a>Volgende stappen

Als u wilt stoppen met het bewaken van uw Vm's gedurende een bepaalde periode of als u VM Insights volledig wilt verwijderen, raadpleegt u [de bewaking van uw vm's in VM Insights uitschakelen](../vm/vminsights-optout.md).
