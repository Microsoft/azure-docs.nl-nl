---
title: Persoonlijke IP-adresbereiken van Azure Firewall SNAT
description: U kunt IP-adresbereiken voor SNAT configureren.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/16/2020
ms.author: victorh
ms.openlocfilehash: 858343b6c5081b52d9e93909f9d52eaccd88a584
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660267"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Persoonlijke IP-adresbereiken van Azure Firewall SNAT

Azure Firewall biedt automatische SNAT voor al het uitgaande verkeer naar open bare IP-adressen. Standaard wordt Azure Firewall niet met netwerk regels gesnat wanneer het doel-IP-adres zich in een privé-IP-adres bereik per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918)bevindt. Toepassings regels worden altijd toegepast met behulp van een [transparante proxy](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) , ongeacht het doel-IP-adres.

Deze logica werkt goed wanneer u verkeer rechtstreeks op Internet rondstuurt. Als u [geforceerde tunneling](forced-tunneling.md)hebt ingeschakeld, wordt het Internet-gebonden verkeer echter omgezet naar een van de privé-IP-adressen van de firewall in AzureFirewallSubnet, waarbij u de bron van uw on-premises firewall kunt verbergen.

Als uw organisatie gebruikmaakt van een openbaar IP-adres bereik voor particuliere netwerken, Azure Firewall SNATs het verkeer naar een van de privé-IP-adressen van de firewall in AzureFirewallSubnet. U kunt Azure Firewall echter zodanig configureren dat uw open bare IP-adres bereik **niet** wordt gesnat. Als u bijvoorbeeld een afzonderlijk IP-adres wilt opgeven, kunt u dit als volgt opgeven: `192.168.1.10` . Als u een bereik van IP-adressen wilt opgeven, kunt u dit als volgt opgeven: `192.168.1.0/24` .

- Als u Azure Firewall wilt configureren op **nooit** -SNAT ongeacht het doel-IP-adres, gebruikt u **0.0.0.0/0** als privé-IP-adres bereik. Met deze configuratie kan Azure Firewall verkeer nooit rechtstreeks naar Internet routeren. 

- Als u de firewall wilt configureren op **altijd** SNAT, ongeacht het doel adres, gebruikt u **255.255.255.255/32** als uw privé-IP-adres bereik.

> [!IMPORTANT]
> Als u uw eigen persoonlijke IP-adresbereiken wilt opgeven en de standaard IANA RFC 1918-adresbereiken wilt behouden, moet u ervoor zorgen dat uw aangepaste lijst nog steeds het IANA RFC 1918-bereik bevat. 

## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>Persoonlijke IP-adresbereiken voor het SNAT configureren-Azure PowerShell

U kunt Azure PowerShell gebruiken om particuliere IP-adresbereiken op te geven voor de firewall.

### <a name="new-firewall"></a>Nieuwe firewall

Voor een nieuwe firewall is de Azure PowerShell opdracht:

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> IANAPrivateRanges wordt uitgebreid naar de huidige standaard waarden op Azure Firewall terwijl de andere bereiken hieraan worden toegevoegd. Als u de IANAPrivateRanges standaard wilt behouden in uw specificatie van het persoonlijk bereik, moet deze in uw specificatie blijven, `PrivateRange` zoals wordt weer gegeven in de volgende voor beelden.

Zie [New-AzFirewall](/powershell/module/az.network/new-azfirewall?view=azps-3.3.0)voor meer informatie.

### <a name="existing-firewall"></a>Bestaande firewall

Als u een bestaande firewall wilt configureren, gebruikt u de volgende Azure PowerShell-opdrachten:

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Sjablonen

U kunt het volgende toevoegen aan de `additionalProperties` sectie:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>Persoonlijke IP-adresbereiken voor het SNAT configureren-Azure Portal

U kunt de Azure Portal gebruiken om persoonlijke IP-adresbereiken op te geven voor de firewall.

1. Selecteer de resource groep en selecteer vervolgens uw firewall.
2. Selecteer op **Overview** de pagina overzicht **persoonlijke IP-adresbereiken** de standaard waarde **IANA RFC 1918**.

   De pagina **persoonlijke IP-voor Voegsels bewerken** wordt geopend:

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="Privé-IP-voor voegsels bewerken":::

1. **IANAPrivateRanges** is standaard geconfigureerd.
2. Bewerk de privé-IP-adresbereiken voor uw omgeving en selecteer vervolgens **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure firewall geforceerde tunneling](forced-tunneling.md).