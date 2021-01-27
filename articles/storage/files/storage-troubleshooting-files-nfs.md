---
title: Problemen met de bestands shares van Azure NFS oplossen-Azure Files
description: Problemen met de bestands shares van Azure NFS oplossen.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: jeffpatt
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: ed86cc76984388618c177590b3f6358421f09f65
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878490"
---
# <a name="troubleshoot-azure-nfs-file-shares"></a>Problemen met Azure NFS-bestands shares oplossen

In dit artikel vindt u enkele veelvoorkomende problemen met betrekking tot Azure NFS-bestands shares. Het biedt mogelijke oorzaken en tijdelijke oplossingen wanneer deze problemen optreden.

## <a name="chgrp-filename-failed-invalid-argument-22"></a>chgrp ' filename ' is mislukt: ongeldig argument (22)

### <a name="cause-1-idmapping-is-not-disabled"></a>Oorzaak 1: idmapping is niet uitgeschakeld
Azure Files geen alfanumerieke UID/GID toestaan. Idmapping moet daarom worden uitgeschakeld. 

### <a name="cause-2-idmapping-was-disabled-but-got-re-enabled-after-encountering-bad-filedir-name"></a>Oorzaak 2: idmapping is uitgeschakeld, maar opnieuw ingeschakeld na een onjuiste bestands-/mapnaam
Zelfs als idmapping correct is uitgeschakeld, worden de instellingen voor het uitschakelen van idmapping in sommige gevallen overschreven. Als bijvoorbeeld de Azure Files een onjuiste bestands naam tegen komt, wordt een fout bericht weer gegeven. Als deze specifieke fout code wordt weer gegeven, besluit de NFS v 4,1 Linux-client om idmapping opnieuw in te scha kelen en de toekomstige aanvragen opnieuw te worden verzonden met een alfanumerieke UID/GID. Raadpleeg dit [artikel](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)voor een lijst met niet-ondersteunde tekens in azure files. Dubbele punt is een van de niet-ondersteunde tekens. 

### <a name="workaround"></a>Tijdelijke oplossing
Controleer of idmapping is uitgeschakeld en niets opnieuw inschakelt en voer de volgende handelingen uit:

- De share ontkoppelen
- Id-toewijzing uitschakelen met # ECHO Y >/sys/module/nfs/parameters/nfs4_disable_idmapping
- De share opnieuw koppelen
- Als rsync wordt uitgevoerd, voert u rsync uit met het argument ' — numeric-id's ' uit de map zonder ongeldige dir/bestands naam.

## <a name="unable-to-create-an-nfs-share"></a>Kan geen NFS-share maken

### <a name="cause-1-subscription-is-not-enabled"></a>Oorzaak 1: abonnement is niet ingeschakeld

Uw abonnement is mogelijk niet geregistreerd voor de Azure Files NFS preview. U moet een paar extra Commandlets uitvoeren vanaf een Cloud Shell of een lokale terminal om de functie in te scha kelen.

> [!NOTE]
> Mogelijk moet u tot 30 minuten wachten totdat de registratie is voltooid.


#### <a name="solution"></a>Oplossing

Gebruik het volgende script om de functie en resource provider te registreren, vervang `<yourSubscriptionIDHere>` voordat u het script uitvoert:

```azurepowershell
Connect-AzAccount

#If your identity is associated with more than one subscription, set an active subscription
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowNfsFileShares - ProviderNamespace Microsoft.Storage

Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="cause-2-unsupported-storage-account-settings"></a>Oorzaak 2: niet-ondersteunde instellingen voor het opslag account

NFS is alleen beschikbaar voor opslag accounts met de volgende configuratie:

- Laag-Premium
- Account type-FileStorage
- Redundantie-LRS
- Regio's- [lijst met ondersteunde regio's](./storage-files-how-to-create-nfs-shares.md?tabs=azure-portal#regional-availability)

#### <a name="solution"></a>Oplossing

Volg de instructies in het artikel: [een NFS-share maken](storage-files-how-to-create-nfs-shares.md).

### <a name="cause-3-the-storage-account-was-created-prior-to-registration-completing"></a>Oorzaak 3: het opslag account is gemaakt vóór de registratie is voltooid

Als u een opslag account wilt gebruiken, moet dit worden gemaakt zodra het abonnement is geregistreerd voor NFS. Het kan tot 30 minuten duren voordat de registratie is voltooid.

#### <a name="solution"></a>Oplossing

Nadat de registratie is voltooid, volgt u de instructies in het artikel: [een NFS-share maken](storage-files-how-to-create-nfs-shares.md).

## <a name="cannot-connect-to-or-mount-an-azure-nfs-file-share"></a>Kan geen verbinding maken met een Azure NFS-bestands share of deze koppelen

### <a name="cause-1-request-originates-from-a-client-in-an-untrusted-networkuntrusted-ip"></a>Oorzaak 1: aanvraag afkomstig van een client in een niet-vertrouwd netwerk/niet-vertrouwd IP-adres

In tegens telling tot SMB heeft NFS geen authenticatie op basis van een gebruiker. De verificatie voor een share is gebaseerd op de configuratie van uw netwerk beveiligings regel. Als gevolg hiervan moet u het service-eind punt of het persoonlijke eind punt gebruiken om ervoor te zorgen dat alleen beveiligde verbindingen tot stand worden gebracht met de NFS-share. Voor toegang tot shares van on-premises naast persoonlijke eind punten moet u VPN-of ExpressRoute instellen. IP-adressen die zijn toegevoegd aan de acceptatie lijst van het opslag account voor de firewall, worden genegeerd. U moet een van de volgende methoden gebruiken om toegang te krijgen tot een NFS-share:


- [Service-eind punt](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - Geopend door het open bare eind punt
    - Alleen beschikbaar in dezelfde regio.
    - VNet-peering biedt geen toegang tot uw share.
    - Elk virtueel netwerk of subnet moet afzonderlijk worden toegevoegd aan de acceptatie lijst.
    - Voor on-premises toegang kunnen service-eind punten worden gebruikt met ExpressRoute, punt-naar-site-en site-naar-site-Vpn's, maar we raden u aan een privé-eind punt te gebruiken omdat het veiliger is.

In het volgende diagram ziet u de connectiviteit met behulp van open bare eind punten.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg" alt-text="Diagram van connectiviteit van open bare eind punten." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg":::

- [Persoonlijk eind punt](storage-files-networking-endpoints.md#create-a-private-endpoint)
    - De toegang is veiliger dan het service-eind punt.
    - Toegang tot de NFS-share via een persoonlijke koppeling is beschikbaar vanuit en buiten de Azure-regio van het opslag account (cross-Region, on-premises)
    - Virtual Network-peering met virtuele netwerken die worden gehost in het persoonlijke eind punt bieden NFS-share toegang tot de clients in gekoppelde virtuele netwerken.
    - Privé-eind punten kunnen worden gebruikt in combi natie met ExpressRoute, punt-naar-site-en site-naar-site-Vpn's.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg" alt-text="Diagram van connectiviteit van privé-eind punten." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg":::

### <a name="cause-2-secure-transfer-required-is-enabled"></a>Oorzaak 2: beveiligde overdracht vereist is ingeschakeld

Dubbele versleuteling wordt nog niet ondersteund voor NFS-shares. Azure biedt een laag code ring voor alle gegevens die onderweg zijn tussen Azure-data centers met behulp van MACSec. NFS-shares kunnen alleen worden geopend vanuit vertrouwde virtuele netwerken en via VPN-tunnels. Er is geen extra transport laag versleuteling beschikbaar op NFS-shares.

#### <a name="solution"></a>Oplossing

Schakel beveiligde overdracht uit op de Blade configuratie van uw opslag account.

:::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Scherm opname van de Blade configuratie van opslag account, veilige overdracht uitschakelen vereist.":::

### <a name="cause-3-nfs-common-package-is-not-installed"></a>Oorzaak 3: gemeen schappelijk NFS-pakket is niet geïnstalleerd
Voordat u de koppelings opdracht uitvoert, installeert u het pakket door de distributie-specifieke opdracht uit te voeren.

Als u wilt controleren of het NFS-pakket is geïnstalleerd, voert u de volgende handelingen uit: `rpm qa | grep nfs-utils`

#### <a name="solution"></a>Oplossing

Als het pakket niet is geïnstalleerd, installeert u het pakket in uw distributie.

##### <a name="ubuntu-or-debian"></a>Ubuntu of Debian

```
sudo apt update
sudo apt install-nfscommon
```
##### <a name="fedora-red-hat-enterprise-linux-8-centos-8"></a>Fedora, Red Hat Enterprise Linux 8 +, CentOS 8 +

Gebruik DNF Package Manager: `sudo dnf install nfs-common` .

Oudere versies van Red Hat Enterprise Linux en CentOS gebruiken de yum-pakket beheer: `sudo yum install nfs-common` .

##### <a name="opensuse"></a>openSUSE

Gebruik Zypper Package Manager: `sudo zypper install-nfscommon` .

### <a name="cause-4-firewall-blocking-port-2049"></a>Oorzaak 4: Firewall die poort 2049 blokkeert

Het NFS-protocol communiceert met de server via poort 2049, zorg ervoor dat deze poort is geopend voor het opslag account (de NFS-server).

#### <a name="solution"></a>Oplossing

Controleer of poort 2049 is geopend op de client door de volgende opdracht uit te voeren: `telnet <storageaccountnamehere>.file.core.windows.net 2049` . Als de poort niet open is, opent u deze.

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Neem contact op met ondersteuning.
Als u nog steeds hulp nodig hebt, [neemt u contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.
