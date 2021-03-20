---
title: Een script toevoegen aan een herstel plan in Azure Site Recovery
description: Meer informatie over het toevoegen van een VMM-script aan een herstel plan voor nood herstel van virtuele Hyper-V-machines in VMM-Clouds.
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sharrai
ms.openlocfilehash: 3217c30737a133c1c1092fc4a8a8caaa0338e980
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89425871"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>Een VMM-script toevoegen aan een herstel plan

In dit artikel wordt beschreven hoe u een System Center Virtual Machine Manager (VMM)-script maakt en dit toevoegt aan een herstel plan in [Azure site Recovery](site-recovery-overview.md).

Post opmerkingen of vragen onderaan dit artikel of op de [pagina micro soft Q&een vraag voor Azure Recovery Services](/answers/topics/azure-site-recovery.html).

## <a name="prerequisites"></a>Vereisten

U kunt Power shell-scripts gebruiken in uw herstel plannen. Als u vanuit het herstel plan toegang wilt hebben, moet u het script ontwerpen en het script in de VMM-bibliotheek plaatsen. Houd rekening met de volgende overwegingen wanneer u het script schrijft:

* Zorg ervoor dat scripts try-catch-blokken gebruiken, zodat uitzonde ringen op de juiste wijze worden afgehandeld.
    - Als er een uitzonde ring optreedt in het script, wordt het script gestopt en wordt de taak als mislukt weer gegeven.
    - Als er een fout optreedt, wordt de rest van het script niet uitgevoerd.
    - Als er een fout optreedt wanneer u een niet-geplande failover uitvoert, wordt het herstel plan voortgezet.
    - Als er een fout optreedt wanneer u een geplande failover uitvoert, wordt het herstel plan gestopt. Herstel het script, Controleer of het wordt uitgevoerd zoals verwacht en voer het herstel plan opnieuw uit.
        - De `Write-Host` opdracht werkt niet in een herstel plan script. Als u de `Write-Host` opdracht in een script gebruikt, mislukt het script. Als u een uitvoer wilt maken, maakt u een proxy script dat op zijn beurt uw hoofd script uitvoert. Gebruik de opdracht om ervoor te zorgen dat alle uitvoer wordt uitgepiped **\>\>** .
        - Er wordt een time-out van het script gegeven als het niet binnen 600 seconden wordt geretourneerd.
        - Als er iets wordt geschreven naar STDERR, wordt het script als mislukt geclassificeerd. Deze informatie wordt weer gegeven in de details van de uitvoering van het script.

* Scripts in een herstel plan worden uitgevoerd in de context van het VMM-service account. Zorg ervoor dat dit account lees machtigingen heeft voor de externe share waarop het script zich bevindt. Test het script om uit te voeren met hetzelfde niveau van gebruikers rechten als het VMM-service account.
* VMM-cmdlets worden geleverd in een Windows Power shell-module. De module wordt geïnstalleerd tijdens de installatie van de VMM-console. Als u de module in uw script wilt laden, gebruikt u de volgende opdracht in het script: 

    `Import-Module -Name virtualmachinemanager`

    Zie [aan de slag met Windows Power shell en VMM](/previous-versions/system-center/system-center-2012-R2/hh875013(v=sc.12))voor meer informatie.
* Zorg ervoor dat uw VMM-implementatie ten minste één bibliotheek server bevat. Het pad naar de bibliotheek share voor een VMM-server bevindt zich standaard lokaal op de VMM-server. De mapnaam is MSCVMMLibrary.

  Als het pad naar de bibliotheek share extern is (of als het lokaal is, maar niet is gedeeld met MSCVMMLibrary), configureert u de share als volgt met behulp van \\ libserver2. contoso. com\share\ als voor beeld:
  
  1. Open de REGI ster-editor en ga naar **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**.

  1. Wijzig de waarde voor **ScriptLibraryPath** in **\\ \libserver2.contoso.com\share \\**. Geef de volledige FQDN op. Machtigingen voor de share locatie opgeven. Dit is het hoofd knooppunt van de share. Als u wilt controleren op het hoofd knooppunt, gaat u in VMM naar het hoofd knooppunt in de bibliotheek. Het pad dat wordt geopend, is de hoofdmap van het pad. Dit is het pad dat u moet gebruiken in de variabele.

  1. Test het script met behulp van een gebruikers account dat hetzelfde niveau van gebruikers rechten heeft als het VMM-service account. Met deze gebruikers rechten wordt gecontroleerd of zelfstandige, geteste scripts op dezelfde manier worden uitgevoerd als in herstel plannen. Stel op de VMM-server het uitvoerings beleid als volgt in op bypass:

     a. Open de **64-bits Windows Power shell-** console als beheerder.
     
     b. Geef **Set-ExecutionPolicy bypass** op. Zie [using the Set-ExecutionPolicy cmdlet](/previous-versions/windows/it-pro/windows-powershell-1.0/ee176961(v=technet.10))(Engelstalig) voor meer informatie.

     > [!IMPORTANT]
     > Stel **Set-ExecutionPolicy bypass** alleen in de 64-bits Power shell-console in. Als u deze instelt voor de 32-bits Power shell-console, worden de scripts niet uitgevoerd.

## <a name="add-the-script-to-the-vmm-library"></a>Het script toevoegen aan de VMM-bibliotheek

Als u een VMM-bron site hebt, kunt u een script maken op de VMM-server. Neem vervolgens het script op in het herstel plan.

1. Maak een nieuwe map in de bibliotheek share. Bijvoorbeeld \<VMM server name> \MSSCVMMLibrary\RPScripts. Plaats de map op de bron-en doel-VMM-servers.
1. Maak het script. Geef bijvoorbeeld het script de naam RPScript. Controleer of het script werkt zoals verwacht.
1. Plaats het script in de \<VMM server name> map \MSSCVMMLibrary op de bron-en doel-VMM-servers.

## <a name="add-the-script-to-a-recovery-plan"></a>Het script toevoegen aan een herstel plan

Nadat u Vm's of replicatie groepen hebt toegevoegd aan een herstel plan en het plan hebt gemaakt, kunt u het script toevoegen aan de groep.

1. Open het herstel plan.
1. Selecteer een item in de **stap** lijst. Selecteer vervolgens **script** of **hand matige actie**.
1. Geef op of het script of de actie vóór of na het geselecteerde item moet worden toegevoegd. Als u de positie van het script omhoog of omlaag wilt verplaatsen, selecteert u de knoppen **omhoog** en **omlaag** .
1. Als u een VMM-script toevoegt, selecteert u **failover naar VMM-script**. In het pad naar het **script** voert u het relatieve pad naar de share in. Voer bijvoorbeeld **\RPScripts\RPScript.PS1** in.
1. Als u een Azure Automation runbook toevoegt, geeft u het Automation-account op waarin het runbook zich bevindt. Selecteer vervolgens het Azure runbook-script dat u wilt gebruiken.
1. Voer een testfailover van het herstel plan uit om ervoor te zorgen dat het script werkt zoals verwacht.


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [uitvoeren van failovers](site-recovery-failover.md).

