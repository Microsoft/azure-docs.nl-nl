---
title: Azure Migrate en Site Recovery vergelijken voor migratie naar Azure
description: Hierin wordt een overzicht gegeven van de voor delen van het gebruik van Azure Migrate voor migratie, in plaats van Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: raynew
ms.openlocfilehash: 358efaa1493aa08fb76c9bb83e0e4289950e0969
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87844317"
---
# <a name="migrating-to-azure"></a>Migreren naar Azure

Voor migratie raden wij u aan de Azure Migrate-service te gebruiken om Vm's en servers te migreren naar Azure, in plaats van de Azure Site Recovery-service. Meer [informatie](../migrate/migrate-services-overview.md) over Azure Migrate.


## <a name="why-use-azure-migrate"></a>Waarom Azure Migrate gebruiken?

Het gebruik van Azure Migrate voor migratie biedt een aantal voor delen:
 
 
- Azure Migrate biedt een gecentraliseerde hub voor detectie, evaluatie en migratie naar Azure.
- Het gebruik van Azure Migrate biedt interoperabiliteit en toekomstige uitbrei ding met Azure Migrate-hulpprogram ma's, andere Azure-Services en hulpprogram ma's van derden.
- De Azure Migrate: hulp programma voor server migratie is bedoeld voor server migratie naar Azure. Deze is geoptimaliseerd voor migratie. U hoeft niet meer te weten te komen over concepten en scenario's die niet rechtstreeks relevant zijn voor migratie. 
- Er zijn geen kosten voor het gebruik van hulpprogram ma's voor de migratie van 180 dagen, vanaf het moment dat de replicatie voor een virtuele machine wordt gestart. Dit geeft u tijd om de migratie te volt ooien. U betaalt alleen voor de opslag-en netwerk resources die worden gebruikt tijdens de replicatie, en voor de reken kosten die tijdens de test migraties worden verbruikt.
- Azure Migrate ondersteunt alle migratie scenario's die door Site Recovery worden ondersteund. Daarnaast biedt Azure Migrate voor VMware-Vm's een migratie optie zonder agent.
- Er worden prioriteiten gegeven voor nieuwe migratie functies voor de Azure Migrate: alleen hulp programma voor server migratie. Deze functies zijn niet gericht op Site Recovery.

## <a name="when-to-use-site-recovery"></a>Wanneer moet ik Site Recovery gebruiken?

Site Recovery moet worden gebruikt:

- Voor herstel na nood gevallen van on-premises machines naar Azure.
- Voor herstel na nood gevallen van Azure-Vm's tussen Azure-regio's.

We raden u aan Azure Migrate te gebruiken voor het migreren van on-premises servers naar Azure, als u uw migratie traject al hebt gestart met Site Recovery, kunt u deze blijven gebruiken om de migratie te volt ooien.  

## <a name="next-steps"></a>Volgende stappen

> [Lees de veelgestelde vragen](../migrate/resources-faq.md) over Azure Migrate.
