---
title: Zone inschakelen voor herstel na nood gevallen voor Azure Virtual Machines
description: In dit artikel wordt beschreven hoe en hoe u zone kunt gebruiken om herstel na nood gevallen voor virtuele Azure-machines uit te zones.
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 04/28/2020
ms.author: sideeksh
ms.openlocfilehash: 21bdbecdbba31eae112053bf81411781245cc170
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97678678"
---
# <a name="enable-azure-vm-disaster-recovery-between-availability-zones"></a>Herstel na nood geval voor Azure VM tussen beschikbaarheids zones inschakelen

In dit artikel wordt beschreven hoe u virtuele Azure-machines vanuit de ene beschikbaarheids zone naar een andere repliceert in dezelfde Azure-regio.

>[!NOTE]
>
>- Ondersteuning voor zone-naar-zone herstel na nood gevallen is momenteel beperkt tot vijf regio's: Zuidoost-Azië, UK-zuid, Japan-Oost, Australië-oost en Europa-west.  
>- Site Recovery verplaatst of slaat klant gegevens niet uit de regio waarin deze wordt geïmplementeerd wanneer de klant gebruikmaakt van zone om herstel na nood gevallen van een zone te maken. Klanten kunnen een Recovery Services kluis uit een andere regio selecteren als ze dat doen. De Recovery Services kluis bevat meta gegevens, maar geen werkelijke klant gegevens.

Site Recovery-service draagt bij aan uw strategie voor bedrijfs continuïteit en herstel na nood gevallen door uw zakelijke apps tijdens geplande en ongeplande uitval te laten werken. Het is de aanbevolen optie voor nood herstel om ervoor te zorgen dat uw toepassingen actief blijven als er regionale storingen optreden.

Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Elke zone heeft een of meer data centers. 

Als u Vm's wilt verplaatsen naar een beschikbaarheids zone in een andere regio, [raadpleegt u dit artikel](../resource-mover/move-region-availability-zone.md).

## <a name="using-availability-zones-for-disaster-recovery"></a>Beschikbaarheidszones gebruiken voor herstel na nood gevallen 

Normaal gesp roken worden Beschikbaarheidszones gebruikt voor het implementeren van Vm's in een configuratie met hoge Beschik baarheid. Ze zijn mogelijk te dicht bij elkaar om als nood herstel oplossing in het geval van een natuur ramp te dienen.

In sommige gevallen kan Beschikbaarheidszones echter worden gebruikt voor herstel na nood gevallen:

- Veel klanten die een strategie voor het plannen van een metro-nood herstel hadden en toepassingen op locatie hosten, kunnen deze strategie ook nabootsen nadat ze toepassingen naar Azure hebben gemigreerd. Deze klanten erkennen dat de strategie voor het mogelijk is om een metro nood herstel te gebruiken in het geval van een grootschalige fysieke nood situatie en dit risico te accepteren. Voor dergelijke klanten kan een zone-naar-zone nood herstel worden gebruikt als nood herstel optie.

- Veel andere klanten hebben gecompliceerde netwerk infrastructuur en willen deze niet opnieuw maken in een secundaire regio vanwege de bijbehorende kosten en complexiteit. Zone-naar-zone herstel na nood gevallen vermindert de complexiteit omdat deze gebruikmaakt van redundante netwerk concepten over Beschikbaarheidszones een veel eenvoudiger configuratie maken. Deze klanten geven de voor keur aan het gemak en kunnen ook Beschikbaarheidszones gebruiken voor herstel na nood gevallen.

- In sommige regio's die niet beschikken over een gepaard gebied binnen dezelfde rechts bevoegdheid (bijvoorbeeld Zuidoost-Azië), kan de zone naar zone herstel na nood gevallen worden uitgevoerd als de niet-conforme oplossing voor herstel na een hoger niveau, omdat uw toepassingen en gegevens niet over nationale grenzen worden verplaatst. 

- Voor een zone-naar-zone-herstel na nood geval wordt de replicatie van gegevens op korte afstanden in vergelijking met Azure tot Azure-herstel na nood gevallen gereduceerd en daarom ziet u mogelijk een lagere latentie en dus lagere RPO.

Hoewel dit een hoge voor deel is, is het mogelijk dat zone-herstel na nood gevallen in het geval van een natuur ramp in de hele regio de flexibiliteits vereisten beperkt.

## <a name="networking-for-zone-to-zone-disaster-recovery"></a>Netwerken voor nood herstel van zone naar zone

Zoals hierboven vermeld, vermindert zone-to-zone-herstel na nood gevallen de complexiteit omdat deze gebruikmaakt van redundante netwerk concepten over Beschikbaarheidszones een veel eenvoudiger configuratie maken. Het gedrag van netwerk onderdelen in het scenario voor herstel na nood geval in zones wordt hieronder beschreven: 

- Virtual Network: u kunt hetzelfde virtuele netwerk gebruiken als het bron netwerk voor daad werkelijke failovers. Gebruik een ander virtueel netwerk voor het virtuele bron netwerk voor testfailover.

- Subnet: de failover naar hetzelfde subnet wordt ondersteund.

- Privé-IP-adres: als u vaste IP-adressen gebruikt, kunt u dezelfde Ip's in de doel zone gebruiken als u ervoor kiest om ze op een zodanige manier te configureren.

- Versnelde netwerken: net als bij Azure voor herstel na nood gevallen kunt u versneld netwerken inschakelen als de VM-SKU dit ondersteunt.

- Openbaar IP-adres: u kunt een eerder gemaakt standaard openbaar IP-adres in dezelfde regio koppelen aan de doel-VM. Algemene open bare IP-adressen bieden geen ondersteuning voor scenario's met een beschikbaarheids zone.

- Load Balancer: standaard load balancer is een regionale resource en daarom kan de doel-VM worden gekoppeld aan de back-end-groep van hetzelfde load balancer. Er is geen nieuwe load balancer vereist.

- Netwerk beveiligings groep: u kunt dezelfde netwerk beveiligings groepen gebruiken die worden toegepast op de bron-VM.

## <a name="pre-requisites"></a>Vereisten

Voordat u de zone implementeert naar zone herstel na nood gevallen voor uw virtuele machines, is het belang rijk om ervoor te zorgen dat andere functies die zijn ingeschakeld op de virtuele machine, compatibel zijn met zones voor herstel na nood gevallen.

|Functie  | Ondersteunings verklaring  |
|---------|---------|
|Klassieke VM's   |     Niet ondersteund    |
|ARM-Vm's    |    Ondersteund    |
|Azure Disk Encryption v1 (dubbele Pass, met Azure Active Directory (Azure AD))     |     Ondersteund   |
|Azure Disk Encryption v2 (eenmalige Pass, zonder Azure AD)    |    Ondersteund    |
|Onbeheerde schijven    |    Niet ondersteund    |
|Managed Disks    |    Ondersteund    |
|Door klant beheerde sleutels    |    Ondersteund    |
|Nabijheidsplaatsingsgroepen    |    Ondersteund    |
|Interoperabiliteit van back-ups    |    Back-up en herstel op bestands niveau worden ondersteund. Back-up en herstel op schijf-en VM-niveau en niet worden ondersteund.    |
|Hot toevoegen/verwijderen    |    Schijven kunnen worden toegevoegd nadat zone replicatie is ingeschakeld. Het verwijderen van schijven nadat zone replicatie is ingeschakeld, wordt niet ondersteund.    | 

## <a name="set-up-site-recovery-zone-to-zone-disaster-recovery"></a>Herstel na nood geval voor zone instellen in Site Recovery zone

### <a name="log-in"></a>Aanmelden

Meld u aan bij Azure Portal.

### <a name="enable-replication-for-the-zonal-azure-virtual-machine"></a>Replicatie inschakelen voor de virtuele zonegebonden-machine van Azure

1. Selecteer virtuele machines in het menu Azure Portal of zoek en selecteer virtuele machines op een wille keurige pagina. Selecteer de virtuele machine die u wilt repliceren. Voor een zone-naar-zone-herstel na nood gevallen moet deze virtuele machine zich al in een beschikbaarheids zone bevinden.

2. Selecteer in Bewerkingen de optie Herstel na noodgeval.

3. Zoals hieronder wordt weer gegeven, selecteert u op het tabblad basis beginselen ja voor herstel na nood geval tussen Beschikbaarheidszones?

    ![Pagina basis instellingen](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-basic-settings-blade.png)

4. Als u alle standaard waarden accepteert, klikt u op ' controleren + replicatie starten ', gevolgd door replicatie starten.

5. Als u wijzigingen in de replicatie-instellingen wilt aanbrengen, klikt u op volgende: geavanceerde instellingen.

6. Wijzig de instellingen van de standaard instelling, indien van toepassing. Voor gebruikers van Azure tot Azure-herstel na nood gevallen lijkt deze pagina mogelijk bekend te zijn. Meer informatie over de opties die op deze Blade worden weer gegeven, vindt u [hier](./azure-to-azure-tutorial-enable-replication.md)

    ![Pagina Geavanceerde instellingen](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-advanced-settings-blade.png)

7. Klik op volgende: controleren + replicatie starten en vervolgens replicatie starten.

## <a name="faqs"></a>Veelgestelde vragen

**1. Hoe werkt de prijs voor een zone voor een nood herstel van zones?**
De prijzen voor zone-naar-zone herstel na nood gevallen zijn gelijk aan de prijs van Azure tot Azure nood herstel. U vindt [hier](https://azure.microsoft.com/pricing/details/site-recovery/) meer informatie over de pagina met [prijzen.](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) Houd er rekening mee dat de kosten voor uitgaand verkeer die u in de zone zou zien, voor een nood herstel van de zone lager zijn dan de regio naar regio herstel na nood gevallen.

**2. Wat is de SLA voor RTO en RPO?**
De SLA voor RTO is hetzelfde als die voor Site Recovery totaal. We beloofen RTO tot twee uur. Er is geen SLA voor RPO gedefinieerd.

**3. is de capaciteit gegarandeerd in de secundaire zone?**
Het team van de Site Recovery-team en het Azure Capacity Management-abonnement voor voldoende infrastructuur capaciteit. Wanneer u een failover start, kunnen de teams er ook voor zorgen dat VM-exemplaren die worden beveiligd door Site Recovery worden geïmplementeerd in de doel zone.

**4. welke besturings systemen worden ondersteund?**
Zone-naar-zone herstel na nood gevallen ondersteunt dezelfde besturings systemen als Azure voor herstel na nood gevallen. Raadpleeg [hier](./azure-to-azure-support-matrix.md)de ondersteunings matrix.

**5. zijn de bron-en doel resource groepen hetzelfde?**
Nee, u moet een failover uitvoeren naar een andere resource groep.

## <a name="next-steps"></a>Volgende stappen

De stappen die moeten worden gevolgd om een nood herstel analyse uit te voeren, een failover, opnieuw beveiligen en failback zijn hetzelfde als de stappen in azure naar Azure nood herstel scenario.

Volg de stappen die [hier](./azure-to-azure-tutorial-dr-drill.md)worden beschreven om een analyse van nood herstel uit te voeren.

Volg de stappen die [hier](./azure-to-azure-tutorial-failover-failback.md)worden beschreven voor het uitvoeren van een failover en het opnieuw beveiligen van vm's in de secundaire zone.

Volg de stappen die [hier](./azure-to-azure-tutorial-failback.md)worden beschreven om de primaire zone te failback.
