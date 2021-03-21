---
title: Herstel na nood geval voor Azure-gegevens share
description: Herstel na nood geval voor Azure-gegevens share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 1d7c9935d7e0d6bb2d457aa4c08f9b2b3e5fa910
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92218694"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Herstel na nood geval voor Azure-gegevens share

In dit artikel wordt uitgelegd hoe u een nood herstel omgeving kunt configureren voor een Azure-gegevens share. Storingen in azure Data Center zijn zeldzaam, maar kunnen een paar minuten tot uur duren. Data Center storingen kunnen leiden tot onderbrekingen in omgevingen die afhankelijk zijn van gegevens die worden gedeeld door de gegevens provider. Door de stappen te volgen die in dit artikel worden beschreven, kunnen gegevens providers gegevens blijven delen met hun eigen gegevens, in het geval van een storing in een Data Center voor de primaire regio die als host fungeert voor uw gegevens share. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Bedrijfs continuïteit voor Azure-gegevens delen bereiken

Voor de voor bereiding op een Data Center-storing kan de gegevens provider een gegevens share omgeving hebben die is ingericht in een secundaire regio. Metingen kunnen worden uitgevoerd om te zorgen voor een soepele failover in het geval dat er een storing optreedt in een Data Center. 

Gegevens providers kunnen secundaire Azure-gegevens share resources inrichten in een extra regio. Deze gegevens share bronnen kunnen worden geconfigureerd voor het opnemen van shares en gegevens sets die voor komen in de primaire Azure-gegevens share bron. Ze kunnen gegevens gebruikers uitnodigen voor de secundaire shares bij het configureren van de nood herstel omgeving of op een later tijdstip (bijvoorbeeld Als onderdeel van hand matige stappen voor failover).

Als de gegevens gebruikers actieve abonnementen op shares hebben in een secundaire omgeving die is ingericht voor DR-doel einden, kunnen ze het momentopname schema inschakelen als onderdeel van de failover. Als de gegevens gebruikers zich niet willen abonneren op een secundaire regio voor DR-doel einden, kunnen ze op een later tijdstip worden uitgenodigd voor de secundaire share. 

Data-consumers kunnen een actief share-abonnement hebben dat niet wordt gebruikt voor DR-doel einden, of gegevens providers kunnen ze op een later tijdstip uitnodigen als onderdeel van hand matige procedures voor failover. 

## <a name="related-information"></a>Gerelateerde informatie

- [Bedrijfs continuïteit en herstel na nood gevallen](../best-practices-availability-paired-regions.md)
- [Hoge beschikbaarheid in uw BCDR-strategie bouwen](/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over hoe u gegevens kunt beginnen delen, gaat u verder naar de zelfstudie [uw gegevens delen](share-your-data.md).