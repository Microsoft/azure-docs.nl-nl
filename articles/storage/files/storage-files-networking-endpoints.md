---
title: Netwerkeindpunten configureren voor Azure Files | Microsoft Docs
description: Meer informatie over het configureren van Azure File-netwerk eindpunten.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 42e83facec7817b6588bf69977fea5ab74b6b10d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759875"
---
# <a name="configuring-azure-files-network-endpoints"></a>Azure Files-netwerkeindpunten configureren

Azure Files biedt twee hoofdtypen eindpunten voor toegang tot Azure-bestandsshares: 
- Openbare eindpunten, die een openbaar IP-adres hebben en overal ter wereld toegankelijk zijn.
- Privé-eindpunten, die zich binnen een virtueel netwerk bevinden en een privé-IP-adres hebben in de adresruimte van het virtuele netwerk.

Openbare en privé-eindpunten bestaan in het Azure-opslagaccount. Een opslagaccount is een beheerconstructie die een gedeelde opslaggroep vertegenwoordigt waarin u meerdere bestandsshares kunt implementeren, evenals andere opslagresources, zoals blob-containers of wachtrijen.

Dit artikel richt zich op het configureren van de eindpunten van een opslagaccount voor het rechtstreeks openen van de Azure-bestandsshare. De meeste informatie in dit document is ook van toepassing op de manier waarop Azure File Sync samenwerkt met openbare en privé-eindpunten voor het opslagaccount. Als u echter meer wilt weten over aandachtspunten voor netwerken bij een Azure File Sync-implementatie, raadpleegt u [Azure File Sync proxy and firewall settings](../file-sync/file-sync-firewall-and-proxy.md) (Azure File Sync-proxy- en firewallinstellingen).

Het is raadzaam om [Azure Files networking considerations](storage-files-networking-overview.md) (Aandachtspunten voor Azure Files-netwerken) te lezen voordat u dit artikel verder leest.

## <a name="prerequisites"></a>Vereisten

- In dit artikel wordt ervan uitgegaan dat u al een Azure-abonnement hebt gemaakt. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
- In dit artikel wordt ervan uitgenomen dat u al een Azure-bestands share hebt gemaakt in een opslagaccount waar u on-premises verbinding mee wilt maken. Zie [Een Azure-bestandsshare maken](storage-how-to-create-file-share.md) als u wilt lezen hoe u een Azure-bestandsshare maakt.
- Als u van plan bent om Azure PowerShell te gebruiken, [installeert u de nieuwste versie](/powershell/azure/install-az-ps).
- Als u van plan bent om de Artikel CLI te gebruiken, [installeert u de nieuwste versie](/cli/azure/install-azure-cli).

## <a name="endpoint-configurations"></a>Eindpuntconfiguraties

U kunt uw eindpunten configureren om de netwerktoegang tot uw opslagaccount te beperken. Er zijn twee benaderingen voor het beperken van de toegang van een opslagaccount tot een virtueel netwerk:

- [Een of meer privé-eindpunten maken voor het opslagaccount](#create-a-private-endpoint) en alle toegang tot het openbare eindpunt beperken. Hierdoor heeft alleen verkeer dat afkomstig is uit de gewenste virtuele netwerken toegang tot de Azure-bestandsshares binnen het opslagaccount.
- [Beperk het openbare eindpunt tot een of meer virtuele netwerken.](#restrict-public-endpoint-access) Hiervoor wordt een voorziening van het virtuele netwerk gebruikt met de naam *service-eindpunten*. Wanneer u het verkeer naar een opslagaccount beperkt via een service-eindpunt, hebt u nog steeds toegang tot het opslagaccount via het openbare IP-adres, maar is toegang alleen mogelijk vanaf de locaties die u in uw configuratie opgeeft.

### <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken

Als u een privé-eindpunt maakt voor uw opslagaccount, worden de volgende Azure-resources geïmplementeerd:

- **Een privé-eindpunt**: Een Azure-resource die het privé-eindpunt van het opslagaccount voorstelt. U kunt dit zien als een resource die een verbinding opzet tussen een opslagaccount en een netwerkinterface.
- **Een netwerkinterface (NIC)** : De netwerkinterface die een privé-IP-adres onderhoudt binnen het opgeven virtuele netwerk/subnet. Dit is exact dezelfde resource die wordt geïmplementeerd als u een virtuele machine implementeert, alleen wordt de resource nu niet toegewezen aan een VM, maar is deze het eigendom van het privé-eindpunt.
- **Een privé-DNS-zone**: Als u niet eerder een privé-eindpunt hebt geïmplementeerd voor dit virtuele netwerk, wordt er een nieuwe privé-DNS-zone geïmplementeerd voor uw virtuele netwerk. Er wordt ook een DNS A-record gemaakt voor het opslagaccount in deze DNS-zone. Als u al een privé-eindpunt hebt geïmplementeerd in dit virtuele netwerk, wordt er een nieuwe A-record voor het opslagaccount toegevoegd aan de bestaande DNS-zone. Het implementeren van een DNS-zone is optioneel, maar wordt wel sterk aanbevolen. Het is vereist als u Azure-bestandsshares koppelt met behulp van een AD-serviceprincipal of de FileREST-API.

> [!Note]  
> In dit artikel wordt het DNS-achtervoegsel voor opslagaccounts gebruikt voor de openbare regio's van Azure, te weten `core.windows.net`. Deze opmerking geldt ook voor onafhankelijke Azure-clouds zoals de Azure-cloud voor de Amerikaanse overheid en de Azure China-cloud. In dat geval vervangt u het achtervoegsel door dat van de onafhankelijke cloud. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-private-portal](../../../includes/storage-files-networking-endpoints-private-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-private-powershell](../../../includes/storage-files-networking-endpoints-private-powershell.md)]

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-private-cli](../../../includes/storage-files-networking-endpoints-private-cli.md)]
---

## <a name="verify-connectivity"></a>Connectiviteit verifiëren

# <a name="portal"></a>[Portal](#tab/azure-portal)

Als er een virtuele machine aanwezig is in het virtuele netwerk, of als u het doorsturen in DNS hebt geconfigureerd zoals wordt beschreven in [DNS doorsturen configureren voor Azure Files](storage-files-networking-dns.md), kunt u testen of uw privé-eindpunt goed is ingesteld door de volgende opdrachten uit te voeren vanuit PowerShell, vanaf de opdrachtregel of vanuit de terminal (werkt voor Windows, Linux en macOS). U moet `<storage-account-name>` door de juiste naam van het opslagaccount:

```
nslookup <storage-account-name>.file.core.windows.net
```

Als alles in orde is, ziet u de volgende uitvoer, waarbij `192.168.0.5` het privé-IP-adres is van het privé-eindpunt in uw virtuele netwerk (de weergegeven uitvoer is voor Windows):

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als er een virtuele machine aanwezig is in het virtuele netwerk, of als u het doorsturen in DNS hebt geconfigureerd zoals wordt beschreven in [DNS doorsturen configureren voor Azure Files](storage-files-networking-dns.md), kunt u testen of uw privé-eindpunt goed is ingesteld door de volgende opdrachten uit te voeren:

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Als alles in orde is, ziet u de volgende uitvoer, waarbij `192.168.0.5` het privé-IP-adres is van het privé-eindpunt in uw virtuele netwerk:

```Output
Name                             Type   TTL   Section    NameHost
----                             ----   ---   -------    --------
storageaccount.file.core.windows CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
.net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 600
Section    : Answer
IP4Address : 192.168.0.5
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als er een virtuele machine aanwezig is in het virtuele netwerk, of als u het doorsturen in DNS hebt geconfigureerd zoals wordt beschreven in [DNS doorsturen configureren voor Azure Files](storage-files-networking-dns.md), kunt u testen of uw privé-eindpunt goed is ingesteld door de volgende opdrachten uit te voeren:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Als alles in orde is, ziet u de volgende uitvoer, waarbij `192.168.0.5` het privé-IP-adres is van het privé-eindpunt in uw virtuele netwerk. U moet nog steeds storageaccount.file.core.windows.net om uw bestands share te mounten in plaats van het `privatelink` pad.

```Output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```
---

## <a name="restrict-public-endpoint-access"></a>Toegang tot openbare eindpunten beperken

Als u eerst de toegang tot openbare eindpunten beperkt, moet u algemene toegang tot het openbare eindpunt uitschakelen. Het uitschakelen van toegang tot het openbare eindpunt heeft geen invloed op privé-eindpunten. Nadat het openbare eindpunt is uitgeschakeld, kunt u specifieke netwerken of IP-adressen selecteren die er mogelijk nog toegang toe hebben. Over het algemeen beperken de meeste firewallbeleidsregels voor een opslagaccount de netwerktoegang tot een of meer virtuele netwerken.

#### <a name="disable-access-to-the-public-endpoint"></a>Toegang tot het openbare eindpunt uitschakelen

Wanneer alle toegang tot het openbare eindpunt wordt uitgeschakeld, is het opslagaccount nog toegankelijk via de privé-eindpunten van het account. Anders worden geldige aanvragen naar het openbare eindpunt van het opslagaccount geweigerd, tenzij ze afkomstig zijn [van een bron die specifiek is toegestaan.](#restrict-access-to-the-public-endpoint-to-specific-virtual-networks) 

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-disable-portal](../../../includes/storage-files-networking-endpoints-public-disable-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-disable-powershell](../../../includes/storage-files-networking-endpoints-public-disable-powershell.md)]

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-disable-cli](../../../includes/storage-files-networking-endpoints-public-disable-cli.md)]

---

#### <a name="restrict-access-to-the-public-endpoint-to-specific-virtual-networks"></a>Toegang tot het openbare eindpunt beperken tot specifieke virtuele netwerken

Wanneer u de toegang tot het opslagaccount beperkt tot bepaalde virtuele netwerken, staat u aanvragen naar het openbare eindpunt toe vanuit de opgegeven virtuele netwerken. Hiervoor wordt een voorziening van het virtuele netwerk gebruikt met de naam *service-eindpunten*. Deze voorziening kan worden gebruikt met of zonder privé-eindpunten.

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-portal](../../../includes/storage-files-networking-endpoints-public-restrict-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-powershell](../../../includes/storage-files-networking-endpoints-public-restrict-powershell.md)]

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-cli](../../../includes/storage-files-networking-endpoints-public-restrict-cli.md)]

---

## <a name="see-also"></a>Zie ook

- [Azure Files networking considerations](storage-files-networking-overview.md) (Aandachtspunten voor Azure Files-netwerken)
- [Configuring DNS forwarding for Azure Files](storage-files-networking-dns.md) (DNS doorsturen configureren voor Azure Files)
- [Configuring S2S VPN for Azure Files](storage-files-configure-s2s-vpn.md) (S2S-VPN configureren voor Azure Files)