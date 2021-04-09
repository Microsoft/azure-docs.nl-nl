---
title: Aandachtspunten voor Azure File Sync-netwerken | Microsoft Docs
description: Meer informatie over het configureren van netwerken voor het gebruik van Azure File Sync om on-premises bestanden in de cache op te slaan.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 76d97e3312c1df51193d8a881f3ee07fcd155d75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629356"
---
# <a name="azure-file-sync-networking-considerations"></a>Aandachtspunten voor Azure File Sync-netwerken
U kunt op twee manieren verbinding maken met een Azure-bestandsshare:

- U kunt de share rechtstreeks openen via de SMB- of FileREST-protocollen. Dit toegangspatroon wordt voornamelijk gebruikt om de noodzaak voor on-premises servers zoveel mogelijk te elimineren.
- Het maken van een cache van de Azure-bestandsshare op een on-premises server (of op een Azure-VM) met Azure File Sync, en het verkrijgen van toegang tot de gegevens van de bestandsshare vanaf de on-premises server met het protocol van uw keuze (SMB, NFS, FTPS, enzovoort) voor uw use-case. Dit toegangspatroon is handig omdat het een goede combinatie is van on-premises prestaties, de schaal van de cloud en serverloze koppelbare services, zoals Azure Backup.

In dit artikel wordt uitgelegd hoe u netwerken kunt configureren als het in uw use-case nodig is Azure File Sync te gebruiken om bestanden on-premises in de cache op te slaan in plaats van de Azure-bestandsshare rechtstreeks te koppelen via SMB. Zie [Aandachtspunten voor Azure Files-netwerken](storage-files-networking-overview.md) voor meer informatie over aandachtspunten voor een netwerk voor een Azure Files-implementatie.

Een netwerkconfiguratie voor Azure File Sync omvat twee verschillende Azure-objecten: een opslagsynchronisatieservice en een Azure-opslagaccount. Een opslagaccount is een beheerconstructie die een gedeelde opslaggroep vertegenwoordigt waarin u meerdere bestandsshares kunt implementeren, evenals andere opslagresources, zoals blob-containers of wachtrijen. Een opslagsynchronisatieservice is een beheerconstructie die geregistreerde servers vertegenwoordigt, d.w.z. Windows-bestandsservers met een gevestigde vertrouwensrelatie met Azure File Sync, en synchronisatiegroepen, die de topologie van de synchronisatierelatie definiëren. 

## <a name="connecting-windows-file-server-to-azure-with-azure-file-sync"></a>Windows-bestandsserver verbinden met Azure via Azure File Sync 
Om Azure Files en Azure File Sync in te stellen en te gebruiken met een on-premises Windows-bestandsserver, is er behalve een eenvoudige internetverbinding geen speciale netwerkverbinding met Azure nodig. Om Azure File Sync te implementeren, installeert u de Azure File Sync-agent op de Windows-bestandsserver die u wilt synchroniseren met Azure. De Azure File Sync-agent realiseert synchronisatie met een Azure-bestandsshare via twee kanalen:

- Het FileREST-protocol, dat een HTTPS-gebaseerd protocol is voor toegang tot uw Azure-bestandsshare. Omdat het FileREST-protocol gebruikmaakt van standaard HTTPS voor gegevensoverdracht, moet alleen poort 443 uitgaand toegankelijk zijn. Azure File Sync maakt geen gebruik van het SMB-protocol om gegevens over te dragen van uw on-premises Windows-servers naar uw Azure-bestandsshare.
- Het Azure File Sync-synchronisatieprotocol is een HTTPS-gebaseerd protocol voor het uitwisselen van synchronisatiekennis, d.w.z. de versie-informatie over de bestanden en mappen in uw omgeving, tussen eindpunten in uw omgeving. Dit protocol wordt ook gebruikt om metagegevens uit te wisselen over de bestanden en mappen in uw omgeving, zoals tijdstempels en toegangsbeheerlijsten (ACL’s). 

Omdat Azure Files rechtstreekse SMB-protocoltoegang op Azure-bestandsshares biedt, vragen klanten zich vaak af of ze een speciaal netwerk moeten configureren om de Azure-bestandsshares te koppelen aan SMB voor toegang door de Azure File Sync-agent. Dit is niet alleen niet nodig, maar wordt ook afgeraden, behalve voor beheerdersscenario’s, vanwege het gebrek aan snelle detectie van wijzigingen die rechtstreeks aan de Azure-bestandsshare worden aangebracht (wijzigingen worden soms pas meer dan 24 uur later ontdekt, afhankelijk van de grootte van items en het aantal items in de Azure-bestandsshare). Als u de Azure-bestandsshare rechtstreeks wilt gebruiken, d.w.z. als u niet Azure File Sync wilt gebruiken om bestanden on-premises in de cache op te slaan, vindt u in [Overzicht van Azure Files-netwerken](storage-files-networking-overview.md) meer informatie over de aandachtspunten voor netwerken voor directe toegang.

Hoewel er voor Azure File Sync geen speciale netwerkconfiguratie nodig is, willen sommige klanten wellicht geavanceerde netwerkinstellingen configureren om de volgende scenario’s mogelijk te maken:

- Interoperabiliteit met de proxyserverconfiguratie van uw organisatie.
- Open de on-premises firewall van uw organisatie naar de services Azure Files en Azure File Sync.
- Tunnel Azure Files en Azure File Sync via ExpressRoute of een VPN-verbinding.

### <a name="configuring-proxy-servers"></a>Proxyservers configureren
Veel organisaties gebruiken een proxyserver als intermediair tussen resources in hun on-premises netwerk en resources buiten hun netwerk, zoals in Azure. Proxyservers zijn handig voor vele toepassingen, zoals netwerkisolatie en -beveiliging, en bewaking en logboekregistratie. Azure File Sync kan volledig samenwerken met een proxyserver, maar u moet dan wel de proxyeindpuntinstellingen voor uw omgeving handmatig configureren met Azure File Sync. Dit moet via PowerShell worden gedaan met behulp van de Azure File Sync-server-cmdlets. 

Zie [Azure File Sync configureren met een proxyserver](storage-sync-files-firewall-and-proxy.md) voor meer informatie over hoe u Azure File Sync kunt configureren met een proxyserver.

### <a name="configuring-firewalls-and-service-tags"></a>Firewalls en servicetags configureren
U kunt uw bestandsservers om beveiligingsredenen isoleren van de meeste internetlocaties. Als u Azure File Sync in uw omgeving wilt gebruiken, moet u uw firewall zodanig aanpassen dat deze wordt geopend voor bepaalde Azure-services. U kunt dit doen door de IP-adresbereiken op te halen voor de services die u nodig hebt, via een mechanisme dat [servicetags](../../virtual-network/service-tags-overview.md) wordt genoemd.

Azure File Sync vereist de IP-adresbereiken voor de volgende services, zoals geïdentificeerd door hun servicetags:

| Service | Beschrijving | Servicetag |
|---------|-------------|-------------|
| Azure File Sync | De Azure File Sync-service, zoals vertegenwoordigd door het opslagsynchronisatieservice-object, is verantwoordelijk voor de kernactiviteit van het synchroniseren van gegevens tussen een Azure-bestandsshare en een Windows-bestandsserver. | `StorageSyncService` |
| Azure Files | Alle gegevens die via Azure File Sync worden gesynchroniseerd, worden opgeslagen in een Azure-bestandsshare. Bestanden die worden gewijzigd op uw Windows-bestandsservers, worden gekopieerd naar uw Azure-bestandsshare, en bestanden die worden gelaagd op uw on-premises bestandsserver, worden naadloos gedownload wanneer een gebruiker ze aanvraagt. | `Storage` |
| Azure Resource Manager | De Azure Resource Manager is de beheerinterface voor Azure. Alle beheeraanroepen, waaronder Azure File Sync-serverregistratie en voortdurende synchronisatieservertaken, vinden plaats via de Azure Resource Manager. | `AzureResourceManager` |
| Azure Active Directory | Azure Active Directory, oftewel Azure AD, bevat de gebruiker-principals die nodig zijn om serverregistratie te autoriseren tegen een opslagsynchronisatieservice, en de service-principals die nodig zijn om Azure File Sync toegang tot uw cloudresources te verlenen. | `AzureActiveDirectory` |

Als u Azure File Sync in Azure gebruikt, kunt u (zelfs als het een andere regio is) de naam van de servicetag rechtstreeks in uw netwerkbeveiligingsgroep gebruiken om verkeer naar die service toe te staan. Zie [Netwerkbeveiligingsgroepen](../../virtual-network/network-security-groups-overview.md) voor meer informatie over hoe u dit doet. 

Als u Azure File Sync on-premises gebruikt, kunt u de servicetag-API gebruiken om specifieke IP-adresbereiken op te halen voor de lijst met toegestane IP-adressen van uw firewall. Er zijn twee manieren om deze informatie op te halen:

- De huidige lijst met IP-adresbereiken voor alle Azure-services die servicetags ondersteunen, wordt wekelijks in het Microsoft Downloadcentrum gepubliceerd in de vorm van een JSON-document. Elke Azure-cloud heeft zijn eigen JSON-document met de IP-adresbereiken die relevant zijn voor die cloud:
    - [Azure openbaar](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure van de Amerikaanse overheid](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure China](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Duitsland](https://www.microsoft.com/download/details.aspx?id=57064)
- Met de API voor servicetagdetectie (preview-versie) kunt u de huidige lijst met servicetags programmatisch ophalen. In de preview-versie kan de API voor servicetagdetectie informatie retourneren die minder actueel is dan informatie die wordt geretourneerd uit de JSON-documenten die in het Microsoft Downloadcentrum worden gepubliceerd. U kunt het API-gebied gebruiken op basis van uw automatiseringsvoorkeur:
    - [REST API](/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure-CLI](/cli/azure/network#az-network-list-service-tags)

Zie [Lijst met toegestane Azure File Sync-IP-adressen](storage-sync-files-firewall-and-proxy.md#allow-list-for-azure-file-sync-ip-addresses) voor meer informatie over hoe u de servicetag-API kunt gebruiken om de adressen van uw services op te halen.

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Verkeer tunnelen via een virtueel particulier netwerk of ExpressRoute
In sommige organisaties moet de communicatie met Azure via een netwerktunnel plaatsvinden, zoals een virtueel particulier netwerk (VPN) of ExpressRoute, voor een extra beveiligingslaag of om te verzekeren dat communicatie met Azure een deterministische route volgt. 

Wanneer u een netwerktunnel tussen uw on-premises netwerk en Azure tot stand brengt, wordt uw on-premises netwerk via peering gekoppeld aan een of meer virtuele netwerken in Azure. Een [virtueel netwerk](../../virtual-network/virtual-networks-overview.md), ook wel VNet genoemd, is te vergelijken met een traditioneel netwerk dat on-premises wordt uitgevoerd. Net zoals een Azure-opslagaccount of een Azure-VM, is een VNet een Azure-resource die in een resourcegroep is geïmplementeerd. 

Azure Files en File Sync ondersteunen de volgende mechanismen om verkeer te tunnelen tussen uw on-premises servers en Azure:

- [Azure VPN-gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): Een VPN-gateway is een speciaal soort virtueel-netwerkgateway die wordt gebruikt om versleuteld verkeer te verzenden tussen een virtueel Azure-netwerk en een andere locatie (zoals een on-premises locatie) via internet. Een Azure-VPN Gateway is een Azure-resource die kan worden geïmplementeerd in een resourcegroep naast een opslagaccount of andere Azure-resources. Omdat Azure File Sync bedoeld is voor gebruik met een on-premises Windows-bestandsserver, zou u gewoonlijk een [site-naar-site-VPN](../../vpn-gateway/design.md#s2smulti) gebruiken, hoewel het technisch gezien ook mogelijk is een [punt-naar-site-VPN](../../vpn-gateway/point-to-site-about.md) te gebruiken. 

    Site-naar-site-VPN-verbindingen verbinden uw virtuele Azure-netwerk met het on-premises netwerk van uw organisatie. Met een site-naar-site-VPN-verbinding kunt u een VPN-verbinding eenmaal configureren, voor een VPN-server of een apparaat dat wordt gehost op het netwerk van uw organisatie, in plaats van dit te doen voor elk clientapparaat dat toegang tot uw Azure-bestandsshare moet hebben. Zie [Een site-naar-site-VPN configureren voor gebruik met Azure Files](storage-files-configure-s2s-vpn.md) voor het vereenvoudigen van de implementatie van een site-naar-site-VPN-verbinding.

- [ExpressRoute](../../expressroute/expressroute-introduction.md). Hiermee kunt u een gedefinieerde route tussen Azure en uw on-premises netwerk maken die niet via internet loopt. Omdat ExpressRoute een toegewezen pad biedt tussen uw on-premises datacentrum en Azure, is ExpressRoute mogelijk handig wanneer netwerkprestaties van belang zijn. ExpressRoute is ook een goede optie wanneer een deterministisch pad naar uw resources in de cloud vereist is vanwege het beleid of de wettelijke vereisten van uw organisatie.

### <a name="private-endpoints"></a>Privé-eindpunten
Naast de standaard openbare eindpunten die Azure Files en File Sync bieden via het opslagaccount en de opslagsynchronisatieservice, bieden Azure Files en File Sync de optie om één of meer privé-eindpunten per resource te hebben. Wanneer u een privé-eindpunt voor een Azure-resource maakt, ontvangt deze een privé-IP-adres binnen de adresruimte van uw virtuele netwerk, zoals uw on-premises Windows-bestandsserver een IP-adres heeft binnen de toegewezen adresruimte van uw on-premises netwerk. 

> [!Important]  
> Om privé-eindpunten te kunnen gebruiken op de opslagsynchronisatieservice-resource, moet u Azure File Sync-agent versie 10.1 of hoger hebben. Agentversies vóór 10.1 bieden geen ondersteuning voor privé-eindpunten op de opslagsynchronisatieservice. Alle eerdere agentversies ondersteunen wel privé-eindpunten op de opslagaccount-resource.

Een privé-eindpunt is gekoppeld aan een specifiek subnet van het virtuele Azure-netwerk. Opslagaccounts en opslagsynchronisatieservices kunnen privé-eindpunten hebben in meer dan één virtueel netwerk.

Door gebruik te maken van privé-eindpunten kunt u het volgende doen:
- Veilig verbinding maken met uw Azure-resources vanuit on-premises netwerken met behulp van een VPN- of ExpressRoute-verbinding met privé-peering.
- Uw Azure-resources beveiligen door de openbare eindpunten voor Azure Files en File Sync uit te schakelen. Standaard worden verbindingen met het openbare eindpunt niet geblokkeerd als u een privé-eindpunt maakt.
- De beveiliging voor het virtuele netwerk verbeteren door gegevensoverdracht van het virtuele netwerk (en de grenzen van peering) te blokkeren.

Zie [Privé-eindpunten configureren voor Azure File Sync](storage-sync-files-networking-endpoints.md) voor het maken van een privé-eindpunt.

### <a name="private-endpoints-and-dns"></a>Privé-eindpunten en DNS
Wanneer u een privé-eindpunt maakt, wordt standaard ook een privé-DNS-zone gemaakt (of een bestaande privé-DNS-zone bijgewerkt) die overeenkomt met het `privatelink`-subdomein. Voor openbare cloudregio’s zijn deze DNS-zones `privatelink.file.core.windows.net` voor Azure Files en `privatelink.afs.azure.net` voor Azure File Sync.

> [!Note]  
> In dit artikel wordt het DNS-achtervoegsel voor opslagaccounts gebruikt voor de openbare regio's van Azure, te weten `core.windows.net`. Deze opmerking geldt ook voor onafhankelijke Azure-clouds zoals de Azure-cloud voor de Amerikaanse overheid en de Azure China-cloud. In dat geval vervangt u het achtervoegsel door dat van de onafhankelijke cloud. 

Wanneer u privé-eindpunten maakt voor een opslagaccount en een opslagsynchronisatieservice, worden er A-records voor gemaakt in hun respectieve privé-DNS-zones. Ook wordt de openbare DNS-vermelding zodanig bijgewerkt dat de reguliere Fully Qualified Domain Names CNAME’s zijn voor de relevante privatelink-naam. Zo kunnen de Fully Qualified Domain Names naar het IP-adres(sen) van het privé-eindpunt wijzen wanneer de aanvrager zich binnen het virtuele netwerk bevindt en naar het IP-adres(sen) van het openbare eindpunt wijzen wanneer de aanvrager zich buiten het virtuele netwerk bevindt. 

Voor Azure Files is er voor elk privé-eindpunt één Fully Qualified Domain Name (die het patroon `storageaccount.privatelink.file.core.windows.net` volgt) toegewezen aan één privé-IP-adres voor het privé-eindpunt. Voor Azure File Sync heeft elk privé-eindpunt vier Fully Qualified Domain Names, voor de vier verschillende eindpunten die Azure File Sync beschikbaar maakt: beheer, synchronisatie (primair), synchronisatie (secundair) en bewaking. De Fully Qualified Domain Names voor deze eindpunten volgen gewoonlijk de naam van de opslagsynchronisatieservice, tenzij de naam niet-ASCII-tekens bevat. Als de naam van uw opslagsynchronisatieservice bijvoorbeeld `mysyncservice` is in de regio VS - west 2, zouden de equivalente eindpunten `mysyncservicemanagement.westus2.afs.azure.net`, `mysyncservicesyncp.westus2.afs.azure.net`, `mysyncservicesyncs.westus2.afs.azure.net` en `mysyncservicemonitoring.westus2.afs.azure.net` zijn. Elk privé-eindpunt voor een opslagsynchronisatieservice bevat vier unieke IP-adressen. 

Omdat uw privé-DNS-zone van Azure is verbonden met het virtuele netwerk met het privé-eindpunt, kunt u de DNS-configuratie bekijken wanneer u de `Resolve-DnsName`-cmdlet aanroept vanuit PowerShell in een Azure VM (`nslookup` in Windows en Linux):

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

Dit weerspiegelt het feit dat Azure Files en Azure File Sync zowel hun openbare eindpunten als één of meer privé-eindpunten per resource beschikbaar kunnen maken. Om ervoor te zorgen dat de Fully Qualified Domain Names voor uw resources worden omgezet in de privé-IP-adressen van de privé-eindpunten, moet u de configuratie op uw on-premises DNS-servers wijzigen. Dit kan op verschillende manieren worden gerealiseerd:

- Het hosts-bestand op uw clients wijzigen zodat de Fully Qualified Domain Names voor uw opslagaccounts en opslagsynchronisatieservices worden omgezet in de gewenste privé-IP-adressen. Dit wordt sterk afgeraden voor productieomgevingen, omdat u deze wijzigingen moet aanbrengen op elke client die toegang tot uw privé-eindpunten nodig heeft. Wijzigingen in uw privé-eindpunten/resources (verwijderingen, aanpassingen, enz.) worden niet automatisch verwerkt.
- DNS-zones op uw on-premises servers maken voor `privatelink.file.core.windows.net` en `privatelink.afs.azure.net` met A-records voor uw Azure-resources. Dit heeft als voordeel dat clients in uw on-premises omgeving automatisch Azure-resources kunnen omzetten zonder dat elke client hoeft te worden geconfigureerd. Het nadeel van deze oplossing is echter, net als bij het hosts-bestand, dat wijzigingen niet worden doorgevoerd. Hoewel deze oplossing niet ideaal is, is het mogelijk wel de beste keuze voor sommige omgevingen.
- De zones `core.windows.net` en `afs.azure.net` doorsturen van uw on-premises DNS-servers naar uw privé-DNS-zone van Azure. De privé-DNS-host van Azure kan worden bereikt via een speciaal IP-adres (`168.63.129.16`) dat alleen toegankelijk is in virtuele netwerken die zijn gekoppeld aan de privé-DNS-zone van Azure. Als u deze beperking wilt omzeilen, kunt u extra DNS-servers in uw virtuele netwerk uitvoeren waarmee `core.windows.net` en `afs.azure.net` worden doorgestuurd naar de equivalente privé-DNS-zones van Azure. Om het instellen te vereenvoudigen, zijn er PowerShell-cmdlets beschikbaar waarmee DNS-servers automatisch in uw virtuele Azure-netwerk worden geïmplementeerd en u deze naar wens kunt configureren. Zie [DNS configureren met Azure Files](storage-files-networking-dns.md) voor meer informatie over het instellen van DNS-doorschakeling.

## <a name="encryption-in-transit"></a>Versleuteling in-transit
Verbindingen die worden gemaakt van de Azure File Sync-agent naar uw Azure-bestandsshare of opslagsynchronisatieservice, worden altijd versleuteld. Hoewel Azure-opslagaccounts een instelling hebben om het vereisen van versleuteling in-transit uit te schakelen voor communicatie met Azure Files (en de andere Azure-opslagservices die buiten het opslagaccount worden beheerd), heeft het uitschakelen van deze instelling geen invloed op de versleuteling van Azure File Sync voor communicatie met Azure Files. Standaard is versleuteling in-transit ingeschakeld voor alle Azure-opslagaccounts. 

Zie [Veilige overdracht vereisen in Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor meer informatie over versleuteling in-transit.

## <a name="see-also"></a>Zie ook
- [Planning voor een Azure Files Sync-implementatie](storage-sync-files-planning.md)
- [Azure Files SYNC implementeren](storage-sync-files-deployment-guide.md)