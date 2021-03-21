---
title: IBM Db2 HADR instellen op Azure virtual machines (Vm's) | Microsoft Docs
description: Stel hoge Beschik baarheid in van IBM Db2 LUW op Azure virtual machines (Vm's).
author: msjuergent
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 10/16/2020
ms.author: juergent
ms.reviewer: cynthn
ms.openlocfilehash: faafce32c3452a5c4ff08783ec2edd28f7f961e9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671882"
---
# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Hoge Beschik baarheid van IBM Db2 LUW op Azure Vm's op SUSE Linux Enterprise Server met pacemaker

IBM Db2 voor Linux, UNIX en Windows (LUW) in de [configuratie hoge Beschik baarheid en herstel na nood geval (HADR)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) bestaat uit één knoop punt waarop een primair data base-exemplaar wordt uitgevoerd en ten minste één knoop punt waarop een secundair data base-exemplaar wordt uitgevoerd. Wijzigingen in het primaire data base-exemplaar worden synchroon of asynchroon gerepliceerd naar een secundaire data base-instantie, afhankelijk van uw configuratie. 

> [!NOTE]
> Dit artikel bevat verwijzingen naar de termen *Master* en *Slave*, termen die micro soft niet meer gebruikt. Wanneer deze voor waarden worden verwijderd uit de software, worden ze uit dit artikel verwijderd.
   
In dit artikel wordt beschreven hoe u virtuele Azure-machines (Vm's) implementeert en configureert, het cluster raamwerk installeert en de IBM Db2-LUW installeert met HADR-configuratie. 

Het artikel heeft geen betrekking op het installeren en configureren van IBM Db2 LUW met HADR of SAP-software-installatie. Om u te helpen deze taken uit te voeren, bieden we verwijzingen naar SAP-en IBM-installatie handleidingen. Dit artikel is gericht op onderdelen die specifiek zijn voor de Azure-omgeving. 

De ondersteunde IBM Db2-versies zijn 10,5 en hoger, zoals beschreven in SAP Note [1928533].

Raadpleeg de volgende SAP-opmerkingen en-documentatie voordat u met de installatie begint:

| SAP-Opmerking | Beschrijving |
| --- | --- |
| [1928533] | SAP-toepassingen op Azure: ondersteunde producten en Azure VM-typen |
| [2015553] | SAP on Azure: vereisten voor ondersteuning |
| [2178632] | Belangrijkste meet waarden voor het bewaken van SAP on Azure |
| [2191498] | SAP op Linux met Azure: uitgebreide bewaking |
| [2243692] | Linux op Azure (IaaS) VM: SAP-licentie problemen |
| [1984787] | SUSE LINUX Enter prise Server 12: installatie notities |
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
| [SUSE Linux Enterprise Server voor SAP-toepassingen 12 SP4 best practices-hand leidingen][sles-for-sap-bp] |
| [SUSE Linux Enter prise-extensie voor hoge Beschik baarheid 12 SP4][sles-ha-guide] |
| [DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor IBM Db2][dbms-db2] |
| [IBM Db2 HADR 11,1][db2-hadr-11.1] |
| [IBM Db2 HADR R 10,5][db2-hadr-10.5] |

## <a name="overview"></a>Overzicht
Om hoge Beschik baarheid te kunnen garanderen, wordt IBM Db2 LUW met HADR geïnstalleerd op ten minste twee virtuele machines van Azure, die worden geïmplementeerd in een [Azure-beschikbaarheidsset](../../windows/tutorial-availability-sets.md) of op meerdere [Azure-beschikbaarheidszones](./sap-ha-availability-zones.md). 

In de volgende afbeeldingen ziet u een installatie van twee Azure-Vm's voor de Data Base-Server. Zowel de Azure-Vm's van de database server als de eigen opslag zijn gekoppeld en zijn actief. In HADR is één data base-exemplaar op een van de virtuele machines van Azure de rol van het primaire exemplaar. Alle clients zijn verbonden met dit primaire exemplaar. Alle wijzigingen in database transacties worden lokaal opgeslagen in het Db2-transactie logboek. Omdat de transactie logboek records lokaal blijven bestaan, worden de records via TCP/IP overgebracht naar het data base-exemplaar op de tweede database server, de stand-by-server of de stand-by-instantie. Het standby-exemplaar werkt de lokale data base bij door de overgebrachte transactie logboek records door te sturen. Op deze manier wordt de stand-by-server gesynchroniseerd met de primaire server.

HADR is slechts een replicatie functionaliteit. Er is geen detectie van fouten en geen automatische overname-of failover-faciliteiten. Een overname of overdracht naar de stand-by-server moet hand matig worden geïnitieerd door een database beheerder. U kunt de functie Linux pacemaker-clustering gebruiken om een automatische overname en detectie van de fout te verzorgen. Pacemaker bewaakt de twee Data Base-Server exemplaren. Wanneer het primaire database server exemplaar vastloopt, initieert pacemaker een *automatische* HADR-overname door de stand-by-server. Pacemaker zorgt er ook voor dat het virtuele IP-adres wordt toegewezen aan de nieuwe primaire server.

![Overzicht van IBM Db2-hoge Beschik baarheid](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Als u SAP-toepassings servers verbinding wilt laten maken met een primaire data base, hebt u een virtuele-machinehost en een virtueel IP-adres nodig. In het geval van een failover maakt de SAP-toepassings servers verbinding met het nieuwe primaire data base-exemplaar. In een Azure-omgeving is [azure Load Balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) vereist voor het gebruik van een virtueel IP-adres in de manier die vereist is voor HADR van IBM Db2. 

De volgende afbeelding geeft een overzicht van een Maxi maal beschik bare installatie van een SAP-systeem op basis van een IBM Db2-data base, zodat u beter begrijpt hoe IBM Db2-LUW met HADR en pacemaker in een Maxi maal beschik bare SAP-systeem installatie past. Dit artikel behandelt alleen IBM Db2, maar bevat verwijzingen naar andere artikelen over het instellen van andere onderdelen van een SAP-systeem.

![Overzicht van de volledige Beschik baarheid van IBM DB2-hele omgeving](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>Overzicht op hoog niveau van de vereiste stappen
Als u een configuratie van IBM Db2 wilt implementeren, moet u de volgende stappen uitvoeren:

  + Plan uw omgeving.
  + Implementeer de Vm's.
  + Update SUSE Linux en bestands systemen configureren.
  + Pacemaker installeren en configureren.
  + Installeer [Maxi maal beschik bare NFS][nfs-ha].
  + Installeer [ASCS/ers op een afzonderlijk cluster][ascs-ha].
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
| Azure-omheining | Azure-omheining of SBD-omheining (sterk aanbevolen). Methode om te voor komen dat het gebruik van een hersenen wordt gesplitst. |
| SBD-VM | SBD-grootte voor virtuele machines, opslag, netwerk. |
| Azure Load Balancer | Gebruik van Basic of Standard (aanbevolen), test poort voor de Db2-data base (onze aanbeveling 62500) **test poort**. |
| Naamomzetting| Hoe naam omzetting werkt in de omgeving. DNS-service wordt sterk aanbevolen. Lokaal Hosts-bestand kan worden gebruikt. |
    
Zie [pacemaker instellen op SuSE Linux Enterprise Server in azure](./high-availability-guide-suse-pacemaker.md)voor meer informatie over Linux-pacemaker in Azure.

## <a name="deployment-on-suse-linux"></a>Implementatie op SUSE Linux

De resource agent voor IBM Db2 LUW is opgenomen in SUSE Linux Enterprise Server voor SAP-toepassingen. Voor de instellingen die in dit document worden beschreven, moet u SUSE Linux server voor SAP-toepassingen gebruiken. De Azure Marketplace bevat een installatie kopie voor SUSE Enter prise server voor SAP-toepassingen 12 die u kunt gebruiken om nieuwe virtuele machines van Azure te implementeren. Houd rekening met de verschillende ondersteunings-of service modellen die door SUSE worden aangeboden via de Azure Marketplace wanneer u een VM-installatie kopie kiest in de Azure VM Marketplace. 

### <a name="hosts-dns-updates"></a>Hosts: DNS-updates
Maak een lijst van alle hostnamen, met inbegrip van namen van virtuele hosts, en werk uw DNS-servers bij om het juiste IP-adres in te stellen voor omzetting van hostnamen. Als er geen DNS-server bestaat of als u geen DNS-vermeldingen kunt bijwerken en maken, moet u de lokale host-bestanden gebruiken van de afzonderlijke virtuele machines die deel nemen aan dit scenario. Als u vermeldingen voor host-bestanden gebruikt, moet u ervoor zorgen dat de vermeldingen worden toegepast op alle virtuele machines in de SAP-systeem omgeving. We raden u echter aan uw DNS te gebruiken die in het ideale geval wordt uitgebreid naar Azure


### <a name="manual-deployment"></a>Handmatige implementatie

Zorg ervoor dat het geselecteerde besturings systeem wordt ondersteund door IBM/SAP voor IBM Db2 LUW. De lijst met ondersteunde versies van besturings systemen voor Azure Vm's en Db2-releases is beschikbaar in SAP Note [1928533]. De lijst met versies van het besturings systeem per afzonderlijke Db2-versie is beschikbaar in de SAP-product beschikbaarheids matrix. We raden ten zeerste aan SLES 12 SP4 ten gevolge van Azure-gerelateerde prestatie verbeteringen in deze of latere versies van SUSE Linux.

1. Een resource groep maken of selecteren.
1. Maak of selecteer een virtueel netwerk en een subnet.
1. Maak een beschikbaarheidsset voor Azure of implementeer een beschikbaarheids zone.
    + Stel de maximale update domeinen in op 2 voor de beschikbaarheidsset.
1. Virtuele machine 1 maken.
    + Gebruik SLES voor SAP-installatie kopie in de Azure Marketplace.
    + Selecteer de beschik bare Azure-beschikbaarheidsset die u hebt gemaakt in stap 3 of selecteer beschikbaarheids zone.
1.  Maak de virtuele machine 2.
    + Gebruik SLES voor SAP-installatie kopie in de Azure Marketplace.
    + Selecteer de beschik baarheid van Azure die u hebt gemaakt in stap 3 of selecteer beschikbaarheids zone (niet dezelfde zone als in stap 3).
1. Voeg gegevens schijven toe aan de Vm's en controleer vervolgens de aanbeveling van een bestandssysteem installatie in het artikel [IBM Db2 Azure virtual machines DBMS-implementatie voor SAP-workload][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Het Pacemaker-cluster maken
    
Als u een basis-pacemaker-cluster wilt maken voor deze IBM Db2-server, raadpleegt [u pacemaker instellen op SuSE Linux Enterprise Server in azure][sles-pacemaker]. 

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

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Installatie hints voor het instellen van IBM Db2 LUW met HADR

Het primaire exemplaar van de IBM Db2 LUW-data base instellen:

- Gebruik de optie hoge Beschik baarheid of gedistribueerd.
- Installeer de SAP ASCS/ERS en het data base-exemplaar.
- Maak een back-up van de zojuist geïnstalleerde data base.


> [!IMPORTANT] 
> Noteer de "data base-communicatie poort" die tijdens de installatie is ingesteld. Dit moet hetzelfde poort nummer zijn voor beide data base-exemplaren

Voer de volgende stappen uit om de stand-by-database server in te stellen met behulp van de SAP-procedure voor het kopiëren van een systeem:

1. Selecteer de optie **kopiëren** van het systeem >   >  **gedistribueerd**  >  **Data Base-exemplaar** van het doel systeem.
1. Als Kopieer methode selecteert u **homo geen systeem** zodat u back-up kunt gebruiken om een back-up te herstellen op het stand-by-Server exemplaar.
1. Wanneer u de stap afsluiten hebt bereikt om de data base te herstellen voor een homogene systeem kopie, sluit u het installatie programma af. Zet de data base terug vanuit een back-up van de primaire host. Alle volgende installatie fasen zijn al uitgevoerd op de primaire database server.
1. Stel HADR in voor IBM Db2.

   > [!NOTE]
   > Voor installatie-en configuratie-informatie die specifiek is voor Azure en pacemaker: tijdens de installatie procedure via SAP software Provisioning Manager, is er een expliciete vraag over hoge Beschik baarheid voor IBM Db2 LUW:
   >+ Selecteer geen **IBM Db2-pureScale**.
   >+ Selecteer geen **IBM Tivoli-systeem automatisering installeren voor meerdere platformen**.
   >+ Selecteer geen **cluster configuratie bestanden genereren**.

   Wanneer u een SBD-apparaat voor Linux pacemaker gebruikt, stelt u de volgende para meters voor de Db2-HADR in:
   + Duur van HADR-peer venster (seconden) (HADR_PEER_WINDOW) = 300  
   + HADR time-outwaarde (HADR_TIMEOUT) = 60

   Wanneer u een Azure pacemaker omheinings agent gebruikt, stelt u de volgende para meters in:
   + Duur van HADR-peer venster (seconden) (HADR_PEER_WINDOW) = 900  
   + HADR time-outwaarde (HADR_TIMEOUT) = 60

We raden de voor gaande para meters aan op basis van initiële failover/overname-tests. Het is verplicht om te testen op de juiste functionaliteit van failover en overname met deze parameter instellingen. Omdat afzonderlijke configuraties kunnen variëren, moeten de para meters mogelijk worden aangepast. 

> [!IMPORTANT]
> Specifiek voor IBM Db2 met HADR-configuratie met normaal opstarten: het secundaire of standby-data base-exemplaar moet actief zijn voordat u het primaire data base-exemplaar kunt starten.

Voor demonstratie doeleinden en de procedures die in dit artikel worden beschreven, is de data base-SID **PTR**.

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR-controle
Nadat u HADR hebt geconfigureerd en de status is ingesteld op PEER en op de primaire en stand-by-knoop punten is aangesloten, voert u de volgende controle uit:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
# Database Member 0 -- Database PTR -- Active -- Up 1 days 01:51:38 -- Date 2019-02-06-15.35.28.505451
# 
#                             <b>HADR_ROLE = PRIMARY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 1
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.170561 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6137
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 13
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000025
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223713
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 374400
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#        STANDBY_RECV_REPLAY_GAP(bytes) = 0
#                      PRIMARY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#                      STANDBY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:40:25.000000 (1549467625)
#              READS_ON_STANDBY_ENABLED = N

#Secondary output:
# Database Member 0 -- Database PTR -- Standby -- Up 1 days 01:46:43 -- Date 2019-02-06-15.38.25.644168
# 
#                             <b>HADR_ROLE = STANDBY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 0
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.205067 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6186
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 5
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000023
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223725
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 372480
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#        STANDBY_RECV_REPLAY_GAP(bytes) = 155
#                      PRIMARY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#                      STANDBY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:43:19.000000 (1549467799)
#              READS_ON_STANDBY_ENABLED = N
</code></pre>



## <a name="db2-pacemaker-configuration"></a>Configuratie van Db2 pacemaker

Wanneer u pacemaker gebruikt voor automatische failover in het geval van een storing in een knoop punt, moet u de Db2-instanties en pacemaker dienovereenkomstig configureren. In deze sectie wordt dit type configuratie beschreven.

De volgende items worden voorafgegaan door een van beide:

- **[A]**: van toepassing op alle knoop punten
- **[1]**: alleen van toepassing op knoop punt 1 
- **[2]**: alleen van toepassing op knoop punt 2

**[A]** vereisten voor pacemaker-configuratie:
1. Sluit beide database servers met gebruikers-DB2 af \<sid> met db2stop.
1. Wijzig de shell-omgeving voor de DB2- \<sid> gebruiker in */bin/ksh*. U wordt aangeraden het YaST-hulp programma te gebruiken. 


### <a name="pacemaker-configuration"></a>Pacemaker-configuratie

> [!IMPORTANT]
> Recente tests hebben getoonde situaties, waarbij netcat niet meer reageert op aanvragen als gevolg van achterstand en de beperking van het verwerken van slechts één verbinding. De netcat-resource stopt met Luis teren naar de Azure Load Balancer-aanvragen en het zwevende IP-adres is niet meer beschikbaar.  
> Voor bestaande pacemaker-clusters wordt geadviseerd in de laatste Vervang netcat met socat. Momenteel raden we u aan Azure-lb resource agent te gebruiken, die deel uitmaakt van pakket resource-agents, met de volgende pakket versie vereisten:
> - Voor SLES 12 SP4/SP5 moet de versie ten minste resource-agents-4.3.018. a7fb5035-3.30.1 zijn.  
> - Voor SLES 15/15 SP1 moet de versie ten minste resource-agents-4.3.0184.6 ee15eb2-4.13.1 zijn.  
>
> Houd er rekening mee dat voor de wijziging korte uitval tijd nodig is.  
> Voor bestaande pacemaker-clusters geldt dat als de configuratie al is gewijzigd in het gebruik van socat, zoals beschreven in [Azure Load-Balancer-detectie beveiliging](https://www.suse.com/support/kb/doc/?id=7024128), u niet onmiddellijk over de resource-agent van Azure lb hoeft te scha kelen.

**[1]** IBM Db2 HADR-specifieke pacemaker-configuratie:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]** IBM Db2-resources maken:
<pre><code># Replace **bold strings** with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virtual IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> azure-lb port=<b>62500</b>

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]** IBM Db2-resources starten:
* Pacemaker uit de onderhouds modus plaatsen.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]** zorg ervoor dat de cluster status OK is en dat alle resources zijn gestart. Het is niet belang rijk op welke knoop punten de resources worden uitgevoerd.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 knoop punten geconfigureerd
# <a name="5-resources-configured"></a>5 geconfigureerde resources

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [azibmdb01 azibmdb02]

# <a name="full-list-of-resources"></a>Volledige lijst met resources:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-SBD (stonith: External/SBD): gestart azibmdb02
#  <a name="resource-group-g_ip_db2ptr_ptr"></a>Resource groep: g_ip_db2ptr_PTR
#      <a name="rsc_ip_db2ptr_ptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR (OCF:: heartbeat: IPaddr2): gestart azibmdb02
#      <a name="rsc_nc_db2ptr_ptr--ocfheartbeatazure-lb------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (OCF:: heartbeat: Azure-lb): gestart azibmdb02
#  <a name="masterslave-set-msl_db2_db2ptr_ptr-rsc_db2_db2ptr_ptr"></a>Hoofd-/slave-set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Stramienen: [azibmdb02]
#      <a name="slaves--azibmdb01-"></a>Slaves: [azibmdb01]
</pre>

> [!IMPORTANT]
> U moet de geclusterde Db2-instantie pacemaker beheren met behulp van pacemaker-hulpprogram ma's. Als u DB2-opdrachten gebruikt, zoals db2stop, detecteert pacemaker de actie als een storing van de resource. Als u onderhoud uitvoert, kunt u de knoop punten of resources in de onderhouds modus plaatsen. Pacemaker suspendeert bewakings bronnen en u kunt vervolgens normale DB2-beheer opdrachten gebruiken.


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
 
1. Meld u aan bij de primaire toepassings server van het J2EE-exemplaar en voer de volgende handelingen uit:   `sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh`
1. Kies in het linkerdeel venster de optie **beveiligings archief**.
1. Kies in het rechterdeel venster de sleutel JDBC/pool/ \<SAPSID> /URL.
1. Wijzig de hostnaam in de JDBC-URL in de naam van de virtuele host.
     `jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0`
1. Selecteer **Toevoegen**.
1. Als u de wijzigingen wilt opslaan, selecteert u het schijf pictogram in de linkerbovenhoek.
1. Sluit het configuratie programma.
1. Start het Java-exemplaar opnieuw.

## <a name="configure-log-archiving-for-hadr-setup"></a>Logboek Archivering configureren voor HADR Setup
Als u de instelling voor het archiveren van de Db2-logboeken voor HADR wilt configureren, raden we u aan om zowel de primaire als de stand-by-data base zodanig te configureren dat het automatisch ophalen van logboeken van alle locatie van logboek archief is. Zowel de primaire als de stand-by-data base moeten logboek archiefbestanden kunnen ophalen van alle logboeken van de logbestanden waartoe een van de data base-exemplaren logboek bestanden kan archiveren. 

Het archiveren van het logboek wordt alleen uitgevoerd door de primaire data base. Als u de HADR-rollen van de database servers wijzigt of als er een fout optreedt, is de nieuwe primaire data base verantwoordelijk voor het archiveren van Logboeken. Als u meerdere archief locaties voor logboek registratie hebt ingesteld, kunnen uw logboeken twee keer worden gearchiveerd. In het geval van een lokale of externe achterstand moet u mogelijk ook hand matig de gearchiveerde logboeken van de oude primaire server kopiëren naar de actieve logboek locatie van de nieuwe primaire server.

U kunt het beste een algemene NFS-share configureren waarbij logboeken worden geschreven van beide knoop punten. De NFS-share moet Maxi maal beschikbaar zijn. 

U kunt bestaande Maxi maal beschik bare NFS-shares gebruiken voor trans porten of profielmap. Zie voor meer informatie:

- [Hoge Beschik baarheid voor NFS op Azure Vm's op SUSE Linux Enterprise Server][nfs-ha] 
- [Hoge Beschik baarheid voor SAP NetWeaver op Azure Vm's op SUSE Linux Enterprise Server met Azure NetApp Files voor SAP-toepassingen](./high-availability-guide-suse-netapp-files.md)
- [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md) (voor het maken van NFS-shares)


## <a name="test-the-cluster-setup"></a>De Cluster installatie testen

In deze sectie wordt beschreven hoe u de Setup van de Db2-HADR kunt testen. Bij *elke test wordt ervan uitgegaan dat u bent aangemeld als hoofdmap* van de gebruiker en de primaire IBM Db2-machine wordt uitgevoerd op de virtuele *azibmdb01* .

De initiële status voor alle test cases wordt hier uitgelegd: (crm_mon-r-of CRM-status)

- de **CRM-status** is een moment opname van de pacemaker-status tijdens de uitvoerings tijd 
- **crm_mon-r** doorlopende uitvoer van pacemaker-status

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

De oorspronkelijke status in een SAP-systeem wordt beschreven in trans actie DBACOCKPIT > configuratie > overzicht, zoals wordt weer gegeven in de volgende afbeelding:

![DBACockpit-pre-migratie](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>De overname van IBM Db2 testen


> [!IMPORTANT] 
> Voordat u met de test begint, moet u ervoor zorgen dat:
> * Pacemaker heeft geen mislukte acties (CRM-status).
> * Er zijn geen locatie beperkingen (resten van de migratie test)
> * De synchronisatie van IBM Db2 HADR werkt. Controleren met gebruikers-DB2\<sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migreer het knoop punt waarop de primaire Db2-data base wordt uitgevoerd door de volgende opdracht uit te voeren:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

Nadat de migratie is voltooid, ziet de CRM-status uitvoer er als volgt uit:
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

De oorspronkelijke status in een SAP-systeem wordt beschreven in trans actie DBACOCKPIT > configuratie > overzicht, zoals wordt weer gegeven in de volgende afbeelding:

![DBACockpit-na migratie](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

Resource migratie met "CRM resource migrate" maakt locatie beperkingen. Locatie beperkingen moeten worden verwijderd. Als locatie beperkingen niet worden verwijderd, kan de resource geen failback uitvoeren of kunnen er ongewenste overname worden uitgevoerd. 

Migreer de resource terug naar *azibmdb01* en wis de locatie beperkingen
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **CRM-resource migreren \<res_name> \<host> :** maakt locatie beperkingen en kan problemen veroorzaken met overname
- **CRM-bron \<res_name> wissen**: Hiermee worden de beperkingen voor locaties gewist
- **CRM-bron \<res_name> opschonen**: Hiermee worden alle fouten van de resource gewist

### <a name="test-the-fencing-agent"></a>De omheinings agent testen

In dit geval testen we de SBD-omheining, die we het beste doen wanneer u SUSE Linux gebruikt.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

Cluster knooppunt *azibmdb01* moet opnieuw worden opgestart. De primaire HADR-rol van IBM Db2 wordt verplaatst naar *azibmdb02*. Wanneer *azibmdb01* weer online is, gaat de Db2-instantie over naar de rol van een secundaire data base-instantie. 

Als de pacemaker-service niet automatisch wordt gestart voor de eerste primaire, start u deze hand matig met de volgende opties:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Een hand matige overname testen

U kunt een hand matige overname testen door de pacemaker-service op het *azibmdb01* -knoop punt te stoppen:
<pre><code>service pacemaker stop</code></pre>

status op *azibmdb02*
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

Na de failover kunt u de service opnieuw starten op *azibmdb01*.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Het Db2-proces beëindigen op het knoop punt waarop de primaire HADR-data base wordt uitgevoerd

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

Het Db2-exemplaar gaat mislukken en pacemaker rapporteert de volgende status:

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

Pacemaker start het primaire Db2-data base-exemplaar opnieuw op hetzelfde knoop punt, of er wordt een failover uitgevoerd naar het knoop punt waarop het secundaire data base-exemplaar wordt uitgevoerd en er wordt een fout gemeld.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Het Db2-proces beëindigen op het knoop punt waarop het secundaire data base-exemplaar wordt uitgevoerd

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

Het knoop punt wordt weer gegeven als mislukt en er wordt een fout gerapporteerd
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

Het Db2-exemplaar wordt opnieuw gestart in de secundaire rol waaraan het is toegewezen.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>DATA base stoppen via db2stop forceren op het knoop punt waarop het primaire exemplaar van de HADR-data base wordt uitgevoerd

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Als User DB2 \<sid> Execute opdracht db2stop Force:
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

Fout gedetecteerd
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

Het secundaire data base-exemplaar van de Db2-HADR is gepromoveerd tot de primaire rol
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Een crash-VM met opnieuw opstarten op het knoop punt waarop het HADR-exemplaar van de primaire data base wordt uitgevoerd

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Pacemaker bevordert het secundaire exemplaar naar de rol van het primaire exemplaar. De oude primaire instantie wordt verplaatst naar de secundaire rol na de virtuele machine en alle services worden volledig hersteld na het opnieuw opstarten van de VM:

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>De VM met de HADR Primary data base-instantie crashen met ' Stop '

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

In dat geval detecteert pacemaker dat het knoop punt waarop het primaire data base-exemplaar wordt uitgevoerd, niet reageert.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

De volgende stap is om te controleren op een situatie met *Split-hersenen* . Nadat het nagelaten knoop punt heeft vastgesteld dat het knoop punt waarop het primaire data base-exemplaar voor het laatst werd uitgevoerd, niet beschikbaar is, wordt er een failover van resources uitgevoerd.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


In het geval van een onderbreking van het knoop punt moet het knoop punt waarvoor een fout is opgetreden opnieuw worden gestart via Azure-beheer hulpprogramma's (in de Azure Portal, Power shell of de Azure CLI). Nadat het knoop punt met de fout weer online is, wordt het Db2-exemplaar gestart in de secundaire rol.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>Volgende stappen
- [Architectuur en scenario's met hoge Beschik baarheid voor SAP net-Weaver](./sap-high-availability-architecture-scenarios.md)
- [Pacemaker instellen voor SUSE Linux Enterprise Server in azure](./high-availability-guide-suse-pacemaker.md)

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP4/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist
