---
title: Problemen met Azure Files oplossen in Linux | Microsoft Docs
description: Problemen met Azure Files problemen in Linux oplossen. Zie algemene problemen met betrekking tot Azure Files wanneer u verbinding maakt vanaf Linux-clients en mogelijke oplossingen vindt.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 10/16/2018
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: e680ba10c507ef83591b56652ee8e95c4d665dda
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96492060"
---
# <a name="troubleshoot-azure-files-problems-in-linux-smb"></a>Problemen met Azure Files oplossen in Linux (SMB)

In dit artikel vindt u algemene problemen die betrekking hebben op Azure Files wanneer u verbinding maakt vanaf Linux-clients. Het biedt ook mogelijke oorzaken en oplossingen voor deze problemen. 

Naast de stappen voor probleem oplossing in dit artikel, kunt u [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Linux) gebruiken om ervoor te zorgen dat de Linux-client over de juiste vereisten beschikt. AzFileDiagnostics automatiseert de detectie van de meeste symptomen die in dit artikel worden genoemd. Het helpt bij het instellen van uw omgeving om optimale prestaties te krijgen. U kunt deze informatie ook vinden in de [probleem oplosser Azure files-shares](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares). De probleem Oplosser biedt stappen om u te helpen bij het verbinden, koppelen en koppelen van Azure Files shares.

> [!IMPORTANT]
> De inhoud van dit artikel is alleen van toepassing op SMB-shares. Zie [problemen met Azure NFS-bestands shares oplossen](storage-troubleshooting-files-nfs.md)voor meer informatie over NFS-shares.

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Kan geen verbinding maken met een Azure-bestands share of deze koppelen

### <a name="cause"></a>Oorzaak

Veelvoorkomende oorzaken van dit probleem zijn:

- U gebruikt een niet-compatibele Linux-distributie-client. U wordt aangeraden de volgende Linux-distributies te gebruiken om verbinding te maken met een Azure-bestands share:

|   | SMB 2.1 <br>(Koppelt op Vm's binnen dezelfde Azure-regio) | SMB 3.0 <br>(Koppelt van on-premises en kruis regio's) |
| --- | :---: | :---: |
| **Ubuntu Server** | 14.04 + | 16.04 + |
| **RHEL** | 7 + | 7.5 + |
| **CentOS** | 7 + |  7.5 + |
| **Debian** | 8 + |   |
| **openSUSE** | 13.2 + | 42.3 + |
| **SUSE Linux Enterprise Server** | 12 | 12 SP3 + |

- CIFS-hulpprogram ma's (CIFS-hulppr.) zijn niet geïnstalleerd op de client.
- De minimale versie van SMB/CIFS, 2,1, is niet geïnstalleerd op de client.
- SMB 3,0-versleuteling wordt niet ondersteund op de client. De voor gaande tabel bevat een lijst met Linux-distributies die ondersteuning bieden voor het koppelen van on-premises en meerdere regio's met behulp van versleuteling. Voor andere distributies zijn kernel 4.11 en hogere versies vereist.
- U probeert verbinding te maken met een opslag account via TCP-poort 445, wat niet wordt ondersteund.
- U probeert verbinding te maken met een Azure-bestands share vanaf een virtuele Azure-machine en de virtuele machine bevindt zich niet in dezelfde regio als het opslag account.
- Als de instelling [beveiligde overdracht vereist]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) is ingeschakeld voor het opslag account, worden met Azure files alleen verbindingen toegestaan die gebruikmaken van SMB 3,0 met versleuteling.

### <a name="solution"></a>Oplossing

Om het probleem op te lossen, gebruikt u het [hulp programma voor probleem oplossing voor het Azure files koppelen van fouten in Linux](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Linux). Dit hulp programma:

* Helpt u bij het valideren van de client die omgeving uitvoert.
* Detecteert de niet-compatibele client configuratie waardoor er geen toegang kan worden Azure Files.
* Biedt prescriptieve richt lijnen voor zelf herstel.
* Verzamelt de diagnostische traceringen.

<a id="mounterror13"></a>
## <a name="mount-error13-permission-denied-when-you-mount-an-azure-file-share"></a>"Koppelings fout (13): de machtiging is geweigerd" wanneer u een Azure-bestands share koppelt

### <a name="cause-1-unencrypted-communication-channel"></a>Oorzaak 1: niet-versleuteld communicatie kanaal

Verbindingen met Azure-bestandsshares worden uit veiligheidsoverwegingen geblokkeerd als het communicatiekanaal niet is versleuteld en als de verbindingspoging niet is ondernomen vanuit hetzelfde datacenter als waar de Azure-bestandsshares zich bevinden. Niet-versleutelde verbindingen binnen hetzelfde datacenter kunnen ook worden geblokkeerd als de instelling [Veilige overdracht vereist](../common/storage-require-secure-transfer.md) is ingeschakeld in het opslagaccount. Er wordt alleen een versleuteld communicatiekanaal geboden als het clientbesturingssysteem van de gebruiker ondersteuning biedt voor SMB-versleuteling.

Zie [Vereisten voor het koppelen van een Azure-bestandsshare met Linux en het cifs-utils-pakket](storage-how-to-use-files-linux.md#prerequisites) voor meer informatie. 

### <a name="solution-for-cause-1"></a>Oplossing voor oorzaak 1

1. Verbinding maken vanaf een client die ondersteuning biedt voor SMB-versleuteling of verbinding maken vanaf een virtuele machine in hetzelfde Data Center als het Azure-opslag account dat wordt gebruikt voor de Azure-bestands share.
2. Controleer of de instelling [beveiligde overdracht vereist](../common/storage-require-secure-transfer.md) is uitgeschakeld op het opslag account als de client geen SMB-versleuteling ondersteunt.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Oorzaak 2: het virtuele netwerk of de firewall regels zijn ingeschakeld voor het opslag account 

Als er regels voor het VNET (virtueel netwerk) of de firewall zijn geconfigureerd in het opslagaccount, wordt netwerkverkeer de toegang geweigerd, tenzij het IP-adres van de client of het virtuele netwerk toegang heeft.

### <a name="solution-for-cause-2"></a>Oplossing voor oorzaak 2

Controleer of regels voor het virtuele netwerk of de firewall juist zijn geconfigureerd in het opslagaccount. Als u wilt testen of het probleem wordt veroorzaakt door regels voor het virtuele netwerk of de firewall, wijzigt u de instelling in het opslagaccount in **Toegang toestaan vanaf alle netwerken**. Zie [Firewalls en virtuele netwerken voor Azure Storage configureren](../common/storage-network-security.md) voor meer informatie.

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>"[toestemming geweigerd] schijf quotum overschreden" wanneer u een bestand probeert te openen

In Linux wordt een fout bericht van de volgende strekking weer gegeven:

**\<filename> [machtiging geweigerd] Schijf quotum overschreden**

### <a name="cause"></a>Oorzaak

U hebt de maximum limiet bereikt van gelijktijdige open ingangen die zijn toegestaan voor een bestand of map.

Er is een quotum van 2.000 open ingangen voor één bestand of map. Wanneer u 2.000 open ingangen hebt, wordt een fout bericht weer gegeven met de melding dat het quotum is bereikt.

### <a name="solution"></a>Oplossing

Verminder het aantal gelijktijdige open ingangen door enkele ingangen te sluiten en voer de bewerking vervolgens opnieuw uit.

Gebruik de Power shell [-cmdlet Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) om de geopende ingangen voor een bestands share, map of bestand weer te geven.  

Gebruik de Power shell [-cmdlet close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) om open ingangen voor een bestands share, map of bestand te sluiten.

> [!Note]  
> De cmdlets Get-AzStorageFileHandle en Close-AzStorageFileHandle zijn opgenomen in AZ Power shell-module versie 2,4 of hoger. Zie [de module Azure PowerShell installeren](/powershell/azure/install-az-ps)om de nieuwste AZ Power shell-module te installeren.

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Trage bestanden kopiëren naar en van Azure Files in Linux

- Als u geen specifieke minimale I/O-grootte vereiste hebt, raden we u aan om 1 MiB te gebruiken als de I/O-grootte voor optimale prestaties.
- Gebruik de juiste Kopieer methode:
    - Gebruik [AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor elke overdracht tussen twee bestands shares.
    - Als u CP of DD met parallel gebruikt, kan de Kopieer snelheid worden verbeterd. het aantal threads is afhankelijk van de use-case en de werk belasting. De volgende voor beelden gebruiken zes: 
    - CP-voor beeld (CP gebruikt de standaard blok grootte van het bestands systeem als segment grootte): `find * -type f | parallel --will-cite -j 6 cp {} /mntpremium/ &` .
    - DD voor beeld (met deze opdracht wordt de segment grootte expliciet ingesteld op 1 MiB): `find * -type f | parallel --will-cite-j 6 dd if={} of=/mnt/share/{} bs=1M`
    - Open source-hulpprogram ma's van derden, zoals:
        - [GNU parallel](https://www.gnu.org/software/parallel/).
        - [Fpart](https://github.com/martymac/fpart) : Hiermee worden bestanden gesorteerd en verpakt in partities.
        - [Fpsync](https://github.com/martymac/fpart/blob/master/tools/fpsync) : maakt gebruik van Fpart en een kopieer programma om meerdere instanties te starten voor het migreren van gegevens van src_dir naar dst_url.
        - [Meerdere](https://github.com/pkolano/mutil) multi-threaded CP en md5sum op basis van GNU coreutils.
- Door de bestands grootte vooraf in te stellen, in plaats van elke schrijf bewerking uit te voeren, helpt u de Kopieer snelheid te verbeteren in scenario's waarin de bestands grootte bekend is. Als uitbrei ding van schrijf bewerkingen moet worden vermeden, kunt u een doel bestands grootte instellen met behulp van de `truncate - size <size><file>` opdracht. Daarna `dd if=<source> of=<target> bs=1M conv=notrunc` wordt een bron bestand gekopieerd zonder dat de grootte van het doel bestand herhaaldelijk moet worden bijgewerkt. U kunt bijvoorbeeld de grootte van het doel bestand instellen voor elk bestand dat u wilt kopiëren (ervan uitgaande dat er een share is gekoppeld onder/mnt/share):
    - `$ for i in `` find * -type f``; do truncate --size ``stat -c%s $i`` /mnt/share/$i; done`
    - en vervolgens bestanden kopiëren zonder parallelle schrijf bewerkingen uit te breiden: `$find * -type f | parallel -j6 dd if={} of =/mnt/share/{} bs=1M conv=notrunc`

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>"Mount-fout (115): bewerking wordt nu uitgevoerd" wanneer u Azure Files koppelt met behulp van SMB 3,0

### <a name="cause"></a>Oorzaak

Sommige Linux-distributies bieden nog geen ondersteuning voor versleutelingsfuncties in SMB 3.0. Gebruikers ontvangen mogelijk foutbericht 115 als ze proberen Azure Files te koppelen met behulp van SMB 3.0, omdat er een functie ontbreekt. SMB 3.0 met volledige versleuteling wordt alleen ondersteund als u gebruikmaakt van Ubuntu 16.04 of hoger.

### <a name="solution"></a>Oplossing

De versleutelingsfunctie voor SMB 3.0 voor Linux is geïntroduceerd in de kernel 4.11. Deze functie maakt het koppelen van een Azure-bestandsshare mogelijk, on-premises of vanuit een andere Azure-regio. Sommige Linux-distributies hebben mogelijk Backported wijzigingen van de 4,11-kernel naar oudere versies van de Linux-kernel die ze onderhouden. Raadpleeg het [Azure files gebruiken met Linux](storage-how-to-use-files-linux.md)als u hulp nodig hebt bij het bepalen of uw versie van Linux SMB 3,0 ondersteunt met versleuteling. 

Als uw Linux SMB-client geen ondersteuning biedt voor versleutelen, koppelt u Azure Files met behulp van SMB 2.1 vanaf een Azure Linux-VM die zich in hetzelfde datacenter bevindt als de bestandsshare. Controleer of de instelling [Veilige overdracht vereist]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) is uitgeschakeld in het opslagaccount. 

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Fout ' geen toegang ' wanneer u een Azure-bestands share probeert te openen of verwijderen  
Wanneer u probeert een Azure-bestands share in de portal te openen of verwijderen, wordt mogelijk de volgende fout weer gegeven:

Geen toegang  
Fout code: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Oorzaak 1: het virtuele netwerk of de firewall regels zijn ingeschakeld voor het opslag account

### <a name="solution-for-cause-1"></a>Oplossing voor oorzaak 1

Controleer of regels voor het virtuele netwerk of de firewall juist zijn geconfigureerd in het opslagaccount. Als u wilt testen of het probleem wordt veroorzaakt door regels voor het virtuele netwerk of de firewall, wijzigt u de instelling in het opslagaccount in **Toegang toestaan vanaf alle netwerken**. Zie [Firewalls en virtuele netwerken voor Azure Storage configureren](../common/storage-network-security.md) voor meer informatie.

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Oorzaak 2: uw gebruikers account heeft geen toegang tot het opslag account

### <a name="solution-for-cause-2"></a>Oplossing voor oorzaak 2

Blader naar het opslag account waar de Azure-bestands share zich bevindt, klik op **toegangs beheer (IAM)** en controleer of uw gebruikers account toegang heeft tot het opslag account. Zie [uw opslag account beveiligen met Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../blobs/security-recommendations.md#data-protection)voor meer informatie.

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Kan een bestand of map in een Azure-bestandsshare niet verwijderen

### <a name="cause"></a>Oorzaak
Dit probleem treedt doorgaans op als het bestand of de map een geopende ingang heeft. 

### <a name="solution"></a>Oplossing

Als de SMB-clients alle geopende ingangen hebben gesloten en het probleem blijft optreden, voert u de volgende handelingen uit:

- Gebruik de Power shell [-cmdlet Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) om open ingangen weer te geven.

- Gebruik de Power shell [-cmdlet close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) om open ingangen te sluiten. 

> [!Note]  
> De cmdlets Get-AzStorageFileHandle en Close-AzStorageFileHandle zijn opgenomen in AZ Power shell-module versie 2,4 of hoger. Zie [de module Azure PowerShell installeren](/powershell/azure/install-az-ps)om de nieuwste AZ Power shell-module te installeren.

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Langzame prestaties van een Azure-bestandsshare die is gekoppeld aan een Linux-VM

### <a name="cause-1-caching"></a>Oorzaak 1: opslaan in cache

Een mogelijke oorzaak van trage prestaties is het opslaan in de cache uitgeschakeld. Caching kan nuttig zijn als u een bestand herhaaldelijk opent, anders kan het een overhead zijn. Controleer of u de cache gebruikt voordat u deze uitschakelt.

### <a name="solution-for-cause-1"></a>Oplossing voor oorzaak 1

Als u wilt controleren of caching is uitgeschakeld, zoekt u naar de vermelding **cache =** .

**Cache = geen** geeft aan dat caching is uitgeschakeld. Koppel de share opnieuw met behulp van de standaard koppelings opdracht of door expliciet de optie **cache = strikt** toe te voegen aan de koppelings opdracht om ervoor te zorgen dat de standaard cache of ' strikte ' cache modus is ingeschakeld.

In sommige gevallen kan de **serverino** -koppelings optie ervoor zorgen dat de opdracht voor het uitvoeren van de **ls** een Statie voor elke mapvermelding heeft. Dit gedrag resulteert in verminderde prestaties wanneer u een grote map vermeldt. U kunt de koppelings opties in uw **bestand/etc/fstab** -vermelding controleren:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

U kunt ook controleren of de juiste opties worden gebruikt door de opdracht  **sudo mount | grep CIFS** uit te voeren en de uitvoer te controleren. Hier volgt een voor beeld van uitvoer:

```
//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)
```

Als de optie **cache = strikt** of **serverino** niet aanwezig is, ontkoppelen en koppelen Azure files opnieuw door de koppelings opdracht uit te voeren vanuit de [documentatie](./storage-how-to-use-files-linux.md). Controleer vervolgens of de **bestand/etc/fstab** -vermelding de juiste opties heeft.

### <a name="cause-2-throttling"></a>Oorzaak 2: beperking

Het is mogelijk dat u beperking ondervindt en dat uw aanvragen naar een wachtrij worden verzonden. U kunt dit controleren door gebruik te maken van [Azure Storage metrische gegevens in azure monitor](../blobs/monitor-blob-storage.md).

### <a name="solution-for-cause-2"></a>Oplossing voor oorzaak 2

Zorg ervoor dat uw app binnen de [Azure files schaal doelen](storage-files-scale-targets.md#azure-files-scale-targets)valt.

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Er zijn tijds tempels verloren gegaan bij het kopiëren van bestanden van Windows naar Linux

Op Linux/Unix-platforms mislukt de **CP-p-** opdracht als verschillende gebruikers eigenaar zijn van het bestand 1 en het bestand 2.

### <a name="cause"></a>Oorzaak

De Force Flag **f** in COPYFILE resulteert in het uitvoeren van **CP-p-f** op UNIX. Met deze opdracht kan ook geen tijds tempel worden bewaard van het bestand waarvan u geen eigenaar bent.

### <a name="workaround"></a>Tijdelijke oplossing

De gebruiker van het opslagaccount gebruiken voor het kopiëren van de bestanden:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: geen toegang tot ' &lt; Path &gt; ': invoer/uitvoer fout

Wanneer u bestanden in een Azure-bestands share probeert weer te geven met behulp van de opdracht ls, loopt de opdracht vast wanneer er bestanden worden weer gegeven. U krijgt de volgende fout:

**ls: geen toegang tot ' &lt; Path &gt; ': invoer/uitvoer fout**


### <a name="solution"></a>Oplossing
Voer een upgrade uit voor de Linux-kernel naar de volgende versies met een oplossing voor dit probleem:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Alle versies die groter zijn dan of gelijk zijn aan 4,13

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Kan geen symbolische koppelingen maken-ln: kan geen symbolische koppeling maken ': de bewerking wordt niet ondersteund

### <a name="cause"></a>Oorzaak
Het koppelen van Azure-bestands shares op Linux met behulp van CIFS biedt standaard geen ondersteuning voor symbolische koppelingen (symlinks). Dit ziet er als volgt uit:
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>Oplossing
De Linux CIFS-client biedt geen ondersteuning voor het maken van symbolische koppelingen van Windows-stijl via het SMB 2-of 3-protocol. Op dit moment ondersteunt de Linux-client een andere stijl van symbolische koppelingen met de naam [Minshall + Frans symlinks](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) voor zowel Create als follow-bewerkingen. Klanten die symbolische koppelingen nodig hebben, kunnen de koppeling ' mfsymlinks ' gebruiken. We raden u aan mfsymlinks te gebruiken, omdat het ook de indeling is die Macs gebruikt.

Als u symlinks wilt gebruiken, voegt u het volgende toe aan het einde van de opdracht CIFS-koppeling:

```
,mfsymlinks
```

De opdracht ziet er ongeveer als volgt uit:

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

U kunt vervolgens symlinks maken zoals aanbevolen op de [wiki](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>"Mount-fout (112): host is niet beschikbaar" vanwege een time-out voor het opnieuw verbinden

Een koppelfout 112 treedt op de Linux-client op wanneer de client gedurende lange tijd inactief is. Na een lange tijd van inactiviteit wordt de verbinding met de client verbroken en treedt er een time-out op.  

### <a name="cause"></a>Oorzaak

De verbinding kan om de volgende redenen inactief zijn:

-   Fouten in de netwerkcommunicatie waardoor een TCP-verbinding met de server niet opnieuw tot stand kan worden gebracht, wanneer de standaardoptie voor soft koppelen wordt gebruikt
-   Recente oplossingen voor opnieuw verbinden die niet aanwezig zijn in oudere kernels

### <a name="solution"></a>Oplossing

Dit probleem met opnieuw verbinden in de Linux-kernel is nu opgelost als onderdeel van de volgende wijzigingen:

- [Opnieuw verbinden herstellen om de nieuwe verbinding voor de SMB3-sessie niet uit te stellen lang nadat de socket opnieuw is verbonden](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
- [Echoservice onmiddellijk aanroepen nadat de socket opnieuw is verbonden](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
- [CIFS: Mogelijke geheugenbeschadiging herstellen tijdens opnieuw verbinden](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
- [CIFS: een mogelijke dubbele vergren deling van mutex herstellen tijdens opnieuw verbinding maken (voor kernel v 4.9 en hoger)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

Deze wijzigingen zijn mogelijk nog niet doorgevoerd in alle Linux-distributies. Als u een populaire Linux-distributie gebruikt, kunt u de [Azure files met Linux](storage-how-to-use-files-linux.md) controleren om te zien welke versie van uw distributie de nood zakelijke wijzigingen in de kernel heeft.

### <a name="workaround"></a>Tijdelijke oplossing

U kunt dit probleem omzeilen door een harde koppeling op te geven. Met een harde koppeling wordt afgedwongen dat er wordt gewacht totdat er een verbinding tot stand is gebracht, of tot de client expliciet wordt onderbroken. U kunt dit gebruiken om fouten vanwege time-outs van het netwerk te voorkomen. Deze tijdelijke oplossing kan echter leiden tot onbepaalde wachttijden. Wees voorbereid om verbindingen te stoppen, indien nodig.

Als u geen upgrade kunt uitvoeren naar de nieuwste kernelversies, kunt u dit probleem omzeilen door een bestand in de Azure-bestandsshare te bewaren waarnaar u elke 30 seconden, of minder, schrijft. Dit moet een schrijfbewerking zijn, zoals het herschrijven van de aanmaakdatum of wijzigingsdatum van het bestand. Anders ontvangt u wellicht de resultaten die in de cache zijn opgeslagen, en wordt het opnieuw verbinden mogelijk niet geactiveerd.

## <a name="cifs-vfs-error--22-on-ioctl-to-get-interface-list-when-you-mount-an-azure-file-share-by-using-smb-30"></a>"CIFS VFS: error-22 op IOCTL om de interface lijst op te halen" wanneer u een Azure-bestands share koppelt met behulp van SMB 3,0

### <a name="cause"></a>Oorzaak
Deze fout wordt geregistreerd omdat Azure Files [momenteel geen SMB meerdere kanalen ondersteunt](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).

### <a name="solution"></a>Oplossing
Deze fout kan worden genegeerd.


### <a name="unable-to-access-folders-or-files-which-name-has-a-space-or-a-dot-at-the-end"></a>Kan geen toegang krijgen tot mappen of bestanden waarvan de naam een spatie of een punt aan het einde bevat

U hebt geen toegang tot mappen of bestanden vanuit de Azure-bestands share tijdens het koppelen aan linux, opdrachten als du en LS en/of toepassingen van derden kunnen mislukken met de fout bericht ' no file or directory ' tijdens het openen van de share, maar u kunt wel bestanden uploaden naar genoemde mappen via de portal.

### <a name="cause"></a>Oorzaak

De mappen of bestanden zijn geüpload vanaf een systeem waarmee de tekens aan het einde van de naam naar een ander teken worden verzonden. bestanden die worden geüpload vanaf een Macintosh-computer, hebben mogelijk een ' 0xF028 ' of ' 0xF029 ' in plaats van 0x20 (spatie) of 0X2E (punt).

### <a name="solution"></a>Oplossing

Gebruik de optie mapchars op de share terwijl u de share op Linux koppelt: 

In plaats van:

```bash
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

gebruiken

```bash
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino,mapchars
```


## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Neem contact op met ondersteuning.

Als u nog steeds hulp nodig hebt, [neemt u contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.