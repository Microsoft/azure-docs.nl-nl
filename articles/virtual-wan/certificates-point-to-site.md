---
title: Certificaten voor VPN-verbindingen voor gebruikers genereren en exporteren | Virtueel WAN van Azure
description: Maak een zelfondertekend basis certificaat, Exporteer de open bare sleutel en Genereer client certificaten voor VPN-verbindingen van gebruikers met behulp van Power shell op Windows 10 of Windows Server 2016.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 2205f170ee846d4db94db7f524a1c424cfbc8f7b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91328035"
---
# <a name="generate-and-export-certificates-for-user-vpn-connections"></a>Certificaten voor VPN-verbindingen van gebruikers genereren en exporteren

Gebruikers VPN-verbindingen (punt-naar-site) gebruiken certificaten voor verificatie. In dit artikel wordt beschreven hoe u een zelfondertekend basis certificaat maakt en client certificaten genereert met behulp van Power shell op Windows 10 of Windows Server 2016.

U moet de stappen in dit artikel uitvoeren op een computer met Windows 10 of Windows Server 2016. De Power shell-cmdlets die u gebruikt om certificaten te genereren, maken deel uit van het besturings systeem en werken niet op andere versies van Windows. De computer met Windows 10 of Windows Server 2016 is alleen nodig voor het genereren van de certificaten. Zodra de certificaten zijn gegenereerd, kunt u deze uploaden of installeren op elk ondersteund client besturingssysteem.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>Volgende stappen

Door gaan met de [virtuele WAN-stappen voor de gebruiker VPN-verbinding](virtual-wan-about.md)
