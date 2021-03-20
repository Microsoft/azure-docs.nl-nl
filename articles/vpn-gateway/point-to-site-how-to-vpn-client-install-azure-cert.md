---
title: 'Azure VPN Gateway: een punt-naar-site-client certificaat installeren'
description: Installeer het client certificaat voor P2S-certificaat verificatie-Windows, Mac, Linux.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 8c4d37f67d89ad1b7973a85fb6c2a9f7e818a028
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89421659"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>Client certificaten installeren voor P2S-certificaat authenticatie verbindingen

Alle clients die verbinding maken met een virtueel netwerk met behulp van punt-naar-site-Azure-certificaat verificatie, vereisen een client certificaat. Dit artikel helpt u bij het installeren van een client certificaat dat wordt gebruikt voor verificatie wanneer u verbinding maakt met een VNet met behulp van P2S.

## <a name="acquire-a-client-certificate"></a><a name="generate"></a>Een client certificaat verkrijgen

Ongeacht het besturings systeem van de client waarmee u verbinding wilt maken, moet u altijd een client certificaat hebben. U kunt een client certificaat genereren van een basis certificaat dat is gegenereerd met een bedrijfs CA-oplossing of een zelfondertekend basis certificaat. Zie de [Power shell](vpn-gateway-certificates-point-to-site.md)-, [makecert](vpn-gateway-certificates-point-to-site-makecert.md)-of [Linux](vpn-gateway-certificates-point-to-site-linux.md) -instructies voor de stappen voor het genereren van een client certificaat. 

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="mac"></a><a name="installmac"></a>Mac

>[!NOTE]
>Mac VPN-clients worden alleen ondersteund voor het Resource Manager-implementatie model. Ze worden niet ondersteund voor het klassieke implementatie model.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="linux"></a><a name="installlinux"></a>Spreek

Het Linux-client certificaat wordt geïnstalleerd op de client als onderdeel van de client configuratie. Zie [client configuratie-Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli) voor instructies.

## <a name="next-steps"></a>Volgende stappen

Ga door met de stappen voor punt-naar-site [-configuratie om configuratie bestanden voor VPN-clients te maken en te installeren](point-to-site-vpn-client-configuration-azure-cert.md).
