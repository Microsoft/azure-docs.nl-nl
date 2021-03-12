---
title: TLS 1,2 configureren op Windows-clients die toegang krijgen tot Azure Stack Edge Pro GPU-apparaat
description: Hierin wordt beschreven hoe u TLS 1,2 configureert op Windows-clients die toegang krijgen tot Azure Stack Edge Pro GPU-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 4a159f7fa384a6899fb3cbb4db3bba9e0ed02d52
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102638099"
---
# <a name="configure-tls-12-on-windows-clients-accessing-azure-stack-edge-pro-device"></a>TLS 1,2 configureren op Windows-clients die toegang krijgen tot Azure Stack Edge Pro-apparaat

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Als u een Windows-client gebruikt om toegang te krijgen tot uw Azure Stack Edge Pro-apparaat, moet u TLS 1,2 configureren op uw client. In dit artikel vindt u bronnen en richt lijnen voor het configureren van TLS 1,2 op uw Windows-client. 

De richt lijnen die hier worden beschreven, zijn gebaseerd op tests die worden uitgevoerd op een client met Windows Server 2016.

## <a name="configure-tls-12-for-current-powershell-session"></a>TLS 1,2 configureren voor de huidige Power shell-sessie

Voer de volgende stappen uit om TLS 1,2 op uw client te configureren.

1. Voer Power shell uit als Administrator.
2. Als u TLS 1,2 wilt instellen voor de huidige Power shell-sessie, typt u:
  
    ```azurepowershell
    $TLS12Protocol = [System.Net.SecurityProtocolType] 'Ssl3 , Tls12'
    [System.Net.ServicePointManager]::SecurityProtocol = $TLS12Protocol
    ```
## <a name="configure-tls-12-on-client"></a>TLS 1,2 op client configureren

Als u de systeem breedte TLS 1,2 wilt instellen voor uw omgeving, volgt u de richt lijnen in deze documenten:

- [Algemeen-TLS 1,2 inschakelen](/windows-server/security/tls/tls-registry-settings#tls-12)
- [TLS 1,2 op clients inschakelen](/configmgr/core/plan-design/security/enable-tls-1-2-client)
- [TLS 1,2 inschakelen op de site servers en externe site systemen](/configmgr/core/plan-design/security/enable-tls-1-2-server)
- [Protocollen in TLS/SSL (Schannel-SSP)](/windows-server/security/tls/manage-tls#configuring-tls-ecc-curve-order)
- [Coderings suites](/windows-server/security/tls/tls-registry-settings#tls-12): specifiek configureren van de [volg orde van de TLS-coderings Suite](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order) zorg ervoor dat u uw huidige coderings suites vermeld en laten voorafgaan door ontbreekt in de volgende lijst:

    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

    U kunt deze coderings suites ook toevoegen door de Register instellingen rechtstreeks te bewerken.

    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "Functions"  -PropertyType String -Value ("TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384")
    ```

- Elliptische curven instellen

    Zorg ervoor dat u uw huidige elliptische curves opneemt en laten voorafgaan door mist in de volgende lijst:

    - P-256 
    - P-384

    U kunt deze elliptische curven ook toevoegen door de Register instellingen rechtstreeks te bewerken.
    
    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "EccCurves" -PropertyType MultiString -Value @("NistP256", "NistP384")
    ```
    
    - [Stel de minimale grootte van RSA-sleutel uitwisseling in op 2048](/windows-server/security/tls/tls-registry-settings#keyexchangealgorithm---client-rsa-key-sizes).



## <a name="next-steps"></a>Volgende stappen

[Verbinding maken met Azure Resource Manager](azure-stack-edge-j-series-connect-resource-manager.md)