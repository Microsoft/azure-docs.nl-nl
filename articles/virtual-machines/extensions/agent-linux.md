---
title: Overzicht van Azure Linux VM-agent
description: Meer informatie over het installeren en configureren van Linux-agent (waagent) voor het beheren van de interactie van uw virtuele machine met de Azure Fabric-controller.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: linux
ms.date: 10/17/2016
ms.openlocfilehash: e8851ddd5211536394614727d990a2b52d32bfcc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565374"
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Meer informatie over het gebruik van de Azure Linux-agent

De Microsoft Azure Linux-agent (waagent) beheert Linux & FreeBSD-inrichting en VM-interactie met de Azure Fabric-controller. Naast de Linux-agent die inrichtings functionaliteit biedt, biedt Azure ook de mogelijkheid om Cloud-init te gebruiken voor bepaalde Linux-besturings systemen. De Linux-agent biedt de volgende functionaliteit voor Linux-en FreeBSD IaaS-implementaties:

> [!NOTE]
> Raadpleeg het [Leesmij-bestand](https://github.com/Azure/WALinuxAgent/blob/master/README.md)voor meer informatie.
> 
> 

* **Installatie kopie inrichten**
  
  * Een gebruikersaccount maken
  * SSH-verificatietypen configureren
  * Implementatie van openbare SSH-sleutels en sleutelparen
  * De hostnaam instellen
  * De hostnaam publiceren naar het platform-DNS
  * De vingerafdruk van de SSH-hostwaarde rapporteren aan het platform
  * Resourceschijven beheren
  * De resourceschijf formatteren en koppelen
  * Wissel ruimte configureren
* **Netwerken**
  
  * Beheert routes om de compatibiliteit met DHCP-platformservers te verbeteren
  * Zorgt voor de stabiliteit van de naam van de netwerkinterface
* **Kernel**
  
  * Hiermee configureert u virtuele NUMA (uitschakelen voor kernel <`2.6.37`)
  * Gebruikt Hyper-V entropie voor/dev/random
  * Hiermee configureert u SCSI-time-outs voor het hoofdapparaat (dit kan extern zijn)
* **Diagnostics**
  
  * Console-omleiding naar de seriële poort
* **SCVMM-implementaties**
  
  * Detecteert en Boots trapt de VMM-agent voor Linux wanneer deze wordt uitgevoerd in een System Center Virtual Machine Manager 2012 R2-omgeving
* **VM-extensie**
  
  * Het inbrengen van het onderdeel dat is geschreven door micro soft en partners in Linux VM (IaaS) om software-en configuratie automatisering in te scha kelen
  * VM-extensie referentie-implementatie op [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Communicatie
De informatiestroom van het platform naar de agent vindt plaats via twee kanalen:

* Een DVD die is gekoppeld aan het opstarttijdstip voor IaaS-implementaties. Deze DVD bevat een OVF-compatibel configuratie bestand dat alle inrichtings gegevens bevat, behalve de daad werkelijke SSH-sleutel paars.
* Een TCP-eind punt geeft een REST API die wordt gebruikt voor het verkrijgen van implementatie-en topologie configuratie.

## <a name="requirements"></a>Vereisten
De volgende systemen zijn getest en bekend bij het werken met de Azure Linux-agent:

> [!NOTE]
> Deze lijst kan afwijken van de officiële lijst met [ondersteunde distributies](../linux/endorsed-distros.md).
> 
> 

* CoreOS
* CentOS 6.3 +
* Red Hat Enterprise Linux 6,7 +
* Debian 7.0 +
* Ubuntu 12.04 +
* openSUSE 12.3 +
* SLES 11 SP3 +
* Oracle Linux 6.4 +

Andere ondersteunde systemen:

* FreeBSD 10 + (Azure Linux-agent v 2.0.10 +)

De Linux-agent is afhankelijk van sommige systeem pakketten om goed te kunnen functioneren:

* Python 2.6 of later
* OpenSSL 1.0 of later
* OpenSSH 5.3 of later
* Bestandssysteem hulpprogramma's: sfdisk, fdisk, mkfs, opgenomen
* Hulpmiddelen voor wachtwoord: chpasswd, sudo
* Hulpmiddelen voor tekstverwerking: sed, grep
* Hulpprogramma's voor netwerk: ip-route
* Kernelondersteuning voor het koppelen van UDF-bestandssysteem.

Zorg ervoor dat uw virtuele machine toegang heeft tot het IP-adres 168.63.129.16. Zie [Wat is IP-adres 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)? voor meer informatie.


## <a name="installation"></a>Installatie
Installatie met behulp van een RPM-of een DEB-pakket uit de opslag plaats van uw distributie pakket is de voorkeurs methode voor het installeren en upgraden van de Azure Linux-agent. Alle [gewaarmerkte distributie providers](../linux/endorsed-distros.md) integreren het Azure Linux-agent pakket in hun installatie kopieën en opslag plaatsen.

Raadpleeg de documentatie in de [Azure Linux-agent opslag plaats op github](https://github.com/Azure/WALinuxAgent) voor geavanceerde installatie opties, zoals het installeren van bron of naar aangepaste locaties of voor voegsels.

## <a name="command-line-options"></a>Command-Line opties
### <a name="flags"></a>Markering
* uitgebreid: Verhoog de uitgebreide waarde van de opgegeven opdracht
* geforceerd: interactieve bevestiging voor sommige opdrachten overs Laan

### <a name="commands"></a>Opdracht
* Help: geeft een lijst van de ondersteunde opdrachten en vlaggen.
* inrichting opheffen: Probeer het systeem op te schonen en maak het geschikt voor opnieuw inrichten. De volgende bewerking wordt verwijderd:
  
  * Alle SSH-host-sleutels (indien ingericht. RegenerateSshHostKeyPair is ' y ' in het configuratie bestand)
  * Naam server-configuratie in/etc/resolv.conf
  * Het Hoofdwacht woord van/etc/shadow (indien inrichting. DeleteRootPassword is ' y ' in het configuratie bestand)
  * DHCP-clientleases in cache
  * Hiermee wordt de naam van de host opnieuw ingesteld op localhost. localdomain

> [!WARNING]
> Het ongedaan maken van de inrichting garandeert niet dat de installatie kopie is gewist van alle gevoelige informatie en is geschikt voor herdistributie.
> 
> 

* inrichting opheffen + gebruiker: voert de volledige inrichting uit (boven) en verwijdert ook de laatste ingerichte gebruikers account (verkregen door/var/lib/waagent) en de bijbehorende gegevens. Deze para meter wordt gebruikt bij het ongedaan maken van de inrichting van een installatie kopie die eerder is ingericht op Azure, zodat deze kan worden vastgelegd en opnieuw kan worden gebruikt.
* versie: geeft de versie van waagent
* serialconsole: configureert GRUB om ttyS0 (de eerste seriële poort) als opstart console te markeren. Dit zorgt ervoor dat de opstart chassis-logboeken van de kernel worden verzonden naar de seriële poort en beschikbaar worden gesteld voor fout opsporing.
* daemon: Voer waagent uit als een daemon om de interactie met het platform te beheren. Dit argument is opgegeven voor waagent in het waagent init-script.
* starten: Voer waagent uit als achtergrond proces

## <a name="configuration"></a>Configuratie
Een configuratie bestand (/etc/waagent.conf) regelt de acties van waagent. Hieronder ziet u een voor beeld van een configuratie bestand:

```config
Provisioning.Enabled=y
Provisioning.DeleteRootPassword=n
Provisioning.RegenerateSshHostKeyPair=y
Provisioning.SshHostKeyPairType=rsa
Provisioning.MonitorHostName=y
Provisioning.DecodeCustomData=n
Provisioning.ExecuteCustomData=n
Provisioning.AllowResetSysUser=n
Provisioning.PasswordCryptId=6
Provisioning.PasswordCryptSaltLength=10
ResourceDisk.Format=y
ResourceDisk.Filesystem=ext4
ResourceDisk.MountPoint=/mnt/resource
ResourceDisk.MountOptions=None
ResourceDisk.EnableSwap=n
ResourceDisk.SwapSizeMB=0
LBProbeResponder=y
Logs.Verbose=n
OS.RootDeviceScsiTimeout=300
OS.OpensslPath=None
HttpProxy.Host=None
HttpProxy.Port=None
AutoUpdate.Enabled=y
```

De volgende verschillende configuratie opties worden beschreven. Configuratie opties zijn van drie typen. Booleaans, teken reeks of geheel getal. De opties voor de Booleaanse configuratie kunnen worden opgegeven als "y" of "n". Het speciale sleutel woord ' geen ' kan worden gebruikt voor bepaalde configuratie vermeldingen voor teken reeksen als de volgende details:

**Inrichting. ingeschakeld:**  
```txt
Type: Boolean  
Default: y
```
Hiermee kan de gebruiker de inrichtings functionaliteit in de agent in-of uitschakelen. Geldige waarden zijn "y" of "n". Als het inrichten is uitgeschakeld, worden de SSH-host en de gebruikers sleutels in de installatie kopie behouden en wordt de configuratie die is opgegeven in de Azure-inrichtings-API genegeerd.

> [!NOTE]
> De `Provisioning.Enabled` para meter wordt standaard ingesteld op ' n ' op Ubuntu Cloud-installatie kopieën die gebruikmaken van Cloud-init voor het inrichten.
> 
> 

**Provisioning. DeleteRootPassword:**  
```txt
Type: Boolean  
Default: n
```
Als deze instelling is ingesteld, wordt het hoofd wachtwoord in het/etc/shadow-bestand gewist tijdens het inrichtings proces.

**Provisioning. RegenerateSshHostKeyPair:**  
```txt
Type: Boolean  
Default: y
```
Als deze instelling is ingesteld, worden alle sleutel paren van SSH-hosts (ECDSA, DSA en RSA) verwijderd tijdens het inrichtings proces van/etc/ssh/. En er wordt één nieuw sleutel paar gegenereerd.

Het versleutelings type voor het nieuwe sleutel paar kan worden geconfigureerd door de inrichtings SshHostKeyPairType-vermelding. Bij sommige distributies worden SSH-sleutel paren opnieuw gemaakt voor ontbrekende versleutelings typen wanneer de SSH-daemon opnieuw wordt gestart (bijvoorbeeld bij het opnieuw opstarten).

**Provisioning. SshHostKeyPairType:**  
```txt
Type: String  
Default: rsa
```
Dit kan worden ingesteld op een type versleutelings algoritme dat wordt ondersteund door de SSH-daemon op de virtuele machine. De doorgaans ondersteunde waarden zijn ' RSA ', ' DSA ' en ' ECDSA '. ' putty.exe ' op Windows biedt geen ondersteuning voor ' ECDSA '. Als u van plan bent om putty.exe in Windows te gebruiken om verbinding te maken met een Linux-implementatie, gebruikt u ' RSA ' of ' DSA '.

**Provisioning. MonitorHostName:**  
```txt
Type: Boolean  
Default: y
```
Als deze optie is ingesteld, controleert waagent de virtuele Linux-machine op hostnamen (zoals geretourneerd door de opdracht ' hostname ') en werkt de netwerk configuratie in de installatie kopie automatisch bij om de wijziging door te voeren. Als u de naam wijziging naar de DNS-servers wilt pushen, wordt het netwerk opnieuw gestart in de virtuele machine. Dit resulteert in een kort verlies van Internet verbinding.

**Provisioning. DecodeCustomData**  
```txt
Type: Boolean  
Default: n
```
Indien ingesteld, waagent decodeert CustomData uit base64.

**Provisioning.ExecuteCustomData**  
```txt
Type: Boolean  
Default: n
```
Als deze instelling is ingesteld, wordt CustomData na het inrichten door waagent uitgevoerd.

**Provisioning. AllowResetSysUser**
```txt
Type: Boolean
Default: n
```
Met deze optie kan het wacht woord voor de sys-gebruiker opnieuw worden ingesteld. de standaard waarde is uitgeschakeld.

**Provisioning. PasswordCryptId**  
```txt
Type: String  
Default: 6
```
Algoritme dat door crypt wordt gebruikt bij het genereren van de wachtwoord-hash.  
 1-MD5  
 2a-blowfish  
 5-SHA-256  
 6-SHA-512  

**Provisioning. PasswordCryptSaltLength**  
```txt
Type: String  
Default: 10
```
Lengte van wille keurig zout dat wordt gebruikt bij het genereren van een wacht woord-hash.

**ResourceDisk. Format:**  
```txt
Type: Boolean  
Default: y
```
Als deze is ingesteld, wordt de bron schijf die door het platform wordt opgegeven, geformatteerd en gekoppeld door waagent als het bestandssysteem type dat door de gebruiker in ResourceDisk. File System is aangevraagd, een andere is dan NTFS. Er wordt één partitie van het type Linux (83) beschikbaar gemaakt op de schijf. Deze partitie is niet geformatteerd als deze kan worden gekoppeld.

**ResourceDisk. bestands systeem:**  
```txt
Type: String  
Default: ext4
```
Hiermee geeft u het bestandssysteem type voor de bron schijf op. Ondersteunde waarden variëren per Linux-distributie. Als de teken reeks X is, wordt mkfs. X moet aanwezig zijn op de Linux-installatie kopie. SLES 11-afbeeldingen moeten normaal gesp roken gebruikmaken van ' ext3 '. FreeBSD-installatie kopieën moeten hier ' UFS2 ' gebruiken.

**ResourceDisk. Koppel punt:**  
```txt
Type: String  
Default: /mnt/resource 
```
Hiermee geeft u het pad op waar de bron schijf is gekoppeld. De bron schijf is een *tijdelijke* schijf en kan worden leeg gemaakt wanneer de inrichting van de virtuele machine ongedaan is.

**ResourceDisk.MountOptions**  
```txt
Type: String  
Default: None
```
Hiermee geeft u de opties voor schijf koppeling op die moeten worden door gegeven aan de opdracht mount-o. Dit is een door komma's gescheiden lijst met waarden, bijvoorbeeld 'nodev,nosuid'. Zie mount (8) voor meer informatie.

**ResourceDisk.EnableSwap:**  
```txt
Type: Boolean  
Default: n
```
Als deze instelling is ingesteld, wordt er een wissel bestand (/swapfile) gemaakt op de bron schijf en toegevoegd aan de wissel ruimte van het systeem.

**ResourceDisk.SwapSizeMB:**  
```txt
Type: Integer  
Default: 0
```
De grootte van het wissel bestand in mega bytes.

**Logboeken. uitgebreid:**  
```txt
Type: Boolean  
Default: n
```
Als deze instelling is ingesteld, wordt de uitgebreidheid van het logboek verbeterd. Waagent meldt zich aan bij/var/log/waagent.log en maakt gebruik van de systeem logrotate-functionaliteit om logboeken te draaien.

**Vermelding. EnableRDMA**  
```txt
Type: Boolean  
Default: n
```
Als deze instelling is ingesteld, probeert de agent een RDMA-kernelstuurprogramma te installeren en vervolgens te laden dat overeenkomt met de versie van de firmware op de onderliggende hardware.

**Vermelding. RootDeviceScsiTimeout:**  
```txt
Type: Integer  
Default: 300
```
Met deze instelling configureert u de SCSI-time-out in seconden op de besturingssysteem schijf en de gegevens stations. Als deze niet is ingesteld, worden de standaard waarden van het systeem gebruikt.

**Vermelding. OpensslPath:**  
```txt
Type: String  
Default: None
```
Deze instelling kan worden gebruikt om een alternatief pad op te geven voor de binaire openssl die moet worden gebruikt voor cryptografische bewerkingen.

**Http proxy. host, http proxy. Port**  
```txt
Type: String  
Default: None
```
Als deze instelling is ingesteld, gebruikt de agent deze proxy server voor toegang tot internet. 

**Auto update. ingeschakeld**
```txt
Type: Boolean
Default: y
```
Automatisch bijwerken voor de doel status verwerking in-of uitschakelen; de standaard instelling is ingeschakeld.



## <a name="ubuntu-cloud-images"></a>Cloud installatie kopieën Ubuntu
Ubuntu Cloud-installatie kopieën maken gebruik van [Cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) voor het uitvoeren van veel configuratie taken die anders door de Azure Linux-agent zouden worden beheerd. De volgende verschillen zijn van toepassing:

* **Inrichten. ingeschakeld** standaard ingesteld op ' n ' voor Ubuntu-Cloud installatie kopieën die gebruikmaken van Cloud-init om inrichtings taken uit te voeren.
* De volgende configuratie parameters hebben geen effect op Ubuntu-Cloud installatie kopieën die gebruikmaken van Cloud-init voor het beheren van de bron schijf en wissel ruimte:
  
  * **ResourceDisk. Format**
  * **ResourceDisk. bestands systeem**
  * **ResourceDisk. Koppel punt**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**

* Raadpleeg de volgende bronnen voor meer informatie over het configureren van het koppel punt voor de bron schijf en het wisselen van ruimte op Ubuntu Cloud installatie kopieën tijdens het inrichten:
  
  * [Ubuntu-wiki: swap-partities configureren](https://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Aangepaste gegevens injecteren in een virtuele Azure-machine](../windows/tutorial-automate-vm-deployment.md)