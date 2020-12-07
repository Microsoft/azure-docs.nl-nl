---
title: Reageren op waarschuwingen over Azure Defender voor Resource Manager
description: Meer informatie over de stappen die nodig zijn voor het reageren op waarschuwingen van Azure Defender voor Resource Manager
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 54790795aab8aac247e17198159130d7139dd38c
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754649"
---
# <a name="respond-to-azure-defender-for-resource-manager-alerts"></a>Reageren op waarschuwingen van Azure Defender voor Resource Manager

Wanneer u een waarschuwing ontvangt van Azure Defender voor Resource Manager, raden we u aan de waarschuwing te onderzoeken en erop te reageren, zoals hieronder wordt beschreven. Azure Defender voor Resource Manager beveiligt alle verbonden resources, zelfs als u bekend bent met de toepassing of gebruiker die de waarschuwing heeft geactiveerd, is het belang rijk om de situatie rondom elke waarschuwing te controleren.  


## <a name="step-1-contact"></a>Stap 1. Contactpersoon

1. Neem contact op met de resource-eigenaar om te bepalen of het gedrag verwacht of opzettelijk is.
1. Als de activiteit wordt verwacht, sluit u de waarschuwing.
1. Als de activiteit niet wordt verwacht, behandelt u de gerelateerde gebruikers accounts, abonnementen en virtuele machines als aangetast en verkleind zoals beschreven in de volgende stap.

## <a name="step-2-immediate-mitigation"></a>Stap 2. Onmiddellijke beperking 

1. Verkraakte gebruikers accounts herstellen:
    - Als ze niet bekend zijn, kunt u ze verwijderen als ze zijn gemaakt door een bedreigings actor
    - Als ze bekend zijn, wijzig dan hun verificatie referenties
    - Gebruik Azure-activiteiten Logboeken om alle activiteiten te bekijken die door de gebruiker worden uitgevoerd en om te zien welke er verdacht zijn

1. Verholpen abonnementen herstellen:
    - Verwijder onbekende Runbooks uit het gemanipuleerde Automation-account
    - IAM-machtigingen voor het abonnement controleren en machtigingen verwijderen voor onbekende gebruikers accounts
    - Alle Azure-resources in het abonnement bekijken en alle bronnen verwijderen die niet bekend zijn
    - Beveiligings waarschuwingen voor het abonnement controleren en onderzoeken in Azure Security Center
    - Azure-activiteiten Logboeken gebruiken om alle activiteiten in het abonnement te controleren en eventuele verdachte te identificeren

1. De gemanipuleerde virtuele machines herstellen
    - De wacht woorden voor alle gebruikers wijzigen
    - Een volledige anti-malware-scan uitvoeren op de computer
    - Installatie kopieën van de machines terugzetten van een bron zonder malware


## <a name="next-steps"></a>Volgende stappen

Op deze pagina wordt het proces van het reageren op een waarschuwing van Azure Defender voor Resource Manager uitgelegd. Raadpleeg de volgende pagina's voor verwante informatie:

- [Inleiding tot Azure Defender voor Resource Manager](defender-for-resource-manager-introduction.md)
- [Waarschuwingen van Azure Defender onderdrukken](alerts-suppression-rules.md)
- [Security Center-gegevens continu exporteren](continuous-export.md)