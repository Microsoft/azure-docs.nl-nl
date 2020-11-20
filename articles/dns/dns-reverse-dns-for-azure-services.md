---
title: Omgekeerde DNS voor Azure-Services-Azure DNS
description: Met dit leer traject gaat u aan de slag met het configureren van reverse DNS-Zoek opdrachten voor services die worden gehost in Azure.
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 3adbf34e4c19a76c2b5b2b1968c7821f37965929
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965626"
---
# <a name="configure-reverse-dns-for-services-hosted-in-azure"></a>Omgekeerde DNS configureren voor services die worden gehost in azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In dit artikel wordt uitgelegd hoe u reverse DNS-Zoek opdrachten kunt configureren voor services die worden gehost in Azure.

Services in azure gebruiken IP-adressen die zijn toegewezen door Azure en eigendom zijn van micro soft. Deze omgekeerde DNS-records (PTR-records) moeten worden gemaakt in de bijbehorende reverse DNS lookup-zones van micro soft. In dit artikel wordt uitgelegd hoe u dit doet.

Dit scenario mag niet worden verward met de mogelijkheid om [de reverse DNS-lookup zones te hosten voor uw toegewezen IP-bereiken in azure DNS](dns-reverse-dns-hosting.md). In dit geval moeten de IP-bereiken die worden vertegenwoordigd door de zone voor reverse lookup, meestal door uw Internet provider worden toegewezen aan uw organisatie.

Voordat u dit artikel leest, moet u bekend zijn met dit [overzicht van omgekeerde DNS en ondersteuning in azure](dns-reverse-dns-overview.md).

In Azure DNS worden reken bronnen (zoals virtuele machines, schaal sets voor virtuele machines of Service Fabric clusters) weer gegeven via een PublicIpAddress-resource. Omgekeerde DNS-Zoek opdrachten worden geconfigureerd met de eigenschap ReverseFqdn van de PublicIpAddress.


Omgekeerde DNS wordt momenteel niet ondersteund voor de Azure App Service.

## <a name="validation-of-reverse-dns-records"></a>Validatie van omgekeerde DNS-records

Een derde partij mag geen reverse DNS-records voor hun Azure-Service toewijzing maken voor uw DNS-domeinen. Om dit te voor komen, staat Azure alleen het maken van een omgekeerde DNS-record toe, waarbij de domein naam die is opgegeven in de omgekeerde DNS-record hetzelfde is als of wordt omgezet naar de DNS-naam of het IP-adres van een PublicIpAddress of Cloud service in hetzelfde Azure-abonnement.

Deze validatie wordt alleen uitgevoerd wanneer de omgekeerde DNS-record is ingesteld of gewijzigd. Periodieke hervalidatie wordt niet uitgevoerd.

Bijvoorbeeld: Stel dat de PublicIpAddress-resource de DNS-naam contosoapp1.northus.cloudapp.azure.com en het IP-adres 23.96.52.53 heeft. U kunt de ReverseFqdn voor de PublicIpAddress als volgt opgeven:
* De DNS-naam voor de PublicIpAddress, contosoapp1.northus.cloudapp.azure.com
* De DNS-naam voor een andere PublicIpAddress in hetzelfde abonnement, zoals contosoapp2.westus.cloudapp.azure.com
* Een Vanity DNS-naam, zoals app1.contoso.com, zolang deze naam *eerst* is geconfigureerd als een CNAME-naar-contosoapp1.northus.cloudapp.Azure.com, of op een andere PublicIpAddress in hetzelfde abonnement.
* Een Vanity DNS-naam, zoals app1.contoso.com, zolang deze naam *eerst* is geconfigureerd als een record voor het IP-adres 23.96.52.53, of op het IP-adres van een andere PublicIpAddress in hetzelfde abonnement.

Dezelfde beperkingen gelden voor reverse DNS voor Cloud Services.


## <a name="reverse-dns-for-publicipaddress-resources"></a>Omgekeerde DNS voor PublicIpAddress-resources

In deze sectie vindt u gedetailleerde instructies voor het configureren van omgekeerde DNS voor PublicIpAddress-resources in het Resource Manager-implementatie model met behulp van Azure PowerShell, Azure Classic CLI of Azure CLI. Het configureren van omgekeerde DNS voor PublicIpAddress-resources wordt momenteel niet ondersteund via de Azure Portal.

Azure biedt momenteel alleen ondersteuning voor omgekeerde DNS voor IPv4-PublicIpAddress-resources. Het wordt niet ondersteund voor IPv6.

### <a name="add-reverse-dns-to-an-existing-publicipaddresses"></a>Omgekeerde DNS toevoegen aan een bestaande PublicIpAddresses

#### <a name="powershell"></a>PowerShell

Omgekeerde DNS bijwerken naar een bestaande PublicIpAddress:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

U moet ook een DNS-naam opgeven om omgekeerde DNS toe te voegen aan een bestaande PublicIpAddress die nog geen DNS-naam heeft:

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

Omgekeerde DNS toevoegen aan een bestaande PublicIpAddress:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -f contosoapp1.westus.cloudapp.azure.com.
```

U moet ook een DNS-naam opgeven om omgekeerde DNS toe te voegen aan een bestaande PublicIpAddress die nog geen DNS-naam heeft:

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure CLI

Omgekeerde DNS toevoegen aan een bestaande PublicIpAddress:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

U moet ook een DNS-naam opgeven om omgekeerde DNS toe te voegen aan een bestaande PublicIpAddress die nog geen DNS-naam heeft:

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

### <a name="create-a-public-ip-address-with-reverse-dns"></a>Een openbaar IP-adres maken met omgekeerde DNS

Een nieuw PublicIpAddress maken met de omgekeerde DNS-eigenschap die al is opgegeven:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

#### <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

```azurecli
azure network public-ip create -n PublicIp -g MyResourceGroup -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip create --name PublicIp --resource-group MyResourceGroup --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

### <a name="view-reverse-dns-for-an-existing-publicipaddress"></a>Omgekeerde DNS weer geven voor een bestaande PublicIpAddress

De geconfigureerde waarde voor een bestaande PublicIpAddress weer geven:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
```

#### <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

```azurecli
azure network public-ip show -n PublicIp -g MyResourceGroup
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip show --name PublicIp --resource-group MyResourceGroup
```

### <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Omgekeerde DNS verwijderen uit bestaande Openbare IP-adressen

Een omgekeerde DNS-eigenschap verwijderen uit een bestaande PublicIpAddress:

#### <a name="powershell"></a>PowerShell

```powershell
$pip = Get-AzPublicIpAddress -Name "PublicIp" -ResourceGroupName "MyResourceGroup"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzPublicIpAddress -PublicIpAddress $pip
```

#### <a name="azure-classic-cli"></a>Klassieke versie van Azure CLI

```azurecli
azure network public-ip set -n PublicIp -g MyResourceGroup –f ""
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network public-ip update --resource-group MyResourceGroup --name PublicIp --reverse-fqdn ""
```


## <a name="configure-reverse-dns-for-cloud-services"></a>Omgekeerde DNS configureren voor Cloud Services

In deze sectie vindt u gedetailleerde instructies voor het configureren van omgekeerde DNS voor Cloud Services in het klassieke implementatie model met behulp van Azure PowerShell. Het configureren van omgekeerde DNS voor Cloud Services wordt niet ondersteund via de Azure Portal, de klassieke Azure-CLI of de Azure CLI.

### <a name="add-reverse-dns-to-existing-cloud-services"></a>Omgekeerde DNS toevoegen aan bestaande Cloud Services

Een reverse DNS-record toevoegen aan een bestaande Cloud service:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="create-a-cloud-service-with-reverse-dns"></a>Een Cloud service maken met omgekeerde DNS

Een nieuwe Cloud service maken met de omgekeerde DNS-eigenschap die al is opgegeven:

```powershell
New-AzureService –ServiceName "contosoapp1" –Location "West US" –Description "App1 with Reverse DNS" –ReverseDnsFqdn "contosoapp1.cloudapp.net."
```

### <a name="view-reverse-dns-for-existing-cloud-services"></a>Omgekeerde DNS weer geven voor bestaande Cloud Services

De omgekeerde DNS-eigenschap voor een bestaande Cloud service weer geven:

```powershell
Get-AzureService "contosoapp1"
```

### <a name="remove-reverse-dns-from-existing-cloud-services"></a>Omgekeerde DNS verwijderen uit bestaande Cloud Services

Een omgekeerde DNS-eigenschap verwijderen uit een bestaande Cloud service:

```powershell
Set-AzureService –ServiceName "contosoapp1" –Description "App1 with Reverse DNS" –ReverseDnsFqdn ""
```

## <a name="faq"></a>Veelgestelde vragen

### <a name="how-much-do-reverse-dns-records-cost"></a>Wat zijn de kosten voor reverse DNS-records?

Ze zijn gratis.  Er zijn geen extra kosten voor reverse DNS-records of-query's.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>Worden mijn reverse DNS-records omgezet van Internet?

Ja. Zodra u de omgekeerde DNS-eigenschap voor uw Azure-service hebt ingesteld, beheert Azure alle DNS-delegeringen en DNS-zones die zijn vereist om ervoor te zorgen dat reverse DNS-records worden omgezet voor alle Internet gebruikers.

### <a name="are-default-reverse-dns-records-created-for-my-azure-services"></a>Worden standaard reverse DNS-records gemaakt voor mijn Azure-Services?

Nee. Omgekeerde DNS is een opt-in-functie. Er worden geen standaard reverse DNS-records gemaakt als u deze niet wilt configureren.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Wat is de indeling voor de FQDN-naam (Fully Qualified Domain Name)?

FQDN-standaards worden opgegeven in een voorwaartse volg orde en moeten worden afgesloten met een punt (bijvoorbeeld ' app1.contoso.com ').

### <a name="what-happens-if-the-validation-check-for-the-reverse-dns-ive-specified-fails"></a>Wat gebeurt er als de validatie controle voor de omgekeerde DNS die ik heb opgegeven, mislukt?

Wanneer de omgekeerde DNS-validatie controle mislukt, mislukt de bewerking voor het configureren van de omgekeerde DNS-record. Corrigeer zo nodig de omgekeerde DNS-waarde en probeer het opnieuw.

### <a name="can-i-configure-reverse-dns-for-azure-app-service"></a>Kan ik omgekeerde DNS voor Azure App Service configureren?

Nee. Omgekeerde DNS wordt niet ondersteund voor de Azure App Service.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-azure-service"></a>Kan ik meerdere reverse DNS-records voor mijn Azure-service configureren?

Nee. Azure biedt ondersteuning voor één reverse DNS-record voor elke Azure-Cloud service of-PublicIpAddress.

### <a name="can-i-configure-reverse-dns-for-ipv6-publicipaddress-resources"></a>Kan ik omgekeerde DNS voor IPv6 PublicIpAddress-bronnen configureren?

Nee. Azure biedt momenteel alleen ondersteuning voor omgekeerde DNS voor IPv4 PublicIpAddress-resources en Cloud Services.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Kan ik e-mail berichten verzenden naar externe domeinen vanuit mijn Azure Compute-Services?

De technische mogelijkheid om rechtstreeks e-mail berichten te verzenden vanuit een Azure-implementatie is afhankelijk van het type abonnement. Micro soft raadt u aan om vertrouwde mail relay services te gebruiken voor het verzenden van uitgaande e-mail, ongeacht het type abonnement. Zie [verbeterde Azure-beveiliging voor het verzenden van e-mails – November 2017 update](../virtual-network/troubleshoot-outbound-smtp-connectivity.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie [Reverse DNS-zoek opdracht op Wikipedia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup)voor meer informatie over omgekeerde DNS.
<br>
Informatie over [het hosten van de zone voor reverse lookup voor het IP-adres bereik dat is toegewezen aan uw Internet provider in azure DNS](dns-reverse-dns-for-azure-services.md).