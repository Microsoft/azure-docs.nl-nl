---
title: Problemen met de Azure VM-extensie oplossen voor herstel na nood gevallen met Azure Site Recovery
description: Los problemen met de Azure VM-extensie op voor herstel na nood gevallen met Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: c1915d108bf9465d3e5b8d6a55053b583ee4f580
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009701"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>Problemen met Azure VM-extensies oplossen

Dit artikel bevat probleemoplossings stappen die u kunnen helpen bij het oplossen van Azure Site Recovery fouten met betrekking tot de VM-agent en-extensie.

## <a name="low-system-resources"></a>Weinig systeem bronnen

Dit probleem treedt op wanneer het systeem weinig beschikbaar geheugen heeft en kan geen geheugen toewijzen voor de installatie van de Mobility-service. Zorg ervoor dat er voldoende geheugen beschikbaar is voor de installatie om door te gaan en goed te volt ooien.

## <a name="azure-site-recovery-extension-time-out"></a>Time-out voor uitbrei ding van Azure Site Recovery  

Fout bericht: er is een time-out opgetreden bij het uitvoeren van de taak tijdens het bijhouden van de extensie bewerking die moet worden gestart<br>
Fout code: "151076"

 Azure Site Recovery een uitbrei ding op de virtuele machine hebt geïnstalleerd als onderdeel van een taak voor het inschakelen van beveiliging. Een van de volgende omstandigheden kan verhinderen dat de beveiliging wordt geactiveerd en dat de taak mislukt. Voer de volgende stappen voor probleem oplossing uit en voer de bewerking opnieuw uit:

- [De agent is geïnstalleerd op de VM, maar reageert niet (voor Windows-Vm's)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [De agent die is geïnstalleerd in de virtuele machine is verouderd (voor Linux-Vm's)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [De uitbrei ding van de Site Recovery kan niet worden bijgewerkt of geladen](#the-site-recovery-extension-fails-to-update-or-load)

Fout bericht: de bewerking voor een eerdere Site Recovery-extensie neemt meer tijd in beslag dan verwacht.<br>
Fout code: "150066"

- [De agent is geïnstalleerd op de VM, maar reageert niet (voor Windows-Vm's)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [De agent die is geïnstalleerd in de virtuele machine is verouderd (voor Linux-Vm's)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [De status van de Site Recovery-extensie is onjuist](#the-site-recovery-extension-fails-to-update-or-load)

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>De beveiliging is mislukt omdat de VM-agent niet reageert

Fout bericht: er is een time-out opgetreden bij het uitvoeren van de taak tijdens het bijhouden van de extensie bewerking die moet worden gestart.<br>
Fout code: "151099"

Deze fout kan optreden als de Azure-gast agent op de virtuele machine niet de status gereed heeft.

U kunt de status van de Azure-gast agent controleren in het [Azure Portal](https://portal.azure.com/). Ga naar de virtuele machine die u wilt beveiligen en controleer de status in de eigenschappen van de **VM**-  >  **instellingen**  >    >  . De meeste tijd is de status van de agent gereed na het opnieuw opstarten van de virtuele machine. Als u echter niet opnieuw kunt opstarten of u nog steeds het probleem ondervindt, voert u de volgende stappen voor probleem oplossing uit:

- [De agent is geïnstalleerd op de VM, maar reageert niet (voor Windows-Vm's)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [De agent die is geïnstalleerd in de virtuele machine is verouderd (voor Linux-Vm's)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)


Fout bericht: er is een time-out opgetreden bij het uitvoeren van de taak tijdens het bijhouden van de extensie bewerking die moet worden gestart.<br>
Fout code: "151095"

Deze fout treedt op wanneer de agent versie op de Linux-computer verouderd is. Voer de volgende stap voor het oplossen van problemen uit:

- [De agent die is geïnstalleerd in de virtuele machine is verouderd (voor Linux-Vm's)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)  

## <a name="causes-and-solutions"></a>Oorzaken en oplossingen

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>De agent is geïnstalleerd op de VM, maar reageert niet (voor Windows-Vm's)

#### <a name="solution"></a>Oplossing
De VM-agent is mogelijk beschadigd of de service is gestopt. Het opnieuw installeren van de VM-agent helpt de meest recente versie op te halen. Het helpt ook om communicatie met de service opnieuw te starten.

1. Bepaal of de Windows Azure Guest Agent-service wordt uitgevoerd in de VM-Services (Services. msc). Start de Windows Azure Guest Agent-service opnieuw.    
1. Als de Windows Azure Guest Agent-service niet wordt weer gegeven in Services, opent u het configuratie scherm. Ga naar **Program ma's en onderdelen** om te controleren of de Windows Guest Agent-service is geïnstalleerd.
1. Als de Windows Azure Guest-agent wordt weer gegeven in **Program ma's en onderdelen**, verwijdert u de Windows Azure-gast agent.
1. Down load en installeer de [nieuwste versie van de agent-MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). U hebt beheerders rechten nodig om de installatie te volt ooien.
1. Controleer of de Windows Azure Guest Agent-service wordt weer gegeven in Services.
1. Start de beveiligings taak opnieuw.

Controleer ook of [Microsoft .NET 4,5 is geïnstalleerd](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) in de VM. U hebt .NET 4,5 nodig voor de VM-agent om te communiceren met de service.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>De agent die is geïnstalleerd in de virtuele machine is verouderd (voor Linux-Vm's)

#### <a name="solution"></a>Oplossing
De meeste fout-en extensie-gerelateerde storingen voor Linux-Vm's worden veroorzaakt door problemen die van invloed zijn op een verouderde VM-agent. Volg deze algemene richt lijnen om dit probleem op te lossen:

1. Volg de instructies voor [het bijwerken van de Linux VM-agent](../virtual-machines/extensions/update-linux-agent.md).

   > [!NOTE]
   > We *raden u ten zeerste* aan de agent alleen bij te werken via een distributie opslagplaats. Het is niet raadzaam de agent code rechtstreeks vanuit GitHub te downloaden en bij te werken. Als de meest recente agent voor uw distributie niet beschikbaar is, neemt u contact op met de distributie ondersteuning voor instructies over het installeren ervan. Als u de meest recente agent wilt controleren, gaat u naar de pagina van de [Windows Azure Linux-agent](https://github.com/Azure/WALinuxAgent/releases) in de GitHub-opslag plaats.

1. Zorg ervoor dat de Azure-agent op de virtuele machine wordt uitgevoerd door de volgende opdracht uit te voeren: `ps -e`

   Als het proces niet wordt uitgevoerd, start u het opnieuw met behulp van de volgende opdrachten:

   - Voor Ubuntu: `service walinuxagent start`
   - Voor andere distributies: `service waagent start`

1. [Configureer de agent voor automatisch opnieuw opstarten](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
1. Schakel de beveiliging van de virtuele machine in.

### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>De uitbrei ding van de Site Recovery kan niet worden bijgewerkt of geladen

De status van de uitbrei ding wordt weer gegeven als ' leeg ', ' geen loopvlak ' of ' overgang '.

#### <a name="solution"></a>Oplossing

Verwijder de extensie en start de bewerking opnieuw.

De uitbrei ding verwijderen:

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de virtuele machine waarop een back-up is mislukt.
1. Selecteer **Instellingen**.
1. Selecteer **Extensies**.
1. Selecteer **site Recovery extensie**.
1. Selecteer **Verwijderen**.

Als de VMSnapshot-extensie niet wordt weer gegeven in de Azure Portal, moet u voor de virtuele Linux-machine [de Azure Linux-agent bijwerken](../virtual-machines/extensions/update-linux-agent.md). Voer vervolgens de beveiliging uit.

Wanneer u deze stappen hebt voltooid, wordt de extensie tijdens de beveiliging opnieuw geïnstalleerd.
