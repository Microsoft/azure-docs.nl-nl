---
title: Linux-prestatiehulpprogramma's
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het gebruik van Linux-prestatie hulpprogramma's voor het oplossen van veelvoorkomende problemen bij het gebruik van Azure Kubernetes service (AKS).
services: container-service
author: alexeldeib
ms.service: container-service
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: aleldeib
ms.openlocfilehash: 74f65780594c7bc938ed6d59437473c4363e5848
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90982032"
---
# <a name="linux-performance-troubleshooting"></a>Problemen met Linux-prestaties oplossen

Bron uitputting op Linux-machines is een veelvoorkomend probleem en kan zich in een groot aantal symptomen voordoen. Dit document bevat een overzicht van de hulpprogram ma's die beschikbaar zijn om te helpen bij het vaststellen van dergelijke problemen.

Veel van deze hulpprogram ma's accepteren een interval voor het produceren van uitvoer. Met deze uitvoer indeling worden herkennen patronen doorgaans veel eenvoudiger gemaakt. Wanneer u dit hebt geaccepteerd, wordt het aanroepen van het voor beeld meegenomen `[interval]` .

Veel van deze hulpprogram ma's hebben een uitgebreide geschiedenis en een breed scala aan configuratie opties. Deze pagina bevat slechts een eenvoudige subset van aanroepen om veelvoorkomende problemen te markeren. De canonieke bron van informatie is altijd de referentie documentatie voor elk bepaald hulp programma. Deze documentatie is veel uitgebreider dan u hier vindt.

## <a name="guidance"></a>Hulp

Zorg voor een systematische aanpak bij het onderzoeken van prestatie problemen. Er zijn twee veelvoorkomende benaderingen (gebruik, intensiteit, fouten) en rood (frequentie, fouten, duur). ROOD wordt doorgaans gebruikt in de context van services voor bewaking op basis van aanvragen. GEBRUIK wordt meestal gebruikt voor bewakings bronnen: voor elke bron op een machine, het gebruik, de verzadiging en de fouten bewaken. De vier belangrijkste soorten resources op elke computer zijn CPU, geheugen, schijf en netwerk. Hoog gebruik, verzadiging of fout tarieven voor een van deze resources duiden op een mogelijk probleem met het systeem. Als er een probleem optreedt, onderzoekt u de hoofd oorzaak: Waarom is de schijf-i/o-latentie hoog? Zijn de schijven of de SKU van de virtuele machine beperkt? Welke processen schrijven naar de apparaten en naar welke bestanden?

Enkele voor beelden van veelvoorkomende problemen en indica toren om ze te diagnosticeren:
- IOPS-beperking: gebruik iostat gebruiken om IOPS per apparaat te meten. Zorg ervoor dat er geen afzonderlijke schijven meer zijn dan de limiet en dat de som voor alle schijven kleiner is dan de limiet voor de virtuele machine.
- Bandbreedte regeling: gebruik iostat gebruiken als voor IOPS, maar lees/schrijf doorvoer meten. Zorg ervoor dat zowel per apparaat als geaggregeerde door Voer lager zijn dan de bandbreedte limieten.
- SNAT-uitputting: dit kan worden gemanifesteerd als hoge actieve (uitgaande) verbindingen in SAR. 
- Pakket verlies: dit kan worden gemeten met behulp van een aantal TCP-verzen dingen ten opzichte van het aantal verzonden/ontvangen. Beide `sar` en `netstat` kunnen deze gegevens weer geven.

## <a name="general"></a>Algemeen

Deze hulpprogram ma's zijn algemeen gebruik en gelden voor basis informatie over het systeem. Het is een goed uitgangs punt voor verder onderzoek.

### <a name="uptime"></a>systeem

```
$ uptime
 19:32:33 up 17 days, 12:36,  0 users,  load average: 0.21, 0.77, 0.69
```

uptime zorgt voor de uptime van het systeem en de gemiddelde belasting van 1, 5 en 15 minuten. Deze belasting gemiddelden komen ongeveer overeen met threads die werken of wachten op het volt ooien van een nood werk. In absolute deze getallen kan het lastig zijn om te interpreteren, maar na verloop van tijd kunnen ze nuttige informatie vertellen:

- gemiddeld 1 minuut > 5 minuten gemiddelde betekent dat de belasting toeneemt.
- gemiddeld 1 minuut < 5 minuten gemiddelde betekent dat de belasting afneemt.

de uptime kan ook worden verlicht waarom er geen informatie beschikbaar is: het probleem is mogelijk afzonderlijk opgelost of opnieuw opgestart voordat de gebruiker toegang tot de computer heeft.

Het laden van gemiddelden hoger dan het aantal beschik bare CPU-threads kan duiden op een prestatie probleem met een bepaalde werk belasting.

### <a name="dmesg"></a>dmesg

```
$ dmesg | tail 
$ dmesg --level=err | tail
```

dmesg dumpt de kernel-buffer. Gebeurtenissen zoals OOMKill voegen een vermelding toe aan de kernel-buffer. Het vinden van een OOMKill of andere berichten over bron uitputting in dmesg-Logboeken is een sterke indicatie van een probleem.

### <a name="top"></a>top

```
$ top
Tasks: 249 total,   1 running, 158 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.2 us,  1.3 sy,  0.0 ni, 95.4 id,  1.0 wa,  0.0 hi,  0.2 si,  0.0 st
KiB Mem : 65949064 total, 43415136 free,  2349328 used, 20184600 buff/cache
KiB Swap:        0 total,        0 free,        0 used. 62739060 avail Mem

   PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
116004 root      20   0  144400  41124  27028 S  11.8  0.1 248:45.45 coredns
  4503 root      20   0 1677980 167964  89464 S   5.9  0.3   1326:25 kubelet
     1 root      20   0  120212   6404   4044 S   0.0  0.0  48:20.38 systemd
     ...
```

`top` biedt een breed overzicht van de huidige systeem status. De headers bieden enkele nuttige statistische informatie:

- status van taken: uitvoeren, in slaap stand, gestopt.
- CPU-gebruik: in dit geval wordt meestal niet-actieve tijd weer gegeven.
- totaal, vrij en gebruikt systeem geheugen.

`top` Er kunnen processen met een korte levens duur worden gemist; alternatieven als `htop` en `atop` bieden vergelijk bare interfaces tijdens het oplossen van enkele van deze tekortkomingen.

## <a name="cpu"></a>CPU

Deze hulpprogram ma's bieden informatie over CPU-gebruik. Dit is met name handig bij Rolling uitvoer, waarbij patronen gemakkelijk te herkennen zijn.

### <a name="mpstat"></a>mpstat

```
$ mpstat -P ALL [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

19:49:03     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
19:49:04     all    1.01    0.00    0.63    2.14    0.00    0.13    0.00    0.00    0.00   96.11
19:49:04       0    1.01    0.00    1.01   17.17    0.00    0.00    0.00    0.00    0.00   80.81
19:49:04       1    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
19:49:04       2    1.01    0.00    0.00    0.00    0.00    1.01    0.00    0.00    0.00   97.98
19:49:04       3    0.00    0.00    0.99    0.00    0.00    0.99    0.00    0.00    0.00   98.02
19:49:04       4    1.98    0.00    1.98    0.00    0.00    0.00    0.00    0.00    0.00   96.04
19:49:04       5    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       6    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       7    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
```

`mpstat` Hiermee worden vergelijk bare CPU-informatie naar boven afgedrukt, maar dit wordt onderverdeeld per CPU-thread. Alle kernen tegelijk bekijken kan nuttig zijn bij het detecteren van het CPU-gebruik met een zeer groot aantal, bijvoorbeeld wanneer een toepassing met één thread één kern op 100% gebruik gebruikt. Dit probleem kan lastiger zijn bij het samen voegen van alle Cpu's in het systeem.

### <a name="vmstat"></a>vmstat

```
$ vmstat [interval]
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 43300372 545716 19691456    0    0     3    50    3    3  2  1 95  1  0
```

`vmstat` bevat vergelijk bare informatie `mpstat` en het `top` inventariseren van het aantal processen dat wacht op CPU (r-kolom), geheugen statistieken en percentage CPU-tijd die aan elke werk status worden besteed.

## <a name="memory"></a>Geheugen

Het geheugen is zeer belang rijk en gelukkig eenvoudig, te traceren resource. Sommige hulpprogram ma's kunnen zowel de CPU als het geheugen rapporteren, zoals `vmstat` . Hulpprogram ma's, zoals `free` mogelijk, zijn nog steeds handig voor snelle fout opsporing.

### <a name="free"></a>macro's

```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:          64403        2338       42485           1       19579       61223
Swap:             0           0           0
```

`free` geeft basis informatie over het totale geheugen en het gebruikte en vrije geheugen. `vmstat` is mogelijk handiger, zelfs bij een elementaire geheugen analyse door de mogelijkheid om uitvoer te leveren.

## <a name="disk"></a>Schijf

Deze hulpprogram ma's meten schijf-IOPS, wacht rijen en totale door voer. 

### <a name="iostat"></a>iostat

```
$ iostat -xy [interval] [count]
$ iostat -xy 1 1
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           3.42    0.00    2.92    1.90    0.00   91.76

Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
loop0             0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sdb               0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sda               0.00    56.00    0.00   65.00     0.00   504.00    15.51     0.01    3.02    0.00    3.02   0.12   0.80
scd0              0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
```

`iostat` biedt uitgebreide inzichten in het schijf gebruik. Deze aanroep geeft `-x` uitgebreide statistieken, `-y` om de eerste uitvoer van het afdruk systeem na het opstarten over te slaan, en `1 1` om op te geven dat het interval van 1 seconde moet eindigen na één uitvoer blok. 

`iostat` beschrijft veel nuttige statistieken:

- `r/s` en `w/s` zijn Lees bewerkingen per seconde. De som van deze waarden is IOPS.
- `rkB/s` en `wkB/s` zijn kilo bytes per seconde voor lezen/schrijven. De som van deze waarden is door voer.
- `await` is de gemiddelde iowait tijd in milliseconden voor aanvragen in de wachtrij.
- `avgqu-sz` is de gemiddelde wachtrij grootte boven het gegeven interval.

Op een virtuele Azure-machine:

- de som van `r/s` en `w/s` voor een afzonderlijk blok apparaat mag niet hoger zijn dan de SKU-limieten van die schijf.
- de som van `rkB/s` en `wkB/s`  voor een afzonderlijk blok apparaat mag de SKU-limieten van die schijf niet overschrijden
- de som van `r/s` en `w/s` voor alle blok apparaten overschrijdt mogelijk niet de limieten voor de VM-SKU.
- de som van  `rkB/s` en ' wkB/s voor alle blok apparaten overschrijdt mogelijk niet de limieten voor de VM-SKU.

Houd er rekening mee dat de schijf van het besturings systeem telt als een beheerde schijf van de kleinste SKU die overeenkomt met de capaciteit. Een 1024GB-besturingssysteem schijf komt bijvoorbeeld overeen met een P30-schijf. Tijdelijke besturingssysteem schijven en tijdelijke schijven hebben geen afzonderlijke schijf limieten. ze worden alleen beperkt door de volledige VM-limieten.

Niet-nulwaarde waarden van await of avgqu-SZ zijn ook goede indica toren voor i/o-conflicten.

## <a name="network"></a>Netwerk

Deze hulpprogram ma's meten netwerk statistieken, zoals door Voer, storingen en gebruik. Een diep gaande analyse kan leiden tot gedetailleerde TCP-statistieken over congestie en verloren pakketten.

### <a name="sar"></a>SAR

```
$ sar -n DEV [interval]
22:36:57        IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
22:36:58      docker0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azv604be75d832      1.00      9.00      0.06      1.04      0.00      0.00      0.00      0.00
22:36:58       azure0     68.00     79.00     27.79     52.79      0.00      0.00      0.00      0.00
22:36:58    azv4a8e7704a5b    202.00    207.00     37.51     21.86      0.00      0.00      0.00      0.00
22:36:58    azve83c28f6d1c     21.00     30.00     24.12      4.11      0.00      0.00      0.00      0.00
22:36:58         eth0    314.00    321.00     70.87    163.28      0.00      0.00      0.00      0.00
22:36:58    azva3128390bff     12.00     20.00      1.14      2.29      0.00      0.00      0.00      0.00
22:36:58    azvf46c95ddea3     10.00     18.00     31.47      1.36      0.00      0.00      0.00      0.00
22:36:58       enP1s1     74.00    374.00     29.36    166.94      0.00      0.00      0.00      0.00
22:36:58           lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azvdbf16b0b2fc      9.00     19.00      3.36      1.18      0.00      0.00      0.00      0.00
```

`sar` is een krachtig hulp middel voor een breed scala aan analyses. Hoewel in dit voor beeld de mogelijkheid van netwerk statistieken wordt gebruikt, is deze even krachtig voor het meten van CPU-en geheugen gebruik. In dit voor beeld wordt `sar` met `-n` vlag aangeroepen om het `DEV` sleutel woord (netwerk apparaat) op te geven, waarbij de netwerk doorvoer per apparaat wordt weer gegeven.

- De som van `rxKb/s` en `txKb/s` is de totale door Voer voor een bepaald apparaat. Wanneer deze waarde de limiet voor de ingerichte Azure-NIC overschrijdt, hebben werk belastingen op de computer een verhoogde netwerk latentie.
- `%ifutil` meet gebruik voor een bepaald apparaat. Omdat deze waarde 100% benadert, hebben werk belastingen een verhoogde netwerk latentie.

```
$ sar -n TCP,ETCP [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

22:50:08     active/s passive/s    iseg/s    oseg/s
22:50:09         2.00      0.00     19.00     24.00

22:50:08     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
22:50:09         0.00      0.00      0.00      0.00      0.00

Average:     active/s passive/s    iseg/s    oseg/s
Average:         2.00      0.00     19.00     24.00

Average:     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
Average:         0.00      0.00      0.00      0.00      0.00
```

Met deze aanroep van `sar` worden de `TCP,ETCP` tref woorden gebruikt voor het onderzoeken van TCP-verbindingen. De derde kolom van de laatste rij, ' hertrans ', is het aantal TCP-retransmits per seconde. Hoge waarden voor dit veld duiden op een onbetrouwbare netwerk verbinding. In de eerste en derde rij betekent "actief" dat een verbinding afkomstig is van het lokale apparaat, terwijl "externe" wijst op een binnenkomende verbinding.  Een veelvoorkomend probleem op Azure is een SNAT-poort uitputting, waarmee `sar` kan worden gedetecteerd. De SNAT-poort ontdubbeling wordt als hoge ' Active '-waarden gemanifesteerd, omdat het probleem wordt veroorzaakt door een hoog aantal uitgaande, lokaal geactiveerde TCP-verbindingen.

Als `sar` er een interval wordt gebruikt, wordt de uitvoer uitgevoerd en worden vervolgens definitieve rijen met uitvoer afgedrukt met de gemiddelde resultaten van de aanroep.

### <a name="netstat"></a>netstat

```
$ netstat -s
Ip:
    71046295 total packets received
    78 forwarded
    0 incoming packets discarded
    71046066 incoming packets delivered
    83774622 requests sent out
    40 outgoing packets dropped
Icmp:
    103 ICMP messages received
    0 input ICMP message failed.
    ICMP input histogram:
        destination unreachable: 103
    412802 ICMP messages sent
    0 ICMP messages failed
    ICMP output histogram:
        destination unreachable: 412802
IcmpMsg:
        InType3: 103
        OutType3: 412802
Tcp:
    11487089 active connections openings
    592 passive connection openings
    1137 failed connection attempts
    404 connection resets received
    17 connections established
    70880911 segments received
    95242567 segments send out
    176658 segments retransmited
    3 bad segments received.
    163295 resets sent
Udp:
    164968 packets received
    84 packets to unknown port received.
    0 packet receive errors
    165082 packets sent
UdpLite:
TcpExt:
    5 resets received for embryonic SYN_RECV sockets
    1670559 TCP sockets finished time wait in fast timer
    95 packets rejects in established connections because of timestamp
    756870 delayed acks sent
    2236 delayed acks further delayed because of locked socket
    Quick ack mode was activated 479 times
    11983969 packet headers predicted
    25061447 acknowledgments not containing data payload received
    5596263 predicted acknowledgments
    19 times recovered from packet loss by selective acknowledgements
    Detected reordering 114 times using SACK
    Detected reordering 4 times using time stamp
    5 congestion windows fully recovered without slow start
    1 congestion windows partially recovered using Hoe heuristic
    5 congestion windows recovered without slow start by DSACK
    111 congestion windows recovered without slow start after partial ack
    73 fast retransmits
    26 retransmits in slow start
    311 other TCP timeouts
    TCPLossProbes: 198845
    TCPLossProbeRecovery: 147
    480 DSACKs sent for old packets
    175310 DSACKs received
    316 connections reset due to unexpected data
    272 connections reset due to early user close
    5 connections aborted due to timeout
    TCPDSACKIgnoredNoUndo: 8498
    TCPSpuriousRTOs: 1
    TCPSackShifted: 3
    TCPSackMerged: 9
    TCPSackShiftFallback: 177
    IPReversePathFilter: 4
    TCPRcvCoalesce: 1501457
    TCPOFOQueue: 9898
    TCPChallengeACK: 342
    TCPSYNChallenge: 3
    TCPSpuriousRtxHostQueues: 17
    TCPAutoCorking: 2315642
    TCPFromZeroWindowAdv: 483
    TCPToZeroWindowAdv: 483
    TCPWantZeroWindowAdv: 115
    TCPSynRetrans: 885
    TCPOrigDataSent: 51140171
    TCPHystartTrainDetect: 349
    TCPHystartTrainCwnd: 7045
    TCPHystartDelayDetect: 26
    TCPHystartDelayCwnd: 862
    TCPACKSkippedPAWS: 3
    TCPACKSkippedSeq: 4
    TCPKeepAlive: 62517
IpExt:
    InOctets: 36416951539
    OutOctets: 41520580596
    InNoECTPkts: 86631440
    InECT0Pkts: 14
```

`netstat` kan een groot aantal netwerk statistieken introspect, die hier wordt aangeroepen met een samenvattings uitvoer. Er zijn hier veel nuttige velden, afhankelijk van het probleem. Een handig veld in de TCP-sectie is mislukte Verbindings pogingen. Dit kan een indicatie zijn van de SNAT-poort afvoer of andere problemen met het maken van uitgaande verbindingen. Een hoog tempo van opnieuw verzonden segmenten (ook onder het TCP-gedeelte) kan duiden op problemen met pakket levering. 
