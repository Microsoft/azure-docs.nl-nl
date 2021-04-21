---
title: Aandachtspunten voor Azure Files-netwerken | Microsoft Docs
description: Een overzicht van de netwerkopties voor Azure Files.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 02/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1dce7795b8c62c36b80c51d5ba0dd8bc9b667e0e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759683"
---
# <a name="azure-files-networking-considerations"></a>Aandachtspunten voor Azure Files-netwerken 
U kunt op twee manieren verbinding maken met een Azure-bestandsshare:

- Directe toegang tot de share via de protocollen Server Message Block (SMB), NFS (Network File System) (preview) of FileREST. Dit toegangspatroon wordt voornamelijk gebruikt om de noodzaak voor on-premises servers zoveel mogelijk te elimineren.
- Het maken van een cache van de Azure-bestandsshare op een on-premises server (of op een Azure-VM) met Azure File Sync, en het verkrijgen van toegang tot de gegevens van de bestandsshare vanaf de on-premises server met het protocol van uw keuze (SMB, NFS, FTPS, enzovoort) voor uw use-case. Dit toegangspatroon is handig omdat het een goede combinatie is van on-premises prestaties, de schaal van de cloud en serverloze koppelbare services, zoals Azure Backup.

In dit artikel wordt uitgelegd hoe u netwerken kunt configureren als het in uw use-case nodig is de Azure-bestandsshare rechtstreeks te openen in plaats van met Azure File Sync. Zie [Aandachtspunten voor Azure File Sync-netwerken](../file-sync/file-sync-networking-overview.md) voor meer informatie over netwerkaandachtspunten voor een Azure File Sync-implementatie.

Een netwerkconfiguratie voor Azure-bestandsshares wordt uitgevoerd op het Azure-opslagaccount. Een opslagaccount is een beheerconstructie die een gedeelde opslaggroep vertegenwoordigt waarin u meerdere bestandsshares kunt implementeren, evenals andere opslagresources, zoals blob-containers of wachtrijen. Opslagaccounts stellen meerdere instellingen beschikbaar waarmee u netwerktoegang tot uw bestandsshares kunt beveiligen: netwerkeindpunten, firewallinstellingen voor opslagaccounts en versleuteling in transit. 

U wordt aangeraden [Implementatie van Azure Files plannen](storage-files-planning.md) te lezen voordat u deze conceptuele handleiding leest.

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Deze video is een handleiding en demo voor het veilig beschikbaar maken van Azure-bestands shares rechtstreeks aan informatiemedewerkers en apps in vijf eenvoudige stappen. De onderstaande secties bieden koppelingen en aanvullende context naar de documentatie waarnaar in de video wordt verwezen.
   :::column-end:::
:::row-end:::

## <a name="accessing-your-azure-file-shares"></a>Toegang tot uw Azure-bestandsshares
Wanneer u een Azure-bestandsshare in een opslagaccount implementeert, is uw bestandsshare direct toegankelijk via het openbare eindpunt van het opslagaccount. Dit betekent dat geverifieerde aanvragen, zoals aanvragen die zijn geautoriseerd door de aanmeldingsidentiteit van een gebruiker, veilig kunnen zijn van binnen of buiten Azure. 

In veel klantomgevingen kan een initiële koppeling van de Azure-bestandsshare op uw on-premises werkstation mislukken, zelfs als het wel lukt om Azure-VM's te koppelen. De reden hiervoor is dat veel organisaties en internetproviders (ISP's) de poort blokkeren die door SMB wordt gebruikt voor communicatie: poort 445. NFS-shares hebben dit probleem niet. Deze werkwijze is afkomstig uit beveiligingsrichtlijnen over verouderde en afgeschafte versies van het SMB-protocol. Hoewel SMB 3.0 een internet-veilig protocol is, zijn oudere versies van SMB, met name SMB 1.0, dat niet. Azure-bestandsshares mogen alleen extern toegankelijk zijn via SMB 3.0 en het FileREST-protocol (dat ook internet-veilig is) via het openbare eindpunt.

De eenvoudigste manier om toegang te krijgen tot uw Azure SMB-bestandsshare vanuit een on-premises netwerk, is poort 445 van uw on-premises netwerk open te zetten. Daarom raadt Microsoft u aan de volgende stappen uit te voeren om SMB 1.0 uit uw omgeving te verwijderen:

1. Controleer of SMB 1.0 is verwijderd of uitgeschakeld voor de apparaten van uw organisatie. Alle momenteel ondersteunde versies van Windows en Windows Server ondersteunen het verwijderen of uitschakelen van SMB 1.0 en vanaf Windows 10, versie 1709, is SMB 1.0 standaard niet meer in Windows geïnstalleerd. Ga voor meer informatie over het uitschakelen van SMB 1.0 naar onze pagina's voor specifieke besturingssystemen:
    - [Windows/Windows Server beveiligen](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Linux beveiligen](storage-how-to-use-files-linux.md#securing-linux)
1. Controleer of er geen producten in uw organisatie zijn waarvoor SMB 1.0 vereist is, en verwijder de producten waarvoor SMB 1.0 wel vereist is. We onderhouden een [SMB1 Product Clearinghouse](https://aka.ms/stillneedssmb1), met alle eigen producten en producten van derden waarvan bij Microsoft bekend is dat ze SMB 1.0 vereisen. 
1. (Optioneel) Gebruik een firewall van derden met het on-premises netwerk van uw organisatie om te voorkomen dat uitgaand verkeer via SMB 1.0 de netwerkgrens uw organisatie overschrijdt.

Als uw organisatie vereist dat poort 445 wordt geblokkeerd per beleid of voorschrift, of als uw organisatie vereist dat verkeer naar Azure vereist een deterministisch pad volgt, kunt u Azure VPN Gateway of ExpressRoute gebruiken om verkeer naar uw Azure-bestandsshares te tunnelen. Voor NFS-shares is dit niet vereist, omdat hiervoor poort 445 niet nodig is.

> [!Important]  
> Zelfs als u besluit een alternatieve methode te gebruiken om toegang te krijgen tot uw Azure-bestandsshares, raadt Microsoft aan om SMB 1.0 toch te verwijderen uit uw omgeving.

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Verkeer tunnelen via een virtueel particulier netwerk of ExpressRoute
Wanneer u een netwerktunnel tussen uw on-premises netwerk en Azure tot stand brengt, wordt uw on-premises netwerk via peering gekoppeld aan een of meer virtuele netwerken in Azure. Een [virtueel netwerk](../../virtual-network/virtual-networks-overview.md), ook wel VNet genoemd, is te vergelijken met een traditioneel netwerk dat on-premises wordt uitgevoerd. Net zoals een Azure-opslagaccount of een Azure-VM, is een VNet een Azure-resource die in een resourcegroep is geïmplementeerd. 

Azure Files ondersteunt de volgende mechanismen om verkeer te tunnelen tussen uw on-premises werkstations en servers en Azure SMB/NFS-bestandsshares:

- Een VPN-gateway is een speciaal soort virtueel-netwerkgateway die wordt gebruikt om versleuteld verkeer te verzenden tussen een virtueel Azure-netwerk en een andere locatie (zoals een on-premises locatie) via internet. Een Azure-VPN Gateway is een Azure-resource die kan worden geïmplementeerd in een resourcegroep naast een opslagaccount of andere Azure-resources. Voor VPN-gateways zijn twee soorten verbindingen beschikbaar:
    - [Punt-naar-site-VPN](../../vpn-gateway/point-to-site-about.md)-gatewayverbindingen. Dit zijn VPN-verbindingen tussen Azure en een afzonderlijke client. Deze oplossing is vooral nuttig voor apparaten die geen deel uitmaken van het on-premises netwerk van uw organisatie, zoals telewerkers die hun Azure-bestandsshare willen kunnen koppelen vanuit huis of een café of hotel, terwijl ze onderweg zijn. Als u een punt-naar-site-VPN-verbinding met Azure Files wilt gebruiken, moet er een punt-naar-site-VPN-verbinding worden geconfigureerd voor elke client die verbinding wil maken. Zie [Een punt-naar-site-VPN in Windows configureren voor gebruik met Azure Files](storage-files-configure-p2s-vpn-windows.md) en [Een punt-naar-site-VPN in Linux configureren voor gebruik met Azure Files](storage-files-configure-p2s-vpn-linux.md) om de implementatie van een punt-naar-site-VPN-verbinding te vereenvoudigen.
    - [Site-naar-site-VPN](../../vpn-gateway/design.md#s2smulti). Dit zijn VPN-verbindingen tussen Azure en het netwerk van uw organisatie. Met een site-naar-site-VPN-verbinding kunt u een VPN-verbinding eenmaal configureren, voor een VPN-server of een apparaat dat wordt gehost op het netwerk van uw organisatie, in plaats van dit te doen voor elk clientapparaat dat toegang tot uw Azure-bestandsshare moet hebben. Zie [Een site-naar-site-VPN configureren voor gebruik met Azure Files](storage-files-configure-s2s-vpn.md) voor het vereenvoudigen van de implementatie van een site-naar-site-VPN-verbinding.
- [ExpressRoute](../../expressroute/expressroute-introduction.md). Hiermee kunt u een gedefinieerde route tussen Azure en uw on-premises netwerk maken die niet via internet loopt. Omdat ExpressRoute een toegewezen pad biedt tussen uw on-premises datacentrum en Azure, is ExpressRoute mogelijk handig wanneer netwerkprestaties van belang zijn. ExpressRoute is ook een goede optie wanneer een deterministisch pad naar uw resources in de cloud vereist is vanwege het beleid of de wettelijke vereisten van uw organisatie.

Welke tunnelingmethode u ook gebruikt om toegang te krijgen tot uw Azure-bestandsshares, u hebt een mechanisme nodig om ervoor te zorgen dat het verkeer naar uw opslagaccount via de tunnel loopt in plaats van via uw normale internetverbinding. Het is technisch mogelijk om het verkeer naar het openbare eindpunt van het opslagaccount te leiden, maar hiervoor moeten alle IP-adressen voor de Azure-opslagclusters in een regio hard worden gecodeerd, omdat opslagaccounts altijd kunnen worden verplaatst naar andere opslagclusters. Hiervoor moeten ook de IP-adrestoewijzingen constant worden bijgewerkt, omdat er voortdurend nieuwe clusters worden toegevoegd.

In plaats van het IP-adres van uw opslagaccounts hard te coderen in uw VPN-regels voor doorsturen, bevelen we aan privé-eindpunten te gebruiken, zodat aan uw opslagaccount een IP-adres van de adresruimte van een virtueel Azure-netwerk wordt toegewezen. Omdat bij het maken van een tunnel naar Azure uw on-premises netwerk via peering wordt gekoppeld aan een of meer virtuele netwerken, kan de juiste routering op een duurzame manier worden ingesteld.

### <a name="private-endpoints"></a>Privé-eindpunten
Naast het standaard openbare eindpunt voor een opslagaccount, biedt Azure Files de mogelijkheid om een of meer privé-eindpunten te configureren. Een privé-eindpunt is een eindpunt dat alleen toegankelijk is binnen een virtueel Azure-netwerk. Wanneer u een privé-eindpunt voor uw opslagaccount maakt, ontvangt uw opslagaccount een privé-IP-adres in de adresruimte van uw virtuele netwerk, zoals een on-premises bestandsserver of NAS-apparaat een IP-adres ontvangt binnen de toegewezen adresruimte van uw on-premises netwerk. 

Een privé-eindpunt is gekoppeld aan een specifiek subnet van het virtuele Azure-netwerk. Een opslagaccount kan privé-eindpunten hebben in meer dan één virtueel netwerk.

Door gebruik te maken van privé-eindpunten met Azure Files kunt u het volgende doen:
- Veilig verbinding maken met uw Azure-bestandsshares vanuit on-premises netwerken met behulp van een VPN- of ExpressRoute-verbinding met privé-peering.
- Uw Azure-bestandsshares beveiligen door de firewall van het opslagaccount zodanig te configureren dat alle verbindingen op het openbare eindpunt worden geblokkeerd. Standaard worden verbindingen met het openbare eindpunt niet geblokkeerd als u een privé-eindpunt maakt.
- De beveiliging voor het virtuele netwerk verbeteren door gegevensoverdracht van het virtuele netwerk (en de grenzen van peering) te blokkeren.

Zie [Privé-eindpunten configureren voor Azure Files](storage-files-networking-endpoints.md) voor het maken van een privé-eindpunt.

### <a name="private-endpoints-and-dns"></a>Privé-eindpunten en DNS
Wanneer u een privé-eindpunt maakt, wordt standaard ook een privé-DNS-zone gemaakt (of een bestaande privé-DNS-zone bijgewerkt) die overeenkomt met het `privatelink`-subdomein. Het is strikt genomen niet vereist om een privé-DNS-zone te maken voor het gebruik van een privé-eindpunt voor uw opslagaccount, maar dit wordt sterk aanbevolen in het algemeen en is expliciet vereist bij koppeling van uw Azure-bestandsshare aan een principal van een Active Directory-gebruiker of bij toegang via de FileREST-API.

> [!Note]  
> In dit artikel wordt het DNS-achtervoegsel voor opslagaccounts gebruikt voor de openbare regio's van Azure, te weten `core.windows.net`. Deze opmerking geldt ook voor onafhankelijke Azure-clouds zoals de Azure-cloud voor de Amerikaanse overheid en de Azure China 21Vianet-cloud. In dat geval vervangt u het achtervoegsel door dat van de onafhankelijke cloud. 

In uw privé-DNS-zone maken we een A-record voor `storageaccount.privatelink.file.core.windows.net` en een CNAME-record voor de reguliere naam van het opslagaccount, met het patroon `storageaccount.file.core.windows.net`. Omdat uw privé-DNS-zone van Azure is verbonden met het virtuele netwerk met het privé-eindpunt, kunt u de DNS-configuratie bekijken wanneer u de `Resolve-DnsName`-cmdlet aanroept vanuit PowerShell in een Azure VM (`nslookup` in Windows en Linux):

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

In dit voorbeeld wordt het opslagaccount `storageaccount.file.core.windows.net` omgezet in het privé-IP-adres van het privé-eindpunt. Dat is `192.168.0.4`.

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4


Name                   : privatelink.file.core.windows.net
QueryType              : SOA
TTL                    : 269
Section                : Authority
NameAdministrator      : azureprivatedns-host.microsoft.com
SerialNumber           : 1
TimeToZoneRefresh      : 3600
TimeToZoneFailureRetry : 300
TimeToExpiration       : 2419200
DefaultTTL             : 300
```

Als u dezelfde opdracht uitvoert vanuit een on-premises locatie, ziet u dat dezelfde opslagaccountnaam wordt omgezet in het openbare IP-adres van het opslagaccount. `storageaccount.file.core.windows.net` is een CNAME-record voor `storageaccount.privatelink.file.core.windows.net`, die op zijn beurt weer een CNAME-record is voor het Azure-opslagcluster dat als host fungeert voor het opslagaccount:

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
net
storageaccount.privatelink.file.c CNAME  60    Answer     file.par20prdstr01a.store.core.windows.net
ore.windows.net

Name       : file.par20prdstr01a.store.core.windows.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 52.239.194.40
```

Dit weerspiegelt het feit dat het opslagaccount zowel het openbare eindpunt als een of meer privé-eindpunten beschikbaar kan stellen. Om ervoor te zorgen dat de naam van het opslagaccount wordt omgezet in het privé-IP-adres van het privé-eindpunt, moet u de configuratie op uw on-premises DNS-servers wijzigen. Dit kan op verschillende manieren worden gerealiseerd:

- Het hosts-bestand op uw clients aanpassen om `storageaccount.file.core.windows.net` om te zetten in het privé-IP-adres van het gewenste privé-eindpunt. Dit wordt sterk afgeraden voor productieomgevingen, omdat u deze wijzigingen moet aanbrengen op elke client die uw Azure-bestandsshares wil koppelen, en wijzigingen in het opslagaccount of het privé-eindpunt niet automatisch worden verwerkt.
- Een A-record maken voor `storageaccount.file.core.windows.net` in uw on-premises DNS-servers. Dit heeft als voordeel dat clients in uw on-premises omgeving automatisch het opslagaccount kunnen omzetten zonder dat elke client hoeft te worden geconfigureerd. Het nadeel van deze oplossing is echter, net als bij het hosts-bestand, dat wijzigingen niet worden doorgevoerd. Hoewel deze oplossing niet ideaal is, is het mogelijk wel de beste keuze voor sommige omgevingen.
- De zone `core.windows.net` doorsturen van uw on-premises DNS-servers naar uw privé-DNS-zone van Azure. De privé-DNS-host van Azure kan worden bereikt via een speciaal IP-adres (`168.63.129.16`) dat alleen toegankelijk is in virtuele netwerken die zijn gekoppeld aan de privé-DNS-zone van Azure. Als u deze beperking wilt omzeilen, kunt u extra DNS-servers in uw virtuele netwerk uitvoeren waarmee `core.windows.net` wordt doorgestuurd naar de privé-DNS-zone van Azure. Om het instellen te vereenvoudigen, zijn er PowerShell-cmdlets beschikbaar waarmee DNS-servers automatisch in uw virtuele Azure-netwerk worden geïmplementeerd en u deze naar wens kunt configureren. Zie [DNS configureren met Azure Files](storage-files-networking-dns.md) voor meer informatie over het instellen van DNS-doorschakeling.

## <a name="storage-account-firewall-settings"></a>Firewallinstellingen voor opslagaccount
Een firewall is een netwerkbeleidsregel die bepaalt welke aanvragen voor het openbare eindpunt worden toegestaan voor een opslagaccount. Met de firewall van het opslagaccount kunt u de toegang tot het openbare eindpunt van het opslagaccount beperken tot bepaalde IP-adressen of bereikwaarden of een virtueel netwerk. Over het algemeen beperken de meeste beleidsregels van firewalls voor een opslagaccount de netwerktoegang tot een of meer virtuele netwerken. 

Er zijn twee benaderingen voor het beperken van de toegang van een opslagaccount tot een virtueel netwerk:
- Een of meer privé-eindpunten maken voor het opslagaccount en alle toegang tot het openbare eindpunt beperken. Hierdoor heeft alleen verkeer dat afkomstig is uit de gewenste virtuele netwerken toegang tot de Azure-bestandsshares binnen het opslagaccount.
- Het openbare eindpunt beperken tot een of meer virtuele netwerken. Hiervoor wordt een voorziening van het virtuele netwerk gebruikt met de naam *service-eindpunten*. Wanneer u het verkeer naar een opslagaccount beperkt via een service-eindpunt, krijgt u nog steeds toegang tot het opslagaccount via het openbare IP-adres.

> [!NOTE]
> NFS-shares hebben geen toegang tot het openbare eindpunt van een opslagaccount via het openbare IP-adres. Ze hebben alleen toegang tot het openbare eindpunt van een opslagaccount via virtuele netwerken. NFS-shares kunnen ook toegang krijgen tot het opslagaccount via privé-eindpunten.

Zie [Firewalls en virtuele netwerken voor Azure Storage configureren](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor meer informatie over het configureren van de firewall voor het opslagaccount.

## <a name="encryption-in-transit"></a>Versleuteling tijdens overdracht

> [!IMPORTANT]
> Deze sectie bevat informatie over versleuteling tijdens overdracht voor SMB-shares. Zie [Beveiliging](storage-files-compare-protocols.md#security) voor meer informatie over versleuteling tijdens overdracht voor NFS-shares.

Standaard is versleuteling in-transit ingeschakeld voor alle Azure-opslagaccounts. Dit betekent dat wanneer u een bestandsshare koppelt via SMB of de bestandsshare opent via het FileREST-protocol (bijvoorbeeld via de Azure-portal, PowerShell/CLI of Azure-SDK's), de verbinding alleen wordt toegestaan als deze wordt gemaakt met SMB 3.0+ met versleuteling of HTTPS. Op clients die SMB 3.0 niet ondersteunen of op clients die SMB 3.0 wel ondersteunen maar SMB-versleuteling niet, kan de Azure-bestandsshare niet worden gekoppeld als versleuteling in transit is ingeschakeld. Zie onze gedetailleerde documentatie voor [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) en [Linux-](storage-how-to-use-files-linux.md) voor meer informatie over besturingssystemen waarin SMB 3.0 met versleuteling wordt ondersteund. Alle huidige versies van PowerShell, CLI en SDK's ondersteunen HTTPS.  

U kunt versleuteling in transit uitschakelen voor een Azure-opslagaccount. Wanneer versleuteling is uitgeschakeld, worden door Azure Files ook SMB 2.1, SMB 3.0 zonder versleuteling en niet-versleutelde FileREST-API-aanroepen via HTTP toegestaan. De belangrijkste reden om versleuteling in transit uit te schakelen, is het ondersteunen van een verouderde toepassing die moet worden uitgevoerd op een ouder besturingssysteem, zoals Windows Server 2008 R2 of een oudere Linux-distributie. In Azure Files worden SMB 2.1-verbindingen alleen toegestaan binnen dezelfde Azure-regio als de Azure-bestandsshare. SMB 2.1-clients buiten de Azure-regio van de Azure-bestandsshare, zoals on-premises clients of clients in een andere Azure-regio, hebben geen toegang tot de bestandsshare.

Zie [Veilige overdracht vereisen in Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor meer informatie over versleuteling in transit.

## <a name="see-also"></a>Zie ook
- [Overzicht van Azure Files](storage-files-introduction.md)
- [Implementatie van Azure Files plannen](storage-files-planning.md)