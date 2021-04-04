---
title: Azure Site Recovery bewaken | Microsoft Docs
description: Azure Site Recovery replicatie problemen en-bewerkingen controleren en problemen oplossen met behulp van de portal
author: raynew
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: raynew
ms.openlocfilehash: d441284b265ab11dd5ece42ec3737e455d662435
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023564"
---
# <a name="monitor-site-recovery"></a>Site Recovery bewaken

In dit artikel leest u hoe u Azure [site Recovery](site-recovery-overview.md)bewaakt met behulp van site Recovery ingebouwde bewaking.  U kunt het volgende controleren:

- De status en status van computers die worden gerepliceerd door Site Recovery
- De status van de testfailover van machines testen.
- Problemen en fouten die van invloed zijn op de configuratie en replicatie.
- Infrastructuur onderdelen zoals on-premises servers.


## <a name="before-you-start"></a>Voordat u begint

U kunt [algemene controle vragen](monitoring-common-questions.md) bekijken voordat u begint.

## <a name="monitor-in-the-dashboard"></a>Bewaken in het dash board

1. Klik in de kluis op **overzicht**. Het Recovery Services-dash board consolideert alle bewakings gegevens voor de kluis op één locatie. Er zijn pagina's voor zowel Site Recovery als de Azure Backup-Service, en u kunt hiertussen scha kelen.

    ![Site Recovery dash board](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2. In het dash board kunt u inzoomen op verschillende gebieden. 

    ![Scherm afbeelding waarin de gebieden op het dash board worden weer gegeven waar u kunt inzoomen.](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png).

3. Klik in **gerepliceerde items** op **alles weer geven** om alle servers in de kluis weer te geven.
4. Klik op de status Details in elke sectie om in te zoomen.
5. In de **weer gave infra structuur** sorteert u bewakings gegevens op het type machines dat u repliceert.

## <a name="monitor-replicated-items"></a>Gerepliceerde items bewaken

In **gerepliceerde items** controleert u de status van alle computers in de kluis waarvoor replicatie is ingeschakeld.

**Status** | **Details**
--- | ---
In orde | De replicatie wordt normaal uitgevoerd. Er zijn geen fout-of waarschuwings symptomen gedetecteerd.
Waarschuwing | Er zijn een of meer waarschuwings symptomen gedetecteerd die van invloed kunnen zijn op replicatie.
Kritiek | Er zijn een of meer kritieke symptomen van de replicatie fout gedetecteerd.<br/><br/> Deze fout symptomen zijn doorgaans indica toren die zijn achtergebleven, of die niet zo snel als de snelheid van gegevens wijzigingen worden uitgevoerd.
Niet van toepassing | Servers die momenteel niet naar verwachting repliceren. Dit kan ook betrekking hebben op machines waarvoor een failover is uitgevoerd.

## <a name="monitor-test-failovers"></a>Testfailover controleren

Controleer de failover-status voor computers in de kluis in de **failover-test**.

- We raden u aan om ten minste één keer per zes maanden een testfailover uit te voeren op gerepliceerde machines. Het is een manier om te controleren of failover werkt zoals verwacht, zonder uw productie omgeving te onderbreken. 
- Een testfailover wordt alleen als geslaagd beschouwd nadat de failover en het opruimen na de failover zijn voltooid.

**Status** | **Details**
--- | ---
Aanbevolen test | Computers die geen testfailover hebben gehad sinds de beveiliging is ingeschakeld.
Is uitgevoerd | Machines met een of meer geslaagde testfailover.
Niet van toepassing | Machines die momenteel niet in aanmerking komen voor een testfailover. Computers waarvoor failover is uitgevoerd, hebben bijvoorbeeld een initiële replicatie/testfailover/failover.

## <a name="monitor-configuration-issues"></a>Configuratie problemen bewaken

In **configuratie problemen** bewaakt u eventuele problemen die van invloed kunnen zijn op de failover van de functie.

- Configuratie problemen (met uitzonde ring van de beschik baarheid van software-updates) worden gedetecteerd door een periodieke validatie bewerking die standaard elke 12 uur wordt uitgevoerd. U kunt afdwingen dat de validatie bewerking onmiddellijk wordt uitgevoerd door te klikken op het pictogram Vernieuwen naast de sectie **configuratie problemen** .
- Klik op de koppelingen voor meer informatie. Klik op **aandacht vereist** in de kolom **doel configuraties** voor problemen die invloed hebben op specifieke computers. Details zijn onder andere aanbevelingen voor herstel.

**Status** | **Details**
--- | ---
Ontbrekende configuraties | Er ontbreekt een vereiste instelling, zoals een herstel netwerk of een resource groep.
Ontbrekende resources | Een opgegeven resource is niet gevonden of is niet beschikbaar in het abonnement. De resource is bijvoorbeeld verwijderd of gemigreerd. Bewaakte resources bevatten de doel resource groep, het doel-VNet/subnet, het logboek/het doel opslag account, de beschik baarheid van doelen, het doel-IP-adres.
Abonnements quotum |  Het resource quotum van het beschik bare abonnement wordt vergeleken met het saldo dat nodig is voor het uitvoeren van een failover voor alle computers in de kluis.<br/><br/> Als er onvoldoende bronnen beschikbaar zijn, wordt er een ontoereikend quotum voor de quota gerapporteerd.<br/><br/> Quota worden bewaakt voor het aantal virtuele machines van de VM, de kern telling van de VM-serie, de netwerk interface kaart (NIC).
Software-updates | De beschik baarheid van nieuwe software-updates en informatie over het verlopen van software versies.


## <a name="monitor-errors"></a>Fouten bewaken

Controleer in **fout samenvatting** momenteel actieve fout symptomen die van invloed kunnen zijn op de replicatie van servers in de kluis en controleer het aantal betrokken machines.

- De fouten die van invloed zijn op de onderdelen van on-premises infra structuur worden weer gegeven aan het begin van de sectie. Bijvoorbeeld een niet-ontvangst van een heartbeat van de Azure Site Recovery provider op de on-premises configuratie server of Hyper-V-host.
- Vervolgens worden de problemen met de replicatie fout weer gegeven die van invloed zijn op gerepliceerde servers.
- De tabel vermeldingen worden gesorteerd door de volg orde van de ernst van de fout te verlagen en vervolgens door de volg orde van het aantal betrokken machines te verminderen.
- Het aantal betrokken servers is een handige manier om te begrijpen of één onderliggend probleem van invloed kan zijn op meerdere computers. Een netwerk fout kan bijvoorbeeld van invloed zijn op alle computers die repliceren naar Azure. 
- Er kunnen meerdere replicatie fouten optreden op één server. In dit geval telt elk fout symptoom dat de server in de lijst met betrokken servers bevindt. Nadat het probleem is opgelost, worden de replicatie parameters verbeterd en wordt de fout van de computer gewist.

## <a name="monitor-the-infrastructure"></a>Controleer de infra structuur.

Bewaak in **infrastructuur weergave** de infrastructuur onderdelen die bij de replicatie betrokken zijn, en connectiviteits status tussen servers en de Azure-Services.

- Een groene lijn geeft aan dat de verbinding in orde is.
- Een rode lijn met het pictogram overlay-fout geeft het bestaan van een of meer fout symptomen die van invloed zijn op de connectiviteit.
-  Beweeg de muis aanwijzer over het fout pictogram om de fout en het aantal betrokken entiteiten weer te geven. Klik op het pictogram voor een gefilterde lijst met betrokken entiteiten.

    ![Site Recovery infrastructuur weergave (kluis)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

### <a name="tips-for-monitoring-the-infrastructure"></a>Tips voor het bewaken van de infra structuur

- Zorg ervoor dat de onderdelen van de on-premises infra structuur (configuratie server, proces servers, VMM-servers, Hyper-V-hosts, VMware-machines) de nieuwste versies van de Site Recovery provider en/of agents uitvoeren.
- Als u alle functies in de infrastructuur weergave wilt gebruiken, moet u [Update pakket 22](https://support.microsoft.com/help/4072852) voor deze onderdelen uitvoeren.
- Als u de infrastructuur weergave wilt gebruiken, selecteert u het juiste replicatie scenario in uw omgeving. U kunt inzoomen op de weer gave voor meer informatie. In de volgende tabel ziet u welke scenario's worden weer gegeven.

    **Scenario** | **Status**  | **Weer gave beschikbaar?**
    --- |--- | ---
    **Replicatie tussen on-premises sites** | Alle staten | No 
    **Azure-VM-replicatie tussen Azure-regio's**  | Replicatie ingeschakeld/initiële replicatie wordt uitgevoerd | Yes
    **Azure-VM-replicatie tussen Azure-regio's** | Failover/failback is voltooid | No   
    **Replicatie van VMware naar Azure** | Replicatie ingeschakeld/initiële replicatie wordt uitgevoerd | Yes     
    **Replicatie van VMware naar Azure** | Failover/fail-back | No      
    **Hyper-V-replicatie naar Azure** | Failover/fail-back | No

- Als u de weer gave van de infra structuur voor één replicerende computer wilt zien, klikt u in het menu kluis op **gerepliceerde items** en selecteert u een server.  




## <a name="monitor-recovery-plans"></a>Herstel plannen controleren

Bewaak in **herstel plannen** het aantal plannen, maak nieuwe plannen en wijzig bestaande abonnementen.  

## <a name="monitor-jobs"></a>Taken controleren

Bewaak de status van Site Recovery bewerkingen in **taken**.

- De meeste bewerkingen in Azure Site Recovery worden asynchroon uitgevoerd, waarbij een tracerings taak wordt gemaakt en gebruikt om de voortgang van de bewerking bij te houden. 
- Het taak object bevat alle informatie die u nodig hebt om de status en de voortgang van de bewerking bij te houden. 

Bewaak taken als volgt:

1. In de sectie > **taken** in het dash board ziet u een overzicht van de taken die zijn voltooid, worden uitgevoerd of in de afgelopen 24 uur op invoer wachten. U kunt op een wille keurige status klikken voor meer informatie over de relevante taken.
2. Klik op **alles weer geven** om alle taken in de afgelopen 24 uur weer te geven.

    > [!NOTE]
    > U kunt taak gegevens ook openen vanuit het menu kluis > **site Recovery taken**. 

2. In de lijst **site Recovery taken** wordt een lijst met taken weer gegeven. In het bovenste menu kunt u fout gegevens voor een specifieke taken ophalen, de taken lijst filteren op basis van specifieke criteria en de geselecteerde taak details exporteren naar Excel.
3. U kunt inzoomen op een taak door erop te klikken. 

## <a name="monitor-virtual-machines"></a>Virtuele machines bewaken

Haal in **gerepliceerde items** een lijst met gerepliceerde computers op. 
    ![Lijst weergave van gerepliceerde items Site Recovery](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. U kunt gegevens weer geven en filteren. In het menu Actie bovenaan kunt u acties uitvoeren voor een bepaalde machine, waaronder het uitvoeren van een testfailover of het weer geven van specifieke fouten.
3. Klik op **kolommen** om aanvullende kolommen weer te geven, bijvoorbeeld om RPO, doel configuratie problemen en replicatie fouten weer te geven.
4. Klik op **filter** om informatie weer te geven op basis van specifieke para meters, zoals replicatie status, of een bepaald replicatie beleid.
5. Klik met de rechter muisknop op een machine om bewerkingen zoals een testfailover te initiëren of om specifieke fout gegevens weer te geven die eraan zijn gekoppeld.
6. Klik op een machine om in te zoomen op meer informatie. Details zijn onder andere:
   - **Replicatie-informatie**: huidige status en status van de computer.
   - **RPO** (Recovery Point Objective): huidige RPO voor de virtuele machine en het tijdstip waarop de RPO het laatst is berekend.
   - **Herstel punten**: meest recente beschik bare herstel punten voor de machine.
   - **Failover-gereedheid**: Hiermee geeft u aan of er een testfailover voor de machine wordt uitgevoerd, de agent versie die op de computer wordt uitgevoerd (voor machines met de Mobility-service) en eventuele configuratie problemen.
   - **Fouten**: de lijst met de replicatie fout symptomen die momenteel op de computer zijn waargenomen en mogelijke oorzaken/acties.
   - **Gebeurtenissen**: een chronologische lijst met recente gebeurtenissen die van invloed zijn op de computer. Fout Details worden weer gegeven met de momenteel waarneem bare fout symptomen, terwijl gebeurtenissen een historisch overzicht zijn van problemen die van invloed zijn op de computer.
   - **Infrastructuur weergave**: de status van de infra structuur voor het scenario wordt weer gegeven wanneer machines naar Azure worden gerepliceerd.

     ![Details van gerepliceerde items Site Recovery/overzicht](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

## <a name="subscribe-to-email-notifications"></a>Abonneren op e-mail meldingen

U kunt zich abonneren voor het ontvangen van e-mail meldingen voor deze kritieke gebeurtenissen:
 
- Kritieke status voor de gerepliceerde machine.
- Geen verbinding tussen de on-premises infrastructuur onderdelen en de Site Recovery service. De connectiviteit tussen Site Recovery en on-premises servers die zijn geregistreerd in een kluis, wordt gedetecteerd met behulp van een heartbeat-mechanisme.
- Failover-fouten.

Abonneer u als volgt:

Klik in de sectie kluis > **bewaking** op **site Recovery gebeurtenissen**.
1. Klik op **E-mailmeldingen**.
1. Schakel in **e-mail melding** meldingen in en geef aan wie u wilt verzenden. U kunt verzenden naar alle abonnements beheerders die meldingen ontvangen en optioneel specifieke e-mail adressen.

    ![E-mailmeldingen](./media/site-recovery-monitor-and-troubleshoot/email.png)

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over](monitor-log-analytics.md) het bewaken van Site Recovery met Azure monitor.
