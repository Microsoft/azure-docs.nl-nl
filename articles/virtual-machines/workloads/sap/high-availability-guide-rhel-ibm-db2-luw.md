---
title: IBM Db2 HADR instellen op Azure virtual machines (Vm's) op RHEL | Microsoft Docs
description: Stel hoge Beschik baarheid in van IBM Db2 LUW op virtuele machines van Azure (Vm's) RHEL.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2020
ms.author: juergent
ms.openlocfilehash: 979de230655b4804fa65a4bddb678e21b59c4ea2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101674456"
---
# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Hoge beschikbaarheid van IBM Db2 LUW in Azure-VM's in Red Hat Enterprise Linux Server

IBM Db2 voor Linux, UNIX en Windows (LUW) in de [configuratie hoge Beschik baarheid en herstel na nood geval (HADR)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) bestaat uit één knoop punt waarop een primair data base-exemplaar wordt uitgevoerd en ten minste één knoop punt waarop een secundair data base-exemplaar wordt uitgevoerd. Wijzigingen in het primaire data base-exemplaar worden synchroon of asynchroon gerepliceerd naar een secundaire data base-instantie, afhankelijk van uw configuratie. 

> [!NOTE]
> Dit artikel bevat verwijzingen naar de termen *Master* en *Slave*, termen die micro soft niet meer gebruikt. Wanneer deze voor waarden worden verwijderd uit de software, worden ze uit dit artikel verwijderd.

In dit artikel wordt beschreven hoe u virtuele Azure-machines (Vm's) implementeert en configureert, het cluster raamwerk installeert en de IBM Db2-LUW installeert met HADR-configuratie. 

Het artikel heeft geen betrekking op het installeren en configureren van IBM Db2 LUW met HADR of SAP-software-installatie. Om u te helpen deze taken uit te voeren, bieden we verwijzingen naar SAP-en IBM-installatie handleidingen. Dit artikel is gericht op onderdelen die specifiek zijn voor de Azure-omgeving. 

De ondersteunde IBM Db2-versies zijn 10,5 en hoger, zoals beschreven in SAP Note [1928533].

Raadpleeg de volgende SAP-opmerkingen en-documentatie voordat u met de installatie begint:

| SAP-Opmerking | Description |
| --- | --- |
| [1928533] | SAP-toepassingen op Azure: ondersteunde producten en Azure VM-typen |
| [2015553] | SAP on Azure: vereisten voor ondersteuning |
| [2178632] | Belangrijkste meet waarden voor het bewaken van SAP on Azure |
| [2191498] | SAP op Linux met Azure: uitgebreide bewaking |
| [2243692] | Linux op Azure (IaaS) VM: SAP-licentie problemen |
| [2002167] | Red Hat Enterprise Linux 7. x: installatie en upgrade |
| [2694118] | Red Hat Enterprise Linux HA Add-On op Azure |
| [1999351] | Problemen met verbeterde Azure-bewaking voor SAP oplossen |
| [2233094] | DB6: SAP-toepassingen op Azure die gebruikmaken van IBM Db2 voor Linux, UNIX en Windows-aanvullende informatie |
| [1612105] | DB6: veelgestelde vragen over Db2 met HADR |


| Documentatie | 
| --- |
| [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): bevat alle vereiste SAP-opmerkingen voor Linux |
| [Azure virtual machines planning en implementatie van SAP in Linux][planning-guide] -hand leiding |
| [Azure virtual machines-implementatie voor SAP op Linux][deployment-guide] (dit artikel) |
| [Azure Virtual Machines Database Management System (DBMS) implementatie voor SAP in Linux-][dbms-guide] hand leiding |
| [SAP-werk belasting op de Azure-planning en implementatie Controlelijst][azr-sap-plancheck] |
| [Overzicht van de Add-On met hoge Beschik baarheid voor Red Hat Enterprise Linux 7][rhel-ha-addon] |
| [Add-On beheer met hoge Beschik baarheid][rhel-ha-admin] |
| [Referentie voor Add-On met hoge Beschik baarheid][rhel-ha-ref] |
| [Ondersteunings beleid voor RHEL-clusters met hoge Beschik baarheid-Microsoft Azure Virtual Machines als cluster leden][rhel-azr-supp]
| [Installeren en configureren van een Red Hat Enterprise Linux 7,4 (en hoger) High-Availability cluster op Microsoft Azure][rhel-azr-inst]
| [DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor IBM Db2][dbms-db2] |
| [IBM Db2 HADR 11,1][db2-hadr-11.1] |
| [IBM Db2 HADR 10,5][db2-hadr-10.5] |
| [Ondersteunings beleid voor RHEL-clusters met hoge Beschik baarheid: beheer van IBM Db2 voor Linux, UNIX en Windows in een cluster][rhel-db2-supp]



## <a name="overview"></a>Overzicht
Om hoge Beschik baarheid te kunnen garanderen, wordt IBM Db2 LUW met HADR geïnstalleerd op ten minste twee virtuele machines van Azure, die worden geïmplementeerd in een [Azure-beschikbaarheidsset](../../windows/tutorial-availability-sets.md) of op meerdere [Azure-beschikbaarheidszones](./sap-ha-availability-zones.md). 

In de volgende afbeeldingen ziet u een installatie van twee Azure-Vm's voor de Data Base-Server. Zowel de Azure-Vm's van de database server als de eigen opslag zijn gekoppeld en zijn actief. In HADR is één data base-exemplaar op een van de virtuele machines van Azure de rol van het primaire exemplaar. Alle clients zijn verbonden met een primair exemplaar. Alle wijzigingen in database transacties worden lokaal opgeslagen in het Db2-transactie logboek. Omdat de transactie logboek records lokaal blijven bestaan, worden de records via TCP/IP overgebracht naar het data base-exemplaar op de tweede database server, de stand-by-server of de stand-by-instantie. Het standby-exemplaar werkt de lokale data base bij door de overgebrachte transactie logboek records door te sturen. Op deze manier wordt de stand-by-server gesynchroniseerd met de primaire server.

HADR is slechts een replicatie functionaliteit. Er is geen detectie van fouten en geen automatische overname-of failover-faciliteiten. Een overname of overdracht naar de stand-by-server moet hand matig worden geïnitieerd door een database beheerder. U kunt de functie Linux pacemaker-clustering gebruiken om een automatische overname en detectie van de fout te verzorgen. Pacemaker bewaakt de twee Data Base-Server exemplaren. Wanneer het primaire database server exemplaar vastloopt, initieert pacemaker een *automatische* HADR-overname door de stand-by-server. Pacemaker zorgt er ook voor dat het virtuele IP-adres wordt toegewezen aan de nieuwe primaire server.

![Overzicht van IBM Db2-hoge Beschik baarheid](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

Als u SAP-toepassings servers verbinding wilt laten maken met een primaire data base, hebt u een virtuele-machinehost en een virtueel IP-adres nodig. In het geval van een failover maakt de SAP-toepassings servers verbinding met het nieuwe primaire data base-exemplaar. In een Azure-omgeving is [azure Load Balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) vereist voor het gebruik van een virtueel IP-adres in de manier die vereist is voor HADR van IBM Db2. 

De volgende afbeelding geeft een overzicht van een Maxi maal beschik bare installatie van een SAP-systeem op basis van een IBM Db2-data base, zodat u beter begrijpt hoe IBM Db2-LUW met HADR en pacemaker in een Maxi maal beschik bare SAP-systeem installatie past. Dit artikel behandelt alleen IBM Db2, maar bevat verwijzingen naar andere artikelen over het instellen van andere onderdelen van een SAP-systeem.

![Overzicht van de volledige Beschik baarheid van IBM DB2-hele omgeving](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>Overzicht op hoog niveau van de vereiste stappen
Als u een configuratie van IBM Db2 wilt implementeren, moet u de volgende stappen uitvoeren:

  + Plan uw omgeving.
  + Implementeer de Vm's.
  + RHEL Linux bijwerken en bestands systemen configureren.
  + Pacemaker installeren en configureren.
  + [Glusterfs-cluster][glusterfs] of- [Azure NetApp files][anf-rhel] instellen
  + Installeer [ASCS/ers op een afzonderlijk cluster][ascs-ha-rhel].
  + Installeer de IBM Db2-data base met de optie gedistribueerde/hoge Beschik baarheid (SWPM).
  + Installeer en maak een secundair database knooppunt en-exemplaar en configureer HADR.
  + Controleer of HADR werkt.
  + Pas de pacemaker-configuratie toe om IBM Db2 te beheren.
  + Azure Load Balancer configureren.
  + Primaire en dialoog toepassings servers installeren.
  + De configuratie van SAP-toepassings servers controleren en aanpassen.
  + Voer failover-en overname tests uit.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Azure-infra structuur plannen voor het hosten van IBM Db2 LUW met HADR

Voltooi het plannings proces voordat u de implementatie uitvoert. Planning bouwt de basis voor het implementeren van een configuratie van Db2 met HADR in Azure. De belangrijkste elementen die deel moeten uitmaken van planning voor IMB Db2 LUW (database onderdeel van de SAP-omgeving), worden weer gegeven in de volgende tabel:

| Onderwerp | Korte beschrijving |
| --- | --- |
| Azure-resource groepen definiëren | Resource groepen waar u VM, VNet, Azure Load Balancer en andere resources implementeert. Kan bestaand of nieuw zijn. |
| Definitie van virtueel netwerk/subnet | Waar Vm's voor IBM Db2 en Azure Load Balancer worden geïmplementeerd. U kunt bestaande of nieuwe maken. |
| Virtuele machines die IBM Db2 LUW hosten | VM-grootte, opslag, netwerken, IP-adres. |
| Naam van virtuele host en virtueel IP-adres voor de IBM Db2-data base| De naam van het virtuele IP-adres of de hostnaam die wordt gebruikt voor de verbinding met SAP-toepassings servers. **db-virt-hostname**, **db-virt-IP**. |
| Azure-omheining | Methode om te voor komen dat situaties met gesplitste problemen worden voor komen. |
| Azure Load Balancer | Gebruik van Basic of Standard (aanbevolen), test poort voor de Db2-data base (onze aanbeveling 62500) **test poort**. |
| Naamomzetting| Hoe naam omzetting werkt in de omgeving. DNS-service wordt sterk aanbevolen. Lokaal Hosts-bestand kan worden gebruikt. |
    
Zie [pacemaker instellen op Red Hat Enterprise Linux in azure][rhel-pcs-azr]voor meer informatie over Linux-pacemaker in Azure.

## <a name="deployment-on-red-hat-enterprise-linux"></a>Implementatie op Red Hat Enterprise Linux

De resource agent voor IBM Db2 LUW is opgenomen in Red Hat Enterprise Linux server HA-invoeg toepassing. Voor de instellingen die in dit document worden beschreven, moet u Red Hat Enterprise Linux voor SAP gebruiken. Azure Marketplace bevat een installatie kopie voor Red Hat Enterprise Linux 7,4 voor SAP of hoger die u kunt gebruiken om nieuwe virtuele machines van Azure te implementeren. Houd rekening met de verschillende ondersteunings-of service modellen die door Red Hat worden aangeboden via de Azure Marketplace wanneer u een VM-installatie kopie kiest in de Azure VM Marketplace.

### <a name="hosts-dns-updates"></a>Hosts: DNS-updates
Maak een lijst van alle hostnamen, met inbegrip van namen van virtuele hosts, en werk uw DNS-servers bij om het juiste IP-adres in te stellen voor omzetting van hostnamen. Als er geen DNS-server bestaat of als u geen DNS-vermeldingen kunt bijwerken en maken, moet u de lokale host-bestanden gebruiken van de afzonderlijke virtuele machines die deel nemen aan dit scenario. Als u vermeldingen voor host-bestanden gebruikt, moet u ervoor zorgen dat de vermeldingen worden toegepast op alle virtuele machines in de SAP-systeem omgeving. We raden u echter aan uw DNS te gebruiken die in het ideale geval wordt uitgebreid naar Azure


### <a name="manual-deployment"></a>Handmatige implementatie

Zorg ervoor dat het geselecteerde besturings systeem wordt ondersteund door IBM/SAP voor IBM Db2 LUW. De lijst met ondersteunde versies van besturings systemen voor Azure Vm's en Db2-releases is beschikbaar in SAP Note [1928533]. De lijst met versies van het besturings systeem per afzonderlijke Db2-versie is beschikbaar in de SAP-product beschikbaarheids matrix. We raden ten zeerste aan een minimum aan van Red Hat Enterprise Linux 7,4 voor SAP vanwege verbeteringen in de prestaties van Azure in deze of latere versies van Red Hat Enterprise Linux.

1. Een resource groep maken of selecteren.
1. Maak of selecteer een virtueel netwerk en een subnet.
1. Maak een beschikbaarheidsset voor Azure of implementeer een beschikbaarheids zone.
    + Stel de maximale update domeinen in op 2 voor de beschikbaarheidsset.
1. Virtuele machine 1 maken.
    + Gebruik Red Hat Enterprise Linux voor SAP-installatie kopie in de Azure Marketplace.
    + Selecteer de beschik bare Azure-beschikbaarheidsset die u hebt gemaakt in stap 3 of selecteer beschikbaarheids zone.
1.  Maak de virtuele machine 2.
    + Gebruik Red Hat Enterprise Linux voor SAP-installatie kopie in de Azure Marketplace.
    + Selecteer de beschik baarheid van Azure die u hebt gemaakt in stap 3 of selecteer beschikbaarheids zone (niet dezelfde zone als in stap 3).
1. Voeg gegevens schijven toe aan de Vm's en controleer vervolgens de aanbeveling van een bestandssysteem installatie in het artikel [IBM Db2 Azure virtual machines DBMS-implementatie voor SAP-workload][dbms-db2].

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>De IBM Db2 LUW-en SAP-omgeving installeren

Voordat u begint met de installatie van een SAP-omgeving op basis van IBM Db2 LUW, raadpleegt u de volgende documentatie:

+ Azure-documentatie
+ SAP-documentatie
+ IBM-Documentatie

Koppelingen naar deze documentatie vindt u in de introductie sectie van dit artikel.

Raadpleeg de SAP-installatie handleidingen voor het installeren van toepassingen op basis van netweave op IBM Db2 LUW.
U kunt de hand leidingen vinden in de SAP-Help-Portal met behulp van de [SAP-installatie gids][sap-instfind]zoeken.

U kunt het aantal gidsen dat wordt weer gegeven in de portal verminderen door de volgende filters in te stellen:
- Ik wil: "een nieuw systeem installeren"
- Mijn Data Base: ' IBM Db2 voor Linux, UNIX en Windows '
- Aanvullende filters voor SAP NetWeaver-versies, stack configuratie of besturings systeem

#### <a name="red-hat-firewall-rules"></a>Red Hat firewall-regels
Red Hat Enterprise Linux is Firewall standaard ingeschakeld. 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Installatie hints voor het instellen van IBM Db2 LUW met HADR

Het primaire exemplaar van de IBM Db2 LUW-data base instellen:

- Gebruik de optie hoge Beschik baarheid of gedistribueerd.
- Installeer de SAP ASCS/ERS en het data base-exemplaar.
- Maak een back-up van de zojuist geïnstalleerde data base.

> [!IMPORTANT] 
> Noteer de "data base-communicatie poort" die tijdens de installatie is ingesteld. Dit moet hetzelfde poort nummer zijn voor beide data base-exemplaren.
>![SAP SWPM-poort definitie](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>IBM Db2 HADR-instellingen voor Azure

   Wanneer u een Azure pacemaker omheinings agent gebruikt, stelt u de volgende para meters in:

   - Duur van HADR-peer venster (seconden) (HADR_PEER_WINDOW) = 240  
   - HADR time-outwaarde (HADR_TIMEOUT) = 45

We raden de voor gaande para meters aan op basis van initiële failover/overname-tests. Het is verplicht om te testen op de juiste functionaliteit van failover en overname met deze parameter instellingen. Omdat afzonderlijke configuraties kunnen variëren, moeten de para meters mogelijk worden aangepast. 

> [!NOTE]
> Specifiek voor IBM Db2 met HADR-configuratie met normaal opstarten: het secundaire of standby-data base-exemplaar moet actief zijn voordat u het primaire data base-exemplaar kunt starten.

   
> [!NOTE]
> Voor installatie-en configuratie-informatie die specifiek is voor Azure en pacemaker: tijdens de installatie procedure via SAP software Provisioning Manager, is er een expliciete vraag over hoge Beschik baarheid voor IBM Db2 LUW:
>+ Selecteer geen **IBM Db2-pureScale**.
>+ Selecteer geen **IBM Tivoli-systeem automatisering installeren voor meerdere platformen**.
>+ Selecteer geen **cluster configuratie bestanden genereren**.
>![Opties voor SAP SWPM-DB2 HA](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


Voer de volgende stappen uit om de stand-by-database server in te stellen met behulp van de SAP-procedure voor het kopiëren van een systeem:

1. Selecteer de optie **kopiëren** van het systeem >   >  **gedistribueerd**  >  **Data Base-exemplaar** van het doel systeem.
1. Als Kopieer methode selecteert u **homo geen systeem** zodat u back-up kunt gebruiken om een back-up te herstellen op het stand-by-Server exemplaar.
1. Wanneer u de stap afsluiten hebt bereikt om de data base te herstellen voor een homogene systeem kopie, sluit u het installatie programma af. Zet de data base terug vanuit een back-up van de primaire host. Alle volgende installatie fasen zijn al uitgevoerd op de primaire database server.

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>Red Hat firewall-regels voor DB2 HADR
Voeg firewall regels toe om verkeer naar DB2 toe te staan en tussen DB2 voor HADR te werken:
+ Data base-communicatie poort. Als u partities gebruikt, voegt u deze poorten ook toe.
+ HADR-poort (waarde van de DB2-para meter HADR_LOCAL_SVC)
+ Azure-test poort
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR-controle
Voor demonstratie doeleinden en de procedures die in dit artikel worden beschreven, is de SID van de Data Base **id2**.

Nadat u HADR hebt geconfigureerd en de status is ingesteld op PEER en op de primaire en stand-by-knoop punten is aangesloten, voert u de volgende controle uit:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
Database Member 0 -- Database ID2 -- Active -- Up 1 days 15:45:23 -- Date 2019-06-25-10.55.25.349375

                            <b>HADR_ROLE = PRIMARY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 1
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.076494 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 5
                   HEARTBEAT_EXPECTED = 52
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 5
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 369280
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 132242668
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 300
                      PEER_WINDOW_END = 06/25/2019 11:12:03.000000 (1561461123)
             READS_ON_STANDBY_ENABLED = N


#Secondary output:
Database Member 0 -- Database ID2 -- Standby -- Up 1 days 15:45:18 -- Date 2019-06-25-10.56.19.820474

                            <b>HADR_ROLE = STANDBY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 0
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.078116 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 0
                   HEARTBEAT_EXPECTED = 10
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 1
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 367360
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 0
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 1000
                      PEER_WINDOW_END = 06/25/2019 11:12:59.000000 (1561461179)
             READS_ON_STANDBY_ENABLED = N
</code></pre>

### <a name="configure-azure-load-balancer"></a>Azure Load Balancer configureren

Als u Azure Load Balancer wilt configureren, is het raadzaam om de [Azure Standard load BALANCER SKU](../../../load-balancer/load-balancer-overview.md) te gebruiken en vervolgens de volgende handelingen uit te voeren.

> [!NOTE]
> De Standard Load Balancer SKU heeft beperkingen voor het openen van open bare IP-adressen van de knoop punten onder de Load Balancer. De connectiviteit van het [open bare eind punt van het artikel voor virtual machines het gebruik van Azure Standard Load Balancer in scenario's met hoge Beschik baarheid van SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md) beschrijft manieren om deze knoop punten in te scha kelen voor toegang tot open bare IP-adressen

> [!IMPORTANT]
> Zwevend IP wordt niet ondersteund voor een secundaire IP-configuratie in een NIC in scenario's voor taak verdeling. Zie beperkingen voor [Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations)voor meer informatie. Als u een extra IP-adres voor de virtuele machine nodig hebt, implementeert u een tweede NIC.  

1. Een front-end-IP-adres groep maken:

   a. Open in de Azure Portal de Azure Load Balancer, selecteer **frontend-IP-adres groep** en selecteer vervolgens **toevoegen**.

   b. Voer de naam in van de nieuwe front-end-IP-adres groep (bijvoorbeeld **Db2-verbinding**).

   c. Stel de **toewijzing** in op **statisch** en voer **het IP-adres in** dat aan het begin is gedefinieerd.

   d. Selecteer **OK**.

   e. Nadat de nieuwe front-end-IP-groep is gemaakt, noteert u het IP-adres van de groep.

1. Maak een back-end-pool:

   a. Open in de Azure Portal de Azure Load Balancer, selecteer **back-endservers** en selecteer vervolgens **toevoegen**.

   b. Voer de naam van de nieuwe back-end-pool in (bijvoorbeeld **Db2-back-end**).

   c. Selecteer **een virtuele machine toevoegen**.

   d. Selecteer de beschikbaarheidsset of de virtuele machines die als host dienen voor de IBM Db2-data base die in de vorige stap is gemaakt.

   e. Selecteer de virtuele machines van het IBM Db2-cluster.

   f. Selecteer **OK**.

1. Een status test maken:

   a. Open in de Azure Portal de Azure Load Balancer, selecteer **status controles** en selecteer **toevoegen**.

   b. Voer de naam in van de nieuwe status test (bijvoorbeeld **Db2-HP**).

   c. Selecteer **TCP** als protocol en poort **62500**. Behoud de waarde voor **interval** ingesteld op **5** en behoud de drempel waarde voor **onjuiste status** ingesteld op **2**.

   d. Selecteer **OK**.

1. De regels voor taak verdeling maken:

   a. Open in de Azure Portal de Azure Load Balancer, selecteer **taakverdelings regels** en selecteer vervolgens **toevoegen**.

   b. Voer de naam in van de nieuwe Load Balancer regel (bijvoorbeeld **Db2-sid**).

   c. Selecteer het front-end-IP-adres, de back-end-pool en de status test die u eerder hebt gemaakt (bijvoorbeeld **Db2-** front-end).

   d. Zorg ervoor dat het **protocol** is ingesteld op **TCP** en geef poort *database communicatie poort* op.

   e. Verhoog de **time-out voor inactiviteit** tot 30 minuten.

   f. Zorg ervoor dat u **zwevende IP-adressen inschakelt**.

   g. Selecteer **OK**.

**[A]** firewall regel toevoegen voor test poort:

<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

## <a name="create-the-pacemaker-cluster"></a>Het Pacemaker-cluster maken
    
Als u een basis-pacemaker-cluster wilt maken voor deze IBM Db2-server, raadpleegt u [pacemaker instellen op Red Hat Enterprise Linux in azure][rhel-pcs-azr]. 

## <a name="db2-pacemaker-configuration"></a>Configuratie van Db2 pacemaker

Wanneer u pacemaker gebruikt voor automatische failover in het geval van een storing in een knoop punt, moet u de Db2-instanties en pacemaker dienovereenkomstig configureren. In deze sectie wordt dit type configuratie beschreven.

De volgende items worden voorafgegaan door een van beide:

- **[A]**: van toepassing op alle knoop punten
- **[1]**: alleen van toepassing op knoop punt 1 
- **[2]**: alleen van toepassing op knoop punt 2

**[A]** vereiste voor configuratie van pacemaker:
1. Sluit beide database servers met gebruikers-DB2 af \<sid> met db2stop.
1. Wijzig de shell-omgeving voor de DB2- \<sid> gebruiker in */bin/ksh*:
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>  

### <a name="pacemaker-configuration"></a>Pacemaker-configuratie

**[1]** IBM Db2 HADR-specifieke pacemaker-configuratie:
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]** IBM Db2-resources maken:

Als u een cluster op **RHEL 7. x** maakt, gebruikt u de volgende opdrachten:

<pre><code># Replace <b>bold strings</b> with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.
sudo pcs resource create Db2_HADR_<b>ID2</b> db2 instance='<b>db2id2</b>' dblist='<b>ID2</b>' master meta notify=true resource-stickiness=5000

#Configure resource stickiness and correct cluster notifications for master resoruce
sudo pcs resource update Db2_HADR_<b>ID2</b>-master meta notify=true resource-stickiness=5000

# Configure virtual IP - same as Azure Load Balancer IP
sudo pcs resource create vip_<b>db2id2</b>_<b>ID2</b> IPaddr2 ip='<b>10.100.0.40</b>'

# Configure probe port for Azure load Balancer
sudo pcs resource create nc_<b>db2id2</b>_<b>ID2</b> azure-lb port=<b>62500</b>

#Create a group for ip and Azure loadbalancer probe port
sudo pcs resource group add g_ipnc_<b>db2id2</b>_<b>ID2</b> vip_<b>db2id2</b>_<b>ID2</b> nc_<b>db2id2</b>_<b>ID2</b>

#Create colocation constrain - keep Db2 HADR Master and Group on same node
sudo pcs constraint colocation add g_ipnc_<b>db2id2</b>_<b>ID2</b> with master Db2_HADR_<b>ID2</b>-master

#Create start order constrain
sudo pcs constraint order promote Db2_HADR_<b>ID2</b>-master then g_ipnc_<b>db2id2</b>_<b>ID2</b>
</code></pre>

Als u een cluster op **RHEL 8. x** maakt, gebruikt u de volgende opdrachten:

<pre><code># Replace <b>bold strings</b> with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.
sudo pcs resource create Db2_HADR_<b>ID2</b> db2 instance='<b>db2id2</b>' dblist='<b>ID2</b>' promotable meta notify=true resource-stickiness=5000

#Configure resource stickiness and correct cluster notifications for master resoruce
sudo pcs resource update Db2_HADR_<b>ID2</b>-clone meta notify=true resource-stickiness=5000

# Configure virtual IP - same as Azure Load Balancer IP
sudo pcs resource create vip_<b>db2id2</b>_<b>ID2</b> IPaddr2 ip='<b>10.100.0.40</b>'

# Configure probe port for Azure load Balancer
sudo pcs resource create nc_<b>db2id2</b>_<b>ID2</b> azure-lb port=<b>62500</b>

#Create a group for ip and Azure loadbalancer probe port
sudo pcs resource group add g_ipnc_<b>db2id2</b>_<b>ID2</b> vip_<b>db2id2</b>_<b>ID2</b> nc_<b>db2id2</b>_<b>ID2</b>

#Create colocation constrain - keep Db2 HADR Master and Group on same node
sudo pcs constraint colocation add g_ipnc_<b>db2id2</b>_<b>ID2</b> with master Db2_HADR_<b>ID2</b>-clone

#Create start order constrain
sudo pcs constraint order promote Db2_HADR_<b>ID2</b>-clone then g_ipnc_<b>db2id2</b>_<b>ID2</b>
</code></pre>

**[1]** IBM Db2-resources starten:
* Pacemaker uit de onderhouds modus plaatsen.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]** zorg ervoor dat de cluster status OK is en dat alle resources zijn gestart. Het is niet belang rijk op welke knoop punten de resources worden uitgevoerd.
<pre><code>sudo pcs status
2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
</code></pre>

> [!IMPORTANT]
> U moet de geclusterde Db2-instantie pacemaker beheren met behulp van pacemaker-hulpprogram ma's. Als u DB2-opdrachten gebruikt, zoals db2stop, detecteert pacemaker de actie als een storing van de resource. Als u onderhoud uitvoert, kunt u de knoop punten of resources in de onderhouds modus plaatsen. Pacemaker suspendeert bewakings bronnen en u kunt vervolgens normale DB2-beheer opdrachten gebruiken.

### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Wijzigingen aanbrengen in SAP-profielen voor het gebruik van een virtueel IP-adres voor verbinding
Als u verbinding wilt maken met het primaire exemplaar van de HADR-configuratie, moet de SAP-toepassingslaag gebruikmaken van het virtuele IP-adres dat u hebt gedefinieerd en geconfigureerd voor de Azure Load Balancer. De volgende wijzigingen zijn vereist:

/sapmnt/ \<SID> /profile/default. KON
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/ \<SID> /global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>

## <a name="install-primary-and-dialog-application-servers"></a>Primaire en dialoog toepassings servers installeren

Wanneer u primaire en dialoogvenster toepassings servers installeert op basis van een Db2 HADR-configuratie, gebruikt u de naam van de virtuele host die u hebt gekozen voor de configuratie.

Als u de installatie hebt uitgevoerd voordat u de configuratie van de Db2 HADR hebt gemaakt, brengt u de wijzigingen aan zoals beschreven in de voor gaande sectie en als volgt voor SAP Java-stacks.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>Controle van JDBC-URL voor ABAP + Java of Java stack Systems

Gebruik het J2EE-configuratie programma om de JDBC-URL te controleren of bij te werken. Omdat het J2EE-configuratie programma een grafisch hulp programma is, moet X server zijn geïnstalleerd:
 
1. Meld u aan bij de primaire toepassings server van het J2EE-exemplaar en voer de volgende handelingen uit:
    
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>  
    
1. Kies in het linkerdeel venster de optie **beveiligings archief**.
1. Kies in het rechterdeel venster de sleutel `jdbc/pool/\<SAPSID>/url` .
1. Wijzig de hostnaam in de JDBC-URL in de naam van de virtuele host.
    
    <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>  
    
1. Selecteer **Toevoegen**.
1. Als u de wijzigingen wilt opslaan, selecteert u het schijf pictogram in de linkerbovenhoek.
1. Sluit het configuratie programma.
1. Start het Java-exemplaar opnieuw.

## <a name="configure-log-archiving-for-hadr-setup"></a>Logboek Archivering configureren voor HADR Setup

Als u de instelling voor het archiveren van de Db2-logboeken voor HADR wilt configureren, raden we u aan om zowel de primaire als de stand-by-data base zodanig te configureren dat het automatisch ophalen van logboeken van alle locatie van logboek archief is. Zowel de primaire als de stand-by-data base moeten logboek archiefbestanden kunnen ophalen van alle logboeken van de logbestanden waartoe een van de data base-exemplaren logboek bestanden kan archiveren. 

Het archiveren van het logboek wordt alleen uitgevoerd door de primaire data base. Als u de HADR-rollen van de database servers wijzigt of als er een fout optreedt, is de nieuwe primaire data base verantwoordelijk voor het archiveren van Logboeken. Als u meerdere archief locaties voor logboek registratie hebt ingesteld, kunnen uw logboeken twee keer worden gearchiveerd. In het geval van een lokale of externe achterstand moet u mogelijk ook hand matig de gearchiveerde logboeken van de oude primaire server kopiëren naar de actieve logboek locatie van de nieuwe primaire server.

U kunt het beste een algemene NFS-share of GlusterFS configureren, waarbij logboeken worden geschreven van beide knoop punten. De NFS-share of GlusterFS moet Maxi maal beschikbaar zijn. 

U kunt bestaande Maxi maal beschik bare NFS-shares of GlusterFS gebruiken voor trans porten of een profielmap. Zie voor meer informatie:

- [GlusterFS in Azure VM's in Red Hat Enterprise Linux voor SAP NetWeaver][glusterfs] 
- [Hoge Beschik baarheid voor SAP NetWeaver op Azure Vm's op Red Hat Enterprise Linux met Azure NetApp Files voor SAP-toepassingen][anf-rhel]
- [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md) (voor het maken van NFS-shares)

## <a name="test-the-cluster-setup"></a>De Cluster installatie testen

In deze sectie wordt beschreven hoe u de Setup van de Db2-HADR kunt testen. Elke test gaat ervan uit dat IBM Db2 Primary wordt uitgevoerd op de virtuele machine *AZ-idb01* . Gebruiker met sudo-bevoegdheden of root (niet aanbevolen) moet worden gebruikt.

De initiële status voor alle test cases wordt hier uitgelegd: (crm_mon-r-of PCs-status)

- de **status van pc's** is een moment opname van de pacemaker-status tijdens de uitvoerings tijd 
- **crm_mon-r** doorlopende uitvoer van pacemaker-status

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
</code></pre>

De oorspronkelijke status in een SAP-systeem wordt beschreven in trans actie DBACOCKPIT > configuratie > overzicht, zoals wordt weer gegeven in de volgende afbeelding:

![DBACockpit-pre-migratie](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)

### <a name="test-takeover-of-ibm-db2"></a>De overname van IBM Db2 testen


> [!IMPORTANT] 
> Voordat u met de test begint, moet u ervoor zorgen dat:
> * Pacemaker heeft geen mislukte acties (pc's status).
> * Er zijn geen locatie beperkingen (resten van de migratie test)
> * De synchronisatie van IBM Db2 HADR werkt. Controleren met gebruikers-DB2\<sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>

Migreer het knoop punt waarop de primaire Db2-data base wordt uitgevoerd door de volgende opdracht uit te voeren:
<pre><code># On RHEL 7.x
sudo pcs resource move Db2_HADR_<b>ID2</b>-master
# On RHEL 8.x
sudo pcs resource move Db2_HADR_<b>ID2</b>-clone --master
</code></pre>

Nadat de migratie is voltooid, ziet de CRM-status uitvoer er als volgt uit:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

</code></pre>

De oorspronkelijke status in een SAP-systeem wordt beschreven in trans actie DBACOCKPIT > configuratie > overzicht, zoals wordt weer gegeven in de volgende afbeelding:

![DBACockpit-na migratie](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

Met resource migratie met ' pc's verplaatsen ' worden locatie beperkingen gemaakt. Locatie beperkingen in dit geval wordt het uitvoeren van een IBM Db2-exemplaar op AZ-idb01 voor komen. Als locatie beperkingen niet worden verwijderd, kan de resource geen failback uitvoeren.

Het knoop punt voor locatie beperking en stand-by verwijderen wordt gestart op AZ-idb01.
<pre><code># On RHEL 7.x
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
# On RHEL 8.x
sudo pcs resource clear Db2_HADR_<b>ID2</b>-clone</code></pre>

De cluster status wordt gewijzigd in:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

![DBACockpit-verwijderde locatie beperken](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)

Migreer de resource terug naar *AZ-idb01* en wis de locatie beperkingen
<pre><code># On RHEL 7.x
sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
# On RHEL 8.x
sudo pcs resource move Db2_HADR_<b>ID2</b>-clone --master
sudo pcs resource clear Db2_HADR_<b>ID2</b>-clone</code></pre>

- **Op RHEL 7. x-pc's resource verplaatsen \<res_name> <host> :** maakt locatie beperkingen en kan problemen veroorzaken met overname
- **Op RHEL 8. x-pc's resource verplaatsen \<res_name> --Master:** maakt locatie beperkingen en kan problemen veroorzaken met overname
- **pc's bron wissen \<res_name>**: Hiermee worden de beperkingen voor locaties gewist
- **pc's bron opschonen \<res_name>**: Hiermee worden alle fouten van de resource gewist

### <a name="test-a-manual-takeover"></a>Een hand matige overname testen

U kunt een hand matige overname testen door de pacemaker-service op *AZ-idb01* te stoppen:
<pre><code>systemctl stop pacemaker</code></pre>

status op *AZ-ibdb02*
<pre><code>2 nodes configured
5 resources configured

Node az-idb01: pending
Online: [ az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled</code></pre>

Na de failover kunt u de service opnieuw starten op *AZ-idb01*.
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Het Db2-proces beëindigen op het knoop punt waarop de primaire HADR-data base wordt uitgevoerd

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

Het Db2-exemplaar is mislukt en pacemaker verplaatst het hoofd knooppunt en rapport over de volgende status:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=49, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 09:57:35 2019', queued=0ms, exec=362ms</code></pre>

Pacemaker start het primaire Db2-data base-exemplaar opnieuw op hetzelfde knoop punt, of er wordt een failover uitgevoerd naar het knoop punt waarop het secundaire data base-exemplaar wordt uitgevoerd en er wordt een fout gemeld.

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Het Db2-proces beëindigen op het knoop punt waarop het secundaire data base-exemplaar wordt uitgevoerd

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

Het knoop punt wordt weer gegeven als mislukt en er wordt een fout gerapporteerd
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Failed Actions:
* Db2_HADR_ID2_monitor_20000 on az-idb02 'not running' (7): call=144, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 10:02:09 2019', queued=0ms, exec=0ms</code></pre>

Het Db2-exemplaar wordt opnieuw gestart in de secundaire rol waaraan het is toegewezen.

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>DATA base stoppen via db2stop forceren op het knoop punt waarop het primaire exemplaar van de HADR-data base wordt uitgevoerd

Als User DB2 \<sid> Execute opdracht db2stop Force:
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

Fout gedetecteerd:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Slaves: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Stopped
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Stopped

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</code></pre>

Het secundaire data base-exemplaar van de Db2-HADR is gepromoveerd tot de primaire rol.
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</pre></code>


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>De VM met de HADR Primary data base-instantie crashen met ' Stop '

<pre><code>#Linux kernel panic.
sudo echo b > /proc/sysrq-trigger</code></pre>

In dat geval detecteert pacemaker dat het knoop punt waarop het primaire data base-exemplaar wordt uitgevoerd, niet reageert.

<pre><code>2 nodes configured
5 resources configured

Node az-idb01: UNCLEAN (online)
Online: [ az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01</code></pre>

De volgende stap is om te controleren op een situatie met *Split-hersenen* . Nadat het nagelaten knoop punt heeft vastgesteld dat het knoop punt waarop het primaire data base-exemplaar voor het laatst werd uitgevoerd, niet beschikbaar is, wordt er een failover van resources uitgevoerd.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb02 ]
OFFLINE: [ az-idb01 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02 </code></pre>


In het geval van een failover van een kernel wordt het knoop punt waarvoor een storing is opgetreden, opnieuw genoteerd door de afomheinings agent. Nadat het knoop punt met de fout weer online is, moet u het pacemaker-cluster starten door
<pre><code>sudo pcs cluster start</code></pre> het Db2-exemplaar wordt gestart in de secundaire rol.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

## <a name="next-steps"></a>Volgende stappen
- [Architectuur en scenario's met hoge Beschik baarheid voor SAP net-Weaver](./sap-high-availability-architecture-scenarios.md)
- [Pacemaker instellen voor Red Hat Enterprise Linux in azure][rhel-pcs-azr]

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105
[2694118]:https://launchpad.support.sap.com/#/notes/2694118


[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm

[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[rhel-ha-addon]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index
[rhel-ha-admin]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index
[rhel-ha-ref]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index
[rhel-azr-supp]:https://access.redhat.com/articles/3131341
[rhel-azr-inst]:https://access.redhat.com/articles/3252491
[rhel-db2-supp]:https://access.redhat.com/articles/3144221
[ascs-ha-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel
[glusterfs]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs
[rhel-pcs-azr]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker
[anf-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist
