---
title: bestand opnemen
description: bestand opnemen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9caf63fc90be7bae0461ddc24c94594a32199765
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91812676"
---
#### <a name="microsoft-windows"></a>Microsoft Windows

##### <a name="openvpn"></a>OpenVPN

1. Download en installeer de OpenVPN-client op de officiële website.
1. Download het VPN-profiel voor de gateway. Dit kan via het tabblad VPN-configuraties voor gebruikers in de Azure-portal of via New-AzureRmVpnClientConfiguration in PowerShell.
1. Pak het profiel uit. Open het configuratiebestand vpnconfig.ovpn uit de map OpenVPN in Kladblok.
1. Vul het gedeelte P2S client certificate met de openbare P2S-clientcertificatcode in base64. In een certificaat met PEM-indeling kunt u het .cer-bestand openen en de base64-code tussen de headers van het certificaat kopiëren. Zie [Een certificaat exporteren om de gecodeerde openbare sleutel te verkrijgen](../articles/virtual-wan/certificates-point-to-site.md) voor de te volgen stappen.
1. Vul in het gedeelte voor de persoonlijke sleutel de persoonlijke P2S-clientcertificaatsleutel in Base64 in. Zie [De persoonlijke sleutel uitpakken](../articles/virtual-wan/howto-openvpn-clients.md#windows) voor de te volgen stappen.
1. Wijzig geen andere velden. Gebruik de ingevulde configuratie in de clientinvoer om verbinding te maken met de VPN.
1. Kopieer het bestand vpnconfig.ovpn naar C:\Program Files\OpenVPN\config folder.
1. Klik met de rechtermuisknop op het pictogram van OpenVPN in het systeemvak en selecteer **Verbinding maken**.

##### <a name="ikev2"></a>IKEv2

1. Selecteer de VPN-clientconfiguratiebestanden die overeenkomen met de architectuur van de Windows-computer. Kies voor een architectuur met 64-bits processor het installatiepakket ‘VpnClientSetupAmd64’. Kies voor een architectuur met 32-bits processor het installatiepakket ‘VpnClientSetupAmdX86’.
1. Dubbelklik op het pakket om het te installeren. Selecteer **Meer info** en vervolgens **Toch uitvoeren** als u een SmartScreen-melding ziet.
1. Navigeer op de clientcomputer naar **Netwerkinstellingen** en selecteer **VPN**. De VPN-verbinding bevat de naam van het virtuele netwerk waarmee verbinding wordt gemaakt.
1. Controleer of u een clientcertificaat op de clientcomputer hebt geïnstalleerd voordat u verbinding probeert te maken. Een clientcertificaat is vereist voor verificatie als u het systeemeigen verificatietype met Azure-certificaat gebruikt. Raadpleeg [Certificaten genereren](../articles/virtual-wan/certificates-point-to-site.md) voor meer informatie over het genereren van certificaten. Raadpleeg [Een clientcertificaat installeren](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md) voor meer informatie over het installeren van een clientcertificaat.
