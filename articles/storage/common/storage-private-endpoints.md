---
title: Privé-eindpunten gebruiken
titleSuffix: Azure Storage
description: Overzicht van persoonlijke eind punten voor beveiligde toegang tot opslag accounts van virtuele netwerken.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 3fcc58f626622bcc728265e782906226859e1bf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104600459"
---
# <a name="use-private-endpoints-for-azure-storage"></a>Privé-eind punten voor Azure Storage gebruiken

U kunt [privé-eind punten](../../private-link/private-endpoint-overview.md) voor uw Azure Storage-accounts gebruiken om clients op een virtueel netwerk (VNet) toe te staan om veilig toegang te krijgen tot gegevens via een [privé-koppeling](../../private-link/private-link-overview.md). Het persoonlijke eind punt gebruikt een IP-adres uit de VNet-adres ruimte voor uw Storage-account service. Netwerk verkeer tussen de clients in het VNet en het opslag account gaat over het VNet en een persoonlijke koppeling in het micro soft-backbone-netwerk, waardoor de bloot stelling van het open bare Internet wordt voor komen.

Door gebruik te maken van privé-eind punten voor uw opslag account kunt u:

- Beveilig uw opslag account door de opslag firewall zodanig te configureren dat alle verbindingen op het open bare eind punt voor de opslag service worden geblokkeerd.
- Verbeter de beveiliging van het virtuele netwerk (VNet) door u in staat te stellen exfiltration van gegevens van het VNet te blok keren.
- Maak veilig verbinding met opslag accounts vanuit on-premises netwerken die verbinding maken met het VNet met behulp van [VPN-](../../vpn-gateway/vpn-gateway-about-vpngateways.md) of [expressroutes waaraan](../../expressroute/expressroute-locations.md) met privé-peering.

## <a name="conceptual-overview"></a>Conceptueel overzicht

![Overzicht van privé-eind punten voor Azure Storage](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

Een persoonlijk eind punt is een speciale netwerk interface voor een Azure-service in uw [Virtual Network](../../virtual-network/virtual-networks-overview.md) (VNet). Wanneer u een persoonlijk eind punt maakt voor uw opslag account, biedt het een beveiligde verbinding tussen clients op uw VNet en uw opslag. Het persoonlijke eind punt krijgt een IP-adres uit het IP-adres bereik van uw VNet. De verbinding tussen het persoonlijke eind punt en de opslag service maakt gebruik van een beveiligde persoonlijke koppeling.

Toepassingen in het VNet kunnen naadloos verbinding maken met de opslag service via het persoonlijke eind punt, **met behulp van dezelfde verbindings reeksen en autorisatie mechanismen die ze anders zouden gebruiken**. Privé-eind punten kunnen worden gebruikt met alle protocollen die worden ondersteund door het opslag account, inclusief REST en SMB.

Privé-eind punten kunnen worden gemaakt in subnetten die gebruikmaken van [service-eind punten](../../virtual-network/virtual-network-service-endpoints-overview.md). Clients in een subnet kunnen daarom verbinding maken met één opslag account met behulp van een persoonlijk eind punt, terwijl service-eind punten worden gebruikt voor toegang tot anderen.

Wanneer u een privé-eindpunt voor een opslagservice in uw VNet maakt, wordt er een aanvraag voor goedkeuring verzonden naar de eigenaar van het opslagaccount. Als de gebruiker die het persoonlijke eind punt wil maken ook eigenaar van het opslag account is, wordt deze aanvraag voor toestemming automatisch goedgekeurd.

Eigen aren van opslag accounts kunnen toestemmings aanvragen en de persoonlijke eind punten beheren via het tabblad *privé-eind punten* voor het opslag account in de [Azure Portal](https://portal.azure.com).

> [!TIP]
> Als u de toegang tot uw opslag account wilt beperken via het persoonlijke eind punt, configureert u de firewall voor opslag om toegang via het open bare eind punt te weigeren of te beheren.

U kunt uw opslag account beveiligen, zodat alleen verbindingen van uw VNet worden geaccepteerd door [de opslag firewall te configureren om de](storage-network-security.md#change-the-default-network-access-rule) toegang tot het open bare eind punt standaard te weigeren. U hebt geen firewall regel nodig om verkeer van een VNet met een persoonlijk eind punt toe te staan, omdat de opslag firewall alleen de toegang via het open bare eind punt beheert. In plaats daarvan wordt gebruikgemaakt van de toestemming stroom voor het verlenen van toegang tot de opslag service aan persoonlijke eind punten.

> [!NOTE]
> Bij het kopiëren van blobs tussen opslag accounts moet uw client netwerk toegang hebben tot beide accounts. Als u er dus voor kiest om een persoonlijke koppeling voor slechts één account (de bron of de bestemming) te gebruiken, moet u ervoor zorgen dat uw client netwerk toegang heeft tot het andere account. Zie [Azure Storage firewalls en virtuele netwerken configureren](storage-network-security.md?toc=/azure/storage/blobs/toc.json)voor meer informatie over andere manieren om netwerk toegang te configureren. 

<a id="private-endpoints-for-azure-storage"></a>

## <a name="creating-a-private-endpoint"></a>Een persoonlijk eind punt maken

Zie voor het maken van een persoonlijk eind punt met behulp [van Azure Portal persoonlijke verbinding maken met een opslag account vanuit de ervaring voor het opslag account in de Azure Portal](../../private-link/tutorial-private-endpoint-storage-portal.md).

Zie een van deze artikelen voor het maken van een persoonlijk eind punt met behulp van Power shell of de Azure CLI. Beide bieden een Azure-web-app als de doel service, maar de stappen voor het maken van een persoonlijke koppeling zijn hetzelfde voor een Azure Storage-account.

- [Een persoonlijk eind punt maken met behulp van Azure CLI](../../private-link/create-private-endpoint-cli.md)

- [Een privé=eindpunt maken met Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)



Wanneer u een persoonlijk eind punt maakt, moet u het opslag account en de opslag service opgeven waarmee de verbinding tot stand wordt gebracht. 

U hebt een apart persoonlijk eind punt nodig voor elke opslag Resource waartoe u toegang wilt hebben, namelijk [blobs](../blobs/storage-blobs-overview.md), [Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md), [bestanden](../files/storage-files-introduction.md), [wacht rijen](../queues/storage-queues-introduction.md), [tabellen](../tables/table-storage-overview.md)of [statische websites](../blobs/storage-blob-static-website.md). In het persoonlijke eind punt worden deze opslag services gedefinieerd als de **doel-subbron** van het gekoppelde opslag account. 

Als u een persoonlijk eind punt voor de opslag resource van Data Lake Storage Gen2 maakt, moet u er ook een maken voor de Blob Storage-Resource. Dat komt doordat bewerkingen die gericht zijn op het Data Lake Storage Gen2-eind punt, kunnen worden omgeleid naar het BLOB-eind punt. Door een persoonlijk eind punt voor beide resources te maken, zorgt u ervoor dat de bewerkingen correct kunnen worden uitgevoerd.

> [!TIP]
> Maak een apart persoonlijk eind punt voor het secundaire exemplaar van de opslag service voor betere Lees prestaties op de RA-GRS-accounts.
> Zorg ervoor dat u een opslag account voor algemeen gebruik v2 (Standard of Premium) maakt.

Voor lees toegang tot de secundaire regio met een opslag account dat is geconfigureerd voor geo-redundante opslag, hebt u afzonderlijke privé-eind punten nodig voor de primaire en secundaire instanties van de service. U hoeft geen persoonlijk eind punt te maken voor het secundaire exemplaar voor **failover**. Het persoonlijke eind punt maakt na een failover automatisch verbinding met het nieuwe primaire exemplaar. Zie [Azure Storage redundantie](storage-redundancy.md)voor meer informatie over opties voor opslag redundantie.

<a id="connecting-to-private-endpoints"></a>

## <a name="connecting-to-a-private-endpoint"></a>Verbinding maken met een persoonlijk eind punt

Clients op een VNet met behulp van het privé-eind punt moeten hetzelfde connection string gebruiken voor het opslag account, wanneer clients verbinding maken met het open bare eind punt. We vertrouwen op DNS-omzetting om de verbindingen van het VNet naar het opslag account via een persoonlijke koppeling automatisch te routeren.

> [!IMPORTANT]
> Gebruik hetzelfde connection string om verbinding te maken met het opslag account met behulp van privé-eind punten, zoals u anders zou gebruiken. Maak geen verbinding met het opslag account met behulp van de `privatelink` subdomein-URL.

Er wordt standaard een [privé-DNS-zone](../../dns/private-dns-overview.md) gekoppeld aan het VNet met de vereiste updates voor de privé-eind punten. Als u echter uw eigen DNS-server gebruikt, moet u mogelijk aanvullende wijzigingen aanbrengen in uw DNS-configuratie. In de volgende sectie over [DNS-wijzigingen](#dns-changes-for-private-endpoints) worden de vereiste updates voor persoonlijke eind punten beschreven.

## <a name="dns-changes-for-private-endpoints"></a>DNS-wijzigingen voor privé-eind punten

Wanneer u een persoonlijk eind punt maakt, wordt de DNS CNAME-bron record voor het opslag account bijgewerkt naar een alias in een subdomein met het voor voegsel `privatelink` . Standaard maken we ook een [privé-DNS-zone](../../dns/private-dns-overview.md), die overeenkomt met het `privatelink` subdomein, met de DNS a-bron records voor de privé-eind punten.

Wanneer u de URL van het opslag eindpunt oplost van buiten het VNet met het persoonlijke eind punt, wordt dit omgezet in het open bare eind punt van de opslag service. Wanneer het is opgelost vanuit het VNet dat het persoonlijke eind punt host, wordt de URL van het opslag eindpunt omgezet naar het IP-adres van het privé-eind punt.

Voor het bovenstaande voor beeld is de DNS-bron records voor het opslag account ' StorageAccountA ', wanneer deze zijn omgezet van buiten het VNet dat als host fungeert voor het persoonlijke eind punt:

| Naam                                                  | Type  | Waarde                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<storage service public endpoint\>                   |
| \<storage service public endpoint\>                   | A     | \<storage service public IP address\>                 |

Zoals eerder vermeld, kunt u de toegang tot clients buiten het VNet via het open bare eind punt met behulp van de opslag firewall weigeren of beheren.

De DNS-bron records voor StorageAccountA, wanneer deze zijn opgelost door een client in de VNet die als host fungeert voor het persoonlijke eind punt, zijn:

| Naam                                                  | Type  | Waarde                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

Deze aanpak maakt het mogelijk toegang tot het opslag account te krijgen **met behulp van dezelfde Connection String** voor clients op het VNet dat als host fungeert voor de privé-eind punten, evenals clients buiten het vnet.

Als u een aangepaste DNS-server gebruikt in uw netwerk, moeten clients de FQDN-naam voor het eind punt van het opslag account kunnen omzetten naar het IP-adres van het privé-eind punt. U moet uw DNS-server zo configureren dat uw privé-koppelings subdomein wordt overgedragen aan de privé-DNS-zone voor het VNet, of dat u de A-records voor '*StorageAccountA.privatelink.blob.core.Windows.net*' configureert met het IP-adres van het privé-eind punt.

> [!TIP]
> Wanneer u een aangepaste of lokale DNS-server gebruikt, moet u de DNS-server zo configureren dat de naam van het opslag account in het subdomein wordt omgezet in `privatelink` het IP-adres van het privé-eind punt. U kunt dit doen door het `privatelink` subdomein te delegeren aan de privé-DNS-zone van het VNet of door de DNS-zone op de DNS-server te configureren en de DNS A-records toe te voegen.

De aanbevolen DNS-zone namen voor privé-eind punten voor opslag Services en de bijbehorende subbronnen voor het eind punt zijn:

| Opslag service        | Stel subresource in | Zone naam                            |
| :--------------------- | :------------------ | :----------------------------------- |
| Blob-service           | blob                | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | DFS                 | `privatelink.dfs.core.windows.net`   |
| Bestandsservice           | file                | `privatelink.file.core.windows.net`  |
| Queue-service          | wachtrij               | `privatelink.queue.core.windows.net` |
| Table service          | tabel               | `privatelink.table.core.windows.net` |
| Statische websites        | web                 | `privatelink.web.core.windows.net`   |

Raadpleeg de volgende artikelen voor meer informatie over het configureren van uw eigen DNS-server voor de ondersteuning van persoonlijke eind punten:

- [Naamomzetting voor resources in virtuele Azure-netwerken](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [DNS-configuratie voor privé-eind punten](../../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="pricing"></a>Prijzen

Zie [prijzen van Azure Private Link](https://azure.microsoft.com/pricing/details/private-link) voor meer informatie over prijzen.

## <a name="known-issues"></a>Bekende problemen

Denk aan de volgende bekende problemen met persoonlijke eind punten voor Azure Storage.

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Toegangs beperkingen voor opslag voor clients in VNets met privé-eind punten

Clients in VNets met bestaande persoonlijke eind punten geven gezichts beperkingen bij het openen van andere opslag accounts met persoonlijke eind punten. Stel bijvoorbeeld dat een VNet N1 een persoonlijk eind punt heeft voor een opslag account a1 voor Blob Storage. Als opslag account a2 een persoonlijk eind punt in een VNet N2 heeft voor Blob Storage, moeten clients in VNet N1 ook toegang krijgen tot Blob Storage in account a2 met behulp van een persoonlijk eind punt. Als het opslag account a2 geen privé-eind punten voor Blob Storage heeft, hebben clients in VNet N1 toegang tot de Blob-opslag in dat account zonder een persoonlijk eind punt.

Deze beperking is het gevolg van de DNS-wijzigingen die zijn aangebracht wanneer account a2 een persoonlijk eind punt maakt.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Regels voor netwerkbeveiligingsgroepen voor subnetten met privé-eindpunten

Op dit moment kunt u geen NSG-regels ( [netwerk beveiligings groep](../../virtual-network/network-security-groups-overview.md) ) en door de gebruiker gedefinieerde routes configureren voor privé-eind punten. NSG-regels die worden toegepast op het subnet waarop het persoonlijke eind punt wordt gehost, worden niet toegepast op het persoonlijke eind punt. Ze worden alleen toegepast op andere eind punten (bijvoorbeeld: netwerk interface-controllers). Een beperkte tijdelijke oplossing voor dit probleem is het implementeren van uw toegangs regels voor privé-eind punten op de bron-subnetten, hoewel deze benadering mogelijk een hogere beheer overhead nodig heeft.

### <a name="copying-blobs-between-storage-accounts"></a>Blobs kopiëren tussen opslag accounts

U kunt blobs kopiëren tussen opslag accounts met behulp van privé-eind punten als u de Azure REST API gebruikt, of hulpprogram ma's die gebruikmaken van de REST API. Deze hulpprogram ma's zijn onder andere AzCopy, Storage Explorer, Azure PowerShell, Azure CLI en de Azure Blob Storage Sdk's. 

Alleen persoonlijke eind punten die zijn gericht op de Blob Storage-Resource worden ondersteund. Privé-eind punten die het Data Lake Storage Gen2 doel hebben of de bestands resource, worden nog niet ondersteund. Ook wordt het kopiëren tussen opslag accounts met behulp van het NFS-protocol (Network File System) nog niet ondersteund. 

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage-firewalls en virtuele netwerken configureren](storage-network-security.md)
- [Beveiligings aanbevelingen voor Blob Storage](../blobs/security-recommendations.md)
