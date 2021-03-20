---
title: MPIO configureren op StorSimple Linux-host
description: Meer informatie over de stappen die nodig zijn voor het configureren van multipath IO (MPIO) op de StorSimple Linux (CentOS 6,6)-hostserver.
author: alkohli
ms.assetid: ca289eed-12b7-4e2e-9117-adf7e2034f2f
ms.service: storsimple
ms.topic: how-to
ms.date: 06/12/2019
ms.author: alkohli
ms.openlocfilehash: 2b7ddf6423db4c471ee2065635f4e3e89f7eb7b2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98745730"
---
# <a name="configure-mpio-on-a-storsimple-host-running-centos"></a>MPIO configureren op een StorSimple-host met CentOS
In dit artikel worden de stappen beschreven die nodig zijn voor het configureren van MPIO (multipath-i/o) op de CentOS 6,6-hostserver. De hostserver is verbonden met uw Microsoft Azure StorSimple-apparaat voor hoge Beschik baarheid via iSCSI-initia tors. Hierin wordt de automatische detectie van multipath-apparaten en de specifieke installatie van alleen voor StorSimple-volumes beschreven.

Deze procedure is van toepassing op alle modellen van de StorSimple 8000-serie apparaten.

> [!NOTE]
> Deze procedure kan niet worden gebruikt voor een StorSimple Cloud Appliance. Zie voor meer informatie host-servers configureren voor uw Cloud apparaat.

## <a name="about-multipathing"></a>Over meerdere paden
Met de functie multipath kunt u meerdere I/O-paden configureren tussen een hostserver en een opslag apparaat. Deze I/O-paden zijn fysieke SAN-verbindingen die afzonderlijke kabels, switches, netwerk interfaces en controllers kunnen bevatten. Met meerdere paden worden de I/O-paden geaggregeerd om een nieuw apparaat te configureren dat is gekoppeld aan alle geaggregeerde paden.

Het doel van meerdere paden is twee vouwen:

* **Hoge Beschik baarheid**: het biedt een alternatief pad als een element van het I/O-pad (zoals een kabel, Switch, netwerk interface of controller) mislukt.
* **Taak verdeling**: afhankelijk van de configuratie van uw opslag apparaat kan dit de prestaties verbeteren door de belasting van de I/O-paden te detecteren en deze belastingen dynamisch te herverdelen.

### <a name="about-multipathing-components"></a>Over onderdelen voor meerdere paden
Meerdere paden in Linux bestaat uit kernel-onderdelen en onderdelen van de gebruikers ruimte, zoals hieronder beschreven.

* **Kernel**: het hoofd onderdeel is de *toewijzing* van het apparaat waarmee I/O wordt omgeleid en failover voor paden en paden wordt ondersteund.

* **Gebruikers ruimte**: Dit zijn *meerdere paden* waarmee apparaten met meerdere paden kunnen worden beheerd door de toewijzings module voor het apparaat in te voeren. De hulpprogram ma's bestaan uit:
   
   * **Multipath**: geeft een lijst en configureert apparaten met meerdere paden.
   * **Meerdere** paden: daemon waarmee meerdere paden worden uitgevoerd en de paden worden bewaakt.
   * **Devmap-name**: biedt een herken bare apparaat-naam voor udev voor devmaps.
   * **Kpartx**: wijst lineaire devmaps toe aan toepassingsmappartities om meerdere paden partitionisch te maken.
   * **Multipath. conf**: configuratie bestand voor multipath daemon dat wordt gebruikt om de ingebouwde configuratie tabel te overschrijven.

### <a name="about-the-multipathconf-configuration-file"></a>Over het configuratie bestand voor meerdere paden. conf
Het configuratie bestand `/etc/multipath.conf` maakt veel van de multipath-functies die door de gebruiker kunnen worden geconfigureerd. De `multipath` opdracht en de kernel-daemon `multipathd` gebruiken informatie die in dit bestand is gevonden. Het bestand wordt alleen geraadpleegd tijdens de configuratie van de multipath-apparaten. Zorg ervoor dat alle wijzigingen zijn aangebracht voordat u de `multipath` opdracht uitvoert. Als u het bestand later wijzigt, moet u de meerdere paden stoppen en opnieuw starten om de wijzigingen van kracht te laten worden.

Het multipath. conf heeft vijf secties:

- **Standaard waarden systeem niveau** *(standaard instellingen)*: u kunt de standaard instellingen van het systeem niveau opheffen.
- **Apparaten in de zwarte** *lijst: u* kunt opgeven welke apparaten moeten worden beheerd door de toewijzing van het apparaat.
- **Black-uitzonde ringen** *(blacklist_exceptions)*: u kunt specifieke apparaten identificeren die moeten worden behandeld als apparaten met meerdere paden, zelfs als deze worden vermeld in de blokkerings lijst.
- **Specifieke instellingen voor de opslag controller** *(apparaten)*: u kunt configuratie-instellingen opgeven die worden toegepast op apparaten met leverancier-en product informatie.
- **Apparaatspecifieke instellingen** *(meerdere paden)*: u kunt deze sectie gebruiken om de configuratie-instellingen voor afzonderlijke lun's te verfijnen.

## <a name="configure-multipathing-on-storsimple-connected-to-linux-host"></a>Meerdere paden configureren op StorSimple die zijn verbonden met de Linux-host
Een StorSimple-apparaat dat is verbonden met een Linux-host kan worden geconfigureerd voor hoge Beschik baarheid en taak verdeling. Als de Linux-host bijvoorbeeld twee interfaces heeft die zijn verbonden met het SAN en het apparaat twee interfaces heeft die zijn verbonden met het SAN, zodat deze interfaces zich op hetzelfde subnet bevinden, zijn er vier paden beschikbaar. Als elke gegevens interface op het apparaat en de host interface zich echter op een ander IP-subnet bevindt (en niet routeerbaar), zijn er slechts twee paden beschikbaar. U kunt meerdere paden configureren om automatisch alle beschik bare paden te detecteren, een taakverdelings algoritme voor die paden te kiezen, specifieke configuratie-instellingen toe te passen voor StorSimple volumes en vervolgens meerdere paden in te scha kelen en te controleren.

In de volgende procedure wordt beschreven hoe u meerdere paden configureert wanneer een StorSimple-apparaat met twee netwerk interfaces is verbonden met een host met twee netwerk interfaces.

## <a name="prerequisites"></a>Vereisten
In deze sectie vindt u meer informatie over de configuratie vereisten voor CentOS-server en uw StorSimple-apparaat.

### <a name="on-centos-host"></a>Op CentOS-host
1. Zorg ervoor dat op de CentOS-host twee netwerk interfaces zijn ingeschakeld. Type:
   
    `ifconfig`
   
    In het volgende voor beeld ziet u de uitvoer wanneer twee netwerk interfaces ( `eth0` en `eth1` ) aanwezig zijn op de host.
   
    ```output
    [root@centosSS ~]# ifconfig
    eth0  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:41  
        inet addr:10.126.162.65  Bcast:10.126.163.255  Mask:255.255.252.0
        inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3341/64 Scope:Global
        inet6 addr: fe80::215:5dff:fea2:3341/64 Scope:Link
        UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
        RX packets:36536 errors:0 dropped:0 overruns:0 frame:0
        TX packets:6312 errors:0 dropped:0 overruns:0 carrier:0
        collisions:0 txqueuelen:1000
        RX bytes:13994127 (13.3 MiB)  TX bytes:645654 (630.5 KiB)

    eth1  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:42  
        inet addr:10.126.162.66  Bcast:10.126.163.255  Mask:255.255.252.0
        inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3342/64 Scope:Global
        inet6 addr: fe80::215:5dff:fea2:3342/64 Scope:Link
        UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
        RX packets:25962 errors:0 dropped:0 overruns:0 frame:0
        TX packets:11 errors:0 dropped:0 overruns:0 carrier:0
        collisions:0 txqueuelen:1000
        RX bytes:2597350 (2.4 MiB)  TX bytes:754 (754.0 b)

    loLink encap:Local Loopback  
        inet addr:127.0.0.1  Mask:255.0.0.0
        inet6 addr: ::1/128 Scope:Host
        UP LOOPBACK RUNNING  MTU:65536  Metric:1
        RX packets:12 errors:0 dropped:0 overruns:0 frame:0
        TX packets:12 errors:0 dropped:0 overruns:0 carrier:0
        collisions:0 txqueuelen:0
        RX bytes:720 (720.0 b)  TX bytes:720 (720.0 b)
    ```
1. Installeer *iSCSI-initiator-hulppr* . op uw CentOS-server. Voer de volgende stappen uit om *iSCSI-initiator-hulppr*. te installeren.
   
   1. Meld u aan bij `root` uw CentOS-host.
   1. Installeer de *iSCSI-initiator-hulppr*. Type:
      
       `yum install iscsi-initiator-utils`
   1. Nadat de *iSCSI-initiator-hulppr* . is geïnstalleerd, start u de iSCSI-service. Type:
      
       `service iscsid start`
      
       In `iscsid` de praktijk wordt mogelijk niet daad werkelijk gestart en is de `--force` optie mogelijk vereist
   1. Gebruik de `chkconfig` opdracht om de service in te scha kelen om ervoor te zorgen dat uw iSCSI-initiator tijdens de opstart tijd is ingeschakeld.
      
       `chkconfig iscsi on`
   1. Voer de volgende opdracht uit om te controleren of deze correct is ingesteld:
      
       `chkconfig --list | grep iscsi`
      
       Hieronder ziet u een voorbeeld van de uitvoer.
      
        ```output
        iscsi   0:off   1:off   2:on3:on4:on5:on6:off
        iscsid  0:off   1:off   2:on3:on4:on5:on6:off
        ```
      
       In het bovenstaande voor beeld kunt u zien dat uw iSCSI-omgeving wordt uitgevoerd op de opstart tijd op uitvoerings niveaus 2, 3, 4 en 5.
1. *Apparaatinstallatie-Mapper-meerdere paden*. Type:
   
    `yum install device-mapper-multipath`
   
    De installatie wordt gestart. Typ **j** om door te gaan als u om bevestiging wordt gevraagd.

### <a name="on-storsimple-device"></a>Op StorSimple-apparaat
Uw StorSimple-apparaat moet het volgende hebben:

* Ten minste twee interfaces zijn ingeschakeld voor iSCSI. Als u wilt controleren of twee interfaces op uw StorSimple-apparaat zijn ingeschakeld, voert u de volgende stappen uit in de klassieke Azure-portal voor uw StorSimple-apparaat:
  
  1. Meld u aan bij de klassieke portal voor uw StorSimple-apparaat.
  1. Selecteer uw StorSimple Manager-service, klik op **apparaten** en kies het specifieke StorSimple-apparaat. Klik op **configureren** en controleer de instellingen van de netwerk interface. Hieronder ziet u een scherm opname met twee netwerk interfaces met iSCSI-functionaliteit. Hier gegevens 2 en DATA 3 zijn beide 10 GbE-interfaces ingeschakeld voor iSCSI.
     
      ![MPIO StorsSimple DATA 2 config](./media/storsimple-configure-mpio-on-linux/IC761347.png)
     
      ![MPIO StorSimple DATA 3 config](./media/storsimple-configure-mpio-on-linux/IC761348.png)
     
      Op de pagina **configureren**
     
     1. Zorg ervoor dat voor beide netwerk interfaces iSCSI is ingeschakeld. Het veld **iSCSI ingeschakeld** moet worden ingesteld op **Ja**.
     1. Zorg ervoor dat de netwerk interfaces dezelfde snelheid hebben, beide moeten 1 GbE of 10 GbE hebben.
     1. Noteer de IPv4-adressen van de iSCSI-interfaces en sla deze op voor later gebruik op de host.
* De iSCSI-interfaces op uw StorSimple-apparaat moeten bereikbaar zijn vanaf de CentOS-server.
      Als u dit wilt controleren, moet u de IP-adressen van uw StorSimple iSCSI-netwerk interfaces op de hostserver opgeven. De gebruikte opdrachten en de bijbehorende uitvoer met BESTAND2 (10.126.162.25) en DATA3 (10.126.162.26) worden hieronder weer gegeven:
  
    ```console
    [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
    ```

### <a name="hardware-configuration"></a>Hardwareconfiguratie
U wordt aangeraden de twee iSCSI-netwerk interfaces op afzonderlijke paden te verbinden voor redundantie. In de afbeelding hieronder ziet u de aanbevolen hardwareconfiguratie voor hoge Beschik baarheid en meerdere paden voor taak verdeling voor uw CentOS-server en StorSimple-apparaat.

![MPIO-hardwareconfiguratie voor StorSimple naar Linux-host](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

Zoals in de vorige afbeelding wordt weer gegeven:

* Uw StorSimple-apparaat bevindt zich in een actief-passieve configuratie met twee controllers.
* Twee SAN-switches zijn verbonden met uw apparaat-controllers.
* Er zijn twee iSCSI-initia tors ingeschakeld op uw StorSimple-apparaat.
* Er zijn twee netwerk interfaces ingeschakeld op de CentOS-host.

Met de bovenstaande configuratie worden vier afzonderlijke paden tussen uw apparaat en de host opgeleverd als de host en de gegevens interfaces routeerbaar zijn.

> [!IMPORTANT]
> * We raden u aan om 1 GbE-en 10 GbE-netwerk interfaces niet te combi neren voor meerdere paden. Wanneer u twee netwerk interfaces gebruikt, moeten beide interfaces van hetzelfde type zijn.
> * Op uw StorSimple-apparaat zijn DATA0, BESTAND1, DATA4 en DATA5 1 GbE-interfaces, terwijl DATA2 en DATA3 10 GbE-netwerk interfaces zijn. |
> 
> 

## <a name="configuration-steps"></a>Configuratiestappen
Bij de configuratie stappen voor meerdere paden moet u de beschik bare paden configureren voor automatische detectie, het te gebruiken taakverdelings algoritme opgeven, meerdere paden inschakelen en de configuratie ten slotte controleren. Elk van deze stappen wordt gedetailleerd beschreven in de volgende secties.

### <a name="step-1-configure-multipathing-for-automatic-discovery"></a>Stap 1: meerdere paden configureren voor automatische detectie
De apparaten die door meerdere paden worden ondersteund, kunnen automatisch worden gedetecteerd en geconfigureerd.

1. Bestand initialiseren `/etc/multipath.conf` . Type:
   
     `mpathconf --enable`
   
    Met de bovenstaande opdracht wordt een `sample/etc/multipath.conf` bestand gemaakt.
1. Start multipath service. Type:
   
    `service multipathd start`
   
    U ziet de volgende uitvoer:
   
    `Starting multipathd daemon:`
1. Automatische detectie van meerdere paden inschakelen. Type:
   
    `mpathconf --find_multipaths y`
   
    Hiermee wijzigt u de standaard instellingen in de sectie `multipath.conf` zoals hieronder wordt weer gegeven:
   
    ```config
    defaults {
    find_multipaths yes
    user_friendly_names yes
    path_grouping_policy multibus
    }
    ```

### <a name="step-2-configure-multipathing-for-storsimple-volumes"></a>Stap 2: meerdere paden configureren voor StorSimple-volumes
Standaard zijn alle apparaten blocklisted in het bestand met meerdere paden. conf en worden overgeslagen. U moet blokkerings lijst-uitzonde ringen maken om meerdere paden voor volumes van StorSimple-apparaten toe te staan.

1. Het bestand `/etc/mulitpath.conf` bewerken. Type:
   
    `vi /etc/multipath.conf`
1. Zoek de sectie blacklist_exceptions in het bestand met meerdere paden. conf. Uw StorSimple-apparaat moet in deze sectie worden vermeld als een blokkerings lijst-uitzonde ring. U kunt opmerkingen van relevante regels in dit bestand opheffen om deze te wijzigen, zoals hieronder wordt weer gegeven (gebruik alleen het specifieke model van het apparaat dat u gebruikt):
   
    ```config
    blacklist_exceptions {
        device {
                    vendor  "MSFT"
                    product "STORSIMPLE 8100*"
        }
        device {
                    vendor  "MSFT"
                    product "STORSIMPLE 8600*"
        }
    }
    ```

### <a name="step-3-configure-round-robin-multipathing"></a>Stap 3: meerdere paden configureren met Round Robin
Deze taakverdelings algoritme gebruikt alle beschik bare meerdere paden naar de actieve controller op een evenwichtige, Round-Robin manier.

1. Het bestand `/etc/multipath.conf` bewerken. Type:
   
    `vi /etc/multipath.conf`
1. Stel in de `defaults` sectie in `path_grouping_policy` op `multibus` . `path_grouping_policy`Hiermee geeft u het groeps beleid voor standaard paden op dat moet worden toegepast op niet-opgegeven meerdere paden. De sectie standaard waarden ziet er ongeveer uit zoals hieronder wordt weer gegeven.
   
    ```config
    defaults {
            user_friendly_names yes
            path_grouping_policy multibus
    }
    ```

> [!NOTE]
> De meest voorkomende waarden `path_grouping_policy` zijn onder andere:
> 
> * failover = 1 pad per prioriteits groep
> * multibus = alle geldige paden in de groep 1 prioriteit
> 
> 

### <a name="step-4-enable-multipathing"></a>Stap 4: meerdere paden inschakelen
1. Start de `multipathd` daemon opnieuw. Type:
   
    `service multipathd restart`
1. De uitvoer ziet er als volgt uit:
   
    ```output
    [root@centosSS ~]# service multipathd start
    Starting multipathd daemon:  [OK]
    ```

### <a name="step-5-verify-multipathing"></a>Stap 5: meerdere paden controleren
1. Controleer eerst of de iSCSI-verbinding met het StorSimple-apparaat als volgt is ingesteld:
   
   a. Ontdek uw StorSimple-apparaat. Type:
      
    `iscsiadm -m discovery -t sendtargets -p  <IP address of network interface on the device>:<iSCSI port on StorSimple device>`
    
    De uitvoer wanneer IP-adres voor DATA0 is 10.126.162.25 en poort 3260 wordt geopend op het StorSimple-apparaat voor uitgaand iSCSI-verkeer, zoals hieronder wordt weer gegeven:
    
    ```output
    10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
    ```

    Kopieer de IQN van uw StorSimple-apparaat, `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target` van de voor gaande uitvoer.

   b. Maak verbinding met het apparaat via de doel-IQN. Het StorSimple-apparaat is hier het iSCSI-doel. Type:

      `iscsiadm -m node --login -T <IQN of iSCSI target>`

    In het volgende voor beeld ziet u de uitvoer met een IQN van het doel van `iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target` . De uitvoer geeft aan dat u verbinding hebt gemaakt met de twee iSCSI-netwerk interfaces op het apparaat.

    ```output
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
    Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
    Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
    ```

    Als u hier slechts één host-interface en twee paden ziet, moet u beide interfaces op de host inschakelen voor iSCSI. U kunt de [gedetailleerde instructies in de Linux-documentatie](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html)volgen.

1. Een volume wordt weer gegeven op de CentOS-server van het StorSimple-apparaat. Zie voor meer informatie [stap 6: een volume maken](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) via de Azure Portal op uw StorSimple-apparaat.

1. Controleer de beschik bare paden. Type:

    `multipath -l`

      In het volgende voor beeld ziet u de uitvoer voor twee netwerk interfaces op een StorSimple-apparaat dat is verbonden met een netwerk interface met één host en twee beschik bare paden.

    ```output
    mpathb (36486fd20cc081f8dcd3fccb992d45a68) dm-3 MSFT,STORSIMPLE 8100
    size=100G features='0' hwhandler='0' wp=rw
    `-+- policy='round-robin 0' prio=0 status=active
    |- 7:0:0:1 sdc 8:32 active undef running
    `- 6:0:0:1 sdd 8:48 active undef running
    ```

    In het volgende voor beeld ziet u de uitvoer voor twee netwerk interfaces op een StorSimple-apparaat dat is verbonden met twee host netwerk interfaces met vier beschik bare paden.

    ```output
    mpathb (36486fd27a23feba1b096226f11420f6b) dm-2 MSFT,STORSIMPLE 8100
    size=100G features='0' hwhandler='0' wp=rw
    `-+- policy='round-robin 0' prio=0 status=active
    |- 17:0:0:0 sdb 8:16 active undef running
    |- 15:0:0:0 sdd 8:48 active undef running
    |- 14:0:0:0 sdc 8:32 active undef running
    `- 16:0:0:0 sde 8:64 active undef running
    ```

    Nadat de paden zijn geconfigureerd, raadpleegt u de specifieke instructies op het hostbesturingssysteem (CentOS 6,6) om dit volume te koppelen en te Format teren.

## <a name="troubleshoot-multipathing"></a>Problemen met meerdere paden oplossen
In deze sectie vindt u nuttige tips als u problemen ondervindt tijdens het configureren van meerdere paden.

V. Ik zie de wijzigingen in het bestand worden niet `multipath.conf` doorgevoerd.

A. Als u wijzigingen hebt aangebracht in het `multipath.conf` bestand, moet u de multipath-service opnieuw starten. Typ de volgende opdracht:

`service multipathd restart`

V. Ik heb twee netwerk interfaces ingeschakeld op het StorSimple-apparaat en twee netwerk interfaces op de host. Wanneer ik de beschik bare paden vermeld, worden er slechts twee paden weer geven. Ik verwacht vier beschik bare paden te zien.

A. Zorg ervoor dat de twee paden zich in hetzelfde subnet bevinden en routeerbaar zijn. Als de netwerk interfaces zich op verschillende Vlan's bevinden en niet routeerbaar zijn, worden er slechts twee paden weer geven. Een manier om dit te controleren, is om ervoor te zorgen dat u de interfaces van de host kunt bereiken vanuit een netwerk interface op het StorSimple-apparaat. U moet [contact opnemen met Microsoft ondersteuning](storsimple-8000-contact-microsoft-support.md) als deze verificatie alleen kan worden uitgevoerd via een ondersteunings sessie.

V. Wanneer ik beschik bare paden vermeld, zie ik geen uitvoer.

A. Als er geen paden met meerdere paden worden weer gegeven, wordt er meestal een probleem met de multipath daemon voorgesteld. het is waarschijnlijk dat er een probleem is in het `multipath.conf` bestand.

Het is ook een goed idee om te controleren of u bepaalde schijven daad werkelijk kunt zien nadat u verbinding hebt gemaakt met het doel, omdat er ook geen enkele schijf hoeft te worden beantwoord.

* Gebruik de volgende opdracht om de SCSI-bus opnieuw te scannen:
  
    `$ rescan-scsi-bus.sh` (onderdeel van sg3_utils pakket)
* Typ de volgende opdrachten:
  
    `$ dmesg | grep sd*`
     
     of
  
    `$ fdisk -l`
  
    Deze geven details over recent toegevoegde schijven.
* Gebruik de volgende opdrachten om te bepalen of het een StorSimple-schijf is:
  
    `cat /sys/block/<DISK>/device/model`
  
    Hiermee wordt een teken reeks geretourneerd die bepaalt of het een StorSimple-schijf is.

Een minder waarschijnlijke, maar mogelijke oorzaak kan ook een verouderde PID van iSCSI zijn. Gebruik de volgende opdracht om u af te melden bij de iSCSI-sessies:

`iscsiadm -m node --logout -p <Target_IP>`

Herhaal deze opdracht voor alle verbonden netwerk interfaces op het iSCSI-doel, wat het StorSimple-apparaat is. Zodra u zich hebt afgemeld bij alle iSCSI-sessies, gebruikt u de IQN van het iSCSI-doel om de iSCSI-sessie opnieuw tot stand te brengen. Typ de volgende opdracht:

`iscsiadm -m node --login -T <TARGET_IQN>`


V. Ik weet niet of mijn apparaat is toegestaan.

A. Gebruik de volgende procedure voor het oplossen van problemen met de interactieve opdracht om te controleren of het apparaat is toegestaan:

```console
multipathd -k
multipathd> show devices
available block devices:
ram0 devnode blacklisted, unmonitored
ram1 devnode blacklisted, unmonitored
ram2 devnode blacklisted, unmonitored
ram3 devnode blacklisted, unmonitored
ram4 devnode blacklisted, unmonitored
ram5 devnode blacklisted, unmonitored
ram6 devnode blacklisted, unmonitored
ram7 devnode blacklisted, unmonitored
ram8 devnode blacklisted, unmonitored
ram9 devnode blacklisted, unmonitored
ram10 devnode blacklisted, unmonitored
ram11 devnode blacklisted, unmonitored
ram12 devnode blacklisted, unmonitored
ram13 devnode blacklisted, unmonitored
ram14 devnode blacklisted, unmonitored
ram15 devnode blacklisted, unmonitored
loop0 devnode blacklisted, unmonitored
loop1 devnode blacklisted, unmonitored
loop2 devnode blacklisted, unmonitored
loop3 devnode blacklisted, unmonitored
loop4 devnode blacklisted, unmonitored
loop5 devnode blacklisted, unmonitored
loop6 devnode blacklisted, unmonitored
loop7 devnode blacklisted, unmonitored
sr0 devnode blacklisted, unmonitored
sda devnode whitelisted, monitored
dm-0 devnode blacklisted, unmonitored
dm-1 devnode blacklisted, unmonitored
dm-2 devnode blacklisted, unmonitored
sdb devnode whitelisted, monitored
sdc devnode whitelisted, monitored
dm-3 devnode blacklisted, unmonitored
```


Ga voor meer informatie naar [probleem oplossing voor meerdere paden](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/mpio_admin-troubleshoot).

## <a name="list-of-useful-commands"></a>Lijst met handige opdrachten
| Type | Opdracht | Beschrijving |
| --- | --- | --- |
| **iSCSI** |`service iscsid start` |ISCSI-service starten |
| &nbsp; |`service iscsid stop` |ISCSI-service stoppen |
| &nbsp; |`service iscsid restart` |De iSCSI-service opnieuw starten |
| &nbsp; |`iscsiadm -m discovery -t sendtargets -p <TARGET_IP>` |Beschik bare doelen op het opgegeven adres detecteren |
| &nbsp; |`iscsiadm -m node --login -T <TARGET_IQN>` |Aanmelden bij het iSCSI-doel |
| &nbsp; |`iscsiadm -m node --logout -p <Target_IP>` |Afmelden bij het iSCSI-doel |
| &nbsp; |`cat /etc/iscsi/initiatorname.iscsi` |Naam van iSCSI-initiator afdrukken |
| &nbsp; |`iscsiadm -m session -s <sessionid> -P 3` |Controleer de status van de iSCSI-sessie en het volume dat op de host is gedetecteerd |
| &nbsp; |`iscsi -m session` |Toont alle iSCSI-sessies die zijn gemaakt tussen de host en het StorSimple-apparaat |
|  | | |
| **Gebruik van meerdere paden** |`service multipathd start` |Multipath daemon starten |
| &nbsp; |`service multipathd stop` |Multipath daemon stoppen |
| &nbsp; |`service multipathd restart` |Multipath daemon opnieuw starten |
| &nbsp; |`chkconfig multipathd on` </br> OF </br> `mpathconf -with_chkconfig y` |Multipath daemon inschakelen om te starten op het moment van opstarten |
| &nbsp; |`multipathd -k` |De interactieve console starten voor het oplossen van problemen |
| &nbsp; |`multipath -l` |Meerdere paden en apparaten weer geven |
| &nbsp; |`mpathconf --enable` |Maak een voor beeld van een bestand mulitpath. conf in `/etc/mulitpath.conf` |
|  | | |

## <a name="next-steps"></a>Volgende stappen
Tijdens het configureren van MPIO op een Linux-host moet u mogelijk ook de volgende CentoS 6,6-documenten raadplegen:

* [MPIO instellen op CentOS](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/dm_multipath/index)
* [Linux-trainings gids](http://linux-training.be/linuxsys.pdf)
