---
title: Overzicht van bedrijfs continuïteit
titleSuffix: Microsoft Genomics
description: In dit overzicht worden de mogelijkheden beschreven die Microsoft Genomics biedt voor bedrijfs continuïteit en herstel na nood gevallen.
keywords: bedrijfs continuïteit, herstel na nood gevallen
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 04/06/2018
ms.openlocfilehash: 3097041c96f70dda384f0b116bbbc9bc9130be4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93395747"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Overzicht van bedrijfs continuïteit met Microsoft Genomics
In dit overzicht worden de mogelijkheden beschreven die Microsoft Genomics biedt voor bedrijfs continuïteit en herstel na nood gevallen. Meer informatie over opties voor het herstellen van verstorende gebeurtenissen, zoals een onderbreking van de Azure-regio, die gegevens verlies kan veroorzaken. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>Microsoft Genomics functies die bedrijfs continuïteit ondersteunen 
Hoewel zeldzame gevallen kan een Azure-Data Center een storing hebben, waardoor een bedrijfs onderbreking kan leiden tot een paar minuten tot enkele uren. Als er een storing optreedt, zullen alle taken die momenteel in het Data Center worden uitgevoerd, mislukken en worden de taken niet automatisch door de Microsoft Genomics-service verzonden naar een secundaire regio. 

* Een optie is te wachten tot het Data Center weer online is wanneer de storing van het Data Center wordt overschreden. Als de storing korter is, detecteert de Microsoft Genomics-service automatisch de mislukte taken en wordt de werk stroom automatisch opnieuw gestart.

* Een andere mogelijkheid is om proactief de werk stroom in een ander gegevens gebied in te dienen. Microsoft Genomics implementeert instanties in verschillende [Azure-regio's](https://azure.microsoft.com/regions/services/)en elke regio-specifiek exemplaar is onafhankelijk. Als een van de Microsoft Genomics exemplaren een regionale lokale storing ondervindt, blijven andere regio's waarop exemplaren van Microsoft Genomics worden uitgevoerd, taken verwerken. Deze overdracht naar een alternatieve regio ligt onder controle van de gebruiker en is op elk moment beschikbaar.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Hand matig failover-Microsoft Genomics werk stromen naar een andere regio
Als er sprake is van een storing in een regionaal Data Center, kunt u ervoor kiezen om Microsoft Genomics werk stromen in een secundaire regio in te dienen, op basis van uw eigen vereisten voor gegevens soevereiniteit en bedrijfs continuïteit. Als u hand matig failover-Microsoft Genomics werk stromen wilt, gebruikt u een andere regio-specifiek. Genomics-account en verzend de taak met de juiste specifieke Genomics en referenties voor het opslag account.

U moet het volgende doen:
* Maak een Genomics-account in de secundaire regio met behulp van de Azure Portal. 
* Migreer uw invoer gegevens naar een opslag account in de secundaire regio en stel een uitvoermap in de secundaire regio in.
* Verzend de werk stroom in de secundaire regio.

Wanneer de oorspronkelijke regio wordt hersteld, migreert de Microsoft Genomics-service de gegevens van de secundaire regio niet terug naar de oorspronkelijke regio. U kunt ervoor kiezen om de invoer-en uitvoer bestanden van de secundaire regio terug te zetten naar de oorspronkelijke regio.  Als u ervoor kiest om hun gegevens te verplaatsen, is dit buiten de Genomics-service en zijn alle kosten gerelateerd aan de verplaatsing van gegevens uw verantwoordelijkheid. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Voorbereiden op een mogelijke regio-specifieke onderbreking
Als u zich zorgen maakt over een snellere herstel bewerking in het geval van een storing in een Data Center, zijn er enkele stappen die u kunt uitvoeren om uw Microsoft Genomics-werk stromen hand matig opnieuw in te dienen bij een secundaire regio:

* Identificeer een geschikte secundaire regio en pro-actief een Genomics-account maken in die regio
* Dupliceer uw gegevens in de primaire en secundaire regio zodat uw gegevens onmiddellijk beschikbaar zijn in de secundaire regio. Dit kan hand matig worden gedaan of met behulp van de [geo-redundante opslag](../storage/common/storage-redundancy.md) functie die beschikbaar is in azure Storage. 

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd over de opties voor bedrijfs continuïteit en herstel na nood gevallen bij het gebruik van de Microsoft Genomics service. Voor meer informatie over bedrijfs continuïteit en herstel na nood gevallen in Azure in het algemeen raadpleegt u [technische richt lijnen voor Azure-tolerantie.](/azure/architecture/resiliency/recovery-loss-azure-region)