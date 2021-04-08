---
title: 'Azure Peering Service: Verbindingstelemetrie meten '
description: In deze zelfstudie leert u hoe u verbindingstelemetrie kunt meten.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: derekol
Customer intent: Customer wants to measure their connection telemetry per prefix to Microsoft services with Azure Peering Service.
ms.openlocfilehash: abbe69ebbaed56ed416f85fafa7b77a1740fabe7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96022493"
---
# <a name="tutorial-measure-peering-service-connection-telemetry"></a>Zelfstudie: Peering Service-verbindingstelemetrie meten

 In deze zelfstudie leert u hoe u telemetrie meet voor uw Peering Service-verbindingen.
 
 Verbindingstelemetrie biedt inzichten die worden verzameld voor de connectiviteit tussen de locatie van de klant en het Microsoft-netwerk. In dit artikel leert u het latentierapport te interpreteren voor een specifieke Azure Peering Service-verbinding. 

Als u de Peering Service-verbindingstelemetrie wilt meten, moet u een Peering Service-verbinding in de Azure-portal registreren. Zie [Een Peering Service-verbinding registreren - Azure-portal](azure-portal.md) voor informatie over het registreren van een verbinding.


## <a name="view-a-latency-report"></a>Een latentierapport bekijken

Voer de volgende stappen uit om een latentierapport voor een specifieke Peering Service-verbinding te bekijken.

1. Selecteer **Alle resources** in het linkerdeelvenster en selecteer de Peering Service-verbinding. Selecteer vervolgens **Openen** onder **Voorvoegsels**. 

   ![De Peering Service-verbinding selecteren](./media/peering-service-measure/peering-service-measure-menu.png)

2. Er wordt een pagina van het latentierapport weergegeven voor alle voorvoegsels die gekoppeld zijn aan die Peering Service-verbinding. 

      ![Pagina van latentierapport](./media/peering-service-measure/peering-service-latency-report.png)

3. Standaard wordt het rapport bijgewerkt voor elk uur dat op deze pagina wordt weergegeven. Als u het rapport voor verschillende tijdlijnen wilt bekijken, kiest u de gewenste optie bij **Gegevens weergeven voor de laatste**. 

4. Als u gebeurtenissen voor een specifiek voorvoegsel wilt bekijken, selecteert u de naam van het voorvoegsel en selecteert u **Voorvoegselgebeurtenissen** in het linkerdeelvenster. De vastgelegde gebeurtenissen worden weergegeven.


   ![Voorvoegselgebeurtenissen](./media/peering-service-measure/peering-service-prefix-event.png)

 Enkele van de mogelijke gebeurtenissen die worden vastgelegd in de lijst **Voorvoegselgebeurtenissen** worden hier weergegeven.

| **Voorvoegselgebeurtenissen** | **Gebeurtenistype**:|**Redenen geven**|
|-----------|---------|---------|
| PrefixAnnouncementEvent |Informatie|Aankondiging van voorvoegsel ontvangen|
| PrefixWithdrawalEvent|Waarschuwing| Het intrekken van het voorvoegsel is ontvangen |
| PrefixBackupRouteAnnouncementEvent |Informatie|Aankondiging van back-uproute van voorvoegsel is ontvangen |
| PrefixBackupRouteWithdrawalEvent|Waarschuwing|Intrekken van back-uproute van voorvoegsel is ontvangen |
| PrefixActivePath |Informatie| Actieve route van huidig voorvoegsel   |
| PrefixBackupPath | Informatie|Huidige back-uproute van voorvoegsel   |
| PrefixOriginAsChangeEvent|Kritiek| Exact voorvoegsel ontvangen met een ander autonoom systeemnummer van herkomst (voor actieve route)| 
| PrefixBackupRouteOriginAsChangeEvent  | Fout|Voorvoegsel ontvangen met een ander autonoom systeemnummer van herkomst (voor back-uproute)  |

## <a name="next-steps"></a>Volgende stappen

- Zie [Peering Service-verbinding](connection.md) voor meer informatie over de Peering Service-verbinding.
- Zie [Peering Service-verbindingstelemetrie](connection-telemetry.md) voor meer informatie over de Peering Service-verbindingstelemetrie.