---
title: Richt lijnen voor herstel na nood gevallen voor Azure Data Lake Analytics
description: Meer informatie over het plannen van herstel na nood gevallen voor uw Azure Data Lake Analytics-accounts.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: ce5e96bf464eebb9b91e3ffac9312e89df9cbabe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220972"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Richt lijnen voor herstel na nood gevallen voor Azure Data Lake Analytics

Azure Data Lake Analytics is een service op aanvraag voor het uitvoeren van analysetaken, om de analyse van big data te vereenvoudigen. U hoeft geen hardware te implementeren, configureren en optimaliseren, maar kunt query's schrijven om uw data te transformeren en waardevolle inzichten te verkrijgen. De analyseservice kan taken van elke schaal onmiddellijk verwerken, door de hoeveelheid rekenkracht die u nodig hebt aan te passen. U betaalt alleen voor de tijd die nodig is voor het uitvoeren van uw taak, wat het gebruik van deze service zeer rendabel maakt. Dit artikel bevat richt lijnen voor het beveiligen van uw taken tegen zeldzame regionale onderbrekingen of onbedoelde verwijderingen.

## <a name="disaster-recovery-guidance"></a>Richtlijnen voor herstel na noodgevallen

Wanneer u Azure Data Lake Analytics gebruikt, is het essentieel om uw eigen plan voor herstel na nood gevallen voor te bereiden. Dit artikel helpt u bij het maken van een plan voor herstel na nood gevallen. Er zijn aanvullende bronnen die u kunnen helpen bij het maken van uw eigen plan:
- [Herstel na fouten en noodgevallen voor Azure-toepassingen](/azure/architecture/reliability/disaster-recovery)
- [Technische richtlijnen voor flexibiliteit van Azure](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>Aanbevolen procedures en scenario richtlijnen

U kunt een terugkerende U-SQL-taak uitvoeren in een ADLA-account in een regio die U-SQL-tabellen leest en schrijft, evenals ongestructureerde gegevens.  Bereid u voor op een nood geval door de volgende stappen uit te voeren:

1. Maak ADLA-en ADLS-accounts in de secundaire regio die wordt gebruikt tijdens een storing.

   > [!NOTE]
   > Omdat account namen wereld wijd uniek zijn, gebruikt u een consistent naam schema dat aangeeft welk account secundair is.

2. Raadpleeg de [richt lijnen voor herstel na nood gevallen voor gegevens in azure data Lake Storage gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md) voor ongestructureerde gegevens

3. Voor gestructureerde gegevens die zijn opgeslagen in ADLA-tabellen en-data bases, maakt u kopieën van de meta gegevensartefacten, zoals data bases, tabellen, functies voor tabel waarden en verzamelingen. U moet deze artefacten periodiek opnieuw synchroniseren wanneer er wijzigingen in de productie plaatsvinden. Zo moeten nieuw ingevoegde gegevens worden gerepliceerd naar de secundaire regio door de gegevens te kopiëren en in de secundaire tabel in te voegen.

   > [!NOTE]
   > Deze object namen vallen binnen het bereik van het secundaire account en zijn niet globaal uniek, zodat ze dezelfde namen kunnen hebben als in het primaire productie account.

Tijdens een storing moet u uw scripts bijwerken zodat de invoer paden naar het secundaire eind punt verwijzen. Vervolgens verzenden de gebruikers hun taken naar het ADLA-account in de secundaire regio. De uitvoer van de taak wordt vervolgens geschreven naar het ADLA-en ADLS-account in de secundaire regio.

## <a name="next-steps"></a>Volgende stappen

[Richt lijnen voor herstel na nood gevallen voor gegevens in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
