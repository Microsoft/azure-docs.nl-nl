---
title: 'Firmware-integriteit: Azure-beveiliging'
description: Meer informatie over cryptografische metingen om de integriteit van de firmware te garanderen.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: f085858a9d550623704efd4f051ed525e55a37e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94557667"
---
# <a name="project-cerberus"></a>Project Cerberus

Cerberus is een op NIST 800-193 compatibele hardware-basis-of-Trust met een identiteit die niet kan worden gekloond. Cerberus is ontworpen om de beveiligings-postuur van Azure-infra structuur verder te verhogen door een sterk anker van vertrouwen te bieden voor firmware-integriteit.

## <a name="enabling-an-anchor-of-trust"></a>Een anker van vertrouwen inschakelen
Elke Cerberus-chip heeft een unieke cryptografische identiteit die wordt ingesteld met behulp van een ondertekende certificaat keten die is geroot naar een micro soft-certificerings instantie (CA). Metingen die zijn verkregen via Cerberus kunnen worden gebruikt voor het valideren van de integriteit van onderdelen, zoals:

- Host
- Baseboard Management Controller (BMC)
- Alle rand apparatuur, waaronder netwerk interface kaart en [systeem-op-een-chip](https://en.wikipedia.org/wiki/System_on_a_chip) (SOC)

Dit anker van de vertrouwens relatie helpt platform firmware te beschermen tegen:

- Beschadigde firmware bestanden die op het platform worden uitgevoerd
- Malware en hackers die gebruikmaken van fouten in het besturings systeem, de toepassing of de Hyper Visor
- Bepaalde typen toeleverings keten aanvallen (productie, assembly, door Voer)
- Kwaad aardige insiders met beheerders bevoegdheden of toegang tot hardware

## <a name="cerberus-attestation"></a>Cerberus-Attestation
Cerberus verifieert de integriteit van firmware voor Server onderdelen met behulp van een platform firmware-manifest (PFM). PFM definieert een lijst met geautoriseerde firmware versies en biedt een platform meting aan de Azure [host Attestation-service](measured-boot-host-attestation.md). De Attestation-service van de host valideert de metingen en maakt een bepaling zodat alleen vertrouwde hosts kunnen worden toegevoegd aan de Azure-vloot en werk belastingen van de klant hosten.

In combi natie met de Cerberus-mogelijkheden van de host wordt een zeer veilige Azure-productie infrastructuur verbeterd en bevorderd.

> [!NOTE]
> Zie de [project Cerberus](https://github.com/opencomputeproject/Project_Olympus/tree/master/Project_Cerberus) -informatie over github voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over de mogelijkheden van platform integriteit en-beveiliging:

- [Firmware-beveiliging](firmware.md)
- [Beveiligd opstarten](secure-boot.md)
- [Gemeten opstarten en host-Attestation](measured-boot-host-attestation.md)
- [Versleuteling 'at rest'](encryption-atrest.md)
- [Hypervisor-beveiliging](hypervisor.md)