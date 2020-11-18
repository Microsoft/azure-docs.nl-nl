---
title: VPN-door voer naar een Microsoft Azure Virtual Network valideren
description: Dit artikel helpt u bij het valideren van de netwerk doorvoer van uw on-premises resources naar een virtuele machine van Azure.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
manager: dcscontentpm
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 09/02/2020
ms.author: radwiv
ms.reviewer: chadmat;genli
ms.openlocfilehash: 2d5b51e8cfbfcb5f771e9da524231f8ddfc40a9e
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660930"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>VPN-doorvoer naar een virtueel netwerk valideren

Met een VPN-gateway verbinding kunt u een veilige, cross-premises verbinding tot stand brengen tussen uw Virtual Network in Azure en uw on-premises IT-infra structuur.

In dit artikel wordt beschreven hoe u de netwerk doorvoer kunt valideren van de on-premises resources naar een virtuele machine van Azure (VM).

> [!NOTE]
> Dit artikel is bedoeld om veelvoorkomende problemen te diagnosticeren en op te lossen. Als u het probleem niet kunt oplossen met behulp van de volgende informatie, [neemt u contact op](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)met de ondersteuning.

## <a name="overview"></a>Overzicht

De VPN-gateway verbinding bestaat uit de volgende onderdelen:

* On-premises VPN-apparaat (een lijst met [gevalideerde VPN-apparaten](vpn-gateway-about-vpn-devices.md#devicetable)weer geven.)
* Openbaar Internet
* Azure VPN-gateway
* Azure VM

In het volgende diagram ziet u de logische connectiviteit van een on-premises netwerk met een virtueel Azure-netwerk via VPN.

![Logische verbinding van klant netwerk met MSFT-netwerk via VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Het verwachte maximum aantal ingangs-en uitgangs gegevens berekenen

1. Bepaal de vereisten voor de basislijn doorvoer van uw toepassing.
1. Bepaal de doorvoer limieten van Azure VPN gateway. Zie de sectie ' gateway-Sku's ' van [About VPN gateway](vpn-gateway-about-vpngateways.md#gwsku)voor meer informatie.
1. Bepaal de [Azure VM-doorvoer richtlijnen](../virtual-machines/sizes.md) voor uw VM-grootte.
1. Bepaal de band breedte van uw Internet provider (ISP).
1. Bereken de verwachte door voer door de minste band breedte van de virtuele machine, VPN Gateway of ISP, te nemen. Dit wordt gemeten in megabits per seconde (/) gedeeld door acht (8).

Als de berekende door Voer niet voldoet aan de vereisten voor basis doorvoer van uw toepassing, moet u de band breedte verg Roten van de bron die u als bottleneck hebt geïdentificeerd. Als u het formaat van een Azure VPN Gateway wilt wijzigen, raadpleegt u [een gateway-SKU wijzigen](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Zie [het formaat van een](../virtual-machines/windows/resize-vm.md)virtuele machine wijzigen als u het formaat van een VM wilt wijzigen. Als u niet de verwachte Internet bandbreedte ondervindt, kunt u ook contact opnemen met uw Internet provider.

> [!NOTE]
> VPN Gateway door Voer is een aggregatie van alle Site-to-Site\VNET-to-VNET-of Point-to-site-verbindingen.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Netwerk doorvoer valideren met behulp van prestatie hulpprogramma's

Deze validatie moet worden uitgevoerd tijdens niet-piek uren, aangezien bij het testen van de prestaties van de VPN-tunnel geen nauw keurige resultaten worden verstrekt.

Het hulp programma dat voor deze test wordt gebruikt, is iPerf, dat werkt op zowel Windows als Linux en zowel de client-als server modus heeft. Het is beperkt tot 3Gbps voor Windows-Vm's.

Dit hulp programma voert geen lees-en schrijf bewerkingen uit op schijf. Het produceert alleen zelf gegenereerd TCP-verkeer van het ene eind naar het andere. Er worden statistieken gegenereerd op basis van experimenten die de band breedte beschik bare tussen client-en server knooppunten meet. Bij het testen tussen twee knoop punten fungeert één knoop punt als de-server en fungeert het andere knoop punt als een-client. Wanneer deze test is voltooid, wordt u aangeraden de rollen van de knoop punten om te keren van zowel upload-als download doorvoer door Voer op beide knoop punten.

### <a name="download-iperf"></a>IPerf downloaden

Down load [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Zie [iPerf-documentatie](https://iperf.fr/iperf-doc.php)voor meer informatie.

 > [!NOTE]
 > De producten van derden die in dit artikel worden besproken, worden geproduceerd door bedrijven die onafhankelijk zijn van micro soft. Micro soft biedt geen enkele garantie, impliciet noch anderszins, over de prestaties of betrouw baarheid van deze producten.

### <a name="run-iperf-iperf3exe"></a>IPerf uitvoeren (iperf3.exe)

1. Schakel een NSG/ACL-regel in die het verkeer toestaat (voor het testen van het open bare IP-adres op de VM van Azure).

1. Schakel op beide knoop punten een firewall uitzondering in voor poort 5001.

   **Windows:** Voer de volgende opdracht uit als beheerder:

   ```CMD
   netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
   ```

   Als u de regel wilt verwijderen wanneer het testen is voltooid, voert u de volgende opdracht uit:

   ```CMD
   netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
   ```

   **Azure Linux:** Azure Linux-installatie kopieën hebben een strikte firewall. Als er een toepassing luistert op een poort, wordt het verkeer toegestaan via. Voor aangepaste installatie kopieën die zijn beveiligd, zijn mogelijk poorten expliciet geopend. Algemene firewalls voor Linux-besturings systemen bevatten `iptables` , `ufw` , of `firewalld` .

1. Ga naar de map waarin iperf3.exe wordt geëxtraheerd op het server knooppunt. Voer vervolgens iPerf uit in de server modus en stel deze in op poort 5001 als de volgende opdrachten:

   ```CMD
   cd c:\iperf-3.1.2-win65

   iperf3.exe -s -p 5001
   ```

   > [!Note]
   > Poort 5001 is aanpasbaar aan accounts voor bepaalde firewall beperkingen in uw omgeving.

1. Ga op het client-knoop punt naar de map waarin het hulp programma iperf wordt geëxtraheerd en voer de volgende opdracht uit:

   ```CMD
   iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
   ```

   De client stuurt dertig seconden aan verkeer op poort 5001, naar de server. De vlag-P geeft aan dat er Maxi maal 32 gelijktijdige verbindingen met het server knooppunt worden gemaakt.

   In het volgende scherm ziet u de uitvoer van dit voor beeld:

   ![Uitvoer](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

1. Beschrijving Als u de test resultaten wilt behouden, voert u deze opdracht uit:

   ```CMD
   iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
   ```

1. Nadat u de voor gaande stappen hebt voltooid, voert u dezelfde stappen uit met de functies die zijn omgekeerd, zodat het server knooppunt nu het client knooppunt is en vice versa.

> [!Note]
> Iperf is niet het enige hulp programma. [NTTTCP is een alternatieve oplossing voor testen](../virtual-network/virtual-network-bandwidth-testing.md).

## <a name="test-vms-running-windows"></a>Vm's met Windows testen

### <a name="load-latteexe-onto-the-vms"></a>Latte.exe op Vm's laden

De nieuwste versie van [Latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b) downloaden

Overweeg Latte.exe in een afzonderlijke map te plaatsen, zoals `c:\tools`

### <a name="allow-latteexe-through-the-windows-firewall"></a>Latte.exe via de Windows Firewall toestaan

Maak op de ontvanger een regel voor toestaan op de Windows Firewall zodat het Latte.exe verkeer kan binnenkomen. Het is eenvoudig om het hele Latte.exe-programma toe te staan op naam in plaats van specifieke TCP-poorten toestaan.

### <a name="allow-latteexe-through-the-windows-firewall-like-this"></a>Latte.exe via Windows Firewall als volgt toestaan

`netsh advfirewall firewall add rule program=<PATH>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

Als u bijvoorbeeld latte.exe hebt gekopieerd naar de map ' c:\Tools ', is dit de opdracht

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Latentie tests uitvoeren

latte.exe starten op de ontvanger (uitvoeren vanuit CMD, niet vanuit Power shell):

`latte -a <Receiver IP address>:<port> -i <iterations>`

Rond 65k-iteraties is lang genoeg om representatieve resultaten te retour neren.

Elk beschikbaar poort nummer is prima.

Als de virtuele machine een IP-adres heeft van 10.0.0.4, ziet deze er als volgt uit:

`latte -c -a 10.0.0.4:5005 -i 65100`

latte.exe op de afzender starten (uitvoeren vanuit CMD, niet vanuit Power shell)

`latte -c -a <Receiver IP address>:<port> -i <iterations>`

De resulterende opdracht is hetzelfde als op de ontvanger, behalve met de toevoeging van '-c ' om aan te geven dat dit de ' client ' of afzender is

`latte -c -a 10.0.0.4:5005 -i 65100`

Wacht op de resultaten. Afhankelijk van hoe ver de Vm's bestaan, kan het enkele minuten duren voordat het proces is voltooid. Overweeg te beginnen met minder herhalingen om te testen op geslaagde pogingen voordat u langere tests uitvoert.

## <a name="test-vms-running-linux"></a>Vm's met Linux testen

Gebruik [SockPerf](https://github.com/mellanox/sockperf) om vm's te testen.

### <a name="install-sockperf-on-the-vms"></a>SockPerf installeren op de virtuele machines

Voer op de virtuele Linux-machines (zowel de afzender als de ontvanger) deze opdrachten uit om SockPerf op uw virtuele machines voor te bereiden:

#### <a name="centos--rhel---install-git-and-other-helpful-tools"></a>CentOS/RHEL-Installeer GIT en andere nuttige hulpprogram ma's

`sudo yum install gcc -y -q`
`sudo yum install git -y -q`
`sudo yum install gcc-c++ -y`
`sudo yum install ncurses-devel -y`
`sudo yum install -y automake`

#### <a name="ubuntu---install-git-and-other-helpful-tools"></a>Ubuntu: GIT en andere nuttige hulpprogram ma's installeren

`sudo apt-get install build-essential -y`
`sudo apt-get install git -y -q`
`sudo apt-get install -y autotools-dev`
`sudo apt-get install -y automake`

#### <a name="bash---all"></a>Bash-alle

Vanaf de opdracht regel van bash (ervan uitgaande dat Git is geïnstalleerd)

`git clone https://github.com/mellanox/sockperf`
`cd sockperf/`
`./autogen.sh`
`./configure --prefix=`

Het maken van een tragere, kan enkele minuten duren

`make`

Installeren is snel

`sudo make install`

### <a name="run-sockperf-on-the-vms"></a>SockPerf uitvoeren op de Vm's

#### <a name="sample-commands-after-installation-serverreceiver---assumes-servers-ip-is-10004"></a>Voorbeeld opdrachten na installatie. Server/ontvanger: neemt het IP-adres van de server 10.0.0.4

`sudo sockperf sr --tcp -i 10.0.0.4 -p 12345 --full-rtt`

#### <a name="client---assumes-servers-ip-is-10004"></a>Client-gaat ervan uit dat de server-IP 10.0.0.4 is

`sockperf ping-pong -i 10.0.0.4 --tcp -m 1400 -t 101 -p 12345  --full-rtt`

> [!Note]
> Zorg ervoor dat er geen tussenliggende hops (bijvoorbeeld virtueel apparaat) zijn tijdens het testen van de door Voer tussen de virtuele machine en de gateway.
> Raadpleeg het volgende artikel om inzicht te krijgen in de belangrijkste factoren achter de mogelijke hoofd oorzaken van het probleem, als er sprake is van slechte resultaten (wat een algemene door Voer is) die afkomstig zijn van de hierboven genoemde iPERF/NTTTCP-tests. https://docs.microsoft.com/azure/virtual-network/virtual-network-tcpip-performance-tuning

Met name de analyse van pakket Capture-traceringen (wireshark/Network Monitor) die parallel van de client en de server zijn verzameld tijdens deze tests, kunnen helpen bij de evaluatie van slechte prestaties. Deze traceringen kunnen pakket verlies, hoge latentie, MTU-grootte omvatten. fragmentatie, TCP 0-venster, fragmenten met een andere volg orde, enzovoort.

## <a name="address-slow-file-copy-issues"></a>Problemen met trage bestanden kopiëren oplossen

Zelfs als de algemene door Voer die is beoordeeld met de vorige stappen (iPERF/NTTTCP/etc..) goed was, kunt u trage bestands omgaan ervaren wanneer u Windows Verkenner gebruikt, of door middel van het slepen en neerzetten via een RDP-sessie. Dit probleem wordt meestal veroorzaakt door een van de volgende factoren:

* Voor het kopiëren van bestanden, zoals Windows Verkenner en RDP, worden niet meerdere threads gebruikt tijdens het kopiëren. Gebruik voor betere prestaties een multi-threaded Copy-toepassing zoals [RichCopy](/previous-versions/technet-magazine/dd547088(v=msdn.10)) om bestanden te kopiëren met behulp van 16-of 32-threads. Als u het thread nummer voor het kopiëren van bestanden in RichCopy wilt wijzigen, klikt u op **actie**  >  **kopiëren opties**  >  **bestand kopiëren**.

   ![Problemen bij het kopiëren van bestanden vertragen](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>

   > [!Note]
   > Niet alle toepassingen werken hetzelfde en niet alle threads worden gebruikt voor alle toepassingen en processen. Als u de test uitvoert, ziet u dat sommige threads leeg zijn en geen nauw keurige doorvoer resultaten opleveren.
   > Als u de prestaties van de bestands overdracht van uw toepassing wilt controleren, gebruikt u multi-thread door het aantal threads achter elkaar of afnemend te verhogen om de optimale door Voer van de toepassing of bestands overdracht te vinden.

* Onvoldoende VM-Lees-en schrijf snelheid. Zie [Azure Storage Troubleshooting](/previous-versions/azure/storage/common/storage-e2e-troubleshooting)(Engelstalig) voor meer informatie.

## <a name="on-premises-device-external-facing-interface"></a>On-premises apparaat extern gerichte interface

Vermeld de subnetten van on-premises bereiken die door Azure moeten worden bereikt via VPN op de lokale netwerk gateway. Definieer tegelijkertijd de VNET-adres ruimte in azure op het on-premises apparaat.

* **Op route gebaseerde gateway**: het beleid of de selectie van verkeer voor op route gebaseerde vpn's worden geconfigureerd als een wille keurig (of Joker).

* **Op beleid gebaseerde gateway**: op beleid gebaseerde vpn's versleutelen en direct pakketten via IPSec-tunnels op basis van de combi Naties van adres voorvoegsels tussen uw on-premises netwerk en Azure VNet. Het beleid (of de verkeersselector) wordt gewoonlijk gedefinieerd als een toegangslijst in de VPN-configuratie.

* **UsePolicyBasedTrafficSelector** -verbindingen: (' UsePolicyBasedTrafficSelectors ' om een verbinding te $True, configureert de Azure VPN-gateway om verbinding te maken met op beleid gebaseerde VPN-Firewall on-premises. Als u PolicyBasedTrafficSelectors inschakelt, moet u ervoor zorgen dat op uw VPN-apparaat de overeenkomende verkeers selectie is gedefinieerd met alle combi Naties van uw on-premises netwerk (lokale netwerk gateway) voor voegsels van en naar de voor voegsels van het virtuele Azure-netwerk in plaats van any.

Onjuiste configuratie kan leiden tot veelvuldige verbindingen binnen de tunnel, pakket drup pels, ongeldige door Voer en latentie.

## <a name="check-latency"></a>Latentie controleren

U kunt de latentie controleren met behulp van de volgende hulpprogram ma's:

* WinMTR
* TCPTraceroute
* `ping` en `psping` (deze hulpprogram ma's kunnen een goede schatting van de RTT bieden, maar ze kunnen niet in alle gevallen worden gebruikt.)

![Latentie controleren](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

Als u een hoge latentie piek bij een van de hops ziet voordat u MS Network backbone invoert, wilt u mogelijk door gaan met verdere onderzoeken met uw Internet provider.

Als er een grote, ongebruikelijke latentie piek wordt opgemerkt vanuit hops binnen "msn.net", neemt u contact op met MS ondersteuning voor verdere onderzoeken.

## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende koppeling voor meer informatie of hulp:

* [Microsoft Ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)