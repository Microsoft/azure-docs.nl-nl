---
title: 'Zelfstudie: Azure Virtual WAN gebruiken om een punt-naar-site-verbinding te maken met Azure'
description: In deze zelfstudie leert u hoe u Azure Virtual WAN kunt gebruiken om een punt-naar-site-VPN-verbinding te maken met Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 02/22/2021
ms.author: cherylmc
ms.openlocfilehash: 9d207e2ee0ddff49ab01094626b9af1c8505cb4e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732480"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Zelfstudie: Een VPN-verbinding voor gebruikers maken met Azure Virtual WAN

In deze zelfstudie leert u hoe u Virtual WAN kunt gebruiken om verbinding te maken met uw resources in Azure via een VPN-verbinding met IPsec/IKE (IKEv2)- of OpenVPN. Voor dit type verbinding moet de VPN-client op de clientcomputer zijn geconfigureerd. Zie voor meer informatie over Virtual WAN het [Overzicht van Virtual WAN](virtual-wan-about.md).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een virtueel WAN maken
> * Een P2S-configuratie maken
> * Een virtuele hub maken
> * Client adres groepen kiezen
> * DNS-servers opgeven
> * Het configuratiepakket voor het VPN-clientprofiel genereren
> * VPN-clients configureren
> * Uw virtuele WAN weergeven

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Een virtueel WAN maken

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Een P2S-configuratie maken

Een punt-naar-site-configuratie (P2S) definieert de parameters om verbinding te maken met externe clients.

[!INCLUDE [Create P2S configuration](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-virtual-hub-and-gateway"></a><a name="hub"></a>Virtuele hub en gateway maken

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="choose-p2s-client-address-pools"></a><a name="chooseclientpools"></a> Kies P2S-client adres groepen

[!INCLUDE [Choose pools](../../includes/virtual-wan-allocating-p2s-pools.md)]

## <a name="specify-dns-server"></a><a name="dns"></a>DNS-servers opgeven

U kunt deze instelling configureren tijdens het maken van de hub of deze wijzigen op een later tijdstip. Zoek de virtuele hub om deze te wijzigen. Selecteer onder **Gebruikers-VPN (punt-naar-site)** **configureren** en voer het/de IP-adress(sen) van de DNS-server in het/de tekstvak(ken) **Aangepaste DNS-servers** in. U kunt maximaal 5 DNS-servers opgeven.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="Aangepaste DNS" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="generate-vpn-client-profile-package"></a><a name="download"></a>Het pakket voor het VPN-clientprofiel genereren

Genereer en download het pakket voor het VPN-clientprofiel om uw VPN-clients te configureren.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="configure-client"></a>VPN-clients configureren

Gebruik het gedownloade profielpakket om de VPN-clients voor externe toegang te configureren. De procedure is anders voor elk besturingssysteem. Volg de instructies die van toepassing zijn op uw systeem.
Zodra u klaar bent met het configureren van uw client, kunt u verbinding maken.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Uw virtuele WAN weergeven

1. Navigeer naar uw virtuele WAN.
1. Op de pagina **Overzicht** vertegenwoordigt elk punt op de kaart een hub.
1. In de sectie **Hubs en verbindingen** vindt u informatie over de hubstatus, site, regio, VPN-verbindingsstatus en verzonden en ontvangen bytes.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Resources opschonen

U kunt de opdracht [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, wanneer u deze niet meer nodig hebt. Vervangen 'myResourceGroup' door de naam van uw resourcegroep en voer de volgende PowerShell-opdracht uit:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN:

> [!div class="nextstepaction"]
> * [Veelgestelde vragen over Virtual WAN](virtual-wan-faq.md)
