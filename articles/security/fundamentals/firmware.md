---
title: Firmware beveiliging-Azure-beveiliging
description: Meer informatie over hoe micro soft de hardware en firmware van Azure beveiligt.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 8233cc714d977083f4d55716d35c0b7094a069ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94557720"
---
# <a name="firmware-security"></a>Firmware-beveiliging
In dit artikel wordt beschreven hoe micro soft de hardware ecosysteem en toeleverings ketens van de Cloud beveiligt.

## <a name="securing-the-cloud-hardware-ecosystem"></a>Het hardware-ecosysteem voor de Cloud beveiligen
Micro soft actieve partners in het Cloud hardware-ecosysteem om voortdurende beveiligings verbeteringen door te lopen:

- Samen werken met Azure hardware-en firmware partners (zoals onderdeel fabrikanten en systeem integrators) om te voldoen aan de beveiligings vereisten van Azure hardware en firmware.

- Het inschakelen van partners om de beveiliging van hun producten continu te evalueren en te verbeteren met behulp van door micro soft gedefinieerde vereisten op gebieden zoals:

  - Firmware beveiligd opstarten
  - Veilig herstel van de firmware
  - Beveiligde firmware-update
  - Firmware-crypto grafie
  - Vergrendelde hardware
  - Gedetailleerde telemetrie voor fout opsporing
  - Systeem ondersteuning voor TPM 2,0-hardware om gemeten opstarten mogelijk te maken

- Het [Open Compute project (OCP)-](https://www.opencompute.org/wiki/Security) beveiligings project aan het nemen en bijdragen aan de ontwikkeling van de specificaties. Specificaties bevorderen de consistentie en duidelijkheid voor veilig ontwerpen en architectuur in het ecosysteem.

   > [!NOTE]
   > Een voor beeld van onze bijdrage aan het OCP-beveiligings project is de [Hardware Secure boot](https://docs.google.com/document/d/1Se1Dd-raIZhl_xV3MnECeuu_I0nF-keg4kqXyK4k4Wc/edit#heading=h.5z2d7x9gbhk0) -specificatie.

## <a name="securing-hardware-and-firmware-supply-chains"></a>Hardware-en firmware-toeleverings ketens beveiligen
Cloud hardware-leveranciers en leveranciers voor Azure zijn ook verplicht om te voldoen aan de beveiligings processen en-vereisten van de toeleverings keten die door micro soft zijn ontwikkeld. Ontwikkel-en implementatie processen voor hardware en firmware zijn vereist om de micro soft-processen voor [Security Development Lifecycle](https://www.microsoft.com/securityengineering/sdl) (SDL) te volgen, zoals:

- Bedreigingsmodellen
- Veilige ontwerp beoordelingen
- Evaluatie van de firmware en indringings tests
- Ontwikkel-en test omgevingen beveiligen
- Beheer van beveiligings problemen en reactie op incidenten

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over de mogelijkheden van platform integriteit en-beveiliging:

- [Beveiligd opstarten](secure-boot.md)
- [Gemeten opstarten en host-Attestation](measured-boot-host-attestation.md)
- [Project Cerberus](project-cerberus.md)
- [Versleuteling 'at rest'](encryption-atrest.md)
- [Hypervisor-beveiliging](hypervisor.md)