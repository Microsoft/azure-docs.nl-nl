---
title: Reageren op Azure Defender voor DNS-waarschuwingen
description: Meer informatie over de stappen die nodig zijn voor het reageren op waarschuwingen van Azure Defender voor DNS
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 375a81127a000741ca5e0397642800794610bcda
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754672"
---
# <a name="respond-to-azure-defender-for-dns-alerts"></a>Reageren op Azure Defender voor DNS-waarschuwingen

Wanneer u een waarschuwing van Azure Defender voor DNS ontvangt, raden wij u aan de waarschuwing te onderzoeken en erop te reageren, zoals hieronder wordt beschreven. Azure Defender voor DNS beveiligt alle verbonden resources, zelfs als u bekend bent met de toepassing of gebruiker die de waarschuwing heeft geactiveerd, is het belang rijk om de situatie rondom elke waarschuwing te controleren.  


## <a name="step-1-contact"></a>Stap 1. Contactpersoon

1. Neem contact op met de resource-eigenaar om te bepalen of het gedrag verwacht of opzettelijk is.
1. Als de activiteit wordt verwacht, sluit u de waarschuwing.
1. Als de activiteit niet wordt verwacht, behandelt u de bron als mogelijk aangetast en verkleind zoals beschreven in de volgende stap.

## <a name="step-2-immediate-mitigation"></a>Stap 2. Onmiddellijke beperking 

1. Isoleer de bron van het netwerk om te voor komen dat er zijdelingse verplaatsing is.
1. Voer een volledige anti-malware-scan uit voor de resource, volgens eventuele resulterende herstel aanbevelingen.
1. Controleer geïnstalleerde en actieve software op de bron, waarbij alle onbekende of ongewenste pakketten worden verwijderd.
1. Zet de computer terug naar een bekende status, installeer het besturings systeem indien nodig opnieuw en herstel de software van een geverifieerde malware-vrije bron.
1. Verhelp eventuele Azure Security Center aanbevelingen voor de machine, waarbij gemarkeerde beveiligings problemen worden opgelost om te voor komen dat er inbreuken optreden.


## <a name="next-steps"></a>Volgende stappen

Op deze pagina wordt het proces van het reageren op een waarschuwing van Azure Defender voor DNS uitgelegd. Raadpleeg de volgende pagina's voor verwante informatie:

- [Inleiding tot Azure Defender voor DNS](defender-for-dns-introduction.md)
- [Waarschuwingen van Azure Defender onderdrukken](alerts-suppression-rules.md)
- [Security Center-gegevens continu exporteren](continuous-export.md)