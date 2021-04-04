---
title: Door sturen van DNS configureren voor Azure Files | Microsoft Docs
description: Meer informatie over het configureren van DNS-door sturen voor Azure Files.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9abe306668a4b20e42e45c498bf85b540dfaaee5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94630189"
---
# <a name="configuring-dns-forwarding-for-azure-files"></a>DNS doorsturen configureren voor Azure Files
Met Azure Files kunt u privé-eind punten maken voor de opslag accounts met uw bestands shares. Hoewel het nuttig is voor veel verschillende toepassingen, zijn privé-eind punten vooral nuttig om verbinding te maken met uw Azure-bestands shares van uw on-premises netwerk met behulp van een VPN-of ExpressRoute-verbinding met behulp van privé-peering. 

Als u verbinding wilt maken met uw opslag account om de netwerk tunnel te passeren, moet de Fully Qualified Domain Name (FQDN) van uw opslag account worden omgezet naar het privé-IP-adres van uw privé-eind punt. Om dit te bereiken, moet u het achtervoegsel van het opslag eindpunt ( `core.windows.net` voor open bare Cloud regio's) door sturen naar de privé-DNS-service van Azure die toegankelijk is vanuit uw virtuele netwerk. In deze hand leiding wordt uitgelegd hoe DNS-door sturen kan worden ingesteld en geconfigureerd om te worden omgezet in het IP-adres van het privé-eind punt van uw opslag account.

We raden u ten zeerste [aan de planning te lezen van een Azure files implementatie](storage-files-planning.md) en [Azure files netwerk overwegingen](storage-files-networking-overview.md) voordat u de stappen die in dit artikel worden beschreven, uitvoert.

## <a name="overview"></a>Overzicht
Azure Files biedt twee hoofdtypen eindpunten voor toegang tot Azure-bestandsshares: 
- Openbare eindpunten, die een openbaar IP-adres hebben en overal ter wereld toegankelijk zijn.
- Privé-eindpunten, die zich binnen een virtueel netwerk bevinden en een privé-IP-adres hebben in de adresruimte van het virtuele netwerk.

Openbare en privé-eindpunten bestaan in het Azure-opslagaccount. Een opslagaccount is een beheerconstructie die een gedeelde opslaggroep vertegenwoordigt waarin u meerdere bestandsshares kunt implementeren, evenals andere opslagresources, zoals blob-containers of wachtrijen.

Elk opslag account heeft een Fully Qualified Domain Name (FQDN). Voor de open bare Cloud regio's volgt deze FQDN het patroon `storageaccount.file.core.windows.net` waarin de `storageaccount` naam van het opslag account is. Wanneer u aanvragen voor deze naam maakt, zoals het koppelen van de share op uw werk station met behulp van SMB, voert het besturings systeem een DNS-zoek opdracht uit om de Fully Qualified Domain Name om te zetten in een IP-adres dat kan worden gebruikt om de SMB-aanvragen te verzenden naar.

Wordt standaard `storageaccount.file.core.windows.net` omgezet naar het IP-adres van het open bare eind punt. Het open bare eind punt voor een opslag account wordt gehost op een Azure Storage-cluster waarop veel andere open bare eind punten van opslag accounts worden gehost. Wanneer u een persoonlijk eind punt maakt, wordt een privé-DNS-zone gekoppeld aan het virtuele netwerk waaraan deze is toegevoegd, met een CNAME-record toewijzing `storageaccount.file.core.windows.net` aan een record vermelding voor het privé-IP-adres van het privé-eind punt van uw opslag account. Hierdoor kunt u `storageaccount.file.core.windows.net` FQDN binnen het virtuele netwerk gebruiken en deze omzetten in het IP-adres van het privé-eind punt.

Omdat het uiteindelijke doel is om toegang te krijgen tot de Azure-bestands shares die worden gehost in het opslag account van on-premises met behulp van een netwerk tunnel, zoals een VPN-of ExpressRoute-verbinding, moet u uw lokale DNS-servers zo configureren dat aanvragen worden doorgestuurd naar de Azure Files-service naar de persoonlijke DNS-service van Azure. Hiervoor moet u *voorwaardelijk door sturen* van `*.core.windows.net` (of het juiste achtervoegsel van het opslag eindpunt voor de nationale Clouds van de Amerikaanse overheid, Duitsland of China) instellen op een DNS-server die wordt gehost in uw virtuele Azure-netwerk. Deze DNS-server stuurt de aanvraag vervolgens recursief door naar de privé-DNS-service van Azure, waarmee de Fully Qualified Domain Name van het opslag account wordt omgezet naar het juiste privé-IP-adres.

Voor het configureren van DNS-door sturen voor Azure Files moet een virtuele machine worden uitgevoerd voor het hosten van een DNS-server om de aanvragen door te sturen, maar dit is een eenmalige stap voor alle Azure-bestands shares die worden gehost in uw virtuele netwerk. Daarnaast is dit geen exclusieve vereiste voor het Azure Files-elke Azure-service die persoonlijke eind punten ondersteunt waartoe u vanaf de on-premises toegang wilt, kan het gebruik van de DNS-door Voer configureren in deze hand leiding: Azure Blob-opslag, SQL Azure, Cosmos DB, enzovoort. 

In deze hand leiding worden de stappen beschreven voor het configureren van DNS-door sturen voor het Azure Storage-eind punt, dus naast Azure Files, aanvragen voor DNS-naam omzetting voor alle andere Azure Storage-services (Azure Blob-opslag, Azure-tabel opslag, Azure Queue-opslag, enzovoort) worden doorgestuurd naar de privé-DNS-service van Azure. Extra eind punten voor andere Azure-Services kunnen eventueel ook worden toegevoegd. DNS-door sturen naar uw on-premises DNS-servers wordt ook geconfigureerd, waardoor Cloud bronnen in uw virtuele netwerk (zoals een DFS-N-server) worden omgezet om on-premises computer namen om te zetten. 

## <a name="prerequisites"></a>Vereisten
Voordat u het door sturen van DNS naar Azure Files kunt instellen, moet u de volgende stappen hebben voltooid:

- Een opslag account met een Azure-bestands share die u wilt koppelen. Zie [een Azure-bestands share maken](storage-how-to-create-file-share.md)voor meer informatie over het maken van een opslag account en een Azure-bestands share.
- Een persoonlijk eind punt voor het opslag account. Zie [een persoonlijk eind punt maken](storage-files-networking-endpoints.md#create-a-private-endpoint)voor meer informatie over het maken van een persoonlijk eind punt voor Azure files.
- De [nieuwste versie](/powershell/azure/install-az-ps) van de Azure PowerShell-module.

> [!Important]  
> In deze hand leiding wordt ervan uitgegaan dat u de DNS-server in Windows Server gebruikt in uw on-premises omgeving. Alle stappen die in deze hand leiding worden beschreven, zijn mogelijk met een DNS-server, niet alleen de Windows DNS-server.

## <a name="manually-configuring-dns-forwarding"></a>DNS-door sturen hand matig configureren
Als u al DNS-servers in uw virtuele Azure-netwerk hebt, of als u liever uw eigen virtuele machines implementeert om DNS-servers te zijn door een wille keurige methodologie die door uw organisatie wordt gebruikt, kunt u DNS hand matig configureren met de ingebouwde DNS-Server Power shell-cmdlets.

Maak op uw on-premises DNS-servers een voorwaardelijke doorstuur server met behulp van `Add-DnsServerConditionalForwarderZone` . Deze voorwaardelijke doorstuur server moet worden geïmplementeerd op al uw on-premises DNS-servers om effectief verkeer naar Azure door te sturen. Vergeet niet `<azure-dns-server-ip>` door de juiste IP-adressen voor uw omgeving te vervangen.

```powershell
$vnetDnsServers = "<azure-dns-server-ip>", "<azure-dns-server-ip>"

$storageAccountEndpoint = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers $vnetDnsServers
```

Op de DNS-servers in uw virtuele Azure-netwerk moet u ook een doorstuur server plaatsen zodat aanvragen voor de DNS-zone van het opslag account worden omgeleid naar de privé-DNS-service van Azure, die wordt getransporteerd door het gereserveerde IP-adres `168.63.129.16` . (Vergeet niet om te vullen `$storageAccountEndpoint` Als u de opdrachten in een andere Power shell-sessie uitvoert.)

```powershell
Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers "168.63.129.16"
```

## <a name="using-the-azure-files-hybrid-module-to-configure-dns-forwarding"></a>De hybride module Azure Files gebruiken voor het configureren van DNS-door sturen
Om het configureren van DNS zo eenvoudig mogelijk te maken, hebben we automatisering in de hybride module Azure Files. Met de cmdlets voor het bewerken van DNS in deze module helpt u bij het implementeren van DNS-servers in uw virtuele Azure-netwerk en bij het bijwerken van uw on-premises DNS-servers om deze te verzenden. 

Als u de hybride module Azure Files niet meer hebt gebruikt, moet u deze eerst op uw werk station installeren. Down load de [nieuwste versie](https://github.com/Azure-Samples/azure-files-samples/releases) van de Azure files Hybrid Power shell-module:

```PowerShell
# Unzip the downloaded file
Expand-Archive -Path AzFilesHybrid.zip

# Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\AzFilesHybrid\CopyToPSPath.ps1 

# Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid
```

De implementatie van de DNS-doorstuur oplossing bestaat uit twee stappen: het maken van een DNS-doorstuur regel set, waarmee wordt gedefinieerd naar welke Azure-Services u aanvragen wilt door sturen en de daad werkelijke implementatie van de DNS-doorstuur servers. 

In het volgende voor beeld worden aanvragen doorgestuurd naar het opslag account, inclusief aanvragen voor Azure Files, Azure Blob-opslag, Azure-tabel opslag en Azure Queue-opslag. Desgewenst kunt u door sturen toevoegen voor extra Azure-service naar de regel via de `-AzureEndpoints` para meter van de `New-AzDnsForwardingRuleSet` cmdlet. Vergeet niet om `<virtual-network-resource-group>` , `<virtual-network-name>` en `<subnet-name>` met de juiste waarden voor uw omgeving te vervangen.

```PowerShell
# Create a rule set, which defines the forwarding rules
$ruleSet = New-AzDnsForwardingRuleSet -AzureEndpoints StorageAccountEndpoint

# Deploy and configure DNS forwarders
New-AzDnsForwarder `
        -DnsForwardingRuleSet $ruleSet `
        -VirtualNetworkResourceGroupName "<virtual-network-resource-group>" `
        -VirtualNetworkName "<virtual-network-name>" `
        -VirtualNetworkSubnetName "<subnet-name>"
```

Het kan ook nuttig/nood zakelijk zijn om verschillende extra para meters op te geven:

| Parameternaam | Type | Description |
|----------------|------|-------------|
| `DnsServerResourceGroupName` | `string` | De DNS-servers worden standaard geïmplementeerd in dezelfde resource groep als het virtuele netwerk. Als dit niet gewenst is, kunt u met deze para meter een alternatieve resource groep kiezen die in moet worden geïmplementeerd. |
| `DnsForwarderRootName` | `string` | De DNS-servers die in azure zijn geïmplementeerd, hebben standaard de namen `DnsFwder-*` , waarbij het sterretje door een iterator wordt gevuld. Met deze para meter wordt de hoofdmap van die naam (dat wil zeggen `DnsFwder` ) gewijzigd. |
| `VmTemporaryPassword` | `SecureString` | Standaard wordt een wille keurig wacht woord gekozen voor het tijdelijke standaard account dat een virtuele machine heeft voordat deze lid is van een domein. Wanneer het lid is van een domein, wordt het standaard account uitgeschakeld. |
| `DomainToJoin` | `string` | Het domein om lid te worden van de DNS-VM (s) die u wilt toevoegen. Dit domein wordt standaard gekozen op basis van het domein van de computer waarop u de cmdlets uitvoert. |
| `DnsForwarderRedundancyCount` | `int` | Het aantal DNS-Vm's dat voor het virtuele netwerk moet worden geïmplementeerd. `New-AzDnsForwarder`Implementeert standaard twee DNS-servers in uw virtuele Azure-netwerk, in een beschikbaarheidsset, om redundantie te garanderen. Dit nummer kan naar wens worden gewijzigd. |
| `OnPremDnsHostNames` | `HashSet<string>` | Een hand matig opgegeven lijst met lokale DNS-hostnamen om doorstuur servers te maken. Deze para meter is handig als u geen doorstuur servers wilt Toep assen op alle on-premises DNS-server, bijvoorbeeld wanneer u een bereik met clients hebt met hand matig opgegeven DNS-namen. |
| `Credential` | `PSCredential` | Een referentie die moet worden gebruikt bij het bijwerken van de DNS-servers. Dit is handig wanneer het gebruikers account waarmee u bent aangemeld, geen machtigingen heeft om DNS-instellingen te wijzigen. |
| `SkipParentDomain` | `SwitchParameter` | DNS-doorstuur servers worden standaard toegepast op het domein van het hoogste niveau dat zich in uw omgeving bevindt. Als bijvoorbeeld `northamerica.corp.contoso.com` een onderliggend domein is van `corp.contoso.com` , wordt de doorstuur server gemaakt voor de DNS-servers die zijn gekoppeld aan `corp.contoso.com` . Deze para meter zorgt ervoor dat doorstuur servers worden gemaakt in `northamerica.corp.contoso.com` . |

## <a name="confirm-dns-forwarders"></a>DNS-doorstuur servers bevestigen
Voordat u test om te controleren of de DNS-doorstuur servers zijn toegepast, raden we u aan de DNS-cache op uw lokale werk station uit te scha kelen met `Clear-DnsClientCache` . Als u wilt testen of u de Fully Qualified Domain Name van uw opslag account kunt oplossen, gebruikt u `Resolve-DnsName` of `nslookup` .

```powershell
# Replace storageaccount.file.core.windows.net with the appropriate FQDN for your storage account.
# Note the proper suffix (core.windows.net) depends on the cloud your deployed in.
Resolve-DnsName -Name storageaccount.file.core.windows.net
```

Als de naam omzetting is geslaagd, ziet u dat het opgeloste IP-adres overeenkomt met het IP-adres van uw opslag account.

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
```

Als u al een VPN-of ExpressRoute-verbinding hebt ingesteld, kunt u ook gebruiken `Test-NetConnection` om te zien dat een TCP-verbinding met uw opslag account kan worden uitgevoerd.

```PowerShell
Test-NetConnection -ComputerName storageaccount.file.core.windows.net -CommonTCPPort SMB
```

## <a name="see-also"></a>Zie ook
- [Een Azure Files-implementatie plannen](storage-files-planning.md)
- [Azure Files networking considerations](storage-files-networking-overview.md) (Aandachtspunten voor Azure Files-netwerken)
- [Azure Files-netwerkeindpunten configureren](storage-files-networking-endpoints.md)