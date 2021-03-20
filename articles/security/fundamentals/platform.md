---
title: Azure-platform integriteit en beveiliging-Azure-beveiliging
description: Technisch overzicht van de integriteit en beveiliging van het Azure-platform.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 4755bc19a645d196487f0b8e0f4d1ef2120723ca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94557732"
---
# <a name="platform-integrity-and-security-overview"></a>Overzicht van platform integriteit en beveiliging
De Azure-vloot bestaat uit miljoenen servers (hosts) met duizenden extra informatie die dagelijks is toegevoegd. Duizenden hosts ondergaan ook dagelijks onderhoud door het opnieuw opstarten, vernieuwen van het besturings systeem of reparaties. Voordat een host kan worden toegevoegd aan de vloot en aan de slag gaat met het accepteren van klant workloads, controleert micro soft of de host zich in een veilige en betrouw bare staat bevindt. Deze verificatie zorgt ervoor dat er geen kwaad aardige of onopzettelijke wijzigingen zijn aangebracht in de onderdelen van de opstart procedure tijdens de toeleverings keten of onderhouds werk stromen.

## <a name="securing-azure-hardware-and-firmware"></a>Azure hardware en firmware beveiligen
In deze reeks artikelen wordt beschreven hoe micro soft de integriteit en beveiliging van hosts garandeert via verschillende fasen in hun levens cyclus, van productie tot Zons ondergang. Het artikel adres:
 
- [Firmware-beveiliging](firmware.md)
- [UEFI Secure boot](secure-boot.md)
- [Gemeten opstarten en host-Attestation](measured-boot-host-attestation.md)
- [Project Cerberus](project-cerberus.md)
- [Versleuteling 'at rest'](encryption-atrest.md)
- [Hypervisor-beveiliging](hypervisor.md)
 
## <a name="next-steps"></a>Volgende stappen

- Meer informatie over hoe micro soft actief partners binnen het ecosysteem van de Cloud hardware doorlopende verbeteringen in de firmware voor de [beveiliging](firmware.md)kan aansturen

- Begrijp uw [gedeelde verantwoordelijkheid in de Cloud](shared-responsibility.md).