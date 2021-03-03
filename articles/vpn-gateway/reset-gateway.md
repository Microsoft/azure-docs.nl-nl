---
title: VPN-gateway of-verbinding opnieuw instellen om IPsec-tunnel opnieuw in te stellen
titleSuffix: Azure VPN Gateway
description: Een verbinding of een VPN-gateway opnieuw instellen om IPsec-tunnels opnieuw tot stand te brengen.
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/22/2021
ms.author: cherylmc
ms.openlocfilehash: adc2ffd63d73baaddce00324787df61061ea69dc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726633"
---
# <a name="reset-a-vpn-gateway-or-a-connection"></a>Een VPN-gateway of een verbinding opnieuw instellen

Het opnieuw instellen van een Azure VPN-gateway of gateway verbinding is handig als u cross-premises VPN-connectiviteit verliest op een of meer site-naar-site-VPN-tunnels. In een dergelijke situatie functioneren al uw on-premises VPN-apparaten naar behoren, maar kunnen ze geen IPSec-tunnels tot stand brengen met de Azure VPN-gateways. Dit artikel helpt u bij het opnieuw instellen van een VPN-gateway of gateway verbinding.

## <a name="what-happens-during-a-reset"></a>Wat er gebeurt tijdens het opnieuw instellen

### <a name="gateway-reset"></a>Gateway opnieuw instellen

Een VPN-gateway bestaat uit twee VM-exemplaren die worden uitgevoerd in een configuratie met de modus actief/stand-by. Wanneer u de gateway opnieuw instelt, wordt de gateway opnieuw opgestart. vervolgens worden de cross-premises configuraties opnieuw toegepast. De gateway behoudt hetzelfde openbare IP-adres. Dat betekent dat u de VPN-routerconfiguratie niet hoeft bij te werken met een nieuw openbaar IP-adres voor de Azure VPN-gateway.

Wanneer u de opdracht geeft om de gateway opnieuw in te stellen, wordt de huidige actieve instantie van de Azure VPN-gateway onmiddellijk opnieuw opgestart. Tijdens de failover van het actieve exemplaar (dat opnieuw wordt opgestart) is er een korte ruimte beschikbaar voor de stand-by-instantie. Deze onderbreking zou niet langer dan een minuut moeten duren.

Als de verbinding na de eerste keer opnieuw opstarten niet is hersteld, voert u deze opdracht opnieuw uit om de tweede VM-instantie (de nieuwe actieve gateway) opnieuw op te starten. Als de twee keer opnieuw opstarten direct na elkaar wordt aangevraagd, duurt het iets langer omdat beide VM-exemplaren (actief en stand-by) opnieuw worden opgestart. Dit leidt tot een langere ruimte op de VPN-verbinding, tot 30 tot 45 minuten voor Vm's om het opnieuw opstarten te volt ooien.

Nadat twee keer opnieuw is opgestart, kunt u, als u nog steeds problemen hebt met cross-premises connectiviteit, een ondersteunings aanvraag openen via de Azure Portal.

### <a name="connection-reset"></a>Verbinding opnieuw instellen

Wanneer u selecteert om een verbinding opnieuw in te stellen, wordt de gateway niet opnieuw opgestart. Alleen de geselecteerde verbinding wordt opnieuw ingesteld en teruggezet.

## <a name="reset-a-connection"></a>Een verbinding opnieuw instellen

U kunt een verbinding eenvoudig opnieuw instellen met behulp van de Azure Portal.

1. Ga naar de **verbinding** die u wilt herstellen. U kunt de verbindings bron vinden door deze te zoeken in **alle resources** of door te navigeren naar de **' gateway naam '-> verbindingen-> ' verbindings naam '**
1. Selecteer op de pagina **verbinding** de optie **opnieuw instellen** in het menu links.
1. Klik op de pagina **opnieuw instellen** op **opnieuw instellen** om de verbinding opnieuw in te stellen.

   :::image type="content" source="./media/reset-gateway/reset-connection.png" alt-text="Scherm opname met reset.":::

## <a name="reset-a-vpn-gateway"></a>Een VPN-gateway opnieuw instellen

Controleer voordat u de gateway opnieuw instelt de hieronder vermelde belangrijke zaken voor elke S2S-VPN-tunnel (site-naar-site) met IPsec. Als items niet overeenkomen, wordt de verbinding met S2SVPN-tunnels verbroken. Door de configuraties voor uw on-premises en Azure VPN-gateways te controleren en te corrigeren, bespaart u onnodig opnieuw opstarten en onderbrekingen voor de andere werkende verbindingen op de gateways.

Controleer de volgende items voordat u de gateway opnieuw instelt:

* Controleer of de internet-IP-adressen (VIP's) voor zowel de Azure VPN-gateway als de on-premises VPN-gateway correct zijn geconfigureerd in zowel het Azure- als het lokale VPN-beleid.
* De vooraf gedeelde sleutel moet hetzelfde zijn in de Azure- en de on-premises VPN-gateway.
* Als u een specifieke IPsec/IKE-configuratie, zoals versleuteling, hash-algoritmen en PFS (Perfect Forward Secrecy) toepast, moet u controleren of de Azure- en on-premises VPN-gateway dezelfde configuratie hebben.

### <a name="azure-portal"></a><a name="portal"></a>Azure Portal

U kunt een resource manager VPN-gateway opnieuw instellen met behulp van de Azure Portal. Als u een klassieke Gateway opnieuw wilt instellen, raadpleegt u de Power shell-stappen voor het [klassieke implementatie model](#resetclassic).

[!INCLUDE [portal steps](../../includes/vpn-gateway-reset-gw-portal-include.md)]

### <a name="powershell"></a><a name="ps"></a>Zo

#### <a name="resource-manager-deployment-model"></a>Resource Manager-implementatiemodel

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

De cmdlet voor het opnieuw instellen van een gateway is **Reset-AzVirtualNetworkGateway**. Zorg ervoor dat u beschikt over de nieuwste versie van de [Power shell AZ-cmdlets](/powershell/module/az.network)voordat u de herstel bewerking uitvoert. In het volgende voor beeld wordt de gateway van een virtueel netwerk met de naam VNet1GW in de resource groep TestRG1 opnieuw ingesteld:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Resultaat:

Wanneer u een retour resultaat ontvangt, kunt u aannemen dat de gateway opnieuw is ingesteld. Er is echter niets in het retour resultaat dat aangeeft dat het opnieuw instellen is geslaagd. Als u de geschiedenis nauw keurig wilt zien wanneer de gateway opnieuw is ingesteld, kunt u deze informatie bekijken in de [Azure Portal](https://portal.azure.com). Navigeer in de portal naar **' gatewaynaam '-> resource Health**.

#### <a name="classic-deployment-model"></a><a name="resetclassic"></a>Klassiek implementatiemodel

De cmdlet voor het opnieuw instellen van een gateway is **Reset-azurevnetgateway gebruikt**. De Azure PowerShell-cmdlets voor service management moeten lokaal op uw bureau blad zijn geïnstalleerd. U kunt Azure Cloud Shell niet gebruiken. Zorg ervoor dat u beschikt over de nieuwste versie van de [Power shell-cmdlets voor Service Management (SM)](/powershell/azure/servicemanagement/install-azure-ps#azure-service-management-cmdlets)voordat u de herstel bewerking uitvoert. Wanneer u deze opdracht gebruikt, moet u ervoor zorgen dat u de volledige naam van het virtuele netwerk gebruikt. Klassieke VNets die zijn gemaakt met behulp van de portal hebben een lange naam die vereist is voor Power shell. U kunt de lange naam weer geven met behulp van Get-AzureVNetConfig-ExportToFile C:\Myfoldername\NetworkConfig.xml.

In het volgende voor beeld wordt de gateway opnieuw ingesteld voor een virtueel netwerk met de naam ' Group TestRG1 TestVNet1 ' (die in de portal wordt weer gegeven als gewoon ' TestVNet1 '):

```powershell
Reset-AzureVNetGateway –VnetName 'Group TestRG1 TestVNet1'
```

Resultaat:

```powershell
Error          :
HttpStatusCode : OK
Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
Status         : Successful
RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
StatusCode     : OK
```

### <a name="azure-cli"></a><a name="cli"></a>Azure CLI

Als u de gateway opnieuw wilt instellen, gebruikt u de opdracht [AZ Network vnet-gateway reset](/cli/azure/network/vnet-gateway) . In het volgende voor beeld wordt de gateway van een virtueel netwerk met de naam VNet5GW in de resource groep TestRG5 opnieuw ingesteld:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Resultaat:

Wanneer u een retour resultaat ontvangt, kunt u aannemen dat de gateway opnieuw is ingesteld. Er is echter niets in het retour resultaat dat aangeeft dat het opnieuw instellen is geslaagd. Als u de geschiedenis nauw keurig wilt zien wanneer de gateway opnieuw is ingesteld, kunt u deze informatie bekijken in de [Azure Portal](https://portal.azure.com). Navigeer in de portal naar **' gatewaynaam '-> resource Health**.
