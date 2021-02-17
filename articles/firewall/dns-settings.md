---
title: DNS-instellingen Azure Firewall
description: U kunt Azure Firewall configureren met DNS-server-en DNS-proxy-instellingen.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: d6a79e87e9999dd520358e0722011cf4e54d8c63
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546241"
---
# <a name="azure-firewall-dns-settings"></a>DNS-instellingen Azure Firewall

U kunt een aangepaste DNS-server configureren en DNS-proxy inschakelen voor Azure Firewall. Configureer deze instellingen wanneer u de firewall implementeert of configureer deze op een later tijdstip vanaf de pagina **DNS-instellingen** .

## <a name="dns-servers"></a>DNS-servers

Een DNS-server onderhoudt en verhelpt domein namen naar IP-adressen. Azure Firewall maakt standaard gebruik van Azure DNS voor naam omzetting. Met de **DNS-server** instelling kunt u uw eigen DNS-servers configureren voor Azure firewall naam omzetting. U kunt één of meerdere servers configureren.

> [!NOTE]
> Voor exemplaren van Azure Firewall die worden beheerd met behulp van Azure Firewall Manager, worden de DNS-instellingen in het bijbehorende Azure Firewall-beleid geconfigureerd.

### <a name="configure-custom-dns-servers---azure-portal"></a>Aangepaste DNS-servers configureren-Azure Portal

1. Selecteer onder Azure Firewall **instellingen** de optie **DNS-instellingen**.
2. Onder **DNS-servers** kunt u bestaande DNS-servers die eerder in uw virtuele netwerk zijn opgegeven, typen of toevoegen.
3. Selecteer **Opslaan**.

De firewall stuurt nu DNS-verkeer naar de opgegeven DNS-servers voor naam omzetting.

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="Scherm opname met instellingen voor D N S-servers.":::

### <a name="configure-custom-dns-servers---azure-cli"></a>Aangepaste DNS-servers configureren-Azure CLI

In het volgende voor beeld worden Azure Firewall met aangepaste DNS-servers bijgewerkt met behulp van de Azure CLI.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --dns-servers 10.1.0.4 10.1.0.5
```

> [!IMPORTANT]
> Voor de opdracht moet `az network firewall` de Azure cli-extensie `azure-firewall` worden geïnstalleerd. U kunt deze installeren met behulp van de opdracht `az extension add --name azure-firewall` . 

### <a name="configure-custom-dns-servers---azure-powershell"></a>Aangepaste DNS-servers configureren-Azure PowerShell

In het volgende voor beeld worden Azure Firewall met aangepaste DNS-servers bijgewerkt met behulp van Azure PowerShell.

```azurepowershell
$dnsServers = @("10.1.0.4", "10.1.0.5")
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSServer = $dnsServers

$azFw | Set-AzFirewall
```

## <a name="dns-proxy"></a>DNS-proxy

U kunt Azure Firewall configureren om te fungeren als een DNS-proxy. Een DNS-proxy is een intermediair voor DNS-aanvragen van virtuele client machines naar een DNS-server. Als u een aangepaste DNS-server configureert, schakelt u DNS-proxy in om te voor komen dat de DNS-omzetting niet overeenkomt en schakelt u het filteren van FQDN (Fully Qualified Domain Name) in de netwerk regels in.

:::image type="content" source="media/dns-settings/dns-proxy-2.png" alt-text="D N S proxy configuratie met behulp van een aangepaste D N S-server.":::


Als u DNS-proxy niet inschakelt, kunnen DNS-aanvragen van de client op een ander tijdstip worden getransporteerd naar een DNS-server of een andere reactie retour neren vergeleken met de firewall. De DNS-proxy plaatst Azure Firewall in het pad van de client aanvragen om inconsistentie te voor komen.

Wanneer Azure Firewall een DNS-proxy is, zijn er twee typen cache functies mogelijk:

- **Positieve cache**: DNS-omzetting is geslaagd. De firewall gebruikt de TTL (time to Live) van het pakket of het object. 

- **Negatieve cache**: DNS-omzetting resulteert in geen reactie of geen oplossing. De firewall slaat deze informatie één uur in de cache op.

De DNS-proxy slaat alle opgeloste IP-adressen op uit de FQDN-waarden in netwerk regels. Gebruik als best practice FQDN-adressen die worden omgezet in één IP-adres.  

### <a name="dns-proxy-configuration"></a>Configuratie van DNS-proxy

De DNS-proxy configuratie vereist drie stappen:
1. Schakel de DNS-proxy in Azure Firewall DNS-instellingen in.
2. U kunt eventueel uw aangepaste DNS-server configureren of de meegeleverde standaard waarde gebruiken.
3. Configureer het Azure Firewall privé-IP-adres als een aangepast DNS-adres in de instellingen van de DNS-server van uw virtuele netwerk. Deze instelling zorgt ervoor dat DNS-verkeer wordt omgeleid naar Azure Firewall.

#### <a name="configure-dns-proxy---azure-portal"></a>DNS-proxy-Azure Portal configureren

Als u een DNS-proxy wilt configureren, moet u de instelling van de DNS-servers voor het virtuele netwerk configureren voor gebruik van het privé IP-adres van de firewall Schakel vervolgens de DNS-proxy in de **DNS-instellingen** van Azure firewall in.

##### <a name="configure-virtual-network-dns-servers"></a>DNS-servers voor het virtuele netwerk configureren 

1. Selecteer het virtuele netwerk waarin het DNS-verkeer wordt doorgestuurd via het Azure Firewall-exemplaar.
2. Selecteer onder **Instellingen** de optie **DNS-servers**.
3. Selecteer onder **DNS-servers** de optie **Aangepast**.
4. Voer het privé IP-adres van de firewall in.
5. Selecteer **Opslaan**.
6. Start de virtuele machines die zijn verbonden met het virtuele netwerk opnieuw op, zodat de nieuwe DNS-server instellingen worden toegewezen. Vm's blijven hun huidige DNS-instellingen gebruiken totdat ze opnieuw zijn opgestart.

##### <a name="enable-dns-proxy"></a>DNS-proxy inschakelen

1. Selecteer uw Azure Firewall-exemplaar.
2. Selecteer **DNS-instellingen** onder **instellingen**.
3. **DNS-proxy** is standaard uitgeschakeld. Als deze instelling is ingeschakeld, luistert de firewall op poort 53 en stuurt DNS-aanvragen door naar de geconfigureerde DNS-servers.
4. Controleer de configuratie van de **DNS-servers** om ervoor te zorgen dat de instellingen geschikt zijn voor uw omgeving.
5. Selecteer **Opslaan**.

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="Scherm afbeelding met instellingen voor de D N S proxy.":::

#### <a name="configure-dns-proxy---azure-cli"></a>DNS-proxy configureren-Azure CLI

U kunt de Azure CLI gebruiken voor het configureren van DNS-proxy-instellingen in Azure Firewall. U kunt dit ook gebruiken om virtuele netwerken bij te werken om Azure Firewall als de DNS-server te gebruiken.

##### <a name="configure-virtual-network-dns-servers"></a>DNS-servers voor het virtuele netwerk configureren

In het volgende voor beeld wordt het virtuele netwerk geconfigureerd om Azure Firewall als de DNS-server te gebruiken.
 
```azurecli-interactive
az network vnet update \
    --name VNetName \ 
    --resource-group VNetRG \
    --dns-servers <firewall-private-IP>
```

##### <a name="enable-dns-proxy"></a>DNS-proxy inschakelen

In het volgende voor beeld wordt de functie DNS-proxy in Azure Firewall ingeschakeld.

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --enable-dns-proxy true
```

#### <a name="configure-dns-proxy---azure-powershell"></a>DNS-proxy-Azure PowerShell configureren

U kunt Azure PowerShell gebruiken om DNS-proxy-instellingen te configureren in Azure Firewall. U kunt dit ook gebruiken om virtuele netwerken bij te werken om Azure Firewall als de DNS-server te gebruiken.

##### <a name="configure-virtual-network-dns-servers"></a>DNS-servers voor het virtuele netwerk configureren

In het volgende voor beeld wordt het virtuele netwerk geconfigureerd om Azure Firewall als een DNS-server te gebruiken.

```azurepowershell
$dnsServers = @("<firewall-private-IP>")
$VNet = Get-AzVirtualNetwork -Name "VNetName" -ResourceGroupName "VNetRG"
$VNet.DhcpOptions.DnsServers = $dnsServers

$VNet | Set-AzVirtualNetwork
```

##### <a name="enable-dns-proxy"></a>DNS-proxy inschakelen

In het volgende voor beeld wordt de functie DNS-proxy in Azure Firewall ingeschakeld.

```azurepowershell
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSEnableProxy = $true

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Volgende stappen

[FQDN-filtering in netwerk regels](fqdn-filtering-network-rules.md)
