---
title: Configuratie scripts voor VPN-apparaten voor S2S VPN-verbindingen downloaden
description: In dit artikel wordt stapsgewijs uitgelegd hoe u configuratie scripts voor VPN-apparaten voor S2S VPN-verbindingen met Azure VPN-gateways kunt downloaden met behulp van Azure Resource Manager.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: d2593c656e17310c5afb4a897b94cc8c12a618f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879491"
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>Configuratie scripts voor VPN-apparaten voor S2S VPN-verbindingen downloaden

In dit artikel wordt stapsgewijs uitgelegd hoe u configuratie scripts voor VPN-apparaten voor S2S VPN-verbindingen met Azure VPN-gateways kunt downloaden met behulp van Azure Resource Manager. In het volgende diagram ziet u de werk stroom op hoog niveau.

![downloaden-script](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

De volgende apparaten hebben scripts die beschikbaar zijn:

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about-vpn-device-configuration-scripts"></a><a name="about"></a>Over configuratie scripts voor VPN-apparaten

Een cross-premises VPN-verbinding bestaat uit een Azure VPN-gateway, een on-premises VPN-apparaat en een IPsec S2S VPN-tunnel die de twee verbindt. De typische werk stroom omvat de volgende stappen:

1. Een Azure VPN-gateway maken en configureren (virtuele netwerk gateway)
2. Een lokale Azure-netwerk gateway maken en configureren die uw on-premises netwerk en VPN-apparaat vertegenwoordigt
3. Een Azure VPN-verbinding tussen de Azure VPN-gateway en de lokale netwerk gateway maken en configureren
4. Configureer het on-premises VPN-apparaat dat wordt weer gegeven door de lokale netwerk gateway om de daad werkelijke S2S VPN-tunnel met de Azure VPN-gateway in te stellen

U kunt de stappen 1 tot en met 3 volt ooien met behulp van Azure [Portal](./tutorial-site-to-site-portal.md), [Power shell](vpn-gateway-create-site-to-site-rm-powershell.md)of [cli](vpn-gateway-howto-site-to-site-resource-manager-cli.md). De laatste stap omvat het configureren van de on-premises VPN-apparaten buiten Azure. Met deze functie kunt u een configuratie script voor uw VPN-apparaat downloaden met de overeenkomstige waarden van de Azure VPN-gateway, het virtuele netwerk en on-premises netwerk adres voorvoegsels en VPN-verbindings eigenschappen, enzovoort. U kunt het script als uitgangs punt gebruiken of het script rechtstreeks Toep assen op uw on-premises VPN-apparaten via de configuratie console.

> [!IMPORTANT]
> * De syntaxis voor elk configuratie script voor VPN-apparaten wijkt af en is sterk afhankelijk van de modellen en firmware versies. U kunt het model van uw apparaat en de versie gegevens van de beschik bare sjablonen best Eden.
> * Sommige parameter waarden moeten uniek zijn op het apparaat en kunnen niet worden bepaald zonder toegang tot het apparaat. De door Azure gegenereerde configuratie scripts vullen deze waarden vooraf in, maar u moet ervoor zorgen dat de gegeven waarden geldig zijn op het apparaat. Voorbeelden:
>    * Interface nummers
>    * Toegangs beheer lijst nummers
>    * Beleids namen of-nummers, enzovoort.
> * Zoek naar het tref woord '**replace**', Inge sloten in het script om de para meters te vinden die u moet controleren voordat u het script toepast.
> * Sommige sjablonen bevatten een sectie '**opschonen**' die u kunt Toep assen om de configuraties te verwijderen. De opschoon secties worden standaard in een opmerkingen opgenomen.

## <a name="download-the-configuration-script-from-azure-portal"></a>Down load het configuratie script van Azure Portal

Maak een Azure VPN-gateway, een lokale netwerk gateway en een verbindings bron die de twee verbindt. Op de volgende pagina vindt u instructies voor het uitvoeren van de stappen:

* [Een site-naar-site-verbinding maken in Azure Portal](./tutorial-site-to-site-portal.md)

Zodra de verbindings bron is gemaakt, volgt u de onderstaande instructies om de configuratie scripts voor VPN-apparaten te downloaden:

1. Ga in een browser naar de [Azure Portal](https://portal.azure.com) en meld u indien nodig aan met uw Azure-account
2. Ga naar de verbindings bron die u hebt gemaakt. U kunt de lijst met alle verbindings resources vinden door te klikken op alle services en vervolgens op ' netwerk ' en ' verbindingen '.

    ![verbinding-lijst](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Klik op de verbinding die u wilt configureren.

    ![verbinding-overzicht](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. Klik op de koppeling ' configuratie downloaden ' zoals gemarkeerd in rood op de overzichts pagina voor verbindingen. Hiermee opent u de pagina configuratie downloaden.

    ![down load-script-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. Selecteer de model familie en firmware versie voor uw VPN-apparaat en klik vervolgens op de knop configuratie downloaden.

    ![download66-script-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. U wordt gevraagd het gedownloade script (een tekst bestand) op te slaan in uw browser.
7. Nadat u het configuratie script hebt gedownload, opent u het met een tekst editor en zoekt u naar het tref woord ' REPLACE ' om de para meters te identificeren en te controleren die mogelijk moeten worden vervangen.

    ![bewerken-script](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Down load het configuratie script met behulp van Azure PowerShell



U kunt het configuratie script ook downloaden met behulp van Azure PowerShell, zoals wordt weer gegeven in het volgende voor beeld:

```azurepowershell-interactive
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration script for the connection
Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="apply-the-configuration-script-to-your-vpn-device"></a>Het configuratie script Toep assen op uw VPN-apparaat

Nadat u het configuratie script hebt gedownload en gevalideerd, is de volgende stap het script op uw VPN-apparaat toe te passen. De daad werkelijke procedure is afhankelijk van het maken en modellen van uw VPN-apparaat. Raadpleeg de bewerkings handleidingen of de instructie pagina's voor uw VPN-apparaten.

## <a name="next-steps"></a>Volgende stappen

Ga door met het configureren van de [site-naar-site-verbinding](./tutorial-site-to-site-portal.md).