---
title: Hyper Visor beveiliging op Azure vloot-Azure-beveiliging
description: Technisch overzicht van de beveiliging van Hyper Visor op de Azure-vloot.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 766266edd663b75b893a5883e30bb48eed7bbfdf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94696112"
---
# <a name="hypervisor-security-on-the-azure-fleet"></a>Hyper Visor beveiliging op de Azure-vloot

Het Azure-Hyper Visor systeem is gebaseerd op Windows Hyper-V. Met het Hyper Visor systeem kan de computer beheerder gast partities opgeven die een afzonderlijke adres ruimte hebben. Met de afzonderlijke adres ruimten kunt u een besturings systeem en toepassingen laden die parallel functioneren van het besturings systeem (host) dat wordt uitgevoerd in de hoofd partitie van de computer. Het hostbesturingssysteem (ook wel bekend als privileged Root Partition) heeft directe toegang tot alle fysieke apparaten en rand apparatuur op het systeem (opslag controllers, netwerk aanpassingen). Met het besturings systeem van de host kunnen gast partities het gebruik van deze fysieke apparaten delen door virtuele apparaten zichtbaar te maken voor elke gast partitie. Een besturings systeem dat wordt uitgevoerd in een gast partitie heeft dus toegang tot gevirtualiseerde rand apparaten die worden verschaft door virtualisatie services die worden uitgevoerd in de hoofd partitie.

De Azure-Hyper Visor houdt in dat de volgende beveiligings doelstellingen in acht worden genomen:

| Doelstelling | Bron |
|--|--|
| Isolatie | Een beveiligings beleid verplicht geen informatie overdracht tussen Vm's. Deze beperking vereist mogelijkheden in de Virtual Machine Manager (VMM) en de hardware voor het isoleren van geheugen, apparaten, het netwerk en beheerde resources, zoals persistente gegevens. |
| VMM-integriteit | Ter verkrijging van de algehele systeem integriteit wordt de integriteit van afzonderlijke Hyper Visor onderdelen tot stand gebracht en onderhouden. |
| Platform integriteit | De integriteit van de Hyper Visor is afhankelijk van de integriteit van de hardware en software waarop deze van toepassing is. Hoewel de Hyper Visor geen directe controle heeft over de integriteit van het platform, is Azure afhankelijk van de hardware-en firmware mechanismen, zoals de [Cerberus](project-cerberus.md) -chip, om de onderliggende platform integriteit te beveiligen en te detecteren. De VMM en gasten kunnen niet worden uitgevoerd als de platform integriteit is aangetast. |
| Beperkte toegang | Beheer functies worden alleen uitgeoefend door geautoriseerde beheerders die zijn verbonden via beveiligde verbindingen. Een beginsel van minimale bevoegdheden wordt afgedwongen door Azure RBAC-mechanismen (op rollen gebaseerd toegangs beheer). |
| Controleren | Azure maakt het mogelijk om gegevens vast te leggen en te beveiligen over wat er gebeurt op een systeem, zodat het later kan worden geïnspecteerd. |

De benadering van micro soft om de Azure-Hyper Visor en het subsysteem voor virtualisatie te beveiligen, kan worden onderverdeeld in de volgende drie categorieën.

## <a name="strongly-defined-security-boundaries-enforced-by-the-hypervisor"></a>Sterk gedefinieerde beveiligings grenzen afgedwongen door de Hyper Visor

De Azure-Hyper Visor dwingt meerdere beveiligings grenzen af tussen:

- Gevirtualiseerde ' gast ' partities en geprivilegieerde partitie (' host ')
- Meerdere gasten
- Zelf en de host
- Zelf en alle gasten

Vertrouwelijkheid, integriteit en beschik baarheid zijn gegarandeerd voor de beveiligings grenzen van Hyper Visor. De grenzen van een aantal aanvallen, waaronder side-Channel informatie lekkages, denial-of-service en uitbrei ding van bevoegdheden.

De beveiligings grens Hyper Visor biedt ook segmentatie tussen tenants voor netwerk verkeer, virtuele apparaten, opslag, reken resources en alle andere VM-resources.

## <a name="defense-in-depth-exploit-mitigations"></a>Ingrijpende oplossingen voor misbruik

In het onwaarschijnlijke geval een beveiligings grens heeft een beveiligingslek, bevat de Azure-Hyper Visor meerdere laag oplossingen, waaronder:

- Isolatie van op een host gebaseerd proces dat fungeert als host voor cross-VM-onderdelen
- Beveiliging op basis van virtualisatie (VBS) voor het garanderen van de integriteit van onderdelen van de gebruikers-en kernelmodus-modus vanuit een veilige wereld
- Meerdere niveaus van misbruik van het beveiligingslek. Voor beelden van oplossingen zijn ASLR (Address Space layout randoming), preventie van gegevens uitvoering (DEP), wille keurige code beveiliging, controle stroom integriteit en preventie van gegevens beschadiging
- Automatische initialisatie van stack variabelen op het niveau van de compiler
- Kernel-Api's waarbij kernel-heap-toewijzingen die zijn gemaakt door Hyper-V automatisch worden geïnitialiseerd

Deze oplossingen zijn ontworpen om de ontwikkeling van een aanval te maken voor een niet-haalbaar beveiligingslek in meerdere VM'S.

## <a name="strong-security-assurance-processes"></a>Strenge beveiligings Assurance-processen

De kwets baarheid die betrekking heeft op de Hyper Visor omvat software netwerken, virtuele apparaten en alle cross-VM-Opper vlakken. De kwets baarheid wordt getraceerd via geautomatiseerde build-integratie, waarmee periodieke beveiligings beoordelingen worden geactiveerd.

Alle Opper vlakken van VM-aanvallen zijn bedreigingen gemodelleerd, de code wordt gecontroleerd, op benadering en getest door ons rode team voor schendingen van de beveiligings grens. Micro soft heeft een [bug Bounty-programma](https://www.microsoft.com/msrc/bounty-hyper-v) dat een Award afbetaalt voor relevante beveiligings lekken in in aanmerking komende product versies voor Microsoft Hyper-V.

> [!NOTE]
> Meer informatie over [krachtige beveiligings verzekeringen](../../azure-government/azure-secure-isolation-guidance.md#strong-security-assurance-processes) in Hyper-V.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over de mogelijkheden van platform integriteit en-beveiliging:

- [Firmware-beveiliging](firmware.md)
- [Beveiligd opstarten](secure-boot.md)
- [Gemeten opstarten en host-Attestation](measured-boot-host-attestation.md)
- [Project Cerberus](project-cerberus.md)
- [Versleuteling 'at rest'](encryption-atrest.md)