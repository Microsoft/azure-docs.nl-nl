---
title: Onderhoudsmeldingen
description: Overzicht van onderhouds meldingen voor virtuele machines die worden uitgevoerd in Azure.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 8/12/2020
ms.author: shants
ms.openlocfilehash: 92cb780a80f1010fd1c2f5d19fe616e0285de73b
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564523"
---
# <a name="handling-planned-maintenance-notifications"></a>Geplande onderhouds meldingen verwerken

Van tijd tot tijd voert Azure updates uit om de betrouwbaarheid, prestaties en veiligheid te verbeteren van de host-infrastructuur voor virtuele machines. Updates zijn wijzigingen zoals het patchen van de hosting omgeving of het upgraden en uit bedrijf nemen van hardware. Een meerderheid van deze updates wordt voltooid zonder enige invloed op de gehoste virtuele machines. Er zijn echter gevallen waarin updates een impact hebben:

- Als voor het onderhoud niet opnieuw moet worden opgestart, wordt de virtuele machine gedurende enkele seconden onderbroken tijdens het bijwerken van de host. Dit type onderhouds bewerkingen worden een fout domein toegepast op het fout domein. De voortgang wordt gestopt als er waarschuwings status signalen worden ontvangen.

- Als de computer opnieuw moet worden opgestart, wordt een melding weer gegeven wanneer het onderhoud is gepland. U krijgt een tijd venster van ongeveer 35 dagen waarin u de onderhouds werkzaamheden zelf kunt starten.


Gepland onderhoud waarvoor opnieuw opstarten is vereist, wordt gepland in golven. Elke golf heeft een ander bereik (regio's).

- Een golf begint met een melding aan klanten. Standaard wordt de melding verzonden naar de abonnements beheerder en mede beheerders. U kunt meer ontvangers en bericht opties toevoegen, zoals e-mail, SMS en webhooks, met behulp van [waarschuwingen voor activiteiten logboeken](../service-health/alerts-activity-log-service-notifications-portal.md).  
- Zodra een melding wordt weer gegeven, wordt er een *self-service venster* beschikbaar gemaakt. Tijdens dit venster kunt u een query uitvoeren op de virtuele machines die worden beïnvloed en onderhoud starten op basis van uw eigen plannings behoeften. Het venster voor selfservice Services is doorgaans ongeveer 35 dagen.
- Na het self-service venster begint een *gepland onderhouds venster* . Op een bepaald moment tijdens dit venster plant Azure en past het vereiste onderhoud toe op uw virtuele machine. 

Het doel van het hebben van twee vensters is dat u voldoende tijd hebt om onderhoud te starten en uw virtuele machine opnieuw op te opstarten, terwijl u zeker weet wanneer Azure automatisch onderhoud start.


U kunt de Azure Portal, Power shell, REST API en CLI gebruiken om een query uit te zoeken naar de onderhouds Vensters voor uw virtuele machines en het onderhoud van self-service te starten.

 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>Moet u onderhoud starten met tijdens het selfservice venster?  

De volgende richt lijnen kunnen u helpen beslissen of u deze mogelijkheid wilt gebruiken en op uw eigen moment onderhoud kunt starten. 

> [!NOTE] 
> Het onderhoud van self-service is mogelijk niet beschikbaar voor al uw virtuele machines. Als u wilt bepalen of proactieve herimplementatie beschikbaar is voor uw virtuele machine, zoekt u naar de **Start nu** in de onderhouds status. Self-service onderhoud is momenteel niet beschikbaar voor Cloud Services (Web/Worker-rol) en Service Fabric.


Self-service onderhoud wordt niet aanbevolen voor implementaties met behulp van **beschikbaarheids sets**. Beschikbaarheids sets worden al slechts één update domein per keer bijgewerkt. 

- Laat Azure het onderhoud activeren. Voor onderhoud waarvoor opnieuw opstarten is vereist, wordt onderhoud uitgevoerd op update domein. De update domeinen ontvangen niet noodzakelijkerwijs het onderhoud opeenvolgend en er is een onderbreking van 30 minuten tussen de update domeinen. 
- Als een tijdelijk verlies van enige capaciteit (1 update domein) een probleem is, kunt u instanties toevoegen tijdens de onderhouds periode. 
- Voor onderhoud waarbij niet opnieuw moet worden opgestart, worden updates toegepast op het niveau van het fout domein. 

Gebruik self-service onderhoud **niet** in de volgende scenario's: 
- Als u uw virtuele machines regel matig afsluit, met behulp van DevTest Labs, met behulp van automatisch afsluiten of volgens een planning, kan dit de onderhouds status herstellen en daardoor extra downtime veroorzaken.
- Op korte en gelaagde Vm's die u weet, worden vóór het einde van de onderhouds Golf verwijderd. 
- Voor werk belastingen met een grote status die is opgeslagen in de lokale (tijdelijke) schijf die bij het bijwerken wordt bewaard. 
- Wanneer u de grootte van uw virtuele machine regel matig wijzigt, kan dit de onderhouds status herstellen. 
- Als u geplande gebeurtenissen hebt aangenomen die proactieve failover of het correct afsluiten van uw werk belasting hebben uitgevoerd, wordt 15 minuten voor het afsluiten van het onderhoud gestart

**Gebruik** self-service onderhoud als u van plan bent om uw virtuele machine tijdens de geplande onderhouds fase zonder onderbreking uit te voeren en geen van de hierboven vermelde prestatie meters van toepassing zijn. 

Het is aan te raden selfservice onderhoud in de volgende gevallen te gebruiken:
- U moet een exact onderhouds venster aan uw beheer of eind klant communiceren. 
- U moet het onderhoud op een bepaalde datum volt ooien. 
- U moet de volg orde van onderhoud, bijvoorbeeld een toepassing met meerdere lagen, beheren om veilig herstel te garanderen.
- Er is meer dan 30 minuten voor de herstel tijd van de VM vereist tussen twee update domeinen (UDs). Als u de tijd tussen update domeinen wilt bepalen, moet u onderhoud activeren op uw virtuele machines met één update domein (UD) tegelijk.


## <a name="faq"></a>Veelgestelde vragen


**V: Waarom moet u nu mijn virtuele machines opnieuw opstarten?**

**A:** Hoewel het meren deel van updates en upgrades voor het Azure-platform geen invloed hebben op de beschik baarheid van de virtuele machine, zijn er gevallen waarin we niet kunnen voor komen dat virtuele machines die worden gehost in azure niet worden gestart. We hebben verschillende wijzigingen verzameld die ons nodig hebben om de servers opnieuw op te starten. Dit leidt ertoe dat virtuele machines opnieuw worden opgestart.

**V: als ik de aanbevelingen voor hoge Beschik baarheid volg met een Beschikbaarheidsset, ben ik dan veilig?**

**A:** Virtuele machines die zijn geïmplementeerd in een beschikbaarheidsset of virtuele-machine schaal sets hebben het begrip update domeinen (UD). Bij het uitvoeren van onderhoud voldoet Azure aan de UD-beperking en worden virtuele machines van verschillende UD niet opnieuw opgestart (binnen dezelfde beschikbaarheidsset).  Azure wacht nog ten minste 30 minuten voordat de volgende groep virtuele machines wordt verplaatst. 

Zie [Beschik baarheid voor virtuele machines in azure](availability.md)voor meer informatie over maximale Beschik baarheid.

**V: Hoe kan ik ontvang een melding over gepland onderhoud?**

**A:** Een geplande onderhouds Golf wordt gestart door een planning in te stellen voor een of meer Azure-regio's. Binnenkort nadat wordt een e-mail melding verzonden naar de abonnements beheerders, mede beheerders, eigen aren en mede werkers (één e-mail per abonnement). Aanvullende kanalen en ontvangers voor deze melding kunnen worden geconfigureerd met behulp van waarschuwingen voor activiteiten Logboeken. Als u een virtuele machine implementeert in een regio waar gepland onderhoud al is gepland, wordt de melding niet weer gegeven, maar moet u de onderhouds status van de VM controleren.

**V: Ik zie geen indicatie van gepland onderhoud in de portal, Power shell of CLI. Wat is er aan de hand?**

**A:** Informatie met betrekking tot gepland onderhoud is alleen beschikbaar tijdens een geplande onderhouds Golf voor de virtuele machines waarop deze worden beïnvloed. Met andere woorden, als u geen gegevens ziet, kan het zijn dat de onderhouds Golf al is voltooid (of niet is gestart) of dat uw virtuele machine al wordt gehost op een bijgewerkte server.

**V: is er een manier om precies te weten wanneer mijn virtuele machine wordt beïnvloed?**

**A:** Wanneer u het schema instelt, definiëren we een tijd venster van enkele dagen. De exacte volg orde van servers (en Vm's) in dit venster is echter onbekend. Klanten die de exacte tijd willen weten voor hun virtuele machines, kunnen [geplande gebeurtenissen](./linux/scheduled-events.md) en query's vanaf de virtuele machine gebruiken en een melding van 15 minuten ontvangen voordat een VM opnieuw wordt opgestart.

**V: hoe lang duurt het om mijn virtuele machine opnieuw op te starten?**

**A:**  Afhankelijk van de grootte van uw virtuele machine kan het enkele minuten duren voordat de computer opnieuw wordt opgestart. Tijdens het opnieuw opstarten van Azure in het venster gepland onderhoud wordt het systeem doorgaans ongeveer 25 minuten uitgevoerd. Houd er rekening mee dat als u Cloud Services (Web/Worker), Virtual Machine Scale Sets of beschikbaarheids sets gebruikt, u 30 minuten krijgt tussen elke groep Vm's (UD) tijdens het geplande onderhouds venster.

**V: wat is de ervaring in het geval van Virtual Machine Scale Sets?**

**A:** Gepland onderhoud is nu beschikbaar voor Virtual Machine Scale Sets. Zie [gepland onderhoud voor virtuele-machine schaal sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md) document voor instructies over het initiëren van self-service onderhoud.

**V: wat is de ervaring in het geval van Cloud Services (Web/Worker) en Service Fabric?**

**A:** Hoewel deze platformen worden beïnvloed door gepland onderhoud, worden klanten die gebruikmaken van deze platformen als veilig beschouwd, omdat alleen Vm's in één upgrade domein (UD) op een gegeven moment worden beïnvloed. Self-service onderhoud is momenteel niet beschikbaar voor Cloud Services (Web/Worker-rol) en Service Fabric.

**V: Ik zie geen onderhouds informatie op mijn Vm's. Wat is er verkeerd gegaan?**

**A:** Er zijn verschillende redenen waarom u geen onderhouds informatie ziet over uw Vm's:
1.  U gebruikt een abonnement dat is gemarkeerd als interne micro soft.
2.  Uw Vm's zijn niet gepland voor onderhoud. Het kan zijn dat de onderhouds Golf is beëindigd, geannuleerd of gewijzigd, zodat uw Vm's hierdoor niet meer worden beïnvloed.
3. U hebt de toewijzing van de virtuele machine ongedaan gemaakt en vervolgens gestart. Dit kan ertoe leiden dat de virtuele machine wordt verplaatst naar een locatie waarvoor geen geplande onderhouds Golf is gepland. De virtuele machine geeft dus geen onderhouds informatie meer weer. 
4.  U beschikt niet over de kolom **onderhoud** die is toegevoegd aan de lijst weergave van de virtuele machine. Hoewel we deze kolom aan de standaard weergave hebben toegevoegd, moeten klanten die zijn geconfigureerd om niet-standaard kolommen te zien, de kolom **onderhoud** hand matig toevoegen aan hun VM-lijst weergave.

**V: mijn VM is voor de tweede keer gepland voor onderhoud. Waarom?**

**A:** Er zijn verschillende situaties waarin u uw VM kunt zien die is gepland voor onderhoud nadat u de onderhouds-en-implementatie al hebt voltooid:
1.  De onderhouds Golf is geannuleerd en opnieuw gestart met een andere nettolading. Het kan zijn dat er een beschadigde Payload is gedetecteerd en dat er alleen een extra Payload moet worden geïmplementeerd.
2.  De virtuele machine is op een ander knoop punt *retoucheerd* vanwege een hardwarefout.
3.  U hebt ervoor gekozen om te stoppen (toewijzing ongedaan te maken) en de virtuele machine opnieuw op te starten.
4.  U hebt **automatisch afsluiten** ingeschakeld voor de virtuele machine.



## <a name="next-steps"></a>Volgende stappen

U kunt gepland onderhoud verwerken met behulp van de [Azure cli](maintenance-notifications-cli.md), [Azure PowerShell](maintenance-notifications-powershell.md) of [Portal](maintenance-notifications-portal.md).
