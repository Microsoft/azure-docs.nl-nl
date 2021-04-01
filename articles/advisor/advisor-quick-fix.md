---
title: Snel oplossen van herbemiddeling voor Advisor-aanbevelingen
description: Bulksgewijs herstel uitvoeren met snelle oplossing in Advisor
ms.topic: article
ms.date: 03/13/2020
ms.author: sagupt
ms.openlocfilehash: a9c86a7ae510d9657f64c71db2aa8c4e3e558f52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90968594"
---
# <a name="quick-fix-remediation-for-advisor"></a>Snel oplossen van herstel voor Advisor
Met **snel oplossen** kunt u een snellere en eenvoudiger herstel methode voor een aanbeveling op meerdere resources. Het biedt mogelijkheden voor het bulksgewijs oplossen van resources en helpt u uw abonnementen sneller te optimaliseren met herbemiddeling op schaal voor uw resources.
De functie is alleen beschikbaar voor bepaalde aanbevelingen via Azure Portal.


## <a name="steps-to-use-quick-fix"></a>Stappen voor het gebruik van Quick Fix

1. Klik in de lijst met aanbevelingen die het label **snel oplossen** hebben op de aanbeveling.

   :::image type="content" source="./media/quick-fix-1.png" alt-text="{Scherm opname van Azure Advisor weer geven van labels voor snel oplossen in de aanbevelingen.}":::
   
   *De prijzen in de afbeelding zijn alleen bedoeld als voor beeld*

2. Op de pagina aanbeveling details ziet u een lijst met resources waarvoor u deze aanbeveling hebt. Selecteer alle resources die u wilt herstellen voor de aanbeveling.

   :::image type="content" source="./media/quick-fix-2.png" alt-text="Scherm opname van het venster betrokken resources met lijst items en de knop snel herstellen gemarkeerd.":::
   
   *De prijzen in de afbeelding zijn alleen bedoeld als voor beeld*

3. Zodra u de resources hebt geselecteerd, klikt u op de knop **snel herstellen** om bulksgewijs te herstellen.

   > [!NOTE]
   > Sommige van de vermelde bronnen zijn mogelijk uitgeschakeld, omdat u niet over de juiste machtigingen beschikt om deze te wijzigen.
   
   > [!NOTE]
   > Als er andere implicaties zijn, naast de voor delen die in Advisor worden genoemd, wordt u in de ervaring gecommuniceerd om u te helpen weloverwogen beslissingen te nemen.
   
4. U ontvangt een melding voor het volt ooien van het herstel. Er wordt een fout bericht weer gegeven als er resources zijn die niet zijn hersteld en resources in de geselecteerde modus in de resource lijst weergave.  


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Advisor-aanbevelingen:
* [Inleiding tot Azure Advisor](advisor-overview.md)
* [Aan de slag met Advisor](advisor-get-started.md)
* [Aanbevelingen voor Advisor-kosten](advisor-cost-recommendations.md)
* [Aanbevelingen voor Advisor-prestaties](advisor-performance-recommendations.md)
* [Aanbevelingen voor de beveiliging van Advisor](advisor-security-recommendations.md)
* [Aanbevelingen voor operationele uitmuntendheid van Advisor](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
