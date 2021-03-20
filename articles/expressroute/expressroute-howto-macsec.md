---
title: 'Azure-ExpressRoute: MACsec configureren'
description: Dit artikel helpt u bij het configureren van MACsec om de verbindingen tussen uw Edge-routers en de Edge-routers van micro soft te beveiligen.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 10/22/2019
ms.author: duau
ms.openlocfilehash: 8ee7f42a4b05def7c2239c7a1e5bcef54f6f7e50
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89395771"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>MACsec configureren op ExpressRoute Direct-poorten

Dit artikel helpt u bij het configureren van MACsec om de verbindingen tussen uw Edge-routers en de Edge-routers van micro soft te beveiligen met behulp van Power shell.

## <a name="before-you-begin"></a>Voordat u begint

Bevestig het volgende voordat u begint met de configuratie:

* U begrijpt [ExpressRoute direct Provisioning-werk stromen](expressroute-erdirect-about.md).
* U hebt een [ExpressRoute-directe poort resource](expressroute-howto-erdirect.md)gemaakt.
* Als u Power shell lokaal wilt uitvoeren, controleert u of de meest recente versie van Azure PowerShell op uw computer is geïnstalleerd.

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>Meld u aan en selecteer het juiste abonnement

Als u de configuratie wilt starten, meldt u zich aan bij uw Azure-account en selecteert u het abonnement dat u wilt gebruiken.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. Maak Azure Key Vault, MACsec geheimen en gebruikers identiteit

1. Maak een Key Vault-exemplaar om MACsec geheimen op te slaan in een nieuwe resource groep.

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    Als u al een sleutel kluis of een resource groep hebt, kunt u deze opnieuw gebruiken. Het is echter essentieel dat u de functie voor [ **voorlopig verwijderen**](../key-vault/general/soft-delete-overview.md) in uw bestaande sleutel kluis inschakelt. Als voorlopig verwijderen niet is ingeschakeld, kunt u de volgende opdrachten gebruiken om het in te scha kelen:

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. Een gebruikers-id maken.

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    Als New-AzUserAssignedIdentity niet wordt herkend als een geldige Power shell-cmdlet, installeert u de volgende module (in de beheerders modus) en voert u de bovenstaande opdracht opnieuw uit.

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. Maak een connectiviteits sleutel (CAK) en een connectiviteits sleutel naam (CKN) en sla deze op in de sleutel kluis.

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
4. Wijs de machtiging GET toe aan de gebruikers-id.

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   Deze identiteit kan nu de geheimen ophalen, bijvoorbeeld CAK en CKN, van de sleutel kluis.
5. Stel deze gebruikers-id in die moet worden gebruikt door ExpressRoute.

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. MACsec op ExpressRoute direct-poorten configureren

### <a name="to-enable-macsec"></a>MACsec inschakelen

Elk ExpressRoute direct-exemplaar heeft twee fysieke poorten. U kunt ervoor kiezen om MACsec op beide poorten tegelijk in te scha kelen of MACsec in te scha kelen op één poort tegelijk. Als de service één poort op het moment is (door verkeer naar een actieve poort te scha kelen tijdens het onderhoud van de andere poort), kan de onderbreking worden geminimaliseerd als uw ExpressRoute direct in gebruik is.

1. Stel MACsec geheimen en versleuteling in en koppel de gebruikers identiteit aan de poort zodat de ExpressRoute-beheer code zo nodig toegang heeft tot de MACsec geheimen.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[0]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[1]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.identity = $erIdentity
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```
2. Beschrijving Als de poorten in de status van beheer worden weer geven, kunt u de volgende opdrachten uitvoeren om de poorten weer te geven.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    Op dit punt is MACsec ingeschakeld voor de directe poorten van ExpressRoute aan micro soft. Als u dit nog niet hebt geconfigureerd op uw edge-apparaten, kunt u door gaan met het configureren van de MACsec-geheimen en versleuteling.

### <a name="to-disable-macsec"></a>MACsec uitschakelen

Als MACsec niet meer nodig is voor uw ExpressRoute direct-exemplaar, kunt u de volgende opdrachten uitvoeren om het uit te scha kelen.

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

Op dit punt is MACsec uitgeschakeld op de ExpressRoute direct-poorten aan de kant van micro soft.

### <a name="test-connectivity"></a>Connectiviteit testen
Nadat u MACsec (inclusief MACsec-sleutel update) hebt geconfigureerd op uw ExpressRoute direct-poorten, [controleert](expressroute-troubleshooting-expressroute-overview.md) u of de BGP-sessies van de circuits actief zijn. Als u nog geen circuit op de poorten hebt, moet u er eerst een maken en persoonlijke Azure-peering of micro soft-peering van het circuit instellen. Als MACsec onjuist is geconfigureerd, met inbegrip van de MACsec-sleutel die niet overeenkomt tussen uw netwerk apparaten en de netwerk apparaten van micro soft, ziet u geen ARP-resolutie op laag 2 en BGP-inrichting op laag 3. Als alles goed is geconfigureerd, ziet u dat de BGP-routes correct zijn geadverteerd in zowel de richtingen als de gegevens stroom van uw toepassing overeenkomstig ExpressRoute.

## <a name="next-steps"></a>Volgende stappen
1. [Een ExpressRoute-circuit maken op ExpressRoute direct](expressroute-howto-erdirect.md)
2. [Een ExpressRoute-circuit koppelen aan een virtueel Azure-netwerk](expressroute-howto-linkvnet-arm.md)
3. [ExpressRoute-connectiviteit controleren](expressroute-troubleshooting-expressroute-overview.md)
