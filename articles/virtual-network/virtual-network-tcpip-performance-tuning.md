---
title: TCP/IP-prestaties afstemmen voor Azure-Vm's | Microsoft Docs
description: Leer verschillende algemene methoden voor het afstemmen van TCP/IP-prestaties en hun relatie met virtuele Azure-machines.
services: virtual-network
documentationcenter: na
author: rimayber
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: rimayber
ms.reviewer: dgoddard, stegag, steveesp, minale, btalb, prachank
ms.openlocfilehash: 10fae563f80a4d77e3f4bb1d5ca809a4130b050a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558845"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>TCP/IP-prestaties afstemmen voor Azure-Vm's

In dit artikel worden veelvoorkomende technieken voor het afstemmen van TCP/IP-prestaties en enkele dingen beschreven waarmee u rekening moet houden wanneer u deze gebruikt voor virtuele machines die worden uitgevoerd op Azure. Het bevat een basis overzicht van de technieken en ontdek hoe deze kunnen worden afgestemd.

## <a name="common-tcpip-tuning-techniques"></a>Algemene methoden voor het afstemmen van TCP/IP

### <a name="mtu-fragmentation-and-large-send-offload"></a>MTU, fragmentatie en grote verzendings-offload

#### <a name="mtu"></a>MTU

De maximale verzend eenheid (MTU) is het grootste frame (pakket), opgegeven in bytes, dat via een netwerk interface kan worden verzonden. De MTU is een Configureer bare instelling. De standaard-MTU die wordt gebruikt op virtuele machines van Azure en de standaard instelling op de meeste netwerk apparaten wereld wijd, is 1.500 bytes.

#### <a name="fragmentation"></a>Fragmentatie

Fragmentatie treedt op wanneer een pakket wordt verzonden dat de MTU van een netwerk interface overschrijdt. De TCP/IP-stack verbreekt het pakket in kleinere delen (fragmenten) die voldoen aan de MTU van de interface. Fragmentatie vindt plaats op de IP-laag en is onafhankelijk van het onderliggende protocol (zoals TCP). Wanneer een 2.000-byte-pakket wordt verzonden via een netwerk interface met een MTU van 1.500, wordt het pakket uitgesplitst in een pakket van 1 1.500 bytes en een pakket met 1 500 bytes.

Netwerk apparaten in het pad tussen een bron en doel kunnen pakketten verwijderen die de MTU overschrijden of het pakket fragmenteren in kleinere delen.

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>De bit Don't fragment in een IP-pakket

De bit Don't fragment (DF) is een vlag in de IP-protocol header. De VG-bit geeft aan dat netwerk apparaten op het pad tussen de afzender en ontvanger het pakket niet moeten fragmenteren. Deze bit kan om verschillende redenen worden ingesteld. (Zie de sectie Path MTU Discovery in dit artikel voor een voor beeld.) Wanneer een netwerk apparaat een pakket ontvangt met de bit Don't fragment, en dat pakket de interface MTU van het apparaat overschrijdt, is het standaard gedrag voor het apparaat om het pakket neer te zetten. Het apparaat verzendt een ICMP-fragmentatie vereist bericht terug naar de oorspronkelijke bron van het pakket.

#### <a name="performance-implications-of-fragmentation"></a>Prestatie implicaties van fragmentatie

Fragmentatie kan negatieve gevolgen voor de prestaties hebben. Een van de belangrijkste redenen voor het effect op de prestaties is de impact van de CPU/het geheugen van de fragmentatie en het opnieuw samen stellen van pakketten. Wanneer een netwerk apparaat een pakket moet fragmenteren, moeten er CPU/geheugen bronnen worden toegewezen om fragmentatie uit te voeren.

Hetzelfde gebeurt wanneer het pakket opnieuw is geassembleerd. Het netwerk apparaat moet alle fragmenten opslaan totdat deze worden ontvangen zodat ze opnieuw kunnen worden geassembleerd in het oorspronkelijke pakket. Dit proces van fragmentatie en opnieuw samen stellen kan ook een latentie veroorzaken.

De andere mogelijke negatieve prestatie implicatie van fragmentatie is dat gefragmenteerde pakketten niet in de juiste volg orde arriveren. Wanneer pakketten niet in de juiste volg orde worden ontvangen, kunnen sommige typen netwerk apparaten deze verwijderen. Als dat het geval is, moet het hele pakket opnieuw worden verzonden.

Fragmenten worden doorgaans verwijderd door beveiligings apparaten als netwerk firewalls of wanneer de ontvangst buffers van een netwerk apparaat worden uitgeput. Wanneer de ontvangst buffers van een netwerk apparaat worden uitgeput, probeert een netwerk apparaat een gefragmenteerd pakket opnieuw te assembleren, maar beschikt het niet over de resources om het pakket op te slaan en uit te voeren.

Fragmentatie kan worden gezien als een negatieve bewerking, maar de ondersteuning voor fragmentatie is nood zakelijk wanneer u verschillende netwerken via internet verbindt.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Voor delen en gevolgen van het wijzigen van de MTU

Over het algemeen kunt u een efficiëntere netwerk maken door de MTU te verhogen. Elk pakket dat wordt verzonden, bevat header-informatie die wordt toegevoegd aan het oorspronkelijke pakket. Wanneer fragmentatie meer pakketten maakt, is er meer header overhead en is het netwerk minder efficiënt.

Hier volgt een voorbeeld. De grootte van de Ethernet-header is 14 bytes plus een frame controle reeks van 4 bytes om consistentie van frames te garanderen. Als 1 2.000-bytes pakket wordt verzonden, worden er 18 bytes aan Ethernet-overhead toegevoegd op het netwerk. Als het pakket is gefragmenteerd in een 1.500-byte-pakket en een 500-byte-pakket, heeft elk pakket 18 bytes aan Ethernet-header, een totaal van 36 bytes.

Denk eraan dat het verhogen van de MTU niet noodzakelijkerwijs een efficiëntere netwerk kan maken. Als een toepassing alleen 500-byte pakketten verzendt, bestaat dezelfde header overhead of de MTU 1.500 bytes of 9.000 bytes is. Het netwerk zal alleen efficiënter worden als er grotere pakket grootten worden gebruikt die door de MTU worden beïnvloed.

#### <a name="azure-and-vm-mtu"></a>Azure-en VM-MTU

De standaard-MTU voor Azure Vm's is 1.500 bytes. De Azure Virtual Network stack probeert een pakket op 1.400 bytes te fragmenteren.

Houd er rekening mee dat de Virtual Network stack niet inherent inefficiënt is omdat deze pakketten bij 1.400 bytes fragmenteert, zelfs als Vm's een MTU van 1.500 hebben. Een groot percentage netwerk pakketten is veel kleiner dan 1.400 of 1.500 bytes.

#### <a name="azure-and-fragmentation"></a>Azure en fragmentatie

Virtual Network stack is ingesteld op het verwijderen van ' out of order-fragmenten ', dat wil zeggen gefragmenteerde pakketten die niet in de oorspronkelijke gefragmenteerde volg orde binnenkomen. Deze pakketten worden voornamelijk verwijderd vanwege een beveiligingslek in de netwerk beveiliging dat in november 2018 met de naam FragmentSmack wordt aangekondigd.

FragmentSmack is een fout in de manier waarop de Linux-kernel het opnieuw samen stellen van gefragmenteerde IPv4-en IPv6-pakketten verwerkt. Een externe aanvaller zou dit probleem kunnen misbruiken om dure fragmenten van gefragmenteerde bewerkingen te activeren. Dit kan leiden tot een toegenomen CPU en een denial of service op het doel systeem.

#### <a name="tune-the-mtu"></a>De MTU afstemmen

U kunt een VM-MTU van Azure configureren, zoals u dat in elk ander besturings systeem kunt doen. Maar u moet wel rekening houden met de fragmentatie die in azure wordt beschreven, wanneer u een MTU configureert.

We moedigen klanten niet aan om VM-Mtu's te verhogen. Deze bespreking bevat een uitleg van de details over hoe Azure MTU implementeert en fragmentatie uitvoert.

> [!IMPORTANT]
>Een toenemende MTU is niet bekend om de prestaties te verbeteren en kan een negatief effect hebben op de prestaties van de toepassing.
>
>

#### <a name="large-send-offload"></a>Grote verzendings-offload

Grote verzendings-offload (LSO) kan de netwerk prestaties verbeteren door de segmentatie van pakketten te offloaden naar de Ethernet-adapter. Als LSO is ingeschakeld, maakt de TCP/IP-stack een groot TCP-pakket en verzendt dit naar de Ethernet-adapter voor segmentatie voordat het wordt doorgestuurd. Het voor deel van LSO is dat de CPU van het segmenteren van pakketten kan worden vrijgemaakt naar grootten die voldoen aan de MTU en de offload die verwerkt in de Ethernet-interface waar deze wordt uitgevoerd in hardware. Zie voor meer informatie over de voor delen van LSO [grote verzendings-offload ondersteunen](/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso).

Als LSO is ingeschakeld, kunnen Azure-klanten grote frame grootten zien wanneer ze pakket opnames uitvoeren. Deze grote frame grootten kunnen ertoe leiden dat sommige klanten denken dat fragmentatie wordt uitgevoerd of dat een grote MTU wordt gebruikt wanneer dat niet het geval is. Met LSO kan de Ethernet-adapter een grotere maximum segment grootte (MSS) aankondigen aan de TCP/IP-stack om een groter TCP-pakket te maken. Dit volledige niet-gesegmenteerde frame wordt vervolgens doorgestuurd naar de Ethernet-adapter en wordt weer gegeven in een pakket opname die op de virtuele machine wordt uitgevoerd. Het pakket wordt echter onderverdeeld in veel kleinere frames door de Ethernet-adapter, volgens de MTU van de Ethernet-adapter.

### <a name="tcp-mss-window-scaling-and-pmtud"></a>TCP MSS-venster schalen en PMTUD

#### <a name="tcp-maximum-segment-size"></a>TCP maximum segment grootte

TCP Maximum Segment Size (MSS) is een instelling die de grootte van TCP-segmenten beperkt, waardoor de fragmentatie van TCP-pakketten wordt voor komen. In besturings systemen wordt deze formule meestal gebruikt voor het instellen van MSS:

`MSS = MTU - (IP header size + TCP header size)`

De IP-header en de TCP-header zijn elk 20 bytes, of 40 bytes totaal. Een interface met een MTU van 1.500 heeft dus een MSS van 1.460. De MSS kan echter wel worden geconfigureerd.

Deze instelling wordt in de drieweg van de TCP-handshake geaccepteerd wanneer een TCP-sessie wordt ingesteld tussen een bron en een bestemming. Beide zijden verzenden een MSS-waarde en de laagste van deze twee wordt gebruikt voor de TCP-verbinding.

Houd er rekening mee dat de Mtu's van de bron en bestemming niet de enige factoren zijn die de MSS-waarde bepalen. Tussenliggende netwerk apparaten, zoals VPN-gateways, met inbegrip van Azure VPN Gateway, kunnen de MTU onafhankelijk van de bron en bestemming aanpassen om optimale netwerk prestaties te garanderen.

#### <a name="path-mtu-discovery"></a>Pad MTU-detectie

MSS wordt onderhandeld, maar kan niet de daad werkelijke MSS aangeven die kan worden gebruikt. De reden hiervoor is dat andere netwerk apparaten in het pad tussen de bron en de bestemming een lagere MTU-waarde hebben dan de bron en bestemming. In dit geval is het apparaat waarvan de MTU kleiner is dan het pakket het pakket verwijdert. Het apparaat ontvangt een ICMP-fragmentatie dat is vereist (type 3, code 4) dat de MTU bevat. Met dit ICMP-bericht kan de bronhost de pad-MTU op de juiste wijze verlagen. Het proces wordt Path MTU Discovery (PMTUD) genoemd.

Het PMTUD-proces is inefficiënt en heeft invloed op de netwerk prestaties. Als pakketten worden verzonden die de MTU van een netwerkpad overschrijden, moeten de pakketten opnieuw worden verzonden met een lagere MSS. Als de afzender het bericht ICMP-fragmentatie dat nodig is, mogelijk niet ontvangt, is het mogelijk dat er een netwerk firewall is in het pad (meestal een *PMTUD-BlackHole* genoemd), de afzender niet het nodig heeft om de MSS te verlagen en het pakket continu opnieuw te verzenden. Daarom raden wij niet aan de MTU van de Azure-VM te verhogen.

#### <a name="vpn-and-mtu"></a>VPN en MTU

Als u virtuele machines gebruikt die inkapseling uitvoeren (zoals IPsec-Vpn's), zijn er enkele aanvullende overwegingen met betrekking tot pakket grootte en MTU. Vpn's voegen meer headers toe aan pakketten, waardoor de pakket grootte wordt verhoogd en een kleinere MSS vereist is.

Voor Azure wordt u aangeraden TCP MSS in te stellen op 1.350 bytes en de tunnel interface MTU tot 1.400. Zie de [pagina VPN-apparaten en IPSec/IKE-para meters](../vpn-gateway/vpn-gateway-about-vpn-devices.md)voor meer informatie.

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Latentie, round-trip tijd en TCP-venster schalen

#### <a name="latency-and-round-trip-time"></a>Latentie en retour tijd

De netwerk latentie is onderhevig aan de snelheid van het licht via een glasvezel netwerk. De netwerk doorvoer van TCP wordt ook effectief beheerst door de RTT (round-trip) tussen twee netwerk apparaten.

| Route | Afstand | Tijd in één richting | RTT |
| ----- | -------- | ------------ | --- |
|New York naar San Francisco|4.148 km|21 MS|42 MS|
|New York naar Londen|5.585 km|28 MS|56 MS|
|New York naar Sydney|15.993 km|80 MS|160 MS|

In deze tabel wordt de lineaire afstand tussen twee locaties weer gegeven. In netwerken is de afstand doorgaans langer dan de lineaire afstand. Hier volgt een eenvoudige formule voor het berekenen van de minimale RTT, afhankelijk van de snelheid van het licht:

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

U kunt 200 gebruiken voor de snelheid van door geven. Dit is de afstand, in kilo meters, die door het licht in één milliseconde wordt verplaatst.

Laten we een voor beeld doen van New York naar San Francisco. De lineaire afstand is 4.148 km. Als u die waarde aan de vergelijking koppelt, krijgen we het volgende:

`Minimum RTT = 2 * (4,148 / 200)`

De uitvoer van de vergelijking is in milliseconden.

Als u de beste netwerk prestaties wilt krijgen, is de logische optie het selecteren van bestemmingen met de kortste afstand ertussen. U moet ook uw virtuele netwerk ontwerpen om het pad naar het verkeer te optimaliseren en de latentie te verminderen. Zie de sectie overwegingen bij het ontwerpen van het netwerk in dit artikel voor meer informatie.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Tijd effecten voor latentie en round trip op TCP

De retour tijd is direct van invloed op de maximale TCP-door voer. In het TCP-protocol is de grootte van het *venster* de maximale hoeveelheid verkeer die via een TCP-verbinding kan worden verzonden voordat de afzender bevestiging van de ontvanger moet ontvangen. Als de TCP MSS is ingesteld op 1.460 en de TCP-venster grootte is ingesteld op 65.535, kan de afzender 45 pakketten verzenden voordat de ontvanger de bevestiging kan ontvangen. Als de afzender geen bevestiging krijgt, worden de gegevens opnieuw verzonden. Dit is de formule:

`TCP window size / TCP MSS = packets sent`

In dit voor beeld wordt 65.535/1.460 afgerond tot 45.

Hiermee wordt de status van ' wachten op bevestiging ', een mechanisme voor een betrouw bare levering van gegevens, de RTT beïnvloed door de TCP-door voer. Hoe langer de afzender wacht op bevestiging, des te langer moet worden gewacht voordat er meer gegevens worden verzonden.

Dit is de formule voor het berekenen van de maximale door Voer van één TCP-verbinding:

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

In deze tabel wordt de maximale door Voer van het aantal mega bytes/per seconde van één TCP-verbinding weer gegeven. (Voor de Lees baarheid worden mega bytes voor de maat eenheid gebruikt.)

| TCP-venster grootte (bytes) | Latentie van RTT (MS) | Maximale door Voer van MB/seconde | Maximale door Voer van megabit/seconde |
| ----------------------- | ---------------- | ---------------------------------- | --------------------------------- |
|65.535|1|65,54|524,29|
|65.535|30|2.18|17,48|
|65.535|60|1.09|8,74|
|65.535|90|.73|5,83|
|65.535|120|.55|4,37|

Als pakketten verloren zijn gegaan, wordt de maximale door Voer van een TCP-verbinding verlaagd terwijl de afzender de gegevens die ze al hebben verzonden, opnieuw verzendt.

#### <a name="tcp-window-scaling"></a>TCP-venster schalen

TCP-venster schalen is een techniek waarmee de TCP-venster grootte dynamisch wordt verhoogd zodat er meer gegevens kunnen worden verzonden voordat een bevestiging is vereist. In het vorige voor beeld worden 45 pakketten verzonden voordat een bevestiging is vereist. Als u het aantal pakketten verhoogt dat kan worden verzonden voordat een bevestiging nodig is, verkleint u het aantal keren dat een afzender wacht op bevestiging, waardoor de maximale door Voer van TCP wordt verhoogd.

Deze tabel illustreert de volgende relaties:

| TCP-venster grootte (bytes) | Latentie van RTT (MS) | Maximale door Voer van MB/seconde | Maximale door Voer van megabit/seconde |
| ----------------------- | ---------------- | ---------------------------------- | --------------------------------- |
|65.535|30|2.18|17,48|
|131.070|30|4,37|34,95|
|262.140|30|8,74|69,91|
|524.280|30|17,48|139,81|

Maar de waarde voor TCP-header voor TCP-venster grootte is slechts 2 bytes lang, wat betekent dat de maximum waarde voor een Receive-venster 65.535 is. Om de maximale venster grootte te verg Roten, is een schaal factor voor TCP-Vensters geïntroduceerd.

De schaal factor is ook een instelling die u kunt configureren in een besturings systeem. Hier volgt de formule voor het berekenen van de TCP-venster grootte met behulp van schaal factoren:

`TCP window size = TCP window size in bytes \* (2^scale factor)`

Dit is de berekening voor een Window-schaal factor van 3 en een venster grootte van 65.535:

`65,535 \* (2^3) = 262,140 bytes`

Een schaal factor 14 resulteert in een TCP-venster grootte van 14 (de maximale offset is toegestaan). De grootte van het TCP-venster is 1.073.725.440 bytes (8,5 gigabits).

#### <a name="support-for-tcp-window-scaling"></a>Ondersteuning voor TCP-venster schaling

Windows kan verschillende schaal factoren instellen voor verschillende verbindings typen. (Klassen van verbindingen zijn Data Center, Internet, enzovoort.) U gebruikt de `Get-NetTCPConnection` Power shell-opdracht voor het weer geven van het verbindings type venster schaal:

```powershell
Get-NetTCPConnection
```

U kunt de `Get-NetTCPSetting` Power shell-opdracht gebruiken om de waarden van elke klasse weer te geven:

```powershell
Get-NetTCPSetting
```

U kunt de eerste TCP-venster grootte en TCP-schaal factor in Windows instellen met behulp van de `Set-NetTCPSetting` Power shell-opdracht. Zie  [set-NetTCPSetting](/powershell/module/nettcpip/set-nettcpsetting)voor meer informatie.

```powershell
Set-NetTCPSetting
```

Dit zijn de effectiefste TCP-instellingen voor `AutoTuningLevel` :

| AutoTuningLevel | Schaal factor | Vermenigvuldigings factor schalen | Formule naar<br/>Maximum venster grootte berekenen |
| --------------- | -------------- | ------------------ | -------------------------------------------- |
|Uitgeschakeld|Geen|Geen|Venster grootte|
|Beperkt|4|2 ^ 4|Venster grootte * (2 ^ 4)|
|Zeer beperkt|2|2 ^ 2|Venster grootte * (2 ^ 2)|
|Normaal|8|2 ^ 8|Venster grootte * (2 ^ 8)|
|Experimenteel|14|2 ^ 14|Venster grootte * (2 ^ 14)|

Deze instellingen zijn de meest waarschijnlijke gevolgen voor TCP-prestaties, maar houd er rekening mee dat veel andere factoren op het Internet, buiten het beheer van Azure, ook van invloed kunnen zijn op TCP-prestaties.

#### <a name="increase-mtu-size"></a>MTU-grootte verhogen

Omdat een grotere MTU een grotere MSS betekent, kunt u zich wellicht afvragen of het verhogen van de MTU de TCP-prestaties kan verbeteren. Waarschijnlijk niet. Er zijn voor-en nadelen voor pakket grootte buiten gewoon TCP-verkeer. Zoals eerder is besproken, zijn de belangrijkste factoren die van invloed zijn op de prestaties van TCP-door Voer, de TCP-venster grootte, het pakket verlies en de RTT.

> [!IMPORTANT]
> Azure-klanten wordt niet aangeraden de standaard MTU-waarde op virtuele machines te wijzigen.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Versneld netwerken en schalen aan de ontvangst zijde

#### <a name="accelerated-networking"></a>Versneld netwerken

Virtuele-machine netwerk functies hebben historische CPU-intensief op zowel de gast-VM als de Hyper Visor/host. Elk pakket dat door Voer via de host wordt verwerkt door de host-CPU, met inbegrip van alle virtuele-netwerk-inkapseling en ontkapseling. Het meer verkeer dat door de host loopt, des te hoger de CPU-belasting. En als de host-CPU bezig is met andere bewerkingen, is dit ook van invloed op de netwerk doorvoer en-latentie. Dit probleem wordt door Azure opgelost met versneld netwerken.

Versnelde netwerken bieden consistente ultralow netwerk latentie via de interne Programmeer bare hardware van Azure en technologieën zoals SR-IOV. Versneld netwerken verplaatsen veel van de door de software gedefinieerde Azure-netwerk stack van de Cpu's en naar op FPGA gebaseerde SmartNICs. Met deze wijziging kunnen eindgebruikers toepassingen reken cycli vrijmaken, waardoor er minder belasting op de virtuele machine wordt gemaakt, waarbij jitter en inconsistentie in latentie dalen. Met andere woorden, de prestaties kunnen meer deterministisch zijn.

Versneld netwerken verbeteren de prestaties doordat de gast-VM de host kan omzeilen en een DataPath rechtstreeks tot stand kan brengen met de SmartNIC van een host. Hier volgen enkele voor delen van versneld netwerken:

- **Lagere latentie/hogere pakketten per seconde (PPS)**: door de virtuele switch uit de DataPath te verwijderen, elimineert u de tijd pakketten in de host voor beleids verwerking en verhoogt u het aantal pakketten dat kan worden verwerkt in de virtuele machine.

- **Verminderde jitter**: virtuele switch verwerking is afhankelijk van de hoeveelheid beleid die moet worden toegepast en de werk belasting van de CPU die de verwerking uitvoert. Door het beleid afdwingen naar de hardware te verwijderen, verwijdert u die variabiliteit door pakketten rechtstreeks aan de virtuele machine te leveren, waardoor de host-naar-VM-communicatie en alle software-interrupts en-context switches worden geëlimineerd.

- **Verminderd CPU-gebruik**: het overs laan van de virtuele switch in de host leidt tot minder CPU-gebruik voor het verwerken van netwerk verkeer.

Als u versneld netwerken wilt gebruiken, moet u het expliciet inschakelen op elke van toepassing zijnde VM. Zie [een virtuele Linux-machine met versneld netwerken maken](./create-vm-accelerated-networking-cli.md) voor instructies.

#### <a name="receive-side-scaling"></a>Schalen aan de ontvangst zijde

Schalen aan de ontvangst zijde (RSS) is een technologie voor netwerk Stuur Programma's die de ontvangst van netwerk verkeer efficiënter distribueert door ontvangst verwerking over meerdere Cpu's in een multiprocessor systeem te distribueren. In eenvoudige termen kan een systeem meer ontvangen verkeer verwerken omdat alle beschik bare Cpu's worden gebruikt in plaats van slechts één. Zie [Introduction to Receive Side Scaling](/windows-hardware/drivers/network/introduction-to-receive-side-scaling)(Engelstalig) voor een meer technische bespreking van RSS.

Als u de beste prestaties wilt krijgen wanneer versnelde netwerken zijn ingeschakeld op een VM, moet u RSS inschakelen. RSS kan ook voor delen bieden op Vm's die geen versneld netwerken gebruiken. Zie [netwerk doorvoer optimaliseren voor virtuele Azure-machines](./virtual-network-optimize-network-bandwidth.md)voor een overzicht van hoe u kunt bepalen of RSS is ingeschakeld en hoe u deze functie inschakelt.

### <a name="tcp-time_wait-and-time_wait-assassination"></a>TCP-TIME_WAIT en TIME_WAIT Assassination

TCP TIME_WAIT is een andere algemene instelling die van invloed is op de prestaties van het netwerk en de toepassing. Op drukke Vm's die veel sockets openen en sluiten, hetzij als clients of als servers (bron-IP: bron poort + doel-IP: doel poort), tijdens de normale werking van TCP, kan een bepaalde socket gedurende een lange periode in een TIME_WAIT status eindigen. De TIME_WAIT status is bedoeld om ervoor te zorgen dat eventuele extra gegevens op een socket worden bezorgd voordat ze worden gesloten. TCP/IP-stacks voor komen doorgaans het hergebruik van een socket door het TCP SYN-pakket van de client op de achtergrond te verwijderen.

De hoeveelheid tijd die een socket in TIME_WAIT kan worden geconfigureerd. Het bereik kan variëren van 30 seconden tot 240 seconden. Sockets zijn een eindige resource en het aantal sockets dat op een bepaald moment kan worden gebruikt, kan worden geconfigureerd. (Het aantal beschik bare sockets is doorgaans ongeveer 30.000.) Als de beschik bare sockets worden verbruikt, of als clients en servers niet-overeenkomende TIME_WAIT-instellingen hebben en een virtuele machine probeert een socket opnieuw te gebruiken in een TIME_WAIT status, mislukken nieuwe verbindingen omdat TCP SYN-pakketten op de achtergrond worden verwijderd.

De waarde voor het poort bereik voor uitgaande sockets kan doorgaans worden geconfigureerd in de TCP/IP-stack van een besturings systeem. Hetzelfde geldt voor TCP-TIME_WAIT-instellingen en het opnieuw gebruiken van sockets. Als u deze nummers wijzigt, kan dit de schaal baarheid verbeteren. Maar afhankelijk van de situatie kunnen deze wijzigingen leiden tot interoperabiliteits problemen. U moet voorzichtig zijn als u deze waarden wijzigt.

U kunt TIME_WAIT Assassination gebruiken om deze schaal beperking te verhelpen. Met TIME_WAIT Assassination kan een socket opnieuw worden gebruikt in bepaalde situaties, zoals wanneer het Volg nummer in het IP-pakket van de nieuwe verbinding het Volg nummer van het laatste pakket van de vorige verbinding overschrijdt. In dit geval staat het besturings systeem toe dat de nieuwe verbinding tot stand wordt gebracht (de nieuwe SYN/ACK wordt geaccepteerd) en sluit de vorige verbinding af die de status TIME_WAIT heeft. Deze mogelijkheid wordt ondersteund op Windows-Vm's in Azure. Neem contact op met de leverancier van het besturings systeem voor meer informatie over ondersteuning in andere Vm's.

Zie [instellingen die kunnen worden gewijzigd om de netwerk prestaties te verbeteren](/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance)voor meer informatie over het configureren van TCP-TIME_WAIT instellingen en het bron poort bereik.

## <a name="virtual-network-factors-that-can-affect-performance"></a>Virtuele netwerk factoren die de prestaties kunnen beïnvloeden

### <a name="vm-maximum-outbound-throughput"></a>Maximale uitgaande door Voer van virtuele machine

Azure biedt verschillende VM-grootten en-typen, elk met een andere combi natie van prestatie mogelijkheden. Een van deze mogelijkheden is netwerk doorvoer (of band breedte), die wordt gemeten in megabits per seconde (Mbps). Omdat virtuele machines worden gehost op gedeelde hardware, moet de netwerk capaciteit eerlijk worden gedeeld tussen de virtuele machines met dezelfde hardware. Grotere virtuele machines hebben meer band breedte toegewezen dan kleinere virtuele machines.

De netwerk bandbreedte die aan elke virtuele machine wordt toegewezen, wordt gemeten op basis van uitgaand verkeer van de virtuele machine. Al het netwerk verkeer dat de virtuele machine verlaat, wordt meegeteld bij de toegewezen limiet, ongeacht de bestemming. Als een virtuele machine bijvoorbeeld een limiet van 1.000 Mbps heeft, is deze limiet van toepassing, ongeacht of het uitgaande verkeer bestemd is voor een andere virtuele machine in hetzelfde virtuele netwerk of een buiten Azure.

Ingangs rechten worden niet rechtstreeks in een Data limiet gemeten. Maar er zijn andere factoren, zoals CPU-en opslag limieten, die van invloed kunnen zijn op de mogelijkheid van een virtuele machine om binnenkomende gegevens te verwerken.

Versneld netwerken zijn ontworpen om de netwerk prestaties te verbeteren, inclusief latentie, door Voer en CPU-gebruik. Versnelde netwerken kunnen de door Voer van een virtuele machine verbeteren, maar dit kan alleen tot de toegewezen band breedte van de virtuele machine.

Er is ten minste één netwerk interface aan Azure virtual machines gekoppeld. Ze kunnen meerdere hebben. De band breedte die is toegewezen aan een virtuele machine is de som van alle uitgaand verkeer op alle netwerk interfaces die zijn gekoppeld aan de computer. Met andere woorden, de band breedte wordt toegewezen op basis van een virtuele machine, ongeacht het aantal netwerk interfaces dat aan de computer is gekoppeld.

Verwachte uitgaande door Voer en het aantal netwerk interfaces dat wordt ondersteund door elke VM-grootte, worden gedetailleerd beschreven in [grootten voor virtuele Windows-machines in azure](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Als u de maximale door voer wilt bekijken, selecteert u een type, zoals **Algemeen doel**, en zoekt u de sectie over de grootte reeks op de resulterende pagina (bijvoorbeeld ' dv2-serie '). Voor elke reeks is er een tabel met netwerk specificaties in de laatste kolom, met de titel Max Nic's/verwachte netwerk bandbreedte (Mbps).

De doorvoer limiet is van toepassing op de virtuele machine. De door Voer wordt niet beïnvloed door de volgende factoren:

- **Aantal netwerk interfaces**: de bandbreedte limiet is van toepassing op de som van al het uitgaande verkeer van de virtuele machine.

- **Versneld netwerken**: Hoewel deze functie nuttig kan zijn bij het bereiken van de gepubliceerde limiet, wordt de limiet niet gewijzigd.

- **Verkeers bestemming**: alle doelen tellen mee voor de uitgaande limiet.

- **Protocol**: al het uitgaande verkeer via alle protocollen telt de limiet.

Zie [netwerk bandbreedte van virtuele machines](./virtual-machine-network-throughput.md)voor meer informatie.

### <a name="internet-performance-considerations"></a>Aandachtspunten voor Internet prestaties

Zoals besproken in dit artikel, kunnen factoren op internet en buiten het beheer van Azure invloed hebben op de netwerk prestaties. Hier volgen enkele van deze factoren:

- **Latentie**: de round-trip tijd tussen twee bestemmingen kan worden beïnvloed door problemen in tussenliggende netwerken, door verkeer dat niet het "kortste" pad "en door suboptimale peering paden afneemt.

- **Pakket verlies**: pakket verlies kan worden veroorzaakt door netwerk congestie, problemen met fysieke paden en het uitvoeren van netwerk apparaten.

- **MTU-grootte/fragmentatie**: fragmentatie langs het pad kan leiden tot vertragingen bij het ontvangen van gegevens of in pakketten die in de juiste volg orde arriveren. Dit kan van invloed zijn op de levering van pakketten.

Traceroute is een goed hulp middel voor het meten van de netwerk prestatie kenmerken (zoals pakket verlies en latentie) langs elk netwerkpad tussen een bron apparaat en een doel apparaat.

### <a name="network-design-considerations"></a>Overwegingen bij het ontwerpen van netwerken

Naast de overwegingen die eerder in dit artikel zijn beschreven, kan de topologie van een virtueel netwerk invloed hebben op de prestaties van het netwerk. Een hub-en-spoke-ontwerp dat verkeer globaal backhauls naar een virtueel netwerk met één hub brengt bijvoorbeeld netwerk latentie uit, wat van invloed is op de algehele netwerk prestaties.

Het aantal netwerk apparaten dat door netwerk verkeer wordt door gegeven, kan ook van invloed zijn op de algehele latentie. Als verkeer bijvoorbeeld in een hub en spoke-ontwerp wordt door gegeven via een spoke-netwerk virtueel apparaat en een hub-virtueel apparaat vóór door voer naar Internet, kan de virtuele netwerk apparaten latentie introduceren.

### <a name="azure-regions-virtual-networks-and-latency"></a>Azure-regio's, virtuele netwerken en latentie

Azure-regio's bestaan uit meerdere data centers in een algemene geografische regio. Deze data centers zijn mogelijk niet fysiek naast elkaar. In sommige gevallen worden ze gescheiden door Maxi maal 10 kilo meters. Het virtuele netwerk is een logische overlay boven op het fysieke Azure-datacenter netwerk. Een virtueel netwerk impliceert geen specifieke netwerk topologie binnen het Data Center.

Zo kunnen twee virtuele machines die zich in hetzelfde virtuele netwerk en subnet bevinden zich in verschillende racks, rijen of zelfs data centers. Ze kunnen worden gescheiden door een glas vezel glasvezel kabel of door middel van een glasvezel kabel van kilo meters. Deze variatie kan variabele latentie veroorzaken (een paar milliseconden verschil) tussen verschillende Vm's.

De geografische plaatsing van Vm's en de mogelijke resulterende latentie tussen twee Vm's kan worden beïnvloed door de configuratie van beschikbaarheids sets en Beschikbaarheidszones. Maar de afstand tussen data centers in een regio is een regio-specifiek en wordt voornamelijk beïnvloed door Data Center-topologie in de regio.

### <a name="source-nat-port-exhaustion"></a>Poort uitputting bron-NAT

Een implementatie in azure kan communiceren met eind punten buiten Azure op het open bare Internet en/of in de open bare IP-ruimte. Wanneer een exemplaar een uitgaande verbinding initieert, wijst Azure het privé-IP-adres dynamisch toe aan een openbaar IP-adres. Nadat Azure deze toewijzing heeft gemaakt, kan het retour verkeer voor de uitgaande stroom van het netwerk ook het privé-IP-adres bereiken waarvan de stroom afkomstig is.

Voor elke uitgaande verbinding moet de Azure Load Balancer voor een bepaalde periode deze toewijzing onderhouden. Met de multi tenant-aard van Azure kan het onderhoud van deze toewijzing voor elke uitgaande stroom voor elke VM veel resources zijn. Daarom gelden er limieten die zijn ingesteld en gebaseerd op de configuratie van de Azure-Virtual Network. U kunt er ook voor zorgen dat een virtuele machine van Azure een bepaald aantal uitgaande verbindingen op een bepaald moment kan maken. Wanneer deze limieten zijn bereikt, kan de virtuele machine geen meer uitgaande verbindingen maken.

Dit gedrag kan echter wel worden geconfigureerd. Zie [dit artikel](../load-balancer/load-balancer-outbound-connections.md)voor meer informatie over de uitputting van de SNAT-en SNAT-poort.

## <a name="measure-network-performance-on-azure"></a>Netwerk prestaties meten op Azure

Een aantal van de prestatie limieten in dit artikel is gerelateerd aan de netwerk latentie/round-trip tijd (RTT) tussen twee Vm's. In deze sectie vindt u enkele suggesties voor het testen van de latentie/RTT en het testen van TCP-prestaties en VM-netwerk prestaties. U kunt afstemmen en prestaties testen de TCP/IP-en netwerk waarden die eerder zijn besproken met behulp van de technieken die in deze sectie worden beschreven. U kunt de waarden van de latentie, MTU, MSS en venster grootte koppelen aan de eerder beschik bare berekeningen en het theoretische maximum aantal vergelijken met de werkelijke waarden die u tijdens het testen bekijkt.

### <a name="measure-round-trip-time-and-packet-loss"></a>Round-trip tijd en pakket verlies meten

TCP-prestaties zijn sterk afhankelijk van RTT en pakket verlies. Het hulp programma PING dat beschikbaar is in Windows en Linux biedt de eenvoudigste manier om RTT en pakket verlies te meten. Bij de uitvoer van PING wordt de minimale/maximale/gemiddelde latentie tussen een bron en bestemming weer gegeven. Ook wordt het pakket verlies weer gegeven. PING maakt standaard gebruik van het ICMP-protocol. U kunt PsPing gebruiken om TCP RTT te testen. Zie [PsPing](/sysinternals/downloads/psping)voor meer informatie.

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Werkelijke door Voer van een TCP-verbinding meten

NTttcp is een hulp programma voor het testen van de TCP-prestaties van een Linux-of Windows-VM. U kunt verschillende TCP-instellingen wijzigen en vervolgens de voor delen testen met behulp van NTttcp. Zie de volgende bronnen voor meer informatie:

- [Bandbreedte/doorvoer testen (NTttcp)](./virtual-network-bandwidth-testing.md)

- [Hulp programma NTttcp](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Werkelijke band breedte van een virtuele machine meten

U kunt de prestaties van verschillende VM-typen, versnelde netwerken, enzovoort, testen met behulp van een hulp programma met de naam iPerf. iPerf is ook beschikbaar in Linux en Windows. iPerf kunnen TCP of UDP gebruiken om de algehele netwerk doorvoer te testen. iPerf TCP-doorvoer tests worden beïnvloed door de factoren die in dit artikel worden besproken (zoals latentie en RTT). UDP kan dus betere resultaten opleveren als u alleen de maximale door voer wilt testen.

Raadpleeg deze artikelen voor meer informatie:

- [Problemen met Expressroute-netwerk prestaties oplossen](../expressroute/expressroute-troubleshooting-network-performance.md)

- [VPN-doorvoer naar een virtueel netwerk valideren](../vpn-gateway/vpn-gateway-validate-throughput-to-vnet.md)

### <a name="detect-inefficient-tcp-behaviors"></a>Inefficiënt TCP-gedrag detecteren

In pakket opnames kunnen Azure-klanten TCP-pakketten met TCP-vlaggen (zakken, dubbele ACK, retransmit en snel opnieuw verzenden) zien. Dit kan duiden op problemen met de netwerk prestaties. Deze pakketten wijzen specifiek op netwerk inefficiënties die het resultaat zijn van pakket verlies. Maar pakket verlies is niet noodzakelijkerwijs veroorzaakt door prestatie problemen van Azure. Prestatie problemen kunnen het gevolg zijn van toepassings problemen, problemen met het besturings systeem of andere problemen die mogelijk niet rechtstreeks verband houden met het Azure-platform.

Houd er ook voor dat sommige herverzendings-en dubbele bevestigingen normaal in een netwerk zijn. TCP-protocollen zijn gebouwd om betrouwbaar te zijn. Aanwijzingen van deze TCP-pakketten in een pakket opname duiden niet noodzakelijkerwijs op een systeem netwerk probleem, tenzij ze buitensporig zijn.

Deze pakket typen zijn echter aanwijzingen dat TCP-door Voer niet de maximale prestaties bereikt, om redenen die in andere secties van dit artikel worden besproken.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd over het afstemmen van TCP/IP-prestaties voor Azure-Vm's, wilt u mogelijk meer informatie over andere overwegingen voor het [plannen van virtuele netwerken](./virtual-network-vnet-plan-design-arm.md) of [meer informatie over het verbinden en configureren van virtuele netwerken](./index.yml).