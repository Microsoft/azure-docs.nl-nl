---
title: Herstel na nood geval voor een share point-app met meerdere lagen met Azure Site Recovery
description: In dit artikel wordt beschreven hoe u herstel na nood gevallen instelt voor een share point-toepassing met meerdere lagen met behulp van Azure Site Recovery mogelijkheden.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: sutalasi
ms.openlocfilehash: 08e971e52f994ec5fa5663708fa9f173daf33d80
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013748"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Herstel na nood geval instellen voor een share point-toepassing met meerdere lagen voor herstel na nood geval met behulp van Azure Site Recovery

In dit artikel wordt gedetailleerde informatie gegeven over het beveiligen van een share point-toepassing met behulp van  [Azure site Recovery](site-recovery-overview.md).


## <a name="overview"></a>Overzicht

Micro soft share point is een krachtige toepassing die u kan helpen bij het organiseren, samen werken en delen van informatie. Share point biedt intranet portals, document-en bestands beheer, samen werking, sociale netwerken, extra netten, websites, Enter prise Search en business intelligence. Het biedt ook systeem integratie, proces integratie en mogelijkheden voor het automatiseren van werk stromen. Doorgaans beschouwen organisaties IT als een laag-1-toepassing gevoelig voor downtime en gegevens verlies.

Micro soft share point biedt vandaag geen mogelijkheden voor herstel na nood gevallen. Afhankelijk van het type en de schaal van een nood geval is het gebruik van een stand-by Data Center dat u de farm kunt herstellen. Stand-by data centers zijn vereist voor scenario's waarbij lokale redundante systemen en back-ups niet kunnen worden hersteld van de storing in het primaire Data Center.

Een goede oplossing voor herstel na nood gevallen moet het mogelijk maken dat herstel plannen worden gemodelleerd rond de complexe toepassings architecturen, zoals share point. Het moet ook de mogelijkheid hebben om aangepaste stappen toe te voegen voor het afhandelen van toepassings toewijzingen tussen verschillende lagen, waardoor een failover met één klik kan worden uitgevoerd met een lager RTO in het geval van een ramp.

In dit artikel wordt gedetailleerde informatie gegeven over het beveiligen van een share point-toepassing met behulp van [Azure site Recovery](site-recovery-overview.md). In dit artikel worden aanbevolen procedures beschreven voor het repliceren van een share point-toepassing met drie lagen naar Azure, hoe u een nood herstel analyse kunt uitvoeren en hoe u de toepassing kunt failoveren naar Azure.

U kunt de onderstaande video bekijken over het herstellen van een toepassing met meerdere lagen naar Azure.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u het volgende weten:

1. [Een virtuele machine repliceren naar Azure](./vmware-azure-tutorial.md)
2. [Een herstel netwerk ontwerpen](./concepts-on-premises-to-azure-networking.md)
3. [Een testfailover naar Azure uitvoeren](site-recovery-test-failover-to-azure.md)
4. [Een failover naar Azure uitvoeren](site-recovery-failover.md)
5. [Een domein controller repliceren](site-recovery-active-directory.md)
6. [SQL server repliceren](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>Share point-architectuur

Share point kan worden geïmplementeerd op een of meer servers met behulp van gelaagde topologieën en Server functies voor het implementeren van een farm ontwerp dat voldoet aan specifieke doel stellingen en doel stellingen. Een typische share Point-server farm met hoge vraag die ondersteuning biedt voor een groot aantal gelijktijdige gebruikers en een groot aantal inhouds items service groeperingen gebruiken als onderdeel van hun schaalbaarheids strategie. Deze benadering omvat het uitvoeren van services op speciale servers, het groeperen van deze services en het schalen van de servers als groep. De volgende topologie illustreert de groepering van services en servers voor een share Point-server farm met drie lagen. Raadpleeg de documentatie van share point en product lijn architecturen voor gedetailleerde richt lijnen voor verschillende share point-topologieën. In [dit document](/SharePoint/sharepoint-server)vindt u meer informatie over de implementatie van share point 2013.



![Implementatie patroon 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Ondersteuning voor Site Recovery

Site Recovery is neutraal van toepassingen en moet werken met elke versie van share point die wordt uitgevoerd op een ondersteunde computer. Voor het maken van dit artikel zijn virtuele VMware-machines met Windows Server 2012 R2 Enter prise gebruikt. Share point 2013 Enter prise Edition en SQL Server 2014 Enter prise Edition zijn gebruikt.

### <a name="source-and-target"></a>Bron en doel

**Scenario** | **Op een secundaire site** | **Naar Azure**
--- | --- | ---
**Hyper-V** | Ja | Ja
**VMware** | Ja | Ja
**Fysieke server** | Ja | Ja
**Azure** | N.v.t. | Ja


### <a name="things-to-keep-in-mind"></a>Zaken om rekening mee te houden

Als u een cluster op basis van schijven gebruikt als een wille keurige laag in uw toepassing, kunt u Site Recovery replicatie niet gebruiken om die virtuele machines te repliceren. U kunt systeem eigen replicatie gebruiken die door de toepassing wordt verschaft en vervolgens een [herstel plan](site-recovery-create-recovery-plans.md) gebruiken om alle lagen te failoveren.

## <a name="replicating-virtual-machines"></a>Virtuele machines repliceren

Volg [deze richt lijnen](./vmware-azure-tutorial.md) om te beginnen met het repliceren van de virtuele machine naar Azure.

* Nadat de replicatie is voltooid, controleert u of u naar elke virtuele machine van elke laag gaat en selecteert u dezelfde beschikbaarheidsset in het gerepliceerde item > instellingen > eigenschappen > Compute en netwerk. Als uw weblaag bijvoorbeeld drie Vm's heeft, zorgt u ervoor dat alle drie de virtuele machines zijn geconfigureerd om deel te uitmaken van dezelfde beschikbaarheidsset in Azure.

    ![Set-Availability-set](./media/site-recovery-sharepoint/select-av-set.png)

* Raadpleeg voor meer informatie over het beveiligen van Active Directory en DNS het document [Active Directory en DNS beveiligen](site-recovery-active-directory.md) .

* Raadpleeg SQL Server-document [beveiligen](site-recovery-sql.md) voor meer informatie over het beveiligen van de database tier die op SQL Server wordt uitgevoerd.

## <a name="networking-configuration"></a>Netwerkconfiguratie

### <a name="network-properties"></a>Netwerk eigenschappen

* Configureer voor de virtuele machines van de app en de weblaag netwerk instellingen in Azure Portal zodat de Vm's worden gekoppeld aan het juiste DR-netwerk na een failover.

    ![Netwerk selecteren](./media/site-recovery-sharepoint/select-network.png)


* Als u een statisch IP-adres gebruikt, geeft u het IP-adres op dat u wilt dat de virtuele machine in het **doel-IP-** veld moet worden

    ![Statisch IP-adres instellen](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>DNS en verkeers routering

Voor Internet gerichte sites [maakt u een Traffic Manager profiel van het type Priority](../traffic-manager/quickstart-create-traffic-manager-profile.md) in het Azure-abonnement. En configureer uw DNS-en Traffic Manager profiel op de volgende manier.


| **Positie** | **Bron** | **Doel**|
| --- | --- | --- |
| Openbare DNS | Open bare DNS voor share point-sites <br/><br/> Bijvoorbeeld: sharepoint.contoso.com | Traffic Manager <br/><br/> contososharepoint.trafficmanager.net |
| On-premises DNS | sharepointonprem.contoso.com | Openbaar IP-adres op de on-premises Farm |


Maak in het Traffic Manager profiel [de primaire en herstel eindpunten](../traffic-manager/traffic-manager-configure-priority-routing-method.md). Gebruik het externe eind punt voor het on-premises eind punt en het open bare IP-eind punt voor Azure. Zorg ervoor dat de prioriteit hoger is ingesteld op een on-premises eind punt.

Host een test pagina op een specifieke poort (bijvoorbeeld 800) in de share point-weblaag, zodat de beschik baarheid na failover automatisch door Traffic Manager wordt gedetecteerd. Dit is een tijdelijke oplossing voor het geval u anonieme verificatie niet kunt inschakelen op uw share point-sites.

[Configureer het Traffic Manager profiel](../traffic-manager/traffic-manager-configure-priority-routing-method.md) met de onderstaande instellingen.

* Routerings methode-' Priority '
* DNS time to Live (TTL)-' 30 seconden '
* Instellingen voor eindpunt monitor: als u anonieme verificatie kunt inschakelen, kunt u een specifiek eind punt voor de website opgeven. U kunt ook een test pagina gebruiken op een specifieke poort (bijvoorbeeld 800).

## <a name="creating-a-recovery-plan"></a>Een herstel plan maken

Met een herstel plan kan de failover van verschillende lagen in een toepassing met meerdere lagen worden gevolgd, dus toepassings consistentie wordt gehandhaafd. Volg de onderstaande stappen voor het maken van een herstel plan voor een webtoepassing met meerdere lagen. [Meer informatie over het maken van een herstel plan](site-recovery-runbook-automation.md#customize-the-recovery-plan).

### <a name="adding-virtual-machines-to-failover-groups"></a>Virtuele machines toevoegen aan failover-groepen

1. Maak een herstel plan door de Vm's voor de app en de weblaag toe te voegen.
2. Klik op aanpassen om de Vm's te groeperen. Standaard maken alle Vm's deel uit van ' groep 1 '.

    ![RP aanpassen](./media/site-recovery-sharepoint/rp-groups.png)

3. Maak een andere groep (groep 2) en verplaats de virtuele machines van de weblaag naar de nieuwe groep. De Vm's van de app-laag moeten deel uitmaken van ' groep 1 ' en virtuele machines in de weblaag moeten deel uitmaken van ' groep 2 '. Dit is om ervoor te zorgen dat de app-laag-Vm's eerst worden opgestart, gevolgd door de weblaag Vm's.


### <a name="adding-scripts-to-the-recovery-plan"></a>Scripts toevoegen aan het herstel plan

U kunt de meest gebruikte Azure Site Recovery scripts implementeren in uw Automation-account door te klikken op de knop implementeren naar Azure hieronder. Wanneer u een gepubliceerd script gebruikt, zorg er dan voor dat u de instructies in het script volgt.

[![Implementeren in Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Voeg een pre-action script aan groep 1 toe aan een failover-SQL-beschikbaarheids groep. Gebruik het script ASR-SQL-FailoverAG dat is gepubliceerd in de voorbeeld scripts. Zorg ervoor dat u de instructies in het script volgt en breng de vereiste wijzigingen in het script op de juiste wijze aan.

    ![Add-AG-script-stap-1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Add-AG-script-stap-2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Voeg een bericht actie script toe om een load balancer toe te voegen aan de virtuele machines waarvoor een failover is uitgevoerd op de weblaag (groep 2). Gebruik het script ASR-AddSingleLoadBalancer dat is gepubliceerd in de voorbeeld scripts. Zorg ervoor dat u de instructies in het script volgt en breng de vereiste wijzigingen in het script op de juiste wijze aan.

    ![Add-LB-script-stap-1](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Add-LB-script-stap-2](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Voeg een hand matige stap toe om de DNS-records bij te werken zodat deze naar de nieuwe farm in azure verwijzen.

    * Voor Internet gerichte sites zijn er geen DNS-updates vereist na failover. Volg de stappen die worden beschreven in de sectie netwerk richtlijnen om Traffic Manager te configureren. Als het Traffic Manager profiel is ingesteld zoals beschreven in de vorige sectie, voegt u een script toe om de dummy poort (800 in het voor beeld) op de virtuele Azure-machine te openen.

    * Voor interne sites voegt u een hand matige stap toe om de DNS-record bij te werken zodat deze verwijst naar de nieuwe load balancer IP-adres van de virtuele weblaag.

4. Voeg een hand matige stap toe om de zoek toepassing te herstellen vanuit een back-up of start een nieuwe zoek service.

5. Volg de onderstaande stappen voor het herstellen van de zoek service toepassing vanuit een back-up.

    * Bij deze methode wordt ervan uitgegaan dat er een back-up van de Search Service toepassing is uitgevoerd vóór het onherstelbare gebeurtenis en dat de back-up beschikbaar is op de DR-site.
    * Dit kan eenvoudig worden bereikt door de back-up te plannen (bijvoorbeeld eenmaal per dag) en een Kopieer procedure te gebruiken om de back-up te plaatsen op de DR-site. Kopieer procedures kunnen scripts bevatten zoals AzCopy (Azure Copy) of DFSR instellen (Distributed File Services-replicatie).
    * Nu de share point-farm wordt uitgevoerd, navigeert u naar Centraal beheer, back-up en terugzetten en selecteert u herstellen. De Restore heeft de opgegeven back-uplocatie (mogelijk moet u de waarde bijwerken). Selecteer de Search Service back-up van de toepassing die u wilt herstellen.
    * De zoek opdracht wordt hersteld. Houd er rekening mee dat de Restore verwacht dezelfde topologie te vinden (hetzelfde aantal servers) en dat er dezelfde vaste stationsletters zijn toegewezen aan deze servers. Zie [' Search service-toepassing herstellen in share point 2013 '](/SharePoint/administration/restore-a-search-service-application) document voor meer informatie.


6. Volg de onderstaande stappen om te beginnen met een nieuwe zoek service toepassing.

    * Bij deze methode wordt ervan uitgegaan dat er een back-up van de data base ' Search Administration ' beschikbaar is op de DR-site.
    * Omdat de andere Search Service toepassings databases niet worden gerepliceerd, moeten ze opnieuw worden gemaakt. Hiertoe gaat u naar Centraal beheer en verwijdert u de Search Service toepassing. Verwijder de index bestanden op alle servers die als host fungeren voor de zoek index.
    * Maak de Search Service-toepassing opnieuw en maakt de data base opnieuw. Het is raadzaam om een voor bereid script te hebben dat deze service toepassing opnieuw maakt, omdat het niet mogelijk is om alle acties uit te voeren via de gebruikers interface. Het instellen van de locatie van het index station en het configureren van de zoek topologie is bijvoorbeeld alleen mogelijk met share point Power shell-cmdlets. Gebruik de Windows Power shell-cmdlet Restore-SPEnterpriseSearchServiceApplication en geef de door het logboek verzonden en gerepliceerde Zoek beheer database Search_Service__DB op. Deze cmdlet geeft de zoek configuratie, het schema, de beheerde eigenschappen, regels en bronnen en maakt een standaardset van de andere onderdelen.
    * Zodra de Search Service toepassing opnieuw is gemaakt, moet u een volledige verkenning starten voor elke inhouds bron om de Search Service te herstellen. U verliest enige analyse gegevens van de on-premises Farm, zoals zoek aanbevelingen.

7. Als alle stappen zijn voltooid, slaat u het herstel plan op en het laatste herstel plan ziet er als volgt uit.

    ![RP opgeslagen](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Een testfailover uitvoeren
Volg [deze richt lijnen](site-recovery-test-failover-to-azure.md) om een testfailover uit te voeren.

1.  Ga naar Azure Portal en selecteer de Recovery service-kluis.
2.  Klik op het herstel plan dat is gemaakt voor de share point-toepassing.
3.  Klik op Failover testen.
4.  Selecteer herstel punt en virtueel Azure-netwerk om het proces voor de testfailover te starten.
5.  Zodra de secundaire omgeving actief is, kunt u uw validaties uitvoeren.
6.  Zodra de validaties zijn voltooid, kunt u op ' testfailover opschonen ' klikken in het herstel plan en de test-failover-omgeving opschonen.

Raadpleeg voor meer informatie over het uitvoeren van een testfailover voor AD en DNS de [overwegingen voor het testen van failover voor AD-en DNS-](site-recovery-active-directory.md#test-failover-considerations) document.

Raadpleeg voor meer informatie over het uitvoeren van een testfailover voor SQL always ON-beschikbaarheids groepen het [uitvoeren van Application Dr met Azure site Recovery en](site-recovery-sql.md#disaster-recovery-of-an-application) het uitvoeren van een test-failover document.

## <a name="doing-a-failover"></a>Een failover uitvoeren
Volg [deze richt lijnen](site-recovery-failover.md) voor het uitvoeren van een failover.

1.  Ga naar Azure Portal en selecteer uw Recovery Services kluis.
2.  Klik op het herstel plan dat is gemaakt voor de share point-toepassing.
3.  Klik op failover.
4.  Selecteer herstel punt om het failoverproces te starten.

## <a name="next-steps"></a>Volgende stappen
U kunt meer te weten komen over het [repliceren van andere toepassingen](site-recovery-workload.md) met behulp van site Recovery.
