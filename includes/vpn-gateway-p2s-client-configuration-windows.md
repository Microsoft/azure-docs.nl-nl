---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.openlocfilehash: f37876a0b6b7e8b520b5b6428381ebaf7b5b5c24
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93042600"
---
U kunt hetzelfde configuratie pakket voor de VPN-client gebruiken op elke Windows-client computer, mits de versie overeenkomt met de architectuur van de client. Zie de sectie punt-naar-site van de [Veelgestelde vragen over VPN gateway](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S)voor de lijst met ondersteunde client besturingssystemen.

>[!NOTE]
>U moet over beheerders rechten beschikken voor de Windows-client computer waarmee u verbinding wilt maken.
>

Gebruik de volgende stappen om de systeem eigen Windows VPN-client te configureren voor verificatie van certificaten:

1. Selecteer de VPN-clientconfiguratiebestanden die overeenkomen met de architectuur van de Windows-computer. Kies voor een architectuur met 64-bits processor het installatiepakket ‘VpnClientSetupAmd64’. Kies voor een architectuur met 32-bits processor het installatiepakket ‘VpnClientSetupAmdX86’. 
1. Dubbelklik op het pakket om het te installeren. Als u een SmartScreen-melding ziet, klikt u op **Meer info** en vervolgens op **Toch uitvoeren**.
1. Navigeer op de client computer naar **netwerk instellingen** en klik op **VPN**. De VPN-verbinding bevat de naam van het virtuele netwerk waarmee verbinding wordt gemaakt.
1. Controleer of u een clientcertificaat op de clientcomputer hebt geïnstalleerd voordat u verbinding probeert te maken. Een clientcertificaat is vereist voor verificatie als u het systeemeigen verificatietype met Azure-certificaat gebruikt.