---
title: Windows virtueel bureau blad integreren met Azure Advisor-Azure
description: Azure Advisor gebruiken met uw Windows-implementatie voor virtueel bureau blad.
author: Heidilohr
ms.topic: how-to
ms.date: 08/28/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 76b7f97b6020a3a0d5571a3a105d15f7d7893485
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89147534"
---
# <a name="use-azure-advisor-with-windows-virtual-desktop"></a>Azure Advisor gebruiken met virtueel bureau blad van Windows

Azure Advisor kunnen gebruikers helpen bij het oplossen van veelvoorkomende problemen zonder dat ze hiervoor ondersteuning hoeven te gebruiken. De aanbevelingen verminderen de nood zaak om hulp aanvragen te verzenden, zodat u tijd en kosten bespaart.

In dit artikel wordt beschreven hoe u Azure Advisor instelt in uw Windows-implementatie voor virtueel bureau blad om uw gebruikers te helpen.

## <a name="what-is-azure-advisor"></a>Wat is Azure Advisor?

Azure Advisor analyseert uw configuraties en telemetrie om persoonlijke aanbevelingen te bieden voor het oplossen van veelvoorkomende problemen. Met deze aanbevelingen kunt u uw Azure-resources optimaliseren voor betrouw baarheid, beveiliging, bedrijfs kwaliteit, prestaties en kosten. Meer informatie vindt u op [de Azure Advisor-website](https://azure.microsoft.com/services/advisor/).

## <a name="how-to-start-using-azure-advisor"></a>Azure Advisor gaan gebruiken

Alles wat u nodig hebt om aan de slag te gaan is een Azure-account op de Azure Portal. Open eerst de Azure Portal op <https://portal.azure.com/#home> en selecteer vervolgens **Advisor** onder **Azure-Services**, zoals wordt weer gegeven in de volgende afbeelding. U kunt ook ' Azure Advisor ' invoeren in de zoek balk van de Azure Portal.

> [!div class="mx-imgBorder"]
> ![Een scherm opname van de Azure Portal. De gebruiker beweegt de muis aanwijzer over de Azure Advisor koppeling, waardoor er een vervolg keuzemenu wordt weer gegeven.](media/azure-advisor.png)

Wanneer u Azure Advisor opent, ziet u vijf categorieën:

- Kosten
- Beveiliging
- Betrouwbaarheid
- Operationele topprestaties
- Prestaties

> [!div class="mx-imgBorder"]
> ![Een scherm afbeelding van Azure Advisor waarin de vijf menu's voor elke categorie worden weer gegeven. De vijf items die in hun eigen vakken worden weer gegeven, zijn kosten, beveiliging, betrouw baarheid, bedrijfs kwaliteit en prestaties.](media/advisor-categories.png)

Wanneer u een categorie selecteert, gaat u naar de pagina met actieve aanbevelingen. Op deze pagina kunt u zien welke aanbevelingen Azure Advisor voor u hebben, zoals wordt weer gegeven in de volgende afbeelding.

> [!div class="mx-imgBorder"]
> ![Een scherm afbeelding van de lijst met actieve aanbevelingen voor operationele uitmuntendheid. In de lijst worden zeven aanbevelingen met verschillende prioriteits niveaus weer gegeven.](media/active-suggestions.png)

## <a name="additional-tips-for-azure-advisor"></a>Aanvullende tips voor Azure Advisor

- Zorg ervoor dat u uw aanbevelingen regel matig controleert, ten minste één keer per week. De actieve aanbevelingen van Azure Advisor worden meerdere keren per dag bijgewerkt. Het controleren op nieuwe aanbevelingen kan leiden tot grotere problemen door u te helpen kleinere oplossingen te herkennen en op te lossen.

- Probeer altijd de problemen met het hoogste prioriteits niveau in Azure Advisor op te lossen. Problemen met hoge prioriteit worden gemarkeerd met rood. Niet-opgeloste aanbevelingen met hoge prioriteit kunnen leiden tot problemen met de regel.

- Als een aanbeveling minder belang rijk lijkt te zijn, kunt u deze sluiten of uitstellen. Als u een aanbeveling wilt negeren of uitstellen, gaat u naar de kolom **actie** en wijzigt u de status van het item.

- Negeer aanbevelingen pas wanneer u weet waarom ze worden weer gegeven en er zeker van zijn dat deze geen negatieve gevolgen voor u of uw gebruikers hebben. Selecteer altijd meer **informatie** om te zien wat het probleem is. Als u een probleem verhelpt door de instructies in Azure Advisor te volgen, wordt het automatisch uit de lijst verwijderd. U kunt problemen oplossen door het probleem op te lossen en deze herhaaldelijk uit te stellen.

- Telkens wanneer u een probleem ondervindt in het virtuele bureau blad van Windows, moet u Azure Advisor eerst altijd controleren. Azure Advisor geeft u instructies voor het oplossen van het probleem of u kunt ten minste verwijzen naar een resource die u kan helpen.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Advisor aanbevelingen oplossen](azure-advisor-recommendations.md)voor meer informatie over het oplossen van aanbevelingen.

Als u suggesties hebt voor nieuwe aanbevelingen, plaatst u deze op ons [Azure Advisor Voice-Forum voor gebruikers](https://windowsvirtualdesktop.uservoice.com/forums/930847-azure-advisor-recommendations).
