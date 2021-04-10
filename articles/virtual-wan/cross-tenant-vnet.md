---
title: 'Cross-Tenant VNets verbinden met een hub: Power shell'
titleSuffix: Azure Virtual WAN
description: Dit artikel helpt u bij het verbinden van cross-Tenant VNets met een virtuele hub met behulp van Power shell.
services: virtual-wan
author: wtnlee
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/28/2020
ms.author: wellee
ms.openlocfilehash: 82a5e90221a77b891df78984c7fddfd63b6532aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585414"
---
# <a name="connect-cross-tenant-vnets-to-a-virtual-wan-hub"></a>Cross-Tenant VNets verbinden met een virtuele WAN-hub

Dit artikel helpt u bij het gebruik van Virtual WAN om een VNet te verbinden met een virtuele hub in een andere Tenant. Deze architectuur is handig als u client werkbelastingen hebt die moeten zijn verbonden met hetzelfde netwerk, maar zich op verschillende tenants bevinden. Zoals in het volgende diagram wordt weer gegeven, kunt u bijvoorbeeld een niet-contoso VNet (de externe Tenant) verbinden met een virtuele contoso-hub (de bovenliggende Tenant).

:::image type="content" source="./media/cross-tenant-vnet/connectivity.png" alt-text="Routerings configuratie instellen" :::

In dit artikel leert u het volgende:

* Voeg nog een Tenant toe als Inzender voor uw Azure-abonnement.
* Verbind een multi tenant-VNet met een virtuele hub.

De stappen voor deze configuratie worden uitgevoerd met een combi natie van Azure Portal en Power shell. De functie zelf is echter alleen beschikbaar in Power shell en CLI.

## <a name="before-you-begin"></a>Voordat u begint

### <a name="prerequisites"></a>Vereisten

Als u de stappen in dit artikel wilt gebruiken, moet u de volgende configuratie al hebben ingesteld in uw omgeving:

* Een virtuele WAN en virtuele hub in uw bovenliggende abonnement.
* Een virtueel netwerk dat is geconfigureerd in een abonnement in een andere (externe) Tenant.
* Zorg ervoor dat de VNet-adres ruimte in de externe Tenant niet overlapt met een andere adres ruimte binnen andere VNets die al zijn verbonden met de bovenliggende virtuele hub.

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="assign-permissions"></a><a name="rights"></a>Machtigingen toewijzen

Om het bovenliggende abonnement met de virtuele hub te wijzigen en toegang te krijgen tot de virtuele netwerken in de externe Tenant, moet u **Inzender** machtigingen toewijzen aan uw bovenliggende abonnement vanuit het externe Tenant abonnement.

1. Voeg de rol **Inzender** toe aan het bovenliggende account (de toewijzing met de virtuele WAN-hub). U kunt Power shell of de Azure Portal gebruiken om deze rol toe te wijzen. Zie de volgende artikelen voor het **toevoegen of verwijderen van roltoewijzingen** voor stappen:

   * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
   * [Portal](../role-based-access-control/role-assignments-portal.md)

1. Voeg vervolgens het externe Tenant abonnement en het bovenliggende Tenant abonnement toe aan de huidige sessie van Power shell. Voer de volgende opdracht uit. Als u bent aangemeld bij het bovenliggende item, hoeft u alleen de opdracht voor de externe Tenant uit te voeren.

   ```azurepowershell-interactive
   Add-AzAccount -SubscriptionId "xxxxx-b34a-4df9-9451-4402dcaecc5b"
   ```

1. Controleer of de roltoewijzing is geslaagd door u aan te melden bij Azure PowerShell met de bovenliggende referenties en voer de volgende opdracht uit:

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. Als de machtigingen zijn door gegeven aan het bovenliggende item en zijn toegevoegd aan de sessie, wordt het abonnement dat eigendom is van de externe Tenant weer gegeven in de uitvoer van de opdracht.

## <a name="connect-vnet-to-hub"></a><a name="connect"></a>VNet verbinden met hub

In de volgende stappen schakelt u tussen de context van de twee abonnementen wanneer u het virtuele netwerk koppelt aan de virtuele hub. Vervang de voorbeeld waarden zodat deze overeenkomen met uw eigen omgeving.

1. Zorg ervoor dat u zich in de context van uw externe account bevindt door de volgende opdracht uit te voeren:

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[remote ID]"
   ```

1. Maak een lokale variabele voor het opslaan van de meta gegevens van het virtuele netwerk waarmee u verbinding wilt maken met de hub.

   ```azurepowershell-interactive
   $remote = Get-AzVirtualNetwork -Name "[vnet name]" -ResourceGroupName "[resource group name]"
   ```

1. Schakel terug naar het bovenliggende account.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[parent ID]"
   ```

1. Verbind het VNet met de hub.

   ```azurepowershell-interactive
   New-AzVirtualHubVnetConnection -ResourceGroupName "[parent resource group name]" -VirtualHubName "[virtual hub name]" -Name "[name of connection]" -RemoteVirtualNetwork $remote
   ```

1. U kunt de nieuwe verbinding weer geven in Power shell of in de Azure Portal.

   * **Power shell:** De meta gegevens van de zojuist opgemaakte verbinding worden weer gegeven in de Power shell-console als de verbinding is gemaakt.
   * **Azure portal:** Ga naar de virtuele hub, **connectiviteit-> Virtual Network verbindingen**. U kunt de verwijzing naar de verbinding weer geven. Als u de werkelijke resource wilt zien, hebt u de juiste machtigingen nodig.
   
## <a name="troubleshooting"></a><a name="troubleshoot"></a>Problemen oplossen

* Controleer of de meta gegevens in $remote (uit de voor gaande [sectie](#connect)) overeenkomen met de gegevens van de Azure Portal.
* U kunt machtigingen controleren met behulp van de IAM-instellingen van de resource groep voor externe tenants of met behulp van Azure PowerShell opdrachten (Get-AzSubscription).
* Zorg ervoor dat de aanhalings tekens zijn opgenomen rond de namen van resource groepen of andere specifieke omgevings variabelen (bijvoorbeeld "VirtualHub1" of "VirtualNetwork1").

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN:

* [Veelgestelde vragen over](virtual-wan-faq.md) virtuele WAN
