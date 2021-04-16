---
title: Azure Firewall privé-IP-adresbereiken van SNAT
description: U kunt IP-adresbereiken configureren voor SNAT.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 04/14/2021
ms.author: victorh
ms.openlocfilehash: 91d4d631376c03b668128936f3840ce1119f9b6f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482739"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure Firewall privé-IP-adresbereiken van SNAT

Azure Firewall biedt automatische SNAT voor al het uitgaande verkeer naar openbare IP-adressen. Standaard wordt Azure Firewall SNAT niet met netwerkregels wanneer het doel-IP-adres zich in een privé-IP-adresbereik per [IANA RFC 1918.](https://tools.ietf.org/html/rfc1918) Toepassingsregels worden altijd toegepast met behulp van een [transparante proxy,](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) ongeacht het doel-IP-adres.

Deze logica werkt goed wanneer u verkeer rechtstreeks naar internet routeert. Als u echter geforceerd [tunnelen](forced-tunneling.md)hebt ingeschakeld, wordt internetverkeer via SNAT naar een van de privé-IP-adressen van de firewall in AzureFirewallSubnet geforceerd, waardoor de bron voor uw on-premises firewall wordt verborgen.

Als uw organisatie een openbaar IP-adresbereik voor particuliere netwerken gebruikt, Azure Firewall SNATs het verkeer naar een van de privé-IP-adressen van de firewall in AzureFirewallSubnet. U kunt echter configureren Azure Firewall uw **openbare** IP-adresbereik niet via SNAT te configureren. Als u bijvoorbeeld een afzonderlijk IP-adres wilt opgeven, kunt u dit als het volgende opgeven: `192.168.1.10` . Als u een bereik van IP-adressen wilt opgeven, kunt u dit als het volgende opgeven: `192.168.1.0/24` .

- Azure Firewall Gebruik **0.0.0.0.0/0** als uw privé-IP-adresbereik om **SNAT** nooit te configureren, ongeacht het doel-IP-adres. Met deze configuratie kunnen Azure Firewall verkeer nooit rechtstreeks naar internet omgeleid. 

- Gebruik **255.255.255.255/32** als uw privé-IP-adresbereik om de firewall altijd te configureren voor  SNAT, ongeacht het doeladres.

> [!IMPORTANT]
> Het privéadresbereik dat u opgeeft, is alleen van toepassing op netwerkregels. Op dit moment worden toepassingsregels altijd SNAT gebruikt.

> [!IMPORTANT]
> Als u uw eigen privé-IP-adresbereiken wilt opgeven en de standaard IANA RFC 1918-adresbereiken wilt behouden, moet u ervoor zorgen dat uw aangepaste lijst nog steeds het IANA RFC 1918-bereik bevat. 

U kunt de privé-IP-adressen van SNAT configureren met behulp van de volgende methoden. U moet de privé-SNAT-adressen configureren met behulp van de methode die geschikt is voor uw configuratie. Firewalls die zijn gekoppeld aan een firewallbeleid, moeten het bereik in het beleid opgeven en niet `AdditionalProperties` gebruiken.


|Methode            |Klassieke regels gebruiken  |Firewallbeleid gebruiken  |
|---------|---------|---------|
|Azure Portal     | [Ondersteund](#classic-rules-3)| [Ondersteund](#firewall-policy-1)|
|Azure PowerShell     |[Configureren `PrivateRange`](#classic-rules)|momenteel niet ondersteund|
|Azure CLI|[Configureren `--private-ranges`](#classic-rules-1)|momenteel niet ondersteund|
|ARM-sjabloon     |[configureren `AdditionalProperties` in firewall-eigenschap](#classic-rules-2)|[configureren `snat/privateRanges` in firewallbeleid](#firewall-policy)|


## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>Privé-IP-adresbereiken voor SNAT configureren - Azure PowerShell
### <a name="classic-rules"></a>Klassieke regels

U kunt deze Azure PowerShell privé-IP-adresbereiken voor de firewall op te geven.

> [!NOTE]
> De `PrivateRange` firewall-eigenschap wordt genegeerd voor firewalls die zijn gekoppeld aan een firewallbeleid. U moet de eigenschap `SNAT` in gebruiken zoals beschreven in Privé-IP-adresbereiken voor SNAT configureren `firewallPolicies` - [ARM-sjabloon.](#firewall-policy)

#### <a name="new-firewall"></a>Nieuwe firewall

Voor een nieuwe firewall met klassieke regels is de Azure PowerShell cmdlet:

```azurepowershell
$azFw = @{
    Name               = '<fw-name>'
    ResourceGroupName  = '<resourcegroup-name>'
    Location           = '<location>'
    VirtualNetworkName = '<vnet-name>'
    PublicIpName       = '<public-ip-name>'
    PrivateRange       = @("IANAPrivateRanges", "192.168.1.0/24", "192.168.1.10")
}

New-AzFirewall @azFw
```
> [!NOTE]
> Voor het implementeren Azure Firewall met `New-AzFirewall` behulp van is een bestaand VNet en een openbaar IP-adres vereist. Zie [Implementeren en configureren Azure Firewall met Azure PowerShell](deploy-ps.md) voor een volledige implementatiehandleiding.

> [!NOTE]
> IANAPrivateRanges wordt uitgebreid naar de huidige standaardinstellingen op Azure Firewall terwijl de andere reeksen eraan worden toegevoegd. Als u de standaardwaarde IANAPrivateRanges in uw persoonlijke bereikspecificatie wilt houden, moet deze in uw specificatie blijven zoals `PrivateRange` wordt weergegeven in de volgende voorbeelden.

Zie [New-AzFirewall](/powershell/module/az.network/new-azfirewall)voor meer informatie.

#### <a name="existing-firewall"></a>Bestaande firewall

Als u een bestaande firewall wilt configureren met behulp van klassieke regels, gebruikt u de Azure PowerShell cmdlets:

```azurepowershell
$azfw = Get-AzFirewall -Name '<fw-name>' -ResourceGroupName '<resourcegroup-name>'
$azfw.PrivateRange = @("IANAPrivateRanges","192.168.1.0/24", "192.168.1.10")
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="configure-snat-private-ip-address-ranges---azure-cli"></a>Privé-IP-adresbereiken voor SNAT configureren - Azure CLI
### <a name="classic-rules"></a>Klassieke regels

U kunt Azure CLI gebruiken om privé-IP-adresbereiken voor de firewall op te geven met behulp van klassieke regels. 

#### <a name="new-firewall"></a>Nieuwe firewall

Voor een nieuwe firewall die gebruik maakt van klassieke regels, is de Azure CLI-opdracht:

```azurecli-interactive
az network firewall create \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

> [!NOTE]
> Voor het implementeren Azure Firewall azure CLI-opdracht zijn aanvullende configuratiestappen vereist om openbare `az network firewall create` IP-adressen en IP-configuratie te maken. Zie [Implementeren en configureren Azure Firewall azure CLI](deploy-cli.md) voor een volledige implementatiehandleiding.

> [!NOTE]
> IANAPrivateRanges wordt uitgebreid naar de huidige standaardinstellingen op Azure Firewall terwijl de andere reeksen eraan worden toegevoegd. Als u de standaardwaarde IANAPrivateRanges in uw persoonlijke bereikspecificatie wilt houden, moet deze in uw specificatie blijven zoals `private-ranges` wordt weergegeven in de volgende voorbeelden.

#### <a name="existing-firewall"></a>Bestaande firewall

De Azure CLI-opdracht is als u een bestaande firewall wilt configureren met behulp van klassieke regels:

```azurecli-interactive
az network firewall update \
-n <fw-name> \
-g <resourcegroup-name> \
--private-ranges 192.168.1.0/24 192.168.1.10 IANAPrivateRanges
```

## <a name="configure-snat-private-ip-address-ranges---arm-template"></a>Privé-IP-adresbereiken voor SNAT configureren - ARM-sjabloon
### <a name="classic-rules"></a>Klassieke regels

Als u SNAT wilt configureren tijdens Sjabloonimlementatie ARM-configuratie, kunt u het volgende toevoegen aan de `additionalProperties` eigenschap :

```json
"additionalProperties": {
   "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
},
```
### <a name="firewall-policy"></a>Firewallbeleid

Azure Firewalls die zijn gekoppeld aan een firewallbeleid, ondersteunen SNAT-privébereiken sinds de API-versie 2020-11-01. Op dit moment kunt u een sjabloon gebruiken om het privébereik van SNAT in het firewallbeleid bij te werken. In het volgende voorbeeld wordt de firewall geconfigureerd voor **altijd** SNAT-netwerkverkeer:

```json
{ 

            "type": "Microsoft.Network/firewallPolicies", 
            "apiVersion": "2020-11-01", 
            "name": "[parameters('firewallPolicies_DatabasePolicy_name')]", 
            "location": "eastus", 
            "properties": { 
                "sku": { 
                    "tier": "Standard" 
                }, 
                "snat": { 
                    "privateRanges": [255.255.255.255/32] 
                } 
            } 
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>Privé-IP-adresbereiken voor SNAT configureren - Azure Portal
### <a name="classic-rules"></a>Klassieke regels

U kunt de Azure Portal privé-IP-adresbereiken voor de firewall opgeven.

1. Selecteer uw resourcegroep en selecteer vervolgens uw firewall.
2. Selecteer op **de** pagina Overzicht, **Privé-IP-adresbereiken,** de standaardwaarde **IANA RFC 1918.**

   De **pagina Privé IP-voorvoegsels bewerken** wordt geopend:

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="Privé-IP-voorvoegsels bewerken":::

1. Standaard is **IANAPrivateRanges** geconfigureerd.
2. Bewerk de privé-IP-adresbereiken voor uw omgeving en selecteer **vervolgens Opslaan.**

### <a name="firewall-policy"></a>Firewallbeleid

1.  Selecteer uw resourcegroep en selecteer vervolgens uw firewallbeleid.
2.  Selecteer **Privé-IP-adresbereiken (SNAT)** in de **kolom** Instellingen.

    Standaard is **Het standaardgedrag Azure Firewall SNAT-beleid** geselecteerd. 
3. Als u de SNAT-configuratie wilt aanpassen, schakel het selectievakje uit en selecteert u onder **SNAT** uitvoeren de voorwaarden voor het uitvoeren van SNAT voor uw omgeving.
      :::image type="content" source="media/snat-private-range/private-ip-ranges-snat.png" alt-text="Privé-IP-adresbereiken (SNAT)":::


4.   Selecteer **Toepassen**.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie [Azure Firewall geforceerd tunnelen.](forced-tunneling.md)