---
title: Key Vault-firewalls en virtuele netwerken configureren - Azure Key Vault
description: Stapsgewijze instructies om Key Vault-firewalls en virtuele netwerken te configureren
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: sudbalas
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3639237a0dc34c521fd3fa52631fdb19c26ec284
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936342"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Azure Key Vault-firewalls en virtuele netwerken configureren

Dit artikel bevat richtlijnen voor het configureren van de Azure Key Vault-firewall. In dit document worden de verschillende configuraties voor de Key Vault-firewall in detail beschreven en vindt u stapsgewijze instructies voor het configureren van Azure Key Vault om met andere toepassingen en Azure-Services te werken.

Zie [Service-eindpunten voor een virtueel netwerk voor Azure Key Vault](overview-vnet-service-endpoints.md) voor meer informatie.

## <a name="firewall-settings"></a>Firewallinstellingen

Deze sectie gaat over de verschillende manieren waarop de Azure Key Vault-firewall kan worden geconfigureerd.

### <a name="key-vault-firewall-disabled-default"></a>Key Vault-firewall uitgeschakeld (standaard)

Wanneer u een nieuwe sleutel kluis maakt, wordt de Azure Key Vault-firewall standaard uitgeschakeld. Alle toepassingen en Azure-services hebben toegang tot de sleutelkluis en verzenden aanvragen naar de sleutelkluis. Dat betekent echter niet dat elke gebruiker bewerkingen kan uitvoeren op uw sleutelkluis. De sleutelkluis beperkt nog wel de toegang tot geheimen, sleutels en certificaten die in de sleutelkluis zijn opgeslagen door Azure Active Directory-verificatie en toegangsbeleidmachtigingen te vereisen. Meer informatie over sleutelkluisverificatie vindt u in [dit document](./authentication-fundamentals.md) over de basisprincipes van de sleutelkluisverificatie.

### <a name="key-vault-firewall-enabled-trusted-services-only"></a>Key Vault-firewall ingeschakeld (alleen vertrouwde services)

Wanneer u de Key Vault-firewall inschakelt, krijgt u de optie om vertrouwde Microsoft-services toe te staan deze firewall over te slaan. De lijst met vertrouwde services bevat niet elke Azure-service. Zo bevindt Azure DevOps zich niet in de lijst met vertrouwde services. **Dit betekent niet dat services die niet worden weergegeven in de lijst met vertrouwde services niet worden vertrouwd of onveilig zijn.** De lijst met vertrouwde services bevat services waarbij Microsoft alle code beheert die op de service wordt uitgevoerd. Omdat gebruikers aangepaste code kunnen schrijven in Azure-services zoals Azure DevOps, biedt Microsoft geen optie om een algemene goedkeuring voor de service te maken. Bovendien betekent het feit dat een service op de lijst met vertrouwde services staat, niet dat deze in alle scenario's is toegestaan.

Als u wilt bepalen of een service die u wilt gebruiken, zich in de lijst met vertrouwde services bevindt, raadpleegt u [dit document](./overview-vnet-service-endpoints.md#trusted-services).

### <a name="key-vault-firewall-enabled-ipv4-addresses-and-ranges---static-ips"></a>Key Vault Firewall ingeschakeld (IPv4-adressen en -bereiken - vaste IP-adressen)

Als u een bepaalde service wilt toestaan om toegang te krijgen tot de sleutelkluis via de firewall van Key Vault, kunt u het IP-adres ervan toevoegen aan de acceptatielijst van de sleutelkluis. Deze configuratie is het meest geschikt voor services die gebruikmaken van vaste IP-adressen of bekende bereiken.

Als u een IP-adres of bereik van een Azure-resource, zoals een web-app of logische app, wilt toestaan, voert u de volgende stappen uit.

1. Aanmelden bij Azure Portal
1. Selecteer de resource (specifiek exemplaar van de service)
1. Klik op de blade 'Eigenschappen' onder 'Instellingen'
1. Zoek het veld 'IP-adres'.
1. Kopieer deze waarde of dit bereik en voer het in in de acceptatielijst van de sleutelkluis.

Als u een hele Azure-service wilt doorlaten door de Key Vault-firewall, gebruikt u [deze](https://www.microsoft.com/download/details.aspx?id=41653) lijst met openbaar gedocumenteerde IP-adressen voor Azure-datacenters. Zoek de IP-adressen die zijn gekoppeld aan de service die u wilt gebruiken in de gewenste regio en voeg deze IP-adressen toe aan de firewall van de sleutelkluis met behulp van de bovenstaande stappen.

### <a name="key-vault-firewall-enabled-virtual-networks---dynamic-ips"></a>Key Vault Firewall ingeschakeld (virtuele netwerken - dynamische IP-adressen)

Als u een Azure-resource zoals een virtuele machine wilt toelaten via de sleutelkluis, kunt u mogelijk geen vaste IP-adressen gebruiken, en u wilt mogelijk niet alle IP-adressen voor Azure Virtual Machines toegang geven tot uw sleutelkluis.

In dit geval moet u de resource in een virtueel netwerk maken en vervolgens verkeer van het specifieke virtuele netwerk en subnet toestaan om toegang te krijgen tot uw sleutelkluis. Volg de volgende stappen om dit te doen.

1. Aanmelden bij Azure Portal
1. Selecteer de sleutelkluis die u wilt configureren
1. Selecteer de blade 'Netwerken'
1. Selecteer '+Bestaand virtueel netwerk toevoegen'
1. Selecteer het virtuele netwerk en subnet dat u wilt toelaten door de firewall van de sleutelkluis.

### <a name="key-vault-firewall-enabled-private-link"></a>Key Vault Firewall ingeschakeld (Private Link)

Zie het document [hier](./private-link-service.md)als u wilt weten hoe u een verbinding met een privékoppeling kunt configureren in uw sleutelkluis.

> [!IMPORTANT]
> Als de firewallregels van kracht zijn, kunnen gebruikers alleen Key Vault-[gegevenslaagbewerkingen](secure-your-key-vault.md#data-plane-access-control) uitvoeren wanneer hun aanvragen afkomstig zijn van toegestane virtuele netwerken of IPv4-adresbereiken. Dit is tevens van toepassing voor toegang tot Key Vault vanuit Azure Portal. Hoewel gebruikers kunnen bladeren naar een sleutelkluis van Azure Portal, kunnen ze mogelijk geen sleutels, geheimen of certificaten weergeven als hun clientcomputer niet in de lijst met toegestane clients staat. Dit is ook van invloed op de Key Vault-kiezer door andere Azure-Services. Gebruikers zien mogelijk een lijst met sleutelkluizen, maar geen lijst met sleutels als firewallregels hun clientcomputer weigeren.

> [!NOTE]
> Houd rekening met de volgende configuratielimieten:
> * Er zijn maximaal 127 regels voor virtuele netwerken en 127 IPv4-regels toegestaan. 
> * IP-netwerkregels zijn alleen toegestaan voor openbare IP-adressen. IP-adresbereiken die zijn gereserveerd voor privénetwerken (zoals gedefinieerd in RFC 1918) zijn niet toegestaan in IP-regels. Privénetwerken omvatten adressen die beginnen met **10.** , **172.16-31**, en **192.168.** . 
> * Momenteel worden alleen IPv4-adressen ondersteund.

## <a name="use-the-azure-portal"></a>Azure Portal gebruiken

U kunt als volgt Key Vault-firewalls en virtuele netwerken configureren met behulp van Azure Portal:

1. Blader naar de sleutelkluis die u wilt beveiligen.
2. Selecteer **Netwerken** en het tabblad **Firewalls en virtuele netwerken**.
3. Selecteer **Geselecteerde netwerken** onder **Toegang toestaan uit**.
4. Als u bestaande virtuele netwerken wilt toevoegen aan firewalls en regels voor virtuele netwerken, selecteert u **+ Bestaande virtuele netwerken toevoegen**.
5. Selecteer op de nieuwe blade die wordt geopend het abonnement, de virtuele netwerken en de subnetten die u toegang wilt geven tot deze sleutelkluis. Als voor de virtuele netwerken en subnetten die u selecteert geen service-eindpunten zijn ingeschakeld, bevestigt u dat u service-eindpunten wilt inschakelen en selecteert u **Inschakelen**. Het kan tot vijftien minuten duren voordat deze instelling van kracht is.
6. Voeg onder **IP-netwerken** IPv4-adresbereiken toe door IPv4-adresbereiken in [CIDR-notatie (Classless Inter-Domain Routing)](https://tools.ietf.org/html/rfc4632) of afzonderlijke IP-adressen in te voeren.
7. Als u wilt toestaan dat vertrouwde services van Microsoft de Key Vault-firewall overslaan, selecteert u Ja. Klik op de volgende koppeling voor een volledige lijst van de huidige vertrouwde Key Vault-services. [Vertrouwde services van Azure Key Vault](./overview-vnet-service-endpoints.md#trusted-services)
7. Selecteer **Opslaan**.

U kunt ook nieuwe virtuele netwerken en subnetten toevoegen en vervolgens service-eindpunten inschakelen voor de zojuist gemaakte virtuele netwerken en subnetten door **+ Nieuw virtuele netwerk toevoegen** te selecteren. Volg daarna de aanwijzingen.

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken 

U kunt als volgt Key Vault-firewalls en virtuele netwerken configureren met behulp van Azure CLI

1. [Installeer Azure CLI](/cli/azure/install-azure-cli) en [meld u aan](/cli/azure/authenticate-azure-cli).

2. Bekijk de beschikbare regels voor virtuele netwerken. Als u geen regels voor deze sleutelkluis hebt ingesteld, is de lijst leeg.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. Schakel een service-eindpunt in voor Key Vault op een bestaand virtueel netwerk en subnet.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. Voeg een netwerkregel toe voor een virtueel netwerk en subnet.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. Voeg een IP-adresbereik toe waaruit verkeer kan worden toegestaan.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. Als deze sleutelkluis toegankelijk moet zijn voor vertrouwde services, stelt u `bypass` in op `AzureServices`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. Schakel de netwerkregels in door de standaardactie in te stellen op `Deny`.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Azure PowerShell gebruiken

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

U kunt als volgt Key Vault-firewalls en virtuele netwerken configureren met behulp van PowerShell:

1. Installeer de nieuwste versie van [Azure PowerShell](/powershell/azure/install-az-ps) en [meld u aan](/powershell/azure/authenticate-azureps).

2. Bekijk de beschikbare regels voor virtuele netwerken. Als u geen regels voor deze sleutelkluis hebt ingesteld, is de lijst leeg.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. Schakel een service-eindpunt in voor Key Vault op een bestaand virtueel netwerk en subnet.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. Voeg een netwerkregel toe voor een virtueel netwerk en subnet.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. Voeg een IP-adresbereik toe waaruit verkeer kan worden toegestaan.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. Als deze sleutelkluis toegankelijk moet zijn voor vertrouwde services, stelt u `bypass` in op `AzureServices`.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. Schakel de netwerkregels in door de standaardactie in te stellen op `Deny`.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>Verwijzingen
* ARM-sjabloonreferentie: [ARM-sjabloonreferentie voor Azure Key Vault](/azure/templates/Microsoft.KeyVault/vaults)
* Azure CLI-opdrachten: [az keyvault network-rule](/cli/azure/keyvault/network-rule)
* Azure PowerShell-cmdlets: [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>Volgende stappen

* [Service-eindpunten voor virtuele netwerken voor Key Vault](overview-vnet-service-endpoints.md)
* [Overzicht van Azure Key Vault-beveiliging](security-overview.md)