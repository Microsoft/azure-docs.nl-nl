---
title: 'Azure VPN Gateway: over P2S VPN-client profielen'
description: Gebruik dit artikel om de informatie te vinden die u nodig hebt voor een VPN-client profiel.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: 9bb66363d525648df08f32451842402ad1d0d93b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99979064"
---
# <a name="working-with-p2s-vpn-client-profile-files"></a>Werken met P2S VPN-client profiel bestanden

De profiel bestanden bevatten informatie die nodig is voor het configureren van een VPN-verbinding. Dit artikel helpt u bij het verkrijgen en begrijpen van de informatie die nodig is voor een VPN-client profiel.

## <a name="generate-and-download-profile"></a>Profiel genereren en downloaden

U kunt client configuratie bestanden genereren met behulp van Power shell of met behulp van de Azure Portal. Beide methoden retour neren hetzelfde zip-bestand.

### <a name="portal"></a>Portal

1. Navigeer in het Azure Portal naar de virtuele netwerk gateway voor het virtuele netwerk waarmee u verbinding wilt maken.
1. Selecteer op de pagina virtuele netwerk gateway de optie **punt-naar-site-configuratie**.
1. Selecteer aan de bovenkant van de pagina punt-naar-site-configuratie de optie **VPN-client downloaden**. Het duurt enkele minuten voordat het client configuratie pakket is gegenereerd.
1. Uw browser geeft aan dat een zip-bestand voor de client configuratie beschikbaar is. Deze heeft dezelfde naam als uw gateway. Pak het bestand uit om de mappen weer te geven.

### <a name="powershell"></a>PowerShell

U kunt het volgende voor beeld gebruiken om te genereren met behulp van Power shell:

1. Bij het genereren van configuratie bestanden voor VPN-clients is de waarde '-EapTls '. Genereer de configuratie bestanden voor de VPN-client met de volgende opdracht:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. Kopieer de URL naar uw browser om het zip-bestand te downloaden en pak het bestand uit om de mappen weer te geven.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* De **map openvpn** bevat het *ovpn* -profiel dat moet worden gewijzigd om de sleutel en het certificaat op te laten bevatten. Zie [openvpn-clients configureren voor Azure VPN gateway](vpn-gateway-howto-openvpn-clients.md#windows)voor meer informatie. Als Azure AD-verificatie is geselecteerd op de VPN-gateway, is deze map niet aanwezig in het zip-bestand. Ga in plaats daarvan naar de map AzureVPN en zoek azurevpnconfig.xml.

## <a name="next-steps"></a>Volgende stappen

Zie [about Point-to-site](point-to-site-about.md)(Engelstalig) voor meer informatie over punt-naar-site.
